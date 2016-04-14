<properties 
	pageTitle="Administración de extremos de streaming en una cuenta de Servicios multimedia" 
	description="En este tema se muestra cómo administrar los puntos de conexión de streaming mediante el Portal de Azure clásico." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Administración de extremos de streaming en una cuenta de Servicios multimedia

> [AZURE.SELECTOR]
- [Portal](media-services-manage-origins.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)


En Servicios multimedia de Microsoft Azure, un **extremo de streaming** representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para la distribución posterior. Servicios multimedia también proporciona integración sin problemas de CDN de Azure. La secuencia de salida de un servicio de StreamingEndpoint puede ser una secuencia en vivo o un recurso de vídeo bajo demanda en su cuenta de Servicios multimedia.

Además, puede controlar la capacidad del servicio de extremo de streaming para administrar las necesidades crecientes de ancho de banda mediante el ajuste de unidades de escalado (también conocidas como unidades de streaming). Se recomienda asignar una o más unidades de escalado para las aplicaciones en el entorno de producción. Las unidades de escalado proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y funcionalidad adicional que incluye: [empaquetado dinámico](media-services-dynamic-packaging-overview.md), integración con CDN y configuración avanzada.

Tenga en cuenta que solo se le cobrará cuando StreamingEndpoint esté en estado en ejecución.

Este tema proporciona información general de las principales funcionalidades proporcionadas por los extremos de streaming. El tema también muestra cómo usar el Portal de Azure clásico para administrar puntos de conexión de streaming.


##Adición y eliminación de extremos de streaming

Puede agregar o quitar puntos de conexión de streaming con el SDK de .NET, la API de REST o el Portal de Azure clásico.

Para agregar o quitar el punto de conexión de streaming mediante el Portal de Azure clásico, haga lo siguiente:

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.
2. Seleccione la página **EXTREMOS DE STREAMING**.
3. Haga clic en el botón ADD o DELETE en la parte inferior de la página. Tenga en cuenta que no es posible eliminar el extremo de streaming predeterminado.
4. Haga clic en el botón INICIAR para iniciar el extremo de streaming.
5. Haga clic en el nombre del extremo de streaming para configurarlo.

![Página Extremo de streaming][streaming-endpoint]


De forma predeterminada puede tener hasta dos extremos de streaming. Si necesita solicitar más, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).

##<a id="scale_streaming_endpoints"></a>Escalado del extremo de streaming

Las unidades de streaming proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y una funcionalidad adicional que actualmente incluye [funcionalidades de empaquetado dinámico](media-services-dynamic-packaging-overview.md). De forma predeterminada, el streaming se configura en un modelo de instancia compartida para el que los recursos del servidor (por ejemplo, cálculo, capacidad de salida, etc.) se comparten con los demás usuarios. Para mejorar el rendimiento de streaming, se recomienda adquirir unidades de streaming.

Puede escalar con el SDK de .NET , la API de REST o el Portal de Azure clásico.

Para cambiar el número de unidades de streaming mediante el Portal, haga lo siguiente:

1. Para especificar el número de unidades de streaming, seleccione la pestaña ESCALA y desplace el control deslizante de **capacidad reservada**.

	![Página de escala](./media/media-services-manage-origins/media-services-origin-scale.png)

4. Presione el botón GUARDAR para guardar los cambios.

	La asignación de cualquier nueva unidad de streaming puede tardar unos 20 minutos en finalizarse.

	 
	>[AZURE.NOTE] Actualmente, pasar de cualquier valor positivo de unidades de streaming a ninguno puede deshabilitar el streaming a petición hasta una hora.

	>[AZURE.NOTE] Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).
	
##<a id="configure_streaming_endpoints"></a>Configuración del extremo de streaming

Extremo de streaming le permite configurar las siguientes propiedades cuando tenga al menos una unidad de escala:

- Control de acceso
- Nombres de host personalizados
- Control de caché
- Directivas de acceso en varios sitios

Para obtener información detallada acerca de estas propiedades, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Puede configurar estas propiedades utilizando el SDK de .NET, la API de REST o el Portal de Azure clásico.

Para cambiar el número de unidades de streaming mediante el Portal, haga lo siguiente:

1. Seleccione el extremo de streaming que desee configurar.
1. Seleccione la pestaña CONFIGURAR.
  
Aparecerá una breve descripción de los campos.

![Configuración del origen][configure-origin]
  

1. Definir el período máximo de almacenamiento en caché que se especificará en el encabezado de control de caché de las respuestas HTTP. Este valor no sobrescribirá el valor máximo de caché que se definió explícitamente en el contenido de blobs.

2. Especificar las direcciones IP que podrán conectarse al extremo de streaming publicado. Si no se especificaron direcciones IP, cualquier dirección IP se podrá conectar.

3. Especifique la configuración para la autenticación del encabezado de firma de Akamai.

4. Puede especificar una directiva de acceso entre dominios para los clientes de Adobe Flash (para obtener más información, consulte [Especificación de archivos de directiva entre dominios](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)). Así como la directiva de acceso de cliente para los clientes de Microsoft Silverlight (para obtener más información, consulte [Disponibilidad de un servicio entre límites del dominio.aspx](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).

5. También puede configurar nombres de host personalizados haciendo clic en el botón **Configurar**. Para obtener más información, consulte la propiedad **CustomHostNames** en el tema [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).


##<a id="enable_cdn"></a>Habilitación de la integración de CDN de Azure

Puede especificar habilitar la integración de CDN de Azure para un extremo de streaming(está deshabilitada de forma predeterminada).

Para configurar la integración de CDN de Azure en true:

- El extremo de streaming debe tener al menos una unidad (escala) de streaming. Si más adelante desea establecer unidades de escala en 0, primero debe deshabilitar la integración de CDN. De forma predeterminada, cuando se crea un nuevo extremo de streaming, automáticamente se establece una unidad de streaming.

- El extremo de streaming debe estar en un estado detenido. Cuando se habilita CDN, puede iniciar el extremo de streaming.

Es posible que la integración de CDN de Azure tarde en habilitarse hasta 90 minutos. Se tarda hasta dos horas en activar los cambios en todos los POP de la red CDN.


La integración de CDN está habilitada en todos los centros de datos de Azure: oeste de EE. UU., este de EE. UU., Europa del Norte, Europa Occidental, Oeste de Japón, Este de Japón, Sudeste de Asia y Asia Oriental.

Una vez habilitada, se deshabilitan las siguientes configuraciones: **Nombres de host personalizados** y **Control de acceso**.

![Extremo de streaming con la red CDN habilitada][streaming-endpoint-enable-cdn]


###Consideraciones adicionales

- Cuando la red CDN está habilitada para un extremo de streaming, los clientes no pueden solicitar contenido directamente desde el origen. Si necesita poder probar el contenido con o sin red CDN, puede crear otro extremo de streaming que no tenga habilitada la red CDN.
- El nombre de host del extremo de streaming permanece igual después de habilitar la red CDN. No es necesario realizar ningún cambio en el flujo de trabajo de Servicios multimedia una vez habilitada la red CDN. Por ejemplo, si el nombre de host del extremo de streaming es strasbourg.streaming.mediaservices.windows.net, después de habilitar la red CDN se usa exactamente el mismo nombre de host.
- Para los nuevos extremos de streaming, puede habilitar la red CDN creando un nuevo extremo; para los extremos de streaming existentes, deberá detener primero el extremo y, después, habilitar la red CDN.
 

Para obtener más información, consulte [Anuncio de la integración de Servicios multimedia de Azure con la red CDN de Azure (red de entrega de contenido)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png
 

<!---HONumber=AcomDC_0211_2016-->