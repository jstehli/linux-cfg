# Arch 2022

After switching to Mac for two years almost (my current company gave me a MBP. I can now confirm that I hate it.), I am now back to Linux.
I just bought a system76 Lemur Pro, and will cover my setup here.

## Prep

Follow the pre-installation in the [installation guide](https://wiki.archlinux.org/title/Installation_guide) up until "partition the disks", because we deviate there.

## Disk Setup

This is inspired by the following two videos, please support the guy who makes those, he helped me a lot: [1](https://www.youtube.com/watch?v=sm_fuBeaOqE) [2](https://www.youtube.com/watch?v=sm_fuBeaOqE)

We will install BTRFS and encrypt data at rest, incl. the swap partition. Note that i have a UEFI system and will use a GPT table.

### Partition the disk

Start by using `lsblk` to figure out the label of your disk. In my case, I have to do `fdisk /dev/nvme0n1`. Then, I create a new GPT table by entering `g`. Now, we are ready to create the first partition by entering `n`. We need to create a EFI partition, I use 512 MB for this, to be on the safe side.

    n
    1
    [enter]
    +512M
    t
    1
 
I have 24 GB of RAM, so I'll create a swap partition of 32 GB to be on the safe side as well. The rest will be used for the main partition. I like to have the swap partition as last partition. This can be achieved as follows.

    n
    2
    [enter]
    -32G
    t
    2
    19
    n
    3
    [enter]
    [enter]

Write the table with `w`.
  


## Installation

### Make it fast.

To speed up downloads during installation, you can use `reflector`. This will help you optimize the mirrorlist.
I live in Switzerland...

  reflector -c Switzerland -a 6
