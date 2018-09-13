---
layout: post
title: Creando network Team (active-backup)
---

> En el archivo de teamd.conf del S.O puedes encontrar informacion y ejemplos de cada configuracion.
___

## CREACION INTERFAZ VIRTUAL
 

Llamaremos nuestra interfaz virtual ossy1
 

     nmcli connection add type team con-name ossy1 ifname ossy1 config '{"runner": {"name": "activebackup"}}'

## CONFIGURANDO IPS
> xxxx= _ip a agregar

    nmcli connection modify ossy1 ipv4.addresses x.x.x.x/24

ESPECIFICANDO CONEXION MANUAL

    nmcli connection modify ossy1 ipv4.method manual 
 
LIGANDO LAS INTERFACES ESCLAVAS A MAESTRAS

> En este caso ens10 y ens11

    nmcli connection add type team-slave con-name ossy1-slave1 ifname ens10 master ossy1

    nmcli connection add type team-slave con-name ossy1-slave2 ifname ens11 master ossy1


LEVANTANDO CONEXIONES

    nmcli connection up ossy1-slave1

    nmcli connection up ossy1-slave2

VERIFICANDO ESTADO

```

teamdctl ossy1 state

setup:
  runner: activebackup
ports:
  ens10
    link watches:
      link summary: up
      instance[link_watch_0]:
        name: ethtool
        link: up
        down count: 0
  ens11
    link watches:
      link summary: up
      instance[link_watch_0]:
        name: ethtool
        link: up
        down count: 0
runner:
  active port: ens10
  ```

## Script de configuraracion rapida :turtle:

```

        #!/bin/bash/
echo
echo CREANDO CONFIGURACION PARA ACTIVE-BACKUP
echo -------------------------------------------------
echo  CONFIG DE LA INTERFAZ VIRTUAL
echo ----------------------------------
echo "ingrese nombre del con-name":
read con1
echo "ingrese nombre del ifname":
read if1  
nmcli connection add type team con-name $con1 ifname $if1 config '{"runner": {"name": "activebackup"}}' 
echo --------------------------------
echo  MODIFICANDO LA AGRUPACION DE IP
echo --------------------------------
echo "introduce ip":
read req_ipadr
echo "introduce mask":
read mask
ip_full=$(echo $req_ipadr | sed -n 's/^\(\(\([1-9][0-9]\?\|[1][0-9]\{0,2\}\|[2][0-4][0-9]\|[2][5][0-4]\)\.\)\{3\}\([1-9][0-9]\?\|[1][0-9]\{0,2\}\|[2][0-4][0-9]\|[2][5][0-4]\)\)$/\1/p')
[ "$ip_full" != "" ] && echo "$req_ipadr vaild ip" || echo "$req_ipadr invaild ip"
nmcli connection modify $con1 ipv4.addresses $req_ipadr/$mask 
nmcli connection modify $con1 ipv4.method manual 
echo --------------------------------------
echo  LIGANDO INTERFAZ ESCLAVA A MAESTRA
echo --------------------------------------
echo "ingrese nombre del slave primario con-name":
read sc1
echo "ingrese nombre del slave secundario con-name":
read sc2
echo "ingrese nombre del la interfaz primaria ":
read int1  
echo "ingrese nombre del la interfaz secundaria ":
read int2  
nmcli connection add type team-slave con-name $con1-$sc1 ifname $int1 master $con1  
nmcli connection add type team-slave con-name $con1-$sc2 ifname $int2 master $con1  
nmcli connection up $con1-$sc1
nmcli connection up $con1-$sc2
nmcli connection down $con1
nmcli connection up $con1
echo ---------------------------------------------
echo            VERIFICANDO ESTADO
echo --------------------------------------------
teamdctl $con1 state
sleep 15
exit

```
