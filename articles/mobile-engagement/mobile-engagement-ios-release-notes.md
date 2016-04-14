<properties
	pageTitle="Notas de la versión del SDK de iOS de Azure Mobile Engagement"
	description="Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="MehrdadMzfr" />

#Notas de la versión

##3\.2.1 (12/11/2015)

-   Se ha corregido el retraso que se produce cuando se desencadena una nueva instancia de aplicación por una notificación con vínculos profundos. 

##3\.2.0 (10/08/2015)

-   Se habilitó Bitcode en el SDK para que funcione con **Xcode 7**.
-   Se corrigieron los errores relacionados con las notificaciones desde la aplicación.
-   Las notificaciones desde la aplicación ahora son más fiables en caso de que, por ejemplo, la batería esté baja.
-   Se quitaron registros de la consola adicionales generados por la biblioteca de terceros.

##3\.1.0 (08/26/2015)

-   Corrija los errores de compatibilidad de iOS 9 con una biblioteca de terceros. Generaban bloqueos al enviar resultados de sondeo, información de la aplicación o datos adicionales.

##3\.0.0 (19/06/2015)

-   Mobile Engagement usa notificaciones push silenciosas.
-   Soporte de iOS 4.X eliminado. A partir de esta versión, el destino de implementación de la aplicación debe ser como mínimo iOS 6.

##2\.2.0 (05/21/2015)

-   El identificador de dispositivo de Mobile Engagement para dispositivos anteriores a iOS 6 ahora se basa en un GUID que se genera durante la instalación.

##2\.1.0 (24/04/2015)

-   Agregada compatibilidad con Swift.
-   Al hacer clic en una notificación, la dirección URL de la acción se ejecuta ahora justo después de abrir la aplicación.
-   Se ha agregado el archivo de encabezado que faltaba en el paquete del SDK.
-   Se ha corregido un problema cuando se deshabilitaba el generador de informes de error de Mobile Engagement.

##2\.0.0 (02/17/2015)

-   Versión inicial de Azure Mobile Engagement
-   La configuración de appId o sdkKey se sustituye por una configuración de la cadena de conexión.
-   Se ha eliminado la API para enviar y recibir mensajes XMPP arbitrarios de entidades XMPP arbitrarias.
-   Se ha eliminado la API para enviar y recibir mensajes entre dispositivos.
-   Mejoras de seguridad.
-   Se ha eliminado el seguimiento de SmartAd.

<!---HONumber=AcomDC_0302_2016-->