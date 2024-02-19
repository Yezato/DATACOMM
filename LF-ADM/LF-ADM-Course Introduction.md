# Course Introduction
We are assuming you have either taken The Linux Foundation's free Introduction to Linux (6th Ed.) at edX (this MOOC - Massive Open Online Course - can be found by clicking here, or by searching the edX website for "Linux"), or you have knowledge equivalent to, or better than what would be gained from taking that course.

LFS101x is free and, even if you feel you do not need to take that course, we recommend you sign up for it; you can at least do a quick browse through the material. Some of the content areas covered in LFS101x are covered in this course as well, usually at a more detailed level, befitting our audience. The topic of package management is one such example. Rather than repeat ourselves, we urge you to reference the introductory course for basic topics not covered here.

There are some other topics which are necessary for system administrators to be knowledgeable about, that we will not cover in this course. In particular:

- Text editors (vi, emacs, nano, gedit, etc.)
- Finding Linux documentation (getting help)
- Manipulating text (sed, grep, awk, cut, paste, tail, head, etc.)
- File utilities (find, locate, file, etc.)
- Printing (configuring printers, managing print jobs)
- Graphical interfaces and their administration
- bash shell scripting
- System installation.

Before you take this course, we strongly recommend that you at least take a look at LFS101x. If you are not completely familiar or comfortable with some of the basic utilities and procedures we will be using in this course, you will probably find they are discussed there: this course is not meant to stand alone.

# Course Audience And Requirements
## Audience
This course is designed primarily for system administrators.

We are assuming that most of you are tasked with configuring, running, and troubleshooting multiple machines in what is called an Enterprise environment. While you are likely to have significant experience with other operating systems, you want to learn how to do things properly and natively in Linux, while drawing on your previous knowledge.

It is also likely that you are already using Linux, but have not yet seriously studied topics more complicated than keeping your own personal system running and secure.

This course is not designed primarily for:

- New users without much experience with any operating system relatively new Linux users are advised to take this course after partaking in an introductory course, such as The Linux Foundation's Introduction to Linux (LFS101x).
- Developers (at either kernel or application level) most developers will find this material interesting, and the more anyone knows about operating system theory, kernel internals, applications, etc., the more they will get out of this course. And there are plenty of "power users" who do both administration and developers.
- Experienced administrators concentrating on performance tuning we will discuss some performance tuning issues because administration and performance are clearly linked. However, we will not get into great detail, as this belongs in a more advanced and dedicated performance tuning course, such as The Linux Foundation's LFS426.

## Knowledge/Skills
Basic knowledge of Linux, including installing Linux and using the command line is helpful, but not required.

## Software Environment
The material produced by The Linux Foundation is distribution-flexible. This means that technical explanations, labs and procedures should work on most modern Linux distributions, and we do not promote products sold by any specific vendor (although we may mention them for specific scenarios).

In practice, most of our material is written with the three main Linux distribution families in mind:

- Debian/Ubuntu
- Red Hat/Fedora​
- openSUSE/SUSE.
  
Distributions used by our students tend to be one of these three alternatives, or a product that is derived from them.

# Target Platform
This course is designed to work on x86-based platforms, either on native hardware or running as a virtual machine (VM), under a hypervisor, such as those in the KVM, VMWare, or Virtual Box families.

Almost everything in this course is completely relevant for other architectures, but real or virtual x86 covers the majority of deployments.

We will not consider 32-bit and will assume 64-bit throughout. However, if you are running a 32-bit x86 for some reason, pretty much everything we are doing will still be valid, although we have not tested for it.

We are also not targeting embedded Linux platforms, where even though deployments are based on the same Linux kernel and use many of the same user space ingredients, one does not usually talk about system administration for devices that are used as appliances.

There are important basic ingredients which are quite different, like using different boot loader programs and filesystems. Furthermore, resources such as memory, processor power and storage capacity differ greatly.

The same considerations apply to Android (a particular form of embedded Linux), where while the kernel is Linux, the filesystem and application space are extremely different from what is found on standard Linux systems.

# Command Line vs. Graphical Interface
Many administrative tasks can be accomplished either from the command line or from within a graphical application. There is almost always more flexibility and additional capabilities in the command line approach, since the indirection layer has been removed. The drawback for the command line, however, is that the administrator may have more to remember or look up when a task needs to be accomplished.

There are a variety of graphical desktop environments used in Linux, the two most common being GNOME and KDE. Each of them comes in multiple versions, or generations.

In this course, we do not want to get involved in graphical interfaces, as they vary too much among Linux distribution families and versions. Furthermore, many servers do not have a graphical interface installed.

However, we will do a few video demonstrations showing the use of graphical interfaces, so you can get a feel for how they work.

If you have already taken LFS101x, then you are already familiar with the issues.

The benefit is that there will be very little difference between distributions for what we are doing, and other than straightforward tasks involving package management, we will not have to explain things separately for each distribution very often.

For this course, you can work from the command line on the local machine, using either a console, or a terminal emulator such as gnome-terminal running on a graphical desktop. Or, you can work remotely through an ssh or vpn session. Almost everything we are doing will apply equally well in the remote session case. Exceptions will arise only when one needs physical access to the machine, such as when trying to boot into a rescue environment.
