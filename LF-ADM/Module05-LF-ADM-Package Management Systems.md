# Package Management Systems
## Learning Objectives
By the end of this chapter, you should be able to:

- Explain why software package management systems are necessary.
- Understand the function of both binary and source packages.
- Enumerate the main available package management systems.
- Understand why two levels of utilities are needed: one that deals with just bare packages, and one that deals with dependencies among packages.
- Explain how creating your own package enhances the control you have over exactly what goes in software and how it is installed.
- Understand the role of source control systems, and git in particular.

# Why Use Packages?
Software package management systems are widely seen as one of the biggest advancements Linux brought to enterprise IT environments. By keeping track of files and metadata in an automated, predictable and reliable way, system administrators can use package management systems to make their installation processes scale to thousands of systems without requiring manual work on each individual system. Features include:

- Automation: No need for manual installs and upgrades.
- Scalability: Install packages on one system, or 10,000 systems.
- Repeatability and predictability.
- Security and auditing.

# Software Packaging Concepts
Package management systems supply the tools that allow system administrators to automate installing, upgrading, configuring and removing software packages in a known, predictable and consistent manner. These systems:

- Gather and compress associated software files into a single package (archive), which may require other packages to be installed first.
- Allow for easy software installation or removal.​
- Can verify file integrity via an internal database.​
- Can authenticate the origin of packages.​
- Facilitate upgrades.​
- Group packages by logical features.​
- Manage dependencies between packages.

A given package may contain executable files, data files, documentation, installation scripts and configuration files. Also included are metadata attributes such as version numbers, checksums, vendor information, dependencies, descriptions, etc.
Upon installation, all that information is stored locally into an internal database, which can be conveniently queried for version status and update information.

# Package Types
Packages come in several different types.

## Binary Packages
Binary packages contain files ready for deployment, including executable files and libraries. These are architecture dependent.

## Source Packages
Source packages are used to generate binary packages; you should always be able to rebuild a binary package from the source package. One source package can be used for multiple architectures.

## Architecture-independent
Architecture-independent packages contain files and scripts that run under script interpreters, as well as documentation and configuration files.

## Meta-packages
Meta-packages are groups of associated packages that collect everything needed to install a relatively large subsystem, such as a desktop environment, or an office suite, etc.

# Available Package Management Systems
There are two very common package management systems.

## RPM (Red Hat Package Manager)
This system is used by all Red Hat-derived distributions, such as Red Hat Enterprise Linux, Fedora, CentOS, as well as by SUSE and its related community openSUSE distribution.

## dpkg (Debian Package)
This system is used by all Debian-derived distributions, including Debian, Ubuntu and Linux Mint.

There are other package management systems, such as portage/emerge used by Gentoo, pacman used by Arch, and specialized ones used by Embedded Linux systems and Android.

Another ancient system is just to supply packages as tarballs without any real management or clean removal strategies; this approach still marks Slackware, one of the oldest Linux distributions.

But most of the time, it is either RPM or dpkg, and these are the only ones we will consider in this course.

# Packaging Tool Levels and Varieties
## Packaging Tool Levels
the two levels of packaging systems.

### Low Level Utilities
This simply installs or removes a single package, or a list of packages, each one of which is individually and specifically named. Dependencies are not fully handled, only warned about or produce an error:
- If another package needs to be installed, first installation will fail.
- If the package is needed by another package, removal will fail.

The rpm and dpkg utilities play this role for the packaging systems that use them.

### High Level Utilities
This solves the dependency problems:
- If another package or group of packages needs to be installed before software can be installed, such needs will be satisfied.
- If removing a package interferes with another installed package, the administrator will be given the choice of either aborting, or removing all affected software.

The dnf and zypper utilities (and the older yum) take care of the dependency resolution for rpm systems, and apt-get and apt-cache and other utilities take care of it for dpkg systems.

# Package Sources
Every distribution has one or more package repositories where system utilities go to obtain software and to update with new versions. It is the job of the distribution to make sure all packages in the repositories play well with each other.

There are always other external repositories which can be added to the standard distribution-supported list. Sometimes, these are closely associated with the distribution, and only rarely produce significant problems; an example would be the EPEL (Extra Packages for Enterprise Linux) set of version-dependent repositories, which fit well with RHEL since their source is Fedora and the maintainers are close to Red Hat.

However, some external repositories are not very well constructed or maintained. For example, when a package is updated in the main repository, dependent packages may not be updated in the external one, which can lead to one form of dependency hell.

# Creating Software Packages
Building your own custom software packages makes it easy to distribute and install your own software. Almost every version of Linux has some mechanism for doing this.

Building your own package allows you to control exactly what goes in the software and exactly how it is installed. You can create the package so that installing it runs scripts that perform all tasks needed to install the new software and/or remove the old software, such as:
- Creating needed symbolic links
- Creating directories as needed
- Setting permissions
- Anything that can be scripted.

There are many formats available:
- RPM for Red Hat and SUSE-based systems
- DEB for Debian for Debian-based systems
- TGZ for Slackware
- APK for Android

We will not discuss mechanisms of how to build .rpm or .deb packages, as that is a question mostly for developers, rather than administrators.

# Revision Control Systems
Software projects become more complex to manage as either the size of the project increases, or the number of contributing developers goes up.

In order to organize updates and facilitate cooperation, many different schemes are available for source control. Standard features of such programs include the ability to keep an accurate history, or log, of changes, be able to back up to earlier releases, coordinate possibly conflicting updates from more than one developer, etc.

Source Control Systems (or Revision Control Systems, as they are also commonly called) fill the role of coordinating cooperative development.

There is no shortage of available products, both proprietary and open; a brief list of products released under a GPL license includes:

- Revision Control System (RCS)
- Concurrent Versions System (CVS)
- Apache Subversion
- git
- GNU Arch
- Monotone
- Mercurial.

We will focus only on git, a widely used product which arose from the Linux kernel development community. git has risen to a dominant position in use for open source projects in a remarkably short time, and is often used even in closed source environments.

# The Linux Kernel and the Birth of git
The Linux kernel development system has special needs in that it is widely distributed throughout the world, with literally thousands of developers involved. Furthermore it is all done very publicly, under the GPL license.

For a long time, there was no real source revision control system. Then, major kernel developers went over to the use of BitKeeper, a commercial project which granted a restricted use license for Linux kernel development.

However, in a very public dispute over licensing restrictions in the spring of 2005, the free use of BitKeeper became unavailable for Linux kernel development.

The response was the development of git, whose original author was Linus Torvalds. The source code for git can be obtained from the Index of /pub/software/scm/git, and full documentation can be found online as well.

# How git Works
Technically, git is not a source control management system in the usual sense, and the basic units it works with are not files. It has two important data structures: an object database and a directory cache.

The object database contains objects of three varieties:

- Blobs: Chunks of binary data containing file contents
- Trees: Sets of blobs including file names and attributes, giving the directory structure
- Commits: Changesets describing tree snapshots.
  
The directory cache captures the state of the directory tree.

By liberating the controls system from a file-by-file-based system, one is better able to handle changesets which involve many files.

git is always under rapid development and graphical interfaces to it are also under speedy construction. For example, see git repositories web page. You can easily browse particular changes, as well as source trees. GitHub now host literally millions of git repositories, both public and private. There are a host of easy-to-find articles, books, online tutorials, etc., on how to profitably use git.


# Lab 5.1. Version Control with git
1. Install servicss git
```lua
sudo apt-get install git
```
2. First we create a working directory and then initialize git to work with it:
Create directory "git-test"
```lua
mkdir git-test
```
Move in to directory
```lua
cd git-test
```
Create a new Git repository for directory git-test
```lua
git init
```
3. Initializing the project creates a .git directory which will contain all the version control information; the main directories included in the project remain untouched. The initial contents of this directory look like:
Show all contents on directory .git
```lua
ls -l .git
```
```lua
total 40
drwxrwxr-x 2 coop coop 4096 Dec 30 13:59 branches/
-rw-rw-r-- 1 coop coop 92 Dec 30 13:59 config
-rw-rw-r-- 1 coop coop 58 Dec 30 13:59 description
-rw-rw-r-- 1 coop coop 23 Dec 30 13:59 HEAD
drwxrwxr-x 2 coop coop 4096 Dec 30 13:59 hooks/
drwxrwxr-x 2 coop coop 4096 Dec 30 13:59 info/
drwxrwxr-x 4 coop coop 4096 Dec 30 13:59 objects/
drwxrwxr-x 4 coop coop 4096 Dec 30 13:59 refs/
```
4. Next we create a file and add it to the project:
Create a dummy file "some junk"
```lua
echo some junk > somejunkfile
```
Add a new file "somejunkfile" to staging 
```lua
git add somejunkfile
```
5. We can see the current status of our project with:
```lua
git status
```
```lua
On branch master

Initial commit

Changes to be committed:
           (use "git rm --cached <file>..." to unstage)
                           new file: somejunkfile
```
Notice it is telling us that our file is staged but not yet committed
6. Let’s tell git who is responsible for this repository:
Inisialization username information is for git local 
```lua
git config user.name "<username_adinusa>"
```
## Inisialization email information is for git local 
```lua
git config user.email "<email>"
```
This must be done for each new project unless you have it predefined in a global configuration file.

7. Now let’s modify the file, and then see the history of differences:

Modify file "somejunkfile" on stagging 
```lua
echo tambah kalimat >> somejunkfile
```
Compare the differences between two versions of a file or directory 
```lua
git diff
```
```lua
diff --git a/somejunkfile b/somejunkfile

index 9638122..6023331 100644

previous files 

--- a/somejunkfile

## new files after modify 
+++ b/somejunkfile
@@ -1 +1,2 @@
tambah kalimat
+another line
```
8. To actually commit the changes to the repository we do:
Commit all staged changes with a custom commit message
```lua
git commit -m "My initial commit"
```
```lua
Created initial commit eafad66: My initial commit
1 files changed, 1 insertions(+), 0 deletions(-)
create mode 100644 somejunkfile
```
9. You can see your history with:
```lua
git log
```
```lua
commit cfe4caeddaf9ba4169fd036ffdffdefbc00c25de (HEAD -> master)
Author: <username_adinusa> <email_adinusa>
Date: Fri Sep 22 03:31:03 2023 +0000

My initial commit
```
and you can see the information got in there. You will note the long hexadecimal string which is the commit number; it is a 160-bit, 40-digit unique identifier. git cares about these beasts, not file names.
