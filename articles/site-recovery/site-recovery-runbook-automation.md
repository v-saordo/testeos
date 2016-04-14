<properties 
   pageTitle="Incorporación de runbooks de automatización de Azure a los planes de recuperación | Microsoft Azure" 
   description="Este artículo describe cómo Azure Site Recovery ahora le permite ampliar los planes de recuperación mediante Automatización de Azure para completar tareas complejas durante la recuperación en Azure" 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="12/14/2015"
   ms.author="ruturajd@microsoft.com"/>


# Incorporación de runbooks de automatización de Azure a los planes de recuperación


Este tutorial describe cómo se integra Azure Site Recovery con Automatización de Azure para proporcionar extensibilidad a los planes de recuperación. Los planes de recuperación pueden coordinar la recuperación de las máquinas virtuales protegidas mediante Azure Site Recovery para escenarios de replicación en la nube secundaria y replicación en Azure. También ayudan a realizar la recuperación **coherente y precisa**, **repetible** y **automatizada**. Si conmuta por error las máquinas virtuales en Azure, la integración con Automatización de Azure amplía los planes de recuperación y le ofrece la capacidad de ejecutar runbooks, lo que proporciona tareas de automatización eficaces.

Si aún no ha oído hablar de Automatización de Azure, suscríbase [aquí](https://azure.microsoft.com/services/automation/) y descargue sus scripts de ejemplo [aquí](https://azure.microsoft.com/documentation/scripts/). Obtenga más información sobre [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) y cómo llevar a cabo la recuperación en Azure con los planes de recuperación [aquí](https://azure.microsoft.com/blog/?p=166264).

En este tutorial, veremos cómo se pueden integrar runbooks de Automatización de Azure en planes de recuperación. Automatizaremos tareas sencillas que anteriormente requerían una intervención manual y veremos cómo convertir una recuperación de varios paso en una acción de recuperación de un solo clic. También veremos cómo puede solucionar problemas de un script sencillo si algo va mal.

## Protección de la aplicación en Azure

Comenzaremos con una aplicación simple que consta de dos máquinas virtuales. En este caso, tenemos una aplicación HRweb de Fabrikam. Fabrikam-HRweb-frontend y Fabrikam-Hrweb-backend son dos máquinas virtuales protegidas en Azure mediante Azure Site Recovery. Para proteger las máquinas virtuales con la Azure Site Recovery, siga estos pasos.

1.  Habilite la protección para las máquinas virtuales.

2.  Asegúrese de que las máquinas virtuales han completado la replicación inicial y se replican.

3.  Espere hasta que se complete la replicación inicial y el estado de replicación indique Protegido.

![](media/site-recovery-runbook-automation/01.png)
---------------------

En este tutorial, se creará un plan de recuperación para la aplicación de Fabrikam HRweb para la conmutación por error de la aplicación en Azure. A continuación, se integrará con un runbook que va a crear un extremo en la máquina virtual de Azure conmutada por error para servir páginas web en el puerto 80.

En primer lugar, vamos a crear un plan de recuperación para nuestra aplicación.

## Creación del plan de recuperación

Para recuperar la aplicación en Azure, deberá crear un plan de recuperación. Mediante un plan de recuperación, puede especificar el orden de recuperación de las máquinas virtuales. La máquina virtual colocada en el grupo 1 se recupera y se inicia en primer lugar y, a continuación, seguirá la máquina virtual en el grupo 2.

Crear un plan de recuperación como el siguiente.

![](media/site-recovery-runbook-automation/12.png)

Para obtener más información acerca de los planes de recuperación, lea la documentación [aquí](https://msdn.microsoft.com/library/azure/dn788799.aspx "aquí").

A continuación, vamos a crear los artefactos necesarios en Automatización de Azure.

## Creación de la cuenta de automatización y sus activos

Necesita una cuenta de Automatización de Azure para crear runbooks. Si aún no dispone de una cuenta, navegue a la pestaña Automatización de Azure indicada en![](media/site-recovery-runbook-automation/02.png)y cree una nueva cuenta.

1.  Asigne un nombre a la cuenta para identificarla.

2.  Especifique la región geográfica donde desea colocar la cuenta.

Se recomienda colocar la cuenta en la misma región que el almacén de ASR.

![](media/site-recovery-runbook-automation/03.png)

A continuación, cree los siguientes activos en la cuenta.

### Incorporación de un nombre de suscripción como activo

1.  Agregue una nueva opción ![](media/site-recovery-runbook-automation/04.png) en los activos de Automatización de Azure y seleccione ![](media/site-recovery-runbook-automation/05.png)

2.  Seleccione el tipo de variable como **Cadena**

3.  Especifique el nombre de la variable como **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Especifique el nombre real de la suscripción de Azure como el valor de la variable.

	![](media/site-recovery-runbook-automation/07_1.png)

Puede identificar el nombre de la suscripción desde la página de configuración de la cuenta en el portal de Azure.

### Incorporación de una credencial de inicio de sesión de Azure como activo

Automatización de Azure usa Azure PowerShell para conectarse a la suscripción y funciona en los artefactos ahí. Para ello, deberá autenticarse con su cuenta de Microsoft o una cuenta profesional o educativa. Puede almacenar las credenciales de la cuenta en un activo para que el runbook la use de forma segura.

1.  Agregue una nueva opción ![](media/site-recovery-runbook-automation/04.png) en los activos de Automatización de Azure y seleccione ![](media/site-recovery-runbook-automation/09.png)

2.  Seleccione el tipo de credencial como **Credencial de Windows PowerShell**

3.  Especifique el nombre como **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Especifique el nombre de usuario y la contraseña con los que iniciar sesión.

Ahora ambas configuraciones están disponibles en los activos.

![](media/site-recovery-runbook-automation/11.png)

Se proporciona más información acerca de cómo conectarse a su suscripción mediante powershell [aquí](../install-configure-powershell.md).

A continuación, creará un runbook en Automatización de Azure que puede agregar un extremo para la máquina virtual front-end después de la conmutación por error.

## Contexto de automatización de Azure

ASR pasa una variable de contexto al runbook para ayudarle a escribir scripts deterministas. Se puede pensar que los nombres del servicio en la nube y la máquina virtual son predecibles, pero no siempre es así debido a ciertas situaciones; por ejemplo, puede darse el caso de que el nombre de la máquina virtual haya cambiado debido a caracteres no admitidos en Azure. Por lo tanto, esta información se pasa al plan de recuperación de ASR como parte del *contexto*.

A continuación se muestra un ejemplo del aspecto de la variable de contexto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


La tabla siguiente contiene el nombre y la descripción de cada variable en el contexto.

**Nombre de la variable** | **Descripción**
---|---
RecoveryPlanName | Nombre del plan que se va a ejecutar. Permite realizar acciones según el nombre mediante el mismo script.
FailoverType | Especifica si la conmutación por error se probó y si se planeó o no. 
FailoverDirection | Especifica si la recuperación es al principal o secundario.
GroupID | Identifica el número de grupo dentro del plan de recuperación cuando se ejecuta el plan.
VmMap | Tabla con todas las máquinas virtuales del grupo.
Clave de VMMap | Clave única (GUID) para cada máquina virtual. Es el mismo que el identificador de VMM de la máquina virtual, si procede. 
RoleName | Nombre de la máquina virtual de Azure que se está recuperando.
CloudServiceName | Nombre del servicio en la nube de Azure con el que se crea la máquina virtual.


Para identificar la clave de VmMap en el contexto también puede ir a la página de propiedades de la máquina virtual en ASR y ver la propiedad VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## Creación de un runbook de Automatización

Ahora cree el runbook para abrir el puerto 80 en la máquina virtual front-end.

1.  Creación de un nuevo runbook en la cuenta de Automatización de Azure con el nombre **OpenPort80**

	![](media/site-recovery-runbook-automation/14.png)

2.  Desplácese a la vista del autor del runbook y entre en el modo de borrador.

3.  Especifique primero la variable que se usará como contexto del plan de recuperación
  
	```
		param (
			[Object]$RecoveryPlanContext
		)

	```

4.  A continuación, conéctese a la suscripción con la credencial y el nombre de la suscripción

	```
		$Cred = Get-AutomationPSCredential -Name 'AzureCredential'
	
		# Connect to Azure
		$AzureAccount = Add-AzureAccount -Credential $Cred
		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
	```

	Tenga en cuenta que utiliza los activos de Azure **AzureCredential** y **AzureSubscriptionName** aquí.

5.  Especifique ahora los detalles del extremo y el GUID de la máquina virtual para la que quiere exponer el punto de conexión. En este caso, la máquina virtual front-end.

	```
		# Specify the parameters to be used by the script
		$AEProtocol = "TCP"
		$AELocalPort = 80
		$AEPublicPort = 80
		$AEName = "Port 80 for HTTP"
		$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
	```

	Esto especifica el protocolo de extremo de Azure, el puerto local en la máquina virtual y su puerto público asignado. Estas variables son parámetros requeridos por los comandos de Azure que agregan extremos a las máquinas virtuales. El VMGUID contiene el GUID de la máquina virtual en la que necesita trabajar.

6.  El script ahora extraerá el contexto para el VM GUID indicado y creará un extremo en la máquina virtual a la que hace referencia.

	```
		#Read the VM GUID from the context
		$VM = $RecoveryPlanContext.VmMap.$VMGUID

		if ($VM -ne $null)
		{
			# Invoke pipeline commands within an InlineScript

			$EndpointStatus = InlineScript {
				# Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
				# Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

				$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
					Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
					Update-AzureVM
				Write-Output $Status
			}
		}
	```

7. Una vez finalizado, presione Publicar ![](media/site-recovery-runbook-automation/20.png) para permitir que el script esté disponible para su ejecución.

El script completo se muestra a continuación para su referencia

```
  workflow OpenPort80
  {
	param (
		[Object]$RecoveryPlanContext
	)

	$Cred = Get-AutomationPSCredential -Name 'AzureCredential'
	
	# Connect to Azure
	$AzureAccount = Add-AzureAccount -Credential $Cred
	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

	# Specify the parameters to be used by the script
	$AEProtocol = "TCP"
	$AELocalPort = 80
	$AEPublicPort = 80
	$AEName = "Port 80 for HTTP"
	$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
	
	#Read the VM GUID from the context
	$VM = $RecoveryPlanContext.VmMap.$VMGUID

	if ($VM -ne $null)
	{
		# Invoke pipeline commands within an InlineScript

		$EndpointStatus = InlineScript {
			# Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
			# This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

			$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
				Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
				Update-AzureVM
			Write-Output $Status
		}
	}
  }
```

## Incorporación del script para el plan de recuperación

Una vez que el script esté listo, puede agregarlo al plan de recuperación que creó anteriormente.

1.  En el plan de recuperación que creó, elija agregar un script después del grupo 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Especifique un nombre de script. Se trata simplemente de un nombre descriptivo para este script que se muestra en el plan de recuperación.

3.  En el script de conmutación por error a Azure, seleccione el nombre de la cuenta de Automatización de Azure.

4.  En los Runbooks de Azure, seleccione el runbook que creó.

![](media/site-recovery-runbook-automation/16.png)

## Scripts principales

Cuando se está ejecutando una conmutación por error en Azure, también puede ejecutar scripts principales. Estos scripts se ejecutarán en el servidor VMM durante la conmutación por error. Los scripts principales solo están disponibles solo para las fases de preapagado y postapagado. Esto es porque se espera que el sitio principal no esté disponible normalmente cuando se produzca un desastre. Durante una conmutación por error no planeada, solo si participa en operaciones de sitio principal, intentará ejecutar los scripts principales. Si no son accesibles o se agota el tiempo de espera, la conmutación por error continuará recuperando las máquinas virtuales. Los scripts principales están sin disponibles para sitios VMware/físicos/Hyper-v sin VMM protegido en Azure: durante la conmutación por error en Azure. Sin embargo, cuando se produce la conmutación por recuperación de Azure en local, los scripts principales (Runbooks) pueden utilizarse para todos los destinos excepto VMware.

## Prueba del plan de recuperación

Una vez que haya agregado el runbook al plan puede iniciar una conmutación por error de prueba y verlo en acción. Siempre se recomienda ejecutar una conmutación por error de prueba para probar la aplicación y el plan de recuperación para asegurarse de que no hay ningún error.

1.  Seleccione el plan de recuperación e inicie una conmutación por error de prueba.

2.  Durante la ejecución del plan, puede ver si se ha ejecutado el runbook o no según su estado.

    ![](media/site-recovery-runbook-automation/17.png)

3.  También puede ver el estado de ejecución del runbook detallado en la página de tareas de Automatización de Azure para el runbook.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Una vez completada la conmutación por error, además del resultado de la ejecución de runbook, puede ver si la ejecución es correcta o no al visitar la página de la máquina virtual de Azure y observar los extremos.

![](media/site-recovery-runbook-automation/19.png)

## Scripts de ejemplo

Mientras describimos la automatización de la tarea habitual de agregar un extremo a una máquina virtual de Azure en este tutorial, podría hacer una serie de otras tareas de automatización eficaces mediante la Automatización de Azure. Microsoft y la comunidad de Automatización de Azure proporcionan runbooks de ejemplos, que pueden ayudarle a empezar a crear sus propias soluciones, y runbooks de utilidades, que puede usar como bloques de creación para tareas de automatización más grandes. Comience a utilizarlos desde la galería y cree planes de recuperación de un solo clic eficaces para las aplicaciones con Azure Site Recovery.

## Recursos adicionales

[Información general sobre Automatización de Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Información general sobre Automatización de Azure")

[Scripts de Automatización de Azure de ejemplo](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Scripts de Automatización de Azure de ejemplo")

 

<!---HONumber=AcomDC_0128_2016-->