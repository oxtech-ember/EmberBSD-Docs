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

## Evidence limits

The initial guide checked documentation, configuration names, command interfaces, and links.
It does not report a new OS build, package release, deployment, or hardware test.
Support records and executable evidence remain with their owners.

Conceptual explanations link to exact commands, configuration, and extension contracts at their owners.
When an implementation changes, update its exact documentation at the owner.
Update this guide when its conceptual explanation or source navigation changes.
Record a new baseline for the affected owner when checking a later revision.
Keep architecture intent, implemented behavior, and tested behavior distinguishable.
