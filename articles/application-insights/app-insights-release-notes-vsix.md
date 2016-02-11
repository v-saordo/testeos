<properties 
	pageTitle="Notas de la versión de la extensión de Visual Studio para Application Insights" 
	description="Las últimas novedades sobre las herramientas de Visual Studio para Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Notas de la versión de Herramientas de Application Insights para Visual Studio 4.1

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

 

<!---HONumber=AcomDC_0121_2016-->