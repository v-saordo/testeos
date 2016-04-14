<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: cobertura" 
   description="Obtenga información acerca de cómo llegar a los usuarios de su aplicación mediante notificaciones de inserción usando Azure Mobile Engagement" 
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
   ms.date="11/29/2015"
   ms.author="piyushjo"/>


# Cómo llegar a los usuarios de su aplicación mediante notificaciones de inserción

Este artículo describe la pestaña **ALCANCE** del portal **Mobile Engagement**. Utilice el portal **Mobile Engagement** para supervisar y administrar sus aplicaciones móviles. Tenga en cuenta que, para comenzar a usar el portal, debe crear en primer lugar una cuenta de **Azure Mobile Engagement**. Para obtener más información, consulte [Crear una cuenta de Azure Mobile Engagement](mobile-engagement-create-account.md).

La sección de cobertura de la interfaz de usuario es la herramienta de administración de campaña de inserción en la que puede crear/editar/activar/finalizar/supervisar y obtener estadísticas de las campañas de notificaciones de inserción y funciones a las que también se puede acceder a través de la API de cobertura (y algunos elementos de la API de inserción de bajo nivel). Recuerde que tanto si está utilizando las API o la interfaz de usuario, deberá integrar Azure Mobile Engagement y la cobertura en la aplicación para cada plataforma con el SDK para poder utilizar campañas de cobertura.

>[AZURE.NOTE] Muchas de las secciones de la interfaz de usuario del portal **Mobile Engagement** contienen el botón **MOSTRAR AYUDA**. Pulse este botón para obtener más información contextual sobre una sección.

## Cuatro tipos de notificaciones de inserción
1.    Anuncios: le permiten enviar mensajes de publicidad a los usuarios que los redireccionan a otra ubicación dentro de la aplicación o enviarlos a una página web o tienda fuera de la aplicación. 
2.    Sondeos: le permiten reunir información de los usuarios finales formulándoles preguntas.
3.    Inserciones de datos: le permiten enviar un archivo de datos binario o base64. La información contenida en una inserción de datos se envía a la aplicación para modificar la actual experiencia del usuario en la aplicación. La aplicación debe ser capaz de procesar los datos de una inserción de datos.

## Detalles de la campaña

Puede editar, clonar, eliminar o activar las campañas que no se han activado todavía pasando el mouse sobre sus nombres o puede hacer clic para abrirlas. Se pueden clonar las campañas que ya se han activado pasando el mouse sobre sus nombres o puede hacer clic para abrirlas. Sin embargo, no puede cambiar una campaña una vez activada.
 
![Reach1][18]

## Comentarios sobre la cobertura

Haga clic en **Estadísticas** para ver los detalles de una campaña de cobertura. La vista **sencilla** proporciona una representación visual en forma de un gráfico de barras de columnas de lo que ha ocurrido después de que se activara una campaña. La vista **avanzada** proporciona detalles más específicos sorbe la campaña de inserción. Estos detalles no estarán disponibles si va a enviar una campaña de prueba, es decir, se envía una inserción a un dispositivo de prueba. A continuación le mostramos cómo interpretar estos detalles:

1. **Insertados**: especifica el número de mensajes insertados en los dispositivos. Este número dependerá de la audiencia de destino que especificara al crear la campaña de inserción. Si no especifica ninguna audiencia de destino, se enviará esta inserción a todos los dispositivos registrados. Al igual que sucede con todos los demás servicios de inserción, no inserte las notificaciones directamente en el dispositivo, insértelas en los servicios de notificaciones de inserción específicos de la plataforma respectiva (PNS: APNs/GCM/WNS) para que puedan entregar las notificaciones a los dispositivos. 

2.	**Entregados**: especifica el número de mensajes que el PNS entrega correctamente al dispositivo y que el SDK de Mobile Engagement confirma como recibidos.
		
	*Razones de que el número de entregados sea inferior al número de insertados:*
	
	1. Si el usuario ha desinstalado la aplicación del dispositivo pero el PNS no lo sabe cuando le enviamos la inserción, el mensaje se eliminará.
	2. Si el dispositivo tiene la aplicación, pero ha estado desconectado durante largos períodos de tiempo, el PNS no podrá entregar el mensaje al dispositivo. 
	3. Si el mensaje se entrega al dispositivo, pero el SDK de Mobile Engagement en la aplicación no reconoce su contenido, entonces se elimina ese mensaje. Esto podría ocurrir si la personalización de la notificación en la aplicación genera una excepción que capturamos en el SDK y eliminamos el mensaje. Esto también puede ocurrir si la aplicación en el dispositivo utiliza una versión del SDK de Mobile Engagement que no es capaz de entender la versión más reciente del mensaje de inserción enviado desde la plataforma. Pero esto solo sucede, si la aplicación se ha actualizado después de que la notificación se distribuyera desde la plataforma de servicio. La pestaña **Avanzadas** indicará cuántos mensajes se han eliminado. 
	4. En dispositivos iOS, los mensajes a veces no se entregan si el dispositivo tiene poca batería o si la aplicación consume demasiada energía al procesar las notificaciones remotas. Se trata de una limitación de los dispositivos iOS.   

3.	**Mostrados**: especifica el número de mensajes que se muestran correctamente al usuario de la aplicación en el dispositivo en forma de una notificación push del sistema o de fuera de aplicación en el centro de notificaciones, o en forma de una notificación en aplicación dentro de la aplicación móvil. La pestaña **Avanzadas** le indicará cuántas eran notificaciones del sistema y cuántas notificaciones en aplicación.

4.	**Interacciones de usuario**: especifica el número de mensajes con los que ha interactuado el usuario de la aplicación e incluye los mensajes que están accionados o cerrados.

	- *El usuario de la aplicación puede accionar una notificación de las siguientes formas:*
			
		1. Si la notificación es una notificación del sistema o de fuera de aplicación o una notificación en aplicación enviada solo como notificación, el usuario de la aplicación hace clic en la notificación.
		2. Si la notificación es una notificación en aplicación con una vista de texto o web o sondeos, el usuario de la aplicación hace clic entonces en el botón de acción en la notificación.
		3. Si la notificación es una notificación en aplicación con una vista web, el usuario de la aplicación hace clic en una dirección URL en la vista web [solo para Android]
	
	- *El usuario de la aplicación puede cerrar una notificación de las siguientes formas:*
	
		1. Haciendo clic directamente en el botón Cerrar en la notificación. 
		2. Con el gesto de deslizarla hacia afuera o eliminándola. 
		3. Las notificaciones en aplicación con contenido de texto o web y sondeos normalmente se muestran al usuario de la aplicación en un proceso de dos pasos. Primero ven una notificación y, cuando hacen clic en ella, ven el contenido de texto, web o sondeo subsiguiente. El usuario de la aplicación puede cerrar una notificación en cualquiera de estos pasos y los detalles de la vista avanzada capturan esta acción. 

5.	**Accionados**: especifica el número de mensajes que el usuario de la aplicación accionó explícitamente. Es el número más interesante ya que le indica cuántos usuarios de la aplicación estuvieron interesados en el mensaje que se expulsó en la notificación.
 
> [AZURE.NOTE] En las plataformas iOS y Windows, si el usuario tiene abierta la aplicación y la campaña era de tipo "Cualquier hora", es posible que tanto las notificaciones en aplicación como fuera de aplicación se muestren al mismo tiempo. Como consecuencia, el número de mensajes mostrados podría ser superior al de los entregados. Si el usuario interactúa con la notificación o la acciona, incluso el número de interacciones de usuario o de mensajes accionados podría ser superior al de los entregados.


![Reach2][19]

## Consulte también

- [Conceptos][Link 6]
- [Guía de solución de problemas de servicios][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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
 

<!---HONumber=AcomDC_0204_2016-->