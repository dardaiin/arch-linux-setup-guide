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

#### 5. Set the console keyboard layout and font

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

#### 1. Identify Disks

- List available disks:
  ```bash
  lsblk
  # Or use fdisk
  fdisk -l
  ```

#### 2. Partition the Disk

Use fdisk or other tool.

  ```bash
  fdisk nvme0n1
  ```

- Create partitions:
  - **EFI Partition**: `/dev/nvme0n1p1` (512 MB, type EFI System)
  - **Root Partition**: `/dev/nvme0n1p2` (remaining space minus swap size)
  - **Swap Partition**: `/dev/nvme0n1p3` (swap size = RAM size, e.g., 16 GB)

#### 3. Encrypt Partitions

- Encrypt root and swap:

  ```bash
  cryptsetup luksFormat /dev/nvme0n1p2
  cryptsetup open /dev/nvme0n1p2 cryptroot

  cryptsetup luksFormat /dev/nvme0n1p3
  cryptsetup open /dev/nvme0n1p3 nvme0n1p3
  ```

#### 4. Format and Mount Partitions

- Using fdisk
  ```bash
  # To list disks
  fdisk -l

  # Choose disk and start partition
  fdisk nvme0n1p
  ```
- Format partitions:
  ```bash
  mkfs.fat -F32 nvme0n1p1
  mkfs.btrfs /dev/mapper/cryptroot
  mkswap /dev/mapper/cryptswap
  swapon /dev/mapper/cryptswap
  ```
- Create and mount Btrfs subvolumes:

  ```bash
  # Mount the Btrfs root
  mount /dev/mapper/cryptroot /mnt

  # Create subvolumes
  btrfs subvolume create /mnt/@       # Root filesystem
  btrfs subvolume create /mnt/@home  # User data
  btrfs subvolume create /mnt/@log   # Logs
  btrfs subvolume create /mnt/@cache # Cache
  btrfs subvolume create /mnt/@snapshots # Snapshots

  # Unmount the Btrfs root
  umount /mnt

  # Mount subvolumes with compression
  mount -o compress=zstd,subvol=@ /dev/mapper/cryptroot /mnt
  mkdir /mnt/{boot,home,var/log,var/cache,.snapshots}
  mount -o compress=zstd,subvol=@home /dev/mapper/cryptroot /mnt/home
  mount -o compress=zstd,subvol=@log /dev/mapper/cryptroot /mnt/var/log
  mount -o compress=zstd,subvol=@cache /dev/mapper/cryptroot /mnt/var/cache
  mount -o compress=zstd,subvol=@snapshots /dev/mapper/cryptroot /mnt/.snapshots
  mount nvme0n1p1 /mnt/boot
  ```

### Installation

#### 1. Install Essential Packages

- Install the base system:
  ```bash
  pacstrap /mnt base linux linux-firmware btrfs-progs
  ```

#### 2. Generate Fstab

- Generate the fstab file:
  ```bash
  genfstab -U /mnt >> /mnt/etc/fstab
  ```
- Verify:
  ```bash
  cat /mnt/etc/fstab
  ```

#### 3. Chroot into the Installed System

- Change root into the new system:
  ```bash
  arch-chroot /mnt
  ```

### Configuration

#### 1. Set Time Zone (I live in Sweden so will set my timezone)

- Set your time zone:
  ```bash
  ln -sf /usr/share/zoneinfo/Europe/Stockholm /etc/localtime
  hwclock --systohc
  ```

#### 2. Configure Locale

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

#### 3. Set Hostname

- Set the hostname:
  ```bash
  echo "archlinux" > /etc/hostname
  ```

#### 4. Configure Initramfs with Dracut

- Install Dracut:
  ```bash
  pacman -S dracut
  ```
- Create Dracut configuration:
  ```bash
  nano /etc/dracut.conf.d/crypt.conf
  ```
  Add:
  ```
  add_dracutmodules+=" crypt lvm resume btrfs "
  ```
- Regenerate initramfs:
  ```bash
  dracut --force --regenerate-all
  ```

#### 5. Install and Configure GRUB

- Install GRUB:
  ```bash
  pacman -S grub efibootmgr
  ```
- Install GRUB to EFI partition:
  ```bash
  grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
  
  # For regular BIOS
  grub-install --target=i386-pc /dev/nvme0n1p1
  ```
- Get UUID of partition
  ```bash
  sudo blkid /dev/nvme0n1p2
  ```
- Edit `/etc/default/grub`:
  ```bash
  nano /etc/default/grub
  ```
  Add:
  ```
  cryptdevice=UUID=<UUID_of_nvme0n1p2>:cryptroot resume=/dev/mapper/cryptswap
  ```
- Generate GRUB configuration:
  ```bash
  grub-mkconfig -o /boot/grub/grub.cfg
  ```

### 6. Set Root Password and Create a User

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

#### 7. Finalize

- Exit chroot:
  ```bash
  exit
  ```
- Unmount partitions:
  ```bash
  umount -R /mnt
  ```
- Reboot:
  ```bash
  reboot
  ```

---

## Part 2: Additional Setup for Dell XPS 9520

### Desktop Environment Installation / GNOME Installation Example

- Install GNOME:
  ```bash
  pacman -S gnome gnome-extra
  ```
- Enable GDM:
  ```bash
  systemctl enable gdm.service
  ```

- Reboot or start GDM:
  ```bash
  systemctl start gdm.service
  ```

### Optional Configuration

- Customize GNOME settings and install additional GNOME-specific utilities as needed.

### Nvidia GPU Setup

- Check docs for how to install NVIDIA drivers for Dell XPS 9520.