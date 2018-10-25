
---
layout: post
title: Repos locales en RHEL
---

> Creacion de repositorio locales en RHEL

CREACION DE REPOSITORIOS

### creacion de directorio

    mkdir /mnt/iso

### montar iso

    mount | grep sr0

### agregar entrada a fstab#

    echo "/dev/sr0 /mnt/iso iso9660 ro,nosuid,nodev,relatime 0 0" >> /etc/fstab

### desmontar iso#

    umount /dev/sr0
    mount -a

### verificar punto de montaje#

    df -h

### deberia visualizar esto en la consola"

    /dev/sr0    4,4G   4,4G     0 100% /mnt/iso

### copiar salida de media.repo a base.repo#

    cat /mnt/iso/media.repo >> /etc/yum.repos.d/base.repo

### editar base.repo#

    vim /etc/yum.repos.d/base.repo

### agregar a base.repo#

    baseurl=file:///mnt/iso
    enabled=1

### verificamos con el comando en la consola#

    yum repolist

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
---
layout: post
title: Configurando HTTP part1
---



> Instalación de servidor apache

Instalando Apache y manuales

    yum install httpd http-manual
    
Habilitando servicios

    systemctl enable httpd
    
    systemctl start httpd
    
Creando Virtual hosts
 
     cd /etc/httpd/conf.d
 
     vim 00_vhost.conf

>ANEXO I

>Agregar informacion rapidamente a vim

      :r!cat /usr/share/doc/httpd-2.4.6/httpd-vhosts.conf

>Encontrar formas de directorio con la ayuda del sistema

      cat ../conf/httpd.conf

______________________________________


Se deberia visualizar con esta forma el documento

```
<VirtualHost _default_:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/data/default/www"
    ServerName www1.example.com
    ServerAlias www1.example.com
    ErrorLog "/var/log/httpd/www1.example.com-error_log"
    CustomLog "/var/log/httpd/www1.example.com-access_log" combined
</VirtualHost>

Directory=dir donde se encuentra el VH

<Directory "/data/default/www">
    AllowOverride None
    Require all granted
</Directory>
```

Agregamos directorio y generamos un index de prueba

    mkdir -p /data/default/www

    echo "Defaults Web Server" > /data/default/www/index.html
    
Generamos contextos a selinux 

    semanage fcontext -a -t httpd_sys_content_t "/data(/.*)?"
    
    restorecon -Rvvv /data
    
 Verificar desde el navegador
    
    http://www1.example.com/


Reiniciamos servicios y agregamos regla al firewall

```
    systemctl restart httpd

    firewall-cmd --permanent --add-service=http


```

>ANEXO II

>Agregar DNS ZONE al dominio desde servidor IPA si se quiere visualisar desde otro cliente

>-CONFIG :

>Record name = WWW1

>Record Type = CNAME

>Hostname = Nombredeservidor


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


---
layout: post
title: ISCSI
---

[ISCSI](https://www.webopedia.com/TERM/I/iSCSI.html) 
>(Abreviatura de Internet SCSI) es un estándar que permite el uso del
>protocolo SCSI sobre redes TCP/IP. iSCSI es un protocolo de la capa de transporte definido en las especificaciones SCSI-3. 
>Otros protocolos en la capa de transporte son SCSI Parallel Interface y canal de fibra. 

# CONFIGURACIÓN

SABER SOBRE MIS PARTICIONES

    fdisk -l

## CREAR PARTICION 

```
fdisk /dev/vdb 
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x0cd07c31.

Orden (m para obtener ayuda): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): 
Using default response p
Número de partición (1-4, default 1): 
Primer sector (2048-4194303, valor predeterminado 2048): 
Se está utilizando el valor predeterminado 2048
Last sector, +sectors or +size{K,M,G} (2048-4194303, valor predeterminado 4194303): +500M
Partition 1 of type Linux and of size 500 MiB is set

Orden (m para obtener ayuda): p

Disk /dev/vdb: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Identificador del disco: 0x0cd07c31

Disposit. Inicio    Comienzo      Fin      Bloques  Id  Sistema
/dev/vdb1            2048     1026047      512000   83  Linux

Orden (m para obtener ayuda): w
¡Se ha modificado la tabla de particiones!

Llamando a ioctl() para volver a leer la tabla de particiones.
Se están sincronizando los discos.
```
CREACION DE VOLUMEN 

    partprobe -s
 
CREANDO VOLUMEN DE GRUPO
 
    vgcreate vg1 /dev/vdb1
 
CREANDO VOLUMEN LOGICO QUE UTILIZE TODO EL ESPACIO
 
    lvcreate -n lv01 vg1 -l 100%FREE
    
VERIFICANDO EL VOLUMEN CREADO
 
 ```
 lvs
 
  LV   VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root rhel_x1 -wi-ao----   5,29g                                                    
  swap rhel_x1 -wi-ao---- 716,00m                                                    
  lv01 vg1     -wi-a----- 496,00m 
  ```

## INSTALANDO TARGET EN SERVIDOR HOST

    yum install targetcli

INICIAR TARGET 

    targetcli

## CREANDO BLOCKDEVICE 
 ```
/> backstores/block create datos /dev/vg1/lv01 
Created block storage object datos using /dev/vg1/lv01. 
 ```

## CREAR IQN 
 ```
/> iscsi/ create iqn.2017-08.com.example:server1
Created target iqn.2017-08.com.example:server1.
Created TPG 1.
Global pref auto_add_default_portal=true
Created default portal listening on all IPs (0.0.0.0), port 3260.
 ```

## CREAR ACL PARA HABILITAR QUIEN PUEDE LEER ESTE BLOCKDEVICE
 ```
/> iscsi/iqn.2017-08.com.example:server1/tpg1/acls create iqn.2017-08.com.example.server2
Created Node ACL for iqn.2017-08.com.example.server2
 ```
## CREANDO LUNS
 ```
/> iscsi/iqn.2017-08.com.example:server1/tpg1/luns create /backstores/block/datos 
Created LUN 0.
Created LUN 0->0 mapping in node ACL iqn.2017-08.com.example.server2
 ```
## ASIGNANDO NETWORK PORTALS (IP DEL SERVIDOR HOST)
 ```
/> iscsi/iqn.2017-08.com.example:server1/tpg1/portals/ delete 0.0.0.0 3260
Deleted network portal 0.0.0.0:3260

/> iscsi/iqn.2017-08.com.example:server1/tpg1/portals/ create 192.168.122.16 3260
Using default IP port 3260
Created network portal 192.168.122.16:3260.
 ```
## VERIFICANDO CONFIGURACION

 CON CAT 
 ```
cat /etc/target/saveconfig.json
 ```
 CON TARGET
 ```
targetcli ls

o- / ..................................................................................................... [...]
  o- backstores .......................................................................................... [...]
  | o- block .............................................................................. [Storage Objects: 1]
  | | o- datos ................................................. [/dev/vg1/lv01 (496.0MiB) write-thru activated]
  | o- fileio ............................................................................. [Storage Objects: 0]
  | o- pscsi .............................................................................. [Storage Objects: 0]
  | o- ramdisk ............................................................................ [Storage Objects: 0]
  o- iscsi ........................................................................................ [Targets: 1]
  | o- iqn.2017-08.com.example:server1 ............................................................... [TPGs: 1]
  |   o- tpg1 ........................................................................... [no-gen-acls, no-auth]
  |     o- acls ...................................................................................... [ACLs: 1]
  |     | o- iqn.2017-08.com.example.server2 .................................................. [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ............................................................. [lun0 block/datos (rw)]
  |     o- luns ...................................................................................... [LUNs: 1]
  |     | o- lun0 ................................................................ [block/datos (/dev/vg1/lv01)]
  |     o- portals ................................................................................ [Portals: 1]
  |       o- 192.168.122.16:3260 .......................................................................... [OK]
  o- loopback ..................................................................................... [Targets: 0]
 
 ```
## HABILITANDO FIREWALL

 ```
firewall-cmd --permanent --add-port=3260/tcp
success
firewall-cmd --reload
success
firewall-cmd --list-all

public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources: 
  services: dhcpv6-client
  ports: 3260/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  sourceports: 
  icmp-blocks: 
  rich rules: 
 ```
 
 ## INICIANDO SERVICIOS 
 
  ```
systemctl enable target
Created symlink from /etc/systemd/system/multi-user.target.wants/target.service to /usr/lib/systemd/system/target.service.

systemctl start target

systemctl status target

● target.service - Restore LIO kernel target configuration
   Loaded: loaded (/usr/lib/systemd/system/target.service; enabled; vendor preset: disabled)
   Active: active (exited) since mar 2018-08-28 20:13:29 CLST; 8s ago
  Process: 5509 ExecStart=/usr/bin/targetctl restore (code=exited, status=0/SUCCESS)
 Main PID: 5509 (code=exited, status=0/SUCCESS)

ago 28 20:13:29 x1.example.com systemd[1]: Starting Restore LIO kernel target configuration...
ago 28 20:13:29 x1.example.com systemd[1]: Started Restore LIO kernel target configuration.

```
## CONFIGURANDO EN CLIENTE

 INSTALANDO CLIENTE 

    yum install iscsi-initiator-utils


COPIAMOS LA IQN DE LA ACL DEL HOST

    vim /etc/iscsi/initiatorname.iscsi 

    InitiatorName=iqn.2017-08.com.example.server2

REINICIAMOS EL SERVICIO 

```
systemctl enable iscsi
systemctl restart iscsi
systemctl status iscsi

```
DESCUBRIR TARGET

> saber sobre ejemplos de target en apartado examples
  
    man iscsiadm
    
 DESCUBRIENDO TARGET 

    iscsiadm --mode discoverydb --type sendtargets --portal 192.168.122.16 --discover

    192.168.122.16:3260,1 iqn.2017-08.com.example:server1

HACER LOGIN

```
iscsiadm --mode node --targetname iqn.2017-08.com.example:server1 --portal 192.168.122.16:3260 --login
Logging in to [iface: default, target: iqn.2017-08.com.example:server1, portal: 192.168.122.16,3260] (multiple)
Login to [iface: default, target: iqn.2017-08.com.example:server1, portal: 192.168.122.16,3260] successful.

```
VERIFICAR ISCSI

```
iscsiadm -m session -P3 | grep -i attach
lsscsi

```

CREANDO DIRECTORIO

```
mkdir /iscsi-datos
mkfs.xfs /dev/sda

```

MOSTRANDO UID

```
blkid | grep sda
echo "UUID="69d79151-9b6a-452b-801c-c243070b29ec" /iscsi-datos xfs _netdev 0 0" >> /etc/fstab

```
 MONTANDO 

    mount -a

VERIFICANDO 

```
df -h | grep iscsi

/dev/sda                   493M    25M  468M   6% /iscsi-datos

```

 DESMONTANDO Y DESLOGUEANDO

```
umount /iscsi-datos/
iscsiadm --mode node --targetname iqn.2017-08.com.example:server1 --portal 192.168.122.16:3260 --logout

```

REINICIAMOS EL EQUIPO Y AL INICIAR VERIFICAMOS

```
systemctl reboot

iscsiadm -m session -P3 | grep -i attach

```
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
    
    ---
layout: post
title: Midiendo la performance de servidores
---

> Para medir la performance de memoria en servidores mediante CLI, se pueden crear instancias con una herramienta de apache llamada ab. La cual nos permite simular la solicitud simultanea de múltiples usuarios contra el servidor.


# Instancia con Apache

El entorno fue creado en un servidor web con el sistema en carga baja de trabajo, como se puede visualizar con el comando top .

![top](/ass/m1.png "top")
___

Generamos una instancia con el comando AB

-n = numero de solicitudes 

-c = cantidad de conexiones recurrentes.

    ab -k -r -n 1000000 -c1000 -H 'Accept-Encoding: gzip.deflate' -g test.csv http://192.168.226.101/
    
 ![ab -k -r -n 1000000 -c1000 -H 'Accept-Encoding: gzip.deflate' -g test.csv http://192.168.226.101/](/ass/m2.png "ab")


Monitoriamos con top la performance de las solicitudes.

![top](/ass/m3.png "top")


# Instancia con Estress

[RPM stress](https://rpmfind.net/linux/rpm2html/search.php?query=stress)

>Para generar una sobrecarga de memoria de los servidores utilizaremos la herramienta llamadas stress. 
>La cual nos permite realizar diferentes pruebas que generan hilos de procesos saturando la memoria del equipo.
>Para poder monitorear la instancias usamos los comando uptime y free :

Uptime nos permite saber el % de la carga que esta teniendo el sistema cada 1,5 y 15 minutos.
En este caso tenemos el sistema con carga de baja de trabajo.

    watch uptime
    
![uptime](/ass/m6.png "uptime")
    
Free nos permite saber el uso de la memoria libre y swap de forma continua. 
 
    watch free

![free](/ass/m5.png "free")

    
Ejecutamos el comando de stress

-m = hilos de trabajo 

-t = segundos

    stress -m 1000 -t 120s

Visualizamos la carga de trabajo

![loadmemo](/ass/m7.png "stress")

![loadmemo](/ass/m8.png "top")





    
    






















 





















    


