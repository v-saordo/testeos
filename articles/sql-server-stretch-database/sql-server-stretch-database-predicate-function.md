<properties
	pageTitle="Escritura de una función con valores de tabla insertada para seleccionar filas que migrar (Stretch Database) | Microsoft Azure"
	description="Aprenda a crear un predicado de filtro para seleccionar las filas que migrar."
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

# Escritura de una función con valores de tabla insertada para seleccionar filas que migrar (Stretch Database)

Si almacena los datos históricos en una tabla independiente, puede configurar Stretch Database para migrar toda la tabla. Si la tabla contiene datos históricos y actuales, por otra parte, puede especificar un predicado de filtro para seleccionar las filas que desea migrar. El predicado de filtro debe llamar a una función con valores de tabla insertada. Este tema describe cómo escribir una función con valores de tabla insertada para seleccionar filas que migrar.

En CTP 3.1 a través de RC0, la opción de especificar un predicado no está disponible en el asistente para habilitar una base de datos para Stretch. Tendrá que usar la instrucción ALTER TABLE para configurar Stretch Database con esta opción. Para obtener más información, vea [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

Si no especifica un predicado de filtro, se migra toda la tabla.

> [IMPORTANTE] Si proporciona un predicado de filtro que tiene un rendimiento insuficiente, la migración de datos también lo tendrá. Stretch Database aplica el predicado de filtro a la tabla con el operador CROSS APPLY.

## Requisitos básicos para la función con valores de tabla insertada
La función con valores de tabla insertada necesaria para una función de filtro de Stretch Database es similar al ejemplo siguiente.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE <predicate>
```
Los parámetros para la función deben tener identificadores para las columnas de la tabla.

La vinculación del esquema es necesaria para evitar que las columnas que utilizan el predicado de filtro se quite o altere.

### Valor devuelto
Si la función devuelve un resultado no vacío, la fila es elegible para migrarse; de lo contrario (es decir, si la función no devuelve ninguna fila), la fila no es elegible para la migración.

### Condiciones
El & lt;*predicado*& gt; puede constar de una condición o varias condiciones combinadas con el operador lógico AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
A su vez, cada condición puede constar de una condición primitiva o de varias condiciones primitivas combinadas con el operador lógico OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### Condiciones primitivas
Una condición primitiva puede realizar una de las siguientes comparaciones.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Compare un parámetro de función con una expresión constante. Por ejemplo: `@column1 < 1000`.

    Este es un ejemplo en el que se comprueba si el valor de una columna *date* es & lt; 1\\/1\\/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Aplique el operador IS NULL o IS NOT NULL a un parámetro de función.

-   Use el operador IN para comparar un parámetro de función con una lista de valores constantes.

    En este ejemplo se comprueba si el valor de una columna *shipment\_status* es `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

### Operadores de comparación
Se admiten los siguientes operadores de comparación.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### Expresiones constantes
Las constantes que se usan en un predicado de filtro pueden ser cualquier expresión determinista que se puede evaluarse cuando se defina la función. Las expresiones constantes pueden contener lo siguiente.

-   Literales. Por ejemplo: `N’abc’, 123`.

-   Expresiones algebraicas. Por ejemplo: `123 + 456`.

-   Funciones deterministas. Por ejemplo: `SQRT(900)`.

-   Conversiones deterministas que usen CAST o CONVERT. Por ejemplo: `CONVERT(datetime, '1/1/2016', 101)`.

### Otras expresiones
Puede usar los operadores BETWEEN y NOT BETWEEN si el predicado resultante se ajusta a las reglas descritas aquí después de reemplazar los operadores BETWEEN y NOT BETWEEN por las expresiones AND y OR equivalentes.

No puede usar subconsultas o funciones no deterministas como RAND() o GETDATE().

## Ejemplos de funciones válidas

-   El ejemplo siguiente combina dos condiciones primitivas con el operador lógico AND.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   En el ejemplo siguiente se usa una conversión determinista y varias condiciones con CONVERT.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   El ejemplo siguiente usa las funciones y operadores matemáticos.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   En el ejemplo siguiente se usan los operadores BETWEEN y NOT BETWEEN. Este uso es válido porque el predicado resultante se ajusta a las reglas descritas aquí después de reemplazar los operadores BETWEEN y NOT BETWEEN por las expresiones AND y OR equivalentes.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 BETWEEN 0 AND 100
    			AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    La función anterior es equivalente a la función siguiente después de reemplazar los operadores BETWEEN y NOT BETWEEN por expresiones AND y OR equivalentes.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## Ejemplos de funciones que no son válidas

-   La siguiente función no es válida porque contiene una conversión no determinista.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   La siguiente función no es válida porque contiene una llamada de función no determinista.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   La siguiente función no es válida porque contiene una subconsulta.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Las siguientes funciones no son válidas porque las expresiones que usan operadores algebraicos o funciones incorporadas deben evaluarse en una constante cuando define la función. No puede incluir referencias de columna en expresiones algebraicas o llamadas de función.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE SQRT(@column1) = 30
    GO
    ```

-   La siguiente función no es válida porque infringe las reglas descritas aquí después de reemplazar el operador BETWEEN por la expresión AND equivalente.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    La función anterior es equivalente a la función siguiente después de reemplazar el operador BETWEEN por a expresión AND equivalente. Esta función no es válida porque las condiciones primitivas solo pueden usar el operador lógico OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## Aplicación de Stretch Database al predicado de filtro
Stretch Database aplica el predicado de filtro a la tabla y determina filas elegibles mediante el operador CROSS APPLY. Por ejemplo:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Si la función devuelve un resultado no vacío para la fila, la fila es elegible para la migración.

## Adición de un predicado de filtro a una tabla
Agregue un predicado de filtro a una tabla mediante la ejecución de la instrucción ALTER TABLE y la especificación de una función con valores de tabla insertada existente como el valor del parámetro FILTER\_PREDICATE. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
) )
```
Después de vincular la función con la tabla como un predicado, se cumple lo siguiente:

-   La próxima vez que se produzca la migración de datos, solo se migrarán las filas para las que la función devuelve un valor no vacío.

-   Las columnas usadas por la función cuentan con una vinculación de esquema. No puede modificar estas columnas si la tabla usa la función como predicado de filtro.

## Eliminación de un predicado de filtro de una tabla
Para migrar toda la tabla en lugar de las filas seleccionadas, quite el FILTER\_PREDICATE existente estableciéndolo en nulo. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
Después de quitar el predicado de filtro, todas las filas de la tabla son elegibles para la migración.

## Reemplazo de un predicado de filtro existente
Puede reemplazar un predicado de filtro especificado anteriormente ejecutando de nuevo la instrucción ALTER TABLE y especificando un nuevo valor para el parámetro FILTER\_PREDICATE. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
```
La nueva función con valores de tabla insertada tiene los siguientes requisitos.

-   La nueva función tiene que ser menos restrictiva que la función anterior.

-   Todos los operadores que existían en la función antigua deben existir en la nueva función.

-   La nueva función no puede contener operadores que no existan en la función anterior.

-   No se puede cambiar el orden de los argumentos del operador.

-   Solo los valores constantes que forman parte de una comparación `<, <=, >, >=` pueden cambiarse de manera que el predicado sea menos restrictivo.

### Ejemplo de un reemplazo válido
Suponga que la función siguiente es el predicado de filtro actual.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
La siguiente función es un reemplazo válido porque la constante de fecha nueva (que especifica una fecha límite posterior) hace que el predicado sea menos restrictivo.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
			AND (@column2 < -50 OR @column2 > 50)
GO
```

### Ejemplos de reemplazos que no son válidos
La siguiente función no es un reemplazo válido porque la constante de fecha nueva (que especifica una fecha límite anterior) no hace que el predicado sea menos restrictivo.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
La siguiente función no es un reemplazo válido porque uno de los operadores de comparación se ha eliminado.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -50)
GO
```
La siguiente función no es un reemplazo válido porque se ha agregado una nueva condición con el operador lógico AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
			AND (@column2 <> 0)
GO
```

## Eliminación de un predicado de filtro
No puede quitar la función con valores de tabla insertada si la tabla está usando la función como predicado de filtro.

## Comprobación del predicado de filtro aplicado a una tabla
Para comprobar el predicado de filtro aplicado a una tabla, abra la vista de catálogo **sys.remote\_data\_archive\_tables** y compruebe el valor de la columna **filter\_predicate**. Si el valor es null, la tabla completa es elegible para el archivado. Para obtener más información, vea [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Consulte también
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0302_2016-->