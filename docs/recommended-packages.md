# Recommended Arch Linux Installation List for a stable installation.

```bash
  sudo pacman -S intel-ucode nvidia networkmanager wpa_supplicant openssh dnsmasq dnsutils nss-mdns tlp tlp-rdw thermald efitools haveged pipewire pipewire-pulse wireplumber neovim wget sed rsync git findutils reflector downgrade pacman-contrib pkgfile rebuild-detector smartmontools tldr glances kitty firefox pavucontrol
```

## Drivers (NVIDIA and Intel-Specific)

- **`intel-ucode`**: Provides microcode updates for Intel processors, ensuring stability, security fixes, and performance improvements.
- **`nvidia`**: Check docs for how to install NVIDIA drivers for Dell XPS 9520.

## Networking

- **`networkmanager`**: A widely used service to manage wired and wireless network connections. Provides a graphical interface in GNOME.
- **`wpa_supplicant`**: Handles wireless authentication and security, ensuring proper connectivity to WPA/WPA2 networks.
- **`openssh`**: Enables secure remote connections via SSH, allowing you to remotely log in or manage your machine.
- **`dnsmasq`**: Provides lightweight DNS and DHCP services, often used in local networking configurations.
- **`dnsutils`**: A set of command-line tools (e.g., `dig`, `nslookup`) for troubleshooting and querying DNS servers.
- **`nss-mdns`**: Allows hostname resolution for devices on local networks using multicast DNS (mDNS), useful for accessing devices like printers or other computers.

## Boot and System Utilities

- **`efitools`**: Provides tools for managing UEFI Secure Boot keys and EFI variables, essential for secure boot setups.
- **`haveged`**: Improves entropy generation, crucial for cryptographic functions, especially during boot.

## Audio

- **`pipewire`**: A modern multimedia framework for handling audio and video streams, designed to replace PulseAudio and JACK.
- **`pipewire-pulse`**: Ensures compatibility with applications expecting PulseAudio, allowing seamless migration.
- **`wireplumber`**: Acts as a session manager for PipeWire, handling policy and routing for audio streams.

## Utilities

- **`neovim`**: A lightweight and essential text editor available on nearly every Linux system, useful for quick file edits.
- **`wget`**: A command-line utility for downloading files from the internet, supporting HTTP, HTTPS, and FTP protocols.
- **`sed`**: A stream editor for processing and transforming text, often used in scripts for automating text modifications.
- **`rsync`**: A fast and flexible file copying/syncing tool, useful for backups and remote file transfers.
- **`git`**: A version control system for tracking changes in files and collaborating on code projects.
- **`findutils`**: Provides tools like `find` and `xargs` for locating and processing files based on various criteria.

## System Maintenance

- **`reflector`**: Automatically updates the pacman mirror list to use the fastest or most reliable servers for package downloads.
- **`downgrade`**: Enables reverting to older versions of packages, useful when a recent update causes issues.
- **`pacman-contrib`**: Provides additional utilities for managing packages, such as orphaned packages and package statistics.
- **`pkgfile`**: A command-line tool to search for the package that owns a file, useful for troubleshooting.
- **`rebuild-detector`**: Identifies packages that need rebuilding after system updates, especially for AUR packages.

## Filesystem Tools

- **`smartmontools`**: Tools for monitoring and managing the health of storage devices, supporting Self-Monitoring, Analysis, and Reporting Technology (SMART).

## Utilities for Power Users

- **`tldr`**: A simplified and user-friendly alternative to `man` pages, providing concise explanations and examples for commands.
- **`glances`**: A cross-platform, real-time system monitoring tool that provides a comprehensive overview of CPU, memory, disk, and network usage.

### Other Packages (GUI)

- **`kitty`**: A modern and highly customizable terminal emulator with GPU-based rendering for improved performance. It's lightweight, fast, and supports features like tabs and Unicode.
- **`firefox`**: A popular open-source web browser that’s privacy-focused and feature-rich. It is included to provide a reliable browser for web access.
- **`pavucontrol`**: A graphical tool to manage and fine-tune audio devices and streams, useful for advanced control beyond GNOME’s built-in sound settings.

## Power Management

After trying a couple of different one's this worked best for my laptop.

- **`tlp`**: An advanced power management tool for Linux, optimizing battery life on laptops by adjusting CPU frequency, device power states, etc.

# Thermald

**Thermald** is a Linux daemon used to prevent the overheating of platforms. This daemon monitors temperature and applies compensation using available cooling methods.

---

### **Prevent Overwrites During Updates**

By default, each update of the `thermald` package will overwrite the service file. To ensure it remains functional after updates, move the service file to:

`/etc/systemd/system/`

---

### **Installation and Setup**

#### 1. Install Thermald

Run the following command to install `thermald`:

```bash
sudo pacman -S --needed thermald
```

Copy the Service File:

```bash
sudo cp /usr/lib/systemd/system/thermald.service /etc/systemd/system/
```

Edit the Service File:

```bash
sudo nano /etc/systemd/system/thermald.service
```

Updated this lines:

```bash
ExecStart=/usr/bin/thermald --systemd --dbus-enable --adaptive
# Update to
ExecStart=/usr/bin/thermald --systemd --dbus-enable --adaptive --ignore-cpuid-check
```

Enable and Start Thermald

```bash
sudo systemctl enable thermald
# Reboot system
sudo reboot
```

---
