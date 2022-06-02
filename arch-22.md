# Arch 2022

After switching to Mac for two years almost (my current company gave me a MBP. I can now confirm that I hate it.), I am now back to Linux.
I just bought a system76 Lemur Pro, and will cover my setup here.

I will install BTRFS and encrypt data at rest, incl. the swap partition. Note that I have a UEFI system and will use a GPT table.

To encrypt the swap partition, we use LVM on top of LUKS. This is the easiest way to achieve this. We then use BTRFS because the snapshot feature is neat.

## Prep

Follow the pre-installation in the [installation guide](https://wiki.archlinux.org/title/Installation_guide) up until "partition the disks", because we deviate there.

## Disk Setup

This is inspired by the following two videos, please support the guy who makes those, he helped me a lot: [1](https://www.youtube.com/watch?v=sm_fuBeaOqE) [2](https://www.youtube.com/watch?v=sm_fuBeaOqE)

### Partition the disk

Start by using `lsblk` to figure out the label of your disk. In my case, I have to do `fdisk /dev/nvme0n1`. Then, I create a new GPT table by entering `g`. Now, we are ready to create the first partition. We need to create a EFI partition, I use 512 MB for this, to be on the safe side.

    n
    1
    [enter]
    +512M
    t
    1

The rest of the disk will be one large partition that will later be encrypted.

    n
    2
    [enter]
    [enter]

Write the table with `w`.

### Format and Encrypt the Disk

Format the EFI partition:

    mkfs.fat -F32 /dev/nvme0n1p1

We want to encrypt the system partition and unlock it. We use LUKS1, which actually should be the default option, but in the Arch wiki they tell us to specify it. LUKS2 seems to be buggy when used with GRUB. We call the mapped device `cryptlvm`.

    cryptsetup luksFormat --type luks1 /dev/nvme0n1p2
    [follow menu]
    cryptsetup luksOpen /dev/nvme0n1p2 cryptlvm

### Set Up LVM

Create the LVM physical volume and volume group.

    pvcreate /dev/mapper/cryptlvm
    vgcreate LvmVolGroup /dev/mapper/cryptlvm

Now create two logical volumes - system and swap. I have 24 GB RAM and use 32GB swap, to be on the safe side.

    lvcreate -L 32G LvmVolGroup -n swap
    lvcreate -l 100%FREE LvmVolGroup -n system

### Create the File Systems

Now, create the file systems.

    mkfs.btrfs /dev/LvmVolGroup/system
    mkswap /dev/LvmVolGroup/swap

### Mount the File Systems and Set Up BTRFS

I generate the BTRFS subvolumes that we need. I keep it minimal here and only prepare subvolumes for `/`, `/home`, and `/.snapshots'.
You can set that up however you want if you know what you're doing. I do not really.

    mount /dev/LvmVolGroup/system /mnt
    cd /mnt
    btrfs subvolume create @
    btrfs subvolume create @home
    btrfs subvolume create @snapshots
    cd
    umount /mnt

Mount all the freshly created subvolumes using some fancy options.

    mount -o noatime,compress=zstd,space_cache=v2,discard=async,subvol=@ /dev/LvmVolGroup/system /mnt
    mkdir /mnt/home
    mount -o noatime,compress=zstd,space_cache=v2,discard=async,subvol=@home /dev/LvmVolGroup/system /mnt/home
    mkdir /mnt/.snapshots
    mount -o noatime,compress=zstd,space_cache=v2,discard=async,subvol=@snapshots /dev/LvmVolGroup/system /mnt/.snapshots

We'll also need to mount the boot partition.

    mount --mkdir /dev/nvme0n1p1 /mnt/boot
    
And the swap partition.
    
    swapon /dev/LvmVolGroup/swap


## Installation

### Make it fast.

To speed up downloads during installation, you can use `reflector`.
This will update the mirrorlist with recently updated mirrors in nearby countries, prioritized by speed (could take a couple of minutes, ignore the warnings).

    reflector -c ch,de,at,it,fr --age 8 --sort rate --save /etc/pacman.d/mirrorlist

### Install Packages

We also install `git`, an editor (`vim`/`vi` in my case), `btrfs-progs` and `snapper` here. This will also take some time.

    pacstrap /mnt base linux linux-firmware git vi vim btrfs-progs snapper
    
## System Configuration

I now follow the "configure the system" section in the official install guide.
I skip the "Initramfs" and "Bootloader" part, we're going to do this later.

### More Packages

We should install a few things now.

    pacman -S grub grub-btrfs efibootmgr intel-ucode lvm2 networkmanager network-manager-applet wpa_supplicant rsync reflector sudo
    
### Bootloader Config

    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
    grub-mkconfig -o /boot/grub/grub.cfg

### Enable NetworkManager, Create a User, Set Up `sudo`

    systemctl enable NetworkManager
    useradd -mG wheel [username]
    passwd [username]
    [enter password]
    visudo
    
Edit the file: Uncomment the line that gives the wheel group full power. Save.

### `mkinitcpio`

Edit `/etc/mkinitcpio.conf`. Add `btrfs` under `MODULES`. 
Under `HOOKS`, add `keyboard` (delete the potential later occurence) and `keymap` after `autodetect`, and `encrypt` and `lvm2` before `filesystems`.
Save end exit, then run `mkinitcpio -p linux`.

### GRUB Config for Encryption

Run `blkid` and copy the `UUID` (what is inside the quotes) of your encrypted device (`system`, **not** the `PARTUUID` and **not** the one of a mapper device).
Then, edit `/etc/default/grub` and change the line that is `GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"` to:

    GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet cryptdevice=UUID=[the UUID that you copied before]:cryptlvm root=/dev/LvmVolGroup/system"

Regenerate the GRUB config file with `grub-mkconfig -o /boot/grub/grub.cfg`

8d0d3cf1-9db8-4c1c-a091-6726783e7e73


### Exit and Reboot

`exit`, then `reboot`. You should be able to choose Arch Linux in GRUB and then enter the password for the encrypted device. Log into your account.
If you are on wifi, set up a connection with `nmtui`.

## AUR 

Install `yay`.

    sudo pacman -S --needed base-devel
    git clone https://aur.archlinux.org/yay.git
    cd yay
    makepkg -si

## Set Up the System

### System 76

Follow [this link](https://support.system76.com/articles/system76-driver). If you installed `yay`, you simply can do

    yay system76-driver
    sudo systemctl enable --now system76
    
Then, there is more to install [here](https://support.system76.com/articles/system76-software)

### Display Server / Window Manager

https://wiki.archlinux.org/title/Xorg

    sudo pacman -S xorg-server i3-wm xorg-xinit dmenu i3status

### Shell & Terminal

    pacman -S zsh rxvt-unicode
    
Change the default shell: arch wiki...

### Dotfile Management

Use the bare git repo approach.
Setup a github repo called dotfiles. Then:

    cd
    git clone --bare https://github.com/jstehli/dotfiles.git .dotfiles
    echo 'alias dotfiles="/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME"' >> $HOME/.zshrc
    source ~/.zshrc
    dotfiles config --local status.showUntrackedFiles no

### Internet Browsing

    sudo pacman -S firefox

Recommended extensions are uBlock origin & KeePassXC-browser.

### Audio

    sudo pacman -S pulseaudio pasystray pavucontrol
    pulseaudio -D

### Password Management

I use `keepassxc`. (https://wiki.archlinux.org/title/KeePass) is documented quite well. Firefox integration is available.
I sync the database file via cloud storage currently (to my other devices), works fine.

### Cloud Storage

I am currently using Dropbox and want to switch to something else eventually.
I installed it from AUR and followed the doc in the Arch wiki.
Update: For now, I will stick to it. There is documentation in the Arch wiki on how to set up `encfs` with it, s.t. you have encrypted Files.

### Snapper

.. follow the video, I did not save when I copied the steps

### Hibernation

edit `/etc/default/grub` and add, under `GRUB_CMDLINE_LINUX_DEFAULT`, `resume=/dev/LvmVolGroup/swap`. Rebuild the grub config with `grub-mkconfig -o /boot/grub/grub.cfg`.

Also edit `/etc/mkinitcpio.conf`, add `resume` between `filesystems` and `fsck`, then run `mkinitcpio -P`.

# TODO

make dotfiles push work to github
make i3 work
make snapper/snapshots work
add snapper config back here..
make changing brightness work
