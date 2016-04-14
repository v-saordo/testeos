<properties
	pageTitle="Identificación de bases de datos y tablas para Stretch Database mediante la ejecución de Stretch Database Advisor | Microsoft Azure"
	description="Aprenda a identificar las bases de datos y las tablas que son candidatas a Stretch Database."
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

# Identificación de bases de datos y tablas para Stretch Database mediante la ejecución de Stretch Database Advisor

Para identificar las bases de datos y las tablas que son candidatas a Stretch Database, descargue el Asesor de actualizaciones de SQL Server 2016 y ejecute Stretch Database Advisor. Stretch Database Advisor también identifica problemas de bloqueo.

## Descarga e instalación del Asesor de actualizaciones
Descargue e instale el Asesor de actualizaciones desde [aquí](http://go.microsoft.com/fwlink/?LinkID=613421). Esta herramienta no se incluye en los medios de instalación de SQL Server.

## Ejecución de Stretch Database Advisor

1.  Ejecute el Asesor de actualizaciones.

2.  Seleccione **Escenarios** y, después, **EJECUTAR STRETCH DATABASE ADVISOR**.

3.  En la hoja **Ejecutar Stretch Database Advisor**, haga clic en **SELECCIONAR BASES DE DATOS PARA ANALIZAR**.

4.  En la hoja **Seleccionar bases de datos**, haga clic en **INSTANCIA DE SQL**.

5.  En la hoja **Conectar a la instancia de SQL**, escriba el nombre de la instancia de SQL Server. Haga clic en **Conectar**.

6.  En la hoja **Seleccionar bases de datos**, seleccione las bases de datos que quiere analizar. A continuación, haga clic en **Seleccionar**.

7.  En la hoja **Ejecutar Stretch Database Advisor**, haga clic en **Ejecutar**. Se ejecuta el análisis.

## Revisión del resultado

1.  Cuando finalice el análisis, en la hoja **Stretch Database Advisor**, seleccione una de las bases de datos que ha analizado para mostrar la hoja **Resultados del análisis**.

    En la hoja **Resultados del análisis** se muestran las tablas recomendadas de la base de datos seleccionada que coinciden con los criterios de recomendación predeterminados. Tiene la opción de ajustar el tamaño y el recuento de filas para expandir o contraer la lista de tablas recomendadas.

2.  En la lista de tablas recomendadas de la hoja **Resultados del análisis**, seleccione una de las tablas recomendadas para mostrar la hoja **Resultados de la tabla**.

    En la hoja **Resultados de la tabla** se muestran los problemas de bloqueo de la tabla seleccionada. Para más información sobre los problemas de bloqueo detectados por Stretch Database Advisor, consulte [Limitaciones de área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md).

3.  En la lista de problemas de bloqueo de la hoja **Resultados de la tabla**, seleccione uno de los problemas para mostrar la hoja **Resultado de la regla**.

    En la hoja **Resultado de la regla** se describe el problema seleccionado y se proponen pasos para resolverlo. Implemente los pasos de solución sugeridos si quiere configurar la tabla seleccionada para Stretch Database.

## Paso siguiente
Habilite Stretch Database.

-   Para habilitar Stretch Database en una **base de datos**, consulte [Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md).

-   Para habilitar Stretch Database en otra **tabla** cuando Stretch ya está habilitado en la base de datos, consulte [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md).

## Consulte también
[Limitaciones de área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md), [Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md) y [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md).

<!---HONumber=AcomDC_0302_2016-->