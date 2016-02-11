<properties 
	pageTitle="Creación o importación de un runbook en Automatización de Azure"
	description="En este artículo se describe cómo crear un runbook nuevo en la automatización de Azure o importar uno desde un archivo."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/22/2015"
	ms.author="bwren" />

# Creación o importación de un runbook en Automatización de Azure

Puede agregar un runbook a la Automatización de Azure, ya sea [creando uno nuevo](#creating-a-new-runbook) o importando un runbook existente desde un archivo o desde la [Galería de runbooks](automation-runbook-gallery.md). Este artículo ofrece información sobre cómo crear e importar runbooks de un archivo. Puede obtener todos los detalles sobre el acceso a los módulos y los runbooks de la comunidad en [Galería de runbooks y módulos para la Automatización de Azure](automation-runbook-gallery.md).

## Creación de un nuevo runbook

Puede crear un nuevo runbook en la Automatización de Azure mediante uno de los portales de Azure o Windows PowerShell. Cuando se haya creado el runbook, puede editarlo con la información de [Flujo de trabajo de PowerShell de aprendizaje](automation-powershell-workflow.md) y [Creación gráfica en la Automatización de Azure](automation-graphical-authoring-intro.md).

### Para crear un nuevo runbook de Automatización de Azure con el portal de Azure

Solo puede trabajar con [Runbooks del flujo de trabajo de PowerShell ](automation-runbook-types.md#powershell-workflow-runbooks) en el portal de Azure.

1. En el portal de Azure, haga clic en **Nuevo**, **Servicios de aplicaciones**, **Automatización**, **Runbook**, **Creación rápida**.
2. Escriba la información necesaria y luego haga clic en **Crear**. El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
3. Si quiere editar el runbook ahora, haga clic en **Editar runbook**. De lo contrario, haga clic en **Aceptar**.
4. Su nuevo runbook aparecerá en la pestaña **Runbooks**.


### Para crear un nuevo runbook de Automatización de Azure con el portal de vista previa de Azure

1. En el Portal de vista previa de Azure, abra su cuenta de Automatización. 
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un runbook** y luego en **Crear un nuevo runbook**.
2. Escriba un **Nombre** para el runbook y seleccione su [Tipo](automation-runbook-types.md). El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
3. Haga clic en **Crear** para crear el runbook y abra el editor.


### Para crear un nuevo runbook de automatización de Azure con Windows PowerShell

Puede utilizar el cmdlet [New-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690272.aspx) para crear un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vacío. Puede especificar el parámetro **Name** para crear un runbook vacío que puede editar más adelante, o puede especificar el parámetro **Path** para importar un archivo de script.

Los comandos de ejemplo siguientes muestran cómo crear un nuevo runbook vacío.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName 

## Importar un runbook de un archivo en la automatización de Azure

Puede crear un nuevo runbook en automatización de Azure importando un script de PowerShell o un flujo de trabajo de PowerShell (extensión. ps1) o un runbook gráfico exportado (.graphrunbook). Debe especificar el [tipo de runbook](automation-runbook-types.md) que se creará de la importación teniendo en cuenta las siguientes consideraciones.

- Solo se puede importar un archivo de .graphrunbook en un [runbook gráfico](automation-runbook-types.md#graphical-runbooks) nuevo y solo se pueden crear runbooks gráficos desde un archivo .graphrunbook.
- Un archivo .ps1 que contiene un flujo de trabajo de PowerShell solo se puede importar en un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Si el archivo contiene varios flujos de trabajo de PowerShell, se producirá un error en la importación. Debe guardar cada flujo de trabajo en su propio archivo e importar cada uno por separado.
- Un archivo .ps1 que no contiene un flujo de trabajo puede importarse en un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) o en un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Si se importa en un runbook de flujo de trabajo de PowerShell, se convertirá en un flujo de trabajo y los comentarios se incluirán en el runbook especificando los cambios realizados.

### Para importar un runbook de un archivo con el portal de Azure
Puede usar el siguiente procedimiento para importar un archivo de script en la Automatización de Azure. Tenga en cuenta que solo puede importar un archivo. ps1 en un runbook de flujo de trabajo de PowerShell mediante este portal. Debe usar el portal de vista previa de Azure para otros tipos.

1. En el Portal de administración de Azure, seleccione **Automatización** y luego haga clic en el nombre de una cuenta de automatización.
2. Haga clic en **Import**.
3. Haga clic en **Buscar archivo** y busque el archivo de script que va a importar.
4. Si quiere editar el runbook ahora, haga clic en **Editar runbook**. De lo contrario, haga clic en Aceptar.
5. El runbook nuevo aparecerá en la pestaña **Runbooks** para la cuenta de automatización.
6. Debe [publicar el runbook](#publishing-a-runbook) para poder ejecutarlo.


### Para importar un runbook de un archivo con el portal de vista previa de Azure
Puede usar el siguiente procedimiento para importar un archivo de script en la Automatización de Azure. Tenga en cuenta que solo puede importar un archivo. ps1 en un runbook de flujo de trabajo de PowerShell mediante este portal.

1. En el Portal de vista previa de Azure, abra su cuenta de Automatización. 
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un runbook** y luego en **Importar**.
4. Haga clic en **Archivo de runbook** para seleccionar el archivo que se va a importar.
2. Si el campo **Nombre** está habilitado, tiene la opción de cambiarlo. El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
3. Seleccione un [tipo de runbook](automation-runbook-types.md) teniendo en cuenta las restricciones enumeradas anteriormente.
3. El nuevo runbook aparecerá en la lista de runbooks para la cuenta de automatización.
4. Debe [publicar el runbook](#publishing-a-runbook) para poder ejecutarlo.

### Para importar un runbook de un archivo de script con Windows PowerShell

Puede usar el cmdlet [AzureAutomationRunbookDefinition Set](https://msdn.microsoft.com/library/dn690267.aspx) para importar un archivo de script en la versión de borrador de un runbook existente. El archivo de script debe contener un flujo de trabajo de Windows PowerShell único. Si el runbook ya tiene una versión de borrador, la importación generará un error a menos que use el parámetro Overwrite. Cuando se haya importado el runbook, puede publicarlo con [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx).

Los siguientes comandos de ejemplo muestran cómo importar un archivo de script en un runbook existente y luego publicarlo.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition –AutomationAccountName $automationAccountName –Name $runbookName –Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName


## Publicación de un runbook

Cuando cree o importe un runbook nuevo, debe publicarlo para poder ejecutarlo. Cada runbook de Automatización de Azure tiene una versión de borrador y una versión publicada. Solo es posible ejecutar la versión publicada y solo es posible editar la versión de borrador. Los cambios realizados en la versión de borrador no afectan la versión publicada. Cuando la versión de borrador deba estar lista para su disponibilidad, puede publicarla, lo que sobrescribirá la versión publicada con la versión de borrador.

## Para publicar un runbook mediante el portal de Azure

1. Abra el runbook en el portal de Azure.
1. En la parte superior de la pantalla, haga clic en **Autor**.
1. En la parte inferior de la pantalla, haga clic en **Publicar** y luego en **Sí** para el mensaje de comprobación.

## Para publicar un runbook mediante el portal de vista previa de Azure

1. Abra el runbook mediante el portal de vista previa de Azure.
1. Haga clic en el botón **Editar**.
1. Haga clic en el botón **Publicar** y luego en **Sí** para el mensaje de comprobación.


## Para publicar un runbook mediante Windows PowerShell

Puede usar el cmdlet [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) para publicar un runbook con Windows PowerShell. Los comandos de ejemplo siguientes muestran cómo crear un runbook de ejemplo.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	
	Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName



## Artículos relacionados

- [Galerías de runbooks y módulos para la automatización de Azure](automation-runbook-gallery.md)
- [Edición de runbooks de texto en Automatización de Azure](automation-edit-textual-runbook.md)
- [Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md)

<!---HONumber=Oct15_HO3-->