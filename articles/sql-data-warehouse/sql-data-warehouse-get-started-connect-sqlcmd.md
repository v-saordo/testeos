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

# Conexión y consultas con SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

En este tutorial se muestra cómo conectarse y realizar consultas a una base de datos de Almacenamiento de datos SQL de Azure en solo unos minutos con la utilidad sqlcmd.exe. En este tutorial realizará lo siguiente:

+ Instalar el software necesario
+ Conectarse a una base de datos que contenga la base de datos de ejemplo AdventureWorksDW
+ Ejecutar una consulta en la base de datos de ejemplo  

## Requisitos previos

+ [sqlcmd.exe](https://msdn.microsoft.com/library/azure/ms162773.aspx): para descargar sqlcmd.exe, consulte el [Utilidades de la línea de comandos 11 de Microsoft para SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501).

## Obtención del nombre completo del servidor de Azure SQL Server

Para conectarse a la base de datos, necesita el nombre completo del servidor (****servername**.database.windows.net*) que contiene la base de datos a la que quiere conectarse.

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Vaya a la base de datos a la que quiere conectarse.
3. Busque el nombre de servidor completo (se usará en los pasos siguientes):

![][1]


## Conexión a Almacenamiento de datos SQL con sqlcmd

Para conectarse a una instancia específica de Almacenamiento de datos SQL cuando use sqlcmd, deberá abrir el símbolo del sistema y escribir **sqlcmd**, seguido de la cadena de conexión de la base de datos de Almacenamiento de datos SQL. La cadena de conexión deberá tener los siguientes parámetros:

+ **Usuario (-U):** usuario de servidor con el formato `<`usuario`>`
+ **Contraseña (-P):** la contraseña asociada con el usuario.
+ **Servidor (-S):** servidor con el formato `<`Nombre del servidor`>`.database.windows.net
+ **Base de datos (-D):** el nombre de la base de datos.
+ **Habilitar identificadores entre comillas (-I):** los identificadores entre comillas deben estar habilitados para poder conectarse a una instancia de Almacenamiento de datos SQL.

Por lo tanto, para ello, debe escribir lo siguiente:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Ejecutar consultas de ejemplo

Después de la conexión, puede emitir cualquier instrucción Transact-SQL en la instancia.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT COUNT(*) FROM dbo.FactInternetSales;
2> GO
3> QUIT
```

Para obtener información adicional sobre sqlcmd, consulte la [documentación de sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).


## Pasos siguientes

Ahora que puede conectarse y realizar consultas, pruebe a [conectarse con PowerBI][].

[conectarse con PowerBI]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=AcomDC_0309_2016-->