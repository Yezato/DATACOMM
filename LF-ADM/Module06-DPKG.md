# DPKG Essentials
DPKG (Debian Package) is the packaging system used to install, remove, and manage software packages under Debian Linux and other distributions derived from it. Like RPM, it is not designed to directly retrieve packages in day-to-day use, but to install and remove them locally.

Package files have a .deb suffix and the DPKG database resides in the /var/lib/dpkg directory.

Like rpm, the dpkg program has only a partial view of the universe: it knows only what is installed on the system, and whatever it is given on the command line, but knows nothing of the other available packages, whether they are in some other directory on the system, or out on the Internet. As such, it will also fail if a dependency is not met, or if one tries to remove a package other installed packages need.

# Package File Names and Source
Debian package file names are based on fields that represent specific information. The standard naming format for a binary package is:

> (name)(version)-(revision_number)(architecture).deb

as in:

> logrotate_3.14.0-4_amd64.deb

> logrotate_3.14.0-4ubuntu3_amd64.deb

For historical reasons, the 64-bit x86 platform is called amd64 rather than x86_64, and distributors such as Ubuntu manage to insert their name in the package name.

In the Debian packaging system, a source package consists of at least three files:

- An upstream tarball, ending with .tar.gz. This is the unmodified source as it comes from the package maintainers.
- A description file, ending with .dsc, containing the package name and other metadata, such as architecture and dependencies.
- A second tarball that contains any patches to the upstream source, and additional files created for the package, and ends with a name .debian.tar.gz or .diff.gz, depending on distribution.
  
For example, on a Ubuntu system, one can download a source package, and then see what files are downloaded or created, as seen below.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/9a602344-5db0-42f9-b7e0-6e4c75326d4c)

Getting Source Packages on Ubuntu
