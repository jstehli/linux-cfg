# Linux Setup

## Internet Browsing

firefox with extensions: ublock origin (ad block), PassFF (password manager), Vimium (vim bindings)

## Text Editor

vim

## Password Manager

GNU pass
.password-store is git enabled and pushed via cronjob (todo) to github repository
pw's & metadata are stored in encrypted text files, using a directory structure like:
~/.password-store/dirxyz/pwfile.gpg
text files are structured like:

    pw
    login: login
    url: *url.com*

## Android Smartphone

### Password Manager

Password Store (available on Google Play Store) -> connect to github .password-store repo
