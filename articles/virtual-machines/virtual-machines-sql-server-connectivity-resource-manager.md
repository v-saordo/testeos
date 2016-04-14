<properties 
	pageTitle="Conexión a una máquina virtual de SQL Server en Azure | Microsoft Azure"
	description="En este tema se usan recursos creados con el modelo de implementación clásica y se describe cómo conectarse a SQL Server en una máquina virtual en Azure. Los escenarios varían según la configuración de red y la ubicación del cliente."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"    
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/18/2015"
	ms.author="jroth" />

# Conexión a una máquina virtual de SQL Server en Azure (Administrador de recursos)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-sql-server-connectivity-resource-manager.md)
- [Classic](virtual-machines-sql-server-connectivity.md)

## Información general

Configurar la conectividad con SQL que se ejecuta en una máquina virtual de Azure en el Administrador de recursos no es muy distinto de los pasos necesarios para una instancia local de SQL Server. Tendrá que trabajar con los pasos de configuración relacionados con el firewall, la autenticación y los inicios de sesión de base de datos.

Sin embargo, hay algunos aspectos de la conectividad de SQL Server que son específicos de las máquinas virtuales de Azure. En este artículo se describen algunos [escenarios de conectividad generales](#connection-scenarios) y, después, se proporcionan los [pasos detallados para configurar la conectividad de SQL Server en una máquina virtual de Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

Este artículo se centra en la conectividad. Para obtener un tutorial completo sobre aprovisionamiento y conectividad, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

## Escenarios de conexión

La forma en que un cliente se conecta a SQL Server que se ejecuta en una máquina virtual varía según la ubicación del cliente y la configuración del equipo y la red. Entre los escenarios se incluyen los siguientes:

- [Conexión a SQL Server a través de Internet](#connect-to-sql-server-over-the-internet)
- [Conexión a SQL Server en la misma red virtual](#connect-to-sql-server-in-the-same-virtual-network)

### Conexión a SQL Server a través de Internet

Si desea conectarse a su motor de base de datos de SQL Server desde Internet, debe completar varios pasos, como configurar el firewall, habilitar la Autenticación de SQL y configurar el grupo de seguridad de red; además, debe tener una regla de grupo de seguridad de red para permitir el tráfico TCP en el puerto 1433.

Si utiliza el portal para aprovisionar una imagen de máquina virtual de SQL Server con el administrador de recursos, estos pasos se realizan de forma automática cuando selecciona **Pública** como la opción de conectividad de SQL:

![](./media/virtual-machines-sql-server-connectivity-resource-manager/sql-vm-portal-connectivity.png)

Si esto no ocurrió durante el aprovisionamiento, puede configurar manualmente SQL Server y las máquinas virtuales; para ello, siga los [pasos que aparecen en este artículo para configurar de forma manual la conectividad](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

Una vez hecho esto, cualquier cliente con acceso a Internet podrá conectarse a la instancia de SQL Server si especifica la dirección IP pública de la máquina virtual o la etiqueta DNS asignada a esa dirección IP. Si el puerto de SQL Server es 1433, no es necesario que lo especifique en la cadena de conexión.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Aunque esto permite a los clientes conectarse a través de Internet, esto no implica que cualquier usuario pueda conectarse a SQL Server. Los clientes externos necesitan el nombre de usuario y la contraseña correctos. Para obtener más seguridad, puede evitar utilizar el conocido puerto 1433. Por ejemplo, si configuró SQL Server para escuchar en el puerto 1500 y estableció reglas adecuadas de firewall y de grupo de seguridad de red, podría conectarse si anexa el número de puerto al nombre del servidor, tal como se indica en el ejemplo siguiente:

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Es importante tener en cuenta que, cuando se usa esta técnica para comunicarse con SQL Server, todos los datos devueltos se consideran tráfico saliente desde el centro de datos. Está sujeto a los [precios normales de transferencias de datos salientes](https://azure.microsoft.com/pricing/details/data-transfers/). Esto ocurre aunque use esta técnica desde otra máquina o servicio en la nube en el mismo centro de datos de Azure, ya que el tráfico sigue pasando por el equilibrador de carga pública de Azure.

### Conexión a SQL Server en la misma red virtual

[La red virtual](..\virtual-network\virtual-networks-overview.md) permite otros escenarios. Puede conectar las máquinas virtuales en la misma red virtual, incluso si esas máquinas virtuales existen en distintos grupos de recursos. Asimismo, con una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md), puede crear una arquitectura híbrida que conecta las máquinas virtuales con redes y máquinas locales.

Las redes virtuales también permiten unir las máquinas virtuales de Azure a un dominio. Esta es la única forma de usar la autenticación de Windows para SQL Server. Los demás escenarios de conexión requieren la autenticación de SQL con nombres de usuario y contraseñas.

Si utiliza el portal para aprovisionar una imagen de máquina virtual con el administrador de recursos, las reglas adecuadas de firewall para la comunicación en la red virtual se configuran cuando selecciona **Privada** como la opción de conectividad de SQL. Si esto no ocurrió durante el aprovisionamiento, puede configurar manualmente SQL Server y las máquinas virtuales; para ello, siga los [pasos que aparecen en este artículo para configurar de forma manual la conectividad](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Sin embargo, si planea configurar un entorno de dominio y la autenticación de Windows, no es necesario seguir los pasos de este artículo para configurar los inicios de sesión y la autenticación de SQL. Además, tampoco es necesario configurar reglas de grupo de seguridad de red para el acceso a través de Internet.

Si configuró DNS en la red virtual, puede conectarse a la instancia de SQL Server si especifica el nombre de equipo de la máquina virtual de SQL Server en la cadena de conexión. En el siguiente ejemplo, se presume que también se configuró la autenticación de Windows y que se concedió al usuario acceso a la instancia de SQL Server.

	"Server=mysqlvm;Integrated Security=true" 

Tenga en cuenta que, en este escenario, también puede especificar la dirección IP de la máquina virtual.

## Pasos para configurar manualmente la conectividad de SQL Server en una máquina virtual de Azure

Los pasos siguientes muestran cómo configurar manualmente la conectividad con la instancia de SQL Server y, luego, conectarse de forma opcional a través de Internet mediante SQL Server Management Studio (SSMS). Observe que muchos de estos pasos se realizan de forma automática cuando selecciona las opciones de conectividad de SQL Server adecuadas en el portal.

Antes de que pueda conectarse a la instancia de SQL Server desde otra máquina virtual o Internet, debe completar las siguientes tareas descritas en las secciones que aparecen a continuación:

- [Apertura de puertos TCP en el firewall de Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configuración de SQL Server para escuchar en el protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configuración de SQL Server para autenticación de modo mixto](#configure-sql-server-for-mixed-mode-authentication)
- [Creación de inicios de sesión para la autenticación de SQL Server](#create-sql-server-authentication-logins)
- [Configuración de una etiqueta DNS para la dirección IP pública](#configure-a-dns-label-for-the-public-ip-address)
- [Conexión al motor de base de datos desde otro equipo](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Conexión a SQL Server en una máquina virtual](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Conexión a SQL Server en el Administrador de recursos de una máquina virtual](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Conexión a SQL Server en el Administrador de recursos de una máquina virtual](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## Pasos siguientes

Para ver las instrucciones de aprovisionamiento además de estos pasos de conectividad, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

Es importante revisar todos los procedimientos recomendados de seguridad para SQL Server que se ejecuta en una máquina virtual de Azure. Para obtener más información, consulte [Consideraciones de seguridad para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-security-considerations.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->