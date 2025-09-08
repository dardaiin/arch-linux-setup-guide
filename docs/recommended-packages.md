# Essential Arch Linux Package Installation

This guide provides a complete package installation strategy organized by necessity and use case. Install packages in order based on your needs.

## 🚀 Installation Commands by Category

### 1. Base System (Essential for all installations)
```bash
sudo pacman -S --needed intel-ucode mesa vulkan-intel networkmanager openssh avahi nss-mdns power-profiles-daemon base-devel git neovim reflector pacman-contrib pkgfile man-db man-pages texinfo which lsof fwupd hwdetect systemd-resolvconf ufw inetutils cups cups-pdf system-config-printer
```

### 2. Full Desktop System (Required for desktop environments)
```bash
sudo pacman -S --needed pipewire pipewire-pulse pipewire-alsa wireplumber pavucontrol udisks2 gvfs gvfs-smb gnome-keyring polkit-gnome xdg-user-dirs-gtk firefox nautilus noto-fonts noto-fonts-emoji ttf-dejavu wl-clipboard playerctl unzip p7zip
```

### 3A. GNOME Desktop Environment
```bash
sudo pacman -S --needed gnome-shell gdm gnome-control-center gnome-terminal gnome-text-editor xdg-desktop-portal-gnome
```

### 3B. Hyprland Desktop Environment
```bash
sudo pacman -S --needed hyprland waybar mako wofi xdg-desktop-portal-hyprland xdg-desktop-portal-gtk
```

### 4. Modern Tools & Productivity (Nice to have)
```bash
sudo pacman -S --needed fish starship alacritty btop fastfetch timeshift bash-completion curl wget tree-sitter-cli fd ripgrep zoxide fzf github-cli tldr rsync
```

---

## 📋 Complete Single Commands

### Minimal Server/Headless System
```bash
# Base system only (27 packages)
sudo pacman -S --needed intel-ucode mesa vulkan-intel networkmanager openssh avahi nss-mdns power-profiles-daemon base-devel git neovim reflector pacman-contrib pkgfile man-db man-pages texinfo which lsof fwupd hwdetect systemd-resolvconf ufw inetutils cups cups-pdf system-config-printer
```

### Complete GNOME Desktop
```bash
# Base + Desktop + GNOME (52 packages)
sudo pacman -S --needed intel-ucode mesa vulkan-intel networkmanager openssh avahi nss-mdns power-profiles-daemon base-devel git neovim reflector pacman-contrib pkgfile man-db man-pages texinfo which lsof fwupd hwdetect systemd-resolvconf ufw inetutils cups cups-pdf system-config-printer pipewire pipewire-pulse pipewire-alsa wireplumber pavucontrol udisks2 gvfs gvfs-smb gnome-keyring polkit-gnome xdg-user-dirs-gtk firefox nautilus noto-fonts noto-fonts-emoji ttf-dejavu wl-clipboard playerctl unzip p7zip gnome-shell gdm gnome-control-center gnome-terminal gnome-text-editor xdg-desktop-portal-gnome
```

### Complete Hyprland Desktop
```bash
# Base + Desktop + Hyprland (51 packages)
sudo pacman -S --needed intel-ucode mesa vulkan-intel networkmanager openssh avahi nss-mdns power-profiles-daemon base-devel git neovim reflector pacman-contrib pkgfile man-db man-pages texinfo which lsof fwupd hwdetect systemd-resolvconf ufw inetutils cups cups-pdf system-config-printer pipewire pipewire-pulse pipewire-alsa wireplumber pavucontrol udisks2 gvfs gvfs-smb gnome-keyring polkit-gnome xdg-user-dirs-gtk firefox nautilus noto-fonts noto-fonts-emoji ttf-dejavu wl-clipboard playerctl unzip p7zip hyprland waybar mako wofi xdg-desktop-portal-hyprland xdg-desktop-portal-gtk
```

### Everything (Power User Setup)
```bash
# All packages (70+ packages)
sudo pacman -S --needed intel-ucode mesa vulkan-intel networkmanager openssh avahi nss-mdns power-profiles-daemon base-devel git neovim reflector pacman-contrib pkgfile man-db man-pages texinfo which lsof fwupd hwdetect systemd-resolvconf ufw inetutils cups cups-pdf system-config-printer pipewire pipewire-pulse pipewire-alsa wireplumber pavucontrol udisks2 gvfs gvfs-smb gnome-keyring polkit-gnome xdg-user-dirs-gtk firefox nautilus noto-fonts noto-fonts-emoji ttf-dejavu wl-clipboard playerctl unzip p7zip fish starship alacritty btop fastfetch timeshift bash-completion curl wget tree-sitter-cli fd ripgrep zoxide fzf github-cli tldr rsync
```

---

## 📝 Package Descriptions

### Base System Packages
**Hardware & Drivers:**
- **`intel-ucode`**: CPU microcode updates for Intel processors
- **`mesa`**: Open-source graphics drivers 
- **`vulkan-intel`**: Vulkan API support for Intel graphics

**Networking:**
- **`networkmanager`**: Network connection management with Wi-Fi support
- **`openssh`**: Secure remote access via SSH
- **`avahi`**: Network service discovery (for printers, local devices)
- **`nss-mdns`**: Hostname resolution for local network devices (.local domains)
- **`inetutils`**: Network utilities (telnet, ftp, etc.)

**System Essentials:**
- **`power-profiles-daemon`**: Modern power profile management
- **`base-devel`**: Essential development packages (gcc, make, etc.)
- **`git`**: Version control system
- **`neovim`**: Modern terminal text editor

**System Utilities:**
- **`reflector`**: Automatically update pacman mirror list
- **`pacman-contrib`**: Additional pacman utilities
- **`pkgfile`**: Find which package owns a file
- **`man-db`**: Manual page database
- **`man-pages`**: Linux manual pages
- **`texinfo`**: Documentation system
- **`which`**: Locate a command
- **`lsof`**: List open files and processes

**Hardware & Maintenance:**
- **`fwupd`**: Firmware update daemon
- **`hwdetect`**: Hardware detection and configuration helper
- **`systemd-resolvconf`**: Modern DNS resolution management

**Security:**
- **`ufw`**: Uncomplicated Firewall - easy firewall management

**Printing:**
- **`cups`**: Print server and drivers
- **`cups-pdf`**: PDF printer support
- **`system-config-printer`**: Printer configuration GUI

### Desktop System Packages
**Audio System:**
- **`pipewire`**: Modern audio framework (replaces PulseAudio/JACK)
- **`pipewire-pulse`**: PulseAudio compatibility layer
- **`pipewire-alsa`**: ALSA compatibility for PipeWire
- **`wireplumber`**: PipeWire session manager
- **`pavucontrol`**: GUI audio control panel

**File Management:**
- **`udisks2`**: Automatic mounting of USB drives and external devices
- **`gvfs`**: Virtual filesystem (trash, network shares, etc.)
- **`gvfs-smb`**: SMB/CIFS network share support
- **`nautilus`**: GNOME file manager (works in all desktop environments)

**Desktop Integration:**
- **`gnome-keyring`**: Password and secrets management
- **`polkit-gnome`**: Authentication agent for privileged operations
- **`xdg-user-dirs-gtk`**: Standard user directories

**Applications:**
- **`firefox`**: Web browser with Wayland support

**Fonts:**
- **`noto-fonts`**: Google's Noto font family (covers most languages)
- **`noto-fonts-emoji`**: Emoji support
- **`ttf-dejavu`**: DejaVu fonts (fallback for many applications)

**Utilities:**
- **`wl-clipboard`**: Clipboard utilities for Wayland
- **`playerctl`**: Media key support and control
- **`unzip`**: ZIP file extraction
- **`p7zip`**: 7-Zip archive support

### Desktop Environment Packages

**GNOME:**
- **`gnome-shell`**: GNOME desktop environment core
- **`gdm`**: GNOME display manager
- **`gnome-control-center`**: System settings
- **`gnome-terminal`**: Terminal emulator
- **`gnome-text-editor`**: Text editor
- **`xdg-desktop-portal-gnome`**: Desktop integration portal

**Hyprland:**
- **`hyprland`**: Dynamic tiling Wayland compositor
- **`waybar`**: Customizable status bar
- **`mako`**: Notification daemon
- **`wofi`**: Application launcher
- **`xdg-desktop-portal-hyprland`**: Hyprland integration portal
- **`xdg-desktop-portal-gtk`**: GTK integration portal

### Modern Tools & Productivity

**Shell & Terminal:**
- **`fish`**: User-friendly command line shell
- **`starship`**: Modern, fast shell prompt
- **`alacritty`**: GPU-accelerated terminal emulator
- **`bash-completion`**: Tab completion for bash commands

**System Monitoring:**
- **`btop`**: Modern interactive system monitor
- **`fastfetch`**: Fast system information display

**Development & Utilities:**
- **`tree-sitter-cli`**: Syntax highlighting engine
- **`curl`**: Command-line tool for transferring data
- **`wget`**: Network downloader
- **`rsync`**: Fast file synchronization and backup

**Modern CLI Tools:**
- **`fd`**: Fast find replacement
- **`ripgrep`**: Fast grep replacement
- **`zoxide`**: Smart directory navigation
- **`fzf`**: Fuzzy finder
- **`github-cli`**: GitHub integration
- **`tldr`**: Simplified manual pages

**System Maintenance:**
- **`timeshift`**: System backup and restore using Btrfs snapshots

---

## ⚙️ Enable Services

### Base System Services (Required for all installations)
```bash
# Network management
sudo systemctl enable NetworkManager

# Network discovery (for local devices, printers)
sudo systemctl enable avahi-daemon

# Power management
sudo systemctl enable power-profiles-daemon

# Firewall
sudo systemctl enable ufw
sudo ufw enable

# SSD optimization (TRIM support)
sudo systemctl enable fstrim.timer

# Firmware updates
sudo systemctl enable fwupd.service

# Print service
sudo systemctl enable cups.service
sudo systemctl enable cups-browsed.service
```

### Desktop Environment Services

**For GNOME:**
```bash
# Display manager
sudo systemctl enable gdm.service
```

**For Hyprland:**
```bash
# No display manager needed - Hyprland starts from TTY
# Optional: Install a display manager if preferred
# sudo pacman -S sddm
# sudo systemctl enable sddm.service
```

---

## 🔧 Post-Installation Setup

### Base System Configuration (Required for all installations)

**Update package database:**
```bash
sudo pkgfile --update
```

**Configure reflector for faster downloads:**
```bash
sudo reflector --country 'Sweden' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

**Configure printer detection:**
```bash
# Enable printer discovery
sudo systemctl enable cups-browsed.service
```

**Verify zram is working (if configured during installation):**
```bash
swapon --show
zramctl
```

### Desktop Environment Specific Configuration

**For GNOME:**
```bash
# GNOME handles keyring and authentication automatically
# No additional configuration needed
```

**For Hyprland:**
```bash
# Add GNOME keyring and polkit to Hyprland autostart
echo 'exec-once = gnome-keyring-daemon --start --components=secrets' >> ~/.config/hypr/hyprland.conf
echo 'exec-once = /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1' >> ~/.config/hypr/hyprland.conf

# Set SSH_AUTH_SOCK for keyring (add to shell profile)
echo 'export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/gcr/ssh"' >> ~/.bashrc
```

### Optional Configuration

**Configure Fish shell (if installed):**
```bash
# Set fish as default shell
chsh -s /usr/bin/fish

# Create fish config directory and initialize starship
mkdir -p ~/.config/fish
echo "starship init fish | source" >> ~/.config/fish/config.fish
```

**Configure Timeshift for system snapshots (if installed):**
```bash
# Run timeshift setup (first-time configuration)
sudo timeshift --list-devices
sudo timeshift --create --comments "Initial system snapshot"
```

**Configure modern CLI tools (if installed):**
```bash
# Initialize zoxide
echo 'eval "$(zoxide init bash)"' >> ~/.bashrc

# Add fzf key bindings
echo 'source /usr/share/fzf/key-bindings.bash' >> ~/.bashrc
echo 'source /usr/share/fzf/completion.bash' >> ~/.bashrc
```

---

## 🌟 Recommended Installation Path

1. **Start with Base System** - Essential for any Arch installation
2. **Add Desktop System** - If you want a GUI desktop
3. **Choose Desktop Environment** - GNOME (traditional) or Hyprland (tiling)
4. **Add Modern Tools** - For enhanced productivity and development

This modular approach lets you build exactly what you need without bloat.

---

**Note**: This provides a complete, production-ready Arch Linux system with all functionality properly separated by use case. You can mix and match categories based on your specific needs.