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


