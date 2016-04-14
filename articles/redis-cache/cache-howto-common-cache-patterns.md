<properties 
   pageTitle="Patrones de caché comunes con Caché en Redis de Azure" 
   description="Aprender dónde y por qué usar Caché en Redis de Azure" 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="02/23/2016"
   ms.author="riande"/>

# Patrones de caché comunes con Caché en Redis de Azure

En esta página se enumeran las ventajas más comunes de usar una memoria caché.

## Optimización del acceso a datos con una memoria caché

El uso de una memoria caché puede acelerar considerablemente el acceso a datos realizando captura desde un almacén de datos. Una memoria caché proporciona alto rendimiento y baja latencia. Mediante la captura de datos actuales desde la memoria caché, no solamente acelera su aplicación, sino que también reduce la carga del acceso a los datos y aumenta su capacidad de respuesta para otras consultas. El almacenamiento de información en una memoria caché ayuda a ahorrar recursos y aumenta la escalabilidad a medida que las exigencias para la aplicación aumentan. Su aplicación tendrá mucha más capacidad de respuesta para cargas que se producen en ráfagas cuando pueda capturar datos de una caché.

## Estado de sesión distribuido
Aunque se recomienda evitar el uso del estado de sesión, algunas aplicaciones realmente obtienen ventaja de este uso en forma de rendimiento o complejidad reducida, mientras que otras aplicaciones requieren totalmente el estado de sesión. La opción predeterminada del proveedor de memoria para el estado de sesión no permite escalar horizontalmente (ejecución de varias instancias del sitio web). El proveedor del estado de sesión de SQL Server de ASP.NET permitirá que varios sitios web usen el estado de sesión, pero el precio será una alta latencia si se compara con un proveedor en memoria. El proveedor de caché de estado de sesión de Redis es una alternativa de baja latencia que es muy fácil de configurar e instalar. Si su aplicación usa solamente una cantidad limitada de estado de sesión, puede utilizar la mayoría de la memoria caché para almacenar en caché datos y una pequeña cantidad de estado de sesión.

## Sobrevivir al tiempo de inactividad del servicio (reserva de caché)
 Mediante el almacenamiento de datos en una memoria caché, la aplicación puede sobrevivir a errores del sistema como latencia de red, problemas con el servicio web y errores de hardware. A menudo es mejor servir datos almacenados en caché hasta que el servicio web o la base de datos se recupere que la aplicación se bloquee completamente.

## Pasos siguientes
Para obtener más información acerca del uso de Caché en Redis de Azure:
 
- [Documentación de Caché en Redis de Azure](https://azure.microsoft.com/documentation/services/cache/): esta página proporciona numerosos y excelentes vínculos para usar Caché en Redis de Azure.
- [MVC movie app with Azure Redis Cache in 15 minutes](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/) (Aplicación de película MVC con Caché en Redis de Azure en 15 minutos): la entrada de blog proporciona un inicio rápido para usar Caché en Redis de Azure en una aplicación MVC de ASP.NET.
- [Uso del estado de la sesión de ASP.NET con Sitios web Azure](../app-service-web/web-sites-dotnet-session-state-caching.md): en este tema se explica cómo utilizar el servicio Caché en Redis de Azure para el estado de sesión.

<!---HONumber=AcomDC_0224_2016-->