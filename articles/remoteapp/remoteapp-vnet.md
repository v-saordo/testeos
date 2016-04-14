
<properties
    pageTitle="Validación de la red virtual de Azure para su uso con Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre cómo asegurarse de que la red virtual de Azure está lista para usarse con Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/22/2016"
    ms.author="elizapo" />



# Validación de la red virtual de Azure para su uso con Azure RemoteApp

Antes de usar una red virtual de Azure con Azure RemoteApp, debe validar la red virtual. Esto ayuda a evitar problemas con la conectividad.

Para validar la red virtual de Azure, haga lo siguiente:

1. Cree una máquina virtual de Azure dentro de la subred de la red virtual de Azure que quiere usar con Azure RemoteApp.

2. Conéctese a esa máquina virtual mediante la opción **Conectar** del portal de administración.
3. Conecte la máquina virtual al mismo dominio que desea usar con Azure RemoteApp. Si está creando una colección híbrida que se conecta a la red local, una la máquina virtual al dominio local.

Si esto se realiza correctamente, la red virtual de Azure está lista para usarse con RemoteApp.

Para obtener más información sobre el flujo de trabajo completo de la colección híbrida, vea los siguientes artículos:

- [Planeación de la red virtual de Azure RemoteApp](remoteapp-planvnet.md)
- [Creación de una colección híbrida](remoteapp-create-hybrid-deployment.md)
- [Implementación de la colección Azure RemoteApp en la Red virtual de Azure (con compatibilidad para ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)

<!---HONumber=AcomDC_0128_2016-->