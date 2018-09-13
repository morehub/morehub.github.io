---
layout: post
title: Ejemplos forwarding a puerto 22
---

### saber los puertos

    semanage port -l | grep ssh

# Firewalld
## saber sobre firewalld

    man -k firewall

    man firewalld.richlanguage

    firewall-cmd --help | grep forwar
    
   ## saber sobre semanage

    man semanage-port
___

### LISTAS ACTIVAS

    firewall-cmd --list-all
    firewall-cmd --list-all-zones

### bloquear acceso a ssh

    firewall-cmd --permanent --zone=public --remove-service=ssh


### agregar firewall en zonas especificas
remover firewall

    firewall-cmd --permanent --remove-source=192.168.4.0/24
    firewall-cmd --permanent --add-source=192.168.4.0/24 --zone=work

### recargar firewall

    firewall-cmd --reload 

### remover servicios

    firewall-cmd --permanent --zone=work --remove-service=ssh

    
 ### agregar puerto nuevo a ssh puerto 22

semanage port -a -t ssh_port_t -p tcp 123

### agregar port forwarding 

    firewall-cmd --permanent --zone=work --add-forward-port=port=123:proto=tcp:toport=22



### Conectar con netcat 

    nc 192.168.122.16 123
    SSH-2.0-OpenSSH_6.6.1

### trabajando con richrules

    man firewalld.richlanguage

### EXAMPLE 5

    rule family="ipv6" source address="1:2:3:4:6::" forward-port to-addr="1::2:3:4:7" to-port="4012" protocol="tcp" port="4011"


 ### Reedirigir puerto 456 a 22

    firewall-cmd --permanent --zone=work --add-rich-rule='rule family="ipv4" source address=192.168.122.100/24 forward-port     port="456" protocol="tcp" to-port="22"'

    nc 192.168.122.16 456
    SSH-2.0-OpenSSH_6.6.1









