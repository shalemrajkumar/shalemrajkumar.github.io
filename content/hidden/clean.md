---
title: Clean the Arch Linux filesystem
weight : 0
tags: ["arch", "distro", "clean", "system maintenance"]
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
searchHidden: true
ShowReadingTime: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
---


### system cleaning

#### `cleaning packages`

1. Check the size of the package cache:
   
```bash 
    du -sh /var/cache/pacman/pkg/

```

2. Removing packages

    i. Remove uninstalled packages from the cache:

    ```bash
        sudo pacman -Sc
        yay -Sc

    ```

    ii. Remove all cached packages except for the most recent three versions of each package:

    ```bash
        sudo pacman -S pacman-contrib

        sudo paccache -r

    ```

    iii. Remove all cached packages:

    ```bash
        sudo pacman -Scc
        yay -Scc

    ```
    iv. Remove orphaned packages (packages that were installed as dependencies but are no longer required by any installed package):

    to list them :
    
    ```bash
        pacman -Qdt

    ```

    to remove them:

    ```bash
        sudo pacman -Rns $(pacman -Qtdq)

    ```

#### `cleaning journal logs`

1. Check the size of journal logs:

```bash
    journalctl --disk-usage

```

2. Clean journal logs:

    i. Remove journal logs older than a specific time period (e.g., 2 weeks):

    ```bash
        sudo journalctl --vacuum-time=2weeks

    ```

    ii. Limit the size of journal logs to a specific size (e.g., 500M):

    ```bash
        sudo journalctl --vacuum-size=500M

    ```
    > Note: for more options, you can refer to the `man journalctl` page and edit `/etc/systemd/journald.conf` file to set persistent storage and size limits.

#### clean system temporary files and user cache

1. Clean system temporary files:

```bash
    sudo rm -rf /tmp/*
    sudo rm -rf /var/tmp/*
```
2. Clean user cache:

```bash
    rm -rf ~/.cache/*

```
3. places to look out

    - `~/.config/` -- where applications stores their configuration
    - `~/.cache/` -- cache of some programs may grow in size
    - `~/.local/share/` -- old files may be lying there
    - `~/.streamio-server/` -- cache of streamio applications

### other useful tools

- `gdu` - A disk usage analyzer with a terminal user interface.
- `paccache from pacman-contrib` - A utility to manage the package cache of pacman.
- `tmpwatch` - A utility to remove files that haven't been accessed for a specified period of time.
- `bleachbit` - A graphical tool to clean unnecessary files and free up disk space.
- `fdupes` - A command-line tool to find and remove duplicate files.
- `rmlint` - A tool to find and remove duplicate files and other lint on the filesystem.
- `localepurge (AUR)` - A tool to remove unnecessary locale files and free up disk space.

### automate pacman cleaning

- Systemd timer create file in /etc/systemd/system/paccache.timer

```ini
    [Unit]
    Description=Clean-up old pacman pkg cache

    [Timer]
    OnCalendar=monthly
    Persistent=true

    [Install]
    WantedBy=multi-user.target

```
- enable
```bash
    sudo systemctl enable paccache.timer
    sudo systemctl start paccache.timer

```


### References

- [Arch wiki](https://wiki.archlinux.org/title/System_maintenance#Clean_the_filesystem)
- [siberology](https://www.siberoloji.com/how-to-free-up-disk-space-on-arch-linux/)
- [rumansaleem's clean-up-arch-linux github gist](https://gist.github.com/rumansaleem/083187292632f5a7cbb4beee82fa5031)
