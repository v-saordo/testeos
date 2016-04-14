<properties 
   pageTitle="Recursos de variables en Automatización de Azure | Microsoft Azure"
   description="Los activos de variables son valores que están disponibles para todos los runbooks y configuraciones de DSC en Automatización de Azure. En este artículo se explican los detalles de las variables y cómo trabajar con ellas en la creación de texto y gráficos."
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

# Recursos de variables en Automatización de Azure

Los activos de variables son valores que están disponibles para todos los runbooks y configuraciones de DSC en su cuenta de Automatización. Pueden crearse, modificarse y recuperarse desde el Portal de Azure, Windows PowerShell y desde un runbook o una configuración de DSC. Las variables de Automatización son útiles para los siguientes escenarios:

- Compartir un valor entre varios runbooks o configuraciones de DSC.

- Compartir un valor entre varios trabajos del mismo runbook o configuración de DSC.

- Administrar un valor desde el portal o desde la línea de comandos de Windows PowerShell que usan los runbooks o las configuraciones de DSC.

Las variables de Automatización se conservan para que sigan estando disponibles, incluso si se produce un error en el runbook o la configuración de DSC. Esto también permite que se establezca un valor por un runbook que se usará por otro o bien se usará por el mismo runbook o configuración de DSC la siguiente vez que se ejecute.

Cuando se crea una variable, puede especificar que se almacene cifrada. Cuando se cifra una variable, se almacena de forma segura en Automatización de Azure y su valor no se puede recuperar del cmdlet [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) que se incluye como parte del módulo Azure PowerShell. Es la única manera de que se puede recuperar un valor cifrado desde la actividad **Get-AutomationVariable** en un runbook o una configuración de DSC.

>[AZURE.NOTE]Los recursos protegidos en Automatización de Azure incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Automatización de Azure con una clave única que se genera para cada cuenta de automatización. Esta clave se cifra mediante un certificado maestro y se almacena en Automatización de Azure. Antes de almacenar un recurso seguro, la clave de la cuenta de automatización se descifra con el certificado maestro y, a continuación, se utiliza para cifrar el recurso.

## Tipos de variables

Cuando se crea una variable con el Portal de Azure, debe especificar un tipo de datos en la lista desplegable para que el portal pueda mostrar el control adecuado para escribir el valor de la variable. La variable no se limita a este tipo de datos, pero hay que establecer la variable mediante Windows PowerShell si se desea especificar un valor de un tipo diferente. Si especifica **No definido**, el valor de la variable se establecerá en **$null** y deberá establecer el valor con el cmdlet [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) o con la actividad **Set-AutomationVariable**. No se puede crear o cambiar el valor de un tipo complejo de variable en el portal, pero puede proporcionar un valor de cualquier tipo mediante Windows PowerShell. Los tipos complejos se devolverán como un [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Puede almacenar varios valores en una única variable mediante la creación de una matriz o tabla hash y guardarlos en la variable.

## Cmdlets y actividades de flujo de trabajo

Los cmdlets de la tabla siguiente se usan para crear y administrar variables de Automatización con Windows PowerShell. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md) que está disponible para su uso en los runbooks de Automatización y las configuraciones de DSC.

|Cmdlets|Descripción|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|Recupera el valor de una variable existente.|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|Crea una nueva variable y establece su valor.|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|Quita una variable existente.|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|Establece el valor de una variable existente.|

Las actividades de flujo de trabajo en la tabla siguiente se usan para acceder a las variables de Automatización en un runbook. Solo están disponibles para su uso en un runbook o una configuración de DSC y no se incluyen como parte del módulo Azure PowerShell.

|Actividades de flujo de trabajo|Descripción|
|:---|:---|
|Get-AutomationVariable|Recupera el valor de una variable existente.|
|Set-AutomationVariable|Establece el valor de una variable existente.|

>[AZURE.NOTE] Debe evitar el uso de variables en el parámetro – Name de **Get-AutomationVariable** en un runbook o una configuración de DSC, ya que puede complicar la detección de dependencias entre runbooks o configuraciones de DSC y las variables de Automatización en tiempo de diseño.

## Creación de una nueva variable de Automatización

### Para crear una nueva variable con el Portal de Azure

1. En la cuenta de Automatización, haga clic en **Recursos** en la parte superior de la ventana.
1. En la parte inferior de la ventana, haga clic en **Agregar configuración**.
1. Haga clic en **Agregar Variable**.
1. Realice los pasos del asistente y haga clic en la casilla para guardar la nueva variable.


### Para crear una nueva variable con el Portal de Azure

1. En la cuenta de Automatización, haga clic en la parte **Recursos** para abrir la hoja **Recursos**.
1. Haga clic en la parte **Variables** para abrir la hoja **Variables**.
1. Haga clic en **Agregar una variable** en la parte superior de la hoja.
1. Complete el formulario y haga clic en **Crear** para guardar la nueva variable.


### Para crear una nueva variable con Windows PowerShell

El cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) crea una nueva variable y establece su valor inicial. Puede recuperar el valor mediante [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx). Si el valor es un tipo simple, se devuelve ese mismo tipo. Si es un tipo complejo, se devuelve **PSCustomObject**.

Los comandos de ejemplo siguientes muestran cómo crear una variable de tipo cadena y que después devuelva su valor.


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Los comandos de ejemplo siguientes muestran cómo crear una variable con un tipo complejo y que después devuelva su valor. En este caso, se usa un objeto de máquina virtual desde **Get-AzureVM**.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $vmValue.Name
	$vmIpAddress = $vmValue.IpAddress



## Uso de una variable en un runbook o una configuración de DSC

Use la actividad **Set-AutomationVariable** para establecer el valor de una variable de Automatización en un runbook o una configuración de DSC y **Get-AutomationVariable** para recuperarlo. No debe usar los cmdlets **Set-AzureAutomationVariable** o **Get-AzureAutomationVariable** en un runbook o una configuración DSC, ya que son menos eficientes que las actividades de flujo de trabajo. Tampoco puede recuperar el valor de variables seguras con **Get-AzureAutomationVariable**. La única forma de crear una nueva variable desde un runbook o una configuración de DSC es usar el cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### Ejemplos de runbook textual

#### Establecimiento y recuperación de un valor simple de una variable

Los comandos de ejemplo siguientes muestran cómo establecer y recuperar una variable en un runbook textual. En este ejemplo, se asume que se han creado las variables de tipo entero llamadas *NumberOfIterations* y *NumberOfRunnings* y una variable de tipo cadena denominada *SampleMessage*.

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)


#### Establecimiento y recuperación de un objeto completo en una variable

El código de ejemplo siguiente muestra cómo actualizar una variable con un valor complejo en un runbook textual. En este ejemplo, se recupera una máquina virtual de Azure con **Get-AzureVM** y se guarda en una variable de Automatización existente. Como se explica en [Tipos de variables](#variable-types), se almacena como un PSCustomObject.

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


En el código siguiente, el valor se recupera de la variable y se usa para iniciar la máquina virtual.

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### Establecimiento y recuperación de una colección en una variable

El código de ejemplo siguiente muestra cómo usar una variable con un colección de valores complejos en un runbook textual. En este ejemplo, se recuperan varias máquinas virtuales de Azure con **Get-AzureVM** y se guardan en una variable de Automatización existente. Como se explica en [Tipos de variables](#variable-types), se almacena como una colección de objetos PSCustomObject.

	$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

En el código siguiente, la colección se recupera de la variable y se usa para iniciar las máquinas virtuales.

	$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
	ForEach ($vmValue in $vmValues)
	{
	   if ($vmValue.PowerState -eq 'Stopped') {
	      Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
	   }
	}

### Ejemplos de runbook gráfico

En un runbook gráfico, agregue **Get-AutomationVariable** o **Set-AutomationVariable** haciendo clic con el botón derecho en la variable en el panel Biblioteca del editor gráfico y seleccionando la actividad que desee.

![Adición de una variable al lienzo](media/automation-variables/variable-add-canvas.png)

#### Configuración de valores de una variable

La imagen siguiente muestra las actividades de ejemplo para actualizar una variable con un valor simple en un runbook gráfico. En este ejemplo, se recupera una única máquina virtual de Azure con **Get-AzureVM** y se guarda el nombre del equipo en una variable de Automatización existente con un tipo de cadena. No importa si el [vínculo es una canalización o una secuencia](automation-graphical-authoring-intro.md#links-and-workflow), puesto que solo esperamos un único objeto en la salida.

![Establecimiento de una variable simple](media/automation-variables/set-simple-variable.png)

La imagen siguiente muestra las actividades usadas para actualizar una variable con un valor complejo en un runbook gráfico. El único cambio del ejemplo anterior consiste en no especificar una **ruta de acceso de campo** para la **salida de la actividad** en el **Set-AutomationVariable**, de tal forma que se almacena el objeto en lugar de únicamente una propiedad del objeto. Como se explica en [Tipos de variables](#variable-types), se almacena como un objeto PSCustomObject.

![Establecimiento de una variable compleja](media/automation-variables/set-complex-variable.png)

La imagen siguiente muestra una funcionalidad similar a la del ejemplo anterior, con varias máquinas virtuales guardadas en la variable. Se debe usar aquí un [vínculo de secuencia](automation-graphical-authoring-intro.md#links-and-workflow) para que la actividad **Set-AutomationVariable** reciba el conjunto completo de máquinas virtuales como una colección. Si se ha usado un [vínculo de canalización](automation-graphical-authoring-intro.md#links-and-workflow), la actividad **Set-AutomationVariable** se habría ejecutado por separado para cada objeto, cuyo resultado es que se guardaría únicamente la última máquina virtual de la colección. Como se explica en [Tipos de variables](#variable-types), se almacena como una colección de objetos PSCustomObject.

![Establecimiento de una variable de colección compleja](media/automation-variables/set-complex-variable-collection.png)

#### Recuperación de valores de una variable

La imagen siguiente muestra las actividades de ejemplo para que recuperan y usan una variable en un runbook gráfico. La primera actividad recupera las máquinas virtuales guardadas en la variable del ejemplo anterior. El vínculo debe ser una [canalización](automation-graphical-authoring-intro.md#links-and-workflow) para que la actividad **Start-AzureVM** se ejecuta una vez para cada objeto enviado desde la actividad **Get-AutomationVariable**. Esto funcionará igual si un único objeto o varios se almacenan en la variable. La actividad **Start-AzureVM** utiliza las propiedades del objeto PSCustomObject que representa cada máquina virtual.

![Obtención de una variable compleja](media/automation-variables/get-complex-variable.png)

La imagen siguiente muestra cómo filtrar los objetos almacenados en una variable de un runbook gráfico. Se agrega una [condición](automation-graphical-authoring-intro.md#links-and-workflow) al vínculo en el ejemplo anterior para filtrar únicamente aquellas máquinas virtuales que se detienen cuando se ha establecido la variable.

![Obtención de una variable compleja filtrada](media/automation-variables/get-complex-variable-filter.png)


## Artículos relacionados

- [Vínculos de creación gráfica](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=AcomDC_0302_2016-->