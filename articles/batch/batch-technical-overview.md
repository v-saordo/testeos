<properties
	pageTitle="Datos básicos del servicio Lote de Azure | Microsoft Azure"
	description="Información acerca del servicio Lote de Azure para cargas de trabajo HPC y paralelas a gran escala"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/16/2016"
	ms.author="marsma"/>

# Datos básicos de Lote de Batch

Lote de Azure le permite ejecutar a gran escala aplicaciones paralelas y de informática de alto rendimiento (HPC) de manera eficaz en la nube. Se trata de un servicio de plataforma que programa el trabajo de proceso intensivo para que se ejecute en una colección administrada de máquinas virtuales, y que puede escalar automáticamente los recursos de proceso para satisfacer las necesidades de sus trabajos.

Con el servicio Lote, se definen mediante programación los recursos de proceso de Azure para ejecutar trabajos por lotes a gran escala. Puede ejecutar estos trabajos a petición o siguiendo una programación, y no tiene que configurar y administrar manualmente un clúster de HPC, máquinas virtuales individuales, redes virtuales o un programador de trabajos.

## Casos de uso de Lote

Lote es un servicio administrado de Azure que se usa para el *procesamiento por lotes* o la *computación por lotes*, es decir, la ejecución de grandes volúmenes de tareas similares para obtener el resultado deseado. La computación por lotes la utilizan normalmente organizaciones que procesan, transforman y analizan grandes volúmenes de datos con regularidad.

Lote funciona bien con cargas de trabajo y aplicaciones intrínsecamente paralelas (a veces llamadas "lamentablemente paralelas"). Las cargas de trabajo intrínsecamente paralelas se dividen fácilmente en varias tareas que trabajan simultáneamente en varios equipos.

![Tareas paralelas][1]<br/>

Algunos ejemplos de cargas de trabajo que normalmente se procesan mediante esta técnica son:

* Modelado de riesgos financieros
* Análisis de datos de clima e hidrología
* Representación, análisis y procesamiento de imágenes
* Codificación y transcodificación multimedia
* Análisis de secuencia genética
* Análisis de esfuerzos en ingeniería
* Pruebas de software

El servicio Lote también puede realizar cálculos paralelos con un paso de reducción al final, además de ejecutar cargas de trabajo HPC más complejas, como las aplicaciones de [interfaz de transferencia de mensajes (MPI)](batch-mpi.md).

Para obtener una comparación entre Lote y otras opciones de solución HPC en Azure, consulte [Soluciones de lote y HPC en la nube de Azure](batch-hpc-solutions.md).

>[AZURE.NOTE] En la actualidad, Lote solo es compatible con las cargas de trabajo que se ejecutan en máquinas virtuales basadas en Windows Server.

## Desarrollo con Lote

Cuando compila soluciones que usan Lote de Azure para el procesamiento paralelo de la carga de trabajo, lo hace mediante programación utilizando las API de Lote. Con las API de Lote, puede crear y administrar grupos de nodos de proceso (máquinas virtuales) y programar trabajos y tareas para que se ejecuten en esos nodos. Un servicio o una aplicación cliente que usted cree, usa las API de Lote para comunicarse con el servicio Lote. Puede procesar de forma eficiente cargas de trabajo a gran escala para su organización, o proporcionar un servicio front-end a los clientes para que estos pueden ejecutar trabajos y tareas (a petición o dentro de una programación) en uno, cientos o miles de nodos. También puede usar Lote como parte de un flujo de trabajo mayor, administrado mediante herramientas como [Data Factory de Azure][data_factory].

> [AZURE.TIP] Cuando esté listo para adentrarse en la API de Lote para una comprensión más a fondo de las características que proporciona, consulte [Información general de las características de Lote de Azure](batch-api-basics.md).

### Cuentas de Azure que necesita

Cuando se desarrollan soluciones de Lote, es necesario usar las siguientes cuentas en Microsoft Azure.

- **Cuenta de Azure y suscripción**: si aún no tiene una suscripción de Azure, puede activar su [crédito de Azure para suscriptores de MSDN][msdn_benefits] o bien registrarse para obtener una [cuenta gratuita de Azure][free_account]. Al crear una cuenta, se creará una suscripción predeterminada para usted.

- **Cuenta de Lote**: cuando las aplicaciones interactúan con el servicio Lote, el nombre de cuenta, la dirección URL de la cuenta y una clave de acceso se utilizarán como credenciales. Todos los recursos de Lote como grupos, nodos de proceso, trabajos y tareas están asociados a una cuenta de Lote. Puede [crear y administrar una cuenta de Lote](batch-account-create-portal.md) en el Portal de Azure.

- **Cuenta de Almacenamiento**: Lote incluye compatibilidad integrada para trabajar con archivos en [Almacenamiento de Azure][azure_storage]. Casi todos los escenarios de Lote usarán Almacenamiento de Azure para el almacenamiento provisional de archivos (de los programas que ejecuten las tareas, y de los datos que procesen) y para el almacenamiento de los datos de salida que generen las tareas. Para crear una cuenta de Almacenamiento, consulte [Acerca de las cuentas de Almacenamiento de Azure](./../storage/storage-create-storage-account.md).

### Herramientas y bibliotecas de desarrollo de Lote

Para compilar soluciones mediante Lote de Azure, puede usar las bibliotecas de cliente .NET de Lote, PowerShell o incluso emitir llamadas API de REST directas. Use una o todas estas herramientas para desarrollar aplicaciones de cliente y servicios que ejecuten trabajos en Lote.

- Biblioteca de cliente [.NET de Lote][api_net]\: la mayoría de soluciones de Lote se compilan mediante la biblioteca de cliente .NET de Lote, que está [disponible a través de NuGet][api_net_nuget].

- Biblioteca de cliente [. NET de Administración de Lote][api_net_mgmt]\: también [disponible a través de NuGet][api_net_mgmt_nuget]; use la biblioteca de cliente .NET de Administración de Lote para administrar mediante programación las cuentas de Lote en los servicios o las aplicaciones de cliente.

- API de [REST de Lote][batch_rest]\: las API de REST de Lote proporcionan la misma funcionalidad que la Biblioteca de cliente .NET de Lote. De hecho, la propia biblioteca .NET de Lote usa la API de REST de Lote internamente para interactuar con el servicio Lote.

- [Cmdlets de PowerShell de Lote][batch_ps]\: los cmdlets de Lote de Azure en el módulo [Azure PowerShell](./../powershell-install-configure.md) le permiten administrar los recursos de Lote con PowerShell.

- [Explorador de Lote de Azure][batch_explorer]\: el Explorador de Lote es una de las aplicaciones de ejemplo de .NET de Lote que está [disponible en GitHub][github_samples]. Compile esta aplicación de Windows Presentation Foundation (WPF) con Visual Studio 2013 o 2015 y úsela para examinar y administrar los recursos de la cuenta de Lote durante el desarrollo y depuración de las soluciones de Lote. Vea los detalles de trabajos, grupos y tareas, descargue archivos desde los nodos de proceso, o incluso conecte con nodos de forma remota mediante el uso de archivos de Escritorio remoto (RDP) que puede obtener con unos cuantos clics en la interfaz del Explorador de Lote.

- [Explorador de almacenamiento de Microsoft Azure][storage_explorer]\: aunque no es estrictamente una herramienta de Lote de Azure, el Explorador de almacenamiento es otra herramienta valiosa con la que contar mientras desarrolla y depura las soluciones de Lote.

## Escenario: Escalar horizontalmente una carga de trabajo paralela

Una solución común que usa las API de Lote para interactuar con el servicio Lote implica escalar horizontalmente trabajo intrínsecamente paralelo, como la representación de imágenes para escenas 3D, en un grupo de nodos de proceso. Este grupo de nodos de proceso puede ser la "granja de representación" que le proporciona decenas, cientos o incluso miles de núcleos de su trabajo de representación, por ejemplo.

El siguiente diagrama muestra un flujo de trabajo común de Lote, con una aplicación cliente o un servicio hospedado usando Lote para ejecutar una carga de trabajo paralela.

![Flujo de trabajo de soluciones de Lote][2]

En este escenario común, la aplicación o servicio procesa una carga de trabajo de computación en Lote de Azure realizando los pasos siguientes:

1. Carga de los **archivos de entrada** y de la **aplicación** que procesará esos archivos a su cuenta de Almacenamiento de Azure. Los archivos de entrada pueden ser cualquier dato que vaya a procesar la aplicación, como diseños de modelos financieros, o archivos de vídeo que se van a transcodificar. Los archivos de aplicación pueden ser cualquier aplicación que se use para procesar los datos, como una aplicación de representación de 3D o un transcodificador de medios.

2. Creación de un **grupo** de nodos de proceso de Lote en su cuenta de Lote, estas son las máquinas virtuales que ejecutarán las tareas. Especifique las propiedades como el [tamaño de nodo](./../cloud-services/cloud-services-sizes-specs.md), su sistema operativo y la ubicación en Almacenamiento de Azure de la aplicación para instalar cuando los nodos se unan al grupo (la aplicación que ha cargado en el paso 1). También puede configurar el grupo para [escalar automáticamente](batch-automatic-scaling.md) (ajustar de forma dinámica el número de nodos de proceso en el grupo), en respuesta a la carga de trabajo que generen las tareas.

3. Creación de un **trabajo** de Lote para ejecutar la carga de trabajo en el grupo de nodos de proceso. Cuando crea un trabajo, lo asocia a un grupo de Lote.

4. Incorporación de **tareas** al trabajo. Al agregar tareas a un trabajo, el servicio Lote programa automáticamente las tareas para su ejecución en los nodos de proceso en el grupo. Cada tarea usa la aplicación que ha cargado para procesar los archivos de entrada.

    - 4a. Antes de que la tarea se ejecute, puede descargar los datos (los archivos de entrada) que va a procesar al nodo de proceso al que está asignada. Si la aplicación no se ha instalado aún en el nodo (consulte el paso 2), se puede descargar aquí. Cuando haya completado las descargas, las tareas se ejecutan en sus nodos asignados.

5. Mientras se ejecutan las tareas, puede solicitar a Lote que supervise el progreso del trabajo y sus tareas. El servicio o la aplicación de cliente se comunica con el servicio Lote a través de HTTPS y es posible que esté supervisando miles de tareas que se están ejecutando en miles de nodos de proceso, por ello asegúrese de que realiza una [consulta eficaz del servicio Lote de Azure](batch-efficient-list-queries.md).

6. Cuando se completan las tareas, estas cargan los datos de sus resultados en Almacenamiento de Azure. También puede recuperar archivos directamente desde los nodos de proceso.

7. Cuando la supervisión detecta que se han completado las tareas en su trabajo, el servicio o la aplicación de cliente puede descargar los datos de salida para su posterior procesamiento o evaluación.

No olvide que esto es simplemente una forma de usar Lote, y que este escenario describe solo algunas de sus características. Por ejemplo, puede ejecutar [varias tareas en paralelo](batch-parallel-node-tasks.md) en cada nodo de proceso, y puede usar [tareas de preparación y la finalización del trabajo](batch-job-prep-release.md) para preparar los nodos para los trabajos y limpiar después.

## Pasos siguientes

Ahora que ha visto un escenario de proceso por lotes de ejemplo, ha llegado el momento de profundizar en el servicio para obtener información sobre cómo puede utilizarlo para procesar las cargas de trabajo paralelas de proceso intensivo.

- Consulte [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md) para obtener información sobre cómo usar C# y la biblioteca .NET de Lote para realizar las técnicas descritas anteriormente. Esta debería ser una de sus primeras lecturas mientras está aprendiendo a utilizar el servicio Lote.

- Eche un vistazo a [Información general de las características de Lote de Azure](batch-api-basics.md) para ver información más detallada sobre las funciones de API que proporciona Lote para procesar las cargas de trabajo intensivas de proceso.

- Además del Explorador de Lote, los otros [códigos de ejemplo en GitHub][github_samples] muestran cómo utilizar muchas de las características de Lote mediante la biblioteca de .NET de Lote.

- Consulte la [ruta de aprendizaje de Lote][learning_path] para hacerse una idea de los recursos que tiene a su disposición a medida que va aprendiendo a trabajar con Lote.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[data_factory]: https://azure.microsoft.com/documentation/services/data-factory/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0224_2016-->