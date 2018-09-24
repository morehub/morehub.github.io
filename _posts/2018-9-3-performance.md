---
layout: post
title: Midiendo performance de servidores
---

> Para medir la performance de memoria en servidores mediante CLI, se pueden crear instancias con una herramienta de apache llamada ab. La cual nos permite simular la solicitud simultanea de múltiples usuarios contra el servidor.


# Instancia con Apache

El entorno fue creado en un servidor web con el sistema en carga baja de trabajo, como se puede visualizar con el comando top .

![top](ass/m1.png "top")
___

Generamos una instancia con el comando AB

-n = numero de solicitudes 

-c = cantidad de conexiones recurrentes.

    ab -k -r -n 1000000 -c1000 -H 'Accept-Encoding: gzip.deflate' -g test.csv http://192.168.226.101/
    
 ![ab -k -r -n 1000000 -c1000 -H 'Accept-Encoding: gzip.deflate' -g test.csv http://192.168.226.101/](/ass/m2.png "ab")


Monitoriamos con top la performance de las solicitudes.

![top](ass/m3.png "top")


# Instancia con Estress

[RPM stress](https://rpmfind.net/linux/rpm2html/search.php?query=stress)

>Para generar una sobrecarga de memoria de los servidores utilizaremos la herramienta llamadas stress. 
>La cual nos permite realizar diferentes pruebas que generan hilos de procesos saturando la memoria del equipo.
>Para poder monitorear la instancias usamos los comando uptime y free :

Uptime nos permite saber el % de la carga que esta teniendo el sistema cada 1,5 y 15 minutos.
En este caso tenemos el sistema con carga de baja de trabajo.

    watch uptime
    
![uptime](ass/m6.png "uptime")
    
Free nos permite saber el uso de la memoria libre y swap de forma continua. 
 
    watch free

![free](ass/m5.png "free")

    
Ejecutamos el comando de stress

-m = hilos de trabajo 

-t = segundos

    stress -m 1000 -t 120s

Visualizamos la carga de trabajo

![loadmemo](ass/m7.png "stress")

![loadmemo](ass/m8.png "top")





    
    


