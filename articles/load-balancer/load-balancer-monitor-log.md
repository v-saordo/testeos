<properties 
   pageTitle="Supervisión de operaciones, eventos y contadores para el Equilibrador de carga | Microsoft Azure"
   description="Aprenda a habilitar eventos de alerta y el registro del estado de mantenimiento del sondeo para el Equilibrador de carga de Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="joaoma" />

# Análisis del registros para el Equilibrador de carga de Azure (vista previa)
Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas de equilibradores de carga. Se puede acceder a algunos de estos registros a través del portal y se pueden extraer todos los registros desde un almacenamiento de blobs de Azure y verse en distintas herramientas, como Excel y PowerBI. Puede obtener más información acerca de los diferentes tipos de registros en la lista siguiente.


- **Registros de auditoría:** puede usar los [registros de auditoría de Azure](insights-debugging-with-events.md) (anteriormente conocido como registros operativos) para ver todas las operaciones enviadas a sus suscripciones de Azure, así como su estado. Los registros de auditoría están habilitados de forma predeterminada y se pueden ver en el Portal de Azure.
- **Registros de eventos de alerta:** puede utilizar este registro para ver qué alertas se generan para el equilibrador de carga. El estado del equilibrador de carga se recopila cada cinco minutos. Este registro se escribe solo si se produce un evento de alerta del equilibrador de carga.  
- **Registros de sondeo de estado:** puede utilizar este registro para comprobar el estado de mantenimiento del sondeo, cuántas instancias están en línea en el back-end del equilibrador de carga y el porcentaje de máquinas virtuales que reciben tráfico de red del equilibrador de carga. Este registro se escribe en el cambio de evento de estado de sondeo.

>[AZURE.WARNING] Los registros solo están disponibles para los recursos implementados en el modelo de implementación del Administrador de recursos. No puede usar los registros de recursos del modelo de implementación clásica. Para entender mejor los dos modelos, consulte el artículo [Descripción de la implementación del Administrador de recursos y la implementación clásica](resource-manager-deployment-model.md). <BR> El análisis de registros actualmente solo funciona para los equilibradores de carga orientados hacia Internet. Esta limitación es temporal y puede cambiar en cualquier momento. Asegúrese de volver a visitar esta página para comprobar los cambios futuros.

## Habilitación del registro
El registro de auditoría se habilita automáticamente siempre para todos los recursos del Administrador de recursos. Debe habilitar el registro de eventos y de sondeos de estado para iniciar la recopilación de los datos disponibles a través de esos registros. Para habilitar el registro, siga estos pasos.

Inicie sesión en el [Portal de Azure](http://portal.azure.com). Si aún no tiene un equilibrador de carga, [cree uno](load-balancer-internet-arm-ps.md) antes de continuar.

En el portal, haga clic en **Examinar** >> **Equilibradores de carga**.

![portal - load-balancer](./media/load-balancer-monitor-log/load-balancer-browse.png)

Seleccione un equilibrador de carga existente >> **Toda la configuración**.

![portal - load-balancer-settings](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

En la hoja **Configuración**, haga clic en **Diagnósticos**, y, a continuación, en el panel **Diagnósticos**, junto a **Estado**, haga clic en **Activado**. En la hoja **Configuración**, haga clic en **Cuenta de almacenamiento** y seleccione una cuenta de almacenamiento existente o cree una nueva.

En la lista desplegable situada justo debajo de **Cuenta de almacenamiento**, seleccione si desea registrar los eventos de alerta, el estado de mantenimiento del sondeo (o ambas opciones) y, después, haga clic en **Guardar**.

![Portal de vista previa: Registros de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] Los registros de auditoría no requieren una cuenta de almacenamiento separada. El uso del almacenamiento para el registro de eventos y de sondeo de estado supondrán un costo adicional de servicio.

## Registro de auditoría
Azure genera este registro (anteriormente conocido como "registro operativo") de forma predeterminada. Los registros se conservan durante 90 días en el almacén de registros de eventos de Azure. Para obtener más información sobre estos registros, consulte el artículo [Visualización de eventos y registros de auditoría](insights-debugging-with-events.md).

## Registro de eventos de alerta
Este registro solo se genera si lo habilitó para cada uno de los equilibradores de carga, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. La información se registra en formato JSON, tal como se muestra a continuación.

	
	{
    "time": "2016-01-26T10:37:46.6024215Z",
	"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
	

El resultado de JSON muestra la propiedad *eventname* que describirá el motivo de creación de una alerta por parte del equilibrador de carga. En este caso, la alerta generada se debió al agotamiento de puertos TCP causado por los límites de IP NAT de origen (SNAT).

## Registro de sondeo de estado
Este registro solo se genera si lo habilitó para cada uno de los equilibradores de carga, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Se crea un contenedor denominado "insights-logs-loadbalancerprobehealthstatus" y se registran los datos siguientes:

		{
	    "records":

	    {
	   		"time": "2016-01-26T10:37:46.6024215Z",
	        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 1,
	            "healthPercentage": 50.000000
	        }
	    },
	    {
	        "time": "2016-01-26T10:37:46.6024215Z",
			"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 0,
	            "healthPercentage": 100.000000
	        }
	    }

	]
	}

El resultado JSON muestra en el campo de propiedades la información básica del estado de mantenimiento del sondeo. La propiedad *dipDownCount* muestra el número total de instancias en el back-end que no están recibiendo tráfico de red debido a las respuestas de sondeo con error.

## Visualización y análisis del registro de auditoría
Puede ver y analizar los datos del registro de auditoría mediante el uso de cualquiera de los métodos siguientes:

- **Herramientas de Azure:** puede recuperar información de los registros de auditoría a través de Azure PowerShell, de la interfaz de la línea de comandos (CLI) de Azure, la API de REST de Azure o el Portal de vista previa de Azure. En el artículo [Operaciones de auditoría con el Administrador de recursos](resource-group-audit.md) se detallan instrucciones paso a paso de cada método.
- **Power BI:** si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing), puede probarlo gratis. Con el [paquete de contenido de los registros de auditoría de Azure para Power BI](https://support.powerbi.com/knowledgebase/articles/742695) puede analizar los datos con los paneles preconfigurados que puede usar tal cual o personalizarlos.

## Visualización y análisis del registro de eventos y de sondeos de estado 
Debe conectarse a la cuenta de almacenamiento y recuperar las entradas del registro de JSON para los registros de eventos y de sondeos de estado. Cuando descargue los archivos JSON, se pueden convertir a CSV y consultarlos en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

>[AZURE.TIP] Si está familiarizado con Visual Studio y con los conceptos básicos de cambiar los valores de constantes y variables de C#, puede usar las [herramientas convertidoras de registros](https://github.com/Azure-Samples/networking-dotnet-log-converter), disponibles en Github.

## Recursos adicionales

- Entrada de blog [Visualize your Azure Audit Logs with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizar los registros de auditoría de Azure con Power BI).
- Entrada de blog [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Ver y analizar registros de auditoría de Azure en Power BI y más).

<!---HONumber=AcomDC_0204_2016-->