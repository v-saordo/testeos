<properties 
	pageTitle="Creación de una presencia web global en Aplicaciones web del Servicio de aplicaciones de Azure" 
	description="En esta guía se ofrece información general de carácter técnico acerca de cómo hospedar el sitio (.COM) de su organización en Aplicaciones web del Servicio de aplicaciones de Azure. Esto incluye la implementación, los dominios personalizados, SSL y la supervisión." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="cephalin"/>


# Creación de una presencia web global en Aplicaciones web del Servicio de aplicaciones de Azure

Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) tiene todas las funcionalidades necesarias para establecer una presencia web global para su sitio .COM. Independientemente del tamaño de la organización, necesita una plataforma sólida, segura y escalable para impulsar su negocio, el reconocimiento de su marca y las comunicaciones de su cliente. Aplicaciones web del Servicio de aplicaciones puede ayudar a mantener su identidad y marca corporativa con la continuidad empresarial respaldada por Microsoft.

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

A continuación, aparece un ejemplo de un sitio web .COM que se ejecuta en Aplicaciones web del Servicio de aplicaciones. Muestra lo que puede hacer al simplemente componer Aplicaciones web en conjunto con otros servicios con inversiones técnicas mínimas. **Haga clic en un elemento de la topografía para leer más información al respecto.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Esta guía presenta algunas de las tareas y áreas más comunes alineadas con la ejecución de un sitio .COM de acceso público en Aplicaciones web del Servicio de aplicaciones de Azure. Sin embargo, existen otras soluciones comunes que puede implementar en Aplicaciones web del Servicio de aplicaciones de Azure. Para revisar estas soluciones, consulte también las otras guías sobre [campañas de marketing digital](web-sites-digital-marketing-application-solution-overview.md) y [aplicaciones empresariales](web-sites-business-application-solution-overview.md).

## Crear desde cero o aprovechar recursos existentes

Cree rápidamente sitios nuevos a partir de un CMS popular en la galería o aproveche recursos web existentes en Aplicaciones web del Servicio de aplicaciones de una variedad de lenguajes y marcos.

Azure Marketplace proporciona plantillas provenientes de los sistemas de administración de contenido (CMS) de sitios web populares, como Orchard, Umbrado, Drupal y [WordPress]. Puede crear una aplicación web usando el tipo de CMS de su preferencia. Puede elegir entre varios backends de bases de datos para satisfacer sus necesidades, incluida [Base de datos SQL de Azure] y [MySQL].

Los recursos web existentes pueden ejecutarse en Aplicaciones web del Servicio de aplicaciones, ya sean .NET, PHP, Java, Node.js o Python. Puede moverlos a Aplicaciones web con sus herramientas [FTP] conocidas o el sistema de administración de control de código fuente. El servicio Aplicaciones web admite la publicación directa desde opciones de control de código fuente conocidas, como [Visual Studio], [Visual Studio Team Services] y [Git]\: local, GitHub, BitBucket, DropBox, Mercurial, etc.

## Publicar de manera confiable

Publique su sitio web de manera confiable publicando de manera continua directamente desde el sistema de control de código fuente existente y pruebe el contenido de manera activa.

Durante la planificación, la creación de prototipos y la implementación inicial de un sitio, puede consultar versiones de trabajo reales del sitio web antes de su lanzamiento mediante la [implementación en una ranura de ensayo] del sitio en Aplicaciones web del Servicio de aplicaciones. Al integrar el control de código fuente con Aplicaciones web, puede [publicar de manera constante] en una ranura de ensayo y cambiarlo a producción sin tiempo de inactividad cuando esté preparado para hacerlo. Si algo sale mal en el sitio de producción, también puede cambiarlo de inmediato por una versión anterior del sitio.

Además, cuando se planifican cambios en un sitio web activo, puede ejecutar [pruebas A/B] fácilmente en las actualizaciones propuestas usando la característica Probar en producción y analizar el comportamiento de usuario real para poder tomar decisiones informadas sobre el diseño del sitio.

## Marca y seguridad

Utilice el dominio de Aplicaciones web del Servicio de aplicaciones gratis o asigne a su nombre de dominio registrado y, a continuación, proteja su marca con su certificado SSL firmado de CA.

El dominio ***.azurewebsites.net** es gratuito cuando ejecuta su sitio web en Aplicaciones web. O bien puede asignar su sitio web a un [dominio personalizado] (por ejemplo, contoso.com) obtenido desde cualquier registro de DNS, como GoDaddy.

Si recopila cualquier información de usuario, realiza actividades de comercio electrónico o administra cualquier otra información confidencial, puede proteger la reputación de su marca y a sus clientes con [HTTPS]. El nombre de dominio ***.azurewebsites.net** ya incluye un certificado SSL y si usa el dominio personalizado, puede aprovechar su certificado SSL para el mismo en Aplicaciones web. Todo certificado SSL tiene asociado un cobro mensual (prorrateado por hora). Para obtener más información, consulte [Detalles de precios del Servicio de aplicaciones].

## Globalizarse

Globalícese al servir a sitios regionales con Administrador de tráfico de Azure y entregar contenido de manera muy rápida con CDN de Azure.

Para servir a clientes globales en sus respectivas regiones, use [Administrador de tráfico de Azure] para enrutar a los visitantes del sitio a un sitio regional que proporciona el mejor rendimiento. De manera alternativa, puede distribuir la carga del sitio de manera uniforme entre varias copias del sitio web hospedadas en varias regiones.

Entregue el contenido estático a los usuarios de manera muy rápida globalmente mediante la [integración de su aplicación web con CDN de Azure]. CDN de Azure almacena en caché contenido estático en el [nodo CDN] más cercano al usuario, lo que minimiza la latencia y las conexiones a su sitio web.

## Optimizar

Optimice su sitio .COM al escalar de manera automática con Autoescala, almacenar en caché con Caché en Redis de Azure, ejecutar tareas en segundo plano con WebJobs y mantener la alta disponibilidad con Administrador de tráfico de Azure.

La capacidad que las Aplicaciones web del Servicio de aplicaciones tienen para [escalar vertical y horizontalmente] satisface la necesidad de su sitio .COM, independientemente del tamaño de la carga de trabajo. Escale horizontalmente el sitio web de manera manual mediante el [Portal de Azure](https://portal.azure.com), programáticamente mediante la [API de administración de servicios] o [scripting de PowerShell] o de manera automática, mediante la característica Autoescala. En el plan de hospedaje **Estándar**, la característica Autoescala le permite escalar horizontalmente un sitio web de manera automática según la utilización de CPU. Si desea conocer los procedimientos recomendados, consulte el artículo de [Troy Hunt]\: [10 things I learned about rapidly scaling web apps with Azure] (Diez cosas que aprendí sobre el escalado rápido de aplicaciones web con Azure).

Haga que su sitio web tenga mayor capacidad de respuesta con [Caché en Redis de Azure]. Utilícelo para almacenar datos en caché desde bases de datos de back-end y otros aspectos, como el [estado de sesiones de ASP.NET] y la [caché de resultados].

Mantenga la alta disponibilidad del sitio web mediante [Administrador de tráfico de Azure]. Mediante el método **Conmutación por error**, Administrador de tráfico enruta automáticamente el tráfico a un sitio secundario si se presenta un problema en el sitio principal.

## Supervisar y analizar

Manténgase al día con el rendimiento de su sitio web con Azure o con herramientas de terceros. Reciba alertas sobre eventos críticos de sitio web. Obtenga información de usuario fácilmente con Application Insight o con análisis de registros web desde HDInsight.

Obtenga una [vista rápida] de las cuotas de recurso y métricas de rendimiento actuales del sitio web en la hoja del [Portal de Azure](https://portal.azure.com) correspondiente a la aplicación web. Para obtener una vista de 360° de la aplicación a través de la disponibilidad, el rendimiento y el uso, use [Azure Application Insights] para recibir información rápida y poderosa sobre la solución de problemas, el diagnóstico y el uso. O bien, utilice una herramienta de terceros como [New Relic] para proporcionar datos de supervisión avanzados para sus sitios web.

En el plan de hospedaje **Estándar**, supervise las notificaciones de correo electrónico recibidas sobre la capacidad de respuesta del sitio cada vez que el sitio deje de responder. Para obtener más información, consulte [Recepción notificaciones de alerta y administración de reglas de alerta en Azure].

## Usar medios enriquecidos y llegar a todos los dispositivos

Haga que su sitio .COM sea atractivo con medios enriquecidos, como:

-  Cargue y transmita vídeos de manera global con [Servicios multimedia de Azure]
-  Envíe correos electrónicos a usuarios con [Servicio SendGrid en Azure Marketplace]

## Más recursos

- [Documentación de Aplicaciones web del Servicio de aplicaciones](/services/app-service/web/)
- [Mapa de aprendizaje para Aplicaciones web del Servicio de aplicaciones de Azure](websites-learning-map.md)
- [Blog web de Azure](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


[Azure App Service]: /services/app-service/web/

[WordPress]: web-sites-php-web-site-gallery.md
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Base de datos SQL de Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Team Services]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[implementación en una ranura de ensayo]: web-sites-staged-publishing.md
[publicar de manera constante]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[pruebas A/B]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[dominio personalizado]: web-sites-custom-domain-name.md
[HTTPS]: web-sites-configure-ssl-certificate.md
[Detalles de precios del Servicio de aplicaciones]: /pricing/details/app-service/#ssl-connections

[Administrador de tráfico de Azure]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integración de su aplicación web con CDN de Azure]: cdn-websites-with-cdn.md
[nodo CDN]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[escalar vertical y horizontalmente]: web-sites-scale.md
[Azure Management Portal]: http://manage.windowsazure.com/
[API de administración de servicios]: https://msdn.microsoft.com/library/azure/ee460799.aspx
[scripting de PowerShell]: https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Caché en Redis de Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[estado de sesiones de ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[caché de resultados]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[vista rápida]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Recepción notificaciones de alerta y administración de reglas de alerta en Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Servicios multimedia de Azure]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Servicio SendGrid en Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md

 

<!---HONumber=AcomDC_1217_2015-->