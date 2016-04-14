<properties
   pageTitle="La distribución hash y su efecto sobre el rendimiento de las consultas en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre las tablas hash distribuidas y cómo afectan al rendimiento de las consultas en Almacenamiento de datos SQL para el desarrollo de soluciones."
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
   ms.date="02/09/2016"
   ms.author="jrj;barbkess;sonyama"/>

# La distribución hash y su efecto sobre el rendimiento de las consultas en Almacenamiento de datos SQL

Tomar decisiones inteligentes sobre la distribución hash es una de las maneras más importantes de mejorar el rendimiento de las consultas.

De hecho, hay tres factores principales:

1. Reducir el movimiento de los datos
2. Evitar el sesgado de los datos
3. Proporcionar una ejecución equilibrada

## Reducir el movimiento de los datos
El movimiento de los datos surge normalmente cuando se unen tablas o se realizan agregaciones en tablas. La distribución hash de tablas en una clave compartida es uno de los métodos más efectivos de reducir este movimiento.

Sin embargo, para que la distribución hash sea eficaz a la hora de reducir el movimiento, se deben cumplir todos estos criterios:

1. Ambas tablas deben tener distribución hash y estar combinadas en la misma clave de distribución compartida
2. Los tipos de datos de ambas columnas deben coincidir
3. Las columnas de combinación deben usar el operador «=» (es decir, los valores de la columna de la tabla izquierda deben ser iguales a los valores de la columna de la tabla derecha)
4. La combinación **no** es un `CROSS JOIN`

> [AZURE.NOTE] Las columnas usadas en las cláusulas `JOIN`, `GROUP BY`, `DISTINCT` y `HAVING` son todas buenas candidatas a columnas HASH. Por otra parte, las columnas de la cláusula `WHERE` **no** son buenas candidatas a columnas hash. Vea a continuación la sección sobre la ejecución equilibrada.

El movimiento de los datos también puede surgir de la sintaxis de consulta (las cláusulas `COUNT DISTINCT` y `OVER` son magníficos ejemplos) cuando se usa con columnas que no incluyen la clave de distribución hash.

> [AZURE.NOTE] Las tablas Round-robin generan normalmente movimiento de los datos. Los datos de la tabla se han asignado de forma no determinista y, por lo tanto, se deben mover primero antes de completar la mayoría de las consultas.

## Evitar el sesgado de los datos
Para que la distribución hash sea eficaz es importante que la columna elegida presente las siguientes propiedades:

1. La columna contiene un número significativo de valores distintos.
2. La columna no presenta **sesgado de datos**.

Cada valor distinto se asignará a una distribución. Por consiguiente, los datos necesitarán un número razonable de valores distintos para garantizar que se generan suficientes valores hash únicos. En caso contrario, podríamos obtener un hash de baja calidad. Si, por ejemplo, el número de distribuciones supera el número de valores distintos, algunas distribuciones se dejarán vacías. Esto podría afectar negativamente al rendimiento.

Igualmente, si todas las filas de la columna con hash contienen el mismo valor, se dice que los datos están **sesgados**. En este caso extremo, solo se habría creado un valor hash, dando como resultado que todas las filas acabasen dentro de una única distribución. Idealmente, cada valor distinto de la columna con hash tendría el mismo número de filas.

> [AZURE.NOTE] Las tablas Round-robin no presentan signos de sesgado. Esto se debe a que los datos se almacenan uniformemente en todas las distribuciones.

## Proporcionar una ejecución equilibrada
La ejecución equilibrada se logra cuando cada distribución tiene la misma cantidad de trabajo que realizar. El Procesamiento masivo en paralelo (Massively Parallel Processing , MPP) es un juego de equipo; todos tienen que cruzar la línea antes de que cualquiera pueda declararse ganador. Si todas las distribuciones tienen la misma cantidad de trabajo (es decir, datos para procesar), entonces todas las consultas finalizarán mas o menos al mismo tiempo. Esto se conoce como ejecución equilibrada.

Como se ha visto, el sesgado de los datos puede afectar a la ejecución equilibrada. Sin embargo, puede hacerlo también la elección de la clave de distribución hash. Si se ha elegido una columna que aparece en la cláusula `WHERE` de una consulta, es bastante probable que la consulta no se equilibre.

> [AZURE.NOTE] La cláusula `WHERE` normalmente ayuda a identificar las columnas que se usan mejor para la creación de particiones.

Un buen ejemplo de una columna que aparece en la cláusula `WHERE` sería un campo de fecha. Los campos de fecha son ejemplos clásicos de columnas buenas para la creación de particiones, pero a menudo malas como columnas de distribución hash. Normalmente, las consultas de almacenamiento de datos tienen lugar durante un período de tiempo especificado, como un día, una semana o un mes. La distribución hash por fecha puede limitar de hecho la escalabilidad y afectar negativamente al rendimiento. Si, por ejemplo, el intervalo de fecha especificado era una semana, es decir, siete días, el número máximo de hashes sería de siete, uno por cada día. Esto significa que solo siete de nuestras distribuciones contendrían datos. El resto de distribuciones no tendría ningún dato. Esto daría lugar a una ejecución de consulta desequilibrada ya que solo siete distribuciones procesan datos.

> [AZURE.NOTE] Las tablas Round-robin suelen proporcionan una ejecución equilibrada. Esto se debe a que los datos se almacenan uniformemente en todas las distribuciones.

## Recomendaciones
Para maximizar el rendimiento y la capacidad de proceso global de las consultas, intente y asegúrese de que las tablas con distribución hash sigan este patrón lo más posible:

Clave de distribución hash:

1. Es un valor estático, ya que no se puede actualizar la columna de hash. 
2. Se usa en las cláusulas `JOIN`, `GROUP BY`, `DISTINCT` o `HAVING` de las consultas.
2. No se usa en las cláusulas `WHERE`
3. Tiene muchos valores diferentes, al menos 1000.
4. No tiene un número de filas desproporcionadamente grande que aplicarán hash a un número más pequeño de distribuciones.
5. Está definido como NOT NULL. Las filas NULL se agruparán en una distribución.

## Resumen

La distribución hash se puede resumir como sigue:

- La función hash es determinista. El mismo valor siempre se asigna a la misma distribución.
- Una columna se usa como columna de distribución. La función hash usa la columna designada para calcular las asignaciones de fila a distribuciones.
- La función hash se basa en el tipo de la columna no en los propios valores
- La distribución hash de una tabla puede dar lugar algunas veces a una tabla sesgada
- Las tablas con distribución hash requieren generalmente menor movimiento de los datos al resolver consultas y, por lo tanto, mejoran el rendimiento de las consultas en tablas de hechos de gran tamaño.
- Observe las recomendaciones para la selección de columnas con distribución hash para mejorar el rendimiento de las consultas.

> [AZURE.NOTE] En Almacenamiento de datos SQL, la coherencia de los datos importa. Asegúrese de que el esquema existente sea coherente usando el mismo tipo para una columna. Esto es especialmente importante para la clave de distribución. Si los tipos de datos de la clave de distribución no están sincronizados y las tablas se combinan, se producirá un movimiento innecesario de los datos. Esto podría resultar costoso si las tablas son grandes, y daría lugar a una capacidad de proceso y un rendimiento menores.


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0211_2016-->