# Driver architecture

[Modules](README.md) · [Hardware and boot](hardware.md) · [Development](../development/README.md)

A driver connects a device to an existing subsystem contract.
Its central task is to control when hardware and software may use each resource.
This page explains the common lifecycle and ownership model, then links to the exact interfaces.
It does not define a new driver API or a state machine shared by all drivers.
Detach, power management and recovery support must be checked in the selected implementation.
Hardware applicability remains in [DevBoards](https://github.com/oxtech-ember/EmberBSD-DevBoards).

## Divide the responsibilities

| Layer | Responsibility |
| --- | --- |
| Common subsystem | Protocol state, validation, shared request queues, scheduling and retry policy; the interface seen by consumers |
| Bus attachment | Match the device and obtain register, interrupt and DMA resources from its parent |
| Controller adapter | Translate requests into registers, descriptors or firmware messages; report completion and hardware errors |
| Platform | Supply wiring, clocks, power, reset and other device properties |
| Kernel helpers | Provide autoconfiguration, locking, DMA mapping, interrupts, timers and worker threads |

The bus attachment and controller core can be separate parts of one driver.
Keep a reusable chip core independent of the way its registers and interrupts are discovered.
Hardware descriptor rings belong to the adapter; general request policy belongs to the subsystem.
Use the subsystem's helpers before adding a private queue, protocol parser or recovery policy.

For example, [usbnet](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/usb/usbnet.c)
owns common USB Ethernet transfer and interface lifecycle work.
A chip adapter supplies its device operations instead of repeating that lifecycle.

## Lifecycle: attached is not running

These are conceptual stages, not names of kernel constants.

| Stage | What changes |
| --- | --- |
| Discover and match | The parent presents a device and its resources; autoconfiguration selects an attachment |
| Attach | Initialize instance state, acquire resources and connect to the subsystem; keep unfinished paths inaccessible |
| Start | Prepare queues and device state, then admit requests through the subsystem |
| Run | Submit operations and return their resources when completion makes reuse safe |
| Quiesce | Prevent new activity and bring existing device accesses and callbacks to a known stopping point |
| Resume or recover | Restore the state required for new work; re-enable requests only after initialization succeeds |
| Detach | Remove consumers and callbacks, release resources and end the instance lifetime |

Autoconfiguration creates the device instance and calls the attachment.
It does not provide a generic rollback for every resource the driver acquires.
Track which initialization stages succeeded so partial failure can release only those resources.
An attachment message alone does not establish an operational device.

Stopping I/O can preserve resources for restart. Detach releases them permanently.
Suspend must leave the device safe for the selected power transition.
The power management framework coordinates class, driver and bus operations; it does not implement the chip's save/restore sequence.
Support for attachment does not imply support for live removal or suspend/resume.

## Resource ownership

The instance's private state, often called its `softc`, connects resources to their lifetime.
It must remain valid while any IRQ handler, timer, worker or consumer can refer to it.
Allocating memory, holding a reference and having permission to modify a buffer are distinct responsibilities.

| Resource | Ownership question | Condition before release or reuse |
| --- | --- | --- |
| Register mapping | Which attachment acquired it, and which callbacks use it? | No callback can access the mapping; the device is stopped or inaccessible under the bus removal contract |
| Request and payload | Who may finish, cancel or retry the operation? | One path owns completion; all other paths have stopped using the request |
| DMA map and descriptors | Can the device still read or write this memory? | Completion, abort or reset has established that access ended; required synchronization is complete |
| IRQ, softint, timer or work item | Who can schedule it, including from another callback? | Scheduling is disabled and pending or executing callbacks are handled before their state is freed |
| Child device or subsystem registration | Which consumers can still reach the driver? | The owning framework has disconnected or drained those consumers |

Release resources in dependency order: stop users before freeing what they use.
A timeout is not evidence that the device stopped using memory.
Retain request state and buffers until completion or the bus/controller's abort or reset contract makes release safe.

## DMA: ownership crosses the CPU/device boundary

Direct memory access (DMA) lets a device access memory without a CPU copying each byte.
The CPU address and the device-visible DMA address need not be the same.
Use the parent's DMA interface and mapped segments; do not derive a device address from a pointer.
The mapping must satisfy the device's address range, alignment and segment limits.
Mapping can fail, so failure must leave the request with a defined owner.

A typical transfer has four ownership boundaries:

1. The CPU prepares the payload and descriptors in memory that will remain valid for the transfer.
2. DMA synchronization makes the required contents visible before the adapter gives the device access.
3. The device uses the published descriptors and buffers. Software does not overwrite or recycle that in-flight storage.
4. Completion establishes which storage is no longer in use. Synchronization then permits CPU access and eventual reuse.

Payloads, descriptor rings and completion rings each have their own access direction and lifetime.
An interrupt is a notification to inspect completion state, not permission to reclaim every outstanding buffer.
CPU locks protect software participants. DMA synchronization handles visibility across the device boundary.
Register access and its ordering use the bus-space interface; these mechanisms do different jobs.

Coherent memory is an optimization, not permission to omit the DMA synchronization contract.
Unloading a DMA map neither performs implicit synchronization nor stops a device.
The [DMA manual](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/bus_dma.9)
defines the exact directional operations and mapping rules.

## IRQs and deferred work

Choose the execution context from the work it must perform.

| Context | Suitable work | Constraint |
| --- | --- | --- |
| Hardware interrupt | Inspect/acknowledge the device cause, make bounded progress, schedule further processing | No sleeping or adaptive mutex acquisition; use synchronization suitable for the interrupt level |
| Software interrupt | Short deferred completion and packet processing | Limited context; no condition-variable waits or waiting for memory/resources |
| Callout | Detect a deadline or request periodic work | Runs at softclock level; move blocking recovery to a worker |
| Worker thread | Operations that need sleepable context, including suitable initialization or recovery work | Still obey the subsystem's locking and lifetime rules |

Softints can use short synchronization supported by their contract; they are not general-purpose worker threads.
A worker does not make waiting safe while holding a spin mutex or a lock needed by the awaited operation.
Declare which lock protects lifecycle changes and which protects each queue before splitting work between contexts.
Follow the existing subsystem's lock order.

Interrupt masking, source acknowledgement and handler removal are different operations.
Masking prevents notifications; it does not drain an executing handler or stop DMA.
The acknowledgement/rearm sequence depends on the controller and interrupt mode.
Do not apply a level-triggered sequence unchanged to every MSI/MSI-X path.

## Queues and completion

A request queue holds work. A hardware ring describes work the device can execute.
A workqueue or softint schedules CPU processing of that work.
These objects do not have interchangeable ownership rules.

Define the handoff: when does submission transfer responsibility, and who retains it if submission fails?
Keep accepted requests represented until they complete or are explicitly failed.
Respect finite queue capacity and the subsystem's flow control instead of creating an unbounded private backlog.
Notifications can be coalesced; the queue or device status must retain the actual work.

Completion, timeout and cancellation can race on different CPUs.
Select one final completion path under the appropriate synchronization.
Prevent it from returning a request or buffer while another path still uses it.
An upper-layer callback can immediately submit new work, so preserve the subsystem's callback and lock-order rules.
An empty software queue does not mean that hardware or another CPU is idle.

## Stop and recover without stale work

Recovery is a coordinated transition with the common subsystem.
The adapter performs the hardware reset; the subsystem decides what failure, retry or session loss means to its consumers.
Use the following dependency model, with the ordering required by the selected bus and controller:

1. Block new submissions and prevent timers, workers and completion callbacks from restarting normal work.
2. Complete or abort outstanding operations and stop device access. Keep any completion path needed by that operation alive until it finishes.
3. Synchronize remaining IRQ and deferred callbacks. Fail or return each accepted request through its owner before freeing its state.
4. For recovery, rebuild queues and device/firmware state. Exclude events from the previous operation or device lifetime before reusing identities.
5. Reopen the subsystem only after restoration succeeds. If recovery fails, keep it unavailable and report the failure.

This is a dependency model, not a universal order of API calls.
Detach ends with resource release; suspend may retain allocations but still requires safe device quiescence.
After physical removal, use the bus teardown path rather than accessing vanished registers.
Request identities or generations help reject stale events only when the completion protocol can distinguish them.
A software generation counter alone does not stop old DMA or make an ambiguous firmware completion safe.

Stopping a producer is also separate from waiting for its callback:

- `callout_stop` can leave a running callback; `callout_halt` provides the wait under its locking contract.
- Before `workqueue_wait`, prevent the work from being queued again. A workqueue must be drained before destruction.
- Before `softint_disestablish`, stop all sources that can schedule it; its implementation waits for execution to finish.

Do not wait while holding a lock required by the callback being drained.
Use bounded hardware waits and the subsystem's failure policy.
Reset must preserve security and protocol guarantees, or explicitly end the affected session; restarting queues alone is insufficient.

## Read the implementation

| Concern | Exact owner references |
| --- | --- |
| Discovery, instance lifetime and detach | [driver(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/driver.9), [autoconf(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/autoconf.9), [autoconfiguration implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/kern/subr_autoconf.c) |
| Register access and DMA | [bus_space(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/bus_space.9), [bus_dma(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/bus_dma.9), [ARM32 DMA implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/arch/arm/arm32/bus_dma.c) |
| Locks and thread waits | [mutex(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/mutex.9), [condvar(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/condvar.9) |
| Interrupts and deferred callbacks | [PCI interrupts](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/pci_intr.9), [softint contract](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/softintr.9), [softint implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/kern/kern_softint.c), [callouts](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/callout.9) |
| Worker threads | [workqueue(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/workqueue.9), [implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/kern/subr_workqueue.c) |
| Power transitions | [pmf(9)](https://github.com/oxtech-ember/EmberBSD/blob/main/share/man/man9/pmf.9), [implementation](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/kern/kern_pmf.c) |

Use these source examples for the named interaction, not as universal driver templates:

- [usbnet](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/usb/usbnet.c): common transfer ownership and preventing callback rescheduling during stop/detach.
- [wm](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/pci/if_wm.c): bounded queue processing, stopping flags and watchdog work in thread context.
- [NVMe](https://github.com/oxtech-ember/EmberBSD/blob/main/sys/dev/ic/nvme.c): submission/completion rings, DMA visibility and callback handoff. Its polled timeout path still notes unresolved controller-reset handling; it is not a complete recovery model.

For a new adapter, first select the subsystem contract and the closest bus attachment.
Describe resource ownership and failure boundaries beside the implementation, then connect hardware operations to that contract.
Check partial initialization, queue exhaustion, timeout/completion races, stop under load and the reset/removal paths the driver supports.
Use the [subsystem chapters](README.md) for protocol-specific responsibilities and the [development guide](../development/README.md#understand-the-evidence) for validation scope.
