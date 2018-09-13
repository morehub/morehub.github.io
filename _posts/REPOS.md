---
layout: post
title: Repos locales en RHEL

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
    

-------------------------------------------------------------------------[ir a la entrada del blog](morehub.github.com)
