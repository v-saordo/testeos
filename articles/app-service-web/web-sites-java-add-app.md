<properties 
	pageTitle="Incorporación de una aplicación de Java a Aplicaciones web del Servicio de aplicaciones de Azure" 
	description="Este tutorial muestra cómo agregar una página o aplicación a su instancia de Aplicaciones web del Servicio de aplicaciones de Azure que ya está configurado para usar Java." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# Incorporación de una aplicación de Java a Aplicaciones web del Servicio de aplicaciones de Azure

Cuando haya inicializado la aplicación web de Java en [Servicio de aplicaciones de Azure][] tal como se documenta en [Creación de una aplicación web de Java en Servicio de aplicaciones de Azure](web-sites-java-get-started.md), podrá cargar su aplicación colocando su WAR en la carpeta **webapps**.

La ruta de acceso de navegación a la carpeta **webapps** varía en función de cómo haya configurado la instancia de Aplicaciones web.

- Si configura su aplicación web con Azure Martketplace, la ruta de acceso a la carpeta **webapps** tiene el formato **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**, donde **application\_server** es el nombre del servidor de la aplicación vigente en su instancia de Aplicaciones web. 
- Si configura su aplicación web con la interfaz de usuario de configuración de Azure, la ruta de acceso a la carpeta **webapps** tiene el formato **d:\\home\\site\\wwwroot\\webapps**. 

Tenga en cuenta que puede utilizar el control de código fuente para cargar la aplicación o páginas web, incluso en escenarios de integración continua. Las instrucciones para utilizar el control de código fuente con la aplicación web están disponibles en [Implementación continua con GIT en Servicio de aplicaciones de Azure](web-sites-publish-source-control.md). FTP también es una opción para cargar la aplicación o las páginas web.

Nota para aplicaciones web de Tomcat: después de cargar el archivo WAR a la carpeta **webapps**, el servidor de la aplicación Tomcat detectará que lo ha agregado y lo cargará automáticamente. Tenga en cuenta que si copia los archivos (excepto los archivos WAR) al directorio raíz, será necesario reiniciar el servidor de aplicaciones antes de utilizar esos archivos. La funcionalidad de carga automática para las aplicaciones web de Java de Tomcat que se ejecutan en Azure se basa en un nuevo archivo WAR que se agrega, o en archivos o directorios nuevos que se agregan a la carpeta **webapps**.

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0114_2016-->