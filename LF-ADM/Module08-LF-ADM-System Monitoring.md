# System Monitoring
## Learning Objectives
By the end of this chapter, you should be able to:
- Understand the concept of inventory and gain familiarity with available system monitoring tools.
- Understand where the system stores log files and examine the most important ones.
- Use the /proc and /sys pseudo-filesystems.
- Use sar to gather system activity and performance data and create reports that are readable by humans.


# Monitoring Tools
While there are a number of graphical system monitors that hide many of the details, we will consider primarily the command line tools in this course.

Linux distributions come with many standard performance and profiling tools already installed. Many of them are familiar from other UNIX-like operating systems while some were developed specifically for Linux.

Most of these tools make use of mounted pseudo-filesystems, especially /proc and secondarily /sys, both of which we have already discussed when examining filesystems, and will revisit when we take up kernel configuration.

The /proc and /sys pseudo-filesystems contain a lot of information about the system. Furthermore, many of the entries in these directory trees are writable and can be used to change system behavior; in most cases this requires a root user.

Like /dev, these are pseudo-filesystems because they exist totally in memory; if you look at the disk partition when the system is not running there will be only an empty directory which is used as a mount point.

Furthermore, the information displayed is gathered only when it is looked at; there is no constant or periodic polling to update entries.

Graphical system monitors available on all Linux distributions:
- gnome-system-monitor
- ksysguard

Table: Process and Load Monitoring Utilities

![image](https://github.com/Yezato/DATACOMM/assets/95903200/eeb42dcd-b949-45b0-bda9-8577e0eb1c01)

Table: Memory Monitoring Utilities

![image](https://github.com/Yezato/DATACOMM/assets/95903200/c6f5dc74-0016-410a-92f0-80e9315fc82c)

Table: I/O Monitoring Utilities

![image](https://github.com/Yezato/DATACOMM/assets/95903200/0392ba06-e7cc-4034-bee1-ad642588ed7e)

Table: Network Monitoring Utilities

![image](https://github.com/Yezato/DATACOMM/assets/95903200/730bce14-3e6a-470f-9228-8f73fd62a370)

Given time constraints, it is not possible to include detailed discussion of network monitoring in this course. However, we do discuss network configuration in some detail later
