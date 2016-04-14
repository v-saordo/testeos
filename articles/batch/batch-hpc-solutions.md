<properties
   pageTitle="Soluciones de lote y HPC en la nube | Microsoft Azure"
   description="Presenta escenarios (Big Compute) de informática de alto rendimiento y de ejecución por lotes y opciones de solución de Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="01/21/2016"
   ms.author="danlep"/>

# Soluciones de lote y HPC en la nube de Azure

Azure ofrece soluciones en la nube eficientes y escalables para ejecución por lotes e informática de alto rendimiento (HPC), también llamadas *Big Compute*. Obtenga información aquí sobre cargas de trabajo Big Compute y servicios de Azure para admitirlas, o bien vaya directamente a [escenarios de solución](#scenarios) más adelante en este artículo. Este artículo está destinado principalmente a los encargados de la toma de decisiones técnicas, los administradores de TI y los proveedores de software independientes, aunque otros profesionales de TI y desarrolladores pueden usarlo para familiarizarse con estas soluciones.

Las organizaciones tienen problemas informáticos a gran escala como el diseño de ingeniería y el análisis, la representación de imágenes, los modelos complejos, las simulaciones de Monte Carlo y los cálculos de riesgos financieros. Azure ayuda a las organizaciones a solucionar estos problemas y tomar decisiones con los recursos, la escala y la programación que necesitan. Con Azure, las organizaciones pueden:

* Crear soluciones híbridas, ampliando un clúster de HPC local para las descargas de cargas de trabajo máximas a la nube

* Ejecución de cargas de trabajo y herramientas de clúster HPC completamente en Azure

* Usar un servicio administrado y escalable de Azure como [lote](https://azure.microsoft.com/documentation/services/batch/) para ejecutar cargas de trabajo de proceso intensivo sin tener que implementar y administrar la infraestructura de proceso

Aunque fuera del ámbito de este artículo, Azure también proporciona a los desarrolladores y partners un conjunto completo de funcionalidades, opciones de arquitectura y herramientas de desarrollo para crear flujos de trabajo Big Compute a gran escala y personalizados. Y un ecosistema de partners creciente está listo para ayudarle a aumentar la productividad de las cargas de trabajo Big Compute en la nube de Azure.


## Aplicaciones HPC y de ejecución por lotes

A diferencia de las aplicaciones web y de muchas aplicaciones de línea de negocio, las aplicaciones HPC y de ejecución por lotes tienen un inicio y una finalización definidos y pueden ejecutarse de manera programada o a petición, a veces durante horas o más tiempo. La mayoría se dividen en dos categorías principales: *intrínsecamente paralelas* (en ocasiones denominada "embarazosamente paralelas", porque los problemas que solucionan se prestan a ejecutarse en paralelo en varios procesadores o equipos) y *estrechamente acopladas*. Para obtener más información acerca de estos tipos de aplicaciones, consulte la tabla siguiente. Algunas soluciones de Azure funcionan mejor para un tipo u otro.

>[AZURE.NOTE] En las soluciones HPC y de ejecución por lotes, una instancia en ejecución de una aplicación normalmente se denomina un *trabajo* y cada trabajo podría dividirse en *tareas*. Y los recursos de proceso en clúster para la aplicación se denominan a menudo *nodos de ejecución*.

Escriba | Características | Ejemplos
------------- | ----------- | ---------------
**Intrínsecamente paralelos**<br/><br/>![Intrínsecamente paralelos][parallel] |• Los equipos individuales ejecutan la lógica de la aplicación de forma independiente<br/><br/> • Agregar equipos permite que la aplicación escale y reduzca el tiempo de cálculo<br/><br/>• La aplicación consta de archivos ejecutables independientes o se divide en un grupo de servicios invocados por un cliente (una arquitectura orientada a servicios o SOA, aplicación) |• Modelado de riesgos financieros<br/><br/>• Representación y procesamiento de imágenes<br/><br/>• Codificación y transcodificación multimedia<br/><br/>• Simulaciones de Monte Carlo<br/><br/>• Pruebas de Software
**Estrechamente acoplados**<br/><br/>![Estrechamente acoplados][coupled] |• La aplicación requiere la Interactuación de los nodos de ejecución o el intercambio de resultados intermedios<br/><br/>• Los nodos de ejecución pueden comunicarse mediante la Interfaz de Paso de Mensajes (MPI), un protocolo de comunicación común para la informática paralela<br/><br/>• La aplicación es sensible a la latencia de red y al ancho de banda<br/><br/>• El rendimiento de la aplicación se puede mejorar mediante tecnologías de redes de alta velocidad como InfiniBand y acceso directo a memoria remoto (RDMA) |• Modelado de depósitos de petróleo y gas •<br/><br/>• Diseño de ingeniería y análisis, como la dinámica de fluidos computacionales<br/><br/>• Simulaciones físicas como choques de automóviles y reacciones nucleares<br/><br/>• Previsión meteorológica de

### Consideraciones para la ejecución de aplicaciones HPC y de ejecución en lotes en la nube

Puede migrar fácilmente muchas aplicaciones que están diseñadas para ejecutarse en clústeres HPC locales a Azure o a un entorno híbrido (entre entornos). Sin embargo, puede haber algunas limitaciones o consideraciones, entre las que se incluyen:


* **Disponibilidad de los recursos en la nube**: según el tipo de recursos de proceso en la nube que use, es posible que no pueda disfrutar de disponibilidad continua de la máquina durante la ejecución de un trabajo. Señalización de comprobación de progreso y control de estado son técnicas comunes para controlar posibles errores transitorios, y resultan más necesarias al aprovechar los recursos en la nube.


* **Acceso a datos**: las técnicas de acceso a datos normalmente disponibles en un clúster de red empresarial, por ejemplo, NFS, es posible que requieran una configuración especial en la nube, o puede que deban adoptar prácticas de acceso de datos y patrones diferentes para la nube.

* **Movimiento de datos**: para las aplicaciones que procesan grandes cantidades de datos, es necesario idear estrategias para mover los datos al almacenamiento en la nube y para procesar recursos, y es posible que necesite la conexión de redes entre instalaciones de alta velocidad como [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Considere también las limitaciones legales, normativas o de directivas para almacenar o acceder a esos datos.


* **Licencias**: póngase en contacto con el proveedor de cualquier aplicación comercial para obtener información acerca de las licencias u otras restricciones para la ejecución en la nube. No todos los proveedores ofrecen licencias de pago por uso. Es posible que deba planear un servidor de licencias en la nube para su solución o conectarse a un servidor de licencias local.


### ¿Big Compute o Big Data?

La línea divisoria entre aplicaciones Big Compute y de macrodatos no siempre está clara y es posible que algunas aplicaciones tengan características de ambas. Ambas incluyen cálculos a gran escala, normalmente en clústeres de equipos. Pero los enfoques de las soluciones y las herramientas de soporte técnico pueden diferir.

• **Big Compute** suele implicar a las aplicaciones que se basan en la capacidad y la memoria de la CPU, como simulaciones de ingeniería, modelado de riesgos financieros y representación digital. Es posible que los clústeres que están detrás de una solución Big Compute incluyan equipos con procesadores de varios núcleos especializados para realizar cálculos sin formato y hardware especializado de alta velocidad para conectar los equipos.

• **Big Data** soluciona problemas de análisis de datos que implican grandes cantidades de datos que no pueden administrarse mediante un único equipo o sistema de administración de datos, como grandes volúmenes de registros web o de otros datos de inteligencia empresarial. Los macrodatos se basan más en la capacidad del disco y el rendimiento de E/S que en la potencia de la CPU, y podrían usar herramientas especializadas, como Apache Hadoop, para administrar el clúster y efectuar particiones en los datos. (Para obtener información acerca de Azure HDInsight y otras soluciones Azure Hadoop, consulte[Hadoop](https://azure.microsoft.com/solutions/hadoop/)).

## Administración de procesos y programación de trabajos

La ejecución de las aplicaciones de lote y HPC normalmente incluye un *administrador de clústeres* y un *programador de trabajos* para ayudar a administrar los recursos de proceso en clúster y asignarlos a las aplicaciones que ejecutan los trabajos. Estas funciones pueden realizarse mediante herramientas independientes o una herramienta o servicio integrado.

* **Administrador de clústeres**: aprovisiona, publica y administra recursos de proceso (o nodos de ejecución). Un administrador de clústeres podría automatizar la instalación de imágenes de sistema operativo y aplicaciones en nodos de ejecución, escalar los recursos de proceso en función de la demanda y supervisar el rendimiento de los nodos.

* **Programador de trabajos**: especifica los recursos (por ejemplo, los procesadores o la memoria) que necesita una aplicación y las condiciones que determinan cuándo se ejecutará. Un programador de trabajos mantiene una cola de trabajos y les asigna recursos en función de una prioridad asignada u otras características.

Las herramientas de agrupación en clústeres y programación de trabajos para clústeres basados en Windows y Linux pueden migrarse correctamente a Azure. Por ejemplo, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) (solución de clúster de proceso gratis de Microsoft para cargas de trabajo HPC de Windows y Linux) ofrece varias opciones para ejecución en Azure. También puede crear clústeres de Linux para ejecutar herramientas de código abierto como par y SLURM, o ejecutar herramientas comerciales como [TIBCO DataSynapse GridServer](http://www.tibco.com/products/automation/application-development/grid-computing/gridserver) y [Univa Grid Engine](http://www.univa.com/products/grid-engine) en Azure.

Como se muestra en las secciones siguientes, también pueden sacar partido de los servicios de Azure para administrar recursos de proceso y programar trabajos sin (o además de) las herramientas de administración de clústeres tradicionales.


## Escenarios

Aquí se muestran tres escenarios comunes para ejecutar cargas de trabajo de Big Compute en Azure mediante el aprovechamiento de soluciones de clúster HPC existentes, servicios de Azure o una combinación de ambos. Se enumeran consideraciones clave para elegir cada escenario pero no son exhaustivas. Posteriormente en este artículo, puede obtener más información sobre los servicios de Azure disponibles que puede usar en la solución.

 | Escenario | ¿Por qué elegirlo?
------------- | ----------- | ---------------
**Irrumpir un clúster HPC en Azure**<br/><br/>[![Ráfaga de clúster][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Más información:<br/>• [Burst to Azure with Microsoft HPC Pack (Irrumpir en Azure con Microsoft HPC Pack)](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Set up a hybrid compute cluster with Microsoft HPC Pack (Configurar un clúster de proceso híbrido con Microsoft HPC Pack)](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>|• Combinar el clúster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) local con recursos adicionales de Azure en una solución híbrida.<br/><br/>• Ampliar las cargas de trabajo Big Compute para que se ejecuten en instancias de máquina virtual de plataforma como servicio (PaaS) (actualmente solo Windows Server).<br/><br/>• Acceso a un almacén de datos o servidor de licencias local mediante una red virtual opcional de Azure.|• Tiene un clúster de HPC Pack existente y necesita más recursos. <br/><br/>• No quiere comprar y administrar una infraestructura de clúster de HPC adicional.<br/><br/>• Tiene períodos de máxima demanda transitorios o proyectos especiales.
**Crear un clúster HPC completamente en Azure**<br/><br/>[![Clúster en IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Más información:<br/>• [HPC cluster solutions in Azure (Soluciones de clúster HPC en Azure)](./big-compute-resources.md)<br/><br/>|• Implementar rápida y coherentemente las aplicaciones y herramientas de clúster en máquinas virtuales de infraestructura como servicio (IaaS) de Windows o Linux estándar o personalizadas.<br/><br/>• Ejecutar diversas cargas de trabajo Big Compute mediante la solución de programación de trabajos que prefiera.<br/><br/>• Usar servicios de Azure adicionales, como redes y almacenamiento, para crear soluciones completas basadas en nube. |• No desea comprar y administrar una infraestructura de clúster HPC de Linux o Windows adicional.<br/><br/>• Tienen períodos de máxima demanda transitorios o proyectos especiales.<br/><br/>• Necesita un clúster adicional durante un período de tiempo pero no desea invertir en equipos y espacio para implementarlo.<br/><br/>• Desea descargar la aplicación de proceso intensivo para que se ejecute como un servicio completamente en la nube.
**Escalar horizontalmente una aplicación paralela en Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Más información:<br/>• [Datos básicos de Lote de Azure](./batch-technical-overview.md)<br/><br/>• [Introducción a la biblioteca de Lote de Azure para .NET](./batch-dotnet-get-started.md)|• Desarrollar con las API de [Lote de Azure](https://azure.microsoft.com/documentation/services/batch/) para escalar horizontalmente diversas cargas de trabajo Big Compute para ejecutar en grupos de máquinas virtuales de plataforma como servicio (PaaS) (actualmente solo Windows Server).<br/><br/>• Usar un servicio de Azure para administrar la implementación y el escalado automático de máquinas virtuales, la programación de trabajos, la recuperación ante desastres, el movimiento de datos, la administración de dependencias y la implementación de aplicaciones (sin necesidad de un programador de trabajos o clúster HPC independiente).|• No desea administrar recursos de proceso o un programador de trabajos; en su lugar, desea centrarse en la ejecución de las aplicaciones.<br/><br/>• Desea descargar la aplicación de proceso intensivo para que se ejecute como un servicio en la nube.<br/><br/>• Desea escalar automáticamente los recursos de proceso para que coincidan con la carga de trabajo de proceso.


## Servicios de Azure para Big Compute

Aquí hay más información sobre servicios de proceso, datos, redes y otros servicios relacionados que puede combinar para soluciones y flujos de trabajo Big Compute. Para obtener información detallada acerca de los servicios de Azure, consulte la [documentación](https://azure.microsoft.com/documentation/) de dichos servicios. Los [escenarios](#scenarios) anteriores de este artículo muestran solo algunas formas de utilizar estos servicios.

>[AZURE.NOTE] Azure presenta nuevos servicios con cierta frecuencia que podrían ser útiles para su escenario. Si tiene alguna pregunta, póngase en contacto con un [asociado de Azure](https://pinpoint.microsoft.com/es-ES/search?keyword=azure) o envíe un correo electrónico a **bigcompute@microsoft.com*.

### Servicios de proceso

Servicios de proceso de Azure son el núcleo de una solución Big Compute y los diversos servicios de proceso ofrecen ventajas para diferentes escenarios. En un nivel básico, estos servicios ofrecen modos distintos para que las aplicaciones se ejecuten en instancias de proceso basadas en máquinas virtuales que Azure proporciona mediante tecnología Windows Server Hyper-V. Estas instancias pueden ejecutar una amplia variedad de herramientas y sistemas operativos Linux y Windows estándar y personalizados. Azure le ofrece [tamaños de instancia](../virtual-machines/virtual-machines-size-specs.md) con diferentes configuraciones de núcleos de CPU, memoria, capacidad de disco y otras características. Según sus necesidades puede escalar las instancias a miles de núcleos y, a continuación, reducirlas cuando se necesiten menos recursos.

>[AZURE.NOTE] Puede aprovechar las instancias A8-A11 para mejorar el rendimiento de algunas cargas de trabajo HPC, incluidas las aplicaciones MPI paralelas que requieren una red de aplicaciones de baja latencia y alto rendimiento. Consulte [Sobre las instancias informáticas intensivas A8, A9, A10 y A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

Servicio | Descripción
------------- | -----------
**[Servicios en la nube](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Puede ejecutar aplicaciones de Big Compute en instancias de rol de trabajo, que son máquinas virtuales que ejecutan una versión de Windows Server y están administradas completamente por Azure<br/><br/>• Habilite aplicaciones escalables y confiables con baja sobrecarga administrativa, que se ejecutan en un modelo de plataforma como servicio (PaaS)<br/><br/>• Es posible que requieran herramientas adicionales o desarrollo para la integración de soluciones de clúster HPC locales
**[Máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Proporcionan una infraestructura de proceso como servicio (IaaS)<br/><br/>• Permiten aprovisionar y administrar de manera flexible equipos en la nube persistentes desde imágenes de Windows Server o Linux estándar o imágenes y discos de datos proporcionados por usted o de [Azure Marketplace](https://azure.microsoft.com/marketplace/)<br/><br/>• Ejecutan herramientas y aplicaciones de clúster de proceso locales completamente en la nube
**[Lote](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Ejecuta cargas de trabajo en lotes y paralelas a gran escala en un servicio totalmente administrado.<br/><br/>• Proporciona la programación de trabajos y el escalado automático de un grupo de máquinas virtuales administrado.<br/><br/>• Permite a los programadores crear y ejecutar aplicaciones como un servicio o habilitar aplicaciones existentes para la nube.<br/>

### Servicios de almacenamiento

Normalmente, una solución Big Compute funciona en un conjunto de datos de entrada y genera datos para sus resultados. Entre algunos de los servicios de almacenamiento de Azure que se usan en soluciones Big Compute se incluyen los siguientes:

* [Almacenamiento de blobs, tablas y colas](https://azure.microsoft.com/documentation/services/storage/): administre grandes cantidades de datos no estructurados, datos NoSQL y mensajes de flujo de trabajo y comunicación, respectivamente. Por ejemplo, puede usar el almacenamiento de blobs para grandes conjuntos de datos técnicos o las imágenes de entrada o archivos multimedia que su aplicación procesa. Puede usar colas para la comunicación asincrónica en una solución. Vea [Introducción a Almacenamiento de Microsoft Azure](../storage/storage-introduction.md).

* [Almacenamiento de archivos de Azure](https://azure.microsoft.com/services/storage/files/): comparte archivos comunes y datos en Azure mediante el protocolo SMB estándar, que es necesario para algunas soluciones de clúster HPC.

### Servicios de datos y análisis

Algunos escenarios Big Compute implican flujos de datos a gran escala o generan datos que necesitan un procesamiento o análisis posterior. Para ello, Azure ofrece una serie de servicios de datos y análisis, entre los que se incluyen:

* [Factoría de datos](https://azure.microsoft.com/documentation/services/data-factory/): crea flujos de trabajo controlados por datos (canalizaciones) que unen, agregan y transforman datos de almacenes de datos locales, basados en la nube y de Internet.

* [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/): proporciona las características clave de un sistema de administración de base de datos relacional de Microsoft SQL Server en un servicio administrado.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): implementa y aprovisiona clústeres de Apache Hadoop basados en Windows Server o Linux en la nube para administrar, analizar e informar sobre macrodatos.

* [Aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/): le ayuda a crear, probar, operar y administrar soluciones de análisis predictivo en un servicio totalmente administrado.

### Servicios adicionales

Es posible que la solución Big Compute necesite otros servicios de Azure para conectarse a recursos locales o de otros entornos. Algunos ejemplos son:

* [Red virtual](https://azure.microsoft.com/documentation/services/virtual-network/): crea una sección aislada lógicamente en Azure para conectarse a recursos de Azure en su centro de datos local o en un único equipo cliente con IPSec; permite que las aplicaciones Big Compute tengan acceso a datos locales, servicios de Active Directory y servidores de licencias

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/): crea una conexión privada entre los centros de datos de Microsoft y la infraestructura local o en un entorno de ubicación compartida, con una mayor seguridad, más confiabilidad, velocidades más rápidas y latencias más bajas que las de las conexiones normales a través de Internet.

* [Bus de servicio](https://azure.microsoft.com/documentation/services/service-bus/): proporciona varios mecanismos para que las aplicaciones se comuniquen o intercambien datos, independientemente de si están ubicadas en Azure, en otra plataforma en la nube o en un centro de datos.

## Pasos siguientes

* Consulte [Recursos técnicos para informática de alto rendimiento (HPC) y computación por lotes](big-compute-resources.md) para encontrar orientación técnica para crear su solución.

* Converse sobre las opciones de Azure con partners, como Cycle Computing y UberCloud.

* Obtenga información sobre soluciones Big Compute de Azure proporcionadas por [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/) y [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Para los anuncios más recientes, vea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=AcomDC_0218_2016-->