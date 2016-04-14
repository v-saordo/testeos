<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: criterio de cobertura" 
   description="Aprenda a usar los criterios de orientación para enviar campañas de inserción a un subconjunto seleccionado de usuarios mediante Azure Mobile Engagement" 
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


# Cómo usar los criterios de orientación para enviar campañas de inserción a un subconjunto seleccionado de usuarios

Orientar su audiencia según criterios específicos mediante el botón "Nuevos criterios" es uno de los conceptos más eficaces de Azure Mobile Engagement, que le ayuda a enviar notificaciones de inserción relevantes a las que responderán los clientes en lugar de enviar spam a todo el mundo. Puede limitar la audiencia basándose en criterios estándar y simular inserciones para determinar cuántas personas recibirán la notificación.

**Consulte también:**

- [Documentación de la interfaz de usuario - Alcance - Nueva campaña de inserción][Link 27]

## Dentro de los criterios de audiencia se pueden incluir:
- ****Aspectos técnicos:** puede orientar en función de la misma información técnica que puede ver en las secciones de análisis y supervisión. **Consulte también**: [Documentación de la interfaz de usuario - Análisis][Link 15], [Documentación de la interfaz de usuario - Supervisión][Link 16]
- **Ubicación:** las aplicaciones que usan "informes de ubicación en tiempo real" con Geofencing pueden usar la geolocalización como criterio para orientarse a una audiencia desde la ubicación de GPS. La llamada "Informes de ubicación de área diferida" también se puede usar para orientarse a una audiencia desde la ubicación del teléfono móvil ("Informes de ubicación en tiempo real" e " Informes de ubicación de área diferida" deben activarse desde el SDK) **Consulte también:** [Documentación del SDK - iOS -][Link 5], [Documentación del SDK - Android - Integración][Link 5]
- **Comentarios sobre la cobertura:** puede tener como destino su audiencia según sus comentarios de notificaciones de cobertura anteriores a través de comentarios sobre la cobertura de Anuncios, Sondeos e Inserciones de datos. Esto le permite orientarse mejor a la audiencia después de dos o tres campañas de cobertura que la primera vez. También puede usarse para filtrar los usuarios que ya han recibido una notificación con contenido similar, al establecer una campaña para NO enviarse a los usuarios que ya han recibido una determinada campaña anterior. Incluso puede excluir a los usuarios incluidos en una campaña específica que está todavía activa para recibir inserciones nuevas. **Vea también:** [Documentación de la interfaz de usuario - Cobertura - Insertar contenido][Link 29]
- **Seguimiento de la instalación:** puede realizar un seguimiento de información basada en dónde instalaron los usuarios su aplicación. **Consulte también:** [Documentación de interfaz de usuario - Configuración][Link 20]
- **Perfil de usuario:** puede orientarse según la información de usuario estándar y basándose en la información de la aplicación personalizada que ha creado. Aquí se incluye a los usuarios que han iniciado sesión y a los que han respondido preguntas específicas que les ha solicitado definir en la propia aplicación en lugar de tan solo cómo han respondido a campañas anteriores. Toda la información de la aplicación definida para la aplicación aparece en esta lista.
- Segmentos: también puede orientarse en función de segmentos que ha creado según el comportamiento específico del usuario que contiene varios criterios. Todos los segmentos definidos para la aplicación aparecen en esta lista. **Consulte también:** [Documentación de interfaz de usuario - Segmentos][Link 18]
- **Información de la aplicación:** es posible crear etiquetas de información de aplicación personalizadas desde "Configuración" para realizar un seguimiento del comportamiento de los usuarios. **Consulte también:** [Documentación de interfaz de usuario - Configuración][Link 20]

## Ejemplo: 
Si desea insertar un anuncio solo en el subconjunto de los usuarios que han realizado una acción de compra en la aplicación.

1. Vaya a la página de configuración de la aplicación, seleccione el menú "Información de la aplicación" y seleccione "Nueva información de la aplicación"
2. Registre una nueva información de aplicación de booleano denominada "inAppPurchase"
3. Haga que la aplicación establezca esta información de aplicación en "true" cuando el usuario realice correctamente una compra en la aplicación (mediante el uso de la función sendAppInfo ("inAppPurchase",...))
4. Si no desea hacerlo desde la aplicación, puede hacerlo desde el backend mediante el uso de la API del dispositivo)
5. A continuación, solo necesita crear el anuncio con un criterio que limite la audiencia a los usuarios que tienen "inAppPurchase" establecido en "true")
 
> Nota: la orientación basada en criterios que no sean etiquetas de información de aplicación requiere que Azure Mobile Engagement recopile información de los dispositivos de los usuarios antes de enviar la inserción para poder provocar un retraso. Las opciones de configuración de inserción complejas (como la actualización de insignias) también pueden retrasar las inserciones. El uso de una campaña de "Monoestable" de la API de inserción es el método de inserción de más rápido absoluto en Azure Mobile Engagement. Utilizar solo etiquetas de información de aplicación como criterios de inserción para una campaña de cobertura (ya sea desde la API de cobertura o la interfaz de usuario) es el siguiente método más rápido, dado que las etiquetas de información de la aplicación se almacenan en el servidor. Usar otros criterios de orientación para una campaña de inserción es el método de inserción más flexible pero más lento, ya que Azure Mobile Engagement tiene que consultar los dispositivos para enviar la campaña.
 
![Reach-Criterion1][29]

## Las opciones de criterios se aplican a:
- **Notas técnicas**     
- Nombre del firmware: nombre del firmware
- Versión del firmware: versión del firmware
- Modelo del dispositivo: modelo del dispositivo
- Fabricante del dispositivo: fabricante del dispositivo
- Versión de la aplicación: versión de la aplicación
- Nombre del operador: nombre del operador
- País del operador: país del operador
- Tipo de red: tipo de red
- Configuración regional: configuración regional
- Tamaño de pantalla: tamaño de pantalla
- **Ubicación**      
- Última área conocida: país, región, localidad
- Geofencing en tiempo real: lista de puntos de interés (nombre, acciones), punto de interés circular (nombre, latitud, longitud, radio en metros)
- **Comentarios sobre la cobertura**     
- Comentarios de anuncio: anuncio, comentarios
- Comentarios de sondeo: sondeo, comentarios
- Comentarios de respuesta de sondeo: comentarios de respuesta de sondeo, pregunta, opción
- Comentarios de inserción de datos: inserción de datos, comentarios
- **Seguimiento de la instalación**     
- Tienda: tienda, sin definir
- Origen: origen, sin definir
- **Perfil de usuario**     
- Género: hombre o mujer, sin definir
- Fecha de nacimiento: operador, fecha, sin definir
- Participar: verdadero o falso, sin definir
- **Información de la aplicación**      
- Cadena: cadena, sin definir
- Fecha: operador, fecha, sin definir
- Entero: operador, número, sin definir
- Booleano: verdadero o falso, sin definir
- **Segmento**    
- Nombre de segmentos (de la lista desplegable), exclusión (usuarios de destino que no forman parte de este segmento).

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
 

<!---HONumber=AcomDC_0302_2016-->