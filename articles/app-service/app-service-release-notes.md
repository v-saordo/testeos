<properties 
   pageTitle="Notas de la versión de SDK de Azure para .NET 2.5.1." 
   description="Notas de la versión de SDK de Azure para .NET 2.5.1." 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="01/03/2016"
   ms.author="juliako"/>


# Notas de la versión de SDK de Azure para .NET 2.5.1.

Este documento contiene las notas de la versión de SDK de Azure para la versión .NET 2.5.1.

##Notas de la versión de SDK de Azure para .NET 2.5.1

A continuación se muestran características nuevas y actualizaciones en el SDK de Azure para .NET 2.5.1.

- Nuevas características o escenarios relacionados con las **extensiones de herramientas web**. 

	- Sitios Web Azure ha cambiado el nombre a Servicios de aplicaciones de Azure. Para obtener más información, vea [Servicios de aplicaciones de Azure y servicios de Azure existentes](app-service-changes-existing-services.md).
	- Se ha agregado compatibilidad de aplicaciones de la API de Azure (vista previa) para que los clientes puedan publicar proyectos ASP.NET como aplicaciones de la API y, a continuación, usar el gesto Agregar > Cliente de aplicaciones de la API de Azure en C# para generar código basado en la estructura de la aplicación de la API implementada. 
	- El nodo de Sitios Web en el Explorador de servidores se ha sustituido por el nodo de Servicios de aplicaciones de Azure, que incluye compatibilidad para el conjunto basado en grupos de recursos de aplicaciones web, aplicaciones móviles y aplicaciones de la API de Azure.
	- Se ha agregado compatibilidad a aplicaciones móviles de Azure (vista previa) para que los clientes pueden crear nuevos proyectos de aplicaciones móviles, agregar controladores de aplicaciones móviles, publicar los proyectos y depurar las aplicaciones de forma remota.
	- El gesto Agregar > Cliente de aplicaciones de la API de Azure ahora admite archivos JSON de Swagger locales, por lo que los desarrolladores de la API web pueden utilizar NuGets de terceros como Swashbuckle para generar Swagger o crearlo manualmente. De este modo, los desarrolladores cliente pueden usar las características de generación de código para consumir cualquier extremo Swagger en proyectos de C#. 
	- Los cuadros de diálogo de publicación de la aplicación de la API y la aplicación web se han mejorado para admitir el concepto de Portal de Azure de agrupación de recursos y la selección/creación de grupos de recursos de Azure y los planes de servicio de aplicación se representan en el nuevo cuadro de diálogo de aprovisionamiento de la aplicación de la API o de la aplicación web. 
	- Los nodos del explorador de servidores de la aplicación de la API de Azure proporcionan vínculos al vínculo profundo de aplicaciones de la API en el Portal de Azure, así como otras características como el streaming de registros y la depuración remota.

	Para saber los problemas conocidos y las limitaciones actuales del SDK de Azure para .NET 2.5.1 consulte [esta](app-service-release-notes.md#known_issues_2_5_1) sección más adelante.


- Las características o escenarios relacionados con las **herramientas de HDInsight** en Visual Studio se encuentran habilitadas en esta versión.
	- Validación local de los scripts de hive. Haga clic en el botón Validar script en la barra de herramientas para ver si hay errores en el script. 
	- Mejora de la depuración de los trabajos de Hive. Ahora puede depurar los trabajos de Hive mediante el acceso a los registros de hilo en Visual Studio. Si la aplicación tiene problemas de rendimiento, la investigación los registros de hilo proporcionará información útil.
	- (Vista previa pública) Finalización automática de palabra clave y compatibilidad con IntelliSense para Hive. Para ayudar a crear scripts de Hive, las herramientas de HDInsight para Visual Studio agregan la palabra clave de finalización automática y compatibilidad con IntelliSense para Hive.
	- Compatibilidad con Storm. Ahora puede usar herramientas de HDInsight para Visual Studio para desarrollar topologies/Spouts/Bolts de Storm en C#. A continuación, puede enviar la topología desarrollada a un clúster de Storm y ver el estado de topology/bolt/spout. Puede usar los registros del sistema y los registros de cliente para solucionar problemas de topologies/Bolts/Spouts de Storm. También puede utilizar los activos existentes de JAVA en Storm en HDInsight.
	
	Para obtener más información, consulte [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Problemas y limitaciones conocidos del SDK de Azure para .NET 2.5.1

- Las aplicaciones de la API de Azure están visibles como un destino de implementación para las aplicaciones móviles. Las aplicaciones web deben ser único destino para las aplicaciones móviles hasta una versión posterior. 
- El aprovisionamiento de aplicaciones de la API de Azure puede ser correcto pero fallar intermitentemente al actualizar el progreso en la ventana de actividad de servicio de la aplicación de Azure. La solución consiste en comprobar el estado de la nueva aplicación de la API de Azure en el Portal de Azure. 
- La experiencia de Archivo > Nuevo proyecto > Aplicación de la API > F5 produce un error HTTP porque no existe default/index.html. La solución es dirigirse manualmente a la dirección URL de /api/values. 
- De forma intermitente, los iconos del explorador de servidores aparecen planos. Si reinicia el VS se resuelve este problema. 
- Si se produce una excepción durante el aprovisionamiento de aplicaciones web o de la aplicación de la API (por ejemplo, errores de cuota excedida o nombre duplicado de puerta de enlace de aplicación de la API de Azure), los errores de mostrarán texto sin formato JSON. 
- Problemas intermitentes de la creación del proyecto cuando Application Insights se activa en el momento de creación del proyecto.
- En ocasiones, el código generado del cliente de aplicación de la API de Azure carece de espacios de nombres, deben incluirse manualmente (o importarse automáticamente a través de las indicaciones de Visual Studio) para compilar el código. 
- Los proyectos de aplicación móvil deben publicarse en aplicaciones web, pero debe elegir un sitio creado como una aplicación móvil en el Portal de Azure ya que los proyectos de aplicaciones móviles requieren una base de datos. 
- La página de inicio para las aplicaciones móviles utiliza el término "servicio móvil" en lugar de "aplicaciones móviles" 
- La creación de proyectos de aplicaciones móviles puede tardar hasta un minuto en crearse. 
- Durante el aprovisionamiento de la aplicación de la API (en algunos casos), se produce un error en la API de Azure que refleja que los permisos no podrían establecerse correctamente, mientras que la API de la aplicación se ha aprovisionado correctamente y está lista para su uso. Puede establecer manualmente los permisos mediante el Portal de Azure.
- Application Insights no es compatible con plantillas de aplicación de la API y plantillas de la aplicación móvil.
- Los proyectos de aplicación de la API no pueden usarse junto con los proyectos de servicio en la nube.
- Las plantillas de proyecto de la aplicación de la API solo están disponibles en C#.
- El consumo de la aplicación de la API a través del menú contextual "Agregar cliente de aplicación de la API de Azure" solo es compatible en C#.

 

<!---HONumber=AcomDC_0107_2016-->