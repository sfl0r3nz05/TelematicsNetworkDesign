# Laboratorio de SDN

## Prerequisitos

1. [Desplegar el servidor GNS3](../GNS3ServerDeployment/README.md)

## Despliegue de la Red SDN

1. Obtención de programas y preconfiguraciones. [Getting Started](./GETTINGSTARTED.md)

2. Controlador SDN. [Controlador](./Controlador/CONTROLADOR.md)
  
      2.1 OpenDayLight
      
      2.2 OpenFlowApp
      
  3. Red GNS3. [Red GNS3](./RedGNS3/REDGNS3.md)
  
      3.1 OpenVSwitch + Conexión controlador
      
      3.2 Expansión de red + establecimiento OSPF
      
      3.3 Introducción en la red de router atacante
      
  4. Ataque. [Ataque](./Ataque/ATAQUE.md)
  
      4.1 Adaptación de ataque
      
      4.2 Análisis (WireShark) 

## Detección y contramedidas
 
A partir de aquí será trabajo del lector continuar el proyecto en la dirección de los objetivos previamente expuestos. Al finalizar el apartado 3 (Red GNS3) ya se tiene puesta en marcha una pequeña red SDN. Usando las herramientas de visualización del apartado 2 (las interfaces gráficas de ODL y OFM) ys se puede empezar a investigar como sacar el máximo rendimiento a la figura del controlador, poniendo especial atención al apartado de la seguridad.

Una vez terminado el laboratorio virtual, el paso siguiente será desarrollar la red. Se deberá inteoducir complejidad al sistema (añadiendo más routers, diferenciar areas OSPF...) para que refleje con mayor fidelidad una situación real. Después habrá que continuar con los objetivos establecidos de desarrollar técnicas de detección y mitigación contra este tipo de ataques.

En la sección 7 de la memoria se describen brevemente las líneas de trabajos futuras que se podrían llevar a cabo.

