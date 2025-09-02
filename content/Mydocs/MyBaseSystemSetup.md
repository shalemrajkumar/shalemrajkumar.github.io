---
title: My Arch Docs
weight : 0
tags: ["arch", "distro", "configuration"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
# description: ""
# canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
---


## My Arch Linux Base System Setup

These are packages and configurations I use to set up a base Arch Linux system.


### Why Arch Linux?

Arch Linux ships naked out of the box with only few most essential packages. This allows me to set up my system exactly the way I want it, with full control over tiny details. it has a great community and [support](https://wiki.archlinux.org/title/Main_page) with rolling releases. This basically runs on anything that can be called a computer.

### My Setup

#### system

- [`base`]() (base package group)
- [`linux`]() (linux kernel)
- [`linux-firmware`]() (firmware for devices)
- [`REFind`]() (boot manager like grub, systemd-boot, etc.)
- [`wpa_supplicant`]() (for internet)

#### Essential

- [`emacs`]() or [`nvim`]() or [`gvim`]() or [`vim`]() or [`vi`]() (text editor)
- [`git`]() (version control)
- [`btop`]() [`htop`]() or [`top`]() (system monitor)
- [`curl`]() or [`wget`]() (download files from internet)
- [`unzip`]() or [`tar`]() (extract files)
- [`man`]() (manual pages)
- [`tldr`]() (short discriptions of commands)
- [`sudo`]() (execute commands as root)
- [`system python`]() (never touch this)
- [`uv`]() or [`pyenv`]() (manage python versions)
- [`zsh`]() or [`fish`]() (shell)
- [`rclone`]() (manage cloud storage)
- [`openssh`]() (ssh client and server)
- [`tmux`]() (terminal multiplexer)
- [`alacritty`]() or [`kitty`]() (terminal emulator)
- [`yay`]() or [`paru`]() (AUR helper) (AUR packages can be installed manually be be aware any one can upload anything to AUR)
- [`reflector`]() (update mirrorlist) (also can be done manually)

#### other essential

- [`pipewire`]() (audio and video management)
- [`blueman`]() (bluetooth management)
- [`networkmanager`]() or [`dhcpcd`]() or  [`netctl`]() or [`iwd`]() (network management) (for simple management)
- [`firewalld`]() or [`ufw`]() (firewall management)



### Environment

- [`xorg-server`]() (Xorg display server)
- [`i3`]() (tiling window manager)
- [`alacritty`]() or [`kitty`]() (terminal emulator)
- [`nitrogen`]() (set wallpaper)
- [`redshift`]() (adjust screen color temperature)
- [`rofi`]() (application launcher)
- [`polybar`]() (status bar)
- [`picom`]() (compositor)
- [`betterlockscreen`]() (lock screen)
- [`xautolock`]() (auto lock screen)
- [`xclip`]() (clipboard management)

### cli tools and untilities

- [`gdu`]() or [`ncdu`]() (disk usage analyzer)
- [`tree`]() (display directory tree)
- [`atuin`]() (shell history manager)
- [`fzf`]() (fuzzy finder)
- [`zoxide`]() (smart cd command)
- [`bat`]() (cat with syntax highlighting)
- [`exa`]() (ls with more features)
- [`fd`]() (find with more features)
- [`ripgrep`]() (grep with more features)
- [`eza`]() (ls with more features)
- [`jq`]() (process json)
- [`duf`]() (disk usage)
- [`stow`]() (manage dotfiles)
- [`hyperfine`]() (any command benchmarking)
- [`csvlens`]() (view and process csv files)
- [`togo`]() (todo list manager)
- [`neofetch`]() or [`fastfetch`]() (system information)
- [`asciinema`]() (terminal session recorder)


### system tools and monitoring

- [`timeshift`]() or [`snapper`]() (system backup and restore)
- [`lact`]() (gpu monitoring)
- [`powertop`]() (power consumption monitoring)
- [`journalctl`]() (view system logs)
- [`dmesg`]() (view kernel logs)
- [`lsblk`]() (list block devices)
- [`fdisk`]() (partition management)
- [`gparted`]() or [`parted`]() (graphical partition management)
- [`zram-generator`]() (compress ram to use as swap)


### development

- [`docker`]() (containerization)
- [`nodejs`]() (javascript runtime)
- [`npm`]() or [`yarn`]() (javascript package manager)
- [`go`]() (go programming language)
- [`rust`]() (rust programming language)
- [`gcc`]() (c/c++ compiler)
- [`clang`]() (c/c++ compiler)
- [`make`]() (build automation)
- [`cmake`]() (build automation)
- [`gdb`]() (debugger)
- [`valgrind`]() (memory debugging)
- [`strace`]() (trace system calls)
- [`ltrace`]() (trace library calls)
- [`postman`]() (api testing)
- [`insomnia`]() (api testing)


### internet

- [`firefox`]() or [`qutebrowser`]() or [`librewolf`]() (web browser)
- [`thunderbird`]() (email client)
- [`transmission-gtk`]() or [`qbittorrent`]() (torrent client)
- [`telegram-desktop`]() (messaging app)


### networking

- [`wireshark`]() (network protocol analyzer)
- [`nmap`]() (network scanner)
- [`speedtest-cli`]() (internet speed test)


### office
- [`libreoffice-fresh`]() (office suite)
- [`zatura`]() (pdf viewer)



### multimedia
- [`mpv`]() (media player)
- [`qimgv`]() (image viewer)
- [`gimp`]() (image editor)
- [`inkscape`]() (vector graphics editor)
- [`audacity`]() (audio editor)
- [`ffmpeg`]() (audio and video converter)



### gui apps

- [`thunar`]() (file manager)
- [`obs-studio`]() (screen recording and streaming)
- [`obsidian`]() (note taking)
- [`ghostwriter`]() (distraction free markdown editor)

### font and latex

- [`ttf-jetbrains-mono`]() (programming font)
- [`texlive-most`]() (latex distribution)
- [`texstudio`]() (latex editor)

### misc

- [`flameshot`]() (screenshot tool)
- [`zotero`]() (reference manager)
- [`bitwarden`]() (password manager)
- [`xrandr`]() (screen resolution management)
- [`brightnessctl`]() (screen brightness management)
- [`playerctl`]() (media player controller)
- [`rustdesk`]() (remote desktop)

## my dotfiles

## few addon modifications

## scripts

## important commands

## tips and tricks

## references

1. [Arch Wiki](https://wiki.archlinux.org/title/List_of_applications/Utilities)
2. [fwcd](https://github.com/fwcd/arch-pkgs/blob/main/README.md)
3. reddit
4. [Find Your Footing by Elijan Mastnak](https://ejmastnak.com/tutorials/arch/about/)

