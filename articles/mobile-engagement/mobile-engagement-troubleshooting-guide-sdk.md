<properties 
   pageTitle="Guía de solución de problemas de Azure Mobile Engagement - SDK" 
   description="Solución de problemas de integración de SDK en Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/29/2016"
   ms.author="piyushjo"/>

# Guía de solución de problemas de la integración de SDK

Los siguientes son posibles problemas que pueden producirse con cómo Azure Mobile Engagement se integra en la aplicación.

## Problemas SDK descubiertos por un error en otra área de la aplicación

### Problema
- Error de colección de datos de interfaz de usuario (en el análisis, supervisión, segmentación o paneles).
- Errores de inserción (las inserciones no funcionan en la aplicación, fuera de la aplicación o en ambos).
- Fallos de función avanzados (no funcionan el seguimiento, la geolocalización o las inserciones específicas de plataforma).
- Errores de la API (las API fallan a menudo de manera silenciosa sin que se muestren mensajes de error).
- Errores del servicio (ninguno de los Azure Mobile Engagement funciona para su aplicación).

### Causas

- La mayoría de errores que necesitan resolverse con el SDK de Azure Mobile Engagement se descubrirán mediante un fallo en su aplicación (por ejemplo, un error de recopilación de datos de interfaz de usuario, un error de inserción, un error de función avanzada, un error de API, bloqueos de aplicación o una interrupción del servicio aparente).  
- Si una función determinada de Azure Mobile Engagement nunca ha funcionado en la aplicación antes, deberá completar la integración. 
- Si una función determinada de Azure Mobile Engagement funcionaba y se detuvo, puede que necesite actualizar a la última versión con el SDK de Azure Mobile Engagement. Recuerde que hay una versión diferente del SDK de Azure Mobile Engagement para cada plataforma compatible con Azure Mobile Engagement (Android, iOS, Windows y Windows Phone).

#### Integración de SDK

- Azure Mobile Engagement no integrado correctamente en el SDK (análisis).
- Cobertura no integrada correctamente en SDK (inserciones dentro y fuera de la aplicación).
- Certificado caducado o PROD frente a DEV incorrecto (únicamente iOS).
- GCM o ADM incorrectamente integrado en el SDK (solo en Android - Inserciones específicas del servicio).
- Seguimiento incorrectamente integrado en el SDK (instalar el seguimiento de la tienda).
- Ubicación diferida o ubicación de GPS incorrectamente integrada en el SDK (orientación mediante ubicación geográfica).


**Consulte también:**

- [Documentación del SDK - Guías de integración][Link 5] 
- [Guía de solución de problemas - Push][Link 23]

#### Actualización del SDK

- Debe actualizar el SDK para resolver problemas con versiones anteriores del SDK (a menudo relacionados con las versiones más recientes del sistema operativo del dispositivo).
- Desinstale todas las versiones anteriores de la aplicación del dispositivo y vuelva a instalar la versión más reciente de la aplicación, vuelva a registrar el identificador de dispositivo desde la interfaz de usuario de Azure Mobile Engagement para confirmar que el dispositivo está utilizando la versión más reciente de la aplicación.

**Consulte también:**

- [Documentación del SDK - Notas de la versión](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Documentación del SDK - Guía de actualización](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### Otros errores del SDK

- Los errores en el manifiesto de la aplicación "AndroidManifest.xml" pueden provocar que Azure Mobile Engagement no funcione (solo en Android).
- Un problema común con la integración del SDK y el uso de la API es confundir la clave del SDK y la clave de la API.

**Consulte también:**

- [Conceptos - Glosario][Link 6]

## Problemas de codificación avanzados

### Problema
-  Un código específico de plataforma no relacionado directamente con Azure Mobile Engagement puede causar problemas en Windows Phone, iOS y Android.

### Causas

- Muchos problemas de codificación avanzada con Azure Mobile Engagement están provocados por códigos específicos de plataforma escritos incorrectamente no relacionados directamente con Azure Mobile Engagement. Deberá consultar la documentación específica de la plataforma para la que está desarrollando además de la documentación de Azure Mobile Engagement (Android, iOS, Web, Windows y Windows Phone).
- No configurar correctamente las "categorías" impide la vinculación desde una notificación a otra ubicación dentro o fuera de la aplicación (solo Android). 
- No establecer "UIKit.framework" en "opcional" en el código de iOS provoca que se muestre un "Error de símbolo no encontrado" o bloqueos en los dispositivos iOS antiguos (únicamente en iOS).
- Los certificados caducados o que no usan la versión de DEV o PROD correctamente causan errores de inserción (únicamente iOS).
- Existen algunas limitaciones inherentes a una plataforma que Azure Mobile Engagement no puede controlar (por ejemplo, cómo funciona el centro del sistema para las inserciones fuera de la aplicación en iOS y Android).
- Azure Mobile Engagement publica una lista completa de los paquetes internos utilizados por Azure Mobile Engagement para iOS y Android a modo de referencia. Tenga en cuenta que algunas funciones de Azure Mobile Engagement son específicas de la plataforma (Android, iOS, Web, Windows y Windows Phone).

### Consulte también

 - [Guía de solución de problemas - Push][Link 23] 
 - [Documentación del SDK - Notas de la versión][Link 5]
 - [Documentación del SDK - Guía de actualización][Link 5]

## Bloqueos de la aplicación

### Problema
- La aplicación se bloquea en el dispositivo de los usuarios finales.

### Causas

- La información del bloqueo puede verse en la *interfaz de usuario del análisis* o la *API del análisis*.
- Puede encontrar el identificador del dispositivo de su dispositivo de prueba y realizar la misma acción que provocó que la aplicación se bloquee para un usuario final para ayudar a identificar la causa de su bloqueo.
- Los problemas conocidos con el SDK de Azure Mobile Engagement que provocan que las aplicaciones se bloqueen a veces se resuelven con la actualización a la versión más reciente del SDK. Asegúrese de comprobar las notas de la versión de la plataforma cuando investigue los bloqueos.

### Consulte también

- [Documentación del SDK - Notas de la versión][Link 5]
- [Documentación del SDK - Guía de actualización][Link 5]

## Fallos de carga de la tienda de aplicaciones

### Problema
- Errores relacionados con la carga de la versión más reciente de su aplicación a Apple, Google o la tienda de aplicaciones Windows.

### Causas

- Las tiendas de aplicaciones a veces bloquean aplicaciones con ciertas funciones habilitadas (por ejemplo, Apple Store impide el uso de IDFV en las aplicaciones de la tienda y la tienda GooglePlay impide el uso compartido de información de la aplicación entre las aplicaciones). 
- Asegúrese de comprobar las notas de la versión acerca de la plataforma y la versión actual del SDK si tiene dificultades para cargar una aplicación en la tienda.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=AcomDC_0302_2016-->