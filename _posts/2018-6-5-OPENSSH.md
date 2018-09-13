---
layout: post
title: Configurando OPENSSH
---


> Configuracion rapida para acceder a SSH


 Instalamos el servidor OpenSSH con el comando:

     sudo yum install openssh-server


## Configurar SSH

Editamos el archivo 

    /etc/ssh/sshd_config

Primero hacemos un backup del archivo original.

    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.old

Restringimos al usuario root para que no pueda conectarse de forma remota.

    PermitRootLogin no

Cambiamos el puerto por defecto de 22 a 2244.

    Port 2244

Reiniciamos el servidor OpenSSH

    sudo systemctl reload sshd

Para que el servidor OpenSSH inicie de forma automática.

    sudo chkconfig sshd on


## Creando usuarios para SSH

Para conectarnos de forma remota vamos a crear un nuevo usuario.

    sudo adduser userpublico

    sudo passwd userpublico


## Configurar el Firewall

Abrimos el puerto que cambiamos.

    sudo firewall-cmd --permanent --add-port=2244/tcp

Y reiniciamos el Firewall.

    sudo firewall-cmd --reload


## Probar conexión SSH

Desde otra maquina ejecutamos este comando para ingresar remotamente con el nuevo usuario, donde 192.168.1.20 es la IP del servidor OpenSSH.

    ssh -p 2244 userpublico@192.168.1.20

