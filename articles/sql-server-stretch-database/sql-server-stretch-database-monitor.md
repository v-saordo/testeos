<properties
	pageTitle="Supervisión y solución de problemas de migración de datos (Stretch Database) | Microsoft Azure"
	description="Aprenda a supervisar el estado de migración de datos."
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

# Supervisión y solución de problemas de migración de datos (Stretch Database)

Para supervisar la migración de datos en Stretch Database Monitor, seleccione **Tareas | Stretch | Monitor** para una base de datos en SQL Server Management Studio.

## Comprobación del estado de la migración de datos en Stretch Database Monitor
Seleccione **Tareas | Stretch | Monitor** para una base de datos en SQL Server Management Studio para abrir Stretch Database Monitor y supervisar la migración de datos.

-   La parte superior del monitor muestra información general acerca de la base de datos de SQL Server habilitada para Stretch y la base de datos de Azure remota.

-   La parte inferior del monitor muestra el estado de migración de datos para cada tabla habilitada para Stretch en la base de datos.

![Stretch Database Monitor][StretchMonitorImage1]

## <a name="Migration"></a>Comprobación del estado de la migración de datos en una vista de administración dinámica
Abra la vista de administración dinámica **sys.dm\_db\_rda\_migration\_status** para ver cuántos lotes y filas de datos se han migrado. Para más información, consulte [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="Firewall"></a>Solución de problemas de migración de datos
Hay varios problemas que pueden afectar a la migración. Compruebe lo siguiente.

-   Compruebe la conectividad de red para el equipo con SQL Server.

-   Compruebe el estado del lote más reciente en la vista de administración dinámica **sys.dm\_db\_rda\_migration\_status**. Si se ha producido un error, compruebe los valores error\_number, error\_state y error\_severity para el lote.

    -   Para más información sobre la vista, consulte [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

    -   Para más información sobre el contenido de un mensaje de error de SQL Server, consulte [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

## Otras referencias
[Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0302_2016-->