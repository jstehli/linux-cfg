# Linux Setup

## Laptop

After spilling a coffee over my old Yoga Convertible, I bought a cheap Thinkpad 440 and updated it's RAM to 12GB. Arch runs smoothly.

## Distro

I use Arch Linux.

## Arch installation

I followed the installation guide. Some remarks:

- I use BTRFS with snapper (have to check it out further to fully configure it to my needs, but looks awesome!). Followed this guide: https://www.youtube.com/watch?v=sm_fuBeaOqE

### Programs worth installing during installation

    vim
    vi
    sudo
    networkmanager
    git
    rsync
    htop
    openssh
    man-db
    man-pages
    i3lock
    i3-wm
    i3blocks
    i3status
    cronie
    
    
### User setup

Add yourself as a user and add it to group wheel for sudo access. Also edit sudo cfg (`visudo`) to make sure that the wheel group gets sudo rights.

## Folder Structure

I installed `xdg-user-dirs` to set the config dir - mutt will use this


## Display Server

Xorg

## Keyboard

I used the following sweet little command to allow me to switch to a Swiss-German keyboard setting via alt-shift:

`localectl --no-convert set-x11-keymap us,ch "" "" grp:alt_shift_toggle`

## Power Management

I have a lock script in my .dotfiles repo that blurs the screen before locking it with `i3lock`.

To make hibernation work, I followed the arch wiki.

I installed tlp for power management (turned USB autosuspend off in the config, because my keyboard didn't wake up after hibernation). [not using TLP at the moment]

`xss-lock` allows locking the screen before suspending/hibernating.

## Network Setup

Install `networkmanager`, then `systemctl enable NetworkManager.service --now`

Install `networkmanager-openconnect` for Cisco VPN Connections if desired

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
`playerctl` lets you use multimedia keys.

## Backup

I use an `rsync` script to backup to an external HD, automated by cron (I installed `cronie`)

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

You'll also need to install passFF host, click on the extension symbol in firefox for instructions (from github page..)

## Keyring

    gnome-keyring
    seahorse
    libsecret
    
Setup following arch wiki for autologin using user PW
    

## Sync

syncthing, todo (?): config to github repo

## cloud

    nextcloud-client

## Screen Setup

All based on `xrandr`. 

Use `arandr` to configure & activate a setting for each setup, then save it to `autorandr` - you can always call autorandr after hotswapping screens afterwards to check if the right config is detected. All should work automatically now if you save a profile for each setup.

`autorandr` will also be called in the i3 config s.t. it starts with the correct setup

## Window Manager

I use i3 window manager plus a set of tools to make it work:
`numlockx` to turn num lock on after startup.
## laptop stuff
`tlp` for power management

## VPN
:
I have VPN access to the network of my university. They are using Cisco VPN, and I use the openconnect implementation, the respective command that works for me is
    
    sudo openconnect --protocol=anyconnect --user=XXX@student-net.ethz.ch --usergroup=student-net sslvpn.ethz.ch

## Notifications

`dunst` 

## various

- `sysstat` for system monitoring, this is a dependency of some i3blocks scripts

- `cbatticon` for battery tray icon

- `udisks2` with `udiskie` for mounting external media

- `man-db` and `man-pages` for manuals

- `xclip` to pipe output of command into clipboard

- `scrot`, screenshots from command line

- `imagemagick`, command line image manipulation

- `xkblayout-state` (from AUR), to query current keyboard layout for status bar 

## xorg additions

`xorg-xbacklight` to change the laptop screen brightness

`xorg-xev` to identify what key has been pressed

## Android Smartphone

### Password Manager

Password Store (available on Google Play Store) -> connect to github .password-store repo
