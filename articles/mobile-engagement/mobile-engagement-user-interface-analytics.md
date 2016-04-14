<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: análisis" 
   description="Obtenga información acerca de cómo analizar datos históricos acerca de la aplicación mediante Azure Mobile Engagement" 
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

# Cómo analizar datos históricos acerca de la aplicación

Este artículo describe la pestaña **ANÁLISIS** del portal **Mobile Engagement**. Utilice el portal **Mobile Engagement** para supervisar y administrar sus aplicaciones móviles. Tenga en cuenta que, para comenzar a usar el portal, debe crear en primer lugar una cuenta de **Azure Mobile Engagement**. Para obtener más información, consulte [Crear una cuenta de Azure Mobile Engagement](mobile-engagement-create-account.md).


La sección de análisis de la interfaz de usuario proporciona información total sobre la aplicación basada en datos históricos que se actualizan cada 24 horas. La información se muestra en diferentes paneles compuestos por gráficos de líneas/barras/circulares, cuadrículas y mapas. También se pueden descargar los datos como archivos .csv. La mayor parte de esta misma información está disponible en tiempo real en la sección de Supervisión de la interfaz de usuario, y también puede obtenerse a través de la API de análisis.

>[AZURE.NOTE]Muchas de las secciones de la interfaz de usuario del portal **Mobile Engagement** contienen el botón **MOSTRAR AYUDA**. Pulse este botón para obtener más información contextual sobre una sección.

## Análisis estándar y personalizado

Azure Mobile Engagement proporciona un conjunto de información analítica básico y estándar acerca de las aplicaciones que se puede representar gráficamente al integrar su aplicación con el SDK. Azure Mobile Engagement también proporciona la capacidad de recopilar la información de análisis personalizada adicional que desea acerca del comportamiento de sus usuarios finales. Puede hacerlo creando un plan de "etiquetas de información de aplicación" personalizado, creado desde **Configuración**, para que Azure Mobile Engagement pueda recopilar estos datos adicionales por usted.

 
 
## Análisis
- Panel: muestra información general acerca de los usuarios nuevos y activos y de sus tendencias.
- Usuarios: los usuarios se identifican mediante su identificador de dispositivo: este identificador es único para cada dispositivo (un nuevo usuario es realmente un dispositivo nuevo). Un usuario se considera como nuevo en un intervalo de tiempo determinado si ha realizado su primera sesión durante este intervalo de tiempo. Un usuario se considera conservado si ha realizado al menos una sesión durante los últimos 7 días. Los usuarios activos son usuarios que realizaron al menos una sesión durante un período determinado. Puede ordenar por mes, semana, día u hora. Todos los gráficos tienen un aspecto similar, pero le permiten filtrar por diferentes características, como la versión de la aplicación y, a continuación, ordenar por un período de tiempo. La información estándar recopilada mediante la integración del SDK incluye lo siguiente: usuarios activos, nuevo usuario, número de sesiones, duración de cada sesión, información técnica sobre el país, variables locales, ubicación, operador de lenguaje, dispositivos, firmware, red (Wi-Fi), versiones de la aplicación y el SDK, utilizados por los clientes. Esta información puede verse en tiempo real desde la sección de supervisión. 

> Nota: El período de tiempo se basa en la fecha de la configuración de dispositivo de los usuarios, por lo que un usuario cuyo teléfono tiene la fecha establecida incorrectamente podría aparecer en el período de tiempo incorrecto.

- Retención: un usuario se considera como retenido en un intervalo de tiempo determinado si ha realizado su primera sesión durante este intervalo de tiempo. Puede cambiar los intervalos de tiempo durante los que se cuentan los usuarios retenidos (y los nuevos usuarios) a horas, días, semanas o meses. El análisis de retención del usuario se basa en las cohortes. Una cohorte es el conjunto de todos los nuevos usuarios que se han detectado durante un período determinado (es decir, el conjunto de usuarios que realizan su primera sesión durante este período). Utilizamos las cohortes de 1 día, 2 días, 4 días, 7 días o 1 mes. Una vez facilitada una cohorte, cada día 1, 2 días, 4 días, 7 días o 1 mes, Azure Mobile Engagement calcula el conjunto de todos los usuarios que pertenecen a la cohorte y siguen activos (es decir, el conjunto de usuarios que ha realizado al menos una sesión durante el período). Este conjunto de usuarios se denomina versión de la cohorte. (Azure Mobile Engagement puede mostrar cuántos de los usuarios utilizan todavía la aplicación, pero solo la tienda específica de la plataforma puede indicar cuántos de los usuarios desinstalaron la aplicación - por ejemplo, GooglePlay, iTunes, Tienda Windows, etc.). 
- Sesiones: un uso de la aplicación por parte de un usuario. Las sesiones se generan a partir de la secuencia de actividades realizadas por los usuarios (una actividad suele estar asociada al uso de una pantalla de la aplicación, pero puede variar según la manera en que el SDK se ha integrado en la aplicación). Un usuario solo puede realizar una actividad a la vez: las sesiones se inician en cuanto el usuario inicia su primera actividad y se detienen cuando finaliza su última actividad. Si un usuario permanece más de unos pocos segundos sin realizar ninguna actividad, su secuencia de actividades se divide en dos sesiones distintas.
- Actividades: los nombres de cada pantalla de la aplicación y el tiempo que los usuarios dedican en cada pantalla. Las actividades son una opción de análisis personalizada que establece la correspondencia de las etiquetas de "información de la aplicación" que ha configurado para su propia aplicación:
- Ruta de acceso de usuario: muestra cómo navegan los usuarios por las actividades de la aplicación (pantallas). Puede mover el control deslizante para ajustar el nivel de detalle. Los nodos azules representan las actividades de la aplicación. Su tamaño es proporcional al tiempo pasado por los usuarios en él. Los nodos blancos representan el inicio y la detención de la sesión. Los nodos rojos representan bloqueos. Los vínculos representan las transiciones entre las actividades de la aplicación (o entre las actividades y los bloqueos). Haga clic en un nodo o un vínculo para mostrar una información sobre herramientas con más información acerca de los datos: el tiempo empleado en una pantalla en particular, el número de transiciones y el porcentaje de transiciones desde la actividad de origen a la actividad de destino. (Un ---60% ---> B significa que los usuarios de la actividad A pasan a la actividad B el 60 % del tiempo). Puede reorganizar el gráfico como desee para aclararlo. Cada vez que realiza un cambio, se guarda su posición. Puede mostrar u ocultar los bloqueos para aclarar el gráfico.
- Eventos: acciones específicas que realiza un usuario en la aplicación. La distribución de eventos se muestra como el número de eventos por usuario y sesión. Un evento representa una acción instantánea, por ejemplo, hacer clic en un botón o la recepción de una notificación. (El significado de los eventos depende de cómo se ha integrado el SDK en la aplicación). Un evento puede producirse durante una sesión o un trabajo o puede ser independiente.
- Trabajos: similares a los eventos, excepto en que se centran en la duración de la acción. Por ejemplo, Trabajos podría indicarle información técnica acerca de cuánto tiempo tarda el contenido en cargarse o en llamar al servicio web. También puede mostrar el tiempo que tarda un usuario en rellenar un formulario, crear una cuenta o realizar una compra. Un trabajo representa la duración de una tarea, por ejemplo, la duración de una tarea de descarga o el tiempo que se muestra una pancarta en la pantalla. (El significado de Trabajos depende de cómo se ha integrado el SDK en la aplicación). Los trabajos se suelen asociados a tareas en segundo plano que se realizan fuera del ámbito de una sesión (es decir, sin ninguna actividad de usuario).
- Notas técnicas: información técnica acerca de los dispositivos de los usuarios de la aplicación de los que puede realizar un seguimiento, como la configuración regional, el operador, la red, el dispositivo, el firmware, el tamaño de la pantalla de los dispositivos de los usuarios y la versión de la aplicación y la versión del SDK utilizada en la aplicación.
- Errores: información sobre los errores técnicos dentro de la aplicación que no provocan que la aplicación se bloquee. Un error es un problema de instantáneo, por ejemplo, un error de red o una manipulación incorrecta. (El significado de los eventos depende de cómo se ha integrado el SDK en la aplicación). Un error puede producirse durante una sesión o un trabajo o puede ser independiente.
- Bloqueos: información sobre los errores que provocan que la aplicación se bloquee. Un bloqueo es una condición inesperada en la que la aplicación deja de realizar sus funciones esperadas y se debe detener. Un bloqueo suele ser debido a un error en la aplicación.
 
![Analytics2][11]

## Acceso a la información general de retención
![Analytics3][12]

La información general de retención se desglosa a la mitad en varias tarjetas, cada una con la información general correspondiente a un período de retención determinado. El período de retención de dos días se muestra en el ejemplo. Las demás tarjetas muestran los períodos de retención de 4 y 7 días.

## Descripción de las tarjetas de información general de retención
![Analytics4][13]

### Cada tarjeta se compone de tres partes principales:
1. 1: la cohorte y el período considerado
2. 2-4: la retención del período actual
3. 5: un minigráfico del historial

### Aquí se detalla información sobre cada elemento:
1.    Cohorte y período: este título proporciona el tipo de cohorte. Aquí "Período de dos días" significa que se observará el comportamiento de los usuarios durante 2 días, los usuarios que han llegado durante un período de 2 días y si se conectan en los siguientes bloques de 2 días. El ejemplo anterior considera la actividad de los usuarios entre el 21 y el 22 de noviembre.
2.    Esto proporciona la tasa de retención del 21 y el 22 de noviembre de los usuarios que llegan el 19 y el 20 de noviembre. Aquí tuvimos 1 usuario activo entre el 21 y el 22, de los 3 que eran usuarios nuevos entre el 19 y el 20.
3.    Este indicador visual proporciona la misma información que anteriormente representada gráficamente. (La tercera parte del círculo corresponde al número de 33 %). El color proporciona información adicional: el color verde indica que este número está creciendo en comparación con el cálculo anterior. El color amarillo significa estable, y el rojo significa decreciente.
4.    Esto indica los valores utilizados para el cálculo.
5.    Se trata de un minigráfico del historial de los valores de retención. Permite ver los valores del pasado para tener una visión amplia de cómo ha evolucionado.


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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 

<!---HONumber=AcomDC_1203_2015-->