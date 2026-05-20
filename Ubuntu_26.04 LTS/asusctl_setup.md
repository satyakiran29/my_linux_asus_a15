# Running ASUS Armoury Crate Features on Ubuntu 26.04 🔥

Successfully configured the **ASUS TUF Gaming A15 FA506NC** on **Ubuntu 26.04 LTS (Resolute Raccoon)** with native Linux ASUS controls. 

This guide documents the full setup process, including troubleshooting steps to unlock the NVIDIA GPU disabled by Armoury Crate's Eco Mode in Windows, compiling tools from source, and resolving desktop application crashes.

---

## 💻 Laptop Specs
- **Model:** ASUS TUF Gaming A15 FA506NC
- **CPU:** AMD Ryzen 5 7535HS (with Radeon 680M Integrated Graphics)
- **GPU:** NVIDIA GeForce RTX 3050 Laptop GPU (4GB GDDR6)
- **OS:** Ubuntu 26.04 LTS (Resolute Raccoon)
- **Kernel:** Linux 7.0.0-generic

---

## 🚀 What We Achieved
- [x] **Revived NVIDIA GPU** directly from Linux (bypassing Windows Armoury Crate Eco Mode).
- [x] **Compiled & Configured** `asusctl` / `asusd` and `rog-control-center` from source.
- [x] **Resolved D-Bus Access Denied issues** for the ASUS daemon.
- [x] **Fixed `rog-control-center` crashes** caused by missing assets/icons.
- [x] **Activated performance profiles** (Performance, Balanced, Quiet) and fan curves.

---

## 🛠️ Step-by-Step Setup Guide

### 1. Enabling the NVIDIA GPU (Fixing Eco Mode)
When Armoury Crate's **Eco Mode** is enabled under Windows, the NVIDIA GPU is turned off at the firmware level. As a result, Linux commands like `lspci` or `nvidia-smi` will completely fail to detect the card.

Instead of booting back into Windows to toggle it, you can force-enable the GPU directly from Linux:

```bash
# Force enable the dedicated GPU at the ACPI platform level
echo 0 | sudo tee /sys/devices/platform/asus-nb-wmi/dgpu_disable

# Force a PCI bus rescan to detect the newly enabled hardware
echo 1 | sudo tee /sys/bus/pci/rescan
```

After running these, verify that the system detects the card:
```bash
lspci | grep -i nvidia
```

---

### 2. Building `asusctl` & `rog-control-center` from Source
Because Ubuntu 26.04 ("resolute") is a very new release, the official PPA repository (`ppa:aslatter/ppa`) returns a `404 Not Found` error. To work around this, compile the utilities from the official source repository:

```bash
# Clone the repository
git clone https://gitlab.com/asus-linux/asusctl.git
cd asusctl

# Build the release binaries
cargo build --release
```

The compiled binaries will be located under `target/release/`:
* `asusd` (System daemon)
* `asusctl` (CLI tool)
* `rog-control-center` (GUI dashboard)

---

### 3. Setting Up `asusd` System Service
Once built, install and enable the service so it runs at startup:

```bash
# Copy binaries to a standard path
sudo cp target/release/asusd /usr/local/bin/
sudo cp target/release/asusctl /usr/local/bin/
sudo cp target/release/rog-control-center /usr/local/bin/

# Enable and start the system service
sudo systemctl enable --now asusd
```

> [!NOTE]
> If you encounter a D-Bus `AccessDenied` error (e.g., connection is not allowed to own the service `xyz.ljones.Asusd`), ensure you have copied the D-Bus configuration files from the source repository's `data/` directory to `/usr/share/dbus-1/system.d/` and restarted the D-Bus service.

---

### 4. Fixing `rog-control-center` Panics (Missing Icons)
When launching the GUI (`rog-control-center`), it may crash immediately with a panic message such as:
`thread 'tokio-rt-worker' panicked at ... Missing icon: "/usr/share/icons/hicolor/512x512/apps/asus_notif_red.png"`

This happens because the asset icons are not automatically placed in the system's icon theme directory. To fix this:

```bash
# Ensure target directories exist
sudo mkdir -p /usr/share/icons/hicolor/512x512/apps

# Copy the notification tray icons
sudo cp ~/Desktop/asusctl/data/icons/*.png /usr/share/icons/hicolor/512x512/apps/

# Copy the main application icon
sudo cp ~/Desktop/asusctl/rog-control-center/data/rog-control-center.png /usr/share/icons/hicolor/512x512/apps/

# Update the system icon cache
sudo gtk-update-icon-cache /usr/share/icons/hicolor
```

After doing this, run `rog-control-center` from the terminal or launcher to open the GUI successfully!

---

## ⚡ Useful Control Commands

### Manage Power Profiles
Show available profiles:
```bash
asusctl profile list
```

Switch between power profiles:
```bash
# Set Performance Mode
asusctl profile set Performance

# Set Balanced Mode
asusctl profile set Balanced

# Set Quiet Mode
asusctl profile set Quiet
```

### GUI control
Launch the graphical configuration tool:
```bash
rog-control-center
```

Check active NVIDIA graphics settings:
```bash
nvidia-smi
```

---

## 🤝 Credits & Acknowledgements

This setup is made possible by the dedication of the **ASUS Linux** community. Special thanks to:
* **Luke Jones (Shadowryg / aslatter)** — Creator and lead developer of `asusctl`, `asusd`, and `supergfxctl`.
* **ASUS Linux Community** — For providing documentation, discord support, and resources at [asus-linux.org](https://asus-linux.org/).
* **Open Source Contributors** — Everyone helping make gaming and hardware management on Linux a seamless experience.

#Linux #Ubuntu #ASUS #GamingLaptop #NVIDIA #OpenSource #GNOME #Ryzen #LinuxGaming

---
*Tested and documented by Satyakiran.*
