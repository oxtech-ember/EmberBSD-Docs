# Services and application configuration

[Configuration](README.md) · [Networking](../modules/networking.md) · [Spark](../modules/spark.md)

Installed-system configuration controls which services run and how applications use them.
It is separate from kernel compilation and package selection.

## System startup

Use `/etc/rc.conf` for local service choices.
Preserve its existing defaults include and board-specific settings.
The installation supplies its identity and enables the services needed for the device's task.
Service-specific files supply settings such as network authentication or SSH policy.
Enable a service after its prerequisites are installed and configured.
Use the service's documented validation and reload/restart behavior.

Local overrides belong in `/etc/rc.conf`; system defaults remain with the installed base.
An account, a listening service and permission to use that service are separate settings.
Choose a normal account for daily work and an explicit administration path; membership of `wheel` does not install `sudo`.
Image defaults differ, so retain console access until the intended remote access survives a normal reboot.

Read the implementation's [startup model](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man8/rc.8),
[rc.conf reference](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man5/rc.conf.5)
and [account creation reference](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/user/useradd.8)
for exact commands and options. Use the installed manuals for the device's release.

## Network and remote access

A network path needs an attached interface, link authentication where required, an address, routing and name resolution.
Wi-Fi association alone does not supply an IP configuration. Ethernet success does not establish Wi-Fi or Bluetooth support.
Use one owner for address configuration on an interface; avoid competing DHCP clients.
Keep transient diagnostics separate from startup configuration.
Read [networking](../modules/networking.md) for the layers and
[interface startup configuration](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man5/ifconfig.if.5)
for the exact persistent mechanism. Radio and firmware applicability remain in DevBoards.

SSH has two identities: the server's host key identifies the device; a user's key or password authenticates the account.
Keep private keys with their owner and verify the server fingerprint through a trusted path such as the console.
Configure the account and its administrative access before restricting existing login methods.
Validate a changed server configuration and test a second connection before closing working access.
The [sshd manual](https://github.com/oxtech-ember/EmberBSD/blob/main/crypto/external/bsd/openssh/dist/sshd.8)
and [server configuration](https://github.com/oxtech-ember/EmberBSD/blob/main/crypto/external/bsd/openssh/dist/sshd_config.5)
own the command, file and permission details.
First-boot defaults belong to the selected image's
[installer contract](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/sysinst/docs/text-installer.md#first-login).

## Applications and desktop sessions

Ports owns shared packages; each application owns its configuration, data and execution requirements.
Select a compatible target ABI and complete dependency closure through the [package model](packages-and-sdk.md).
Package installation does not by itself guarantee configured services or migrated application data;
follow the package/application contract. It is separate from an OS base upgrade.
The host SDK already provides build and package operations; the separate Runtime remains a proposed device application environment.
On the installed OS, package tools may depend on the image profile.
Their [install](https://github.com/oxtech-ember/EmberBSD/blob/main/external/bsd/pkg_install/dist/add/pkg_add.1),
[inventory and reverse-dependency](https://github.com/oxtech-ember/EmberBSD/blob/main/external/bsd/pkg_install/dist/info/pkg_info.1)
and [removal](https://github.com/oxtech-ember/EmberBSD/blob/main/external/bsd/pkg_install/dist/delete/pkg_delete.1)
references own exact operations. Review dependency impact and preserve application data before changes.

A desktop is an optional installed application profile. Its packages, display path, service dependencies and session launcher
must agree. A console profile can manage networking without a graphical Wi-Fi manager.
Use the [installer's desktop contract](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/sysinst/docs/text-installer.md#first-login)
for available choices and required bundles; no desktop name by itself establishes release or hardware acceptance.
Read [installation and updates](installation-and-updates.md) before replacing the base or restoring a backup.

## Spark model and task configuration

Start from Spark's [`examples/agent.json`](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/examples/agent.json).
It is an application template, not a complete system image configuration.
The installation supplies a compatible task server, model service, and credentials.

Direct model routing puts model access at the agent installation.
Routing through the task server centralizes provider access there.
Choose according to endpoint compatibility, credential placement, and service availability.
Direct model routing still uses the task server for task execution.
Persistent state lets the agent retain task progress across process restarts.
Credential handling, path resolution, defaults, and precedence belong to the owner's exact configuration contract.

## Spark extensions and optional native features

| Extension type | Architectural choice |
| --- | --- |
| In-process Lua extension | Share agent state and access for trusted installation behavior |
| In-process MCP adapter | Connect installed tool adapters inside the agent process |
| External MCP service | Connect tools across a process or service boundary |
| Optional native feature | Select hardware-related dependencies at build time |

An in-process extension inherits the agent process's access.
The portable agent bundle does not itself install the separate workshop Lua interpreter.
Read the extension contract before choosing process and trust boundaries.

Spark's extension contracts belong to that application. They are not a universal EmberBSD plugin API.
The separate [Runtime](../modules/runtime.md) still has no implemented application configuration contract.

Read [Spark runtime and extensions](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/runtime.md)
and [cross-build options](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/cross-build.md)
for complete fields, defaults, validation, and execution commands.
