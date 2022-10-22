# Controller installation

- [Controller installation](#controller-installation)
  - [Prerequisitos](#prerequisitos)
  - [Desplegar OpenDayLight](#desplegar-opendaylight)
  - [OpenFlow App](#openflow-app)

Esta sección establece los procedimientos para descargar y configurar el controlador.

## Prerequisitos

- Tener la instancia `t2.medium` (*SDN-Controller*) desplegada
- Conexión a dicha instancia vía `ssh`.
- [Instalar Docker sobre Ubuntu](https://docs.docker.com/engine/install/ubuntu/).

## Desplegar OpenDayLight

> *Note:* The OpenDaylight Project is a collaborative open-source project hosted by the Linux Foundation. The project serves as a platform for software-defined networking for open, centralized, computer network device monitoring.

1. Lanzar el contenedor:

    ```console
     docker run -d -p 6633:6633 -p 8101:8101 -p 8181:8181 --name=opendaylight stephanfuhrmannionos/opendaylight:0.8.4
    ```

2. Conectarse al contenedor:

    ```console
    docker exec -ti opendaylight bin/client
    ```

   - Output:
  
     ```console
     ubuntu@ip-172-31-95-13:~/karaf-0.8.4$ ./bin/karaf
     Apache Karaf starting up. Press Enter to open the shell now...
     100% [========================================================================]
     Karaf started in 3s. Bundle stats: 54 active, 55 total

          ________                       ________                .__  .__       .__     __
          \_____  \ ______   ____   ____ \______ \ _____  ___.__.|  | |__| ____ |  |___/  |_
           /   |   \\____ \_/ __ \ /    \ |    |  \\__  \<   |  ||  | |  |/ ___\|  |  \   __\
          /    |    \  |_> >  ___/|   |  \|    `   \/ __ \\___  ||  |_|  / /_/  >   Y  \  |
          \_______  /   __/ \___  >___|  /_______  (____  / ____||____/__\___  /|___|  /__|
                  \/|__|        \/     \/        \/     \/\/            /_____/      \/


     Hit '<tab>' for a list of available commands
     and '[cmd] --help' for help on a specific command.
     Hit '<ctrl-d>' or type 'system:shutdown' or 'logout' to shutdown OpenDaylight.

     opendaylight-user@root> 
     ```

3. Install Karaf features:

     - To install a feature, use the following command, where feature1 is the feature name listed in the table below:

          ```console
          feature:install <feature1>
          ```

     - You can install multiple features using the following command:

          ```console
          feature:install <feature1> <feature2> ... <featureN-name>
          ```

     1. odl-l2switch-switch
     2. odl-dlux-core
     3. odl-dluxapps-yangutils
     4. features-dlux
     5. odl-dluxapps-applications
     6. odl-dluxapps-yangvisualizer
     7. odl-dluxapps-topology
     8. odl-dluxapps-nodes

          ```console
          feature:install odl-l2switch-switch odl-dlux-core odl-dluxapps-yangutils features-dlux odl-dluxapps-applications odl-dluxapps-yangvisualizer odl-dluxapps-topology odl-dluxapps-nodes
          ```  

4. Para verificar la lista de las features:

    ```console
    feature:list
    ```

5. `OpenDaylight` exposed TCP ports:
   - `6633` Openflow,
   - `8101` Karaf CLI via SSH (see below),
   - `8181` RESTCONF / HTTP

6. A través del navegador, y usando la IP pública de la instancia (e.g.: `http://35.174.155.88:8181/index.html#/login`) podremos ver la interfaz web del controller.

    1. Pero, en primer lugar, hay que introducir las credenciales que por defecto son:

          ```console
          Username: admin
          Password: admin
          ```

          ![image](./img/OpenDaylight.png)

## OpenFlow App

- El siguiente paso será la instalación del OpenFlow Manager y su posterior conexión con ODL.

1. Instala NodeJS:

     ```console
     sudo apt-get install -y npm
     sudo apt-get install -y nodejs
     ```

2. Se clona el repositorio de GitHub:

     ```console
     git clone https://github.com/CiscoDevNet/OpenDaylight-Openflow-App
     ```

3. Se edita el fichero ubicado en ofm/src/common/config y cambiarle el campo `baseURL`. 
   1. Tal y como se puede observar en la siguiente figura, se le añade  después de `http` la `IP_publica` de la máquina donde tenemos descargado ODL, con lo cual estaremos apuntando hacia ODL y podremos hacer la conexión con él, con el objetivo de obtener la topología y de que el OFM pueda hacer el control sobre el tráfico

     ![image](https://user-images.githubusercontent.com/98832318/192136644-6594b676-d92a-4856-8df8-870812f2ccde.png)

4. Se ejecutará el comando grunt.

     ```console
     cd OpenFlowApp
     grunt
     ```

5. Ejecutará el programa y abrirá una conexión en localhost:9000 donde podremos ver algo como lo de la siguiente figura.

     ![image](https://user-images.githubusercontent.com/98832318/192136666-20fab8e6-651a-4d88-b4d8-2cfd1fe458e8.png)
