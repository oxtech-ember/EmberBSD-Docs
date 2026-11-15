# Sources and review baseline

[Guide](README.md)

This guide was checked against published sources on 2026-11-15.
The table records the source revisions used for the initial explanation.
Page links normally follow `main` so readers can find the current owner contract.
Use these pinned revisions when comparing the guide with its original baseline.

| Owner | Reviewed revision | Main material used |
| --- | --- | --- |
| EmberBSD | [`4b0a7d3299c6`](https://github.com/oxtech-ember/EmberBSD/tree/4b0a7d3299c6f5e287e50730d4c8cc7f241175ab) | README, ownership, contribution guide, `EMBER64`, user guide, littlefs contract, and subsystem entry points |
| Ports | [`038f206fd08f`](https://github.com/oxtech-ember/EmberBSD-Ports/tree/038f206fd08f4422139029c3c092f3e8821ce737) | README, package selection, and package-set definitions |
| SDK | [`e73222fdba3c`](https://github.com/oxtech-ember/EmberBSD-SDK/tree/e73222fdba3cfc582e84686573f88e35c240d65e) | README, tool selection, package selection, client configuration, and selector implementation |
| DevBoards | [`81f916b8a45d`](https://github.com/oxtech-ember/EmberBSD-DevBoards/tree/81f916b8a45d545848fc3c130f42ecc2e4055edd) | Reader entry points, hardware ownership, and catalog structure |
| Spark | [`4f4f91e2e018`](https://github.com/oxtech-ember/EmberBSD-Spark/tree/4f4f91e2e01881ba933ea7fa5d64da42de600aeb) | README, runtime/extensions, cross-build guide, and build options |
| Runtime | [`4eb00179fae3`](https://github.com/oxtech-ember/EmberBSD-Runtime/tree/4eb00179fae3d79c69acc86476a567d89b50b9da) | Purpose and explicit design-stage boundary |
| Examples | [`fe58ba7dad1a`](https://github.com/oxtech-ember/EmberBSD-Examples/tree/fe58ba7dad1a98ae23b95c1bd144f75bd22880a9) | Application catalog and ownership |

The guide summarizes these sources in original prose.
Source code is also documentation; exact interfaces and configuration references remain with their implementation owners.
Each source repository retains its own licensing and attribution.
New guide text uses [BSD-2-Clause](LICENSE).

## Wi-Fi review

The [Wi-Fi chapter](modules/wifi.md) was checked on 2026-11-15 against EmberBSD
[`4460bde0ec74`](https://github.com/oxtech-ember/EmberBSD/tree/4460bde0ec74f5479fca38dc3b1a44172b2a63a6).
The review covers net80211 headers, native frame/association/security paths, station-management and aggregation contracts,
the BSD supplicant backend, native rtw88 integration, and bwfm/aicwf firmware adapters.
It distinguishes implemented protocol mechanisms from optional provider integration.
Hardware applicability and physical acceptance remain in DevBoards; no hardware results are copied here.

## Graphics and compute review

The [graphics and compute chapter](modules/graphics-and-compute.md) was checked on 2026-11-15 against:

| Owner | Reviewed revision | Material used |
| --- | --- | --- |
| EmberBSD | [`8ae54fdfcf56`](https://github.com/oxtech-ember/EmberBSD/tree/8ae54fdfcf56eed96c4466b7d1fec12bc452e0c8) | DRM device/GEM/KMS interfaces; native identity, submission, syncobj, DMA-BUF, import/mmap and hotplug contracts; V3D, PowerVR, display, VirtGPU and NPU integration boundaries |
| Ports | [`865fa793bbe6`](https://github.com/oxtech-ember/EmberBSD-Ports/tree/865fa793bbe677d705ba40e304a948ee99a15f83) | Shared Mesa recipe/options, libdrm and Vulkan loader ownership, native generators, device/input integration, Qt bsdfb offscreen presentation, and UI measurement |

The review separates API availability, backend selection, buffer sharing, hardware execution, and presentation.
Upstream Mesa and Khronos references explain API semantics, not EmberBSD device acceptance.
Hardware support records remain in DevBoards; no new GPU execution or conformance result is claimed.

## Ethernet review

The [Ethernet chapter](modules/ethernet.md) was checked on 2026-11-15 against EmberBSD
[`d0b62867b447`](https://github.com/oxtech-ember/EmberBSD/tree/d0b62867b4479ecf126f6e1ad795c15471e15f90).
The review covers interface/Ethernet lifecycle, packet queues and capabilities, MII/MDIO helpers,
GENET and EQOS controller paths, wm registration/offloads, PHY adapters, usbnet consumers, VirtIO,
VLAN/bridge/lagg interfaces, and the enabled wired supplicant backend.
IEEE references identify standards; source interfaces and driver behavior define the implementation boundary.
No new controller build, physical-link test, or standards conformance result is claimed.

## NPU review

The [NPU and inference chapter](modules/npu.md) was checked on 2026-11-15 against:

| Owner | Reviewed revision | Material used |
| --- | --- | --- |
| EmberBSD | [`049b6f9e2754`](https://github.com/oxtech-ember/EmberBSD/tree/049b6f9e27543300e969c2ee848d6792db646af8) | Native NPU attachment and VIPLite subset, fixed NN task, host fixture generation, MMU/DMA ownership, frontend/run completion, clock/power contracts, and executable source checks |
| Ports | [`865fa793bbe6`](https://github.com/oxtech-ember/EmberBSD-Ports/tree/865fa793bbe677d705ba40e304a948ee99a15f83) | ONNX Runtime features/options, LiteRT shared sources and CPU profiles, Compass userspace probe, Mesa Teflon selection, and openTPU tools |

Upstream ONNX, ONNX Runtime, LiteRT and Mesa references explain formats and extension mechanisms.
The review distinguishes a source package, runtime backend, native transport and executed workload.
It establishes no new arbitrary-model support, hardware execution, benchmark or standards-conformance result.
Hardware applicability and physical acceptance remain in DevBoards.

## Bluetooth review

The [Bluetooth chapter](modules/bluetooth.md) was checked on 2026-11-15 against:

| Owner | Reviewed revision | Material used |
| --- | --- | --- |
| EmberBSD | [`73529b6e94a8`](https://github.com/oxtech-ember/EmberBSD/tree/73529b6e94a8f004ef148928cc8857db03db697e) | HCI/L2CAP/RFCOMM/SCO contracts and implementations; USB/UART adapters; controller/firmware setup; pairing helpers and key service; HID, SPP, PAN and audio profiles; startup and focused checks |
| Ports | [`a2d6d55e841b`](https://github.com/oxtech-ember/EmberBSD-Ports/tree/a2d6d55e841b1ab4223198028a9f5ac5d56da30c) | BluezQt/Bluedevil client boundaries and the native HCI recorder |

Bluetooth SIG references define terminology and standard/profile roles, not EmberBSD conformance.
The proposed extension architecture compares primary FreeBSD, Zephyr, Apache NimBLE and BlueZ documentation, reviewed on the same date.
Those upstream implementations are design references, not installed EmberBSD capabilities.
No new build, pairing, profile-interoperability or physical acceptance result is claimed.

## Evidence limits

The initial guide checked documentation, configuration names, command interfaces, and links.
It does not report a new OS build, package release, deployment, or hardware test.
Support records and executable evidence remain with their owners.

Conceptual explanations link to exact commands, configuration, and extension contracts at their owners.
When an implementation changes, update its exact documentation at the owner.
Update this guide when its conceptual explanation or source navigation changes.
Record a new baseline for the affected owner when checking a later revision.
Keep architecture intent, implemented behavior, and tested behavior distinguishable.
