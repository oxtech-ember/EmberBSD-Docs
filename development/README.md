# Develop a component

[Guide](../README.md) · [Glossary](glossary.md)

Start with one observable result and the interface that owns it.
Use this page to find the development path; use the receiving repository's current contribution procedure for the change.

## Select an owner

| Change | Destination |
| --- | --- |
| Cross-project concept, module explanation, or reading order | This guide |
| Kernel, base command, driver, loader, or OS-native build | [EmberBSD](https://github.com/oxtech-ember/EmberBSD/blob/main/CONTRIBUTING.md) |
| Board facts, wiring, loader applicability, or support evidence | [DevBoards](https://github.com/oxtech-ember/EmberBSD-DevBoards/blob/main/CONTRIBUTING.md) |
| Shared dependency, compiler recipe, or portability patch | [Ports](https://github.com/oxtech-ember/EmberBSD-Ports) |
| Host orchestration, sysroot, package client, or release tool | [SDK](https://github.com/oxtech-ember/EmberBSD-SDK) |
| Standalone application or demonstration | Its repository or [Examples](https://github.com/oxtech-ember/EmberBSD-Examples) |
| Task agent, model adapter, or agent-local tool | [Spark](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/CONTRIBUTING.md) |
| Assistant scenario instructions | [Developer skills](https://github.com/oxtech-ember/Ember-Agent-Skills) or [user skills](https://github.com/oxtech-ember/EmberBSD-User-Skills) |

## A first application change

Select an example close to the intended workload.
Read its prerequisites, target, build inputs, and checks.
Obtain the required native tools and target dependencies through SDK/Ports.
Build against the matching target sysroot, then run the example on its declared execution platform.
Extend one behavior and check the result through the application's interface.

Application development can use current OS/library interfaces without waiting for the planned Runtime.
A hardware-independent fixture is useful for logic; device access still needs the appropriate target check.

## A subsystem or driver change

Find the common subsystem contract and its existing consumers.
Separate shared behavior from chip operations and board properties.
Keep general protocol/state logic with the subsystem, then attach hardware callbacks.
Update affected consumers and configuration when the interface changes.
Keep detailed API and resource-lifetime documentation beside the implementation.

For a GPU, display adapter, or accelerator, start with the [graphics architecture](../modules/graphics-and-compute.md)
and the [kernel/userspace interface map](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/external/bsd/drm2/drm/docs/README.md).
Choose the render, display, or compute result before selecting the required components.

## Understand the evidence

| Check | What it establishes |
| --- | --- |
| Source and documentation inspection | The named interface and integration path exist |
| Build | The declared inputs produce an artifact |
| Software contract test | A specified behavior works under the test conditions |
| VM execution | The stated guest scenario runs |
| Physical-device test | The stated operation works on the named hardware and configuration |
| Sustained workload | The operation holds for the recorded workload and duration |

Choose checks for the changed behavior and its consumers.
Reuse valid evidence for unchanged inputs.
Have an independent reviewer inspect the change and its claims before integration under the owner's process.
Update this guide when the explanation changes; keep detailed results with the implementation or hardware owner.

## Follow the source

Use [module pages](../modules/README.md) to reach the relevant contract.
Use [source baselines](../sources.md) to identify the revisions reviewed for this guide.
Report an implemented source change, a merged change, and a distributed release as separate states.
