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
