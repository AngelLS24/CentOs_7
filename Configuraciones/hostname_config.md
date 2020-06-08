# Configurar el nombre del hostname

*   Para establecer o cambiar el nombre de nuestro equipo sólo se debe de editar el archivo **_/etc/hostname_**

*   Abrimos el archivo con un editor y con privilegios de _root._
    > $ sudo vim /etc/hostname

*   Dentro del archivo editamos su contenido
    ```
    Cambiamos la línea que aparece en el archivo con el nombre de dominio de nuestro equipo de la siguiente manera

    De:
    localhost.localdomain

    A:
    nombre.del.dominio

    ```

*   Reiniciamos el servicio del hostname
    > $ sudo systemctl restart systemd-hostnamed

*   Finalmente se reinicia el equipo
    > $ sudo reboot

## Opcionalmente se puede cambiar el hostname del equipo de la siguiente manera
*   Se cambia el nombre
    > $ hostnamectl set-hostname nombre_del_hostname

*   Se reinicia el equipo
    > $ sudo reboot