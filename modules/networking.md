# Networking

[Modules](README.md) · [Service configuration](../configuration/services-and-applications.md)

The network stack supplies sockets, packet processing, routing, and network interfaces.
Drivers connect these interfaces to Ethernet, wireless, or other transports.
Userland services configure addresses and wireless authentication.
Applications use sockets or a library for their selected application protocol.

## Wireless responsibilities

| Layer | Responsibility |
| --- | --- |
| Common wireless subsystem | Shared net80211 state, checks, and driver interfaces |
| Userland security service | Authentication and key-management operations defined by its protocol contract |
| Radio driver and firmware | Bus transport, firmware ABI, hardware operations, and declared offload |
| Board description | Power, reset, bus wiring, and device properties |
| Network configuration | Credentials, addressing, routes, and service startup |

Host and firmware must agree on the owner of keys, events, and protocol state.
Offload capabilities differ between radios. Read the driver's security and firmware limits.
A generic wireless API does not establish support for every security mode on every device.

## Configuration

Observe actual interface names before configuring them.
Use the installed network tools and the applicable radio guide.
Keep persistent service choices in `/etc/rc.conf` and network-specific settings in their service configuration.
Credentials belong to the local installation.

Optional application protocols such as robotics messaging belong to packages and applications.
They do not become requirements of the base network stack.

## Read the owner

- [Installed-system network setup](https://github.com/oxtech-ember/EmberBSD/blob/main/doc/guide/user-guide.md)
- [net80211 contracts](https://github.com/oxtech-ember/EmberBSD/tree/main/sys/net80211/docs)
- [bwfm SAE integration](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/ic/docs/bwfm-sae.md)
- [aicwf SAE integration](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/sdmmc/docs/aicwf-sae.md)
- [Application examples](https://github.com/oxtech-ember/EmberBSD-Examples)

Verify link, authentication, address assignment, and the intended traffic separately.
Ethernet success does not establish wireless or Bluetooth support.
