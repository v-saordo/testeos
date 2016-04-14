<properties
	pageTitle="Información general sobre la prueba comparativa Base de datos SQL de Azure"
	description="Este tema describe la prueba comparativa Base de datos SQL de Azure Benchmark, que se usa para la medición del rendimiento de la Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/15/2015"
	ms.author="jroth" />

# Información general sobre la prueba comparativa Base de datos SQL de Azure

## Información general
La Base de datos SQL de Microsoft Azure ofrece tres [niveles de servicio](sql-database-service-tiers.md) con varios niveles de rendimiento. Cada nivel de rendimiento proporciona un conjunto de recursos (o "capacidad") cada vez mayor, diseñado para proporcionar un mayor rendimiento.

Es importante poder cuantificar cómo se traduce la potencia creciente de cada nivel de rendimiento en un rendimiento de base de datos mejorado. Para ello, Microsoft ha desarrollado la prueba comparativa Base de datos SQL de Azure Benchmark (ASDB). La prueba comparativa ejerce una combinación de operaciones básicas que se encuentran en todas las cargas de trabajo OLTP. Medimos el rendimiento conseguido para bases de datos que se ejecuten en cada nivel de rendimiento.

Los recursos y la potencia de cada nivel de servicio y nivel de rendimiento se expresan en términos de [unidades de rendimiento de base de datos (DTU)](sql-database-technical-overview.md#understand-dtus). Las DTU proporcionan un modo de describir la capacidad relativa de un nivel de rendimiento basándose en una medición combinada de CPU, memoria y velocidades de lectura y escritura ofrecidas por cada nivel de rendimiento. Doblar la tasa de DTU de una base de datos es equiparable a doblar la potencia de la base de datos. La prueba comparativa nos permite evaluar el impacto en el rendimiento de la base de datos de la cada vez mayor capacidad que ofrece cada nivel de rendimiento realizando operaciones reales en la base de datos, mientras se escala el tamaño de la base de datos, el número de usuarios y las velocidades de transacción en proporción a los recursos proporcionados a la base de datos.

Al expresar el rendimiento del nivel de servicio Básico mediante el uso de transacciones por hora, el nivel de servicio Estándar mediante el uso de transacciones por minuto y el nivel de servicio Premium mediante el uso de transacciones por segundo, se facilita la rápida correlación del potencial rendimiento de cada nivel de servicio con los requisitos de una aplicación.

## Correlación de los resultados de la prueba comparativa con el rendimiento real de la base de datos
Es importante comprender que el ASDB, al igual que todas las pruebas comparativas, solo es representativo e indicativo. Las velocidades de transacción logradas con la aplicación de la prueba comparativa no serán iguales que las que se podrían lograr con otras aplicaciones. La prueba comparativa comprende un conjunto de diferentes tipos de transacción ejecutados en un esquema que contiene una variedad de tipos de datos y tablas. Si bien la prueba comparativa ejerce las mismas operaciones básicas que son comunes para todas las cargas de trabajo OLTP, no representa ninguna clase específica de base de datos o aplicación. El objetivo de la prueba comparativa es proporcionar una orientación razonable del rendimiento relativo de una base de datos que se puede esperar al aumentar o reducir el nivel de rendimiento. En realidad, las bases de datos son de distintos tamaños y complejidad, tienen distintas combinaciones de cargas de trabajo y responden de maneras diferentes. Por ejemplo, una aplicación que haga un uso intensivo de ES podría alcanzar antes el umbral de ES, o una que haga un uso intensivo de la CPU podría alcanzar antes los límites de CPU. No se garantiza que una base de datos concreta se escale de la misma manera que la prueba comparativa bajo una carga creciente.

La prueba comparativa y su metodología se describen a continuación de forma más detallada.

## Resumen de la prueba comparativa
El ASDB mide el rendimiento de una combinación de operaciones de bases de datos básicas que se producen con mayor frecuencia en las cargas de trabajo de procesamiento de transacciones en línea (OLTP). Aunque la prueba comparativa está diseñada teniendo en cuenta la computación en la nube, el esquema de la base de datos, el rellenado de datos y las transacciones se diseñaron para representar ampliamente los elementos básicos usados con mayor frecuencia en las cargas de trabajo OLTP.

## Esquema
El esquema se ha diseñado para que presente una variedad y complejidad suficientes como para permitir una amplia gama de operaciones. La prueba comparativa se ejecuta en una base de datos formada por seis tablas. Las tablas pertenecen a tres categorías: de tamaño fijo, de escalado y de crecimiento. Existen dos tablas de tamaño fijo, tres tablas de escalado y una tabla de crecimiento. Las tablas de tamaño fijo tienen un número de filas constante. Las tablas de escalado presentan una cardinalidad proporcional al rendimiento de la base de datos, pero no cambian durante la prueba comparativa. La tabla de crecimiento tiene un tamaño igual que la tabla de escalado en la carga inicial, pero después la cardinalidad cambia durante el transcurso de la prueba comparativa según se van insertando y eliminando filas.

El esquema incluye una combinación de tipos de datos que incluyen valores enteros, numéricos, caracteres y fecha/hora. El esquema incluye claves principales y secundarias, pero no claves externas; es decir, no hay restricciones de integridad referenciales entre las tablas.

Un programa de generación de datos genera los datos para la base de datos inicial. Los datos enteros y numéricos se generan con diversas estrategias. En algunos casos, los valores se distribuyen al azar a lo largo de un intervalo. En otros casos, se permuta al azar un conjunto de valores para asegurarse de que se mantiene una distribución específica. Los campos de texto se generan a partir de una lista ponderada de palabras para producir datos con aspecto real.

La base de datos se dimensiona basándose en un “factor de escala”. El factor de escala (abreviado SF) determina la cardinalidad de las tablas de escalado y de crecimiento. Como se describe a continuación en la sección Usuarios y velocidad, el tamaño de la base de datos, el número de usuarios y el rendimiento máximo se escalan de modo proporcional entre sí.

## Transacciones
La carga de trabajo consta de nueve tipos de transacciones, como se muestra en la tabla siguiente. Cada transacción se diseño para destacar un conjunto determinado de características del sistema en el motor de la base de datos y en el hardware del sistema, con un elevado contraste con respecto a las otras transacciones. Este enfoque facilita la evaluación del impacto de diferentes componentes sobre el rendimiento global. Por ejemplo, la transacción “Lectura intensa” produce un número significativo de operaciones de lectura de disco.

| Tipo de transacción | Descripción |
|---|---|
| Lectura ligera | SELECT; en memoria; solo lectura |
| Lectura mediana | SELECT; principalmente en memoria; solo lectura |
| Lectura intensa | SELECT; principalmente no en memoria; solo lectura |
| Actualización ligera | UPDATE; en memoria; solo escritura |
| Actualización intensa | UPDATE; principalmente no en memoria; solo escritura |
| Inserción ligera | INSERT; en memoria; solo escritura |
| Inserción intensa | INSERT; principalmente no en memoria; solo escritura |
| Eliminar | DELETE; combinación de en memoria y no en memoria; solo lectura |
| CPU intensa | SELECT; en memoria; carga en CPU relativamente intensa; solo lectura |

## Combinación de cargas de trabajo
Las transacciones se seleccionan aleatoriamente de una distribución ponderada con la siguiente combinación global. La combinación global presenta una relación de lectura/escritura aproximadamente de 2:1.

| Tipo de transacción | % de combinación |
|---|---|
| Lectura ligera | 35 |
| Lectura mediana | 20 | |
| Lectura intensa | 5 |
| Actualización ligera | 20 | |
| Actualización intensa | 3 |
| Inserción ligera | 3 |
| Inserción intensa | 2 |
| Eliminar | 2 |
| CPU intensa | 10 |

## Usuarios y velocidad
La carga de trabajo de la prueba comparativa está dirigida a partir de una herramienta que envía transacciones a través de un conjunto de conexiones para simular el comportamiento de numerosos usuarios simultáneos. Aunque todas las conexiones y transacciones son generadas a máquina, para simplificar nos referiremos a estas conexiones como “usuarios”. Aunque cada usuario opera independientemente de todos los demás usuarios, todos los usuarios realizan el mismo ciclo de pasos mostrado a continuación:

1. Establecer una conexión de base de datos.
2. Repetir hasta que se señale la salida:
	- Seleccionar una transacción aleatoriamente (a partir de una distribución ponderada).
	- Realizar la transacción seleccionada y medir el tiempo de respuesta.
	- Esperar un retraso de velocidad.
3. Cerrar la conexión de la base de datos.
4. Salir.

El retraso de velocidad (en el paso 2c) se selecciona aleatoriamente, pero con una distribución que tenga un promedio de 1,0 segundos. De este modo, cada usuario puede, en promedio, generar como máximo una transacción por segundo.

## Reglas de escalado
El número de usuarios viene determinado por el tamaño de la base de datos (en unidades de factor de escala). Hay un usuario por cada cinco unidades de factor de escala. Debido al retraso de velocidad, un usuario puede, en promedio, generar como máximo una transacción por segundo.

Por ejemplo, una base de datos que tenga un factor de escala 500 (SF=500) tendrá 100 usuarios y podrá alcanzar una velocidad máxima de 100 TPS. Para generar una velocidad de TPS mayor, son necesarios más usuarios y una base de datos mayor.

La tabla siguiente muestra el número de usuarios sostenidos realmente para cada nivel de servicio y nivel de rendimiento.

| Nivel de servicio (nivel de rendimiento) | Usuarios | Tamaño de base de datos |
|---|---|---|
| Básica | 5 | 720 MB |
| Estándar (S0) | 10 | 1 GB |
| Estándar (S1) | 20 | | 2,1 GB |
| Estándar (S2) | 50 | 7,1 GB |
| Premium (P1) | 100 | 14 GB |
| Premium (P2) | 200 | 28 GB |
| Premium (P6/P3) | 800 | 114 GB |

## Duración de la medición
Una ejecución válida de la prueba comparativa precisa una duración de medición en estado fijo de al menos una hora.

## Métricas
Las métricas clave de la prueba comparativa son rendimiento y tiempo de respuesta.

- El rendimiento es la medición de rendimiento esencial en la prueba comparativa. El rendimiento se indica en transacciones por unidad de tiempo, contando todos los tipos de transacciones.
- El tiempo de respuesta es una medición de la previsibilidad del rendimiento. La restricción del tiempo de respuesta varía con la clase de servicio, presentando las clases de servicio mayores un requisito de tiempo de respuesta más estricto, como se muestra a continuación.

| Clase de servicio | Medición del rendimiento | Requisito del tiempo de respuesta |
|---|---|---|
| Premium | Transacciones por segundo | Percentil 95 en 0,5 segundos |
| Standard | Transacciones por minuto | Percentil 90 en 1,0 segundo |
| Básica | Transacciones por hora | Percentil 80 en 2,0 segundos |

## Conclusión
La prueba comparativa Base de datos SQL de Azure Benchmark mide el rendimiento relativo de la Base de datos SQL de Azure que se ejecuta a lo largo del intervalo de niveles de servicio y niveles de rendimiento disponibles. La prueba comparativa ejerce una combinación de operaciones de bases de datos básicas que se producen con mayor frecuencia en las cargas de trabajo de procesamiento de transacciones en línea (OLTP). Mediante la medición del rendimiento real, la prueba comparativa proporciona una evaluación más significativa del impacto que tiene sobre el rendimiento el cambio del nivel de rendimiento de lo que sería posible con solo enumerando los recursos proporcionados por cada nivel, como velocidad de CPU, tamaño de memoria e IOPS. En el futuro, continuaremos evolucionando la prueba comparativa para ampliar su ámbito y los datos proporcionados.

## Recursos
[Introducción a Base de datos SQL](sql-database-technical-overview.md)

[Niveles de servicio y niveles de rendimiento](sql-database-service-tiers.md)

[Guía de rendimiento de Base de datos SQL de Azure para bases de datos únicas](sql-database-performance-guidance.md)

<!---HONumber=Oct15_HO3-->