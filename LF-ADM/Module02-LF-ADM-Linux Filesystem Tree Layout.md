# Linux Filesystem Tree Layout
## Learning Objectives
By the end of this chapter, you should be able to:

Explain why Linux requires the organization of one big filesystem tree, and what the major considerations are for how it is done.
Explain the role played by the Filesystem Hierarchy Standard.
Describe what must be available at boot in the root (/) directory, and what can be available only once the system has started.
Explore each of the main subdirectory trees, explain their purposes, and examine their contents.

# One Big Filesystem
Linux, like all UNIX-based operating systems, consists of one big filesystem tree. Actually, it is usually diagrammed as an inverted tree with the root directory, /, being at the top of the tree.

Within this one large logical filesystem there may be more than one, even many, distinct filesystems, mounted at various points, which appear as subdirectories. These distinct filesystems are usually on different partitions, which can be on any number of devices, including those which are on a network.

Regardless of exactly how things are joined together, it all just looks like one big filesystem; applications do not usually care at all about what physical device files actually reside on.

Once upon a time, different UNIX-like operating systems organized this one big tree in varying ways; even among the various Linux distributions there were many differences. This made both writing applications and accomplishing system administration tasks on more than one kind of system difficult and often frustrating.

As a consequence, the Linux ecosystem has worked hard to establish standardized procedures to minimize such pain.

# Data Distinctions
When talking about how files and data are organized in the one big directory tree, it is important to learn some taxonomy on what kind of information has to be read and written. In particular, there are two kinds of distinctions.

## Shareable vs. non-shareable
Shareable data is that which can be shared between different hosts. Non-shareable data is that which must be specific to a particular host. For example, user home directories may be shareable, while device lock files are not.

## Variable vs. static
Static data include binaries, libraries, documentation, and anything that does not change without system administrator assistance. Variable data is anything that may change even without a system administrator's help.

# FHS Linux Standard Directory Tree
The Filesystem Hierarchy Standard (FHS), administered originally by the Free Standards Group, and now by The Linux Foundation, specifies the main directories that need to be present, and describes their purposes. The Filesystem Hierarchy Standard document can be retrieved online.

By specifying a standard layout, the FHS simplifies predictions of file locations. While most Linux distributions respect the FHS, probably none of them follow it exactly, and the last official version is old enough that it does not take into account some new developments.

Distributions like to experiment and, eventually, some of the experiments become generally accepted.

### Table: Main Directories
FHS Linux Standard Directory Tree
The Filesystem Hierarchy Standard (FHS), administered originally by the Free Standards Group, and now by The Linux Foundation, specifies the main directories that need to be present, and describes their purposes. The Filesystem Hierarchy Standard document can be retrieved online.

By specifying a standard layout, the FHS simplifies predictions of file locations. While most Linux distributions respect the FHS, probably none of them follow it exactly, and the last official version is old enough that it does not take into account some new developments.

Distributions like to experiment and, eventually, some of the experiments become generally accepted.

Table: Main Directories
![35bde077-1cce-42ad-bace-8069c8369f1d](https://github.com/Yezato/DATACOMM/assets/95903200/f08736a3-d849-4236-8dbe-6d79184f3e14)

There may be additional distribution-specific directories found under the root directory. These might include /misc, which can be used for miscellaneous data, and /tftpboot, which is used for booting using tftp. If there are files in the directory, they are related to diskless workstation booting. Note that it does not violate the FHS to have other directories; however, it does violate it to have components in directories other than those dictated by the standard.

# The Root (/) Directory
While the entire filesystem can be viewed as one big tree, as we have pointed out, there may be multiple partitions and filesystems joined together.

The partition and filesystem that the root directory itself is contained in is rather special and is often in a special dedicated partition, with other components with directories such as /home, /var and /opt, etc., to be mounted later.

The root partition must contain all essential files required to boot the system and then mount all other filesystems. Thus, it needs utilities, configuration files, boot loader information, and other essential startup data. It must be adequate to:

- Boot the system.​
- Restore the system from system backups on external media such as tapes and other removable media or NAS etc.​
- Recover and/or repair the system; an experienced maintainer must have the tools to diagnose and reconstruct a damaged system.
  
According to the FHS, no application or package should create new subdirectories of the root directory.

Important: / is not /root. We are not talking about /root, which is the home directory of root, the superuser. The word choices here can be confusing!

# /bin
The /bin directory is very important because:

- It contains executable programs and scripts needed by both system administrators and unprivileged users, which are required when - no other filesystems have yet been mounted; for example, when booting into single user or recovery mode.
- It may also contain executables which are used indirectly by scripts.
- It may not include any subdirectories.

Required programs which must exist in /bin/ include:

> cat, chgrp, chmod, chown, cp, date, dd, df, dmesg, echo, false, hostname, kill, ln, login, ls, mkdir, mknod, more, mount, mv, ps, pwd, rm, rmdir, sed, sh, stty, su, sync, true, umount and uname

[ and test may be there as well, and optionally, it may include: csh, ed, tar, cpio, gunzip, zcat, netstat and ping.
Command binaries that are deemed not essential enough to merit a place in /bin go in /usr/bin. Programs required only by non-root users are placed in this category.
> Note Some recent distributions have abandoned the strategy of separating /bin and /usr/bin (as well as /sbin and /usr/sbin) and just have one directory with symbolic links, thereby preserving a two directory view. They view the time-honored concept of enabling the possibility of placing /usr on a separate partition to be mounted after boot as obsolete.

Recent distribution versions of RHEL, CentOS, Fedora, and Ubuntu have symbolically linked /bin and /usr/bin so they are actually the same.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/8005ba8b-d88a-42fc-8837-c534715406d2)
/bin Directory: Ubuntu 18.04

# /boot
Essential files for booting the system must be in the /boot directory and its subdirectories.

the two files which are absolutely essential.

## vmlinuz
The compressed Linux kernel.

## initramfs
The initial RAM filesystem, which is mounted before the real root filesystem becomes available.

/boot stores data used before the kernel begins executing user-mode programs. It also includes two files used for information and debugging:

- config Used to configure the kernel compilation.
- System.map Kernel symbol table, used for debugging.
  
The exact contents of /boot will vary by distribution and time; on one Ubuntu system we have:
![image](https://github.com/Yezato/DATACOMM/assets/95903200/d0ced180-71bb-441c-a5db-74f8e16fac0c)
/boot Directory: Ubuntu 20.04

These files have longer names which depend on the Linux distribution and kernel version. Also, instead of initramfs, one might have initrd (initial ram disk.)

# /dev
This directory contains special device files (also known as device nodes) which represent devices built into or connected to the system. These special files are essential for the system to function properly.

Such device files represent character (byte-stream) and block I/O devices. Network devices do not have device nodes in Linux, and are instead referenced by name, such as eth1 or wlan0.

All modern Linux distributions use the udev system, which creates nodes in /dev only as needed when devices are found. If you were to look at the /dev directory on an unmounted filesystem, you would find it empty.

On ancient systems (or embedded devices, it can be created by MAKEDEV or mknod at install or at any other time, as needed

![image](https://github.com/Yezato/DATACOMM/assets/95903200/19d6005c-792f-4950-8882-23f337b85812)

Device Nodes

![image](https://github.com/Yezato/DATACOMM/assets/95903200/fcee95c6-4946-4ad6-97ea-19727b31842e)

/dev Directory

# /etc
/etc This directory contains machine-local configuration files and some startup scripts; there should be no executable binary programs. Files and directories which may be found in this directory include:

> csh.login, exports, fstab, ftpusers, gateways, gettydefs, group, host.conf, hosts.allow, hosts.deny, hosts,equiv, hosts.lpd, inetd.conf, inittab, issue, ld.so.conf, motd, mtab, mtools.conf, networks, passwd, printcap, profile, protocols, resolv.conf, rpc, securetty, services, shells, syslog.conf.

Some of these files are pretty irrelevant today, such as mtools.conf, which is used by floppy disks. Some will not be found any more, no matter what the FHS says, due to software obsolescence.

Distributions often add configuration files and directories to /etc. For example, Red Hat adds a number of other directories, including /etc/sysconfig, where a number of system configuration files and directories live.

## Other Subdirectories
> /etc/skel

Contains skeleton files used to populate newly created home directories.

> /etc/systemd

Contains or points to configuration scripts for starting, stopping system services when using systemd.

> /etc/init.d

Contains startup and shut down scripts when using System V initialization.

# /home
On Linux systems, user directories are conventionally placed under /home, as in /home/coop, /home/student, etc. All personal configuration, data, and executable programs are placed in this directory hierarchy. /home, may also contain subdirectories for various groups or associations of users, such as /home/students, /home/staff, /home/aliens, etc.

On other UNIX-like operating systems, the concept of the /home directory tree exists, but can be subtly different. For example, on Solaris, user directories are created in /export/home and then, the automount facility will eventually mount them in /home. This is because the usual situation is that the home directory may be anywhere on a corporate network, probably on an NFS server, and the home directory will be mounted automatically upon use.

Linux has these same automount facilities, but many users are not even aware of them, and, on self-contained systems, the concept of NFS mounts will probably not apply.

A given user can always substitute the environmental variable $HOME for their root directory, or the shorthand ~; i.e., the following are equivalent:
```lua
ls -l $HOME/public_html
```
```lua
ls -l ~/public_html
```
There is one exception: the home directory for the root user on Linux systems is always found under /root. Some older UNIX systems may use / instead, which can cause clutter.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/c15cd466-6ec3-41e8-b0ba-7245f7035f34)

/home Directory

# /lib and /lib64
These directories should contain only those libraries needed to execute the binaries in /bin and /sbin. These libraries are particularly important for booting the system and executing commands within the root filesystem.

Kernel modules (often device or filesystem drivers) are located under /lib/modules/<kernel-version-number>.

PAM (Pluggable Authentication Modules) files are stored in /lib/security.

Systems which support both 32-bit and 64-bit binaries must keep both kinds of libraries on the system. On some distributions, there are separate directories for 32-bit libraries (/lib) and 64-bit libraries (/lib64).
```lua
$ ls -l /lib*
```
- `rwxrwxrwx 1 root root 8 Apr 23 2020 /sbin -> usr/sbin`
- `lrwxrwxrwx 1 root root 7 Apr 23 2020 /lib -> usr/lib`
- `lrwxrwxrwx 1 root root 9 Apr 23 2020 /lib64 -> usr/lib64`

Note Some recent distributions have abandoned the strategy of separating /lib and /usr/lib (as well as /lib64 and /usr/lib64) and just have one directory with symbolic links, thereby preserving a two directory view. They view the time-honored concept of enabling the possibility of placing /usr on a separate partition to be mounted after boot as obsolete.


# /media
This directory was typically used to mount filesystems on removable media. These include CDs, DVDs, and USB drives, and even Paleolithic era floppies.

Linux systems mount such media dynamically upon insertion, and udev creates directories and then mounts the removable filesystems there, with names that are set with udev rules specified in configuration files. Upon unmounting and removal, the directories used as mount points disappear.

If the media has more than one partition and filesystem, more than one entry will appear. On many Linux distributions, the file manager (such as Nautilus) will pop up when the media is mounted.

> Note With current Linux distributions, removable media will pop up under /run/media/[username]/.... instead of /media. We will discuss /run later.


# /mnt
This directory is provided so that the system administrator can temporarily mount a filesystem when needed. A common use is for network filesystems, including:

- NFS
- Samba
- CIFS
- AFS.
```lua
$ sudo mount c8:/ISO_IMAGES /mnt
```
Historically, **/mnt** was also used for the kinds of files which are now mounted under **/media** (or **/run/media**) in modern systems.

Generally speaking, this directory should not be used by installation programs. Another temporary directory not currently being used serves better.


# /opt
This directory is designed for software packages that wish to keep all or most of their files in one isolated place, rather than scatter them all over the system in directories shared by other software. For example, if dolphy_app were the name of a package which resided under /opt, then all of its files should reside in directories under /opt/dolphy_app, including /opt/dolphy_app/bin for binaries and /opt/dolphy_app/man for any man pages.
```lua
$ ls -l /opt
```
```lua
total 20
drwxr-xr-x 4 root root 4096 Feb 4 2020 brother
drwxr-xr-x 4 root root 4096 Sep 1 2019 google
drwxr-xr-x 29 root root 12288 Jan 19 17:06 zoom
```
This can make both installing and uninstalling software relatively easy, as everything is in one convenient isolated location in a predictable and structured manner. It also makes it easier for system administrators to determine the nature of each file within a package.

> Note, however, if one uses packaging systems such as RPM and APT, as we shall discuss later, it is also easy to install and uninstall with a clear sense of file manifests and locations, without exhibiting such antisocial behavior.

In Linux, the /opt directory is often used by application providers with either proprietary software, or those who like to avoid complications of distribution variance. For example, on one system the packages are in /opt/brother, /opt/zoom and /opt/google and the latter has subdirectories for chrome and earth.

The directories /opt/bin, /opt/doc, /opt/include, /opt/info, /opt/lib, and /opt/man are reserved for local system administrator use. Packages may provide files which are linked or copied to these reserved directories, but the packages must also be able to function without the programs being in these special directories. Most systems do not populate these directories.


# /proc
This directory is the mount point for a pseudo-filesystem, where all information resides only in memory, not on disk. Like /dev, the /proc directory is empty on a non-running system.

The kernel exposes some important data structures through /proc entries. Additionally, each active process on the system has its own subdirectory that gives detailed information about the state of the process, the resources it is using, and its history.

The entries in /proc are often termed virtual files and have interesting qualities. While most are listed as zero bytes in size, when viewed, they can contain a large amount of information.

In addition, most of the time and date settings on virtual files reflect the current time and date, indicative of the fact they are constantly changing. In fact, the information in these files is obtained only when they are viewed; they are not being constantly or periodically updated.

Important pseudo-files, including /proc/interrupts, /proc/meminfo, /proc/mounts, and /proc/partitions, provide an up-to-the-moment glimpse of the system's hardware.

Others, like /proc/filesystems and the /proc/sys/ directory, provide system configuration information and interfaces.

For organizational purposes, files containing information on a similar topic are grouped into virtual directories and sub-directories. For instance, /proc/scsi/ contains information for all physical SCSI devices. Likewise, the process directories contain information about each running process on the system.

We will extensively examine entries in /proc throughout this course, and we will take a more detailed look at them in upcoming chapters about kernel configuration and system monitoring.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/c644ddc8-b931-40a9-893c-a56ae3d6244c)

Screenshot of the ls -F /proc command and output on Ubuntu

In the screenshot below, you can see the contents of the /proc directory belonging to one particular process:

![image](https://github.com/Yezato/DATACOMM/assets/95903200/8ff067c2-14f2-4625-9a1b-7671d1722345)

Screenshot of the ls -F /proc/3589 command and output

In the screenshot below, you can see one of the many files showing important system information, /proc/interrupts.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/ad699fd3-7cea-4aa3-aebe-0b6d614fe8b3)

Screenshot of the cat /proc/interrupts command and output

# /sys
This directory is the mount point for the sysfs pseudo-filesystem where all information resides only in memory, not on disk. Like /dev and /proc, the /sys directory is empty on a non-running system. It contains information about devices and drivers, kernel modules, system configuration structures, etc.

sysfs is used both to gather information about the system, and modify its behavior while running. In that sense, it resembles /proc, but it is younger than and has adhered to strict standards about what kind of entries it can contain. For example, almost all pseudo-files in /sys contain only one line, or value; there are none of the long entries you can find in /proc.

As with /proc, we will examine entries in /sys throughout this course, and it will become relevant in upcoming chapters about kernel configuration and system monitoring.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/d87c9d4b-2ed1-4586-a259-59046f6c2363)

Screenshot of the ls -lF /sys command and its output

# /root
This directory (pronounced "slash-root") is the home directory for the root user.

The root account that owns this directory should only be used for actions which require superuser privilege. For those actions which can be done as a non-privileged user, use another account.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/3f3a9a39-fb11-46df-8170-1caff59f2353)

Screenshot of the ls -saF /root command and its output

# /sbin
This directory contains binaries essential for booting, restoring, recovering, and/or repairing in addition to those binaries in the /bin directory. They also must be able to mount other filesystems on /usr, /home and other locations if needed, once the root filesystem is known to be in good health during boot.

The following programs should be included in this directory (if their subsystems are installed):

> fdisk, fsck, getty, halt, ifconfig, init, mkfs, mkswap, reboot, route, swapon, swapoff, update
```lua
$ ls -l /sbin

rwxrwxrwx 1 root root 8 Apr 23 2020 /sbin -> usr/sbin
```
> Note As mentioned earlier, some recent distributions have abandoned the strategy of separating /sbin and /usr/sbin (as well as /bin and /usr/bin) and just having one directory with symbolic links preserving a two-directory view.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/3b00a99b-5c4d-4ab2-a4a3-11ec94b96dd1)

Screenshot of the ls /sbin command and its output

Recent distribution versions of RHEL, CentOS, Fedora, and Ubuntu have symbolically linked /sbin and /usr/sbin so they are actually the same.

The above screenshot is from Ubuntu 18.04; later versions no longer have a separate un-linked directory.

# /srv
According to the FHS:

"/srv contains site-specific data which is served by this system.

This main purpose of specifying this is so that users may find the location of the data files for particular service, and so that services which require a single tree for readonly data, writable data and scripts (such as cgi scripts) can be reasonably placed.

The methodology used to name subdirectories of /srv is unspecified as there is currently no consensus on how this should be done. One method for structuring data under /srv is by protocol, e.g. ftp, rsync, www, and cvs."

Some system administrators (and distributions) swear by the use of /srv; others ignore it. There is often confusion about what is best to go in /var, as opposed to /srv.

# /tmp
This directory is used to store temporary files, and can be accessed by any user or application. However, the files on /tmp cannot be depended on to stay around for a long time:

- Some distributions run automated cron jobs, which remove any files older than 10 days typically, unless the purge scripts have been modified to exclude them.
- Some distributions remove the contents of /tmp with every reboot. This has been the Ubuntu policy.
- Some modern distributions utilize a virtual filesystem, using the /tmp directory only as a mount point for a ram disk using the tmpfs filesystem. This is the default policy on Fedora systems. When the system reboots, all information is thereby lost; /tmp is indeed temporary!

In the last case, one must avoid creating large files on /tmp: they will actually occupy space in memory rather than disk, and it is easy to harm or crash the system through memory exhaustion. While the guideline is for applications to avoid putting large files in /tmp, there are plenty of applications that violate this policy and which make large temporary files in /tmp. Even if it is possible to put them somewhere else (perhaps by specifying an environment variable), many users are not aware of how to configure this and all users have access to /tmp.

This policy can be canceled on systems using systemd, such as Fedora, by issuing the command:
```lua
$ sudo systemctl mask tmp.mount
```
followed by a system reboot.

# /usr
The /usr directory can be thought of as a secondary hierarchy. It is used for files which are not needed for system booting. Indeed, /usr need not reside in the same partition as the root directory, and can be shared among hosts using the same system architecture across a network.

Software packages should not create subdirectories directly under /usr. Some symbolic links may exist to other locations for compatibility purposes.

This directory is typically read-only data. It contains binaries which are not needed in single user mode. It contains the /usr/local directory, where local binaries and such may be stored. man pages are stored under /usr/share/man.

> Note Some recent distributions have abandoned the strategy of separating /bin, /sbin, /lib, and /lib64 from their partners under /usr, using symbolic links to join them. They view the time-honored concept of enabling the possibility of placing /usr on a separate partition to be mounted after boot as obsolete.

Table: Directories under /usr

![image](https://github.com/Yezato/DATACOMM/assets/95903200/72b88b65-f43c-47d4-b70e-47e8e1ee77a2)

# /var
This directory contains variable (or volatile) data files that change frequently during system operation. These include:

- Log files
- Spool directories and files
- Administrative data files
- Transient and temporary files, such as cache contents.

Obviously, /var cannot be mounted as a read-only filesystem.

For security reasons, it is often considered a good idea to mount /var as a separate filesystem. Furthermore, if the directory gets filled up, it should not lock up the system.

/var/log is where most of the log files are located, and /var/spool is where local files for processes such as mail, printing, and cron jobs are stored while awaiting action.

Table: Subdirectories under /var
![image](https://github.com/Yezato/DATACOMM/assets/95903200/d2aa292f-f41d-49bd-85cb-04597ee0a8f6)

![image](https://github.com/Yezato/DATACOMM/assets/95903200/a555b918-d954-4f08-8ca0-4f8a0ce96074)

Screenshot of the ls -lF /var command and its output

# /run
A new directory tree mounted at /run has been in use for several years by major Linux distributions, and, while proposed for FHS, it has not been formally accepted. However, it is good to know about it, as you are quite likely to encounter it.

The purpose of /run is to store transient files: those that contain runtime information, which may need to be written early in system startup, and which do not need to be preserved when rebooting.

Generally, /run is implemented as an empty mount point, with a tmpfs ram disk (like /dev/shm) mounted there at runtime. Thus, this is a pseudo-filesystem existing only in memory.

Some existing locations, such as /var/run and /var/lock, will be now just symbolic links to directories under /run. Other locations, depending on distribution taste, may also just point to locations under /run.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/56afd6d3-69b5-4967-9d84-a7598bac4bc0)

Screenshot of the ls -rF /run command and its output

# Lab 2.1. Sizes of the Default Linux Directories
1. Checking file at /proc

- Move into `/proc`
```lua
cd /proc
```
- Do a directory listing This should display a number of files and directories:
```lua
ls -F
```
![image](https://github.com/Yezato/DATACOMM/assets/95903200/17894ebf-a711-498e-9a18-6621fd29a5ae)

> Notice Many of the directory names are numbers; each corresponds to a running process and the name is the process ID. An important subdirectory we will discuss later is /proc/sys, under which many system parameters can be examinedor modified.

2. View the following files:

- Displays what type of processor your system is running including the number of CPUs present
```lua
cat  /proc/cpuinfo
```
- Provides a usage report about memory on the system.
```lua
cat /proc/meminfo
```
- display all mounted file systems Linux, It is actually not a real file, but part of the virtual file system.
```lua
cat /proc/mounts
```
The names give a pretty good idea about what information they reveal.

> Note That this information is not being constantly updated; it is obtained only when one wants to look at it

3. Take a peek at any random process directory (if it is not a process you own some of the information might be limited unless you use sudo
```lua
ls -F 44XX
```
![image](https://github.com/Yezato/DATACOMM/assets/95903200/af910001-7ccd-42ad-b25d-9d5197bdcdd7)

Take a look at some of the fields in here such as: cmdline, cwd, environ, mem,and status.


# Lab 2.2. Touring the /proc Filesystem
1. As root, cd into /proc and do a directory listing. This should display a number of files and directories:
```lua
cd /proc
```
```lua
ls -F
```
![image](https://github.com/Yezato/DATACOMM/assets/95903200/8280c8fa-e36e-4b72-be52-63f8a54fce20)

> Notice

> Many of the directory names are numbers; each corresponds to a running process and the name is the process ID. An important subdirectory we will discuss later is /proc/sys, under which many system parameters can be examinedor modified.

2. View the following files:

File displays the type of processor in a system, including the number of CPUs present
```lua
cat /proc/cpu/info
```
Provides a usage report about memory on the system
```lua
cat /proc/meminfo 
```
#File lists the status of all currently mounted file systems
```lua
cat /proc/mounts 
```
The names give a pretty good idea about what information they reveal.

> Note

> That this information is not being constantly updated; it is obtained only when one wants to look at it

3. Take a peek at any random process directory (if it is not a process you own some of the information might be limited unless you use sudo
```lua
ls -F 44XX
```
![image](https://github.com/Yezato/DATACOMM/assets/95903200/62b31200-b886-439b-8696-089225be7238)

Take a look at some of the fields in here such as: cmdline, cwd, environ, mem,and status.
