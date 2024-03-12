# APT
##Introduction
For use on Debian-based systems, the APT (Advanced Packaging Tool) set of programs provides a higher level of intelligent services for using the underlying dpkg program, and plays the same role as dnf on Red Hat-based systems. The main utilities are apt-get and apt-cache. It can automatically resolve dependencies when installing, updating and removing packages. It accesses external software repositories, synchronizing with them and retrieving and installing software as needed.

## Learning Objectives
By the end of this chapter, you should be able to:
- Explain what APT is.
- Perform package queries.
- Clean up system information about packages.
- Install, remove, and upgrade packages using apt.

# What Is APT?
For use on Debian-based systems, the APT (Advanced Packaging Tool) set of programs provides a higher level of intelligent services for using the underlying dpkg program, and plays the same role as dnf on Red Hat-based systems. The main utilities are apt-get and apt-cache. It can automatically resolve dependencies when installing, updating and removing packages. It accesses external software repositories, synchronizing with them and retrieving and installing software as needed.

The APT system works with Debian packages whose files have a .deb extension. There are many distributions that have descended from Debian (including Ubuntu and Linux Mint) which have adopted the Debian packaging system with no essential modification. In fact, it is not uncommon to use a repository on more than one Debian-based Linux distribution.

Once again, we are going to ignore graphical interfaces (on your computer), such as Synaptic or the Ubuntu Software Center, or other older front ends to APT, such as aptitude.

> Note

> For almost all interactive purposes, it is no longer necessary to use apt-get; one can just use the shorter name apt. However, you will see apt-get used all the time out of habit, and it works better in scripts. In this course we will use apt-get because the commands we reference can be used either at the command line or in scripts.

# apt, apt-get, apt-cache, etc.
ap  t, apt-get, apt-cache utilities are the APT command line tools for package management. They can be used to install, manage and upgrade individual packages or the entire system, and can even upgrade the distribution to a completely new release, which can be a difficult task.

There are even (imperfect) extensions that let apt work with rpm files.

Like dnf and zypper, apt works with multiple remote repositories.

Excellent Internet-based resources can be found on the Debian packages webpage and the Ubuntu packages webpage. These databases let you search for packages, examine their contents, and download.

# Queries
Queries are done using the apt-cache or apt-file utilities. You may have to install apt-file first, and update its database, as in:
```lua
sudo apt-get install apt-file
```
```lua
sudo apt-file update
```
Searches the repository for a package named apache2:
```lua
apt-cache search apache2
```
Displays basic information about the apache2 package:
```lua
apt-cache show apache2
```
Displays detailed information about the apache2 package:
```lua
apt-cache showpkg apache2
```
Lists all dependent packages for apache2:
```lua
apt-cache depends apache2
```
Searches the repository for a file named apache2.conf:
```lua
apt-file search apache2.conf
```
Lists all files in the apache2 package:
```lua
apt-file list apache2
```

# Installing/Removing/Upgrading Packages with apt
Used to install new packages or update a package which is already installed:
```lua
sudo apt-get install [package]
```
Used to remove a package from the system (this does not remove the configuration files):
```lua
sudo apt-get remove [package]
```
Used to remove a package and its configuration files from a system:
```lua
sudo apt-get --purge remove [package]
```
Used to synchronize the package index files with their sources. The indexes of available packages are fetched from the location(s) specified in /etc/apt/sources.list:
```lua
sudo apt-get update
```
upgrade is used to apply all available updates to packages already installed; dist-upgrade will not update to a whole new distribution version as is commonly misunderstood:
```lua
sudo apt-get upgrade
```
```lua
sudo apt-get dist-upgrade
```
> Note

> That you must update before you upgrade, unlike with dnf or zypper, which can be confusing to habitual users of those utilities.

# Cleaning Up
Gets rid of any packages not needed anymore, such as older Linux kernel versions:
```lua
sudo apt-get autoremove
```
Cleans out cache files and any archived package files that have been installed:
```lua
sudo apt-get clean
```
This can save a lot of space.

# Lab Requirements
To do the following labs you need to have access to a system that is Debian-based, such as Debian, Ubuntu, or Linux Mint.
## Lab 7.1. Basic APT Commands
1. Check to see if there are any available upgrades for your system.
```lua
## lists all of the packages on your system that can be upgraded to a newer version
sudo apt list --upgradable
```
2. List all installed kernel-related packages, and check current kernel linux.
```lua
## list all installed kernel-related packages
dpkg --list | grep linux-image
```
```lua
## kernel is currently running
uname -r
```
3. List of all of the package names that are known to the APT package manager.
```lua
## List all of the package names that are known to APT
apt-cache pkgnames
```
```lua
## Specific packages list
apt-cache pkgnames | grep "traceroute"
```
4. List of the all package and Install the traceroute package
```lua
## Install package 
apt-get install traceroute
```

## Lab 7.2. Using APT to Find Information About a Package
1. First synchronize the package index files with remote repositories:
```lua
sudo apt-get update -y
```
2. Check to see if there are any available upgrades for your system.
```lua
## lists all of the packages on your system that can be upgraded to a newer version
sudo apt list --upgradable
```
3. List all installed kernel-related packages, and check current kernel linux.
```lua
## list all installed kernel-related packages
dpkg --list | grep linux-image
```
```lua
# Alternative list
apt-cache search -n "kernel"
```
```lua
## kernel is currently running
uname -r
```
4. List of all of the package names that are known to the APT package manager.
```lua
## List all of the package names that are known to APT
apt-cache pkgnames
```
```lua
## Specific packages list
apt-cache pkgnames | grep "traceroute"
```
5. List of the all package and Install the traceroute package
```lua
## Install package 
apt-get install traceroute
```
