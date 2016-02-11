<properties pageTitle="Configurar un nombre de dominio personalizado para una aplicación web en el Servicio de aplicaciones de Azure que usa el Administrador de tráfico"wpickett"Usar un nombre de dominio personalizado para una aplicación web en el Servicio de aplicaciones de Azure que incluye Administrador de tráfico para el equilibrio de cargas". description="Use un nombre de dominio personalizado para una aplicación web en el Servicio de aplicaciones de Azure que incluye Administrador de tráfico para el equilibrio de cargas". services="app-service\\web" documentationCenter="" authors="rmcmurray" manager="wpickett" editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

#Configuración de un nombre de dominio personalizado para una aplicación web en Servicio de aplicaciones de Azure utilizando el Administrador de tráfico

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

En este artículo se ofrecen instrucciones generales para usar un nombre de dominio personalizado con el Servicio de aplicaciones de Azure que usa el Administrador de tráfico para el equilibrio de carga.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## Descripción de los registros DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Configuración de las aplicaciones web para el modo estándar

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Incorporación de un registro DNS al dominio personalizado

> [AZURE.NOTE]Si ha adquirido el dominio a través de Aplicaciones web del Servicio de aplicaciones de Azure, omita los pasos siguientes y consulte el paso final del artículo [Comprar dominio para Aplicaciones web](custom-dns-web-site-buydomains-web-app.md).

Para asociar su dominio personalizado con una aplicación web del Servicio de aplicaciones de Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador de dominios al que ha adquirido su nombre de dominio. Siga estos pasos para ubicar y utilizar las herramientas DNS.

1. Inicie sesión en su cuenta en el registrador de dominios y busque la página de administración de los registros DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**. A menudo se puede encontrar un vínculo a esta página al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**.

4. Cuando haya encontrado la página de administración para su nombre de dominio, busque un vínculo que le permita modificar los registros DNS. Debe aparecer como **Zone file**, **DNS Records** o un vínculo de configuración a las opciones avanzadas (**Advanced**).

	* Esta página seguramente mostrará algunos que ya se han creado, como una entrada en la que se asocia "**@**" o "*" a una página donde figuran los dominios. Es posible también que contenga registros para los subdominios más comunes, como **www**.
	* Esta página mencionará los **registros CNAME**, o bien facilitará una lista desplegable donde podrá seleccionar un tipo de registro. También es posible que mencione otros registros, como los **registros A** y los **registros MX**. En algunos casos, los registros CNAME tendrán otros nombres, como **Registro de Alias**.
	* Esta página contendrá también campos que le permiten **asignar** desde un **nombre de host** o un **nombre de dominio** hasta otro nombre de dominio.

5. Aunque los detalles varían en función del registrador que se esté utilizando, en general se asigna *desde* el nombre del dominio personalizado (como **contoso.com**,) *hasta* el nombre de dominio del Administrador de tráfico (**contoso.trafficmanager.net**) usado para la aplicación web.

6. Una vez que haya terminado de agregar o modificar registros DNS en su registrador, guarde los cambios.

<a name="enabledomain"></a>
## Habilitación del Administrador de tráfico

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=AcomDC_0114_2016-->