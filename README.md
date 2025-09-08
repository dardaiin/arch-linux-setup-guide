# Dell XPS 9520 Arch Linux Setup Guide

This repository provides a comprehensive guide to setting up Arch Linux on a Dell XPS 9520, but will work on other systems as well. It includes detailed instructions for installing a secure, high-performance base system with modern technologies and optimizing it for stability and functionality.

---

## Table of Contents
- [Introduction](#introduction)
- [System Architecture](#system-architecture)
- [Prerequisites](#prerequisites)
- [Installation Guide](#installation-guide)
  - [Base System Installation](#base-system-installation)
  - [Desktop Environment Options](#desktop-environment-options)
- [Package Installation](#package-installation)
- [Power Management](#power-management)
- [Contributing](#contributing)
- [License](#license)

---

## Introduction

This guide is optimized for modern Arch Linux installations with a focus on security, performance, and simplicity. It features:

- **Full disk encryption** with LUKS2
- **Flexible storage** with LVM
- **Modern filesystem** with Btrfs subvolumes
- **Efficient memory usage** with zram
- **Wayland-first** desktop environments
- **Modern power management** with power-profiles-daemon
- **Streamlined package selection** - no bloat

---

## System Architecture

### Storage Stack
```
┌─────────────────────────────────────────────────────────────┐
│ /boot (EFI System Partition) - 1GB                         │
├─────────────────────────────────────────────────────────────┤
│ LUKS2 Encrypted Partition                                   │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ LVM Volume Group (vg0)                                  │ │
│ │ ┌─────────────────────────────────────────────────────┐ │ │
│ │ │ root (uses all available space)                     │ │ │
│ │ │ Btrfs with subvolumes:                              │ │ │
│ │ │   @ (root filesystem)                               │ │ │
│ │ │   @home (user home directories)                     │ │ │
│ │ │   @var (system logs, cache)                          │ │ │
│ │ │   @tmp (temporary files)                             │ │ │
│ │ │   @snapshots (system snapshots)                     │ │ │
│ │ └─────────────────────────────────────────────────────┘ │ │
│ └─────────────────────────────────────────────────────────┐ │
└─────────────────────────────────────────────────────────────┘
```

### Key Technologies
- **LUKS2**: Full disk encryption for security
- **LVM**: Flexible volume management 
- **Btrfs**: Copy-on-write filesystem with compression and snapshots
- **zram**: Compressed swap in RAM (replaces traditional swap)
- **mkinitcpio**: Standard Arch Linux initramfs
- **PipeWire**: Modern audio system
- **Wayland**: Modern display protocol

---

## Prerequisites

Before you begin, ensure you have the following:
- A Dell XPS 9520 laptop (or compatible system)
- A bootable USB drive with the Arch Linux ISO
- An active internet connection
- Basic familiarity with command line operations

---

## Installation Guide

### Base System Installation

Follow the steps in [`arch-base-installation.md`](docs/arch-base-installation.md) to install the base system. This includes:

1. **Pre-Installation Setup**
   - Booting the Arch Linux installer
   - Verifying UEFI boot mode
   - Connecting to the internet
   - Setting system clock and keyboard layout

2. **Disk Setup**
   - Creating EFI and encrypted LVM partitions
   - Setting up LUKS2 encryption
   - Configuring LVM logical volumes
   - Creating Btrfs filesystems with subvolumes

3. **System Installation**
   - Installing essential packages with `pacstrap`
   - Configuring `fstab` for automatic mounting
   - Setting up system locale, timezone, and hostname

4. **Boot Configuration**
   - Configuring `mkinitcpio` for LUKS+LVM support
   - Installing and configuring GRUB bootloader
   - Setting up zram for compressed swap

5. **User Setup**
   - Creating root password and user account
   - Configuring sudo access

### Desktop Environment Options

Choose between two modern desktop environments:

#### GNOME (Traditional Desktop)
- Wayland by default
- Full-featured desktop environment
- Excellent hardware integration
- Perfect for users wanting a familiar desktop experience

#### Hyprland (Tiling Window Manager)
- Pure Wayland compositor
- Dynamic tiling window management  
- Highly customizable
- Uses GNOME tools (Nautilus, keyring) for integration
- Ideal for power users and developers

---

## Package Installation

This system uses a **modular package approach** - install only what you need for your specific use case.

### 📋 Installation Strategy

**For detailed commands and explanations, see [`recommended-packages.md`](docs/recommended-packages.md)**

The package installation is organized into **4 categories**:

1. **Base System (27 packages)** - Essential for all installations
2. **Desktop System (19 packages)** - Required for GUI desktop environments  
3. **Desktop Environment** - Choose GNOME (6 packages) or Hyprland (6 packages)
4. **Modern Tools (18+ packages)** - Optional productivity and development tools

### 🚀 Quick Installation Options

**Option 1: Server/Headless System**
```bash
# Install base system only (27 packages)
# See recommended-packages.md for command
```

**Option 2: Complete GNOME Desktop**
```bash
# Install base + desktop + GNOME (52 packages)  
# See recommended-packages.md for command
```

**Option 3: Complete Hyprland Desktop**
```bash
# Install base + desktop + Hyprland (51 packages)
# See recommended-packages.md for command
```

**Option 4: Everything (Power User)**
```bash
# Install all categories (70+ packages)
# See recommended-packages.md for command
```

### 📖 Detailed Installation Instructions

**👉 See [`recommended-packages.md`](docs/recommended-packages.md) for:**
- Complete installation commands for each option
- Individual category commands for modular installation  
- Package descriptions and explanations
- Service configuration commands
- Post-installation setup steps

### 🏗️ Package Architecture

#### **1. Base System (27 packages)**
**Essential for ANY Arch installation - servers, desktops, everything:**
- Hardware drivers (Intel CPU/GPU, firmware)
- Networking (full stack with device discovery)
- System utilities (documentation, package management) 
- Security (firewall, authentication)
- Printing (CUPS setup)
- Development basics (build tools, Git, editor)

#### **2. Desktop System (19 packages)**
**Required for GUI desktop environments:**
- Audio system (complete PipeWire setup)
- File management (auto-mounting, network shares)
- Desktop integration (fonts, clipboard, authentication)
- Essential applications (web browser)

#### **3. Desktop Environment Choice**
**Pick one based on your preference:**
- **GNOME (6 packages)**: Traditional desktop, beginner-friendly
- **Hyprland (6 packages)**: Tiling window manager, advanced users

#### **4. Modern Tools & Productivity (18+ packages)**
**Optional enhancements for developers and power users:**
- Modern CLI tools (fd, ripgrep, zoxide, fzf)
- Enhanced shell experience (Fish, Starship)
- Development utilities (GitHub CLI, tree-sitter)
- System monitoring (btop, fastfetch)
- Backup tools (Timeshift)

### 🎯 Installation Recommendations

- **New users**: Start with Base System + Complete GNOME Desktop
- **Experienced users**: Base System + Desktop System + Hyprland + selected Modern Tools
- **Servers**: Base System only
- **Developers**: Everything for full productivity suite

**📚 For step-by-step installation commands, see [`recommended-packages.md`](docs/recommended-packages.md)**

---

## Power Management

This setup uses **power-profiles-daemon** for modern, integrated power management:

### Benefits over traditional tools:
- **GNOME integration**: Native support in GNOME settings
- **Automatic switching**: Intelligent power profile switching
- **Modern approach**: Replaces both tlp and thermald
- **Simplified management**: Single service for all power needs

### Power Profiles:
- **Performance**: Maximum performance for demanding tasks
- **Balanced**: Optimal balance of performance and battery life  
- **Power Saver**: Extended battery life for mobile use

### Manual Control:
```bash
# List available profiles
powerprofilesctl list

# Set specific profile
powerprofilesctl set power-saver
powerprofilesctl set balanced
powerprofilesctl set performance
```

---

## System Features

### Security
- ✅ **Full disk encryption** with LUKS2
- ✅ **Secure boot** compatible (optional)
- ✅ **Firewall** with UFW
- ✅ **Password management** with GNOME Keyring

### Performance  
- ✅ **zram** for efficient memory usage
- ✅ **Btrfs compression** for space savings
- ✅ **Modern audio** with PipeWire
- ✅ **GPU acceleration** with Vulkan support

### Usability
- ✅ **Network discovery** for printers and local devices
- ✅ **Automatic device mounting** for USB drives
- ✅ **Font coverage** for international languages
- ✅ **Wayland** for modern display handling

### Development
- ✅ **Complete toolchain** with base-devel
- ✅ **Modern editor** with Neovim + Tree-sitter
- ✅ **Version control** with Git
- ✅ **Package management** utilities

---

## Contributing

Contributions are welcome! If you have improvements or suggestions:
1. Open an issue to discuss the change
2. Submit a pull request with detailed description

Your contributions help improve this guide for all users.

---

## License

This project is licensed under the [MIT License](LICENSE). See the `LICENSE` file for details.

---

**Result**: A secure, high-performance Arch Linux system optimized for modern laptops with full disk encryption, efficient storage management, and streamlined package selection. Perfect for both daily use and development work.