# How to delete LVM partition safely


## Steps to follow to delete LVM:

1. Check existing Logical Volumes:
   * `lvs or lvdisplay`

2. Optional: if a device is still in use, check/kill processes:
   * `lsof /dev/VG_NAME/LV_NAME      # Check used files. If not, should return no output`
   * `fuser -vm /dev/sdbX            # Check processes using the disk. If not, should show nothing`
   * `kill -15 PID1                  # For graceful termination first and then -9`

3. Unmount the LV (if mounted):
   * `umount /path/to/mount_point`

4. Check existing FSTAB:
   * `vi /etc/fstab`

5. Remove the Logical Volume:
   * `lvremove /dev/VG_NAME/LV_NAME`
   * `lvremove -f /dev/VG_NAME/LV_NAME # (Use -f to force removal if needed)`

6. Verify Volume Groups:
   * `vgs or vgdisplay`

7. Deactivate the VG (if active):
   * `vgchange -a n VG_NAME`

8. Remove the Volume Group:
   * `vgremove VG_NAME`

9.  Check Physical Volumes:
    * `pvs or pvdisplay`

10. Remove the PV from LVM:
    * `pvremove /dev/DISK_NAME`

11. Wipe LVM signatures (Optional if reusing disk):
    * `wipefs -a /dev/DISK_NAME`

12. Remove a partition:
    * `fdisk /dev/sdX`
    * Press `d → 1` (partition number to delete).
    * Confirm deletion and save with `w`.