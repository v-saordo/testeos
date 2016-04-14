<properties 
	pageTitle="Aplicación de línea de negocio de Azure | Microsoft Azure" 
	description="Obtenga información sobre el valor de una aplicación de línea de negocio de Azure, configure un entorno de prueba e implemente una configuración de alta disponibilidad." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="josephd"/>

# Carga de trabajo de servicios de infraestructura de Azure: aplicación de línea de negocio de alta disponibilidad

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Configure la primera o siguiente aplicación de línea de negocio basada en web solo de intranet en Microsoft Azure y aprovéchese de la facilidad de configuración y de la posibilidad de ampliar rápidamente la aplicación para que incluya una nueva capacidad.
 
Con las máquinas virtuales y las características de la Red virtual de los Servicios de infraestructura de Azure, podrá implementar y ejecutar rápidamente una aplicación de línea de negocio que sea accesible para los usuarios de su organización. Por ejemplo, puede configurar lo siguiente.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Dado que Red Virtual de Azure es una extensión de la red local con toda la nomenclatura correcta y el enrutamiento del tráfico establecidos, los usuarios tendrán acceso a los servidores de la aplicación de línea de negocio de la misma manera que si se encontrara en un centro de datos local.

Esta configuración permite ampliar fácilmente la capacidad de la aplicación agregando nuevas máquinas virtuales de Azure en las que los costos en curso tanto de mantenimiento como de hardware son menores que los de ejecutar un conjunto equivalente en el centro de datos.

El siguiente paso es configurar una aplicación de línea de negocio de desarrollo y pruebas hospedada en Azure.

## Creación de una aplicación de línea de negocio de desarrollo y pruebas hospedada en Azure

Una red virtual entre locales está conectada a una red local con una conexión de ExpressRoute o VPN de sitio a sitio. Si quiere crear un entorno de desarrollo y pruebas que imite la configuración final y experimentar con el acceso a la aplicación y realizar la administración remota a través de una conexión VPN, vea [Configuración de una aplicación de línea de negocio (LOB) basada en web en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-high-availability-LOB-application/CreateLOBAppHybridCloud_3.png)
 
Puede crear este entorno de desarrollo y pruebas de forma gratuita con su [suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una [suscripción de evaluación de Azure](https://azure.microsoft.com/pricing/free-trial/).

El siguiente paso es crear una aplicación de línea de negocio de alta disponibilidad en Azure.

## Implementación de una aplicación de línea de negocio hospedada en Azure

La configuración representativa y básica de una aplicación de línea de negocio de alta disponibilidad en Azure tiene el siguiente aspecto.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Consta de:

- Una aplicación de línea de negocio solo de intranet con dos servidores en los niveles web y de base de datos.
- Una configuración AlwaysOn de SQL Server con dos máquinas virtuales que ejecuten SQL Server y un equipo de nodos de mayoría en un clúster.
- Servicios de dominio de Active Directory en la red virtual con dos controladores de dominio de réplica.

Para obtener información general sobre aplicaciones de línea de negocio, vea [Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664).

Para implementar esta configuración, siga este proceso :

- Fase 1: Configuración de Azure 

	Use Azure PowerShell para crear una red virtual entre locales, conjuntos de disponibilidad y las cuentas de almacenamiento. Para los pasos de configuración detallados, consulte [Fase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

- Fase 2: Configuración de los controladores de dominio.

	Configure dos controladores de dominio de réplica de Active Directory y los valores de DNS para la red virtual. Para los pasos de configuración detallados, consulte [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).

- Fase 3: Configuración de la infraestructura de SQL Server.

	Cree las máquinas virtuales que ejecutan SQL Server y el clúster. Para los pasos de configuración detallados, consulte [Fase 3](virtual-machines-workload-high-availability-LOB-application-phase3.md).

- Fase 4: Configuración de servidores web.

	Cree las máquinas virtuales de servidor web y agregue la aplicación de línea de negocio. Para la información detallada, consulte [Fase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md).

- Fase 5: Configuración de un grupo de disponibilidad AlwaysOn de SQL Server.

	Prepare las bases de datos de la aplicación de línea de negocio, cree un grupo de disponibilidad AlwaysOn de SQL Server y agregue las bases de datos de la aplicación al grupo. Para los pasos de configuración detallados, consulte [Fase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md).

Una vez configurada, puede ampliar fácilmente esta aplicación de línea de negocio agregando más servidores web o máquinas virtuales que ejecuten SQL Server al clúster.

## Paso siguiente

- Obtenga [información general](virtual-machines-workload-high-availability-lob-application-overview.md) de la carga de trabajo de producción antes de comenzar con la configuración.

<!---HONumber=AcomDC_0128_2016-->