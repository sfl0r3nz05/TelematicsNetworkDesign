# Introducción
Hasta ahora hemos desarrollado toda una red SDN en la cual tenemos unos routers que se rigen por OSPF que han sido sometidos a un ataque. 

Lo siguiente será comprobar que dicho ataque se está ejecutando correctamente y que estamos obteniendo los resultados deseados. Para ello, existe la herramienta Wireshar que sirve para capturar y visualizar el tráfico en un enlace dado. Usando esto, comprobaremos que la secuencia de mensajes es la esperada y, al mismo tiempo, servirá como herramienta de apoyo en el posterior desarrollo del pryecto.

# Usar Wireshark
Para desplegar Wireshark en un determinado enlace para ver el tráfico que circula a trvés de el deberemos hacer Click derecho sobre enlace y elegir la opción "Start to capture". Si se desea se puede añadir una etiqueta que identifique el enlace. 

Ya dentro de la interfaz del programa pulsando el símbolo de Start (el que tiene forma de aleta de tiburón) podremos empezar a visualizar los paquetes que fluyen. Los paquetes se muestran con diferentes campos que dan información sobre él. Existen diferentes campos como IP origen, IP destino, protocolo usado etc. Una herramienta interesante será usar un filtrado para que aparezcan solo paquetes de nuestra incumbencia según el caso.

En la siguiente tabla se muestran algunos ejemplos:

![image](https://user-images.githubusercontent.com/98832318/192808331-ff28a160-a206-4326-9a81-ac07e3e6e0f9.png)

# Análisis del ataque
Tal y como se ha explicado previamente, una vez ejecutado el programa, veremos si ha ocurrido la secuencia deseada y si esta ha tenido el efecto sobre los routers.

Captando el tráfico entre el atacante y el enrutador objetivo, se tendrá que verificar que se recibe la siguiente secuencia de mensajes.
Primero se recibe el LSA de activación. El cual tiene efecto sobre el rúter víctima, no obstante, también se envía al rúter objetivo simultáneamente sólo para garantizar el orden de secuencia de LSA Update que llega.

![image](https://user-images.githubusercontent.com/98832318/192809490-373f1d17-b109-4bd1-b857-257286b68c4d.png)

Después se recibirá el LSA disfrazado, con una información falseada que infectará la tabla de enrutamiento del rúter.

![image](https://user-images.githubusercontent.com/98832318/192809741-41eedabe-aac1-42dd-b2a0-97d93950df2c.png)

Finalmente, llegará el LSA Update mandado debido a la activación del “Fight-Back”. Este paquete será ignorado, ya que se asumirá como duplicado del encubierto, el cual ha llegado antes. Se comprueba que tienen mismo checksum, edad y número de secuencia.

![image](https://user-images.githubusercontent.com/98832318/192809853-d388174c-1890-409b-8091-b145612ec54d.png)

Se podrá chechear que los paquetes han sido admitidos por el rúter activando el debug de adyacencia.

```
debug ip ospf adj
```

Finalmente, se comprueba la tabla de enrutamiento del enrutador objetivo para ver que, efectivamente, ha sido infectada.

![image](https://user-images.githubusercontent.com/98832318/192810211-09091055-eada-438d-b380-32fe868f7295.png)

También se puede comprobar viendo la base de datos.

![image](https://user-images.githubusercontent.com/98832318/192810265-fdf8ee0b-cc5d-4fef-9844-68e80628d864.png)
