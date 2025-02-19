# Boot Loaders

* [GRUB](grub.md) - Use this if this is your first time installing Arch Linux manually.
  * Pros:
    * Supports BIOS and EFI.
    * Compatible with most systems.
    * Offers good multiboot support.
  * Cons:
    * [Doesn't play nicely with Intel-based Macs.](grub.md#mac-support)
* [SYSLINUX](syslinux.md) - Used by the Arch Linux image.
* [systemd-boot](systemd_boot.md)
  * Pros:
    * Lightweight, simple and faster than GRUB.
    * Works great with Intel-based Macs.
  * Cons:
    * Only supports EFI.
    * Requires manual configuration.