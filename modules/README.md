# Module guide

[Guide](../README.md) · [Configuration](../configuration/README.md)

Read a page for a component's purpose, dependencies, interfaces, configuration, and current limits.
Subsystem pages explain architecture. Their linked owner documents define the detailed behavior.

| Component | Responsibility | Main owner |
| --- | --- | --- |
| [Base OS](base-os.md) | Processes, memory, system calls, libraries, and administration | EmberBSD |
| [Hardware and boot](hardware.md) | Device discovery, drivers, loader integration, and board descriptions | EmberBSD; hardware reference in DevBoards |
| [Storage](storage.md) | VFS, filesystem adapters, media access, and image construction | EmberBSD; shared library sources in Ports |
| [Networking](networking.md) | Network protocols, interfaces, radio integration, and network services | EmberBSD; optional packages in Ports |
| [Ethernet](ethernet.md) | MAC/PHY boundaries, frame transport, link management, standards, and controller/device integration | EmberBSD; hardware applicability in DevBoards |
| [Wi-Fi](wifi.md) | Native and firmware MAC boundaries, station security, protocol limits, and driver integration | EmberBSD; hardware applicability in DevBoards |
| [Graphics and compute](graphics-and-compute.md) | DRM/KMS, Mesa/Vulkan, buffer sharing, UI acceleration, and driver integration | EmberBSD, Ports, and applications; hardware applicability in DevBoards |
| [Ports](ports.md) | Shared recipes, patches, package roots, and dependency graph | EmberBSD-Ports |
| [SDK](sdk.md) | Host tools, cross builds, sysroots, packages, and release composition | EmberBSD-SDK |
| [Spark](spark.md) | Optional task agent, model access, MCP, and Lua extensions | EmberBSD-Spark |
| [Runtime](runtime.md) | Planned application lifecycle, permissions, and shared device operations | EmberBSD-Runtime |

[Examples](https://github.com/oxtech-ember/EmberBSD-Examples) supplies complete application scenarios with explicit prerequisites and checks.
[Developer skills](https://github.com/oxtech-ember/Ember-Agent-Skills) and
[user skills](https://github.com/oxtech-ember/EmberBSD-User-Skills) help assistants select the owner's workflow.
They contain instructions; they do not supply the device runtime or install its dependencies.
