<properties 
	pageTitle="Configuración de un firewall de aplicaciones web (WAF) para entornos del Servicio de aplicaciones" 
	description="Obtenga información sobre cómo configurar un firewall de aplicaciones web delante del entorno del Servicio de aplicaciones." 
	services="app-service\web" 
	documentationCenter="" 
	authors="naziml" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="naziml"/>

# Configuración de un firewall de aplicaciones web (WAF) para entornos del Servicio de aplicaciones

## Información general ##
Los firewalls de aplicaciones web, como [Barracuda WAF para Azure](https://www.barracuda.com/programs/azure) que está disponible en [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) ayuda a proteger las aplicaciones web mediante la inspección de tráfico web entrante para bloquear ataques por inyección de código SQL, scripts de sitios, cargas de malware, DDoS de aplicaciones y otros ataques. También examina las respuestas de los servidores web back-end para prevención de pérdida de datos (DLP). Combinado con el aislamiento y la escala adicional proporcionados por los entornos del Servicio de aplicaciones, esto proporciona un entorno ideal para hospedar importantes aplicaciones web empresariales que deben soportar peticiones malintencionadas y tráfico de gran volumen.

\+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Configuración ##
Para este documento, configuraremos nuestro entorno del Servicio de aplicaciones detrás de varias instancias de equilibrio de carga de Barracuda WAF para que únicamente el tráfico del WAF pueda ponerse en contacto con el entorno del Servicio de aplicaciones, y no se podrá obtener acceso desde la red perimetral. También dispondremos del Administrador de tráfico de Azure delante de nuestras instancias de Barracuda WAF para equilibrar la carga entre las regiones y los centros de datos de Azure. Un diagrama de alto nivel del programa de instalación tendría el aspecto que se muestra a continuación.

![Arquitectura][Architecture]

## Configuración del entorno del Servicio de aplicaciones ##
Para configurar un entorno del Servicio de aplicaciones, consulte [nuestra documentación](app-service-web-how-to-create-an-app-service-environment.md) sobre el tema. Después de crear un entorno del Servicio de aplicaciones, puede crear [aplicaciones web](app-service-web-overview.md), [aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md) y [aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop.md) en este entorno, que se protegerán detrás del WAF que vamos a configurar en la sección siguiente.

## Configuración del servicio en la nube Barracuda WAF ##
Barracuda tiene un [artículo detallado](https://techlib.barracuda.com/WAF/AzureDeploy) sobre la implementación de su WAF en una máquina virtual en Azure. No obstante, habida cuenta de que queremos redundancia y no introducir un único punto de error, hay que implementar al menos dos máquinas virtuales de la instancia de WAF en el mismo servicio en la nube al seguir estas instrucciones.

### Incorporación de extremos al servicio en la nube ###
Cuando ya tenga dos o más instancias de máquina virtual de WAF en el servicio en la nube, puede usar el [Portal de Azure](https://portal.azure.com/) para agregar puntos de conexión HTTP y HTTPS que la aplicación use, como se muestra en la imagen siguiente.

![Configuración de extremos][ConfigureEndpoint]

Si las aplicaciones utilizan otros extremos, asegúrese de agregarlos a esta lista.

### Configuración de Barracuda WAF a través de su portal de administración ###
Barracuda WAF utiliza el puerto TCP 8000 para la configuración a través de su portal de administración. Como tenemos varias instancias de máquinas virtuales de WAF, tendrá que repetir los pasos siguientes para cada instancia de máquina virtual.


> Nota: Una vez que haya terminado con la configuración de WAF, quite el extremo TCP/8000 de todas las máquinas virtuales de WAF para preservar la seguridad del WAF.

Agregue el extremo de administración como se muestra en la imagen siguiente para configurar Barracuda WAF.

![Incorporación de extremos de administración][AddManagementEndpoint]
 
Utilice un explorador para examinar el extremo de administración en el servicio en la nube. Si el servicio en la nube se llama test.cloudapp.net, podría acceder a este extremo entrando en http://test.cloudapp.net:8000. Debería ver una página de inicio de sesión como la siguiente en la que puede iniciar sesión con las credenciales especificadas en la fase de instalación de la máquina virtual de WAF.

![Página de inicio de sesión de administración][ManagementLoginPage]

Tras iniciar sesión, debe aparecer un panel de información como el de la imagen siguiente que presentará estadísticas básicas sobre la protección de WAF.

![Panel de administración][ManagementDashboard]

Al hacer clic en la pestaña Servicios, le permitirá configurar el WAF para los servicios que protege. Para obtener más información sobre cómo configurar Barracuda WAF, puede consultar [su documentación](https://techlib.barracuda.com/waf/getstarted1). En el ejemplo siguiente, se ha configurado una aplicación web de Azure que atiende el tráfico en HTTP y HTTPS.

![Administración de agregar servicios][ManagementAddServices]

> Nota: Dependiendo de cómo se configuran las aplicaciones y qué características se utilizan en el entorno del Servicio de aplicaciones, deberá reenviar el tráfico para los puertos TCP distintos del 80 y el 443, por ejemplo, si ha configurado SSL de IP para una aplicación web. Para obtener una lista de los puertos de red usados en entornos del Servicio de aplicaciones, vea la sección dedicada a los puertos de red en la [documentación del control del tráfico de entrada](app-service-app-service-environment-control-inbound-traffic.md).

## Configuración del Administrador de tráfico de Microsoft Azure (OPCIONAL) ##
Si la aplicación se encuentra disponible en varias regiones, debe equilibrar la carga entre ellas detrás del [Administrador de tráfico de Azure](../traffic-manager/traffic-manager-overview.md). Para ello, puede agregar un punto de conexión en el [Portal de Azure clásico](https://manage.azure.com) con el nombre del servicio en la nube de su WAF en el perfil del Administrador de tráfico, como se muestra en la imagen siguiente.

![Extremo del Administrador de tráfico][TrafficManagerEndpoint]

Si la aplicación requiere autenticación, asegúrese de que tiene algún recurso que no requiere ninguna autenticación para que el Administrador de tráfico haga ping para la disponibilidad de la aplicación. Puede configurar la dirección URL en la sección Configuración del [Portal de Azure clásico](https://manage.azure.com), como se muestra a continuación.

![Configuración del Administrador de tráfico][ConfigureTrafficManager]

Para reenviar los pings del Administrador de tráfico desde el WAF a la aplicación, debe configurar las traducciones de sitios web en Barracuda WAF para reenviar el tráfico a la aplicación, tal como se muestra en el ejemplo siguiente.

![Traducciones de sitios web][WebsiteTranslations]

## Protección del tráfico en el entorno del Servicio de aplicaciones mediante grupos de recursos de red##
Vea la [documentación sobre el control del tráfico de entrada](app-service-app-service-environment-control-inbound-traffic.md) para obtener detalles sobre la restricción del tráfico en el entorno del Servicio de aplicaciones desde el WAF solo mediante la utilización de la dirección IP virtual del servicio en la nube. A continuación se muestra un comando PowerShell de ejemplo para realizar esta tarea para el puerto TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Reemplace SourceAddressPrefix con la dirección IP virtual (VIP) del servicio en la nube del WAF.

> Nota: La dirección VIP del servicio en la nube cambiará al eliminar y volver a crear el servicio en la nube. Asegúrese de actualizar la dirección IP en el grupo de recursos de red una vez hecho esto.
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png

<!---HONumber=AcomDC_0302_2016-->