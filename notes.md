# Linux Setup

## Distro

I use Arch Linux.

## Arch installation

I followed the installation guide. Some remarks:

- Didn't have a physical network connection, did all via WiFi. In the installation, I set up the connection using `iwd`, now on the system I use NetworkManager. I followed the `iwd` guide in the arch wiki during installation to connect to wifi.

- I set up 3 partitions using a GPT: UEFI start partition (260MiB), Main ext4 (rest), Swap (8GiB). Something went wrong with the fstab and the swap partition, which made boot super slow. I then commented out the swap line from fstab since systemd recognizes GPT swap partitions automatically. Now, everything runs fine.

### Programs worth installing during installation

    vim
    vi
    sudo
    networkmanager
    git
    
### User setup

Add yourself as a user and add it to group wheel for sudo access. Also edit sudo cfg (`visudo`) to make sure that the wheel group gets sudo rights.


## Display Server

Xorg



## Network Setup

Install `networkmanager`, then `systemctl enable NetworkManager.service --now`

Connect to a wireless network from command line -> arch wiki NetworkManager

Also install `network-manager-applet` for a system tray icon and `openssh` for SSH access.

## Fonts

Arch comes with very minimal fonts. To make i3 start correctly, I installed `adobe-source-code-pro-fonts`, `adobe-source-sans-pro-fonts` and `adobe-source-serif-pro-fonts`.

I also followed the guide in arch wiki -> fontconfig to enable sub pixel rgb rendering, via symlink. The fonts now look good!

## Terminal Emulator

I use URXVT.

## Internet Browsing

firefox with extensions: ublock origin (ad block), PassFF (password manager), Vimium (vim bindings)

## Text Editor

vim

## Audio

While I started with installing `alsa-utils` and `alsa-plugins`, I ended up using `pulseaudio` with `pasystray` tray and `pavucontrol` control (can then be launched from tray!). 

## Password Manager

GNU pass

.password-store is git enabled, backed up atm to github (this needs to change sometime)

synced across devices using syncthing

pw's & metadata are stored in encrypted text files, using a directory structure like:

~/.password-store/dirxyz/pwfile.gpg

text files are structured like:

    pw
    login: login
    url: *url.com*

## Sync

syncthing, todo (?): config to github repo

## Screen Setup

All based on `xrandr`. 

Use `arandr` to configure & activate a setting for each setup, then save it to `autorandr` - you can always call autorandr after hotswapping screens afterwards to check if the right config is detected. All should work automatically now if you save a profile for each setup.

`autorandr` will also be called in the i3 config s.t. it starts with the correct setup

## Window Manager

I use i3 window manager plus a set of tools to make it work:
`numlockx` to turn num lock on after startup.


## VPN
:
I have VPN access to the network of my university. They are using Cisco VPN, and I use the openconnect implementation, the respective command that works for me is
    
    sudo openconnect --protocol=anyconnect --user=XXX@student-net.ethz.ch --usergroup=student-net sslvpn.ethz.ch

## Notifications

`dunst` 

## various

`sysstat` for system monitoring, this is a dependency of some i3blocks scripts
`cbatticon` for battery tray icon

## Android Smartphone

### Password Manager

Password Store (available on Google Play Store) -> connect to github .password-store repo
