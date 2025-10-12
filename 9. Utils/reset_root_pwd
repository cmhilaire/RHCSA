# Reset Root Password – RHEL 9

## Scenario
You have lost the **root password** and there are **no other admin (sudo) accounts** available. To regain access, you must boot into the **rescue (rd.break)** mode and reset the password **locally**.

---

## Step-by-Step Procedure

### 1️⃣ Reboot and Interrupt the GRUB Menu
1. Reboot the system.  
2. When the **GRUB** menu appears, highlight the default boot entry.  
3. Press **`e`** to **edit** the kernel boot parameters.

---

### 2️⃣ Modify the Kernel Line
Find the line that starts with `linux` or `linux16`, and at the **end of that line**, append: `rd.break`


> `rd.break` tells the system to break into the **initramfs (dracut) shell** before the real root filesystem mounts.

Press **`Ctrl + X`** to boot with this temporary setting.

---

### 3️⃣ Remount the System with Write Permissions
Once you’re dropped into the **emergency shell**, the real root filesystem is mounted read-only under `/sysroot`.

Make it writable:
```bash
mount -o remount,rw /sysroot
```

---

### 4️⃣ Switch to the Real Root Environment
```bash
chroot /sysroot
```
Now you’re effectively operating inside the system as root.

---

### 5️⃣ Reset the Root Password

Set a new password:
```bash
passwd root
```
Enter and confirm your new password.

---

### 6️⃣ Update SELinux Contexts

Because the password file was modified outside normal boot, ensure correct SELinux labeling:
```bash
touch /.autorelabel
```
This will trigger SELinux to relabel all files on the next reboot.

---

### 7️⃣ Exit and Reboot

Exit the chroot and the emergency shell:
```bash
exit
exit
```
The system will reboot.
Allow it to perform SELinux relabeling (it may take a few minutes).

---

### 8️⃣ Verify
After reboot, log in as root using your new password:
```bash
login: root
Password: <your-new-password>
```

---

### 9️⃣ Summary

| Step | Command/Action                 | Description               |
| ---- | ------------------------------ | ------------------------- |
| 1    | `e` at GRUB                    | Edit boot parameters      |
| 2    | Add `rd.break`                 | Boot into emergency shell |
| 3    | `mount -o remount,rw /sysroot` | Enable write mode         |
| 4    | `chroot /sysroot`              | Switch to system root     |
| 5    | `passwd root`                  | Set new root password     |
| 6    | `touch /.autorelabel`          | Fix SELinux labels        |
| 7    | `exit` twice, reboot           | Reboot to normal mode     |
