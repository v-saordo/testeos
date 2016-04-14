<properties
   pageTitle="Diseño de tablas en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para diseñar las tablas en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
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

# Diseño de tablas en el Almacenamiento de datos SQL #
El Almacenamiento de datos SQL es un sistema de base de datos distribuidas de procesamiento masivo en paralelo (MPP). Almacena datos en diferentes ubicaciones conocidas como **distribuciones**. Cada **distribución** es como un depósito; almacenar un único subconjunto de datos en el almacenamiento de datos. Al distribuir los datos y la capacidad de procesamiento entre varios nodos, el Almacenamiento de datos SQL puede ofrecer una gran escalabilidad, mucha más que un único sistema.

Cuando se crea una tabla en el Almacenamiento de datos SQL, en realidad se propaga a todas las distribuciones.

En este artículo se tratarán los siguientes temas:

- Tipos de datos admitidos
- Principios de la distribución de datos
- Distribución round robin
- Distribución hash
- Partición de tabla
- Estadísticas
- Características no admitidas

## Tipos de datos admitidos
Almacenamiento de datos SQL admite los tipos de datos empresariales comunes:

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **time**
- **tinyint**
- **varbinary**
- **varchar**

Puede identificar columnas en el almacenamiento de datos que contienen tipos incompatibles mediante la consulta siguiente:

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

La consulta incluye los tipos de datos definidos por el usuario, que no se admiten.

a continuación se muestran algunas alternativas que puede utilizar en vez de los tipos de datos no admitidos.

En lugar de:

- **geometry**, use un tipo varbinary
- **geography**, use un tipo varbinary
- **hierarchyid**, tipo CLR no nativo
- **image**, **text**, **ntext** cuando se usa varchar/nvarchar basado en texto (cuanto menos, mejor)
- **nvarchar (max)**, use varchar (4000) o más pequeño para un mejor rendimiento
- **numeric**, use decimal
- **sql\_variant**, divida la columna en varias columnas fuertemente tipadas
- **sysname**, use nvarchar(128)
- **table**, convierta en tablas temporales
- **timestamp**, vuelva a procesar el código para que use datetime2 y la función `CURRENT_TIMESTAMP`. Tenga en cuenta que no puede tener current\_timestamp como restricción DEFAULT y el valor no se actualizará automáticamente. Si tiene que migrar valores rowversion de una columna con tipo timestamp, use BINARY(8) o VARBINARY(8) para valores de versión de fila NOT NULL o NULL.
- **varchar (max)**, use varchar(8000) o más pequeño para un mejor rendimiento
- **uniqueidentifier**, use varbinary(8)
- **tipos definidos por el usuario**, vuelva a convertirlos a sus tipos nativos siempre que sea posible
- **xml**, use un varchar(8000) o más pequeño para un mejor rendimiento; dividir en columnas si es necesario

Compatibilidad parcial:

- Las restricciones DEFAULT solo admiten literales y constantes. No se admiten funciones ni expresiones no deterministas, tales como `GETDATE()` o `CURRENT_TIMESTAMP`.

> [AZURE.NOTE]Defina las tablas para que el tamaño máximo posible de fila, incluida la longitud total de columnas de longitud variable, no supere los 32.767 bytes. Aunque puede definir una fila con datos de longitud variable que superen esta cifra, no podrá insertar datos en la tabla. Además, intente limitar el tamaño de las columnas de longitud variable para un rendimiento incluso mejor para ejecutar consultas.

## Principios de la distribución de datos

Hay dos opciones para distribuir los datos en el Almacenamiento de datos SQL:

1. Distribuir los datos de manera uniforme, pero aleatoria 
2. Distribuir datos en función de los valores de hash de una sola columna

La distribución de datos se decide a nivel de tabla. Todas las tablas se distribuyen. Asignará la distribución a cada tabla de la base de datos de Almacenamiento de datos SQL.

La primera opción se conoce como distribución **round robin**; a veces se conoce como el hash aleatorio. Puede pensar en esto como la opción segura predeterminada o errónea.

La segunda opción se conoce como la distribución **hash**. Puede considerarla una forma optimizada de distribución de datos. Es preferible cuando los clústeres de tablas comparten criterios comunes de combinación o agregación.

## Distribución round robin

La distribución round robin es un método de propagación de datos lo más uniforme posible entre todas las distribuciones. Los búferes que contienen filas de datos se asignan a su vez (de ahí el nombre round robin) a cada distribución. El proceso se repite hasta que se han asignado todos los búferes de datos. En ningún momento los datos se ordenan o clasifican en una tabla con distribución round robin. Una distribución round robin a veces se denomina hash aleatorio por este motivo. Los datos se propagan lo más uniformemente posible por todas las distribuciones.

A continuación se muestra un ejemplo de tabla con distribución round robin:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

También se trata de un ejemplo de tabla con distribución round robin:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
)
;
```

> [AZURE.NOTE]Observe que el segundo ejemplo anterior no hace mención alguna a la clave de distribución. Round robin es el valor predeterminado, por lo que no es estrictamente necesario. Sin embargo, ser explícito se considera una buena práctica, ya que garantiza que sus colegas son conscientes de sus intenciones al revisar el diseño de tabla.

Este tipo de tabla se utiliza normalmente cuando no hay ninguna columna de clave obvia para aplicar un algoritmo hash de datos. También se puede utilizar en tablas más pequeñas o menos importantes cuando el coste de la transferencia no pueda ser tan alto.

Cargar los datos en una tabla con distribución round robin tiende a ser una tarea más rápida que cargar en una tabla con distribución hash. Con una tabla con distribución round robin, no es necesario conocer los datos ni ejecutar el algoritmo hash antes de realizar la carga. Por este motivo, las tablas round robin suelen tener objetivos de carga adecuados.

> [AZURE.NOTE]Si lo datos presentan una distribución round robin, estos se asignan a la distribución a nivel de *búfer*.

### Recomendaciones

Considere la opción de usar la distribución round robin para la tabla en los siguientes casos:

- Cuando no hay ninguna clave de combinación obvia.
- Si no se conoce una clave de distribución de hash de candidatos.
- Si la tabla no comparte una clave común de combinación con otras tablas.
- Si la combinación es menos importante que otras combinaciones de la consulta.
- Cuando la tabla es una tabla de carga inicial.

## Distribución hash

La distribución hash utiliza una función interna para distribuir un conjunto de datos en las distribuciones aplicando un algoritmo hash de una sola columna. Cuando a los datos se les aplica un algoritmo hash, no hay ningún orden explícito asignado a una distribución. Sin embargo, el propio algoritmo hash es un proceso determinante. Esto permite que se puedan prever los resultados del algoritmo hash. Por ejemplo, el algoritmo hash de una columna de tipo entero que contiene el valor 10 siempre dará el mismo valor hash. Esto significa que ***cualquier*** columna de enteros con algoritmos hash que contenga el valor 10 terminaría por asignarse a la misma distribución. Esto se aplica incluso en las tablas.

La capacidad de predicción del algoritmo hash es muy importante. Esto significa que el hash que distribuye los datos puede generar mejoras de rendimiento al leer datos y combinar tablas.

Como verá a continuación, la distribución de hash puede ser muy eficaz para la optimización de consultas. Por ello se considera una forma optimizada para distribuir los datos.

> [AZURE.NOTE]¡No lo olvide! El valor hash no se basa en el valor de los datos, sino en el tipo de datos a los que se aplica un algoritmo hash.

A continuación se muestra una tabla que ha distribuido ProductKey.

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE]Si lo datos presentan una distribución hash, estos se asignan a la distribución a nivel de fila.

## Particiones de tabla
Las particiones de tabla son compatibles y fáciles de definir.

Ejemplos del comando `CREATE TABLE` con particiones del Almacenamiento de datos SQL:

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Observe que no hay ningún esquema ni función de partición en la definición. Todo lo que se tiene en cuenta cuando se crea la tabla. Todo lo que debe hacer es identificar los puntos límite de la columna que va a ser la clave de partición.

## Estadísticas

El Almacenamiento de datos SQL usa un optimizador de consultas distribuidas para crear el plan de consulta adecuado cuando los usuarios consultan las tablas. Una vez creado, el plan de consulta proporciona la estrategia y el método que la base de datos utiliza para tener acceso a los datos y satisfacer la solicitud del usuario. El optimizador de consultas del Almacenamiento de datos SQL se basa en el coste. En otras palabras compara diversas opciones (planes) en función de su coste relativo y selecciona el plan más eficaz disponible. Por lo tanto, el Almacenamiento de datos SQL necesita mucha información para tomar decisiones informadas basadas en el coste. Contiene información estadística acerca de la tabla (para el tamaño de la tabla) y en los objetos de base de datos conocidos como `STATISTICS`.

Las estadísticas se alojan en una o varias columnas de índices o tablas. Ofrecen el optimizador basado en costes con información importante sobre la cardinalidad y selectividad de valores. Esto es de especial interés cuando el optimizador debe evaluar las cláusulas JOIN, GROUP BY, HAVING y WHERE en una consulta. Por lo tanto, es muy importante que la información contenida en estos objetos estadísticos refleje *con precisión* el estado actual de la tabla. Es fundamental saber que lo importante es la precisión del coste. Si las estadísticas reflejan con precisión el estado de la tabla, los plantes pueden compararse en función del coste más bajo. Si no son precisas, el Almacenamiento de datos SQL puede elegir el plan incorrecto.

Las estadísticas de nivel de columna del Almacenamiento de datos SQL las define el usuario.

En otras palabras, tenemos que crearlas nosotros mismos. Como acabamos de ver, no es algo que podamos ignorar. Se trata de una diferencia importante entre SQL Server y el Almacenamiento de datos SQL. SQL Server creará automáticamente las estadísticas cuando se consultan las columnas. De forma predeterminada, SQL Server también actualizará automáticamente estas estadísticas. Sin embargo, en el almacenamiento de datos SQL las estadísticas deben crearse y administrarse manualmente.

### Recomendaciones

Se aplican las siguientes recomendaciones para la generación de estadísticas:

1. Crear estadísticas de columnas individuales en las columnas utilizadas en las cláusulas `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY` y `DISTINCT`.
2. Generar estadísticas de varias columnas en las cláusulas compuestas.
3. Actualizar las estadísticas de forma periódica. Recuerde que esto no se realiza automáticamente.

>[AZURE.NOTE]Es habitual que el Almacenamiento de datos SQL dependa únicamente de `AUTOSTATS` para mantener actualizadas las estadísticas de columna. No es una práctica recomendada, incluso para los almacenamientos de datos SQL Server. `AUTOSTATS` se desencadenan a una velocidad de cambio del 20 % para las tablas de hechos grandes que contenían millones o miles de millones de filas que pueden no ser suficientes. Por lo tanto, siempre es conveniente mantener las actualizaciones de estadísticas como una prioridad para garantizar que las estadísticas reflejan con precisión la cardinalidad de la tabla.

## Características no admitidas
Almacenamiento de datos SQL no usa ni admite estas características:

- Claves principales
- Claves externas
- Restricciones CHECK
- Restricciones UNIQUE
- Índices únicos
- Columnas calculadas
- Columnas dispersas
- Tipos definidos por el usuario
- Vistas indizadas
- Identidades
- Secuencias
- Desencadenadores
- Sinónimos


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->