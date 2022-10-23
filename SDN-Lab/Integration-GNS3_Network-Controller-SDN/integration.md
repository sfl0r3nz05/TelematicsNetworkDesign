# GNS3-Network and SDN-Controller integration

## OpenVSwitch - SDN-Controller Connection

- Antes de realizar la conexión con el controlador, es recomendable fijar algunos parámetros de la configuración para el controlador del OVS. En este caso ha sido necesario fijar el protocolo y habilitar el flujo en br0.

```console
ovs-vsctl set bridge br0 protocols=OpenFlow13
ovs-vsctl set bridge br0 other_config:enable-flush=true
```

- Una vez establecidos estos parámetros se procede a conectar el OVS con el controlador. OpenDayLight ha de estar lanzado en el momento que se intente hacer la conexión. La conexión se hará a través del puerto 6633.

```console
ovs-vsctl set-controller br0 tcp:54.152.14.26:6633
```

- Los siguientes comandos son una colección de comandos útiles para comprobar si la conexión y configuración es correcta y/o comprobar las tablas de flujo establecidas por el controlador.

```console
ovs-vsctl list controller
ovs-vsctl list bridge br0
ovs-ofctl -O OpenFlow13 dump-flows br0
ovs-ofctl -O OpenFlow13 dump-ports br0
```

# Expansión de red
En este momento tenemos una red formada por un switch y dos routers (además de la nube para conectarse al controlador). El siguiente paso será expandir la red la red añadiendole más routers y dos nodos.

Vamos a añadir un router más a cada lado del switch, pero, más adelante veremos que uno de ellos requiere ser de otro tipo, asique, en este paso añadiremos un router y un nodo (un lado del switch). El router del otro lado veremos en el siguiente apartado como integrarlo, y el nodo se añadirá de igual manera.

## Router
Arrastraremos al proyecto un router del mismo modelo del que el otro. Del mismo modo que haciamos antes, configuraremos las interfaces del router. Pero antes habra que configurar una nueva interfaz en R1. Para ello

```
configure terminal
interface eth1
ip address 10.1.2.1 255.255.255.0
no shutdown
end
```

Para el nuevo router, que llamaremo R1_2

```
configure terminal
interface eth0
ip address 10.1.2.2 255.255.255.0
no shutdown
exit
interface eth1
ip address 10.1.4.1 255.255.255.0
no shutdown
end
```

Las direcciones ip se pueden poner las que quieran, siempre que sean válidas, para hacerlas más entendibles para el lector.

![image](https://user-images.githubusercontent.com/98832318/192571156-b18068d0-b55c-431b-8e56-19109303690a.png)

## Host
El VPSC, disponible por defecto en GNS3, es el hosts que conectaremos al extremo de la red. La configuración a aplicar en este caso es simplemente agregar las IPs en las interfaces que se conectan. 

```
show ip
ip 10.1.5.2/8 10.1.5.1
dns 10.1.5.1
```
![image](https://user-images.githubusercontent.com/98832318/192570929-2aca6e6a-0d96-4ac2-b12b-dcc6782e6db8.png)


## Añadir protocolo OSPF
Para hacer que los routers operen bajo el protocolo habrá que configurarlos de la siguiente manera (todos igual).

```
configure terminal
router ospf 1
network 10.0.0.0 0.255.255.255 area 0
default-information originate
end
```

Se podrán comprobar los estados de las interfaces y las relaciones de vecindad establecidas con los siguientes comandos.

```
show ip interface brief
show ip ospf nei
```

# Introducción atacante
FRR habrá que descargarlo (disponible en Imagenes) y, posteriormente, configurarlo para integrarlo en la red, por una parte, y añadirle el programa de ataque por otra. 

Durante el proceso de importarlo a GNS3 habrá que elegir la versión de FRR que se desea implementar y descargar los archivos requeridos para ello, lo cual se puede hacer directamente desde GNS3.

Por otra parte, al agregarlo al proyecto se nos pedirá que hagamos ciertos cambios en la configuración del servidor GNS3. Estos cambios consisten en añadir la línea enable_kvm=False debajo de [Qemu] en el archivo gns3_server.conf de etc/gns3.
Ahora el dispositivo se podrá añadir al proyecto e integrarlo en la red. Para integrarlo habrá que asignar IPs a las interfaces que se deseen conectar, además de configurar el enrutamiento OSPF.
Para lo primero, en este caso se va a realizar la configuración para las interfaces eth1 y eth2. Se ha de señalar que la interfaz de los dispositivos Cisco y FRR son muy similares, lo que facilita en gran medida la labor del programador. Para configurar las interfaces (del mismo modo las dos):

```
configure terminal
interface eth0
ip address 10.1.1.2/24
no shutdown
end
```
Para lo segundo será necesario hacer

```
configure terminal
router ospf
network 10.0.0.0/8 area 0
default-information originate
end
```

## Preparación Sistema Operativo
Con el dispositivo FRR configurado de esta manera estará listo para ser integrado en la red, establecer relaciones de vecindad con los rúters colindantes y enrutar paquetes. Lo que quedará será preparar el sistema operativo para la integración del programa de ataque.
Son varios los requisitos previos que debe cumplir el sistema operativo:
  •	Máquina Linux
  •	Python 2.7
  •	Scapy
  
Antes de todo hay que dar acceso a internet a la máquina. Para ello usando la herramienta cloud de GNS3 y usando la interfaz gráfica virbr0, tal y como se observa en la siguiente figura, se procede a configurar la interfaz eth2 de la máquina.

![image](https://user-images.githubusercontent.com/98832318/192574194-0f29a794-36d4-49c0-b297-6556dcc7d17f.png)

En primer lugar, hay que asignar una IP a la interfaz eth2, y por otra hay que generar un gateway por defecto para dar acceso a internet vía virbr0. Esto se hará haciendo uso de los siguientes comandos

```
ifconfig eth0 192.168.122.21
route add defautl gw 192.168.122.1 eth0
echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null
```
Una vez hecho esto, se procederá a instalar Python. Como siempre, es recomendable actualizar los repositorios. Después, se procederá a la instalación Python. 

```
apk update && apk upgrade –available
apk add --update python2
```
Como apunte, cabe destacar que la distribución de este dispositivo con FRR es una Alpine. Por lo tanto, ciertos comandos típicos, como apt, correapondientes a otras distribuciones deberán sustituirse por apk. Este proceso se realizará en tres pasos. Primero, se debe instalar el sistema de control de versiones Git:

```
apk add --update git
```

En segundo lugar, consulta un clon del repositorio de Scapy. Y para finalizar, instalar Scapy.

```
git clone https://github.com/secdev/scapy.git
cd scapy
python setup.py install
```
Por último, quedará instalar la librería libpcap necesaria para capturar paquetes. Para obtenerla se hará

```
apk add --upgrade libpcap-dev
```
