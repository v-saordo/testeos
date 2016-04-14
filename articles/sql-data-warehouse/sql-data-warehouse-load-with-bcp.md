<properties
   pageTitle="Uso de bcp para cargar datos en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre qué es bcp y cómo usarlo en escenarios de almacenamiento de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
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


# Carga de datos con bcp

> [AZURE.SELECTOR]
- [Factoría de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** es una utilidad de carga masiva de línea de comandos que permite copiar datos entre SQL Server, archivos de datos y Almacenamiento de datos SQL. Use bcp para importar grandes cantidades de filas en tablas de Almacenamiento de datos SQL o para exportar datos de tablas de SQL Server a archivos de datos. Excepto cuando se usa con la opción queryout, bcp no exige ningún conocimiento de Transact-SQL.

bcp es una forma rápida y sencilla de realizar operaciones de introducción y extracción de datos en una base de datos de Almacenamiento de datos SQL. La cantidad exacta de datos que se recomienda para la carga o extracción mediante bcp dependerá de la conexión de red con el centro de datos de Azure. Por lo general, se pueden cargar y extraer tablas de dimensiones, pero es posible que las tablas de hechos bastante grandes tarden una cantidad considerable de tiempo en cargarse o extraerse.

Con bcp puede:

- Usar una utilidad de línea de comandos sencilla para cargar datos en Almacenamiento de datos SQL.
- Usar una utilidad de línea de comandos sencilla para extraer datos de Almacenamiento de datos SQL.

Este tutorial le mostrará cómo:

- Importar datos en una tabla mediante el comando in de bcp
- Importar datos en una tabla mediante el comando out de bcp

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Requisitos previos

Para seguir paso a paso este tutorial, necesita:

- Una base de datos de Almacenamiento de datos SQL
- La utilidad de línea de comandos bcp instalada
- La utilidad de línea de comandos SQLCMD instalada

>[AZURE.NOTE] Puede descargar las utilidades bcp y SQLCMD del [Centro de descarga de Microsoft][].

## Importación de datos en Almacenamiento de datos SQL

En este tutorial, creará una tabla en Almacenamiento de datos SQL de Azure e importará datos en la tabla.

### Paso 1: Crear una tabla en Almacenamiento de datos SQL de Azure

Desde un símbolo del sistema, conéctese a la instancia con el comando siguiente, reemplazando los valores según corresponda:

```
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I
```
Una vez conectado, copie el script de tabla siguiente en el símbolo del sistema de SQLCMD y presione la tecla ENTRAR:

```
CREATE TABLE DimDate2
(
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
);
GO
```
>[AZURE.NOTE] Consulte el tema [Diseño de tablas][] en el grupo de temas relacionados con el desarrollo para obtener más información sobre las opciones disponibles con la cláusula WITH.

### Paso 2: Crear un archivo de datos de origen

Abra el Bloc de notas y copie las siguientes líneas de datos en un nuevo archivo.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

Guárdelo en el directorio temporal local, C:\\Temp\\DimDate2.txt.

> [AZURE.NOTE] Es importante recordar que bcp.exe no admite la codificación de archivos UTF-8. Use archivos codificados en ASCII o la codificación UTF-16 para los archivos al usar bcp.exe.

### Paso 3: Conectar e importar los datos
Con bcp, puede conectarse e importar los datos con el comando siguiente, reemplazando los valores según corresponda:

```
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Puede comprobar que los datos se cargaron conectándose con SQLCMD como antes y ejecutando el siguiente comando TSQL:

```
SELECT * FROM DimDate2 ORDER BY 1;
GO
```

Esto debe devolver los siguientes resultados:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### Paso 4: Crear estadísticas de los datos recién cargados

Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas. Con la finalidad de obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos. Si desea ver una explicación detallada de las estadísticas, consulte el tema [Estadísticas][] en el grupo de temas relacionados con el desarrollo. A continuación, puede ver un ejemplo rápido de cómo crear estadísticas sobre los datos cargados y organizados en tablas que aparecen en este ejemplo.

Ejecute las siguientes instrucciones CREATE STATISTICS desde un símbolo del sistema sqlcmd:

```
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
GO
```

## Exportación de datos de Almacenamiento de datos SQL
En este tutorial, creará un archivo de datos a partir de una tabla de Almacenamiento de datos SQL. Se exportarán los datos creados anteriormente a un nuevo archivo de datos denominado DimDate2\_export.txt.

### Paso 1: Exportar los datos

Con la utilidad bcp, puede conectarse y exportar los datos con el comando siguiente, reemplazando los valores según corresponda:

```
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Puede comprobar que los datos se exportaron correctamente abriendo el nuevo archivo. Los datos del archivo deben coincidir con el texto siguiente:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] Dada la naturaleza de los sistemas distribuidos, puede que el orden de los datos no sea el mismo en todas las bases de datos de Almacenamiento de datos SQL. También podría usar el parámetro queryout para especificar qué consulta Transact-SQL desea ejecutar.

## Pasos siguientes
Para obtener información general sobre la carga, vea [Carga de datos en Almacenamiento de datos SQL][]. Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->

[Carga de datos en Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-load.md
[información general sobre desarrollo de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-develop.md
[Diseño de tablas]: ./sql-data-warehouse-develop-table-design.md
[Estadísticas]: ./sql-data-warehouse-develop-statistics.md


<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx


<!--Other Web references-->
[Centro de descarga de Microsoft]: http://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0309_2016-->