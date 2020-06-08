# Configurar notificaciones a través de Telegram
##  Configuración de Telegram
*   Creamos el bot que se usara para que realice las notificaciones de los servicios
    *   En Telegram, en la caja de busqueda ingresamos **@BotFather**
    *   En caso de ser la primera vez que utilizamos el **@BotFather**, basta con picarle en **START** para empezar el proceso
    *   Ingresamos el comando **/newbot**
    *   Ahora nos solicita que ingresemos el nombre del **bot**
    *   Después ingresamos el *username* del bot, este deberá terminar con la palabra *bot*
    *   Finalmente nos regresa el token con el que nos vamos a identificar con la BotApi
*   Creamos un grupo, en este grupo serán entregadas las notificaciones de Nagios
*   Obtenemos el identificador del bot
    *   Enviaremos el mensaje *"/start"* en el chat del grupo que creamos
    *   Ingresamos a https://api.telegram.org/bot**TU_TOKEN**/getUpdates para obtener el id del bot
    *   En el sitio nos aparece un json en el que se podrá observar el id del bot, será algo parecido a lo siguiente
        ```
        "chat": {
                    "id": identificador_del_bot,
                    "title": "nombre_de_tu_bot",
                    "type": "group",
                    "all_members_are_administrators": true
                },
        ```

## Definición de los comandos
Una vez obtenido el id del bot se definen los comandos para la realización de las notificaciones

```
# 'notify-host-by-telegram-bot' command definition
define command{
    command_name    notify-host-by-telegram-bot
    command_line /usr/bin/wget "https://api.telegram.org/bot<TU_TOKEN>/sendMessage?chat_id=<ID_DE_TU_BOT>&text=***** Nagios *****%0A%0ANotification Type: $NOTIFICATIONTYPE$%0AHost: $HOSTNAME$%0AState: $HOSTSTATE$%0AAddress: $HOSTADDRESS$%0AInfo: $HOSTOUTPUT$%0A%0ADate/Time: $LONGDATETIME$%0A"
}


# 'notify-service-by-telegram-bot' command definition
define command{
    command_name    notify-service-by-telegram-bot
    command_line    /usr/bin/wget "https://api.telegram.org/bot<TU_TOKEN>/sendMessage?chat_id=<ID_DE_TU_BOT>&text=***** Nagios *****%0A%0ANotification Type: $NOTIFICATIONTYPE$%0A%0AService: $SERVICEDESC$%0AHost: $HOSTALIAS$%0AAddress: $HOSTADDRESS$%0AState: $SERVICESTATE$%0A%0ADate/Time: $LONGDATETIME$%0A%0AAdditional Info:%0A%0A$SERVICEOUTPUT$%0A"
}
```

## Definición del contacto
Definimos el contacto en nagios que se usara para ejectuar los comandos y realizar las notificaciones
```
define contact{
    contact_name                    nagiostelegram
    use                             generic-contact
    alias                           Nagios Telegram
    service_notification_period     24x7
    host_notification_period        24x7
    service_notification_options    w,u,c,r
    host_notification_options       d,r
    service_notification_commands   notify-service-by-telegram-bot
    host_notification_commands      notify-host-by-telegram-bot
}

define contactgroup{
    contactgroup_name       admins
    alias                   Nagios Administrators
    members                 nagiosadmin,nagiostelegram
}
```
## Configuración del contacto con los hosts
Finallmente se agrega en cada host/servicio del cual quieres recibir notificaciones, para eso agredamos la siguinete linea en sus definiciones

```
contact_groups          admins
```
