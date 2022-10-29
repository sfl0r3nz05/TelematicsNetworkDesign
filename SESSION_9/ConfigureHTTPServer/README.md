# Configure HTTP/HTTPS server on Cisco Router

- [Configure HTTP/HTTPS server on Cisco Router](#configure-httphttps-server-on-cisco-router)
  - [Practice objectives](#practice-objectives)
  - [Network Scenario](#network-scenario)
  - [Import Appliances](#import-appliances)
  - [Configure HTTP AND HTTPS Server on the Router](#configure-http-and-https-server-on-the-router)
    - [Verify vty from Kali Linux VM](#verify-vty-from-kali-linux-vm)
  - [Security Tests](#security-tests)
  - [Access Router page through HTTPS from Kali Linux VM](#access-router-page-through-https-from-kali-linux-vm)

## Practice objectives

- Create a design of the network.
- Configure the network which consist of:
  - Configure hostname, domain name.
- Generate crypto keys
- Configure line vty, interfaces, DHCP Pool.
- Create the user.
- Configure the HTTP and HTTPS on cisco router.
- Verify the accounts by accessing through telnet and SSH.
- Access cisco router through browser.

## Network Scenario

This is a very simple network scenario which consists of one cisco router 7200 series and one virtual machine (running kali linux).

- Network: 10.0.0.0/8
- Router IP: 10.0.0.1
- Linux IP: 10.0.0.2

## Import Appliances

- Import [Kali Linux Appliance](./appliances/kali-linux.gns3a)
- Import [Cisco 7200 Router](./appliances/cisco-7200.gns3a)

## Configure HTTP AND HTTPS Server on the Router

- Configure HOSTNAME 

```console
R1(config)#hostname router1
```

- Configure DOMAIN NAME 

```console
router1(config)#ip domain-name hackingdna.com
```

- Generate CRYPTO KEYS 

```console
router1(config)#crypto key generate rsa general-keys modulus 1024
```

- Configure LINE VTY 

```console
router1#config t
router1(config)#line vty 0 4
router1(config-line)#login local
router1(config-line)#end
router1#
```

- Configure INTERFACE 

```console
router1#config t
router1(config)#int f0/0
router1(config-if)#ip add 10.0.0.1 255.0.0.0
router1(config-if)#no shut
router1(config-if)#exit
router1(config)#
```

- Configure DHCP POOL 

```console
router1(config)#ip dhcp pool drt
router1(dhcp-config)#network 10.0.0.0
router1(dhcp-config)#dns-server 10.0.0.1
router1(dhcp-config)#default-router 10.0.0.1
router1(dhcp-config)#exit
router1(config)#end
router1#
```

- Configure HTTP SERVER 

```console
router1(config)#ip http server
router1(config)#ip http secure-server
router1(config)#ip http authentication local
```

- Create USER

```console
router1(config)#username vivek privilege 15 password 0 12345
router1(config)#logging buffered 51200 warning
router1(config)#
```

### Verify vty from Kali Linux VM

- Verifying SSH

```console
ssh -l vivek 10.0.0.1
```

- Access Telnet

```console
telnet 10.0.0.1
```

## Security Tests

- Performing enumeration of HTTP/HTTPS from Kali Linux VM

```console
nmap -ns 10.0.0.1
```

- Scan HTTP/HTTPS services from Kali Linux VM

  - Find out what information you will get when you perform a version scan on port 80 and 443

```console
nmap -sV 10.0.0.1 -p80,443
```

- Scan SSL services from Kali Linux VM

  - Now we know that ssl is running on port 443. Lets confirm it which version of ssl is running on this cisco router.
  - We can make this work easier by using sslscan. You can find this tool easily on kali linux or simply you can download from the repositories. 

```console
sslscan 10.0.0.1
```

## Access Router page through HTTPS from Kali Linux VM

- To access this page, open your web browser and enter the router ip address.
- As you enter the router ip, it shows some exception. Click on add exception to move forward.
- To access the router, we need to enter the **username:** `vivek` and **password:** `12345`.