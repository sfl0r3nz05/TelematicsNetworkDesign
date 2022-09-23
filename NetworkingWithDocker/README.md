# Networking with Docker

## Docker Networking Types

When a Docker container launches, the Docker engine assigns it a network interface with an IP address, a default gateway, and other components, such as a routing table and DNS services. By default, all addresses come from the same pool, and all containers on the same host can communicate with one another. We can change this by defining the network to which the container should connect, either by creating a custom user-defined network or by using a network provider plugin.

The network providers are pluggable using drivers. We connect a Docker container to a particular network by using the `--net` switch when launching it.

- The following command launches a container from the busybox image and joins it to the host network. This container prints its IP address and then exits.

    ```console
    docker run --rm --net=host busybox ip addr
    ```

- Output:

    ```console
    Unable to find image 'busybox:latest' locally
    latest: Pulling from library/busybox
    729ce43e2c91: Pull complete 
    Digest: sha256:ad9bd57a3a57cc95515c537b89aaa69d83a6df54c4050fcf2b41ad367bec0cd5
    Status: Downloaded newer image for busybox:latest
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
            valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq qlen 1000
        link/ether 0a:c8:28:10:4f:a9 brd ff:ff:ff:ff:ff:ff
        inet 172.31.28.46/20 brd 172.31.31.255 scope global dynamic eth0
            valid_lft 2316sec preferred_lft 2316sec
        inet6 fe80::8c8:28ff:fe10:4fa9/64 scope link 
            valid_lft forever preferred_lft forever
    3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue qlen 1000
        link/ether 52:54:00:50:2c:a1 brd ff:ff:ff:ff:ff:ff
        inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
           valid_lft forever preferred_lft forever
    4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 qlen 1000
        link/ether 52:54:00:50:2c:a1 brd ff:ff:ff:ff:ff:ff
    5: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue 
        link/ether 02:42:67:96:74:bc brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
           valid_lft forever preferred_lft forever
    ```

