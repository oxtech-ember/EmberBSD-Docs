# Storage and filesystems

[Modules](README.md) · [Kernel configuration](../configuration/kernel-and-hardware.md)

Applications access files through OS file operations.
VFS connects those operations to a filesystem implementation.
Storage drivers provide block or flash operations below that layer.
Image builders prepare the bytes and metadata that the loader and target will consume.

## Selection and dependencies

Choose a filesystem supported by the selected kernel, loader, media, and image producer.
The general VFS architecture does not make every filesystem bootable on every board.
Filesystem permissions, synchronization, supported operations, and recovery behavior are part of the choice.

Kernel configuration selects filesystem support.
Image creation sets layout and contents. Boot configuration selects the root.
The installed system uses mount configuration, including `/etc/fstab`, for its persistent mount layout.

## Example: littlefs

The optional littlefs integration demonstrates the boundary between shared library and OS adapter.
Ports prepares the common upstream source. The OS VFS adapter supplies BSD metadata and vnode behavior.
The driver handles the underlying device rather than file permissions or application policy.

`file-system LITTLEFS` selects the kernel integration.
`EMBER_LITTLEFS_SOURCE` names the source tree prepared through Ports.
`options LITTLEFS_FLASH` additionally selects the generic NOR flash backend.
These entries are fragments, not a complete bootable kernel configuration.

The current contract documents unsupported hard links, NFS file handles, and public ACL/xattr operations.
The flash backend also excludes NAND until its ECC and bad-block contract is implemented.
Select this backend only when its semantics fit the application.

## Read the owner

- [littlefs integration and image contract](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/fs/littlefs/README.md)
- [OS filesystem sources](https://github.com/oxtech-ember/EmberBSD/tree/main/sys/fs)
- [Release and root-image composition](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/os-release.md)
- [Board-specific loader and media requirements](https://github.com/oxtech-ember/EmberBSD-DevBoards)
