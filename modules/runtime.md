# Application Runtime

[Modules](README.md) · [Spark](spark.md)

The first optional Runtime component renders small compiled QML scenes.
Application installation, lifecycle management, permission enforcement and a
stable device-wide API remain design work.

## Embedded QML renderer

EmberBSD QML is an embedded subset of the QML used by Qt Quick. The host
SDK compiler translates a supported scene and its input handlers to C. The
application links the generated code with EmberBSD Runtime. This path suits
a device that needs a small standalone UI without installing Qt on the target.

The application owns the event loop. On a touch device, a wscons adapter
reads pointer events from wsmouse. The Runtime applies them to the scene,
advances animations on timed updates and identifies the region to redraw.
The renderer produces RGB565 pixels and the wsdisplay backend writes them
to the framebuffer. The kernel display driver delivers those pixels to the
panel. A native Qt Quick reference can check layout and interaction on the
host; a device run checks the actual display and input path.

- [Compiler and supported QML constructs](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/qml.md)
- [Runtime renderer and display contract](https://github.com/oxtech-ember/EmberBSD-Runtime/tree/main/qml)
- [Weather application and RV32 build comparison](https://github.com/oxtech-ember/EmberBSD-Examples/tree/main/desktop/mosaico-weather)
- [Qt reference tests and interactive device examples](https://github.com/oxtech-ember/EmberBSD-Examples/tree/main/desktop/qml-qt-tests)

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
