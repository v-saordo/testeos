<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement - Guía práctica de Cobertura"
   description="Introducción de la interfaz de usuario de Azure Mobile Engagement" 
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

# Cómo empezar a usar y administrar inserciones para llegar a los usuarios finales

Una vez que el SDK está totalmente integrado en la aplicación, puede empezar a usar la sección Cobertura de la interfaz de usuario para enviar notificaciones a los usuarios de la aplicación.

## Creación de la primera campaña de notificación de inserción
-    Confirme que tiene integrado Reach en la aplicación con el SDK. 
-    Seleccione la aplicación.
 
![First1][1]

-    Vaya a la sección "Reach" y haga clic en "Nuevo anuncio".
 
![First2][2]

-    Cree una campaña nueva y asígnele un nombre.
 
 ![First3][3]

-    Seleccione cómo se debe entregar la notificación, por ejemplo, si es solo en la aplicación.
 
![First4][4]

-    Cree el mensaje que desea insertar.
 
![First5][5]

-    Puede escribir un título en la notificación (opcional).
-    Escriba el contenido del mensaje de inserción.
-    Puede cargar una imagen. Tenga en cuenta que el tamaño del archivo no puede superar 32.768 bytes.
-    También tiene la posibilidad de seleccionar más opciones, pero para el objetivo de este tutorial, las veremos más adelante.

-    Seleccionar tipo de contenido como Solo notificación
 
![First6][6]

-    Cree la campaña de inserción, que aparecerá en la lista de campañas.
 
![First7][7]

## Prueba de la campaña de notificación de inserción
![Test1][8]

-    Registre el dispositivo.
-    Haga clic en la casilla del dispositivo donde desea realizar la inserción.
-    Haga clic en el botón "Probar" para enviar la inserción al dispositivo.
 
![Test2][9]

-    Activar la campaña
 
![Test3][10]

-    Ahora que ha creado la campaña, basta con activarla para insertar la notificación para los usuarios.
 
## Envío de inserciones personalizadas
-    En este ejemplo se crea una inserción donde se introduce un código de descuento personalizado en la notificación de inserción.
 
![Personalize1][11]

La personalización funciona mediante la sustitución del marcador de una etiqueta de información de la aplicación, por lo que antes tendrá que asegúrese de que el usuario tiene definida la información de la aplicación adecuada. En este ejemplo, los usuarios de destino tienen una etiqueta definida de información de la aplicación que se denomina rebate\_code. Como puede ver, el contenido de la notificación de inserción incluye el marcador ${rebate\_code}, que indica que se tiene que reemplazar por el contenido real de la etiqueta de información de la aplicación.

> Advertencia: si el usuario no define la etiqueta de información de la aplicación, no recibirá la inserción.

-    Resultado
 
![Personalize2][12]

### Puede personalizar aún más el texto de la notificación,
![Personalize3][13]

-    incluido el título dela notificación
-    y el contenido del mensaje.
-    Elija el tipo de anuncio (vista de texto o vista web).
 
![Personalize4][14]

### También se puede personalizar el cuerpo de un anuncio con:
-    la URL de acción, en caso de que quiera personalizar la página de inicio;
-    el título y
-    el cuerpo del mensaje.
 
 
## Diferenciación de la notificación de inserción (en la aplicación o fuera de ella)
-    Elija el tipo de notificación que insertará, seleccione la aplicación, vaya a la sección "Reach", seleccione o cree una campaña de inserción y vaya a la sección "Notificación".
 
-    Haga clic en el "modo de entrega" que desee.
-    Haga clic en la casilla "Restringir actividades" cuando quiera que la notificación se produzca en actividades concretas (pantallas).

![Differentiate1][15]

### Modo de entrega "Solo fuera de la aplicación"
![Differentiate2][16]

El modo de entrega "Solo fuera de la aplicación sólo" proporciona una notificación de inserción cuando se cierra la aplicación. Se trata de la notificación de inserción estándar. Al seleccionar "Solo fuera de la aplicación", debe haber proporcionado antes los certificados de la plataforma en la que se basa la aplicación (APN o GCM).

### Consulte también
-  [Servicio de notificaciones de inserción de Apple: certificados](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Servicio de mensajería en la nube de Google: certificado](http://developer.android.com/google/gcm/index.html) 

### Modo de entrega "Solo en la aplicación"
![Differentiate3][17]

El modo de entrega "Solo en la aplicación" proporciona notificaciones de inserción cuando la aplicación se está ejecutando. En el caso de esta notificación, es necesario pasar por el sistema de APNS y GCM. Puede usar el sistema de entrega en la aplicación para llegar a los usuarios finales. Puede personalizar totalmente la notificación y decidir en que actividad (pantalla) aparecerá la notificación.

### Modo de entrega "En cualquier momento"
Si elige el modo de entrega "En cualquier momento", se asegurará de llegar a los usuarios finales tanto si la aplicación se está ejecutando como si no. Al seleccionar "En cualquier momento", deben haber proporcionado antes los certificados de la plataforma en la que se basa la aplicación (APN o GCM).
 
## Programación una campaña de inserción
### Planeación del inicio de una campaña
![Shedule1][18]

Es 21 de marzo y tiene un anuncio que hacer, planeado para el 22 de marzo a medianoche. No es necesario que esté delante de la interfaz para realizar una inserción. Puede planear de antemano el minuto exacto en que se enviarán las notificaciones.
-    Desactive la casilla "Ninguna" y seleccione una hora de inicio. 
-    Elija la fecha y la hora en que desea iniciar la campaña de inserción.

### Planeación del fin de una campaña
![Shedule2][19]

Desea que la campaña finalice el 25 de marzo a las 15:00 h pero sabe que no estará presente para hacerlo. No es necesario que esté delante de la interfaz para realizar la inserción. Puede planear de antemano el minuto exacto en que se detendrá la campaña.
-    Haga clic en la casilla "Ninguna" o seleccione una hora de finalización.
-    Elija la fecha y la hora en que desea finalizar la campaña de inserción.

### Finalización de una campaña manualmente
![Shedule3][20]

De forma predeterminada, las casillas "Ninguna" están seleccionadas. Esto significa que la campaña comenzará en cuanto la active en la sección "Reach" y finalizará cuando la detenga en dicha sección.
 
> Nota: Las campañas creadas sin una fecha de finalización almacenan la inserción de forma local en el dispositivo y la muestran la próxima vez que se abre la aplicación incluso si la campaña ha finalizado manualmente.

## Mejora de una notificación de inserción con una vista de texto
### ¿Qué es una vista de texto?
![TextView1][21]

Una vista de texto es un elemento emergente con contenido de texto. Este elemento emergente aparece cuando el usuario final hace clic en la notificación de inserción. Una vista de texto permite presentar más contenido al usuario final. Esto también es una oportunidad de presentar una llamada a la acción, como saltar a una página de la aplicación, redirigir a una Tienda, abrir una página web, enviar un correo electrónico, iniciar una búsqueda localizada geográficamente, etc.

### Ejemplo: vista de texto
-    Cree la campaña de notificación de inserción en la sección "Reach" y asígnele un nombre.
 
![TextView2][22]

-    Escriba el mensaje que aparecerá en la notificación.
-    Seleccione el tipo de contenido del anuncio de "texto".
 
![TextView3][23]

> Nota: cuando inserta una vista de texto, siempre va acompañada de una notificación en primer lugar.

- Defina el texto (después de seleccionar el contenido del anuncio de texto, aparecerá la subsección, que le permite definir el texto que desea que se muestre).
 
![TextView4][24]

-    Escriba el título que aparecerá en la parte superior del mensaje.
-    Escriba el contenido principal de la vista de texto.
-    Escriba el contenido que aparecerá en el botón de acción (un botón de acción permite a la aplicación realizar una acción concreta, como abrir una página de la aplicación, redirigir a una tienda de aplicaciones o cualquier tipo de orígenes que proporcione).
-    Escriba el contenido que aparecerá en el botón Salir (haciendo clic en el botón Salir, la vista de texto desaparecerá).
 
-    Cree la campaña de notificación de inserción, que aparecerá en la lista de campañas.
 
![TextView5][25]

-    Active la campaña de notificación de inserción para enviar la vista de texto a los usuarios.
 
![TextView6][26]

-    Resultado
 
![TextView7][27]

-    El usuario recibe la notificación y hace clic en ella.
-    La vista de texto aparece como un elemento emergente que permite al usuario interactuar con él.

## Mejora de una notificación de inserción con una vista web
### ¿Qué es una vista web?
![WebView1][28]

Una vista web es un elemento emergente con contenido web. Este elemento emergente aparece cuando el usuario final hace clic en la notificación de inserción. Una vista web le permite interactuar más con el usuario final. Esto también es una oportunidad de presentar una llamada a la acción, como redirigir a una tienda de aplicaciones, abrir una página web, enviar un correo electrónico, iniciar una búsqueda localizada geográficamente, etc.

### Ejemplo: vista web
-    Cree la campaña de inserción en la sección "Reach" y asígnele un nombre.
 
![WebView2][29]

-    Escriba el mensaje que aparecerá en la notificación.
-    Seleccione el tipo de contenido del anuncio como "web".
 
![WebView3][30]

### Acerca de los tipos de anuncio:
- Solo notificación: es una notificación estándar simple. Lo que significa que si un usuario hace clic en él, no aparecerá ninguna vista adicional, pero se producirá solo la acción asociada a él.
- Anuncio de texto: es una notificación que compromete al usuario a echar un vistazo a una vista de texto.
- Anuncio web: es una notificación que compromete al usuario a echar un vistazo a una vista de texto. Seleccione el contenido de "Anuncio web".

> Nota: Cuando inserta una vista de web, siempre va acompañada de una notificación en primer lugar.

- Defina el contenido web (después de seleccionar el contenido del anuncio de web, aparecerá la subsección, que le permite definir el contenido de la vista web que desea que se muestre).

 
![WebView4][31]

-    Escribir el título que aparecerá en la parte superior del mensaje (opcional).
-    Escriba el código HTML aquí.
-    Haga clic en el botón de modo de edición de origen para cambiar la edición y ver su aspecto.
-    Escriba el contenido que aparecerá en el botón de acción (un botón de acción permite a la aplicación realizar una acción concreta, como abrir una página de la aplicación, redirigir a una tienda o cualquier tipo de orígenes que proporcione).
-    Escriba el contenido que aparecerá en el botón Salir (haciendo clic en el botón Salir, la vista de web desaparecerá).
 
-    Resultado
 
![WebView5][32]

-    El usuario recibe la notificación y hace clic en ella.
-    La vista de texto aparece como un elemento emergente que permite al usuario interactuar con él.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 

<!---HONumber=AcomDC_0302_2016-->