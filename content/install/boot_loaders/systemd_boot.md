# `systemd-boot`

`systemd-boot` is an EFI boot loader that can be used with Arch Linux. It is lightweight, faster than GRUB and works great with Intel-based Macs, but only supports EFI and requires manual configuration.

## <a id="facts" href="#facts">Facts</a>

* `systemd-boot` inherits filesystem support from the firmware. This is guaranteed to be FAT12, FAT16 and FAT32, so a FAT32 EFI system partition is recommended.
* `systemd-boot` is included in the `systemd` package, which is a dependency of the `base` meta package, so no additional packages have to be installed manually.
* The OS kernels that the bootloader is to load are actually stored in the EFI system partition on EFI systems.
  * However, they can optionally be stored on a separate partition of type "Linux Extended Boot" (XBOOTLDR) instead of resizing the existing EFI system partition if it's not big enough.<br>
  This is covered on the official Arch Linux wiki [here](https://wiki.archlinux.org/title/Systemd-boot#Installation_using_XBOOTLDR).
* Secure boot is not covered in this page, click [here](https://wiki.archlinux.org/title/Systemd-boot#Signing_for_Secure_Boot) for it instead, or [just don't bother](../index.md#secure-boot).

## <a id="install" href="#install">Installation</a>

If chrooted, install systemd-boot with:
```
# bootctl install
```

Otherwise:
```
# bootctl install --esp-path=/mnt/boot
```

## <a id="update" href="#update">Updates</a>

`systemd-boot` can be updated manually or automatically. I will be covering automatic updates, click [here](https://wiki.archlinux.org/title/Systemd-boot#Manual_update) for manual updates.

There are 2 solutions for automatic updates: `systemd-boot-update.service` and a `pacman` hook. I will be covering the `systemd` service, click [here](https://wiki.archlinux.org/title/Systemd-boot#pacman_hook) for the `pacman` hook.

Enabling this service is as simple as running this. Make sure you are chrooted if running this during Arch Linux installation:
```
# systemctl enable systemd-boot-update.service
```

This will cause `systemd-boot` to update on each boot if an update is available.

## <a id="config" href="#config">Configuration</a>

If chrooted, exit with `exit` or Ctrl+d.

### <a id="main-config" href="#main-config">Main Configuration</a>

Edit `<esp mountpoint>/loader/loader.conf`. In the case of a new Arch Linux installation, this is `/mnt/boot/loader/loader.conf`. Set the contents to something like this:
```
default arch.conf
timeout 4
console-mode max
editor no
```

Explanation (full explanation [here](https://man.archlinux.org/man/loader.conf.5#OPTIONS)):
* `default`: The default OS to boot. `arch.conf` specifies our Arch Linux install.
  * Fun Fact: This is a glob pattern, so patterns like `?`, `*` and `[...]` are supported.
  * You can set this to `@saved` instead so that it will remember the last option you select.<br>
  This is useful for when you want to reboot a non-default OS.
* `timeout`: How long the boot menu should wait for the user to choose an entry before booting the default one.
  * You can set this to 0 so that it won't ask unless you hold space after powering the hardware on.
  * Even with only 1 OS installed, there will be a 2nd option: A fallback `initramfs`. Consider this before setting this value to 0.
* `console-mode`: The size of the boot menu's console. `max` is the maximum size, attempting to match the screen size.

### <a id="entry-config" href="#entry-config">Entry Configuration</a>

Create 2 entry files under `<esp mountpoint>/loader/entries`. In the case of a new Arch Linux installation, this is `/mnt/boot/loader/entries`.

Make sure to create these files with no trailing EOLs. This is because the rest of the files will be appended via a command. In `vim`, you can achieve this with these commands before saving the file:
```
:set noeol
:set nofixeol
```

You can `cat` them to check if they were created without a trailing EOL, indicated by a highlighted `#` at the end of the output. Alternatively, you can just remove the extra line break after appending the rest of the files.

The contents of both files should be something like this:

`arch.conf`:
```
title Arch Linux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options root=UUID=
```

`arch-fallback.conf`:
```
title Arch Linux (fallback initramfs)
linux /vmlinuz-linux
initrd /initramfs-linux-fallback.img
options root=UUID=
```

Assuming the root partition of your Arch Linux install is `/dev/sda2`, append the UUID and `rw` statement to both files using:
```
# echo $(lsblk -dno UUID /dev/sda2) rw >> <esp mountpoint>/loader/entries/arch.conf
# echo $(lsblk -dno UUID /dev/sda2) rw >> <esp mountpoint>/loader/entries/arch-fallback.conf
```

Explanation:
* `-d` hides block device hierarchy. For example:
  * `lsblk -d /dev/sda2` hides it's parent device, `/dev/sda`.
  * `lsblk -d /dev/sda` hides it's partitions such as `/dev/sda2`.
* `-n` hides the column headers.
* `-o UUID` cause the UUID to be the only column shown.

The files should now look something like:

`arch.conf`:
```
title Arch Linux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options root=UUID=xxxxxxxx-xx-xx-xx-xxxxxxxxxxxx rw
```

`arch-fallback.conf`:
```
title Arch Linux (fallback initramfs)
linux /vmlinuz-linux
initrd /initramfs-linux-fallback.img
options root=UUID=xxxxxxxx-xx-xx-xx-xxxxxxxxxxxx rw
```

## <a id="check" href="#check">Checking</a>

You can check if `systemd-boot` can parse the configuration properly. If chrooted, run:
```
# bootctl
```

Otherwise:
```
# bootctl --esp-path=/mnt/boot
```