# Update architecture

An enrolled EmberBSD installation uses one base program, `ember-update`, for packages and OS releases.
The storage profile determines when an update takes effect and how the device can recover.
Python, the development SDK and S3 publisher credentials belong on the build host.

## From the build host to the device

| Component | Responsibility |
| --- | --- |
| Ports and OS build host | Build binary packages, distribution sets, kernels and matching modules. |
| SDK publisher | Resolve package dependencies for each target. Publish immutable artifacts, a signed release and the signed channel pointer to S3. |
| Base service | Fetch the channel over HTTPS. Verify both CMS signatures, validity periods, target identity, archive sizes and SHA-256. Select an eligible OS generation and installed packages. |
| Common installer | Install verified local package archives with `pkg_add`. On FFS slots, copy the running root from a snapshot and prepare the inactive root. |
| Boot adapter | Report the running and confirmed slots. Arm, cancel or confirm a one-time trial using the firmware's boot interface. |
| Hardware profile | Identify the target ABI, boot files, partition labels, hardware reset provider and local health check. |
| Discover and the notifier | Read local update state. Discover uses polkit to request an update or to install the package version selected in the Store. The base service owns the transaction. |

The TLS certificate store authenticates the HTTPS endpoint. The release root in
`/etc/openssl/emberbsd-release-root.pem` authenticates the publisher.
Machines with a compatible target can share package archives. Their OS profiles also identify the kernel, boot files and modules.
[Native client and signed release contract](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/ember-update/README.md)

## Enable automatic updates

Enrolled images start `/etc/rc.d/ember_update` after networking and TLS initialization.
The service checks at boot and then hourly. It retries delivery failures.
At boot, the rc script also checks an already staged trial when periodic updates are disabled.
The image supplies `/etc/ember-update-profile.json`. Administrators set policy in `/etc/rc.conf`.
This example selects the current channel and permits automatic restarts between 01:00 and 04:00 local time.

```sh
certctl_init=YES
ember_update=YES
ember_update_packages=YES
ember_update_os=YES
ember_update_channel=current
ember_update_reboot="01:00-04:00"
```

The default restart policy is `ask`. A prepared update waits for an authorized restart.
Run `ember-update status` and `ember-update history` as an ordinary user.
As root, run `ember-update check` to refresh the selection or `ember-update apply` to install it.
With a local kernel, status reports an OS hold. Compatible package updates can proceed.
[Service startup](https://github.com/oxtech-ember/EmberBSD/blob/main/etc/rc.d/ember_update) · [Client settings and commands](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/ember-update/README.md)

## What happens at installation

| Installed profile | Result |
| --- | --- |
| Single root | The service updates installed packages in place. Each successful registration is visible immediately. An interruption can leave a partially applied package selection. For an OS upgrade, use the profile's separate installation procedure. |
| Two FFS roots with a qualified boot adapter | Both package-only and OS updates prepare a complete inactive root. The running root stays available until a trial passes. |

On two-slot systems the installer compares the previous and new distribution defaults with the site's configuration.
A conflicting edit stops preparation. The service arms the loader only after it verifies the installation receipt.
The loader consumes the trial request before entering the candidate.
A local health check and matching boot identity confirm the slot. On failure the reset provider restarts the system into the confirmed root.
The service records the failed OS generation and waits for a newer compatible release.
Applications that share data across slots must support its format after rollback.

The service writes public state atomically to `/var/db/ember-update/state.json`.
It records the last verified channel release, available OS generation, packages, reboot requirement, kernel hold and recent history.
The desktop reads this public state for update notifications. Read the local history after restarting to distinguish preparation, confirmation and rollback.
After confirmation, the available generation clears. Root can read the installed generation from the private receipt in `/var/db/ember-update/os-release.json`.

Discover obtains its Store catalog from `ember-update catalog`. An ordinary user can read it.
Installation requires authorization. The client verifies that the selected version still belongs to the current signed catalog.
It installs the application and its dependencies with the same storage and rollback policy as other package changes.
[Store command contract](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/ember-update/README.md#store-catalog-and-application-installation)

## Older installations and other media

Check for `/usr/sbin/ember-update` and the installed profile first.
An older image with `ember_os_update` uses its original release and recovery procedure until it migrates or is reinstalled.
A publisher can supply one signed transition release for an enrolled legacy U-Boot FFS profile.
The SDK prepares the transition base and etc sets from the stock sets and the matching image description.
The old service stages the candidate. At first boot the native service checks the old receipt, the running kernel and local health.
The confirmed generation becomes the minimum for later updates. The service imports the old OS and package enable flags.
Local overrides keep their priority. Later updates use the standard sets.
[Prepare a transition release](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/signed-releases.md#prepare-a-transition-from-the-former-ffs-updater) · [First boot and recovery](https://github.com/oxtech-ember/EmberBSD/blob/main/usr.sbin/ember-update/README.md#one-time-transition-from-the-former-ffs-updater)

Use the hardware profile to check boot and recovery support. A second partition alone cannot reset a hung kernel.
A NOR image needs space for a complete recoverable candidate and firmware that can select it.
[Installed-system procedures](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/tools/docs/installed-system-update.md) · [Hardware profiles](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/catalog.json)
