# Package and SDK configuration

[Configuration](README.md) · [Ports](../modules/ports.md) · [SDK](../modules/sdk.md)

Keep package selection separate from toolchain and target selection.
Package roots describe the requested functionality.
The selected configuration supplies host identity, target ABI, prefixes, and sysroots.

## Understand package sets

A set names the package roots needed for a task.
Recipes supply the versions and dependencies. The set does not duplicate that graph.
Native roots select host programs; target roots select programs for the device ABI.

A C application development selection needs host compiler tools and a matching target sysroot.
A desktop selection starts from session packages and includes their dependencies.
These selections serve different tasks without requiring separate dependency recipes.

Read the current [package-set definitions](https://github.com/oxtech-ember/EmberBSD-Ports/tree/main/profiles/package-sets)
for their exact contents. Use the [SDK selector](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/package-selection.md)
for catalog inspection and planning commands.
Catalog inspection describes available source selections; it does not establish binary availability or a completed build.

## Client configuration

Client configuration connects package selection to a concrete installation.
It identifies host and target environments, installation destinations, shared storage, and a package channel.
Optional source-build inputs connect missing binaries to the existing Ports producer.
These settings describe where and how to obtain components; they do not define new package versions.
Platform identity must match the selected catalog and execution host where required.
HTTPS trust and package-signing trust serve different purposes.
Use the owner's configuration template and reference for exact fields, defaults, and validation.
Planning lets the user inspect a selection before installation changes its destination.

## Source builds and installed updates

Explicit source builds combine selected package roots with host tools, target inputs, and persistent build state.
Their installation destination is the selected build prefix or sysroot, not a board.
The native prefix supplies build tools; the target sysroot supplies target headers and libraries.

Adding application roots and updating installed components are different user intentions.
Removing a root from a later selection does not automatically remove an installed package.
The client's source and reference define the exact operations and safeguards for each intention.

## Read the complete contracts

- [Ports package sets](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/docs/package-selection.md)
- [SDK source selection](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/package-selection.md)
- [SDK client fields and operations](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sdk-client.md)
- [First-install bootstrap](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/bootstrap-client.md)
- [Compiler and tool selection](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/tool-selection.md)
