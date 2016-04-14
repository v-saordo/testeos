<properties 
   pageTitle="Configuración de una conexión VPN entre dos redes virtuales | Microsoft Azure" 
   description="Aprenda a configurar las conexiones VPN y la resolución de nombres de dominio entre dos redes virtuales de Azure, y cómo configurar la replicación geográfica de HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Configuración de una conexión VPN entre dos redes virtuales de Azure  

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 

La conectividad de sitio a sitio de redes virtuales de Azure usa una puerta de enlace VPN para proporcionar un túnel seguro mediante Ipsec/IKE. Las redes virtuales pueden estar en diferentes suscripciones y regiones diferentes. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Hay varias razones para la conectividad de red virtual a red virtual:

- Presencia geográfica y redundancia geográfica entre regiones 
- Aplicaciones regionales de niveles múltiples con límite de aislamiento sólido 
- Comunicación entre suscripciones y entre organizaciones en Azure

Para obtener más información, vea [Configuración de una conexión de red virtual a red virtual](../virtual-network/virtual-networks-configure-vnet-to-vnet-connection.md).

Para verla en vídeo:

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Este tutorial es una parte de la [serie][hdinsight-hbase-replication] sobre la creación de replicación geográfica de HBase.

- Configuración de la conectividad VPN entre dos redes virtuales (este tutorial)
- [Configuración de DNS para las redes virtuales][hdinsight-hbase-geo-replication-dns]
- [Configuración de la replicación geográfica de HBase][hdinsight-hbase-geo-replication]

El diagrama siguiente muestra las dos redes virtuales que creará en este tutorial:

![Diagrama de red virtual de replicación de HBase para HDInsight][img-vnet-diagram]
 

##Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con Azure PowerShell**. Vea [Instalar y usar Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

		Add-AzureAccount

	Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

		Select-AzureSubscription <AzureSubscriptionName>


>[AZURE.NOTE] Los nombres de servicio de Azure y los nombres de máquina virtual deben ser únicos. El nombre usado en este tutorial es Contoso-[Servicio de Azure/nombre de máquina virtual]-[EU/US]. Por ejemplo, Contoso-VNet-EU es la red virtual de Azure del centro de datos Norte de Europa; Contoso-DNS-US es la máquina virtual del servidor DNS del centro de datos Este de EE. UU. Debe proponer sus propios nombres.
 

##Creación de dos redes virtuales de Azure



**Para crear una red virtual llamada Contoso-VNet-EU en el Norte de Europa**

1.	Inicie sesión en el [Portal de Azure clásico][azure-portal].
2.	Haga clic en **NUEVO**, **SERVICIOS DE RED**, **RED VIRTUAL**, **CREACIÓN PERSONALIZADA**.
3.	Especifique:

	- **NOMBRE**: Contoso-VNet-EU
	- **UBICACIÓN**: Norte de Europa

		Este tutorial usa centros de datos del Norte de Europa y del Este de EE. UU. Puede elegir sus propios centros de datos.
4.	Especifique:

	- **SERVIDOR DNS**: (déjelo en blanco) 
	
		Necesitará su propio servidor DNS para la resolución de nombres dentro de las redes virtuales. Para obtener más información sobre cuándo usar la resolución de nombres proporcionada por Azure y cuándo usar su propio servidor DNS, vea [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Para obtener instrucciones para configurar la resolución de nombres entre redes virtuales, vea [Configurar DNS entre dos redes virtuales de Azure][hdinsight-hbase-dns].
  
	- **Configurar una VPN de punto a sitio**: (déjelo desactivado)

		Punto a sitio no se aplica a este escenario.

 	- **Configurar una VPN de sitio a sitio**: (déjelo desactivado)
 	
		Configurará la conexión VPN de sitio a sitio para la red virtual de Azure en el centro de datos del Este de Estados Unidos.
5.	Especifique:

	- 	**IP INICIAL DEL ESPACIO DE DIRECCIONES**: 10.1.0.0
	- 	**CIDR DE ESPACIO DE DIRECCIONES**: /16
	- 	**IP INICIAL de subred-1**: 10.1.0.0
	- 	**CIDR DE SUBRED-1**: /24

	El espacio de direcciones no puede solaparse con la red virtual de Estados Unidos.

**Para crear una red virtual llamada Contoso-VNet-EU en el Este de Europa**

- Repita el último procedimiento con los siguientes valores:

	- **NOMBRE**: Contoso-VNet-US
	- **UBICACIÓN**: Este de EE. UU.
	 
	- **SERVIDOR DNS**: (déjelo en blanco)
	- **Configurar una VPN de punto a sitio**: (déjelo desactivado)
	- **Configurar una VPN de sitio a sitio**: (déjelo desactivado)
	 
	- **IP INICIAL DEL ESPACIO DE DIRECCIONES**: 10.2.0.0
	- **CIDR DE ESPACIO DE DIRECCIONES**: /16
	- **IP INICIAL de subred-1**: 10.2.0.0
	- **CIDR DE SUBRED-1**: /24

















##Configuración de una conexión VPN entre las redes dos redes virtuales

###Creación de redes locales

Cuando crea una configuración de red virtual a red virtual, debe configurar cada red virtual para que se identifiquen entre sí como un sitio de red local. En esta sección, configurará cada red virtual como una red local. Las redes locales comparten los mismos espacios de direcciones IP con la red virtual correspondiente.

![Configuración de sitio a sitio de VPN de Azure: redes locales de Azure][img-vnet-lnet-diagram]


**Para crear una red local llamada Contoso-LNet-EU que coincida con el espacio de direcciones de la red Contoso-VNet-EU**

1. En el Portal de Azure clásico, haga clic en **NUEVO**, **SERVICIOS DE RED**, **RED VIRTUAL** y **AGREGAR A RED LOCAL**.
3. Especifique:

	- **NOMBRE**: Contoso-LNet-EU
	- **DIRECCIÓN IP DEL DISPOSITIVO VPN**: 192.168.0.1 (esta dirección se actualizará más tarde)

		Normalmente, se usaría la dirección IP externa real para un dispositivo VPN. Para las configuraciones de red virtual a red virtual, usará la dirección IP de la puerta de enlace de VPN. Dado que no ha creado las puertas de enlace VPN para las dos redes virtuales todavía, escriba una dirección IP arbitraria y regrese para solucionarlo.
4.	Especifique:

	- **IP INICIAL DEL ESPACIO DE DIRECCIONES**: 10.1.0.0
	- **CIDR DE ESPACIO DE DIRECCIONES**: /16
	
	Se debe corresponder exactamente con el intervalo que especificó anteriormente para Contoso-VNet-EU.

**Para crear una red local llamada Contoso-LNet-EU que coincida con el espacio de direcciones de la red Contoso-VNet-US**

- Repita el último procedimiento con los siguientes parámetros:

	- **NOMBRE**: Contoso-LNet-US
	- **DIRECCIÓN IP DEL DISPOSITIVO VPN**: 192.168.0.1 (esta dirección se actualizará más tarde)
	 
	- **IP INICIAL DEL ESPACIO DE DIRECCIONES**: 10.2.0.0
	- **CIDR DE ESPACIO DE DIRECCIONES**: /16


###Creación de puertas de enlace VPN

Hay dos partes en esta configuración. Primero debe configurar una conexión de sitio a sitio de red virtual a una red local y, a continuación, crear una VPN de enrutamiento dinámico. VNet a VNet necesita puertas de enlace VPN de Azure con VPN de enrutamiento dinámico. No se admiten VPN de enrutamiento estático de Azure.

**Para configurar la conexión de sitio a sitio de Contoso-VNet-EU a Contoso-LNet-US**

1.	En el Portal de Azure clásico, haga clic en **REDES** en el panel izquierdo,
2.	Haga clic en **Contoso-VNet-EU**.
3.	Haga clic en la pestaña **CONFIGURAR**.
4.	Active **Conectarse a la red local**.
5.	En **RED LOCAL**, seleccione **Contoso-LNet-US**.
6.	Haga clic en **Agregar subred de puerta de enlace** en la sección de espacios de direcciones de red virtual.
7.	Haga clic en **GUARDAR**.
8.	Haga clic en **ACEPTAR** para continuar.


**Para crear una puerta de enlace VPN para Contoso-VNet-EU**

1.	En el Portal de Azure clásico, haga clic en la pestaña **PANEL**.
4.	Haga clic en **CREAR PUERTA DE ENLACE** en la parte inferior de la página y, a continuación, haga clic en **Enrutamiento dinámico**.
5.	Haga clic en **Sí** para continuar. Observe que el gráfico de la puerta de enlace de la página cambia a amarillo y muestra el mensaje Creando puerta de enlace. La creación de la puerta de enlace suele tardar unos 15 minutos.

	Cuando el estado de la puerta de enlace cambia a Conectando, la dirección IP para cada puerta de enlace será visible en el panel. Anote la dirección IP que corresponde a cada red virtual, teniendo cuidado para no mezclarlas. Estas son las direcciones IP que se utilizarán al editar las direcciones IP de marcador de posición para el dispositivo VPN en redes locales.

6.	Cree una copia de **DIRECCIÓN IP DE LA PUERTA DE ENLACE**. La utilizará para configurar la dirección IP de la puerta de enlace VPN para Contoso-VNet-EU en la sección siguiente.

**Para crear una puerta de enlace VPN para Contoso-VNet-EU**

- Repita los dos últimos procedimientos para configurar la conectividad de sitio a sitio de Contoso-VNet-US a Contoso-LNet-EU y crear una puerta de enlace VPN para Contoso-Vnet-US. Cuando haya terminado, tendrá la dirección IP de puerta de enlace VPN para Contoso-VNet-US.


### Establecimiento de direcciones IP del dispositivo VPN para redes locales
En la última sección, creará una puerta de enlace VPN para cada una de las redes virtuales. Tiene las direcciones IP de las puertas de enlace VPN. Ahora puede volver a configurar las direcciones IP del dispositivo VPN de red local.

**Para configurar la dirección IP del dispositivo VPN para Contoso-LNet-EU**

1.	En el Portal de Azure clásico, haga clic en **REDES** en el panel izquierdo.
2.	Haga clic en **REDES LOCALES** en la parte superior.
3.	Haga clic en **Contoso-LNet-EU** y, a continuación, haga clic en **EDITAR** en la parte inferior.
4.	Actualice la **DIRECCIÓN IP DEL DISPOSITIVO VPN**. Se trata de la dirección que se obtiene de la pestaña PANEL de Contoso-VNET-EU.
5.	Haga clic en el botón derecho.
6.	A continuación, haga clic en el botón Comprobar.

**Para configurar la dirección IP del dispositivo VPN para Contoso-LNet-US**

- Repita el último procedimiento para configurar la dirección IP del dispositivo VPN para Contoso-LNet-US.

###Establecimiento de claves de puerta de enlace de red virtual

Las puertas de enlace de red virtual, usan una clave compartida para autenticar conexiones entre las redes virtuales. La clave no se puede configurar desde el Portal de Azure clásico. Debe usar PowerShell o .NET SDK.

**Para establecer las claves**

1. Desde la estación de trabajo, abra **Windows PowerShell ISE** o la consola de Windows PowerShell.
2. Actualice los parámetros de este script de seguimiento y ejecútelo:

		Add-AuzreAccount
		Select-AzureSubscription -[AzureSubscriptionName]
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##Comprobación de la conexión VPN 

Sin ninguna máquina virtual implementada en las redes virtuales, puede usar el diagrama visual de red virtual que se encuentra en la página del panel de red virtual en el Portal de Azure clásico, para comprobar el estado de la conexión:

![Estado de la conexión de VPN de red virtual de replicación de HBase para HDInsight][img-vpn-status]
  



##Pasos siguientes

En este tutorial ha aprendido cómo configurar una conexión VPN entre dos redes virtuales de Azure. Los otros dos artículos de la serie tratan lo siguiente:

- [Configuración de DNS entre dos redes virtuales de Azure][hdinsight-hbase-geo-replication-dns]
- [Configuración de la replicación geográfica de HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell



[hdinsight-hbase-replication]: ../hdinsight-hbase-geo-replication/
[hdinsight-hbase-dns]: ../hdinsight-hbase-geo-replication-configure-DNS/


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.LNet.diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.status.png

<!---HONumber=AcomDC_0128_2016-->