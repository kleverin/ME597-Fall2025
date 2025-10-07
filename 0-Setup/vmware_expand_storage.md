# Expanding Storage for vmware

## Issue
As you use your VM, your image may outgrow the space you originally allocated to it. This can cause issues including preventing it from booting. You may get a warning like `root filesystem full`. This is a sign to either delete some files or expand your storage on vm and your ubuntu partition.

## Expanding VM storage
To expand your available storage you must do two things:
1. Expand VM allocated storage
2. Expand Ubuntu Partition

#1 is the equivalent of physically increasing the storage capacity of the machine, almost like installing another storage device. Follow the instructions here to do that: [VMware Docs - Expand a Virtual Hard Disk](https://docs.vmware.com/en/VMware-Workstation-Pro/17/com.vmware.ws.using.doc/GUID-73BEB4E6-A1B9-41F4-BA37-364C4B067AA8.html)

#2 expands the Ubuntu partition to begin using that newly available storage. Follow these instructions: [Ubuntu Docs - Adjust the Size of a Filesystem](https://help.ubuntu.com/stable/ubuntu-help/disk-resize.html.en)

## View your partitions
Open the 'disks' application. Or, via terminal, do `sudo lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT,LABEL`

## View your storage
Use `df -h` to check your available storage in that partition.

## If your VM crashes because it runs out of storage:
You will need to:
1. Open recovery mode from BIOS
2. Mount your root partition to get access to your normal filesystem
3. Delete some files to make space
4. Exit and boot normally
5. Expand your VM storage, or delete more files you don't need


Follow these instructions: [Ubuntu Docs - Recovery Mode](https://wiki.ubuntu.com/RecoveryMode)


# Expanding Storage for vmware after it stops booting.

## Issue
If you fail to expand your filesystem in time (by following both steps #1 and #2 above), the os might refuse to boot. If you are facing this issue, you can expand the size in recovery mode. For this, follow the steps below.

## Steps to resolve

1. Make sure you made space by powering off the VM and expanding the storage in the settings (#1 above).
2. Go into recovery mode by hitting Esc as soon as it turns on. See [link](https://platform9.com/kb/openstack/how-to-boot-into-recovery-mode-on-an-ubuntu-cloud-image).
3. Drop to the root terminal.
4. Type `lsblk`. Ensure the disk size increased and find the main partition number, where the file system is, based on its size. For example, the disk could be `/dev/sda`, since it is now the size you expanded it to, and the partition could be `/dev/sda3` since it was sized that much initially.
5. Type `sudo parted /dev/sda`. Replace with the disk you noted.
6. After it opens into `/dev/sda`, type `resizepart 3 100%` and type yes. This should grow your partition size to fill the free space left. Replace the command with the partition number in place of 3 (3 from /dev/sda3).
7. Now exit the parted interface by clicking `ctrl + c`.
8. Type `sudo resize2fs /dev/sda3` in the root terminal and click enter. The filesystem should now grow to utilize the full partition.
9. Confirm by typing `df -hT`, and check if the filesystem now is using the new space (check the size) as you entered. Replace with the partition you noted.
10. Click `ctrl + D` to go back to the recovery menu. Click enter on resume and the boot should work.






