# Graphics and compute

[Modules](README.md) · [Package selection](../configuration/packages-and-sdk.md)

Graphics and accelerated compute cross several module boundaries.
The OS owns device access and synchronization. Packages provide libraries and user interfaces.
Applications choose workloads, models, and user interaction.

## Graphics path

| Layer | Purpose |
| --- | --- |
| Display controller and KMS | Configure display modes and scan out buffers |
| GPU adapter and common DRM | Manage device resources, submissions, memory, and synchronization |
| Userspace graphics libraries | Provide rendering APIs and hardware-specific userspace support |
| Window system or compositor | Organize surfaces, input, and presentation |
| Toolkit and application | Draw the interface and implement application behavior |

A working framebuffer, a rendered frame, and a complete desktop session prove different paths.
Likewise, a nested compositor does not establish direct display operation.
Read each board's evidence before selecting a graphical session.

## Compute path

CPU inference and accelerated inference are separate configurations.
Ports supplies common libraries; an application supplies its model and data preparation.
Kernel adapters execute supported hardware operations through their documented interfaces.
GPU or NPU identification alone does not establish a working inference backend.

Spark can use a compatible model endpoint.
That endpoint can be provided by another service; Spark is not itself the universal accelerator runtime.
Keep the model format, service protocol, and hardware execution contract distinct.

## Read the owner

- [Common DRM contracts](https://github.com/oxtech-ember/EmberBSD/tree/main/sys/external/bsd/drm2/drm/docs)
- [Allwinner hardware interfaces](https://github.com/oxtech-ember/EmberBSD/tree/main/sys/arch/arm/sunxi/docs)
- [Ports graphics integration](https://github.com/oxtech-ember/EmberBSD-Ports/tree/main/profiles/common-graphics)
- [Local inference example](https://github.com/oxtech-ember/EmberBSD-Examples/tree/main/ai/local-inference)
- [Hardware Qt/KWin example](https://github.com/oxtech-ember/EmberBSD-Examples/tree/main/desktop/kwin-frame-check)

The Ports `plasma` set describes package selection.
Its existence does not establish a supported installer option or a complete session on every target.
