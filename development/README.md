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
| Embedded QML rendering, input handling or display integration | [Runtime](https://github.com/oxtech-ember/EmberBSD-Runtime/tree/main/qml) |
| Standalone application or demonstration | Its repository or [Examples](https://github.com/oxtech-ember/EmberBSD-Examples) |
| Task agent, model adapter, or agent-local tool | [Spark](https://github.com/oxtech-ember/EmberBSD-Spark/blob/main/CONTRIBUTING.md) |
| Assistant scenario instructions | [Developer skills](https://github.com/oxtech-ember/Ember-Agent-Skills) or [user skills](https://github.com/oxtech-ember/EmberBSD-User-Skills) |

## Documentation ownership

Use this guide for concepts, responsibilities, interactions and configuration choices.
Source code is also documentation: exact APIs, formats, commands, error handling and executable checks belong to their implementation owner.
Hardware facts and scoped support evidence belong to DevBoards. Skills select a task and link to these owners.
Keep one current description instead of copying a manual, hardware table or test report into the guide.

When moving material, identify live links, build inputs, tests and pinned consumers first.
Publish and check the destination before switching consumers and removing the obsolete page.
Preserve authorship, licenses and immutable evidence; a new path is not a new build or device test.
Keep historical commit links when they establish provenance, and update live links to the current owner.
Use [the contribution rules](../CONTRIBUTING.md#separate-concepts-from-implementation) when maintaining this guide.
The OS [contribution guide](https://github.com/oxtech-ember/EmberBSD/blob/main/CONTRIBUTING.md#documentation-ownership)
defines its source-directory boundaries.

## A first application change

Start with the program that should run on the device. The SDK prepares the
native build tools and a sysroot for the chosen target. Ports supplies the
recipes for the compiler, tools and package dependencies. The OS supplies
the target's system headers, startup objects and libc. A generator runs on
the development host; the cross-compiler links its output against the target
sysroot. Only the resulting program and its runtime dependencies need to go
into the device image. [SDK and toolchain](../modules/sdk.md) ·
[Package selection](../configuration/packages-and-sdk.md)

Choose the application layer to suit the interface:

- A console command or background service can call BSD interfaces directly
  and link the libraries it needs.
- For a small display with a bounded set of screens and touch interactions,
  [EmberBSD QML](../modules/runtime.md) lets the author describe a scene in QML
  and compile it into the application. The board runs the renderer and the
  application's event loop.
- An application that uses Qt Quick controls, imports or other features beyond
  the embedded subset needs Qt Quick and its target dependencies. The
  [weather example](https://github.com/oxtech-ember/EmberBSD-Examples/tree/main/desktop/mosaico-weather)
  illustrates both approaches and a third option for compact displays, LVGL with widgets defined in C.

Choose an [example](https://github.com/oxtech-ember/EmberBSD-Examples) with
similar inputs and outputs. Check application logic on the host, then exercise
the device interfaces and resource use on the intended hardware.

## A subsystem or driver change

Start with [driver architecture](../modules/drivers.md) for the shared lifecycle and resource model.
Use [buses and device discovery](../modules/buses-and-discovery.md) to choose attachment, controller or peripheral work.
Find the common subsystem contract and its existing consumers.
Separate shared behavior from chip operations and board properties.
Keep general protocol/state logic with the subsystem, then attach hardware callbacks.
Update affected consumers and configuration when the interface changes.
Keep detailed API and resource-lifetime documentation beside the implementation.

For an Ethernet controller, PHY, or USB adapter, read the [Ethernet architecture](../modules/ethernet.md)
and the [driver interface map](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/net/docs/ethernet.md).
Choose between extending a common MAC core, adding a bus attachment, implementing PHY operations, and using `usbnet`.

For a Bluetooth controller or peripheral, read the [architecture and profile boundaries](../modules/bluetooth.md)
and the [controller/profile interface map](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/netbt/docs/README.md).
Choose transport, common protocol or profile work; the chapter labels missing capabilities and proposed solutions separately.

For a GPU or display adapter, start with the [graphics architecture](../modules/graphics-and-compute.md)
and the [kernel/userspace interface map](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/external/bsd/drm2/drm/docs/README.md).
Choose the render, display, or compute result before selecting the required components.

For an NPU or inference backend, read the [NPU architecture and technologies](../modules/npu.md)
and the [native/runtime interface map](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/arch/arm/sunxi/docs/npu-integration.md).
Match the runtime, compiler and device contract; distinguish the existing fixed task from a general application interface.

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
