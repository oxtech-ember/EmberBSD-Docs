# How modularity works

[Guide](../README.md) · [Architecture](architecture.md) · [Module guide](../modules/README.md)

A module is a unit with a defined responsibility and interface.
In this guide, the word covers subsystems, services, libraries, and tools.
A repository is an ownership boundary. A package is an installation unit.
A loadable kernel module is one specific implementation mechanism.
These units need not have the same boundaries.

## Selection happens at several times

| Selection point | What it controls | Example |
| --- | --- | --- |
| Kernel build | Compiled subsystems, filesystems, and device attachments | A kernel configuration selects the required OS facilities |
| Hardware discovery | Attachment to actual devices and their resources | ACPI/FDT descriptions and bus matching |
| Package selection | Installed application roots and their dependencies | Versionless Ports package sets |
| Service startup | Which installed services run | `/etc/rc.conf` |
| Application configuration | Providers, extensions, endpoints, and local policy | Spark JSON and Lua modules |

There is no single universal EmberBSD module manifest.
Use the mechanism that owns the selected behavior.
Optional means removable from a suitable composition; it does not mean every component supports live unloading.
Removing a dependency still requires adjusting or removing its consumers.

## Separate mechanism from policy

The common subsystem owns protocol parsing, shared state, validation, and scheduling policy.
The driver owns registers, interrupts, DMA, bus transactions, and firmware operations.
The board description supplies wiring, power, reset, and other hardware properties.
The application owns its data and task policy.

For Wi-Fi, shared wireless code and userland security services cooperate with a radio driver.
Firmware offload changes who performs an operation; it still needs explicit state and event ownership.
For graphics, common DRM interfaces connect display and GPU adapters to userspace libraries and compositors.
Each adapter reports its actual capabilities and limitations.
The [driver architecture](../modules/drivers.md) explains resource ownership across DMA, interrupts, queues and recovery.

## What a module boundary must explain

A usable interface describes inputs, outputs, errors, optional capabilities, and resource ownership.
An asynchronous interface also defines completion, cancellation, timeouts, and teardown order.
An unavailable operation must have a documented failure result.
Applications use declared capabilities instead of guessing from a board or package name.

These are design principles. Read the owner's contract to see which capabilities an implementation currently exposes.
The [Runtime page](../modules/runtime.md) identifies interfaces that remain planned.

## Change a module with its consumers

Locate the interface and all affected callers before changing behavior.
Keep the shared implementation with its subsystem owner.
Update consumers, tests, and documentation together when an internal contract changes.
Preserve on-disk data through an explicit migration when a format changes.
Check the common behavior separately from each affected hardware adapter.

Examples of concrete boundaries:

- [littlefs VFS integration](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/fs/littlefs/README.md)
- [Wireless security contracts](https://github.com/oxtech-ember/EmberBSD/tree/main/sys/net80211/docs)
- [DRM contracts](https://github.com/oxtech-ember/EmberBSD/tree/main/sys/external/bsd/drm2/drm/docs)
- [SDK native tool selection](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/native-toolset.md)
- [Spark extension interfaces](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/runtime.md)
