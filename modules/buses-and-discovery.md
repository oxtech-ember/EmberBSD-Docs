# Buses and device discovery

[Modules](README.md) · [Hardware and boot](hardware.md) · [Driver lifecycle](drivers.md)

Device discovery answers two questions: what device is present, and how can a driver reach it?
ACPI/FDT describe platform devices and resources. Bus protocols can discover additional devices behind a controller.
Autoconfiguration connects those candidates to the drivers selected by the kernel configuration.
Discovery, driver attachment and working I/O are separate results.
Board wiring and hardware support records remain in [DevBoards](https://github.com/oxtech-ember/EmberBSD-DevBoards).

## From platform description to a usable device

The common attachment relationship is:

```text
Platform description or parent bus
    -> controller attachment and shared controller core
    -> common bus framework
    -> peripheral/function driver
    -> network, storage, input or another subsystem
```

A directly described platform device can attach to its subsystem without creating a child bus.
A bridge or hub can add another level. The diagram describes ownership, not a mandatory chain for every transfer.

| Part | Responsibility |
| --- | --- |
| Platform description and platform code | Identify built-in devices, resource connections and board-dependent properties |
| Controller attachment | Obtain resources through ACPI, FDT or the parent bus; connect the controller core |
| Bus framework | Enumerate or accept declared children; supply the bus's common matching and transfer interface |
| Peripheral/function driver | Match the device or function, implement its protocol and connect it to a subsystem |
| Autoconfiguration | Select an attachment from the configured candidates and manage parent/child instances |

A USB or I2C peripheral driver uses the bus transfer interface, not the host controller's registers.
The controller driver handles transport IRQs and DMA. A peripheral can also require separately described resources, such as a GPIO interrupt.
Use the [driver lifecycle](drivers.md) for ownership, asynchronous completion and teardown.

## ACPI and FDT: description before transport

**ACPI** supplies tables, a device namespace and control methods.
The ACPI code checks device availability, handles declared dependencies and presents matching candidates.
Attachments obtain resource descriptions and use ACPI helpers for platform operations.
For an enumerable bus such as PCI, ACPI can supplement the device found by that bus with routing, power or other information.
An ACPI namespace entry is not necessarily a separate driver instance.

**FDT** is the flattened representation of a device tree passed through the boot path.
Nodes describe devices and their relationships. A binding defines how a driver interprets a node's properties.
Compatibility identifiers select candidate drivers; resources can refer to other nodes that provide clocks, interrupts, resets or power.
The current FDT framework uses attachment passes and provider helpers. It does not synthesize a missing provider or driver from a property.
Child buses have their own property import paths, so check the selected binding and importer rather than assuming all properties are handled uniformly.

Firmware descriptions and bus discovery cooperate. They should not create competing owners for the same device.
Keep firmware parsing in attachments and shared helpers; pass the resulting resources and properties to the chip core.
A valid table or DTB does not establish that a device responded or that every described feature is implemented.

The [ACPI device-configuration specification](https://uefi.org/specs/ACPI/6.6/06_Device_Configuration.html)
and [Devicetree specification](https://devicetree-specification.readthedocs.io/en/stable/devicetree-basics.html)
define the description models. The EmberBSD sources below define which paths consume them.

## What each bus discovers

| Bus | Where identity comes from | What the peripheral driver receives |
| --- | --- | --- |
| PCI/PCIe | Configuration-space IDs, class and function information | A function attachment with configuration access, register-mapping, interrupt and DMA facilities |
| USB | Device/configuration/interface descriptors obtained during enumeration | A device or interface attachment and access to pipes/transfers |
| SDIO | Card initialization, function information and card information structures | An SD/MMC function attachment and shared SDIO operations |
| I2C | Usually a declared address and device identity; optional indirect probing exists | A controller tag, bus address and available device properties |
| SPI | Declared chip select and device identity, from firmware or kernel configuration | A peripheral handle and properties for the selected chip select |

An address selects an instance; it does not identify its programming model.
PCI function location includes its bus/segment context. An I2C address belongs to a particular bus or mux channel.
SPI chip select belongs to one controller. A USB address is assigned during enumeration and can change after reconnection.
Use the bus's match information and device contract instead of treating an address or generated device name as a product ID.

## PCIe: host bridge, bus, function

EmberBSD uses its common PCI subsystem for PCIe function discovery and attachment.
The host bridge connects CPU resources to PCI configuration, memory/I/O windows, interrupts and DMA.
Bridges extend the topology. The common bus code reads function identity and passes attachment information to matching drivers.

The function driver maps the register windows it needs through PCI/bus-space helpers and uses the supplied DMA facilities.
It requests interrupts through the PCI interface rather than assuming a fixed platform IRQ.
INTx, MSI and MSI-X have different setup and delivery rules; usable modes depend on the device and host path.
Likewise, the existence of PCIe enumeration does not imply every link generation, optional capability or hotplug scenario works.
Keep host-controller setup in the platform/bridge layer and device-register logic in the function driver.

## USB: host controller, hub, device and interfaces

The host controller driver implements the USB transport backend.
The common stack and hub driver detect port changes, reset and address devices, read descriptors and attempt attachment.
The source has distinct device-level and interface-level matching paths.
A composite device can expose several interfaces, and a driver can claim the interfaces needed for one function.

Class or vendor drivers use pipes and transfers for control, bulk, interrupt or isochronous traffic as required by their protocol.
The controller backend implements those requests using its own scheduling, DMA and interrupts.
Reuse a class driver when the peripheral follows its supported contract; add a vendor adapter when its protocol requires one.

Disconnection can race with transfers. Use the USB cancellation and detach contracts before releasing driver state.
USB speed, transfer support and device functionality depend on the controller, topology and selected driver.
This is the host-side discovery model; USB device mode, connector role switching and USB-C Power Delivery require their own implementations.

## SDIO: I/O functions behind an SD/MMC host

The SD/MMC host driver provides command execution, clock/power control and card-event handling to the common framework.
The framework distinguishes memory cards from SDIO I/O functions, including cards that provide both.
It initializes functions and obtains their identity before invoking function drivers.
Function zero supplies common card control; it is not attached as an ordinary SDIO peripheral.

A function driver uses common SDIO operations to access registers, move data and arrange interrupts.
Some devices need several functions under one coordinated driver.
Function discovery does not load device firmware or implement the higher-level Wi-Fi, Bluetooth or other protocol.

Bus width, timing and interrupt delivery depend on the host, card and platform path.
Successful SD memory I/O does not establish SDIO operation or card interrupt delivery.
The [checked SDIO interrupt contract](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/sdmmc/docs/checked-sdio-irqs.md)
defines shared interrupt ownership and error reporting; use it instead of private card-register updates.

## I2C: described clients and a shared controller interface

The controller publishes an I2C interface; the common `iic` layer provides bus locking and connects client drivers.
Clients acquire the bus for their transactions and release it afterwards.
They use that interface instead of depending on a particular controller's register layout.
An I2C mux adds a bus-selection boundary; the address alone is not enough to locate the client.

Direct configuration supplies child identity and address from firmware/platform data.
Both the ACPI I2C resource importer and the FDT I2C importer feed this model.
The FDT I2C path filters unavailable child nodes and disables indirect scanning, including when no children are declared.
The general I2C framework also retains indirect probing for configurations that select it.

An address acknowledgement is not a reliable chip identity, and a probe transaction can affect a device.
Prefer an explicit description for a known peripheral instead of adding a blind address scan.
I2C and SMBus clients share infrastructure, but a particular controller can support only a subset of transaction forms.
Validate the operations the client needs through the controller contract.

## SPI: declared chip select and transaction framing

SPI has no universal device-enumeration protocol.
The current framework attaches children declared through FDT or explicit kernel configuration.
The peripheral driver supplies the device protocol and uses a handle for its chip select.
Clock mode, speed, data direction and chip-select timing must agree with the peripheral and controller.

The common SPI layer separates peripheral calls from controller configuration and transfer callbacks.
A transfer can contain several chunks while preserving the transaction boundary required by the device.
The interface supports ordinary single-line transfers and optional 3-wire or dual/quad data phases through explicit controller capabilities.
Unsupported combinations must be rejected; the presence of those capability definitions does not establish support in every controller.
The [GPIO SPI implementation guide](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/spi/README.spi-gpio.md)
is one concrete example of a deliberately limited backend.

## Add the missing layer

| Change | Where to work |
| --- | --- |
| Existing chip, another platform discovery path | Add or extend an ACPI/FDT/parent-bus attachment; reuse the chip core |
| New host or bus controller | Implement the bus backend and expose children through the common framework |
| New peripheral or function | Implement its bus client and subsystem contract; reuse enumeration and transport |
| Existing hardware with different wiring | Update the platform description and resource properties; keep hardware facts in DevBoards |

The DesignWare I2C driver shows the separation:
[FDT attachment](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/fdt/dwiic_fdt.c)
and [ACPI attachment](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/acpi/dwiic_acpi.c)
both use the [controller core](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/ic/dwiic.c).
They obtain resources differently, then connect the same I2C transfer implementation to the common bus.
This illustrates composition, not a guarantee that every attachment has identical lifecycle support.

For a new device, establish which stage is missing: parent/controller, child discovery, driver match, resource setup or working transactions.
An unmatched device needs a compatible attachment; a matched device can still fail initialization or I/O.
Check required resource providers, supported transfer modes and error propagation before adding identifiers to a match table.
Use the [kernel configuration guide](../configuration/kernel-and-hardware.md) to select code and the [driver lifecycle](drivers.md) to handle failures and removal.

## Read the implementation

| Concern | Owner references |
| --- | --- |
| Common driver selection | [autoconf(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/autoconf.9), [implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/kern/subr_autoconf.c) |
| ACPI description and resources | [namespace/attachment](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/acpi/acpi.c), [resource parser](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/acpi/acpi_resource.c), [I2C importer](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/acpi/acpi_i2c.c) |
| FDT discovery and providers | [FDT bus](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/fdt/fdtbus.c), [interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/fdt/fdtvar.h), [I2C importer](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/fdt/fdt_i2c.c), [SPI importer](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/fdt/fdt_spi.c) |
| PCI/PCIe functions | [bus implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/pci/pci.c), [attachment interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/pci/pcivar.h), [mapping](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/pci/pci_map.c), [interrupts](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/pci_intr.9) |
| USB host and peripheral boundaries | [enumeration](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/usb/usb_subr.c), [hubs](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/usb/uhub.c), [host backend interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/usb/usbdivar.h), [peripheral interface](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/usbdi.9) |
| SD/MMC hosts and SDIO functions | [host interface](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/sdmmc/sdmmcchip.h), [discovery](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/sdmmc/sdmmc.c), [function interface](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/sdmmc/sdmmcvar.h), [SDIO operations](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/sdmmc/sdmmc_io.c) |
| I2C controllers and clients | [interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/i2c/i2cvar.h), [discovery](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/i2c/i2c.c), [transactions](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/i2c/i2c_exec.c) |
| SPI controllers and clients | [interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/spi/spivar.h), [bus and transfer implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/spi/spi.c), [firmware child properties](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/ofw/ofw_spi_subr.c) |

Use these links for exact callbacks, property formats and supported operations.
The chapter describes implemented integration paths, not blanket conformance to all revisions of the named standards.
