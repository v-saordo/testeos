<properties 
	pageTitle="Uso de New Relic con Azure | Microsoft Azure" 
	description="Aprenda a utilizar el servicio New Relic para administrar y supervisar su aplicación de Azure." 
	services="" 
	documentationCenter=".net" 
	authors="nickfloyd" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="nickfloyd@newrelic.com"/>



# Administración del rendimiento de la aplicación New Relic en Azure

En esta guía se describe cómo agregar la supervisión del rendimiento de primer nivel de New Relic a sus aplicaciones hospedadas en Azure. Veremos cómo agregar de forma fácil y sencilla New Relic a la aplicación y le mostraremos algunas de las características de New Relic. Para obtener más información sobre el uso de New Relic, consulte [Uso de New Relic](#using-new-relic).

## ¿Qué es New Relic?

New Relic es una herramienta para desarrolladores que supervisa las aplicaciones de producción y proporciona un conocimiento profundo de su rendimiento y confiabilidad. Está diseñado para ahorrar tiempo al identificar y diagnosticar problemas de rendimiento, poniendo al alcance de la mano toda la información necesaria para solucionar estos problemas.

New Relic realiza el seguimiento del tiempo de carga y de la capacidad de proceso de su transacción web, tanto para los exploradores del servidor como de los usuarios. Muestra cuánto tiempo pasa en la base de datos, analiza las consultas lentas y las solicitudes web, proporciona supervisión y alertas del tiempo de actividad, realiza el seguimiento de excepciones de la aplicación y mucho más.

## Precio especial de New Relic en la Tienda de Azure


Los usuarios de Azure pueden obtener la versión estándar de New Relic de manera gratuita, mientras que New Relic Pro se ofrece por tamaño de instancia para los Servicios en la nube de Azure.

Para obtener información sobre precios, consulte la [página de New Relic en la Tienda de Azure](https://azure.microsoft.com/marketplace/partners/newrelic/newrelic/).

> [AZURE.NOTE] El precio solo se muestra para 10 equipos como máximo. Para cantidades mayores, póngase en contacto con New Relic (sales@newrelic.com) para obtener información de precios por volumen.

Los clientes de Azure reciben una suscripción de prueba a New Relic Pro de dos semanas de duración cuando implementan el agente de New Relic.

## Suscripción en New Relic mediante el uso de la Tienda de Azure

New Relic se integra perfectamente con los roles web y de trabajo de Azure.

Para suscribirse directamente a New Relic desde la Tienda de Azure, siga estos tres sencillos pasos.

### Paso 1. Registro a través de la Tienda de Azure

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. En el panel inferior del portal de administración, haga clic en **Nuevo**.
3. Haga clic en **Tienda**.
4. En el cuadro de diálogo **Elegir un complemento**, seleccione **New Relic** y haga clic en **Siguiente**.
5. En el cuadro de diálogo **Personalizar complemento**, seleccione el plan de New Relic que quiera.
6. Especifique, si procede, un código de promoción.
7. Escriba el nombre con el que aparecerá el servicio New Relic en la configuración de Azure o use el valor predeterminado **NewRelic**. El nombre debe ser único en la lista de elementos suscritos de la Tienda de Azure.
8. Elija un valor para la región; por ejemplo, **Oeste de EE. UU.**.
9. Haga clic en **Siguiente**.
10. En el cuadro de diálogo **Revisar compra**, revise la información del plan y los precios, así como los términos legales. Si acepta los términos, haga clic en **Comprar**.
11. Cuando haya hecho clic en **Comprar**, comenzará el proceso de creación de la cuenta de New Relic. Puede supervisar el estado en el Portal de administración de Azure.
12. Para recuperar la clave de licencia de New Relic, haga clic en **Valores de salida**. 
13. Copie la clave de licencia que se muestra. Tendrá que escribirla cuando instale el paquete New Relic Nuget.

### Paso 2: Instalar el paquete NuGet

1. Abra la solución de Visual Studio o cree una nueva seleccionando **Archivo > Nuevo > Proyecto**.

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. Si todavía no tiene un proyecto de servicios en la nube de Azure en la solución, agregue uno haciendo clic con el botón secundario en la aplicación en el Explorador de soluciones y seleccione **Agregar proyecto de servicios en la nube de Azure**.

	![Crear un servicio en la nube](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. Abra la Consola del Administrador de paquetes seleccionando **Tools > Library Package Manager > Package Manager Console**. Establezca su proyecto para que sea el predeterminado en la parte superior de la ventana de la Consola del Administrador de paquetes.

	![Consola del Administrador de paquetes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. En el símbolo del sistema del Administrador de paquetes, escriba `Install-Package
   NewRelicWindowsAzure` y pulse **Entrar**.

	![Instalar el Administrador de paquetes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. Cuando se le solicite la clave de licencia, escriba la clave que ha recibido de la Tienda de Azure.

	![Introducción de clave de licencia](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. Opcional: en el mensaje donde se pide un nombre de aplicación, escriba el nombre de la aplicación tal como aparecerá en el panel de New Relic. O bien, utilice su nombre de solución como valor predeterminado.

	![Escribir el nombre de aplicación](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. Desde el Explorador de soluciones, haga clic con el botón secundario en el proyecto de servicios en la nube de Azure y seleccione **Publicar**.

	![Publicar el proyecto en la nube](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**Nota:** si es la primera vez que implementa esta aplicación en Azure, se le pedirá que escriba las credenciales correspondientes. Para obtener más información, consulte [Implementación de una aplicación web ASP.NET en un sitio web de Azure](app-service-web\web-sites-dotnet-get-started.md).

![configuración de publicación](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Paso 3: Comprobar el rendimiento de la aplicación en New Relic.

Para ver el panel de New Relic:

1. En el Portal de Azure, haga clic en el botón **Administrar**.
2. Inicie sesión con el correo electrónico y la contraseña de la cuenta de New Relic.
3. En la barra de menús de New Relic, seleccione **Applications > (nombre de la aplicación)**.

	El panel **Monitoring > Overview** se muestra automáticamente.

	![Panel de supervisión de New Relic](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	Después de seleccionar una aplicación de la lista en el menú **Applications**, el panel **Overview** muestra información actual del explorador y del servidor de aplicaciones.

### <a id="using-new-relic"></a>Uso de New Relic

Después de seleccionar la aplicación de la lista en el menú Applications, el panel Overview muestra información actual del explorador y del servidor de aplicaciones. Para alternar entre las dos vistas, haga clic en el botón **App server** o **Browser**.

Además de las funciones de [la interfaz de usuario estándar de New Relic](https://newrelic.com/docs/site/the-new-relic-ui#functions") y la [información detallada del panel](https://newrelic.com/docs/site/the-new-relic-ui#drilldown), el panel Applications Overview muestra funciones adicionales.

| Si desea... | Haga esto... |
| ----------------- | ---------- |
| Mostrar la información del panel para el explorador o servidor de aplicaciones seleccionado | Haga clic en el botón **App Server** o **Browser**. |
| Vista de los niveles de umbral para la puntuación de [Apdex](https://newrelic.com/docs/site/apdex) de la aplicación | Seleccione el icono **?** de puntuación de Apdex. |
| Ver los detalles de Apdex globales | En la vista **Browser** de Overview, seleccione cualquier lugar en el mapa Global Apdex. **Sugerencia:** para ir directamente al panel [Geography](https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard") de la aplicación seleccionada, haga clic en el título **Global Apdex** o haga clic en cualquier lugar del mapa Global Apdex. |
| Vista del panel [Web Transactions](https://newrelic.com/docs/applications-dashboards/web-transactions) | Haga clic en la tabla Web Transactions en el panel Applications Overview. O bien, para ver los detalles sobre una transacción web específica (incluidas [Key Transactions](https://newrelic.com/docs/site/key-transactions")), haga clic en su nombre. |
| Ver el panel [Errors](https://newrelic.com/docs/site/errors). | Haga clic en el título del gráfico de tasas de error del panel Applications Overview. **Sugerencia:** también puede ver el panel Errors en **Applications** > (su aplicación) > Events > Errors. |


Además, si desea ver los detalles del servidor de la aplicación, realice una de las acciones siguientes:

- Alterne entre una vista de tabla de los hosts o desglose los detalles de las métricas de cada host.
- Haga clic en un nombre de servidor individual.
- Seleccione una puntuación Apdex del servidor individual.
- Haga clic en una memoria o uso de CPU del servidor individual.

A continuación se muestra un ejemplo del panel Applications Overview cuando se selecciona la vista de explorador.

![Consola del Administrador de paquetes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## Pasos siguientes

Consulte estos recursos adicionales para obtener más información:

 * [Instalación de .NET Agent en Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): procedimientos de instalación de NET Agent de New Relic. 
 * [Nueva interfaz de usuario de New Relic](https://newrelic.com/docs/site/the-new-relic-ui): información general sobre la interfaz de usuario de New Relic, configuración de derechos de usuario y perfiles, uso de funciones estándar e información detallada del panel.
 * [Descripción general de aplicaciones](https://newrelic.com/docs/site/applications-overview): características y funciones disponibles al usar el panel Applications Overview de New Relic.
 * [Apdex](https://newrelic.com/docs/site/apdex): información general sobre cómo mide Apdex la satisfacción de los usuarios finales con la aplicación.
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): información general sobre cómo detalla RUM el tiempo que tardan los exploradores de sus usuarios en cargar las páginas web, de dónde proceden y qué exploradores usan.
 * [Buscar ayuda](https://newrelic.com/docs/site/finding-help): recursos disponibles en el centro de ayuda en línea de New Relic.

<!---HONumber=AcomDC_0128_2016-->