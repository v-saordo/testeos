<properties
   pageTitle="Configuración de un clúster de Service Fabric con una plantilla del Administrador de recursos de Azure | Microsoft Azure"
   description="Configure un clúster de Service Fabric con una plantilla del Administrador de recursos de Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Configuración de un clúster de Service Fabric con una plantilla del Administrador de recursos de Azure

Esta página le ayuda a configurar un clúster de Service Fabric con una plantilla del Administrador de recursos de Azure. Para ello, la suscripción debe contar con núcleos suficientes para implementar las máquinas virtuales de IaaS que compondrán este clúster.

## Requisitos previos

- Para configurar un clúster seguro, primero debe cargar un certificado X.509 en el almacén de claves. Necesitará la dirección URL del almacén de origen, la dirección URL del certificado y la huella digital del certificado.
- Vea [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md) para más detalles sobre cómo configurar un clúster seguro.

## Adquisición de una plantilla de ejemplo del Administrador de recursos

Las plantillas de ejemplo del Administrador de recursos están disponibles en la [Galería de plantillas de inicio de rápido de Azure en GitHub](https://github.com/Azure/azure-quickstart-templates). Todos los nombres de plantilla de Service Fabric comienzan con "service-fabric..". Puede buscar “fabric” en el repositorio o desplazarse hacia abajo hasta el conjunto de plantillas de ejemplo. Para ayudar a encontrar rápidamente lo que busca, las plantillas se han nombrado de la siguiente manera:

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) para indicar una plantilla de clúster no seguro de nodo único de cinco nodos.

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) para indicar una plantilla de clúster seguro de nodo único de cinco nodos habilitada para WAD.

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) para indicar una plantilla de clúster seguro de dos nodos de diez nodos habilitada para WAD.

## Creación de una plantilla personalizada del Administrador de recursos

Hay dos maneras de crear una plantilla personalizada del Administrador de recursos:

1. Puede adquirir una plantilla de ejemplo de la [Galería de plantillas de inicio rápido de Azure en GitHub](https://github.com/Azure/azure-quickstart-templates) y realizar cambios en ella.

2. Puede iniciar sesión en el portal de Azure y usar las páginas del portal de Service Fabric para generar la plantilla para personalizarla. Para ello, sigue estos pasos:

    a. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

    b. Siga el proceso de creación del clúster, tal como se describe en [Creación del clúster de Azure Service Fabric a través del portal](service-fabric-cluster-creation-via-portal.md), pero no haga clic en **Crear**. En su lugar, vaya a **Resumen** y descargue la plantilla, como se muestra en la siguiente captura de pantalla.

 ![Captura de pantalla de la página del clúster de Service Fabric en la que se muestra el vínculo para descargar una plantilla del Administrador de recursos][DownloadTemplate]

## Implementación de la plantilla del Administrador de recursos en Azure con Azure PowerShell

Vea [Deploying Resource Manager templates by using PowerShell](../resource-group-template-deploy.md) (Implementación de plantillas del Administrador de recursos mediante PowerShell) para obtener instrucciones detalladas sobre cómo implementar la plantilla con PowerShell.

>[AZURE.NOTE] Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos en todo momento con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, normalmente no es seguro apagar todas las máquinas del clúster a menos que antes haya realizado una [copia de seguridad completa del estado](service-fabric-reliable-services-backup-restore.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
- [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introducción al modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0218_2016-->