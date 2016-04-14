<properties 
    pageTitle="Glosario de las herramientas de Base de datos elástica | Microsoft Azure" 
    description="Explicación de los términos usados en las herramientas de bases de datos elásticas" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/01/2016" 
    ms.author="ddove;sidneyh"/>

# Glosario de las herramientas de bases de datos elásticas
Los siguientes términos se definen para las [herramientas de Base de datos elástica](sql-database-elastic-scale-introduction.md), una característica de Base de datos SQL de Azure. Las herramientas se usan para administrar [mapas de particiones](sql-database-elastic-scale-shard-map-management.md) e incluyen la [biblioteca de cliente](sql-database-elastic-database-client-library.md), la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md), los [grupos elásticos](sql-database-elastic-pool.md) y las [consultas](sql-database-elastic-query-overview.md).

Estos términos se usan en [Incorporación de una partición con herramientas de bases de datos elásticas](sql-database-elastic-scale-add-a-shard.md) y [Uso de la clase RecoveryManager para solucionar problemas de asignaciones de particiones](sql-database-elastic-database-recovery-manager.md).

![Términos de Escalado elástico][1]

**Base de datos**: una base de datos SQL de Azure.

**Enrutamiento dependiente de los datos**: la funcionalidad que permite que una aplicación se conecte a una partición dada una clave de partición específica. Consulte [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md). Comparar con **[Consulta a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md)**.

**Mapa de particiones global**: el mapa entre las claves de particionamiento y sus respectivas particiones dentro de un **conjunto de datos**. El mapa de particiones global se almacena en el **administrador de mapas de particiones**. Comparar con **mapa de particiones local**.

**Mapa de particiones de lista**: un mapa de particiones en el que las claves de particionamiento se asignan de manera individual. Comparar con **Mapa de particiones de intervalo**.

**Mapa de particiones local**: almacenado en una partición, el mapa de particiones local contiene asignaciones para los shardlets que residen en la partición.

**Consulta a través de particiones múltiples**: la capacidad de emitir una consulta a través de varias particiones; los conjuntos de resultados se devuelven usando la semántica UNION ALL (conocida también como "consulta de distribución ramificada"). Comparar con **enrutamiento dependiente de datos**.

**Mapa de particiones de intervalo**: un mapa de particiones en el que la estrategia de distribución de particiones se basa en varios intervalos de valores contiguos.

**Tablas de referencia**: tablas no particionadas, pero que se replican entre particiones. Por ejemplo, los códigos postales se pueden almacenar en una tabla de referencia.

**Partición**: una base de datos SQL de Azure que almacena datos desde un conjunto de datos particionados.

**Elasticidad de partición**: la posibilidad de realizar **escalado horizontal** y **escalado vertical**.

**Tablas particionadas**: tablas que se particionan, es decir, cuyos datos se distribuyen entre particiones según sus valores de clave de particionamiento.

**Clave de particionamiento**: un valor de columna que determina cómo se distribuyen los datos entre particiones. El tipo de valor puede ser uno de los siguientes: **int**, **bigint**, **varbinary** o **uniqueidentifier**.

**Conjunto de particiones**: la recopilación de particiones que se atribuyen al mismo mapa de particiones en el administrador de mapa de particiones.

**Shardlet**: todos los datos asociados con un valor único de una clave de particionamiento en una partición. Un shardlet es la unidad de desplazamiento de datos más pequeña posible cuando se distribuyen tablas particionadas.

**Mapa de particiones**: el conjunto de asignaciones entre las claves de particionamiento y sus respectivas particiones.

**Administrador de mapas de particiones**: un almacén de datos y objetos de administración que contiene los mapas de particiones, ubicaciones de particiones y asignaciones para uno o más conjuntos de particiones.

![Asignaciones][2]


##Verbos

**Escalado horizontal**: el acto de escalar (o reducir) horizontalmente una recopilación de particiones agregando o quitando particiones de un mapa de particiones.

![Escalado horizontal y vertical][3]

**Combinar**: el acto de mover shardlets desde dos particiones a una partición y actualizar el mapa de particiones como corresponda.

**Desplazamiento de shardlets**: el acto ve mover un shardlet a una partición distinta.

**Particionar**: el acto de particionar de manera horizontal datos estructurados de manera idéntica entre varias bases de datos según una clave de particionamiento.

**Dividir**: el acto de mover varios shardlets desde una partición a otra (normalmente nueva). El usuario proporciona una clave de particionamiento como el punto de división.

**Escalado vertical**: el acto de escalar (o reducir) verticalmente el nivel de rendimiento de una partición individual. Por ejemplo, cambiando una partición de Standard a Premium (lo que genera más recursos informáticos).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png
 

<!---HONumber=AcomDC_0211_2016-->