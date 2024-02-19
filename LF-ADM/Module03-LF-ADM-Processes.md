# Processes
## Learning Objectives
By the end of this chapter, you should be able to:
- Describe a process and the resources associated with it.
- Describe the role of the init process.
- Distinguish between processes, programs and threads.
- Understand process attributes, permissions and states, and know how to control limits.
- Explain the difference between running in user and kernel modes.
- Describe daemon processes.
- Understand how new processes are forked (created).
- Use nice and renice to set and modify priorities.
- Understand how shared and static libraries are used.

# What Is a Program?
A program is a set of instructions, along with any internal data used while carrying the instructions out. Programs may also use external data. Internal data might include text strings inside the program, which are used to display user prompts. External data might include data from a database. Programs consist of machine level instructions run directly by a CPU or a list of commands to be interpreted by another program. Programmers use various languages (such as C, C++, Perl, and many more) to code instructions in a program.

Many user commands, such as ls, cat and rm are programs which are external to the operating system kernel, or shell (in other words, they have their own executable program on disk).

> Note

> People often distinguish between programs, which are compiled into a binary executable form; and scripts, which need to be run by an interpreter such as bash, Python or Perl.

# What Is a Process?
A process is an instance of a program in execution. It may be in a number of different states, such as running or sleeping.

Every process has a pid (Process ID), a ppid (Parent Process ID), and a pgid (Process Group ID). In addition, every process has program code, data, variables, file descriptors, and an environment.

Processes are controlled by scheduling, which is completely preemptive. Only the kernel has the right to preempt a process; they cannot do it to each other.

For historical reasons, the largest PID has been limited to a 16-bit number, or 32768. It is possible to alter this value by changing /proc/sys/kernel/pid_max, since it may be inadequate for larger servers. As processes are created, eventually they will reach pid_max, at which point they will start again at PID = 300.

> Note

> A program may be composed of multiple simultaneous threads (multithreading), each of which is considered as its own process.

# Process Attributes
All processes have certain attributes:

- The program being executed
- Context (state)
- Permissions
- Associated resources.
  
Every process is executing some program. At any given moment, the process may take a snapshot of itself by trapping the state of its CPU registers, where it is executing in the program, what is in the process' memory, and other information. This is the context of the process.

Since processes can be scheduled in and out when sharing CPU time with others (or have to be put to sleep while waiting for some condition to be fulfilled, such as the user to make a request or data to arrive), being able to store the entire context when swapping out the process and being able to restore it upon execution resumption is critical to the kernel's ability to do context switching.

Every process has permissions based on which user has called it to execute. It may also have permissions based on who owns its program file. Programs which are marked with an ”s” execute bit have a different ”effective” user id than their ”real” user id. These programs are referred to as setuid programs. They run with the user-id of the user who owns the program, where a non-setuid program runs with the permissions of the user who starts it. setuid programs owned by root can be a security problem.

The passwd command is an example of a setuid program. It is runnable by any user. When a user executes this program, the process runs with root permission in order to be able to update the write-restricted files, /etc/passwd and /etc/shadow, where the user passwords are maintained.

> Note that every process has resources such as allocated memory, file handles, etc.

# Process Resource Isolation
When a process is started, it is isolated in its own user space to protect it from other processes. This promotes security and creates greater stability.

Processes do not have direct access to hardware. Hardware is managed by the kernel, so a process must use system calls to indirectly access hardware. System calls are the fundamental interface between an application and the kernel.

# Controlling Processes with ulimit
ulimit is a built-in bash command that displays or resets a number of resource limits associated with processes running under a shell. You can see what running ulimit with the -a argument gives us in the screenshot below.

![image](https://github.com/Yezato/DATACOMM/assets/95903200/21323c8b-528b-462f-8f2d-c59163b086ed)

Screenshot of the ulimit -a command and its output


A system administrator may need to change some of these values in either direction:
- To restrict capabilities so an individual user and/or process cannot exhaust system resources, such as memory, cpu time or the maximum number of processes on the system.
- To expand capabilities so a process does not run into resource limits; for example, a server handling many clients may find that the default of 1024 open files makes its work impossible to perform.

There are two kinds of limits:
- Hard
The maximum value, set only by the root user, that a user can raise the resource limit to.
```lua
ulimit -H -n
```
> 4096

- Soft
The current limiting value, which a user can modify, but cannot exceed the hard limit.
```lua
ulimit -S -n
```
> 1024

One can set any particular limit by doing:

> ulimit [options] [limit]

as in

> ulimit -n 1600

which would increase the maximum number of file descriptors to 1600.

Note that the changes only affect the current shell. To make changes that are effective for all logged-in users, you need to modify /etc/security/limits.conf, a very nicely self-documented file, and then reboot.

# Creating Processes
An average Linux system is always creating new processes. This is often called forking; the original parent process keeps running, while the new child process starts.

Often, rather than just a fork, one follows it with an exec, where the parent process terminates, and the child process inherits the process ID of the parent. The term fork and exec is used so often, people think of it sometimes as one word.

Older UNIX systems often used a program called spawn, which is similar in many ways to fork and exec, but differs in details. It is not part of the POSIX standard and is not a normal part of Linux.

To see how new processes may start, consider a web server that handles many clients. It may launch a new process every time a new connection is made with a client. On the other hand, it may simply start only a new thread as part of the same process; in Linux, there really is not much difference on a technical level between creating a full process or just a new thread, as each mechanism takes about the same time and uses roughly the same amount of resources.

As another example, the sshd daemon is started when the init process executes the sshd init script, which then is responsible for launching the sshd daemon. This daemon process listens for ssh requests from remote users.

When a request is received, sshd creates a new copy of itself to service the request. Each remote user gets their own copy of the sshd daemon running to service their remote login. The sshd process will start the login program to validate the remote user. If the authentication succeeds, the login process will fork off a shell (say bash) to interpret the user commands, and so on.

Internal kernel processes take care of maintenance work, such as making sure buffers get flushed out to disk, that the load on different CPUs is balanced evenly, that device drivers handle work that has been queued up for them to do, etc. These processes often run as long as the system is running, sleeping except when they have something to do.

External processes are processes which run in user space like normal applications, but which the kernel started. There are very few of these and they are usually short lived.

> Note systemd-based systems run a special process named kthreadd with pid=2 whose job is to adopt orphaned children, who will then show ppid=2.

# Creating Processes in a Command Shell
What happens when a user executes a command in a command shell interpreter, such as bash?

- A new process is created (forked from the user's login shell).
- A wait system call puts the parent shell process to sleep.
- The command is loaded onto the child process's space via the exec system call. In other words, the code for the command replaces the bash program in the child process's memory space.
- The command completes executing, and the child process dies via the exit system call.
- The parent shell is re-awakened by the death of the child process and proceeds to issue a new shell prompt.
- The parent shell then waits for the next command request from the user, at which time the cycle will be repeated.

If a command is issued for background processing (by adding an ampersand -&- at the end of the command line), the parent shell skips the wait request and is free to issue a new shell prompt immediately, allowing the background process to execute in parallel. Otherwise, for foreground requests, the shell waits until the child process has completed or is stopped via a signal.

Some shell commands (such as echo and kill) are built into the shell itself, and do not involve loading of program files. For these commands, neither a fork nor an exec is issued for the execution.

# Process States
Processes can be in one of several possible states. The scheduler manages all of the processes. The process state is reported by the process listing.

## Main Process States
### Running
The process is either currently executing on a CPU or CPU core or sitting in the run queue, eagerly awaiting a new time slice. It will resume running when the scheduler decides it is now deserving to occupy the CPU, or when another CPU becomes idle and the scheduler migrates the process to that CPU.

### Sleeping (i.e., Waiting)
The process is waiting on a request (usually I/O) that it has made and cannot proceed further until the request is completed. When the request is completed, the kernel will wake up the process and put it back on the run queue, and it will be given a time slice on a CPU when the scheduler decides to do so.

### Stopped
The process has been suspended. This state is commonly experienced when a programmer wants to examine the executing program's memory, CPU registers, flags, or other attributes. Once this is done, the process may be resumed. This is generally done when the process is being run under a debugger or the user hits Ctrl-Z.

### Zombie
The process enters this state when it terminates, and no other process (usually the parent) has inquired about its exit state; i.e., reaped it. Such a process is also called a defunct process. A zombie process has released all of its resources, except its exit state and its entry in the process table. If the parent of any process dies, the process is adopted by init (PID = 1) or kthreadd (PID = 2).

# Execution Modes
At any given time, a process (or any particular thread of a multi-threaded process) may be executing in either user mode or system mode, which is usually called kernel mode by kernel developers.

What instructions can be executed depends on the mode and is enforced at the hardware, not software, level.

The mode is not a state of the system; it is a state of the processor, as in a multi-core or multi-CPU system each unit can be in its own individual state.

In Intel parlance, user mode is also termed Ring 3, and system mode is termed Ring 0.

# User Mode
Except when executing a system call, processes execute in user mode, where they have lesser privileges than in the kernel mode.

When a process is started, it is isolated in its own user space to protect it from other processes. This promotes security and creates greater stability. This is sometimes called process resource isolation.

Each process executing in user mode has its own memory space, parts of which may be shared with other processes; except for the shared memory segments, a user process is not able to read or write into or from the memory space of any other process.

Even a process run by the root user or as a setuid program runs in user mode, except when jumping into a system call, and has only limited ability to access hardware.
![image](https://github.com/Yezato/DATACOMM/assets/95903200/c8a95882-8d45-4b40-ae48-871af09572b6)

System Cal

# System (Kernel) Mode
In kernel (system) mode, the CPU has full access to all hardware on the system, including peripherals, memory, disks, etc. If an application needs access to these resources, it must issue a system call, which causes a context switch from user mode to kernel mode. This procedure must be followed when reading and writing from files, creating a new process, etc.

Application code never runs in kernel mode, only the system call itself which is kernel code. When the system call is complete, a return value is produced and the process returns to user mode with the inverse context switch.

There are other times when the system is in kernel mode that have nothing to do with processes, such as when handling hardware interrupts or running the scheduling routines and other management tasks for the system.

Daemons
A daemon process is a background process whose sole purpose is to provide some specific service to users of the system. Here are some more information about daemons:

- They can be quite efficient because they only operate when needed.
- Many daemons are started at boot time.
- Daemon names often (but not always) end with d, e.g. httpd and systemd-udevd.
- Daemons may respond to external events (systemd-udevd) or elapsed time (crond).
- Daemons generally have no controlling terminal and no standard input/output devices.
- Daemons sometimes provide better security control.
- Some examples include xinetd, httpd, lpd, and vsftpd.


# Using nice to Set Priorities
Process priority can be controlled through the nice and renice commands. Since the early days of UNIX, the idea has been that a nice process lowers its priority to yield to others. Thus, the higher the niceness is, the lower the priority.

The niceness value can range from -20 (the highest priority) to +19 (the lowest priority). The normal way to run nice is as in:
```lua
nice -n 5 command [ARGS]
```
which would set the niceness by 5. This is equivalent to doing:
```lua
nice -5 command [ARGS]
```
If you do not give a nice value, the default is to increase the niceness by 10. If you give no arguments at all, you report your current niceness. So, for example:
```lua
nice
```
> 0
```lua
nice cat &
```
> [1] 24908
```lua
ps -l
```
```lua
F S  UID    PID    PPID  C  PRI NI ADDR SZ WCHAN    TTY     TIME  CMD
0 S  500   4670    4603  0  80   0 - 16618 wait   pts/0 00:00:00 bash
0 S  500  24855    4670  0  80   0 - 16560 wait   pts/0 00:00:00 bash
0 T  500  24908   24855  0  90  10 - 14738 signal pts/0 00:00:00 cat
0 R  500  24909   24855  0  80   0 - 15887 -      pts/0 00:00:00 ps
```
> Note that increasing the niceness of a process does not mean it won't run; it may even get all the CPU time if there is nothing else with which to compete.

If you supply such a large increment or decrement that you try to step outside the -20 to 19 range, the increment value will be truncated.

# Modifying the Nice Value
renice is used to raise or lower the nice value of an already running process. It basically lets you change the nice value on the fly.
```lua
renice --help
```
```lua
Usage:
renice [-n] <priority> [-p|--pid] <pid>...
renice [-n] <priority> -g|--pgrp <pgid>...
renice [-n] <priority> -u|--user <user>...
Raise the nice value of pid 20003 to 5 :
```
```
$ renice +5 -p 20003
```
By default, only a superuser can decrease the niceness; i.e., increase the priority. However, it is possible to give normal users the ability to decrease their niceness within a predetermined range, by editing /etc/security/limits.conf.

After a non-privileged user has increased the nice value, only a superuser can lower it back. More than one process can be done at the same time, and there are some other options, so see man renice.

# Static and Shared Libraries
Programs are built using libraries of code, developed for multiple purposes and used and reused in many contexts.

Two types of libraries are :

- Static

The code for the library functions is inserted in the program at compile time, and does not change thereafter, even if the library is updated.

- Shared

The code for the library functions is loaded into the program at run time, and if the library is changed later, the running program runs with the new library modifications.

Using shared libraries is more efficient because they can be used by many applications at once; memory usage, executable sizes, and application load time are reduced.

Shared Libraries are also called Dynamic Link Libraries (DLLs).
```lua
c8:/usr/lib64>ls -l libpthread.*

-rwxr-xr-x 1 root root 320504 Jan 7 11:20 libpthread-2.28.so
-rw-r--r-- 1 root root 993146 Jan 7 11:21 libpthread.a
lrwxrwxrwx 1 root root     27 Jan 7 11:08 libpthread.so -> ../../lib64/libpthread.so.0
lrwxrwxrwx 1 root root     18 Jan 7 11:09 libpthread.so.0 -> libpthread-2.28.so
```
