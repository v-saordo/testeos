<properties
   pageTitle="Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga: vista previa | Microsoft Azure"
   description="Uso de PowerShell para el Equilibrador de carga con el Administrador de recursos de Azure (ARM) en vista previa. Uso de plantillas para el equilibrador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga 

El Administrador de recursos de Azure (ARM) es el nuevo marco de administración de servicios en Azure. El Equilibrador de carga de Azure puede administrarse ahora mediante las herramientas y las API basadas en el Administrador de recursos de Azure. Para obtener más información sobre el Administrador de recursos de Azure, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-preview-portal-using-resource-groups.md).

## Conceptos

Con ARM, el Equilibrador de carga de Azure contiene los siguientes recursos secundarios:

- Configuración de dirección IP front-end: un equilibrador de carga puede incluir una o varias direcciones IP front-end, conocidas también como IP virtuales (VIP). Estas direcciones IP sirven como entrada para el tráfico.

- Grupo de direcciones back-end: se trata de direcciones IP asociadas a las tarjetas de interfaz de red (NIC) de máquina virtual a las que se distribuirá la carga.

- Reglas de equilibrio de carga: una propiedad de regla asigna una combinación dada de dirección IP front-end y puerto a un conjunto de combinaciones de direcciones IP back-end y puerto. Con una única definición de un recurso de equilibrador de carga, puede definir varias reglas de equilibrio de carga, donde cada regla refleja una combinación de dirección IP de front-end y puerto y de dirección IP de back-end y puerto asociada con las máquinas virtuales.

- Sondeos: los sondeos permiten realizar un seguimiento del estado de las instancias de máquina virtual. Si se produce un error en un sondeo de estado, la instancia de VM se sacará automáticamente de la rotación.

- Reglas NAT entrantes: las reglas NAT que definen el tráfico entrante que fluye a través de la dirección IP front-end y se distribuye a la dirección IP back-end.


![](./media/load-balancer-arm/load-balancer-arm.png)



## Plantillas de inicio rápido
El Administrador de recursos de Azure le permite aprovisionar sus aplicaciones mediante una plantilla declarativa. En una plantilla, puede implementar varios servicios junto con sus dependencias. Use la misma plantilla para implementar su aplicación de forma repetida durante cada fase de su ciclo de vida.

Las plantillas incluyen Máquinas virtuales, Redes virtuales, Conjuntos de disponibilidad, Interfaces de red (NIC), Cuentas de almacenamiento, Equilibradores de carga, Grupos de seguridad de red y Direcciones IP públicas. Con las plantillas puede crear todo lo que necesita para una aplicación compleja con un simple archivo que puede proteger y también colaborar en él.

[Más información sobre las plantillas](http://go.microsoft.com/fwlink/?LinkId=544798)

[Más información sobre los recursos de red](../resource-groups-networking)

Las plantillas que usan el Equilibrador de carga de Azure se pueden encontrar en un [repositorio de GitHub](https://github.com/Azure/azure-quickstart-templates) que hospeda un conjunto de plantillas generadas por la comunidad.

Ejemplos de plantillas:

- [Dos máquinas virtuales en un Equilibrador de carga y reglas de equilibrio de carga](http://go.microsoft.com/fwlink/?LinkId=544799)

- [Dos máquinas virtuales en una red virtual con un Equilibrador de carga interno y reglas del Equilibrador de carga](http://go.microsoft.com/fwlink/?LinkId=544800)

- [Dos máquinas virtuales en un Equilibrador de carga y configuración de reglas NAT en el Equilibrador de carga](http://go.microsoft.com/fwlink/?LinkId=544801)


## Configuración del Equilibrador de carga de Azure con PowerShell o CLI

Los [cmdlets de Redes de Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) pueden usarse para crear un equilibrador de carga. Introducción a los cmdlets de ARM y las API de REST

- [Cómo crear un Equilibrador de carga mediante el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md)

- [Uso de CLI de Azure con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager)

- [API de REST del Equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## Pasos siguientes

También puede [empezar a crear un equilibrador de carga orientado a Internet](load-balancer-get-started-internet-arm-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento especifico del tráfico de red del equilibrador de carga.

Si la aplicación necesita mantener conexiones activas para servidores detrás de un equilibrador de carga, puede obtener más información acerca de la [configuración de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md). Le ayudará a conocer el comportamiento de conexión del tiempo de inactividad cuando se usa el Equilibrador de carga de Azure.

<!---HONumber=AcomDC_0218_2016-->