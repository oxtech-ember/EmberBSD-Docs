# Application Runtime

[Modules](README.md) · [Spark](spark.md)

The first optional Runtime component renders small compiled QML scenes.
Application installation, lifecycle management, permission enforcement and a
stable device-wide API remain design work.

## Embedded QML renderer

On the developer machine, the SDK compiles a defined subset of QML into C
scene data. The target application owns its model values and event loop. It
passes those values to a portable renderer which produces RGB565 scanlines.
A separate wsdisplay backend presents them through the OS framebuffer ABI.
Developers can use this path independently of a desktop or Qt installation
on the device. The weather example changes an integer binding at run time.

- [Compiler and supported QML constructs](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/qml.md)
- [Runtime renderer and display contract](https://github.com/oxtech-ember/EmberBSD-Runtime/tree/main/qml)
- [Weather application and RV32 build comparison](https://github.com/oxtech-ember/EmberBSD-Examples/tree/main/desktop/mosaico-weather)

## Intended responsibility

Runtime is intended to own application execution, installation, lifecycle, permissions, and shared device operations.
The planned application SDK defines developer-facing interfaces and compatibility checks.
The OS continues to own drivers and hardware support.
Ports continues to own shared external dependencies.

The proposed lifecycle and permission services still need defined contracts
and reproducible applications that exercise them.
Optional profiles are intended to let small devices select the operations they need.

## Develop with current interfaces

Current applications use implemented OS interfaces, libraries, services, and the host SDK.
Spark has its own implemented extension and process model.
Its `docs/runtime.md` describes Spark execution; it is not the separate EmberBSD-Runtime implementation.

Start from an [application example](https://github.com/oxtech-ember/EmberBSD-Examples)
or the [host SDK](https://github.com/oxtech-ember/EmberBSD-SDK).
Use the [Runtime owner](https://github.com/oxtech-ember/EmberBSD-Runtime/blob/main/README.md)
for the current design boundary.
