# NVIDIA GPU Configuration for Dell XPS 9520

This guide configures the NVIDIA GeForce RTX 3050 Ti Mobile GPU for optimal power management and hybrid graphics support.

## Prerequisites

**Note**: Complete the base system installation first using [`arch-base-installation.md`](arch-base-installation.md) and install essential packages from [`recommended-packages.md`](recommended-packages.md).

Install necessary tools and drivers:

### 1. Install NVIDIA Drivers and Utilities

```bash
# NVIDIA drivers and prime support
sudo pacman -S nvidia nvidia-utils nvidia-prime

# Verification tools
sudo pacman -S vulkan-tools mesa-utils
```

### 2. Install AUR Helper (if not already installed)

```bash
# Install paru for AUR packages
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

### 3. Install EnvyControl

```bash
# Hybrid GPU mode management
paru -S envycontrol
```

---

## Set Up Hybrid Mode

Configure EnvyControl to hybrid mode for optimal power management:

```bash
# Enable hybrid mode with RTD3 power management
envycontrol --switch hybrid --rtd3=3

# Reboot to apply changes
sudo reboot
```

**What this does:**
- Intel GPU used by default (better battery life)
- NVIDIA GPU powers down when not in use
- NVIDIA GPU activates on-demand with `prime-run`

---

## Fix GNOME + Wayland + NVIDIA Issues

**Problem**: GNOME 43+ forces itself onto the discrete GPU instead of using the integrated GPU in Wayland setups, preventing proper power management.

**Solution**: Configure EGL vendor priority and force GNOME to use Intel GPU.

**Reference**: [GNOME GitLab Issue #2969](https://gitlab.gnome.org/GNOME/mutter/-/issues/2969)

---

### 1. Change EGL Vendor Priority

Ensure Mesa (Intel) is prioritized over Nvidia for EGL-based applications like GNOME Shell.

```bash
sudo mv /usr/share/glvnd/egl_vendor.d/10_nvidia.json /usr/share/glvnd/egl_vendor.d/90_nvidia.json
```

---

### 2. Configure Environment Variables

Set system-wide environment variables to force Intel for rendering and Vulkan-based applications.

```bash
sudo nano /etc/environment
```

Add:

```bash
__EGL_VENDOR_LIBRARY_FILENAMES=/usr/share/glvnd/egl_vendor.d/50_mesa.json
VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/intel_icd.x86_64.json
```

Apply the changes:

```bash
sudo reboot
```

---

### 3. Add GNOME-Specific Configuration

Create a configuration file to ensure GNOME Shell always uses Intel, even if Nvidia drivers are loaded:

```bash
sudo nano /etc/environment.d/99-gnome-disable-nvidia.conf
```

Add:

```bash
__EGL_VENDOR_LIBRARY_FILENAMES=/usr/share/glvnd/egl_vendor.d/50_mesa.json
VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/intel_icd.x86_64.json
```

---

## Verification and Testing

### 1. Check NVIDIA GPU Power State

Verify the runtime power status of the GPU:

```bash
cat /sys/bus/pci/devices/0000:01:00.0/power/runtime_status
```

- **`suspended`**: GPU is in a low-power state (EnvyControl stays here in hybrid mode).
- **`D3cold`**: GPU is fully powered off (optimal state).

Monitor the GPU power state dynamically:

```bash
watch -n 1 cat /sys/bus/pci/devices/0000:01:00.0/power/runtime_status
```

---

### 2. Check if NVIDIA GPU is Idle

Confirm no processes are using the Nvidia GPU:

```bash
nvidia-smi
```

Output should show `No running processes found` under `Processes:`.

Check for any processes holding GPU resources:

```bash
sudo lsof /dev/nvidia*
```

---

### 3. Check EGL Renderer

Verify that Intel is being used for rendering:

```bash
glxinfo | grep "OpenGL renderer"
```

Expected output: **Intel Integrated Graphics**.

---

### 4. Check Vulkan Renderer

Confirm Intel Vulkan driver is being used:

```bash
vulkaninfo | grep "GPU id"
```

Look for the Intel GPU ID.

---

### 5. Test NVIDIA GPU on Demand

Use Nvidia GPU with `prime-run` to ensure it powers up when needed:

```bash
prime-run glxinfo | grep "OpenGL renderer"
```

Expected output: **Nvidia Renderer**.

---

### 6. Check Logs for Errors

Review logs for any Nvidia-related errors:

```bash
dmesg | grep -i nvidia
```

---

## Summary

This configuration ensures:

✅ **GNOME Shell uses Intel GPU** - Better battery life and proper Wayland support  
✅ **NVIDIA GPU powers down** - `D3cold` or `suspended` state when idle  
✅ **On-demand NVIDIA usage** - Use `prime-run <application>` for gaming/rendering  
✅ **Hybrid graphics working** - Best of both worlds: efficiency + performance

### Usage Examples

```bash
# Run application with NVIDIA GPU
prime-run steam
prime-run blender
prime-run glxgears

# Normal applications use Intel GPU automatically
firefox
gnome-shell
vscode
```

**Result**: Optimal battery life with high-performance graphics available when needed.
