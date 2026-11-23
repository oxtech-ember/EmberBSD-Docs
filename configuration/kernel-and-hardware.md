# Kernel and hardware configuration

[Configuration](README.md) · [Hardware module](../modules/hardware.md)

The kernel configuration declares compiled options and device attachments.
Platform descriptions provide the hardware resources those drivers consume.
Keep these inputs consistent with the loader and selected target.

## Understand a kernel configuration

A kernel configuration combines a base selection with the facilities needed by a target.
Some choices enable shared subsystems. Others permit drivers to attach to matching devices.
Software device facilities can exist without a corresponding physical chip.
The source declarations define the exact dependencies and compiled files.

For example, choosing wireless driver support includes the software needed to communicate with a radio.
The board must still provide the expected bus connection, power, and firmware.
Selecting that code does not establish radio operation on a new board.

Read the current configuration and its source declarations when choosing exact entries.
Keep kernel and loadable modules from a compatible build.

## Choose a kernel

The OS keeps general-purpose configurations, one for each functional task;
the [kernel guide](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/development/kernel.md#kernel-configurations)
lists them. Drivers of absent devices stay passive, so one configuration
serves many boards. Start with `EMBER64` for AArch64.

A trial or diagnostic kernel for one board is a board configuration in
DevBoards, with the evidence of what it was tested for. Use it only for that
scenario, and keep a working general-purpose kernel for recovery.

## Hardware properties and firmware

ACPI/FDT and platform code describe resources such as interrupts, clocks, reset, and power.
A driver configuration entry does not create these resources.
Read [buses and device discovery](../modules/buses-and-discovery.md) for how descriptions and bus enumeration select device instances.
Firmware selection and licensing are separate inputs to the device's documented boot or attachment path.

Use a board record that matches the carrier and revision.
Follow its loader and media-layout contract.
Changing the root filesystem also requires a compatible image producer and boot path.

## Source references

- [`EMBER64`](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/arch/evbarm/conf/EMBER64)
- [Kernel development guide](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/development/kernel.md)
- [Board development guide](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/development/adding-a-board.md)
- [DevBoards catalog](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/catalog.json)
- [OS build guide](https://github.com/oxtech-ember/EmberBSD/blob/main/README.ember.md)

Use the builder's current source and help for exact compiler and output-directory options.
A configuration that compiles still needs the relevant boot and hardware checks.
