# Services and application configuration

[Configuration](README.md) · [Networking](../modules/networking.md) · [Spark](../modules/spark.md)

Installed-system configuration controls which services run and how applications use them.
It is separate from kernel compilation and package selection.

## System startup

Use `/etc/rc.conf` for local service choices.
Preserve its existing defaults include and board-specific settings.
For example, the OS user guide sets these assignments:

```sh
rc_configured=YES
hostname="ember-device"
```

Choose a distinct hostname. Update existing assignments instead of appending conflicting copies.
Service-specific files supply settings such as network authentication or SSH policy.
Enable a service after its prerequisites are installed and configured.
Use the service's documented validation and reload/restart behavior.

Read the [OS user guide](https://github.com/oxtech-ember/EmberBSD/blob/main/doc/guide/user-guide.md)
for actual network and SSH setup. Image defaults differ.

## Spark model and task configuration

Start from Spark's [`examples/agent.json`](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/examples/agent.json).
It is an application template, not a complete system image configuration.
The installation supplies a compatible task server, model service, and credentials.

| Choice | Fields | Effect |
| --- | --- | --- |
| Direct model route | `model.baseUrl`, optional `model.keyEnv`; leave `model.via` unset | Agent contacts the compatible model endpoint |
| Task-server model route | `model.via` is `server`; omit model URL/key fields | Model access goes through the configured task server |
| Task service | `server.baseUrl`, `server.tokenEnv` | Supplies task operations in either model route |
| State | `--state-root`, `OXTORG_LUA_STATE_ROOT`, or `stateRoot` | Selects persistent agent state according to the owner's precedence |

Keys come from the named environment variables. The JSON stores their names.
Role files and relative paths resolve from the configuration directory.
Direct model routing still uses the task server for task execution.

## Spark extensions and optional native features

| Extension | Contract |
| --- | --- |
| Configured Lua module | `config.modules` entry loads a module with `install(context)` |
| Local Lua tool | `luaTools` loads a trusted file returning `function(args)` |
| In-process MCP adapter | Configuration selects an installed `module` |
| External MCP service | Configuration uses `command` or `url` |
| Optional Bluetooth/OBD | Cross build selects `SPARK_ENABLE_BLUETOOTH`; `no` excludes their registration |

An in-process extension inherits the agent process's access.
The portable agent bundle does not itself install the separate workshop Lua interpreter.
Read the extension contract before choosing process and trust boundaries.

These names are specific to Spark. They are not a universal EmberBSD plugin API.
The separate [Runtime](../modules/runtime.md) still has no implemented application configuration contract.

Read [Spark runtime and extensions](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/runtime.md)
and [cross-build options](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/cross-build.md)
for complete fields, defaults, validation, and execution commands.
