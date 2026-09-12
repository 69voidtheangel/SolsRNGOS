# Sols Hybrid Kernel — Complete Architecture

**Project:** SolsRNGOS  
**Implementation direction:** Rust-first / bare-metal  
**Status:** Architecture specification

> **One Kernel. Two Ecosystems. Unified Hardware.**

This document defines the intended architecture of the Sols Hybrid Kernel. It is a design specification, not a claim that every subsystem is already implemented.

---

## 1. Architectural Goal

SolsRNGOS is designed around one new kernel core rather than running a Linux kernel and a Windows kernel side-by-side as unrelated systems.

The kernel owns the machine. Linux and Windows/NT are execution ecosystems implemented through kernel-facing interfaces, services, compatibility layers, and controlled environments.

The architecture has five major goals:

1. Give the kernel unified ownership of CPU, memory, devices, interrupts, DMA, power, and other resources.
2. Allow Linux and Windows/NT application models to coexist.
3. Allow Linux and NT driver ecosystems to coexist without fighting over hardware.
4. Route applications according to actual capabilities and requirements.
5. Provide a legitimate bare-metal Windows hand-off for software that genuinely requires real Windows.

---

# 2. Complete High-Level Stack

```text
┌──────────────────────────────────────────────────────────────────────────┐
│                              USER SPACE                                  │
│                                                                          │
│ Linux apps │ Windows apps │ Games │ Sols apps │ System tools │ Services │
└────────────────────────────────┬─────────────────────────────────────────┘
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                        APPLICATION ROUTER                                │
│                                                                          │
│ ELF/PE detection │ capability detection │ dependencies │ security       │
│ compatibility path │ driver requirements │ environment selection       │
└───────────────────┬──────────────────────────┬───────────────────────────┘
                    │                          │
                    ▼                          ▼
        ┌──────────────────────┐    ┌─────────────────────────┐
        │ Linux Environment    │    │ Windows / NT Environment│
        │ ELF                  │    │ PE                      │
        │ Linux syscalls       │    │ NT interfaces           │
        │ POSIX/userspace      │    │ Win32/userspace         │
        │ Linux services       │    │ Windows services        │
        └──────────┬───────────┘    └────────────┬────────────┘
                   │                             │
                   └─────────────┬───────────────┘
                                 ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                         SOLS HYBRID KERNEL                               │
│                                  RUST                                    │
│                                                                          │
│ Scheduler │ Processes │ Threads │ Memory │ IPC │ Syscalls │ VFS         │
│ Security │ Namespaces │ Timers │ Power │ Networking │ Logging │ Recovery │
└────────────────────────────────┬─────────────────────────────────────────┘
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                          SOLS DRIVER CORE                                │
│                                                                          │
│ registration │ loading │ dependencies │ capabilities │ signing          │
│ ownership │ resource arbitration │ lifecycle │ isolation │ recovery     │
└────────────────────────────────┬─────────────────────────────────────────┘
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                    SOLS-NATIVE INTEGRATION LAYER                         │
│                                                                          │
│ Linux + NT coordination │ resource translation │ synchronization        │
│ DMA/IOMMU │ power │ device communication │ conflict prevention          │
└────────────────────────────────┬─────────────────────────────────────────┘
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                     HARDWARE ABSTRACTION LAYER                           │
│                                                                          │
│ CPU │ GPU │ RAM │ PCIe │ USB │ Storage │ Network │ Audio │ Input        │
│ Display │ Sensors │ Power │ Thermal │ DMA │ IOMMU │ Interrupts          │
└────────────────────────────────┬─────────────────────────────────────────┘
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                           PHYSICAL HARDWARE                              │
└──────────────────────────────────────────────────────────────────────────┘
```

---

# 3. Boot & Kernel Foundation

The kernel begins before userspace exists.

## Boot

- UEFI/firmware interface
- Bootloader protocol
- Early CPU initialization
- Early memory discovery
- ACPI discovery
- Early console/logging
- Kernel image validation
- Initial page tables
- Transition into the kernel runtime

## CPU

- x86-64 support initially
- CPU feature discovery
- SMP/multicore initialization
- CPU topology
- Per-CPU data
- Interrupt controller setup
- Timers
- High-resolution timers
- CPU power states
- Future architecture abstraction

## Interrupts

- Interrupt descriptor handling
- Local APIC support
- I/O APIC support
- MSI/MSI-X
- Interrupt routing
- Interrupt ownership
- Driver interrupt registration
- Interrupt isolation
- Deferred interrupt processing where appropriate

---

# 4. Scheduler & Process Model

The kernel manages execution regardless of whether a process originated from a Linux or Windows application model.

## Processes

- Process creation/destruction
- Address spaces
- Process IDs/handles
- Parent/child relationships
- Resource accounting
- Isolation
- Permissions

## Threads

- Thread creation/destruction
- Scheduling state
- CPU affinity
- Priority
- Synchronization
- Per-thread context
- Thread-local data

## Scheduler

- Preemption
- Multicore scheduling
- CPU affinity
- Priority classes
- Load balancing
- Latency-sensitive scheduling
- Power-aware scheduling
- Performance accounting

---

# 5. Memory Management

The memory subsystem is shared by the entire kernel architecture.

- Physical memory manager
- Virtual memory manager
- Page allocation
- Page tables
- Address-space management
- Memory mapping
- Protection permissions
- Shared memory
- Copy-on-write
- Memory-mapped files
- Kernel heap
- User heaps through userspace interfaces
- Swap/paging infrastructure
- Memory pressure handling
- NUMA abstraction for future hardware
- DMA-safe memory allocation

Memory isolation is enforced independently of the application ecosystem.

---

# 6. IPC & Synchronization

The kernel provides common communication primitives so environments can communicate without pretending that Linux and Windows process semantics are identical.

- Shared memory
- Pipes
- Events
- Semaphores
- Mutexes
- Signals/events
- Message queues
- Sockets
- Named communication objects
- Handle/object translation
- Cross-environment IPC
- Resource permissions
- Synchronization primitives

Cross-environment terminal piping uses an explicit IPC/data-stream protocol rather than assuming a Linux pipe can directly become a Windows kernel pipe.

---

# 7. System Call & Environment Interfaces

The kernel exposes a stable internal service boundary.

```text
Linux syscall/API request ───────► Linux environment interface
                                        │
Windows/NT request ──────────────► NT environment interface
                                        │
                                        ▼
                              Sols kernel services
                                        │
                                        ▼
                              hardware/resources
```

The goal is not to force Linux and Windows APIs to become identical. Each ecosystem gets the interfaces it expects while both ultimately depend on the same kernel-owned resources.

---

# 8. Linux Environment

The Linux side provides the interfaces required for native Linux software.

### Execution

- ELF loading
- Linux process model
- Linux syscall interfaces
- POSIX interfaces
- Linux userspace
- Linux libraries
- Linux services

### Kernel-facing compatibility

Relevant Linux-style interfaces can be implemented where required by the driver/application ecosystem, including:

- Device model concepts
- PCI
- USB
- DMA
- Interrupt interfaces
- DRM/KMS
- ALSA
- V4L2
- Input/HID
- Networking
- Storage/filesystem interfaces

The exact compatibility surface will be implemented incrementally based on real hardware and software requirements.

---

# 9. Windows / NT Environment

The Windows side provides a compatible NT execution environment within the overall Sols architecture where technically and legally feasible.

### Execution

- PE loading
- NT process model
- NT system interfaces
- Win32 interfaces
- Windows userspace
- Windows libraries
- Services
- COM where required
- Registry-compatible services where required

### NT driver-facing compatibility

The intended compatibility surface includes relevant concepts from:

- NT executive/object management
- NT I/O model
- NT memory interfaces
- Windows device model
- Plug-and-Play
- Power management
- WDM/KMDF-related interfaces where feasible
- Device interfaces
- Hardware IDs
- Driver dependencies

This is a new kernel architecture. It is not a literal combination of `ntoskrnl.exe` and the Linux kernel.

---

# 10. Application Router

The Application Router is designed to be invisible during normal use.

A file extension is only an initial hint.

```text
Application
    │
    ▼
Identify executable format
    │
    ├── ELF ───────────────► Linux path
    │
    └── PE/EXE ────────────► Requirement analysis
                                  │
                                  ├── Native compatibility
                                  ├── Wine/Proton
                                  ├── Windows environment
                                  └── Bare-metal Windows handoff
```

The router considers:

- Executable format
- API requirements
- Libraries
- Services
- Drivers
- Device access
- Privileges
- Security requirements
- Compatibility database
- Environment capabilities
- Performance requirements
- Reboot requirements

The router should prefer the least disruptive compatible path that is technically appropriate.

---

# 11. Sols Driver Core

The Sols Driver Core is the central driver management and coordination subsystem.

### Driver lifecycle

```text
Detect device
    ↓
Identify hardware
    ↓
Match compatible drivers
    ↓
Resolve dependencies
    ↓
Verify source/signature
    ↓
Check compatibility/conflicts
    ↓
Stage
    ↓
User approval if required
    ↓
Load/activate
    ↓
Monitor
    ↓
Recover/update/unload
```

### Driver responsibilities

- Registration
- Loading
- Unloading
- Versioning
- Dependency graph
- Capability declarations
- Hardware matching
- Device ownership
- Resource ownership
- Interrupt ownership
- DMA mappings
- MMIO/I/O ownership
- Power lifecycle
- Isolation
- Crash containment
- Recovery
- Signing/authentication

---

# 12. Linux + NT Driver Coexistence

This is one of the most important architectural rules in SolsRNGOS.

```text
                         SOLS HYBRID KERNEL
                                  │
                           SOLS DRIVER CORE
                                  │
                 ┌────────────────┴────────────────┐
                 │                                 │
          Linux drivers                       NT drivers
                 │                                 │
                 └────────────────┬────────────────┘
                                  │
                       SOLS-NATIVE INTEGRATION
                                  │
                              HARDWARE
```

**Sols-native integration does not replace Linux drivers or NT drivers.**

Instead, it coordinates them through:

- Device ownership
- Resource arbitration
- Driver dependency graphs
- Device synchronization
- Power coordination
- DMA/IOMMU policy
- Hardware capability translation
- Driver-to-driver communication
- Shared device state
- Error handling
- Device reset/recovery

A device cannot be allowed to have two independent drivers simultaneously perform conflicting operations on the same resource. Ownership and arbitration are kernel responsibilities.

---

# 13. Hardware Abstraction Layer

The HAL provides a stable hardware-facing abstraction to upper kernel components.

### CPU
- CPU topology
- Context switching
- Interrupts
- Timers
- Power states

### Memory
- Physical memory
- Virtual memory
- DMA buffers
- IOMMU mappings

### PCI/PCIe
- Enumeration
- BAR/MMIO resources
- Configuration space
- MSI/MSI-X
- Hotplug
- Power/reset

### USB
- Host controllers
- Devices
- Hubs
- HID
- Audio
- Storage
- Networking
- Controllers
- Hotplug/power

### GPU
- Device discovery
- Memory
- Scheduling interfaces
- Display
- Reset/recovery
- Compute/media interfaces

### Storage
- NVMe
- SATA/AHCI
- USB storage
- SD/eMMC
- Block devices
- Partition interfaces

### Network
- Ethernet
- Wi-Fi
- Bluetooth
- Network devices
- Power/hotplug

### Audio
- HDA
- USB audio
- Bluetooth audio
- Microphones
- Speakers
- Headsets
- DSPs
- Virtual audio integration

### Input
- Keyboard
- Mouse
- Touch
- Trackpad
- HID
- Gamepad/controller
- USB/Bluetooth input

### Sensors/Power
- ACPI
- Battery
- Charging
- Thermal sensors
- Fans
- IMU/sensors
- Power profiles

---

# 14. DMA & IOMMU

DMA is treated as a first-class security and resource-management concern.

- DMA buffer allocation
- Mapping/unmapping
- IOMMU domains
- Device memory permissions
- Interrupt remapping
- DMA isolation
- Driver-specific DMA permissions
- GPU/device memory protection
- Safe teardown

The Driver Core and Sols-native integration layer coordinate DMA ownership so multiple driver ecosystems cannot accidentally access each other's mappings.

---

# 15. Security Architecture

Security is built into the kernel rather than being a desktop-only feature.

- User/kernel separation
- Address-space isolation
- Privilege enforcement
- Resource permissions
- Driver authentication
- Module verification
- Secure Boot integration
- IOMMU/DMA isolation
- Process sandboxing
- Compatibility-environment isolation
- Signed binary validation
- Dependency validation
- Auditing
- Security logging
- Crash containment

Security-sensitive application behavior must not be defeated or bypassed. If legitimate compatibility requires real Windows, the architecture uses the Windows environment or bare-metal handoff instead.

---

# 16. Filesystems & Storage Services

The kernel storage stack provides common block-device infrastructure while environments expose the filesystem interfaces they require.

- Block device layer
- Storage queues
- I/O scheduling
- NVMe/SATA/USB/SD support
- Partition interfaces
- VFS
- Filesystem drivers
- File permissions
- File locks
- Memory mapping
- Storage power management
- Hotplug
- Error recovery

Planned filesystem support includes common Linux filesystems and NT-compatible filesystem support where required.

---

# 17. Networking Stack

The kernel provides common networking infrastructure.

- Ethernet
- Wi-Fi
- Bluetooth networking
- TCP
- UDP
- IPv4
- IPv6
- Routing
- Sockets
- Firewall integration
- Network namespaces/isolation
- Driver integration
- Power/hotplug

Linux and Windows application APIs can map onto the common networking implementation through their respective environment interfaces.

---

# 18. Audio Architecture

```text
Linux audio apps ──────┐
                       ├── environment APIs ──┐
Windows audio apps ────┘                     │
                                             ▼
                                      Sols audio services
                                             │
                                      PipeWire bridge
                                             │
                                      Driver framework
                                             │
                                   HDA / USB / Bluetooth
```

The architecture supports:

- Playback
- Capture
- Microphones
- Headsets
- Bluetooth audio
- USB audio
- Virtual audio devices
- Low-latency paths
- DSP integration
- Hotplug
- Power management

This provides a path for applications that need Windows-specific audio features while keeping host audio management centralized.

---

# 19. Power & Thermal Management

- ACPI
- CPU C/P states
- GPU power management
- Device power states
- Battery
- Charging
- Thermal zones
- Fan control interfaces
- Thermal throttling
- Suspend
- Resume
- Hibernate
- Wake
- Handheld power profiles

Power state transitions must be coordinated with driver lifecycle and device ownership.

---

# 20. Device Recovery

The kernel must assume that hardware and drivers can fail.

```text
Driver failure
     ↓
Contain fault
     ↓
Protect other resources
     ↓
Collect diagnostics
     ↓
Attempt driver/device recovery
     ↓
Reset device if supported
     ↓
Reload/rebind if safe
     ↓
Report failure if recovery fails
```

Recovery mechanisms include:

- Driver crash detection
- Device reset
- GPU recovery
- Driver restart where safe
- Failed-device isolation
- Safe mode
- Recovery environment
- Crash reports
- Kernel dumps
- Hardware diagnostics

---

# 21. Logging, Debugging & Development

The kernel needs deep observability from day one.

- Early boot logs
- Serial logging
- Kernel console
- Structured logs
- Debug symbols
- Crash dumps
- Stack traces
- Scheduler tracing
- IPC tracing
- Driver diagnostics
- Hardware enumeration diagnostics
- Memory diagnostics
- Performance counters
- Profiling
- Development/test modes

---

# 22. Kernel ↔ OS Service Boundary

Higher-level OS services should not directly bypass the kernel's ownership model.

```text
                 Desktop / OS Services
                          │
                          ▼
                Kernel Service Interface
                          │
       ┌──────────────────┼──────────────────┐
       ▼                  ▼                  ▼
    Devices            Processes          Security
       │                  │                  │
       └──────────────────┼──────────────────┘
                          ▼
                   SOLS KERNEL CORE
```

Services include:

- Device/driver management
- Process management
- Memory management
- Networking
- Audio
- Graphics
- Storage
- Power
- Compatibility
- Security
- Logging
- Diagnostics

---

# 23. Desktop Independence

The kernel does not depend on KDE, Wayland, X11, or the Sols desktop.

```text
Sols Hybrid Kernel
        ↓
Hardware + Driver Services
        ↓
OS Services
        ↓
Desktop Framework
        ↓
┌────────────┬────────────┬──────────────┐
│ KDE        │ Sols UI    │ Other UIs    │
│ Wayland    │ Custom     │ Community    │
│ X11        │ Desktop    │ Future       │
└────────────┴────────────┴──────────────┘
```

This keeps the kernel reusable across desktop, handheld, education, enterprise, and future device profiles.

---

# 24. Unified Terminal Architecture

The terminal is a userspace application, but the kernel provides the process, IPC, security, and resource primitives required to make it unified.

```text
┌───────────────────────────┬───────────────────────────┐
│ Linux shell               │ PowerShell                │
│ bash / zsh / fish         │ Windows command tooling   │
└──────────────┬────────────┴─────────────┬─────────────┘
               │                          │
               └──────────┬───────────────┘
                          ▼
                 Rust terminal frontend
                          │
                 Controlled IPC protocol
                          │
                Sols kernel IPC services
```

Cross-environment piping is implemented as an explicit data stream rather than assuming the two ecosystems have identical process/pipe semantics.

---

# 25. Windows Smart Reboot / Hybrid Hand-off

Some applications may require a genuine Windows kernel/hardware environment. The fallback is a real reboot, not an attempt to bypass those requirements.

```text
SolsRNGOS
   │
   ▼
Application requirement detected
   │
   ▼
Save Sols session/state
   │
   ▼
Synchronize required files transactionally
   │
   ▼
Set one-time Windows boot target
   │
   ▼
Reboot
   │
   ▼
Windows receives full bare-metal hardware
   │
   ▼
Application runs normally
   │
   ▼
Windows shutdown/reboot
   │
   ▼
SolsRNGOS boots
   │
   ▼
Restore session/state
```

The intended command interface is conceptually:

```text
systemctl switch-to-windows
```

This is a temporary hand-off mechanism, not merely a persistent desktop target selector.

---

# 26. Automatic Driver Acquisition

SolsRNGOS should automatically manage supported hardware while respecting licensing and authorization.

```text
Hardware detected
      ↓
Hardware ID
      ↓
Driver database
      ↓
Compatible Linux / NT / Sols-native support
      ↓
License + source + signature validation
      ↓
Dependency validation
      ↓
Conflict/resource analysis
      ↓
Stage
      ↓
Approval if required
      ↓
Activate
```

Proprietary Windows drivers cannot simply be copied into SolsRNGOS for redistribution without authorization. Supported paths include licensed distribution, user-authorized acquisition, or compatible clean-room/native implementations where legally appropriate.

---

# 27. Compatibility Philosophy

The compatibility stack should be layered rather than forcing every application through one mechanism.

```text
                    Application
                         │
                         ▼
                Capability analysis
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
     Native Linux    Compatibility   Windows
          │            layer          environment
          │              │              │
          └──────────────┼──────────────┘
                         │
              Bare-metal fallback
                 when genuinely needed
```

The system should choose a technically appropriate path based on requirements, not on assumptions about a particular application.

---

# 28. Core Kernel Responsibilities

At minimum, the kernel is responsible for:

### CPU
- Boot
- SMP
- Scheduling
- Context switching
- Interrupts
- Timers
- CPU power

### Memory
- Physical memory
- Virtual memory
- Paging
- Protection
- Shared memory
- DMA memory

### Processes
- Processes
- Threads
- Syscalls
- IPC
- Signals/events
- Handles/objects
- Isolation

### Hardware
- PCIe
- USB
- ACPI
- DMA
- IOMMU
- MMIO
- Interrupts
- Device lifecycle

### Drivers
- Linux driver ecosystem
- NT driver ecosystem
- Sols-native integration
- Ownership/arbitration
- Security
- Dependencies
- Recovery

### Devices
- GPU
- Storage
- Network
- Audio
- Input
- USB
- Sensors
- Power

### System
- VFS
- Networking
- Security
- Logging
- Debugging
- Crash recovery
- Reboot/shutdown

### Environments
- Linux execution
- Windows/NT execution
- Cross-environment IPC
- Application routing
- Compatibility services
- State hand-off

---

# 29. What This Architecture Is Not

- It is not a literal merge of `ntoskrnl.exe` and the Linux kernel.
- It is not Linux plus a Windows VM pretending to be one kernel.
- It is not a mechanism for bypassing anti-cheat or security software.
- Sols-native drivers do not replace Linux or NT drivers.
- `.exe` detection alone is not enough to decide an application's execution path.
- Proprietary driver redistribution is not assumed to be legal.

---

# 30. Implementation Order

The intended implementation order is:

```text
Boot
 ↓
CPU + interrupts
 ↓
Memory
 ↓
Scheduler
 ↓
Processes/threads
 ↓
IPC + syscalls
 ↓
PCIe/ACPI/DMA/IOMMU
 ↓
Sols Driver Core
 ↓
Basic Linux driver environment
 ↓
Basic NT driver environment
 ↓
Sols-native integration
 ↓
Storage/network/input/audio/display
 ↓
Linux userspace
 ↓
Windows/NT environment
 ↓
Application Router
 ↓
OS services
 ↓
Desktop
```

The kernel should be tested continuously in virtual machines and on real hardware, with the Steam Deck as an early hardware target.

---

# 31. Long-Term Expansion

The architecture is intended to leave room for:

- More CPU architectures
- More Linux APIs
- More NT/Windows interfaces
- More filesystems
- More hardware ecosystems
- Advanced virtualization interfaces
- Android compatibility
- Additional handheld support
- Enterprise hardware
- Education hardware
- Future GPU architectures
- New driver ecosystems

The core principle remains unchanged:

> **One Sols kernel owns the machine; multiple application ecosystems operate through controlled, compatible interfaces.**
