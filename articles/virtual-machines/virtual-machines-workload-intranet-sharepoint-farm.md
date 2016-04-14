<properties
	pageTitle="Granja de SharePoint Server 2013 en Azure | Microsoft Azure"
	description="Obtenga información sobre el valor de una granja de SharePoint Server 2013 en Azure, configure un entorno de prueba e implemente una configuración de alta disponibilidad."
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

# Carga de trabajo de servicios de infraestructura de Azure: granja de SharePoint de intranet

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Configure la primera o siguiente granja de SharePoint en Microsoft Azure y aprovéchese de la facilidad de configuración y de la capacidad ampliar rápidamente la granja de servidores para incluir la nueva capacidad o la optimización de funcionalidad clave. Muchas de las granjas de SharePoint aumentan a partir de una configuración estándar, de alta disponibilidad y de tres niveles para una granja con posiblemente una docena o más servidores optimizados para rendimiento o roles independientes, como almacenamiento en caché distribuido o búsqueda.

Con las máquinas virtuales y las características de la Red virtual de los Servicios de infraestructura de Azure, podrá implementar y ejecutar rápidamente una granja de SharePoint que se conecte de manera transparente a la red local. Por ejemplo, puede configurar lo siguiente:

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Dado que Red Virtual de Azure es una extensión de la red local con toda la nomenclatura correcta y el enrutamiento del tráfico en su lugar, los usuarios tendrán acceso a ella de la misma manera que si se encontrara en un centro de datos local.

Esta configuración permite ampliar fácilmente la granja de SharePoint mediante la adición de nuevas máquinas virtuales de Azure en las que los costos en curso tanto de mantenimiento como de hardware son menores que el ejecutar un conjunto equivalente del centro de datos.

El hospedaje de una granja de SharePoint de intranet en Servicios de infraestructura de Azure es un ejemplo de una aplicación de línea de negocio. Para obtener información general, consulte [Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664).

El siguiente paso es configurar una granja de SharePoint de intranet de desarrollo y pruebas hospedada en Azure.

> [AZURE.NOTE] Microsoft ha publicado la versión de vista previa de TI de SharePoint Server 2016. Para facilitar la instalación y prueba de esta vista previa, puede utilizar una imagen de la galería de máquinas virtuales de Azure con la vista previa de TI de SharePoint Server 2016 y sus requisitos previos preinstalados. Para obtener más información, consulte [Prueba de la vista previa de TI de SharePoint 2016 en Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Creación de una granja de SharePoint de intranet de desarrollo y pruebas hospedada en Azure

Tiene dos opciones para crear un entorno de desarrollo y pruebas para una granja de SharePoint hospedada en Azure:

- Red virtual solo en la nube
- Red virtual entre locales

Puede crear estos entornos de desarrollo y pruebas de forma gratuita con su [suscripción a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una [suscripción de evaluación de Azure](https://azure.microsoft.com/pricing/free-trial/).

### Red virtual solo en la nube

La red virtual solo en la nube no está conectada a una red local. Si desea crear rápidamente una granja de SharePoint básica o de alta disponibilidad, consulte [Creación de granjas de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md). En el ejemplo siguiente se muestra la configuración básica de la granja de servidores de SharePoint.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/Non-HAFarm.png)

### Red virtual entre locales

Una red virtual entre locales está conectada a una red local con una conexión de ExpressRoute o VPN de sitio a sitio. Si desea crear un entorno de desarrollo y pruebas que imita la configuración final y experimentar con el acceso al servidor de SharePoint y realizar la administración remota a través de una conexión VPN, consulte [Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

El siguiente paso es crear una granja de SharePoint de intranet de alta disponibilidad en Azure.

## Implementación de una granja de servidores de SharePoint de intranet hospedada en Azure

La configuración representativa y básica de una granja de SharePoint de intranet, funcional y de alta disponibilidad es la siguiente:

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Consta de:

- Una granja de SharePoint de intranet con dos servidores en los niveles web, de aplicación y de base de datos.
- Configuración de grupos de disponibilidad AlwaysOn de SQL Server con dos servidores SQL Server y un equipo de nodos de mayoría en un clúster.
- Dos controladores de dominio de réplica de un dominio de Active Directory local.

Para ver esta configuración como infografía, consulte [SharePoint con SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

Para implementar esta configuración, siga este proceso :

- Fase 1: Configuración de Azure.

	Use Azure PowerShell para crear una cuenta de almacenamiento, conjuntos de disponibilidad y una red virtual entre locales. Para los pasos de configuración detallados, consulte [Fase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Fase 2: Configuración de controladores de dominio.

	Configure dos controladores de dominio de réplica de Active Directory y los valores de DNS para la red virtual. Para los pasos de configuración detallados, consulte [Fase 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Fase 3: Configuración de la infraestructura de SQL Server.

	Prepare las máquinas virtuales de SQL Server para su uso con SharePoint y cree el clúster de SQL Server. Para los pasos de configuración detallados, consulte [Fase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Fase 4: Configuración de los servidores de SharePoint.

	Configure las cuatro máquinas virtuales de SharePoint para una nueva granja de Sharepoint. Para la información detallada, consulte [Fase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Fase 5: Creación de un grupo de disponibilidad AlwaysOn.

	Prepare las bases de datos de SharePoint, cree un grupo de disponibilidad AlwaysOn y, a continuación, agregue las bases de datos de SharePoint a las mismas. Para los pasos de configuración detallados, consulte [Fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Una vez configurada, puede expandir esta granja de SharePoint con ayuda de [Arquitecturas de Microsoft Azure para SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Paso siguiente

- Obtenga [información general](virtual-machines-workload-intranet-sharepoint-overview.md) de la carga de trabajo de producción antes de comenzar con la configuración.

<!---HONumber=AcomDC_0128_2016-->