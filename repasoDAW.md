# EXAMEN

## MD

texto

~~~
codigo
~~~

![nombre](1.png)  -->para poner capturas de fotos


https://www.awseducate.com/signin/SiteLogin  -->entrar en Amazon

USUARIOS:
ec2-user --> Linux2
ubuntu -->Ubuntu

ssh -i X.pem (user)@(ip)







## LINUX2

Hacer que la primera pagina entre por un puerto y la otra por otro y hacer la configuración 

~~~
cd /etc/httpd/conf.d
~~~

Se crea un config de cada proyecto.

~~~
sudo nano web1.conf
~~~

Y dentro ponemos

~~~
Listen 22000
<VirtualHost *:22000>
  DocumentRoot /var/www/html/web1
</VirtualHost>
~~~

~~~
sudo systemctl restart httpd
~~~

### Autenticacion BASIC

Crear las carpetas admin y un index.html 
Luego hacemos esto:

~~~
sudo mkdir /etc/httpd/password
sudo htpasswd -c /etc/httpd/password/passwords admin
~~~

Ahora hacemos 

~~~
[ec2-user@ip-172-31-87-161 conf.d]$ sudo nano web1.conf
~~~

~~~
Listen 22000
<VirtualHost *:22000>
  DocumentRoot /var/www/html/web1
</VirtualHost>

<Directory "/var/www/html/web1/admin">
  AuthType Basic
  AuthName "Administracion" 
  AuthUserFile /etc/httpd/password/passwords
  Require valid-user
</Directory>
~~~

Comprobamos que la sintaxis es correcta y recargamos

~~~
sudo apachectl configtest
sudo systemctl restart httpd
~~~

### Autenticacion DIGEST

Hacemos esta sentencia

~~~
sudo htdigest -c /etc/httpd/password/digest "Configuracion" admin
~~~
Ese configuracion tiene que ser igual al que ponemos en el AuthName

Abrimos el siguiente .conf y le ponemos la autenticacion en DIGEST 

~~~
Listen 21000
<VirtualHost *:21000>
  DocumentRoot /var/www/html/web1
</VirtualHost>

<Directory "/var/www/html/web1/admin">
  AuthType Digest
  AuthName "Configuracion"
  AuthUserFile /etc/httpd/password/digest   
  Require valid-user
</Directory>
~~~
Comprobamos que la sintaxis es correcta y recargamos

~~~
sudo apachectl configtest

sudo systemctl restart httpd

~~~

# UBUNTU

Lanzamos una instancia y seleccionamos ubuntu server 20.04 --> luego cogemos la de por defecto--> le damos al review and lunch --> seleccionamos grupo de seguridad --> launch y luego le damos al par de claves que queramos y le damos a launch y listo.

Actualizamos los repositorios e instalamos apache 2 (le damos todo a que si)
~~~
sudo apt update
sudo apt install apache2
~~~

Vemos el status, tiene que aparecer enable, active y en verde!

~~~
sudo systemctl status apache2
~~~

Añadimos el usuario al grupo de administracion de apache nos hace ser superusuarios (darnos permisos).

~~~
sudo usermod -a -G www-data ubuntu
~~~

Salimos  y volvemos a entrar y comprobamos que estamos dentro

~~~
exit 
ssh -i repaso_DAW.pem ubuntu@direccionIP
groups
~~~

Una vez hemos comprobado que está dentro, nos hacemos propietarios de /var/www

~~~
sudo chown -R ubuntu:www-data /var/www
~~~

Cambiamos los permisos 

~~~
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
~~~
Cogemos las carpetas que se creen dentro de este directorio para nosotros ser propietarios de la misma

~~~
sudo find  /var/www -type f -exec sudo chmod 0664 {} \;
~~~
## HACER CAPTURA
Una vez hacemos esto, nos movemos al directorio /var/www y vemos como se contiene html 

~~~
cd /var/www
ls -al
~~~

Instalacion de Mariadb le damos intro y todo que si

~~~
sudo apt install mariadb-server
~~~

Para ponerle la contraseña a todo lo que luego sera php my admin, la primera le damos a enter, luego le decimos yes y le ponemos una contraseña (alexia) y luego le damos a todo que sí

~~~
sudo mysql_secure_installation
~~~

Una vez tenemos instalado mariadb, instalamos php y todas las librerías de php (el usuario siempre será root y la contraseña es la que hemos puesto arriba) le decimos que sí a todo.

~~~
sudo apt install php libapache2-mod-php php-mysql
~~~

Ahora vamos a instalar phpmyadmin y sus librerias principales, le damos al primer si, 

~~~
sudo apt install phpmyadmin php-mbstrig php-zip php-gd php-json php-curl
~~~
luego movernos por la pantallita con el ESPACIO y seleccionamos apache2 y le damos a ok moviendonos con el tabulador y le damos al enter.
Segunda ventana le damos a yes y ponemos una nueva contraseña para phpmyadmin le damos a tabulador y le damos al OK, confirmamos la contraseña, le damos al tabulador y le damos al OK.

## SI NO FUNCIONARA ESTO ANTERIOR:

Nos hace volver a todo lo anterior para darle ok a todo y hacer lo anterior bien, con la contraseña lo del espacio y todo.
~~~
sudo dpkg-reconfigure phpmyadmin
~~~


Vamos al navegador copiamos la direccionIP y la pegamos
Si esto funciona le ponemos : direccionIP/phpmyadmin
Tenemos que ver que la pantalla de inicio se ha cargado, pero no va a funcionar, si la página carga es que vamos bien.

Hacemos una consulta a SQL para cambiar la contraaseña del root 
~~~
sudo mysql

SELECT user, authentication_string,plugin,host FROM mysql.user;
ALTER USER root@localhost IDENTIFIED VIA mysql_native_password;
SET PASSWORD=PASSWORD("aa");

exit; (para salir de mysql)
~~~

Reiniciamos el servicio

~~~
sudo systemctl restart mysql
~~~

Luego de esto reiniciamos la página web para que nos haga caso y entramos con el usuario root y la contraseña que hemos indicado y entramos

## Empezamos con lo relacionado con las páginas web 

Accedemos a html que esta dentro de cd /var/www
~~~
cd html 
ls -al
~~~

Subimos la web con filezilla 



arrastramos las carpetas que tenemos en la parte izquierda abajo dentro de la derecha de html 
Luego en la consola hacemos un para comprobar que están bien subidas

~~~
ls -al
~~~

## Hacer que una página entre en cualquier puerto

Estando dentro de la carpeta /var/www/html
~~~
sudo nano /etc/apache2/sites-available/helados.conf
~~~

Dentro del documento ponemos
~~~
<VirtualHost *:80>
  DocumentRoot /var/www/html/peluqueria
</VirtualHost>
~~~

Por defecto detecta al poner solo nuestra direccion el default porque es la que está activada y tenemos que cambiarla para comprobar que seleccione la que nosotros hemos subido.

Aqui nos sale todo lo que tenemos.
~~~
cd /etc/apache2/sites-available/
ls -al
~~~

Ahora vamos a mirar que archivo es el que está mostrando 
~~~
cd ..
cd sites-enabled/
ls -al
~~~

Añadimos el sitio por ejemplo de (peluqueria), el reload lo podemos hacer ahora o luego 

~~~
sudo a2ensite helados

~~~

~~~
sudo a2dissite 000-default
~~~

Hacemos un reload para que se active todo lo que acabamos de hacer.

~~~
sudo systemctl reload apache2
~~~

Recargamos la página web y saldrá lo que hay dentro de la peluqueria


## Ahora vamos a hacer lo mismo pero con otros puertos (puerto introducido en el grupo de seguridad)

Vamos a sites availabe
~~~
cd ..
cd sites-available/
ls-al
~~~
Siempre que estemos dentro de la ruta anterior 
~~~
sudo nano fontaneria.conf
~~~

~~~
Listen 50000
<VirtualHost *:50000>
  DocumentRoot /var/www/html/fontaneria
</VirtualHost>
~~~

Hacemos restart

~~~
sudo apachectl configtest
sudo systemctl restart apache2
~~~
Una vez dentro añada fontaneria a enable
~~~
ls -al
sudo a2ensite fontaneria
sudo systemctl reload apache2
~~~
Vamos a la pagina web y seguido de nuestra direccionIp:puerto_que_lo_hemos_metido


## Autenticacion Basic

(TODO LO ANTERIOR A ESTO SE HACE IGUAL A LINUX, CREAR TODO LO DEL ADMIN CON EL INDEX.HTML COMO EN LINUX )

Crear directorio dentro de apache2 llamado password 
~~~
sudo  mkdir /etc/apache2/password
~~~
Hacemos el usuario admin dentro de esta carpeta

~~~
sudo htpasswd -c /etc/apache2/password/passwords admin 
~~~
Nos pide contraseña y ponemos (alexia)
Vamos al archivo de configuracion desde sites-available

~~~
ls -al
sudo nano peluqueria.conf
~~~
Y quedaria asi

~~~
<VirtualHost *:80>
  DocumentRoot /var/www/html/peluqueria
</VirtualHost>

<Directory "/var/www/html/peluqueria/admin">
  AuthType Basic
  AuthName "Administracion" 
  AuthUserFile /etc/apache2/password/passwords
  Require valid-user
</Directory>
~~~

Hacemos el configtest y si todo va bien hacemos reload

~~~
sudo apachectl configtest
sudo systemctl reload apache2
~~~

Comprobamos que todo va bien ponemos la ip:el_puerto/admin
usuario:admin
contraseña:la_que_hemos_puesto

## Autenticacion Digest

Estamos desde sites-available, creamos un directorio como el linux (si no esta hecha anteriormente)

~~~
sudo  mkdir /etc/apache2/password (hay que hacerlo si no lo hemos hecho anteriormente)
~~~

Creamos el usuario tipo digest

~~~
sudo htdigest -c /etc/apache2/password/digest "Configuracion" admin
~~~
Y luego ponemos la contraseña que queramos (alexia)

Editamos el archivo de configuracion 

~~~
sudo nano fontaneria.conf
~~~
Y dentro ponemos
~~~
Listen 50000
<VirtualHost *:50000>
  DocumentRoot /var/www/html/fontaneria
</VirtualHost>

<Directory "/var/www/html/fontaneria/admin">
  AuthType Digest
  AuthName "Configuracion"
  AuthUserFile /etc/apache2/password/digest   
  Require valid-user
</Directory>
~~~

Hacemos el configtest 

~~~
sudo apachectl configtest
~~~

Cargamos el digest

~~~
sudo a2enmod auth_digest.load
~~~

Hacemos el restart

~~~
sudo systemctl restart apache2
~~~

Comprobamos que todo va bien ponemos la ip:el_puerto/admin
usuario:admin
contraseña:la_que_hemos_puesto

# SITIOS IMPORTANTES PARA HACER CAPTURA (UBUNTU Y LINUX)
- A LA HORA DE CREAR UN PAR DE CLAVES
- AL CREAR UN GRUPO DE SEGURIDAD (INFO DEL MISMO)
- A LA HORA DE HACER UNA INSTANCIA (INFO DE LA MISMA)
- CAPTURA DEL SSH PRIMERO UNA VEZ LA HEMOS ARRANCADO
- DE TODO LO QUE TE DICE QUE ESTA BIEN INSTALADO (STATUS)
- CAPTURA DE GROUPS COMO QUE SE HA AÑADIDO CORRECTAMENTE
- METER EN /VAR/WWW Y ENSEÑAR COMO QUE HTML TIENE ESOS PERMISOS
- EN UBUNTO LO DE QUE HAY QUE APRETAR ESPACIO, OTRA DE LA DE CONTRASEÑA NUEVA
- DE TODAS LAS CONSUTAS HECHAS EN EL SQL 
- COMRPOBACIONES DE ACCEDER A LA PAGINA WEB (ANTES/DESPUES)
- UNA CAPTURA DEL FILEZILLA COMO LAS QUE HE PUESTO Y DE MOVER LAS COSAS DEL SITIO
- FOTOS DE LOS DOCUMENTOS DE CONFIGURACION
- LO DE LOS SITIOS AVAILABLE Y DE LOS SITIOS AÑADIDOS
- AUTENTICACIONES, EXPLICAMOS Y FOTO AL DOCUMENTO Y EN LA WEB CUANDO TE PIDE LA CONTRASEÑA

# GIT

Tutoriales
~~~
https://www.diegocmartin.com/tutorial-git/
https://drive.google.com/file/d/1X60rTAE0Lh9YAgCvDSZTcMJ7MI6SMe5x/view
~~~


Git  crear repositorio ir a mi pagina de git https://github.com/alexiaagramunt

crear carpeta 
~~~
git init 
~~~
crear index.html dentro de la carpeta
~~~
nano lo que sea.html
~~~
para ver los archivos que estan esperando para ser subidos
~~~
git status
~~~ 
Para añadir el archivo dentro del repositorio con add, y para guardarlo dentro del repositorio hacemos commit -am (am para que se actualicen automáticamente)
~~~
git add index.html
git commit -am "mi primer commit" 
~~~
Para que se añada a nuestra cuenta de guit
~~~
git remote add origin git@github.com:alexiaagramunt/prueba.git  
~~~
Para subir el commit al repositorio de la nube
~~~
git push origin master  
~~~
Si solo quiero hacerlo de uno /para ignorar archivos  
~~~
git commit -m "mi primer commit"  archivo .gitignore 
~~~

---crear rama---  
~~~
git branch rama1
~~~  
Para moverte a esa rama 
~~~
checkout rama1
~~~
vemos donde estamos ubicados, en que rama
~~~
git branch 
~~~
Unamos los cambios de la rama donde estamos trabajando al archivo original  
~~~
git merge rama1 
~~~
Vemos todos los commits que hemos hecho 
~~~
git log
~~~
Sale un codigo largo, nos lleva a como estaban todas las cosas en ese commit
~~~
git checkout sdjflidfj
~~~

# Bin Bash

~~~
Tutorial : https://ryanstutorials.net/bash-scripting-tutorial/
~~~



# Docker



