---
layout: post
title: Midiendo performance de servidores.
---

> Para medir la performance de memoria en servidores mediante CLI, se pueden crear instancias con una herramienta de apache llamada ab. La cual nos permite simular la solicitud simultanea de múltiples usuarios contra el servidor.


#Instancia con apache

El entorno fue creado en un servidor web con el sistema en carga baja de trabajo, como se puede visualizar con el comando top .


Generamos una instancia con el comando AB

    ab -k -r -n 1000000 -c1000 -H 'Accept-Encoding: gzip.deflate' -g test.csv http://192.168.226.101/

-n = numero de solicitudes 
-c = cantidad de conexiones recurrentes.

Monitoriamos con top la performance de las solicitudes.


#Instancia con Estress

[stress](https://rpmfind.net/linux/rpm2html/search.php?query=stress)
Para generar una sobrecarga de memoria de los servidores utilizaremos la herramienta llamadas stress. 
La cual nos permite realizar diferentes pruebas que generan hilos de procesos saturando la memoria del equipo.
Para poder monitorear la instancias usamos los comando uptime y free :

Uptime nos permite saber el % de la carga que esta teniendo el sistema cada 1,5 y 15 minutos.
En este caso tenemos el sistema con carga de baja de trabajo.

    watch uptime
    
Free nos permite saber el uso de la memoria libre y swap de forma continua. 
 
    watch free
    
Ejecutamos el comando de stress

stress -m 1000 -t 120s

-m = hilos de trabajo 
-t = segundos
    
    


