<properties
	pageTitle="Supervisión del almacenamiento en memoria de XTP | Microsoft Azure"
	description="Estimar y supervisar el uso y la capacidad del almacenamiento en memoria de XTP; resolver el error de capacidad 41805"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="jodebrui"/>


# Supervisión del almacenamiento OLTP en memoria

Al usar [In-Memory OLTP](sql-database-in-memory.md), los datos de tablas con optimización para memoria y las variables de tabla residen en el almacenamiento OLTP en memoria. Cada nivel de servicio Premium tiene un tamaño máximo de almacenamiento en memoria, que se documenta en el artículo [Niveles de servicio de Base de datos SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Una vez que se supera este límite, insertar y actualizar las operaciones pueden producir errores (con error 41805). En ese momento se necesita eliminar los datos para reclamar memoria o actualizar el nivel de rendimiento de la base de datos.

## Determinación de si los datos se ajustan dentro del límite de almacenamiento en memoria

Determine el extremo de almacenamiento: consulte en el artículo [Niveles de servicio de Base de datos SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) los extremos de almacenamiento de los diferentes niveles de servicio Premium.

La estimación de los requisitos de memoria para una tabla optimizada en memoria funciona de la misma manera para SQL Server tal y como se hace en la base de datos SQL de Azure. Dedique algunos minutos a revisar ese tema en [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tenga en cuenta que la tabla y las filas de variables de tabla, así como los índices, se contarán para el tamaño de datos de usuario máx. Además, ALTER TABLE necesita suficiente espacio para crear una nueva versión de toda la tabla y sus índices.

## Supervisión y alertas

Puede supervisar el uso del almacenamiento en memoria como un porcentaje del [extremo de almacenamiento para su nivel de rendimiento](sql-database-service-tiers.md#service-tiers-for-single-databases) en el [Portal](https://portal.azure.com/) de Azure:

- En la hoja Base de datos, busque el cuadro de uso de recursos y haga clic en Editar.
- A continuación, seleccione el porcentaje de métricas de almacenamiento de OLTP en memoria.
- Para agregar una alerta, haga clic en el cuadro Uso de recursos para abrir la hoja de métricas y después haga clic en Agregar alerta.

O bien, use la siguiente consulta para mostrar el uso del almacenamiento en memoria:

    select xtp_storage_percent from sys.dm_db_resource_stats


## Corrección de situaciones de falta de memoria - Error 41805

Resultados de falta de memoria durante la ejecución en las operaciones de inserción, actualización y creación con el mensaje de error 41805.

El mensaje de error 41805 indica que las variables de tabla y las tablas optimizadas para memoria han superado el tamaño máximo.

Para resolver este error, haga uno de los siguientes:


- Elimine datos de las tablas optimizadas para memoria, lo que potencialmente descarga los datos a tablas tradicionales, basadas en disco; o bien,
- Actualice el nivel de servicio a uno con suficiente espacio de almacenamiento en memoria para los datos que necesita mantener en tablas optimizadas en memoria.

## Pasos siguientes
Obtenga más información acerca de [Supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md)

<!---HONumber=AcomDC_0224_2016-->