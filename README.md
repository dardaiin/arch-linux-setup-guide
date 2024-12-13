# Dell XPS 9520 Arch Linux Setup Guide

This repository provides a comprehensive guide to setting up Arch Linux on a Dell XPS 9520, but will probably work on other systems as well. It includes detailed instructions for installing the base system, configuring essential drivers and utilities, and optimizing the system for performance and stability.

---

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation Guide](#installation-guide)
  - [Base System Installation](#base-system-installation)
  - [Additional Setup](#additional-setup)
- [Recommended Packages](#recommended-packages)
- [NVIDIA GPU Configuration](#nvidia-gpu-configuration)
- [Power Management](#power-management)
- [Contributing](#contributing)
- [License](#license)

---

## Introduction

This guide is tailored for the Dell XPS 9520, ensuring optimal performance and compatibility with Arch Linux. It covers everything from the initial installation to configuring the NVIDIA GPU and power management tools.

---

## Prerequisites

Before you begin, ensure you have the following:
- A Dell XPS 9520 laptop
- A bootable USB drive with the Arch Linux ISO
- An active internet connection

---

## Installation Guide

### Base System Installation

Follow the steps in `arch-base-installation.md` to install the base system. This includes:
1. Booting the Arch Linux installer
2. Verifying boot mode
3. Connecting to the internet
4. Updating the system clock
5. Setting the console keyboard layout and font
6. Disk partitioning
7. Formatting and mounting partitions
8. Installing essential packages
9. Generating the `fstab` file
10. Chrooting into the installed system
11. Configuring the system (timezone, locale, hostname, initramfs, GRUB)
12. Setting the root password and creating a user
13. Finalizing the installation

---

### Additional Setup

After installing the base system, follow the instructions in `arch-base-installation.md` to set up:
- The GNOME desktop environment
- Optional configurations for enhanced functionality

---

## Recommended Packages

Refer to `recommended-packages.md` for a curated list of packages for a stable and functional Arch Linux setup. This includes:
- Essential drivers
- Networking tools
- System utilities
- Audio frameworks
- Power management tools

---

## NVIDIA GPU Configuration

To configure the NVIDIA GPU on your Dell XPS 9520, follow the steps in `setup-nvidia.md`. This guide includes:
- Installing NVIDIA drivers and utilities
- Setting up hybrid mode with EnvyControl
- Fixing GNOME issues with the dGPU
- Verifying the configuration

---

## Power Management

For optimal power management, install and configure `tlp` and `thermald` as described in `recommended-packages.md`. These tools:
- Extend battery life
- Prevent overheating
- Enhance overall power efficiency

---

## Contributing

Contributions are welcome! If you have any improvements or suggestions:
1. Open an issue
2. Submit a pull request

Your contributions help improve this guide for all users.

---

## License

This project is licensed under the [MIT License](LICENSE). See the `LICENSE` file for details.

---

By following this guide, you can achieve a well-optimized Arch Linux setup on your Dell XPS 9520, ensuring a smooth and efficient computing experience.
