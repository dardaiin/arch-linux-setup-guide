# Arch Linux Installation Guide (For Dell XPS 9520)

## Part 1: Base System Installation

### Pre-Installation

#### 1. Boot the Arch Linux Installer

- Download the Arch Linux ISO from the [official website](https://archlinux.org/download/).
- Create a bootable USB using tools like `dd`, Rufus, or Balena Etcher.
- Boot from the USB and select the appropriate UEFI option.

#### 2. Verify Boot Mode

- Check if you booted in UEFI mode:
  ```bash
  ls /sys/firmware/efi/efivars
  ```

#### 3. Connect to the Internet

- Use `iwctl` for Wi-Fi:
  ```bash
  iwctl
  station <interface> connect <SSID>
  exit
  ```
- Verify the connection:
  ```bash
  ping archlinux.org
  ```

#### 4. Update System Clock

- Synchronize the system clock:
  ```bash
  timedatectl set-ntp true
  ```

#### 5. Optional: Enable SSH for Remote Installation

**If you want to continue the installation from another computer (recommended for easier copy/paste):**

- Set a temporary root password:
  ```bash
  passwd
  ```

- Start SSH service:
  ```bash
  systemctl start sshd
  ```

- Find the IP address:
  ```bash
  ip addr show
  ```
  
- From another computer, connect via SSH:
  ```bash
  ssh root@[IP_ADDRESS]
  ```
  
**Benefits**: Easier to copy/paste commands, use multiple terminals, access documentation
**Security**: This is temporary - SSH will be configured properly later

#### 6. Set the console keyboard layout and font

- The default console keymap is US. Available layouts can be listed with:

  ```bash
  localectl list-keymaps
  ```

- To set the keyboard layout:
  ```bash
  loadkeys sv-latin1
  ```
- To use one of the largest fonts suitable for HiDPI screens, run:
  ```bash
  setfont ter-132b
  ```

### Disk Partitioning

#### 7. Identify Disks

- List available disks:
  ```bash
  lsblk
  # Or use fdisk
  fdisk -l
  ```

#### 8. Partition the Disk

Use fdisk to create a minimal partition scheme for full disk encryption.

  ```bash
  fdisk /dev/nvme0n1
  ```

- Create partitions:
  - **EFI Partition**: `/dev/nvme0n1p1` (1 GB, type EFI System)
  - **LUKS Partition**: `/dev/nvme0n1p2` (remaining space, type Linux LVM)

**Partition Layout:**
```
/dev/nvme0n1p1  1G     EFI System Partition
/dev/nvme0n1p2  Rest   Linux LVM (will contain encrypted LVM)
```

#### 9. Setup Full Disk Encryption (LUKS)

- Encrypt the main partition (full disk encryption):

  ```bash
  cryptsetup luksFormat --type luks2 /dev/nvme0n1p2
  cryptsetup open /dev/nvme0n1p2 cryptlvm
  ```

#### 10. Setup LVM on LUKS

- Create LVM physical volume, volume group, and logical volume:

  ```bash
  # Create physical volume on encrypted device
  pvcreate /dev/mapper/cryptlvm
  
  # Create volume group
  vgcreate vg0 /dev/mapper/cryptlvm
  
  # Create single root logical volume (uses all available space)
  lvcreate -l 100%FREE vg0 -n root
  ```

#### 11. Format Partitions

- Format the EFI partition and LVM volume:

  ```bash
  # Format EFI partition
  mkfs.fat -F32 /dev/nvme0n1p1
  
  # Format root volume with Btrfs
  mkfs.btrfs -L root /dev/vg0/root
  ```

#### 12. Create and Mount Btrfs Subvolumes

- Create Btrfs subvolumes with optimal structure:

  ```bash
  # Mount root volume temporarily
  mount /dev/vg0/root /mnt
  
  # Create subvolumes (modern Btrfs layout)
  btrfs subvolume create /mnt/@             # Root filesystem
  btrfs subvolume create /mnt/@home         # User home directories
  btrfs subvolume create /mnt/@var          # System logs and cache
  btrfs subvolume create /mnt/@tmp          # Temporary files
  btrfs subvolume create /mnt/@snapshots    # System snapshots
  
  # Unmount to remount with subvolumes
  umount /mnt
  
  # Mount root subvolume with optimal options (includes SSD optimization)
  mount -o noatime,compress=zstd:3,space_cache=v2,discard=async,subvol=@ /dev/vg0/root /mnt
  
  # Create directories for mounting subvolumes and EFI partition
  mkdir -p /mnt/{boot,home,var,tmp,.snapshots}
  
  # Mount all subvolumes with optimal options
  mount -o noatime,compress=zstd:3,space_cache=v2,discard=async,subvol=@home /dev/vg0/root /mnt/home
  mount -o noatime,compress=zstd:3,space_cache=v2,discard=async,subvol=@var /dev/vg0/root /mnt/var
  mount -o noatime,compress=zstd:3,space_cache=v2,discard=async,subvol=@tmp /dev/vg0/root /mnt/tmp
  mount -o noatime,compress=zstd:3,space_cache=v2,discard=async,subvol=@snapshots /dev/vg0/root /mnt/.snapshots
  
  # Mount EFI partition
  mount /dev/nvme0n1p1 /mnt/boot
  ```

### Installation

#### 13. Install Essential Packages

- Install the base system with all necessary packages:
  ```bash
  pacstrap /mnt base linux linux-firmware btrfs-progs lvm2 cryptsetup zram-generator
  ```

#### 14. Generate Fstab

- Generate the fstab file:
  ```bash
  genfstab -U /mnt >> /mnt/etc/fstab
  ```
- Verify:
  ```bash
  cat /mnt/etc/fstab
  ```

#### 15. Chroot into the Installed System

- Change root into the new system:
  ```bash
  arch-chroot /mnt
  ```

### Configuration

#### 16. Set Time Zone (I live in Sweden so will set my timezone)

- Set your time zone:
  ```bash
  ln -sf /usr/share/zoneinfo/Europe/Stockholm /etc/localtime
  hwclock --systohc
  ```

#### 17. Configure Locale

- Uncomment your locale in `/etc/locale.gen` (e.g., `en_US.UTF-8 UTF-8`):
  ```bash
  nano /etc/locale.gen
  ```
- Generate locales:
  ```bash
  locale-gen
  ```
- Set system language:
  ```bash
  echo "LANG=en_US.UTF-8" > /etc/locale.conf
  ```

#### 18. Set Hostname

- Choose and set your hostname (replace 'your-hostname' with desired name):
  ```bash
  echo "your-hostname" > /etc/hostname
  ```
  
- Configure hosts file:
  ```bash
  cat > /etc/hosts << EOF
127.0.0.1   localhost
::1         localhost
127.0.1.1   your-hostname.localdomain your-hostname
EOF
  ```
  
  **Note**: Replace 'your-hostname' with the same name you used above.

#### 19. Configure Initramfs with mkinitcpio

- Edit mkinitcpio configuration:
  ```bash
  nano /etc/mkinitcpio.conf
  ```
  
- Update the HOOKS line to include encryption and LVM support:
  ```bash
  HOOKS=(base udev autodetect modconf kms keyboard keymap consolefont block encrypt lvm2 filesystems fsck)
  ```
  
- Regenerate initramfs:
  ```bash
  mkinitcpio -P
  ```

#### 20. Install and Configure GRUB

- Install GRUB:
  ```bash
  pacman -S grub efibootmgr
  ```
- Install GRUB to EFI partition:
  ```bash
  grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
  ```
- Get UUID of encrypted partition:
  ```bash
  blkid /dev/nvme0n1p2
  ```
- Edit `/etc/default/grub`:
  ```bash
  nano /etc/default/grub
  ```
  Update the GRUB_CMDLINE_LINUX line:
  ```
  GRUB_CMDLINE_LINUX="cryptdevice=UUID=<UUID_of_nvme0n1p2>:cryptlvm root=/dev/vg0/root rootflags=subvol=@"
  ```
- Generate GRUB configuration:
  ```bash
  grub-mkconfig -o /boot/grub/grub.cfg
  ```

#### 21. Set Root Password and Create a User

- Set the root password:

  ```bash
  passwd
  ```

- Create a new user:

  ```bash
  useradd -m -G wheel yourusername
  passwd yourusername
  ```

- Allow wheel group to use sudo:

  ```bash
  pacman -S sudo
  EDITOR=nano visudo
  ```

  Uncomment the line:

  ```bash
  %wheel ALL=(ALL:ALL) ALL
  ```

#### 22. Configure Zram (Replaces Traditional Swap)

- Configure zram for swap:
  ```bash
  cat > /etc/systemd/zram-generator.conf << EOF
[zram0]
zram-size = ram / 2
compression-algorithm = zstd
EOF
  ```

- Enable zram service:
  ```bash
  systemctl enable systemd-zram-setup@zram0.service
  ```

#### 23. Finalize

- Exit chroot:
  ```bash
  exit
  ```
- Unmount partitions:
  ```bash
  umount -R /mnt
  ```
- Close encrypted volume:
  ```bash
  vgchange -an vg0
  cryptsetup close cryptlvm
  ```
- Reboot:
  ```bash
  reboot
  ```

---

## Part 2: Additional Setup for Dell XPS 9520

### Post-Installation: Verify Zram Setup

After first boot, verify zram is working:

```bash
# Check zram status
swapon --show
zramctl

# Should show zram0 device with compression
```

### Desktop Environment Installation (Choose One)

#### Option 1: GNOME (Wayland by Default)

- Install essential GNOME packages with Wayland support:
  ```bash
  pacman -S gnome-shell gdm gnome-control-center gnome-terminal nautilus gnome-text-editor xdg-user-dirs-gtk xdg-desktop-portal-gnome
  ```
- Enable GDM (uses Wayland by default):
  ```bash
  systemctl enable gdm.service
  ```

#### Option 2: Hyprland (Pure Wayland Tiling with GNOME Tools)

- Install Hyprland with GNOME integration:
  ```bash
  pacman -S hyprland waybar mako wofi nautilus xdg-user-dirs xdg-desktop-portal-hyprland xdg-desktop-portal-gtk gnome-keyring polkit-gnome
  ```
- Create basic Hyprland config:
  ```bash
  mkdir -p ~/.config/hypr
  cp /usr/share/hyprland/hyprland.conf ~/.config/hypr/
  ```

**Note**: Both options use Wayland by default. After desktop environment installation, install recommended packages from `recommended-packages.md` for a complete system.

### Optional Configuration

- Customize GNOME settings and install additional GNOME-specific utilities as needed.

### Nvidia GPU Setup

- Check docs for how to install NVIDIA drivers for Dell XPS 9520.