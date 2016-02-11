<properties 
	pageTitle="Supervisión de un servicio en la nube | Microsoft Azure" 
	description="Vea cómo supervisar servicios en la nube usando el Portal de Azure clásico." 
	services="cloud-services" 
	documentationCenter="" 
	authors="rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="robb"/>


#Supervisión de servicios en la nube

[AZURE.INCLUDE [declinación de responsabilidades](../../includes/disclaimer.md)]

Puede supervisar las métricas de rendimiento principales de sus servicios en la nube en el Portal de Azure clásico. También es posible configurar el nivel de supervisión a mínimo o detallado para cada rol del servicio, así como personalizar la visualización de la supervisión. Los datos de la supervisión detallada se almacenan en una cuenta de almacenamiento, a la que puede obtener acceso fuera del portal.

En el Portal de Azure clásico, puede configurar muchos parámetros de las visualizaciones de la supervisión. Puede elegir las métricas que desee supervisar en la lista de métricas, en la página **Supervisión**, así como las métricas que desea mostrar en los gráficos de métricas de la página **Supervisión** y del panel.

##Conceptos##

De forma predeterminada, para un servicio en la nube nuevo, se proporciona la supervisión mínima con contadores de rendimiento recopilados del sistema operativo host para las instancias de los roles (máquinas virtuales). Las métricas mínimas se limitan a porcentaje de CPU, datos de entrada, datos de salida, rendimiento de lectura de disco y rendimiento de escritura de disco. Al configurar la supervisión detallada, puede recibir métricas adicionales en función de los datos de rendimiento de las máquinas virtuales (instancias de rol). Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones.

De forma predeterminada, los datos del contador de rendimiento de las instancias de los roles se muestrean y transfieren desde la instancia de rol en intervalos de 3 minutos. Al activar la supervisión detallada, los datos del contador de rendimiento se agregan para cada instancia de rol y entre las instancias de rol de cada rol en intervalos de 5 minutos, 1 hora y 12 horas. Los datos agregados se eliminan después de 10 días.

Después de activar la supervisión detallada, los datos de supervisión agregados se almacenan en tablas en su cuenta de almacenamiento. Para activar la supervisión detallada de un rol, debe configurar una cadena de conexión de diagnósticos que lo vincule a la cuenta de almacenamiento. Puede utilizar cuentas de almacenamiento diferentes para roles diferentes.

Tenga en cuenta que la activación de la supervisión detallada aumentará los costes de almacenamiento relacionados con el almacenamiento de datos, la transferencia de datos y las transacciones de almacenamiento. La supervisión mínima no requiere una cuenta de almacenamiento. Los datos de las métricas que se exponen a un nivel mínimo de supervisión no se almacenan en su cuenta de almacenamiento, incluso si configura la supervisión en un nivel detallado.


##Configuración de la supervisión para los servicios en la nube##

Use los siguientes procedimientos para configurar la supervisión detallada o mínima en el Portal de Azure clásico.

###Antes de empezar###

- Cree una cuenta de almacenamiento para almacenar los datos de supervisión. Puede utilizar cuentas de almacenamiento diferentes para roles diferentes. Para obtener más información, consulte la ayuda de **Cuentas de almacenamiento** o consulte [Creación de una cuenta de almacenamiento](/manage/services/storage/how-to-create-a-storage-account/).

- Active Diagnósticos de Azure en sus roles de servicios en la nube. Consulte [Configuración de Diagnósticos para servicios en la nube](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore).

Asegúrese de que la cadena de conexión de diagnóstico está presente en la configuración de roles. No puede activar la supervisión detallada hasta que habilite Diagnósticos de Azure e incluya una cadena de conexión de diagnóstico en la configuración de roles.

> [AZURE.NOTE]Los proyectos destinados a Azure SDK 2.5 no incluían automáticamente la cadena de conexión de diagnóstico en la plantilla de proyecto. Para estos proyectos, deberá agregar manualmente la cadena de conexión de diagnóstico a la configuración de roles.

**Para agregar manualmente la cadena de conexión de diagnóstico a la configuración de roles**

1. Abra el proyecto Servicio en la nube en Visual Studio.
2. Haga doble clic en el **rol** para abrir el rol diseñador y seleccione la pestaña **Configuración**
3. Busque un ajuste llamado **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Si esta ajuste no está presente, haga clic en el botón **Agregar ajuste** para agregarlo a la configuración y cambie el tipo del ajuste nuevo a **ConnectionString**
5. Establezca el valor de la cadena de conexión haciendo clic en el botón **...**. Se abrirá un cuadro de diálogo en el que podrá seleccionar una cuenta de almacenamiento.

	![Configuración de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

###Para cambiar el nivel de supervisión a detallado o mínimo###

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), abra la página **Configurar** de la implementación del servicio en la nube.

2. En **Nivel**, haga clic en **Detallado** o **Mínimo**.

3. Haga clic en **Guardar**.

Después de activar la supervisión detallada, debería empezar a ver los datos de supervisión en el Portal de Azure clásico en menos de una hora.

Los datos del contador de rendimiento y los datos de supervisión agregados se almacenan en la cuenta de almacenamiento, en tablas, en función del identificador de implementación de los roles.

##Recepción de alertas de métricas de servicios en la nube##

Puede recibir alertas basadas en las métricas de supervisión de los servicios en la nube. En la página de **Servicios de administración** del Portal de Azure clásico, puede crear una regla para desencadenar una alerta cuando la métrica seleccionada alcance el valor que haya especificado. Puede también elegir que se envíe un correo electrónico cuando se desencadene la alerta. Para obtener más información, consulte [Recepción notificaciones de alerta y administración de reglas de alerta en Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

##Adición de métricas a la tabla de métricas##

1. En el [Portal de Azure clásico](http://manage.windowsazure.com/), abra la página **Supervisión** del servicio en la nube.

	La tabla de métricas muestra de forma predeterminada un subconjunto de las métricas disponibles. La ilustración muestra las métricas detalladas predeterminadas de un servicio en la nube, que están limitadas al contador de rendimiento Memoria/MBytes disponibles, con datos agregados en el nivel del rol. Use **Agregar métricas** para seleccionar las métricas agregadas y de nivel del rol adicionales que quiera supervisar en el Portal de Azure clásico.

	![Visualización detallada](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. Para agregar métricas a la tabla de métricas:

	a. Haga clic en **Agregar métricas** para abrir **Elegir métricas**, como se muestra a continuación. La primera métrica disponible se expande para mostrar las opciones que están disponibles. En cada métrica, la opción superior muestra los datos de supervisión agregados de todos los roles. Además, puede elegir los roles individuales de los que desee visualizar los datos.

	![Agregar métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)


	b. Para seleccionar las métricas que desea visualizar:

	- Haga clic en la flecha abajo sobre la métrica para expandir las opciones de supervisión.
	- Active la casilla de verificación de cada opción de supervisión que desee visualizar.

	Puede visualizar hasta 50 métricas en la tabla de métricas.

	> [AZURE.TIP]En la supervisión detallada, la lista de métricas puede contener muchas métricas. Para mostrar una barra de desplazamiento, desplace el mouse sobre el lado derecho del cuadro de diálogo. Para filtrar la lista, haga clic en el icono de búsqueda y escriba en texto en el cuadro de búsqueda como se muestra a continuación.
 
	![Búsqueda de Agregar métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)

3. Después de haber seleccionado las métricas, haga clic en la marca de verificación.

	Las métricas seleccionadas se agregan a la tabla de métricas como se muestra a continuación.

	![supervisar métricas](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. Para eliminar una métrica de la tabla de métricas, haga clic en la métrica para seleccionarla y, a continuación, haga clic en **Eliminar métrica**. (**Eliminar métrica** solo se verá si se ha seleccionado una métrica).

###Para agregar métricas personalizadas a la tabla de métricas###
El nivel de supervisión **Detallado** proporciona una lista de métricas predeterminadas que se pueden supervisar en el portal. Además de estas puede supervisar cualquier métrica personalizada o contador de rendimiento que haya definido la aplicación a través del portal.

En los siguientes pasos se asume que ha activado el nivel de supervisión **Detallado** y que ha configurado la aplicación para que recopile y transfiera los contadores de rendimiento personalizados.

Para mostrar los contadores de rendimiento personalizados en el portal, es preciso actualizar la configuración de wad-control-container:
 
1.	Abra el blob wad-control-container en una cuenta de almacenamiento de diagnósticos. Para ello, se pueden utilizar Visual Studio o cualquier otro explorador de almacenamiento.

	![Explorador de servidores de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Navegue por la ruta de acceso del blob usando el patrón **RoleName/DeploymentId y RoleInstance** para encontrar la configuración de la instancia de rol. 

	![Explorador de almacenamiento de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Descargue el archivo de configuración de la instancia de rol y actualícelo para que incluya cualquier contador de rendimiento personalizado. Por ejemplo, para supervisar *Bytes de escritura en disco/s* en la *unidad C* agregue lo siguiente al nodo **PerformanceCounters\\Subscriptions**

	```xml
	<PerformanceCounterConfiguration>
	<CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
	<SampleRateInSeconds>180</SampleRateInSeconds>
	</PerformanceCounterConfiguration>
	```
4. Guarde los cambios y vuelva a cargar el archivo de configuración en la misma ubicación, sobrescribiendo el archivo existente en el blob.
5. Alterne al modo Detallado de la configuración del Portal de Azure clásico. Si ya estaba en dicho modo, tendrá que alternar a Mínimo y volver a Detallado.
6. El contador de rendimiento personalizado estará disponible en el cuadro de diálogo **Agregar métricas**. 

##Personalización del gráfico de métricas##

1. En la tabla de métricas, seleccione las métricas que desee mostrar en el gráfico de métricas (un máximo de 6). Para seleccionar una métrica, haga clic en la casilla de verificación del lado izquierdo. Para quitar una métrica del gráfico de métricas, desmarque la casilla de verificación en la tabla de métricas.

	A medida que seleccione métricas en la tabla de métricas, estas se agregarán al gráfico de métricas. En un modo de visualización reducido, una lista desplegable **n más** contiene los encabezados de las métricas que no caben en la pantalla.

 
2. Para alternar entre los valores relativos de visualización (solo el valor final de cada métrica) y los valores absolutos (se muestra el eje Y), seleccione Relative o Absolute en la parte superior del gráfico.

	![Relative o Absolute](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. Para cambiar el intervalo de tiempo que se muestra en el gráfico de métricas, seleccione 1 hora, 24 horas o 7 días en la parte superior del gráfico.

	![Periodo de la pantalla de supervisión](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

	En el panel del gráfico de métricas, el método de visualización de métricas es diferente. Hay un conjunto de métricas estándar disponible, y las métricas se agregan o se borran seleccionando el encabezado de la métrica.

###Para personalizar el gráfico de métricas en el panel###

1. Abra el panel del servicio en la nube.

2. Agregue o borre las métricas del gráfico:

	- Para mostrar una nueva métrica, active la casilla de verificación de la métrica en los encabezados del gráfico. En un modo de visualización reducido, haga clic en la flecha inferior sobre ***n*??métricas** para mostrar una métrica que el encabezado del gráfico no puede mostrar.

	- Para eliminar una métrica que se muestra en el gráfico, desmarque la casilla de verificación en su encabezado.

3. Alterne entre las pantallas **Relativo** y **Absoluto**.

4. Elija 1 hora, 24 horas o 7 días para visualizar los datos correspondientes.

##Acceso a los datos de supervisión detallada fuera del Portal de Azure clásico##

Los datos de la supervisión detallada se almacenan en tablas en las cuentas de almacenamiento que ha especificado para cada rol. Para cada implementación de servicios en la nube, se crean seis tablas para el rol. Se crean dos tablas en cada intervalo (5 minutos, 1 hora y 12 horas). Una de estas tablas almacena los agregados a nivel de rol y, la otra, los agregados de las instancias de rol.

Los nombres de tabla tienen el siguiente formato:

	WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

donde:

- *deploymentID* es el GUID asignado a la implementación del servicio en la nube

- *aggregation\_interval* = 5M, 1H o 12H

- agregados a nivel de rol = R

- agregados de las instancias de rol = RI

Por ejemplo, las tablas siguientes almacenarían datos de supervisión detallados agregados en intervalos de 1 hora:

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
 

<!---HONumber=AcomDC_1203_2015-->