<properties 
   pageTitle="Notificación a los usuarios sobre los datos recibidos de los sensores o de otros sistemas | Microsoft Azure"
   description="Se describe cómo usar centros de eventos para notificar a los usuarios de los datos de los sensores."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="spyros;sethm" />

# Notificación a los usuarios sobre los datos recibidos de los sensores o de otros sistemas

Suponga que tiene una aplicación que supervisa los datos en tiempo real o genera informes según una programación. Si consulta el sitio web en el que se muestran los informes o los gráficos en tiempo real, podría observar alguna situación que precisa de una acción. ¿Qué ocurre si necesita recibir notificaciones de tales situaciones, en lugar de tener que acordarse de comprobar el sitio web? Imagine que tiene una luz para cultivo en un invernadero y necesita saber inmediatamente si la luz se apaga. Para ello, podría usar un sensor de luz en el invernadero, que se pueda programar para que envíe un correo electrónico si la luz se apaga.

![][1]

En otro escenario, imagine que dirige un centro de alojamiento de mascotas y que necesita recibir notificaciones cuando los niveles de suministro de inventario sean bajos. Por ejemplo, podría planificar el envío de un mensaje de texto desde el sistema ERP si el inventario de almacén de alimentos para perros alcanza un nivel crítico.

![][2]

El problema radica en cómo obtener información crítica cuando se cumplen determinadas condiciones, y no cuando consulta un informe estático. Si utiliza un [Centro de eventos de Azure][] o un [Centro de IoT][] para recibir datos de dispositivos o aplicaciones empresariales como [Dynamics AX][], tiene varias opciones para procesarlos. Puede consultarlos en un sitio web, analizarlos, almacenarlos y usarlos para desencadenar los comandos para realizar alguna acción. Para ello, puede usar herramientas eficaces como [Sitios web Azure][], [SQL Azure][], [HDInsight][], [Cortana Analytics Suite][], [Conjunto de IoT][], [Aplicaciones lógicas][] o [Centros de notificaciones de Azure][]. Pero a veces lo único que desea hacer es enviar datos a alguien con una sobrecarga mínima. Para mostrarle cómo hacerlo con el uso mínimo de código, hemos proporcionado nuevo ejemplo, [AppToNotifyUsers][]. Las opciones incluidas son el correo electrónico (SMTP), los SMS y el teléfono.

## Estructura de la aplicación

La aplicación está escrita en C# y el archivo Léame del ejemplo contiene toda la información que necesita para modificar, crear y publicar la aplicación. En las secciones siguientes se proporciona información general de alto nivel sobre lo que la aplicación hace.

Comenzamos con la suposición de que dispone de eventos críticos que se van a insertar en un centro de eventos de Azure o en un centro de IoT. Cualquier centro lo hará, siempre que tenga acceso a él y conozca la cadena de conexión.

Si no dispone de un centro de IoT o de un centro de eventos, puede configurar fácilmente un banco de pruebas con una placa Arduino y un Raspberry Pi, siguiendo las instrucciones del proyecto [Connect The Dots](https://github.com/Azure/connectthedots). El sensor de luz de la placa Arduino envía los niveles de luz mediante el Pi a un [Centro de eventos de Azure][] (**ehdevices**) y un trabajo de [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) envía alertas a un segundo centro de eventos (**ehalerts**) si los niveles de luz recibidos caen por debajo de un nivel determinado.

Cuando **AppToNotify** se inicia, lee un archivo de configuración (App.config) para obtener la dirección URL y las credenciales del centro de eventos que recibe las alertas. Después genera un proceso para supervisar continuamente dicho centro de eventos para cualquier mensaje que llegue; siempre y cuando tenga acceso a la dirección URL del centro de eventos o del centro de IoT y credenciales válidas, el código del lector de este centro de eventos leerá continuamente todas las novedades. Durante el inicio, la aplicación también lee la dirección URL y las credenciales del servicio de mensajería (correo electrónico, SMS y teléfono) que desea utilizar, así como el nombre y la dirección del remitente y una lista de destinatarios.

Cuando el monitor del centro de eventos detecta un mensaje, desencadena un proceso que envía dicho mensaje con el método especifica en el archivo de configuración. Tenga en cuenta que envía todos los mensajes que detecta. Si establece el monitor para que remita a un centro de eventos que recibe diez mensajes por segundo, el remitente enviará diez mensajes por segundo: diez correos electrónicos por segundo, diez SMS por segundo y diez llamadas de teléfono por segundo. Por ese motivo, asegúrese de supervisar un centro de eventos que recibe sólo las alertas que se deben enviar, no un centro de eventos que recibe todos los datos sin procesar de las aplicaciones o los sensores.

## Aplicabilidad

El código de este ejemplo sólo muestra cómo supervisar los centros de eventos y cómo llamar a los servicios de mensajería externos en caso de que desee agregar esta funcionalidad a la aplicación. Tenga en cuenta que esta solución está indicada para que la implemente el usuario, con ejemplos dirigidos exclusivamente a los desarrolladores. No aborda las necesidades empresariales, como la redundancia, la conmutación por error, el reinicio por error, etc. Para obtener más soluciones integrales y de producción, vea lo siguiente:

- Mediante conectores o notificaciones de inserción con el servicio [Aplicaciones lógicas de Azure](../app-service-logic/app-service-logic-connectors-list.md).
- Con los [centros de notificaciones de Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), como se describe el blog sobre [difusión de notificaciones de inserción a millones de dispositivos móviles mediante los centros de notificaciones de Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## Pasos siguientes

Es fácil crear un servicio de notificaciones sencillo que envíe correos electrónicos, mensajes de texto o realice llamadas telefónicas a los destinatarios para retransmitir los datos recibidos por un centro de eventos o un centro de IoT. Si desea implementar la solución para notificar a los usuarios sobre los datos recibidos por estos centros, visite [AppToNotifyUsers][].

Para obtener más información, vea los siguientes artículos:

- [Centros de eventos de Azure]
- [Centro de IoT de Azure]
- Empezar a trabajar con un [tutorial de Centros de eventos].
- Una [aplicación de ejemplo completa que usa Centros de eventos].
- Una [solución de mensajería en cola] mediante las colas de Bus de servicio.

Si desea implementar la solución para notificar a los usuarios sobre los datos recibidos por estos centros, visite:

- [AppToNotifyUsers][]

[tutorial de Centros de eventos]: event-hubs-csharp-ephcs-getstarted.md
[Centro de IoT de Azure]: https://azure.microsoft.com/services/iot-hub/
[Centro de IoT]: https://azure.microsoft.com/services/iot-hub/
[Centros de eventos de Azure]: https://azure.microsoft.com/services/event-hubs/
[Centro de eventos de Azure]: https://azure.microsoft.com/services/event-hubs/
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[solución de mensajería en cola]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/es-ES/dynamics/erp-ax-overview.aspx
[Sitios web Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Analytics Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[Conjunto de IoT]: https://azure.microsoft.com/solutions/iot-suite/
[Aplicaciones lógicas]: https://azure.microsoft.com/services/app-service/logic/
[Centros de notificaciones de Azure]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_1223_2015-->