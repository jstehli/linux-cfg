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

Use `arandr` to configure & activate a setting for each setup, then save it to `autorandr` - you can always call autorandr after hotswapping screens afterwards.

`autorandr` will also be called in the i3 config s.t. it starts with the correct setup


## Android Smartphone

### Password Manager

Password Store (available on Google Play Store) -> connect to github .password-store repo
