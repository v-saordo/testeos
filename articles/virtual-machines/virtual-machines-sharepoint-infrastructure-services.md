<properties
	pageTitle="Granjas de SharePoint Server 2013 en Azure | Microsoft Azure"
	description="Busque artículos que describan cómo configurar una granja de servidores de entorno de desarrollo/prueba o de producción de SharePoint 2013 en Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="01/21/2016"
	ms.author="josephd"/>

# Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Configure la primera o siguiente granja de servidores de desarrollo, prueba o producción de SharePoint Server 2013 en los servicios de infraestructura de Microsoft Azure, donde puede aprovecharse de la facilidad de configuración y de la capacidad de ampliar rápidamente la granja para incluir la nueva capacidad o la optimización de la funcionalidad clave.

> [AZURE.NOTE] Microsoft ha publicado la versión de vista previa de TI de SharePoint Server 2016. Para facilitar la instalación y prueba de esta vista previa, puede utilizar una imagen de la galería de máquinas virtuales de Azure con la vista previa de TI de SharePoint Server 2016 y sus requisitos previos preinstalados. Para obtener más información, vea [Prueba de la vista previa de TI de SharePoint Server 2016 en Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Granja de servidores de desarrollo/prueba básica de SharePoint

Este entorno creado automáticamente consta de tres servidores en una red virtual de Azure solo en la nube: un controlador de dominio, un servidor SQL y el servidor SharePoint.

Consulte el elemento [Granja de SharePoint 2013 sin alta disponibilidad](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) en Azure Marketplace del Portal de Azure. Esto crea una granja de servidores de desarrollo y prueba básica para un sitio web de SharePoint a través de Internet. Para obtener detalles adicionales, consulte [Creación de granjas de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md).

También puede usar una plantilla del Administrador de recursos de Azure. Vea [SharePoint](virtual-machines-app-frameworks.md).

> [AZURE.NOTE] Se quitó el elemento **Granja de servidores SharePoint** en Azure Marketplace del Portal de Azure.

## Granja de desarrollo/prueba de SharePoint de alta disponibilidad

Este entorno creado automáticamente que consta de nueve servidores en una red virtual de Azure solo en la nube: dos para los controladores de dominio, tres para un clúster de SQL Server, dos servidores de SharePoint de capa de aplicación y dos servidores de SharePoint de capa de web.

Consulte el elemento [Granja de SharePoint 2013 con alta disponibilidad](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) en Azure Marketplace del Portal de Azure. Con esto se crea una granja de servidores de desarrollo y prueba de alta disponibilidad para un sitio web de SharePoint orientado a Internet. Para obtener detalles adicionales, consulte [Creación de granjas de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md).

También puede usar una plantilla del Administrador de recursos de Azure. Consulte [Implementación de una granja de nueve servidores de SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

> [AZURE.NOTE] Se quitó el elemento **Granja de servidores SharePoint** en Azure Marketplace del Portal de Azure.

## Granja de servidores de desarrollo/prueba de nube híbrida

Con la [granja de servidores de intranet de SharePoint en entorno de prueba o desarrollo de nube híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), se crea una configuración de nube híbrida simulada que hospeda una granja de servidores de SharePoint simple, de dos capas, que puede utilizar para probar una granja de servidores de SharePoint de intranet hospedada en Azure desde su ubicación en Internet.

Esta configuración usa el modelo de implementación clásica.

## Granja de servidores de producción de SharePoint de intranet de alta disponibilidad

Con la implementación de [SharePoint 2013 con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md), cree una granja de servidores de SharePoint Server 2013 de intranet lista para producción y de alta disponibilidad en Azure.

Esta configuración usa el modelo de implementación clásica.

## Paso siguiente

- Descubra configuraciones adicionales de [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) en los servicios de infraestructura de Azure.

<!---HONumber=AcomDC_0204_2016-->