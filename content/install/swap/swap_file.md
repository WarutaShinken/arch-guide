# How to Create a Swap File

## <a id="blank-file" href="#blank-file">Create Blank File</a>

Let's create a swap file simply called `swap.img` at the root of the filesystem, which you probably mounted under `/mnt`. First, we must create a blank file of the size we want. Let's use a 4 GiB swap file as an example:
```
# fallocate -l 4G /mnt/swap.img
```

Alternatively, you can create it using `dd`, although it's slower and less efficient due to unnecessary blank writes:
```
# dd if=/dev/zero of=/mnt/swap.img bs=4K iflag=count_bytes count=4G status=progress
```

`dd` is a command that copies data from one file to another:
* `if=/dev/zero` is the input file. `/dev/zero` is a device that will output zeros (0x00) for every byte read from it.
* `of=/dev/swap.img` is the output file, which is our target swap file.
* `bs=4K` tells `dd` to copy in 4 KiB blocks. By default it copies in 512-byte blocks.
* `iflag=count_bytes count=4G` is the amount of data to copy from the source (`/dev/zero`).
  * Normally, `count` is the number of blocks to copy. With `bs=4K`, this would be 16 EiB of data, which is why `iflag=count_bytes` is specified.
  * Without any of these arguments, it would keep copying until there is no disk space left for the destination file.
* `status=progress` shows a progress indicator so you'll know how quickly it's copying and an estimate of how long it has left. This is optional.

## <a id="format" href="#format">Format</a>

We then format it into a swap volume with:
```
# mkswap /mnt/swap.img
```

The default file permissions are not recommended for swap, run this to set them correctly:
```
# chmod 0600 /mnt/swap.img
```

## <a id="mount" href="#mount">Mount</a>

We can now mount it with:
```
# swapon /mnt/swap.img
```

You must do this so it will be added to `fstab` when you get to running `genfstab`.