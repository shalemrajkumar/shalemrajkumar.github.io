---
title: My Pacman Notes
weight : 0
tags: ["arch", "distro", "pacman"]
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


### Pacman : What we need to know

Pacman is the package manager for Arch Linux and its derivatives. It is used to install, update, and manage software packages on the system. Here are some key points to know about Pacman:

1. **Basic Commands**:
   - `pacman -S <package>`: Install a package.
   - `pacman -R <package>`: Remove a package.
   - `pacman -Syu`: Update the system (synchronize package databases and upgrade all packages).
   - `pacman -Ss <search_term>`: Search for a package in the repositories using string or regex.
   - `pacman -Qi <package>`: Display information about an installed package.
   - `pacman -Ql <package>`: List files installed by a package.

2. **Configuration File**:
   - The main configuration file for Pacman is located at `/etc/pacman.conf`. This file contains settings for repositories, package options, and other configurations.

3. **Package Databases**:
   - Pacman uses package databases to keep track of available packages. The databases are stored in `/var/lib/pacman/sync/`.

4. **Repositories**:
   - Pacman uses repositories to find and download packages. The default repositories include `core`, `extra`, and `community`. Users can also add custom repositories.

5. **Package Files**:
   - Pacman uses `.pkg.tar.zst` files for packages, which are compressed tar archives.

6. **Dependency Management**:
   - Pacman automatically handles dependencies when installing or removing packages, ensuring that all required libraries and tools are present.

7. **Cache Management**:
   - Pacman stores downloaded package files in `/var/cache/pacman/pkg/`. Users can clean the cache using `pacman -Sc` to remove old package files.

8. **Hooks**:
   - Pacman supports hooks, which are scripts that can be executed before or after certain Pacman operations. Hooks are stored in `/etc/pacman.d/hooks/`.


9. **Arguments and options**:
    -  `d` :  Display a list of packages that depend on the specified package.
    -  `k` :  Check the integrity of installed packages.
    -  `Q` :  Query the package database.
    -  `T` :  Check for missing files from installed packages.
    -  `U` :  Install a package from a local file.
    -  `V` :  Display the version of Pacman.
    -  `y` :  Refresh the package database.
    -  `z` :  Compress or decompress package files.


