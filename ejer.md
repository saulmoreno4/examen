## Docker

creamos el dockerfile y lo configuramos, descargamos la web del blog y la dejamos en la misma carpeta

~~~
FROM php:7.0-apache
COPY web /var/www/html/
~~~

donde le decimos que la img sera php con apache y que compie el contenido de web (donde se aloja nuestra web) y la pase a la ruta donde el trabaja.

Ahora generamos nuestra imágen local “ejer1” usando el fichero “Dockerfile” recién creado.

~~~
docker build -t ejer1 .
~~~


![nombre](img/1.png)

build crea el contenedor desde el dockerfile y -t le da un nombre

~~~
docker images
~~~

vemos que la tenemos creada y ahora levantamos el servicio, para ver que funcione

~~~
docker run -d -it -p 80:80 ejer1
~~~

le decimos que arranque en demon del puerto 80 al 80 y le damos un nombre.


![nombre](img/2.png)
(nos peta pero por que la web es una mierda)

una vez levantado, creamos el docker-compose

dentro de el tenemos que declarar primero la version, los servicios que vamos a tener y luego rellenar cada uno y creamos la redes para separar entre backend y fronted y luego las declaramos dentro de cada servicio.


~~~
version: '3'
services: 
  apachephp: 
  phpmyadmin:   
  mariadb:
networks:
  backend:
  frontend:
~~~

primero apache que llamaremos a nuestro dockerfile que hemos creado antes, para llamarlo en build con el . , ponemos los puertos volumenes y dependencias y las redes 

~~~
version: '3'
services: 
  apachephp:
    build: .
    container_name: apachephp
    ports:
      - 80:80
    volumes:
      - ./web:/var/www/html
    depends_on:
      - mariadb
    networks:
      - backend
      - frontend

~~~

si ponemos el nombre de la img original si no la tenemos nos la descarga

~~~
phpmyadmin: 
    image: phpmyadmin/phpmyadmin:latest
    container_name: phpma
    ports:
      - 8080:80
    environment:
      - PMA_HOST=mariadb
    depends_on:
      - mariadb
    networks:
      - backend
~~~

environment ponemos la configuracion de la base de datos

~~~
mariadb:
    image: mariadb:latest
    container_name: mariadb
    volumes: 
      - ./web:/var/lib/mysql
    environment:
      - MYSQL_ROOT_USER=root
      - MYSQL_ROOT_PASSWORD=admin
      - MYSQL_DATABASE=testdb
      - MYSQL_USER=user
      - MYSQL_PASSWORD=user
    networks:
      - backend
~~~

subir img a docker-hub
~~~
docker login
~~~

ahora tenemos que crear la imagen para unificarlo todo, con

~~~
docker-compose up -d
~~~

y verificamos que este el contenedor, si no tiene nuestro nombre de usuario

~~~
docker image tag d583c3ac45fd saulmorenoo/programa
~~~

~~~
docker push saulmorenoo/programa
~~~