# Installation

Traditionally, the Arch Linux install process is very manual. While the bootable image now comes with an automated installation tool called `archinstall`, it's still a good learning experience and way to get more control over the installation process. This guide contains instructions on how to do that.

If you want a more out-of-the-box experience with Arch Linux, complete with a desktop environment, I recommend you check out [EndeavourOS](https://endeavouros.com) instead.

## <a id="mac-support" href="#mac-support">Can I install Arch Linux on an Intel-Based Mac?</a>

Yes, I have successfully done this on an A1225 Mac with `systemd-boot` as my choice of bootloader. I will eventually cover installing `b43-firmware` to enable the usage of WiFi on it.

I will be trying this with an A1237 Macbook Air in the future.

## <a id="download" href="#download">Download Arch Linux</a>

Download Arch Linux from [here](https://archlinux.org/download), I recommend you use the BitTorrent download.

## <a id="verify" href="#verify">Verify the Integrity of Your Copy</a>

### <a id="gpg-install" href="#gpg-install">Install GnuPG</a>

If you are on another Linux system, GnuPG is most likely already installed. If you are on Windows, you can install GnuPG with Gpg4win, which should be listed on [the download page of GnuPG's official website](https://www.gnupg.org/download/index.html) **under "GnuPG Binary Releases"**.
- You can omit the Kleopatra, GpgOL and GpgEX components during installation.
- You must open a new terminal window after installing Gpg4win for it to be in `PATH`.

### <a id="gpg-usage" href="#gpg-usage">Verify with GnuPG</a>

Download the ISO PGP signature (under the "Checksums" section of the Arch Linux downloads page) to the same location as the image and run [GnuPG](https://www.gnupg.org) on it:
```
gpg --keyserver-options auto-key-retrieve --verify archlinux-<version>-x86_64.iso.sig
```

The output should look something like this (the `Good signature from <developer name and email>` line is the important part):
```
gpg: assuming signed data in 'archlinux-<version>-x86_64.iso'
gpg: Signature made <date and time in your timezone>
gpg:                using <algorithm> key <hexadecimal key fingerprint>
gpg:                issuer "<developer email>"
gpg: <path to trustdb.gpg>: trustdb created
gpg: key <part of key fingerprint>: public key "<developer name and email>" imported
gpg: key <part of key fingerprint>: public key "<developer name and email>" imported
gpg: key <part of key fingerprint>: "<developer name and email>" not changed
gpg: key <part of key fingerprint>: "<developer name and email>" not changed
gpg: Total number processed: 4
gpg:               imported: 2
gpg:              unchanged: 2
gpg: no ultimately trusted keys found
gpg: Good signature from "<developer name and email>" [unknown]
gpg: WARNING: The key's User ID is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: <key fingerprint>
```

Find the developer shown in the output [here](https://archlinux.org/people/developers) and check that the primary key fingerprint matches the "PGP Key" field.

### <a id="verify-on-arch" href="#verify-on-arch">Verify with `pacman-key`</a>

You can also verify the image from another (hopefully already verified) Arch Linux system using `pacman-key` instead (make sure both files are in the same directory like before):
```
pacman-key -v archlinux-<version>-x86_64.iso.sig
```

If the image is fine, the output should look something like this:
```
==> Checking archlinux-<version>-x86_64.iso.sig... (detached)
gpg: Signature made <date and time in your timezone>
gpg:                using <algorithm> key <hexadecimal key fingerprint>
gpg:                issuer "<developer email>"
gpg: Good signature from "<developer name and email>" [full]
gpg:                 aka "<developer name and email>" [unknown]
```

Again, compare the PGP key shown in the output with the one [here](https://archlinux.org/people/developers), making sure to match the developer name and email.

## <a id="burn-media" href="#burn-media">Prepare an Installation Medium</a>

You can flash the image onto a thumbdrive with a tool like [Rufus](http://rufus.ie/en) or attach the ISO directly if you're installing it on a virtual machine.

Rufus has an installed and portable (non-installed) version.

Rufus formatted thumbdrives are bootable on old Intel-based Macs, unlike USB multibooting solutions like YUMI and Ventoy which don't support very old machines.

## <a id="boot-media" href="#boot-media">Boot From the Installation Medium</a>

It is assumed you know how to boot from a USB drive. Pick the first option in the GRUB menu. Wait until you are presented with a shell prompt.

### <a id="secure-boot" href="#secure-boot">Secure Boot</a>

Note that Arch Linux installation images do not support Secure Boot. You will need to [disable Secure Boot](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Disabling_Secure_Boot) to boot the installation medium. If desired, [Secure Boot can be set up after completing the installation](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Implementing_Secure_Boot), although I personally wouldn't bother with Secure Boot since it's clearly an attempt from Microsoft to steer people away from Windows alternatives. Read these Reddit comments from the same post for more details:

- [Microsoft intended for Secure Boot to scare people away from using alternative OSes.](https://www.reddit.com/r/archlinux/comments/pyi8f1/comment/hexels5)
- [Microsoft needs to sign other OSes to allow them to work with Secure Boot.](https://www.reddit.com/r/archlinux/comments/pyi8f1/comment/hevxu45?context=3)

### <a id="old-intel-mac-delay" href="#old-intel-mac-delay">Delay on Old Intel-Based Macs</a>

If you are installing this on a very old Intel-based Mac, after a boot option is chosen in GRUB, there can be a roughly 2 minute black screen before any text shows up. You may have to be patient in this case before assuming the boot process got stuck. As long as it's progressing, the thumbdrive's activity light will be going off.

This was the case for my A1225 Mac.

## <a id="keyboard-layout" href="#keyboard-layout">Console Keyboard Layout</a>

The default console keymap is US. Available layouts can be listed with:
```
# ls /usr/share/kbd/keymaps/**/*.map.gz | less
```

Use `loadkeys` to change the keyboard layout if desired. For example, to set a German keyboard layout:
```
loadkeys de-latin1
```

Console fonts are located in `/usr/share/kbd/consolefonts/` and can likewise be set with [setfont](https://wiki.archlinux.org/title/Linux_console#Fonts), but that won't be covered by this guide.

## <a id="boot-mode" href="#boot-mode">Check the Boot Mode</a>

Run:
```
$ ls /sys/firmware/efi/efivars
```

If the directory exists, you're booted in EFI mode. Otherwise you're booted in legacy BIOS/CSM mode. You will need this information when partitioning the drive and setting up the bootloader.

If you are practicing installing Arch Linux on VirtualBox and want to test with EFI, check "Enable EFI (special OSes only)" under the "System" tab in the virtual machine's settings.

Do not assume that just because a system is really old that it doesn't use EFI. For example, old Intel-based Macs like the A1225 Mac from 2007 use it.

## <a id="prep-disk" href="#prep-disk">Prepare the Disk</a>

### <a id="plan-disk" href="#plan-disk">Planning</a>

Make a choice between a swap file and a swap partition [here](swap/swap_types.md). Your swap size should be equal to the amount of physical memory (unless there's a huge amount of it) or 2 GB if there's less than 2 GB of it.

If you are booted in legacy BIOS/CSM mode, you should make an MBR/DOS partition table similar to this:

|Partition|Recommended Size|File System|Mount Point (during installation)|
|-|-|-|-|
|Swap|Depends on RAM|Linux Swap|\[SWAP]|
|Root/OS|Remainder of Disk|ext4|/mnt|

If you are booted in EFI mode, you should make a GPT partition table similar to this. Note the existence of a >=300 MIB FAT32 partition at the start:

|Partition|Recommended Size|File System|Mount Point (during installation)|
|-|-|-|-|
|EFI System Partition|>=300 MiB|FAT32|/mnt/boot|
|Swap|Depends on RAM|Linux Swap|\[SWAP]|
|Root/OS|Remainder of Disk|ext4|/mnt|

Of course, the above examples won't exactly apply to you if you're putting the swap partition at the end of the disk or using a swap file instead. If you want to use a swap file, wait until after you have mounted the root partition.

### <a id="disk-example" href="#disk-example">Example</a>

Let's use an example of a laptop that was upgraded from a HDD to an SSD:
* It uses EFI, let's use GPT with a 512 MiB EFI system partition.
* It has a decent Intel CPU and 8 GB of RAM.
  * It's an Intel CPU, typical of older HDD-based laptops. This means the `intel-ucode` package is required.
  * We will use a swap file:
    * A decent CPU should have little trouble handling filesystem overhead with a swap file.
    * While 8 GB is a low amount of RAM these days, it's not low enough to worry about filesystem overhead with a swap file.
    * 8 GB is at least 2 GB, but not a very high amount of memory, so it will be the size of the swap file too.
* It is an older laptop designed to use a HDD, so the replacement SSD uses SATA.<br>
  Be aware that NVMe drives, which this laptop doesn't natively support, don't show up under `/dev/sd*` names.
* A GPT partition table has been chosen.

Planned Partition Table:

|Partition|Recommended Size|File System|Mount Point (during installation)|
|-|-|-|-|
|EFI System Partition|512 MiB|FAT32|/mnt/boot|
|Root/OS|Remainder of Disk|ext4|/mnt|

Get a list of disks using `lsblk` or `fdisk -l`. As our target is a SATA drive, it should show up as `/dev/sd*`. Let's assume it's `/dev/sda`.

Once you've chosen a disk (not a partition), run:
```
# fdisk /dev/sda
```

Enter these `fdisk` commands:
```
Command (m for help): g
Create a new GPT disklabel

Command (m for help): n
Partition number (1-128, default 1): 
First sector: 
Last sector, +/-sectors or +/-size{K,M,G,T,P}: +512M

Created a new partition 1 of type 'Linux filesystem' and of size 512 MiB.

Command (m for help): t
Selected partition 1
Partition type of alias (type L to list all): 1
Changed type of partition 'Linux filesystem' to 'EFI System'.

Command (m for help): n
Partition number (2-128, default 2): 
First sector: 
Last sector, +/-sectors or +/-size{K,M,G,T,P}: 

Created a new partition 2 of type 'Linux filesystem'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

Explanation:
* Some of these fields are entered as blank as some of the default values are suitable.
* `g` creates a GPT table on the disk.
* `n` is for creating new partitions.
* The default first sector is the one for making a partition that comes right after the previous partition.
* The default last partition is the one for making the partition extend to the end of the disk or right before the partition after the partition to create.
* Setting the partition's last sector to `+512M` tells `fdisk` to set the partition size to 512 MiB<br>
  Specifically, it set the last sector to be 512 MiB after the first one, which isn't at the very start of the disk (the partition table's header needs some room at the start of the disk).
* `t` is for changing the selected partition's type. Type `1` is for an EFI system partition.<br>
  This step is important, as a partition type of Linux Filesystem can't be used for EFI system partitions.
* `w` applies the specified changes to the disk, which were only pending before.

The resulting partitions should be:
* `/dev/sda1` - EFI System Partition
* `/dev/sda2` - Root Partition

Format the new partitions:
```
# mkfs.fat -F 32 /dev/sda1
# mkfs.ext4 -m 1 /dev/sda2 # Set reserved space to 1% instead of the default 5%.
```

The swap file must be created after mounting the root partition, which will be covered later.

Click [here](examples/index.md) for full installation examples. A full version of the above example can be found [here](examples/ssd_upgraded_laptop.md).

## <a id="mount-fs" href="#mount-fs">Mount the Filesystems</a>

Mount the root partition. If using a BIOS-based system:
```
# mount /dev/sda1 /mnt # No EFI system partition preceding it.
```

Otherwise, if using an EFI-based system, run these commands. You can manually create the `/mnt/boot` directory or use the `-m`/`--mkdir` option:
```
# mount /dev/sda2 /mnt
# mount -m /dev/sda1 /mnt/boot
```

If you chose to create a swap file, like in our example, now is the time to create and mount it. Click [here](swap/swap_file.md) for instructions on how to do so.

Mount the swap, you will need it mounted for when you generate `fstab`. If using a swap partition:
```
# swapon /dev/sda3 # If it's a swap partition at /dev/sda3.
```

## <a id="internet" href="#internet">Connect to the Internet</a>

Check if you're connected to the internet using `ping`:
```
$ ping archlinux.org
```

It will ping indefinitely if it succeeds, so be sure to press Ctrl+C when you're done.

If it fails, run this and note the name of the network interface you're trying to use:
```
$ ip link
```

### <a id="ethernet" href="#ethernet">Ethernet</a>

If you're trying to connect via Ethernet, try bringing up the interface with this, substituting `eth0` with the name of the interface:
```
# ip link set eth0 up
```

### <a id="wifi" href="#wifi">Wi-Fi</a>

Sometimes the required driver or firmware doesn't come with the Arch Linux image, which could make Wi-Fi unusable in the live environment. However, if you're trying to connect via Wi-Fi and a WLAN network interface is listed, try the following:

1. Start `wpa_supplicant` with this, substituting `wlan0` with the name of the interface:
```
# wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf
```

2. Run this, it will open an interactive prompt:
```
# wpa_cli
```

3. Use the `scan` and `scan_results` commands to see the available networks:
```
> scan
OK
<3>CTRL-EVENT-SCAN-RESULTS
> scan_results
bssid / frequency / signal level / flags / ssid
00:00:00:00:00:00 2462 -49 [WPA2-PSK-CCMP][ESS] MYSSID
11:11:11:11:11:11 2437 -64 [WPA2-PSK-CCMP][ESS] ANOTHERSSID
```

4. Connect to the network and quit `wpa_cli`:
```
> add_network
0
> set_network 0 ssid "MYSSID"
> set_network 0 psk "passphrase"
> enable_network 0
<2>CTRL-EVENT-CONNECTED - Connection to 00:00:00:00:00:00 completed (reauth) [id=0 id_str=]
> quit
```

### <a id="internet-failure" href="#internet-failure">Failure</a>

If `ping archlinux.org` still fails after that, or the interface you're trying to use isn't available, then you'll have to attempt an offline installation of Arch Linux.

## <a id="offline-install-prep" href="#offline-install-prep">Prepare for Offline Installation</a>

If you failed to get internet access on the target machine, this section is for you. Otherwise, skip to [the next one](#setup-linux).

This will require access to an Arch Linux system with internet access. A virtual machine running the live environment will suffice. You will need another removable drive to transfer the packages from the internet connected system to the target system.

### <a id="create-local-repo" href="#create-local-repo">Create a Local Repository</a>

A local repository will be created from the other system to install packages from instead. The removable drive that will contain the repository must be formatted with a Unix-like filesystem like ext4, as the repository will contain filenames with colons (`:`), which Windows/DOS-like filesystems such as FAT32 and NTFS don't support.

On the internet connected Arch Linux system (such as virtual machine booted from the installation media), create a directory for the repository and `cd` into it. We'll call it `packages`. You may do this directly on the removable drive, although I recommend you only do this if it's decently fast.

If you're on a proper installation of Arch Linux, you may have to make a temporary blank database to make sure `pacman` also downloads dependencies for the packages you're going to include:
```
$ mkdir /tmp/blankdb
```

Now is the time to choose all the packages you want to install with Arch Linux. Click [here](packages.md) for a guide on what packages you should choose.

You must include all the packages you want to install with Arch Linux. Modify the list of packages in the command below and run it to download them:
```
# pacman -Syw --cachedir $(pwd) --dbpath /tmp/blankdb base linux linux-firmware intel-ucode man-db man-pages vim nano less ntfs-3g dosfstools sudo networkmanager dhcpcd git base-devel htop
```

At some point, `pacman` stopped supporting absolute paths for `--cachedir`, which is why `$(pwd)` is used instead of `.` to get the absolute path of the current directory. Since it gives a cryptic error that sounds like this, this is likely a bug:
```
error: could not open file <path to package file download>.part: No such file or directory
```

To finalise the repository, run this. Do not call the repository `local`, as that name is already taken by a repository that's not in `pacman.conf`. We'll call the repository `custom`:
```
$ repo-add ./custom.db.tar.gz ./*[^sig]
```

Copy the `packages` directory to the removable drive if it wasn't directly created on it.

### <a id="use-local-repo" href="#use-local-repo">Use the Local Repository</a>

Transfer the removable drive to the target machine and mount it. We'll assume the repository's filesystem is `/dev/sdb1` and mount it under a new directory called `/repo`:
```
# mount -m /dev/sdb1 /repo
```

Open `/etc/pacman.conf` in an editor and comment out the `core` and `extra` repositories. This is to stop `pacman` from attempting to connect to the internet and failing. Then, add your `custom` repository:
```
...

#[core]
#Include = /etc/pacman.d/mirrorlist

...

#[extra]
#Include = /etc/pacman.d/mirrorlist

[custom]
SigLevel = Optional
Server = file:///repo/packages

...
```

Save the changes and run these commands to reinitialise and reload the keys for the local repository:
```
# pacman-key --init
# pacman-key --populate
```

The `/repo` directory and `pacman.conf` changes are part of the live environment and not the installation, so you won't have to worry about removing them. However, you will have to unmount `/repo` after using it so it doesn't get included in `fstab` upon generation.

## <a id="setup-linux" href="#setup-linux">Install and Configure the OS</a>

### <a id="install-packages" href="#install-packages">Install Packages</a>

If you haven't already chosen the packages you want to install while [preparing for an offline install](#offline-install-prep), you must do so now. [Here](packages.md) is a guide on what packages you should choose.

Run this to install your chosen packages on the root partition, replacing the packages with the ones you have chosen (an Intel CPU is assumed, hence `intel-ucode` is used):
```
# pacstrap -K /mnt base linux linux-firmware intel-ucode man-db man-pages vim nano less ntfs-3g dosfstools sudo networkmanager dhcpcd git base-devel htop
```

### <a id="pre-chroot" href="#pre-chroot">Pre-Chroot Tasks</a>

You will eventually have to chroot into the target system. Chrooting will cause you to lose access to the tools that come with the live environment and make you reliant on the packages that were installed. This can be a problem if, for some reason, you chose not to install a text editor. Luckily, there are no steps that require both chroot and a text editor, so this section will cover everything that can be done prior to chrooting in. 

If you [used a local repository](#offline-install-prep) to install the packages, make sure to unmount so it doesn't get included in `fstab`:
```
# umount /repo
```

Generate `fstab`. The `-U` option specifies the use of filesystem UUIDs. If you want `fstab` to use partition labels (not recommended), use `-L` instead. Just make sure your partitions are labelled.

If using a BIOS-based system:
```
# genfstab -U /mnt >> /mnt/etc/fstab
```

If using an EFI-based system where `/dev/sda1` is the EFI system partition. The EFI system partition it temporarily unmounted to avoid its inclusion in `fstab`:
```
# umount /mnt/boot
# genfstab -U /mnt >> /mnt/etc/fstab
# mount /dev/sda1 /mnt/boot
```

Edit `/mnt/etc/locale.gen` and uncomment needed [locales](https://wiki.archlinux.org/title/Locale), such as `en_US.UTF-8 UTF-8` for a US keyboard layout:
```
...
#en_SG.UTF-8 UTF-8
#en_SG ISO-8859-1
en_US.UTF-8 UTF-8
#en_US ISO-8859-1
#en_ZA.UTF-8 UTF-8
...
```

Create `/mnt/etc/locale.conf` with your preferred system locale using:
```
# echo LANG=en_US.UTF-8 > /mnt/etc/locale.conf
```

If you [set the console keyboard layout](#keyboard-layout) to something other than US, make the changes persistent by creating `/mnt/etc/vconsole.conf`. This example sets it to a German keyboard layout:
```
# echo KEYMAP=de-latin1 > /mnt/etc/vconsole.conf
```

Set the hostname with this, replacing `arch-pc` with your choice of hostname. It can't contain underscores:
```
# echo arch-pc > /mnt/etc/hostname
```

### <a id="chroot" href="#chroot">Chroot Tasks</a>

Chrooting spawns a shell that uses a target directory as the root directory. This is important for running commands that configure the installed OS that assumes the apparent root directory belongs to them.

Change root into the target system:
```
# arch-chroot /mnt
```

Set the time zone with this. For example, this is how you set it to Melbourne, Australia:
```
# ln -sf /usr/share/zoneinfo/Australia/Melbourne /etc/localtime
```

Explanation:
* `ln` is a tool for creating links to files and directories.
  * `-s` specifies the creation of a symbolic link, which works based on a path to its target, as opposed to a more low-level link to another file on the same filesystem.<br>
    While not necessary in this case, it's useful for linking across different filesystems/volumes.
  * `-f` means force, overwriting existing links.
* This command causes `/etc/localtime` to appear as `/usr/share/zoneinfo/Australia/Melbourne`.

Using the system time from the live environment, update the timestamps in `/etc/adjtime` and set the hardware clock with:
```
# hwclock -w
```

Generate the locales with:
```
# locale-gen
```

Set the `root` password with:
```
# passwd
```

If you want to skip having a root password, run this instead:
```
# passwd -d root
```

If using `networkmanager`, enable networking with:
```
# systemctl enable NetworkManager.service
```

## <a id="boot-loader" href="#boot-loader">Boot Loader</a>

Pick a boot loader to use. I have guides for some of them [here](boot_loaders/index.md).

## <a id="reboot" href="#reboot">Reboot</a>

Exit the chroot environment (if you haven't already) with `exit` or Ctrl+d.

Run these commands to check that there are no busy partitions. Thanks to `-R`, this will unmount any mountpoints under `/mnt` as well, such as `/mnt/boot`:
```
# swapoff -a # If using a swap file.
# umount -R /mnt
```

Remove the installation medium and any other external bootable media, then reboot with this. The live environment exists entirely in RAM instead of continuing to rely on the medium it was booted from, so there's no safe eject for it per se.
```
reboot
```

Not removing the bootable media will cause it's boot menu to appear instead of the installed boot menu. You will have to enter the firmware interface and access its boot manager to select booting from the internal drive.

## <a id="post-install" href="#post-install">Post-Installation</a>

Once you've gotten past the boot loader's boot menu and booted, login as `root` using the password you set. See [Creating Users](../configure/users.md) for creating user accounts for regular use.