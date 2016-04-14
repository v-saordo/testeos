<properties
   pageTitle="Introducción a la conexión a Almacenamiento de datos SQL de Azure | Microsoft Azure"
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

# Conexión y realización de consultas con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Este tutorial muestra cómo conectarse y realizar consultas en un Almacenamiento de datos SQL de Azure en solo unos minutos con Visual Studio. En este tutorial realizará lo siguiente:

+ Instalar el software necesario
+ Conectarse a una base de datos que contenga la base de datos de ejemplo AdventureWorksDW
+ Ejecutar una consulta en la base de datos de ejemplo  

## Requisitos previos

+ Visual Studio 2013/2015: para descargar e instalar Visual Studio de 2015 o SSDT, consulte [Instalar Visual Studio y SQL Server Data Tools](sql-data-warehouse-install-visual-studio.md)

## Obtención del nombre completo del servidor de Azure SQL Server

Para conectarse a la base de datos, necesita el nombre completo del servidor ( ***servername**.database.windows.net* ) que contiene la base de datos a la que quiere conectarse.

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Vaya a la base de datos a la que quiere conectarse.
3. Busque el nombre de servidor completo (se usará en los pasos siguientes):

![][1]

## Conexión a la base de datos SQL

1. Abra Visual Studio.
2. En el menú Ver, haga clic en **Explorador de objetos de SQL Server**.

![][2]

3. Haga clic en el botón **Agregar SQL Server**.

![][3]

4. Escriba el *nombre del servidor* que se obtuvo más arriba.
5. En la lista **Autenticación**, seleccione **Autenticación de SQL Server**.
6. Escriba el **Inicio de sesión** y la **Contraseña** que especificó al crear el servidor de Base de datos SQL y haga clic en **Conectar**.

## Ejecutar consultas de ejemplo

Ahora que registramos el servidor, continuemos y escribamos una consulta.

1. Haga clic en la base de datos de usuario en SSDT.

2. Haga clic en el botón **Nueva consulta**. Se abre una nueva ventana.

![][4]

3. Escriba el siguiente código en la ventana de consulta:

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Ejecute la consulta.

	Para ejecutar la consulta, haga clic en la flecha verde o use la combinación de teclas `CTRL`+`SHIFT`+`E`.

## Pasos siguientes

Ahora que puede conectarse y realizar consultas, pruebe a [conectarse con PowerBI][].

[conectarse con PowerBI]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect-query/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect-query/connection-dialog.png
[4]: ./media/sql-data-warehouse-get-started-connect-query/new-query.png

<!---HONumber=AcomDC_0309_2016-->