# Configuracion de cliente Linux en servidor Nagios CentOs 7
## Obtención de OID para la deficion de los comandos

*   Se pueden seleccionar los servicios obteniendo su *OID* desde el cliente, para eso se utiliza el siguiente comando desde nuestro servidor:
    
    `snmpwalk -v 1 -c comunidad_creada dir_ip | grep "filtro" | more`
    
    Donde:

    *   **-c**, es para especificar la comunidad especificada en el cliente.
    *   **comunidad_creada**, es el nombre de la comunidad, en este caso *publica*.
    *   **dir_ip**, la dirección ip del cliente configurado.
    *   **grep**, es una utileria del sistema que nos permite realizar un filtrado en los resultados.
    *   **filtro**, es el filtro que usaremos con la utiliria grep, este podrá ser una expresión regular o como en nuestro caso una cadena de texto.
    *   **more**, es para poder observar detenidamente los elementos listados.

    Comando:
    *   El comando queda de la siguiente manera:  
        `snmpwalk -v 1 -c publica 192.168.1.67 | grep "HOST-RESOURCES-MIB" | more`

*   El comando anterior despliega una lista de elementos de la cual obtendremos los *OIDs* que se pueden utilizar, en este caso se selcciona uno de ellos y ejecutamos el siguiente comando:

    `snmptranslate -On Elemento_seleccionado`
    
    quedando:

    `snmptranslate -On HOST-RESOURCES-MIB::hrStorageSize.1`

*   El comando anterior nos regresa una cadena con el *OID* que usaremos para el comando que usa nagios para verificar el servicio con *snmp*.


## Comandos para el monitoreo del servidor/cliente a través del protocolo *snmp*
*   En la carpeta *Archivos* del repositorio se ha agregado la definicion de los comandos utilizados para el monitoreo a través de *SNMP* en el archivo *commands.cfg*.

# Definicion del archivo para el cliente
*   Se crea el archivo *c1_debian_10.cfg* con privilegios de root en la carpeta objects de nagios.
    
    ```
    $ su
    # cd /usr/local/nagios/etc/objects
    # touch c1_debian_10.cfg
    ```


## Definición del Host
*   Se le agrega la definicion del host al archivo *c1_debian_10.cfg* de la siguiente manera
    ```
    define host {
        use                          linux-server
        host_name                    c1_Debian_10
        alias                        192_168_1_67
        address                      192.168.1.67
        register                     1
        contact_groups               admins
    }
    ```

## Definicion de los servicios a monitorear en el cliente
*   En la carpeta *Archivos* del repositorio se ha agregado el archivo del cliente *debian 10*, este archivo contiene la definición utlizada para cada servicio que es monitoreado a través de *SNMP*, en estas definiciones se utilizan los comandos descritos en el archivo *commands.cfg*.