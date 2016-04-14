<properties
	pageTitle="Crear planes de recuperación | Microsoft Azure" 
	description="Cree planes de recuperación con Azure Site Recovery para conmutar por error y recuperar grupos de máquinas virtuales y servidores físicos." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/01/2016" 
	ms.author="raynew"/>

# Creación de planes de recuperación

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).


## Información general

En este artículo se proporciona información sobre cómo crear y personalizar planes de recuperación.

Los planes de recuperación se componen de uno o varios grupos ordenados que contienen máquinas virtuales o servidores físicos que deben conmutar por error de manera conjunta. Los planes de recuperación realizan las siguientes operaciones:

- Definen los grupos de máquinas que conmutan por error y se inician juntas.
- Modelan las dependencias entre máquinas agrupándolas en un grupo de planes de recuperación. Por ejemplo, si desea conmutar por error y abrir una aplicación específica, tendrá que agrupar las máquinas virtuales de dicha aplicación en el mismo grupo de planes de recuperación.
- Automatizan y extienden la conmutación por error. Puede ejecutar una conmutación por error de prueba, planeada o no planeada en un plan de recuperación. Los planes de recuperación se pueden personalizar con scripts, automatización de Azure y acciones manuales.

Los planes de recuperación se muestran en **Planes de recuperación** en el portal de Site Recovery.


Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Antes de comenzar

Tenga en cuenta lo siguiente:

- Un plan de recuperación no debe mezclar las máquinas virtuales con uno o varios adaptadores de red. Esto se debe a que no se permite mezclar estas máquinas virtuales en un servicio en la nube de Azure.
- Puede ampliar los planes de recuperación con scripts y acciones manuales. Tenga en cuenta lo siguiente:
	- Escriba los scripts con Windows PowerShell.
	- Asegúrese de que los scripts usan bloques try-catch para que las excepciones se controlen correctamente. Si hay alguna excepción en el script, deja de funcionar y la tarea se muestra como con errores. Si se produce un error, el resto del script no se ejecutará. Si esto ocurre cuando se ejecuta una conmutación por error no planeada, el plan de recuperación continuará. Si esto ocurre cuando se ejecuta una conmutación por error planeada, el plan de recuperación se detendrá. En ese caso, corrija el script, asegúrese de que se ejecuta según lo previsto y vuelva a ejecutar el plan de recuperación.
	- El comando Write-Host no funciona en un script de plan de recuperación y se produce un error en el script. Si desea crear una salida, cree un script de proxy que, a su vez, ejecute el script principal y asegúrese de que toda la salida se canaliza con el comando >>.
	- El script agota el tiempo de espera si no se devuelve en 600 segundos.
	- Si se escribe algo en STDERR, el script se clasificará como con errores. La información se mostrará en los detalles de ejecución del script.
	- Si utiliza VMM en la implementación, tenga en cuenta que:

		- Los scripts de los planes de recuperación se ejecutan en el contexto de la cuenta de servicio de VMM. Asegúrese de que esta cuenta tiene permisos de lectura en el recurso compartido remoto en el que se encuentra el script y pruebe que el script se ejecuta en el nivel de privilegios de la cuenta de servicio de VMM.
		- Los cmdlets de VMM se entregan en un módulo de Windows PowerShell. El módulo de Windows PowerShell de VMM se instala al instalar la consola VMM. El módulo VMM se puede cargar en el script, para lo que es preciso usar el siguiente comando en el script: Import-Module -Name virtualmachinemanager. [Obtenga más detalles](hhttps://technet.microsoft.com/library/hh875013.aspx).
		- Asegúrese de que tiene al menos un servidor de biblioteca en la implementación de VMM. De forma predeterminada, la ruta de acceso del recurso compartido de biblioteca para un servidor VMM se encuentra localmente en el servidor VMM con el nombre de carpeta MSCVMMLibrary.
		- Si la ruta de acceso del recurso compartido de biblioteca es remota, o local pero no se comparte con MSCVMMLibrary, configure el recurso compartido como se indica a continuación (por ejemplo, con \\libserver2.contoso.com\\share\\):
			- Abra el Editor del Registro y navegue hasta HKEY\_LOCAL\_MACHINE\\SOFTWARE\\MICROSOFT\\Azure Site Recovery\\Registration.
			-  Edite el valor ScriptLibraryPath y coloque el valor como \\libserver2.contoso.com\\share. Especifique el nombre de dominio completo. Proporcione permisos a la ubicación del recurso compartido.
			-  Asegúrese de probar el script con una cuenta de usuario que tenga los mismos permisos que la cuenta de servicio de VMM, con el fin de asegurarse de que los scripts independientes probados se ejecutan de la misma forma que lo harán en los planes de recuperación. En el servidor VMM, establezca que se omita la directiva de ejecución como se indica a continuación:
				-  Abra la consola de Windows PowerShell de 64 bits con privilegios elevados.
				-  Escriba: **Set-executionpolicy bypass**. [Obtenga más detalles](https://technet.microsoft.com/library/ee176961.aspx).

## Creación de un plan de recuperación

La forma en que se crea un plan de recuperación depende de la implementación de Site Recovery.

- **Replicación de servidores físicos y máquinas virtuales de VMware**: cree un plan y agregue grupos de replicación que contengan máquinas virtuales y servidores físicos a un plan de recuperación.
- **Replicación de máquinas virtuales de Hyper-V (en nubes VMM)**: cree un plan y agregue máquinas virtuales de Hyper-V de una nube de VMM a un plan de recuperación.
- **Replicación de máquinas virtuales de Hyper-V (en nubes distintas de VMM)**: cree un plan y agregue máquinas virtuales de Hyper-V de un grupo de protección a un plan de recuperación.
- **Replicación de SAN**: cree un plan y agregue un grupo de replicación que contiene máquinas virtuales al plan de recuperación. Seleccione un grupo de replicación, en lugar de máquinas virtuales específicas, ya que todas las máquinas virtuales de un grupo de replicación deben conmutar por error conjuntamente (la conmutación por error se produce en primer lugar en la capa de almacenamiento).


Los planes de recuperación se crean como se indica a continuación:

1. Haga clic en la pestaña **Planes de recuperación** > **Crear plan de recuperación**. Especifique un nombre para el plan de recuperación y un origen y destino. El servidor de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación.

	- Si va a realizar la replicación de VMM a VMM, seleccione **Tipo de origen** > **VMM** y los servidores VMM de origen y destino. Haga clic en **Hyper-V** para ver las nubes configuradas para usar réplica de Hyper-V. 
	- Si va a realizar la replicación de VMM a VMM con SAN, seleccione **Tipo de origen** > **VMM** y los servidores VMM de origen y destino. Haga clic en **SAN** para ver las nubes configuradas para la replicación de SAN.
	- Si va a realizar la replicación de VMM a Azure, seleccione **Tipo de origen** > **VMM**. Seleccione el servidor VMM de origen y **Azure** como destino.
	- Si va a realizar la replicación de un sitio Hyper-V, seleccione **Tipo de origen** > **Sitio Hyper-V**. Seleccione el sitio como origen y **Azure **como destino.
	- Si va a realizar una réplica de VMware o un servidor físico de local a Azure, seleccione un servidor de configuración como el origen y **Azure** como destino.

2. En **Seleccionar máquinas virtuales**, seleccione las máquinas virtuales (o un grupo de replicación) que desea agregar al grupo predeterminado (Grupo 1) del plan de recuperación.

## Personalización de planes de recuperación

Después de haber agregado máquinas virtuales o grupos de replicación protegidos al grupo del plan de recuperación predeterminado y haber creado el plan puede personalizarlo:

- **Agregar grupos nuevos**: puede agregar grupos de planes de recuperación adicionales. Los grupos que agregue se numeran en el orden en que se agregan. Puede agregar hasta siete grupos. Puede agregar más máquinas o grupos de replicación a estos grupos nuevos. Tenga en cuenta que una máquina virtual o un grupo de replicación sólo puede incluirse en el grupo de planes de recuperación.
- **Agregar un script**: puede agregar scripts que se ejecuten antes o después de un grupo de planes recuperación. Cuando se agrega un script, este agrega un nuevo conjunto de acciones al grupo. Por ejemplo, un conjunto de pasos previos para el grupo 1 se creará con el nombre: Grupo 1: pasos previos. Dentro de este conjunto se enumerarán todos los pasos previos. Tenga en cuenta que solo puede agregar un script al sitio principal si hay un servidor VMM implementado.
- **Agregar una acción manual**: puede agregar acciones manuales que se ejecuten antes o después de un grupo de planes de recuperación. Cuando se ejecuta el plan de recuperación, este se detiene en el punto en el que se insertó la acción manual y un cuadro de diálogo le pide que especifique que la acción manual se ha completado.

## Extensión de planes de recuperación con scripts

Puede agregar una script a un plan de recuperación:

- Si tiene un sitio de origen VMM, puede crear un script en el servidor VMM e incluirlo en el plan de recuperación.
- Si va a replicar a Azure, puede integrar runbooks de automatización de Azure en el plan de recuperación

### Creación de un script de VMM


Cree el script como se indica a continuación:

1. Cree una carpeta nueva en el recurso compartido de biblioteca, por ejemplo <nombreDeServidorVMM>\\MSSCVMMLibrary\\RPScripts. Colóquela en los servidores VMM de origen y destino.
2. Cree el script (por ejemplo, RPScript) y compruebe que funciona según lo previsto.
3. Coloque el script en la ubicación <nombreDeServidorVMM>\\MSSCVMMLibrary de los servidores VMM de origen y destino.

### Creación de un runbook de automatización de Azure

El plan de recuperación se puede extender mediante la ejecución de un runbook de automatización de Azure como parte del plan. [Más información](site-recovery-runbook-automation.md).


### Adición de una configuración personalizada a un plan de recuperación

1. Abra el plan de recuperación que desea personalizar.
2. Haga clic en él para agregar una máquina virtual o un grupo nuevos.
3. Para agregar un script o una acción manual, haga clic en cualquiera de los elementos de la lista **Paso** y, a continuación, haga clic en **Script** o **Acción manual**. Especifique si desea agregar el script o la acción antes o después del elemento seleccionado. Utilice los botones de comando **Subir** y **Bajar** para mover la posición del script hacia arriba o hacia abajo.
4. Si va a agregar un script de VMM, seleccione **Conmutación por error en script de VMM** y en **Ruta del script** escriba la ruta de acceso relativa al recurso compartido. Así, en nuestro ejemplo, donde se encuentra en el recurso compartido \<VMMServerName>\\MSSCVMMLibrary\\RPScripts, especifique la ruta de acceso: \\RPScripts\\RPScript.PS1.
5. Si va a agregar un runbook de automatización de Azure, especifique la **cuenta de Automatización de Azure ** en que se encuentra el runbook y seleccione el valor de **Script de runbook de Azure**.
5. Realice una conmutación por error del plan de recuperación para asegurarse de que el script funciona según lo previsto.


## Pasos siguientes

Puede ejecutar diferentes tipos de planes de recuperación de conmutaciones por error, incluyendo una conmutación por error de prueba para comprobar el entorno y una conmutación por error planeada o no planeada. [Más información](site-recovery-failover.md).


 

<!----HONumber=AcomDC_0204_2016-->