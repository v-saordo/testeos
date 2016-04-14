<properties
	pageTitle="Deshabilitación de Stretch Database y devolución de datos remotos | Microsoft Azure"
	description="Obtenga información sobre cómo deshabilitar Stretch Database para una tabla y, opcionalmente, recuperar datos remotos."
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

# Deshabilitación de Stretch Database y devolución de datos remotos

Para deshabilitar Stretch Database para una tabla, seleccione **Stretch** para una tabla en SQL Server Management Studio. Seleccione una de las siguientes opciones.

-   **Deshabilitar | Devolver datos de Azure**. Copia los datos remotos para la tabla de Azure a SQL Server y, a continuación, deshabilita Stretch Database para la tabla. Esta operación provoca costos de transferencia de datos y no se puede cancelar.

-   **Deshabilitar | Dejar los datos en Azure**. Deshabilita Stretch Database para la tabla. Abandona los datos remotos para la tabla en Azure.

Después de deshabilitar Stretch Database para una tabla, se detiene la migración de datos y los resultados de la consulta dejan de incluir los resultados de la tabla remota.

También puede utilizar Transact-SQL para deshabilitar Stretch Database para una tabla o una base de datos.

Si simplemente desea pausar la migración de datos, consulte [Pausa y reanudación de Stretch Database](sql-server-stretch-database-pause.md).

## Deshabilitación de Stretch Database para una tabla

### Uso de SQL Server Management Studio para deshabilitar Stretch Database para una tabla

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla para la que desee deshabilitar Stretch Database.

2.  Haga clic con el botón derecho y seleccione **Stretch** y, a continuación, seleccione una de las siguientes opciones.

    -   **Deshabilitar | Devolver datos de Azure**. Copia los datos remotos para la tabla de Azure a SQL Server y, a continuación, deshabilita Stretch Database para la tabla. Este comando no se puede cancelar.

        Esta operación provoca costos de transferencia de datos. Para obtener más información, vea [Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/).

        Después de que todos los datos remotos se hayan copiado de Azure a SQL Server, Stretch se deshabilita para la tabla.

    -   **Deshabilitar | Dejar los datos en Azure**. Deshabilita Stretch Database para la tabla. Abandona los datos remotos para la tabla en Azure.

        El abandono de los datos remotos y la deshabilitación de Stretch no quitan los datos remotos. Si desea eliminar los datos remotos, tiene que quitar la tabla remota mediante el Portal de administración de Azure.

### Uso de Transact-SQL para deshabilitar Stretch Database para una tabla

-   Para deshabilitar Stretch para una tabla y copiar los datos remotos para la tabla de Base de datos SQL de Azure a SQL Server, ejecute el siguiente comando. Este comando no se puede cancelar.

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;
    ```
    Esta operación provoca costos de transferencia de datos. Para obtener más información, vea [Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/).

    Después de que todos los datos remotos se hayan copiado de Azure a SQL Server, Stretch se deshabilita para la tabla.

-   Para deshabilitar Stretch para una tabla y abandonar los datos remotos, ejecute el siguiente comando.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```
    El abandono de los datos remotos y la deshabilitación de Stretch no quitan los datos remotos. Si desea eliminar los datos remotos, tiene que quitar la tabla remota mediante el Portal de administración de Azure.

## Deshabilitación de Stretch Database para una base de datos
Para poder deshabilitar Stretch Database para una base de datos, tendrá que deshabilitar Stretch Database en las tablas habilitadas para Stretch individuales en la base de datos.

### Uso de SQL Server Management Studio para deshabilitar Stretch Database para una base de datos

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la base de datos para la que desee deshabilitar Stretch Database.

2.  Haga clic con el botón derecho y seleccione **Tareas**, **Stretch** y **Deshabilitar**.

### Uso de Transact-SQL para deshabilitar Stretch Database para una base de datos
Ejecute el siguiente comando.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

## Eliminación de una base de datos habilitada para Stretch
La característica de eliminación de una base de datos habilitada para Stretch Database quita la base de datos local, pero no quita los datos remotos. Si desea eliminar los datos remotos, tiene que quitar la base de datos remota mediante el Portal de administración de Azure.

## Consulte también
[ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx) [Pausa y reanudación de Stretch Database](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0302_2016-->