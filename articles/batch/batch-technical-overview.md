<properties
	pageTitle="Datos básicos del servicio Lote de Azure | Microsoft Azure"
	description="Información acerca del servicio Lote de Azure para cargas de trabajo HPC y paralelas a gran escala"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/19/2015"
	ms.author="danlep"/>

# Datos básicos de Lote de Batch

Lote de Azure le ayudará a ejecutar aplicaciones a gran escala paralelas y de informática de alto rendimiento (HPC) de manera eficaz en la nube. Es un servicio de plataforma que programa el trabajo de proceso intensivo para que se ejecute en una colección administrada de máquinas virtuales (nodos de proceso) y puede escalar los recursos de proceso para satisfacer las necesidades del trabajo. Con el servicio de Lote, defina mediante programación los recursos de proceso de Azure y los trabajos por lotes a gran escala que se ejecutan a petición o según una programación y no es necesario configurar y administrar manualmente un clúster de HPC, máquinas virtuales individuales, redes virtuales o un programador de trabajos.

## Casos de uso

Lote es un servicio administrado para el *procesamiento por lotes* o la *informática por lotes*, es decir, la ejecución de grandes volúmenes de tareas similares para obtener el resultado deseado. El procesamiento por lotes es un patrón común para las organizaciones que procesan, transforman y analizan grandes cantidades de datos, ya sea según un programa o a petición, con ejemplos en campos que oscilan entre servicios financieros e ingeniería.

Lote funciona bien con cargas de trabajo o aplicaciones intrínsicamente paralelas (a veces llamadas "lamentablemente paralelas"), las que se prestan para ejecutar como tareas paralelas en varios equipos. Consulte la ilustración 1.

![Tareas paralelas][parallel]

**Ilustración 1: Tareas paralelas que se ejecutan en varios equipos**

Algunos ejemplos son:

* Modelado de riesgos financieros
* Representación y procesamiento de imágenes
* Codificación y transcodificación multimedia
* Análisis de secuencia genética
* Pruebas de software

El servicio Lote también puede realizar cálculos paralelos con un paso de reducción al final y cargas de trabajo HPC más complejas, como aplicaciones de interfaz de transferencia de mensajes (MPI).

>[AZURE.NOTE]En este momento, Lote solo es compatible con las cargas de trabajo que se ejecutan en máquinas virtuales basadas en Windows Server.

Para obtener una comparación de Lote con otras opciones de solución HPC en Azure, consulte [Soluciones HPC y lote](batch-hpc-solutions.md).

## Desarrollo con Lote

Estos escenarios usan las API de Lote para crear y administrar grupos de nodos de ejecución y programar los trabajos y tareas que se ejecutan en ellos. Escriba aplicaciones cliente o front-end para ejecutar trabajos y tareas bajo demanda, según una programación, o como parte de un flujo de trabajo mayor administrado mediante herramientas como [Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/).

Para más información acerca de los conceptos de Lote, consulte [Aspectos básicos sobre la API de Lote de Azure](batch-api-basics.md).

### Cuentas que necesita

+ **Cuenta de Azure y suscripción**: si aún no tiene una cuenta, puede activar las [ventajas de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

+ **Cuenta de lotes**: use el nombre y la dirección URL de una cuenta de Lotes y una clave de acceso como credenciales al realizar las llamadas API de Lote. Todos los recursos de Lote de proceso como nodos de proceso, grupos, trabajos y tareas están asociados a una cuenta de Lote. Una forma de crear una cuenta de Lote y administrar claves de acceso para la cuenta es usar el [Portal de Azure](batch-account-create-portal.md).

+ **Cuenta de almacenamiento**: para la mayoría de los escenarios de Lote tendrá una cuenta de almacenamiento de Azure para almacenar sus entradas y salidas de datos y los scripts o archivos ejecutables que se ejecutan en los nodos de proceso. Para crear una cuenta de almacenamiento, consulte la sección [Acerca de cuentas de Almacenamiento de Azure](../storage/storage-create-storage-account.md).

### Herramientas y bibliotecas de desarrollo de Lote

Use estas bibliotecas y herramientas de .NET con Visual Studio para desarrollar y administrar aplicaciones de Lote de Azure.

+ [Biblioteca de cliente de Lote para .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet): desarrolle aplicaciones cliente para ejecutar trabajos con el servicio Lote
+ [Biblioteca de administración de Lote](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet): administrar cuentas del servicio Lote
+ [Explorador de Lote](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub): compile esta aplicación GUI para examinar, acceder y actualizar recursos en una cuenta de Lote, incluidos trabajos y tareas, grupos y nodos de proceso, y archivos en un nodo de proceso. Consulte la [publicación de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Escenario: Escalar horizontalmente una carga de trabajo paralela

Un escenario común con las API de Lote implica el escalado horizontal de trabajo intrínsecamente paralelo como representación de imágenes en un grupo de nodos de proceso que proporciona hasta miles de núcleos de proceso.

En la Figura 2 se muestra un flujo de trabajo que usa una aplicación cliente .NET de Lote para ejecutar una carga de trabajo en paralelo con Lote.


![Flujo de trabajo de elementos de trabajo][work_item_workflow]

**Ilustración 2. Escalar horizontalmente una carga de trabajo paralela en Lote**

1.	Cargue los archivos de entrada (como imágenes o datos de origen) que se requieren para un trabajo a una cuenta de almacenamiento de Azure. El servicio Lote carga archivos en nodos de proceso al ejecutar las tareas.

2.	Cargar los archivos de programa o secuencias de comandos que se ejecutarán en los nodos de proceso para la cuenta de almacenamiento. Entre ellas se pueden incluir archivos binarios y sus ensamblados dependientes. El servicio Lote también carga estos archivos en nodos de proceso al ejecutar las tareas.

3.	Crear mediante programación un grupo de nodos de proceso por lotes en la cuenta de proceso por lotes, especificar propiedades como su tamaño y el sistema operativo se ejecutan. También puede definir cómo [aumenta o disminuye](batch-automatic-scaling.md) el número de nodos del grupo en respuesta a la carga de trabajo. Cuando se ejecuta una tarea, se asigna un nodo de este grupo.

4.	Defina mediante programación un trabajo de Lote para ejecutar la carga de trabajo en el grupo de nodos.

5.	Agregue tareas al trabajo. Cada tarea usa el programa que cargó para procesar la información desde un archivo que cargó. Dependiendo de la carga de trabajo que desee ejecutar [varias tareas al mismo tiempo](batch-parallel-node-tasks.md) en cada nodo de proceso para maximizar el uso de recursos. Lote también admite la [preparación de trabajos y las tareas de finalización](batch-job-prep-release.md) especializadas para preparar los nodos de proceso antes de que se ejecuten las tareas programadas y la limpieza posterior.

6.	Ejecute la carga de trabajo por lotes y supervise el progreso y los resultados. La aplicación se comunica a través de HTTPS con el servicio de Lote. Para mejorar el rendimiento de la aplicación al supervisar grandes números de grupos, trabajos y tareas, aproveche las ventajas de [técnicas eficaces para consultar](batch-efficient-list-queries.md) el servicio Lote.






## Pasos siguientes

* Empezar a desarrollar su primera aplicación con la [biblioteca .NET de cliente de Lote](batch-dotnet-get-started.md)
* Buscar ejemplos de código de Lote en [GitHub](https://github.com/Azure/azure-batch-samples)

[parallel]: ./media/batch-technical-overview/parallel.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png

<!---HONumber=AcomDC_0128_2016-->