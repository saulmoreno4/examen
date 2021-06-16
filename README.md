
# Ejercicio 1


## Paso 1 - Configuración de una instancia en Ubuntu 20.04 en Amazon AWS
___
___

Lo primero que deberemos considerar es si tenemos una cuenta válida en Amazon AWS, en caso afirmativo, entraremos desde este enlace.

[AWS Educate Login](https://www.awseducate.com/login?startURL=%2FSiteLogin "AWS Educate Login")

![AWS Login](img/awslogin.png)

Introducimos nuestro usuario y contraseña y accedemos. 

Esto nos llevará a otra pantalla y deberemos acceder al apartado "AWS Account" ubicado en la barra horizontal del menú de navegación. 

Una vez accedamos aquí entraremos a la consola, y después al apartado "EC2". 

Deberíamos ver algo así.

![Panel EC2](img/panelec2.png)

Por ahora no tenemos ninguna instacia en ejecución. 

Pulsamos en el apartado "Instancias en ejecución" para configuar la nuestra.

Nos llevará a la siguiente página

![Lanzar Instancia](img/lanzarinstancia.png)

Pulsaremos en el botón "Lanzar Instancia" y esto dará comienzo al proceso de configuración de nuestra instancia. 

Lo primero que haremos es acceder a un menú en el que seleccionaremos la distribución en la que queramos lanzar nuestra instancia.

![Seleccionar distribución](img/distribucion.png)

En nuestro caso es "Ubuntu 20.04" así que esa será la que seleccionaremos. 

En este caso no queremos configurar nada excepto algunas reglas de entrada.

![Reglas de entrada](img/reglas.png)

Como se puede observar hemos configurado dos reglas, una en SSH para poder acceder desde cualquier lugar y otra HTTP para poder acceder desde el puerto 80.

Seguiremos aceptando hasta que lleguemos al final del proceso de configuración, momento en el cual se nos pedirá que utilicemos unas claves para poder lanzar la instancia, en el caso de que las tengamos utilizaremos esas (si lo deseamos), en caso de que no las tengamos, deberemos crearlas.

![Crear claves](img/crearclaves.png)

Seleccionareoms en el menú desplegable la opción "Crear nuevo par de claves" y les pondremos un nombre. A continuación nos las descargaremos.

![Creando claves](img/creandoclaves.png)

Después de haberlas creado y descargado, seleccionaremos la opcion en el desplegable que nos permita utilizar un par de claves ya existentes y las utilizaremos para lanzar la instancia. 

Si regresamos a nuestra página de instancias, deberíamos ver que ahora sí, tenemos una isntancia funcionando. 

![Página de instancias](img/paginstancias.png)
___
___

## Paso 2 - Conectarse a nuestra instancia desde la terminal, MATE o consola.
___
___

Lo primero que deberemos hacer para iniciar el proceso de instalación y configuración de aquellos servicios que necesitemos será conectarnos a nuestra instancia. 

Lo haremos desde la terminal (o consola).

Introduciremos lo siguiente:

```
ssh -i "clavesJuan.pem" ubuntu@52.91.148.181
```

En este caso en concreto, "clavesJuan.pem" se correspone al nombre de nuestro archivo de claves, y ubuntu@52.91.148.181 se corresponde en primer lugar al prefijo con el que debamos conectarnos a nuestra instancia (dependerá de la distribución que seleccionasemos al configurarla) y la IP es la que nos asigne Amazon.

En caso de que tengamos dudas de dónde encontrar esta información, podemos remitirnos a la imagen anterior y buscar la IP en el apartado "Dirección IP" en la barra horizontal.
___

### TroubleShooting

1. **En caso de que no tengamos las claves en el directorio raíz**

Haremos una de dos cosas, o accederemos a la carpeta en la que tengamos las claves

En este caso introduciendo lo siguiente en la consola

```
cd Escritorio
cd Ejercicio1
```

Lo haremos porque, de forma predeterminada, la consola de linux está en el directorio raíz. Si quisieramos hacerlo de forma que pudieramos exportar la instrucción a otros ordenadores, pondríamos la ruta de forma relativa:

```
"Escritorio/Ejercicio1/clavesJuan.pem"
```

2. **En caso de que nos enfrentemos al error "WARNING: UNPROTECTED KEY FILE"**

Si nuestro archivo de claves no tiene los permisos necesarios puede aparecernos este error. En este caso introduciremos lo siguiente en la consola para otorgarnos permisos:

```
chmod 700 clavesJuan.pem
```
___

Si todo ha salido bien, deberiamos ver algo así:

![Conectando desde terminal](img/conectandodesdeterminal.png)

Ahora ya podríamos comenzar a instalar aquellos servicios que necesitemos. 
___
___

### Paso 3 - Instalando los servicios que necesitemos
___
___

#### Paso 3.1 Instalar APACHE

Deberemos instalar apache en nuestra instancia, lo haremos primero utilizando el comando update para asegurarnos de que todo va a estar actualizado.

```
sudo apt update
```

Esto iniciará el proceso de actualización, en caso de que pare y nos haga una pregunta, probablemente sea para solicitarnos más espacio en el disco duro, si queremos dárselo pondremos la letra "Y" y presionaremos la tecla "Enter".

Cuando acabe el proceso de actualización instalaremos apache, para hacerlo introduciremos lo siguiente en la consola:

```
sudo apt install apache2
```

Como en el caso anterior, esto iniciará el proceso de instalación de Apache.

Si queremos comprobar que lo que hemos hecho ha tenido efecto, podemos ir a nuestro navegador web e introducir nuestra IP pública, recordemos que es la que aparece listada en la página de instancias activas de Amazon AWS.

En la barra de búsqueda introduciríamos lo siguiente (en este caso en concreto):

```
http://52.91.148.181/
```

Si hemos hecho todo correctamente, deberíamos ver una página igual o parecida a la siguiente:

![Default Page](img/default.png)


#### Paso 3.2 Instalar MySQL Server

Ya tenemos instalado un servidor Apache, ahora instalaremos un sistema gestor de base de datos, en este caso MYSQL Server.

Para hacerlo introduciremos la siguiente instrucción:

```
sudo apt install mysql-server
```

Esta instrucción iniciará el proceso de instalación de este servicio, como en el caso anterior, si necesita más espacio nos lo pedirá y le diremos que sí.

Para comprobar que podemos acceder escribiremos la siguiente instrucción en la consola:

```
sudo mysql
```

Debería ocurrir lo siguiente:

![Enter SQL](img/sudosql.png)

Si queremos salir, bastará con escribir la instrucción "exit" y presionar la tecla "enter".


#### Paso 3.3 Instalar PHP

Instalaremos PHP que será el procesador de código que utilizaremos para mostrar nuestro contenido.

El proceso es similar a los anteriores.

Introduciremos las siguientes instrucciones:

```
sudo apt install php libapache2-mod-php php-mysql
```

Si queremos saber la versión que hemos instalado introduciremos lo siguiente:

```
php -v
```

A efectos de aprendizaje lo haremos y deberíamos ver algo así:

![Comprobar Version PHP](img/phpversion.png)


#### Paso 3.4 Instalar PHP My Admin

Como en los casos anteriores, introduciremos la siguiente instrucción en la consola:

```
sudo apt-get install phpmyadmin
```

Se iniciará el proceso de instalación que será un poco diferente a los anteriores.

![Configurar PHP My admin](img/configphpmyadmin.png)

Para movernos por el menú utilizaremos las flechas y para fijar la opción seleccionada utilizaremos la barra de espacio, una vez seleccionemos nuestra opción, en este caso "apache2", presionaremos la tecla enter.

Una vez aceptemos saldrá esta pantalla:

![Configurar database PHP](img/configdatabase.png)

En nuestro caso aceptamos y saldrá la siguiente pantalla solicitando que introduzcamos una contraseña:

![Comprobar Version PHP](img/configpass.png)

En nuestro caso la contraseña es "test".

Aceptamos, introducimos la confirmación de la contraseña y volvemos a aceptar, esto terminará de instalar PHPMYADMIN.

Si queremos probar que esto ha funcionado iremos a nuestro navegador web e introduciremos la DNS pública de nuestra instancia de Amazon AWS seguido de lo siguiente: "/phpmyadmin".

Es decir, en nuestro caso la búsqueda completa sería la siguiente:

```
ec2-52-91-148-181.compute-1.amazonaws.com/phpmyadmin
```

Esto nos llevará a la página home de phpmyadmin, recordemos que el usuario por defecto es "phpmyadmin" y la contraseña que hemos introducido es "test", y esto es lo que deberemos introducir para acceder

![Comprobar Version PHP](img/phpmyadminhome.png)

Con esto accederemos a la página principal de phpmyadmin, la siguiente:

![Comprobar Version PHP](img/phpmyadmin.png)

Como se podrá apreciar si se intenta hacer cualquier cosa, no vamos a tener permisos. 

Para solucionarlo intentaremos entrar con el usuario 'root' para darnos cuenta de que no sabemos cuál es la contraseña porque nunca la establecimos.

Para ponerle solución volveremos a la consola y escribiremos lo siguiente:

```
sudo mysql
```

Cuando estemos dentro de la consola de sql escribiremos lo siguiente para ver qué usuarios existen y qué tipo de autenticación tienen o requieren para acceder a phpmyadmin:

```
SELECT user,authentication_string,plugin,host FROM mysql.user;
```

Nos mostrará lo siguiente, y vemos que root pone "auth_socket":

![Root sin permisos](img/rootsinperm.png)


Lo cambiaremos con el siguiente comando que además nos servirá para establecer una nueva contraseña:

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'test';
```

Tras esto, volvemos a escribir la instrucción para ver qué tipo de autenticación requiere el usuario y vemos que ya hay una serie de caracteres y dígitos para el usuario root, ahora sí podremos conectarnos.

![Root sin permisos](img/rootconperm.png)

Para ahorrar tiempo en el futuro, vamos a importar la bbdd que utilizaremos para este ejemplo.

Presuponemos que tenemos un arhivo .txt con nuestra base de datos, así que en el menú horizontal seleccionaremos la pestaña sql e introduciremos el contenido del archivo (en esencia es una consulta):

![SQL QUERY](img/sqlquery.png)

Pulsamos el botón `go` y si nuestra consulta es correcta, debería crear la base de datos. 

___
### OJO 

En caso de que no funcione, probablemente nos informe de un error con código `1046`, esto es porque o no tenemos creada la base de datos cuyo nombre está intentando buscar, o no le hemos dicho cual usar, en ese orden las soluciones pasarían por insertar lo siguiente justo antes de la primera instrucción:

```
USE "database_name";
```

o 

```
CREATE "database_name";
````

En nuestro caso crearemos dos BBDD para utilizarlas como ejemplo de cómo acceder a webs diferentes desde una misma instancia pero con puertos distintos.


___
### Paso 3.5 - Instalando Filezilla

Filezilla es un cliente FTP y es el que utilizaremos para conectarnos a nuestra instancia EC2. 

Si no tenemos filezilla en nuestra computadora, utilizaremos el siguiente comando (siempre que el sistema operativo sea Ubuntu) para instalarlo:

```
sudo apt-get install filezilla
```


### Paso 4 - Configurando Filezilla

Vamos a configurar Filezilla para que se conecte a nuestra instancia. 

Abriremos filezilla, iremos a la pestaña "archivo > gestor de sitios" y deberíamos ver algo como lo siguiente, con algunas salvedades ya que los campos de texto estarán vacios:

![Filezilla](img/filezilla.png)

Dentro del apartado "Seleccionar el sitio" pulsaremos el boton "Nuevo sitio", en nuestro caso se llamará "ubuntu". 

En el apartado "General", configuraremos el gestor de sitios tal y como aparece en la imagen, evidentemente deberemos poner en el apartado servidor la IP pública de nuestra instancia.

Asimismo, cambiaremos el procotolo a SFTP.

En el apartado método de acceso seleccionaremos KeyFile y le indicaremos la ruta para que pueda encontrar nuestras claves públicas de Amazon AWS.

Pulsaremos en el botón "Aceptar" y ya deberíamos tener configurado nuestra conexión a EC2 con Filezilla. 

Es cierto que nos saldrá una pantalla preguntándonos si confiamos en nuestro host, le diremos que sí.

Para subir los archivos al servidor pulsaremos en la carpeta en la que tengamos nuestra web con el botón derecho y seleccionaremos la opción "subir archivos", esto creará una carpeta igual en el sitio remoto. 

Deberíamos subir estos archivos a la carpeta "/var/www/html", eso si, antes deberemos darnos permisos con los siguientes comandos (que ejecutaremos dentro de la instancia EC2 en la consola):

```
sudo chown -R ubuntu /var/www/html
sudo chmod -R 755 /var/www/html
```


Tras esto podremos pasar los archivos:

![Transfer](img/arrastrarfilezilla.png)

Es importante tener en cuenta varias cosas, cuando pretendamos ver nuestra web en un navegador, filezilla buscará por defecto un archivo index, si no encuentra uno simplemente dará un error, debemos indicarle cuál queremos ver si no se llama así. 

Si queremos meter varias webs, podemos soltar cada una en su respectiva carpeta y luego tenerlo en cuenta a la hora de poner la ruta en el buscador.

Si al intentar acceder desde un navegador nos sale este mensaje:

![Error](img/passerror.png)

Es porque en nuestro archivo de conexión a la bbdd probablemente tengamos otra contraseña que la que hemos puesto en nuestra instancia, si es así, editamos ese archivo en el repositorio virtual, pulsando con el botón derecho y seleccionando la opción `view/edit` y ponemos la nueva contraseña para que pueda acceder.

![view edit](img/viewedit.png)

En nuestro caso, la variable $pass estaba iniciada a un string vacio, le damos el valor de "pass" y ya estaría solucionado.

![change pass](img/changepass.png)
___

## Dos webs por dos puertos distintos

Digamos que queremos que, dentro de una misma instancia podamos ver más de una web accediendo desde puertos distintos.

Para esto, crearemos dos carpetas, cada una con un index.html

```
mkdir peluqueria
cd peluqueria
nano index.html
"Hola soy una peluquería"
```

y exactamente lo mismo en otra carpeta cambiando peluquería por bar.

Cuando lo tengamos, subimos esas carpetas al directorio remoto de filezilla, de modo que quede algo así:

![Filezilla directorios](img/filezillafinal.png)

Ahora vamos a crear los archivos necesarios para poder acceder a bar, blog y peluquería a través de puertos de nuestra elección, en este caso serán el 21000, el 22000 y el 23000.

Para ello lo primero que deberemos hacer será editar las reglas de entrada de nuestra instancia y añadir 3 reglas tcp personalizadas, y poner los puertos que hemos dicho:

![New Rules](img/newinbound.png)

Ahora, crearemos los archivos .conf en los que especificaremos qué puerto tiene que escuchar y la carpeta en el directorio remoto a la que debe rediriginos.

Para esto haremos lo siguiente:

```
cd /etc/apache2/sites-enabled/
```

Una vez aquí, ejecutaremos este comando para cada una de las webs que vayamos a querer ver por distintos puertos:

```
sudo a2ensite peluqueria
sudo a2ensite bar
sudo a2ensite blog
```

Después borraremos el archivo default que se encuentra en esta carpeta:

```
sudo a2dissite 000-default
```

Y por último reiniciaremos apache2 para que los cambios tengan efecto:

```
sudo systectl reload apache2
```

Desde el directorio "/sites-enabled" que es en el que nos encontramos, nos desplazaremos al de "sites-available", lo haremos así:

```
cd ..
cd sites-available
```

Ahora crearemos un archivo de configuración para cada una de las webs que queramos ver por distintos puertos:


```
sudo nano peluqueria.conf
```

y 

```
sudo nano bar.conf
```

y 

```
sudo nano blog.conf
```

Solo pondremos uno de los ejemplos para ahorrar tiempo:

```
Listen 21000
<VirtuaHost *:21000>
  DocumentRoot /var/www/html/blog
</VirtualHost>
```

Para cada caso concreto, cambiaremos el número del puerto y el directorio, en este caso es "blog", y en los otros será "peluquería" y "bar".

Es importante prestar atención a la sintáxis de este archivo, cualquier error nos va a dar problemas en un futuro.

Ahora vamos a un navegador y lo probamos:

Para el puerto 21000:

![New Rules](img/21000.png)


Para el puerto 22000:

![New Rules](img/22000.png)

Para el puerto 23000:

![New Rules](img/23000.png)
____

## Autenticación

Tenemos dos opciones, la BASIC y la DIGEST

### BASIC

Vamos a hacerlo en nuestro documento .conf de la página del blog, así que accederemos a este como en el apartado anterior.

```
cd /etc/apache2/
```

Una vez estemos en este directorio, crearemos la carpeta password escribiento lo siguiente:

```
sudo mkdir /etc/apache2/password
```

y luego escribiremos la siguiente instrucción:

```
sudo htpasswd -c /etc/apache2/password/passwords admin
```

Nos pedirá que introduzcamos una contraseña y en este caso será `test`.

Ahora abriremos el archivo blog.conf que hemos creado anteriormente:

```
sudo nano blog.conf
```

En este archivo tendremos la configuración del puerto que hemos escrito anteriormente, y deberemos añadirle lo siguiente:

```
Listen 21000
<VirtualHost *:21000>
  DocumentRoot /var/www/html/blog
</VirtualHost>

<Directory "/var/www/html/blog/admin">
 AuthType Basic
 AuthName "Administracion"
 AuthUserFile /etc/httpd/password/paswords
 Require valid-user
</Directory>
```

Para comprobar que la sintáxis es correcta podemos utilizar el siguiente comando:

```
sudo apachectl configtest
```
y si todo ha ido bien nos dirá textualmente "Syntax OK".

Reiniciamos httpd y ya está:

```
sudo systemctl restart httpd
```

## DIGEST

Esto lo vamos a probar con la web de la peluquería.

Crearemos una carpeta en el respositorio virtual que se llame "admin", ya sea de forma manual en filezilla o desde la propia consola, pero debe estar hecha.

Desde el directorio raíz en nuestra consola escribiremos lo siguiente:

```
sudo htdigest -c /etc/apache2/password/digest "Configuracion" admin
```

Nos pedirá una contraseña que còmo no, también será `test`.

Una vez hecho esto accederemos al archivo .conf de peluquería y como en el caso anterior, añadiremos algunas cosas de modo que quede así:

```
Listen 23000
<VirtualHost *:23000>
  DocumentRoot /var/www/html/peluqueria
</VirtualHost>

<Directory "/var/www/html/peluqueria/admin/">
  AuthType Digest
  AuthName "Configuracion"
  AuthUserFile /etc/httpd/password/digest
  Require valid-user
</Directory>
```

Guardamos y salimos y si queremos comprobar que la sintáxis está bien lo hacemos con el comando:

```
sudo apachectl configtest
```

Si lo está, escribimos lo siguiente para reiniciar httpd:

```
sudo systemctl restart httpd
```

y ya debería funcionar.

___





