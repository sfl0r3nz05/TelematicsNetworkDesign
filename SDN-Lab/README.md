# Laboratorio de SDN

- [Laboratorio de SDN](#laboratorio-de-sdn)
  - [Prerequisitos](#prerequisitos)
  - [Despliegue del controlador SDN](#despliegue-del-controlador-sdn)
  - [Despliegue de la red sobre GNS3](#despliegue-de-la-red-sobre-gns3)

## Prerequisitos

1. Crear 2 instancias *EC2* en *AWS* una `t2.medium` y una `t2.large`:
   1. Renombrar la instancia `t2.medium` como *SDN-Controller*.
   2. Renombrar la instancia `t2.large` como *GNS3-Server*.
2. Desplegar el servidor GNS3 en la instancia `t2.large`(*GNS3-Server*) siguiendo la siguiente [documentación](../GNS3ServerDeployment/README.md).

## Despliegue del controlador SDN

1. Desplegar el *Controlador SDN* en la instancia `t2.medium` siguiendo la siguiente [documentación](./controller/controller.md).
  
## Despliegue de la red sobre GNS3

1. Crear un nuevo proyecto en `File` y `New blank project` para empezar a desplegar la red.

     
  1. Red GNS3. [Red GNS3](./RedGNS3/REDGNS3.md)
  
      3.1 OpenVSwitch + Conexión controlador
      
      3.2 Expansión de red + establecimiento OSPF
      
      3.3 Introducción en la red de router atacante

