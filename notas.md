# Observaciones en el proceso de la configuración

## **Error: Could not stat() command file '/usr/local/nagios/var/rw/nagios.cmd'!**

Nagios puede marcar errores al querer activar notificaciones

```
Error: Could not stat() command file '/usr/local/nagios/var/rw/nagios.cmd'!
```

Este error se puede solucionar de la siguiente manera

```
1.- Ejecuta como root
        # setenforce 0

2.- Reinicia el servicio
        # systemctl restart nagios.service

3.- Finalmente se intenta activar la notificación, esta vez debe de funcionar sin problemas
```

La solucion anterior simplemente deshabilita temporalmente selinux que es lo que causa problemas con la activacion de notificaciones. Otra alternativa es desahabilitar selinux en el archivo de configuracion.

```
1.- Abrimos el archivo
        # vim /etc/selinux/config

2.- Cambiamos la linea 7 de enforcing a disabled
        De:
        SELINUX=enforcing

        A:
        SELINUX=disabled

3.- Cerramos el archivo y reiniciamos el servidor
```

## **Instalacion de bind-utils package**

Si se quiere hacer uso de la herrmienta de **nslookup** se requiere instalar el paquete de **bind-utils**
> sudo yum install bind-utils

## **Could not resolve host: mirrorlist.centos.org Centos 7**

Si no se pueden utilizar los comandos **_yum makecache_** y **_yum update_** es probable que no tenemos bien configurado nuestro servidor DNS o nuestra configuracion de red es incorrecta.

Si no podemos realizar un _ping_ a _8.8.8.8_ intentamos hacer un _ping_ a la ip local del gateway, si esto tambien falla significa que tenemos mal nuestra configuración de red.

Por otro lado si podemos realizar el _ping_ a 8.8.8.8, podemos utilizar los comandos _nslookup, dig y host_ para verificar si se pueden resolver los nombres de dominio, en caso de que no debemos editar nuestro archivo _/etc/resolv.conf_ y agregar lo siguiente
```
nameserver 9.9.9.9
```
Se puede utilizar otro DNS, como 8.8.8.8 o 1.1.1.1. Una vez realizado esto, podemos intentar usar el comando _sudo yum update_ o _sudo yum makecache_, en esta ocacion deberia funcionar.

## **Error: (No output on stdout) stderr: Can't locate Net/SNMP.pm in @INC (@INC contains: /usr/local/lib64/perl5 /usr/local/share/perl5 /usr/lib64/perl5/vendor_perl /usr/share/perl5/vendor_perl /usr/lib64/perl5 /usr/share/perl5 .) at /usr/local/nagios/libexec/check_snmp_storage.pl line 15.**


Al instalar plugins que por alguna razon no fueron instalados con nagios o que nosotros deseamos instalar, nos marcarán errores si es que nos hace falta alguna dependencia, en este caso al descargrar el script *check_snmp_storage.pl* hecho en *perl* nos manda erroes, para solucionar esto, simplemente es instalar *perl-Net-SNMP*

> sudo yum -y install perl-Net-SNMP



