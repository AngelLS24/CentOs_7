# Instalación y configuración de apache
*   Se instala *httpd* en el CentOs
    
    *`sudo yum install httpd -y`*

*   Se inicia el servicio
    
    *`sudo systemctl start httpd`*

*   Se habilita el servicio, de esta manera se iniciará automáticamente al iniciar el servidor

    *`sudo systemctl enable httpd`*

*   Se verifica el estado del servicio

    *`systemctl status httpd`*

*   Se permite el trafico en los puertos *80* y *443*  en el firewall y se reinicia el firewall para que los cambios tengan efecto

    *`sudo firewall-cmd ––permanent ––add-port=80/tcp`*

    *`sudo firewall-cmd ––permanent ––add-port=443/tcp`*

    *`firewall-cmd --reload`*

*   Finalmente se debera comentar todo lo que se encuentra en */etc/httpd/conf.d/welcome.conf*
*   Agregamos un archivo *index.html* en */var/www/html/*
