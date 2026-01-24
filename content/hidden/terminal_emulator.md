---
title: Terminal Emulator
weight : 0
tags: ["terminal", "emulator", "cli"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
# description: ""
# canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
hideSummary: false
searchHidden: true
ShowReadingTime: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
---


## Introduction

Terminal Emulators replicate the functionality of traditional computer terminals within a GUI environment. They allow users to interact with the command line interface (CLI) of an operating system, providing a text-based interface for executing commands, running scripts, and managing files.

## Best Terminal Emulators

1. [Kitty](https://sw.kovidgoyal.net/kitty/)
2. [Ghostty](https://ghostty.org/)
3. [WezTerm](https://wezterm.org/index.html)
4. [Alacritty](https://alacritty.org/)

## Alacritty

I am not really a power user of Alacritty, but I have heard good things about it. It has been known for its speed and simplicity, I liked it have some level of vim-like keybindings for navigation.

My Alacritty [config files]() with Dracula theme.

## Kitty

1. Remove Alacritty 
```
sudo pacman -Rns alacritty
``````
2. Install Kitty 
```
sudo pacman -S Kitty
```
3. make config file 
```
nvim ~/.config/kitty/kitty.conf
```

4. Create a symbolic link for the config file:

```fish
ln -s ~/dotfiles/kitty/kitty.conf ~/.config/kitty/kitty.conf
```
5. I used kitty [config file](https://github.com/Fadilix/dotfiles/blob/main/kitty/kitty.conf) from Fadilix

6. I update my fish shell config such that my alias for ls uses `exa` with colors and icons:
```fish
alias ls='exa --hyperlink --icons --color=auto'
```
7. create a new file in `~/config/kitty/open-action.conf` and add the following content   
 ```conf
 # Tail a log file (*.log) in a new OS Window and reduce its font size
 protocol file
 ext log
 action launch --title ${FILE} --type=os-window tail -f -- ${FILE_PATH}
 action change_font_size current -2
 
 # Open any file with a fragment in vim, fragments are generated
 # by the hyperlink-grep kitten and nothing else so far.
 protocol file
 fragment_matches [0-9]+
 action launch --type=overlay --cwd=current vim +${FRAGMENT} -- ${FILE_PATH}
 
 # Open text files without fragments in the editor
 protocol file
 mime text/*
 action launch --type=overlay --cwd=current -- ${EDITOR} -- ${FILE_PATH}
 
 # Open any image in the full kitty window by clicking on it
 protocol file
 mime image/*
 action launch --type=overlay kitten icat --hold -- ${FILE_PATH}
 
 protocol filelist
 action send_text all ${FRAGMENT}
 
 # Open directories
 protocol file
 mime inode/directory
 action launch --type=overlay --cwd -- $FILE_PATH
 ```
 these changes are based on kitty documentation :

 - [Scripting the mouse click](https://sw.kovidgoyal.net/kitty/open_actions/#scripting-the-mouse-click)
 - [Hyperlink Grep Kitten](https://sw.kovidgoyal.net/kitty/kittens/hyperlinked_grep/#hyperlinked-grep)

8. Download themes using 
```
kitty +kitten themes --list
```
9. my additional addons to kitty.conf
```conf
# Hyperlink hints
map ctrl+f kitten hints --type hyperlink
```
