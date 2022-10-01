# Cgroups Demo

**cgroups** (abbreviated from *control groups*) is a Linux kernel feature that limits, accounts for, and isolates the resource usage (CPU, memory, disk I/O, network, etc.) of a collection of processes. The *control groups* functionality was merged into the Linux kernel mainline in kernel version *2.6.24*, which was released in January 2008.

Groups are materialized in the pseudo-filesystem mounted in `/sys/fs/cgroup` and are created by mkdir in the abovementioned pseudo-filesystem.

```console
ls -la /sys/fs/cgroup/
```

*Output*:

```console
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

## Example of Manual cgroup use

1. Create a script file *test.sh* with the next code:

```console
#!/bin/sh
while [ 1 ]; do
       echo "hello world"
       sleep 60
done
```

2. Create a new object *foo* manually inside of the *cgroup* hierarchy.

```console
sudo mkdir /sys/fs/cgroup/memory/foo
```

3. Echo 50 MBytes into the *limit in bytes* option sets the maximum limit of memory for anything that is in the fod group.

```console
echo 50000000 | sudo tee /sys/fs/cgroup/memory/foo/memory.limit_in_bytes 
```

4. The exact value that is set can be determined using the *cat* command. It should be noted that it is not exactly *50000000* because the architecture is a multiple of *4096*.

```console
sudo cat /sys/fs/cgroup/memory/foo/memory.limit_in_bytes 
```

5. Recover de Process ID because that is the major mechanism that we use to add things to a *cgroup*

```console
sh ./test.sh &
```

6. The PID (e.g., `15606`) above is associated with the *foo* object:

```console
echo 15606 | sudo tee /sys/fs/cgroup/memory/foo/cgroup.procs 
```

7. The *ps* command is used to find out the *cgroups* associated with the previous process (e.g., `15606`):

```console
ps -o cgroup 15606
```

*Output*:

```console
CGROUP
11:blkio:/user.slice,9:pids:/user.slice/user-1000.slice/session-1.scope,8:cpu,cpuacct:/user.slice,7:memory:/foo,5:devices:/use
```

8. Process memory consumption can be checked:

```console
sudo cat /sys/fs/cgroup/memory/foo/memory.usage_in_bytes
```

Ahora es posible controlar, o monitorizar el proceso