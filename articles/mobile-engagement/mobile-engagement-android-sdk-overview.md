<properties
	pageTitle="Integración del SDK de Android para Azure Mobile Engagement"
	description="Procedimientos y actualizaciones más recientes para el SDK de Android para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="piyushjo" />


#SDK de Android para Azure Mobile Engagement

Comience aquí para obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación Android. Si primero quiere probarlo, asegúrese de seguir nuestro [tutorial de 15 minutos](mobile-engagement-android-get-started.md).

Haga clic para ver el [contenido del SDK](mobile-engagement-android-sdk-content.md).

##Procedimientos de integración
1. Comience aquí: [Integración de Mobile Engagement en su aplicación Android](mobile-engagement-android-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Integración de GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Integración de ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implementación del plan de etiquetas: [Uso de la API de etiquetado de Mobile Engagement avanzada en su aplicación Android](mobile-engagement-android-use-engagement-api.md)


##Notas de la versión

##4\.1.5 (02/01/2016)

- Mejoras de estabilidad.

##4\.1.0 (08/25/2015)

- Controle el nuevo modelo de permiso para Android M.
- Ahora puede configurar las características de ubicación en tiempo de ejecución en lugar de usar `AndroidManifest.xml`.
- Corrija un error de permiso: si usa `ACCESS_FINE_LOCATION`, ya no necesita `ACCESS_COARSE_LOCATION`.
- Mejoras de estabilidad.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-android-release-notes.md).

##Procedimientos de actualización

Si ya ha integrado una versión anterior de nuestro SDK en su aplicación, consulte [Procedimientos de actualización](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_0204_2016-->