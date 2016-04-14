<properties
	pageTitle="Pausa y reanudación de la migración de datos (Stretch Database) | Microsoft Azure"
	description="Aprenda a pausar o reanudar la migración de datos en Azure."
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

# Pausa y reanudación de la migración de datos (Stretch Database)

Para pausar o reanudar la migración de datos en Azure, seleccione **Stretch** para una tabla en SQL Server Management Studio y después seleccione **Pausar** para pausar la migración de datos o **Reanudar** para reanudarla. También puede usar Transact-SQL para pausarla o reanudarla.

Pause la migración de datos en tablas individuales cuando desee solucionar problemas en el servidor local o para maximizar el ancho de banda de red disponible.

## Pausa de migración de datos

### Uso de SQL Server Management Studio para pausar la migración de datos

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla habilitada para Stretch en la que desee pausar la migración de datos.

2.  Haga clic con el botón derecho, seleccione **Stretch** y después seleccione **Pausar**.

### Uso de Transact-SQL para pausar la migración de datos
Ejecute el siguiente comando.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;
GO;
```

## Reanudación de la migración de datos

### Uso de SQL Server Management Studio para reanudar la migración de datos

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla habilitada para Stretch en la que desee reanudar la migración de datos.

2.  Haga clic con el botón derecho, seleccione **Stretch** y después seleccione **Reanudar**.

### Uso de Transact-SQL para reanudar la migración de datos
Ejecute el siguiente comando.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;
```

## Consulte también
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0302_2016-->