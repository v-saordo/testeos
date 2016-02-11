<properties 
	pageTitle="Tutorial: exportación de telemetría a Base de datos SQL desde Application Insights" 
	description="Exportación continua de datos de Application Insights a mediante el Análisis de transmisiones" 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="awills"/>
 
# Tutorial: exportación a SQL desde Application Insights mediante Análisis de transmisiones

En este artículo se muestra cómo trasladar los datos de telemetría desde [Application Insights para Visual Studio][start] a una base de datos SQL de Azure mediante la [Exportación continua][export] y el [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/).

La Exportación continua traslada los datos de telemetría a Almacenamiento de Azure en formato JSON. Analizaremos los objetos JSON mediante Análisis de transmisiones de Azure y crearemos filas en una tabla de base de datos.

(De manera más general, la Exportación continua es la forma de realizar su propio análisis de la telemetría que las aplicaciones envían a Application Insights. Se puede adaptar este ejemplo de código para realizar otras operaciones con la telemetría exportada, como la adición de datos).

Comenzaremos con la suposición de que ya dispone de la aplicación que desea supervisar.


En este ejemplo, vamos a usar los datos de la vista de página, pero se puede ampliar fácilmente el mismo patrón a otros tipos de datos, como eventos y excepciones personalizados.


## Incorporación del SDK de Application Insights

Para supervisar la aplicación, se [agrega un SDK de Application Insights][start] a la aplicación. Hay diferentes SDK y herramientas auxiliares para distintas plataformas, IDE y lenguajes. Puede supervisar páginas web, servidores web Java o ASP.NET y dispositivos móviles de varios tipos. Todos los SDK envían telemetría al [portal de Application Insights][portal], donde puede usar nuestras potentes herramientas de análisis y diagnóstico y exportar los datos al almacenamiento.

Primeros pasos:

1. Obtenga una [cuenta en Microsoft Azure](https://azure.microsoft.com/pricing/).
2. En el [portal de Azure][portal], agregue un nuevo recurso de Application Insights para su aplicación:

    ![Elija Nuevo, Servicios para desarrolladores, Application Insights y elija el tipo de aplicación.](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (Su tipo de aplicación y la suscripción podrían ser diferentes).
3. Abra el inicio rápido para descubrir cómo configurar el SDK para su tipo de aplicación.

    ![Elija Inicio rápido y siga las instrucciones.](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    Si no aparece el tipo de aplicación, eche un vistazo a la página [Introducción][start].

4. En este ejemplo, estamos supervisando una aplicación web, por lo que podemos usar las herramientas de Azure en Visual Studio para instalar el SDK. Indique el nombre de su recurso de Application Insights:

    ![En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y elija Agregar Application Insights. Al enviar la telemetría, opte por crear un recurso nuevo o utilizar uno existente.](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. Publique la aplicación y vea los datos de telemetría que aparecen en el recurso de Application Insights.


## Creación de almacenamiento en Azure

La exportación continua siempre envía los datos a una cuenta de almacenamiento de Azure, por lo que necesitará crear primero el almacenamiento.

1. Cree una cuenta de almacenamiento "clásica" en su suscripción en el [portal de Azure][portal].

    ![En el portal de Azure, elija Nuevo, Datos, Almacenamiento. Seleccione Clásico y elija Crear. Proporcione un nombre de almacenamiento.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Crear un contenedor

    ![En el nuevo almacenamiento, seleccione Contenedores, haga clic en el icono Contenedores y luego, en Agregar.](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Copie la clave de acceso de almacenamiento.

    La necesitará en seguida para configurar la entrada para el servicio de análisis de transmisiones.

    ![En el almacenamiento, abra Configuración, Claves y realice una copia de la clave de acceso principal.](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Inicio de la exportación continua al almacenamiento de Azure

1. En el portal de Azure, busque el recurso de Application Insights que ha creado para la aplicación.

    ![Elija Examinar, Application Insights, su aplicación.](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Cree una exportación continua.

    ![Elija Configuración, Exportación continua, Agregar.](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Seleccione la cuenta de almacenamiento que creó anteriormente:

    ![Establezca el destino de exportación.](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Configure los tipos de eventos que desea ver:

    ![Elija los tipos de evento.](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


3. Permita que se acumulen algunos datos. Póngase cómo y deje que los usuarios usen su aplicación durante un tiempo. Así, aparecerá la telemetría y verá gráficos estadísticos en el [explorador de métricas](app-insights-metrics-explorer.md) y eventos individuales en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md).

    Y, además, exportará los datos en el almacenamiento.

4. Inspeccione los datos exportados, ya sea en el portal (elija **Examinar**, seleccione la cuenta de almacenamiento y luego **Contenedores**) o bien en Visual Studio. En Visual Studio, elija **Ver/Cloud Explorer** y abra Azure/Almacenamiento. (Si no dispone de esta opción de menú, deberá instalar el SDK de Azure: abra el cuadro de diálogo Nuevo proyecto y Visual C#/Nube/Obtener el SDK de Microsoft Azure para. NET.)

    ![En Visual Studio, abra Explorador de servidores, Azure, Almacenamiento.](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

    Tome nota de la parte común del nombre de la ruta de acceso, que se deriva del nombre de la aplicación y de la clave de instrumentación.

Los eventos se escriben en archivos de blob en formato JSON. Cada archivo puede contener uno o varios eventos. Así, es probable que queramos leer los datos de eventos y filtrar por los campos que deseemos. Se pueden realizar multitud de acciones con los datos, pero nuestro plan de hoy consiste en usar Stream Analytics para trasladar los datos a una base de datos SQL. De este modo, será más sencillo ejecutar muchas consultas interesantes.

## Creación de una Base de datos SQL de Azure

Una vez más a partir de su suscripción en el [portal de Azure][portal], cree la base de datos (y un servidor nuevo, a menos que ya tenga uno) en la que va a escribir los datos.

![Nuevo, Datos, SQL.](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Asegúrese de que el servidor de base de datos permite el acceso a servicios de Azure:


![Examinar, Servidores, su servidor, Configuración, Firewall, Permitir acceso a Azure.](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Creación de una tabla en la base de datos SQL de Azure

Conéctese a la base de datos creada en la sección anterior con su herramienta preferida de administración. En este tutorial, usaremos [Herramientas de administración de SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Cree una nueva consulta y ejecute la siguiente instrucción T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

En este ejemplo, usamos datos de vistas de página. Para ver los demás datos disponibles, observe el resultado de JSON y consulte el [modelo de exportación de datos](app-insights-export-data-model.md).

## Creación de una instancia de Análisis de transmisiones de Azure

Desde el [Portal de Azure clásico](https://manage.windowsazure.com/), seleccione el servicio Análisis de transmisiones de Azure y cree un nuevo trabajo de Análisis de transmisiones:


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

Cuando se cree el nuevo trabajo, expanda los detalles:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Establecimiento de la ubicación del blob

Configúrelo de manera que tome los datos del blob de Exportación continua:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Ahora, necesitará la clave de acceso principal de la cuenta de almacenamiento, que anotó anteriormente. Establézcala como la clave de cuenta de almacenamiento.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Establecimiento del patrón del prefijo de la ruta de acceso 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Asegúrese de establecer el formato de fecha como **AAAA-MM-DD** (con **guiones**).

El patrón del prefijo de la ruta de acceso especifica cómo busca el Análisis de transmisiones los archivos de entrada en el almacenamiento. Deberá establecerlo para que coincida con el modo en que la Exportación continua almacena los datos. Configúrelo como este caso que se muestra a continuación:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

En este ejemplo:

* `webapplication27` es el nombre del recurso de Application Insights, **todo en minúsculas**. 
* `1234...` es la clave de instrumentación del recurso de Application Insights **con los guiones quitados**. 
* `PageViews` es el tipo de datos que se van a analizar. Los tipos disponibles dependen del filtro definido en la Exportación continua. Examine los datos exportados para ver los demás tipos disponibles y vea el [modelo de exportación de datos](app-insights-export-data-model.md).
* `/{date}/{time}` es un patrón escrito literalmente.

Para obtener el nombre y el valor iKey del recurso de Application Insights, abra Essentials en su página de información general o bien abra la Configuración.

#### Finalización de la instalación inicial

Confirme el formato de serialización:

![Confirme y cierre el asistente.](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Cierre el asistente y espere a que el programa de instalación finalice.

>[AZURE.TIP] Use la función Sample para comprobar que estableció correctamente la ruta de acceso de entrada. Si se produce un error: compruebe que hay datos en el almacenamiento para el intervalo de tiempo de muestreo que eligió. Modifique la definición de entrada y compruebe que establece correctamente la cuenta de almacenamiento, el prefijo de ruta de acceso y el formato de fecha.

## Establecimiento de la consulta

Abra la sección de consulta:

![En Análisis de transmisiones, seleccione Consulta.](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Reemplace la consulta predeterminada por lo siguiente:

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Observe que las primeras propiedades son específicas de los datos de la vista de página. Las exportaciones de otros tipos de telemetría tendrán diferentes propiedades. Vea la [referencia detallada del modelo de datos para los tipos y valores de propiedad](app-insights-export-data-model.md).

## Configuración de la salida a la base de datos

Seleccione SQL como salida.

![En Análisis de transmisiones, seleccione Salidas.](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Especifique la base de datos SQL.


![Rellene los detalles de la base de datos.](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Cierre el asistente y espere una notificación que indica que se ha configurado la salida.

## Inicio del procesamiento

Inicie el trabajo desde la barra de acción:

![En Análisis de transmisiones, haga clic en Inicio.](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

Puede elegir si desea iniciar el procesamiento de los datos a partir de ahora o bien empezar con datos anteriores. Lo segundo resulta útil si la Exportación continua ya se ha estado ejecutando durante un tiempo.


![En Análisis de transmisiones, haga clic en Inicio.](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Después de unos minutos, vuelva a las herramientas de administración de SQL Server y consulte los datos que están entrando. Por ejemplo, utilice una consulta como esta:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## Artículos relacionados

* [Exportación a SQL con un rol de trabajo](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exportación a PowerBI mediante Análisis de transmisiones](app-insights-export-power-bi.md)
* [Referencia detallada del modelo de datos para los tipos y valores de propiedad.](app-insights-export-data-model.md)
* [Exportación continua en Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0128_2016-->