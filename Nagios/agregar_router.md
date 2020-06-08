# Agregar router a nagios

*   abrimos el archivo **nagios.cfg**.
    > vi /usr/local/nagios/etc/nagios.cfg

*   Descomentamos la siguiente linea dentro del archivo.
    > cfg_file=/usr/local/nagios/etc/objects/switch.cfg

*   Nos cambiamos a la direccion donde esta el archivo.
    > cd /usr/local/nagios/etc/objects/

*   Guardamos una copia del archivo original.
    > cp switch.cfg switch.cfg.original

*   Modificamos el archivo del switch/router.
    > vim switch.cfg

*   Cambia cada ocurrencia del nombre del switch **linksys-srw224p** por **huawei-casa** dentro del archivo con sed. De igual manera se puede cambiar el alias.
    > sed -i 's/linksys-srw224p/router-casa/g' switch.cfg
    > sed -i 's/Linksys SRW224P Switch/total/g' switch.cfg

*   En este caso se ha dejado el contenido del archivo de la siguiente manera.
    ```
    define host {
        use                     generic-switch      
        host_name               router-casa         
        alias                   total               
        address                 192.168.100.1       
        hostgroups              switches            
    }
    define hostgroup {
        hostgroup_name          switches            
        alias                   Network Switches    
    }
    define service {
        use                     generic-service                 
        host_name               router-casa                     
        service_description     PING                            
        check_command           check_ping!200.0,20%!600.0,60%  
        check_interval          5                               
        retry_interval          1                               
    }
    ```

*   Verificamos que no haya errores en la congfiguracion.
    > /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg

*   Finalmente reiniciamos el servicio.
    > systectl restart nagios