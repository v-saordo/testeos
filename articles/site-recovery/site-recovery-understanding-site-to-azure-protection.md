<properties
	pageTitle="Descripción de la replicación de Hyper-V con Azure Site Recovery | Microsoft Azure" 
	description="Use este artículo para comprender los conceptos técnicos que le ayudará a instalar, configurar y administrar correctamente Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="anbacker"/>


# Descripción de la replicación de Hyper-V con Azure Site Recovery

En este artículo se describen los conceptos técnicos que le ayudarán a configurar y administrar correctamente la protección de sitios de Hyper-V o VMM con Azure mediante Azure Site Recovery.

## Descripción de los componentes

### Implementación de sitios de Hyper-V o VMM para la replicación entre el entorno local y Azure.

Como parte de la configuración de la recuperación ante desastres entre el sitio local local y Azure; es necesario descargar e instalar el proveedor de Azure Site Recovery Provider en el servidor VMM junto con el agente de Servicios de recuperación de Azure que debe instalarse en cada host de Hyper-V.

![Implementación del sitio de VMM para la replicación entre el sitio local y Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

La implementación de sitios de Hyper-V es igual que la implementación de VMM, la única diferencia es el proveedor y el agente que se instalan en el propio host de Hyper-V.

## Los flujos de trabajo

### Habilitar protección
Una vez que protege una máquina virtual en el portal o de forma local, se inicia un trabajo de ASR denominado *Habilitar protección* que se puede supervisar en la pestaña TRABAJOS.

![Solucionar problemas de Hyper-V locales](media/site-recovery-understanding-site-to-azure-protection/image01.png)

El trabajo *Habilitar protección* comprueba los requisitos previos antes de invocar a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), que crea la replicación en Azure mediante las entradas configuradas durante la protección. El trabajo *Habilitar protección* comienza la replicación inicial del entorno local mediante la invocación de [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), que envía los discos virtuales de la máquina virtual a Azure.

![Solucionar problemas de Hyper-V locales](media/site-recovery-understanding-site-to-azure-protection/image02.png)

### Finalizar la protección
Cuando se desencadena la replicación inicial, se toma una [instantánea de la máquina virtual de Hyper-V](https://technet.microsoft.com/library/dd560637.aspx). Los discos duros virtuales se procesan uno a uno hasta que todos los discos se cargan en Azure. Normalmente esta operación tarda un rato en completarse en función del tamaño del disco y el ancho de banda. Consulte [Administración del uso del ancho de banda de red para la protección del entorno local con Azure](https://support.microsoft.com/kb/3056159) para optimizar el uso de la red. Una vez que finaliza la replicación inicial, el trabajo *Finalizar la protección en la máquina virtual* configura las opciones de red y posteriores a la replicación. Mientras la replicación inicial está en curso, se realiza un seguimiento de todos los cambios en los discos, como se menciona en la sección Replicacion diferencial a continuación. Además, se consume espacio en disco adicional para la instantánea y los archivos HRL. Al finalizar una replicación inicial, la instantánea de máquina virtual de Hyper-V se elimina, lo que dará lugar a la combinación de los cambios en los datos con posterioridad a la replicación inicial en el disco primario.

![Solucionar problemas de Hyper-V locales](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### Replicación diferencial
Hyper-V Replica Replication Tracker, que forma parte del motor de replicación de réplicas de Hyper-V, realiza un seguimiento de los cambios en un disco duro virtual, como los registros de replicación de Hyper-V (*.hrl). Los archivos HRL se encuentran en el mismo directorio que los discos asociados. Cada disco configurado para la replicación tendrá un archivo HRL asociado. Este registro se envía a la cuenta de almacenamiento del cliente una vez completada la replicación inicial. Cuando un registro se encuentra en tránsito hacia Azure, los cambios en el archivo principal se siguen en otro archivo de registro del mismo directorio.

Se puede supervisar el estado de replicación de la máquina virtual durante la replicación inicial o la replicación diferencial en la vista de la máquina virtual mencionada en [Supervisión del estado de la replicación de la máquina virtual](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).

### Resincronización 
Una máquina virtual se marca para resincronización cuando la replicación diferencial da error y la replicación inicial completa es costosa en términos de ancho de banda de red o el tiempo que tardaría en completarse. Por ejemplo, cuando el tamaño de archivo HRL llega al 50 % del tamaño total del disco, la máquina virtual se marca para resincronización. La resincronización minimiza la cantidad de datos enviados a través de la red mediante el cálculo de sumas de comprobación de los discos de máquina virtual de origen y destino y el envío solo de la replicación diferencial.

Una vez finalizada la resincronización, se debe reanudar la replicación diferencial. La resincronización se puede reanudar en caso de una interrupción (por ejemplo, de la red, o de un bloqueo de VMMS, entre otros).

De forma predeterminada, la *resincronización programada automáticamente* se configura fuera del horario de oficina. Si necesita volver a sincronizar manualmente la máquina virtual, seleccione la máquina virtual en el portal y haga clic en VOLVER A SINCRONIZAR. ![Solucionar problemas de Hyper-V locales](media/site-recovery-understanding-site-to-azure-protection/image04.png)

La resincronización usa un algoritmo de fragmentación de bloques fijos donde los archivos de origen y destino se dividen en fragmentos fijos; se generan sumas de comprobación para cada fragmento y, luego, se comparan para determinar qué bloques del origen se deben aplicar al destino.

### Lógica de reintento
Cuando se producen errores de replicación, existe una lógica de reintento integrada. Esta se puede clasificar en dos categorías, tal como se muestra a continuación.

| Categoría | Escenarios |
|---------------------------|----------------------------------------------|
| Error no recuperable | No se realizará ningún reintento. El estado de replicación de la máquina virtual se muestra como Crítico y se requiere la intervención de un administrador. Algunos ejemplos son <ul><li>Una cadena de VHD rota</li><li>La máquina virtual de réplica está en un estado no válido</li><li>Error de autenticación de red</li><li>Error de autorización</li><li>Si no se encuentra una máquina virtual en caso de un servidor de Hyper-V independiente</li></ul>|
| Error recuperable | Los reintentos se producen con cada intervalo de replicación mediante el uso exponencial de retroceso, lo que aumenta el intervalo de reintentos desde el inicio del primer intento (1, 2, 4, 8, 10 minutos). Si el error persiste, reintente cada 30 minutos. Algunos ejemplos incluyen <ul><li>Error de red</li><li>Espacio en disco insuficiente</li><li>Estado de memoria insuficiente</li></ul>|

## Ciclo de vida de protección y recuperación de una máquina virtual de Hyper-V

![Descripción del ciclo de vida de protección y recuperación de la máquina virtual de Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## Otras referencias

- [Protección de supervisión y solución de problemas para los VMware, VMM, Hyper-V y sitios físicos](./site-recovery-monitoring-and-troubleshooting.md)
- [Contacto con el soporte técnico de Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Errores de ASR comunes y sus soluciones](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!---HONumber=AcomDC_1217_2015-->