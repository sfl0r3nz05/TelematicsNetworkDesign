# Configure HTTP/HTTPS server on Cisco Router

- [Configure HTTP/HTTPS server on Cisco Router](#configure-httphttps-server-on-cisco-router)
  - [Practice objectives](#practice-objectives)
  - [Network Scenario](#network-scenario)
  - [Import Appliances](#import-appliances)
  - [Configure HTTP AND HTTPS Server](#configure-http-and-https-server)

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

## Configure HTTP AND HTTPS Server

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
router1(config)#ip dhcp pool networkingdna
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

- Verifying SSH