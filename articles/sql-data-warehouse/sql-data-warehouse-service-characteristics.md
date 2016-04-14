<properties
   pageTitle="Características del servicio Almacenamiento de datos SQL | Microsoft Azure"
   description="Valores máximos para conexiones, consultas, DDL y DML de Transact-SQL y vistas del sistema de Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/11/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Características del servicio Almacenamiento de datos SQL

Estas características incluyen valores máximos que ha establecido Almacenamiento de datos SQL de Azure para respaldar las cargas de trabajo de análisis más exigentes al tiempo que garantizan también que cada consulta individual tenga los recursos que necesita para un rendimiento óptimo.

## Conexiones

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Sesión | Sesiones abiertas simultáneas | 1024<br/><br/>Admitimos 1024 conexiones simultáneas que pueden enviar todas solicitudes simultáneas al almacén de datos. Tenga en cuenta que no hay límite en el número de consultas que se pueden ejecutar a la vez. Cuando se supera un límite, la solicitud entra en una cola interna donde espera a ser procesada.|
| Sesión | Memoria máxima para instrucciones preparadas | 20 MB |
| Inicios de sesión | Inicios de sesión por servidor de SQL. | 500\.000 |


## Procesamiento de consultas

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Consultar | Consultas simultáneas en tablas de usuario | 32<br/><br/>Las consultas adicionales se dirigirán a una cola interna donde quedarán a la espera de ser procesadas. Con independencia del número de consultas que se ejecutan al mismo tiempo, cada consulta está optimizada para hacer un uso completo de la arquitectura de procesamiento paralelo de forma masiva.|
| Consultar | Consultas en cola en tablas de usuario | 1000 |
| Consultar | Consultas simultáneas en vistas de sistema | 100 |
| Consultar | Consultas en cola en vistas de sistema | 1000 |
| Consultar | Parámetros máximos | 2098 |
| Lote | Tamaño máximo | 65 536*4096 |


## Lenguaje de definición de datos (DDL)

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabla | Tablas por base de datos | 2 mil millones |
| Tabla | Columnas por tabla | 1024 columnas |
| Tabla | Bytes por columna | 8000 bytes |
| Tabla | Bytes por fila, tamaño definido | 8060 bytes<br/><br/>Se calcula el número de bytes por fila de la misma manera que para SQL Server con la compresión de página activada. No puede ser mayor que 8060 bytes. Para calcular el tamaño de fila, vea los cálculos de tamaño de fila de [Estimar el tamaño de un índice agrupado](https://msdn.microsoft.com/library/ms178085.aspx) en MSDN.<br/><br/>Para obtener una lista de los tamaños de tipo de datos de Almacenamiento de datos SQL, consulte [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx) (CREATE TABLE (Almacenamiento de datos SQL de Azure)). |
| Tabla | Particiones por tabla | 15 000<br/><br/>Para obtener un alto rendimiento, se recomienda reducir el número de particiones que necesita pero sin perder de vista sus necesidades empresariales. A medida que crece el número de particiones, la sobrecarga de operaciones de DDL y DML crece, lo que hace que el rendimiento sea más lento.|
| Tabla | Caracteres por valor de límite de partición| 4000 |
| Índice | Índices no agrupados por tabla | 999<br/><br/>Solo se aplica a tablas de almacén de filas.|
| Índice | Índices agrupados por tabla | 1<br><br/>Se aplica a tablas de almacén de filas y de almacén de columnas.|
| Índice | Filas de un grupo de filas de índice de almacén de columnas | 1024<br/><br/>Cada índice de almacén de columnas se implementa como varios índices de almacén de columnas. Tenga en cuenta que si inserta 1024 filas en un índice de almacén de columnas de Almacenamiento de datos SQL, las filas no irán al mismo grupo de filas.|
| Índice | Compilaciones simultáneas de índices de almacén de columnas agrupados. | 32<br/><br/>Se aplica cuando los índices de almacén de columnas agrupados se compilan en diferentes tablas. Solo se permite una compilación de índice de almacén de columnas agrupado por tabla. Las solicitudes adicionales esperan en una cola.|
| Índice | Tamaño de clave de índice | 900 bytes.<br/><br/>Se aplica solo a los índices de almacén de filas.<br/><br/>Si los datos existentes en las columnas no superan los 900 bytes cuando se crea el índice, pueden crearse índices en columnas varchar con un tamaño máximo de más de 900 bytes. Sin embargo, las posteriores acciones INSERT o UPDATE en las columnas que hacen que el número total supere los 900 bytes darán error.|
| Índice | Columnas de clave por índice | 16<br/><br/>Solo se aplica a los índices de almacén de filas. Los índices de almacén de columnas agrupados incluyen todas las columnas.|
| Estadísticas | Tamaño de los valores de columna combinados | 900 bytes |
| Estadísticas | Columnas por objeto de estadísticas | 32 |
| Estadísticas | Estadísticas creadas en columnas por tabla | 30 000 |
| Procedimientos almacenados | Niveles máximos de anidamiento | 8 |
| Ver | Columnas por vista | 1024 |


## Lenguaje de manipulación de datos (DML)

| Categoría | Descripción | Máxima |
| :---------------- | :------------------------------------------- | :----------------- |
| Resultados de SELECT | Columnas por fila | 4096<br/><br/>Nunca puede tener más de 4096 columnas por fila en el resultado de SELECT. No hay ninguna garantía de que pueda tener siempre 4096. Si el plan de consulta requiere una tabla temporal, se podría aplicar el máximo de 1024 columnas por tabla.|
| Resultados de SELECT | Bytes por fila | > 8060<br/><br/>El número de bytes por fila en el resultado de SELECT puede ser superior al máximo de 8060 bytes que se permite para filas de tabla. Si el plan de consulta de la instrucción SELECT requiere una tabla temporal, se podría aplicar el máximo de tabla de 8060 bytes.|
| Resultados de SELECT | Bytes por columna | > 8000<br/><br/>El número de bytes por columna en el resultado de SELECT puede ser superior al máximo de 8000 bytes que se permite para columnas de tabla. Si el plan de consulta de la instrucción SELECT requiere una tabla temporal, se podría aplicar el máximo de tabla de 8000 bytes.|
| SELECT | Subconsultas anidadas | 32<br/><br/>Nunca puede tener más de 32 subconsultas anidadas en una instrucción SELECT. No hay ninguna garantía de que siempre pueda tener 32. Por ejemplo, una instrucción JOIN puede introducir una subconsulta en el plan de consulta. El número de subconsultas también puede estar limitado por la memoria disponible.|
| SELECT | Columnas por JOIN | 1024 columnas<br/><br/>Nunca puede tener más de 1024 columnas en la instrucción JOIN. No hay ninguna garantía de que siempre pueda tener 1024. Si el plan JOIN requiere una tabla temporal con más columnas que el resultado de JOIN, se aplica el límite de 1024 a la tabla temporal. |
| SELECT | Bytes por columnas GROUP BY | 8060<br/><br/>Las columnas de la cláusula GROUP BY pueden tener como máximo 8060 bytes.|
| SELECT | Bytes por columnas ORDER BY | 8060 bytes<br/><br/>Las columnas de la cláusula ORDER BY pueden tener como máximo 8060 bytes.|
| INSERT | Bytes por columna, ancho fijo y variable | 8000 bytes Los intentos de insertar un número mayor de bytes que los definidos para la columna darán error.|
| INSERT | Bytes por fila, columnas de ancho variable | >8060 bytes Los datos que superan los 8060 bytes se colocan en el área de almacenamiento de desbordamiento.|
| UPDATE | Bytes por columna, ancho fijo y variable | 8000 bytes Los intentos de actualizar una columna a un valor que requiere un número mayor de bytes que los definidos para la columna darán error.|
| Identificadores y constantes por instrucción | Número de identificadores y constantes de referencia. | 65 535<br/><br/>Almacenamiento de datos SQL limita el número de identificadores y constantes que pueden incluirse en una única expresión de una consulta. Este límite es 65 535 GB. Si se supera este número se produce el error de SQL Server 8632. Para más información, consulte [Error interno: se ha alcanzado un límite de servicios de una expresión](http://support.microsoft.com/kb/913050/).|

## Vistas de sistema

| Vista de sistema | Número máximo de filas |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Número total de trabajadores de DMS para las 1000 solicitudes de SQL más recientes. |
| sys.dm\_pdw\_dms\_worker\_pairs | 10\.000 |
| sys.dm\_pdw\_errors | 10\.000 |
| sys.dm\_pdw\_exec\_requests | 10\.000 |
| sys.dm\_pdw\_exec\_sessions | 10\.000 |
| sys.dm\_pdw\_request\_steps | Número total de pasos para las 1000 solicitudes SQL más recientes que se almacenan en sys.dm\_pdw\_exec\_requests. |
| sys.dm\_pdw\_os\_event\_logs | 10\.000 |
| sys.dm\_pdw\_sql\_requests | Las 1000 solicitudes SQL más recientes que se almacenan en sys.dm\_pdw\_exec\_requests. |

## Pasos siguientes
Para obtener más información de referencia, vea [Información general de referencia de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general de referencia de Almacenamiento de datos SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0218_2016-->