<properties
   pageTitle="Big Compute: Recursos técnicos para informática de alto rendimiento (HPC) y computación por lotes | Microsoft Azure"
   description="Enumera los recursos técnicos para ayudarle a ejecutar cargas de trabajo de HPC, por lotes y en paralelo a gran escala en Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Big Compute en Azure: Recursos técnicos para informática de alto rendimiento (HPC) y computación por lotes
Se trata de una guía sobre recursos técnicos que le ayudará a ejecutar cargas de trabajo de HPC, por lotes y en paralelo a gran escala en Azure. Amplíe sus cargas de trabajo de HPC o de lotes existentes a la nube de Azure, o cree nuevas soluciones de Big Compute en Azure mediante una gama de los servicios de Azure.

## Opciones de soluciones

Obtenga información sobre las opciones de Big Compute en Azure y elija el enfoque correcto para sus necesidades empresariales y de carga de trabajo.

* [Soluciones HPC y Lote](batch-hpc-solutions.md)

* [Vídeo: Big Compute en la nube con Azure y HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](https://azure.microsoft.com/services/batch/) es un servicio de plataforma que facilita el proceso de habilitar para la nube sus aplicaciones y de ejecutar trabajos sin configurar y administrar un clúster y un programador de trabajos. Use el SDK para integrar aplicaciones cliente con Azure Batch a través de una variedad de lenguajes, almacenar datos en Azure y crear canalizaciones de ejecución de trabajos.

* [Documentación](https://azure.microsoft.com/documentation/services/batch/)

* [Referencia de API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Tutorial: Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md)

* [Foro de Lote](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Vídeos de Lote](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluciones de clúster de HPC

Implemente o amplíe su clúster de Windows o Linux HPC existente para ejecutar sus cargas de trabajo intensivas de proceso.

### Microsoft HPC Pack

HPC Pack es la solución HPC gratuita de Microsoft que se basa en las tecnologías de Microsoft Azure y Windows Server, capaz de ejecutar cargas de trabajo HPC de Windows y Linux.

* [Descargar HPC Pack 2012 R2 Update 3](https://www.microsoft.com/es-ES/download/details.aspx?id=49922)

* [Documentación](https://technet.microsoft.com/library/jj899572.aspx)


* [Opciones de clúster de HPC con Microsoft HPC Pack en Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [Irrumpir en instancias de trabajo de Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Irrumpir en Lote de Azure con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)


* [Foros de Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluciones de clúster de Linux y OSS

Use estas plantillas de inicio rápido de Azure para implementar clústeres de HPC de Linux con herramientas de código abierto.

* [Establecimiento de un clúster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) y [una entrada de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Establecimiento de un clúster de par](https://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) es una implementación de Microsoft del estándar de la interfaz de especificación de mensajes para desarrollar y ejecutar aplicaciones en paralelo en la plataforma de Windows.


* [Descargar MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Referencia de MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Foro de MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Instancias intensivas de proceso

Azure ofrece un [intervalo de tamaños](../virtual-machines/virtual-machines-size-specs.md), que incluye las [instancias A8, A9, A10 y A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) de proceso intensivo, para ejecutar las cargas de trabajo HPC de Linux y Windows.

* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Configurar un clúster de Windows RDMA con Microsoft HPC Pack para ejecutar aplicaciones MPI](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## Proyectos de arquitectura

* Lote de Microsoft Azure y Factoría de datos de Azure: la arquitectura para procesamiento de datos a gran escala ([PDF](http://go.microsoft.com/fwlink/?linkid=717686) y [artículo](../data-factory/data-factory-data-processing-using-batch.md)) muestra cómo la Factoría de datos organiza el procesamiento y el movimiento de los datos y cómo Lote de Azure procesa los datos en paralelo.

## Ejemplos y demos

* [Ejemplos de código de Azure Batch](https://github.com/Azure/azure-batch-samples)

## Servicios de Azure relacionados

* [Factoría de datos](https://azure.microsoft.com/documentation/services/data-factory/)

* [Aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [Servicios en la nube](https://azure.microsoft.com/documentation/services/cloud-services/)

* [Servicios multimedia](https://azure.microsoft.com/documentation/services/media-services/)



## Pasos siguientes

* Para los anuncios más recientes, vea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Vea también [Novedades de Batch](https://azure.microsoft.com/updates/?service=batch) o suscríbase a la [fuente RSS](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0128_2016-->