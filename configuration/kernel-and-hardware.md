# Kernel and hardware configuration

[Configuration](README.md) · [Hardware module](../modules/hardware.md)

The kernel configuration declares compiled options and device attachments.
Platform descriptions provide the hardware resources those drivers consume.
Keep these inputs consistent with the loader and selected target.

## Read a kernel configuration

The AArch64 `EMBER64` configuration includes `GENERIC64` and adds EmberBSD-specific selections.
This abbreviated excerpt illustrates the syntax:

```text
include "arch/evbarm/conf/GENERIC64"
options SPLASHSCREEN
aicwf* at sdmmc?
pseudo-device btuart
```

`include` imports another configuration.
`options` enables a named kernel option.
The device entry permits an `aicwf` attachment on an applicable SD/MMC bus.
`pseudo-device` selects a software device facility.
The corresponding source declarations determine dependencies and compiled files.

This excerpt is not a complete replacement for `EMBER64`.
Read its current options and dependencies before deriving a configuration.
Keep kernel and loadable modules from a compatible build.

## Hardware properties and firmware

ACPI/FDT and platform code describe resources such as interrupts, clocks, reset, and power.
A driver configuration entry does not create these resources.
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

Use the builder's current interface for compiler and output-directory selection.
A configuration that compiles still needs the relevant boot and hardware checks.
