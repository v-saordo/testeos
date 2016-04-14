<properties
	pageTitle="Conectar máquinas virtuales en un servicio en la nube | Microsoft Azure"
	description="Conectar una máquina virtual creada con el modelo de implementación clásica en una red virtual o el servicio en la nube."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Conectar máquinas virtuales creadas con el modelo de implementación clásica con un servicio en la nube o red virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Las máquinas virtuales creadas con el modelo de implementación clásico siempre se colocan en un servicio en la nube. El servicio en la nube actúa como contenedor y proporciona un nombre DNS público único, una dirección IP pública y un conjunto de extremos para acceder a la máquina virtual a través de Internet. El servicio en la nube puede estar en una red virtual, pero esto no es un requisito.

Si no es un servicio en la nube en una red virtual, se denomina un servicio en la nube *independiente*. Las máquinas virtuales de un servicio en la nube independiente solo pueden comunicarse con otras máquinas virtuales mediante los nombres DNS públicos de dichas máquinas, y el tráfico viaja a través de Internet. Si un servicio en la nube está en una red virtual, las máquinas virtuales de ese servicio en la nube pueden comunicarse con todas las otras máquinas virtuales de la red virtual sin enviar tráfico a través de Internet.

Si coloca las máquinas virtuales en el mismo servicio en la nube independiente, aún puede usar los equilibrios de cargas y los conjuntos de disponibilidad. Para obtener más información, consulte [Máquinas virtuales de equilibrio de carga](load-balance-virtual-machines.md) y [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md). Sin embargo, no se pueden organizar las máquinas virtuales en subredes ni conectar un servicio en la nube independiente a la red local. Este es un ejemplo:

![Máquinas virtuales en un servicio en la nube independiente](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

Si coloca las máquinas virtuales en una red virtual, puede decidir cuántos servicios en la nube desea utilizar para aprovechar el equilibrio de cargas y los conjuntos de disponibilidad. Además, puede organizar las máquinas virtuales en subredes de la misma manera que en red local y conectar la red virtual a su red local. Este es un ejemplo:

![Máquinas virtuales en una red virtual](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Las redes virtuales son el método recomendado para conectar máquinas virtuales en Azure. La práctica recomendada es configurar cada nivel de la aplicación en un servicio de nube diferente. Sin embargo, puede que necesite combinar algunas máquinas virtuales de diferentes niveles de aplicaciones en el mismo servicio en la nube para no superar el número máximo de 200 servicios en la nube por suscripción. Para revisar este y otros límites, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](azure-subscription-service-limits.md).

## Conexión de máquinas virtuales en una red virtual

Para conectar máquinas virtuales en una red virtual:

1.	Cree la red virtual en el [Portal de Azure](virtual-networks-create-vnet-classic-pportal.md).
2.	Cree el conjunto de servicios en la nube para la implementación de forma que refleje el diseño de los conjuntos de disponibilidad y del equilibrio de carga. En el portal de Azure clásico, haga clic en **Nuevo > Proceso > Servicio en la nube > Creación personalizada** para cada servicio en la nube.
3.	Para crear cada máquina virtual, haga clic en **Nuevo > Proceso > Máquina virtual > Desde la galería**. Elija el servicio en la nube y la red virtual correctos para la máquina virtual. Si el servicio en la nube ya está unido a una red virtual, su nombre se seleccionará automáticamente.

![Selección de un servicio en la nube para una máquina virtual](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Conexión de máquinas virtuales en un servicio en la nube independiente

Para conectar máquinas virtuales en un servicio en la nube independiente:

1.	Cree el servicio en la nube en el [Portal de Azure clásico](http://manage.windowsazure.com). Haga clic en **Nuevo > Proceso > Servicio en la nube > Creación personalizada**. Como alternativa, puede crear el servicio en la nube para su implementación cuando cree la primera máquina virtual.

2.	Cuando cree las máquinas virtuales, elija el nombre del servicio en la nube que creó en el paso anterior.

	![Agregar una máquina virtual a un servicio de nube existente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Recursos
[Máquinas virtuales de equilibrio de carga](load-balance-virtual-machines.md)

[Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md)

Después de haber creado una máquina virtual, es conveniente agregar un disco de datos para que los servicios y las cargas de trabajo tengan una ubicación donde almacenar los datos. Consulte alguno de los recursos siguientes:

[Acoplamiento de un disco de datos a una máquina virtual de Linux](virtual-machines-linux-how-to-attach-disk.md)

[Acoplamiento de un disco de datos a una máquina virtual de Windows](storage-windows-attach-disk.md)

<!---HONumber=AcomDC_0211_2016-->