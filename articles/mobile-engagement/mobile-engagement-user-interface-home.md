<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: inicio" 
   description="Obtenga información acerca de cómo administrar sus aplicaciones y proyectos existentes mediante Azure Mobile Engagement" 
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

# Cómo administrar su aplicación y proyectos existentes


Este artículo describe la página **Inicio** del portal **Mobile Engagement**. Use el portal **Mobile Engagement** para supervisar y administrar las aplicaciones móviles. Tenga en cuenta que para comenzar a usar el portal en primer lugar debe crear una cuenta **Azure Mobile Engagement**. Para obtener más información, consulte [Crear una cuenta de Azure Mobile Engagement](mobile-engagement-create-account.md).
 
Para ir a la página principal, haga clic en **Inicio** en la parte superior izquierda de la página. Contiene la lista de todas las aplicaciones que forman parte de la colección seleccionada. En esta página solo verá una descripción general rápida de las aplicaciones.
   
La página principal también contiene todos los proyectos que pueden incluir cualquier aplicación que se encuentran en su cuenta. Tenga en cuenta que cualquier persona puede tener acceso a la página principal de la interfaz de usuario mediante la creación de una cuenta, pero deberá conceder a otros usuarios permisos para que tengan acceso a las aplicaciones personalizadas en **Mis proyectos**.

También puede ver el gráfico de comparación de las aplicaciones seleccionadas. O bien, puede optar por ver el gráfico de comparación de las aplicaciones seleccionadas en un proyecto.

![Home1][0]


## Mis aplicaciones

La información general rápida de las aplicaciones le permite seleccionar qué aplicación desea abrir para ver las opciones detalladas de la cinta de opciones. Puede hacer clic en el nombre de la aplicación para enviar la ubicación de la cinta de opciones visitada más recientemente en la aplicación, o hacer clic en el icono de engranaje para ir directamente a la página "Configuración" de la aplicación. Puede buscar, filtrar u ordenar la información mostrada en las tablas de las aplicaciones. También puede arrastrar y colocar los encabezados de columna para cambiar el orden.
 
Entre otras cosas, la información general de las aplicaciones incluye:

- **Nueva tendencia de usuarios**: (evolución de nuevos usuarios durante las últimas dos semanas).
- **Usuarios activos**: número de usuarios activos durante los últimos 30 días.
- **Tendencia de los usuarios activos**: evolución de usuarios activos en las últimas dos semanas.
- **Sesiones**: una sesión consiste en el uso individual de la aplicación por parte de un usuario, desde el momento en que comienza a usarla hasta que se detiene. 
- **Tendencias de sesiones**: evolución de las sesiones en las últimas dos semanas.

Una vez que se hace clic en una aplicación, puede comenzar a supervisar y administrar las aplicaciones a través de la interfaz de usuario. Por ejemplo:

- [Supervisión de datos en tiempo real sobre la aplicación](mobile-engagement-user-interface-monitor.md)
- [Análisis de datos históricos sobre la aplicación](mobile-engagement-user-interface-analytics.md)
- [Creación y administración de segmentos de usuarios para identificar patrones de uso](mobile-engagement-user-interface-segments.md)
- [Establecimiento de contacto con los usuarios de la aplicación mediante notificaciones push](mobile-engagement-user-interface-reach.md)
 
## Mis proyectos

Puede usar proyectos para agrupar sus aplicaciones y conceder permisos a otros usuarios para acceder a sus aplicaciones. Se conceden permisos a otros usuarios proporcionando la dirección de correo electrónico. El botón **Proyecto nuevo** permite crear un nuevo proyecto especificando solo un "nombre" y "descripción" del nuevo proyecto. Una vez creado un proyecto, puede hacer clic en el nombre del proyecto para editar el nombre y la descripción del producto y seleccionar todas las aplicaciones que desea ver en este proyecto.


![Home6][60]

Entre los roles se incluyen:

- **Visor**: un visor es un usuario que solo puede ver las aplicaciones asociadas a un proyecto. Un visor puede tener acceso a análisis, supervisar datos y examinar los resultados de cobertura. Un visor no puede cambiar la información ni administrar aplicaciones o usuarios. Un visor no puede crear o cambiar el estado de la campaña de cobertura.
- **Desarrollador**: un desarrollador es un usuario que puede hacer todo lo que un visor, así como administrar las aplicaciones. Un desarrollador puede habilitar y deshabilitar aplicaciones, cambiar la información de las aplicaciones (como el paquete y la firma) y crear campañas de cobertura. Un desarrollador no puede administrar los usuarios. 
- **Administrador**: un administrador es un usuario que puede hacer todo lo que un programador, así como administrar usuarios. Un administrador puede invitar a los usuarios a participar en un proyecto, puede cambiar los roles de usuario y puede cambiar la información del proyecto. Los permisos de nivel de aplicación también pueden establecerse en "configuración".


Haga clic en un proyecto para ver todas las aplicaciones que forman parte de este proyecto. En la imagen siguiente se muestra el gráfico de comparación de las aplicaciones seleccionadas.

![Home2][3]


## Consulte también

- [Conceptos][Link 6]
- [Guía de solución de problemas - Servicio][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md


 
 

<!---HONumber=AcomDC_1203_2015-->