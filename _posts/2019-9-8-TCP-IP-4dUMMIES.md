---
layout: post
title: Algunas cosas sobre TCP/IP
---

> Recopilacion de informacion variada sobre el protocolo TCP/IP encontrada en la red.
>
>
>
![LAYER](https://github.com/room29/room92.github.io/blob/master/ass/slide-3.jpg)



# INDICE RAPIDO

1. [Glosario](#glosario)
2. [Protocolo de Aplicación](#protocolo de aplicación)
3. [Protocolo de Transporte]()
4. [Protocolo de Internet]()
5. [Protocolo de acceso a la red]()


## GLOSARIO

#### Protocolo
- Secuencia de pasos y convenciones.

- Acuerdo para llevar comunicación.

#### Socket
- Comunica 2 procesos a travez de la red.

- Usa algun protocolo de transporte.


#### Proceso
-  Instancia de ejecución de un programa.

 - Puede existir un programa con varios procesos.


## Protocolo de aplicación

- Protocolo de alto nivel

- Sistema cliente y servidor, por el cual se produce el intercambio de datos y la ejecución de comandos.

-  Es cercano a la comunicacion humana y usa textos claros preferentemente sin codificación.

## Socket TCP

1. Un proceso abre un socket TCP en escucha por el n° de puerto xxxx, el servidor esta a la espera de una conexión.
2. El cliente Abre un socket conectandose al servidor haciendo referencia a la IP y Puerto.
3. El socket puede ser visto por el cliente y servidor. Se crea un archivo de lectura y escritura entre ambos.





