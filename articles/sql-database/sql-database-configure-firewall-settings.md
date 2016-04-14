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
	ms.date="12/16/2015"
	ms.author="rickbyh"/>


# Configuración del firewall en la Base de datos SQL mediante el Portal de Azure


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de datos SQL usa reglas de firewall para permitir conexiones con servidores y bases de datos. Puede definir la configuración de firewall de nivel de servidor y de base de datos para el maestro o una base de datos de usuario en el servidor lógico de Base de datos SQL para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT]Para permitir que las aplicaciones de Azure se conecten al servidor de base de datos, deben habilitarse las conexiones de Azure. Para obtener más información sobre las reglas de firewall y sobre cómo habilitar las conexiones de Azure, consulte el artículo [Firewall de la Base de datos SQL de Azure](sql-database-firewall-configure.md). Es posible que tenga que abrir algunos puertos TCP adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


### Administración de reglas de firewall de nivel de servidor a través del Portal de Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Administración de reglas de firewall de nivel de servidor 

1. En el Portal de Azure, haga clic en **Bases de datos SQL**. Aquí se enumeran todas las bases de datos y los servidores correspondientes.
2. Haga clic en **Servers** en la parte superior de la página.
3. Haga clic en la flecha al lado del servidor para el que desea administrar las reglas de firewall.
4. Haga clic en **Configurar** en la parte superior de la página.

	*  Para agregar el equipo actual, haga clic en Agregar a las direcciones IP permitidas.
	*  Para agregar direcciones IP adicionales, escriba el nombre de la regla, la dirección IP inicial y la dirección IP final.
	*  Para modificar una regla existente, haga clic en cualquiera de los campos de la regla y realice la modificación.
	*  Para eliminar una regla existente, mantenga el puntero sobre la regla hasta que aparezca la X al final de la fila. Haga clic en la X para quitar la regla.
5. Para guardar los cambios, haga clic en **Guardar** en la parte inferior de la página.


## Pasos siguientes

Para obtener un tutorial sobre la creación de una base de datos, consulte [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md). Para obtener ayuda acerca de la conexión a una Base de datos SQL de Azure desde aplicaciones de código abierto o de terceros, consulte [Instrucciones para conectar con la Base de datos SQL de Azure mediante programación](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para conseguir más información acerca de cómo acceder a las bases de datos, consulte [Administrar bases de datos e inicios de sesión en la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_1217_2015-->