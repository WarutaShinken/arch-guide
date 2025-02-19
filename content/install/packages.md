# Recommended Installation Packages

I recommend these packages:

* `base`, `linux` and `linux-firmware` - The packages needed for Linux itself.
* `intel-ucode` or `amd-ucode` - The CPU microcode package, keeps the microcode up-to-date. Use the one corresponding to your brand of CPU.
* Boot loader packages such as `syslinux` or `grub`.
* `man-db` and `man-pages` - For use of `man`.
* `vim` and `nano` - Text editors, required for editing config files.
* `less` - For scrolling long command outputs.
* `ntfs-3g` - Required to access NTFS filesystems.
* `dosfstools` - Required to format FAT filesystems such as FAT32.
* `mdadm` - Require to access Linux RAID.
* `sudo` - For running commands as `root` without manually logging in as `root`.
* `networkmanager` and `dhcpcd` - Required for internet access.
* `sudo`, `git` and `base-devel` - Required for cloning and building AUR packages.
  * `git` is important for developers and installing software from Git repos.
* `htop` - Monitors and manages processes and shows CPU, RAM and SWAP usage.
* `nmon` - Useful for monitoring disk activity.
* `lm_sensors` - Useful for monitoring system temperature.
* `lvm2` - LVM (Logical Volume Manager)
* Device firmware not included in `linux-firmware` such as `sof-firmware` for sound cards.

Add or remove packages to your list based on what you need or want.