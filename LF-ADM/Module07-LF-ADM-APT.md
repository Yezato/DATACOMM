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
