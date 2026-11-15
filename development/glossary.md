# Glossary

[Guide](../README.md) · [Development](README.md)

| Term | Meaning in this guide |
| --- | --- |
| ABI | Binary contract between compiled components, including calling conventions and data layout |
| API | Interface used by a program to request an operation |
| Backend or adapter | Implementation that connects a shared interface to a concrete service or device |
| Base userland | Libraries, commands, and services delivered as part of the base OS |
| Capability | A declared operation or property that a consumer can use |
| Cross build | Build on a host for a different target environment |
| Device tree / FDT | A hardware description and its flattened representation used by applicable boot paths |
| ACPI | Firmware tables and interfaces used to describe and manage a platform |
| DRM / KMS | Graphics device infrastructure and kernel display-mode setting |
| Host | Machine that runs development tools and native generators |
| Native prefix | Directory containing tools and packages built for the host |
| Module | A component with a defined responsibility; only some modules are dynamically loadable |
| Package root | A directly selected package whose dependencies the graph resolves |
| Package set | Versionless roots for a task; it does not define package versions or hardware identity |
| `PKGPATH` | Package recipe path, such as `net/fetch` |
| `PKGNAME` | Concrete package name, including version and applicable package revision |
| Profile | An owner-specific selection of configuration; its meaning depends on the owning tool |
| Runtime | Either an execution library/environment, or the explicitly planned EmberBSD-Runtime project |
| Sysroot | Target headers, libraries, startup objects, and metadata used for target builds |
| Target | Environment that will execute a produced artifact |
| Triplet | Toolchain target identifier; compatibility names can retain `netbsd` |
| VFS | Internal OS abstraction that connects file operations to filesystem implementations |

Capitalized Runtime names the separate project when context requires that distinction.
Spark's runtime guide describes Spark's current execution model.
Neither use means that a stable cross-project application Runtime has been released.
