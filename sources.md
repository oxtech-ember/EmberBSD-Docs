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
[`f49cf0668349`](https://github.com/oxtech-ember/EmberBSD/tree/f49cf06683492c867e36c6d1e29ff763a4efc609).
The review covers net80211 headers, native frame/association/security paths, station-management and aggregation contracts,
the BSD supplicant backend/build profiles, native rtw88 integration, and bwfm/aicwf firmware adapters.
The follow-up checked the experimental iwx key replacement, firmware TX counter initialization,
PMF/queue policy and restricted HE/EHT paths. It distinguishes implemented mechanisms,
the TX-offload design and proposed common extensions.
The November 17 update checked pairwise TX ownership against the [implemented native SAE provider contract](https://github.com/oxtech-ember/EmberBSD/blob/674ecb28d6d5/sys/net80211/docs/sae.md#firmware-tx-providers).
Pinned Linux iwlmld sources, mac80211/cfg80211 documentation and FreeBSD net80211 crypto inform
the design; their behavior is not evidence of EmberBSD hardware support.
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

## Driver architecture review

The [driver architecture chapter](modules/drivers.md) was checked on 2026-11-15 against EmberBSD
[`379a9dcfe484`](https://github.com/oxtech-ember/EmberBSD/tree/379a9dcfe4843631379869c56264c0dbedd73fa6).
The review covers autoconfiguration and detach, bus-space/DMA manuals and ARM32 DMA implementation,
mutex and softint constraints, callout/workqueue lifetime, power transitions, usbnet stop/detach,
wm queue/watchdog paths, and native NVMe submission/completion and polled timeout handling.
It separates existing kernel mechanisms from the design obligations of each driver.
The examples establish the named interactions, not complete recovery or hardware support for every adapter.
No OS build, execution or physical-device acceptance result is added.

## Bus and discovery review

The [bus and discovery chapter](modules/buses-and-discovery.md) was checked on 2026-11-15 against EmberBSD
[`e2943bdad622`](https://github.com/oxtech-ember/EmberBSD/tree/e2943bdad62240d6d61eb00eda06dcae9e137427).
The review covers ACPI namespace/resource handling and I2C imports; FDT attachment, providers and child imports;
PCI configuration-space discovery; USB hub/device/interface enumeration; SD/MMC host and SDIO function contracts;
I2C direct/indirect configuration; SPI controller/client interfaces and optional transfer capabilities.
DesignWare I2C ACPI/FDT attachments illustrate reuse of one controller core.
The linked ACPI and Devicetree specifications define terminology and description models, not EmberBSD conformance.
No build, bus interoperability or physical-device acceptance result is added.

## Evidence limits

The initial guide checked documentation, configuration names, command interfaces, and links.
It does not report a new OS build, package release, deployment, or hardware test.
Support records and executable evidence remain with their owners.

Conceptual explanations link to exact commands, configuration, and extension contracts at their owners.
When an implementation changes, update its exact documentation at the owner.
Update this guide when its conceptual explanation or source navigation changes.
Record a new baseline for the affected owner when checking a later revision.
Keep architecture intent, implemented behavior, and tested behavior distinguishable.

## Installation and configuration review

The installation/update model, service configuration and documentation ownership were reviewed on 2026-11-15
against EmberBSD [`343fff8fac54`](https://github.com/oxtech-ember/EmberBSD/tree/343fff8fac548e30560a55a28bd32c68bcfef2c4)
and SDK [`33a7cf5efba4`](https://github.com/oxtech-ember/EmberBSD-SDK/tree/33a7cf5efba4875404802cead6f66e26d5ef554e).
The former OS `doc/guide` material was synthesized into the existing configuration and development structure.
Exact installer procedures remain beside sysinst, which links the immutable historical VM receipts.
Startup, account, SSH and package details link to source manuals; DevBoards remains the hardware owner.
The review checked current guided/expert installation limits and separates the implemented host SDK
from the proposed device Runtime. It adds no build, VM or hardware acceptance result.
