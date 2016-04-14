<properties
	pageTitle="Administración y solución de problemas de Stretch Database | Microsoft Azure"
	description="Obtenga información sobre la administración y solución de problemas de Stretch Database."
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

# Administración y solución de problemas de Stretch Database

Para administrar y solucionar problemas de Stretch Database, use las herramientas y los métodos que se describen en este tema.

## <a name="LocalInfo"></a>Obtención de información sobre las bases de datos y tablas locales habilitadas para Stretch Database
Abra las vistas de catálogo **sys.databases** y **sys.tables** para ver información sobre las bases de datos y tablas de SQL Server habilitadas para Stretch. Para obtener más información, consulte [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) y [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

## <a name="RemoteInfo"></a>Obtención de información sobre las bases de datos y tablas remotas usadas por Stretch Database
Abra las vistas de catálogo **sys.remote\_data\_archive\_databases** y **sys.remote\_data\_archive\_tables** para ver información sobre las tablas y bases de datos remotas en las que se almacenan los datos migrados. Para obtener más información, vea [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) y [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Comprobación del predicado de filtro aplicado a una tabla
Abra la vista de catálogo **sys.remote\_data\_archive\_tables** y compruebe el valor de la columna **filter\_predicate**. Si el valor es null, la tabla completa es elegible para la migración. Para obtener más información, vea [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="Migration"></a>Comprobación del estado de la migración de datos
Seleccione **Tareas | Stretch | Monitor** para que una base de datos en SQL Server Management Studio supervise la migración de datos en Stretch Database Monitor. Para obtener más información, vea [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

También puede abrir la vista de administración dinámica **sys.dm\_db\_rda\_migration\_status** para ver cuántos lotes y filas de datos se han migrado.

## Aumento del nivel de rendimiento de Azure para operaciones que consumen muchos recursos, como la indexación
Al generar, volver a generar o reorganizar un índice en una tabla grande que está configurada para Stretch Database, considere la posibilidad de aumentar el nivel de rendimiento de la base de datos remota correspondiente para la duración de la operación.

## No cambiar el esquema de la tabla remota
No cambie el esquema de una tabla remota de Azure asociada con una tabla de SQL Server configurada para Stretch Database. En particular, no modifique el nombre o el tipo de datos de una columna. La característica de Stretch Database realiza distintos supuestos acerca del esquema de la tabla remota en relación con el esquema de la tabla de SQL Server. Si cambia el esquema remoto, Stretch Database deja de funcionar para la tabla modificada.

## <a name="Firewall"></a>Solución de problemas de migración de datos
Para obtener sugerencias para la solución de problemas, vea [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

## Solución de problemas del rendimiento de las consultas
**Las consultas que incluyen mi tabla habilitada para Stretch son lentas.** Se espera que las consultas que incluyen tablas habilitadas para Stretch se realicen más lentamente de lo que lo hacían antes de que las tablas se habilitaran para Stretch. Si se reduce significativamente el rendimiento de las consultas, vea los siguientes problemas posibles.

-   ¿El servidor de Azure está en una región geográfica diferente de la de SQL Server? Configure el servidor de Azure para que esté en la misma región geográfica que su servidor SQL para reducir la latencia de red.

-   Las condiciones de red pueden reducirse. Póngase en contacto con el administrador de red para obtener información sobre los problemas recientes o interrupciones.

## Consulte también
[Supervisión de Stretch Database](sql-server-stretch-database-monitor.md) [Copia de seguridad y restauración de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0302_2016-->