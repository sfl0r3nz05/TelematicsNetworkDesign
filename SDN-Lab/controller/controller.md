# Controller installation

- [Controller installation](#controller-installation)
  - [Prerequisitos](#prerequisitos)
  - [Prerequisitos OpenDayLight](#prerequisitos-opendaylight)
  - [OpenFlow App](#openflow-app)

Esta sección establece los procedimientos para descargar y configurar el controlador.

## Prerequisitos

- Tener la instancia `t2.medium` (*SDN-Controller*) desplegada
- Conexión a dicha instancia vía `ssh`.

## Prerequisitos OpenDayLight

> *Note:* The OpenDaylight Project is a collaborative open-source project hosted by the Linux Foundation. The project serves as a platform for software-defined networking for open, centralized, computer network device monitoring.

1. Actualizar el Advanced Package Tool (APT):

    ```console
    sudo apt-get -y update
    sudo apt-get -y upgrade
    ```

2. Instalar unzip:

    ```console
    sudo apt-get -y install unzip
    ```

3. Instalar el entorno Java JRE para que ODL funcione correctamente:

    ```console
    sudo apt-get -y install openjdk-8-jre
    sudo update-alternatives --config java
    ```

4. Set the `JAVA_HOME`.

   1. Primero se comprueba el directorio donde se encuentra y, posterirmente, se le aplican los cambios correspondietes.

        ```console
        ls -l /etc/alternatives/java
        echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre' >> ~/.bashrc
        source ~/.bashrc
        ```

   2. Comprobación de que se ha realizado correctamente:

        ```console
        echo $JAVA_HOME
        ```

5. Una vez se haya establecido el entorno adecuado para el programa se procede con la instalación. Desde la página de ODL se escoge la versión de programa que se va a utilizar. En este caso se ha optado por la versión Beryllium 0.4.4.

   - Haciendo uso del comando curl y posteriormente, descomprimiendo el archivo obtenemos el programa.

      ```console
      curl -XGET -O https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/karaf/0.8.4/karaf-0.8.4.zip
      unzip karaf-0.8.4.zip
      ```

6. Lanzar el servicio:

    ```console
    cd karaf-0.8.4/
    ./bin/karaf
    ```

    - Una vez ejecutado el programa se debe observar en una aproxumación a lo que se muestra a continuación:

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

7. Será necesario instalarse las siguientes funciones desde la propia terminal `opendaylight-user@root>`:

   1. odl-restconf-all

        ```console
        feature:install odl-restconf-all
        ```

   2. odl-openflowplugin-all

        ```console
        feature:install odl-openflowplugin-all
        ```

   3. odl-l2switch-all

        ```console
        feature:install odl-l2switch-all
        ```

   4. odl-mdsal-all

        ```console
        feature:install odl-mdsal-all
        ```

   5. odl-yangtools-common

        ```console
        feature:install odl-yangtools-common
        ```

   6. odl-dlux-all

        ```console
        feature:install odl-dlux-all
        ```

8. Para verificar la lista de las features:

    ```console
    feature:list
    ```

9. Una vez instalado `OpenDaylight`, así como todas sus `funciones` correctamente podremos ver como se abren unos ciertos puertos como el `8081`.

10. A través del navegador, y usando la IP pública de la instancia (e.g.: http:// 35.174.155.88:8181/index.html#/login) podremos ver la interfaz web del controller.
    1. Pero, en primer lugar, hay que introducir las credenciales que por defecto son:

          ```console
          Username: admin
          Password: admin
          ```

          ![image](https://user-images.githubusercontent.com/98832318/192136488-7e166ea5-fba4-42c7-a765-0332f6d96499.png)

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
