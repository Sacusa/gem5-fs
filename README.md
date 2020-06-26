# Preparing gem5 for FS simulation

This document explains how to set up the required files for full system (FS) simulation on gem5. All the required files are either included in the repository or have direct download links.

Full system simulation requires two files: a Linux kernel binary and a disk image.

## Linux kernel binary

This repository already contains the kernel binary for version 5.4.46 which can be uses as-is. To compile the binary from scratch:

1. Download and extract the source from [here](https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/linux-5.4.46.tar.xz).
1. Install the requirements by running

```sudo apt install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison```

1. Copy `config-5.4.46` from this repository to the kernel source directory as `.config.`
1. Run make.

After compilation, the generated `vmlinux` binary is the kernel binary.

## Disk image

For the disk image, we will build on the linux-2.6.22.9 disk image referenced in gem5's old documentation. We will replace the image's contents with a modern Ubuntu Base distribution.

1. Download and extract the old binaries and disk images from [here](https://drive.google.com/uc?export=download&id=1fx9qAEAzBxn76saIbsm6cqEYISkNthYF).
1. Mount the disk image (disks/linux-x86.img) by running

```sudo mount -o loop,offset=32256 disks/linux-x86.img <mount directory>```

1. Clear the contents of the mounted image.
1. Download Ubuntu Base 20.04 files from [here](http://cdimage.ubuntu.com/ubuntu-base/releases/20.04/release/ubuntu-base-20.04-base-amd64.tar.gz).
1. Extract the distribution files into the image by running `tar -xvzf ubuntu-base-20.04-base-amd64.tar.gz -C <mount directory>`.
1. Copy the following files from `disk_files` in this repository to respective destinations in the image

| File  | Destination |
|-------|-------------|
| fstab | `etc/fstab` |
| init  | `bin/init`  |
| m5    | `sbin/m5`   |

1. Unmount the image.

At this point, the image is ready for use. The following subsections explain steps for installing additional packages using apt.

### Installing packages to the disk via apt

1. Mount the image to a directory
1. Run

```
sudo mount -o bind /sys <mount directory>/sys
sudo mount -o bind /dev <mount directory>/dev
sudo mount -o bind /proc <mount directory>/proc
sudo chroot <mount directory> /bin/bash
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
```

1. You can now use the `apt` to install packages just like you normally would. Two suggested packages are `software-properties-common` (commonly used libraries and interpreters) and `kmod` (modprobe).

1. After you're done, run the following and then unmount the image.

```
sudo umount mnt/sys
sudo umount mnt/proc
sudo umount mnt/dev
```

