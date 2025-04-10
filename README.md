# Arch Linux on WSL2 (External Storage Setup)

This repository contains a step-by-step guide to install **Arch Linux** on **WSL2** (Windows Subsystem for Linux) and store the distribution on an **external hard drive** to save internal storage.

## 🧰 Requirements

- Windows 10/11 with WSL2 enabled
- External drive (e.g., `E:\`)
- Arch Linux rootfs (`.tar.gz`) or AppX package (if using AppX package, extract AppX package first)
- Basic PowerShell knowledge

---

## 🚀 Installation

### 1. Download Arch Linux rootfs

You can get the official rootfs from [https://github.com/yuk7/ArchWSL/releases](https://github.com/yuk7/ArchWSL/releases)  
Download the `.tar.gz` or AppX package.

### 2. Create installation folder on external drive

```powershell
mkdir E:\Arch
```

### 3. Import Arch Linux to WSL

Using `.tar.gz` rootfs:

```powershell
wsl --import ArchExt E:\Arch E:\Arch\rootfs.tar.gz
```

> Replace `ArchExt` with any name you want for your WSL distro.

---

## 👤 Create a Non-Root User

### 1. Launch the distro

```powershell
wsl -d ArchExt
```

### 2. Create a user

Inside WSL terminal:

```bash
useradd -m -G wheel -s /bin/bash youruser
passwd youruser
```

### 3. Allow `sudo` for wheel group

Edit sudoers file:

```bash
EDITOR=nano visudo
```

Uncomment this line:

```bash
%wheel ALL=(ALL:ALL) ALL
```

Save and exit (`Ctrl + O`, `Enter`, then `Ctrl + X`).

---

## 🔧 Set Default User for WSL

WSL imported via `--import` doesn't support `--set-default-user`, so:

### 1. Get user ID:

```bash
id -u youruser
```

Example output: `1000`

### 2. Open Registry Editor:

Navigate to:

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss
```

Find the distro with `DistributionName` = `ArchExt`, and add new DWORD key:

- **Name:** `DefaultUid`
- **Value:** User ID (e.g., `1000`) in **Decimal**

### 3. Restart WSL:

```powershell
wsl --shutdown
```

Then start it again:

```powershell
wsl -d ArchExt
```

You should now log in as your user (`youruser`), not root.

---

## 🎨 Extras (Optional)

### Install `neofetch`:

```bash
sudo pacman -Syu neofetch
neofetch
```

### Install `yay` (AUR helper):

```bash
sudo pacman -S git base-devel
cd ~
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

---

## 📸 Screenshot

![neofetch](assets/neofetch.png)

---

## ✅ Notes

- Always use `sudo` when using `pacman` as a normal user.
- WSL does not support full systemd by default (though workarounds exist).
- Use `wsl -l -v` to list all installed distributions.

---

# 🐧 Arch Linux on WSL with Yay Support

A guide to set up Arch Linux on Windows Subsystem for Linux (WSL) with `yay` AUR helper, using a **non-root user** for safety.

> ⚠️ This guide assumes you already have Arch Linux installed under WSL. If not, please install a WSL Arch base from [https://github.com/yuk7/ArchWSL](https://github.com/yuk7/ArchWSL) or any other trusted repo.

---

## 📜 Prerequisites

Make sure you:
- Are **not running WSL as root**
- Have internet access inside WSL
- Have updated your system using `sudo pacman -Syu`

---

## ✅ Configuration

### Step 1: Enable systemd & set default user

Create or edit `/etc/wsl.conf`:

```ini
[boot]
systemd=true

[automount]
enabled = true
options = "metadata"
mountFsTab = true

[user]
default=youruser
```

> Replace `youruser` with your actual non-root username.

Then restart WSL:

```powershell
wsl --shutdown
```

Run Arch again:

```powershell
wsl -d <YourDistroName>
```

Now it should log in as your normal user.

---

## 📦 Install Base Development Tools

From your non-root user (e.g., `youruser`):

```bash
sudo pacman -Syu git base-devel
```

---

## 🔧 Install Yay (AUR Helper)

```bash
cd ~
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

> ❗ **Do NOT run `makepkg` as root.** It will throw an error and can harm your system.

---

## 🎉 Usage

Now you can install AUR packages easily:

```bash
yay -S <package-name>
```

Example:

```bash
yay -S google-chrome
```

---

## 💡 Tips

- If you see errors with package downloads, try switching mirrors with `sudo reflector` or edit `/etc/pacman.d/mirrorlist`.
- You can configure yay with `yay --editmenu` to your preference.

---

## 🛑 Troubleshooting

**Q: I still log in as root even after editing wsl.conf?**

A: Make sure you've shut down WSL fully:
```powershell
wsl --shutdown
```

Also, confirm your user exists:
```bash
id youruser
```

If not, create it:
```bash
useradd -m youruser
passwd youruser
```

---

## DONT PLUG OUT THE EXTERNAL STORAGE WHILE YOUR DEVICE IS ACTIVE, TURN OFF FIRST

## 💬 Credits

Created by frosty
