<properties
	pageTitle="Información general de conjuntos de escala de máquinas virtuales | Microsoft Azure"
	description="Más información sobre conjuntos de escala de máquinas virtuales"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Información general de conjuntos de escala de máquinas virtuales

Los conjuntos de escala de máquinas virtuales son un recurso de Proceso de Azure que se puede usar para implementar y administrar un conjunto de máquinas virtuales idénticas. Con todas las máquinas virtuales configuradas de la misma manera, los conjuntos de escala de máquinas virtuales están diseñados para admitir el escalado automático verdadero (no es necesario aprovisionar las máquinas virtuales antes) y, por tanto, facilitan la creación de servicios a gran escala cuyo objetivo son las cargas de trabajo en contenedor, de macroproceso y macrodatos.

[AZURE.INCLUDE [virtual-machines-vmss-preview](../../includes/virtual-machines-vmss-preview-ps-include.md)]

Para las aplicaciones que necesiten escalar y reducir horizontalmente los recursos de proceso, las operaciones de escala se equilibran implícitamente en dominios de actualización y de error. Para una introducción a los conjuntos de escala de máquinas virtuales, consulte el reciente [anuncio en el blog de Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/).

Eche un vistazo a estos vídeos para más información sobre los conjuntos de escala de máquinas virtuales:

 - [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## Creación y administración de conjuntos de escala de máquinas virtuales

Los conjuntos de escala de máquinas virtuales se pueden definir e implementar mediante plantillas JSON y [API de REST](https://msdn.microsoft.com/library/mt589023.aspx) igual que las máquinas virtuales individuales de Administrador de recursos de Azure. Por lo tanto, pueden utilizarse cualquier método de implementación estándar de Administrador de recursos de Azure. Para más información sobre las plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md).

Se puede encontrar un conjunto de plantillas de ejemplo para los conjuntos de escala de máquinas virtuales en el repositorio de GitHub de plantillas de inicio rápido de Azure, aquí:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - busque plantillas con _vmss_ en el título.

En las páginas de detalle de estas plantillas, verá un botón que vincula con la característica de implementación del portal. Para implementar el conjunto de escala de máquinas virtuales, haga clic en el botón y después rellene los parámetros que son necesarios en el portal. Si no está seguro de si un recurso admite mayúsculas o la mezcla de mayúsculas y minúsculas, es más seguro usar siempre los valores de parámetro en minúsculas. Aquí hay también una práctica disección de vídeo de una plantilla del conjunto de escala de máquinas virtuales:

[VM Scale Set Template Dissection](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## Escalado y reducción verticales de un conjunto de escala de máquinas virtuales

Para aumentar o disminuir el número de máquinas virtuales en un conjunto de escala de máquinas virtuales, basta con cambiar la propiedad _capacity_ y volver a implementar la plantilla. Esta sencillez facilita la escritura de su propia capa de escalado personalizada si quiere definir eventos personalizados de escalado que no sean compatibles con el escalado automático de Azure.

Si va a volver a implementar una plantilla para modificar la capacidad, puede definir una plantilla mucho más pequeña que solo incluya la SKU y la capacidad actualizada. Aquí se muestra un ejemplo de esto: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Para recorrer los pasos que crean un conjunto de escala que se escala automáticamente, consulte [Escalado automático de máquinas en un conjunto de escalado de máquinas virtuales](virtual-machines-vmss-walkthrough.md).

## Supervisión del conjunto de escala de máquinas virtuales

Actualmente se recomienda usar el [Explorador de recursos de Azure](https://resources.azure.com) para ver los conjuntos de escala de máquinas virtuales. Los conjuntos de escala de máquinas virtuales son un recurso en Microsoft.Compute, por lo que en este sitio puede verlos si expande los vínculos siguientes:

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## Escenarios con conjuntos de escala de máquinas virtuales

En esta sección se enumeran algunos escenarios típicos de conjunto de escala de máquinas virtuales. Algunos servicios de Azure de niveles superiores (por ejemplo, Lote, Service Fabric, servicio Contenedor de Azure) usarán estos escenarios.

 - **RDP/SSH a instancias de conjuntos de escala de máquinas virtuales**: se crea un conjunto de escala de máquinas virtuales dentro de una red virtual y a las máquinas virtuales individuales no se les asignan direcciones IP públicas. Esto es algo bueno, ya que normalmente no desea los costos ni la sobrecarga administrativa que supone la asignación de direcciones IP independientes a todos los recursos sin estado en la cuadrícula de procesos y puede conectarse fácilmente a estas máquinas virtuales desde otros recursos en la red virtual, incluidas aquellas con direcciones IP públicas, como los equilibradores de carga o las máquinas virtuales independientes.

 - **Conexión a máquinas virtuales con reglas NAT**: puede crear una dirección IP pública, asignarla a un equilibrador de carga y definir reglas NAT entrantes que asignen un puerto de la dirección IP a un puerto de una máquina virtual en el conjunto de escala de máquinas virtuales. Por ejemplo,

	IP pública->Puerto 50000 -> vmss\_0->Puerto 22 IP pública->Puerto 50001 -> vmss\_1->Puerto 22 IP pública->Puerto 50002-> vmss\_2->Puerto 22

	Aquí hay un ejemplo de creación de un conjunto de escalado de máquinas virtuales que usa reglas NAT para permitir la conexión SSH a cada máquina virtual de un conjunto de escalado mediante una sola dirección IP pública: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).

	Este es un ejemplo que hace lo mismo con RDP y Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat).

 - **Conexión a máquinas virtuales con un "jumpbox"**: si crea un conjunto de escala de máquinas virtuales y una máquina virtual independiente en la misma red virtual, la máquina virtual independiente y las máquinas virtuales del conjunto de escala de máquinas virtuales pueden conectarse entre sí mediante sus direcciones IP internas como se define en la red virtual y la subred. Si crea una dirección IP pública y la asigna a la máquina virtual independiente, puede conectarse mediante RDP o SSH a la máquina virtual independiente y después conectarse desde esa máquina a las instancias del conjunto de escala de máquinas virtuales. En este punto, es posible que observe que un simple conjunto de escalado de máquinas virtuales es de forma inherente más seguro que una simple máquina virtual independiente con una dirección IP pública en su configuración predeterminada.

	Para ver un ejemplo de este enfoque, esta plantilla crea un clúster sencillo de Mesos compuesto por una máquina virtual maestra independiente que administra un clúster de máquinas virtuales basado en un conjunto de escala de máquinas virtuales: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json).

 - **Equilibrio de carga round robin en instancias del conjunto de escala de máquinas virtuales**: si desea entregar trabajo a un clúster de proceso de máquinas virtuales con un enfoque "round robin", puede configurar un Equilibrador de carga de Azure con reglas de equilibrio de carga según corresponda. También puede definir sondeos para comprobar que la aplicación se esté ejecutando; estos hacen ping a puertos con un protocolo, un intervalo y una ruta de acceso de solicitud especificados.

	Este es un ejemplo que crea un conjunto de escalado de máquinas virtuales con máquinas virtuales que ejecutan un servidor web de IIS y usa un equilibrador de carga para equilibrar la carga que recibe cada máquina virtual. También usa el protocolo HTTP para hacer ping a una dirección URL específica en cada máquina virtual: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json). Examine el tipo de recurso Microsoft.Network/loadBalancers, networkProfile y extensionProfile en virtualMachineScaleSet.

 - **Implementación de un conjunto de escala de máquinas virtuales como un clúster de proceso en un administrador de clústeres PaaS**: los conjuntos de escala de máquinas virtuales se describen como un rol de trabajo de próxima generación. Es una descripción válida pero también existe el riesgo de confundir las características del conjunto de escalado con las del rol de trabajo v1 de PaaS. En cierto sentido, los conjuntos de escalado de máquinas virtuales proporcionan un verdadero "rol de trabajo" o recurso de trabajo, pues suministran un recurso de proceso generalizado que no depende de la plataforma ni del tiempo de ejecución, es personalizable y se integra en IaaS del Administrador de recursos de Azure.

	Un rol de trabajo de PaaS v1, aunque está limitado en cuanto a la compatibilidad con la plataforma y el tiempo de ejecución (solo imágenes de la plataforma Windows), incluye servicios (como intercambio de VIP, parámetros de actualización configurables o una configuración específica para la implementación de aplicaciones y para tiempo de ejecución) que no están _todavía_ disponibles en los conjuntos de escala de máquinas virtuales o que se ofrecerán con otros servicios de PaaS de nivel superior como Service Fabric. Teniendo esto en cuenta, puede considerar los conjuntos de escalado de máquinas virtuales como una infraestructura que admite PaaS, es decir, las soluciones de PaaS como Service Fabric o los administradores de clústeres como Mesos pueden partir de los conjuntos de escalado de máquinas virtuales como una capa de proceso escalable.

	Este es un ejemplo de un clúster de Mesos que implementa un conjunto de escalado de máquinas virtuales en la misma red virtual que una máquina virtual independiente. La máquina virtual independiente es maestra en Mesos y el conjunto de escala de máquinas virtuales representa un conjunto de nodos subordinados: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Las versiones futuras del [servicio Contenedor de Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implementarán versiones más complejas o reforzadas de este escenario basado en conjuntos de escala de máquinas virtuales.

## Guía de la escala y el rendimiento del conjunto de escala de máquinas virtuales

- Durante la vista previa pública, no cree más de 500 máquinas virtuales en varios conjuntos de escalado de máquinas virtuales a la vez.
- No planee más de 40 máquinas virtuales por cuenta de almacenamiento.
- Distancie las primeras letras de los nombres de las cuentas de almacenamiento lo más posible. Las plantillas de conjunto de escalado de máquinas virtuales de ejemplo en la página de [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/) muestran cómo hacerlo.
- Si usa máquinas virtuales personalizadas, no planee más de 40 máquinas virtuales por conjunto de escalado de máquinas virtuales en una única cuenta de almacenamiento. Necesitará copiar antes la imagen en la cuenta de almacenamiento para poder comenzar la implementación del conjunto de escalado de máquinas virtuales. Consulte las preguntas más frecuentes para más información.
- No planee más de 2048 máquinas virtuales por red virtual. Este límite aumentará en el futuro.
- El número de máquinas virtuales que se pueden crear está limitado por la cuota de núcleos de proceso en cualquier región. Puede que necesite ponerse en contacto con el servicio de soporte al cliente para aumentar el límite de cuota de proceso incluso si ya tiene un límite alto de núcleos para su uso con los servicios en la nube o IaaS v1. Para consultar la cuota, puede ejecutar el siguiente comando en la CLI de Azure: _azure vm list-usage_, y el siguiente comando de PowerShell: _Get-AzureRmVMUsage_ (si usa una versión de PowerShell anterior a 1.0, use _Get-AzureVMUsage_).

## Preguntas más frecuentes sobre los conjuntos de escala de máquinas virtuales

**P.** ¿Cuántas máquinas virtuales puede tener en un conjunto de escala de máquinas virtuales?

**R.** 100 si usa imágenes de plataforma que se puedan distribuir entre varias cuentas de almacenamiento. Si usa imágenes personalizadas, hasta 40, porque las imágenes personalizadas se limitan a una única cuenta de almacenamiento durante la vista previa.

**P** ¿Qué otros límites de recursos existen para los conjuntos de escala de máquinas virtuales?

**R.** También existe un límite de creación de no más de 500 máquinas virtuales en varios conjuntos de escala por región durante el período de versión preliminar. Se aplican los [límites del servicio de suscripción de Azure](../azure-subscription-service-limits.md) existentes.

**P.** ¿Se admiten discos de datos en los conjuntos de escala de máquinas virtuales?

**R.** No en la versión inicial. Las opciones para almacenar datos son:

- Archivos de Azure (unidades compartidas SMB)

- Unidad de sistema operativo

- Unidad temporal (local, no respaldada por almacenamiento de Azure)

- Servicio de datos externo (por ejemplo, base de datos remota, tablas de Azure, blobs de Azure)

**P.** ¿Qué regiones de Azure admiten conjuntos de escala de máquinas virtuales?

**R.** Cualquier región que admita el Administrador de recursos de Azure admite conjuntos de escalado de máquinas virtuales.

**P.** ¿Cómo se crea un conjunto de escala de máquinas virtuales con una imagen personalizada?

**R.** Deje la propiedad vhdContainers en blanco; por ejemplo:

	"storageProfile": {
		"osDisk": {
			"name": "vmssosdisk",
			"caching": "ReadOnly",
			"createOption": "FromImage",
			"image": {
				"uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
			},
			"osType": "Windows"
		}
	},


**P.** Si reduzco la capacidad de mi conjunto de escala de máquinas virtuales de 20 a 15, ¿qué máquinas virtuales se quitarán?

**R.** Las máquinas virtuales se quitan uniformemente del conjunto de escala por los dominios de actualización y dominios de error para potenciar al máximo la disponibilidad.

**P.** ¿Y si después aumento la capacidad de 15 a 18?

**R.** Si la aumenta a 18, se crearán máquinas virtuales con los índices 15, 16 y 17. En ambos casos, las máquinas virtuales se equilibran entre dominios de error y de actualización.

**P.** Si uso varias extensiones en un conjunto de escala de máquinas virtuales, ¿se puede aplicar una secuencia de ejecución?

**R.** No directamente pero, para la extensión customScript, el script podría esperar a que se completara otra extensión (por ejemplo, supervisando el registro de extensiones; consulte [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**P.** ¿Funcionan los conjuntos de escala de máquinas virtuales con los conjuntos de disponibilidad de Azure?

**R.** Sí. Un conjunto de escala es de forma implícita un conjunto de disponibilidad con tres dominios de error y cinco dominios de actualización. No es necesario configurar nada en virtualMachineProfile. En futuras versiones, es probable que los conjuntos de escala abarquen varios inquilinos pero, por ahora, un conjunto de escala es un único conjunto de disponibilidad.

<!---HONumber=AcomDC_0128_2016-->