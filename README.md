# LFS-Doc

# How to Run LFS on VirtualBox

This guide covers installing VirtualBox on a Linux host system and setting up a pre-built Linux From Scratch (LFS) virtual disk.

---

## 1. Install VirtualBox (Linux Host)

If you don't have VirtualBox installed, use the instructions for your distribution below.

### Ubuntu / Debian / Linux Mint
```bash
sudo apt update
sudo apt install virtualbox virtualbox-ext-pack
```

### Fedora / RHEL
```bash
sudo dnf install VirtualBox
```

### Arch Linux / Manjaro
```bash
sudo pacman -Syu virtualbox virtualbox-host-modules-arch
```

> **Note:** After installation, it is recommended to add your user to the `vboxusers` group and reboot or re-login:
> ```bash
> sudo usermod -aG vboxusers $USER
> ```

---

## 2. Extract the Image File

Extract the received archive `lfs-system.vdi.tar.gz` to a directory of your choice:

```bash
tar -xzvf lfs-system.vdi.tar.gz
```

Ensure the unpacked `lfs-system.vdi` file is accessible.

---

## 3. Create the Virtual Machine

1. Open **VirtualBox** and click **New**.
2. Fill in the basic configuration:
   * **Name:** `Linux From Scratch`
   * **Type:** `Linux`
   * **Version:** `Linux 2.6 / 3.x / 4.x / 5.x / 6.x (64-bit)`
3. Set the hardware resources:
   * **Base Memory (RAM):** At least **2048 MB** (2 GB).
   * **Processors:** At least **2 CPUs**.

---

## 4. Attach the Existing Virtual Hard Disk

1. In the **Hard Disk** step, choose **"Use an existing virtual hard disk file"**.
2. Click the folder icon, choose **Add**, and select the extracted `lfs-system.vdi` file.
3. Confirm your selection and finish creating the VM.

---

## 5. Boot Configuration (Important)

Open the VM's **Settings > System > Motherboard**:

* **Legacy BIOS / MBR:** Keep **"Enable EFI (special OSes only)"** unchecked.
* **UEFI:** Check **"Enable EFI (special OSes only)"**.

---

## 6. Start the VM

1. Select your new VM and click **Start**.
2. The GRUB boot menu will appear, and the system will proceed to boot.
3. At the login prompt, sign in using:
   * **Username:** `root`


# LFS Security Hardening & Permissions

## 1. What We Configured

* **File Ownership:** All main folders (`/bin`, `/etc`, `/usr`, `/lib`, etc.) belong to `root:root` to remove any leftover files from the `lfs` build user.
* **Password Protection:** Set `/etc/shadow` to `600` so only `root` can read password hashes.
* **SUID Limits:** Only `passwd` and `su` have the SUID bit (`4755`) so normal users can change passwords and switch users. Tools like `mount` and `umount` stay at `755` for safety.
* **Wheel Group:** Added `SU_WHEEL_ONLY yes` in `/etc/login.defs`. Only members of the `wheel` group can run `su -`.

---

## 2. How to Test

### 1. Check SUID binaries
Make sure only `su` and `passwd` show up:
```bash
find / -xdev \( -perm -4000 -o -perm -2000 \) -type f -exec ls -la {} + 2>/dev/null
```
---

# Configuration Tracking with Git in /etc

## 1. Why Git in /etc?

* **Audit & History:** Tracks every change made to system configurations, recording who changed what and when.
* **Instant Recovery:** Lets you roll back broken or accidentally edited files in seconds without needing system reinstalls.
* **Integrity Baseline:** Provides a clean "checkpoint" of the system at delivery time.
* **Security:** The `/etc/.git` folder is locked to `root:root` (`chmod 700`) to prevent unprivileged users from reading historical changes or secrets.

---

## 2. How to Test

### 1. Check current baseline status
Ensure the repository is clean and tracking `/etc`:
```bash
cd /etc
git status
