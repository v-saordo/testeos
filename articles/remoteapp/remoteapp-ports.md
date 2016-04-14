
<properties
    pageTitle="Lista de puertos y direcciones URL a la lista blanca de Azure RemoteApp implementada en la red virtual de cliente | Microsoft Azure"
    description="Obtenga información sobre qué puertos y direcciones URL tendrá que configurar para la comunicación a través de Azure RemoteApp."
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="elizapo" />



# Lista de puertos y direcciones URL para permitir el acceso a Azure RemoteApp implementada en el cliente de red virtual 

La siguiente información se aplica a Azure RemoteApp, una colección de nube o híbrida, si va a realizar la implementación en una red virtual (VNET). Para más información sobre las redes virtuales, consulte [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md). Si creó un grupo de seguridad de red (NSG) que restringe el tráfico dirigido a los recursos de red virtual que haya elegido para Azure RemoteApp, asegúrese de que los siguientes elementos sean accesibles y permitidos a través de las directivas de seguridad de la red virtual. Para más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  La subred de Azure RemoteApp necesita acceso a estos puntos de conexión y direcciones URL: 
*	**.servicebus.windows.net
*	 **.servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 Salientes - TCP: 443, TCP: 10101-10175 
*	 Opcional. UDP: 10201-10275  
 
## Los clientes Azure RemoteApp necesitan acceso a estos puntos de conexión y direcciones URL: 

Por clientes, me refiero a los escritorios, dispositivos etc. que los usuarios usan para conectarse a las aplicaciones implementadas en la colección de Azure RemoteApp.

-  https://telemetry.remoteapp.windowsazure.com  
-  https://*.remoteapp.windowsazure.com (los puertos UDP opcionales son para esta dirección) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://*.core.windows.net  
-  Saliente: TCP: 443  
-  Opcional: UDP: 3391 

<!---HONumber=AcomDC_0211_2016-->