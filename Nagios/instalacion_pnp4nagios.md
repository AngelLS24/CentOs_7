# Configuración de PNP4Nagios
## Instalación
*   Cambiamos a root
    
    `su`
*   Se instalan las dependencias necesarias para el funcionamiento de PNP4Nagios
    
    `yum install rrdtool perl-Time-HiRes rrdtool-perl php-gd`
*   Cambiamos de directorio
    
    `cd /opt` 

*   Se descarga el archivo *.tar.gz* que contiene los archivo fuente
    
    `wget https://sourceforge.net/projects/pnp4nagios/files/latest/download/pnp4nagios-0.6.26.tar`
*   Se estrae el contenido del archivo *.tar.gz*
    
    `tar zxfv pnp4nagios-0.6.26.tar`

*   Cambiamos al directorio que contiene los archivos descomprimidos
    
    `cd pnp4nagios-0.6.26`
*   Se validan las dependencias

    `./configure`
*   Se realiza la instalación
    `make all && make fullinstall`

*   Se configura que se inicie cada que se reinicie el equipo
    
    `chkconfig --add npcd && chkconfig --level 35 npcd on`
*   Se reinicia Apache
    
    `systemctl reload httpd.service`
*   Verificamos la configuración realizada entrando a la interfaz web

    `http://redes-ico.ddns.net/pnp4nagios/`
*   Deshabilitamos la página que muestra la configuración realizada, cambiandole el nombre al archivo

    `mv /usr/local/pnp4nagios/share/install.php /usr/local/pnp4nagios/share/install.php.original`

## Configuración con Nagios
*   Verificamos que *process_performance_data* sea igual a **1** en el archivo de configuración de nagios
    *   Abrimos el archivo con vim
        
        `vim /usr/local/nagios/etc/nagios.cfg`
    *   Al arbir el archivo se asegura de que el valor sea 1, en caso de tener otro valor, se debe de reemplazar por 1
        ```
        process_performance_data=1
        ```
    *   Agregamos lo siguiente al final del archivo
        ```
        #
        # Bulk / NPCD mode
        #
        # *** the template definition differs from the one in the original nagios.cfg
        #
        service_perfdata_file=/usr/local/pnp4nagios/var/service-perfdata
        service_perfdata_file_template=DATATYPE::SERVICEPERFDATA\tTIMET::$TIMET$\tHOSTNAME::$HOSTNAME$\tSERVICEDESC::$SERVICEDESC$\tSERVICEPERFDATA::$SERVICEPERFDATA$\tSERVICECHECKCOMMAND::$SERVICECHECKCOMMAND$\tHOSTSTATE::$HOSTSTATE$\tHOSTSTATETYPE::$HOSTSTATETYPE$\tSERVICESTATE::$SERVICESTATE$\tSERVICESTATETYPE::$SERVICESTATETYPE$
        service_perfdata_file_mode=a
        service_perfdata_file_processing_interval=15
        service_perfdata_file_processing_command=process-service-perfdata-file
        # *** the template definition differs from the one in the original nagios.cfg
        #
        host_perfdata_file=/usr/local/pnp4nagios/var/host-perfdata
        host_perfdata_file_template=DATATYPE::HOSTPERFDATA\tTIMET::$TIMET$\tHOSTNAME::$HOSTNAME$\tHOSTPERFDATA::$HOSTPERFDATA$\tHOSTCHECKCOMMAND::$HOSTCHECKCOMMAND$\tHOSTSTATE::$HOSTSTATE$\tHOSTSTATETYPE::$HOSTSTATETYPE$
        host_perfdata_file_mode=a
        host_perfdata_file_processing_interval=15
        host_perfdata_file_processing_command=process-host-perfdata-file
        ```

*   Se agregan los comandos necearios para el funcionamiento de *PNP4Nagios*

    *   Se abre el archivo con vim
        
        `vim /usr/local/nagios/etc/objects/commands.cfg`
    *   Agregamos la definición de los comandos al archivo
        ```
        define command {
            command_name process-service-perfdata-file
            command_line /bin/mv /usr/local/pnp4nagios/var/service-perfdata /usr/local/pnp4nagios/var/spool/service-perfdata.$TIMET$
        }
        define command {
            command_name process-host-perfdata-file
            command_line /bin/mv /usr/local/pnp4nagios/var/host-perfdata /usr/local/pnp4nagios/var/spool/host-perfdata.$TIMET$
        }
        ```

* Creamos la plantilla que activa el *"action_url"*, esto nos da el enlace directo a PNP4Nagios
    *   Abrimos el archivo con vim
        
        `vim /usr/local/nagios/etc/objects/templates.cfg`
    *   Agregamos la siguiente definición para los hosts y los servicios
        ```
        define host {
            name host-pnp
            action_url /pnp4nagios/index.php/graph?host=$HOSTNAME$&srv=_HOST_' class='tips' rel='/pnp4nagios/index.php/popup?host=$HOSTNAME$&srv=_HOST_
            register 0
        }
        define service {
            name srv-pnp
            action_url /pnp4nagios/index.php/graph?host=$HOSTNAME$&srv=$SERVICEDESC$' class='tips' rel='/pnp4nagios/index.php/popup?host=$HOSTNAME$&srv=$SERVICEDESC$
            register 0
        }
        ```
## Configuración en el archivo del cliente

Es cada uno de los clientes en los que queramos la graficación con PNP4Nagios se debe configurar de la siguiente manera
*   Para el host se agrega el uso de la plantilla *host-pnp* a la definición del host
    
    `use linux-server,host-pnp`
*   Para cada servicio monitoreado del host en el que se quira obtener su gráfica se agrega el uso de la plantilla *srv-pnp* de la siguiente manera
    
    `use local-service,srv-pnp`
*   Se reinician los servicios

    `systemctl restart npcd && systemctl restart nagios`
*   Copiamos la siguiente carpeta, esta nos muestra el icono del *"action_url"* en miniatura
    
    `cp /opt/pnp4nagios-0.6.26/contrib/ssi/status-header.ssi /usr/local/nagios/share/ssi/`
*   Volvemos a reiniciar los servicios y con esto queda configurado PNP4Nagios
    
    `systemctl restart npcd && systemctl restart nagios`