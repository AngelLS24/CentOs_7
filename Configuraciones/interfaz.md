# Instalación de la interfaz gráfica en una instalación mínima de CentOs
*   Opcionalmente se pueden usar los siguientes comandos para instalar la interfaz grafica dentro de nuestro servdor.

*   Se configura el repositorio EPEL.
    > yum install epel-release -y

*   Se instala el grupo **"Server with GUI"**, esto instalará del entorno **_GNOME_**.
    > yum groupinstall "Server with GUI" -y

*   Establecemos **_graphical.target_** como default, esto iniciará la interaz grafica.
    > systemctl set-default graphical.target

*   Comprobamos que se haya establecido.
    ```
    systemctl get-default
    graphical.target
    ```

*   Cambiamos a la interfaz grafica con el siguiente comando.
    > systemctl isolate graphical.target

*   Otra opción es reiniciar el equipo.
    > reboot
