<properties
	pageTitle="Supervisión de aplicaciones web en el Servicio de aplicaciones de Azure"
	description="Aprenda a supervisar Aplicaciones web en el Servicio de aplicaciones de Azure usando el Portal de administración."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="byvinyal"/>

#<a name="howtomonitor"></a>Supervisión de Aplicaciones web en el Servicio de aplicaciones de Azure

[Aplicaciones web en el Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) proporcionan una funcionalidad de supervisión para los planes Estándar y Premium del Servicio de aplicaciones a través de la página de administración Supervisar. La página de administración de supervisión brinda estadísticas del rendimiento de una aplicación web, tal como se describe a continuación.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##Directiva de retención de métricas

>[AZURE.NOTE] La directiva de retención de las métricas de la aplicación varía según la granularidad.

- Las métricas de granularidad de **minuto** se conservan durante **24 horas**
- Las métricas de granularidad de **hora** se conservan durante **7 días**
- Las métricas de granularidad de **día** se conservan durante **30 días**

##<a name="websitemetrics"></a>Incorporación de métricas de aplicaciones web

1. En el [Portal clásico](https://manage.windowsazure.com), en la página de la aplicación web, haga clic en la pestaña **Supervisar** para mostrar la página de administración **Supervisar**. De manera predeterminada, el gráfico de la página **Supervisar** muestra las mismas métricas que el gráfico de la página **Panel**.

2. Si desea ver métricas adicionales para la aplicación web, haga clic en **Agregar métricas** en la parte inferior de la página para mostrar el cuadro de diálogo **Elegir métricas**.

3. Haga clic para seleccionar métricas adicionales que se mostrarán en la página **Supervisar**.

4. Después de seleccionar las métricas que desea agregar a la página **Supervisar**, haga clic en la marca de verificación de la parte inferior.

5. Después de agregar métricas a la página **Supervisar**, haga clic para habilitar/deshabilitar la casilla de opción junto a cada métrica para agregar/quitar la métrica del gráfico en la parte superior de la página.

6. Para quitar métricas de la página **Supervisar**, seleccione la métrica que desea quitar y, a continuación, haga clic en el icono **Eliminar métrica** en la parte inferior de la página.



##<a name="howtoreceivealerts"></a>Recepción de alertas de métricas de aplicaciones web

En el modo de aplicación web **Estándar** puede recibir alertas basadas en las métricas de supervisión de su aplicación web. La característica de alerta requiere primero configurar un extremo web para supervisión, lo que puede hacerse en la sección **Supervisión** de la página **Configurar**. También puede elegir tener el correo electrónico enviado cuando una métrica seleccionada alcance el valor que especifique. Para obtener más información, consulte [Recepción notificaciones de alerta y administración de reglas de alerta en Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

##<a name="howtoviewusage"></a>Visualización de cuotas de uso para una aplicación web

Las aplicaciones web pueden configurarse para ejecutarse en modo **Compartido** o **Estándar** en la página de administración **Escala** de la aplicación web en el [Portal clásico](https://manage.windowsazure.com). Cada suscripción a Azure tiene acceso a un conjunto de recursos que tienen como finalidad la ejecución de hasta 100 aplicaciones web por región en el modo **Compartido**. El grupo de recursos disponibles para cada suscripción de aplicaciones web con este fin es compartido por otras aplicaciones web en la misma región geográfica que estén configuradas para ejecutarse en modo **Compartido**. Como estos recursos son compartidos para que los usen otras aplicaciones web, todas las suscripciones tienen límites en el uso que hacen de estos recursos. Los límites que se aplican al uso que una suscripción hace de estos recursos se expresan como cuotas de uso enumeradas en la sección de información general de uso de la página de administración **Panel** de cada aplicación web.

>[AZURE.NOTE] Cuando una aplicación web está configurada para ejecutarse en modo **Estándar**, se le asignan recursos dedicados equivalentes a los tamaños de máquina virtual **Pequeña** (valor predeterminado), **Mediana** o **Grande** en la tabla de [Máquina virtual y tamaños de servicio en la nube de Azure][vmsizes]. No hay límites en los recursos que puede utilizar una suscripción para ejecutar aplicaciones web en modo **Estándar**. Sin embargo, la cantidad de aplicaciones web en modo **Estándar** que se pueden crear por región es 500.

### Visualización de las cuotas de uso para aplicaciones web configuradas para el modo Compartido ###
Para determinar el alcance del impacto de una aplicación web en las cuotas de uso de los recursos, siga estos pasos:

1. Abra la página de administración **Panel** de la aplicación web en el [Portal clásico](https://manage.windowsazure.com).
2. En la sección **información general del uso** se muestran las cuotas de uso del plan de [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) correspondiente, que es un subconjunto de:
	-	**Datos de salida**, **Tiempo de CPU** y **Memoria**: cuando se supera la cuota, Azure detiene la aplicación web para el resto del intervalo de cuota actual. Azure iniciará la aplicación web al comienzo del siguiente intervalo de cuota.
	-	**Almacenamiento del sistema de archivos**: cuando se alcanza la cuota, el almacenamiento del sistema de archivos permanece accesible para las operaciones de lectura, pero se bloquean todas las operaciones de escritura, incluidas las necesarias para el funcionamiento normal de la aplicación web. Las operaciones de escritura se reanudarán si se reduce el uso del archivo o si la aplicación web se transfiere a un plan de Servicio de aplicaciones con una cuota mayor.
	-	**Recursos vinculados**: también se muestran aquí las cuotas de todos los recursos vinculados de la aplicación web, como la base de datos o el almacenamiento.

	Algunas cuotas pueden aplicarse por cada plan de hospedaje web, mientras que otras pueden aplicarse por cada sitio. Para obtener información detallada sobre las cuotas de uso de cada plan de hospedaje web, consulte [Límites de sitios web](azure-subscription-service-limits.md#websiteslimits).

##<a name="resourceusage"></a> Evitar exceder las cuotas

Las cuotas no están relacionadas con el rendimiento ni con el coste, sino que se trata de la forma en que Azure rige el uso de recursos en un entorno multiempresa al prevenir que los inquilinos realicen un uso excesivo de los recursos compartidos. Puesto que superar las cuotas supone tiempo de inactividad o una reducción de la funcionalidad de la aplicación web, tenga en cuenta lo siguiente si desea mantener su sitio en funcionamiento cuando esté a punto de superar las cuotas:

- Transfiera las aplicaciones web a un plan de Servicio de aplicaciones de nivel superior para beneficiarse de cuotas más altas. Por ejemplo, la única cuota para los planes **Básico** y **Estándar** es el almacenamiento del sistema de archivos.
- A medida que aumenta el número de instancias de una aplicación web, también aumenta la probabilidad de que se superen las cuotas de recursos compartidos. Si resulta conveniente, plantéese volver a escalar instancias adicionales de una aplicación web cuando se excedan las cuotas de recursos compartidos.

##<a name="howtoconfigdiagnostics"></a>Configuración de registros de diagnóstico y descarga para una aplicación web

Los diagnósticos se habilitan en la pestaña **Configurar** de la aplicación web en el [Portal clásico](https://manage.windowsazure.com). Existen dos tipos de diagnóstico: los **diagnósticos de la aplicación** y los **diagnósticos del sitio**.

#### Diagnósticos de la aplicación ####

La sección de **diagnósticos de la aplicación** en la página de administración **Configurar** controla el registro de la información que genera la aplicación, lo que resulta útil cuando se registran eventos que se producen dentro de una aplicación. Por ejemplo, cuando se produce un error en su aplicación, es posible que desee presentar al usuario un error fácil de comprender mientras que se escribe una información más detallada del error en el registro para un análisis posterior.

Puede habilitar o deshabilitar los siguientes diagnósticos de aplicación:

- **Registro de la aplicación (Sistema de archivos)**: activa el registro de la información generada por la aplicación. El campo **Nivel de registro** determina si se registra información en el nivel de error, advertencia o información. También puede seleccionar Verbose, que registrará toda la información generada por la aplicación.

	Los registros que se generan a partir de esta configuración se almacenan en el sistema de archivos de la aplicación web y se pueden descargar mediante los pasos de la sección **Descarga de archivos de registro para una aplicación web** que aparece a continuación.

- **Registro de la aplicación (Almacenamiento de tablas)**: activa el registro de la información generada por la aplicación, de manera similar a la opción Registro de la aplicación (Sistema de archivos). Sin embargo, la información de registro se almacena en una cuenta de almacenamiento de Azure en una tabla.

	Para especificar la tabla y la cuenta de almacenamiento de Azure, elija **Activado**, seleccione el **Nivel de registro** y, a continuación, elija **Administrar almacenamiento de tablas**. Especifique la tabla y la cuenta de almacenamiento que se utilizará o cree una tabla nueva.

	Puede tener acceso a la información de registro almacenada en la tabla a través de un cliente de almacenamiento de Azure.

- **Registro de la aplicación (Almacenamiento de blobs)**: activa el registro de la información que genera la aplicación, de manera similar a la opción Registro de la aplicación (Almacenamiento de tablas). Sin embargo, la información de registro se almacena en una cuenta de almacenamiento de Azure en un blob.

	Para especificar el blob y la cuenta de almacenamiento de Azure, elija **Activado**, seleccione el **Nivel de registro** y, a continuación, elija **Administrar almacenamiento de blobs**. Especifique la cuenta de almacenamiento, el contenedor de blobs y el nombre de blob que se utilizará o cree un contenedor y un blob nuevos.

Para obtener más información acerca de las cuentas de almacenamiento de Azure, consulte [Administración de cuentas de almacenamiento](/manage/services/storage/how-to-manage-a-storage-account/).

> [AZURE.NOTE] El registro de aplicaciones en almacenamiento de tablas o blobs solo es compatible con aplicaciones .NET.

Como el registro de aplicaciones en almacenamiento requiere utilizar un cliente de almacenamiento para ver los datos de registro, resulta más útil cuando planea utilizar un servicio o aplicación que comprenda cómo leer y procesar los datos directamente desde el almacenamiento de tablas o blobs de Azure. El registro en el sistema de archivos genera archivos que se pueden descargar en el equipo local a través de FTP u otras utilidades, según se describe más adelante en esta sección.

**Diagnósticos de la aplicación (Sistema de archivos)**, **Diagnósticos de la aplicación (Almacenamiento de tablas)** y **Diagnósticos de la aplicación (Almacenamiento de blobs)** se pueden habilitar al mismo tiempo y pueden tener configuraciones individuales en el nivel de registro. Por ejemplo, es posible que desee registrar errores y advertencias en el almacenamiento como una solución de registro a largo plazo, mientras que se habilita el registro de sistema de archivos con un nivel de detalle después de instrumentar el código de la aplicación para solucionar un problema.

Los diagnósticos también se pueden habilitar desde Azure PowerShell con el cmdlet **Set-AzureWebsite**. Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

> [AZURE.NOTE] El registro de aplicaciones se basa en la información de registro que genera su aplicación. El método usado para generar información de registro, así como también el formato de la información, es específico para el lenguaje en que está escrita la aplicación. Si desea información específica para el lenguaje sobre el uso del registro de aplicaciones, consulte los siguientes artículos:
>
> - **.NET**: [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
> - **Node.js**: [Depuración de una aplicación Node.js en Sitios web Azure](web-sites-nodejs-debug.md)
>
> El registro de aplicaciones en almacenamiento de tablas o blobs solo es compatible con aplicaciones .NET.

#### Diagnósticos de sitios ####

La sección **Diagnósticos del sitio** de la página de administración **Configurar** controla el registro que lleva a cabo el servidor web, como el registro de solicitudes web, los errores al atender páginas y el tiempo que se tardó en atender una página. Puede habilitar o deshabilitar las siguientes opciones:

- **Registro del servidor web**: activa el registro de servidores web para guardar registros de aplicaciones web que utilicen el formato de archivo de registro W3C extendido. El registro de servidores web genera un registro de todas las solicitudes entrantes a su aplicación web, que contiene información como la dirección IP del cliente, el URI solicitado, el código de estado HTTP de la respuesta y la cadena de agente de usuario del cliente. Puede guardar los registros en una cuenta de almacenamiento de Azure o en el sistema de archivos.

 Para guardar los registros de servidores web en una cuenta de almacenamiento de Azure, elija **Almacenamiento** y, a continuación, **Administrar almacenamiento** para especificar un contenedor de blobs de Azure donde se conservarán los registros. Para obtener más información acerca de las cuentas de almacenamiento de Azure, consulte [Administración de cuentas de almacenamiento](/manage/services/storage/how-to-manage-a-storage-account/).

   Para guardar registros de servidores web en el sistema de archivos, elija **Sistema de archivos**. Con esto se habilitará la casilla **Cuota**, en la que puede definir la cantidad máxima de espacio en disco para los archivos de registro. El tamaño mínimo es de 25 MB, mientras que el máximo es de 100 MB. El tamaño predeterminado es de 35 MB.

 De manera predeterminada, nunca se eliminan los registros de servidores web. Para especificar un período después del cual los registros se eliminarán automáticamente, seleccione **Establecer retención** y escriba el número de días durante los cuales se conservarán los registros en el cuadro **Período de retención**. Esta configuración está disponible para las opciones de almacenamiento de Azure y del sistema de archivos.

- **Mensajes de error detallados**: activa un registro detallado de los errores para registrar información adicional acerca de los errores HTTP (códigos de estado mayores que 400).

- **Error del seguimiento de solicitudes**: activa el seguimiento de solicitudes con error para capturar información para solicitudes de clientes con error, como un código de estado HTTP de la serie 400. El seguimiento de solicitudes con error genera un documento XML que contiene un seguimiento de los módulos por los que la solicitud ha pasado en IIS, los detalles devueltos por el módulo y la hora en que se invocó el módulo. Esta información se puede utilizar para aislar el componente en que se produjo el error.


Después de habilitar los diagnósticos para una aplicación web, haga clic en el icono **Guardar** en la parte inferior de la página de administración **Configurar** para aplicar las opciones que definió.

> [AZURE.IMPORTANT] Mensajes de error detallados y Seguimiento de solicitudes con error plantean exigencias importantes sobre una aplicación web. Recomendamos desactivar estas características una vez que haya reproducido los problemas que se están solucionando.

### Configuración avanzada ###

Es posible seguir modificando los diagnósticos si agrega pares clave-valor a la sección **Configuración de aplicaciones** de la página de administración **Configurar**. Se pueden configurar las siguientes opciones en **Configuración de aplicaciones**:

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- La ubicación en que se guardarán los registros de aplicaciones, en relación con la raíz web.

- Valor predeterminado: ..\..\LogFiles\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- El tamaño máximo de búfer que se utilizará cuando se capturen registros de aplicación. La información inicialmente se escribe en el búfer antes de vaciarla al archivo o el almacenamiento. Si se escribe información nueva en el búfer antes de poder vaciarla, es posible que pierda la información anteriormente registrada. Si la aplicación genera grandes ráfagas de información de registro, considere aumentar el tamaño del búfer.

- Valor predeterminado: 10 MB

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- El tamaño máximo de la carpeta de **aplicaciones**, en la que se almacenan los diagnósticos de la aplicación escritos en el archivo.

- Valore predeterminado: 1 MB

###Descarga de archivos de registro para una aplicación web

Los archivos de registro se pueden descargar mediante el uso de FTP, Azure PowerShell o la CLI de Azure.

**FTP**

1. Abra la página de administración **Panel** de la aplicación web en el [Portal clásico](https://manage.windowsazure.com) y tome nota del sitio FTP que aparece en **Registros de diagnóstico** y la cuenta que aparece en **Usuario de implementación**. El sitio FTP es donde se ubican los archivos de registro y la cuenta que aparece bajo Deployment User se utiliza para autenticarse en el sitio FTP.
2. Si todavía no ha creado credenciales de implementación, la cuenta que aparece en **Usuario de implementación** se muestra como **No configurado**. En este caso, debe crear credenciales de implementación tal como se describen en la sección Reset Deployment Credentials del Panel, porque estas credenciales se deben usar para autenticarse en el sitio de FTP donde se almacenan los archivos de registro. Azure no es compatible con la autenticación en este sitio FTP con las credenciales de Live ID.
3. Considere utilizar un cliente FTP, como [FileZilla][fzilla], para conectarse al sitio FTP. Con un cliente FTP es más fácil especificar las credenciales y visualizar las carpetas en un sitio FTP de lo que normalmente sería posible con un explorador.
4. Copie los archivos de registro desde el sitio FTP a su equipo local.

**Azure PowerShell**

1. En la **pantalla de inicio** o en el **menú Inicio**, busque **Azure PowerShell**. Haga clic con el botón secundario en la entrada **Azure PowerShell** y seleccione **Ejecutar como administrador**.

	> [AZURE.NOTE] Si **Azure PowerShell** no está instalado, consulte [Introducción a los cmdlets de Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) para obtener información sobre la instalación y configuración.

2. Desde el símbolo del sistema de Azure PowerShell, utilice el siguiente comando para descargar los archivos de registro:

		Save-AzureWebSiteLog -Name webappname

	Con esto se descargarán los archivos de registro para la aplicación web especificada en **webappname** y se guardarán en un archivo **log.zip** en el directorio actual.

	También puede ver una secuencia en vivo de eventos de registro a través del siguiente comando:

		Get-AzureWebSiteLog -Name webappname -Tail

	Con esto aparecerá información de registros en el símbolo del sistema de Azure PowerShell a medida que se produzcan.

**CLI de Azure**

Abra una nueva sesión de Terminal, sesión Bash, PowerShell o símbolo del sistema y utilice el siguiente comando para descargar los archivos de registro:

	azure site log download webappname

Con esto se descargarán los archivos de registro para la aplicación web especificada en **webappname** y se guardarán en un archivo **log.zip** en el directorio actual.

También puede ver una secuencia en vivo de eventos de registro a través del siguiente comando:

	azure site log tail webappname

Con esto aparecerá información de registros en la sesión de Terminal, sesión Bash, PowerShell o símbolo del sistema desde donde se ejecuta el comando.

> [AZURE.NOTE] Si el comando **azure** no está instalado, consulte [Cómo usar la CLI de Azure](../virtual-machines-command-line-tools.md) para obtener información sobre la instalación y la configuración.

### Lectura de archivos de registro ###

Los archivos de registro que se generan después de habilitar el registro o el seguimiento de una aplicación web varían según el nivel de registro/seguimiento definido en la página de administración Configurar para la aplicación web. A continuación aparece la ubicación de los archivos de registro y la manera en que se pueden analizar:

**Tipo de archivo de registro: Registro de aplicaciones**

- Ubicación /LogFiles/Application/. Esta carpeta contiene uno o varios archivos de texto con información generada por el registro de aplicaciones. La información registrada incluye la fecha y la hora, el identificador del proceso (PID) de la aplicación y el valor que produce la instrumentación de la aplicación.

- Lea los archivos con: un editor de texto o un analizador que comprenda los valores que genera su aplicación

**Tipo de archivo de registro: Seguimiento de solicitudes con error**

- Ubicación: /LogFiles/W3SVC#########/. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio de los archivos XML, porque el archivo XSL proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML cuando se visualizan en Internet Explorer.

- Lea los archivos con: Internet Explorer

**Tipo de archivo de registro: Registro detallado de errores**

- Ubicación: /LogFiles/DetailedErrors/. La carpeta /LogFiles/DetailedErrors/ contiene uno o más archivos .htm que proporcionan una amplia información para cualquier error HTTP que se haya generado.

- Lea los archivos con: explorador web

Los archivos .htm incluyen las siguientes opciones:

- **Información detallada del error**: incluye información sobre el error como <em>módulo</em>, <em>controlador</em>, <em>error de código</em> y <em>Dirección URL solicitada</em>.

- **Causas más probables**: muestra una lista de varias causas posibles del error.

- **Acciones que puede intentar**: muestra una lista de posibles soluciones para resolver el problema indicado por el error.

- **Vínculos y más información**: proporciona información resumida adicional acerca del error y también puede incluir vínculos a otros recursos, como artículos de Microsoft Knowledge Base.

**Tipo de archivo de registro: Registro del servidor web**

- Ubicación: /LogFiles/http/RawLogs. Para la información almacenada en los archivos se utiliza el [formato de registro extendido W3C](http://go.microsoft.com/fwlink/?LinkID=90561). Las aplicaciones web Azure no utilizan los campos s-computername, s-ip y cs-version.

- Lea los archivos con: Analizador de registro. Se usa para analizar y consultar archivos de registro de IIS. El Analizador de registro 2.2 está disponible en el Centro de descarga de Microsoft en <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a>Supervisión del estado de extremo web

Esta característica, disponible en el modo **Estándar**, permite supervisar hasta dos extremos desde hasta tres ubicaciones geográficas.

La supervisión de extremo configura pruebas web desde ubicaciones distribuidas geográficamente que prueban el tiempo de respuesta y el tiempo activo de direcciones URL web. La prueba realiza una operación HTTP Get en la dirección URL web para determinar el tiempo de respuesta y el tiempo activo desde cada ubicación. Cada ubicación configurada ejecuta una prueba cada cinco minutos.

El tiempo activo se supervisa a través de códigos de respuesta de HTTP y el tiempo de respuesta se mide en milisegundos. Se considera que el tiempo de actividad es total cuando el tiempo de respuesta es inferior a 30 segundos y el código de estado HTTP es inferior a 400. Se considera que el tiempo de actividad es del 0 % cuando el tiempo de respuesta es superior a 30 segundos o el código de estado HTTP es superior a 400.

Después de configurar la supervisión de extremo, puede obtener detalles sobre los extremos individuales para ver el estado del tiempo activo y el tiempo de respuesta sobre el intervalo de supervisión desde cada una de las ubicaciones de prueba. También puede configurar una regla de alerta cuando el extremo tarda mucho en responder, por ejemplo.

**Para configurar la supervisión de extremos:**

1.	Abra **Aplicaciones web**. Haga clic en el nombre de la aplicación web que desea configurar.
2.	Haga clic en la pestaña **Configurar**.
3.     Vaya a la sección **Supervisión** para especificar la configuración de extremo.
4.	Escriba un nombre para el extremo.
5.	Escriba la dirección URL de una parte de la aplicación web que desea supervisar. Por ejemplo, [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive).
6.	Seleccione una o más ubicaciones geográficas en la lista.
7.	De manera opcional, repita los pasos anteriores para crear un segundo extremo.
8.	Haga clic en **Guardar**. Es posible que los datos de supervisión del extremo web tarden cierto tiempo en estar disponibles en las pestañas **Panel** y **Supervisar**.

Para crear una regla de correo electrónico, realice las siguientes tareas:

9.	En la barra del servicio, situada a la izquierda, haga clic en **Servicios de administración**.
10.	Haga clic en **Agregar regla** en la parte inferior.
11.	En **Tipo de servicio**, seleccione **Aplicación web** y, a continuación, seleccione la aplicación web para la que configuró anteriormente la supervisión de extremos. Haga clic en **Siguiente**.
12.	En **Métrica**, ahora puede seleccionar métricas adicionales para el extremo configurado. Por ejemplo: **Tiempo de respuesta (página principal/EE. UU.: IL-Chicago)**. Seleccione la métrica Tiempo de respuesta y escriba 3 en **Valor de umbral** para especificar un umbral de 3 segundos.
13.	Seleccione **Envíe un correo electrónico al administrador y a los coadministradores del servicio**. Haga clic en **Completo**.

	Azure ahora supervisará de forma activa el extremo y enviará una alerta por correo electrónico si tarda más de 3 segundos en responder.

Vea el siguiente vídeo para obtener más información sobre la supervisión de extremos de aplicaciones web:

- [Scott Guthrie presenta Sitios web Azure y configura la supervisión de extremos](/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Mantener activos Sitios web de Azure y supervisar extremos, con Stefan Schackow (en inglés).](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obtener una guía del cambio del portal de Azure al portal de vista previa de Azure, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715).

[fzilla]: http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]: http://go.microsoft.com/fwlink/?LinkID=309169
 

<!---HONumber=AcomDC_0128_2016-->