<properties 
    pageTitle="Implementación de QuickBooks en Azure RemoteApp | Microsoft Azure" 
    description="Obtenga información acerca de cómo compartir QuickBooks con Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/07/2016" 
    ms.author="elizapo" />



# ¿Cómo se implementa QuickBooks en Azure RemoteApp?

Use la siguiente información para compartir QuickBooks como aplicación en Azure RemoteApp.


Puede compartir QuickBooks 2015 Enterprise con Azure RemoteApp en una colección híbrida o en la nube. El archivo de empresa debe residir en una máquina virtual que está ejecutando el servidor de base de datos de QuickBooks que es independiente de los servidores de Azure RemoteApp. No almacene nunca el archivo en la imagen de Azure RemoteApp: se espera una pérdida de datos si lo hace. Solo QuickBooks Enterprise admite el hospedaje del archivo de QuickBooks en un recurso compartido externo con el servidor de base de datos de QuickBooks accesible a través de redes Windows estándar.

> [AZURE.IMPORTANT]El servidor de base de datos de QuickBooks que hospeda el archivo de empresa debe residir en una máquina virtual independiente dentro de la misma red virtual que la colección de Azure RemoteApp .

## Pasos para implementar QuickBooks

1. Cree una máquina virtual de Azure e instale QuickBooks, el servidor de base de datos de QuickBooks y coloque el archivo de empresa en una máquina virtual de Azure. Asegúrese de configurar correctamente las reglas de firewall.
2. Instale QuickBooks en una [imagen personalizada](remoteapp-imageoptions.md) y cree una [colección de Azure RemoteApp](remoteapp-collections.md), en la nube o híbrida, dentro de la misma red virtual donde reside la máquina virtual que hospeda el servidor de base de datos de QuickBooks con archivos de empresa. 
3.	[Publique](remoteapp-publish.md) la aplicación QuickBooks para los usuarios
4.	Inicie el cliente QuickBooks hospedado en Azure RemoteApp, vaya mediante redes estándar Windows a la máquina virtual que hospeda el servidor de base de datos de QuickBooks y abra el archivo de empresa. 

## Referencias de documentación

- [Configuraciones admitidas](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top) de QuickBooks
- [Opciones de implementación](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/) de QuickBooks

También puede consultar la presentación de Ignite [Fundamentals of Microsoft Azure RemoteApp Management and Administration](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) (Fundamentos de administración y gestión de Microsoft Azure RemoteApp), con un avance rápido hasta 1:02:45 para llegar a la parte de QuickBooks.

## Arquitectura de implementación

![Implementación de QuickBooks y RemoteApp de Azure](./media/remoteapp-quickbooks/ra-quickbooks.png)

<!---HONumber=AcomDC_0114_2016-->