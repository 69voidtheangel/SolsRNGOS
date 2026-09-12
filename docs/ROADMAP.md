# SolsRNGOS Development Roadmap

## Project Direction

SolsRNGOS is being developed **kernel-first**. The Sols Hybrid Kernel and its hardware/driver architecture come before the full desktop and application ecosystem.

---

## Phase 0 — Architecture & Foundation

- Rust development/tooling
- Kernel architecture
- UEFI/firmware boot
- x86-64 initialization
- SMP/multicore
- Interrupts
- Timers
- Scheduler
- Processes/threads
- Privilege separation
- Syscalls
- IPC/synchronization
- Kernel heap
- Physical/virtual memory
- Page tables/protection
- Shared memory
- Kernel modules
- Logging/tracing/debugging
- Crash handling
- Shutdown/reboot
- CPU power states
- Initial storage/filesystem support

**Milestone:** Sols Hybrid Kernel v0.1

## Phase 1 — Unified Hardware & Driver Layer

- Sols Driver Core
- Linux driver environment
- Windows/NT driver environment
- Sols-native integration
- Driver registration/loading/unloading
- Hardware matching
- Dependencies
- Capabilities
- Ownership/arbitration
- Interrupt/DMA/MMIO ownership
- Signing/authentication
- Isolation/crash containment
- Automatic driver management
- Driver compatibility database
- PCI/PCIe
- USB
- ACPI
- DMA/IOMMU
- MSI/MSI-X

**Milestone:** Unified Driver Layer v1.0

## Phase 2 — Hardware Platform

- AMD/Intel/NVIDIA graphics
- Display/multi-monitor
- Vulkan/compute/media
- NVMe/SATA/AHCI/USB/SD/eMMC
- Ethernet/Wi-Fi/Bluetooth
- TCP/IP/UDP/IPv4/IPv6
- Keyboard/mouse/touch/gamepad/HID
- HDA/USB/Bluetooth audio
- PipeWire integration
- ACPI/battery/charging/thermal/fans
- Suspend/resume/hibernate/wake
- Handheld power profiles
- Device/GPU recovery

**Milestone:** Sols Hardware Platform v1.0

## Phase 3 — Execution Environments

- Native Linux ELF execution
- Linux syscall/userspace interfaces
- Windows/NT PE execution
- NT process/system interfaces
- Win32 userspace compatibility
- Application Router
- Capability/requirement detection
- Process isolation
- Cross-environment IPC
- Shared memory
- Controlled resource sharing
- Cross-environment file access
- Session/state handoff
- Compatibility-layer integration

**Milestone:** Multi-Environment Support v1.0

## Phase 4 — Windows Integration

- Legitimate Windows installation workflow
- Windows partition/environment
- Windows driver support
- Regular Tiny11 option, not Tiny11 Core
- Automatic first-boot Tiny11 setup
- Windows compatibility testing
- Temporary `systemctl switch-to-windows`
- Transactional state/file synchronization
- Smart Reboot / Hybrid Hand-off
- Bare-metal Windows for genuine Windows-only requirements
- Return to SolsRNGOS after Windows shutdown/reboot

**Milestone:** Windows Integration v1.0

## Phase 5 — Sols Desktop

- Rust desktop framework
- KDE integration
- Wayland
- X11
- Launcher
- Panels
- Notifications
- Settings
- Control Center
- Themes/wallpapers/icons/cursors
- Login/boot experience
- Accessibility
- Unified Linux + PowerShell terminal
- Split panes
- Cross-environment terminal IPC
- Profiles/customization
- Hardware/performance monitoring

**Milestone:** SolsRNGOS Desktop v1.0

## Phase 6 — Gaming

- Steam
- Proton/Wine
- Vulkan
- Gamescope
- Controllers
- Shader management
- GPU/performance optimization
- Gaming Mode
- Handheld Mode
- TDP/power controls
- Performance monitoring
- Game profiles
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
- Modular OS integration

## Phase 8 — Handheld & Android

- Steam Deck
- Other x86 handhelds
- Controller-first UI
- Suspend/resume
- Performance profiles
- Waydroid
- Android applications
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
- Automatic configuration/testing

## Phase 10 — Installer & Recovery

- Graphical installer
- Partitioning
- Dual-boot configuration
- Windows integration
- Driver setup
- First boot
- Recovery environment
- Safe mode
- Rollback
- Repair tools
- Secure Boot
- Reproducible builds

## Phase 11 — Hardware Expansion & Testing

Test progressively on:

1. Steam Deck
2. AMD desktops
3. Intel desktops
4. NVIDIA systems
5. Laptops
6. Other handhelds
7. Virtual machines
8. Additional hardware

Maintain hardware/driver compatibility data throughout development.

## Phase 12 — Education & Enterprise

### Education
- Developer tools
- Classroom management
- Restricted profiles
- Administration

### Enterprise
- Central management
- Security policies
- Auditing
- Deployment
- Hardware management

## Phase 13 — Public Release

### 0.1
First public experimental/developer release.

### 0.5
Major hardware and compatibility expansion.

### 0.9
Release candidate.

### 1.0
Stable general-purpose SolsRNGOS release.
