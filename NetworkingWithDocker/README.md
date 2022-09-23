# Networking with Docker

- [Networking with Docker](#networking-with-docker)
  - [Docker Networking Types](#docker-networking-types)
    - [Host Networking](#host-networking)
    - [Bridge Networking](#bridge-networking)

## Docker Networking Types

When a Docker container launches, the Docker engine assigns it a network interface with an IP address, a default gateway, and other components, such as a routing table and DNS services. By default, all addresses come from the same pool, and all containers on the same host can communicate with one another. We can change this by defining the network to which the container should connect, either by creating a custom user-defined network or by using a network provider plugin.

The network providers are pluggable using drivers. We connect a Docker container to a particular network by using the `--net` switch when launching it.

- The following command launches a container from the busybox image and joins it to the host network. This container prints its IP address and then exits.

    ```console
    docker run --rm --net=host busybox ip addr
    ```

- Command output:

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

Docker offers five network types, each with a different capacity for communication with other network entities:

- **Host Networking**: The container shares the same IP address and network namespace as that of the host. Services running inside of this container have the same network capabilities as services running directly on the host.

- **Bridge Networking**: The container runs in a private network internal to the host. Communication is open to other containers in the same network. Communication with services outside of the host goes through network address translation (NAT) before exiting the host. (This is the default mode of networking when the --net option isn't specified).

- **Custom bridge network**: This is the same as Bridge Networking but uses a bridge explicitly created for this (and other) containers. An example of how to use this would be a container that runs on an exclusive "database" bridge network. Another container can have an interface on the default bridge and the database bridge, enabling it to communicate with
both networks.

- **Container-defined Networking**: A container can share the address and network configuration of another container. This type enables process isolation between containers, where each container runs one service but where services can still communicate with one another on the localhost address.

**No networking**: This option disables all networking for the container.

### Host Networking

The host mode of networking allows the Docker container to share the same IP address as that of the host and disables the network isolation otherwise provided by network namespaces. The container’s network stack is mapped directly to the host’s network stack. All interfaces and addresses on the host are visible within the container, and all communication possible to or from the host is possible to or from the container.

<img src="img/eth0.png" alt="drawing" width="300"/>

- If you run the command *ip addr* on a host (or *ifconfig -a* if your host doesn’t have the ip command available), you will see information about the network interfaces.

    ```console
    ip addr
    ```

- Command output:

    ```console
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
           valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:c8:28:10:4f:a9 brd ff:ff:ff:ff:ff:ff
        inet 172.31.28.46/20 brd 172.31.31.255 scope global dynamic eth0
           valid_lft 3376sec preferred_lft 3376sec
        inet6 fe80::8c8:28ff:fe10:4fa9/64 scope link 
           valid_lft forever preferred_lft forever
    3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
        link/ether 52:54:00:50:2c:a1 brd ff:ff:ff:ff:ff:ff
        inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
           valid_lft forever preferred_lft forever
    4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 state DOWN group default qlen 1000
        link/ether 52:54:00:50:2c:a1 brd ff:ff:ff:ff:ff:ff
    5: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
        link/ether 02:42:67:96:74:bc brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
           valid_lft forever preferred_lft forever
    ```

- If you run the same command from a container using host networking, you will see the same information.

    ```console
    docker run -it --rm --net=host busybox ip addr
    ```

- Command output:

    ```console
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
            valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
            valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq qlen 1000
        link/ether 0a:c8:28:10:4f:a9 brd ff:ff:ff:ff:ff:ff
        inet 172.31.28.46/20 brd 172.31.31.255 scope global dynamic eth0
           valid_lft 2149sec preferred_lft 2149sec
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

### Bridge Networking

In a standard Docker installation, the Docker daemon creates a bridge on the host with the name of *docker0*. When a container launches, Docker then creates a virtual ethernet device for it. This device appears within the container as *eth0* and on the host with a name like *vethxxx* where *xxx* is a unique identifier for the interface. The *vethxxx* interface is added to the *docker0* bridge, and this enables communication with other containers on the same host that also use the default bridge.

To demonstrate using the default bridge, run the following command on a host with Docker installed. Since we are not specifying the network - the container will connect to the default bridge when it launches.

<img src="img/net.png" alt="drawing" width="300"/>

- Run the **ip addr** and **ip route** commands inside of the container. You will see the IP address of the container with the *eth0* interface:

    ```console
    docker run -it --rm busybox /bin/sh
    ```

- ip addr command output:

    ```console
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
    6: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue 
        link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
           valid_lft forever preferred_lft forever
    ```

- ip route command output:

    ```console
    default via 172.17.0.1 dev eth0 
    172.17.0.0/16 dev eth0 scope link  src 172.17.0.2
    ```