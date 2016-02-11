<properties
   pageTitle="Solucionar problemas de Servicios en la nube con Application Insights | Microsoft Azure"
   description="Aprenda a solucionar problemas del servicio en la nube mediante el uso de Application Insights para procesar datos de Diagnósticos de Azure."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# Solucionar problemas de Servicios en la nube con Application Insights

Con [Azure SDK 2.8](https://azure.microsoft.com/downloads/) y la extensión 1.5 de Diagnósticos de Azure ahora puede enviar los datos de Diagnósticos de Azure para su servicio en la nube directamente a Application Insights. Los distintos tipos de registros recopilados por Diagnósticos de Azure, incluidos los registros de aplicación, registros de eventos de Windows, registros ETW y contadores de rendimiento, ahora se pueden enviar a Application Insights y visualizarse en la IU del portal de Application Insights. Cuando se utilizan junto con el SDK de Application Insights puede obtener información sobre las métricas y los registros de la aplicación, y sobre los datos del sistema y de la infraestructura procedentes de Diagnósticos de Azure.
  
## Configuración de Diagnósticos de Azure para enviar datos a Application Insights

Siga estos pasos para configurar el proyecto del servicio en la nube para enviar datos de Diagnósticos de Azure a Application Insights.

1) En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en un rol y seleccione **Propiedades** para abrir el diseñador de roles.
	
![Propiedades de rol del Explorador de soluciones][1]

(2) En el diseñador de roles, en la sección de diagnósticos, active la casilla **Enviar datos de diagnóstico a Application Insights**.

![Diseñador de roles Enviar datos de diagnóstico a application insights][2]

(3) En el cuadro de diálogo que aparece, seleccione el recurso de Application Insights al que le gustaría enviar los datos de Diagnósticos de Azure. El cuadro de diálogo le permite seleccionar un recurso existente de Application Insights de su suscripción o especificar manualmente una clave de instrumentación para un recurso de Application Insights. Si no tiene un recurso existente de Application Insights, puede crear uno haciendo clic en el vínculo **Crear un nuevo recurso**. Esta acción abrirá una ventana del explorador en el Portal de Azure clásico, donde puede crear un recurso de Application Insights. Para obtener más información sobre la creación de un recurso de Application Insights, consulte [Creación de un nuevo recurso de Application Insights](app-insights-create-new-resource.md).

![seleccionar recurso de application insights][3]

4) Cuando haya agregado el recurso de Application Insights, la clave de instrumentación para ese recurso se almacenará como un valor de configuración de servicio con el nombre **APPINSIGHTS\_INSTRUMENTATIONKEY**. Puede cambiar este valor de configuración para cada entorno o configuración del servicio seleccionando una configuración diferente en el cuadro desplegable Configuración del servicio y especificando una nueva clave de instrumentación para dicha configuración.

![seleccionar configuración de servicio][4]
	
Visual Studio usa el valor de configuración **APPINSIGHTS\_INSTRUMENTATIONKEY** para configurar la extensión de diagnósticos con la información de recursos apropiada de Application Insights durante la publicación. El valor de configuración es una manera cómoda de definir claves de instrumentación diferentes para distintas configuraciones del servicio. Visual Studio traducirá dicho valor y lo insertará en la configuración pública de la extensión de diagnósticos en el momento de la publicación. Para simplificar el proceso de configuración de la extensión de diagnósticos con PowerShell, la salida del paquete de Visual Studio también contiene el XML de configuración público con la clave de instrumentación de Application Insights incluida. Los archivos de configuración públicos se crean en la carpeta Extensiones y siguen el patrón PaaSDiagnostics.<RoleName>.PubConfig.xml. Cualquier implementación basada en PowerShell puede usar este patrón para asignar cada configuración a un rol.

(5) Al habilitar **Enviar datos de diagnóstico a Application Insights** configurará automáticamente Diagnósticos de Azure para enviar todos los contadores de rendimiento y registros de nivel de error que recopila el agente de Diagnósticos de Azure a Application Insights. Si desea configurar qué datos se envían a Application Insights, deberá editar manualmente el archivo *diagnostics.wadcfgx* para cada rol. Consulte [Configuración de Diagnósticos de Azure para enviar datos a Application Insights](azure-diagnostics-configure-applicationinsights.md) para obtener más información sobre cómo actualizar manualmente la configuración.

Una vez configurado el servicio en la nube para enviar datos de Diagnósticos de Azure a Application Insights, puede implementarlo en Azure como lo haría normalmente asegurándose de que la extensión de Diagnósticos de Azure está habilitada. Consulte [Publicar un servicio en la nube mediante Azure Tools](vs-azure-tools-publishing-a-cloud-service.md).

## Visualización de datos de Diagnósticos de Azure en Application Insights
La telemetría de Diagnósticos de Azure aparecerá en el recurso de Application Insights configurado para el servicio en la nube.

A continuación se muestra cómo los distintos tipos de registro de Diagnósticos de Azure se asignan a los conceptos de Application Insights:

-  Los contadores de rendimiento se muestran como métricas personalizadas en Application Insights
-  Los registros de eventos de Windows se muestran como seguimientos y eventos personalizados en Application Insights
-  Los registros de aplicación, los registros ETW y los registros de infraestructura de diagnósticos se muestran como seguimientos en Application Insights.

Para ver de datos de Diagnósticos de Azure en Application Insights:

- Use el [Explorador de métricas](../application-insights/app-insights-metrics-explorer.md) para visualizar los contadores de rendimiento personalizados o los recuentos de diferentes tipos de eventos de registro de eventos de Windows.

![Métricas personalizadas en el Explorador de métricas][5]

- Use la [Búsqueda](../application-insights/app-insights-diagnostic-search.md) para buscar en los distintos registros de seguimiento enviados por Diagnósticos de Azure. Por ejemplo, si tenía una excepción no controlada en un rol que provocó el bloqueo de dicho rol y recicla esa información, aparecería en el canal *Aplicación* del *Registro de eventos de Windows*. Puede utilizar la funcionalidad Buscar para ver el error del registro de eventos de Windows y obtener el seguimiento de la pila completo para la excepción, lo que le permite encontrar la causa raíz del problema. 

![Buscar seguimientos][6]

## Pasos siguientes

- [Agregar el SDK de Application Insights a su servicio en la nube](../application-insights/app-insights-cloudservices.md) para enviar datos acerca de solicitudes, excepciones, dependencias y cualquier telemetría personalizada desde la aplicación. Junto con los datos de Diagnósticos de Azure puede obtener una vista completa de la aplicación y el sistema en el mismo recurso de Application Insights.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

<!---HONumber=AcomDC_1217_2015-->