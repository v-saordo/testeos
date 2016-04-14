<properties 
	pageTitle="Notas de la versión de la extensión de Visual Studio para Application Insights" 
	description="Las últimas novedades sobre las herramientas de Visual Studio para Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="aruna" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="acearun"/>
 
# Notas de la versión de Herramientas de Application Insights para Visual Studio

## Versión 4.3
### Búsqueda de telemetría desde sesiones de depuración local
Con esta versión, introducimos la capacidad para buscar la telemetría de Application Insights generada en la sesión de depuración de Visual Studio. Anteriormente, la búsqueda solo era posible si había registrado la aplicación con Application Insights. Con esta versión, la aplicación únicamente necesita que el SDK de Application Insights esté instalado para buscar la telemetría local.

#### Si tiene una aplicación ASP.NET con el SDK de Application Insights

- Depure la aplicación.
- Abra Búsqueda de Application Insights de una de estas formas.
	- Menú Ver -> Otras ventanas -> Búsqueda de Application Insights
	- Haga clic en el botón de la barra de herramientas de Application Insights.
	- En el Explorador de soluciones, expanda ApplicationInsights.config -> Busque la telemetría de la sesión de depuración.
- Si aún no se ha registrado con Application Insights, se abrirá la ventana de búsqueda en el modo de telemetría de la sesión de depuración.
- Haga clic en el icono de búsqueda para ver los datos de telemetría local.

![Carga completa](./media/app-insights-release-notes-vsix/LocalSearch.png)



##Versión 4.2
En esta versión hemos agregado características para facilitar la búsqueda de datos en el contexto de eventos, la capacidad de saltar al código desde más eventos de datos y una experiencia para enviar sin esfuerzo los datos de registro a Application Insights. Esta extensión se actualiza mensualmente; si tiene comentarios o solicitudes de funcionalidades, envíelos a aidevtools@microsoft.com.
###- Experiencia de registro sin un solo clic
Si ya está usando el seguimiento de NLog, Log4Net o System.Diagnostics, no tendrá que preocuparse acerca de cómo mover todos los seguimientos a AI, ya que estamos integrando los adaptadores de registro de Application Insights con la experiencia de configuración normal. Si ya tiene uno de estos marcos de registro configurados, esto es lo que debe hacer:
####Si ya ha agregado Application Insights
- Haga clic con el botón derecho en el nodo de proyecto y elija Application Insights, Configurar Application Insights. Asegúrese de que ve el la opción de agregar el adaptador correcto en la ventana de configuración. 
- O bien, cuando compile la solución, observe la ventana emergente que aparece en la parte superior derecha de la pantalla y haga clic en Configurar. ![Aviso de inicio de sesión](./media/app-insights-release-notes-vsix/LoggingToast.png)

Una vez instalado el adaptador de registro, puede ejecutar la aplicación y asegurarse de que ve los datos en la pestaña Herramientas de diagnóstico de la forma siguiente: ![Seguimientos](./media/app-insights-release-notes-vsix/Traces.png)
###- El usuario puede saltar al código o buscarlo donde se genera la propiedad de evento de telemetría
Con la nueva versión, el usuario puede hacer clic en cualquier valor en el detalle de eventos para buscar una cadena coincidente en la solución abierta en ese momento. Se mostrarán resultados en la lista "Resultados de la búsqueda" de Visual Studio tal como se muestra a continuación: ![Buscar coincidencia](./media/app-insights-release-notes-vsix/FindMatch.png)
###- Nueva pantalla para el usuario que no ha iniciado sesión en la ventana de búsqueda
Hemos mejorado la apariencia de la ventana de búsqueda para guiar a los usuarios en su búsqueda de los datos de producción. ![Ventana de búsqueda](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- El usuario puede ver todos los eventos de telemetría asociados al evento
Se agregó una nueva pestaña situada junto a los detalles del evento que contiene consultas predefinidas para ver todos los datos relacionados con el evento de telemetría que está buscando el usuario. Por ejemplo: una solicitud tiene un campo denominado identificador de operación y todos los eventos asociados a esta solicitud tendrán el mismo identificador de operación; por tanto, si se produjera una excepción al procesar la solicitud, se obtendría el mismo identificador de operación de la solicitud para facilitar su búsqueda, y así sucesivamente. Así pues, el usuario que vea una solicitud ahora podrá hacer clic en "Toda la telemetría para esta operación" para abrir una nueva pestaña con los nuevos resultados de la búsqueda. ![Elementos relacionados](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - Agregar historial hacia delante y hacia atrás en Búsqueda
El usuario ahora puede ir hacia delante y hacia atrás entre los resultados de la búsqueda. ![Volver](./media/app-insights-release-notes-vsix/GoBAck.png)

##Versión 4.1
Esta versión incluye una serie de nuevas características y mejoras de las existentes. Para obtener esta versión, debe haber instalado Actualización 1 en el equipo.

### Salto de una excepción al método en el código fuente
Ahora los usuarios que vean excepciones procedentes de sus aplicaciones de producción en la ventana Búsqueda de Application Insights pueden saltar al método del código donde se haya producido la excepción. Basta con tener cargado el proyecto adecuado; nosotros nos encargamos del resto. (Para más información sobre la ventana Búsqueda, consulte las notas de la versión 4.0 a continuación).

#### ¿Cómo funciona?

Cuando no hay ninguna solución abierta, se puede usar Búsqueda de Application Insights sin tener que abrir una. En ese caso, el área de seguimiento de la pila mostrará un mensaje informativo y muchos de los elementos en ella aparecerán atenuados.


Si había información disponible sobre el archivo, es posible que algunos elementos sean vínculos, pero el elemento de información de la solución seguirá estando visible.

Al hacer clic en el hipervínculo, le llevará al lugar donde se encuentra el método seleccionado en el código. Puede que exista una diferencia en el número de versión, pero la característica para saltar a la versión correcta del código se incluirá en versiones posteriores.

![Al hacer clic en la excepción](./media/app-insights-release-notes-vsix/jumptocode.png)

###Nuevos puntos de entrada a la experiencia de búsqueda en el Explorador de soluciones 

![Punto de entrada en el Explorador de soluciones](./media/app-insights-release-notes-vsix/searchentry.png)


###Notificación emergente del sistema cuando se completa la publicación
Aparecerá un elemento emergente una vez que se publique el proyecto en línea, para que vea los datos de Application Insights en producción.

![Notificación emergente](./media/app-insights-release-notes-vsix/publishtoast.png)

## Versión 4.0

###Datos de Búsqueda de Application Insights en Visual Studio
Al igual que Búsqueda en el portal de Application Insights, puede filtrar y hacer búsquedas por tipos de eventos, valores de propiedades y texto, además de inspeccionar eventos individuales.

![Ventana Búsqueda](./media/app-insights-release-notes-vsix/search.png)

###Visualización de datos procedentes de su cuadro local en la ventana Herramientas de diagnóstico

La telemetría también aparecerá junto con otros datos de depuración en el concentrador de diagnósticos de Visual Studio. Esto solo admite ASP.NET 4.5; la compatibilidad con ASP.NET 5 se incluirá en próximas versiones.

![Ventana del concentrador de diagnósticos](./media/app-insights-release-notes-vsix/diagtools.png)

###Incorporación del SDK a su proyecto sin tener que iniciar sesión en Azure

Ya no tendrá que iniciar sesión en Azure para agregar paquetes de Application Insights al proyecto, ya sea en el cuadro de diálogo Nuevo proyecto o en el menú contextual del proyecto. Si inicia sesión, se instalará y configurará el SDK para enviar telemetría al portal, como antes. Si no inicia sesión, se agregará el SDK al proyecto y se generará telemetría para el concentrador de diagnósticos; podrá configurarlo más adelante si se desea.

![Cuadro de diálogo Nuevo proyecto](./media/app-insights-release-notes-vsix/newproject.png)

###Compatibilidad con dispositivos

En *Connect();* 2015 [anunciamos](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que la experiencia de DevOps para móviles con dispositivos es HockeyApp. HockeyApp ayuda a distribuir compilaciones beta a los evaluadores, recopilar y analizar todos los bloqueos de la aplicación y recopilar comentarios directamente de los clientes. HockeyApp es compatible con cualquier plataforma en la que compile la aplicación móvil, ya sea iOS, Android, Windows o una solución multiplataforma como Xamarin, Cordova o Unity.

En versiones futuras de la extensión Application Insights, agregaremos nuevas funcionalidades para hacer posible una experiencia más integrada entre HockeyApp y Visual Studio. Por ahora, para empezar a trabajar con HockeyApp, basta con agregar la referencia de NuGet; consulte la [documentación](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) para más información.

 

<!---HONumber=AcomDC_0302_2016-->