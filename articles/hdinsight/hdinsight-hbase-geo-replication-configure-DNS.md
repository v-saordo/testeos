<properties 
   pageTitle="Configuración de DNS entre dos redes virtuales de Azure | Microsoft Azure" 
   description="Aprenda a configurar las conexiones VPN y la resolución de nombres de dominio entre dos redes virtuales, y cómo configurar la replicación geográfica de HBase." 
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

# Configuración de DNS entre dos redes virtuales de Azure

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 


Obtenga información acerca de cómo agregar y configurar los servidores DNS para redes virtuales de Azure para controlar la resolución de nombres dentro de las redes virtuales y entre ellas.

Este tutorial es la segunda parte de la [serie][hdinsight-hbase-geo-replication] sobre la creación de replicación geográfica de HBase.

- [Configuración de la conectividad VPN entre dos redes virtuales][hdinsight-hbase-geo-replication-vnet]
- Configuración de DNS para las redes virtuales (este tutorial)
- [Configuración de la replicación geográfica de HBase][hdinsight-hbase-geo-replication]


El siguiente diagrama muestra las dos redes virtuales que creó en [Configuración de una conectividad VPN entre dos redes virtuales][hdinsight-hbase-geo-replication-vnet]\:

![Diagrama de red virtual de replicación de HBase para HDInsight][img-vnet-diagram]

##Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con Azure PowerShell**. Vea [Instalar y usar Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

		Add-AzureAccount

	Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

		Select-AzureSubscription <AzureSubscriptionName>

- **Dos redes virtuales de Azure con conectividad VPN**. Para obtener instrucciones, consulte [Configuración de una conexión VPN entre dos redes virtuales de Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Los nombres de servicio de Azure y los nombres de máquina virtual deben ser únicos. El nombre usado en este tutorial es Contoso-[Servicio de Azure/nombre de máquina virtual]-[EU/US]. Por ejemplo, Contoso-VNet-EU es la red virtual de Azure del centro de datos Norte de Europa; Contoso-DNS-US es la máquina virtual del servidor DNS del centro de datos Este de EE. UU. Debe proponer sus propios nombres.
 
 
##Creación de máquinas virtuales de Azure que se van a usar como servidores DNS

**Para crear una máquina virtual en Contoso-VNet-EU, denominada Contoso-DNS-EU**

1.	Haga clic en **NUEVO**, **PROCESO**, **MÁQUINA VIRTUAL** y **DE LA GALERÍA**.
2.	Elija **Windows Server 2012 R2 Datacenter**.
3.	Especifique:
	- **NOMBRE DE MÁQUINA VIRTUAL**: Contoso-DNS-EU
	- **NUEVO NOMBRE DE USUARIO:**: 
	- **CONTRASEÑA NUEVA**: 
4.	Especifique:
	- **SERVICIO EN LA NUBE**: creación de un nuevo servicio en la nube
	- **REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL**: (seleccione Contoso-VNet-EU)
	- **SUBREDES DE LA RED VIRTUAL**: Subnet-1
	- **CUENTA DE ALMACENAMIENTO**: use una cuenta de almacenamiento generada automáticamente
	
		El nombre del servicio en la nube será el mismo que el nombre de la máquina virtual. En este caso, es Contoso-DNS-EU. Para las máquinas virtuales posteriores, puedo elegir usar el mismo servicio en la nube. Todas las máquinas virtuales que se encuentran en el mismo servicio en la nube comparten la misma red virtual y sufijo de dominio.

		La cuenta de almacenamiento se usa para almacenar el archivo de imagen de la máquina virtual. 
	- **EXTREMOS**: (desplácese hacia abajo y seleccione **DNS**) 

Después de crear la máquina virtual, descubra la IP interna y externa.

1.	Haga clic en el nombre de la máquina virtual, **Contoso-DNS-EU**.
2.	Haga clic en **Panel**.
3.	Escriba:
	- DIRECCIÓN IP VIRTUAL PÚBLICA
	- DIRECCIÓN IP INTERNA


**Para crear una máquina virtual en Contoso-VNet-US, denominada Contoso-DNS-US**

- Repita el mismo procedimiento para crear una máquina virtual con los siguientes valores:
	- NOMBRE DE MÁQUINA VIRTUAL: Contoso-DNS-US
	- REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL: seleccione Contoso-VNet-EU
	- SUBREDES DE LA RED VIRTUAL: Subnet-1
	- CUENTA DE ALMACENAMIENTO: use una cuenta de almacenamiento generada automáticamente
	- EXTREMOS: (seleccione DNS)

##Configuración de direcciones IP estáticas para las dos máquinas virtuales

Los servidores DNS requieren direcciones IP estáticas. Este paso no puede realizarse desde el Portal de Azure clásico. Utilizará PowerShell de Azure.

**Para configurar la dirección IP estática para las dos máquinas virtuales**

1. Abra Windows PowerShell ISE.
2. Ejecute los siguientes cmdlets.  

		Add-AzureAccount
		Select-AzureSubscription [YourAzureSubscriptionName]
		
		Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
		Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

	ServiceName es el nombre de servicio en la nube. Dado que el servidor DNS es la primera máquina virtual del servicio en la nube, el nombre del servicio en la nube es el mismo que el nombre de la máquina virtual.

	Es posible que tenga que actualizar ServiceName y Nombre para que coincidan con los nombres que tenga.


##Adición de las dos máquinas virtuales al rol de servidor DNS

**Para agregar el rol de servidor DNS para Contoso-DNS-EU**

1.	En el Portal de Azure clásico, haga clic en **Máquinas virtuales** en la parte izquierda. 
2.	Haga clic en **Contoso-DNS-EU**.
3.	Haga clic en **PANEL** en la parte superior.
4.	Haga clic en **CONECTAR** en la parte inferior y siga las instrucciones para conectarse a la máquina virtual a través de RDP.
2.	Dentro de la sesión de RDP, haga clic en el botón de Windows en la esquina inferior izquierda para abrir la pantalla de inicio.
3.	Haga clic en el mosaico **Administrador del servidor**.
4.	Haga clic en **Agregar roles y características**.
5.	Haga clic en **Siguiente**.
6.	Seleccione **Instalación basada en características o en roles** y haga clic en **Siguiente**.
7.	Seleccione la máquina virtual DNS (ya debe estar marcada) y, a continuación, haga clic en **Siguiente**.
8.	Elija **Servidor DNS**.
9.	Haga clic en **Agregar características** y, a continuación, en **Continuar**.
10.	Haga clic en **Siguiente** tres veces y, a continuación, haga clic en **Instalar**. 

**Para agregar el rol de servidor DNS para Contoso-DNS-US**

- Repita los pasos para agregar el rol de DNS para **Contoso-DNS-US**.

##Asignación de servidores DNS a las redes virtuales

**Para registrar los dos servidores DNS**

1.	En el Portal de Azure clásico, haga clic en **NUEVO**, **SERVICIOS DE RED**, **RED VIRTUAL** y **REGISTRAR SERVIDOR DNS**.
2.	Especifique:
	- **Nombre**: Contoso-DNS-EU
	- **DIRECCIÓN IP DEL SERVIDOR DNS**: 10.1.0.4: la dirección IP debe coincidir con la dirección IP de la máquina virtual del servidor DNS.
	 
3.	Repita el proceso para registrar Contoso-DNS-US con la siguiente configuración:
	- **NOMBRE**: Contoso-DNS-US
	- **DIRECCIÓN IP DEL SERVIDOR DNS**: 10.2.0.4

**Para asignar a los dos servidores DNS a las dos redes virtuales**

1.	Haga clic en **Redes** en el panel izquierdo del Portal clásico.
2.	Haga clic en **Contoso-VNet-EU**.
3.	Haga clic en **CONFIGURAR**.
4.	Seleccione **Contoso-DNS-EU** en la sección **Servidores DNS**.
5.	Haga clic en **GUARDAR** en la parte inferior de la página y en **Sí** para confirmar.
6.	Repita el proceso para asignar el servidor DNS **Contoso-DNS-US** para la red virtual de **Contoso-VNet-US**.

Todas las máquinas virtuales que se han implementado en las redes virtuales deben reiniciarse para actualizar la configuración del servidor DNS.

**Para reiniciar las máquinas virtuales**

1. En el Portal de Azure clásico, haga clic en **Máquinas virtuales** en la parte izquierda.
2. Haga clic en **Contoso-DNS-EU**.
3. Haga clic en **Panel** en la parte superior.
4. Haga clic en **REINICIAR** en la parte inferior.
5. Repita los mismos pasos para reiniciar **Contoso-DNS-US**.


##Configuración de reenviadores condicionales de DNS

El servidor DNS en cada red virtual solo puede resolver nombres DNS en la red virtual. Deberá configurar un reenviador condicional para que señale al servidor DNS del mismo nivel para la resolución de nombres en la red virtual del mismo nivel.

Para configurar el reenviador condicional, necesitará saber los sufijos de dominio de los dos servidores DNS. Los sufijos DNS pueden ser diferentes según la configuración de Servicios en la nube que utilizó al crear las máquinas virtuales. Para cada sufijo DNS que se utiliza en la red virtual, debe agregar un reenviador condicional.

**Para buscar los sufijos de dominio de los dos servidores DNS**

1. RDP en **Contoso-DNS-EU**.
2. Abra una consola de Windows PowerShell o un símbolo del sistema.
3. Ejecute **ipconfig** y anote el **sufijo DNS específico de la conexión**.
4. No cierre la sesión de RDP, ya que la necesitará más adelante. 
5. Repita los mismos pasos para averiguar el **sufijo DNS específico de la conexión** de **Contoso-DNS-US**.


**Para configurar reenviadores DNS**
 
1.	Desde la sesión de RDP para **Contoso-DNS-EU**, haga clic en la tecla Windows en la parte inferior izquierda.
2.	Haga clic en **Herramientas administrativas**.
3.	Haga clic en **DNS**.
4.	En el panel izquierdo, expanda **DSN**, **Contoso-DNS-EU**.
5.	Escriba la siguiente información:
	- **Dominio DNS**: escriba el sufijo DNS de Contoso-DNS-US. Por ejemplo: Contoso-DNS-US.b5.internal.cloudapp.net.
	- **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-US.
6.	Presione **ENTRAR** y, a continuación, haga clic en **ACEPTAR**. Ahora podrá resolver la dirección IP de Contoso-DNS-US de Contoso-DNS-EU.
7.	Repita los pasos para agregar un reenviador de DNS al servicio DNS en la máquina virtual de Contoso-DNS-US con los siguientes valores:
	- **Dominio DNS**: escriba el sufijo DNS de Contoso-DNS-EU. 
	- **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-EU.

##Prueba de la resolución de nombres a través de las redes virtuales

Ahora puede realizar la prueba de la resolución de nombres a través de las redes virtuales. El firewall bloquea el ping de forma predeterminada. Puede usar nslookup para resolver las máquinas virtuales del servidor DNS (debe usar FQDN) en las redes del mismo nivel.


##Pasos siguientes

En este tutorial ha aprendido cómo configurar una resolución de nombre en las redes virtuales con conexiones VPN. Los otros dos artículos de la serie tratan sobre lo siguiente:

- [Configuración de una conexión VPN entre dos redes virtuales de Azure][hdinsight-hbase-geo-replication-vnet]
- [Configuración de la replicación geográfica de HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png

<!---HONumber=AcomDC_0218_2016-->