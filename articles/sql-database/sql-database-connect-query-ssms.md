<properties
	pageTitle="Conexión a Base de datos SQL - SQL Server Management Studio | Microsoft Azure"
	description="Aprenda a conectarse a Base de datos SQL en Azure con SQL Server Management Studio (SSMS). Después, ejecute una consulta de ejemplo con Transact-SQL (T-SQL)."
	metaCanonical=""
	keywords="conexión a base de datos sql,sql server management studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/21/2016"
	ms.author="sstein" />

# Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

En este artículo se muestra cómo conectarse a una Base de datos SQL de Azure con SQL Server Management Studio (SSMS) y realizar una consulta simple con instrucciones Transact-SQL (T-SQL).

En primer lugar necesitará una base de datos SQL en Azure. Puede crear una rápidamente con las instrucciones de [Introducción a Base de datos SQL de Microsoft Azure](sql-database-get-started.md). Los ejemplos siguientes se basan en la base de datos de ejemplo AdventureWorks que crea en ese artículo, pero los mismos pasos, hasta que realiza la consulta, se aplican a cualquier base de datos SQL.

## Instalar e iniciar SQL Server Management Studio (SSMS)

Al trabajar con Base de datos SQL, debe usar la versión más reciente de SSMS. Consulte [Descargar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) para obtenerla. Con la versión más reciente, SSMS le avisa automáticamente cuando la actualización más reciente está disponible.

## Iniciar SSMS y conectarse a su servidor Base de datos SQL

1. Escriba "Microsoft SQL Server Management Studio" en el cuadro de búsqueda de Windows y, a continuación, haga clic en la aplicación de escritorio para iniciar SSMS.
2. En el cuadro de diálogo **Conectar al servidor**, en el cuadro **Nombre de servidor**, escriba el nombre del servidor que hospeda su base de datos SQL en el formato *&lt;servername>*.**database.windows.net**.
3. Elija **Autenticación de SQL Server**, en la lista **Autenticación**.
4. Escriba los valores de **Inicio de sesión** y **Contraseña** que configuró al crear el servidor y, después, haga clic en **Conectar** para conectarse a la Base de datos SQL.

	![SQL Server Management Studio: conexión a un servidor de Base de datos SQL](./media/sql-database-connect-query-ssms/1-connect.png)

### Si se produce un error en la conexión

La razón más común de los errores de conexión son los errores en el nombre del servidor (recuerde, *&lt;;servername>* es el nombre del servidor lógico, no el de la base de datos), el nombre de usuario o contraseña, así como el servidor que no permite conexiones por motivos de seguridad. Si se conecta por primera vez o se produce un error en la conexión debido a una configuración de IP modificada, la [versión más reciente de SSMS](https://msdn.microsoft.com/library/mt238290.aspx) le pedirá la información de inicio de sesión de Azure y, a continuación, creará la regla de firewall en Azure para usted. Si está utilizando una versión anterior, se notificará la dirección IP en un mensaje de error y deberá agregar esta dirección IP a la regla de firewall del servidor en Azure. Asegúrese de que la configuración de firewall del servidor permite las conexiones desde la dirección IP de su equipo local y la dirección IP que utiliza el cliente SSMS. A veces son diferentes. Para obtener más información, consulte [Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md).

## Ejecutar consultas de ejemplo

Después de conectarse al servidor lógico, puede conectarse a una base de datos y ejecutar una consulta de ejemplo. Si no creó la base de datos mediante el ejemplo de AdventureWorks en [Introducción a Base de datos SQL de Microsoft Azure](sql-database-get-started.md), esta consulta no funcionará. Vaya directamente a Pasos siguientes para obtener más información.

1. En el **Explorador de objetos**, navegue hasta la base de datos de **AdventureWorks**.
2. Haga clic con el botón derecho en la base de datos y seleccione **Nueva consulta**.

	![Nueva consulta. Conexión a servidor de Base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. En la ventana de consulta, copie y pegue el código siguiente.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Haga clic en el botón **Ejecutar**. En la siguiente captura de pantalla se muestra una consulta correcta.

	![Correcto. Conexión a servidor de Base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Pasos siguientes

Puede usar instrucciones T-SQL para crear y administrar bases de datos en Azure casi de la misma manera que con SQL Server. Si está familiarizado con el uso de T-SQL con SQL Server, consulte [Información sobre Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md) para obtener un resumen de las diferencias.

Si no está familiarizado con T-SQL, consulte [Tutorial: Escribir instrucciones Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) y [Referencia de Transact-SQL (motor de base de datos)](https://msdn.microsoft.com/library/bb510741.aspx).

<!---HONumber=AcomDC_0128_2016-->