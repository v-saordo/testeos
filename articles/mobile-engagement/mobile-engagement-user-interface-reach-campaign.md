<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement - Campaña de cobertura" 
   description="Aprenda cómo crear y administrar campañas de notificaciones de inserción mediante Azure Mobile Engagement" 
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


# Cómo crear y administrar campañas de notificaciones de inserción
Puede utilizar la sección de cobertura de la interfaz de usuario para crear una nueva campaña de inserción con una fórmula compleja proporcionando toda la información que necesita para enviar una notificación de inserción. Las opciones de una campaña de inserción varían ligeramente en función de los cuatro tipos de campaña: anuncios, sondeos, inserciones de datos y mosaicos (solo en Windows Phone).

### La opción se aplica a:
- Idiomas: todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Campaña: todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Notificación: anuncios y sondeos
- Contenido: único para cada tipo de campaña
- Público: todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Período de tiempo: anuncios, sondeos, mosaicos
- Prueba: todo (anuncios, sondeos, inserciones de datos, mosaicos)
 
![Reach-Campaign1][20]

## Idiomas
Puede utilizar el menú desplegable de idiomas para enviar una versión diferente de la inserción a los dispositivos que están configurados para utilizar distintos idiomas. De forma predeterminada, todos los dispositivos recibirán la misma inserción independientemente del lenguaje que están configurados para utilizar. Los usuarios con su dispositivo configurado en un idioma diferente recibirán la versión del idioma predeterminado de la inserción. Muchas de las opciones de la campaña de inserción le permiten especificar contenido alternativo para cada uno de los idiomas adicionales que seleccione.
 
![Reach-Campaign2][21]

### Las diferencias de los idiomas se aplican a:
- Idiomas: se pueden seleccionar idiomas únicos además del idioma predeterminado
- Campaña: igual para todos los idiomas
- Notificación: único para cada idioma además del idioma predeterminado
- Contenido: único para cada idioma además del idioma predeterminado
- Audiencia: se puede filtrar por un criterio de idioma independiente
- Período de tiempo: igual para todos los idiomas
- Prueba: se puede enviar a cada idioma a la vez
 
### Idiomas admitidos
- Árabe (ar) 
- Búlgaro (bg) 
- Catalán (ca) 
- Chino (zh) 
- Croata (hr) 
- Checo (cs) 
- Danés (da) 
- Neerlandés (Países Bajos) 
- Español (es) 
- Finés (fi) 
- Francés (fr) 
- Alemán (de) 
- Griego (el) 
- Hebreo (he) 
- Hindi (hi) 
- Húngaro (hu) 
- Indonesio (id) 
- Italiano (it) 
- Japonés (ja) 
- Coreano (ko) 
- Letón (lv) 
- Lituano (lt) 
- Malayo (macroidioma) (ms) 
- Noruego Bokmål (nb) 
- Polaco (pl) 
- Portugués (pt) 
- Rumano (ro) 
- Ruso (ru) 
- Serbio (sr) 
- Eslovaco (sk) 
- Esloveno (sl) 
- Español (es) 
- Sueco (sv) 
- Tagalo (tl) 
- Tailandés (th) 
- Turco (tr) 
- Ucraniano (uk) 
- Vietnamita (vi) 
 
## Campaña
Puede utilizar la sección Campaña para establecer el nombre y la categoría de la campaña, así como si planea pasar por alto la sección de audiencia de una campaña de inserción y enviar esta campaña a través de la API de cobertura (y algunos elementos de la API de inserción de nivel bajo) en su lugar. Las categorías se pueden utilizar con una plantilla de notificación personalizada para controlar las notificaciones de aplicaciones basadas en configuraciones predefinidas. Puede obtener una lista de las "Categorías" existentes mediante la API de cobertura.

> Advertencia: si usa la opción "Omitir audiencia, la inserción se enviará a los usuarios a través de la API" de la sección "Campaña" de una campaña de cobertura, la campaña NO se enviará automáticamente, deberá enviarla de forma manual mediante la API de cobertura.
 
![Reach-Campaign3][22]
 
### La opción se aplica a:
- Nombre: todo
- Categoría: anuncios, sondeos
- Omitir la audiencia, la inserción se enviará a los usuarios a través de la API: todo
 
## Notificación
Puede utilizar la sección Notificación para establecer la configuración básica de la inserción, incluido: el título de la inserción, el mensaje, una imagen de la aplicación, o si se pueden pasar por alto. Muchas opciones de notificación son específicas de la plataforma del dispositivo. Puede seleccionar si desea enviar la inserción "en la aplicación", "fuera de la aplicación" o a ambas ubicaciones. (Recuerde que los usuarios pueden participar o no participar en las inserciones fuera de la aplicación a nivel del sistema operativo en sus dispositivos, y Azure Mobile Engagement no podrá invalidar esta configuración. Recuerde también que la API de cobertura maneja inserciones "dentro de la aplicación" y "fuera de la aplicación". La API de inserción puede utilizarse para controlar también inserciones "fuera de la aplicación"). Las inserciones pueden personalizarse con imágenes o contenido HTML, incluidos vínculos profundos para vincular fuera de la aplicación o en otra ubicación de la aplicación (se requieren categorías preventivas del SDK de Android 2.1.0 o posteriores). Puede cambiar el icono o el distintivo de iOS y enviar contenido de texto o web (una ventana emergente con contenido html, un vínculo de URL a otra ubicación dentro o fuera de la aplicación). También puede hacer que dispositivos Android suenen o vibren con la inserción. (Recuerde que necesitará los permisos correctos del SDK en su archivo de manifiesto de Android para hacer sonar o vibrar un dispositivo). Actualmente no hay ningún estándar del sector para tamaños de Android de "Imagen grande", puesto que los tamaños de pantalla son diferentes en cada dispositivo, pero las imágenes de 400 x 100 funcionan en casi cualquier tamaño de pantalla.

### Tipos de entrega:
-    Solo fuera de la aplicación: la notificación se entregará cuando el usuario no utilice la aplicación.
- La notificación solo fuera de la aplicación requiere un certificado de Apple o Google (certificado APN o GCM).
- Solo dentro de la aplicación: la notificación aparece solo cuando se ejecuta la aplicación.
- La notificación utiliza el sistema de entrega Capptain para ponerse en contacto con el usuario. Puede personalizar completamente el diseño y presentación visual de la inserción.
- En cualquier momento: esta opción garantiza enviar una notificación tanto si la aplicación se está ejecutando como si no.

 
![Reach-Campaign4][23]

### La opción se aplica a:
- Notificación: anuncios y sondeos
 
## Contenido
Puede utilizar la sección de contenido para modificar el contenido de los anuncios, sondeos, inserciones de datos y mosaicos (solo en Windows Phone). La configuración del contenido de las campañas de inserción es específica del tipo de campaña.

### Consulte también
- [Documentación de la interfaz de usuario - Cobertura - Insertar contenido][Link 29]
 
![Reach-Campaign5][24]

## Público
Puede utilizar la sección de audiencia para definir una lista estándar de elementos para limitar su campaña o limitar la campaña en función de criterios personalizados. El conjunto estándar de opciones para limitar la audiencia le permite insertar a los usuarios nuevos o antiguos o solo a los usuarios de inserción nativa. También puede establecer una cuota para limitar el número de usuarios que reciben la inserción. Puede editar manualmente la expresión sobre cómo se filtra su campaña para incluir uno o más criterios para los usuarios de destino. Manualmente puede escribir una expresión de audiencia. Este tipo de expresión debe definir explícitamente la relación entre los criterios. Un criterio se describe mediante un identificador que debe comenzar con una letra mayúscula y no puede contener espacios. La relación entre los criterios se puede describir mediante los operadores 'and', 'or', 'not', así como '(', ')'. Ejemplo: "Criterio1 o (Criterio1 y no Criterio2)".

> Nota: con una amplia audiencia incluida en las campañas, el análisis de orientación del servidor puede ralentizarse, especialmente si intenta iniciar varias campañas al mismo tiempo.

- Si es posible, inicie una sola campaña de cada vez.
- Como máximo, inicie solamente cuatro campañas de cada vez.
- Realice la inserción únicamente a sus usuarios activos (casilla de verificación "Atraer solo a los usuarios con los que se pueda contactar mediante inserción nativa" y "Atraer solo a usuarios activos") para que solo se tengan que analizar los usuarios que aún tienen la aplicación instalada y la utilicen. Una vez definida la audiencia, puede utilizar el botón de simulación para averiguar cuántos usuarios recibirán esta inserción. Esto calculará el número de usuarios conocidos a los que se dirige potencialmente este público (esta es una estimación basada en una muestra aleatoria de usuarios). Tenga en cuenta que los usuarios que han desinstalado la aplicación también forman parte de esta audiencia, pero no se puede contactar con ellos.

### Consulte también
- [Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción][Link 28]

![Reach-Campaign6][25]

### Editar expresión
![Reach-Campaign7][26]
 
### Limitar la opción de audiencia se aplica a:
- Haga participar a tan solo un subconjunto de usuarios: todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Haga participar solo a los usuarios antiguos: todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Haga participar solo a los usuarios nuevos: todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Haga participar solo a los usuarios inactivos: anuncios, sondeos, mosaicos
- Haga participar solo a los usuarios activos: todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Haga participar solo a los usuarios con los que se pueda contactar mediante la inserción nativa: anuncios, sondeos
 
## Período de tiempo
Puede utilizar la sección de intervalo de tiempo para establecer cuándo se enviará la inserción o puede dejar el intervalo de tiempo en blanco para iniciar la campaña inmediatamente. Recuerde que utilizando la zona horaria de los usuarios finales puede comenzar la campaña un día antes de lo esperado para los usuarios finales de Asia y enviar lotes pequeños de inserciones a la vez hasta que todas las zonas horarias del mundo coincidan con el intervalo de tiempo establecido para la campaña. Utilizar la zona horaria de los usuarios finales también puede provocar retrasos en las campañas porque tiene que solicitar el tiempo desde el teléfono antes de iniciar la inserción.

> Nota: sin una fecha de finalización puede almacenar en caché inserciones localmente y todavía mostrarlas después de completar manualmente las campañas. Para evitar este comportamiento, especifique una hora de finalización para campañas.

### Consulte también
- [Cobertura - Guía práctica - Programación][Link 3] 
 
![Reach-Campaign8][27]

### La configuración se aplica a:
- Período de tiempo: anuncios, sondeos, mosaicos
 
## Prueba
Puede utilizar la sección de prueba para enviar esta inserción a su propio dispositivo de prueba antes de guardar la campaña. Si ha configurado algún idioma personalizado para esta campaña, puede probar la inserción en cada idioma. Puede configurar un dispositivo de prueba desde "Mi cuenta".
> Nota: no se registra ningún dato del servidor cuando utiliza el botón para "probar" inserciones, los datos solo se registran para campañas de inserción real.

### Consulte también
- [Documentación de la interfaz de usuario - Mi cuenta][Link 14]
 
![Reach-Campaign9][28]

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