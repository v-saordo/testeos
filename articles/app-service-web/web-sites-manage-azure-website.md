<properties 
	pageTitle="Administración de una aplicación web en Servicio de aplicaciones de Azure" 
	description="Vínculos a recursos para administrar una aplicación web en Servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="erikre" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/12/2016" 
	ms.author="tdykstra"/>

# Administración de una aplicación web en Servicio de aplicaciones de Azure

Este tema contiene vínculos a recursos para administrar una aplicación web en [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). La administración incluye todas las tareas que hacen que una aplicación web funcione sin problemas.

A lo largo de la vida de una aplicación web, llevará a cabo distintas tareas de administración, desde la implementación inicial hasta el funcionamiento normal, pasando por el mantenimiento y las actualizaciones.

Muchas tareas de administración de aplicaciones web se pueden hacer desde el Portal de Azure.

## Antes de implementar su aplicación web para la producción.

### Elija un nivel

Servicio de aplicaciones de Azure se ofrece en cinco niveles: gratis, compartido, básico, estándar y premium. Para obtener información acerca de las características y los precios de cada nivel, consulte [Detalles de precios](/pricing/details/app-service/).

- Los [planes del Servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) le permiten agrupar varias aplicaciones web en el mismo nivel.
- Siempre puede [cambiar los niveles](web-sites-scale.md) después de crear su aplicación web.

### Configuración

Use el [Portal de Azure](https://portal.azure.com/) para establecer varias opciones de configuración. Para obtener detalles, consulte [Configuración de aplicaciones en el Servicio de aplicaciones de Azure](web-sites-configure.md). Esta es una lista de comprobación breve:

- Seleccione **versiones de tiempo de ejecución** para .NET, PHP, Java o Python, si es necesario.
- Habilite **WebSockets** si su aplicación web usa el protocolo WebSocket. (Se incluyen las aplicaciones que usan [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](web-sites-nodejs-chat-app-socketio.md)).
- ¿Ejecuta trabajos web continuamente? Si es así, habilite la opción **Always On**.
- Establezca el **documento predeterminado**, como index.html.

Además de estas opciones de configuración básicas, puede configurar lo siguiente:

- Cifrado de **Capa de sockets seguros (SSL)**. Para usar SSL con un nombre de dominio personalizado, debe obtener un certificado SSL y configurar la aplicación web para que lo use. Consulte [Habilitación de HTTPS para una aplicación web en el Servicio de aplicaciones de Azure](web-sites-configure-ssl-certificate.md).
- **Nombre de dominio personalizado.** Su aplicación web tiene automáticamente un subdominio en azurewebsites.net. Puede asociar un nombre de dominio personalizado, como contoso.com. Consulte [Configuración de un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](web-sites-custom-domain-name.md).

Configuración específica por idioma:

- **PHP**: [configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure](web-sites-php-configure.md).
- **Python**: [configuración de Python con Aplicaciones web del Servicio de aplicaciones de Azure](web-sites-python-configure.md).


## Cuando su aplicación web está en ejecución

Cuando la aplicación web está en ejecución, querrá asegurarse de que está disponible y de que se escala para satisfacer el tráfico de los usuarios. También puede necesitar solucionar errores.

### Supervisión

- Desde el Portal de Azure, puede [agregar métricas de rendimiento](web-sites-monitor.md), como el uso de la CPU y el número de solicitudes de clientes.
- Si quiere más detalles, use New Relic para supervisar y administrar el rendimiento. Consulte [Aplicación web de .NET en el Servicio de aplicaciones de Azure con administración del rendimiento de la aplicación New Relic](store-new-relic-web-sites-dotnet-application-performance-management.md).
- [Escale su aplicación web](web-sites-scale.md) en respuesta al tráfico. En función de su nivel, puede escalar el número de máquinas virtuales y/o el tamaño de las instancias de las máquinas virtuales. En los niveles estándar y premium, también puede configurar la autoescala para que la aplicación web escale automáticamente, bien según una programación fija, o bien en respuesta a la carga.  
 
### Copias de seguridad

- Establezca las [copias de seguridad automáticas](web-sites-backup.md) de la aplicación web. Obtenga más información sobre las copias de seguridad en [este vídeo](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Conozca las opciones para la [recuperación de bases de datos](../sql-database/sql-database-business-continuity.md) en Base de datos SQL de Azure.

### Solución de problemas

- Si se produce algún error, puede [solucionar los problemas en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) usando registros de diagnóstico y depuración activa en la nube. 
- Fuera de Visual Studio, hay varias formas de recopilar registros de diagnóstico. Consulte [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-enable-diagnostic-log.md).
- Para aplicaciones Node.js, consulte [Depuración de una aplicación Node.js en Servicio de aplicaciones de Azure](web-sites-nodejs-debug.md).

### Restauración de datos

- [Restaure](web-sites-restore.md) una aplicación web de la que se creó una copia de seguridad anteriormente.


## Cuando actualiza la aplicación web

Si no ha habilitado las copias de seguridad automáticas, puede crear una [copia de seguridad manual](web-sites-backup.md).

Puede usar una [implementación de ensayo](web-sites-staged-publishing.md). Esta opción le permite publicar actualizaciones en una implementación provisional que se ejecuta en paralelo a su implementación de producción.

Si usa Visual Studio Team Services, puede configurar una implementación continua desde el control de código fuente:

- [Uso del control de versiones de Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [Uso de Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
<!-- Anchors. -->


[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  

<!---HONumber=AcomDC_0211_2016-->