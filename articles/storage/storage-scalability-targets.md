<properties
   pageTitle="Objetivos de escalabilidad y rendimiento del almacenamiento | Microsoft Azure"
   description="Obtenga información sobre los objetivos de escalabilidad y rendimiento para Almacenamiento de Azure, incluida la capacidad, la velocidad de solicitudes y el ancho de banda entrante y saliente para las cuentas de almacenamiento tanto estándar como premium. Comprenda los objetivos de rendimiento para las particiones en cada uno de los servicios de Almacenamiento de Azure."
   services="storage"
   documentationCenter="na"
   authors="robinsh"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="02/21/2016"
   ms.author="robinsh" />

# Objetivos de escalabilidad y rendimiento del almacenamiento de Azure

## Información general

Este tema trata cuestiones de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure. Para ver un resumen de otros límites de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md).

>[AZURE.NOTE] Todas las cuentas de almacenamiento se ejecutan en la nueva topología de red plana y admiten los objetivos de escalabilidad y rendimiento descritos a continuación, independientemente de cuándo se crearon. Para obtener más información acerca de la arquitectura de red plana del almacenamiento de Azure y de la escalabilidad, vea [Almacenamiento de Microsoft Azure: un servicio de almacenamiento en la nube altamente disponible](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

<!-- -->

>[AZURE.IMPORTANT] Los objetivos de escalabilidad y rendimiento que mencionamos aquí son objetivos exigentes, pero se pueden lograr. En todos los casos, la velocidad de solicitudes y el ancho de banda obtenido por la cuenta de almacenamiento depende del tamaño de los objetos almacenados, de los patrones de acceso utilizados y del tipo de carga de trabajo que realiza la aplicación. Asegúrese de probar el servicio para determinar si el rendimiento se ajusta a sus requisitos. Si es posible, evite picos en la tasa de tráfico y asegúrese de que este se distribuya equitativamente entre las particiones.

>Cuando la aplicación alcanza el límite de lo que puede administrar una partición para la carga de trabajo, Almacenamiento de Azure comenzará a responder con el código de error 503 (servidor ocupado) o el código de error 500 (tiempo de espera de operación). Cuando esto ocurre, la aplicación debe utilizar una directiva de retroceso exponencial para los reintentos. El retroceso exponencial permite que disminuya la carga de la partición y evita los picos de tráfico en esa partición.

Si las necesidades de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, puede compilar la aplicación de forma que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento. Consulte [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) para obtener información sobre los precios por volumen.


## Objetivos de escalabilidad para blobs, colas, tablas y archivos

[AZURE.INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

## Objetivos de escalabilidad para discos de máquinas virtuales

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../../includes/azure-storage-limits-vm-disks.md)]

Consulte [Tamaños de máquina virtual](../virtual-machines/virtual-machines-size-specs.md) para obtener información adicional.

### Cuentas de almacenamiento estándar

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../../includes/azure-storage-limits-vm-disks-standard.md)]

### Cuentas de almacenamiento premium

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../../includes/azure-storage-limits-vm-disks-premium.md)]

## Objetivos de escalabilidad para el Administrador de recursos de Azure

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## Particiones de Almacenamiento de Azure

Cada objeto que contiene datos almacenados en Almacenamiento de Azure (blobs, mensajes, entidades y archivos) pertenece a una partición y se identifica mediante una clave de partición. La partición determina cómo Almacenamiento de Azure equilibra entre servidores la carga de blobs, mensajes, entidades y archivos para satisfacer las necesidades de tráfico de esos objetos. La clave de partición es única dentro de la cuenta de almacenamiento y se utiliza para ubicar un blob, un mensaje o una entidad.

La tabla anterior que aparece en [Objetivos de escalabilidad para las cuentas de almacenamiento estándar](#standard-storage-accounts) muestra los objetivos de rendimiento para una partición única para cada servicio.

Las particiones afectan al equilibrio de carga y la escalabilidad de cada uno de los servicios de almacenamiento de las maneras siguientes:

- **Blobs**: la clave de partición de un blob es el nombre del contenedor + el nombre del blob. Esto significa que cada blob tiene su propia partición. Los blobs, por tanto, se pueden distribuir en varios servidores para escalar horizontalmente el acceso a ellos. Aunque los blobs pueden agruparse lógicamente en contenedores de blob, esta agrupación no afecta a las particiones.

- **Archivos**: la clave de partición para un archivo es nombre de cuenta + nombre de recurso compartido de archivos. Esto significa que todos los archivos de un recurso compartido de archivos también están en una sola partición.

- **Mensajes**: la clave de partición de un mensaje es el nombre de la cola, por lo que todos los mensajes de una cola se agrupan en una sola partición y se sirven mediante un solo servidor. Las distintas colas pueden ser procesadas por distintos servidores para equilibrar la carga de todas las colas que tenga una cuenta de almacenamiento.

- **Entidades**: la clave de partición de una entidad es el nombre de tabla + la clave de partición, donde la clave de partición es el valor de la propiedad obligatoria **PartitionKey** definida por el usuario para la entidad.

	Todas las entidades con el mismo valor de clave de partición se agrupan en la misma partición y se almacenan en el mismo servidor de particiones. Se trata de un punto importante a tener en cuenta en el diseño de la aplicación. La aplicación debería equilibrar las ventajas de escalabilidad que supone propagar las entidades por varias particiones con las ventajas de acceso que supone agrupar las entidades en una sola partición.

	Una ventaja fundamental de la agrupación de un conjunto de entidades de una tabla en una partición única es que permite realizar operaciones por lotes atómicas entre entidades de la misma partición, ya que la partición está en un solo servidor. Por lo tanto, si desea realizar operaciones por lotes, considere la posibilidad de agrupar las entidades con la misma clave de partición.

	Por otra parte, las entidades que, estando en la misma tabla, pertenecen a distintas particiones pueden tener la carga equilibrada entre distintos servidores, lo que permite disponer de una tabla grande con mayor escalabilidad.

## Otras referencias

- [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/)
- [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)
- [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md)
- [Replicación de almacenamiento de Azure](storage-redundancy.md)
- [Lista de comprobación de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure](storage-performance-checklist.md)
- [Almacenamiento de Microsoft Azure: un servicio de almacenamiento en nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_0224_2016-->