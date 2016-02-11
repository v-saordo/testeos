<properties 
	pageTitle="Aplicación web de .NET en el Servicio de aplicaciones de Azure con administración de rendimiento de aplicaciones de New Relic" 
	description="Aprenda a usar la supervisión de rendimiento de New Relic para las aplicaciones de ASP.NET que se ejecutan en el Servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="stepsic"/>



# Aplicación web de .NET en el Servicio de aplicaciones de Azure con administración de rendimiento de aplicaciones de New Relic

En esta guía se describe cómo agregar la supervisión del rendimiento de primer nivel de New Relic en [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Veremos cómo agregar de forma fácil y sencilla New Relic a la aplicación y le mostraremos algunas de las características de New Relic. Para obtener más información sobre el uso de New Relic, consulte [Uso de New Relic](#using-new-relic).

## ¿Qué es New Relic?

New Relic es una herramienta para desarrolladores que supervisa las aplicaciones de producción y proporciona un conocimiento profundo de su rendimiento y confiabilidad. Está diseñado para ahorrar tiempo al identificar y diagnosticar problemas de rendimiento, poniendo al alcance de la mano toda la información necesaria para solucionar estos problemas.

New Relic realiza el seguimiento del tiempo de carga y de la capacidad de proceso de su transacción web, tanto para los exploradores del servidor como de los usuarios. Muestra cuánto tiempo pasa en la base de datos, analiza las consultas lentas y las solicitudes web, proporciona supervisión y alertas del tiempo de actividad, realiza el seguimiento de excepciones de la aplicación y mucho más.

## Precio especial de New Relic en la Tienda de Azure

La versión estándar de New Relic es gratuita para usuarios de Azure. La versión profesional de New Relic se ofrece en varios paquetes basados en el modo de sitio web que use y el tamaño de instancia si está usando el modo reservado.

Para obtener información sobre precios, consulte la [página de New Relic en Azure Marketplace](/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE] El precio solo se muestra para 10 equipos como máximo. Para cantidades mayores, póngase en contacto con New Relic (sales@newrelic.com) para obtener información de precios por volumen.

Los clientes de Azure reciben una suscripción de prueba a New Relic Pro de dos semanas de duración cuando implementan el agente de New Relic.

Suscripción a New Relic a través de Azure Marketplace 
--

New Relic se integra perfectamente con los roles web y de trabajo y el Servicio de aplicaciones de Azure.

Para suscribirse a New Relic directamente desde Azure Marketplace, siga estos cuatro sencillos pasos.

## Paso 1. Creación de una cuenta de New Relic

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y, en la esquina, haga clic en **Nuevo**.
3. Haga clic en **Servicios para desarrolladores** > **New Relic APM**.
4. Configure la cuenta de New Relic especificando los siguientes valores y haga clic en **Crear**.
	- **Name**
	- **Nivel de precios**
	- **Grupo de recursos**
	- **Suscripción**
	- **Ubicación**
	- **Condiciones legales**

11. Cuando haya hecho clic en **Crear**, comenzará el proceso de creación de la cuenta de New Relic. Puede supervisar el estado haciendo clic en el botón **Notificaciones**. Una vez creada, se mostrará la hoja de la cuenta de New Relic.

12. Para recuperar la clave de licencia de New Relic, examine el panel **Essentials** en la parte superior de la hoja. La instancia de Aplicaciones web registrará automáticamente esta clave de licencia en su configuración de la aplicación al integrar la aplicación web con la cuenta de New Relic.

## Paso 2: Configuración de la integración de New Relic para la aplicación web

1. En el [Portal de Azure](https://portal.azure.com/), abra la hoja de la aplicación web.
2. Haga clic en el menú "..." que aparece en la parte superior de la hoja y seleccione **Agregar iconos**.
3. En la ficha **Supervisión**, seleccione **Resumen de la aplicación** y arrástrelo al lugar donde desea que aparezca el icono en la hoja de su aplicación web.
4. Haga clic en Listo para terminar de agregar iconos.
5. Haga clic en el icono **Supervisión de aplicaciones** y seleccione **New Relic**.
6. Seleccione la cuenta que creó en el paso anterior y haga clic en **Aceptar**. 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	Cuando termine de guardarse, haga clic en **Toda la configuración** en la hoja de la aplicación web y luego en **Configuración de la aplicación**. Debería ver el parámetro **NEWRELIC\_LICENSEKEY** agregado a la sección **Configuración de la aplicación** de la hoja para admitir New Relic:

	>[AZURE.NOTE] Se puede tardar hasta 30 segundos en que la nueva configuración de la aplicación surta efecto. Para que la configuración surta efecto de inmediato, reinicie la aplicación web.

## Paso 3: Publicación de la aplicación web ASP.NET

Use Visual Studio para publicar la aplicación web. Si anteriormente publicó la aplicación web, vuelva a publicarla para que la instancia de Aplicaciones web agregue el paquete New Relic NuGet necesario para habilitar la supervisión de New Relic.

## Paso 4 Comprobar el rendimiento de la aplicación en New Relic.

Para ver el panel de New Relic:

2. En el [Portal de Azure](https://portal.azure.com/), abra la hoja de la aplicación web.
3. Haga clic en **Supervisión de aplicaciones** > **nombre de la aplicación** > **Vista en New Relic**.

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. Si es la primera vez que usa la cuenta, configure la información de la cuenta.
3. En la barra de menús de New Relic, seleccione **Applications > (nombre de la aplicación)**.

	El panel **Monitoring > Overview** se muestra automáticamente.

	![Panel de supervisión de New Relic](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

	Después de seleccionar una aplicación de la lista en el menú **Applications**, el panel **Overview** muestra información actual del explorador y del servidor de aplicaciones.

### <a id="using-new-relic"></a>Uso de New Relic

Después de seleccionar la aplicación de la lista en el menú Applications, el panel Overview muestra información actual del explorador y del servidor de aplicaciones. Para alternar entre las dos vistas, haga clic en el botón **App Server** o **Browser**.

Además de las funciones de <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">interfaz de usuario estándar de New Relic</a> e <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">información detallada del panel</a>, el panel Applications Overview muestra funciones adicionales.

<table border="1">
  <thead>
    <tr>
      <th><b>Si desea...</b></th>
      <th><b>Haga esto...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Mostrar la información del panel para el explorador o servidor de aplicaciones seleccionado</td>
       <td>Haga clic en el botón <b>App Server</b> o <b>Browser</b>.</td>
    </tr>
     <tr>
       <td>Vista de los niveles de umbral para la puntuación de <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> de la aplicación</td>
       <td>Seleccione el icono <b>?<b> de puntuación de Apdex.</b></b></td>
    </tr>
    <tr>
       <td>Ver los detalles de Apdex globales</td>
       <td>En la vista <b>Browser</b> de Overview, seleccione cualquier lugar en el mapa Global Apdex.<br /><b>Sugerencia:</b> para ir directamente al panel <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> de la aplicación seleccionada, haga clic en el título <b>Global Apdex</b> o haga clic en cualquier lugar del mapa Global Apdex.</td>
    </tr>
    <tr>
       <td>Vista del panel <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a></td>
       <td>Haga clic en la tabla Web Transactions en el panel Applications Overview. O bien, para ver los detalles sobre una transacción web específica (incluidas <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>), haga clic en su nombre.</td>
    </tr>
    <tr>
       <td>Ver el panel <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td>Haga clic en el título del gráfico de tasas de error del panel Applications Overview.<br /><b>Sugerencia:</b> también puede ver el panel Errors en <b>Applications</b> > (su aplicación) > Events > Errors.</td>
    </tr>
    <tr>
       <td>Ver los detalles del servidor de aplicaciones</td>
       <td><p>Realice una de las operaciones siguientes:<p>
        <ul>
          <li>Alterne entre una vista de tabla de los hosts o desglose los detalles de las métricas de cada host.</li>
          <li>Haga clic en un nombre de servidor individual.</li>
          <li>Seleccione una puntuación Apdex del servidor individual.</li>
          <li>Haga clic en una memoria o uso de CPU del servidor individual.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

A continuación se muestra un ejemplo del panel Applications Overview cuando se selecciona la vista de explorador.

![Consola del Administrador de paquetes](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## Pasos siguientes

Consulte estos recursos adicionales para obtener más información:

 * [Instalación de .NET Agent en aplicaciones web de Azure](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-preview-portal#install-new-relic-azure-webapps): Procedimientos de instalación de .NET Agent de New Relic. 
 * [Nueva interfaz de usuario de New Relic](https://newrelic.com/docs/site/the-new-relic-ui): información general sobre la interfaz de usuario de New Relic, configuración de derechos de usuario y perfiles, uso de funciones estándar e información detallada del panel.
 * [Descripción general de aplicaciones](https://newrelic.com/docs/site/applications-overview): características y funciones disponibles al usar el panel Applications Overview de New Relic.
 * [Apdex](https://newrelic.com/docs/site/apdex): información general sobre cómo mide Apdex la satisfacción de los usuarios finales con la aplicación.
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): información general sobre cómo detalla RUM el tiempo que tardan los exploradores de sus usuarios en cargar las páginas web, de dónde proceden y qué exploradores usan.
 * [Buscar ayuda](https://newrelic.com/docs/site/finding-help): recursos disponibles en el centro de ayuda en línea de New Relic.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).


[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 

<!---HONumber=AcomDC_0128_2016-->