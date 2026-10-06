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
