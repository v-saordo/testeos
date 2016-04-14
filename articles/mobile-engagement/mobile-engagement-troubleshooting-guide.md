<properties 
   pageTitle="Azure Mobile Engagement: Guía de solución de problemas" 
   description="Guía de solución de problemas de Azure Mobile Engagement" 
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
   ms.date="02/26/2016"
   ms.author="piyushjo"/>

# Azure Mobile Engagement: guía de solución de problemas

## Introducción
La siguiente guía de solución de problemas le ayudará a comprender las causas de algunos problemas habituales y le permitirá solucionarlos por sí mismo.

## General

En general, siempre debe asegurarse de lo siguiente:

1. Realizó todos los pasos necesarios para la integración, tal como se describe en nuestros [tutoriales de introducción](mobile-engagement-windows-store-dotnet-get-started.md).
2. Usa la versión más reciente de los SDK de la plataforma. 
3. Realiza pruebas tanto en un dispositivo real como en un emulador, porque algunos problemas son específicos solo del emulador. 
4. No supera los límites de Mobile Engagement que se indican [aquí](../azure-subscription-service-limits.md).
5. Si no se puede conectar con el back-end del servicio Mobile Engagement o si ve que los datos no se cargan de forma continuada, asegúrese de que no hay incidentes de servicio activos comprobándolo [aquí](https://azure.microsoft.com/status/).

## Problemas de 'Supervisar'

### No veo mi dispositivo en la pestaña Supervisar
La pestaña Supervisar muestra los dispositivos conectados a la plataforma Mobile Engagement en tiempo real. Si depura en un emulador y un dispositivo, debe ver aquí al menos una sesión. Si la aplicación se distribuyó, verá que el medidor Sesiones activas refleja los dispositivos conectados a la plataforma en tiempo real.

Si no ve el dispositivo en la pestaña Supervisar, es probable que se trate de un problema de integración del SDK. A continuación se indican algunos pasos habituales para solucionarlo:

1. Asegúrese de que usa la cadena de conexión correcta en la aplicación móvil y que procede de la sección de claves del SDK y no de la sección de claves de la API. La cadena de conexión conecta la aplicación móvil a la sesión de la aplicación Mobile Engagement en cuya pestaña Supervisar verá el dispositivo. 
2. Para la plataforma Windows: si la página invalida el método `OnNavigatedTo`, asegúrese de llamar a `base.OnNavigatedTo(e)`.
3. Si integra Mobile Engagement en una aplicación móvil existente, también puede asegurarse de que no falta ningún paso examinando los pasos de integración avanzada [aquí](mobile-engagement-windows-store-integrate-engagement.md).
4. Asegúrese de que envía al menos una pantalla o actividad sustituyendo la página con EngagementActivity en función de la plataforma con la que está trabajando, tal como se describe en los [tutoriales de introducción](mobile-engagement-windows-store-dotnet-get-started.md).

### Veo que la pestaña Supervisar muestra una sesión aunque he desconectado o cerrado mi aplicación o emulador. 
Si no hay nadie más conectado a la plataforma en este momento y usa un emulador para abrir la aplicación, es probable que se deba a una peculiaridad del emulador. En general, debe asegurarse de que vuelve a la pantalla de inicio del emulador para que la sesión de la aplicación se desconecte correctamente. Además, en la plataforma Windows, durante la depuración con Visual Studio, puede que necesite asegurarse de que la sesión se cierra realmente; para ello, vaya a la barra de menús **Eventos de ciclo de vida** y haga clic en **Suspender**. Consulte el [tutorial de Windows](mobile-engagement-windows-store-dotnet-get-started.md) para obtener más información.

## Problemas de 'Análisis'

### No veo ningún dato o datos actualizados en la pestaña Análisis 
Los datos de análisis se recalculan periódicamente, pero la actualización puede tardar hasta 24 horas. No son datos en tiempo real y aparecerán actualizados en este margen de 24 horas. Asegúrese, sin embargo, de que envía al menos una pantalla o actividad al back-end de la plataforma, ya sea mediante la sustitución al menos de una página con `EngagementActivity` o mediante la llamada explícita a `SendActivity`.

### Veo datos de fecha/hora incorrectos capturados para un dispositivo en la pestaña Análisis
El período de tiempo de Análisis se basa en la fecha de la configuración del dispositivo de los usuarios. Asegúrese por tanto de que el dispositivo tiene establecida la fecha correcta.

## Problemas de 'Segmento'

### Creé un segmento y se muestra atenuado o no muestra ningún dato
La creación del segmento no se realiza en tiempo real por el momento. Se calcula al mismo tiempo que se agregan los datos de análisis, por lo que puede tardar hasta 24 horas. Debe comprobarlo de nuevo más tarde, pero mientras tanto también debe asegurarse de que las aplicaciones móviles realmente envían los datos a partir de los cuales se forman los segmentos. Por ejemplo, si ningún dispositivo móvil está enviando un evento "test", no habrá datos para que se cree un segmento con EventName = test como criterio. También debe comprobar la integración de SDK para garantizar que la aplicación móvil está enviando los datos correctamente.

## Problemas de 'Alcance' o de notificaciones push

### Los mensajes de inserción no se entregan 

1. Intente enviar notificaciones a un dispositivo de prueba en primer lugar para asegurarse de que todos los componentes: aplicación móvil, SDK y el servicio, están conectados correctamente y pueden entregar notificaciones push. 
2. Envíe siempre la 'notificación fuera de aplicación' más sencilla antes a través de una campaña que no esté programada ni tenga criterios de audiencia especificados. De nuevo, esto se realiza para comprobar que la conectividad de notificación funciona correctamente. 
3. Si tiene problemas para entregar notificaciones en aplicación, también es buena idea intentar enviar antes una notificación fuera de aplicación. 
4. Asegúrese de que la 'Inserción nativa' está configurada correctamente para la aplicación móvil. En función de la plataforma implicará claves (Android, Windows) o certificados (iOS). Consulte [Interfaz de usuario: configuración](mobile-engagement-user-interface-settings.md)
5. El usuario también podría haber bloqueado las notificaciones fuera de aplicación mediante el sistema operativo móvil, por lo que debe asegurarse de que no es este el caso. 
6. Asegúrese de que no ha establecido la opción *Ignorar audiencia, la inserción se enviará a los usuarios a través de la API* en la sección **Campaña** de una campaña de Alcance, ya que esto haría que las notificaciones push solo se pudiesen enviar a través de API. 
7. Asegúrese de que prueba la campaña de inserción con un dispositivo conectado a través de WiFi y con una red de operador de telefonía para eliminar la conexión de red como posible fuente de problemas.
8. Asegúrese de que la fecha y hora del sistema en el dispositivo y el emulador es correcta porque cualquier dispositivo que no esté sincronizado también interferirá con la capacidad del Servicio de notificaciones de inserción para entregar notificaciones. 

A continuación se incluyen instrucciones adicionales de solución de problemas específicas de plataforma:

1. **iOS** 

	- Asegúrese de que los certificados son válidos y de que no han expirado para las notificaciones push de iOS. 
	- Asegúrese de que configura correctamente un certificado de *Producción* en la aplicación Mobile Engagement. 
	- Asegúrese de que se está probando en una *dispositivo físico real*. El simulador de iOS no puede procesar mensajes de inserción.
	- Asegúrese de que el identificador de paquete está configurado correctamente en la aplicación móvil. Vea las instrucciones [aquí](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6).
	- Al probar, use la distribución "Ad Hoc" en el perfil de aprovisionamiento móvil. No podrá recibir una notificación si la aplicación se compila con "Debug"

2. **Android**

	- Asegúrese de que ha especificado el número de proyecto correcto en el archivo AndroidManifest.xml de la aplicación móvil seguido del carácter \\n. 
	
	    	<meta-data android:name="engagement:gcm:sender" android:value="************\n" />
	    
	- Asegúrese de que no falta ningún permiso o de que no está configurado incorrectamente en el archivo de manifiesto de Android.
	- Asegúrese de que el número de proyecto que se va a agregar a la aplicación cliente procede de la misma cuenta en la que obtuvo la clave del servidor de GCM. Si los dos no coinciden, las inserciones no saldrán. 
	- Si recibe notificaciones del sistema, pero no de aplicación, revise la [sección Especificación de un icono para las notificaciones](mobile-engagement-android-get-started.md), ya que es probable que no esté especificando el icono correcto en el archivo de manifiesto de Android. 
	- Si envía una notificación de BigPicture y tiene servidores de imágenes externos, asegúrese de que son compatibles con "GET" y "HEAD" de HTTP.

3. **Windows**
	
	- Asegúrese de que asoció la aplicación con una aplicación válida de la Tienda Windows. En Visual Studio, tendrá que hacer clic con el botón derecho en el proyecto, seleccionar la opción "Asociar aplicación con la Tienda" y seleccionar la aplicación que creó en la Tienda Windows. Esta aplicación de la Tienda Windows debe ser la misma desde la que obtuvo las credenciales de inserción nativa para la configuración en el portal de Mobile Engagement.
	- Si recibe notificaciones push fuera de aplicación pero no de aplicación con integración `EngagementOverlay`, asegúrese de que la página incluye un elemento de cuadrícula raíz en la página. EngagementOverlay usa el primer elemento “Grid” que encuentra en el archivo xaml para agregar dos vistas web en la página. Si desea localizar dónde se establecerán las vistas web, puede definir una cuadrícula denominada "EngagementGrid" de este modo; sin embargo, deberá asegurarse de que hay suficiente alto y ancho para las dos vistas web posteriores que mostrarán la notificación y el anuncio siguiente como notificación de aplicación:
		
			<Grid x:Name="EngagementGrid"></Grid>

### Creé una notificación push/un anuncio/una campaña e incluso después de recibir la notificación sigue apareciendo como 'Activo'. ¿Qué significa? 
La **campaña** que creó en Mobile Engagement se denomina de este modo porque es una notificación push de larga ejecución; a medida que nuevos dispositivos se conectan a la plataforma de interacción móvil, se les envía automáticamente la notificación configurada aquí, siempre que cumplan el criterio establecido en la campaña. No se trata de una configuración de notificación de un solo uso. Debe hacer clic manualmente en el botón **Finalizar** para terminar la campaña y que no envíe más notificaciones.

### Creé una campaña de inserción y recibo notificaciones correctamente; sin embargo, cuando abro la aplicación, recibo la misma notificación a pesar de que ya la he respondido antes. ¿A qué se debe? 
Probablemente, esto sucede durante las pruebas y si usa emuladores o algún marco de prueba como TestFlight. Lo que sucede es que cada sesión de ejecución de la aplicación adquiere un nuevo DeviceID y lo envía a nuestro back-end, lo que provoca que la plataforma de Mobile Engagement lo considere como un nuevo dispositivo y envíe la notificación.

## Obtención de soporte técnico

Si no puede resolver el problema por sí mismo, puede realizar lo siguiente:

1. Buscar el problema en hilos existentes del foro de StackOverflow y el [foro de MSDN](https://social.msdn.microsoft.com/Forums/windows/es-ES/home?forum=azuremobileengagement); si no lo encuentra, puede plantear una pregunta en ellos. 
2. Si considera que falta una funcionalidad, agregue una solicitud o vote por ella en nuestro [foro de UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/).
3. Si dispone de Servicio de soporte técnico de Microsoft, abra un incidente de soporte técnico con los siguientes detalles: 
	- Identificador de suscripción de Azure
	- Plataforma (por ejemplo, iOS, Android, etc.)
	- Id. de aplicación
	- Identificador de campaña (para problemas con notificaciones push)
	- Id. de dispositivo
	- Versión de SDK de Mobile Engagement (por ejemplo, Android SDK v2.1.0)
	- Detalles del error con el mensaje de error exacto y el escenario

<!---HONumber=AcomDC_0302_2016-->