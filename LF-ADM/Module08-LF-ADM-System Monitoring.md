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

# sar
sar stands for the Systems Activity Reporter. It is an all-purpose tool for gathering system activity and performance data and creating reports that are readable by humans.

On Linux systems, the backend to sar is sadc (system activity data collector), which actually accumulates the statistics. It stores information in the /var/log/sa directory, with a daily frequency by default, but which can be adjusted. Data collection can be started from the command line, and regular periodic collection is usually started as a cron job stored in /etc/cron.d/sysstat.

sar then reads in this data (either from the default locations or by use of a file specified with the -f option), and then produces a report.

sar is invoked via:

> $ sar [ options ] [ interval ] [ count ]

where the report is repeated after interval seconds a total of count times (which defaults to 1). With no options, it gives a report on CPU usage.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/a5d0ac42-ee7b-424a-9367-a20c4bc43454)

Using sar

Table: sar Options

![image](https://github.com/Yezato/DATACOMM/assets/95903200/a3b14d59-74c6-45b0-8055-fa9006f9e5ed)

For example, in the screenshot here you can take a look at getting paging statistics, and then I/O and transfer rate statistics.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/573f0693-a818-451d-98a3-af3711bfaf51)

Using sar to get I/O statistics

# Log Files
System log files are essential for monitoring and troubleshooting. In Linux, these messages appear in various files under /var/log. Exact names vary with Linux distribution.

Ultimate control of how messages are dealt with is controlled by the syslogd (usually rsyslogd on modern systems) daemon, common to many UNIX-like operating systems. The newer systemd-based systems can use journalctl instead, but usually retain syslogd and cooperate with it.

​Important messages are sent not only to the logging files, but also to the system console window; if you are not running a graphical interface or are at a virtual terminal, you will see them directly there as well. In addition, these messages will be copied to /var/log/messages (on RHEL) or to /var/log/syslog (on Ubuntu), but if you are running X or Wayland, you have to take some steps to view them as they come in fresh.

You can view new messages continuously as new lines appear with:
```lua
sudo tail -f /var/log/messages
```
or
```lua
$ dmesg -w
```
which shows only kernel-related messages.

A good way to see log messages is to open a terminal window, and in that window type tail -f /var/log/messages.

On a GNOME desktop, you can also access the messages by clicking on System -> Administration -> System Log or Applications -> System Tools -> Log File Viewer in your Desktop menus, and other desktops have similar links you can locate.

Table: Some important log files found under /var/log
![image](https://github.com/Yezato/DATACOMM/assets/95903200/40f8fac3-e25a-4c25-af27-3cf63404a582)

In order to keep log files from growing without bound, the logrotate program is run periodically and keeps four previous copies (by default) of the log files (optionally compressed) and is controlled by /etc/logrotate.conf.

# stress-ng
stress is a C language program written by Amos Waterland at the University of Oklahoma, licensed under the GPL v2. It is designed to place a configurable amount of stress by generating various kinds of workloads on the system.

stress-ng is essentially an enhanced version of stress, which respects its symptoms and options. It is actively maintained: see https://wiki.ubuntu.com/Kernel/Reference/stress-ng

# Function
stress-ng is a powerful tool for stress testing computer systems. It can be used to generate workloads that stress the CPU, memory, I/O, and other subsystems of a system. stress-ng can be used to identify hardware and software bottlenecks, and to test the stability of a system under heavy load.

stress-ng includes a wide range of stressors, which are tests that can be used to stress different parts of a system. Some of the most common stressors include:

cpu: Stresses the CPU by performing mathematical operations.
memory: Stresses the memory by allocating and deallocating large amounts of memory.
io: Stresses the I/O subsystem by reading and writing to files or devices.
network: Stresses the network by sending and receiving packets.
vm: Stresses the virtual memory system by creating and destroying virtual machines.
Identity
stress-ng is a powerful tool, but it should be used with caution. Over-stressing a system can cause it to crash or become unstable. It is important to monitor the system while stress testing, and to stop the stress test if the system begins to overheat or exhibit other signs of instability.

Here are some examples of how stress-ng can be used:

To test the stability of a new server before deploying it to production.
To identify bottlenecks in a system before they cause performance problems.
To test the performance of a system under different workloads.
To troubleshoot system crashes or other problems.
stress-ng is a free and open source tool that is available for Linux, macOS, and Windows. It is also included in many Linux distributions.

