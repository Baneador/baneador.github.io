Generar una clave ssh para trabajar en github es extremadamente sencillo, a continuación de muestro como hacerlo

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

1. Ajustes
![](/assets/images/ssh-key-to-github/github1.png)

""SSH and GPG Keys" --> "New SSH key"

aquí agregas un titulo, pegas el contenido de id_rsa.pub y listo! así de facil :)
