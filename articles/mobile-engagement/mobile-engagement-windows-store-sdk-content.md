<properties 
	pageTitle="Contenido del SDK de Windows Universal Apps" 
	description="Obtenga información sobre el contenido del SDK de Windows Universal Apps para Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Contenido del SDK de Windows Universal Apps

En este documento se enumera y describe el contenido implementado por el SDK de la aplicación.

##La carpeta `/Resources`

En esta carpeta se incluyen todos los recursos que necesita Mobile Engagement. Además, puede personalizarlas para adaptarlas a su aplicación.

- `EngagementConfiguration.xml`: el archivo de configuración de Mobile Engagement, donde puede personalizar la configuración de Mobile Engagement (cadena de conexión de Mobile Engagement, bloqueo de informes, etc.).

### Carpeta /html

- `EngagementNotification.html` : el diseño html de vista web `Notification`.

- `EngagementAnnouncement.html` : el diseño html de vista web `Announcement`.

### Carpeta /images

- `EngagementIconNotification.png`: el icono de marca que se muestra a la izquierda de una notificación, sustituya este por el icono de la marca.

- `EngagementIconOk.png`: el icono `Ok` de las páginas de contenido de Cobertura para el botón de acción o validación.

- `EngagementIconNOK.png`: el icono `NOK` que se usa cuando se deshabilita el botón de validación de las páginas de contenido de Cobertura.
 
- `EngagementIconClose.png`: el icono `Close` de las notificaciones y el contenido de Cobertura para el botón Descartar.

### Carpeta /overlay

- `EngagementBaseOverlay.cs`: código base usado por las superposiciones `Announcement` y `Notification`.

- `EngagementOverlayAnnouncement.xaml` : el diseño xaml `Announcement`.

- `EngagementOverlayAnnouncement.xaml.cs`: el código vinculado `EngagementOverlayAnnouncement.xaml`.
 
- `EngagementOverlayNotification.xaml` : el diseño xaml `Notification`.
 
- `EngagementOverlayNotification.xaml.cs`: el código vinculado `EngagementOverlayNotification.xaml`.
 
- `EngagementPageOverlay.cs`: el código de visualización de anuncios y notificaciones de `Overlay`.
  

<!---HONumber=AcomDC_0302_2016-->