<properties 
	pageTitle="Implementación de una aplicación de línea de negocio | Microsoft Azure" 
	description="Implemente una aplicación de línea de negocio de alta disponibilidad basada en web con grupos de disponibilidad AlwaysOn de SQL Server en Azure en cinco fases." 
	documentationCenter=""
	services="virtual-machines" 
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

# Implementación de una aplicación de línea de negocio de alta disponibilidad en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Este artículo contiene vínculos a las instrucciones detalladas para implementar una aplicación de línea de negocio basada en web solo de intranet con grupos de disponibilidad AlwaysOn de SQL Server en servicios de infraestructura de Azure. La aplicación se hospeda en estos equipos:

- Dos servidores web
- Dos servidores de bases de datos
- Un servidor de nodos de mayoría de clúster
- Dos controladores de dominio

Esta es la configuración, con los nombres de marcador de posición para cada servidor.

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
Dos equipos como mínimo para cada rol garantizan la alta disponibilidad. Todas las máquinas virtuales están en una sola ubicación de Azure (también llamada región). Cada grupo de máquinas virtuales para un rol específico está en su propio conjunto de disponibilidad.

## Lista de materiales

Esta configuración básica requiere el conjunto de componentes y servicios siguiente de Azure:

- Siete máquinas virtuales
- Cuatro discos de datos adicionales para los controladores de dominio y las máquinas virtuales que ejecutan SQL Server
- Tres conjuntos de disponibilidad
- Una red virtual entre locales
- Dos cuentas de almacenamiento

Estas son las máquinas virtuales y sus tamaños predeterminados para esta configuración.

Elemento | Descripción de la máquina virtual | Imagen de la Galería | Tamaño predeterminado 
--- | --- | --- | --- 
1\. | Primer controlador de dominio | Windows Server 2012 R2 Datacenter | D1
2\. | Segundo controlador de dominio | Windows Server 2012 R2 Datacenter | D1
3\. | Servidor de base de datos principal | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
4\. | Servidor de base de datos secundaria | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
5\. | Nodo de mayoría para el clúster | Windows Server 2012 R2 Datacenter | D1
6\. | Primer servidor web | Windows Server 2012 R2 Datacenter | D3
7\. | Segundo servidor web | Windows Server 2012 R2 Datacenter | D3

Para calcular los costos estimados para esta configuración, vea [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

1. En **Módulos**, haga clic en **Calcular** y luego en **Máquinas virtuales** las veces suficientes como para crear una lista de siete máquinas virtuales.
2. Para cada máquina virtual, seleccione:
	- La región deseada
	- **Windows** para el tipo
	- **Estándar** para el plan de tarifa
	- El tamaño predeterminado de la tabla anterior o el tamaño deseado para **Tamaño de instancia**

> [AZURE.NOTE]La calculadora de precios de Azure no incluye los costos adicionales de la licencia de SQL Server para las dos máquinas virtuales que ejecutan SQL Server 2014 Enterprise. Para más información, vea [Precios de máquinas virtuales: SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

## Fases de implementación

Esta configuración se implementa en las siguientes fases:

- [Fase 1: Configuración de Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md). Cree una red virtual entre locales, conjuntos de disponibilidad y cuentas de almacenamiento.
- [Fase 2: Configuración de los controladores de dominio](virtual-machines-workload-high-availability-LOB-application-phase2.md). Cree y configure los controladores de dominio de los Servicios de dominio de Active Directory (AD DS) de réplica.
- [Fase 3: Configuración de la infraestructura de SQL Server](virtual-machines-workload-high-availability-LOB-application-phase3.md). Cree y configure las máquinas virtuales que ejecutan SQL Server, cree el clúster y habilite los grupos de disponibilidad AlwaysOn de SQL Server.
- [Fase 4: Configuración de servidores web](virtual-machines-workload-high-availability-LOB-application-phase4.md). Cree y configure las dos máquinas virtuales de servidor web.
- [Fase 5: Adición de las bases de datos de la aplicación a un grupo de disponibilidad AlwaysOn de SQL Server](virtual-machines-workload-high-availability-LOB-application-phase5.md). Prepare las bases de datos de la aplicación de línea de negocio y agréguelas a un grupo de disponibilidad AlwaysOn de SQL Server.

Esta implementación está diseñada para complementar el [Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664) e incorpora las últimas recomendaciones.

Se trata de una arquitectura preceptiva predefinida. Tenga en cuenta lo siguiente:

- Si es un implementador experimentado de aplicaciones de línea de negocio basadas en web, no dude en adaptar las instrucciones indicadas en las fases de la 3 a la 5 para crear la infraestructura de la aplicación que mejor se adapte a sus necesidades. 
- Si ya cuenta con una implementación en la nube híbrida de Azure, puede adaptar u omitir las instrucciones de las fases 1 y 2 con el fin de hospedar las máquinas virtuales para la nueva aplicación en la subred adecuada.
- Todos los servidores se encuentran en una sola subred en la red virtual de Azure. Si desea proporcionar seguridad adicional equivalente al aislamiento de subred, puede usar los [grupos de seguridad de red](../virtual-networks/virtual-networks-nsg.md).

Para compilar un entorno de desarrollo o pruebas, o una prueba de concepto de esta configuración, vea [Configuración de una aplicación de LOB basada en web en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Para más información sobre el diseño de cargas de trabajo de TI en Azure, en [Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md).

## Paso siguiente

Para iniciar la configuración de esta carga de trabajo, vaya a [Fase 1: Configuración de Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md).

<!---HONumber=AcomDC_1223_2015-->