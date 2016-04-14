<properties 
   pageTitle="Migración de grupos de afinidad a una red virtual regional"
   description="Más información acerca de la migración de grupos de afinidad a una red virtual regional"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Migración de grupos de afinidad a una red virtual regional

Puede utilizar un grupo de afinidad para asegurarse de que los recursos creados en el mismo grupo de afinidad son hospedados físicamente por servidores que están cerca, lo que permite que estos recursos se comuniquen con mayor rapidez. En el pasado, los grupos de afinidad eran un requisito para crear redes virtuales. En ese momento, el servicio de administrador de red que administraba redes virtuales solo podía trabajar en un conjunto de servidores físicos o unidad de escalado. Mejoras arquitectónicas han aumentado el ámbito de administración de red a una región.

Como resultado de estas mejoras de arquitectura, los grupos de afinidad ya no se recomiendan ni son necesarios para las redes virtuales. El uso de grupos de afinidad para redes virtuales se reemplaza por regiones. Las redes virtuales que están asociadas a las regiones se denominan redes virtuales regionales.

Además, se recomienda no usar grupos de afinidad en general. Además del requisito de redes virtuales, también era importante usar los grupos de afinidad para asegurarse de que los recursos, como equipo y almacenamiento, se colocaban cerca. Sin embargo, con la arquitectura de red de Azure actual, estos requisitos de colocación ya no son necesarios. Consulte [Grupos de afinidad y máquinas virtuales](#Affinity-groups-and-VMs) para los pocos casos específicos restantes donde puede desear utilizar un grupo de afinidad.

## Creación y migración a redes virtuales regionales

A partir de ahora, al crear redes virtuales nuevas, utilice *Región*. Verá esto como una opción en el Portal de administración. Tenga en cuenta que en el archivo de configuración de red aparece como *Ubicación*.

>[AZURE.IMPORTANT]Aunque es técnicamente posible crear una red virtual que está asociada a un grupo de afinidad, no hay ninguna razón para hacerlo. Muchas características nuevas, como los grupos de seguridad de red, solo están disponibles cuando se usa una red virtual regional y no están disponibles para las redes virtuales que están asociadas a grupos de afinidad.

### Información acerca de redes virtuales asociadas actualmente a grupos de afinidad

Las redes virtuales que están asociadas actualmente a grupos de afinidad pueden migrarse a redes virtuales regionales. Para migrar a una red virtual regional, siga estos pasos:

1. Exporte un archivo de configuración de red. Puede usar PowerShell o el Portal de administración. Para obtener instrucciones mediante el Portal de administración, consulte [Configurar la red virtual con un archivo de configuración de red](../virtual-networks-using-network-configuration-file/).

1. Edite el archivo de configuración de red y reemplace los valores anteriores por los nuevos valores.

	> [AZURE.NOTE]La **ubicación** es la región que especificó para el grupo de afinidad que está asociado a la red virtual. Por ejemplo, si la red virtual está asociada a un grupo de afinidad que se encuentra en Oeste de EE. UU., al migrar, la ubicación debe apuntar a Oeste de EE. UU.
	
	Edite las líneas siguientes del archivo de configuración de red y reemplace los valores por los suyos propios:

	**Valor antiguo:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\>

	**Nuevo valor:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>

1. Guarde los cambios e [importe](../virtual-networks-using-network-configuration-file/) la configuración de red en Azure.

>[AZURE.INFO]Esta migración NO causa ningún tiempo de inactividad en sus servicios.

## Grupos de afinidad y máquinas virtuales

Como se mencionó anteriormente, los grupos de afinidad ya no suelen recomendarse para las máquinas virtuales. Debe utilizar un grupo de afinidad solo cuando un conjunto de máquinas virtuales debe tener la menor latencia de red absoluta entre las máquinas virtuales. Al colocar las máquinas virtuales en un grupo de afinidad, todas las máquinas virtuales se colocarán en el mismo clúster o unidad de escalado de proceso.

Es importante tener en cuenta que el uso de un grupo de afinidad puede tener dos consecuencias, posiblemente negativas:

- El conjunto de tamaños de máquina virtual estará limitado al conjunto de tamaños de máquina virtual que ofrece la unidad de escalado de proceso.

- Hay una mayor probabilidad de no poder asignar una nueva máquina virtual. Esto sucede cuando la unidad de escalado específica para el grupo de afinidad se queda sin capacidad.

### Qué hacer si tiene una máquina virtual en un grupo de afinidad

No es necesario quitar las máquinas virtuales que están en un grupo de afinidad de dicho grupo.

Una vez implementada una máquina virtual, se implementa en una sola unidad de escalado. Los grupos de afinidad pueden restringir el conjunto de tamaños de máquina virtual disponibles para una nueva implementación de máquina virtual, pero cualquier máquina virtual existente que esté implementa ya está restringida al conjunto de tamaños de máquina virtual disponible en la unidad de escalado en el que se implementa la máquina virtual. Por este motivo, quitar una máquina virtual del grupo de afinidad no tendrá ningún efecto.
 

<!---HONumber=AcomDC_1217_2015-->