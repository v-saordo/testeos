<properties 
	pageTitle="Modelo de datos de Application Insights" 
	description="Describe las propiedades exportadas con la exportación continua de JSON y usadas como filtros." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Modelo de exportación de datos de Application Insights

En esta tabla se enumeran las propiedades de telemetría enviadas desde los SDK de [Application Insights](app-insights-overview.md) al portal. Verá estas propiedades en el resultado de datos de [Exportación continua](app-insights-export-telemetry.md). También aparecen en los filtros de propiedad del [Explorador de métricas](app-insights-metrics-explorer.md) y la [Búsqueda de diagnóstico](app-insights-diagnostic-search.md).

Hay varios [ejemplos](app-insights-export-telemetry.md#code-samples) que ilustran cómo usarlas.

El "&lt;telemetryType&gt;" de la primera sección es un marcador de posición para cualquiera de los nombres de tipos de telemetría: vista, solicitud, etc.


## &lt;telemetryType&gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    Un contenedor de propiedades de par clave-valor que ofrece extensibilidad en los elementos de telemetría de AppInsights para agregar métricas personalizadas. 

    *Derivación:* los nombres de medidas tienen un tamaño máximo de 100.

    *Valor predeterminado:* si la clave existe, falta el valor, entonces count = 1, value = 0, min/max = 0.

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    Un contenedor de propiedades de par clave-valor que ofrece extensibilidad en los elementos de telemetría de AppInsights para agregar propiedades personalizadas. El programador es capaz de proporcionar una lista de pares clave-valor asociados a un elemento de telemetría. Se realiza un seguimiento de cada clave y se pueden proporcionar 200 claves únicas como máximo por ikey de AppInsights (aplicación). Una clave puede tener una longitud máxima de 100 caracteres. Todos los valores se tratan como cadenas y se puede proporcionar un tamaño máximo de 1000 caracteres. Cada propiedad se clasifica inicialmente como una dimensión, lo que habilita características de segmentación basadas en el conjunto de valores de cada propiedad. Se realiza un seguimiento de cada valor establecido por clave de propiedad para su cardinalidad. Cuando la cardinalidad de una clave supera 100 valores únicos, la propiedad se clasifica como atributo. Se pueden realizar búsquedas en un atributo, pero el atributo no puede ser el destino de la segmentación (agregación o agrupación por). 

    *Derivación:* los nombres de propiedades tienen un tamaño máximo de 100; los valores de propiedades tienen un tamaño máximo de 1024.

    *Valor predeterminado:* si la clave existe, falta el valor, entonces el valor es null.

**count**

    long <telemetryType>.count      
* 
    El número del elemento de telemetría.   

    *Derivación:* si es null, count = 1.

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    La duración del elemento de telemetría. Para la solicitud, se trata el tiempo de ejecución de la solicitud. 

    *Valor predeterminado:* R1: para la vista, este campo es opcional.

**message**

    string <telemetrytype>.message      Max: 10240
* 
    Un mensaje de texto en el tipo de telemetría. Esta cadena está disponible para la búsqueda de texto completo. 

**name**

    string <telemetrytype>.name      Max: 250
* 
    El nombre del elemento de telemetría. Este nombre no es único en diferentes instancias y representa una agrupación de tipos de telemetría. Para las vistas, este valor predeterminado es URLData.base. Para los eventos, se trata de una etiqueta proporcionada por el desarrollador. Para las solicitudes, se trata de un formato legible de la solicitud, como controller\\action. 

    *Ejemplos:*<br/> Nombres de vista:<br/>70-486 Pregunta del examen 1<br/>Sobre - Mi aplicación ASP.NET<br/><br/>Nombres de solicitudes de ejemplo:<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**severity**

    string <telemetryType>.severity      Max: 100
* 
    La gravedad del elemento de telemetría. Esto es válido para elementos de telemetría de seguimiento y excepción 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    La dirección URL de la vista de página, evento, solicitud o RDD. La dirección URL completa y es compatible con la búsqueda de texto completo y la exportación. Este campo puede tener una cardinalidad muy alta y es un atributo. Se analiza en un conjunto de elementos de datos urlData que puede usarse para las agregaciones en el Explorador de métricas. 

    *Valor predeterminado:* R2: en remotedepencyType, si dependencyType = HTTP, este campo es obligatorio<br/> en clientperformanceType, este campo es obligatorio.

    *Ejemplos*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    Una parte del elemento de datos URL, excluido el host y los parámetros de consulta. Es el URI raíz. Este valor puede usarse para la segmentación o la agregación. 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL.

    *Ejemplos*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    El texto del hashtag del elemento de datos URL 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL.

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    El host del elemento de datos URL. Si el elemento de datos URL es un URI local, se representa como vacío 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL.

    *Ejemplos*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>



## availability

**availability**

    simpleMetric availability.availability      
* 
    Un indicador del éxito de la prueba de disponibilidad 

**dataSize**

    simpleMetric availability.datasize      
* 
    El tamaño del mensaje de texto &lt;telemetry&gt;.message 

**result**

    enum (pass/fail) availability.result      
* 
    Un puntero (llamada HTTP) para recuperar la información detallada de la prueba web de disponibilidad 

**runLocation**

    string availability.runlocation      Max: 100
* 
    La ubicación de ejecución de la prueba de disponibilidad 

**testName**

    string availability.testname      Max: 1024
* 
    El nombre de la prueba de disponibilidad 

**testRunId**

    string availability.testrunid      Max: 100
* 
    Un identificador único para la instancia de ejecución de la prueba de disponibilidad 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    La marca de tiempo del principio de la instancia de ejecución de la prueba de disponibilidad 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**estático**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionTrownAtMethod**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *Derivación*: consulte el apéndice para el análisis de la pila de llamadas 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**exceptions.message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    Una parte del tiempo de perTotal. Esta parte representa el tiempo que tardó la aplicación en procesar la respuesta. Para un cliente web, es el tiempo de procesamiento de Document Object Model (DOM). Este tiempo se captura mediante la API de perfTiming del explorador moderno. 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    Una parte del tiempo de perTotal. Esta parte representa el tiempo que tardó la aplicación en realizar la conexión de red. Este tiempo se captura mediante la API de perfTiming del explorador moderno. 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    El tiempo total para cargar la vista. Para los clientes web, equivale a ""tiempo de carga de página"". Este tiempo se captura mediante la API de perfTiming del explorador moderno. 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    Una parte del tiempo de perTotal. Esta parte representa el tiempo que tardó la aplicación en recibir la respuesta de la solicitud. Este tiempo se captura mediante la API de perfTiming del explorador moderno. 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    Una parte del tiempo de perTotal. Esta parte representa el tiempo que tardó la aplicación en enviar la solicitud al servidor. Este tiempo se captura mediante la API de perfTiming del explorador moderno. 


## contexto

**applicationBuild**

    string context.application.build      Max: 100
* 
    El número de compilación de la aplicación de la aplicación 

**applicationVersion**

    string context.application.version      Max: 100
* 
    La versión de la aplicación de la aplicación cliente. No está disponible si está establecido como Unknown. 

    *Ejemplos*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    Representa el tipo de telemetría para un registro de facturación; se usa internamente como una segmentación de elementos de telemetría facturables para una aplicación 

**dataId**

    string context.data.id      Max: 100
* 
    Identificador único del elemento de telemetría. Se asigna en el extremo de recopilación de datos. 

    *Derivación:* UUID4 generado

    *Ejemplos*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    El tiempo del evento telemetría registrado en UTC. Normalmente, se rellena en el cliente. Si falta este campo, se rellena en el extremo de recopilación de datos. El formato del campo es AAAA-MM-DDTHH:MM:SS.sssZ.    

    *Ejemplos*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    La velocidad de muestreo del productor de datos (SDK). Un valor distinto de 1 significa que las métricas asociadas a este elemento de telemetría representan valores de muestreo. Por lo tanto, una velocidad de muestreo de 0,05 daría como resultado cualquier elemento de telemetría 1 que represente un contador de 20. 

**iPhone**

    string context.device.browser      Max: 100
* 
    El explorador del cliente 

    *Valor predeterminado:* si es null, se establece según el procesamiento del agente de usuario. Consulte el apéndice para analizar el agente de usuario

    *Ejemplos*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    La versión del explorador del cliente 

    *Valor predeterminado:* si es null, se establece según el procesamiento del agente de usuario. Consulte el apéndice para analizar el agente de usuario

    *Ejemplos*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    El identificador de la implementación del servidor 


**deviceName**

    string context.device.name      Max: 100
* 
    El nombre del dispositivo en el que se está ejecutando la aplicación 

**locale**

    string context.device.locale      Max: 100
* 
    La variante local de la aplicación en el cliente. Si no se proporciona explícitamente en el elemento de telemetría, se obtiene mediante el procesamiento del campo de agente de usuario. 

    *Ejemplos*<br/> ru<br/>es-ES<br/>de-DE<br/>desconocido

**machineName**

    string context.device.vmname      Max: 100
* 
    El nombre de la máquina del servidor. En el proceso virtualizado, este elemento de datos es equivalente al host subyacente. En el cálculo dedicado, es el nombre del equipo. 


**operatingSystem**

    string context.device.os      Max: 100
* 
    El sistema operativo del cliente 

    *Valor predeterminado:* si es null, se establece según el procesamiento del agente de usuario. Consulte el apéndice para analizar el agente de usuario

    *Ejemplos*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    La versión del sistema operativo del cliente 

    *Valor predeterminado:* si es null, se establece según el procesamiento del agente de usuario. Consulte el apéndice para analizar el agente de usuario

    *Ejemplos*<br/> Windows XP<br/>iOS 8.3<br/>Nokia serie 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    La instancia de proceso del servidor. En un escenario de nube o virtualizado, es el nombre virtual de la instancia de proceso. En una instancia de proceso dedicado, es el nombre de la máquina. En los servicios en la nube de Azure, debe tomar de forma predeterminada el nombre de instancia de rol PaaS o el nombre de la máquina virtual de IaaS  

**parámetro RoleName**

    string context.device.rolename      Max: 100
* 
    Un espacio de nombres lógico del servidor del grupo de instancias de proceso. Para servicios hospedados de Azure, los roles PaaS deben tomar de forma predeterminada el nombre del rol de PaaS. Los roles de IaaS deben tomar de forma predeterminada el nombre de la máquina virtual  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    El alto de la pantalla de la aplicación en el hardware del cliente en el momento en que se registra el elemento de telemetría. Si no se proporciona explícitamente, se obtiene mediante una transformación del elemento de datos de resolución de pantalla. 

    *Derivación:* analizado desde context.device.screenresolution si está presente

    *Ejemplos*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    La resolución de pantalla en el momento en que la aplicación capturó el elemento de telemetría. Puede alternar entre vertical y horizontal durante una sesión. Cuando este atributo se realiza en el nivel de sesión, se usa la primera resolución de pantalla capturada para representar la sesión completa. 

    *Examples*<br/> Resolución de pantalla: alto y ancho<br/>360X640<br/>1280X800<br/>1920x1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    El ancho de pantalla de la aplicación en el hardware de cliente en el momento en que se registra el elemento de telemetría. Si no se proporciona explícitamente, se obtiene mediante una transformación del elemento de datos de resolución de pantalla. 

    *Derivación:* analizado desde context.device.screenresolution si está presente

    *Ejemplos*<br/> 640<br/>800<br/>1080


**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    El elemento de datos interno; representa la versión del agente de SDK que generó el elemento de telemetría 

**city**

    string context.location.city      Max: 100
* 
    La ciudad de la sesión de la aplicación. Se puede proporcionar directamente en el elemento de telemetría. Si no está presente, se rellena según el IPv4 en el elemento de telemetría. Si no se proporciona ningún IPv4, el campo se deja vacío. 

    *Ejemplos*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    La dirección IPv4 del cliente con el formato xxx.xxx.xxx.xxx.   

    *Valor predeterminado:* si es null, se establece en la dirección IP HTTP capturada en el extremo de recopilación de datos

    *Ejemplos*<br/> 0.123.63.143<br/>123.203.131.197

**continent**

    string context.location.continent      Max: 100
* 
    Continente de la sesión de la aplicación. Se puede proporcionar directamente en el elemento de telemetría. Si no está presente, se rellena según el IPv4 en el elemento de telemetría. Si no se proporciona ningún IPv4, el campo se deja vacío. 

    *Ejemplos*<br/> Europa<br/>Norteamérica

**country**

    string context.location.country      Max: 100
* 
    El país de la sesión de la aplicación. Se puede proporcionar directamente en el elemento de telemetría. Si no está presente, se rellena según el IPv4 en el elemento de telemetría. Si no se proporciona ningún IPv4, el campo se deja vacío. 

    *Ejemplos*<br/> Bielorrusia<br/>Países Bajos<br/>Alemania


**state**

    string context.location.province      Max: 100
* 
    Provincia de la sesión de la aplicación. Se puede proporcionar directamente en el elemento de telemetría. Si no está presente, se rellena según el IPv4 en el elemento de telemetría. Si no se proporciona ningún IPv4, el campo se deja vacío. 

    *Ejemplos*<br/> Minsk<br/>Oregon<br/>Serbia Central<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    operation.id es un identificador de correlación que se puede usar en los elementos de telemetría. Por ejemplo, un identificador de solicitud puede llenarse en el operation.id de todos los elementos de telemetría relacionados, lo que permite una correlación entre los elementos de telemetría como rdd, excepciones, eventos, etc.  

**operationName**

    string context.operation.name      Max: 100
* 
    Un nombre de operación en lenguaje natural. Consulte operation.id. Permite una agrupación de identificadores de operación similares como Compra completa.   

**operationParentId**

     context.operation.parentid      
* 
    Un identificador primario para operation.id, que permite el anidamiento de los id. de correlación de telemetría.   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    Si issynthetic=true, este elemento de datos representa el origen de los datos sintéticos. 

    *Valor predeterminado:* si es null, el agente de usuario se inspecciona para orígenes sintéticos conocidos (agentes de búsqueda, etc.) y, en función de esto, se puede establecer el origen.

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    Indica que se generó el elemento de telemetría debido a pruebas sintéticas y actividad de usuario no real. 

    *Valor predeterminado:* si es null, el agente de usuario se inspecciona con una lista de agentes sintéticos conocidos. Si se encuentra una coincidencia, el valor se establece en true.<br/>Si el agente de usuario es null, se establece en false

**session.Id**

    String context.session.id      Max: 100
* 
    Identificador único de una interacción de usuarios reales con una aplicación. Esta interacción es una "" sesión"". Toda la telemetría generada por la aplicación con la misma iKey debe contener este identificador único. <br/><br/>Una sesión se define mediante los eventos consecutivos dentro de la misma interacción del usuario. Un período de más de 30 minutos sin un evento de telemetría señala el final de una sesión.   

    *Valor predeterminado:* no válido en MetricType, BillingType

    *Ejemplos*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate  
    
**anonUserId**

    string context.user.anonId      Max: 100
* 
    Un identificador único que define un usuario anónimo dentro de la aplicación. Cuando se usa un SDK, se genera automáticamente y se mantiene en el cliente. Si bien no distingue entre los usuarios reales cuando se comparte un dispositivo con el mismo inicio de sesión. Diferencia entre usuarios reales cuando se usan distintos inicios de sesión y el sistema operativo admite perfiles. Es el mejor proxy para usuarios únicos cuando no hay ninguna experiencia autenticada. 

    *Ejemplos*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      

**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate     
 
**authUserId**

    string context.user.authId      Max: 100
* 
    Un identificador único que define un usuario autenticado dentro de la aplicación. Se trata del desarrollador proporcionado. La ventaja de un usuario autenticado es que el identificador puede moverse a través de dispositivos dentro de la aplicación y seguir manteniendo su unicidad. 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    La región del almacén de la que procede la aplicación. 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    Un identificador único que define una cuenta dentro de la aplicación. Se trata del desarrollador proporcionado. 

### Métricas personalizadas

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    Indica si la llamada de dependencia remota fue asincrónica 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    El nombre del comando SQL de la dependencia remota, si la dependencia remota es SQL 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    El nombre del tipo de dependencia remota de la dependencia remota 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    El nombre nf de la dependencia remota 

    *Derivación:* se estandariza en &lt;telemetryType.name&gt;.

**remoteDependencyType**

    string remotedependency.remotedependencytype      Max: 100
* 
    El tipo de dependencia remota. Los tipos admitidos son SQL, recursos de AZURE (almacenamiento, cola, etc.) y HTTP. 

**Correcto**

    boolean remotedependency.success      
* 
    Indica si la llamada de dependencia remota fue correcta o errónea. 


## request

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    Un indicador que identifica si hay elementos de telemetría relacionados en función de operation.id 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    Método HTTP usado en la solicitud.   

**id**

    string request.id      Max: 100
* 
    Un identificador único de una solicitud; generado mediante el SDK. Este identificador se puede rellenar a la propiedad de id. de operación para correlacionar los elementos de telemetría resultantes de la misma solicitud. 

**responseCode**

    long request.responsecode      
* 
    El código de respuesta de una solicitud 

**Correcto**

    boolean request.success      
* 
    Indica si la solicitud es correcta. Un código de respuesta de 200s se considera correcto. 

    *Valor predeterminado:* si es null, se establece en true


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    El tiempo transcurrido desde la última visita de este identificador de usuario anónimo. En la primera visita, este valor está vacío. En cada visita posterior, es el tiempo entre las visitas representado en el nivel de detalle de día. Un valor de 3 significa que hn pasado 3 días desde la instancia de sesión anterior hasta esta instancia de sesión 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    El contador de visitas del usuario anónimo. Es un contador incremental de las sesiones históricas totales para este identificador único de usuario anónimo. Cada sesión con este identificador incrementa el contador. Este contador se desactiva si no se ve el identificador de usuario en un plazo de 30 días, momento en el que el contador se restablece a y la siguiente visita del identificador de usuario se considerará un nuevo usuario. 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    El tiempo transcurrido desde la última visita de este identificador de cuenta. En la primera visita, este valor está vacío. En cada visita posterior, es el tiempo entre las visitas representado en el nivel de detalle de día. Un valor de 3 significa que hn pasado 3 días desde la instancia de sesión anterior hasta esta instancia de sesión 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    El contador de visitas del identificador de cuenta autenticado. Es un contador incremental de las sesiones históricas totales para este identificador único de cuenta. Cada sesión con este identificador incrementa el contador. Este contador se desactiva si no se ve el identificador de usuario en un plazo de 30 días, momento en el que el contador se restablece a y la siguiente visita del identificador de usuario se considerará un nuevo usuario. 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    El tiempo transcurrido desde la última visita de este identificador de usuario autenticado. En la primera visita, este valor está vacío. En cada visita posterior, es el tiempo entre las visitas representado en el nivel de detalle de día. Un valor de 3 significa que hn pasado 3 días desde la instancia de sesión anterior hasta esta instancia de sesión 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    El contador de visitas del identificador de usuario autenticado. Es un contador incremental de las sesiones históricas totales para este identificador único de usuario autenticado. Cada sesión con este identificador incrementa el contador. Este contador se desactiva si no se ve el identificador de usuario en un plazo de 30 días, momento en el que el contador se restablece a y la siguiente visita del identificador de usuario se considerará un nuevo usuario. 

**crashCount**

    Long sessionmetric.crashcount      
* 
    El contador de bloqueos que se produjeron durante esta instancia de sesión. 

**duration**

    timespan sessionmetric.duration      
* 
    La duración de una instancia de sesión 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    El primer evento de la sesión. Se obtiene a partir de event.name y está disponible como una segmentación o agregación para las métricas sessionMetric 

    *Derivación:* con origen en event.name

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    La primera dirección URL de la sesión. Se obtiene a partir de urlData.base y está disponible como una segmentación o agregación para las métricas sessionMetric 

    *Derivación:* con origen en &lt;telemetryType&gt;.Url

**eventCount**

    Long sessionmetric.eventcount      
* 
    El contador de eventos que se produjeron durante esta instancia de sesión 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    El contador de excepciones que se produjeron durante esta instancia de sesión 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    El último evento de la sesión. Se obtiene a partir de event.name y está disponible como una segmentación o agregación para las métricas sessionMetric 

    *Derivación:* con origen en event.name

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    La última dirección URL de la sesión. Se obtiene a partir de urlData.base y está disponible como una segmentación o agregación para las métricas sessionMetric 

    *Derivación:* con origen en &lt;telemetryType&gt;.Url

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    El contador de sesiones de devolución que representa este elemento de telemetría sessionMetric. Una sesión de devolución es una sesión que se crea basada en un elemento de telemetría de una sola vista. 

    *Derivación:* si sessionMetric.viewCount + sessionMetric.requestCount = 1, entonces 1; si no, 0

**pageCount**

    Long sessionmetric.pagecount      
* 
    El contador de las vistas que se produjeron durante esta instancia de sesión 

**requestCount**

    Long sessionmetric.requestcount      
* 
    El número de solicitudes que se produjeron durante esta instancia de sesión 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    Un contador de sesiones que representa esta instancia sessionMetric de la telemetría 


## trace

**contexto**

    string trace.context      Max: 100
* 
    El contexto de la aplicación en el momento de la excepción o el seguimiento 

**exception**

    string trace.exception      Max: 10240
* 
    La excepción asociada al elemento de telemetría de seguimiento 

**excerpt**

    string trace.excerpt      Max: 100
* 
    Un mensaje de texto breve de un elemento de telemetría de seguimiento 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    El mensaje de formato para el elemento de telemetría de seguimiento 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    El proveedor de formato para el elemento de telemetría de seguimiento 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    Indica si el elemento de telemetría de seguimiento incluye un seguimiento de la pila 

**level**

    string trace.level      Max: 100
* 
    El nivel del mensaje de seguimiento 

**loggerName**

    string trace.loggername      Max: 100
* 
    El nombre del registrador de seguimiento 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    El nombre corto del registrador 

**message**

    string trace.message      Max: 10240
* 
    El mensaje de texto completo de un elemento de telemetría de seguimiento 

**messageId**

    string trace.messageId      Max: 100
* 
    Un identificador único del elemento de telemetría de seguimiento 

**parameters**

    string trace.parameters      Max: 100
* 
    Estos son los parámetros proporcionados en la grabadora de seguimiento para el elemento de telemetría de seguimiento 

**processId**

    string trace.processId      Max: 100
* 
    El identificador de proceso de la aplicación en la grabación del elemento de telemetría 

**sourceType**

    string trace.sourceType      Max: 100
* 
    Tipo de origen de un elemento de telemetría de seguimiento 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    Un identificador de secuencia que un proveedor de seguimiento puede usar para registrar la secuencia de los elementos de telemetría de seguimiento 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    Un seguimiento de la pila de lo capturado para el elemento de telemetría de seguimiento 

**threadId**

    string trace.threadId      Max: 100
* 
    Threadid de la aplicación en el momento de registrar el elemento de telemetría 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    El marco de pila de usuario para el elemento de telemetría de seguimiento 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    El número de marco de pila de usuario para el elemento de telemetría de seguimiento 


## view

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    La dirección URL de referencia de la vista de página. La dirección URL completa y es compatible con la búsqueda de texto completo y la exportación. Este campo puede tener una cardinalidad muy alta y es un atributo. Se analiza internamente en un conjunto de elementos de datos referralData que puede usarse para las agregaciones en el Explorador de métricas. 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    Una parte de la dirección URL de referencia, excluido el host y los parámetros de consulta. Es el URI raíz. Este valor puede usarse para la segmentación o la agregación. 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    El texto del hashtag de la dirección URL de referencia 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    El host de la dirección URL de referencia. Si la dirección URL es un URI local, se representa como vacío 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    El puerto de la dirección URL de referencia, si se representa en la dirección URL completa. De lo contrario, está vacío. 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    El protocolo (HTTP, FTP, etc.) de la dirección URL de referencia 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL

    *Ejemplos*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    Una matriz de los nombres de los parámetros de consulta de la dirección URL de referencia 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    Una matriz de valores de los parámetros de consulta analizados desde la dirección URL de referringData. 

    *Derivación:* consulte el apéndice para conocer la transformación de la dirección URL



## Consulte también

* [Application Insights](app-insights-overview.md) 
* [Exportación continua](app-insights-export-telemetry.md)
* [Ejemplos de código](app-insights-export-telemetry.md#code-samples)

<!---HONumber=Nov15_HO4-->