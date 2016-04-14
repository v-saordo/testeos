<properties
	pageTitle="Ejecución de aplicaciones de MPI en Lote de Azure con tareas de instancias múltiples | Microsoft Azure"
	description="Obtenga información sobre cómo ejecutar aplicaciones de Interfaz de paso de mensajes (MPI) con el tipo de tarea de instancias múltiples en Lote de Azure."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="02/19/2016"
	ms.author="marsma" />

# Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Lote de Azure

Con las tareas de instancias múltiples, puede ejecutar una tarea de Lote de Azure en varios nodos de proceso a la vez para permitir escenarios de informática de alto rendimiento, como las aplicaciones de la Interfaz de paso de mensajes (MPI). En este artículo, aprenderá a ejecutar tareas de instancias múltiples mediante la biblioteca [.NET de Lote][api_net].

## Información general de las tareas de instancias múltiples

En Lote, cada tarea se ejecuta normalmente en un solo nodo de proceso: se envían varias tareas a un trabajo y el servicio Lote programa la ejecución de cada tarea en un nodo. Sin embargo, al configurar la **opción de instancias múltiples** para una tarea, puede indicar al servicio Lote que divida esa tarea en subtareas para que se ejecuten en varios nodos.

![Información general de las tareas de instancias múltiples][1]

Al enviar una tarea con configuración de instancias múltiples a un trabajo, el servicio Lote realiza varios pasos que son específicos de las tareas de instancias múltiples:

1. El servicio Lote divide automáticamente la tarea en una **principal** y muchas **subtareas**. A continuación, programa la ejecución de la tarea principal y las subtareas en los nodos de proceso del grupo.
2. Estas tareas, tanto la principal como las subtareas, descargan los **archivos de recursos comunes** que se especifican en la configuración de instancias múltiples.
3. Cuando se han descargado los archivos de recursos comunes, la tarea principal y las subtareas ejecutan el **comando de coordinación** que se especifique en la configuración de instancias múltiples. Este comando de coordinación se utiliza normalmente para iniciar un servicio en segundo plano (como [MPI de Microsoft][msmpi_msdn] `smpd.exe`) y también puede comprobar que los nodos están listos para procesar mensajes entre nodos.
4. Cuando la tarea principal y todas las subtareas han completado correctamente el comando de coordinación, *solo* la **tarea principal** ejecuta la **línea de comandos** de la tarea de instancias múltiples (el "comando de aplicación"). Por ejemplo, en una solución basada en [MS-MPI][msmpi_msdn], se trata del lugar donde ejecuta la aplicación habilitada para MPI mediante `mpiexec.exe`.

> [AZURE.NOTE] Aunque es funcionalmente distinta, la "tarea de instancias múltiples" no es un tipo de tarea única como [StartTask][net_starttask] o [JobPreparationTask][net_jobprep]. La tarea de instancias múltiples es simplemente una tarea de Lote estándar ([CloudTask][net_task] en .NET de Lote) cuya opción de instancias múltiples se ha configurado. En este artículo, nos referiremos a ella como **tarea de instancias múltiples**.

## Requisitos de las tareas de instancias múltiples

Las tareas de instancias múltiples requieren un grupo con la **comunicación ente nodos habilitada** y la **ejecución simultánea de tareas deshabilitada**. Si intenta ejecutar una tarea de instancias múltiples en un grupo con la comunicación entre nodos deshabilitada o con un valor de *maxTasksPerNode* superior a 1, la tarea nunca será programada, sino que permanecerá indefinidamente en estado "activo". Este fragmento de código muestra la creación de un grupo de este tipo mediante la biblioteca .NET de Lote.

```
CloudPool myCloudPool =
	myBatchClient.PoolOperations.CreatePool(
		poolId: "MultiInstanceSamplePool",
		osFamily: "4",
		virtualMachineSize: "small",
		targetDedicated: 3);

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Además, se ejecutarán tareas de instancias múltiples *solo* en nodos de los **grupos creados después del 14 de diciembre de 2015**.

> [AZURE.TIP] Cuando se utilizan los [nodos de proceso de tamaño A8 o A9](./../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) del grupo de Lote, la aplicación de MPI puede aprovechar la red de acceso directo a memoria remota (RDMA) de alto rendimiento y baja latencia de Azure. Puede ver la lista completa de tamaños de nodos de proceso disponibles para los grupos de Lote en [Tamaños de los servicios en la nube](./../cloud-services/cloud-services-sizes-specs.md).

### Uso de StartTask para la instalación de la aplicación de MPI

Para ejecutar aplicaciones de MPI con una tarea de instancias múltiples, primero debe obtener el software de MPI en los nodos de proceso del grupo. Es un buen momento para utilizar una instancia de [StartTask][net_starttask], que se ejecuta cada vez que un nodo se une a un grupo o se ha reiniciado. Este fragmento de código crea una instancia de StartTask que especifica el paquete de instalación de MS-MPI como un [archivo de recursos][net_resourcefile] y la línea de comandos que se ejecutará después de que el archivo de recursos se descargue en el nodo.

```
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [AZURE.NOTE] No está limitado a usar MS-MPI al implementar una solución de MPI con tareas de instancias múltiples en Lote. Puede usar cualquier implementación del estándar de MPI que sea compatible con el sistema operativo que especifique para los nodos de proceso del grupo.

## Creación de una tarea de instancias múltiples con .NET de Lote

Ahora que hemos analizado los requisitos de grupo y la instalación del paquete MPI, vamos a crear la tarea de instancias múltiples. En este fragmento de código, creamos una instancia de [CloudTask][net_task] estándar y luego configuramos su propiedad [MultiInstanceSettings][net_multiinstance_prop]. Como se mencionó anteriormente, la tarea de instancias múltiples no es un tipo de tarea distinto, sino una tarea de Lote estándar configurada con la opción de instancias múltiples.

```
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## Tarea principal y subtareas

Cuando se crea la configuración de instancias múltiples para una tarea, se especifica el número de nodos de proceso que ejecutarán la tarea. Cuando se envía la tarea a un trabajo, el servicio Lote crea una tarea **principal** y suficientes **subtareas** que, juntas, coinciden con el número de nodos especificado.

A estas tareas se les asigna a un identificador entero del intervalo de 0 a *numberOfInstances - 1*. La tarea con el identificador 0 es la tarea principal y todos los demás identificadores son subtareas. Por ejemplo, si crea la siguiente configuración de instancias múltiples para una tarea, la tarea principal tendrá un identificador de 0 y las subtareas tendrán los identificadores del 1 al 9.

```
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

## Comandos de coordinación y aplicación

El **comando de coordinación** ejecuta tanto tareas principales como subtareas. Una vez que la tarea principal y todas las subtareas han terminado de ejecutar el comando de coordinación, *solo* la tarea principal ejecuta la línea de comandos de la tarea de instancias múltiples. Llamaremos a esta línea de comandos el **comando de aplicación** para distinguirlo del comando de coordinación.

La invocación del comando de coordinación se bloquea: el servicio Lote no ejecuta el comando de aplicación hasta que el comando de coordinación se ha devuelto correctamente para todas las subtareas. Por lo tanto, el comando de coordinación debe iniciar los servicios en segundo plano necesarios, comprobar que están listos para utilizarse y luego cerrarse. Por ejemplo, este comando de coordinación para una solución que utiliza la versión 7 de MS-MPI inicia el servicio SMPD en el nodo y luego se cierra:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observe el uso de `start` en este comando de coordinación. Esto es necesario porque la aplicación `smpd.exe` no devuelve resultados inmediatamente después de la ejecución. Sin el uso del comando [start][cmd_start], este comando de coordinación no devolvería resultados y, por tanto, impediría que se ejecutara el comando de aplicación.

*Solo* la tarea principal ejecuta el **comando de aplicación**, la línea de comandos especificada para la tarea de instancias múltiples. En las aplicaciones de MS-MPI, esta será la ejecución de la aplicación habilitada para MPI mediante `mpiexec.exe`. Por ejemplo, este es un comando de aplicación para una solución mediante la versión 7 de MS-MPI:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

## Archivos de recursos

Hay dos conjuntos de archivos de recursos que se deben tener en cuenta para las tareas de instancias múltiples: **archivos de recursos comunes** que descargan *todas* las tareas (tanto principales como subtareas) y **archivos de recursos** especificados para la propia tarea de instancias múltiples, que *solo* descarga la tarea principal.

Puede especificar uno o más **archivos de recursos comunes** en la configuración de instancias múltiples de una tarea. La tarea principal y todas las subtareas descargan estos archivos de recursos comunes desde el [Almacenamiento de Azure](./../storage/storage-introduction.md) en el directorio compartido de tareas de cada nodo. Puede tener acceso al directorio compartido de tareas desde las líneas de comandos de coordinación y aplicación mediante la variable de entorno `AZ_BATCH_TASK_SHARED_DIR`.

*Solo* la tarea principal descarga los archivos de recursos que especifique para la propia tarea de instancias múltiples en el directorio de trabajo de la tarea, `AZ_BATCH_TASK_WORKING_DIR`; las subtareas no descargan los archivos de recursos especificados para la tarea de instancias múltiples.

El contenido de `AZ_BATCH_TASK_SHARED_DIR` es accesible por la tarea principal y todas las subtareas que se ejecutan en un nodo. Un ejemplo de directorio compartido de tareas es `tasks/mybatchjob/job-1/mymultiinstancetask/`. La tarea principal y cada subtarea también tienen un directorio de trabajo al que solo puede acceder esa tarea, y se accede mediante la variable de entorno `AZ_BATCH_TASK_WORKING_DIR`.

Tenga en cuenta que en los ejemplos de código de este artículo, no especificamos los archivos de recursos para la propia tarea de instancias múltiples, solo para la instancia de StartTask del grupo y la instancia de [CommonResourceFiles][net_multiinsance_commonresfiles] de la configuración de instancias múltiples.

> [AZURE.IMPORTANT] Utilice siempre las variables de entorno `AZ_BATCH_TASK_SHARED_DIR` y `AZ_BATCH_TASK_WORKING_DIR` para hacer referencia a estos directorios en las líneas de comando. No intente construir las rutas de acceso manualmente.

## Duración de la tarea

La duración de la tarea principal controla la duración de toda la tarea de instancias múltiples. Cuando se cierra la tarea principal, todas las subtareas se terminan. El código de salida de la tarea principal es el código de salida de la tarea y, por tanto, se utiliza para determinar el éxito o fracaso de la tarea con fines de reintento.

Si se produce un error en alguna de las subtareas, por ejemplo, se cierra con un código de error distinto de cero, la tarea de instancias múltiples entera dará error. Entonces la tarea de instancias múltiples se termina y se reintenta, hasta su límite de reintento.

Cuando se elimina una tarea de instancias múltiples, el servicio Lote también elimina la tarea principal y todas las subtareas. Todos los directorios de subtarea y sus archivos se eliminan de los nodos de proceso, igual que en el caso de una tarea estándar.

Los valores de [TaskConstraints][net_taskconstraints] para una tarea de instancias múltiples, como las propiedades [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] y [RetentionTime][net_taskconstraint_retention], se respetan ya que son para una tarea estándar, y se aplican a la tarea principal y a todas las subtareas. Sin embargo, si cambia la propiedad [RetentionTime][net_taskconstraint_retention] después de agregar la tarea de instancias múltiples al trabajo, este cambio solo se aplica a la tarea principal. Todas las subtareas seguirán usando la propiedad [RetentionTime][net_taskconstraint_retention] original.

La lista de tareas recientes de un nodo de proceso reflejará el identificador de una subtarea si la tarea reciente era parte de una tarea de instancias múltiples.

## Obtención de información sobre las subtareas

Para obtener información sobre las subtareas mediante la biblioteca .NET de Lote, llame al método [CloudTask.ListSubtasks][net_task_listsubtasks]. Este método devuelve información sobre todas las subtareas e información sobre el nodo de proceso que ejecuta las tareas. A partir de esta información, puede determinar el directorio raíz de cada subtarea, el identificador de grupo, su estado actual, el código de salida, etc. Esta información se puede utilizar en combinación con el método [PoolOperations.GetNodeFile][poolops_getnodefile] para obtener los archivos de la subtarea. Tenga en cuenta que este método no devuelve información de la tarea principal (id. 0).

> [AZURE.NOTE] A menos que se indique lo contrario, los métodos .NET de Lote que operan en la propia clase [CloudTask][net_task] de varias instancias, *solo* se aplican a la tarea principal. Por ejemplo, al llamar al método [CloudTask.ListNodeFiles][net_task_listnodefiles] en una tarea de instancias múltiples, solo se devuelven los archivos de la tarea principal.

El fragmento de código siguiente muestra cómo obtener información de la subtarea y cómo solicitar contenido de archivos de los nodos en los que se ejecuta.

```
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
			await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## Pasos siguientes

- Puede crear una aplicación sencilla de MS-MPI para utilizar durante la prueba de tareas de instancias múltiples en Lote. El artículo del blog de Microsoft HPC & Azure Batch, [How to compile and run a simple MS-MPI program][msmpi_howto] (Cómo compilar y ejecutar un programa sencillo de MS-MPI), contiene un tutorial para la creación de una aplicación de MPI sencilla mediante MS-MPI.

- Consulte la página [Microsoft MPI][msmpi_msdn] de MSDN para obtener la información más reciente sobre MS-MPI.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Información general de instancias múltiples"

<!---HONumber=AcomDC_0224_2016-->