# Planned application Runtime

[Modules](README.md) · [Spark](spark.md)

**Design stage.** The Runtime repository currently describes its purpose and boundaries.
It provides no executable Runtime, installer, stable Device API, or validated Wasm environment.
There is no Runtime installation command to configure today.

## Intended responsibility

Runtime is intended to own application execution, installation, lifecycle, permissions, and shared device operations.
The planned application SDK defines developer-facing interfaces and compatibility checks.
The OS continues to own drivers and hardware support.
Ports continues to own shared external dependencies.

These are proposed responsibilities, not available APIs.
An implementation needs explicit contracts and a reproducible consumer that exercises them.
Optional profiles are intended to let small devices select the operations they need.

## Develop with current interfaces

Current applications use implemented OS interfaces, libraries, services, and the host SDK.
Spark has its own implemented extension and process model.
Its `docs/runtime.md` describes Spark execution; it is not the separate EmberBSD-Runtime implementation.

Start from an [application example](https://github.com/oxtech-ember/EmberBSD-Examples)
or the [host SDK](https://github.com/oxtech-ember/EmberBSD-SDK).
Use the [Runtime owner](https://github.com/oxtech-ember/EmberBSD-Runtime/blob/main/README.md)
for the current design boundary.
