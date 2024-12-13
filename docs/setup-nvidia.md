# **Configure NVIDIA GeForce RTX 3050 Ti Mobile GPU on Dell XPS 9520**

## **Prerequisites**

Install necessary tools and drivers using `pacman` or `yay`:

### **1. Install Nvidia Drivers and Utilities**

For Nvidia drivers and tools:

```bash
sudo pacman -S nvidia nvidia-utils nvidia-prime
```

### **2. Install EnvyControl**

To manage hybrid GPU modes:

```bash
yay -S envycontrol
```

### **3. Install Vulkan and OpenGL Utilities**

To verify rendering and Vulkan driver usage:

```bash
sudo pacman -S vulkan-tools mesa-utils
```

---

## **Set Up Hybrid Mode**

Configured **EnvyControl** to hybrid mode so the Intel GPU is used by default, and the Nvidia GPU is only used on-demand using fine-grained power management.

```bash
envycontrol --switch hybrid --rtd3=3
sudo reboot
```

---

## **Gnome Issue: Forces Itself on dGPU**

This is an issue with GNOME 43 and up, where it forces itself onto the dGPU instead of using the iGPU in Wayland setups. To fix this, we need to change EGL Vendor priority and configure GNOME to use the iGPU.

Read more [here](https://gitlab.gnome.org/GNOME/mutter/-/issues/2969).

---

## **1. Change EGL Vendor Priority**

Ensure Mesa (Intel) is prioritized over Nvidia for EGL-based applications like GNOME Shell.

```bash
sudo mv /usr/share/glvnd/egl_vendor.d/10_nvidia.json /usr/share/glvnd/egl_vendor.d/90_nvidia.json
```

---

## **2. Configure Environment Variables**

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

## **3. Add GNOME-Specific Configuration**

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

# **How to Verify Everything is Working**

## **1. Check Nvidia GPU Power State**

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

## **2. Check if Nvidia GPU is Idle**

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

## **3. Check EGL Renderer**

Verify that Intel is being used for rendering:

```bash
glxinfo | grep "OpenGL renderer"
```

Expected output: **Intel Integrated Graphics**.

---

## **4. Check Vulkan Renderer**

Confirm Intel Vulkan driver is being used:

```bash
vulkaninfo | grep "GPU id"
```

Look for the Intel GPU ID.

---

## **5. Test Nvidia GPU on Demand**

Use Nvidia GPU with `prime-run` to ensure it powers up when needed:

```bash
prime-run glxinfo | grep "OpenGL renderer"
```

Expected output: **Nvidia Renderer**.

---

## **6. Check Logs for Errors**

Review logs for any Nvidia-related errors:

```bash
dmesg | grep -i nvidia
```

---

This configuration ensures GNOME Shell, along with other default applications, always uses Intel, while the Nvidia GPU is powered down (`D3cold` or `suspended`) when idle. The Nvidia GPU will activate on demand when needed for tasks like gaming or rendering.
