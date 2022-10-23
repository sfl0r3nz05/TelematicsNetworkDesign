# Laboratorio de SDN

- [Laboratorio de SDN](#laboratorio-de-sdn)
  - [Prerequisitos](#prerequisitos)
  - [Despliegue de la red sobre GNS3](#despliegue-de-la-red-sobre-gns3)
  - [Despliegue del controlador SDN](#despliegue-del-controlador-sdn)
  - [Integración](#integración)

## Prerequisitos

1. Crear 2 instancias *EC2* en *AWS* una `t2.medium` y una `t2.large`:
   1. Renombrar la instancia `t2.medium` como *SDN-Controller*.
   2. Renombrar la instancia `t2.large` como *GNS3-Server*.
2. Desplegar el servidor GNS3 en la instancia `t2.large`(*GNS3-Server*) siguiendo la siguiente [documentación](../GNS3ServerDeployment/README.md).

## Despliegue de la red sobre GNS3

1. Desplegar la Red correspondiente en la instancia `t2.large`(*GNS3-Server*) siguiendo la siguiente [documentación](./RedGNS3/REDGNS3.md)

## Despliegue del controlador SDN

1. Desplegar el *Controlador SDN* en la instancia `t2.medium` siguiendo la siguiente [documentación](./controller/controller.md).

## Integración
