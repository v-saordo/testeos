<properties
	pageTitle="Maximizar el uso de nodos de Lote de Azure con tareas en paralelo | Microsoft Azure"
	description="Aumente la eficiencia y reduzca los costos usando menos nodos de proceso al mismo tiempo que ejecuta tareas simultáneas en cada nodo de un grupo de Lote de Azure"
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
	ms.date="01/22/2016"
	ms.author="marsma" />
	
# Maximizar el uso de recursos de proceso de Lote de Azure con tareas simultáneas de nodo

En este artículo, aprenderá a ejecutar más de una tarea simultáneamente en cada nodo de ejecución dentro de su grupo de Lote de Azure. Al habilitar la ejecución de tareas simultáneas en los nodos de proceso de un grupo, puede maximizar el uso de recursos en un número menor de nodos del grupo. Para algunas cargas de trabajo, esto puede ahorrar tiempo y dinero.

Si bien algunos escenarios se beneficiarán de que todos los recursos de un nodo estén disponibles para asignarse a una sola tarea, en otras situaciones será conveniente permitir que varias tareas compartan esos recursos:

 - **Minimizar la transferencia de datos** cuando las tareas son capaces de compartir datos. En este escenario, puede reducir considerablemente los gastos de transferencia de datos copiando datos compartidos en un número menor de nodos y ejecutando tareas en paralelo en cada nodo. Esto es válido especialmente si los datos que se copian en cada nodo deben transferirse entre regiones geográficas.

 - **Maximizar el uso de memoria** cuando las tareas requieren una gran cantidad de memoria, pero solo durante períodos breves y en momentos variables durante la ejecución. Puede emplear menos nodos de ejecución, pero de mayor tamaño y con más memoria, para controlar de forma eficaz dichos aumentos. Estos nodos tendrían varias tareas ejecutándose en paralelo en cada nodo, pero cada tarea aprovecharía la abundante memoria de los nodos en distintos momentos.

 - **Mitigar los límites de número de nodos** cuando se requiere la comunicación entre nodos dentro de un grupo. Actualmente, los grupos configurados para la comunicación entre nodos están limitados a 50 nodos de ejecución. Por lo tanto, se puede ejecutar un mayor número de tareas simultáneamente si cada nodo de un grupo de este tipo es capaz de ejecutar tareas en paralelo.

 - **Replicar en un clúster de proceso local**, como cuando mueve por primera vez un entorno de procesos a Azure. Puede aumentar el número máximo de tareas de nodo para que refleje con mayor fidelidad una configuración física existente si dicha configuración ejecuta actualmente varias tareas por nodo de proceso.

## Escenario de ejemplo

Este es un ejemplo que ilustra las ventajas de la ejecución en paralelo de tareas. Supongamos que la aplicación de tareas tenga requisitos de CPU y memoria para los que un tamaño de nodo Standard\_D1 es el adecuado. Pero, para ejecutar el trabajo en el tiempo requerido, se necesitan 1.000 nodos de ese tipo.

En lugar de usar los nodos Standard\_D1, que tienen 1 núcleo de CPU, podría emplear nodos Standard\_D14 que tienen 16 núcleos cada uno y habilitar la ejecución de tareas en paralelo. En este caso, se podría usar un *número de nodos 16 veces menor*; es decir, en lugar de 1.000 nodos, solo serían necesarios 63. Esto mejorará enormemente el tiempo de ejecución del trabajo y la eficacia si se requieren archivos de aplicación de gran tamaño o datos de referencia para cada nodo.

## Habilitación de la ejecución en paralelo de tareas

Los nodos de proceso en la solución Lote se configuran para la ejecución en paralelo de tareas en el nivel de grupo. Cuando se usa la biblioteca de .NET Lote, se establece la propiedad [CloudPool.MaxTasksPerComputeNode][maxtasks_net] al crear un grupo. Si usa la API de REST de Lote, se establece el elemento [maxTasksPerNode][maxtasks_rest] en el cuerpo de la solicitud durante la creación del grupo.

Lote de Azure permite una configuración máxima de tareas por nodo que casi cuadriplica el número de núcleos de nodo. Por ejemplo, si el grupo está configurado con nodos de tamaño "Grande" (cuatro núcleos), `maxTasksPerNode` se puede establecer en 16. Para más información sobre el número de núcleos de cada uno de los tamaños de nodo, consulte [Tamaños de los servicios en la nube](./../cloud-services/cloud-services-sizes-specs.md). Para más información sobre los límites del servicio, consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md).

> [AZURE.TIP] Asegúrese de tener en cuenta el valor `maxTasksPerNode` al construir una [fórmula de escalado automático][enable_autoscaling] para el grupo. Por ejemplo, una fórmula que evalúe `$RunningTasks` podría verse afectada considerablemente por un aumento en las tareas por nodo. Consulte [Escalación automática de los nodos de ejecución en un grupo de Lote de Azure](batch-automatic-scaling.md) para obtener más información.

## Distribución de tareas

Cuando los nodos de proceso dentro de un grupo son capaces de ejecutar tareas al mismo tiempo, es importante especificar cómo desea que se distribuyan las tareas entre los nodos del grupo.

Mediante la propiedad [CloudPool.TaskSchedulingPolicy][task_schedule], puede especificar que las tareas se deberían asignar de manera uniforme entre todos los nodos del grupo ("propagación"). O bien, puede especificar que se deberían asignar todas las tareas posibles a cada nodo antes de asignarlas a otro nodo del grupo ("empaquetado").

Como ejemplo de por qué esta característica es importante, considere el grupo de nodos Standard\_D14 (en el ejemplo anterior) configurado con un valor [CloudPool.MaxTasksPerComputeNode][maxtasks_net] de 16. Si [CloudPool.TaskSchedulingPolicy][task_schedule] se configura con un [ComputeNodeFillType][fill_type] de *Pack*, se podría maximizar el uso de los 16 núcleos de cada nodo y permitir que un [grupo con escalado automático](./batch-automatic-scaling.md) elimine los nodos sin usar del grupo (nodos sin tareas asignadas). Esto minimiza el uso de recursos y permite ahorrar dinero.

## Ejemplo de .NET Lote

En este fragmento del código de la API de [.NET Lote][api_net], se muestra una solicitud para crear un grupo que contiene cuatro nodos de gran tamaño con un máximo de cuatro tareas por nodo. Se especifica una directiva de programación de tareas que llenará cada nodo de tareas antes de asignarlas a otro nodo del grupo. Para más información acerca de cómo agregar grupos mediante la API de .NET Lote, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Ejemplo de REST Lote

En este fragmento de la API de [REST Lote][api_rest], se muestra una solicitud para crear un grupo que contiene dos nodos de gran tamaño con un máximo de cuatro tareas por nodo. Para más información acerca de cómo agregar grupos mediante la API de REST, consulte [Agregar un grupo a una cuenta][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE] Solo puede establecer el elemento `maxTasksPerNode` y la propiedad [MaxTasksPerComputeNode][maxtasks_net] en el momento de crear el grupo. No se pueden modificar después de haberlos creado.

## Exploración del proyecto de ejemplo

Consulte el proyecto [ParallelNodeTasks][parallel_tasks_sample] en GitHub. Es un ejemplo de código de trabajo que ilustra el uso de [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Esta aplicación de consola de C# utiliza la biblioteca [.NET de Lote][api_net] para crear un grupo con uno o más nodos de proceso. Ejecuta un número configurable de tareas en esos nodos para simular una carga variable. Los resultados de la aplicación especifican qué nodos han ejecutado cada tarea. La aplicación también proporciona un resumen de los parámetros de trabajo y la duración. Abajo se muestra la parte de resumen de los resultados de dos ejecuciones diferentes de la aplicación de ejemplo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La primera ejecución de la aplicación de ejemplo muestra que, con un solo nodo en el grupo y la configuración predeterminada de una tarea por nodo, la duración del trabajo es superior a 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La segunda ejecución del ejemplo muestra una disminución notable en la duración del trabajo. Esto se debe a que el grupo se configuró con cuatro tareas por nodo, lo que permite la ejecución en paralelo de tareas de forma que el trabajo se completa en casi una cuarta parte del tiempo.

> [AZURE.NOTE] La duración del trabajo en los resúmenes anteriores no incluye el tiempo de creación del grupo. Cada una de las tareas anteriores se envió a grupos ya creados cuyos nodos de proceso se encontraban en el estado *inactivo* en el momento del envío.

## Mapa térmico de Batch Explorer

[Explorador de Lote][batch_explorer], una de las [aplicaciones de ejemplo][github_samples] de Lote de Azure, contiene una característica denominada *Mapa térmico* que proporciona visualización de la ejecución de tareas. Cuando ejecuta la aplicación de ejemplo [ParallelTasks][parallel_tasks_sample], puede usar la característica Mapa térmico para visualizar fácilmente la ejecución en paralelo de tareas en cada nodo.

![Mapa térmico de Batch Explorer][1]

*Mapa térmico de Batch Explorer que muestra un grupo de cuatro nodos, donde cada nodo ejecuta actualmente cuatro tareas*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=AcomDC_0128_2016-->