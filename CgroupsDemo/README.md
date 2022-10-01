# Cgroups Demo

**cgroups** (abbreviated from *control groups*) is a Linux kernel feature that limits, accounts for, and isolates the resource usage (CPU, memory, disk I/O, network, etc.) of a collection of processes. The *control groups* functionality was merged into the Linux kernel mainline in kernel version *2.6.24*, which was released in January 2008.

Groups are materialized in the pseudo-filesystem mounted in `/sys/fs/cgroup` and are created by mkdir in the abovementioned pseudo-filesystem.

```console
ls -la /sys/fs/cgroup/
```

Output:

    ```
    total 0
    dr-xr-xr-x 11 root root 0 Oct  1 15:17 .
    drwxr-xr-x  8 root root 0 Oct  1 15:17 ..
    -r--r--r--  1 root root 0 Oct  1 15:17 cgroup.controllers
    -rw-r--r--  1 root root 0 Oct  1 15:18 cgroup.max.depth
    -rw-r--r--  1 root root 0 Oct  1 15:18 cgroup.max.descendants
    -rw-r--r--  1 root root 0 Oct  1 15:17 cgroup.procs
    -r--r--r--  1 root root 0 Oct  1 15:18 cgroup.stat
    -rw-r--r--  1 root root 0 Oct  1 15:17 cgroup.subtree_control
    -rw-r--r--  1 root root 0 Oct  1 15:18 cgroup.threads
    -rw-r--r--  1 root root 0 Oct  1 15:18 cpu.pressure
    -r--r--r--  1 root root 0 Oct  1 15:18 cpu.stat
    -r--r--r--  1 root root 0 Oct  1 15:18 cpuset.cpus.effective
    -r--r--r--  1 root root 0 Oct  1 15:18 cpuset.mems.effective
    drwxr-xr-x  2 root root 0 Oct  1 15:17 dev-hugepages.mount
    drwxr-xr-x  2 root root 0 Oct  1 15:17 dev-mqueue.mount
    drwxr-xr-x  2 root root 0 Oct  1 15:17 init.scope
    -rw-r--r--  1 root root 0 Oct  1 15:18 io.cost.model
    -rw-r--r--  1 root root 0 Oct  1 15:18 io.cost.qos
    -rw-r--r--  1 root root 0 Oct  1 15:18 io.pressure
    -rw-r--r--  1 root root 0 Oct  1 15:18 io.prio.class
    -r--r--r--  1 root root 0 Oct  1 15:18 io.stat
    -r--r--r--  1 root root 0 Oct  1 15:18 memory.numa_stat
    -rw-r--r--  1 root root 0 Oct  1 15:18 memory.pressure
    -r--r--r--  1 root root 0 Oct  1 15:18 memory.stat
    -r--r--r--  1 root root 0 Oct  1 15:18 misc.capacity
    drwxr-xr-x  2 root root 0 Oct  1 15:17 sys-fs-fuse-connections.mount
    drwxr-xr-x  2 root root 0 Oct  1 15:17 sys-kernel-config.mount
    drwxr-xr-x  2 root root 0 Oct  1 15:17 sys-kernel-debug.mount
    drwxr-xr-x  2 root root 0 Oct  1 15:17 sys-kernel-tracing.mount
    drwxr-xr-x 37 root root 0 Oct  1 15:23 system.slice
    drwxr-xr-x  3 root root 0 Oct  1 15:18 user.slice
    ```
