# GNS3 server configuration on an EC2 instance

- [GNS3 server configuration on an EC2 instance](#gns3-server-configuration-on-an-ec2-instance)
  - [Deploy GNS3 Server](#deploy-gns3-server)
  - [Connect to GNS3 Server from GNS3 client](#connect-to-gns3-server-from-gns3-client)
  - [To Do](#to-do)

The purpose of this repository is to deploy a GNS3 server on an EC2 instance of AWS. The following figure shows the basic architecture of the deployment to be implemented:

<img src="img/client-server.png" alt="drawing" width="300"/>

## Deploy GNS3 Server

To perform the right deployment follow each of the following steps:

1. We assume that we have an EC2 instance deployed with Ubuntu Server 20.04 as OS:

    ![](img/ec2.png)

2. The following rules apply for opening incoming connections from:

    | Type        | Port      | Description                                                     |
    |-------------|-----------|-----------------------------------------------------------------|
    | SSH         | 22        | Instance management                                             |
    | Custom TCP  | 3080      | GNS3 client-server connection                                   |
    | Custom TCP  | 5000-5030 | Telnet connection for the device management created within GNS3 |

    - Example of applied rules

    ![](img/ports.png)

1. GNS3 server installation:

    ```console
    sudo apt update
    sudo add-apt-repository ppa:gns3/ppa
    sudo apt install gns3-server gns3-gui
    ```

    - It is recommended accept both options as part of the installation process:

    | Users able to run GNS3| Users able to capture packages |
    |-------------|-----------|
    | ![](img/install-gns3-ubuntu-01.png) | ![](img/install-gns3-ubuntu-02.png) |


2. IOU (IOS over Unix) is an internal Cisco tool for simulating the ASICs in Cisco Switches. This enables you to play with Layer 2 switching in the Labs:

    ```console
    sudo dpkg --add-architecture i386
    sudo apt update
    sudo apt install gns3-iou
    ```

3. Install docker on ubuntu:

-  Step 1: Update System

    ```console
    sudo apt -y update
    ```
- Step 2: Install basic dependencies
    ```console
    sudo apt -y install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    ```

- Step 3: [Install Docker CE on Ubuntu 22.04|20.04|18.04](https://docs.docker.com/engine/install/ubuntu/).

  - Add your user account to docker group.

    ```console
    sudo usermod -aG docker $USER
    newgrp docker
    ```

  - Verify installation by checking Docker version:

    ```console
    docker version
    ```

  - After installing Docker and IOU, add your user to the following groups:

    ```console
    for i in ubridge libvirt kvm wireshark docker; do
        sudo usermod -aG $i $USER
    done
    ```

1. Run GNS3 server:

    ```console
    gns3server
    ```

   - It is now possible to access the GNS3 service:

    ```console
    http://aws_instance_ip:3080
    ```

   - Verify the service:

    <img src="img/front.png" alt="drawing" width="700"/>

## Connect to GNS3 Server from GNS3 client

1. Now, it is time to connect to the server via the GNS3 client and configure the preferences properly.

    <img src="img/server_preferences.png" alt="drawing" width="700"/>

   - Default username/password:

    ```console
    username: gns3
    password: gns3
    ```

2. Verify that the server has connected properly.

<img src="img/output.png" alt="drawing" width="300"/>

## To Do

1. Enable secure connection between the client and server via TLS
2. Document how to apply rules on aws to open port
3. Document how to add a new router using binary files
4. Documenting how to bypass a network of restrictions by tunneling

```console
 ssh -N -i .\openstack.pem -L 10.62.50.58:3080:172.31.88.200:3080 ubuntu@54.89.220.232
```
