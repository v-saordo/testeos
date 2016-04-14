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


# Configuración del firewall en la Base de datos SQL mediante TSQL


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de datos SQL de Microsoft Azure usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall de nivel de servidor y de base de datos para el maestro o una base de datos de usuario en el servidor de Base de datos SQL de Azure para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure se conecten al servidor de base de datos, deben habilitarse las conexiones de Azure. Para obtener más información sobre las reglas de firewall y sobre cómo habilitar las conexiones de Azure, consulte el artículo [Firewall de la Base de datos SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Administración de reglas de firewall de nivel de servidor a través de Transact-SQL

1. Inicie una ventana de consulta a través del Portal clásico o mediante SQL Server Management Studio.
2. Compruebe que está conectado a la base de datos maestra.
3. Las reglas de firewall de nivel de servidor se pueden seleccionar, crear, actualizar o eliminar desde dentro de la ventana de consulta.
4. Para crear o actualizar las reglas de firewall de nivel de servidor, ejecute el procedimiento almacenado de regla sp\_set\_firewall. El ejemplo siguiente habilita un intervalo de direcciones IP en el servidor Contoso.<br/>Comience comprobando qué reglas existen ya.

		SELECT * FROM sys.firewall_rules ORDER BY name;

	A continuación, agregue una regla de firewall.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Para eliminar una regla de firewall de nivel de servidor, ejecute el procedimiento almacenado sp\_delete\_firewall\_rule. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 
## Reglas de firewall de nivel de base de datos

1. Después de crear un firewall de nivel de servidor para la dirección IP, inicie una ventana de consulta a través del Portal clásico o mediante SQL Server Management Studio.
2. Conéctese a la base de datos para la que desea crear una regla de firewall de nivel de base de datos.

	Para crear una nueva regla de firewall de nivel de base de datos o actualizar una existente, ejecute el procedimiento almacenado sp\_set\_database\_firewall\_rule. En el ejemplo siguiente se crea una nueva regla de firewall llamada ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Para eliminar una nueva regla de firewall de nivel de base de datos existente, ejecute el procedimiento almacenado sp\_delete\_database\_firewall\_rule. En el ejemplo siguiente se elimina la regla llamada ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## Pasos siguientes

Para obtener un tutorial sobre la creación de una base de datos, consulte [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md). Para obtener ayuda para la conexión a una base de datos SQL de Azure desde código abierto o aplicaciones de terceros, consulte [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para conseguir más información acerca de cómo acceder a las bases de datos, consulte [Administrar bases de datos e inicios de sesión en la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!---HONumber=AcomDC_0211_2016-->