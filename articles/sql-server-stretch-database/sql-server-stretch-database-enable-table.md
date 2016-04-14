<properties
	pageTitle="Habilitación de Stretch Database para una tabla | Microsoft Azure"
	description="Obtenga información sobre cómo configurar una tabla para Stretch Database."
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

# Habilitación de Stretch Database para una tabla

Para configurar una tabla para Stretch Database, seleccione **Stretch | Habilitar** en una tabla en SQL Server Management Studio para abrir el asistente para **habilitar una tabla para Stretch**. También puede utilizar Transact-SQL para habilitar Stretch Database para una tabla.

-   Si almacena los datos históricos en una tabla independiente, puede migrar toda la tabla.

-   Si la tabla contiene datos históricos y actuales, puede especificar un predicado de filtro para seleccionar las filas que migrar. En CTP 3.1 a través de RC0, la opción de especificar un predicado de filtro no está disponible en el asistente para habilitar una base de datos para Stretch. Tendrá que usar la instrucción ALTER TABLE para configurar una tabla para Stretch Database con esta opción. Para obtener más información, vea [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

**Requisitos previos:** Si selecciona **Stretch | Habilitar** para una tabla y no ha habilitado todavía Stretch Database para la base de datos, el asistente configura primero la base de datos para Stretch Database. Siga los pasos de [Asistente para habilitar la base de datos para Stretch](sql-server-stretch-database-wizard.md) en lugar de los pasos de este tema.

**Permisos**. La habilitación de Stretch Database en una base de datos o una tabla requiere permisos db\_owner. La habilitación de Stretch Database en una tabla también requiere permisos ALTER en la tabla.

## <a name="EnableWizardTable"></a>Uso del asistente para habilitar Stretch Database en una tabla
**Inicio del asistente**
1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla en la que desee habilitar Stretch.

2.  Haga clic con el botón derecho y seleccione **Stretch** y **Habilitar** para iniciar el asistente.

**Introducción** Consulte el propósito del asistente y los requisitos previos.

**Selección de las tablas de base de datos** Confirme que se muestra y se selecciona la tabla que desea habilitar.

En CTP 3.1 a través de RC0, solo puede migrar una tabla completa mediante el asistente. Si desea especificar un predicado para seleccionar las filas que migrar desde una tabla que contenga los datos históricos y actuales, ejecute la instrucción ALTER TABLE para especificar un predicado después de salir del asistente o salga del asistente y ejecute la instrucción ALTER TABLE, tal como se describe más adelante en este tema.

**Resumen** Consulte los valores que escribió y las opciones que seleccionó en el asistente. A continuación, seleccione **Finalizar** para habilitar Stretch.

**Resultados** Compruebe los resultados.

## <a name="EnableTSQLTable"></a>Uso de Transact-SQL para habilitar Stretch Database en una tabla
Para configurar una tabla para Stretch Database, ejecute el comando ALTER TABLE.

1.  Opcionalmente, use la cláusula `FILTER_PREDICATE = <predicate>` para especificar un predicado y seleccionar las filas que migrar si la tabla contiene datos históricos y actuales. El predicado debe llamar a una función con valores de tabla insertada. Para obtener más información, vea [Escritura de una función con valores de tabla insertada para seleccionar filas (Stretch Database)](sql-server-stretch-database-predicate-function.md). Si no especifica un predicado de filtro, se migra toda la tabla.

    > [IMPORTANTE] Si proporciona un predicado de filtro que tiene un rendimiento insuficiente, la migración de datos también lo tendrá. Stretch Database aplica el predicado de filtro a la tabla con el operador CROSS APPLY.

    En CTP 3.1 a través de RC0, esta opción no está disponible en el asistente para habilitar la base de datos para Stretch. Tendrá que usar la instrucción ALTER TABLE para configurar una tabla para Stretch Database con esta opción. Para obtener más información, vea [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx.

2.  Especifique `MIGRATION_STATE = OUTBOUND` para iniciar la migración de datos inmediatamente o `MIGRATION_STATE = PAUSED` para posponer el inicio de la migración de datos.

Este es un ejemplo en el que se migra toda la tabla y en el que la migración de datos se inicia de inmediato.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
GO;
```
En el ejemplo siguiente se migran solo las filas identificadas mediante la función con valores de tabla insertada de `dbo.fn_stretchpredicate` y pospone la migración de datos. Para obtener más información sobre el predicado de filtro, consulte [Escritura de una función con valores de tabla insertada para seleccionar filas (Stretch Database)](sql-server-stretch-database-predicate-function.md).

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) )
```

## Consulte también
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0302_2016-->