<properties 
	pageTitle="Inicio y detención de máquinas virtuales - gráfico | Microsoft Azure"
	description="Versión del flujo de trabajo de PowerShell de la solución Automatización de Azure que incluye runbooks para iniciar y detener las máquinas virtuales clásicas."
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

Esta es la versión de runbook gráfico de esta solución. Está también disponible con [los runbooks de flujo de trabajo de PowerShell](automation-solution-startstopvm-psworkflow.md).

## Obtención de la solución

Esta solución consta de dos runbooks gráficos que se pueden descargar en los vínculos siguientes. Consulte la [versión de flujo de trabajo de PowerShell](automation-solution-startstopvm-psworkflow.md) de esta solución para obtener vínculos a los runbooks de flujo de trabajo de PowerShell.


| Runbook | Vínculo | Escriba | Descripción |
|:---|:---|:---|:---|
| StartAzureClassicVM | [Runbook gráfico de inicio de máquinas virtuales clásicas de Azure](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) | Gráfico | Inicia todas las máquinas virtuales clásicas de una suscripción de Azure o todas las máquinas virtuales con un nombre de servicio determinado. |
| StopAzureClassicVM | [Runbook gráfico de detención de máquinas virtuales clásicas de Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) | Gráfico | Detiene todas las máquinas virtuales de una cuenta de automatización o todas las máquinas virtuales con un nombre de servicio determinado. |


## Instalación de la solución

### 1\. Instalación de los runbooks

Después de descargar los runbooks, puede importarlos mediante el procedimiento descrito en [Procedimientos de runbook gráficos](automation-graphical-authoring-intro.md#graphical-runbook-procedures).

### 2\. Revisión de la descripción y los requisitos
Los runbooks tienen una actividad llamada **Léame** que incluye una descripción y los recursos necesarios. Puede ver esta información seleccionando la actividad **Léame** y, a continuación, el parámetro de **secuencia de comandos de flujo de trabajo**. También puede obtener la misma información en este artículo.

### 3\. Configuración de activos
Los runbooks requieren los siguientes recursos que se tienen que crear y rellenar con los valores adecuados. Los nombres son predeterminados. Puede utilizar recursos con nombres diferentes si especifica dichos nombres en los [parámetros de entrada](#using-the-solution) al iniciar el runbook.

| Tipo de recurso | Nombre predeterminado | Descripción |
|:---|:---|:---|:---|
| [Credential:](automation-credentials.md) | AzureCredential | Contiene las credenciales de una cuenta que tiene autoridad para iniciar y detener las máquinas virtuales en la suscripción de Azure. |
| [Variable](automation-variables.md) | AzureSubscriptionId | Contiene el identificador de suscripción de su suscripción a Azure. |

## Uso de la solución

### Parámetros

Cada runbook tiene los siguientes [parámetros de entrada](automation-starting-a-runbook#runbook-parameters). Tiene que proporcionar valores para los parámetros obligatorios y opcionalmente puede proporcionar valores para otros parámetros dependiendo de sus necesidades.

| Parámetro | Escriba | Obligatorio | Descripción |
|:---|:---|:---|:---|
| ServiceName | cadena | No | Si se proporciona un valor, todas las máquinas virtuales con ese nombre de servicio se inician o se detienen. Si no se proporciona un valor, todas las máquinas virtuales clásicas de la suscripción de Azure se inician o se detienen. |
| AzureSubscriptionIdAssetName | cadena | No | Contiene el nombre del [recurso de variables](#installing-the-solution) que contiene a su vez el identificador de suscripción de su suscripción de Azure. Si no se especifica un valor, se usa *AzureSubscriptionId*. |
| AzureCredentialAssetName | cadena | No | Contiene el nombre del [activo de credenciales](#installing-the-solution) que contiene las credenciales que usará el runbook. Si no se especifica un valor, se usa *AzureCredential*. |

### Inicio de los runbooks

Puede usar cualquiera de los métodos de [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) para iniciar los runbooks de esta solución.

Los siguientes comandos de ejemplo usan Windows PowerShell para ejecutar **StartAzureClassicVM** para iniciar todas las máquinas virtuales con el nombre del servicio *MyVMService*.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### Salida

Los runbooks [enviarán un mensaje](automation-runbook-output-and-messages.md) para cada máquina virtual indicando si se ha enviado correctamente la instrucción para iniciar o detener. Puede buscar una cadena concreta en la salida para determinar el resultado para cada runbook. En la tabla siguiente se muestran las posibles cadenas de salida.

| Runbook | Condición | Message |
|:---|:---|:---|
| StartAzureClassicVM | La máquina virtual ya se está ejecutando | MyVM ya se está ejecutando. |
| StartAzureClassicVM | La solicitud de inicio de la máquina virtual ha enviado correctamente | Se ha iniciado MyVM |
| StartAzureClassicVM | Se produjo un error en la solicitud de inicio de la máquina virtual | MyVM no se pudo iniciar |
| StopAzureClassicVM | La máquina virtual ya se está ejecutando | MyVM ya se ha detenido. |
| StopAzureClassicVM | La solicitud de inicio de la máquina virtual ha enviado correctamente | Se ha iniciado MyVM |
| StopAzureClassicVM | Se produjo un error en la solicitud de inicio de la máquina virtual | MyVM no se pudo iniciar |


A continuación hay una imagen del uso de **StartAzureClassicVM** como un [runbook secundario](automation-child-runbooks.md) en un runbook gráfico de muestra. Aquí se usan los vínculos condicionales en la tabla siguiente.

| Vínculo | Criterios |
|:---|:---|
| Vínculo de éxito | $ActivityOutput ['StartAzureClassicVM']-like "* se ha iniciado" |
| Vínculo de error | $ActivityOutput ['StartAzureClassicVM']-notlike "* se ha iniciado" |

![Ejemplo de runbook secundario](media/automation-solution-startstopvm/graphical-childrunbook-example.png)


## Desglose detallado

Lo siguiente es un desglose detallado de los runbooks de esta solución. Puede usar esta información para personalizar los runbooks o solo para aprender de ellos para crear sus propias soluciones.
 

### Autenticación

![Autenticación](media/automation-solution-startstopvm/graphical-authentication.png)

El runbook se inicia con actividades que establecen las [credenciales](automation-configuring.md#configuring-authentication-to-azure-resources) y la suscripción de Azure que se usarán para el resto del runbook.

Las dos primeras actividades, **Get Subscription Id** y **Get Azure Credential**, recuperan los [recursos](#installing-the-solution) que se usan en las dos siguientes actividades. Dichas actividades podrían especificar directamente los recursos, pero necesitan los nombres de recurso. Puesto que se permite que el usuario pueda especificar dichos nombres en los [parámetros de entrada](#using-the-solution), necesitamos estas actividades para recuperar los recursos con el nombre que se especificó en el parámetro de entrada.

**Add-AzureAccount** establece las credenciales que se utilizarán para el resto del runbook. El recurso de credencial que recupera con **Get Azure Credential** tiene que tener acceso para iniciar y detener las máquinas virtuales en la suscripción de Azure. La suscripción que se usa la selecciona **Select-AzureSubscription** que utiliza el identificador de suscripción de **Get Subscription Id**.

### Obtención de máquinas virtuales

![Get VMs](media/automation-solution-startstopvm/graphical-getvms.png)

El runbook tiene que determinar las máquinas virtuales con las que va a trabajar y si ya está iniciadas o detenidas (según el runbook del que se trate). Una de dos actividades recuperará las máquinas virtuales. **Get VMs in Service** se ejecutará si el parámetro de entrada *ServiceName* para el runbook contiene un valor. **Get All VMs** se ejecutará si el parámetro de entrada *ServiceName* para el runbook no contiene ningún valor. Esta lógica se realiza mediante los vínculos condicionales que preceden a cada actividad.

Ambas actividades utilizan el cmdlet **Get-AzureVM**. **Get All VMs** utiliza el parámetro **ListAllVMs** que se establece para devolver todas las máquinas virtuales. **Get VMs in Service** utiliza el conjunto de parámetros **GetVMByServiceAndVMName** y proporciona el parámetro de entrada **ServiceName** para el parámetro **ServiceName**.

### Combinación de máquinas virtuales

![Combinación de máquinas virtuales](media/automation-solution-startstopvm/graphical-mergevms.png)

La actividad **Merge VMs** es necesaria para proporcionar entrada a **Start-AzureVM** que necesita el nombre de la máquina o máquinas virtuales y sus nombres de servicio para iniciar. Esta entrada puede provenir bien de **Get All VMs** o de **Get VMs in Service**, pero **Start-AzureVM** sólo puede especificar una actividad para su entrada.

La solución consiste en crear una actividad **Merge VMs** que ejecute el cmdlet **Write-Output**. El parámetro **InputObject** para ese cmdlet es una expresión de PowerShell que combina la entrada de las dos actividades anteriores. Solo se ejecutará una de esas actividades, por lo que solo se espera un conjunto de resultados. **Start-AzureVM** puede utilizar estos resultados para sus parámetros de entrada.

### Inicio y detención de máquinas virtuales

![Start VMs](media/automation-solution-startstopvm/graphical-startvm.png) ![Stop VMs](media/automation-solution-startstopvm/graphical-stopvm.png)

Dependiendo del runbook, las siguientes actividades intentan iniciar o detener el runbook mediante las actividades **Start-AzureVM** o **Stop-AzureVM**. Puesto que la actividad está precedida por un vínculo de canalización, se ejecutará una vez para cada objeto devuelto por **Merge VMs**. El vínculo es condicional de modo que la actividad solo se ejecuta si el *estado de ejecución* de la máquina virtual es *Stopped* (detenido) en el caso de **Start-AzureVM** y *Started* (iniciado) para **Stop-AzureVM**. Si esta condición no se cumple, se ejecuta la actividad **Notify Already Started** o **Notify Already Stopped** para enviar un mensaje a través de **Write-Output**.

### Enviar los resultados

![Notify Start VMs](media/automation-solution-startstopvm/graphical-notifystart.png) ![Notify Stop VMs](media/automation-solution-startstopvm/graphical-notifystop.png)

El último paso del runbook es enviar los resultados, tanto si la solicitud de inicio o detención para cada máquina virtual se envió con éxito como si no. Hay otra actividad de **Write-Output** para cada caso, a través de vínculos condicionales se determina cuál se ejecuta. Se ejecutará **Notify VM Started** o **Notify VM Stopped** si *OperationStatus* es *Succeeded* (exitoso). Si *OperationStatus* tiene cualquier otro valor, se ejecutará **Notify Failed To Start** o **Notify Failed to Stop**.


## Artículos relacionados

- [Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md)
- [Runbooks secundarios en la Automatización de Azure](automation-child-runbooks.md) 
- [Salidas de runbook y mensajes en la Automatización de Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0204_2016-->