# Introducción
El ataque que se relizará es un ataque de "Disguised LSA". En la memoria se desarrolla (4.3, 10.1, 10.2) la técnica en detalle. Se va a usar un programa desarrollado por la comunidad para la implementación en nuestro proyecto (disponible en repositorio).

Este programa necesitará ser "tuneado" para que se adapte a nuestra red y pueda ser efectivo.

# Principio de Operación
Por recordar como era la secuencia que sigue el programa, es la siguiente:

  1.	Se lee el tráfico que fluye por le rúter atacante. Al detectar un paquete “LSA Update” anunciado por el rúter víctima se intercepta y se usa para elaborar los paquetes de activación y encubierto.
  2.	Se elabora y envía el LSA de activación, o “trigger LSA” sobre la víctima, con la idea de activar el mecanismo de “Fight-back”.
  3.	Se elabora el LSA disfrazado, el cual contine los valores de número de secuencia y checksum predichos. El número de secuencia es fácil de predecir, el checksum, en cambio, requerirá de predecirlo por fuerza bruta.

# Adaptación y ejecución
Para poner en marcha el ataque se deberán seguir los siguientes pasos. En primer lugar, clonar el repositorio GitHub del programa ataque. Esto se hará sobre el rúter atacante descargado y configurado previamente (el FRR).

```
git clone https://github.com/lizitong67/OSPF_Attack_and_Detection
```
Posteriormente, se deben cambiar los parámetros de configuración en el código para que el ataque se adapte a nuestra red, la correspondiente a la Figura17. En este caso, en el archivo double_lsa_attack.py de las líneas 60 a la 66 se ha escrito lo siguiente

```
victim = "10.1.2.1"
trigger_send_ip = "10.1.1.1"
trigger_send_if = 'eth0'
disguised_send_ip = "10.1.3.1"
disguised_send_if = 'eth1'
```

El primer valor corresponde al router-id del enrutador víctima. Si no ha sido configurado previamente, el programa por defecto establecerá la IP más alta como id. 

El parámetro trigger_send_ip y disguised_send_ip son las IPs de las interfaces a las que se quiere enviar los paquetes, el de activación el primero, y el encubierto el segundo.

Por último, los valores de trigger_send_if y disguised_send_if corresponden a las interfaces por las que el atacante “ve” al rúter víctima, por un lado, y al rúter objetivo por otro.
Ahora se deberá cambiar la línea 73 del código por lo siguiente

```
pkts = sniff(iface=[“eth0”,”eth1”]filter="proto ospf", stop_filter=lambda x: check_incoming_packet(victim, x))
```
Después, se debe añadir la siguiente línea para corregir la fuente en la cabecera del paquete encubierto

```
pkt_evil[OSPF_Hdr].src= disguised_send_if
```
Para terminar la configuración, se cambia el tiempo de espera entre envío de paquetes de 2 a 6. Este cambio no resulta crítico, pero es conveniente para asegurar la secuencia de mensajes correcta.

```
sendp(pkt_trig,iface=trigger_send_if)
sleep(6)
sendp(pkt_evil,iface=disguised_send_if)
```

Finalmente, se ejecuta el programa y se espera hasta que haya enviado los paquetes. 

```
python3 double_lsa_attack.py
```






