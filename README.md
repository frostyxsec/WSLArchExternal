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

## 📄 License

MIT License

---

## 💬 Credits

Created by frosty
Contributions, improvements, and pull requests are welcome!
```
