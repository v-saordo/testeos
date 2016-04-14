
<properties
   pageTitle="Seguridad de los clústeres de Service Fabric: roles del cliente | Microsoft Azure"
   description="En este artículo, se describen los dos roles del cliente y los permisos que otorga cada uno de ellos."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/09/2016"
   ms.author="subramar"/>



# Control de acceso basado en roles para clientes de Service Fabric

Azure Service Fabric admite dos tipos distintos de control de acceso para los clientes que están conectados a un clúster de Service Fabric: administrador y usuario. El control de acceso permite al administrador de clústeres limitar el acceso a determinadas operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster.

Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.

Especifique los dos roles de cliente (administrador y cliente) cuando cree el clúster con certificados independientes para cada uno. Vea el artículo sobre [seguridad del clúster de Service Fabric](service-fabric-cluster-security.md) para obtener información detallada sobre cómo configurar un clúster seguro de Service Fabric.


## Configuración del control de acceso predeterminado


El tipo de control de acceso del administrador permite acceder a todas las API de FabricClient. Se puede realizar cualquier operación de lectura y escritura en el clúster de Service Fabric, como:

### Operaciones en servicios y aplicaciones
* **CreateService**: creación de servicios 							
* **CreateServiceFromTemplate**: creación de servicios a partir de plantillas 							
* **UpdateService**: actualizaciones de servicios 							
* **DeleteService**: eliminación de servicios 							
* **ProvisionApplicationType**: aprovisionamiento del tipo de aplicación 							
* **CreateApplication**: creación de aplicaciones   							
* **DeleteApplication**: eliminación de aplicaciones 							
* **UpgradeApplication**: inicio o interrupción del proceso de actualización de las aplicaciones 							
* **UnprovisionApplicationType**: desaprovisionamiento de tipos de aplicaciones 							
* **MoveNextUpgradeDomain**: reanudación de las actualizaciones de aplicaciones con un dominio de actualización explícito 							
* **ReportUpgradeHealth**: reanudación de las actualizaciones de aplicaciones con el progreso de actualización actual 							
* **ReportHealth**: generación de informes sobre el estado 							
* **PredeployPackageToNode**: API de preimplementación							
* **CodePackageControl**: reinicio de paquetes de código 							
* **RecoverPartition**: recuperación de una partición 							
* **RecoverPartitions**: recuperación de particiones 							
* **RecoverServicePartitions**: recuperación de particiones de servicio 							
* **RecoverSystemPartitions**: recuperación de particiones de servicio de sistema 							


### Operaciones del clúster
* **ProvisionFabric**: aprovisionamiento de MSI o manifiesto de clúster 							
* **UpgradeFabric**: inicio de actualizaciones del clúster 							
* **UnprovisionFabric**: desaprovisionamiento de MSI o manifiesto de clúster 						
* **MoveNextFabricUpgradeDomain**: reanudación de las actualizaciones del clúster con un dominio de actualización explícito 							
* **ReportFabricUpgradeHealth**: reanudación de las actualizaciones del clúster con el progreso de actualización actual 							
* **StartInfrastructureTask**: inicio de tareas de infraestructura 							
* **FinishInfrastructureTask**: finalización de tareas de infraestructura 							
* **InvokeInfrastructureCommand**: comandos de administración de tareas de infraestructura  							
* **ActivateNode**: activación de un nodo 							
* **DeactivateNode**: desactivación de un nodo 							
* **DeactivateNodesBatch**: desactivación de varios nodos 							
* **RemoveNodeDeactivations**: revertir la desactivación en varios nodos 							
* **GetNodeDeactivationStatus**: comprobación del estado de desactivación 							
* **NodeStateRemoved**: generación de informes sobre el estado del nodo eliminado 							
* **ReportFault**: generación de informes sobre errores 							
* **FileContent**: transferencia de archivos del cliente al almacén de imágenes (externo al clúster) 							
* **FileDownload**: inicio de la descarga de archivos del cliente al almacén de imágenes (externo al clúster) 							
* **InternalList**: operación de enumeración en listas de los archivos del cliente en el almacén de imágenes (interno) 							
* **Delete**: operación de eliminación del cliente del almacén de imágenes  							
* **Upload**: operación de carga del cliente en el almacén de imágenes 							
* **NodeControl**: inicio, detención y reinicio de los nodos 							
* **MoveReplicaControl**: movimiento de réplicas de un nodo a otro 							

### Otras operaciones
* **Ping**: acción de hacer ping al cliente 							
* **Query**: todas las consultas permitidas
* **NameExists**: comprobaciones de la existencia del identificador URI de la nomenclatura 							



El tipo de control de acceso de usuario, de forma predeterminada, está limitado a las siguientes operaciones. (El control de acceso de administrador también tiene acceso a estas operaciones).

* **EnumerateSubnames**: enumeración del identificador URI de la nomenclatura 							
* **EnumerateProperties**: enumeración de las propiedades de la nomenclatura 							
* **PropertyReadBatch**: operaciones de lectura de las propiedades de la nomenclatura 							
* **GetServiceDescription**: notificaciones del servicio de sondeo largo y descripciones del servicio de lectura 							
* **ResolveService**: resolución del servicio basado en reclamaciones 							
* **ResolveNameOwner**: resolución del propietario del identificador URI de la nomenclatura 							
* **ResolvePartition**: resolución de los servicios del sistema 							
* **ServiceNotifications**: notificaciones del servicio basadas en eventos 							
* **GetUpgradeStatus**: sondeo sobre el estado de actualización de la aplicación 							
* **GetFabricUpgradeStatus**: sondeo sobre el estado de actualización del clúster 							
* **InvokeInfrastructureQuery**: consulta sobre las tareas de infraestructura 							
* **List**: operación de enumeración en listas de los archivos del cliente en el almacén de imágenes 							
* **ResetPartitionLoad**: restablecimiento de la carga para una unidad de conmutación por error 							
* **ToggleVerboseServicePlacementHealthReporting**: cambio al sistema de informes detallados sobre el estado de la ubicación de los servicios 							

## Cambio de la configuración predeterminada para los roles de cliente

En el archivo de manifiesto de clúster, puede proporcionar capacidades de administración al cliente si es necesario. Puede cambiar los valores predeterminados mediante la opción **Fabric Settings** durante la [creación del clúster](service-fabric-cluster-creation-via-portal.md) y especificar la configuración especificada anteriormente en los campos **nombre**, **admin**, **usuario** y **valor**.

## Pasos siguientes

[Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)

[Creación de clústeres de Service Fabric](service-fabric-cluster-creation-via-portal.md)

<!---HONumber=AcomDC_0211_2016-->