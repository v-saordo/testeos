<properties 
   pageTitle="Configuración de runbook"
   description="Describe las opciones de configuración para un runbook en Automatización de Azure y cómo cambiarlas mediante el Portal de administración de Azure y Windows PowerShell."
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
   ms.date="02/09/2016"
   ms.author="bwren" />

# Configuración de runbook

Cada runbook en Automatización de Azure tiene varias opciones de configuración para ayudarle a identificar y cambiar su comportamiento de registro. A continuación se describen todas estas opciones, seguido de procedimientos acerca de cómo modificarlas.

## Settings

### Nombre y descripción

No se puede cambiar el nombre de un runbook después de su creación. La descripción es opcional y puede tener hasta 512 caracteres.

### Etiquetas

Las etiquetas permiten asignar diferentes palabras y frases para ayudar a identificar un runbook. Por ejemplo, cuando se envía un runbook a la [Galería de runbooks](https://msdn.microsoft.com/library/dn781422.aspx), se especifican etiquetas determinadas para identificar qué categorías deben aparecer en el runbook. Puede especificar varias etiquetas para un runbook separándolas por comas.

### Registro

De forma predeterminada, los registros detallados y de progreso no se escriben en el historial de trabajos. Puede cambiar la configuración de un runbook determinado para estas entradas de registro. Para obtener más información sobre estos registros, consulte [Salida y mensajes de los runbooks](https://msdn.microsoft.com/library/dn879148.aspx).

## Cambio de la configuración del runbook

### Cambio de la configuración del runbook mediante el Portal de administración de Azure

Puede cambiar la configuración de un runbook en el Portal de administración de Azure desde la página **Configurar** del runbook.

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Seleccione la pestaña **Runbooks**.
1. Haga clic en el nombre de un runbook.
1. Seleccione la pestaña **Configurar**.

### Cambio de la configuración del runbook mediante Windows PowerShell

Puede utilizar el cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) para cambiar la configuración de un runbook. Si desea especificar varias etiquetas, se puede proporcionar una matriz o una sola cadena con valores delimitados por comas al parámetro Tags. Puede obtener las etiquetas actuales con el [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Los siguientes comandos de ejemplo muestran cómo establecer las propiedades de un runbook. Este ejemplo agrega tres etiquetas a las etiquetas existentes y especifica que se deben registrar registros detallados.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	$tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
	$tags += "Tag1,Tag2,Tag3"
	Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## Artículos relacionados
- [Salida y mensajes de los runbooks](../automation-runbook-output-and-messages) 
- [Creación o importación de un runbook](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=AcomDC_0211_2016-->