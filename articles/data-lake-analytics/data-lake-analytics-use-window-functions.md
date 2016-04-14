<properties 
   pageTitle="Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake | Azure" 
   description="Aprenda a usar las funciones de ventana de U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="jgao"/>


# Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake  

Las funciones de ventana se introdujeron en el estándar SQL de ISO/ANSI en 2003. U-SQL adopta un subconjunto de las funciones de ventana según lo definido en el estándar SQL de ANSI.

Las funciones de ventana se usan para realizar cálculos dentro de conjuntos de filas denominados *ventanas*. Las ventanas se definen mediante la cláusula OVER. Las funciones de ventana resuelven algunos escenarios clave de manera muy eficaz.

En esta guía de aprendizaje o tutorial, se usan dos conjuntos de datos de ejemplo para guiarlo por escenarios de ejemplo donde puede aplicar funciones de ventana. Para obtener más información, consulte [Referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

Las funciones de ventana se dividen en las siguientes categorías:

- [Funciones de agregación de informes](#reporting-aggregation-functions), como SUM o AVG
- [Funciones de categoría](#ranking-functions), por ejemplo, DENSE\_RANK, ROW\_NUMBER, NTILE y RANK
- [Funciones analíticas](#analytic-functions), como distribución acumulativa, percentiles o acceso a los datos de una fila anterior en el mismo conjunto de resultados sin usar una autocombinación

**Requisitos previos:**

- Realice estos dos tutoriales:

    - [Introducción al uso de Azure Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
    - [Tutorial: Introducción al uso de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Cree una cuenta de Análisis de Data Lake como se indica en la [introducción al uso de Azure Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Cree un proyecto de U-SQL de Visual Studio como se indica en [Tutorial: Introducción al uso de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).

## Conjuntos de datos de ejemplo

En este tutorial, se usan dos conjuntos de datos:

- QueryLog 

    QueryLog representa una lista de lo que se buscó en el motor de búsqueda. Cada registro de consultas incluye:
    
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
    
    Copie y pegue el script siguiente en el proyecto de U-SQL para construir el conjunto de filas de QueryLog:
    
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
    
    En la práctica, lo más probable es que los datos se almacenen en un archivo de datos. Para acceder a los datos en un archivo delimitado por tabulaciones, usaría el siguiente código:
    
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- Employees

    El conjunto de datos Employees incluye los campos siguientes:
   
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    Copie y pegue el script siguiente en el proyecto de U-SQL para construir el conjunto de filas de Employees:

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
    
    La instrucción siguiente muestra cómo crear el conjunto de filas mediante la extracción de un archivo de datos.
    
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

Cuando pruebe los ejemplos en el tutorial, debe incluir las definiciones de conjuntos de filas. U-SQL requiere que se definan solamente los conjuntos de filas que se usan. Algunos ejemplos solo necesitan un conjunto de filas.

También debe agregar la siguiente instrucción para que el conjunto de filas de resultados se incluya en un archivo de datos:

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();
 
 La mayoría de los ejemplos usan la variable llamada **@result** para los resultados.

## Comparación de las funciones de ventana con la agrupación

Las ventanas y la agrupación están relacionadas conceptualmente, pero son diferentes. Resulta útil comprender esta relación.

### Uso de agregación y agrupación

En la consulta siguiente, se usa una agregación para calcular el salario total de todos los empleados:

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;
    
>[AZURE.NOTE] Para obtener instrucciones para probar y comprobar los resultados, consulte [Tutorial: Introducción al uso de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).

El resultado es una sola fila y una sola columna. 165000 USD es la suma del valor de Salary de toda la tabla.

|TotalSalary
|-----------
|165000

>[AZURE.NOTE] Si desconoce las funciones de ventana, resulta útil recordar los números en los resultados.

La instrucción siguiente usa la cláusula GROUP BY para calcular el salario total para cada departamento:

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Los resultados son:

|DeptName|SalaryByDept
|--------|------------
|Engineering|60000
|HR|30000
|Ejecutivo|50000
|Marketing|25000

La suma de la columna SalaryByDept es 165000 USD, que coincide con la cifra en el último script.
 
En ambos casos, hay menos filas de salida que de entrada:
 
- Sin GROUP BY, la agregación contrae todas las filas en una sola. 
- Con GROUP BY, hay N filas de salida, donde N es el número de valores distintos que aparecen en los datos; en este caso, obtendrá 4 filas en la salida.

###  Uso de una función de ventana

La cláusula OVER en el ejemplo siguiente está vacía. Esto define la "ventana" para que incluya todas las filas. La suma (SUM) en este ejemplo se aplica a la cláusula OVER a la que precede.

Se podría interpretar esta consulta como la suma del salario en una ventana de todas las filas.

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

A diferencia de GROUP BY, hay tantas filas de salida como de entrada:

|EmpName|TotalAllDepts
|-------|--------------------
|Noah|165000
|Sophia|165000
|Liam|165000
|Emma|165000
|Jacob|165000
|Olivia|165000
|Mason|165000
|Ava|165000
|Ethan|165000


El valor 165000 (el total de todos los salarios) se coloca en cada fila de salida. Ese total proviene de la "ventana" de todas las filas, por lo que incluye todos los salarios.

En el siguiente ejemplo, se muestra cómo refinar la "ventana" para obtener una lista de todos los empleados, el departamento y el salario total para el departamento. Se agrega PARTITION BY a la cláusula OVER.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Los resultados son:

|EmpName|DeptName|SalaryByDep
|-------|--------|-------------------
|Noah|Engineering|60000
|Sophia|Engineering|60000
|Liam|Engineering|60000
|Mason|Ejecutivo|50000
|Emma|HR|30000
|Jacob|HR|30000
|Olivia|HR|30000
|Ava|Marketing|25000
|Ethan|Marketing|25000

Una vez más, hay el mismo número de filas de entrada que de salida. Sin embargo, cada fila tiene un salario total para el departamento correspondiente.




## Funciones de agregación de informes

Las funciones de ventana también admiten los agregados siguientes:

- COUNT
- SUM
- MÍN
- MÁX
- MEDIA
- STDEV
- VAR

La sintaxis es:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Nota:

- De forma predeterminada, las funciones de agregación, excepto COUNT, pasan por alto los valores nulos.
- Cuando se especifican funciones de agregado junto con la cláusula OVER, no se permite la cláusula ORDER BY en la cláusula OVER.

### Uso de SUM

En el ejemplo siguiente se agrega un salario total por departamento a cada fila de entrada:
 
    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Este es el resultado:

|EmpID|EmpName|DeptName|DeptID|Salary|TotalByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Engineering|100|10000|60000
|2|Sophia|Engineering|100|20000|60000
|3|Liam|Engineering|100|30000|60000
|7|Mason|Ejecutivo|300|50000|50000
|4|Emma|HR|200|10000|30000
|5|Jacob|HR|200|10000|30000
|6|Olivia|HR|200|10000|30000
|8|Ava|Marketing|400|15000|25000
|9|Ethan|Marketing|400|10000|25000

### Uso de COUNT

En el ejemplo siguiente se agrega un campo adicional a cada fila para mostrar el número total de empleados de cada departamento.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

El resultado es:

|EmpID|EmpName|DeptName|DeptID|Salary|CountByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Engineering|100|10000|3
|2|Sophia|Engineering|100|20000|3
|3|Liam|Engineering|100|30000|3
|7|Mason|Ejecutivo|300|50000|1
|4|Emma|HR|200|10000|3
|5|Jacob|HR|200|10000|3
|6|Olivia|HR|200|10000|3
|8|Ava|Marketing|400|15000|2
|9|Ethan|Marketing|400|10000|2


### Uso de MIN y MAX

En el ejemplo siguiente se agrega un campo adicional a cada fila para mostrar el salario más bajo de cada departamento.

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Los resultados son:

|EmpID|EmpName|DeptName|DeptID|Salary|MinSalary
|-----|-------|--------|------|-------------|----------------
|1|Noah|Engineering|100|10000|10000
|2|Sophia|Engineering|100|20000|10000
|3|Liam|Engineering|100|30000|10000
|7|Mason|Ejecutivo|300|50000|50000
|4|Emma|HR|200|10000|10000
|5|Jacob|HR|200|10000|10000
|6|Olivia|HR|200|10000|10000
|8|Ava|Marketing|400|15000|10000
|9|Ethan|Marketing|400|10000|10000

Reemplace MIN con MAX y pruébelo.


## Funciones de categoría

Las funciones de categoría devuelven un valor de categoría (long) para cada fila de cada partición definida mediante las cláusulas PARTITION BY y OVER. El orden de categorías se controla con la cláusula ORDER BY en la cláusula OVER.

Se admiten estas funciones de categoría:

- RANK
- DENSE\_RANK 
- NTILE
- ROW\_NUMBER

**Sintaxis:**

	[ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
	    OVER (
	        [PARTITION BY <identifier, > …[n]]
	        [ORDER BY <identifier, > …[n] [ASC|DESC]] 
	) AS <alias>

- La cláusula ORDER BY es opcional para las funciones de categoría. Si se especifica ORDER BY, entonces determina el orden de las categorías. Si no se especifica ORDER BY, U-SQL asigna valores según el orden que lea en el registro. El resultado es un valor no determinista de número de fila, categoría o categoría densa si no se especificó la cláusula ORDER BY.
- NTILE requiere una expresión que se evalúe como entero positivo. Este número especifica el número de grupos en que se debe dividir cada partición. Este identificador se usa solo con la función de categoría NTILE. 

Para obtener más detalles sobre la cláusula OVER, consulte la [referencia sobre el lenguaje U-SQL]().

ROW\_NUMBER, RANK y DENSE\_RANK asignan números a las filas de una ventana. En lugar de tratarlas por separado, es más intuitivo ver cómo responden a la misma entrada.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;
        
Tenga en cuenta que las cláusulas OVER son idénticas. El resultado es:

|Consultar|Latency:int|Vertical|RowNumber|Rank|DenseRank
|-----|-----------|--------|--------------|---------|--------------
|Banana|300|Imagen|1|1|1
|Cherry|300|Imagen|2|1|1
|Durian|500|Imagen|3|3|2
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|3|2|2
|Fig|300|Web|4|4|3
|Cherry|400|Web|5|5|4
|Durian|500|Web|6|6|5

### ROW\_NUMBER

Dentro de cada ventana (Vertical, Image o Web), el número de fila aumenta en 1 según el orden de latencia.

![Función de ventana ROW\_NUMBER de U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK

A diferencia de ROW\_NUMBER (), RANK() tiene en cuenta el valor de Latency que se especifica en la cláusula ORDER BY para la ventana.

RANK comienza con (1,1,3) porque los dos primeros valores de Latency son iguales. A continuación, el siguiente valor es 3, porque el valor de Latency cambió a 500. El aspecto clave es que, aunque se proporcionen valores duplicados en la misma categoría, el número de RANK "saltará" al siguiente valor de ROW\_NUMBER. Puede ver la repetición de este patrón con la secuencia (2,2,4) en la vertical Web.

![Función de ventana RANK de U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE\_RANK
	
DENSE\_RANK es igual que RANK salvo que no "salta" al siguiente ROW\_NUMBER, sino que pasa al siguiente número de la secuencia. Observe las secuencias (1,1,2) y (2,2,3) en el ejemplo.

![Función de ventana DENSE\_RANK de U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### Comentarios

- Si no se especifica ORDER BY, se aplicará la función de categoría al conjunto de filas sin ningún orden. Esto dará como resultado un comportamiento no determinista en la aplicación de la función de categoría.
- No hay ninguna garantía de que las filas devueltas por una consulta con ROW\_NUMBER adopten exactamente el mismo orden con cada ejecución, a menos que las condiciones siguientes sean verdaderas.

	- Los valores de la columna particionada son únicos.
	- Los valores de las columnas ORDER BY son únicos.
	- Las combinaciones de valores de la columna particionada y las columnas ORDER BY son únicas.

### NTILE

NTILE distribuye las filas de una partición ordenada entre un número especificado de grupos. Los grupos se numeran comenzando por uno.


En el ejemplo siguiente se divide el conjunto de filas de cada partición (vertical) en 4 grupos en el orden de latencia de la consulta y se devuelve el número de grupo para cada fila.

La vertical Image tiene 3 filas y, por lo tanto, 3 grupos.

La vertical Web tiene 6 filas; las dos filas adicionales se distribuyen entre los dos primeros grupos. Esta es la razón de que haya 2 filas en los grupos 1 y 2, y solo 1 fila en los grupos 3 y 4.

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;
		
Los resultados son:

|Consultar|Latency|Vertical|Quartile
|-----|-----------|--------|-------------
|Banana|300|Imagen|1
|Cherry|300|Imagen|2
|Durian|500|Imagen|3
|Apple|100|Web|1
|Fig|200|Web|1
|Papaya|200|Web|2
|Fig|300|Web|2
|Cherry|400|Web|3
|Durian|500|Web|4

NTILE toma un parámetro ("numgroups"). Numgroups es una expresión constante int o long positiva que especifica el número de grupos en los que se debe dividir cada partición.

- Si el número de filas de la partición es divisible por numgroups, los grupos tendrán el mismo tamaño. 
- Si el número de filas de una partición no es divisible por numgroups, los grupos tendrán dos tamaños que diferirán en un miembro. Los grupos de mayor tamaño preceden a los más pequeños en el orden especificado por la cláusula OVER. 

Por ejemplo:

- 100 filas se dividen en 4 grupos: [25, 25, 25, 25]
- 102 filas se dividen en 4 grupos: [26, 26, 25, 25]


### N registros principales por partición mediante RANK, DENSE\_RANK o ROW\_NUMBER

Muchos usuarios desean seleccionar solamente las n filas principales por grupo. Esto no es posible con la cláusula GROUP BY tradicional.

Vio el ejemplo siguiente al principio de la sección de funciones de categoría. No muestra los N registros principales para cada partición:

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Los resultados son:

|Consultar|Latency|Vertical|Rank|DenseRank|RowNumber
|-----|-----------|--------|---------|--------------|--------------
|Banana|300|Imagen|1|1|1
|Cherry|300|Imagen|1|1|2
|Durian|500|Imagen|3|2|3
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|2|2|3
|Fig|300|Web|4|3|4
|Cherry|400|Web|5|4|5
|Durian|500|Web|6|5|6

### N principales con DENSE RANK

El ejemplo siguiente devuelve los 3 registros principales de cada grupo sin interrupciones en la numeración secuencial de categoría de las filas de cada partición de la ventana.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Los resultados son:

|Consultar|Latency|Vertical|DenseRank
|-----|-----------|--------|--------------
|Banana|300|Imagen|1
|Cherry|300|Imagen|1
|Durian|500|Imagen|2
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2
|Fig|300|Web|3

### N principales con RANK

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Los resultados son:

|Consultar|Latency|Vertical|Rank
|-----|-----------|--------|---------
|Banana|300|Imagen|1
|Cherry|300|Imagen|1
|Durian|500|Imagen|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2


### N principales con ROW\_NUMBER

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Los resultados son:
    
|Consultar|Latency|Vertical|RowNumber
|-----|-----------|--------|--------------
|Banana|300|Imagen|1
|Cherry|300|Imagen|2
|Durian|500|Imagen|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|3

### Asignación de un número de fila único global

Suele resultar útil asignar un número único global a cada fila. Esto es fácil (y más eficaz que usar un reductor) con las funciones de categoría.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## Funciones analíticas

Las funciones analíticas se usan para comprender las distribuciones de valores en ventanas. El escenario más común en el que se usan funciones analíticas es el cálculo de percentiles.

**Funciones de ventana analíticas admitidas**

- CUME\_DIST 
- PERCENT\_RANK
- PERCENTILE\_CONT
- PERCENTILE\_DISC

### CUME\_DIST  

CUME\_DIST calcula la posición relativa de un valor especificado en un grupo de valores. Calcula el porcentaje de consultas que tienen una latencia inferior o igual a la latencia de la consulta actual en la misma vertical. Para una fila R, suponiendo que el orden sea ascendente, la CUME\_DIST de R es el número de filas con valores inferiores o iguales al valor de R, dividido por el número de filas que se evalúan en el conjunto de resultados de la consulta o de la partición. CUME\_DIST devuelve números en el intervalo 0 < x < = 1.

** Sintaxis**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

En el ejemplo siguiente se usa la función CUME\_DIST para calcular el percentil de latencia para cada consulta en una vertical.

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Los resultados son:
    
|Consultar|Latency|Vertical|CumeDist
|-----|-----------|--------|---------------
|Durian|500|Imagen|1
|Banana|300|Imagen|0\.666666666666667
|Cherry|300|Imagen|0\.666666666666667
|Durian|500|Web|1
|Cherry|400|Web|0\.833333333333333
|Fig|300|Web|0\.666666666666667
|Fig|200|Web|0,5
|Papaya|200|Web|0,5
|Apple|100|Web|0\.166666666666667

Hay 6 filas en la partición donde la clave de partición es "Web" (de la cuarta fila en adelante):

- Hay 6 filas con un valor inferior o igual a 500, por lo que la CUME\_DIST es igual a 6/6 = 1.
- Hay 5 filas con un valor inferior o igual a 400, por lo que la CUME\_DIST es igual a 5/6 = 0.83.
- Hay 4 filas con un valor inferior o igual a 300, por lo que la CUME\_DIST es igual a 4/6 = 0.66.
- Hay 3 filas con un valor inferior o igual a 200, por lo que la CUME\_DIST es igual a 3/6 = 0.5. Hay dos filas con el mismo valor de latencia.
- Hay 1 fila con un valor inferior o igual a 100, por lo que la CUME\_DIST es igual a 1/6 = 0.16. 


**Notas de uso:**

- Los valores empatados siempre se evalúan como el mismo valor de la distribución acumulativa.
- Los valores NULL se tratan como los valores más bajos posibles.
- Debe especificar la cláusula ORDER BY para calcular CUME\_DIST.
- CUME\_DIST es similar a la función PERCENT\_RANK.

Nota: no se permite la cláusula ORDER BY si la instrucción SELECT no va seguida de OUTPUT. Por lo tanto, la cláusula ORDER BY en la instrucción OUTPUT determina el orden de visualización del conjunto de filas resultante.


### PERCENT\_RANK

PERCENT\_RANK calcula el orden relativo de una fila dentro de un grupo de filas. PERCENT\_RANK se usa para evaluar la importancia relativa de un valor dentro de un conjunto de filas o una partición. El intervalo de valores devueltos por PERCENT\_RANK es mayor que 0 y menor o igual que 1. A diferencia de CUME\_DIST, PERCENT\_RANK es siempre 0 para la primera fila.
	
** Sintaxis**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Notas**

- La primera fila de cualquier conjunto tiene un PERCENT\_RANK 0.
- Los valores NULL se tratan como los valores más bajos posibles.
- Debe especificar la cláusula ORDER BY para calcular PERCENT\_RANK.
- CUME\_DIST es similar a la función PERCENT\_RANK. 


En el ejemplo siguiente se usa la función PERCENT\_RANK para calcular el percentil de latencia para cada consulta en una vertical.

La cláusula PARTITION BY se especifica para particionar las filas en el conjunto de resultados por la vertical. La cláusula ORDER BY en la cláusula OVER ordena las filas de cada partición.

El valor devuelto por la función PERCENT\_RANK representa el orden de latencia de las consultas en una vertical como porcentaje.


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Los resultados son:

|Consultar|Latency:int|Vertical|PercentRank
|-----|-----------|--------|------------------
|Banana|300|Imagen|0
|Cherry|300|Imagen|0
|Durian|500|Imagen|1
|Apple|100|Web|0
|Fig|200|Web|0,2
|Papaya|200|Web|0,2
|Fig|300|Web|0\.6
|Cherry|400|Web|0\.8
|Durian|500|Web|1

### PERCENTILE\_CONT y PERCENTILE\_DISC

Estas dos funciones calculan un percentil basándose en una distribución continua o discreta de los valores de columna.

**Sintaxis**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric\_literal**: el percentil que se va a calcular. El valor debe estar entre 0.0 y 1.0.

WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ]): especifica una lista de valores numéricos para ordenarlos y calcular el percentil a partir de ellos. Solamente se permite un identificador de columna. La expresión debe evaluarse como un tipo numérico. No se permiten otros tipos de datos. El criterio de ordenación predeterminado es ascendente.

OVER ([ PARTITION BY <identifier,>…[n] ] ): divide el conjunto de filas de entrada en particiones según la clave de partición a la que se aplica la función de percentil. Para obtener más información, consulte la sección de este documento sobre funciones de categoría. Nota: se pasan por alto los valores NULL del conjunto de datos.

**PERCENTILE\_CONT** calcula un percentil basándose en una distribución continua del valor de columna. El resultado se interpola y es posible que no coincida con ninguno de los valores específicos de la columna.

**PERCENTILE\_DISC** calcula el percentil basándose en una distribución discreta de los valores de columna. El resultado es igual a un valor específico de la columna. Es decir, PERCENTILE\_DISC, en contraste con PERCENTILE\_CONT, siempre devuelve un valor real (entrada original).

Puede ver cómo funcionan ambas en el ejemplo siguiente, que intenta encontrar la mediana (percentil = 0,50) para Latency en cada vertical.

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
        
        FROM @querylog;

Los resultados son:

|Consultar|Latency:int|Vertical|PercentileCont50|PercentilDisc50
|-----|-----------|--------|-------------------|----------------
|Banana|300|Imagen|300|300
|Cherry|300|Imagen|300|300
|Durian|500|Imagen|300|300
|Apple|100|Web|250|200
|Fig|200|Web|250|200
|Papaya|200|Web|250|200
|Fig|300|Web|250|200
|Cherry|400|Web|250|200
|Durian|500|Web|250|200


Para PERCENTILE\_CONT, dado que los valores se pueden interpolar, la mediana para Web es 250 aunque no haya ninguna consulta en la vertical Web con una latencia de 250.

PERCENTILE\_DISC no interpola valores, por lo que la mediana de Web es 200, que es un valor que se encuentra en las filas de entrada.











## Consulte también

- [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Introducción a Análisis de Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Uso de tutoriales interactivos de Análisis de Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
- [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md)
- [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake.](data-lake-analytics-u-sql-get-started.md)
- [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md)
- [Administración de Análisis de Azure Data Lake mediante Azure Powershell](data-lake-analytics-manage-use-powershell.md)
- [Supervisión y solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0218_2016-->