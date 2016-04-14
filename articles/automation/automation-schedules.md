<properties 
   pageTitle="Programaciones en Automatización de Azure | Microsoft Azure"
   description="Las programaciones de Automatización se usan para programar runbooks en Automatización de Azure para que se inicien automáticamente. En este artículo se describe cómo crear programaciones."
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
   ms.date="01/27/2016"
   ms.author="bwren" />

# Programaciones en Automatización de Azure

Las programaciones de Automatización se usan para programar la ejecución automática de runbooks. Podría ser una sola fecha y hora para que el runbook se ejecute una vez. O bien, se puede tratar de una programación periódica para iniciar el runbook varias veces. Normalmente, no se accede a las programaciones desde los runbooks.

>[AZURE.NOTE]  Las programaciones no admiten actualmente las configuraciones de DSC de Automatización de Azure.

## Cmdlets de Windows PowerShell

Los cmdlets de la siguiente tabla se usan para crear y administrar programaciones con Windows PowerShell en Automatización de Azure. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md).

|Cmdlets|Descripción|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Recupera una programación.|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Crea una nueva programación.|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Quita una programación.|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Establece las propiedades de una programación existente.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Recupera runbooks programados.|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Asocia un runbook con una programación.|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Anula la asociación de un runbook con una programación.|

## Creación de una nueva programación

### Para crear una nueva programación con el Portal de Azure clásico


1. En la cuenta de Automatización, haga clic en **Recursos** en la parte superior de la ventana.
1. En la parte inferior de la ventana, haga clic en **Agregar configuración**.
1. Haga clic en **Agregar programación**.
1. Realice los pasos del asistente y haga clic en la casilla para guardar la nueva programación.

### Para crear una nueva programación con el Portal de Azure

1. En la cuenta de Automatización, haga clic en la parte **Recursos** para abrir la hoja **Recursos**.
1. Haga clic en la parte **Programaciones** para abrir la hoja **Programaciones**.
1. Haga clic en **Agregar una programación** en la parte superior de la hoja.
1. Complete el formulario y haga clic en **Crear** para guardar la nueva programación.

### Para crear una nueva programación con Windows PowerShell

El cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) crea una nueva programación y establece el valor de una programación existente. Los siguientes comandos de ejemplo de Windows PowerShell crean una nueva programación llamada My Daily Schedule que empieza mañana a mediodía y se desencadena todos los días durante un año:

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)
	
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## Otras referencias
- [Programación de un runbook en Automatización de Azure](automation-scheduling-a-runbook.md)
 

<!---HONumber=AcomDC_0204_2016-->