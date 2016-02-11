<properties 
	pageTitle="Creación de una campaña de marketing digital en Aplicaciones web del Servicio de aplicaciones de Azure" 
	description="En esta guía se ofrece información general de carácter técnico de cómo usar Aplicaciones web del Servicio de aplicaciones de Azure para crear campañas de marketing digital. Esto incluye la implementación, la integración de medios sociales, las estrategias de escalado y la supervisión." 
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

# Creación de una campaña de marketing digital en Aplicaciones web del Servicio de aplicaciones de Azure
Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) es una excelente opción para las campañas de marketing digital. Las campañas de marketing digital suelen tener corta duración y están pensadas para conseguir un objetivo de marketing a corto plazo. Hay dos escenarios principales que se han de tener en cuenta. En el primer escenario, una empresa de marketing externa crea y administra la campaña para sus clientes mientras dura la promoción. En el segundo escenario, la empresa de marketing crea y, a continuación, transfiere la titularidad de los recursos de la campaña de marketing digital a sus clientes. A continuación, el cliente ejecuta y administra la campaña de marketing digital por su cuenta. Es una excelente combinación para ambos escenarios.

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

A continuación, aparece un ejemplo de una campaña de marketing digital global multicanal que utiliza Aplicaciones web del Servicio de aplicaciones. Muestra lo que puede hacer al simplemente componer Aplicaciones web del Servicio de aplicaciones en conjunto con otros servicios con inversiones técnicas mínimas. **Haga clic en un elemento de la topografía para leer más información al respecto.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Esta guía presenta algunas de las áreas y tareas más comunes alineadas con la ejecución de una campaña de marketing digital en Aplicaciones web del Servicio de aplicaciones de Azure. Sin embargo, existentes otras soluciones comunes que puede implementar en Aplicaciones web del Servicio de aplicaciones. Para revisar estas soluciones, consulte las otras guías sobre [presencia web global](web-sites-global-web-presence-solution-overview.md) y [aplicaciones empresariales](web-sites-business-application-solution-overview.md).

## Crear desde cero o aprovechar recursos existentes

Cree rápidamente aplicaciones nuevas a partir de un CMS popular en la galería o aproveche recursos web existentes en Aplicaciones web del Servicio de aplicaciones de una variedad de lenguajes y marcos.

Azure Marketplace proporciona plantillas provenientes de los sistemas de administración de contenido (CMS) de sitios web populares, como Orchard, Umbrado, Drupal y [WordPress]. Puede crear una aplicación web usando el tipo de CMS de su preferencia. Puede elegir entre varios backends de bases de datos para satisfacer sus necesidades, incluida [Base de datos SQL de Azure] y [MySQL].

Los recursos web existentes pueden ejecutarse en Aplicaciones web, ya sean .NET, PHP, JAVA, Node.js o Python. Puede moverlos a Aplicaciones web con sus herramientas [FTP] conocidas. Si crea campañas de marketing digital con frecuencia, es posible que ya disponga de recursos web en un sistema de administración de control de código fuente. Puede implementar directamente en Aplicaciones web desde opciones de control de código fuente ya conocidas, como por ejemplo, [Visual Studio], [Visual Studio Team Services] y [Git]\: local, GitHub, BitBucket, DropBox, Mercurial, etc.

## Mantener la agilidad

Manténgase ágil publicando constantemente de manera directa desde el control de código fuente existente y ejecute pruebas A/B en Aplicaciones web del Servicio de aplicaciones.

Durante la planificación, la creación de prototipos y la implementación inicial de una aplicación web, su cliente y usted pueden consultar versiones de trabajo reales de la aplicación de campaña antes de su lanzamiento mediante la [implementación en una ranura de ensayo] de la aplicación web. Al integrar el control de código fuente con Aplicaciones web del Servicio de aplicaciones, puede [publicar de manera constante] en una ranura de ensayo y cambiarlo a producción sin tiempo de inactividad cuando esté preparado.

Además, cuando se planifican cambios en una aplicación web activa, puede [ejecutar pruebas A/B] en las actualizaciones propuestas usando la característica Probar en producción y analizar el comportamiento de usuario real para poder tomar decisiones informadas sobre el diseño de la aplicación.


## Entrar a las redes sociales

La campaña de marketing digital en Aplicaciones web del Servicio de aplicaciones se puede integrar con los medios sociales si se autentica con proveedores conocidos, como Facebook y Twitter. Si desea obtener un ejemplo de este enfoque con una aplicación ASP.NET, consulte [Crear una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure].

Además, cada sitio de medios sociales normalmente brinda información sobre otras formas de integración desde .NET y muchos otros marcos.

## Usar medios enriquecidos y llegar a todos los dispositivos

Enriquezca su campaña de marketing digital con otros servicios de Azure, como:

-  Cargue y transmita vídeos de manera global con [Servicios multimedia de Azure]
-  Envíe correos electrónicos a usuarios con [Servicio SendGrid en Azure Marketplace]
-  Establezca presencia en dispositivos Windows, iOS y Android con [Servicios móviles]
-  Envíe una notificación de inserción a millones de dispositivos con el [Centro de notificaciones]

## Globalizarse

Globalícese al servir a sitios regionales con Administrador de tráfico de Azure y entregar contenido de manera muy rápida con CDN de Azure.

Para servir a clientes globales en sus respectivas regiones, use [Administrador de tráfico de Azure] para enrutar a los visitantes del sitio a un sitio regional que proporciona el mejor rendimiento. De manera alternativa, puede distribuir la carga el sitio de manera uniforme entre varias copias de la aplicación web hospedadas en varias regiones.

Entregue el contenido estático a los usuarios de manera muy rápida globalmente mediante la [integración de su aplicación web con CDN de Azure]. CDN de Azure almacena en caché contenido estático en el [nodo de CDN] más cercano al usuario, lo que minimiza la latencia y las conexiones a su aplicación web.

## Optimizar

Optimice su aplicación web al escalar de manera automática con Autoescala, almacenar en caché con Caché en Redis de Azure, ejecutar tareas en segundo plano con WebJobs y mantener la alta disponibilidad con Administrador de tráfico de Azure.

La capacidad que tienen Aplicaciones web del Servicio de aplicaciones para [escalar vertical y horizontalmente] es perfecta para las cargas de trabajo imprevisibles, que es el caso de las campañas de marketing digital. Escale horizontalmente la aplicación web de manera manual mediante el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), programáticamente mediante la [API de administración de servicios] o [scripting de PowerShell] o de manera automática, mediante la característica Autoescala. En el nivel **Estándar**, Autoescala le permite escalar horizontalmente una aplicación web de manera automática según la utilización de CPU. Esta característica le ayuda a maximizar la agilidad y, al mismo tiempo, a minimizar el coste al escalar horizontalmente la aplicación web solo en caso de ser necesario, según la actividad del usuario. Si desea conocer los procedimientos recomendados, consulte el artículo de [Troy Hunt]\: [10 things I learned about rapidly scaling web apps with Azure] (Diez cosas que aprendí sobre el escalado rápido de aplicaciones web con Azure).

Haga que su aplicación web tenga mayor capacidad de respuesta con [Caché en Redis de Azure]. Utilícelo para almacenar datos en caché desde bases de datos de back-end y otros aspectos, como el [estado de sesiones de ASP.NET] y la [caché de resultados].

Mantenga la alta disponibilidad de la aplicación web con [Administrador de tráfico de Azure]. Mediante el método **Conmutación por error**, Administrador de tráfico enruta automáticamente el tráfico a un sitio secundario si se presenta un problema en el sitio principal.

## Supervisar y analizar

Manténgase al día con el rendimiento de su aplicación web con Azure o con herramientas de terceros. Reciba alertas sobre eventos críticos de aplicaciones web. Obtenga información de usuario fácilmente con Application Insight o con análisis de registros web desde HDInsight.

Obtenga una [vista rápida] de las cuotas de recurso y métricas de rendimiento actuales de la aplicación web en la hoja del [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) correspondiente a la aplicación web. Para obtener una vista de 360° de la aplicación a través de la disponibilidad, el rendimiento y el uso, use [Azure Application Insights] para recibir información rápida y poderosa sobre la solución de problemas, el diagnóstico y el uso. O bien, utilice una herramienta de terceros como [New Relic] para proporcionar datos de supervisión avanzados para sus aplicaciones web.

En el nivel **Estándar**, supervise las notificaciones de correo electrónico recibidas sobre la capacidad de respuesta de la aplicación cada vez que la aplicación web deje de responder. Para obtener más información, consulte [Recepción notificaciones de alerta y administración de reglas de alerta en Azure].

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
[ejecutar pruebas A/B]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Crear una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Servicios multimedia de Azure]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Servicio SendGrid en Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md
[Servicios móviles]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[Centro de notificaciones]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Administrador de tráfico de Azure]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integración de su aplicación web con CDN de Azure]: cdn-websites-with-cdn.md
[nodo de CDN]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[escalar vertical y horizontalmente]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[API de administración de servicios]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[scripting de PowerShell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Caché en Redis de Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[estado de sesiones de ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[caché de resultados]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[vista rápida]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[Recepción notificaciones de alerta y administración de reglas de alerta en Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=AcomDC_1217_2015-->