---
layout: single
title: Rompiendo contraseña - iniciando 1.2.1
excerpt: "Descifraremos una contraseña simple partiendo de un usuario con privilegios usando un diccionario de contraseñas para realizar un ataque de fuerza bruta."
date: 2022-03-14
classes: wide
header:
  teaser: /assets/images/htb-write-contraseñaseasy/contrahash.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - vulnerabilidad
  - infosec
tags:  
  - password
  - basic
  - fuerza bruta 
  - hash
  - vulnerabilidad
---

![](/assets/images/htb-write-contraseñaseasy/contrahash.png)
Las contraseñas normalmente y por ley están encriptadas a través de la función matemática "hash" siendo esta un algoritmo que transforma cualquier bloque arbitrario de datos en una nueva serie de caracteres con una longitud determinada. Independientemente de la longitud de los datos de entrada, el valor del hash obtendrá la misma longitud. Pero de igual manera estos hash pueden ser atacados con "ataques de fuerza bruta".

Un ataque de fuerza bruta consiste en usar el poder de procesamiento de nuestra máquina para probar distintas combinaciones a través del tiempo con el objetivo de decifrar los datos encriptados. (usaremos la herramienta `john`.

Para saber que funcion usa el sistema para encriptar ejecutaremos la siguiente linea.

```
└──╼ $cat /etc/login.defs | grep "ENCRYPT_METHOD"
```

La cual nos da como resultando

```
# This variable is deprecated. You should use ENCRYPT_METHOD.
ENCRYPT_METHOD SHA512
# Only used if ENCRYPT_METHOD is set to SHA256 or SHA512.
```

Confirmando que se usando las dintintas funciones SHA. 

Los hash por lo general se guardan en `/etc/shadow`. para descubrir un hash en especifco (siempre y cuando contemos con el privilegio) podremos ejecutar la siguente linea para saberlo.

```
❯ cat etc/shadow | grep (filtrar por usuario)
(usuario):$y$j9T$5zrtWdQrZTNCHpBSEPF0f/$qKD40oAVK3MAvCQ/A/gUwH8nmjC./LFiJk7vAGsS4P9:19066:0:99999:7:::
```

Una vez tengamos el hash podremos usar un diccionario como el ` rockyou.txt`

```
❯ cat rockyou.txt | grep "(filtro si se tiene alguna idea de la posible contraseña)"
```

Nos arrojara todas las posibilidades y con herramientas como `john` podremos "romper el hash"

ejecutamos `❯ cat /etc/shadow | grep (usuario) > hash` para guardar el hash y con `john` la siguiente linea

```
❯ john --wordlist=rockyou.txt --rules hash --format=crypt
```

y nos arrojara como resultado la CONTRASEÑA.

```
Loaded 1 password hash (crypt, generic crypt(3) [?/64])
Cost 1 (algorithm [1:descrypt 2:md5crypt 3:sunmd5 4:bcrypt 5:sha256crypt 6:sha512crypt]) is 0 for all loaded hashes
Cost 2 (algorithm specific iterations) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
CONTRASEÑA           (USUARIO)
1g 0:00:00:29 DONE (2022-03-15 00:25) 0.03382g/s 81.19p/s 81.19c/s 81.19C/s skyblue..blingbling
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```







