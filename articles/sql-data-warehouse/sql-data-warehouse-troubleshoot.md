<properties
   pageTitle="Solución de problemas | Microsoft Azure"
   description="Solución de problemas del Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Solución de problemas
En el siguiente tema se muestran algunos de los problemas más comunes que los clientes encuentran con el Almacenamiento de datos SQL de Azure.

## Conectividad
Se puede producir un error en la conexión al Almacenamiento de datos SQL debido a dos motivos habituales:

- No se establecen las reglas de firewall.
- Uso de herramientas o protocolos no admitidos.

### Reglas de firewall
Las bases de datos SQL de Azure están protegidas por firewalls de nivel de servidor y base de datos para garantizar que solo las direcciones IP conocidas pueden tener acceso a las bases de datos. Los firewall son seguros de forma predeterminada, lo que significa que debe permitir el acceso de dirección IP para poder conectarse.

Para configurar el firewall para el acceso, siga los pasos de la sección [Configurar el acceso del firewall del servidor para su IP de cliente](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) de la página [Aprovisionar](sql-data-warehouse-get-started-provision.md).

### Uso de herramientas o protocolos no admitidos.
Almacenamiento de datos de SQL admite [Visual Studio 2013/2015](sql-data-warehouse-get-started-connect.md) como entornos de desarrollo y [SQL Server Native Client 10 y 11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx) para conectividad de cliente.

Vea nuestras páginas [Conectar](sql-data-warehouse-get-started-connect.md) para más información.

## Rendimiento de las consultas
Almacenamiento de datos de SQL usa construcciones comunes de SQL Server para ejecutar consultas incluyendo estadísticas. Las [estadísticas](sql-data-warehouse-develop-statistics.md) son objetos que contienen información sobre el intervalo y la frecuencia de valores en una columna de base de datos. El motor de consultas usa estas estadísticas para optimizar la ejecución de consultas y mejorar el rendimiento de las consultas. Puede usar la siguiente consulta para determinar la última vez que se actualizaron las estadísticas.

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1 
	AND st.[user_created] = 1;
```

Vea nuestra página [estadísticas](sql-data-warehouse-develop-statistics.md) para obtener más información.

## Principales conceptos sobre rendimiento

Consulte los artículos siguientes para comprender mejor algunos conceptos fundamentales adicionales sobre rendimiento y escala:

- [rendimiento y escala][]
- [modelo de simultaneidad][]
- [diseño de tablas][]
- [selección de una clave de distribución hash para una tabla][]
- [estadísticas para mejorar el rendimiento][]

## Pasos siguientes
Consulte el artículo [Introducción al desarrollo][] para obtener orientación sobre la creación de soluciones de Almacenamiento de datos SQL.

<!--Image references-->

<!--Article references-->

[rendimiento y escala]: sql-data-warehouse-performance-scale.md
[modelo de simultaneidad]: sql-data-warehouse-develop-concurrency.md
[diseño de tablas]: sql-data-warehouse-develop-table-design.md
[selección de una clave de distribución hash para una tabla]: sql-data-warehouse-develop-hash-distribution-key
[estadísticas para mejorar el rendimiento]: sql-data-warehouse-develop-statistics.md
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0114_2016-->