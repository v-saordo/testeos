<properties 
   pageTitle="Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio | Azure" 
   description="Aprenda a instalar Data Lake Tools for Visual Studio, y a desarrollar y probar scripts U-SQL." 
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
   ms.date="01/04/2016"
   ms.author="jgao"/>

# Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake

U-SQL es un lenguaje que unifica las ventajas de SQL con la potencia expresiva de su propio código para procesar todos los datos a cualquier escala. La funcionalidad de consulta distribuida escalable de U-SQL permite analizar de forma eficaz los datos en el almacén y en almacenes relacionales como Base de datos SQL de Azure. Permite procesar datos no estructurados mediante la aplicación del esquema al leer, insertar lógica personalizada y UDF, y además incluye extensibilidad para habilitar el control específico sobre la ejecución a escala. Para obtener más información acerca de la filosofía de diseño de U-SQL, consulte esta [entrada de blog de Visual Studio](http://blogs.msdn.com/b/visualstudio/archive/2015/09/28/introducing-u-sql.aspx).

Existen algunas diferencias con respecto a ANSI SQL o T-SQL. Por ejemplo, sus palabras clave, como SELECT, deben ir en MAYÚSCULA.

Su sistema de tipos y lenguaje de expresión dentro de cláusulas Select, predicados Where, etc. están en C#. Esto significa que los tipos de datos son los de C# y usan la semántica de NULL de C#; además, las operaciones de comparación dentro de un predicado siguen la sintaxis de C# (por ejemplo, a == "foo"). Esto supone también que los valores son objetos .NET completos, lo que le permite usar fácilmente cualquier método para operar en el objeto (por ejemplo, "f o o o". Split(' ')).

Para obtener más información, consulte [Referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###Requisitos previos

Debe completar el [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

En el tutorial, ejecutó un trabajo de Análisis de Data Lake con el siguiente script SQL U:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Este script no contiene ningún paso de transformación. Lee el archivo de origen denominado **SearchLog.tsv**, lo esquematiza y genera el conjunto de filas en un archivo denominado **SearchLog-from-adltools.csv**.

Observe el signo de interrogación junto al tipo de datos del campo Duration. Esto significa que el campo Duration podría ser nulo.

Algunos conceptos y palabras clave que se encuentran en el script:

- **Variables de conjunto de filas**: cada expresión de consulta que produce un conjunto de filas se puede asignar a una variable. U-SQL sigue el patrón de nomenclatura de variables de T-SQL; por ejemplo, **@searchlog** en el script. Tenga en cuenta que la asignación no fuerza la ejecución. Simplemente da nombre a la expresión y ofrece la posibilidad de crear expresiones más complejas.
- **EXTRACT** proporciona la capacidad de definir un esquema al leer. El esquema se especifica mediante un par de nombre de columna y nombre de tipo de C# por columna. Usa lo que se conoce como **extractor**, por ejemplo, **Extractors.Tsv()**, para extraer los archivos tsv. Puede desarrollar extractores personalizados.
- **OUTPUT** toma un conjunto de filas y lo serializa. Outputters.Csv() genera un archivo delimitado por comas en la ubicación especificada. También puede desarrollar a outputters personalizados.
- Observe que las dos rutas de acceso son relativas. También puede usar rutas de acceso absolutas. Por ejemplo: 
    
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar una ruta de acceso absoluta para acceder a los archivos en las cuentas de almacenamiento vinculadas. La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Los contenedores de blobs de Azure con permisos de acceso de contenedores públicos o blobs públicos no se admiten actualmente.

## Uso de variables escalares

Puede usar variables escalares para facilitar el mantenimiento del script. El anterior script U-SQL también se puede escribir de la forma siguiente:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";
    
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();
    
    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();
      
## Transformación de conjuntos de filas

Use **SELECT** para transformar conjuntos de filas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La cláusula WHERE usa una [expresión booleana de C#](https://msdn.microsoft.com/library/6a71f45d.aspx). Puede usar el lenguaje de expresiones de C# para crear sus propias expresiones y funciones. Incluso puede realizar un filtrado más complejo si las combina con conjunciones lógicas (and) y disyunciones (or).

El siguiente script usa el método DateTime.Parse() y una conjunción.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");
    
    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();
        
Observe que la segunda consulta opera con el resultado del primer conjunto de filas y por tanto el resultado es una composición de ambos filtros. También puede volver a usar un nombre de variable y los nombres tienen un ámbito léxico.

## Conjuntos de filas agregados

U-SQL proporciona elementos conocidos como **ORDER BY**, **GROUP BY** y las agregaciones.

La siguiente consulta busca la duración total por región y después genera las 5 duraciones principales.

Los conjuntos de filas de U-SQL no conservan el orden en la siguiente consulta. Por lo tanto, para ordenar los resultados, necesita agregar ORDER BY a la instrucción OUTPUT tal y como se muestra a continuación:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";
    
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;
    
    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;
    
    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2 
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
        
La cláusula ORDER BY de U-SQL se debe combinar con la cláusula FETCH en una expresión SELECT.

La cláusula HAVING de U-SQL puede usarse para restringir los resultados a los grupos que cumplan la condición HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## Combinación de datos

U-SQL proporciona operadores de combinación habituales como INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN o SEMI JOIN para combinar no solo tablas, sino también conjuntos de filas (incluso los producidos a partir de archivos).

El script siguiente combina searchlog con un registro de impresión de anuncios y proporciona los anuncios para la cadena de consulta de una fecha determinada.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();
    
    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s 
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;
    
    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL solamente admite la sintaxis de combinación compatible con ANSI: conjuntoDeFilas1 JOIN conjuntoDeFilas2 ON predicado. NO se admite la sintaxis antigua FROM conjuntoDeFilas1, conjuntoDeFilas2 WHERE predicado. El predicado en una JOIN debe ser una combinación de igualdad y no una expresión. Si desea usar una expresión, agréguela a la cláusula SELECT de un conjunto de filas anterior. Si desea realizar una comparación diferente, puede moverla a la cláusula WHERE.

        
## Creación de bases de datos, funciones con valores de tabla, vistas y tablas

U-SQL permite usar datos en el contexto de una base de datos y un esquema, por lo que no siempre tiene que leer archivos o escribir en ellos.

Cada script U-SQL se ejecuta con una base de datos predeterminada (maestra) y el esquema predeterminado (dbo) como su contexto predeterminado. Puede crear una base de datos o un esquema propios. Para cambiar de contexto, use la instrucción **USE** para cambiarlo.


### Creación de una función con valores de tabla (TVF)

En el anterior script U-SQL, repitió el uso de EXTRACT para leer el mismo archivo de origen. La función con valores de tabla de U-SQL permite encapsular los datos para volver a usarlos más adelante.

El siguiente script crea una función TVF llamada *Searchlog()* en la base de datos y el esquema predeterminados:

    DROP FUNCTION IF EXISTS Searchlog;
    
    CREATE FUNCTION Searchlog() 
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN 
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;
    
El siguiente script muestra cómo usar la TVF definida en el anterior script:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
        
### Creación de vistas

Si solo tiene una expresión de consulta que desea hacer abstracta pero no parametrizar, puede crear una vista en lugar de una función con valores de tabla.

El siguiente script crea una vista llamada *SearchlogView* en la base de datos y el esquema predeterminados:

    DROP VIEW IF EXISTS SearchlogView;
    
    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    
El siguiente script muestra el uso de la vista definida:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### Cree las tablas. 

De forma semejante a una tabla de base de datos relacional, U-SQL permite crear una tabla con un esquema predefinido o crear una tabla e inferir el esquema a partir de la consulta que rellena la tabla (lo que también se conoce como CREATE TABLE AS SELECT o CTAS).

El siguiente script crea una base de datos y dos tablas:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;
    
    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;
    
    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,
    
                INDEX sl_idx CLUSTERED (UserId ASC) 
                    PARTITIONED BY HASH (UserId)
    );
    
    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;
    
    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC) 
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### Consulta de tablas

Puede consultar las tablas (creadas en el script anterior) de la misma forma que se consultan los archivos de datos. En lugar de crear un conjunto de filas mediante EXTRACT, ahora se puede hacer referencia solo al nombre de la tabla.

El script de transformación que usó antes se modifica para que lea las tablas:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;
    
    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;
    
    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Observe que actualmente no puede ejecutar una instrucción SELECT en una tabla en el mismo script que aquel donde creó esa tabla.


##Conclusión

Lo que se trata en el tutorial es solo una pequeña parte de U-SQL. Debido al alcance de este tutorial, no se explica todo, como:

- Uso de CROSS APPLY para desempaquetar partes de cadenas, matrices y mapas en filas.
- Operación de conjuntos de datos particionados (conjuntos de archivos y tablas con particiones).
- Desarrollo de operadores definidos por el usuario como extractores, outputters, procesadores o agregadores definidos por el usuario en C#.
- Uso de las funciones de ventana de U-SQL.
- Administración del código de U-SQL con vistas, funciones con valores de tabla y procedimientos almacenados.
- Ejecución de código personalizado arbitrario en sus nodos de procesamiento. 
- Conexión a bases de datos SQL de Azure y federación de consultas entre ellas y sus datos de U-SQL y Azure Data Lake.

## Consulte también 

- [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-use-window-functions.md)
- [Supervisión y solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## Díganos su opinión

- [Sugiera un nuevo trabajo pendiente de documentación](data-lake-analytics-documentation-backlog.md)
- [Envíe una solicitud de característica](http://aka.ms/adlafeedback)
- [Obtenga ayuda en los foros](http://aka.ms/adlaforums)
- [Proporcione comentarios sobre U-SQL](http://aka.ms/usqldiscuss)

<!---HONumber=AcomDC_0107_2016-->