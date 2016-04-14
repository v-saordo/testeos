<properties 
	pageTitle="Supervisión y administración de canalizaciones de la Factoría de datos de Azure" 
	description="Obtenga información sobre cómo usar la Aplicación de supervisión y administración para supervisar y administrar factorías de datos y canalizaciones de Azure" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2016" 
	ms.author="spelluru"/>

# Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración
> [AZURE.SELECTOR]
- [Using Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Using Monitoring and Management App](data-factory-monitor-manage-app.md)

En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante la **Aplicación de supervisión y administración**. También se ofrece información sobre cómo crear alertas y recibir notificaciones cuando se produzcan errores al usar la aplicación.
      
## Iniciar la Aplicación de supervisión y administración 
Para iniciar la Aplicación de supervisión y administración, haga clic en el icono **Aplicación de supervisión** en la hoja **DATA FACTORY** de su factoría de datos.

![Icono de supervisión de la página principal de Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

La Aplicación de supervisión y administración debería iniciarse en una pestaña o ventana independiente.

![Aplicación de supervisión y administración](./media/data-factory-monitor-manage-app/AppLaunched.png)

## Descripción de la Aplicación de supervisión y administración
Hay tres pestañas (**Explorador de recursos**, **Vistas de supervisión** y **Alertas**) a la izquierda, y la primera pestaña (Explorador de recursos) está seleccionada de forma predeterminada.

### Explorador de recursos
En el panel izquierdo se encuentra la **vista de árbol** del Explorador de recursos; en el panel central se encuentran la **vista de diagrama** en la parte superior y la lista de **Ventanas de actividad** en la parte inferior, y en el panel derecho se encuentran las pestañas **Propiedades**/**Explorador de ventanas de actividad**.

Puede ver todos los recursos (canalizaciones, conjuntos de datos, servicios vinculados) de la factoría de datos en una vista de árbol. Al seleccionar un objeto en el Explorador de recursos, observará lo siguiente:

- La entidad de Data Factory asociada se resaltará en la vista de diagrama.
- Las ventanas de actividad asociadas (haga clic [aquí](data-factory-scheduling-and-execution.md) para obtener información sobre las ventanas de actividad) se resaltarán en la lista Ventanas de actividad en la parte inferior.  
- Las propiedades del objeto seleccionado aparecen en la ventana Propiedades, en el panel derecho. 

![Explorador de recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte el artículo [Programación y ejecución](data-factory-scheduling-and-execution.md) para obtener información conceptual detallada sobre la ventana de actividad.

### Vista de diagrama
La Vista de diagrama de una factoría de datos ofrece un panel único para supervisar y administrar la factoría de datos y sus recursos. Cuando seleccione una entidad de Data Factory (conjunto de datos o canalización) en la vista de diagrama, observará lo siguiente:
 
- La entidad de Data Factory se selecciona en la vista de árbol.
- Las ventanas de actividad asociadas se resaltan en la lista Ventanas de actividad.
- Las propiedades del objeto seleccionado aparecen en la ventana Propiedades.

Si la canalización está habilitada (es decir, no está en estado de pausa), se indicará con una línea verde tal como se muestra a continuación.

![Canalización en ejecución](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Observe que hay tres botones de comando para la canalización en la vista de diagrama. Puede usar el segundo botón para pausar la canalización. Esto no finalizará las actividades que se están ejecutando, sino que continuarán hasta su finalización. El tercer botón pausa la canalización y finaliza las actividades que se están ejecutando. El primer botón reanuda la canalización; es decir, anula la pausa. Cuando pause la canalización, observará que el icono de canalización cambia de color tal como se muestra a continuación.

![Pausar o reanudar en el icono](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

Puede seleccionar al mismo tiempo dos o más canalizaciones (mediante la tecla CTRL) y usar los botones de la barra de comandos para pausar o reanudar varias canalizaciones a la vez.

![Pausar o reanudar en la barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

Para ver todas las actividades de la canalización, haga clic con el botón derecho en el icono de canalización y haga clic en **Abrir canalización**.

![Menú Abrir canalización](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

En la vista de canalización que se abre, verá todas las actividades de la canalización. En este ejemplo, solamente hay una actividad: la actividad de copia. Para volver a la vista anterior, haga clic en el nombre de la factoría de datos en el menú de la ruta de navegación en la parte superior.

![Canalización abierta](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

En la vista de canalización abierta o cerrada, al hacer clic en un conjunto de datos de salida, verá que emerge la notificación Ventanas de actividad del conjunto de datos de salida sobre el que mueva el mouse.

![Notificación emergente de Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Puede hacer clic en una ventana de actividad para ver los detalles en la ventana **Propiedad** en el panel derecho.

![Propiedades de la ventana de actividad](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

En el panel derecho, cambie a la pestaña **Explorador de ventanas de actividad** para ver más detalles.

![Explorador de ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Puede ver las ventanas de actividad en tres lugares:

- La notificación emergente Ventanas de actividad en la vista de diagrama (panel central).
- El Explorador de ventanas de actividad en el panel derecho.
- La lista Ventanas de actividad en el panel inferior.

En la notificación emergente Ventanas de actividad y en el Explorador de ventanas de actividad, puede desplazarse a la semana anterior y la semana siguiente usando las flechas izquierda y derecha.

![Flechas izquierda/derecha del Explorador de ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

En la parte inferior de la vista de diagrama, verá que hay botones para acercar, alejar, ajustar al tamaño, ajustar al 100 % y bloquear diseño (impide que mueva accidentalmente tablas y canalizaciones en la vista de diagrama). El botón para bloquear el diseño está activado de forma predeterminada. Puede desactivarlo y mover las entidades por el diagrama. Si lo desactiva, puede usar el último botón para colocar automáticamente las tablas y las canalizaciones. También puede acercar o alejar usando la rueda del mouse.

![Comandos de zoom de la vista de diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### Lista Ventanas de actividad
La lista Ventanas de actividad de la parte inferior del panel central muestra todas las ventanas de actividad del conjunto de datos seleccionado en el Explorador de recursos o en la vista de diagrama. De forma predeterminada, la lista está en orden descendente, lo que significa que en la parte superior aparece la ventana de actividad más reciente.

![Lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Esta lista no se actualiza automáticamente, por lo que debe usar el botón Actualizar de la barra de herramientas para actualizarla manualmente.


Las ventanas de actividad pueden estar en uno de los siguientes estados:

<table>
<tr>
	<th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descripción</th>
</tr>
<tr>
	<td rowspan="8">En espera</td><td>ScheduleTime</td><td>No ha llegado la hora de que se ejecute la ventana de actividad.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Las dependencias de nivel superior no están listas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>No están disponibles los recursos de proceso.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas las instancias de actividad están ocupadas con la ejecución de otras ventanas de actividad.</td>
</tr>
<tr>
<td>ActivityResume</td><td>La actividad está en pausa y no puede ejecutar las ventanas de actividad hasta que se reanude.</td>
</tr>
<tr>
<td>Retry</td><td>Se volverá a intentar la ejecución de la actividad.</td>
</tr>
<tr>
<td>Validación</td><td>Aún no ha iniciado la validación.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>En espera de que se vuelva a intentar la validación.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validating</td><td>Validación en curso.</td>
</tr>
<td></td>
<td>La ventana de actividad se está procesando.</td>
</tr>
<tr>
<td rowspan="4">Con error</td><td>TimedOut</td><td>La ejecución tardó más tiempo del permitido por la actividad.</td>
</tr>
<tr>
<td>Canceled</td><td>Cancelado por acción del usuario.</td>
</tr>
<tr>
<td>Validación</td><td>Error de validación.</td>
</tr>
<tr>
<td></td><td>No se pudo generar o validar la ventana de actividad.</td>
</tr>
<td>Ready</td><td></td><td>La ventana de actividad está lista para su uso.</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>La ventana de actividad no se ha procesado.</td>
</tr>
<tr>
<td>None</td><td></td><td>Una ventana de actividad que existía con un estado distinto, pero que se ha restablecido.</td>
</tr>
</table>


Al hacer clic en una ventana de actividad de la lista, podrá ver detalles de dicha ventana en el Explorador de ventanas de actividad o en la ventana Propiedades de la derecha.

![Explorador de ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### Actualizar las ventanas de actividad  
Los detalles no se actualizan automáticamente, por lo que debe usar el botón **Actualizar** (segundo botón) de la barra de comandos para actualizar manualmente la lista de ventanas de actividad.
 

### Ventana Propiedades
La ventana Propiedades está en el panel del extremo derecho de la Aplicación de supervisión y administración.

![Ventana Propiedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Muestra propiedades del elemento seleccionado en el Explorador de recursos en la vista de árbol, la vista de diagrama o la lista de ventanas de actividad.

### Explorador de ventanas de actividad

La ventana Explorador de ventanas de actividad está en el panel del extremo derecho de la Aplicación de supervisión y administración. Muestra detalles de la ventana de actividad seleccionada en la notificación emergente Ventanas de actividad o en la lista Ventanas de actividad.

![Explorador de ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Puede cambiar a una ventana de actividad diferente haciendo clic en ella en la vista de calendario de la parte superior. También puede usar los botones de **flecha izquierda**/**flecha derecha** de la parte superior para ver las ventanas de actividad de la semana anterior o siguiente.

Puede usar los botones de la barra de herramientas del panel inferior para **volver a ejecutar** la ventana de actividad o para **actualizar** los detalles en el panel.


## Usar vistas de sistema
La Aplicación de supervisión y administración incluye vistas de sistema predefinidas (**Ventanas de actividad recientes**, **Ventanas de actividad con error**, **Ventanas de actividad en curso**) que permiten ver las ventanas de actividad recientes, las que contienen errores o las que están en curso de su factoría de datos.

Cambie a la pestaña **Vistas de supervisión** de la izquierda haciendo clic encima de ella.

![Pestaña Vistas de supervisión](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Hay tres vistas de sistema admitidas en este momento. Seleccione una opción para ver las ventanas de actividad recientes, las ventanas de actividad con error o las ventanas de actividad en curso en la lista Ventanas de actividad (en la parte inferior del panel central).

Al seleccionar la opción **Ventanas de actividad recientes**, verá todas las ventanas de actividad recientes en orden descendente según la **hora del último intento**.

Puede usar la vista **Ventanas de actividad con error** para ver todas las ventanas de actividad que contengan errores de la lista. Seleccione una ventana de actividad con error de la lista para ver los detalles en la ventana **Propiedades** o en el **Explorador de ventanas de actividad**. También puede descargar los registros de una ventana de actividad con error.


## Ordenar y filtrar ventanas de actividad
Cambie la configuración de la **hora de inicio** y la **hora de finalización** en la barra de comandos para filtrar las ventanas de actividad. Después de cambiar la hora de inicio y de finalización, haga clic en el botón situado junto a la hora de finalización para actualizar la lista Ventanas de actividad.

![Horas de inicio y finalización](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] Actualmente, todas las horas están en formato UTC en la Aplicación de supervisión y administración.

En la **lista Ventanas de actividad**, haga clic en el nombre de una columna (por ejemplo, Estado).

![Menú de columna de la lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Puede realizar las siguientes acciones:

- Ordenar en orden ascendente.
- Ordenar en orden descendente.
- Filtrar por uno o más valores (Listo, En espera, etc.).

Cuando especifique un filtro en una columna, verá que el botón de filtro se habilita para esa columna, a fin de indicar que los valores de la columna están filtrados.

![Filtro de columna de la lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Puede usar la misma ventana emergente para borrar filtros. Para borrar todos los filtros de la lista Ventanas de actividad, haga clic en el botón para borrar el filtro de la barra de comandos.

![Borrar todos los filtros de la lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## Realizar acciones por lotes

### Volver a ejecutar las ventanas de actividad seleccionadas
Seleccione una ventana de actividad, haga clic en la flecha abajo del primer botón de la barra de comandos y seleccione **Volver a ejecutar** o **Volver a ejecutar con canal de subida en la canalización**. Al seleccionar la opción **Volver a ejecutar con canal de subida en la canalización**, también se vuelven a ejecutar todas las ventanas de actividad con canal de subida. ![Volver a ejecutar una ventana de actividad](./media/data-factory-monitor-manage-app/ReRunSlice.png)

También puede seleccionar varias ventanas de actividad en la lista y volver a ejecutarlas al mismo tiempo. Puede filtrar las ventanas de actividad según el estado (por ejemplo, **Con error**) y después volver a ejecutar las ventanas de actividad con error después de corregir el problema que hace que se produzca un error en las ventanas de actividad. Vea la siguiente sección para obtener más información sobre el filtrado de ventanas de actividad en la lista.

### Pausar y reanudar varias canalizaciones
Puede seleccionar al mismo tiempo dos o más canalizaciones (mediante la tecla CTRL) y usar los botones de la barra de comandos (resaltados con un rectángulo rojo en la imagen siguiente) para pausarlas o reanudarlas a la vez.

![Suspender o reanudar en la barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## Crear alertas 
La página de alertas le permite crear una alerta nueva, así como ver, editar o eliminar las alertas existentes. También puede habilitar o deshabilitar una alerta. Haga clic en la pestaña Alertas para ver la página.

![Pestaña Alertas](./media/data-factory-monitor-manage-app/AlertsTab.png)

### Para crear una alerta

1. Haga clic en **Agregar alerta** para agregar una alerta. Verá la página Detalles. 

	![Crear alertas: página de detalles](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. Especifique el **nombre** y la **descripción** de la alerta y haga clic en **Siguiente**. Debería ver la página **Filtros**.

	![Crear alertas: página de filtros](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

	También puede **agregar** eventos de alerta, tal y como se muestra a continuación:

	![Agregar alertas](./media/data-factory-monitor-manage-app/AggregateAlerts.png)
2. Seleccione el **evento**, el **estado** y el **subestado** (opcional) sobre los que desea que el servicio Data Factory le alerte y haga clic en **Siguiente**. Debería ver la página **Destinatarios**.

	![Crear alertas: página de destinatarios](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. Seleccione la opción **Administradores de suscripciones de correo electrónico** o escriba un **correo electrónico de administrador adicional** y haga clic en **Finalizar**. Debería ver la alerta en la lista. 
	
	![Lista de alertas](./media/data-factory-monitor-manage-app/AlertsList.png)

En la lista de alertas, use los botones asociados con la alerta para editarla, eliminarla, habilitarla o deshabilitarla.

### Evento/estado/subestado
En la tabla siguiente se ofrece una lista de los eventos y los estados (y subestados) disponibles.

Nombre del evento | Estado | Subestado
-------------- | ------ | ----------
Ejecución de actividad iniciada | Started | Iniciando
Ejecución de actividad finalizada | Succeeded | Succeeded 
Ejecución de actividad finalizada | Con error| Asignación de recursos con error<p>Ejecución con error</p><p>Tiempo de espera agotado </p><p>Validación con error</p><p>Abandonado</p>
Creación de clúster de HDI a petición iniciada | Started | &nbsp; |
Creación correcta de clúster de HDI a petición | Succeeded | &nbsp; |
Clúster de HDI a petición eliminado | Succeeded | &nbsp; |
### Para editar, eliminar o deshabilitar una alerta


![Botones de alertas](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 

<!---HONumber=AcomDC_0218_2016-->