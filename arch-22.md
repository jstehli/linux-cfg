# Arch 2022

After switching to Mac for two years almost (my current company gave me a MBP. I can now confirm that I hate it.), I am now back to Linux.
I just bought a system76 Lemur Pro, and will cover my setup here.

## Prep

Follow the pre-installation in the [installation guide](https://wiki.archlinux.org/title/Installation_guide) up until "partition the disks", because we deviate there.

## Disk Setup

This is inspired by the following two videos, please support the guy who makes those, he helped me a lot: [1](https://www.youtube.com/watch?v=sm_fuBeaOqE) [2](https://www.youtube.com/watch?v=sm_fuBeaOqE)

We will install BTRFS and encrypt data at rest, incl. the swap partition. Note that i have a UEFI system and will use a GPT table.

### Partition the disk

Start by using `lsblk` to figure out the label of your disk. In my case, I have to do `fdisk /dev/nvme0n1`. Then, I create a new GPT table by entering `g`. Now, we are ready to create the first partition. We need to create a EFI partition, I use 512 MB for this, to be on the safe side.

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
    n
    3
    [enter]
    [enter]
    t
    3
    19

Write the table with `w`.
  
### Format and Encrypt the Disk

Format the EFI partition:

    mkfs.fat -F32 /dev/nvme0n1p1
    
Set up the swap partition:

    mkswap /dev/nvme0n1p3
    swapon /dev/nvme0n1p3

Before generating the BTRFS partition, we want to encrypt it and then unlock it. We use LUKS1, which actually should be the default option, but in the Arch wiki they tell us to specify it. LUKS2 seems to be buggy when used with GRUB. We call the mapped device `cryptroot`.

    cryptsetup luksFormat --type luks1 /dev/nvme0n1p2
    [follow menu]
    cryptsetup luksOpen /dev/nvme0n1p2 cryptroot
    
Now, create the file system.

    mkfs.btrfs /dev/mapper/cryptroot
    
### Mount the File Systems

Mount the previously generated `cryptroot` and generate the BTRFS subvolumes that we need. We keep it minimal here and only prepare subvolumes for `/`, `/home`, and `/.snapshots'.
You can set that up however you want if you know what you're doing. I do not really.

    mount /dev/mapper/cryptroot /mnt
    cd /mnt
    btrfs subvolume create @
    btrfs subvolume create @home
    btrfs subvolume create @snapshots
    cd
    umount /mnt
    
Mount all the freshly created subvolumes using some fancy options.

    mount -o noatime,compress=zstd,space_cache=v2,discard=async,subvol=@ /dev/mapper/cryptroot /mnt
    mkdir /mnt/home
    mount -o noatime,compress=zstd,space_cache=v2,discard=async,subvol=@home /dev/mapper/cryptroot /mnt/home
    mkdir /mnt/.snapshots
    mount -o noatime,compress=zstd,space_cache=v2,discard=async,subvol=@snapshots /dev/mapper/cryptroot /mnt/.snapshots
    
We'll also need to mount the boot partition.

    mount --mkdir /dev/nvme0n1p1 /mnt/boot

## Installation

### Make it fast.

To speed up downloads during installation, you can use `reflector`.
This will update the mirrorlist with recently updated mirrors in nearby countries, prioritized by speed (could take a couple of minutes, ignore the warnings).

    reflector -c ch,de,at,it,fr --age 12 --sort rate --save /etc/pacman.d/mirrorlist

### Install Packages

We also install `git`, an editor (`vim` in my case), `btrfs-progs` and `snapper` here.
