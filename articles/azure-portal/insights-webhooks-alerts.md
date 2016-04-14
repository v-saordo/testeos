<properties
	pageTitle="Cómo configurar alertas de Azure para enviar a otros sistemas"
	description="Redistribuir alertas de Azure a otros sistemas que no sean de Azure."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="ashwink"/>

# Cómo configurar webhooks para las alertas

Los webhooks permiten al usuario enrutar las notificaciones de alerta de Azure a otros sistemas para su procesamiento posterior o notificaciones personalizadas. Ejemplos de esto pueden ser el enrutamiento de la alerta a los servicios que pueden controlar una solicitud web entrante para enviar SMS, registrar errores, notificar a un equipo mediante servicios de chat y mensajería, etc.

El URI de webhook debe ser un extremo HTTP o HTTPS válido. El servicio de alertas de Azure realizará una operación POST en el webhook especificado, pasando un esquema y una carga de JSON específicos.

## Configuración de webhooks a través del portal

En la pantalla Crear/actualizar alertas en el [Portal de Azure](https://portal.azure.com/), puede agregar o actualizar el URI de webhook.

![Agregar una regla de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)


## Autenticación

La autenticación puede ser de dos tipos:

1. **Autenticación basada en token** : en este caso guardará el URI de webhook con un id. de token como **https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*.
2.	**Autenticación básica**: mediante un id. de usuario y una contraseña: en este caso guardará el URI de webhook como **https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar*.

## Esquema de carga

La operación POST contendrá el siguiente esquema y carga de JSON para todas las alertas basadas en métricas.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE] En nuestra próxima actualización, agregaremos compatibilidad para las alertas en eventos ("conditionType": "Event").


| Campo | ¿Obligatorio? | ¿Conjunto fijo de valores? | Notas |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Y|"Activado", "Resuelto"|Se trata de cómo averigua qué tipo de alerta. Azure envía automáticamente alertas activadas y resueltas para la condición que se establece.|
|contexto| Y | | El contexto de la alerta|
|timestamp| Y | | La hora en la que se desencadenó la alerta. La alerta se desencadena tan pronto como se lee la métrica del almacenamiento de diagnóstico.|
|id | Y | | Cada regla de alerta tiene un id. único.|
|name|Y | |
|description |Y | |Descripción de la alerta.|
|conditionType |Y |"Métrica", "Evento" |Se admiten dos tipos de alertas. Una basada en métrica y la otra basada en evento. En el futuro admitiremos alertas para eventos; por tanto, use este valor para comprobar si la alerta se basa en métrica o evento.|
|condition |Y | |Esto tendrá los campos específicos para buscar en función del conditionType.|
|metricName |para alertas de métricas | |El nombre de la métrica que define qué supervisa la regla.|
|metricUnit |para alertas de métricas |"Bytes", "BytesPerSecond" , "Count" , "CountPerSecond" , "Percent", "Seconds"|	 La unidad permitida en la métrica. Valores permitidos: https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |para alertas de métricas | |El valor real de la métrica que provocó la alerta|
|threshold |para alertas de métricas | |El valor de umbral que activa la alerta|
|windowSize |para alertas de métricas | |El período de tiempo que se usa para supervisar la actividad de la alerta según el umbral. Debe estar comprendido entre 5 minutos y 1 día. Formato de duración ISO 8601.|
|timeAggregation |para alertas de métricas |"Average", "Last" , "Maximum" , "Minimum" , "None", "Total" |	La manera en que se recopilan los datos se debería combinar con el tiempo. El valor predeterminado es Average. Valores permitidos: https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |para alertas de métricas | |El operador que se usa para comparar los datos y el umbral.|
|subscriptionId |Y | |GUID de suscripción de Azure|
|resourceGroupName |Y | |resource-group-name del recurso afectado|
|resourceName |Y | |resource name del recurso afectado|
|resourceType |Y | |tipo de recurso del recurso afectado|
|resourceId |Y | |URI de id. de recurso que identifica ese recurso de forma única|
|resourceRegion |Y | |región/ubicación del recurso que se ve afectado|
|portalLink |Y | |vínculo directo del portal de azure a la página de resumen de recursos|
|propiedades |N |Opcional |Es un conjunto de pares <Key  Value> (es decir, el diccionario <String  String>) que incluye detalles sobre el evento. El campo de propiedades es opcional. Un flujo de trabajo basado en aplicación lógica o interfaz de usuario personalizada, los usuarios pueden especificar clave/valores que se pueden pasar a través de la carga. La forma alternativa para pasar propiedades personalizadas a la webhook es mediante el propio URI de webhook (como parámetros de consulta).|


>[AZURE.NOTE] No se puede usar el campo de propiedades a través del Portal. En nuestro próximo lanzamiento del SDK de Insights, puede establecer las propiedades mediante la API de la alerta.

## Pasos siguientes

Para obtener información general adicional, obtenga más información sobre los webhooks y las alertas de Azure en el vídeo [Integrar las alertas de Azure con PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080).

Para obtener información sobre cómo crear mediante programación un webhook, consulte [Crear una alerta con webhooks mediante el SDK de Azure Insights (C#)](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a).

Cuando haya configurado sus webhooks y alertas, explore estas otras opciones para iniciar un script de automatización.

[Ejecutar scripts de Automatización de Azure (runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)

Use las alertas de Azure para enviar mensajes a otros servicios. Use las siguientes plantillas de ejemplo para comenzar.

[Usar la aplicación lógica para enviar SMS a través de la API de Twilio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[Usar la aplicación lógica para enviar mensajes de demora](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[Usar la aplicación lógica para enviar mensajes a una cola de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0218_2016-->