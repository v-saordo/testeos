<properties 
   pageTitle="Programación de un runbook en Automatización de Azure"
   description="Describe cómo crear una programación en Automatización de Azure, de modo que pueda iniciar automáticamente un runbook en un momento determinado o en una programación periódica."
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
   ms.date="10/01/2015"
   ms.author="bwren" />

# Programación de un runbook en Automatización de Azure

Para programar un runbook en Automatización de Azure para que se inicie en un momento determinado, vincúlelo a una o más programaciones. Una programación se puede configurar para ejecutarse una vez o para que se repita cada número de horas o días especificado. Un runbook puede vincularse a varias programaciones y una programación puede tener varios runbooks vinculados a ella.

## Creación de una programación

Puede crear una nueva programación con el Portal de administración de Azure o con Windows PowerShell. También tiene la opción de crear una nueva programación cuando se vincula un runbook a una programación mediante el Portal de administración de Azure.

### Para crear una nueva programación con el Portal de administración de Azure

1. En el Portal de administración de Azure, seleccione Automatización y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Seleccione la pestaña **Recursos**.
1. En la parte inferior de la ventana, haga clic en **Agregar configuración**.
1. Haga clic en **Agregar programación**.
1. Escriba un **nombre** y, opcionalmente, una **descripción** para la nueva programación.
1. Seleccione si la programación se ejecutará **Una vez**, **Cada hora** o **Diariamente**.
1. Especifique una **Hora de inicio** y las demás opciones, según el tipo de programación seleccionada.

### Para crear una nueva programación con Windows PowerShell

Puede utilizar el cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) para crear una nueva programación en Automatización de Azure. Debe especificar la hora de inicio para la programación y si debe ejecutarse una vez cada hora o diariamente.

Los siguientes comandos de ejemplo muestran cómo crear una nueva programación que se ejecuta cada día a las 3:30 p.m. a partir del 20 de enero de 2015.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## Vinculación de una programación a un runbook

Un runbook puede vincularse a varias programaciones y una programación puede tener varios runbooks vinculados a ella. Si un runbook tiene parámetros, puede proporcionar valores para ellos. Debe proporcionar valores para los parámetros obligatorios y puede proporcionar valores para los parámetros opcionales. Estos valores se usarán cada vez que esta programación inicia el runbook. Puede adjuntar el mismo runbook a otra programación y especificar distintos valores de parámetros.

### Para vincular una programación a un runbook con el Portal de administración de Azure

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Seleccione la pestaña **Runbooks**.
1. Haga clic en el nombre del runbook que se va a programar.
1. Haga clic en la pestaña **Programar**.
2. Si el runbook no está vinculado a una programación, se le ofrecerá la opción de **Vincular a una nueva programación** o **Vincular a una programación existente**. Si el runbook está vinculado a una programación, haga clic en **Vincular** en la parte inferior de la ventana para acceder a estas opciones.
1. Si el runbook tiene parámetros, se le pedirán sus valores.  

### Para vincular una programación a un runbook con Windows PowerShell

Puede utilizar el [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular una programación a un runbook. Puede especificar valores para los parámetros del runbook con el parámetro Parameters. Consulte [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) para obtener más información sobre cómo especificar valores de parámetro.

Los siguientes comandos de ejemplo muestran cómo vincular una programación a un runbook con parámetros.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## Deshabilitación de una programación

Cuando se deshabilita una programación, los runbooks vinculados a ella no se ejecutarán en dicha programación. Puede deshabilitar una programación manualmente o establecer un fecha de expiración para las programaciones de cada hora o diarias al crearlas. Cuando se alcanza la fecha de expiración, se deshabilitará la programación.

### Para deshabilitar una programación con el Portal de administración de Azure

Puede deshabilitar una programación en el Portal de administración de Azure desde la página de detalles de la programación para la programación.

1. En el Portal de administración de Azure, seleccione Automatización y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Seleccione la pestaña Recursos.
1. Haga clic en el nombre de una programación para abrir la página de detalles.
2. Cambie **Habilitado** a **No**.

### Para deshabilitar una programación con Windows PowerShell

Puede usar el cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para cambiar las propiedades de una programación existente. Para deshabilitar la programación, especifique **false** para el parámetro **IsEnabled**.

Los comandos de ejemplo siguientes muestran cómo deshabilitar una programación.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## Artículos relacionados

- [Recursos de programación en Automatización de Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) 

<!---HONumber=Oct15_HO3-->