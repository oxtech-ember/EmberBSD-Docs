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
The package version, target ABI, and recorded build inputs serve different purposes.

Binary client operations and source builds have different prerequisites.
First installation uses a supported bootstrap path.
Signing and publication are maintainer operations, separate from ordinary package selection and installation.

## Build architecture

| Layer | Owner | Decides |
| --- | --- | --- |
| Profile | SDK format, task author | Platform and OS composition |
| Plan | SDK | Selected stages, their input keys, and `ready` or `build` for each stage |
| Execution | Ninja | Order and parallel work within one jobs budget |
| Stage | OS, Ports | Compilation and installation inside the stage |

The OS and Ports supply the dependency facts. The SDK does not describe a dependency a second time.
A stage is `ready` while its input key matches and all its outputs exist.
After a failure or an interruption, the next run builds only the stages that are not ready.
Inside the OS stage, bmake rebuilds the objects that read a changed OS file.
Changed prepared sources, tools, or `MK*` options give a new key and new object directories.

| Output | Entry point |
| --- | --- |
| Clang kernels and development sets | `profile-plan` and `profile-build` |
| Application sysroot | sysroot producer |
| Release kit | OS release procedure |
| Ports packages | package client `install`, outside the profile |

A build that uses a host prefix or a sysroot reads it.
A write into a prefix that another build reads stops before its first change.
The installs of the reading build continue.
Ports package inputs do not include the sysroot:
see [revision-only changes](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/profiles/common-build-tools/cross/README.md#revision-only-changes)
for a rebuild after an OS ABI change.

## Current boundary

Host SDK tools exist. Supported host operations differ; consult the host-platform contract.
The optional QML compiler emits C scene data for the [embedded renderer](runtime.md).
The stable general-purpose application API and application package contract remain design work.
The name SDK therefore does not imply an implemented universal device API.

## Read the owner

- [SDK overview](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/README.md)
- [First-install bootstrap](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/bootstrap-client.md)
- [Host platforms](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/host-platforms.md)
- [Tool selection](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/tool-selection.md)
- [Build profiles](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/build-profile.md)
- [Build workflow](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/build-workflow.md)
- [Prefix readers and writers](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sdk-client.md#cache-and-interruption)
- [Input keys](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/input-keys.md)
- [Sysroot producer](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sysroot.md)
