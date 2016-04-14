<properties
	pageTitle="Limitaciones de área expuesta y problemas de bloqueo de Stretch Database | Microsoft Azure"
	description="Obtenga información acerca de los problemas de bloqueo que se deben resolver para poder habilitar Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Limitaciones de área expuesta y problemas de bloqueo de Stretch Database

Obtenga información acerca de los problemas de bloqueo que se deben resolver para poder habilitar Stretch Database.

## <a name="Limitations"></a>Problemas de bloqueo
En la versión preliminar actual de SQL Server 2016, los siguientes elementos hacen que una tabla no sea apta para Stretch.

**Propiedades de tabla**
-   Más de 1023 columnas

-   Más de 998 índices

-   Tablas que contienen datos FILESTREAM

-   FileTables

-   Tablas replicadas

-   Tablas que están usando Seguimiento de cambios o Captura de datos modificados

-   Tablas con optimización para memoria

**Tipos de datos y propiedades de columna**
-   timestamp

-   sql\_variant

-   XML

-   geometry

-   geography

-   hierarchyid

-   Tipos definidos por el usuario (UDT) CLR

**Tipos de columna**
-   COLUMN\_SET

-   Columnas calculadas

**Restricciones**
-   Restricciones CHECK

-   Restricciones predeterminadas

-   Restricciones de clave externa que hacen referencia a la tabla

**Índices**
-   Índices de texto completo

-   Índices XML

-   Índices espaciales

-   Vistas indexadas que hacen referencia a la tabla

## <a name="Caveats"></a>Limitaciones y salvedades para las tablas habilitadas para Stretch
En la versión de vista preliminar actual de SQL Server 2016, las tablas habilitadas para Stretch tienen las siguientes limitaciones o salvedades.

-   No se exige la unicidad en las restricciones UNIQUE ni PRIMARY KEY en una tabla habilitada para Stretch.

-   No se pueden ejecutar las operaciones UPDATE ni DELETE en una tabla habilitada para Stretch.

-   No se puede realizar una operación INSERT en la tabla de Base de datos SQL de Azure remota.

-   No se puede crear un índice para una vista que incluya tablas habilitadas para Stretch.

-   No se pueden realizar acciones de actualización ni eliminación desde una vista que incluya tablas habilitadas para Stretch. Sin embargo, se puede insertar en una vista que incluya tablas habilitadas para Stretch.

-   Los filtros en los índices no se propagan a la tabla remota.

## Consulte también
[Identificación de bases de datos y tablas para Stretch Database mediante la ejecución de Stretch Database Advisor](sql-server-stretch-database-identify-databases.md) [Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md) [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0302_2016-->