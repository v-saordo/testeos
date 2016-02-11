<properties 
	pageTitle="Cómo registrar eventos en los centros de eventos de Azure en la administración de API de Azure" 
	description="Aprenda a registrar eventos en los centros de eventos de Azure en la administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Cómo registrar eventos en los centros de eventos de Azure en la administración de API de Azure

Centros de eventos de Azure es un servicio de introducción de datos altamente escalable que permite la introducción de millones de eventos por segundo para que pueda procesar y analizar grandes cantidades de datos generados por los dispositivos y aplicaciones conectados. Centros de eventos actúa como la "puerta principal" de una canalización de eventos y, una vez que los datos se recopilan en un centro de eventos, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes/almacenamiento. Centros de eventos desacopla la producción de un flujo de eventos desde el consumo de los eventos, para que los consumidores de eventos pueden tener acceso a los eventos según su propia programación.

Este artículo es un complemento del vídeo [Integración de Administración de API de Azure con centros de eventos](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) y describe cómo registrar eventos de Administración de API mediante centros de eventos de Azure.

## Crear un centro de eventos de Azure

Para crear un nuevo centro de eventos, inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) y haga clic en **Nuevo**->**Servicios de aplicaciones**->**Bus de servicios**->**Centro de eventos**->**Creación rápida**. Escriba un nombre para el centro de eventos y la región. Seleccione una suscripción y elija un espacio de nombres. Si no ha creado un espacio de nombres anteriormente, puede crear uno escribiendo un nombre en el cuadro de texto **Espacio de nombres**. Una vez configuradas todas las propiedades, haga clic en **Crear un centro de eventos** para crear el centro de eventos.

![Creación de un centro de eventos][create-event-hub]

Luego navegue a la pestaña **Configurar** para el nuevo centro de eventos y cree dos **directivas de acceso compartido**. La primera de ellas se debe llamar **Envío**. Asígnele permisos de **envío**.

![Directiva de envío][sending-policy]

La segunda de ellas se debe llamar **Recepción**. Asígnele permisos de **escucha** y haga clic en **Guardar**.

![Directiva de recepción][receiving-policy]

Cada directiva de acceso compartido permite a las aplicaciones enviar y recibir eventos desde y hacia el centro de eventos. Para obtener acceso a las cadenas de conexión de estas directivas, navegue a la pestaña **Panel** del centro de eventos y haga clic en **Información de conexión**.

![Cadena de conexión][event-hub-dashboard]

La cadena de conexión **Envío** se usa para registrar eventos, mientras que la cadena de conexión **Recepción** se usa para descargar eventos desde el centro de eventos.

![Cadena de conexión][event-hub-connection-string]

## Creación de un registrador de administración de API

Ahora que tiene un centro de eventos, el siguiente paso es configurar un [registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx) en el servicio Administración de API para que se puedan registrar eventos en el centro de eventos.

Los registradores de Administración de API se configuran mediante la [API de REST de Administración de API](http://aka.ms/smapi). Antes de usar la API de REST por primera vez, revise los [requisitos previos](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) y asegúrese de tener [habilitado el acceso a la API de REST](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Para crear un registrador, efectúe una solicitud HTTP PUT con la siguiente plantilla de dirección URL.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-	Sustituya `{your service}` por el nombre de la instancia de servicio de Administración de API.
-	Sustituya `{new logger name}` por el nombre deseado del nuevo registrador. A este nombre se hará referencia al configurar la directiva [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub).

Agregue los siguientes encabezados a la solicitud.

-	Content-Type : application/json
-	Authorization : SharedAccessSignature uid=...
	-	Para instrucciones sobre cómo generar `SharedAccessSignature`, vea [Autenticación de la API de REST de administración de API de Azure](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Especifique el cuerpo de la solicitud utilizando la siguiente plantilla.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-	`type` se debe establecer en `AzureEventHub`.
-	`description` ofrece una descripción opcional del registrador y puede ser una cadena de longitud cero si lo desea.
-	`credentials` contiene el `name` y `connectionString` del centro de eventos de Azure.

Al realizar la solicitud, si se crea el registrador, se devuelve un código de estado de `201 Created`.

>[AZURE.NOTE]Para conocer otros posibles códigos de retorno y sus razones, vea [Creación de un registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Para conocer la forma de realizar otras operaciones como crear listas, actualizar y eliminar, vea la documentación de la entidad del [registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx).

## Configuración de directivas log-to-eventhubs

Una vez que el registrador está configurado en la administración de API, puede configurar las directivas de log-to-eventhubs para registrar los eventos oportunos. La directiva log-to-eventhubs puede utilizarse en la sección de las directivas de entrada o de salida.

Para configurar las directivas, inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com), navegue hasta el servicio de administración de API y haga clic en el **portal para editores** o en **Administrar** para obtener acceso al portal para editores.

![Portal del publicador][publisher-portal]

Haga clic en **Directivas** en el menú de Administración de API situado a la izquierda. Seleccione el producto que quiera y la API. Por último, haga clic en **Agregar directiva**. En este ejemplo, vamos a agregar una directiva para la **API Echo** en el producto **Sin límites**.

![Add policy][add-policy]

Sitúe el cursor en la sección de la directiva de `inbound` y haga clic en la directiva **Registrar en el Centro de eventos** para insertar la plantilla de declaración de directivas `log-to-eventhub`.

![Policy editor][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Sustituya `logger-id` por el nombre del registrador de Administración de API que configuró en el paso anterior.

Puede usar cualquier expresión que devuelva una cadena como valor para el elemento `log-to-eventhub`. En este ejemplo, se registra una cadena que contiene la fecha y la hora, el nombre de servicio, el Id. de la solicitud, la dirección IP de la solicitud y el nombre de la operación.

Haga clic en **Guardar** para guardar la configuración de la directiva actualizada. En el momento de guardarla, la directiva se activa y los eventos se registran en el centro de eventos designado.

## Pasos siguientes

-	Obtenga más información acerca de los centros de eventos de Azure
	-	[Introducción a los centros de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
	-	[Recepción de mensajes con EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
	-	[Guía de programación de Centros de eventos](../event-hubs/event-hubs-programming-guide.md)
-	Obtener más información acerca de la integración de Administración de API y centros de eventos
	-	[Referencia de entidad del registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx)
	-	[referencia de la directiva log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
	-	[Supervisión de API con Administración de API de Azure, Centros de eventos y Runscope](api-management-log-to-eventhub-sample.md)	

## Ver un tutorial en vídeo

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png

<!---HONumber=AcomDC_1210_2015-->