# Choosing components for a system image

[Configuration](README.md) · [Storage](../modules/storage.md) · [System services](services-and-applications.md)

A system image combines a kernel, a root filesystem, a base userland and the applications and services needed for a workload. Each choice takes effect at a different point. A filesystem needs kernel support. A statically linked program incorporates its selected libraries at build time. An installed daemon starts according to the service configuration.

## Which parts can change?

| Component | Why choose a different implementation? | What else must agree? |
| --- | --- | --- |
| Root filesystem | littlefs suits supported NOR flash. FFS is an option for block storage when its Unix filesystem semantics are needed. | The kernel needs the filesystem and storage driver. The image producer must create the selected format and the boot path must locate the root. Check the application's requirements for operations such as hard links before migrating data. |
| SSH service | The tested compact appliance stages Dropbear for command execution, streams and interactive shells. Its OpenSSH selection can also include the SFTP server for clients that require it. | Install the chosen server and startup files. Preserve the device's host identity during a switch, provision authorized user keys and provide pseudo-terminals for interactive sessions. |
| C library allocator | A compact static base can use the BSD page-and-bucket allocator. Keep jemalloc for applications that depend on its specific interfaces. | Build the target libc with the chosen allocator. Relink static programs against that libc for the selection to take effect. |

The implementation owners document the [littlefs storage contract](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/fs/littlefs/README.md), [FFS image creation](https://github.com/oxtech-ember/EmberBSD/blob/main/sbin/newfs/newfs.8), [OS image profiles](https://github.com/oxtech-ember/EmberBSD/blob/main/ember/image/cix-p1-userland.md) and [Dropbear appliance package](https://github.com/oxtech-ember/EmberBSD-Ports/tree/main/pkgsrc/security/dropbear-appliance).

## Start with the workload

A standalone control panel needs its display and input drivers, a mountable root and a way to start the UI. If the UI works offline and console access meets recovery needs, the image can omit the network-management services. A development image for the same board can add Wi-Fi authentication, address configuration and SSH. Add name discovery or local logs when the workflow uses them. Disabling a daemon saves startup work and resident memory. To reclaim storage, omit its binaries and dependencies that no other component uses.

On a more capable system, a graphical session can serve several applications. Select the display stack and desktop packages for the supported hardware, then add the network and storage services that users need. Ports provides a [Plasma session package set](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/profiles/package-sets/plasma.txt). Consult the [hardware catalog](https://github.com/oxtech-ember/EmberBSD-DevBoards/tree/main/boards) for the selected board's display and boot support.

Measure image storage, running memory and boot time separately. For example, a log on tmpfs uses memory as it grows and disappears at reboot. A persistent log uses storage and generates writes to the medium. The [Mosaico design notes](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/boards/esp-mosaico/design.md) show which choices have been exercised on one small board.
