# Instalación de Nagios Core en CentOs 7
## Desactivar SELinux
*   Se pusede deshabilitar temporalmente SELinux, para esto sólo se requiere el siguiente comando

    `sudo setenforce 0`
*   Otra opción es deshabilitarlo completamente en archivo de configuración
    *   Se abre el archivo

        `sudo vim /etc/selinux/config`
    *   Se edita el archivo

        ```
        De:
            SELINUX=enforcing
        
        A:
            SELINUX=disabled
        ```
    *   Se reinicia el servidor

        `sudo reboot`
    * Se verifica que se haya realizado el cambio correctamente

        ```
        sestatus

        Salida:

        SELinux status:                 disabled
        ```

## Instalación de paquetes
*   Se instalan los paquetes necesarios que utiliza nagios

    `sudo yum install php php-cli gcc glibc glibc-common gd gd-devel net-snmp openssl-devel wget`

    `sudo yum install make gettext autoconf net-snmp-utils epel-release perl-Net-SNMP postfix unzip automake`

## Instalación de Nagios
*   Se cambia al directorio don de se localizarán los archivos fuente de Nagios

    `cd /usr/src/`
*   Se descarga Nagios de su repositorio

    `sudo wget https://github.com/NagiosEnterprises/nagioscore/archive/nagios-4.4.6.tar.gz`
*   Se extraen los archivos del archivo *tar.gz*

    `sudo tar zxf nagios-*.tar.gz`
*   Cambiamos al directorio que se genero del *tar.gz*
    
    `cd nagioscore-nagios-*/`
*   Dentro del directorio se encuentra el script *configure*, este script verifica que se encuentren todas las dependencias necesarias instaladas

    `sudo ./configure`
*   Se inicia la compilación 
    
    `sudo make all`
*   Se crea el usuario y el grupo del usuario nagios
    
    `sudo make install-groups-users`

    Salida:
    ```
    groupadd -r nagios
    useradd -g nagios nagios
    ```
*   Se agrega al usuario *apache* al grupo de *nagios*

    `sudo usermod -a -G nagios apache`
*   Se instalan los archivos binarios, CGIs y los archivos HTML de *nagios* 
    
    `sudo make install`
*   Se crea el directorio de comandos externos, esto es necesario para que nagios pueda procesar comandos desde aplicaciones externas. Este comando crea el directorio y establece los permisos adecuados

    `sudo make install-commandmode`
*   Se crean los ejemplos de archivos de configuracion que serviran para definir los hosts, servicios, etc.
    
    `sudo make install-config`
*   Se instalan los archivos de configuración del servidor web Apache
    
    `sudo make install-webconf`
*   Se instala el archivo que utiliza systemd para iniciar el servicio, de igual manera habilita que el servicio se inicie cada vez que se inicia la máquina

    `sudo make install-daemoninit`
*   Se crea el usuario *nagiosadmin* que se usará para ingresar a la interfaz web de Nagios

    `sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin`

    Salida:
    ```
    New password:
    Re-type new password:
    Adding password for user nagiosadmin
    ```
*   Se reinicia Apache

    `sudo systemctl restart httpd`
# Instalación de los plugins
*   Se descarga el archivo que contiene los plugins

    `sudo wget -O nagios-plugins.tar.gz https://github.com/nagios-plugins/nagios-plugins/archive/release-2.2.1.tar.gz`
*   Se extrae el contenido del archivo *tar.gz*
    
    `sudo tar zxf nagios-plugins.tar.gz`
*   Se cambia al directorio que contiene los plugins
    
    `cd nagios-plugins-release-2.2.1`
*   Se compilan e instalan los plugins
    ```
    sudo ./tools/setup
    sudo ./configure
    sudo make
    sudo make install
    ```
*   Se inicia el servicio
    
    `sudo systemctl start nagios`

*   Verificamos el estado del servicio
    
    `sudo systemctl status nagios`
*   Finalmente accesamos a la interfaz web de nagios con el usuario y contraseña creados previamente
    
    `http://redes-ico.ddns.net/nagios`