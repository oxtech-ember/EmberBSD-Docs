# Installation, updates and recovery

[Configuration](README.md) · [Base OS](../modules/base-os.md) · [Services](services-and-applications.md)

Installation combines a boot path, a compatible OS build and local configuration.
It does not enable every driver or optional application in the source tree.
Start with the device's required functions and the exact platform profile in
[DevBoards](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/docs/reader-guide.md).
That profile owns hardware applicability, firmware, media layout and recovery access.

## Choose an installation route

| Route | What it establishes | Detailed owner |
| --- | --- | --- |
| amd64 UEFI VM with sysinst | Installer behavior and a guest boot from the installed disk | [VM procedure](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/sysinst/docs/vm-installation.md) |
| Device-specific image or board installation | A composition for the selected model, loader and storage | [DevBoards](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/catalog.json), then the profile's linked producer |
| A source-built release | Artifacts made from selected OS, toolchain and package inputs | [SDK release workflow](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/os-release.md) |

A VM result does not qualify a physical machine. A kernel port does not by itself provide an installer.
Use the existing development-image route only within its documented limits; it is separate from a complete current release.
The [release list](https://github.com/oxtech-ember/EmberBSD/releases) supplies artifact records.
Choose a release whose architecture, profile and acceptance match the intended route; a newer source commit alone is insufficient.

## Keep the build coherent

Kernel, modules, base userland, headers and runtime must belong to a compatible, recorded composition.
Keep its source identity, build profile and manifest together. NetBSD compatibility names do not select upstream NetBSD binaries.
The SDK prepares releases; the OS owns its native build and installer; Ports owns shared package recipes.

Artifact type determines how it is consumed:

| Artifact | Role |
| --- | --- |
| Installation ISO or bootable USB image | Boots an installer and supplies or locates its matching distribution sets |
| VM disk | Contains an installed guest or a documented development image |
| Board disk image | Contains a layout specific to a boot path and medium |
| Kernel, module, DTB or set archive | One input to a build, installation or update procedure |

Compression does not change an artifact's role. Verify downloaded bytes against the publisher's manifest;
that verifies integrity, while source provenance and platform acceptance answer different questions.
The installer and its selected sets must be compatible. Changing a download URL does not update the running installer kernel.

## Separate the choices inside the image

Base-set selection, graphics and local services are independent choices.
A console system can provide networking and remote administration without a desktop.
A desktop adds packages and their dependencies; it requires a complete compatible bundle and a supported display path.
The [sysinst contract](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/sysinst/docs/text-installer.md)
defines current profiles, source checks, package prerequisites and unsupported operations.
Its experimental options are not a statement that every release contains them.

Before installing, identify the destination storage and preserve its needed data and recovery path.
After installation, boot without the installation medium and check the installed build identity, root filesystem,
configured account, network and required services. Reboot once more to distinguish persistent setup from session-only changes.
Continue with [services and application configuration](services-and-applications.md).

## Three different update operations

| Operation | Compatibility boundary | Owner |
| --- | --- | --- |
| OS base update | Kernel, modules, userland and local system configuration | [OS upgrade notes](https://github.com/oxtech-ember/EmberBSD/blob/main/UPDATING) and the selected installer or device procedure |
| Application/package update | Package dependency closure, ABI/runtime and application data format | [Ports and SDK](packages-and-sdk.md), plus the application |
| Firmware or loader update | Device revision, boot protocol, storage layout and recovery method | The selected DevBoards profile and firmware producer |

### How a binary reaches the device

The [update architecture](../modules/updates.md) describes the publisher, signed S3 channel, native client, package tools and OS installer.
It identifies the required image services, configuration files, trust roots and logs.

### Update packages on a running device

Images with the package layer can update installed applications at boot and periodically afterwards.
For the service setting, manual update and last successful pass, see [the package update cycle](../modules/updates.md#one-package-update-cycle).

<a id="update-the-os-on-an-installed-board"></a>

### Update an installed OS

Start with the [installed system update procedure](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/tools/docs/installed-system-update.md) for a VM or physical machine.
Select a compatible release and collect its verified artifacts before applying changes.
The installed profile determines the installer and boot path. Some [sysinst images](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/sysinst/docs/vm-installation.md#upgrade-and-configuration-merge) deliberately refuse upgrade.
Hardware documentation supplies boot file locations and recovery instructions when a physical target requires them.
See [OS delivery and activation](../modules/updates.md#os-delivery-and-activation) for the boundary between fetching a file and booting a release.

## Recovery is a complete state choice

Preserve a compatible previous OS, its configuration and the data needed by its applications.
A VM recovery point includes its powered-off disk and writable UEFI variables, with the matching firmware and launch configuration.
A board uses its own boot-loader and storage procedure. Replacing a file on a boot partition is insufficient if the loader uses another kernel.

Restoring a backup discards later changes unless they were saved separately.
Verify that the backup is readable and that the recovery route boots before relying on it.
There is no general accepted automatic OS rollback service implied by these recipes.
After update or recovery, verify the build identity, configuration, data and the same useful operation across a normal reboot.

For a failure report, identify the expected operation, actual result, build and last successful stage.
Send a small reproducer and relevant diagnostics to the [implementation owner](../development/README.md#select-an-owner).
Keep credentials, private keys and unrelated personal data outside the report.
