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

## Android Smartphone

### Password Manager

Password Store (available on Google Play Store) -> connect to github .password-store repo
