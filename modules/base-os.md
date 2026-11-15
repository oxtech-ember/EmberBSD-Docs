# Base operating system

[Modules](README.md) · [Kernel configuration](../configuration/kernel-and-hardware.md)

The base OS provides the execution environment shared by applications.
The kernel manages processes, virtual memory, scheduling, files, sockets, and device access.
Base userland supplies system libraries, administration commands, startup scripts, and services.

## Interfaces and dependencies

Applications use supported POSIX/BSD interfaces and the target ABI.
Kernel subsystems use internal OS interfaces with explicit locking and resource lifetimes.
The kernel, headers, libc, startup objects, and selected runtime must form a compatible target set.
Host libraries belong to native tools, not target application links.

The base can operate without a desktop, Spark, or the planned Runtime.
A bootable composition still needs the selected architecture, boot path, drivers, and root filesystem.
Small-memory targets can have narrower supported configurations.

## Source map

| Path in the OS repository | Responsibility |
| --- | --- |
| `sys/kern`, `sys/uvm` | Core kernel services and virtual memory |
| `sys/arch`, `sys/dev` | Architecture code and device support |
| `sys/fs`, `sys/ufs` | Filesystem implementations |
| `sys/net`, `sys/netinet`, `sys/netinet6`, `sys/net80211` | Network stacks |
| `lib`, `libexec`, `bin`, `sbin`, `usr.bin`, `usr.sbin` | Libraries, services, and commands |
| `etc`, `share`, `distrib` | Configuration, shared build rules, and distribution integration |
| `tests`, `regress`, `ember/tests` | Tests and EmberBSD integration checks |

Kernel configuration chooses compiled capabilities.
Build variables choose base outputs. Installed service configuration chooses what starts.
These are separate controls; see the [configuration map](../configuration/README.md).

## Read the owner

- [OS build guide](https://github.com/oxtech-ember/EmberBSD/blob/main/README.ember.md)
- [Kernel development](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/development/kernel.md)
- [Service and application configuration](https://github.com/oxtech-ember/EmberBSD-Docs/blob/main/configuration/services-and-applications.md)
- [OS source tree](https://github.com/oxtech-ember/EmberBSD)

Source availability does not qualify every inherited architecture or configuration.
Use the selected platform's current build and execution evidence.
