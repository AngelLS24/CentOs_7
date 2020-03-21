## Darle privilegios de sudo al usuario
Se ejecuta el siguiente comando como root para darle privilegios a nuestro usuario. Se agrega al usuario **_user_** en el grupo **_wheel_**.
> [root@localhost ~]# usermod -aG **wheel** user

## Configuracion de la ip estatica
Se listan las interfaces con el siguiente comando y se escoge la que se va a configurar.
> nmcli -p dev

Se obtiene una salida como la siguiente.
```
=====================
  Status of devices
=====================
DEVICE  TYPE      STATE      CONNECTION 
--------------------------------------------------------------
ens33   ethernet  connected  ens33      
lo      loopback  unmanaged  --  
```

Una vez seleccionada nuestra interfaz, en este caso la **_"ens33"_**, se cambia su configuracion. Su archivo de configuracion se encuentra en la ruta **_/etc/sysconfig/network-scripts/_**. Podemos ver el contenido con el comando **cat** y editarlo con los editores **vi** o **vim**.

> [root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-ens33

Tendremos una salida como la siguiente:
```
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="9e20029b-3762-428f-a216-26a3c01ea319"
DEVICE="ens33"
ONBOOT="yes"
```
Ahora editamos el archivo con **vim** de la siguiente manera:

> [root@localhost ~]# vim /etc/sysconfig/network-scripts/ifcfg-ens33
```
TYPE="Ethernet"
BOOTPROTO="none"
IPADDR="192.168.100.10"
PREFIX="28"
GATEWAY="192.168.100.1"
DNS1="192.168.100.1"
DNS2="8.8.8.8"
DNS3="1.1.1.1"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="no"
NAME="ens33"
UUID="9e20029b-3762-428f-a216-26a3c01ea319"
DEVICE="ens33"
ONBOOT="yes"
```

Finalmente reiniciamos el servicio y vemos que ahora aparece la ip estatica que asignamos en el archivo
> [root@localhost ~]# systemctl restart network

> [root@localhost ~]# ip a

```
ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:ea:a3:61 brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.10/28 brd 192.168.100.15 scope global noprefixroute ens33
       valid_lft forever preferred_lft forever
```