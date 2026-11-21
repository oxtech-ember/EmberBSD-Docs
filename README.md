# EmberBSD Developer Guide

Understand EmberBSD before reading its implementation.

EmberBSD is an independent NetBSD-derived Unix for intelligent devices.
This guide explains the concept, module boundaries, configuration, and development path.
It is for application authors, system developers, and hardware integrators.

## Start here

1. [Concept and architecture](concepts/architecture.md): what runs on the device and on the development host.
2. [Modularity](concepts/modularity.md): how components connect, become optional, and change independently.
3. [Module guide](modules/README.md): each module's purpose, dependencies, interfaces, and implementation owner.
4. [Configuration](configuration/README.md): select hardware, kernel features, packages, services, and application behavior.
5. [Development](development/README.md): choose an owner and make a checked change.

For unfamiliar terms, use the [glossary](development/glossary.md).
For installation, start with [installation, updates and recovery](configuration/installation-and-updates.md),
then follow the linked procedure for the selected image or device.

## Choose a reading path

| Your task | Read next |
| --- | --- |
| Write an application | [Development tools and application choices](development/README.md#a-first-application-change), [EmberBSD QML](modules/runtime.md), [packages](modules/ports.md) |
| Adapt a board or driver | [Modularity](concepts/modularity.md), [hardware](modules/hardware.md), [kernel configuration](configuration/kernel-and-hardware.md) |
| Understand a driver's lifecycle and resources | [Driver architecture](modules/drivers.md): DMA, interrupts, queues, stop and recovery |
| Connect a controller or peripheral | [Buses and device discovery](modules/buses-and-discovery.md): ACPI/FDT, PCIe, USB, SDIO, I2C and SPI |
| Add a Wi-Fi driver | [Wi-Fi architecture and standards](modules/wifi.md), [driver interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/net80211/docs/README.md) |
| Add a Bluetooth controller or profile | [Bluetooth architecture, profiles and proposed extensions](modules/bluetooth.md), [driver interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/netbt/docs/README.md) |
| Add an Ethernet controller, PHY, or USB adapter | [Ethernet architecture and standards](modules/ethernet.md), [driver interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/net/docs/ethernet.md) |
| Choose components for an appliance or desktop | [System image choices](configuration/system-profiles.md), [base OS](modules/base-os.md), [configuration map](configuration/README.md) |
| Install, configure or update a device | [Installation model](configuration/installation-and-updates.md), [services and applications](configuration/services-and-applications.md) |
| Add a GPU or display driver | [Graphics architecture and standards](modules/graphics-and-compute.md), [driver interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/external/bsd/drm2/drm/docs/README.md) |
| Add an NPU or inference backend | [NPU architecture and technologies](modules/npu.md), [native and runtime interfaces](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/arch/arm/sunxi/docs/npu-integration.md) |
| Accelerate a UI or inference workload | [Rendering and GPU compute](modules/graphics-and-compute.md), [NPU inference](modules/npu.md), [package selection](configuration/packages-and-sdk.md) |
| Extend the agent | [Spark](modules/spark.md), [application configuration](configuration/services-and-applications.md), [Runtime boundary](modules/runtime.md) |

## Scope and sources

This repository owns conceptual explanations: purpose, boundaries, interactions, design reasons, and the meaning of configuration choices.
Source code is also documentation. Code, comments, schemas, tests, help, and owner references describe exact implementation behavior.
Detailed APIs, command recipes, field/default lists, and hardware support records stay with their owners.
Each module page links to those sources. Read the [source baseline](sources.md) for the reviewed revisions.

Implemented code, a successful build, and a working device scenario are separate results.
The guide labels planned interfaces explicitly. Runtime remains a design-stage component.
The existing SDK provides host tools; a stable application SDK is a separate architectural goal.

Read these Markdown files on GitHub or in a local checkout. No site generator is required.
See [Contributing](CONTRIBUTING.md) for documentation changes and [LICENSE](LICENSE) for reuse.
