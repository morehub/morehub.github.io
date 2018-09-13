---
layout: post
title: Ejemplos forwarding a puerto 22
---
> Ejemplos de forwarding

que deberias saber antes de configurar:

> semange port

    semanage port -l | grep ssh
    
> Firewalld 

    man -k firewall

    man firewalld.richlanguage

    firewall-cmd --help | grep forwar
    
> semanage

    man semanage-port

> LISTAS ACTIVAS

    firewall-cmd --list-all
    firewall-cmd --list-all-zones

> Bloquear acceso a ssh

    firewall-cmd --permanent --zone=public --remove-service=ssh


> Agregar firewall en zonas especificas y removerlas

    firewall-cmd --permanent --remove-source=192.168.4.0/24
    firewall-cmd --permanent --add-source=192.168.4.0/24 --zone=work

> Recargar firewall

    firewall-cmd --reload 

> Remover servicios

    firewall-cmd --permanent --zone=work --remove-service=ssh

    
 > Agregar puerto nuevo a ssh puerto 22

    semanage port -a -t ssh_port_t -p tcp 123

> Agregar port forwarding 

    firewall-cmd --permanent --zone=work --add-forward-port=port=123:proto=tcp:toport=22

> probar la conexion con netcat 

    nc 192.168.122.16 123
    SSH-2.0-OpenSSH_6.6.1

> trabajar con richrules

    man firewalld.richlanguage
    
    rule family="ipv6" source address="1:2:3:4:6::" forward-port to-addr="1::2:3:4:7" to-port="4012" protocol="tcp" port="4011"

 ___
 
## EJEMPLO DE FORWARDING

    firewall-cmd --permanent --zone=work --add-rich-rule='rule family="ipv4" source address=192.168.122.100/24 forward-port     port="456" protocol="tcp" to-port="22"'

realizar conexion a al nuevo puerto 

    nc 192.168.122.16 456
    SSH-2.0-OpenSSH_6.6.1









