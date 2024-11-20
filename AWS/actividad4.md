# Instalación de un servidor web
Como servidor web vamos a instalar APACHE, con el lenguaje de programación PHP.

## Instalar APACHE
Los pasos a seguir son:
1. Comprobar que el sistema esté actualizado
```sh
apt update
```
2. Instalar apache
```sh
apt install apache2
# comprobar que arranca el servicio y se encuentra activo
systemctl status apache2
```
3. Comprobar que funciona correctamente. En cualquier navegador acceder por el `DNS` o por la `ip pública` de la instancia. Nos muestra la página web por defecto de Apache 
Ejemplo: 
http://ec2-3-89-85-127.compute-1.amazonaws.com/
![imagen funciona](apache.jpg)

4. Configuración de un host virtual. 
Un host virtual en Apache2 permite alojar múltiples sitios web en un solo servidor. Cada sitio puede tener su propio dominio y configuración. Hay dos tipos de hosts virtuales:

1. **Hosts virtuales basados en nombre**: Permiten que varios dominios compartan la misma dirección IP.
2. **Hosts virtuales basados en IP**: Cada dominio tiene su propia dirección IP.

Aquí tienes un ejemplo de configuración de un host virtual basado en nombre:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@ejemplo.com
    ServerName www.ejemplo.com
    ServerAlias ejemplo.com
    DocumentRoot /var/www/ejemplo.com/public_html
    ErrorLog ${APACHE_LOG_DIR}/ejemplo.com-error.log
    CustomLog ${APACHE_LOG_DIR}/ejemplo.com-access.log combined
</VirtualHost>
```

Guarda esta configuración en un archivo dentro de `/etc/apache2/sites-available/` y luego habilítalo con:

```sh
sudo a2ensite nombre_del_archivo.conf
sudo systemctl reload apache2
```
En Ubuntu tiene habilitado un bloque de servidor por defecto, que esta configurado para proporcionar documentos del directorio /var/www/html. Sin embargo, nosotros vamos a crear otro directorio de publicación web.
Realizamos los siguientes pasos:
```sh
// Crearemos un directorio dentro de /var/www que denominaremos miWeb

mkdir /var/www/miWeb

// permisos adecuados al directorio , por ejemplo 755 ( permitir al propietario leer, escribir y ejecutar los archivos, y solo permisos de lectura y ejecución a los grupos de terceros)

chmod -R 755 /var/www/miWeb

```
A continuación,  crear una página de ejemplo denominada `index.html` por ejemplo:
crear una página HTML5 simple que muestre un mensaje de bienvenida a la página web "miWeb", puedes usar el siguiente código:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bienvenida a miWeb</title>
</head>
<body>
    <h1>Bienvenida a la página web miWeb</h1>
</body>
</html>
```
Guarda este contenido en un archivo llamado `index.html` dentro del directorio `/var/www/miWeb`. Podemos utilizar el editor de archivos nano

```sh
cd /var/www/miWeb
nano index.html # copiamos el código html

```
En lugar de modificar la configuración por defecto que se encuentra en `/etc/apache2/sites-available/000-default.conf` lo copiaremos creando nuestro fichero de configuración.
* el fichero a crear tiene que tener la misma extensión `.conf`
* debemos cambiar la directiva `DocumentRoot` con nuestro directorio

```sh
cd /etc/apache2/sites-available/
cp 000-default.conf miWeb.conf
nano miWeb.conf # cambiar DocumentRoot
```
5. Podemos comprobar que no hemos cometido errores de sintaxis en el fichero de configuración creado
```sh
apache2ctl configtest
```

6. Para que la configuración realizada la tenga en cuenta el sistema requiere que sea habilitada
```sh
a2ensite miWeb.conf
```
7. Deshabilitamos el sitio por defecto
```sh
a2dissite 000-default.conf
```
8. Hay que reiniciar Apache para que tenga efecto la configuración realizada
```sh
systemctl restart apache2
# comprobar que no hay fallos
systemctl status apache
```
9. En el navegador comprobar que atiende a nuestra página
http://ec2-3-89-85-127.compute-1.amazonaws.com/
![imagen funciona](miPagina.jpg)

10. Ahora debemos configurar un firewall para garantizar que solo el tráfico necesario pueda llegar a su proxy inverso. Usaremos Uncomplicated Firewall (UFW) para este propósito.
```sh
# comprobamos si está ya instalado en el sistema y en que estado está
ufw status
# si hubiera que instalarlo ejecutamos el comando siguiente
apt install ufw
# ponerlo activo. !!Ojo!! antes de ponerlo activo hay que añadir las aplicaciones que queramos que filtre, para ver las aplicaciones es **ufw app list** y para añadir las aplicaciones al cortafuegos **ufw alow nombre_aplicación**
ufw app list
# daremos de alta el SSH  y el HTTP 
ufw allow OpenSSH
ufw allow  Apache 
ufw enable
# comprobamos que esta correcto
ufw status
```
Para comprobar que todo esta correcto podemos acceder de nuevo a la página web. 

### Instalar php como lenguaje de programación
Los pasos a seguir son:
1. Comprobar que el sistema esté actualizado
```sh
apt update
```
2. Instalar php 
```sh
apt install php libapache2-mod-php php-mysql

```
3. Configurar php
Para configurar este lenguaje de programación trabajaremos en primer lugar sobre el archivo de configuración del módulo del servicio web. Si has instalado la versión incluida en Ubuntu 24.04 LTS. El fichero de configuración `php.ini` se encuentra en `/etc/php/8.3/apache2/php.ini`

Minimamente deberiamos poner la zona horaria del servidor directiva `date.timezone`  y la configuración por defecto del tratamiento de errores está para un entorno de producción  si lo queremos para un entorno de desarrollo debería ser cambiada.
```sh
nano /etc/php/8.3/apache2/php.ini
# cambiar la zona horaria
date.timezone=Europe/Madrid
# errores para desarrollo
error_reporting= E_All
display_errors= On
display_startup_errors= On
# para que los cambios se tengan en cuenta realizar
systemctl reload apache2
```

4. Para comprobar PHP  creamos en una página denominada `info.php` que tendrá la siguiente información:
```php
<?php
phpinfo();
?>
```
los pasos son:
```sh
# voy al directorio donde poner mi página
cd /var/www/miWeb
# creo mi página info.php
nano info.php #copio la información de la página
```
En el navegador vamos a llamar a la página para comprobar que todo esta correcto
Ejemplo
![imagen funciona](infoPHP.jpg)

## Instalar la Base de datos




## Instalar NGINX
Los pasos a seguir son:
1. Comprobar que el sistema esté actualizado
```sh
apt update
```
2. Instalar Nginx
```sh
apt install nginx
# comprobar que arranca el servicio y se encuentra activo
systemctl status nginx
```
3. Comprobar que funciona correctamente. En cualquier navegador acceder por el `DNS` o por la `ip pública` de la instancia. Nos muestra la página web por defecto de NGINX 
Ejemplo: 
http://ec2-52-90-122-82.compute-1.amazonaws.com

![imagen funciona](servidor.jpg)

1. Configurar el servidor web por defecto 
La instalación se realiza a partir del directorio  `/etc/nginx`, donde encontramos toda la configuración inicial del servidor web. La configuración por defecto del servidor la encontramos en **el fichero default** que se encuentra en: `/etc/nginx/sites-available/default`.
Dentro de este archivo, se  configura Nginx para adaptarse a nuestros requisitos específicos. Nos permite definir bloques de servidor, certificados SSL y otras directivas para optimizar el rendimiento y la seguridad de su configuración de proxy inverso. Pero ahora vamos a editar la configuración para comentar la línea relacionada con IPv6, guardar los cambios y salir del archivo.
```sh
nano  /etc/nginx/sites-available/default
```
El resultado es ![quitaripv6](comentarIP6.jpg)
1. Podemos comprobar que la modificación en el fichero esta bien realizada con el comando dpkg o bien con el que tiene el propio nginx `nginx -t `
```sh
dpkg --configure -a
nginx -t
```
1. Comprobar que el servicio está activo
```sh
service nginx status
systemctl status nginx
```
1. Ahora debemos configurar un firewall para garantizar que solo el tráfico necesario pueda llegar a su proxy inverso. Usaremos Uncomplicated Firewall (UFW) para este propósito.
```sh
# comprobamos si está ya instalado en el sistema y en que estado está
ufw status
# si hubiera que instalarlo ejecutamos el comando siguiente
apt install ufw
# ponerlo activo. !!Ojo!! antes de ponerlo activo hay que añadir las aplicaciones que queramos que filtre, para ver las aplicaciones es **ufw app list** y para añadir las aplicaciones al cortafuegos **ufw alow nombre_aplicación**
ufw app list
# daremos de alta el SSH  y el HTTP 
ufw allow OpenSSH
ufw allow  'Nginx HTTP' 
ufw enable
# comprobamos que esta correcto
ufw status
```
Para comprobar que todo esta correcto podemos acceder de nuevo a la página web de NGINX. 

## Instalación de un proxy inverso en NGINX
Una de las funciones fundamentales de un proxy inverso es actuar como un buffer protector, protegiendo a los servidores backend interceptando y gestionando las solicitudes de los clientes. De esta manera, oculta de forma eficaz información valiosa sobre los servidores, como sus direcciones IP y otros detalles confidenciales. Esto añade una capa adicional de seguridad a su infraestructura, lo que dificulta que los posibles atacantes ataquen directamente a sus servidores.

1. Crear el archivo de configuración con el nombre de nuestro dominio.

Dentro de este archivo, definimos la configuración específica para el proxy: nombre del servidor, puertos de escucha y la ubicación donde se reenviarán las solicitudes entrantes. Podemos partir realizando una copia del fichero default:
```sh
cd /etc/nginx/sites-available
cp default ec2-52-90-122-82.compute-1.amazonaws.com.com
nano ec2-52-90-122-82.compute-1.amazonaws.com.com
```
En la edición del fichero en el `server_name` podemos poner la dirección privada  y en el `proxy_pass` la dirección interna a la que atiende. Además el proxy se configura mediante el archivo /etc/nginx/proxy_params. La configuración predeterminada es adecuada para este ejemplo, pero puede cambiarse. También vamos a cambiar el `root` estableciendo la ruta en donde se va encontrar nuestra web /var/www/miWeb
El resultado es ![configurarproxy](proxy.jpg)

2. Habilitar el proxy realizando un enlace simbólico. Un enlace simbólico, también conocido como enlace simbólico o enlace suave, es un tipo especial de archivo que actúa como un puntero o referencia a otro archivo o directorio en el sistema de archivos. Le permite crear un acceso directo a un archivo o directorio, proporcionando una forma conveniente de acceder o hacer referencia a él desde una ubicación diferente.
```sh
ln -s /etc/nginx/sites-available/ec2-52-90-122-82.compute-1.amazonaws.com.com /etc/nginx/sites-enabled/
```
3. Verificar que no hemos roto nada
```sh
nginx -t