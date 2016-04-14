<properties
   pageTitle="Conexión a Almacenamiento de datos SQL con Visual Studio | Microsoft Azure"
   description="Introducción a la conexión a Almacenamiento de datos SQL y ejecución de algunas consultas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Conexión a Almacenamiento de datos SQL con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Este tutorial muestra cómo conectarse a una base de datos de Almacenamiento de datos SQL de Azure en solo unos minutos con SQL Server Data Tools en Visual Studio. Una vez conectado, ejecutará una consulta simple.

## Requisitos previos

+ Base de datos de ejemplo AdventureWorksDW en Almacenamiento de datos SQL. Para crearla, consulte [Creación de una base de datos de Almacenamiento de datos SQL](sql-data-warehouse-get-started-provision.md).
+ SQL Server Data Tools para Visual Studio Para ver instrucciones y opciones de instalación, consulte [Instalación de Visual Studio y SSDT](sql-data-warehouse-install-visual-studio.md)

## Paso 1: Búsqueda del nombre completo de Azure SQL Server

La base de datos está asociado a un Azure SQL Server. Para conectarse a la base de datos, necesita el nombre completo del servidor (***servername**.database.windows.net*).

Para buscar el nombre del servidor completo.

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Bases de datos SQL** y haga clic en la base de datos a la que desea conectarse. Este ejemplo usa la base de datos de ejemplo AdventureWorksDW.
3. Busque el nombre del servidor completo:

    ![Nombre del servidor completo][1]

## Paso 2: Conexión a la base de datos SQL

1. Abra Visual Studio.
2. Abra el Explorador de objetos de SQL Server. Para ello, seleccione **Ver** > **Explorador de objetos de SQL Server**.

    ![Explorador de objetos de SQL Server][2]

3. Haga clic en el botón **Agregar SQL Server**.

    ![Agregar SQL Server][3]

1. Rellene los campos en la ventana Conectar al servidor.

    ![Conectar al servidor][4]

    - **Nombre del servidor**. Escriba el *nombre del servidor* que hemos encontrado.
    - **Autenticación**. Seleccione Autenticación de SQL Server.
    - **Inicio de sesión** y **contraseña**. Escriba el inicio de sesión y la contraseña para Azure SQL Server.
    - Haga clic en **Conectar**.

1. Para explorar, expanda su Azure SQL Server. Puede ver las bases de datos asociadas al servidor. Expanda AdventureWorksDW para ver las tablas de la base de datos de ejemplo.

    ![Explorar AdventureWorksDW][5]


## Paso 3: Ejecución de una consulta de ejemplo

Ahora que nos hemos conectado al servidor, continuemos y escribamos una consulta.

1. Haga clic con el botón derecho en la base de datos en el Explorador de objetos de SQL Server.

2. Seleccione **Nueva consulta**. Se abre una nueva ventana de consulta.

    ![Nueva consulta][6]

3. Copie esta consulta TSQL en la ventana de consulta:

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Ejecute la consulta. Para hacerlo, haga clic en la flecha verde o use la combinación de teclas `CTRL`+`SHIFT`+`E`.

    ![Ejecutar consulta][7]

1. Consulte los resultados de la consulta. En este ejemplo, la tabla FactInternetSales tiene 60398 filas.

    ![Resultados de la consulta][8]

## Pasos siguientes

Ahora que puede conectarse y realizar consultas, pruebe a [visualizar los datos con PowerBI][].

[visualizar los datos con PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0309_2016-->