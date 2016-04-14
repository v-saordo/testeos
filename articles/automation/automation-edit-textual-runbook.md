<properties 
	pageTitle="Edición de runbooks de texto en Automatización de Azure"
	description="En este artículo, se proporcionan diferentes procedimientos para trabajar con runbooks de PowerShell y de flujo de trabajo de PowerShell en Automatización de Azure mediante el editor de texto."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="02/23/2016"
	ms.author="magoedte;bwren" />

# Edición de runbooks de texto en Automatización de Azure

El editor de texto de Automatización de Azure sirve para editar [runbooks de PowerShell](automation-runbook-types.md#powershell-runbooks) y [runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Además, incluye las características típicas de otros editores de código, como IntelliSense y codificación en colores, junto con otras características especiales que le ayudarán a acceder a recursos habituales de runbooks. En este artículo, se proporcionan pasos detallados para realizar diferentes funciones con este editor.

El editor de texto incluye una característica para insertar código para las actividades, los recursos y los runbooks secundarios en un runbook. En lugar de escribir en el código personalmente, puede seleccionar entre una lista de recursos disponibles para que se inserte el código adecuado en el runbook.

Cada runbook de Automatización de Azure tiene dos versiones: una de borrador y otra publicada. Edite la versión de borrador del runbook y después publíquela para que pueda ejecutarse. No se puede editar la versión publicada. Consulte el artículo sobre la [publicación de un runbook](automation-creating-importing-runbook.md#publishing-a-runbook) para obtener más información.

Para trabajar con [runbooks gráficos](automation-runbook-types.md#graphical-runbooks), consulte [Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md).

## Para editar un runbook con el Portal de Azure

Use el siguiente procedimiento para abrir un runbook para su edición en el editor de texto.

1. En el Portal de Azure, seleccione su cuenta de automatización.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el nombre del runbook que desea editar y después en el botón **Editar**.
6. Realice las modificaciones necesarias.
7. Haga clic en **Guardar** cuando termine las modificaciones.
8. Haga clic en **Publicar** si desea que la última versión de borrador del runbook se publique.

### Para insertar un cmdlet en un runbook

2. En el lienzo del editor de texto, coloque el cursor donde desee colocar el cmdlet.
3. Expanda el nodo **Cmdlets** en el control Biblioteca. 
3. Expanda el módulo que contiene el cmdlet que desea usar.
4. Haga clic con el botón derecho en el cmdlet para insertarlo y seleccione **Agregar a lienzo**. Si el cmdlet tiene más de un conjunto de parámetros, se agregará el conjunto predeterminado. También puede expandir el cmdlet para seleccionar un conjunto de parámetros diferente.
4. Se inserta el código para el cmdlet con toda su lista de parámetros.
5. Proporcione un valor adecuado en lugar del tipo de datos entre corchetes, <>, para los parámetros necesarios. Elimine cualquier parámetro que no sea necesario.

### Para insertar código de runbook secundario en un runbook

2. En el lienzo del editor de texto, coloque el cursor donde desee colocar el código para el [runbook secundario](automation-child-runbooks.md).
3. Expanda el nodo **Runbooks** en el control Biblioteca. 
3. Haga clic con el botón derecho en el runbook para insertarlo y seleccione **Agregar a lienzo**.
4. Se inserta el código para el runbook secundario con los marcadores de posición de los parámetros de runbook.
5. Reemplace los marcadores de posición por valores adecuados para cada parámetro.

### Para insertar un recurso en un runbook

2. En el lienzo del editor de texto, coloque el cursor donde desee colocar el código para el runbook secundario.
3. Expanda el nodo **Recursos** en el control Biblioteca. 
4. Expanda el nodo para el tipo de recurso que desee.
3. Haga clic con el botón derecho en el recurso para insertarlo y seleccione **Agregar a lienzo**. Para [recursos variables](automation-variables.md), seleccione **Agregar "Obtener variable" a lienzo** o **Agregar "Establecer variable" a lienzo** en función de si desea obtener o establecer la variable.
4. El código para el recurso se inserta en el runbook.



## Para editar un runbook con el Portal de Azure

Use el siguiente procedimiento para abrir un runbook para su edición en el editor de texto.

1. En el Portal de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
2. Seleccione la pestaña **Runbooks**.
3. Haga clic en el nombre del runbook que desea editar y después seleccione la pestaña **Autor**.
5. Haga clic en el botón **Editar** en la parte inferior de la pantalla.
6. Realice las modificaciones necesarias.
7. Haga clic en **Guardar** cuando termine las modificaciones.
8. Haga clic en **Publicar** si desea que la última versión de borrador del runbook se publique.

### Para insertar una actividad en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desee colocar la actividad.
1. En la parte inferior de la pantalla, haga clic en **Insertar** y después en **Actividad**.
1. En la columna **Módulo de integración**, seleccione el módulo que contiene la actividad.
1. En el panel **Actividad**, seleccione una actividad.
1. En la columna **Descripción**, observe la descripción de la actividad. Si lo desea, puede hacer clic en Ver ayuda detallada para iniciar la ayuda sobre la actividad en el explorador.
1. Haga clic en la flecha derecha. Si la actividad tiene parámetros, se mostrarán como referencia.
1. A continuación, haga clic en el botón Comprobar. El código para ejecutar la actividad se insertará en el runbook.
1. Si la actividad requiere parámetros, proporcione un valor adecuado en lugar del tipo de datos entre corchetes, <>.

### Para insertar código de runbook secundario en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desee colocar el [runbook secundario](automation-child-runbooks.md).
2. En la parte inferior de la pantalla, haga clic en **Insertar** y después en **Runbook**.
3. Seleccione en la columna central el runbook que desee insertar y haga clic en la flecha derecha.
4. Si la runbook tiene parámetros, se mostrarán como referencia.
5. A continuación, haga clic en el botón Comprobar. El código para ejecutar el runbook seleccionado se insertará en el runbook actual.
7. Si el runbook requiere parámetros, proporcione un valor adecuado en lugar del tipo de datos entre corchetes, <>.

### Para insertar un recurso en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desee colocar la actividad para recuperar el recurso.
1. En la parte inferior de la pantalla, haga clic en **Insertar** y después en **Configuración**.
1. En la columna **Acción de configuración**, seleccione la acción que desee.
1. Seleccione entre los recursos disponibles en la columna central.
1. A continuación, haga clic en el botón Comprobar. El código para obtener o establecer el recurso se insertará en el runbook.



## Para editar un runbook de Automatización de Azure mediante Windows PowerShell

Para editar un runbook con Windows PowerShell, use el editor de su elección y guárdelo en un archivo. ps1. Puede usar el cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) para recuperar el contenido del runbook y después el cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) para reemplazar el borrador del runbook existente por el modificado.

### Para recuperar el contenido de un runbook mediante Windows PowerShell

En los siguientes comandos de ejemplo, se muestra cómo se recupera el script para un runbook y se guarda en un archivo de script. En este ejemplo, se recupera la versión de borrador. También es posible recuperar la versión publicada del runbook, aunque esta no se puede cambiar.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### Para cambiar el contenido de un runbook mediante Windows PowerShell

En los siguientes comandos de ejemplo, se muestra cómo reemplazar el contenido existente de un runbook por el de un archivo de script. Tenga en cuenta que es el mismo procedimiento de ejemplo que en el artículo sobre cómo [importar un runbook desde un archivo de script con Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## Artículos relacionados

- [Creación o importación de un runbook en Automatización de Azure](automation-creating-importing-runbook.md)
- [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md)
- [Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md)
- [Certificados](automation-certificates.md)
- [Conexiones](automation-connections.md)
- [Credenciales](automation-credentials.md)
- [Programaciones](automation-schedules.md)
- [Variables](automation-variables.md)

<!---HONumber=AcomDC_0302_2016-->