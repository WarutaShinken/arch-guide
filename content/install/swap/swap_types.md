# Swap Types

There are 2 types of swap spaces: Swap File and Swap Partition

## <a id="swap-file" href="#swap-file">Swap File</a>

A swap file is better for ease of replacement and resizing, especially if you want to do it while the system is booted. It will also make your partition table less cluttered.

## <a id="swap-partition" href="#swap-partition">Swap Partition</a>

A swap partition may provide less system overhead than a swap file, as there is no filesystem overhead to work around for a stationary, unfragmented partition (if you mention how SSDs work under the hood I'll slap you). Here are some examples where a swap partition would make sense:

* The target machine doesn't have a lot of performance to spare.
* It is expected for the total memory usage to exceed the physical memory capacity, causing swap to be frequently accessed.

If using a swap partition, you should choose its location based on the type of drive you're installing to.
* On a HDD: At the start of drive, right after the EFI boot partition if present. The performance of a HDD is dependent on the location being accessed.
  * The start of the HDD performs better than the end, which will matter for swap.
  * The swap partition will usually be small enough to not offset the OS partition from the start by a meaningful amount.
* On an SSD: At the end of the drive. SSD performance is independent of the location being accessed, and making it the last partition makes it easier to resize.
  * Expanding a swap partition at the start of the drive requires shifting the entire OS partition to the right. This can take a while even on the fastest SSDs.
  * Expanding a swap partition at the end of the drive only requires shrinking the OS partition and moving a much smaller partition to the left. In fact, you could just delete the swap partition, recreate it and either reuse the previous swap UUID with `mkswap -U` or just update your `fstab` with the new UUID.