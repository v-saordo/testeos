<properties 
	pageTitle="Inicio y detención de máquinas virtuales con Automatización de Azure - Flujo de trabajo de PowerShell | Microsoft Azure"
	description="Versión gráfica de la solución de Automatización de Azure con runbooks para iniciar y detener las máquinas virtuales clásicas."
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

# Solución Automatización de Azure - inicio y detención de máquinas virtuales

Esta solución Automatización de Azure incluye runbooks para iniciar y detener las máquinas virtuales clásicas. Puede usar esta solución para cualquiera de las siguientes acciones:

- Usar los runbooks sin modificaciones en su propio entorno. 
- Modificar los runbooks para ejecutar la funcionalidad personalizada.  
- Llamar a los runbooks desde otro runbook como parte de una solución global. 
- Usar los runbooks como tutoriales para aprender los conceptos de creación de runbooks. 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

Es la versión de runbook del flujo de trabajo de PowerShell de esta solución. Está también disponible mediante [runbooks gráficos](automation-solution-startstopvm-graphical.md).

## Obtención de la solución

Esta solución consta de dos runbooks de flujo de trabajo de PowerShell que se pueden descargar en los vínculos siguientes. Consulte la [versión gráfica](automation-solution-startstopvm-graphical.md) de esta solución para obtener vínculos a los runbooks gráficos.

| Runbook | Vínculo | Escriba | Descripción |
|:---|:---|:---|:---|
| Start-AzureVMs | [Inicio de máquinas virtuales de Azure clásicas](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Flujo de trabajo de PowerShell | Inicia todas las máquinas virtuales clásicas en una suscripción de Azure o en todas las máquinas virtuales con un nombre de servicio determinado. |
| Stop-AzureVMs | [Detención de máquinas virtuales de Azure clásicas](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Flujo de trabajo de PowerShell | Detiene todas las máquinas virtuales de una cuenta de automatización o todas las máquinas virtuales con un nombre de servicio determinado. |


## Instalación de la solución

### 1\. Instalación de los runbooks

Después de descargar los runbooks, puede importarlos mediante el procedimiento descrito en [Importación de un runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### 2\. Revisión de la descripción y los requisitos
Los runbooks incluyen el texto de ayuda comentada que incluye una descripción y los activos necesarios. También puede obtener la misma información en este artículo.

### 3\. Configuración de activos
Los runbooks requieren los siguientes activos que se deben crear y rellenar con los valores adecuados.

| Tipo de recurso | Nombre de recurso | Descripción |
|:---|:---|:---|:---|
| Credential: | AzureCredential | Contiene las credenciales para una cuenta que tiene autoridad para iniciar y detener las máquinas virtuales en la suscripción de Azure. Además, puede especificar otro activo de credenciales en el parámetro **Credential** de la actividad **Add-AzureAccount**. |
| Variable | AzureSubscriptionId | Contiene el identificador de suscripción de su suscripción a Azure. |

## Uso de la solución

### Parámetros

Los runbooks tienen los siguientes parámetros. Debe proporcionar valores para los parámetros obligatorios y opcionalmente puede proporcionar valores para otros parámetros dependiendo de sus requisitos.

| Parámetro | Escriba | Obligatorio | Descripción |
|:---|:---|:---|:---|
| ServiceName | cadena | No | Si se proporciona un valor, todas las máquinas virtuales con ese nombre de servicio se inician o se detienen. Si no se proporciona un valor, todas las máquinas virtuales clásicas de la suscripción de Azure se inician o se detienen. |
| AzureSubscriptionIdAssetName | cadena | No | Contiene el nombre del [recurso de variables](#installing-the-solution) que contiene a su vez el identificador de suscripción de su suscripción de Azure. Si no se especifica un valor, se usa *AzureSubscriptionId*. |
| AzureCredentialAssetName | cadena | No | Contiene el nombre del [activo de credenciales](#installing-the-solution) que contiene las credenciales que usará el runbook. Si no se especifica un valor, se usa *AzureCredential*. |

### Inicio de los runbooks

Puede usar cualquiera de los métodos de [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) para iniciar los runbooks de esta solución.

Los siguientes comandos de ejemplo usan Windows PowerShell para ejecutar **StartAzureVMs** para iniciar todas las máquinas virtuales con el nombre del servicio *MyVMService*.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### Salida

Los runbooks [enviarán un mensaje](automation-runbook-output-and-messages.md) para cada máquina virtual indicando si se ha enviado correctamente la instrucción para iniciar o detener. Puede buscar una cadena concreta en la salida para determinar el resultado para cada runbook. En la tabla siguiente se muestran las posibles cadenas de salida.

| Runbook | Condición | Message |
|:---|:---|:---|
| Start-AzureVMs | La máquina virtual ya se está ejecutando. | MyVM ya se está ejecutando. |
| Start-AzureVMs | La solicitud de inicio de la máquina virtual ha enviado correctamente. | Se ha iniciado MyVM. |
| Start-AzureVMs | Se produjo un error en la solicitud de inicio de la máquina virtual. | La MyVM no se pudo iniciar. |
| Stop-AzureVMs | La máquina virtual ya se está ejecutando. | MyVM ya se ha detenido. |
| Stop-AzureVMs | La solicitud de inicio de la máquina virtual ha enviado correctamente. | Se ha iniciado MyVM. |
| Stop-AzureVMs | Se produjo un error en la solicitud de inicio de la máquina virtual. | La MyVM no se pudo iniciar. |

Por ejemplo, el siguiente fragmento de código de un runbook intenta iniciar todas las máquinas virtuales con el nombre de servicio *MyServiceName*. Si se produce un error en las solicitudes de inicio, se pueden realizar acciones de error.

	$results = Start-AzureVMs -ServiceName "MyServiceName"
	foreach ($result in $results) {
		if ($result -like "* has been started" ) {
			# Action to take in case of success.
		}
		else {
			# Action to take in case of error.
		}
	}


## Desglose detallado

Lo siguiente es un desglose detallado de los runbooks de esta solución. Puede usar esta información para personalizar los runbooks o solo para obtener información de ellos.

### Parámetros

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

El flujo de trabajo se inicia al obtener los valores de los [parámetros de entrada](#using-the-solution). Si no se proporcionan los nombres de activos, se usan nombres predeterminados.

### Salida

    # Returns strings with status messages
    [OutputType([String])]

Esta línea declara que la salida del runbook será una cadena. Esto no es necesario, pero es una práctica recomendada para cuando el runbook se usa como un [runbook secundario](automation-child-runbooks.md) para que sepa el tipo de salida puede esperar un runbook primario.

### Autenticación

	# Connect to Azure and select the subscription to work against
	$Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
	$null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
	$SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Las líneas siguientes establecen las [credenciales](automation-configuring.md#configuring-authentication-to-azure-resources) y la suscripción de Azure que se usarán para el resto del runbook. Primero se usa **Get-AutomationPSCredential** para obtener el activo que contiene las credenciales con acceso para iniciar y detener las máquinas virtuales en la suscripción de Azure. **Add-AzureAccount** usa después este activo para establecer las credenciales. La salida se asigna a una variable ficticia para que no se incluya en la salida del runbook.

El activo de variables con el identificador de suscripción se recupera con **Get-AutomationVariable** y la suscripción establecida con **Select-AzureSubscription**.

### Obtención de máquinas virtuales

	# If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
	{ 
		$VMs = Get-AzureVM -ServiceName $ServiceName
	}
    else 
	{ 
		$VMs = Get-AzureVM
	}

**Get-AzureVM** se usa para recuperar las máquinas virtuales con las que funciona el runbook. Si se proporciona un valor en la variable de entrada **ServiceName**, se recuperan solo las máquinas virtuales con ese nombre de servicio. Si **ServiceName** está vacío, se recuperan todas las máquinas virtuales.

### Inicio o detención de máquinas virtuales y envío de resultados

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
		if ($VM.PowerState -eq "Started")
		{
			# The VM is already started, so send notice
			Write-Output ($VM.InstanceName + " is already running")
		}
		else
		{
			# The VM needs to be started
        	$StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

	        if ($StartRtn.OperationStatus -ne 'Succeeded')
	        {
				# The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
	        }
			else
			{
				# The VM started, so send notice
				Write-Output ($VM.InstanceName + " has been started")
			}
		}
    }

Las líneas siguientes recorren cada máquina virtual. Primero se comprueba el **PowerState** de la máquina virtual para ver si se ejecuta o se detiene, dependiendo del runbook. Si ya está en el estado de destino, se envía un mensaje a la salida y el runbook finaliza. Si no lo está, se usa **Start-AzureVM** o **Stop-AzureVM** para intentar iniciar o detener la máquina virtual con el resultado de la solicitud que se almacena en una variable. Después se envía un mensaje a la salida con la especificación de si se envió correctamente la solicitud para iniciar o detener.


## Artículos relacionados

- [Runbooks secundarios en la Automatización de Azure](automation-child-runbooks.md) 
- [Salidas de runbook y mensajes en la Automatización de Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0204_2016-->