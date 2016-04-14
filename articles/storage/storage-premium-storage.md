<properties
	pageTitle="Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure | Microsoft Azure"
	description="El Almacenamiento premium le ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un uso intensivo de E/S, que se ejecutan en máquinas virtuales de Azure. La serie DS de Azure y las máquinas virtuales de la serie GS admiten el Almacenamiento premium."
	services="storage"
	documentationCenter=""
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="prkhad"/>


# Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure

## Información general

El Almacenamiento premium de Azure le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales con cargas de trabajo intensivas de E/S. Discos de máquinas virtuales que usan datos de almacén de Almacenamiento premium en unidades de estado sólido (SSD). Puede migrar los discos de máquina virtual de su aplicación al Almacenamiento premium de Azure para aprovechar la velocidad y rendimiento de estos discos.

Una máquina virtual de Azure admite la conexión de varios discos de Almacenamiento premium para que las aplicaciones puedan disponer de hasta 64 TB de almacenamiento por máquina virtual. Con el Almacenamiento premium, las aplicaciones pueden tener hasta 80.000 E/S por segundo (operaciones de entrada/salida por segundo) por máquina virtual, así como un rendimiento del disco de 2.000 MB por segundo por máquina virtual, con latencias extremadamente bajas para operaciones de lectura.

Con el Almacenamiento premium, Azure ofrece la capacidad de levantar y mover realmente sus exigentes aplicaciones empresariales como Dynamics AX, Dynamics CRM, Exchange Server, granjas de servidores de SharePoint y el conjunto de aplicaciones de SAP Business, a la nube. Puede ejecutar una variedad de cargas de trabajo de bases de datos de rendimiento intensivo como SQL Server, Oracle, MongoDB, MySQL, Redis, que requieren una baja latencia y un alto rendimiento coherentes en el almacenamiento Premium.

>[AZURE.NOTE] Se recomienda migrar cualquier disco de máquina virtual que requiera un número elevado de operaciones de entrada/salida por segundo al Almacenamiento premium de Azure para mejorar el rendimiento de la aplicación. Si el disco no requiere un número elevado de operaciones de entrada/salida por segundo, puede limitar los costos mediante el mantenimiento del almacenamiento estándar, que almacena los datos de disco de máquina virtual en unidades de disco duro (HDD) en lugar de SSD.

Para empezar a usar Almacenamiento premium de Azure, visite la página [Empiece de forma gratuita](https://azure.microsoft.com/pricing/free-trial/). Para información sobre cómo migrar las máquinas virtuales existentes al Almacenamiento premium, vea [Migrar al Almacenamiento premium de Azure](storage-migration-to-premium-storage.md).

## Cosas importantes acerca del Almacenamiento premium

La siguiente es una lista de las cosas importantes que deben tenerse en cuenta antes o al usar Almacenamiento premium:

- Para usar Almacenamiento premium, debes tener una cuenta de Almacenamiento premium. Para obtener información sobre cómo crear una cuenta de Almacenamiento premium, consulte [Creación y uso de una cuenta de Almacenamiento Premium para discos de datos de máquina virtual](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

- Almacenamiento premium está disponible en el [Portal de Azure](https://portal.azure.com) y es accesible a través de las siguientes bibliotecas de SDK: [API de REST de almacenamiento](http://msdn.microsoft.com//library/azure/dd179355.aspx) versión 2014-02-14 o posterior; [API de REST de administración del servicio](http://msdn.microsoft.com/library/azure/ee460799.aspx) versión 2014-10-01 o posterior (implementaciones de la versión clásica); [Referencia de la API de REST del proveedor de recursos de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/mt163683.aspx) (implementaciones de ARM); y [Azure PowerShell](../powershell-install-configure.md) versión 0.8.10 o posterior.

- Para una lista de regiones que admiten actualmente almacenamiento Premium, vea [Servicios de Azure por región](https://azure.microsoft.com/regions/#services).

- Almacenamiento premium admite solo blobs en páginas de Azure, que se usan para contener discos persistentes para máquinas virtuales de Azure (VM). Para obtener información sobre los blobs en páginas de Azure, vea [Información sobre los blobs en bloque, los blobs en anexos y los blobs en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx). Almacenamiento premium no admite blobs en bloque de Azure, blobs en anexos de Azure, archivos de Azure, tablas de Azure ni colas de Azure.

- Una cuenta de Almacenamiento premium es almacenamiento con redundancia local (LRS) y mantiene tres copias de los datos en una única región. Para consideraciones relativas a la replicación geográfica al usar Almacenamiento premium, consulte la sección [Instantáneas y copia de blob al Almacenamiento premium](#snapshots-and-copy-blob-whes-ESing-premium-storage) en este artículo.

- Si quiere usar una cuenta de Almacenamiento premium para los discos de VM, necesitará usar la serie DS o GS de VM. Puede usar discos de Almacenamiento estándar y premium con la serie DS o GS de VM. Pero no puede usar discos de Almacenamiento premium con series que no sean DS o GS de VM. Para obtener información sobre los tamaños y tipos de disco de máquina virtual de Azure disponibles, vea [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md).

- El proceso para configurar los discos de Almacenamiento Premium para una máquina virtual es similar al de los discos de Almacenamiento Estándar. Deberá elegir la opción de Almacenamiento premium más adecuada para sus discos y máquina virtual de Azure. El tamaño de la máquina virtual debe ser el adecuado para su carga de trabajo en función de las características de rendimiento de las ofertas Premium. Para obtener más información, consulte [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](#scalability-and-performance-targets-whes-ESing-premium-storage).

- Una cuenta de almacenamiento premium no se puede asignar a un nombre de dominio personalizado.

- El análisis de almacenamiento no se admite actualmente para el Almacenamiento premium. Para analizar las métricas de rendimiento de máquinas virtuales con discos en cuentas de Almacenamiento premium, utilice las herramientas en función del sistema operativo, tales como [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) para máquinas virtuales Windows e [IOSTAT](http://linux.die.net/man/1/iostat) para máquinas virtuales Linux. También puede habilitar los diagnósticos de máquinas virtuales de Azure en el Portal de Azure. Consulte [Supervisión de máquinas virtuales de Microsoft Azure con la extensión de Diagnósticos de Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) para ver información detallada.

## Uso de Almacenamiento Premium para discos
Puede usar Almacenamiento premium para discos de una de las siguientes maneras:

- Crear una nueva cuenta de almacenamiento premium y usarla al crear la máquina virtual.
- Cree una nueva VM de serie DS o GS. Al crear la máquina virtual, puede seleccionar una cuenta de Almacenamiento premium creada anteriormente, crear una nueva o dejar que el Portal de Azure cree una cuenta premium de manera predeterminada.

Azure usa la cuenta de almacenamiento como un contenedor para el sistema operativo (SO) y los discos de datos. En otras palabras, si crea una VM de serie DS o GS de Azure y selecciona una cuenta de Almacenamiento premium de Azure, el sistema operativo y los discos de datos se almacenan en dicha cuenta de almacenamiento.

Para información sobre cómo migrar las máquinas virtuales existentes al Almacenamiento premium, vea [Migrar al Almacenamiento premium de Azure](storage-migration-to-premium-storage.md).

Para aprovechar las ventajas de Almacenamiento premium, cree primero una cuenta de Almacenamiento premium mediante un tipo de cuenta *Premium\_LRS*. Para ello, puede usar el [Portal de Azure](https://portal.azure.com), [Azure PowerShell](../powershell-install-configure.md), la [API de REST de administración de servicio](http://msdn.microsoft.com/library/azure/ee460799.aspx) (implementaciones de la versión clásica) o la [API de REST de proveedor de recursos de almacenamiento](http://msdn.microsoft.com/library/azure/mt163683.aspx) (implementaciones de ARM). Para obtener instrucciones paso a paso, consulte [Creación y uso de una cuenta de Almacenamiento premium para un disco de datos de la máquina virtual](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

### Notas importantes:

- Para obtener información sobre los límites de capacidad y de ancho de banda de la vista previa de las cuentas de Almacenamiento premium, consulte la sección [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](#scalability-and-performance-targets-whes-ESing-premium-storage). Si las necesidades de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación para que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento. Por ejemplo, si quiere conectar discos de 51 terabytes (TB) a diferentes máquinas virtuales, distribúyalos entre dos cuentas de almacenamiento ya que 35 TB es el límite de una sola cuenta de Almacenamiento premium. Asegúrese de que una sola cuenta de Almacenamiento premium no tenga nunca más de 35 TB de discos aprovisionados.
- De forma predeterminada, la directiva de almacenamiento en caché de los discos es "Solo lectura" para todos los discos de datos Premium y "Lectura y Escritura" para el disco de sistema operativo Premium conectado a la máquina virtual. Se recomienda esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación.
- Asegúrese de que hay suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco. Por ejemplo, una máquina virtual STANDARD\_DS1 tiene un ancho de banda dedicado de 32 MB por segundo disponible para el tráfico de disco de Almacenamiento premium. Esto significa que un disco de Almacenamiento premium P10 conectado a esta máquina virtual solo puede tener hasta 32 MB por segundo, pero no hasta los 100 MB por segundo que puede proporcionar el disco P10. De forma similar, una máquina virtual STANDARD\_DS13 puede llegar hasta 256 MB por segundo en todos los discos. Actualmente, la máquina virtual más grande de la serie DS es STANDARD\_DS14 y puede proporcionar hasta 512 MB por segundo en todos los discos. La VM más grande de la serie GS es STANDARD\_GS5 y puede proporcionar hasta 2000 MB por segundo en todos los discos.

	Tenga en cuenta que estos límites son para el tráfico de disco por sí solo, sin incluir los aciertos de caché y el tráfico de red. Hay un ancho de banda independiente disponible para el tráfico de red de máquina virtual, que es diferente del ancho de banda dedicado para discos de Almacenamiento premium.

	Para obtener la información más actualizada sobre el número máximo de E/S por segundo y el rendimiento (ancho de banda) de las máquinas virtuales de las series DS y GS, vea [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md). Para información acerca de los discos del Almacenamiento premium y sus E/S por segundo y límites de rendimiento, vea la tabla que encontrará en la sección [Objetivos de escalabilidad y rendimiento al usar el Almacenamiento premium](#scalability-and-performance-targets-whes-ESing-premium-storage) de este artículo.

> [AZURE.NOTE] Los aciertos de caché no están limitados por las E/S o rendimiento asignados del disco. Es decir, cuando se utiliza un disco de datos con la configuración de caché de solo lectura en una VM de la serie DS o GS, las lecturas que se atienden desde la memoria caché no están sujetas a los límites de disco de Almacenamiento premium. Por lo tanto, podría obtener un rendimiento muy alto desde un disco si la carga de trabajo es fundamentalmente de lectura. Tenga en cuenta que la caché depende de límites de E/S por segundo o de rendimiento independientes a nivel de la máquina virtual en función del tamaño de dicha máquina. Las máquinas virtuales de la serie DS tienen aproximadamente 4000 E/S por segundo y 33 MB/seg. por núcleo de caché y E/S de SSD locales.

- Puede usar discos de Almacenamiento premium y estándar en la misma VM de la serie DS o GS.
- Con el Almacenamiento Premium, puede aprovisionar una máquina virtual de serie DS y conectar varios discos de datos persistentes a una máquina virtual. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen. Si secciona discos de datos Almacenamiento premium usando [Espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), será necesario configurarlo con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. De forma predeterminada, la interfaz de usuario (IU) del administrador del servidor permite configurar columnas de hasta 8 discos. Pero si tiene más de 8 discos, necesitará usar PowerShell para crear el volumen y especificar manualmente el número de columnas. De lo contrario, la IU del administrador del servidor sigue usando 8 columnas aunque tenga más discos. Por ejemplo, si tiene 32 discos en un conjunto de bandas único, debe especificar 32 columnas. Puede usar el parámetro *NumberOfColumns* del cmdlet [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) de PowerShell para especificar el número de columnas que usa el disco virtual. Para obtener más información, consulte [Storage Spaces Overview](http://technet.microsoft.com/library/hh831739.aspx) (Introducción a espacios de almacenamiento) y [Storage Spaces Frequently Asked Questions](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx) (Preguntas más frecuentes sobre espacios de almacenamiento).
- Evite agregar máquinas virtuales de la serie DS a un servicio en la nube existente que incluya máquinas virtuales que no son de la serie DS. Una posible solución consiste en migrar los VHD existentes a un nuevo servicio en la nube ejecutando solo máquinas virtuales de la serie DS. Si desea conservar la misma dirección IP virtual (VIP) para el nuevo servicio en la nube que hospeda las máquinas virtuales de la serie DS, use la característica [Direcciones IP reservadas](../virtual-network/virtual-networks-instance-level-public-ip.md). Las VM de la serie GS pueden agregarse a un servicio en la nube existente ejecutando solo VM de la serie G.
- La serie DS de máquinas virtuales de Azure se puede configurar para utilizar un disco del sistema operativo (SO) hospedado en una cuenta de Almacenamiento estándar o en una cuenta de Almacenamiento premium. Si utiliza el disco del sistema operativo solo para el arranque, puede usar un disco del sistema operativo basado en el almacenamiento estándar. Esto proporciona ventajas económicas y resultados de rendimiento similares al Almacenamiento premium tras el arranque. Si realiza cualquier tarea adicional en el disco del sistema operativo que no sea de arranque, use el Almacenamiento premium ya que proporciona mejores resultados de rendimiento. Por ejemplo, si la aplicación lee y escribe desde y hacia el disco del sistema operativo, el uso del disco del sistema operativo basado en Almacenamiento premium proporciona un mejor rendimiento para la máquina virtual.
- Puede usar la [Interfaz de línea de comandos de Azure (CLI de Azure)](../xplat-cli-install.md) con Almacenamiento premium. Para cambiar la directiva de caché en uno de los discos mediante la CLI de Azure, ejecute el siguiente comando:

	`$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>`

	Tenga en cuenta que las opciones de directiva de almacenamiento en caché pueden ser ReadOnly, None o ReadWrite. Para ver más opciones, consulte la Ayuda ejecutando el comando siguiente:

	`azure vm disk attach --help`


## Objetivos de escalabilidad y rendimiento al usar Almacenamiento Premium

Cuando aprovisiona un disco con una cuenta de Almacenamiento premium, la cantidad de operaciones de E/S por segundo (IOPS) y el rendimiento (ancho de banda) que se pueden obtener depende del tamaño del disco. Actualmente, hay tres tipos de discos de Almacenamiento premium: P10, P20 y P30. Cada uno tiene límites de IOPS y de rendimiento específicos, tal como se especifica en la tabla siguiente:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Tipo de disco de Almacenamiento premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Tamaño del disco</strong></td>
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>E/S por segundo por disco</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5.000</td>
</tr>
<tr>
	<td><strong>Rendimiento por disco</strong></td>
	<td>100 MB por segundo *</td>
	<td>150 MB por segundo *</td>
	<td>200 MB por segundo *</td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Asegúrese de que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco, tal como se explica en la sección [Uso de Almacenamiento Premium para discos](#using-premium-storage-for-disks) mostrada anteriormente en este artículo. De lo contrario, el rendimiento y las E/S por segundo del disco estarán obligados a reducir los valores basándose en los límites de la máquina virtual en vez de en los límites de disco que se mencionan en la tabla anterior.

Azure asigna el tamaño de disco (redondeado) a la opción de disco de Almacenamiento premium más cercana según se especifica en la tabla. Por ejemplo, un disco de 100 GB se clasifica como una opción P10, puede realizar hasta 500 unidades de E/S por segundo y dispone de un rendimiento de hasta 100 MB por segundo. De forma similar, un disco de 400 GB se clasifica como una opción P20, puede realizar hasta 2300 unidades de E/S por segundo y dispone de un rendimiento de hasta 150 MB por segundo.

El tamaño de la unidad de entrada y salida (E/S) es de 256 KB. Si el tamaño de los datos transferidos es inferior a 256 KB, se considera una sola unidad de E/S. Los tamaños de E/S más grandes se cuentan como varias operaciones de E/S con un tamaño de 256 KB. Por ejemplo, una E/S de 1100 KB se cuenta como cinco unidades de E/S.

El límite de rendimiento incluye escrituras en el disco, así como lecturas desde ese disco que no se sirven desde la memoria caché. Por ejemplo, la suma de las lecturas y de las escrituras que no son de caché debe ser inferior o igual a 100 MB por segundo para un disco P10. De forma similar, por ejemplo, un único disco P10 puede tener 100 MB por segundo de lecturas que no son de caché o 100 MB por segundo de escrituras, o 60 MB por segundo de lecturas con 40 MB de escrituras por segundo.

Al crear los discos de Azure, seleccione la oferta de disco de Almacenamiento premium más adecuada según las necesidades de su aplicación en cuanto a capacidad, rendimiento, escalabilidad y picos de carga.

> [AZURE.NOTE] Puede aumentar fácilmente el tamaño de los discos existentes. Por ejemplo, si desea aumentar el tamaño de un disco de 30 GB a 128 GB o 1 TB. O bien, si lo desea, convertir el disco P20 en disco P30 porque necesita más capacidad o más E/S por segundo y rendimiento. Puede expandir el disco mediante el commandlet "Update-AzureDisk" de PowerShell con la propiedad "-ResizedSizeInGB". Para realizar esta acción, se debe desconectar el disco de la máquina virtual o se debe detener la máquina virtual.

En la tabla siguiente se describen los objetivos de escalabilidad para las cuentas de Almacenamiento premium:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacidad total de la cuenta</strong></td>
	<td><strong>Ancho de banda total de una cuenta de almacenamiento con redundancia local</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacidad de disco: 35 TB</li>
       <li type=round>Capacidad de instantánea: 10 TB</li>
    </ul>
	</td>
	<td>Hasta 50 gigabits por segundo de entrada y salida</td>
</tr>
</tbody>
</table>

- Entrada se refiere a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento.
- Salida se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

Para obtener más información, consulte [Objetivos de escalabilidad y rendimiento de Almacenamiento de Azure](storage-scalability-targets.md).

## Limitación al usar Almacenamiento Premium
Puede que se produzca una limitación si las E/S por segundo o el rendimiento de la aplicación superan los límites asignados a un disco de Almacenamiento premium o si el tráfico de disco total en todos los discos de la máquina virtual supera el límite de ancho de banda de disco disponible para la máquina virtual. Para evitar la limitación, recomendamos que limite el número de solicitudes de E/S pendientes para discos basándose en los objetivos de rendimiento y escalabilidad que ha aprovisionado y en el ancho de banda de disco disponible para la máquina virtual.

La aplicación puede lograr la menor latencia cuando se ha diseñado para evitar la limitación. Por otro lado, si el número de solicitudes de E/S pendientes del disco es demasiado pequeño, la aplicación no puede aprovechar al máximo los niveles de rendimiento y IOPS que están disponibles en el disco.

En los ejemplos siguientes se muestra cómo calcular los niveles de limitación:

### Ejemplo 1:
La aplicación hizo 495 E/S de tamaño de disco de 16 KB (que es igual a 495 unidades de E/S) en un segundo en un disco P10. Si intenta una E/S de 2 MB en el mismo segundo, el total de unidades de E/S es igual a 495 + 8. Esto es porque una E/S de 2 MB resulta en 2048 KB / 256 KB = 8 unidades de E/S cuando el tamaño de la unidad de E/S es de 256 KB. Puesto que la suma de 495 + 8 supera el límite IOPS de 500 para el disco, se produce la limitación.

### Nota:
Todos los cálculos se basan en el tamaño de una unidad de E/S de 256 KB.

### Ejemplo 2:
La aplicación hizo 400 E/S de tamaño de disco de 256 KB en un disco P10. El ancho de banda total consumido es (400 * 256) / 1024 = 100 MB/s. Observe que el límite de rendimiento de un disco P10 es de 100 MB por segundo. Si la aplicación intenta realizar más operaciones de E/S en ese segundo, se limita porque supera el límite asignado.

### Ejemplo 3:
Tiene una máquina virtual DS4 con dos discos P30 conectados. Cada disco P30 es capaz de tener un rendimiento de 200 MB por segundo. Sin embargo, una máquina virtual DS4 tiene una capacidad de ancho de banda de disco total de 256 MB por segundo. Por lo tanto, no puede dirigir los discos conectados al máximo rendimiento en esta máquina virtual DS4 al mismo tiempo. Para resolver este problema, puede mantener el tráfico de 200 MB por segundo en un disco y 56 MB por segundo en el otro disco. Si la suma del disco tráfico supera los 256 MB por segundo, el tráfico de disco queda limitado.

### Nota:
Si el tráfico de disco consta principalmente de tamaños de E/S pequeños, es muy probable que la aplicación alcance el límite de IOPS antes que el límite de rendimiento. Por otro lado, si el tráfico de disco consiste principalmente en tamaños grandes de E/S, es muy probable que la aplicación alcance el límite de rendimiento en lugar del límite de IOPS. Puede aumentar la tasa de IOPS de su aplicación y la capacidad de rendimiento mediante el uso de tamaños de E/S óptimos y también mediante la limitación del número de solicitudes de E/S pendientes de un disco.

## Instantáneas y copias de blobs al usar Almacenamiento Premium
Puede crear una instantánea para el Almacenamiento premium de la misma que crea una instantánea al usar Almacenamiento estándar. Dado que el Almacenamiento premium es redundante localmente, se recomienda crear instantáneas y, luego, copiarlas en una cuenta de almacenamiento estándar con redundancia geográfica. Para obtener más información, consulte [Opciones de redundancia de Almacenamiento de Azure](storage-redundancy.md).

Si un disco está conectado a una máquina virtual, no se permiten determinadas operaciones de API en el blob en página que respalda el disco. Por ejemplo, no puede realizar una operación [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) en ese blob mientras el disco esté conectado a una máquina virtual. En su lugar, cree una instantánea del blob mediante el método de API de REST [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) y, luego, realice la operación [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) de la instantánea para copiar el disco conectado. Como alternativa, puede desasociar el disco y, luego, realizar las operaciones necesarias en el blob subyacente.

### Notas importantes:

- El número de instantáneas de un único blob está limitado a 100. Una instantánea puede tomarse cada 10 minutos como máximo.
- La capacidad máxima para las instantáneas por cuenta de Almacenamiento premium es de 10 TB. Tenga en cuenta que la capacidad de instantáneas solo se refiere a la cantidad total de datos de instantáneas; no incluye datos en el blob de base.
- Para mantener copias con redundancia geográfica, puede copiar instantáneas desde una cuenta de almacenamiento premium a una cuenta de almacenamiento estándar con redundancia geográfica con AzCopy o Copy Blob. Para obtener más información, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md) y [Copia de blobs](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Para obtener más información acerca de cómo realizar operaciones REST en blobs en página en las cuentas de Almacenamiento premium, consulte [Uso de operaciones de servicios BLOB con Almacenamiento premium de Azure](http://go.microsoft.com/fwlink/?LinkId=521969) en MSDN library.

## Uso de máquinas virtuales Linux con Almacenamiento premium
Consulte a continuación instrucciones importantes para configurar las máquinas virtuales Linux en Almacenamiento premium:

- Para todos los discos de Almacenamiento premium con la configuración de caché como “ReadOnly” o “None”, debe deshabilitar “barreras” al montar el sistema de archivos a fin de lograr los objetivos de escalabilidad para Almacenamiento Premium. No es necesita barreras para este escenario porque las escrituras en los discos de Almacenamiento premium de copia de seguridad son duraderas para esta configuración de caché. Cuando la solicitud de escritura se completa correctamente, los datos están escritos en el almacenamiento permanente. Utilice los métodos siguientes para deshabilitar “barreras” en función del sistema de archivos:
	- Si utiliza **reiserFS**, deshabilite las barreras mediante la opción de montaje “barrier=none” (para habilitar las barreras, use “barrier=flush”)
	- Si utiliza **ext3/ext4**, deshabilite las barreras mediante la opción de montaje “barrier=0” (para habilitar las barreras, use “barrier=1”)
	- Si utiliza **XFS**, deshabilite las barreras mediante la opción de montaje “nobarrier” (para habilitar las barreras, use la opción “barrier”)

- Para discos de Almacenamiento premium con caché de configuración "ReadWrite", deben habilitarse las barreras para la durabilidad de las escrituras.
- Para que las etiquetas de volumen persistan después de reiniciar la VM, debe actualizar/etc/fstab con las referencias UUID a los discos. Consulte también [Acoplamiento de un disco de datos a una máquina virtual de Linux](../virtual-machines/virtual-machines-linux-how-to-attach-disk.md).

Las siguientes son las distribuciones de Linux que se validan con Almacenamiento premium. Se recomienda actualizar las máquinas virtuales al menos a una de estas versiones (o posteriores) para mejorar el rendimiento y la estabilidad con Almacenamiento premium. Además, algunas de las versiones requieren el LIS más reciente (Linux Integration Services v4.0 para Microsoft Azure). Siga el vínculo proporcionado para su descarga e instalación. Seguiremos agregando más imágenes a la lista según vayamos completando validaciones adicionales. Tenga en cuenta que nuestras validaciones demostraron que el rendimiento varía para estas imágenes y también depende de las características de carga de trabajo y la configuración de las imágenes. Se optimizan diferentes imágenes para distintos tipos de cargas de trabajo. <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;"> <tbody> <tr> <td><strong>Distribución</strong></td> <td><strong>Versión</strong></td> <td><strong>Kernel admitido</strong></td> <td><strong>Imagen admitida</strong></td> </tr> <tr> <td rowspan="4"><strong>Ubuntu</strong></td> <td>12.04</td> <td>3.2.0-75.110</td> <td>Ubuntu-12\_04\_5-LTS-amd64-server-20150119-es-ES-30GB</td> </tr> <tr> <td>14.04</td> <td>3.13.0-44.73</td> <td>Ubuntu-14\_04\_1-LTS-amd64-server-20150123-es-ES-30GB</td> </tr> <tr> <td>14.10</td> <td>3.16.0-29.39</td> <td>Ubuntu-14\_10-amd64-server-20150202-es-ES-30GB</td> </tr> <tr> <td>15.04</td> <td>3.19.0-15</td> <td>Ubuntu-15\_04-amd64-server-20150422-es-ES-30GB</td> </tr> <tr> <td><strong>SUSE</strong></td> <td>SLES 12</td> <td>3.12.36-38.1</td> <td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td> </tr> <tr> <td><strong>CoreOS</strong></td> <td>584.0.0</td> <td>3.18.4</td> <td>CoreOS 584.0.0</td> </tr> <tr> <td rowspan="2"><strong>CentOS</strong></td> <td>6.5, 6.6, 6.7, 7.0</td> <td></td> <td> <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 requerido </a> </br> *Vea la nota a continuación </td> </tr> <tr> <td>7.1</td> <td>3.10.0-229.1.2.el7</td> <td> <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 recomendado </a> <br/> *Vea la nota a continuación </td> </tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4.</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 necesarios </a></td>
</tr>
<tr>
	<td>7.0</td>
	<td></td>
	<td>Póngase en contacto con soporte técnico para obtener más información</td>
</tr>
</tbody> </table>


### Controladores de LIS para CentOS Openlogic

Los clientes que ejecutan las máquinas virtuales de OpenLogic CentOS tienen que ejecutar el comando siguiente para instalar a los controladores más recientes:

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

A continuación, se le pedirá un reinicio para activar los nuevos controladores.



## Precios y facturación al usar Almacenamiento Premium
Al usar Almacenamiento premium, se aplican las siguientes consideraciones de facturación:

- La facturación de un disco de Almacenamiento premium depende del tamaño aprovisionado del disco. Azure asigna el tamaño de disco (redondeado) a la opción de disco de Almacenamiento premium más cercana según se especifica en la tabla que se proporciona en la sección [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](#scalability-and-performance-targets-whes-ESing-premium-storage). La facturación de cualquier disco aprovisionado se prorratea cada hora con el precio mensual de la oferta de Almacenamiento premium. Por ejemplo, si se aprovisiona un disco P10 y se elimina después de 20 horas, se factura la oferta P10 prorrateada a 20 horas. Esto es así independientemente de la cantidad de datos que se escriba en el disco o del rendimiento/IOPS usado.
- Las instantáneas en el Almacenamiento premium se facturan por la capacidad adicional usada por las instantáneas. Para obtener información sobre las instantáneas, consulte [Crear una instantánea de un blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- Las [transferencias de datos de salida](https://azure.microsoft.com/pricing/details/data-transfers/) (datos que salen de los centros de datos de Azure) incurren en la facturación por el uso de ancho de banda.

Para obtener información detallada sobre los precios de Almacenamiento premium y VM de las series DS y GS, consulte:

- [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/)

## Creación y uso de una cuenta de Almacenamiento premium para un disco de datos de la máquina virtual

En esta sección se muestra cómo crear una cuenta de Almacenamiento premium mediante el Portal de Azure, Azure PowerShell y la Interfaz de línea de comandos de Azure (CLI de Azure). Además, muestra un caso de uso de ejemplo para las cuentas de almacenamiento premium: crear una máquina virtual y adjuntar un disco de datos a una máquina virtual al usar Almacenamiento premium.

### Creación de una máquina virtual de Azure mediante Almacenamiento premium a través del Portal de Azure

En esta sección se muestra cómo crear una cuenta de Almacenamiento premium mediante el Portal de Azure.

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com). Consulte la oferta [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) si todavía no tiene una suscripción.

2.	En el menú Concentrador, haga clic en **Nuevo**.

3.	En **Nuevo**, haga clic en **Todo**. Seleccione **Almacenamiento, caché, + copia de seguridad**. Desde allí, haga clic en **Almacenamiento** y, luego, en **Crear**.

4.	En el cuadro Cuenta de almacenamiento, escriba un nombre para la cuenta de almacenamiento. Haga clic en **Nivel de precios**. En la hoja **Niveles de precios recomendados**, haga clic en **Examinar todos los niveles de precios**. En la hoja **Elegir nivel de precios**, elija **Redundancia local premium**. Haga clic en **Seleccionar**. Tenga en cuenta que la hoja **Cuenta de almacenamiento** muestra **GRS estándar** como **Nivel de precios** predeterminado. Tras hacer clic en **Seleccionar**, el **Nivel de precios** se muestra como **Premium\_LRS**.

	![Nivel de precios][Image1]


5.	En la hoja **Cuenta de almacenamiento**, mantenga los valores predeterminados de **Grupo de recursos**, **Suscripción**, **Ubicación** y **Diagnósticos**. Haga clic en **Crear**.

Para obtener un tutorial completo del entorno de Azure, vea [Creación de una máquina virtual de Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-tutorial.md).

### Creación de una máquina virtual de Azure mediante Almacenamiento premium a través de Azure PowerShell
Este ejemplo de PowerShell muestra cómo crear una nueva cuenta de Almacenamiento premium y conectar un disco de datos que use esa cuenta a una nueva máquina virtual de Azure.

1. Configure el entorno de PowerShell siguiendo los pasos que se indican en [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
2. Inicie la consola de PowerShell, conéctese a su suscripción y ejecute el cmdlet siguiente de PowerShell en la ventana de la consola. Tal como se muestra en la instrucción de PowerShell, deberá especificar el parámetro **Type** como **Premium\_LRS** al crear una cuenta de Almacenamiento premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. A continuación, cree una nueva VM de la serie DS y especifique que quiere Almacenamiento premium mediante la ejecución de los siguientes cmdlets de PowerShell en la ventana de consola. Puede crear una VM de la serie GS con los mismos pasos. Especifique el tamaño de VM adecuado en los comandos. Para, por ejemplo, Standard\_GS2:

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. Si quiere más espacio en disco para la VM, adjunte un nuevo disco de datos a una VM de la serie DS o GS existente después de crearlo mediante la ejecución de los siguientes cmdlets de PowerShell en la ventana de consola:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

### Creación de una máquina virtual de Azure mediante Almacenamiento premium a través de la interfaz de línea de comandos de Azure

La [Interfaz de la línea de comandos de Azure](../xplat-cli-install.md) (CLI de Azure) proporciona un conjunto de comandos de código abierto multiplataforma para trabajar con la plataforma Azure. Los ejemplos siguientes muestran cómo utilizar la CLI de Azure (versión 0.8.14 y posterior) para crear una cuenta de Almacenamiento premium, una nueva máquina virtual y conectar un nuevo disco de datos desde una cuenta de Almacenamiento premium.

#### Crear una cuenta de Almacenamiento premium

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### Creación de una máquina virtual de la serie DS

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-es-ES-30GB" -u "myusername" -p "passwd@123"

#### Visualización de información acerca de la máquina virtual

	azure vm show premium-test-vm

#### Acoplamiento de un nuevo disco de datos

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

#### Visualización de información acerca del nuevo disco de datos

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

## Pasos siguientes

- [Uso de operaciones de servicios BLOB con Almacenamiento premium de Azure](http://go.microsoft.com/fwlink/?LinkId=521969)
- [Migrar a Almacenamiento premium de Azure](storage-migration-to-premium-storage.md)
- [Creación de una máquina virtual de Windows en el Portal de Azure.](../virtual-machines/virtual-machines-windows-tutorial.md)
- [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md)
- [Documentación de almacenamiento](https://azure.microsoft.com/documentation/services/storage/)

[Image1]: ./media/storage-premium-storage/Azure_pricing_tier.png

<!---HONumber=AcomDC_0224_2016-->