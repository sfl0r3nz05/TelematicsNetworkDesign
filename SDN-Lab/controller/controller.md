# Introducción
En esta parte se va a explicar como proceder para descargar y configurar los programas que correspondinetes a la parte del controlador.

Estando activa la instancia 2, nos conectamos mediante VisualStudio Code, lanzamos un terminal y empezamos a descargar los programas.

# OpenDayLight
Empezando por el controlador OpenDayLight, se requerirán unos ciertos requisitos antes de la instalación del programa. 

```
sudo apt-get -y update
sudo apt-get -y upgrade
sudo apt-get -y install unzip
```

Hace falta instalar el entorno Java JRE para que ODL funcione correctamente. 
```
 sudo apt-get -y install openjdk-8-jre
 sudo update-alternatives --config java
```

También habrá que establecer el JAVA_HOME. Primero se comprueba el directorio donde se encuentra y, posterirmente, se le aplican los cambios correspondietes.

```
 ls -l /etc/alternatives/java
 echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre' >> ~/.bashrc
 source ~/.bashrc
 echo $JAVA_HOME (comprobación de que se ha realizado correctamente)
```

Una vez se haya establecido el entorno adecuado para el programa se procede con la instalación. Desde la página de ODL se escoge la versión de programa que se va a utilizar. En este caso se ha optado por la versión Beryllium 0.4.4. 

Haciendo uso del comando curl y posteriormente, descomprimiendo el archivo obtenemos el programa. 

```
curl -XGET -O https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/karaf/0.8.4/karaf-0.8.4.zip
unzip karaf-0.8.4.zip
```
Para correrlo

```
cd karaf-0.8.4/
./bin/karaf
```

Una vez ejecutado el programa se verá algo como lo que se puede observar en la siguiente figura
![image](https://user-images.githubusercontent.com/98832318/192136479-6ceabe3f-ecfd-40ab-9ae7-b1e2d9389a48.png)

Será necesario instalarse las siguientes funciones del programa:
  1. Odl-restconf-all
  2. Odl-openflowplugin-all
  3. Odl-l2switch-all
  3. Odl-mdsal-all
  4. Odl-yangtools-common
  5. Odl-dlux-all

Para descargarlas aplicar el siguiente comando
```
feature:install odl-restconf-all
```
Para ver la lista de las features

```
feature:list
```

Una vez instalado el programa con todas sus funciones correctamente podremos ver como se abren unos ciertos puertos. En la siguiente figura se ve un ejemplo de los puertos abiertos usando la herramienta nmap.

Si por un navegador accedemos a http:// 35.174.155.88:8181/index.html#/login (introducir ip pública de la instancia correspondiente al controlador) podremos ver lo siguiente. Pero, en primer lugar, hay que introducir las credenciales que por defecto son:
Username: admin
Password: admin

![image](https://user-images.githubusercontent.com/98832318/192136488-7e166ea5-fba4-42c7-a765-0332f6d96499.png)

# OpenFlow App

El siguiente paso será la instalación del OpenFlow Manager y su posterior conexión con ODL. Para esto será necesario descargarse un par de funciones previas que aseguran el funcionamiento correcto. Con este objetivo se han ejecutado los siguientes comandos.

```
sudo apt-get install -y npm
sudo apt-get install -y nodejs
```

A continuación, se clona el repositorio de GitHub

```
git clone https://github.com/CiscoDevNet/OpenDaylight-Openflow-App
```

Habrá que editar el fichero ubicado en ofm/src/common/config y cambiarle el campo baseURL. Tal y como se puede observar en la siguiente figura, se le añade  después de http:// la IP publica de la máquina donde tenemos descargado ODL. 

Haciendo esto estaremos apuntando hacia ODL y podremos hacer la conexión con él, con el objetivo de obtener la topología y de que el OFM pueda hacer el control sobre el tráfico

![image](https://user-images.githubusercontent.com/98832318/192136644-6594b676-d92a-4856-8df8-870812f2ccde.png)

Una vez configurado el programa, se ejecutará el comando grunt.

```
cd OpenFlowApp
grunt
```
Ejecutará el programa y abrirá una conexión en localhost:9000 donde podremos ver algo como lo de la siguiente figura.

![image](https://user-images.githubusercontent.com/98832318/192136666-20fab8e6-651a-4d88-b4d8-2cfd1fe458e8.png)
