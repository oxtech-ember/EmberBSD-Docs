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

## Kernel architecture

- The OS keeps one general-purpose configuration for each functional task, not
  one for each board. For AArch64 it is `EMBER64`: `GENERIC64` with the drivers
  of the supported boards.
- A driver without its device stays passive. One kernel therefore serves many
  boards, and DevBoards records use it.
- Experimental code is off by default. A default-off option or non-default
  attachment flags select it.
- The kernel, its modules and its DTBs come from one build.

To extend the kernel, add a passive built-in driver, a default-off option,
attachment flags or a loadable module. To try a change, build a temporary local
configuration on top of the general-purpose one, by its path. An accepted trial
moves into the general-purpose configuration. Keep a working general-purpose
kernel for recovery. The
[kernel guide](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/development/kernel.md#extend-the-kernel)
gives the rule for each mechanism.

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
