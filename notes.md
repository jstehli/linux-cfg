# Linux Setup

## Internet Browsing

firefox with extensions: ublock origin (ad block), PassFF (password manager), Vimium (vim bindings)

## Text Editor

vim

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

Use `arandr` to configure & activate a setting for each setup, then save it to `autorandr` - you can always call autorandr after hotswapping screens afterwards to check if the right config is detected. All should work automatically now if you save a profile for each setup.

`autorandr` will also be called in the i3 config s.t. it starts with the correct setup

## Window Manager

I use i3 window manager plus a set of tools to make it work:
`numlockx` to turn num lock on after startup.


## VPN

I have VPN access to the network of my university. They are using Cisco VPN, and I use the openconnect implementation, the respective command that works for me is
    
    sudo openconnect --protocol=anyconnect --user=jstehli@student-net.ethz.ch --usergroup=student-net s    slvpn.ethz.ch

## Android Smartphone

### Password Manager

Password Store (available on Google Play Store) -> connect to github .password-store repo
