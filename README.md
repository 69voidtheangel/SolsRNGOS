# SolsRNGOS

> **One Kernel. Two Ecosystems. Infinite Possibilities.**
>
> A Rust-first operating system designed to bring Linux and Windows software together under one unified system architecture.

**Status:** Architecture / kernel-first development

**Target:** SolsRNGOS 0.1

---

## What is SolsRNGOS?

SolsRNGOS is a planned operating system built from the ground up around a **Sols Hybrid Kernel**.

The goal is not to place Linux and Windows next to each other and make the user manually choose between them. The goal is to build one kernel and one resource-management architecture capable of hosting both Linux and Windows/NT execution environments while presenting a unified experience to the user.

Applications should be routed automatically to the environment and compatibility path they actually require.

```text
                         USER
                           │
                           ▼
                  ┌──────────────────┐
                  │ Applications     │
                  │ Linux / Windows  │
                  │ Games / Sols     │
                  └────────┬─────────┘
                           │
                           ▼
                  ┌──────────────────┐
                  │ Application      │
                  │ Router           │
                  └────────┬─────────┘
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
        ┌────────────────┐   ┌────────────────┐
        │ Linux          │   │ Windows / NT   │
        │ Environment    │   │ Environment    │
        └───────┬────────┘   └───────┬────────┘
                │                    │
                └──────────┬─────────┘
                           ▼
                ┌─────────────────────┐
                │ SOLS HYBRID KERNEL  │
                │       (Rust)        │
                └──────────┬──────────┘
                           ▼
                ┌─────────────────────┐
                │ Sols Driver Core    │
                │ + Native Integration│
                └──────────┬──────────┘
                           ▼
                ┌─────────────────────┐
                │ Hardware Abstraction│
                │ Layer               │
                └──────────┬──────────┘
                           ▼
                     PHYSICAL HW
```

---

## Core Principles

- **Rust-first:** kernel and core OS architecture are designed around Rust for memory safety, performance, and low-level control.
- **One kernel:** Linux and Windows/NT are execution ecosystems, not two unrelated kernels glued together.
- **Driver coexistence:** Linux drivers and NT drivers coexist. Sols-native drivers do not replace them; they integrate and coordinate them.
- **Unified hardware ownership:** devices, interrupts, DMA, MMIO, power states, and other resources are centrally coordinated.
- **Invisible routing:** users should not need to understand compatibility layers just to launch an application.
- **Capability-aware compatibility:** `.exe` versus ELF is only the beginning. The router considers APIs, services, drivers, security requirements, and other capabilities.
- **Legitimate Windows fallback:** software that genuinely requires a real Windows environment can use Smart Reboot / Hybrid Hand-off and receive bare-metal Windows hardware access.
- **Security by architecture:** privilege separation, memory protection, IOMMU/DMA isolation, driver authentication, permissions, and crash containment are core kernel responsibilities.
- **Desktop independence:** the kernel does not depend on a particular desktop environment.

---

# Roadmap

## Phase 0 — Architecture & Foundation

Build the kernel before building the rest of the operating system.

- Rust development/tooling
- Sols Hybrid Kernel architecture
- UEFI/firmware boot
- x86-64 CPU initialization
- SMP/multicore support
- Interrupt controllers and handling
- Timers and high-resolution timers
- Scheduler
- Processes and threads
- User/kernel privilege separation
- Syscalls
- IPC and synchronization
- Kernel heap
- Physical/virtual memory
- Page tables and memory protection
- Shared memory
- Kernel modules
- Logging, tracing, debugging
- Crash handling
- Shutdown/reboot
- CPU power states
- Initial storage/filesystem support

**Milestone:** Sols Hybrid Kernel v0.1

## Phase 1 — Unified Hardware & Driver Layer

Build the layer that lets Linux and Windows/NT driver ecosystems coexist safely.

- Sols Driver Core
- Linux driver environment
- Windows/NT driver environment
- Sols-native integration layer
- Driver registration/loading/unloading
- Hardware matching
- Dependency management
- Resource ownership
- Interrupt/DMA/MMIO ownership
- Driver signing/authentication
- Driver isolation and crash containment
- Automatic hardware/driver detection
- Driver compatibility database
- PCI/PCIe, USB, ACPI, DMA, IOMMU, MSI/MSI-X
- GPU, audio, network, storage, input, and power support

**Milestone:** Unified Driver Layer v1.0

## Phase 2 — Core Hardware Support

- AMD/Intel/NVIDIA graphics
- Display and multi-monitor support
- Vulkan/compute/media interfaces
- NVMe/SATA/AHCI/USB/SD/eMMC storage
- Ethernet/Wi-Fi/Bluetooth
- TCP/IP, UDP, IPv4, IPv6
- Keyboard/mouse/touch/gamepad/HID
- HDA/USB/Bluetooth audio
- PipeWire integration
- ACPI, battery, charging, thermal/fan management
- Suspend/resume/hibernate/wake
- Handheld power profiles
- GPU/device reset and recovery

**Milestone:** Sols Hardware Platform v1.0

## Phase 3 — Execution Environments

- Native Linux ELF execution
- Linux syscalls and userspace interfaces
- Windows/NT PE execution environment
- NT process/system interfaces
- Win32 userspace compatibility
- Application Router
- Capability/requirement detection
- Linux/Windows process isolation
- Cross-environment IPC
- Shared memory and controlled resource sharing
- Cross-environment file access
- Session/state handoff
- Compatibility-layer integration

**Milestone:** Multi-Environment Support v1.0

## Phase 4 — Windows Integration

- Legitimate Windows installation workflow
- Dedicated Windows environment/partition
- Windows driver support
- Regular Tiny11 option (not Tiny11 Core)
- Automatic first-boot Tiny11 setup
- Windows compatibility testing
- `systemctl switch-to-windows` temporary hand-off
- Transactional file/session synchronization
- Smart Reboot / Hybrid Hand-off
- Bare-metal Windows for software that genuinely requires it
- Return to SolsRNGOS after Windows shutdown/reboot

**Milestone:** Windows Integration v1.0

## Phase 5 — Sols Desktop

- Rust-based desktop framework
- KDE integration
- Wayland support
- X11 support
- Custom panels, launcher, notifications, settings
- Control Center
- Themes, wallpapers, icons, cursors, animations
- Login and boot experience
- Accessibility
- Unified Linux + PowerShell terminal
- Split-pane terminal
- Controlled cross-environment terminal piping
- Profiles and customization
- Hardware/performance monitoring

**Milestone:** SolsRNGOS Desktop v1.0

## Phase 6 — Gaming

- Steam
- Proton/Wine
- Vulkan
- Gamescope
- Controller support
- Shader management
- GPU/performance optimization
- Gaming Mode
- Handheld Mode
- TDP/power controls
- Performance monitoring
- Game-specific profiles
- Legitimate anti-cheat compatibility testing
- Windows fallback when genuinely required

## Phase 7 — SolsRNGCore

- SolsRNGCore integration
- Macro profiles
- Anti-AFK where permitted
- Input management
- Log monitoring
- Biome detection
- Discord/webhooks
- Notifications
- Background services
- Unified configuration
- Modular integration with SolsRNGOS

## Phase 8 — Handheld & Android

- Steam Deck support
- Other x86 handhelds
- Controller-first interface
- Suspend/resume
- Handheld performance profiles
- Waydroid
- Android application support
- Future ARM/mobile targets

## Phase 9 — Compatibility Ecosystem

- Compatibility database
- Automatic application classification
- Native Linux path
- Wine/Proton path
- Windows environment path
- Bare-metal Windows path
- Driver requirement detection
- Dependency management
- Application profiles
- Automatic configuration and testing

## Phase 10 — Installer & Recovery

- Graphical installer
- Partitioning
- Dual-boot configuration
- Windows integration
- Driver setup
- First-boot setup
- Recovery environment
- Safe mode
- Rollback
- System repair
- Secure Boot support
- Reproducible builds

## Phase 11 — Hardware Expansion & Testing

Progressive testing on:

1. Steam Deck
2. AMD desktops
3. Intel desktops
4. NVIDIA systems
5. Laptops
6. Other handhelds
7. Virtual machines
8. Additional hardware configurations

Maintain a hardware/driver compatibility database as testing expands.

## Phase 12 — Education & Enterprise

### Desktop
Everyday general-purpose configuration.

### Gaming
Gaming-focused configuration.

### Handheld
Controller-first optimized configuration.

### Education
Classroom management, developer tooling, restricted profiles, and administration.

### Enterprise
Central management, security policies, auditing, deployment, and hardware management.

## Phase 13 — Public Release

- **0.1:** first public experimental/developer release
- **0.5:** major hardware and compatibility expansion
- **0.9:** release candidate
- **1.0:** stable general-purpose release

---

# Kernel Architecture

The full kernel architecture is documented in [`docs/KERNEL_ARCHITECTURE.md`](docs/KERNEL_ARCHITECTURE.md).

At a high level:

```text
┌───────────────────────────────────────────────────────────────┐
│                        USER SPACE                             │
│  Linux Apps │ Windows Apps │ Games │ Sols Apps │ Tools       │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                 INVISIBLE APPLICATION ROUTER                  │
│  Type → capabilities → dependencies → security → environment │
└───────────────┬───────────────────────────────┬───────────────┘
                │                               │
                ▼                               ▼
┌────────────────────────┐          ┌──────────────────────────┐
│ Linux Environment      │          │ Windows / NT Environment │
│ ELF / Linux syscalls   │◄────────►│ PE / NT / Win32          │
│ POSIX / userspace      │   IPC    │ services / userspace     │
└────────────┬───────────┘          └─────────────┬────────────┘
             └────────────────┬───────────────────┘
                              ▼
┌───────────────────────────────────────────────────────────────┐
│                    SOLS HYBRID KERNEL                         │
│                                                               │
│ Scheduler │ Process/Thread │ Memory │ IPC │ Security          │
│ VFS       │ Syscalls       │ Timers │ Power │ Networking      │
│ Logging   │ Crash Handling │ Namespaces │ Resource Control    │
└────────────────────────────┬──────────────────────────────────┘
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                 SOLS DRIVER CORE                              │
│ Registration │ Loading │ Dependencies │ Signing │ Isolation   │
│ Ownership │ Arbitration │ Lifecycle │ Recovery │ Capabilities │
└────────────────────────────┬──────────────────────────────────┘
                             ▼
┌───────────────────────────────────────────────────────────────┐
│             SOLS-NATIVE INTEGRATION LAYER                     │
│ Coordinates Linux drivers + NT drivers + Sols-native support │
│ Resource translation │ synchronization │ power │ DMA/IOMMU   │
└────────────────────────────┬──────────────────────────────────┘
                             ▼
┌───────────────────────────────────────────────────────────────┐
│             HARDWARE ABSTRACTION LAYER                        │
│ CPU │ GPU │ RAM │ PCIe │ USB │ Storage │ Network │ Audio     │
│ Input │ Sensors │ Power │ Thermal │ Display │ DMA/IOMMU       │
└────────────────────────────┬──────────────────────────────────┘
                             ▼
                    ┌───────────────────┐
                    │ PHYSICAL HARDWARE │
                    └───────────────────┘
```

---

# Windows Smart Reboot

Windows is not treated as a second kernel running beside Sols in the normal path.

When software genuinely needs a real Windows environment:

```text
SolsRNGOS running
      │
      ▼
Requirement detected
      │
      ▼
Save session + required state
      │
      ▼
Synchronize files transactionally
      │
      ▼
Set Windows as one-time next boot
      │
      ▼
Reboot
      │
      ▼
Windows gets bare-metal hardware
      │
      ▼
Windows software runs normally
      │
      ▼
Windows shutdown/reboot
      │
      ▼
SolsRNGOS boots
      │
      ▼
Restore Sols session/state
```

The hand-off is designed for legitimate compatibility, not for bypassing security software or anti-cheat protections.

---

# Driver Coexistence Model

This is a foundational rule of SolsRNGOS:

```text
                     SOLS HYBRID KERNEL
                              │
                       SOLS DRIVER CORE
                              │
              ┌───────────────┴───────────────┐
              │                               │
       Linux driver ecosystem          NT driver ecosystem
              │                               │
              └───────────────┬───────────────┘
                              │
                    SOLS-NATIVE INTEGRATION
                              │
                          HARDWARE
```

Sols-native drivers/integration do **not** replace Linux or NT drivers. They provide the coordination layer required for the two ecosystems to share devices and resources safely.

---

# Security Model

The kernel architecture includes:

- User/kernel privilege separation
- Process isolation
- Memory protection
- Capability-based resource access where appropriate
- Driver authentication/signing
- Secure Boot integration
- IOMMU/DMA restrictions
- Device/resource permissions
- Driver dependency validation
- Driver crash containment
- Device reset/recovery
- Auditing and diagnostics
- Secure state/file hand-off
- Sandboxing for compatibility environments

---

# Repository Direction

The project is intentionally **kernel-first**.

The initial engineering priority is to define and implement the Sols Hybrid Kernel and its hardware/driver architecture before building the full desktop experience.

The desktop, gaming layer, SolsRNGCore integration, Android support, and other higher-level components depend on the kernel and OS service interfaces being stable enough to build on.

See:

- [`docs/KERNEL_ARCHITECTURE.md`](docs/KERNEL_ARCHITECTURE.md) — complete kernel architecture
- [`docs/ROADMAP.md`](docs/ROADMAP.md) — detailed project roadmap
- [`docs/DESIGN_PRINCIPLES.md`](docs/DESIGN_PRINCIPLES.md) — architectural rules and non-goals

---

## Website / Public Project Presentation

The public SolsRNGOS website is intentionally kept as a separate finalized presentation while the ISO is being developed. The project README serves as the canonical public-facing description of the engineering goal inside this repository.

**Development build** · **Target: SolsRNGOS 0.1**

> The OS is actively being built. The public ISO download will appear when the first release is ready.

---

## License

License to be selected as the project architecture and source tree are established.
