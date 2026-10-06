# LFS-Doc

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
