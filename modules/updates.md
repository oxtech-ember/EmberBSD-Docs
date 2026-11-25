# Update architecture

An installed EmberBSD system reads packages and OS sets from the same signed channel over HTTPS.
A VM and a physical machine use the same package client. Their OS installers can use different boot and recovery procedures.

## From producer to device

```text
Build host        Ports / OS builds -> SDK publisher -> signed release on S3

Package update    rc.d/ember_pkg_update -> SDK auto-update -> SDK update <- HTTPS/S3
                                                |                 |
                                             syslogd          pkg_tools
                                                |                 |
                                         /var/log/messages      pkgdb

OS update         user or installer -> SDK fetch-artifact <- HTTPS/S3
                                              |
                                        verified files -> installer -> boot loader
```

| Component | Responsibility |
| --- | --- |
| Ports and OS builders | Ports supplies package recipes. The OS supplies the kernel, modules and distribution sets. Producers reuse matching binaries and build missing or changed inputs. |
| SDK publisher | Signs the release manifest, uploads additions to S3 and verifies them through public HTTPS. It then switches the signed channel pointer. |
| OS image builder | Installs the native SDK client, Python, HTTPS certificates and a native repository configuration. Images with the package layer enable the package service. |
| OS service `ember_pkg_update` | Starts one SDK `auto-update` process as root at boot. Its rc script orders startup after `LOGIN`, `NETWORKING` and `syslogd`. No interactive login is required. |
| Native SDK client | Authenticates the release, reads the installed package database, resolves compatible updates and reuses verified archives. |
| Native package tools | `pkg_info` reports the installed versions. `pkg_admin` checks package signatures. `pkg_add` applies replacements and runs package installation hooks. |
| OS installer and boot loader | Install the selected system sets, merge local configuration and activate the matching kernel through the machine's boot path. |

The publisher alone needs S3 write credentials and signing keys. Devices read the public channel.
TLS authenticates the network endpoint. The separate EmberBSD release root authenticates the signatures on `current.p7m` and `release.p7m`.
The manifest identifies each artifact by size and SHA-256. Packages also carry a signature that native package tools verify before installation.
[Publisher contract](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/s3-distribution.md) · [Signature chain](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/signed-releases.md)

## What the image configures

| Setting or file | Meaning on a device with the package layer |
| --- | --- |
| `/etc/rc.conf.d/ember_pkg_update` | `ember_pkg_update=YES` enables the service at boot. Set it to `NO` to disable future starts. Use the rc script to start or stop the current process. |
| `/usr/local/libexec/ember-sdk.pyz` | Signed SDK client, run by `/usr/pkg/bin/python3.15`. The image provisions the public release trust root with the client. |
| `/usr/local/etc/ember-sdk.json` | The service passes this file to the client. Its `url` selects the release channel. The public image uses `https://emberbsd-pkg.oxteam.me`. |
| `repositories` in the client configuration | Selects the architecture, target ABI, runtime, native installation mode, package prefix and package database. The image uses `/usr/pkg` and `/usr/pkg/pkgdb`. |
| `https_ca` and `trust_root` | The image supplies a CA bundle for HTTPS. The client uses its bundled release root by default. An administrator can provision a separate root for a private channel. |
| `state_dir` and `cache_dir` | The image uses `/var/db/emberbsd-sdk` for client state and locking, and `/var/cache/emberbsd/packages` for verified downloads. Installed versions come from pkgdb. |
| System network and clock | The device needs DNS, a route to the channel and time within the validity periods of certificates and signed metadata. |

The package layer can be built for aarch64 and amd64. A base-only or recovery image can omit it.
The layer is an input to fresh image construction. Applying it over a live package prefix would replace installed package state.
[OS image integration](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/image/README.md#package-updates-on-a-running-system) · [Client configuration](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sdk-client.md)

## One package update cycle

1. The service starts the controller. It checks immediately, then waits one hour after a successful pass by default.
2. The client reads the signed release and the current pkgdb. It selects installed roots and their published dependencies. A package missing from the catalog stays installed.
3. The client reuses compatible installed versions and cached archives, then downloads the missing bytes. Native package tools install the verified replacements and run their configuration hooks.
4. The controller logs the result through `syslogd`. A failed first attempt retries after 30 seconds at the default interval. Later failures double the delay up to that interval. A success resets the delay.

After enabling the service in its rc configuration, run `/etc/rc.d/ember_pkg_update start` as root for an immediate check.
To inspect the running service and the outcome, use:

```sh
/etc/rc.d/ember_pkg_update status
grep ember_pkg_update /var/log/messages | tail
```

The stock rc script uses the default interval. A directly managed SDK controller can select its interval with `--interval`.
A running PID shows that the controller is alive. Its log shows whether the last pass succeeded, reused packages or downloaded replacements.
For a DNS failure, check the route and resolver. For a signature or time error, check the clock, channel and configured trust roots.
For an ABI or dependency refusal, inspect the installed-package plan and the reported dependency before retrying.
An interrupted pass can leave some replacements installed. Each retry reads the actual package database again.
From a root shell, use the same client's `plan --installed` and `update` with the image's state and cache. Add `--package ROOT` to select one installed application.
After a replacement, run the application and check its saved settings. Application data migration belongs to that package.
[Automatic update controller](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/automatic-updates.md) · [OS service](https://github.com/oxtech-ember/EmberBSD/blob/main/etc/rc.d/ember_pkg_update)

## OS delivery and activation

The same client can fetch a named OS artifact from a signed release. An ordinary user can download to a private FFS directory. The command verifies the archive and writes the requested file.
The installer needs administrative access to change the running system.
The installed OS profile determines which kernel, modules, base and configuration sets belong together.
Compatible machines can share userland sets. A custom kernel requires matching modules and a compatible userland.
Check the firmware or loader, disk layout and any required device tree only when selecting or activating boot files.

An OS update needs a supported installation path for the selected profile. Stage its sets, preserve a recoverable installation and merge new defaults.
If an interface changes across kernel and userland, install both sides before the first new boot as required by that interface.
After activation, verify the loaded revision, root filesystem, network and a service with its saved settings.
The package service updates applications. Unattended OS activation, boot confirmation and automatic rollback still require separate implementation and acceptance.
[Installed OS update procedure](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/tools/docs/installed-system-update.md) · [Signed artifact download](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sdk-client.md#download-one-release-artifact)
