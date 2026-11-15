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

Read the [OS user guide](https://github.com/oxtech-ember/EmberBSD/blob/main/doc/guide/user-guide.md)
for actual network and SSH setup. Image defaults differ.

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
