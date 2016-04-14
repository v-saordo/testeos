<properties
    pageTitle="Información general de las características de las herramientas de Base de datos elástica | Microsoft Azure"
    description="Los desarrolladores de Software como servicio (SaaS) pueden crear bases de datos elásticas y escalables con facilidad en la nube mediante estas herramientas"
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

# Información general de las características de bases de datos elásticas

Las característica de la **base de datos elástica** le permiten usar los recursos prácticamente ilimitados de bases de datos de **Base de datos SQL de Azure** para crear soluciones para cargas de trabajo transaccionales y especialmente aplicaciones de Software como servicio (SaaS). Las características de la base de datos elástica se componen de lo siguiente:

* Herramientas de base de datos elástica: estas dos herramientas simplifican el desarrollo y la administración de soluciones de base de datos particionada. Las herramientas son: la [biblioteca de cliente de bases de datos elástica](sql-database-elastic-database-client-library.md) y la [herramienta de división y combinación de base de datos elástica](sql-database-elastic-scale-overview-split-and-merge.md). 
* [Grupos de base de datos elástica](sql-database-elastic-pool-guidance.md) (vista previa): un grupo es una colección de bases de datos a la que puede agregar o quitar bases de datos en cualquier momento. Las bases de datos del grupo comparten una cantidad fija de recursos (conocidos como unidades de transacción de base de datos o DTU). Se paga un precio fijo por los recursos, lo que le permite calcular con facilidad los costos al administrar el rendimiento. 
* [Trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md) (vista previa): use trabajos para administrar un gran número de bases de datos de SQL de Azure. Realice fácilmente operaciones administrativas, como cambios de esquema, administración de credenciales, actualizaciones de datos de referencia, recopilación de datos de rendimiento o de trabajos de recolección de telemetría de inquilinos (cliente).
* [Consulta de Base de datos elástica](sql-database-elastic-query-overview.md) (vista previa): le permite ejecutar una consulta de Transact-SQL que abarca varias bases de datos. Esto permite la conexión con herramientas de informes, como Excel, PowerBI, Tableau, etc.

El siguiente gráfico muestra una arquitectura que incluye las **características de base de datos elástica** en relación con una colección de bases de datos.

![Herramientas de Base de datos elástica][1]

Para obtener una versión imprimible de este gráfico, vaya a [Descarga de información general de la Base de datos elástica](http://aka.ms/axmybc).

En este gráfico, los colores de la base de datos representan esquemas. Las bases de datos con el mismo color comparten los mismos esquemas.

1. Un conjunto de **bases de datos de SQL de Azure** se hospedan en Azure con la arquitectura de particionamiento. 
2. La **biblioteca de cliente de base de datos elástica** se usa para administrar un conjunto de particiones.
3. Un subconjunto de las bases de datos se coloca en un **grupo de bases de datos elásticas**. (Consulte [Domine el crecimiento explosivo con bases de datos elásticas](sql-database-elastic-pool.md)). 
4. Un **trabajo de base de datos elástica** ejecuta scripts de T-SQL en todas las bases de datos.
5. La **herramienta de división y combinación** se usa para mover datos de una partición a otra.
6. La **consulta de base de datos elástica** le permite escribir una consulta que abarque todas las bases de datos del conjunto de particiones.
  
## Promesas y desafíos

Lograr la flexibilidad y escala para aplicaciones de nube ha sido sencillo para el cálculo y almacenamiento de blobs, basta con sumar o restar unidades. Pero sigue siendo un desafío para el procesamiento de datos con estado en bases de datos relacionales. Hemos visto que estos desafíos surgen principalmente en los dos escenarios siguientes:

* Aumento y disminución de la capacidad de la parte de la base de datos relacional de la carga de trabajo.
* La administración de las zonas activas puede afectar a un subconjunto específico de datos, como un usuario final (inquilino) particularmente ocupado.

Tradicionalmente, escenarios similares se han abordado a través de la inversión en servidores de base de datos a gran escala para admitir la aplicación. Sin embargo, esta opción está limitada en la nube donde todo el procesamiento ocurre en hardware estándar predefinido. En su lugar, la distribución de datos y el procesamiento a través de muchas bases de datos con una estructura idéntica (un patrón de escalado horizontal conocido como "particionamiento") proporciona una alternativa a los enfoques de escalado vertical tradicionales, tanto en términos de costo como de elasticidad.

## Escalado horizontal y vertical

En la siguiente ilustración se muestran las dimensiones horizontales y verticales de escalado, que son las formas básicas en que se pueden escalar las bases de datos elásticas.

![Escalado horizontal frente a vertical][2]

Escalar horizontalmente es agregar o quitar bases de datos con el fin de ajustar la capacidad o el rendimiento general. También se denomina "escalado horizontal". El particionamiento, en el que los datos se particionan en toda una recopilación de bases de datos con estructura idéntica, es un enfoque común para implementar el escalado horizontal.

Escalar verticalmente es aumentar o disminuir el nivel de rendimiento de una base individual; también se conoce como "escalado vertical".

La mayoría de las aplicaciones de bases de datos a escala de la nube usarán una combinación de estas dos estrategias. Por ejemplo, una aplicación de software como servicio podría utilizar escalado horizontal para aprovisionar usuarios finales nuevos y escalado vertical para permitir que la base de datos de cada cliente final aumente o disminuya los recursos, según lo requiera la carga de trabajo.

* El escalado horizontal se administra mediante la [biblioteca de cliente de bases de datos elásticas](sql-database-elastic-database-client-library.md).

* El escalado vertical se logra mediante cmdlets de Azure PowerShell para cambiar el nivel de servicio o colocando bases de datos en un grupo de bases de datos elásticas.

## Patrones de multiinquilino o inquilino único

*Particionamiento* es una técnica para distribuir grandes cantidades de datos estructurados de manera idéntica entre un número de bases de datos independientes. De uso muy extendido entre desarrolladores de nube que crean ofertas de software como servicio (SAAS) para empresas o clientes finales. Estos clientes finales a menudo se conocen como "inquilinos". El particionamiento puede ser necesario por diversos motivos:

* La cantidad total de datos es demasiado grande para caber dentro de las restricciones de una base de datos
* El rendimiento de transacciones de la carga de trabajo total supera la capacidad de una sola base de datos
* Los inquilinos pueden requerir el aislamiento físico entre sí, por lo que se necesitan bases de datos independientes para cada inquilino
* Distintas secciones de una base de datos pueden tener que residir en diferentes geografías por motivos de cumplimiento, rendimiento o geopolíticos.

En otros escenarios, como la recopilación de datos desde dispositivos distribuidos, el particionamiento se puede usar para llenar un conjunto de bases de datos organizadas de manera temporal. Por ejemplo, puede dedicarse una base de datos independiente a cada día o semana. En ese caso, la clave de particionamiento puede ser un entero que representa la fecha (presente en todas las filas de las tablas particionadas) y la aplicación debe enrutar las consultas que recuperan información de un intervalo de fechas al subconjunto de bases de datos que abarca el intervalo en cuestión.

El particionamiento funciona mejor cuando todas las transacciones de una aplicación pueden restringirse a un único valor de una clave de particionamiento. De este modo se garantiza que todas las transacciones sean locales con respecto a una base de datos.

Algunas aplicaciones usan el enfoque más simple de crear una base de datos independiente para cada inquilino. Se trata del **patrón de particionamiento de un solo inquilino** que proporciona aislamiento, capacidad de copia de seguridad y restauración, y ajuste de escala de recursos en la granularidad del inquilino. Con el particionamiento de un solo inquilino, cada base de datos se asocia a un determinado valor de identificador de inquilino (o valor de clave de cliente), pero no es necesario que esa clave esté siempre presente en los propios datos. Es responsabilidad de la aplicación enrutar cada solicitud a la base de datos adecuada y la biblioteca de cliente puede simplificar este proceso.

![Un solo inquilino frente a multiinquilinos][4]

Otros escenarios empaquetan varios inquilinos juntos en bases de datos, en lugar de aislarlos en bases de datos independientes. Se trata de un **patrón de particionamiento multiinquilinos** típico y puede estar impulsado por el hecho de que una aplicación administra grandes cantidades de inquilinos muy pequeños. En el particionamiento de varios inquilinos, las filas de las tablas de bases de datos están diseñadas para contener una clave que identifique la clave de particionamiento o el identificador del inquilino. De nuevo, la capa de aplicación es la responsable de enrutar la solicitud de un inquilino a la base de datos adecuada, y esto puede admitirlo la biblioteca de cliente de bases de datos elásticas. Además, es posible usar seguridad en el nivel de fila para filtrar las filas a las que puede tener acceso cada inquilino; si desea obtener detalles, consulte [Aplicaciones multiinquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md). La redistribución de datos entre las bases de datos puede ser necesaria con el patrón de particionamiento multiinquilinos, lo que se ve facilitado por la herramienta de división y combinación de bases de datos elásticas.

### Mover datos de bases de datos de multinIiquilino a inquilino único
Al crear una aplicación SaaS, es típico ofrecer a los clientes potenciales una versión de prueba del software. En este caso, resulta más rentable usar una base de datos multiinquilino para los datos. Sin embargo, cuando un cliente potencial se convierte en un cliente, una base de datos de inquilino único es mejor, puesto que ofrece un mejor rendimiento. Si el cliente había creado datos durante el período de prueba, use la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md) para mover los datos de la base de datos multiinquilino a la nueva base de datos de inquilino único.

## Pasos siguientes

Para una aplicación de ejemplo que demuestra la biblioteca de cliente, consulte [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).

Para usar la herramienta de división y combinación, debe [configurar la seguridad](sql-database-elastic-scale-split-merge-security-configuration.md).

Para ver los detalles del grupo de bases de datos elásticas, consulte [Consideraciones de precio y rendimiento para un grupo de servidores de bases de datos elásticas](sql-database-elastic-pool-guidance.md) o cree un nuevo grupo con el [tutorial](sql-database-elastic-pool-portal.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

### Queremos sus comentarios.
¿Qué podemos hacer mejor? ¿Explica este tema la característica claramente? ¿O se siente desconcertado por alguna de sus partes? Nuestro objetivo es satisfacer sus deseos, de modo que use los botones de votación y cuéntenos si lo hemos logrado o no. Y, si desea que pongamos en contacto con usted, incluya su correo electrónico en sus comentarios.


<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/tools.png
[2]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]: ./media/sql-database-elastic-scale-introduction/overview.png
[4]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

<!---HONumber=AcomDC_0302_2016-->