<properties
	pageTitle="Supervisión de una cuenta de almacenamiento | Microsoft Azure"
	description="Aprenda a supervisar una cuenta de almacenamiento en Azure usando el Portal de Azure."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="robinsh"/>

# Supervisión de una cuenta de almacenamiento en el Portal de Azure

## Información general

Puede supervisar su cuenta de almacenamiento en el [Portal de Azure](https://portal.azure.com). Al configurar la cuenta de almacenamiento para la supervisión a través del portal, Almacenamiento de Azure usa el [análisis del almacenamiento](http://msdn.microsoft.com/library/azure/hh343270.aspx) para realizar un seguimiento de las métricas de la cuenta y registrar los datos de solicitudes.

> [AZURE.NOTE] Existen costos adicionales asociados con el análisis de los datos de supervisión en el [Portal de Azure](https://portal.azure.com). Para obtener más información, consulte <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">Facturación y análisis de almacenamiento</a>. <br />

> Almacenamiento de archivos de Azure admite actualmente las métricas del Análisis de almacenamiento, pero aún no admite el registro. Puede habilitar las métricas para el Almacenamiento de archivos de Azure a través del [Portal de Azure](https://portal.azure.com).

> Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con el Almacenamiento de Azure, consulte [Supervisión, diagnóstico y solución de problemas de Almacenamiento de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## Procedimientos: Configuración de la supervisión para una cuenta de almacenamiento

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Almacenamiento** y, luego, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Configurar** y desplácese hacia abajo hasta la configuración de **supervisión** para los servicios Blob, Tabla y Cola.

	![OpcionesSupervisión](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. En **supervisión**, configure el nivel de supervisión y la directiva de retención de datos para cada servicio:

	-  Para configurar el nivel de supervisión, seleccione una de las opciones siguientes:

      **Minimal**: recopila métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan a los servicios Blob, Tabla y Cola.

      **Verbose**: además de las métricas mínimas, recopila el mismo conjunto de métricas para cada operación de almacenamiento en la API del Servicio de almacenamiento de Azure. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones.

      **Off**: desactiva la supervisión. Los datos de supervisión existentes permanecen disponibles hasta el final del periodo de retención.

- Para configurar la directiva de retención de datos, en **Retención (en días)**, escriba el número de días que se deben retener los datos, entre 1 y 365. Si no desea configurar una directiva de retención, escriba un cero. Si no existe una directiva de retención, es posible eliminar los datos de supervisión. Recomendamos configurar una directiva de retención basada en el tiempo que desee retener los datos de análisis de almacenamiento de su cuenta, de modo que el sistema pueda eliminar sin coste los datos de análisis antiguos o no usados.

4. Al finalizar la configuración de la supervisión, haga clic en **Guardar**.

Debería empezar a ver los datos de supervisión en el panel y en la página **Supervisión** en el plazo de una hora aproximadamente.

Hasta que no haya configurado la supervisión para una cuenta de almacenamiento, no se recopilarán datos de supervisión y los gráficos de métricas del panel y la página **Supervisión** permanecerán vacíos.

Después de configurar los niveles de supervisión y las directivas de retención, puede elegir las métricas disponibles que desea supervisar en el [Portal de Azure](https://portal.azure.com) y las métricas que desea mostrar en los gráficos de métricas. En cada nivel de supervisión se muestra un conjunto de métricas predeterminado. Puede usar **Agregar métricas** para agregar o borrar métricas de la lista de métricas.

Las métricas se almacenan en la cuenta de almacenamiento en cuatro tablas denominadas $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue y $MetricsCapacityBlob. Para obtener más información, vea [Acerca de las métricas del análisis de almacenamiento](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## Procedimientos: Personalización del panel para la supervisión

En el panel, puede elegir hasta seis métricas para mostrar en el gráfico de métricas de las nueve métricas disponibles. Para cada servicio (Blob, Tabla y Cola), existen métricas de disponibilidad, porcentajes de éxito y total de solicitudes. Las métricas disponibles en el panel son las mismas para la supervisión mínima y detallada.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Almacenamiento** y, luego, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Para cambiar las métricas que se muestran en el gráfico, realice una de las siguientes acciones:

	- Para agregar una nueva métrica al gráfico, haga clic en la casilla de verificación de color junto a su encabezado en la tabla debajo del gráfico.

	- Para ocultar una métrica que se muestra en el gráfico, desmarque la casilla de verificación de color junto a su encabezado.

		![Opción n more de supervisión](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. De forma predeterminada, el gráfico muestra tendencias, donde aparece solo el valor actual de cada métrica (la opción **Relativo** situada en la parte superior del gráfico). Para mostrar un eje Y con el fin de visualizar los valores absolutos, seleccione **Absoluto**.

4. Para cambiar el intervalo de tiempo que se muestra en el gráfico de métricas, seleccione 6 horas, 24 horas o 7 días en la parte superior del gráfico.


## Procedimientos: Personalización de la página Supervisar

En la página **Supervisar**, puede visualizar el conjunto de métricas completo para su cuenta de almacenamiento.

- Si su cuenta de almacenamiento se ha configurado para tener una supervisión mínima, se agregan métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito a los servicios Blob, Tabla y Cola.

- Si su cuenta de almacenamiento se ha configurado para tener una supervisión detallada, las métricas están disponibles con un mayor nivel de resolución de las operaciones de almacenamiento individuales, además de los detalles agregados en el nivel de servicio.

Utilice los siguientes procedimientos para elegir las métricas de almacenamiento que desea visualizar en los gráficos y la tabla de métricas que se muestran en la página **Supervisar**. Estas configuraciones no afectan a la recopilación, la incorporación y el almacenamiento de los datos de supervisión en la cuenta de almacenamiento.

## Adición de métricas a la tabla de métricas


1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Almacenamiento** y, luego, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Supervisar**.

	Se abre la página **Supervisar**. La tabla de métricas muestra de forma predeterminada un subconjunto de las métricas que están disponibles para su supervisión. La ilustración muestra la visualización predeterminada de la página Monitor para una cuenta de almacenamiento que tenga configurada una supervisión detallada para los tres servicios. Use **Agregar métricas** para seleccionar las métricas que desea supervisar de entre todas las disponibles.

	![Visualización de supervisión detallada](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

	> [AZURE.NOTE] Tenga en cuenta los costes al seleccionar las métricas. Existen costes de transacción y de salida asociados a la actualización de las visualizaciones de la supervisión. Para obtener más información, consulte[ Facturación y análisis de almacenamiento](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Haga clic en **Agregar métricas**.

	Las métricas agregadas que están disponibles en el modo de supervisión mínimo están situadas en la parte superior de la lista. Si la casilla de verificación está seleccionada, la métrica se muestra en la lista de métricas.

	![Visualización inicial de la opción para agregar métricas](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Desplace el mouse sobre el lado derecho del cuadro de diálogo para mostrar una barra de desplazamiento que podrá arrastrar para visualizar métricas adicionales.

	![Barra de desplazamiento de la opción para agregar métricas](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Haga clic en la flecha abajo junto a una métrica para expandir una lista de operaciones que la métrica puede incluir. Seleccione todas las operaciones que desee visualizar en la tabla de métricas del [Portal de Azure](https://portal.azure.com).

	En la siguiente ilustración, la métrica AUTHORIZATION ERROR PERCENTAGE se ha expandido.

	![Expandir y contraer](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Después de seleccionar métricas para todos los servicios, haga clic en la marca de verificación para actualizar la configuración de la supervisión. Las métricas seleccionadas se agregan a la tabla de métricas.

7. Para eliminar una métrica de la tabla, haga clic en la métrica para seleccionarla y, luego, haga clic en **Eliminar métrica**.

	![Eliminar métrica](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## Procedimientos: Personalización del gráfico de métricas en la página Supervisar

1. En la página **Supervisar** de la cuenta de almacenamiento, en la tabla de métricas, seleccione hasta 6 valores para mostrarlas en el gráfico de métricas. Para seleccionar una métrica, haga clic en la casilla de verificación del lado izquierdo. Para borrar una métrica del gráfico, desactive la casilla de verificación.

2. Para cambiar entre los valores relativos (solo se visualiza el último valor) y los valores absolutos (se visualiza el eje Y) del gráfico, seleccione **Relativo** o **Absoluto** en la parte superior del gráfico.

3.	Para cambiar el intervalo de tiempo que se muestra en el gráfico de métricas, seleccione **6 horas**, **24 horas** o **7 días** en la parte superior del gráfico.



## Procedimiento: Configuración del registro

Para cada uno de los servicios de almacenamiento disponibles en su cuenta de almacenamiento (Blob, Tabla y Cola), puede guardar registros de diagnóstico para solicitudes de lectura, solicitudes de escritura y solicitudes de eliminación, así como configurar la directiva de retención de datos para cada uno de los servicios.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Almacenamiento** y, luego, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Configurar** y use la flecha abajo del teclado para desplazarse hacia abajo hasta **registro**.

	![Registro en Almacenamiento](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Para cada servicio (Blob, Tabla y Cola), configure lo siguiente:

	- Los tipos de solicitudes para registrar: solicitudes de lectura, solicitudes de escritura y solicitudes de eliminación.

	- El número de días para retener los datos registrados. Escriba un cero si no desea configurar una directiva de retención. Si no configura una directiva de retención, puede eliminar los registros si lo desea.

4. Haga clic en **Guardar**.

Los registros de diagnóstico se guardan en un contenedor de blobs denominado $logs en su cuenta de almacenamiento. Para obtener más información acerca del acceso al contenedor $logs, consulte [Acerca del registro del análisis de almacenamiento](http://msdn.microsoft.com/library/azure/hh343262.aspx)

<!---HONumber=AcomDC_0224_2016-->