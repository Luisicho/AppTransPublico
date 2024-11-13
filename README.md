# Primeros Pasos
Aquí estará el tutorial de instalación de la app utilizada para Transporte Publico del Estado de Nayarit

## Programas necesarios
Los programas que utiliza la app de Transporte Publico, así como el sistema operativo son los siguientes:
* [Windows Server](https://soporte.revolutionsoft.net/index.php/descargar-iso-windows-server/) 2019 Mayor o Menor
* Servicio IIS de Hosting a Web
* [Mysql Community Edition](https://downloads.mysql.com/archives/installer/) 5.7 o Menor
* [Mysql Workbench](https://dev.mysql.com/downloads/workbench/)
* [Microsoft Visual C++ Redistributable](https://learn.microsoft.com/es-es/cpp/windows/latest-supported-vc-redist?view=msvc-170)
* [Winrar](https://winrar.es/descargas/rar) o lector de paquetes ([7zip](https://www.7-zip.org/))
* [PHP](https://windows.php.net/downloads/releases/) 5.3 o Menor
* [Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) Windows 64
* [Visual Studio Code](https://code.visualstudio.com/) o algún lector de texto

## Antes de comenzar
El archivo .env, la base de datos y todas las claves de acceso son proporcionadas en la Secretaria de Movilidad y no se compartirán en este documento, únicamente es un manual para la instalación en la Nube Nayarit o en cualquier máquina virtual o servidor.

# Instalación de Mysql
## Instalación base
Se necesita realizar una instalación base de Mysql no voy a ondear mucho dentro de la instalación, más que la configuración del usuario base y de la base de datos de mysql

![Imgur](https://imgur.com/H9vwwkF.png)

## Características
Algunas características necesarias para el buen funcionamiento de este sistema son las siguientes.

![MYSQL](https://imgur.com/7y6x96q.png)

## Configuración de usuario
Dentro de la pantalla de configuración de Mysql es importante agregar un nuevo usuario además del usuario _root_ para el uso de la plataforma, este usuario se encuentra dentro del archivo .env del código.

![Crear Usuario](https://imgur.com/1q2Ve7D.png)

## Configuración My.init
Para continuar es necesario configurar el archivo _my.init_ de mysql en Windows, este archivo es esencial para la configuración, se encuentra en la ruta `C:\ProgramData\MySQL\MySQL Server X.X`

![My.init](https://imgur.com/0YiNrMj.png)

Al abrir el archivo es necesario colocar las siguientes líneas de código en su lugar especifico para el correcto funcionamiento de la base de datos.

```XML
[client]
default-character-set= utf8

[mysql]
default-character-set= utf8

[mysqld]
collation-server=utf8_unicode_ci
character-set-server=utf8
```

## Carga de Base de Datos
En Mysql el respaldo de una base de datos es generada dentro de un archivo _.sql_ este archivo contiene los registros, tablas y todo lo esencial para crear una base de datos, estos archivos de respaldo se encuentran en la Secretaria de Movilidad, aquí únicamente se hace mención de esto para tenerlo en cuenta durante la instalación.

# Instalación PHP
Para la instalación de PHP es fundamental una configuración dentro del código PHP que permite utilizar php para web, su instalación es básica solamente se extrae la carpeta PHP a la raíz de Windows y listo.

![PHP Install](https://imgur.com/uPM7TI2.png)

## Configuración php.ini
Dentro de la carpeta que acabamos de extraer donde se encuentran todos los archivos de PHP necesitaremos crear el archivo `php.ini` este es el archivo de configuración de PHP normalmente renombraremos el archivo ya existente `php.ini-development` a `php.ini` el cual abriremos para configurar.

![php.ini](https://imgur.com/MlUNwsU.png)

En la última línea de código de este archivo agregaremos los siguiente para la configuración de Webapi

```XML
[WebPIChanges]
error_log=C:\Windows\temp\PHP_errors.log
upload_tmp_dir=C:\Windows\temp
session.save_path=C:\Windows\temp
cgi.force_redirect=0
cgi.fix_pathinfo=1
fastcgi.impersonate=1
fastcgi.logging=0
max_execution_time=300
date.timezone=America/Tegucigalpa
extension_dir="{Ruta a tu PHP\ext\}"

[ExtensionList]
extension=php_mysql.dll
extension=php_mysqli.dll
extension=php_mbstring.dll
extension=php_gd2.dll
extension=php_gettext.dll
extension=php_curl.dll
extension=php_exif.dll
extension=php_xmlrpc.dll
extension=php_openssl.dll
extension=php_soap.dll
extension=php_pdo_mysql.dll
extension=php_pdo_sqlite.dll
extension=php_imap.dll
extension=php_tidy.dll

[PHP_WINCACHE]
extension=php_wincache.dll
extension=php_pdo_sqlsrv.dll
extension=php_sqlsrv.dll
```

# Instalación IIS y Componentes
La instalación de IIS es protocolaria, sin embargo, es necesario instalar los siguientes componentes para que exista una funcionalidad en el sistema, estos componentes son los siguientes.

![IIS INSTALL](https://imgur.com/CgyQOwt.png)
![IIS INSTALL 2](https://imgur.com/PMBWDLV.png)
![IIS INSTALL 3](https://imgur.com/bbfry79.png)

También es importante contar con la característica `SMB 1.0` activada.

![smb 1.0](https://imgur.com/KKECt9B.png)

## Configuración IIS
Para configurar una página nueva que sea utilizable en IIS primero se necesita crear una página nueva.

![IIS IMAG](https://imgur.com/plMuun6.png)

## URL Rewrite
Al crear esta página nueva es importante instalar la característica `URL Rewrite` dicha es fácil de instalar, simplemente seguimos el cuadro de instalación.

![Rewrite](https://imgur.com/yLVyMBc.png)

## Configuración Handler Mappings
Para poder utilizar `PHP` dentro de las páginas web se necesita configurar los servicios de _Handler Mappings_ estos pueden ser configurados vía comando o con el asistente de IIS.

### Via comando
El comando de configuración para los _Handler Mappings_ es el siguiente `cd %windir%\system32\inetsrv` después ejecutamos el comando `appcmd set config /section:system.webServer/handlers /+[name='PHP_via_FastCGI',path='*.php',verb='*',modules='FastCgiModule',scriptProcessor='c:{php_folder}php-cgi.exe',resourceType='Unspecified']` donde `scriptProcessor` debe ser modificado para contener la ruta a nuestro ejecutable `php-cgi.exe` de la versión de PHP instalada en la PC con anterioridad.

![Handler](https://imgur.com/OwBlKs8.png)

### Via asistente
La forma de crear un _Handler Mappings_ es sencilla y solamente requiere de seguir los siguientes pasos:
* click en `Add Module Mapping...`
* Completar las casillas de la siguiente forma

![FastCGI](https://imgur.com/5x4TXa2.png)

# Conclusión
El sistema de transporte público es un sistema encargado de mantener, gestionar y utilizar altas, bajas, concesiones y todos los tramites que se realizan en el departamento de transporte público de la [Secretaria de Movilidad](https://semovi.nayarit.gob.mx/), mucho de este sistema era viejo y se montó a los servidores AWS de la secretaria, la configuración de AWS, creación de EC2 y RDS se presentó e informo solamente a los directivos del departamento de informática a fin de salvaguardar la integridad y mantener los protocolos de seguridad necesarios.

![Transporte Publico](https://imgur.com/T8l2tl0.png)
