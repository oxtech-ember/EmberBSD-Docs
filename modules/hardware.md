# Hardware and boot

[Modules](README.md) · [Hardware configuration](../configuration/kernel-and-hardware.md)

Hardware integration connects a concrete board to shared OS services.
It includes the loader handoff, architecture initialization, device discovery, and chip adapters.
DevBoards explains hardware identity and applicability. The OS owns the executable implementation.

## Boundaries

| Part | Responsibility |
| --- | --- |
| Loader and firmware | Prepare the platform and transfer control through the selected boot contract |
| ACPI/FDT and platform code | Describe resources, wiring, power, reset, and device properties |
| Bus attachment | Match a device and acquire its bus resources |
| Chip driver | Access registers, interrupts, DMA, and firmware operations |
| Common subsystem | Provide shared algorithms and interfaces to consumers |

Bus access and DMA use the OS abstractions appropriate to the device.
Driver attachment does not by itself establish that every hardware function works.
Firmware redistribution and loading have their own requirements.

## Configuration and extension

Choose a board, carrier, revision, and loader before choosing an image.
Kernel configuration selects drivers; ACPI/FDT supplies their hardware instances and properties.
A new carrier can change wiring or power even when the CPU module is unchanged.
Keep those facts separate from reusable driver logic.

For a new device, start with the nearest supported bus and subsystem interface.
Read [buses and device discovery](buses-and-discovery.md) to separate platform description, controller and peripheral work.
Read [driver architecture](drivers.md) for lifecycle, resource ownership, DMA, interrupts, queues and recovery.
Add missing common behavior at the subsystem owner, then connect the hardware adapter.
Check resource release, cancellation, reset, and failure paths as well as successful attachment.

## Read the owner

- [DevBoards reader guide](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/docs/reader-guide.md)
- [Hardware catalog](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/catalog.json)
- [From a board to your device](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/docs/device-design.md)
- [Add an OS board port](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/development/adding-a-board.md)

Support belongs to the exact documented configuration and scenario.
A boot result does not imply working Wi-Fi, graphics, power management, or sustained operation.
