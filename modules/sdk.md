# SDK and the development host

[Modules](README.md) · [SDK configuration](../configuration/packages-and-sdk.md)

The implemented Prebuild SDK prepares host tools, target sysroots, build state, and release artifacts.
It connects the OS and Ports producers.
It does not own another compiler recipe or a separate libc implementation.

## Components

| Component | Responsibility |
| --- | --- |
| Native tool prefix | Host compilers, build tools, interpreters, and generators |
| Target sysroot | Matching target headers, libraries, startup objects, and package metadata |
| Package client | Signed catalog access, binary selection, installation, and source-build delegation |
| Build workspace | Reusable objects, package state, and artifacts for a target |
| Release tools | Compose, verify, sign, and publish declared artifact inventories |

Host identity, target ABI, compiler family, and package roots are separate inputs.
Clang/LLVM is the default compiler family; a platform can select GCC explicitly.
Native generators use host tools. Target programs use the selected target and matching runtime.
Kernel-only and freestanding work use their producer inputs without requiring an application sysroot.

## Reuse and delivery

Start with the tools and dependencies needed by the task.
Reuse compatible installed packages, shared caches, and build outputs.
The SDK's input-key contract determines artifact compatibility.
A matching version string or directory name alone does not establish equivalent inputs.

Binary client operations and source builds have different prerequisites.
First installation uses a supported bootstrap path.
Signing and publication are maintainer operations, separate from ordinary package selection and installation.

## Current boundary

Host SDK tools exist. Supported host operations differ; consult the host-platform contract.
The planned stable application API, application package contract, and device Runtime remain distinct work.
The name SDK therefore does not imply an implemented universal device API.

## Read the owner

- [SDK overview](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/README.md)
- [First-install bootstrap](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/bootstrap-client.md)
- [Host platforms](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/host-platforms.md)
- [Tool selection](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/tool-selection.md)
- [Input keys](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/input-keys.md)
- [Sysroot producer](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sysroot.md)
