<properties
	pageTitle="Referencia para navegar en el portal de Azure"
	description="Conozca las distintas experiencias de usuario web del Servicio de aplicaciones entre el portal de administración y el Portal de Azure."
	services="app-service"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="jaime-espinosa"/>

# Referencia para navegar en el portal de Azure

Sitios web de Azure ahora se llama [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). Estamos actualizando toda nuestra documentación para reflejar este cambio de nombre y ofrecer instrucciones para el Portal de Azure. Hasta que este proceso se finalice, puede usar este documento como guía para trabajar con Aplicaciones web en el Portal de Azure.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]
 
## El futuro del Portal de Azure clásico

Aunque observará los cambios de personalización de marca en el Portal de Azure clásico, dicho portal se está reemplazando por el Portal de Azure. Como el portal clásico va a quedar obsoleto, las novedades de desarrollo tienden a centrarse en Portal de Azure. Todas las nuevas características de Aplicaciones web se incluirán en el Portal de Azure. Comience a usar el Portal de Azure para beneficiarse de las mejores y más recientes ventajas que ofrecen las Aplicaciones web.

## Diferencias de diseño entre el Portal de Azure clásico y el Portal de Azure

En el portal clásico, todos los servicios de Azure se muestran en la parte izquierda. La navegación en este portal sigue una estructura de árbol, donde se empieza a partir del servicio y se navega a cada elemento. Esta estructura funciona bien para administrar componentes independientes. Si embargo, las aplicaciones integradas en Azure constituyen una colección de servicios interconectados y la estructura de árbol no es la ideal para trabajar con este tipo de colecciones.

El Portal de Azure facilita la compilación de aplicaciones completas con componentes de varios servicios. El portal se organiza en *viajes*. Un *viaje* es una serie de *hojas*, que funcionan como contenedores de los distintos componentes. Por ejemplo, configurar el escalado automático para una aplicación web es un *viaje* que requiere varias hojas, como se muestra en el ejemplo siguiente: la hoja **sitio-web** (dicho título aún no se ha actualizado para usar la nueva terminología), la hoja **Configuración** y la hoja **Escalar horizontalmente**. En el ejemplo, el ajuste de escala automático se configura para que dependa del uso de la CPU, por lo que también hay una hoja llamada **Porcentaje de CPU**. Los componentes incluidos en las *hojas* se llaman *partes* y parecen iconos.

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## Ejemplo de navegación: crear una aplicación web

La creación de aplicaciones web sigue siendo muy fácil. En la imagen siguiente se muestran el portal clásico y el portal en paralelo, para demostrar que el número de pasos necesarios para poner una aplicación web en marcha no ha cambiado significativamente.

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

En el portal puede elegir entre los tipos más comunes de aplicaciones web, incluidas conocidas aplicaciones de la galería como WordPress. Para obtener una lista completa de las aplicaciones disponibles, visite [Azure Marketplace].

Al crear una aplicación web, se especifica la dirección URL, el plan del Servicio de aplicaciones y la ubicación en el portal de la misma forma que se hacía en el clásico.

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

Además, el portal permite definir otra configuración común. Por ejemplo, los [grupos de recursos](../resource-group-overview.md) simplifican la forma de ver y administrar recursos relacionados de Azure.

## Ejemplo de navegación: configuración y características

Todas las configuraciones y características se agrupan ahora de forma lógica en una sola hoja, a partir de la cual puede navegarse.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

Por ejemplo, puede crear dominios personalizados si hace clic en **Dominios personalizados y SSL** en la hoja **Configuración**.

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Para configurar una alerta de supervisión, haga clic en **Solicitudes y errores** y, a continuación, use **Agregar alerta**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

Para habilitar los diagnósticos, haga clic en **Registros de diagnóstico** en la hoja **Configuración**.

![](./media/app-service-web-app-azure-portal/Diagnostics.png)
 
Para establecer la configuración de una aplicación, haga clic en **Configuración de la aplicación** en la hoja **Configuración**.

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

Aparte de la marca, algunos elementos más del portal se han cambiado de nombre o bien se han agrupado de forma distinta para encontrarlos más fácilmente. Por ejemplo, a continuación se incluye una captura de pantalla de la página correspondiente a la configuración de la aplicación (**Configurar**) en el portal clásico.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## Más recursos

[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
 

<!---HONumber=AcomDC_0302_2016-->