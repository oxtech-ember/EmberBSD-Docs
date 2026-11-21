# Configuration map

[Guide](../README.md) · [Modules](../modules/README.md)

Configuration chooses a composition at several independent layers.
Start with the device's task, target ABI, hardware, and resource constraints.
Then select the controls owned by each layer.
For worked choices from flash storage to desktop packages, read
[Choosing components for a system image](system-profiles.md).

| Layer | What you select | Where it lives | When it takes effect |
| --- | --- | --- | --- |
| Hardware and boot | Board/carrier revision, loader, resources, and media layout | DevBoards applicability record; consumed OS/firmware configuration | Boot and device attachment |
| Kernel | Subsystems, filesystems, options, and drivers | `sys/arch/*/conf` and associated `files.*` declarations | Kernel build and boot |
| Base image | Userland outputs, startup integration, and root contents | OS build inputs and SDK image/release configuration | Build, image assembly, and installation |
| Host SDK | Native prefix, target sysroot, cache, and source inputs | SDK workspace/client configuration | Planning, building, and package operations |
| Package selection | Task roots and native/target roles | Ports package sets and explicit `PKGPATH` arguments | Package planning and installation |
| System services | Enabled daemons and their settings | `/etc/rc.conf` and service-specific files | Startup or the service's documented reload/restart |
| Application | Endpoints, models, tools, state, and user policy | Application-owned configuration | Application startup or its supported reload |

## Use the correct scope

A package set selects software. It does not select a board or declare the target ABI.
A driver entry selects code. It does not supply missing wiring or firmware.
A service flag starts an installed service. It does not build that service or its dependencies.
An application endpoint does not configure the kernel's network interface.

There is no cross-project precedence rule for all these files.
Each owner defines its parser, defaults, overrides, and reload behavior.
For example, `/etc/rc.conf` overrides system service defaults, while SDK command arguments follow the SDK parser.

## Worked composition: a console device with an optional agent

1. Select the exact board and supported boot path in DevBoards.
2. Select a kernel and root filesystem compatible with that path.
3. Prepare matching target libraries and the required application packages through SDK/Ports.
4. Configure the network and required system services on the installed device.
5. If Spark is needed, configure its task server, model route, state, and installed tools.

Leaving out Spark removes the agent application and its service dependencies from this composition.
The base OS and an independent console application still use their own interfaces.
This is a configuration example, not a complete image recipe or a new named profile.

## Detailed guides

- [Installation, updates and recovery](installation-and-updates.md)
- [Kernel and hardware](kernel-and-hardware.md)
- [Packages and SDK](packages-and-sdk.md)
- [Services and applications](services-and-applications.md)

Keep machine paths and credentials in local configuration.
Use the owner's reference for executable deployment commands and their prerequisites.
