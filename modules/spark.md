# Spark agent

[Modules](README.md) · [Application configuration](../configuration/services-and-applications.md)

Spark is an optional C99/Lua agent application.
C adapters provide system operations. Lua implements task execution, model requests, MCP, and local tools.
The base OS remains independent of Spark and its service providers.

## Internal modules

| Part | Responsibility |
| --- | --- |
| Native adapters | HTTP, files, processes, equipment, and optional Bluetooth/OBD operations |
| Task execution | Queue processing, task attempts, saved state, and result handling |
| Model adapter | Responses-compatible requests and reported usage |
| Tool registry and MCP | Discover and invoke configured tools through declared interfaces |
| Lua extensions | Installation-owned behavior and local task policy |

Native adapter APIs and their version identifiers are documented with Spark's implementation.
Those sources define the exact interface exposed to Lua extensions.

## Dependencies and configuration

Spark uses the common Lua, dkjson, and libcurl dependencies.
The current task loop also needs a compatible task server supplied separately.
Direct model access changes the model route; it does not remove the task-server dependency.
The local example and test fixture can run without that external service or a model account.

JSON selects endpoints, model routing, state, and extensions.
Configured Lua extensions connect to the agent's application context.
Trusted in-process extensions run with the agent process's access.
They are not isolated by the planned Runtime.

Bluetooth and OBD adapters are optional build-time features with their own target dependencies.
Use Spark's cross-build reference for the exact selection interface.
MCP configuration selects installed adapters or external services; it does not add missing native hardware support.

## Current boundary and sources

The small host is synchronous and does not implement the complete browser or subagent stack.
Endpoint compatibility, target packaging, and physical equipment operations need their own checks.

- [Spark overview](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/README.md)
- [Runtime, state, and extension contracts](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/runtime.md)
- [Cross-build options](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/cross-build.md)
- [Testing boundaries](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/docs/testing.md)
