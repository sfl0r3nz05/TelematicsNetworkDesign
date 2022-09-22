# GNS3 server configuration on an EC2 instance

The purpose of this repository is to deploy a GNS3 server on an EC2 instance of AWS. The following figure shows the basic architecture of the deployment to be implemented:

<img src="img/client-server.png" alt="drawing" width="300"/>

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

3. GNS3 server installation:

    ```console
    sudo add-apt-repository ppa:gns3/ppa
    sudo apt update
    sudo apt install gns3-server gns3-gui
    ```

    - It is recommended accept both options as part of the installation process:

    | |  |
    |-------------|-----------|
    | ![](img/install-gns3-ubuntu-01.png) | ![](img/install-gns3-ubuntu-02.png) |

    
4. IOU (IOS over Unix) is an internal Cisco tool for simulating the ASICs in Cisco Switches. This enables you to play with Layer 2 switching in the Labs:

    ```console
    sudo dpkg –add-architcture i386
    sudo apt install gns3-iou
    ```

## To Do

1. Enable secure connection between the client and server via TLS