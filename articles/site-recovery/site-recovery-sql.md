<properties 
	pageTitle="Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery | Microsoft Azure" 
	description="En este artículo se describe cómo replicar SQL Server mediante Azure Site Recovery, una de las funcionalidades de recuperación ante desastres de SQL Server." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/14/2016" 
	ms.author="raynew"/>


# Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery 


El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).

 En este artículo describe cómo proteger el back-end de SQL Server de una aplicación con una combinación de tecnologías de SQL Server BCDR y Azure Site Recovery. Debe tener una buena comprensión de las funcionalidades de recuperación ante desastres de SQL Server (agrupación en clústeres de conmutación por error, grupos de disponibilidad de AlwaysOn, creación de reflejos de bases de datos, trasvase de registros) y de Azure Site Recovery antes de implementar los escenarios descritos en este artículo.



## Información general

Muchas cargas de trabajo utilizan SQL Server como base. Las aplicaciones como SharePoint, Dynamics y SAP utilizan SQL Server para implementar servicios de datos. Las aplicaciones implementan SQL Server de muchas maneras diferentes:

- **Servidor SQL independiente**: SQL Server y todas las bases de datos se hospedan en una sola máquina (física o virtual). Cuando se virtualiza, la agrupación en clústeres de host se utiliza para conseguir una elevada disponibilidad local. No se implementa alta disponibilidad de nivel de invitado.
- **Instancias de agrupación en clústeres de conmutación por error de SQL Server**: dos o más nodos de instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error de Windows. Si alguna de las instancias de clúster está inactiva, el clúster puede conmutar por error SQL Server a otra instancia. Esta configuración se usa normalmente para obtener alta disponibilidad en el sitio principal. No protege frente a errores o una interrupción en la capa de almacenamiento compartido. El disco compartido se puede implementar con ISCSI, Canal de fibra o VHDx compartido.
- **Grupos de disponibilidad AlwaysOn de SQL**: en esta configuración, se configuran dos nodos en un clúster no compartido con bases de datos SQL Server configuradas en un grupo de disponibilidad con replicación sincrónica y conmutación por error automática.

SQL Server también proporciona tecnologías nativas de recuperación ante desastres en ediciones empresariales para recuperar bases de datos en un sitio remoto. En este artículo, aprovecharemos e integraremos estas tecnologías de recuperación ante desastres SQL nativas:

- Grupos de disponibilidad AlwayOn de SQL para la recuperación ante desastres para ediciones empresariales de SQL 2012 o 2014
- Reflejo de base de datos SQL en modo de alta seguridad para SQL Server Standard Edition (cualquier versión) o para SQL Server 2008 R2


Site Recovery puede proteger SQL Server como se resume en la tabla.

 |**De local a local** | **De local a Azure** 
---|---|---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí 
**Servidor físico** | Sí | Sí


## Compatibilidad e integración

Estas versiones de SQL Server son compatibles con los escenarios de este artículo:


- SQL Server 2014 Enterprise y Standard
- SQL Server 2012 Enterprise y Standard
- SQL Server 2008 R2 Enterprise y Standard


Site Recovery se puede integrar con las tecnologías nativas de SQL Server BCDR resumidas en la tabla siguiente para proporcionar una solución de recuperación ante desastres.

**Característica** |**Detalles** | **Versión de SQL Server** 
---|---|---
**Grupo de disponibilidad AlwaysOn** | Se ejecutan varias instancias independientes de SQL Server, cada una en un clúster de conmutación por error que tiene varios nodos.<br/><br/>Las bases de datos se pueden agrupar en grupos de conmutación por error que se pueden copiar (reflejar) en instancias de SQL Server, de modo que no es necesario ningún almacenamiento compartido.<br/><br/>Proporciona recuperación ante desastres entre un sitio principal y uno o varios sitios secundarios. Dos nodos pueden configurarse en un clúster no compartido con Bases de datos de SQL Server configurado en un grupo de disponibilidad con replicación sincrónica y conmutación por error automática. | SQL Server 2014 y 2012 Enterprise Edition
**Clústeres de conmutación por error (FCI AlwaysOn)** | SQL Server aprovecha la agrupación en clústeres de conmutación por error de Windows para conseguir alta disponibilidad de las cargas de trabajo locales de SQL Server.<br/><br/>Los nodos que ejecutan instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error. Si una instancia está inactiva, el clúster conmuta por error a una diferente.<br/><br/>El clúster no protege frente a errores o interrupciones en el almacenamiento compartido. El disco compartido se puede implementar con iSCSI, canal de fibra o VHDX compartido. | SQL Server Enterprise Edition<br/> <br/>SQL Server Standard Edition (limitada a solo dos nodos)
**Creación de un reflejo de la base de datos (modo de alta seguridad)** | Protege una sola base de datos en una única copia secundaria. Disponible en modos de replicación de seguridad alta (sincrónica) y de alto rendimiento (asincrónica). No requiere un clúster de conmutación por error. | SQL Server 2008 R2<br/><br/>Todas las ediciones de SQL Server Enterprise
**SQL Server independiente** | SQL Server y la base de datos se hospedan en un único servidor (físico o virtual). Los clústeres de host se utilizan para lograr alta disponibilidad, si el servidor virtual. Sin alta disponibilidad de nivel de invitado. | Edición Enterprise o Standard

## Recomendaciones de implementación


En la siguiente tabla se resumen nuestras recomendaciones para integrar las tecnologías de SQL Server BCDR con Site Recovery.

**Versión** |**Edición** | **Implementación** | **De local a local** | **De local a Azure** 
---|---|---|---|---
SQL Server 2014 o 2012 | Enterprise | Instancia de clúster de conmutación por error | Grupos de disponibilidad AlwaysOn | Grupos de disponibilidad AlwaysOn
| Enterprise | Grupos de disponibilidad AlwaysOn para alta disponibilidad | Grupos de disponibilidad AlwaysOn | Grupos de disponibilidad AlwaysOn
 | Standard | Instancia de clúster de conmutación por error (FCI) | Replicación de Site Recovery con un reflejo local | Replicación de Site Recovery con un reflejo local
 | Enterprise o Standard | Independiente | Replicación de Site Recovery | Replicación de Site Recovery
SQL Server 2008 R2 | Enterprise o Standard | Instancia de clúster de conmutación por error (FCI) | Replicación de Site Recovery con un reflejo local | Replicación de Site Recovery con un reflejo local
 | Enterprise o Standard | Independiente | Replicación de Site Recovery | Replicación de Site Recovery
SQL Server (cualquier versión) | Enterprise o Standard | Instancia de clúster de conmutación por error: aplicación de DTC | Replicación de Site Recovery | No compatible


## Requisitos previos de implementación

Esto es lo necesita antes de empezar:

- La implementación local de SQL Server ejecuta una versión compatible de SQL Server. Normalmente también necesitará un Active Directory para SQL server.
- Los requisitos previos para el escenario que desea implementar. Los requisitos previos pueden encontrarse en cada artículo de la implementación. Se proporcionan vínculos a éstos en [¿Qué es Site Recovery?](site-recovery-overview.md).
- Si desea configurar la recuperación de Azure, necesitará ejecutar la herramienta [Evaluación de preparación de máquinas virtuales de Azure](http://www.microsoft.com/download/details.aspx?id=40898) en sus máquinas virtuales de SQL Server para asegurarse de que son compatibles con Azure y Site Recovery.


## Configuración de Active Directory

Necesitará Active Directory en el sitio de recuperación secundario para que SQL Server se ejecute correctamente. Hay dos opciones:

- **Empresas pequeñas**: si tiene un pequeño número de aplicaciones y un solo controlador de dominio para el sitio local y desea conmutar por error todo el sitio, se recomienda utilizar la replicación de Site Recovery para replicar el controlador de dominio en el centro de datos secundario o en Azure.

- **Empresas medianas y grandes**: si tiene un gran número de aplicaciones, ejecuta un bosque de Active Directory y desea que la conmutación por error se realice por aplicaciones o cargas de trabajo, se recomienda configurar un controlador de dominio adicional en el centro de datos secundario o en Azure. Tenga en cuenta que si utiliza grupos de disponibilidad AlwaysOn para recuperar en un sitio remoto se recomienda configurar otro controlador de dominio adicional en el sitio secundario o en Azure, que se utilizará para la instancia de SQL Server recuperada.

Las instrucciones de este documento suponen que un controlador de dominio está disponible en la ubicación secundaria. [Más información](site-recovery-active-directory.md) sobre la protección de Active Directory con Site Recovery.

## Integración de la protección con SQL Server Always-On (del entorno local a Azure)

### Protección de máquinas virtuales de Hyper-V en nubes de VMM

Site Recovery admite SQL AlwaysOn de forma nativa. Si creó un grupo de disponibilidad de SQL con una máquina virtual de Azure configurada como "secundaria", puede usar Site Recovery para administrar la conmutación por error de los grupos de disponibilidad.

>[AZURE.NOTE] Esta funcionalidad está actualmente en vista previa y está disponible cuando los servidores host de Hyper-V del centro de datos principal se administran en nubes de VMM.

#### Requisitos previos

A continuación se indica lo que necesita para integrar SQL AlwaysOn con Site Recovery cuando se está replicando desde VMM:

- Una instancia de SQL Server local (en un servidor independiente o un clúster de conmutación por error).
- Una o más máquinas virtuales de Azure con SQL Server instalado
- Un grupo de disponibilidad de SQL configurado entre la instancia de SQL Server local y la instancia de SQL Server que se ejecuta en Azure.
- El acceso remoto a PowerShell debe estar habilitado en la máquina de SQL Server local. El servidor VMM debe poder realizar llamadas remotas de PowerShell a SQL Server.
- Se debe agregar una cuenta de usuario en la instancia de SQL Server local, en estos grupos de usuarios de SQL con al menos estos permisos:
	- ALTER AVAILABILITY GROUP: los permisos se describen [aquí](https://msdn.microsoft.com/library/hh231018.aspx) y [aquí](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3).
	- ALTER DATABASE: los permisos se describen [aquí](https://msdn.microsoft.com/library/ff877956.aspx#Security).
- Se debe crear una cuenta RunAs en el servidor VMM para la cuenta del paso anterior.
- Se debe instalar el módulo PS de SQL en los servidores SQL que se ejecutan en el entorno local y en máquinas virtuales de Azure.
- Se debe instalar el agente de máquina virtual en las máquinas virtuales que se ejecutan en Azure.
- NTAUTHORITY\\System debe tener los siguientes permisos en la instancia de SQL Server que se ejecuta en las máquinas virtuales en Azure:
	- ALTER AVAILABILITY GROUP: los permisos se describen [aquí](https://msdn.microsoft.com/library/hh231018.aspx) y [aquí](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3).
	- ALTER DATABASE: los permisos se describen [aquí](https://msdn.microsoft.com/library/ff877956.aspx#Security).

####  Paso 1: Incorporación de un servidor SQL Server


1. Haga clic en **Agregar SQL** para agregar un nuevo servidor SQL Server. 

	![Agregar SQL](./media/site-recovery-sql/add-sql.png)

2. En **Configurar opciones de SQL** > **Nombre**, proporcione un nombre descriptivo para referirse al servidor SQL Server.
3. En **SQL Server (FQDN)**, especifique el FQDN del servidor SQL Server de origen que quiere agregar. En caso de que el servidor SQL Server se instale en un clúster de conmutación por error, proporcione el FQDN del clúster y no el de cualquiera de los nodos del clúster.  
4. En **Instancia de SQL Server**, elija la instancia predeterminada o proporcione el nombre de la instancia personalizada.
5. En **Servidor VMM**, seleccione un servidor VMM registrado en el almacén de Site Recovery. Site Recovery utiliza este servidor VMM para comunicarse con el servidor SQL Server.
6. En **Cuenta de ejecución**, proporcione el nombre de una cuenta RunAs que se creó en el servidor VMM especificado. Esta cuenta se usa para tener acceso a SQL Server y debe tener permisos de lectura y de conmutación por error en los grupos de disponibilidad en el servidor SQL Server.

	![Agregar diálogo SQL](./media/site-recovery-sql/add-sql-dialog.png)

Después de agregar el servidor SQL Server este aparecerá en la pestaña **Servidores SQL**.

![Lista SQL Server](./media/site-recovery-sql/sql-server-list.png)


#### Paso 2: Incorporación de un grupo de disponibilidad de SQL

1. Después de agregar la máquina de SQL Server, el paso siguiente es agregar los grupos de disponibilidad a Site Recovery. Para ello, profundice dentro del servidor SQL Server que agregó en el paso anterior y haga clic en Agregar un grupo de disponibilidad de SQL. 

	![Agregar SQL AG](./media/site-recovery-sql/add-sqlag.png)

2. El grupo de disponibilidad de SQL se puede replicar en una o varias máquinas virtuales en Azure. Al agregar el grupo de disponibilidad de SQL se le pide que proporcione el nombre y la suscripción de la máquina virtual de Azure donde desea que Site Recovery realice la conmutación por error del grupo de disponibilidad.

	![Agregar diálogo SQL AG](./media/site-recovery-sql/add-sqlag-dialog.png)

3. En el ejemplo anterior el grupo de disponibilidad DB1-AG se convertirá en principal en la máquina virtual SQLAGVM2 que se ejecuta dentro de la suscripción DevTesting2 en caso de una conmutación por error.

>[AZURE.NOTE] Solamente los grupos de disponibilidad que son principales en el servidor SQL Server que agregó en el paso anterior están disponibles para agregarse a Site Recovery. Si convirtió a un grupo de disponibilidad en principal en el servidor SQL Server, o si agrega más grupos de disponibilidad en el servidor SQL Server después una vez agregado, realice una actualización con la opción que está disponible para ello en SQL Server.

#### Paso 3: Creación de un plan de recuperación

El siguiente paso es crear un plan de recuperación con las máquinas virtuales y los grupos de disponibilidad. Seleccione el mismo servidor VMM que usó en el paso 1 como origen y Microsoft Azure como destino.

![Creación del plan de recuperación](./media/site-recovery-sql/create-rp1.png)

![Creación del plan de recuperación](./media/site-recovery-sql/create-rp2.png)

En el ejemplo la aplicación de Sharepoint consta de 3 máquinas virtuales que usan un grupo de disponibilidad de SQL como su back-end. En este plan de recuperación podríamos seleccionar tanto el grupo de disponibilidad como la máquina virtual que constituyen la aplicación.

Puede personalizar aún más el plan de recuperación moviendo las máquinas virtuales a diferentes grupos de conmutación por error para secuenciar el orden de conmutación por error. Grupo de disponibilidad siempre se conmuta primero ya que se usará como un back-end de cualquier aplicación.

![Personalización de planes de recuperación](./media/site-recovery-sql/customize-rp.png)

### Paso 4: Conmutación por error

Una vez que se agrega un grupo de disponibilidad a un plan de recuperación hay diferentes opciones de conmutación por error disponibles.

Conmutación por error | Detalles
--- | ---
**Conmutación por error planeada** | Una conmutación por error planeada supone una conmutación sin pérdida de datos. Para lograrlo, el modo de disponibilidad del grupo de disponibilidad de SQL se establece primero en sincrónico y, a continuación, se desencadena una conmutación por error para convertir el grupo de disponibilidad en principal en la máquina virtual proporcionada, al tiempo que se agrega el grupo de disponibilidad a Site Recovery. Una vez completada la conmutación por error, el modo de disponibilidad se establece con el mismo valor que tenía antes de que se desencadenara la conmutación planeada.
**Conmutación por error no planeada** | Una conmutación por error imprevista puede dar lugar a una pérdida de datos. Al desencadenar una conmutación por error que no está planeada, el modo de disponibilidad del grupo de disponibilidad no se cambia y se convierte en principal en la máquina virtual proporcionada al agregar el grupo de disponibilidad a Site Recovery. Una vez que la conmutación por error imprevista se completa y el servidor local que ejecuta SQL Server vuelve a estar disponible, es necesario desencadenar una replicación inversa en el grupo de disponibilidad. Tenga en cuenta que esta acción no está disponible en el plan de recuperación y se puede realizar en el grupo de disponibilidad de SQL en la pestaña de servidores SQL Server
**Conmutación por error de prueba** | La conmutación por error de prueba no se admite para los grupo de disponibilidad de SQL. Si se desencadena una conmutación por error de prueba de un plan de recuperación que contenga un grupo de disponibilidad de SQL, se omitirá la conmutación por error para el grupo de disponibilidad.


Considere las siguientes opciones de conmutación por error.

Opción | Detalles
--- | ---
**Opción 1** | 1\. Realice una conmutación por error de prueba de la aplicación y de los niveles de front-end.<br/><br/>2. Actualice la capa de aplicación para obtener acceso a la copia de réplica en modo de solo lectura y realizar una prueba de solo lectura de la aplicación.
**Opción 2** | 1\. Cree una copia de la instancia de máquina virtual de SQL Server de réplica (mediante el clon de VMM para la copia de seguridad de Azure o de sitio a sitio) y muéstrela en una red de prueba.<br/><br/> 2. Realice la conmutación por error de prueba con el plan de recuperación.

Paso 5: Conmutación por recuperación

Si desea que el grupo de disponibilidad vuelva a ser principal en el servidor local de SQL Server, puede hacerlo desencadenando una conmutación por error planeada en el plan de recuperación y eligiendo la dirección de Microsoft Azure al servidor VMM local.

>[AZURE.NOTE] Después de una conmutación por error no planeada tiene que desencadenarse una replicación inversa en el grupo de disponibilidad para continuar con la replicación. Hasta que esto se realiza la replicación permanece suspendida.



### Protección de máquinas sin VMM

Para los entornos que no están administrados por un servidor VMM, se pueden usar los runbooks de Automatización de Azure para configurar una conmutación por error con script de los grupos de disponibilidad de SQL. A continuación se muestran los pasos a seguir para esta configuración:

1.	Cree un archivo local para que el script realice la conmutación por error de un grupo de disponibilidad. Este script de ejemplo especifica una ruta de acceso al grupo de disponibilidad en la réplica de Azure y realiza la conmutación por error a esa instancia de réplica. Este script se ejecutará en la máquina virtual de réplica de SQL Server y se pasará con la extensión de script personalizado.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Cargue el script en un blob en una cuenta de Almacenamiento de Azure. Use este ejemplo:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Cree un runbook de automatización de Azure para invocar los scripts en la máquina virtual de réplica de SQL Server en Azure. Utilice este script de ejemplo para ello. [Obtenga más información](site-recovery-runbook-automation.md) sobre el uso de runbooks de automatización en los planes de recuperación.

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	Al crear un plan de recuperación para la aplicación, agregue un paso de script "pre-Group 1 boot" que invoca el runbook de automatización para realizar la conmutación por error de grupos de disponibilidad.

## Integración de la protección con SQL AlwaysOn (de local a local)

Si el servidor SQL Server utiliza grupos de disponibilidad para alta disponibilidad o una instancia de clúster de conmutación por error, se recomienda utilizar también grupos de disponibilidad en el sitio de recuperación. Tenga en cuenta de que esta guía es para aplicaciones que no utilizan transacciones distribuidas.

1. [Configure bases de datos](https://msdn.microsoft.com/library/hh213078.aspx) en grupos de disponibilidad.
2. Cree una nueva red virtual en el sitio secundario.
3. Configure una VPN de sitio a sitio entre la nueva red virtual y el sitio principal.
4. Cree una máquina virtual en el sitio de recuperación e instale SQL Server en él.
5. Amplíe los grupos de disponibilidad AlwaysOn existentes a la nueva máquina virtual de SQL Server. Configure esta instancia de SQL Server como una copia de réplica asincrónica.
6. Cree un agente de escucha del grupo de disponibilidad o actualice el agente de escucha existente para incluir la máquina virtual de réplica asincrónica.
7. Asegúrese de que la granja de aplicaciones está configurada con el agente de escucha. Si realiza la configuración mediante el nombre del servidor de la base de datos, actualícelo para utilizar el agente de escucha para que no tenga que volver a configurar después de la conmutación por error.

Para las aplicaciones que usan transacciones distribuidas, le recomendamos usar [Site Recovery con la replicación de SAN](site-recovery-vmm-san.md) o la [replicación de sitio a sitio de servidor físico/VMWare](site-recovery-vmware-to-vmware.md).

### Consideraciones del plan de recuperación

1. Agregue este script de ejemplo a la biblioteca de VMM en los sitios principales y secundarios.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Al crear un plan de recuperación para la aplicación, agregue un paso de script "pre-Group 1 boot" que invoca el script para realizar la conmutación por error de grupos de disponibilidad.



## Protección de un servidor SQL Server independiente

En esta configuración, se recomienda que utilizar la replicación de Site Recovery para proteger el equipo de SQL Server. Los pasos exactos dependerán de si SQL Server está configurado como una máquina virtual o un servidor físico y si desea replicar en Azure o un sitio local secundario. Obtenga instrucciones para todos los escenarios de implementación en [Información general sobre Site Recovery](site-recovery-overview.md).


## Protección de un clúster de SQL Server (Standard o 2008 R2)

Para un clúster que ejecuta SQL Server Standard Edition o SQL Server 2008 R2, se recomienda utilizar la replicación de Site Recovery para proteger SQL Server.

### De local a local

- Si la aplicación usa transacciones distribuidas, se recomienda implementar [Site Recovery con la replicación de SAN](site-recovery-vmm-san.md) en caso de un entorno de Hyper-V y [VMware/servidor físico a VMware](site-recovery-vmware-to-vmware.md) si se trata de un entorno VMware.

- Para las aplicaciones que no sean DTC, aproveche el enfoque anterior para recuperar el clúster como un servidor independiente mediante el uso de un reflejo de la base de datos local de seguridad alta.

### De local a Azure

Site Recovery no admite la compatibilidad con clústeres de invitado al replicar en Azure. SQL Server tampoco proporciona una solución de recuperación ante desastres de bajo costo para la edición Standard. Se recomienda proteger el clúster de SQL Server local en un servidor SQL Server independiente y recuperarla en Azure.


1. Configure una instancia de SQL Server independiente adicional en el sitio local.
2. Configure esta instancia para actuar como un reflejo para las bases de datos que deben protegerse. Configure el reflejo en modo de alta seguridad.
3.	Configure Site Recovery en el sitio local en función del entorno ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware/servidor físico](site-recovery-vmware-to-azure-classic.md).
4.	Utilice la replicación de Site Recovery para replicar la nueva instancia de SQL Server en Azure. Es una copia de seguridad alta de reflejo, por lo que se sincronizará con el clúster principal, pero se puede replicar en Azure con la replicación de Site Recovery.

El gráfico siguiente muestra esta configuración.

![Clúster estándar](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Consideraciones de la conmutación por recuperación

Para los clústeres SQL estándar, la conmutación por recuperación después de una conmutación por error no planeada requiere realizar una copia de seguridad de SQL y una restauración a partir de la instancia de reflejo en el clúster original y, a continuación, restablecer el reflejo.

## Pasos siguientes
[Más información](site-recovery-best-practices.md) sobre cómo prepararse para implementar Site Recovery.










 

<!---HONumber=AcomDC_0218_2016-->