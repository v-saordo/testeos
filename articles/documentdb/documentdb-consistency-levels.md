<properties
	pageTitle="Niveles de coherencia en DocumentDB | Microsoft Azure"
	description="DocumentDB tiene cuatro niveles de coherencia con niveles de rendimiento asociados para ayudar a equilibrar las ventajas finales de coherencia, disponibilidad y latencia."
	keywords="eventual consistency, documentdb, azure, Microsoft azure"
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mimig"/>

# Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB

Los desarrolladores con frecuencia se enfrentan al desafío de elegir entre los dos extremos de una coherencia alta y una coherencia ocasional. Lo cierto es que hay varios puntos intermedios entre estos dos extremos de la coherencia. En la mayoría de los escenarios reales, las aplicaciones se benefician de la realización de equilibrios muy ajustados entre la coherencia, la disponibilidad y la latencia. DocumentDB ofrece cuatro niveles de coherencia bien definidos con niveles de rendimiento asociados. Esto permite a los desarrolladores de aplicaciones realizar equilibrios predecibles entre la coherencia, la disponibilidad y la latencia.

Todos los recursos del sistema, como cuentas de base de datos, bases de datos, colecciones, usuarios y permisos son siempre absolutamente coherentes para lecturas y consultas. Los niveles de coherencia se aplican únicamente a los recursos definidos por el usuario. En lo relativo a consultas y operaciones de lectura sobre recursos definidos por el usuario, como documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF, DocumentDB ofrece cuatro niveles distintos de coherencia:

 - Fuerte coherencia
 - Coherencia de uso vinculado
 - Coherencia de la sesión
 - Coherencia final

Estos niveles de coherencia granulares y bien definidos le permiten realizar sólidos equilibrios entre la coherencia, la disponibilidad y el rendimiento. Estos niveles de coherencia están respaldados por niveles de rendimiento predecibles que garantizan unos resultados coherentes para su aplicación.

## Niveles de coherencia para bases de datos

Puede configurar el nivel de coherencia predeterminado en la cuenta de la base de datos que se aplica a todas las colecciones (en todas las bases de datos) de su cuenta de base de datos. De manera predeterminada todas las lecturas y consultas enviadas a los recursos definidos por el usuario utilizarán el nivel de coherencia predeterminado especificado en la cuenta de la base de datos. Sin embargo, puede reducir el nivel de coherencia de una solicitud de lectura o consulta concreta mediante la especificación del encabezado de solicitud [x-ms-consistency-level]. Los cuatro tipos de niveles de coherencia admitidos por el protocolo de replicación de DocumentDB se describen brevemente a continuación.

>[AZURE.NOTE] En una versión futura se admitirá el reemplazo del nivel de coherencia predeterminado en cada colección individual.

**Alta**: la coherencia alta garantiza que una escritura solo es visible después de confirmarse de forma duradera por la mayoría del cuórum de réplicas. Una escritura se confirma sincrónicamente de forma duradera tanto por el principal y por el cuórum de secundarios o se anula. Una lectura siempre se confirma por la mayoría del cuórum de lectura: el cliente nunca ve una escritura no confirmada o parcial y siempre se garantiza la lectura de la última escritura reconocida.

La coherencia alta proporciona garantías absolutas con relación a la coherencia de datos, pero ofrece el nivel más bajo de rendimiento de lectura y escritura.

**Uso vinculado**: el uso vinculado garantiza el orden total de propagación de las escrituras con la posibilidad de que las lecturas se queden atrás con respecto a las escrituras en al menos K prefijos. La lectura siempre se confirma por cuórums de réplicas mayoritarios. La respuesta de una consulta de lectura especifica su relativa actualización (en términos de K). Con el uso vinculado puede establecer un umbral configurable de uso (como prefijos u hora) para lecturas de latencia de equilibrio y coherencia en estado estable.

La coherencia de uso vinculado proporciona un comportamiento más predecible para la coherencia de lectura mientras ofrece las escrituras de latencia más bajas. Como las lecturas se confirman por un cuórum mayoritario, la latencia de lecturas no es la más baja ofrecida por el sistema. El uso vinculado es una opción para escenarios donde se busca una gran homogeneidad aun no resultando práctica. Si configura el "intervalo de uso" para que la coherencia del uso vinculado sea arbitrariamente grande, aún se mantendrá el orden total global de las escrituras. Esto proporciona una garantía más segura que Sesión u Ocasional.

>[AZURE.NOTE] El uso vinculado garantiza lecturas monotónicas solo en las solicitudes de lectura explícitas. La respuesta de eco reflejada para las solicitudes de escritura no ofrece garantías de uso vinculado.

**Sesión**: a diferencia de los modelos de coherencia global ofrecidos por los niveles de coherencia Alta y De uso vinculado, la coherencia de "sesión" está pensada para una sesión de cliente específica. La coherencia de sesión suele ser suficiente ya que proporciona las escrituras y lecturas monotónicas garantizadas, así como la capacidad de leer sus propias escrituras. Se envía a una réplica una solicitud de lectura para la coherencia de sesión, que puede atender a la versión solicitada del cliente (parte de la cookie de sesión).

La coherencia de sesión proporciona una coherencia de datos de lectura predecibles para una sesión, mientras ofrece escrituras de latencia más bajas. Las lecturas también son de latencia baja ya que, excepto en casos raros, una única réplica atenderá a las lecturas.

**Eventual**: la coherencia ocasional es la forma más débil de coherencia, ya que con el tiempo un cliente puede obtener los valores que son más antiguos que los que ha visto antes. En ausencia de escrituras adicionales, las réplicas dentro del grupo convergirán finalmente. Los índices secundarios atienden a las solicitudes de lecturas.

La coherencia ocasional proporciona la coherencia de lectura más débil, pero ofrece la latencia más baja tanto para lecturas como para escrituras.

### Cambio del nivel de coherencia de la base de datos

1.  En la barra de accesos directos del [Portal de Azure](https://portal.azure.com/), haga clic en **Cuentas de DocumentDB**.

2. En la hoja **Cuentas de DocumentDB**, seleccione la cuenta de base de datos que se va a modificar.

3. En la hoja de cuenta, si la hoja **Configuración** no está abierta, haga clic en el icono **Configuración** de la barra de herramientas superior.

4. En la hoja **Toda la configuración**, haga clic en la entrada **Coherencia predeterminada** en **Característica**.

	![Captura de pantalla que muestra el icono Configuración y la entrada Coherencia predeterminada](./media/documentdb-consistency-levels/database-consistency-level-1.png)

5. En la hoja **Coherencia predeterminada**, seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.

	![Captura de pantalla que muestra el nivel de coherencia y el botón Guardar](./media/documentdb-consistency-levels/database-consistency-level-2.png)

## Niveles de coherencia para consultas

De manera predeterminada, para los recursos definidos por el usuario, el nivel de coherencia de las consultas es igual al de las lecturas. De forma predeterminada, el índice se actualiza de forma sincrónica con cada inserción, reemplazo o eliminación de un documento de la colección. Esto permite a las consultas respetar el mismo nivel de coherencia que el de las lecturas de documentos. Aunque DocumentDB está optimizada para escrituras y admite volúmenes sostenidos de escrituras de documentos junto con un mantenimiento sincrónico de índices y un servicio de consultas coherentes, se pueden configurar determinadas colecciones para actualizar el índice de manera diferida. La indización diferida incrementa aún más el rendimiento de las escrituras y es ideal para aquellos escenarios de ingesta en bloque en donde la carga de trabajo sea sobre todo de lecturas.

Modo de indexación|	Lecturas|	Consultas  
-------------|-------|---------
Coherente (predeterminado)|	Seleccionar entre Alta, De uso vinculado, Sesión y Ocasional|	Seleccionar entre Alta, De uso vinculado, Sesión y Ocasional|
Diferida|	Seleccionar entre Alta, De uso vinculado, Sesión y Ocasional|	Ocasional  

Al igual que con las solicitudes de lecturas, puede disminuir el nivel de coherencia de una solicitud de consulta concreta mediante la especificación del encabezado de solicitud [x-ms-consistency-level].

## Pasos siguientes

Si desea leer más sobre los niveles de coherencia y los compromisos, recomendamos los siguientes recursos:

-	Doug Terry. Explicación de la coherencia de datos replicados a través del béisbol. [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Garantías de sesión para datos replicados con coherencia débil. [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi. Compromisos de coherencia en el diseño de sistemas modernos de bases de datos distribuidas: CAP es solo una parte de la historia". [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Uso vinculado probabilístico (PBS) para cuórums parciales prácticos. [http://vldb.org/pvldb/vol5/p776\_peterbailis\_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Coherencia ocasional - Revisión. [http://allthingsdistributed.com/2008/12/eventually\_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)

<!---HONumber=AcomDC_0302_2016-->