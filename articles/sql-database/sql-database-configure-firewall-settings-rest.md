<properties
	pageTitle="Configuración del firewall | Microsoft Azure"
	description="Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="02/04/2016"
	ms.author="rickbyh"/>


# Configuración del firewall en la Base de datos SQL mediante la API de REST


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de datos SQL de Microsoft Azure usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall de nivel de servidor y de base de datos para el maestro o una base de datos de usuario en el servidor de Base de datos SQL de Azure para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure se conecten al servidor de base de datos, deben habilitarse las conexiones de Azure. Para obtener más información sobre las reglas de firewall y sobre cómo habilitar las conexiones de Azure, consulte el artículo [Firewall de la Base de datos SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Administración de reglas de firewall de nivel de servidor a través de la API de REST
1. La administración de reglas de firewall a través de la API de REST debe autenticarse. Para obtener información, vea Autenticar solicitudes de administración del servicio.
2. Las reglas de nivel de servidor se pueden crear, actualizar o eliminar mediante la API de REST

	Para crear o actualizar una regla de firewall de nivel de servidor, ejecute el método POST mediante lo siguiente:
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	Cuerpo de la solicitud

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	Para quitar una regla de firewall de nivel de servidor existente, ejecute el método DELETE mediante lo siguiente:
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule


## Administración de reglas de firewall mediante la API de REST de administración del servicio

* [Crear regla de firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Eliminar regla de firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Obtener regla de firewall.](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Enumerar reglas de firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## Pasos siguientes

Para obtener un tutorial sobre la creación de una base de datos, consulte [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md). Para obtener ayuda para la conexión a una base de datos SQL de Azure desde código abierto o aplicaciones de terceros, consulte [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para conseguir más información acerca de cómo acceder a las bases de datos, consulte [Administrar bases de datos e inicios de sesión en la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0211_2016-->