# Namespaces Demonstration

- [Namespaces Demonstration](#namespaces-demonstration)
  - [Types of Namespaces](#types-of-namespaces)
  - [An Example of PID Namespaces](#an-example-of-pid-namespaces)

*Namespaces* are a feature of the Linux kernel that partitions kernel resources such that one set of processes sees one set of resources while another set of processes sees a different set of resources. In other words, the key feature of *namespaces* is that they isolate processes from each other. On a server where you are running many different services, isolating each service and its associated processes from other services means that there is a smaller blast radius for changes, as well as a smaller footprint for security‑related concerns. Mostly though, isolating services meets the architectural style of microservices as described.

## Types of Namespaces

Within the Linux kernel, there are different types of namespaces. Each namespace has its own unique properties:

A **user namespace** has its own set of user IDs and group IDs for assignment to processes. In particular, this means that a process can have root privilege within its user namespace without having it in other user namespaces.

A **process ID (PID) namespace** assigns a set of PIDs to processes that are independent from the set of PIDs in other namespaces. The first process created in a new namespace has PID 1 and child processes are assigned subsequent PIDs. If a child process is created with its own PID namespace, it has PID 1 in that namespace as well as its PID in the parent process’ namespace.

A **network namespace** has an independent network stack: its own private routing table, set of IP addresses, socket listing, connection tracking table, firewall, and other network‑related resources.

A **mount namespace** has an independent list of mount points seen by the processes in the namespace. This means that you can mount and unmount filesystems in a mount namespace without affecting the host filesystem.

An **interprocess communication (IPC) namespace** has its own IPC resources, for example POSIX message queues.
A UNIX Time‑Sharing (UTS) namespace allows a single system to appear to have different host and domain names to different processes.

## An Example of PID Namespaces

Understanding PID namespaces will be a better way to understand container isolation.

Before start the demonstration it must be considered that we use 3 terminals named as: *Terminal 1*, *Terminal 2* and *Terminal 3*.

![3-terminals](./img/3-consoles.png)

1. **Terminal 1**: See all processes that are running for the system.

    ```console
    ps -ef
    ```

    *Command Output*:

    ```console
    UID          PID    PPID  C STIME TTY          TIME CMD
    root           1       0  0 09:44 ?        00:00:07 /sbin/init auto automatic-ubiq
    root           2       0  0 09:44 ?        00:00:00 [kthreadd]
    root           3       2  0 09:44 ?        00:00:00 [rcu_gp]
    ```

    There is a column dedicated to identify the process (PID).

2. **Terminal 1**: See all processes that are running for the system.
