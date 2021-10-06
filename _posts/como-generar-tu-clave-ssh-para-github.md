---
layout: single
title: Configurar ssh para github
excerpt: "Generar una clave ssh para trabajar en github es extremadamente sencillo, a continuación te muestro como hacerlo"
classes: wide
header:
  teaser: /assets/images/htb-writeup-multimaster/multimaster_logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - ssh-key
  - githug
tags:
  - llave ssh
  - github

---

![](/assets/images/htb-writeup-multimaster/multimaster_logo.png)


Generar una clave ssh para trabajar en github es extremadamente sencillo, a continuación te muestro como hacerlo

## Generación de llave publica y privada ssh
Lo primero que hay que hacer, es generar la clave publica y privada, para ello ejecutamos el siguiente comando

```
ssh-keygen -t rsa - -b 4096 -C tuemail@dominio.com
``` 

luego, podemos verificar la existencia de estos archivos con

```
ls -la ~/.ssh
```

con lo cual obtendremos una salida similar a esta

```
-rw------- 1 root root 2610 oct  5 18:50 id_rsa
-rw-r--r-- 1 root root  575 oct  5 18:50 id_rsa.pub
-rw-r--r-- 1 root root  884 oct  5 18:41 known_hosts
```

*Nota*: el archivo id_rsa.pub es nuestra clave publica, y el id_rsa es nuestra clave priviada

ahora lo siguiente, es copiar el contenido de id_rsa.pub en nuestro github, para ello utilizamos el comando para visualizarlo

```
cat ~/.ssh/id_rsa.pub
```

luego, en tu repositorio de github, debes irte a la sección 

## 1. Ajustes
![](/assets/images/ssh-key-to-github/github1.png)

## 2. SSH and GPG Keys
![](/assets/images/ssh-key-to-github/github2.png)

## 3. New SSH key
![](/assets/images/ssh-key-to-github/github3.png)

## 4. Agregar titulo y la clave id_rsa.pub que copiamos previamente
![](/assets/images/ssh-key-to-github/github4.png)

Y listo! así de facil :)
