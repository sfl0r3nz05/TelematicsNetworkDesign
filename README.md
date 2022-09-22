# GNS3 server configuration on an EC2 instance

The purpose of this repository is to deploy a GNS3 server on an EC2 instance of AWS. The following figure shows the basic architecture of the deployment to be implemented:

<img src="img/client-server.png" alt="drawing" width="300"/>

To perform the right deployment follow each of the following steps:

1. We assume that we have an EC2 instance deployed with Ubuntu Server 20.04 as OS:
    ![](img/ec2.png)

2. Se aplican las siguientes reglas para abrir las conexiones entrantes de:

    | Type        | Port      | Description                                                     |
    |-------------|-----------|-----------------------------------------------------------------|
    | SSH         | 22        | Instance management                                             |
    | Custom TCP  | 3080      | GNS3 client-server connection                                   |
    | Custom TCP  | 5000-5030 | Telnet connection for the device management created within GNS3 |

    - Example of applied rules
    
    ![](img/ports.png)

3. 

## To Do

1. Enable secure connection between the client and server via TLS