<properties
   pageTitle="Bucles en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para los bucles de Transact-SQL en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Bucles en Almacenamiento de datos SQL
Almacenamiento de datos SQL admite el bucle [WHILE][] para ejecutar bloques de instrucciones de forma repetida. Esta acción continuará siempre y cuando las condiciones especificadas se cumplan o hasta que el código termine específicamente el bucle con la palabra clave `BREAK`. Los bucles son especialmente útiles para reemplazar los cursores definidos en código SQL. Afortunadamente, casi todos los cursores que están escritos en código SQL son de la variedad avance rápido y solo lectura. Por lo tanto, los bucles [WHILE] son una buena alternativa si se encuentra con que tiene que reemplazar uno.

## Aprovechamiento de bucles y sustitución de cursores en Almacenamiento de datos SQL
Sin embargo, antes de profundizar en el tema, debe hacerse la siguiente pregunta: "¿Se pudo escribir de nuevo este cursor para usar operaciones basadas en conjunto?". En muchos casos, la respuesta será afirmativa y este suele ser el mejor enfoque. Una operación basada en conjunto a menudo se realiza bastante más rápido que un enfoque iterativo, fila a fila.

Los cursores de avance rápido y solo lectura se pueden reemplazar fácilmente por una construcción de bucle. A continuación se muestra un ejemplo sencillo: Este código de ejemplo actualiza las estadísticas de todas las tablas de la base de datos. Mediante la iteración a través de las tablas del bucle, podemos ejecutar cada comando en una secuencia.

En primer lugar, cree una tabla temporal que contenga un número de fila único usado para identificar las instrucciones individuales:
  
```
CREATE TABLE #tbl 
WITH 
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

En segundo lugar, inicialice las variables necesarias para realizar el bucle:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ahora, recorra las instrucciones ejecutándolas de una en una:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente, elimine la tabla temporal creada en el primer paso

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0224_2016-->