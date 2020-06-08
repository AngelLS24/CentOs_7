# Configuracion de cliente Debian 10 Buster para nagios 

## IP: 192.168.1.67
## El primer paso es instalar los paquetes necesarios para la configuracion

*   Se instalan el demonio de snmp, el cliente y los archivos en desarrollo:}

    `root@debian:/# apt install snmp snmpd libsnmp-dev`

## Se edita el archivo *snmpd.conf*
### Se habilita el monitoreo de manera remota a traves del puerto 161
*   Se comenta la siguiente linea: 

    `agentAddress  udp:127.0.0.1:161`
*   Se descomenta la siguiente linea: 

    `agentAddress udp:161,udp6:[::1]:161` -> Esta linea pone a escucha el servidor a traves del puerto 161

### Se define la comunidad con la cual se permite el monitoreo

*   Se comentan las lineas de la comunidad que vienen por default(rocommunity y rocommunity6) y se agrega la comunidad que nosotros usaremos de la siguiente manera:
    ```
    # rocommunity public  default    -V systemonly
                                                    #  rocommunity6 is for IPv6
    # rocommunity6 public  default   -V systemonly

    ##################################################
    ##                                              ##
    ##      Definicion de la comunidad              ##
    ##                                              ##
    ##################################################

    rocommunity publica localhost

    rocommunity publica 192.168.1.0/24
    ```
*   Se cierra el archivo

### Finalmente
*   Se reinicia el servicio

    `systemctl restart snmpd`
*   Se habilita el servicio para cada reinicio

    `systemctl enable snmpd`
*   Se verifica que la interfaz este en escucha

    `netstat -nlpu | grep 161`
*   Verificamos si esta corriendo de manera correcta de la siguiente manera

    `snmpwalk -v2c -c publica localhost | head -5`
    ```
    iso.3.6.1.2.1.1.1.0 = STRING: "Linux debian 4.19.0-9-amd64 #1 SMP Debian 4.19.118-2 (2020-04-29) x86_64"
    iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
    iso.3.6.1.2.1.1.3.0 = Timeticks: (19152) 0:03:11.52
    iso.3.6.1.2.1.1.4.0 = STRING: "Me <me@example.org>"
    iso.3.6.1.2.1.1.5.0 = STRING: "debian"
    ```