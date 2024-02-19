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
