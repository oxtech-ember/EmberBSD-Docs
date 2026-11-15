# Package and SDK configuration

[Configuration](README.md) · [Ports](../modules/ports.md) · [SDK](../modules/sdk.md)

Keep package selection separate from toolchain and target selection.
Package roots describe the requested functionality.
The selected configuration supplies host identity, target ABI, prefixes, and sysroots.

## Understand package sets

Ports stores sets in `profiles/package-sets/*.txt`.
Each non-comment line is a versionless package root.
For example, the `sdk-c` set contains:

```text
lang/clang@native
lang/llvm@native
devel/lld@native
```

These are host tools. Select the target application sysroot separately.
The `plasma` set contains `sysutils/ember-plasma-session`.
Its recipe declares dependencies; the set does not duplicate the graph.

Inspect available sets from an SDK source checkout:

```sh
python3 scripts/packages.py --ports-root /path/to/EmberBSD-Ports list
python3 scripts/packages.py --ports-root /path/to/EmberBSD-Ports show --set sdk-c
```

Replace the example path with your Ports checkout.
These source-catalog operations inspect names and roots without requiring bmake or an installed SDK.
They do not prove binary availability or perform a build.

## Client configuration

The SDK client's JSON schema is `emberbsd.sdk-client.v1`.
Use the owner's complete example or the configuration created by its supported bootstrap.

| Field | Purpose |
| --- | --- |
| `host_prefix` | Native tools and packages |
| `repositories` | Exact host/target identities and installation destinations |
| `cache_dir` | Shared package archive cache |
| `state_dir` | Client state |
| `url` | Signed package-channel location |
| `source` | Optional Ports checkout, exported pkgsrc, make configuration, and persistent source-build state |

The JSON is installation configuration; it does not define new package versions.
Platform identity must match the selected catalog and execution host where required.
HTTPS trust and package-signing trust serve different purposes.

With an existing valid client configuration, inspect the selection before installation:

```sh
python3 scripts/packages.py --config /path/to/client.json plan --set sdk-c --target aarch64
```

This example requires a compatible configured catalog and target artifact.
It can access the network and client cache. It does not install the selection.
Use the owner's documented `install` command when that plan matches the intended setup.

## Source builds and installed updates

Explicit source `plan` and `build` use `BMAKE`, `MAKECONF`, `PKGSRCDIR`, `--host-prefix`, and persistent `--state`.
The source graph installs into the selected build prefix/sysroot, not a board.
The native prefix supplies build tools; the target sysroot supplies target headers and libraries.

The client distinguishes adding roots with `install` from updating actual installed roots with `update`.
Removing a root from a later selection does not automatically remove an installed package.
An empty selection does no work.

## Read the complete contracts

- [Ports package sets](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/docs/package-selection.md)
- [SDK source selection](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/package-selection.md)
- [SDK client fields and operations](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sdk-client.md)
- [First-install bootstrap](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/bootstrap-client.md)
- [Compiler and tool selection](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/tool-selection.md)
