<properties
   pageTitle="Ajuste de la agregación de datos y el rendimiento de consulta con Elasticsearch en Azure | Microsoft Azure"
   description="Un resumen de las consideraciones al optimizar el rendimiento de consultas y de búsquedas en Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="masimms"/>
   
# Ajuste de la agregación de datos y el rendimiento de consultas con Elasticsearch en Azure


Este artículo [forma parte de una serie](guidance-elasticsearch.md).

Un motivo principal para usar Elasticsearch es admitir búsquedas a través de los datos. Los usuarios deberían poder encontrar rápidamente la información que están buscando. Además, el sistema debe permitir a los usuarios formular preguntas de los datos, buscar correlaciones y llegar a conclusiones que puedan impulsar decisiones empresariales. Este procesamiento es lo que diferencia los datos de la información.

Este documento resume las opciones que puede tener en cuenta al determinar la mejor forma de optimizar el sistema para el rendimiento de las búsquedas y las consultas.

Todas las recomendaciones de rendimiento dependen en gran medida de los escenarios aplicables a su situación, del volumen de los datos que se van a indexar y la velocidad a la que las aplicaciones y los usuarios consultan los datos. Debe probar con cuidado los resultados de cualquier cambio en la configuración o en la estructura de indexación mediante sus propios datos y cargas de trabajo para evaluar las ventajas para sus escenarios específicos. Para ello, este documento también describe una serie de pruebas comparativas realizadas para un escenario específico que se implementa mediante diferentes configuraciones. Puede adaptar el enfoque tomado para evaluar el rendimiento de sus propios sistemas. Los detalles de estas pruebas se describen en el [apéndice](#appendix-the-query-and-aggregation-performance-test).

## Consideraciones sobre rendimiento de índices y consultas

En esta sección se describen algunos de los factores comunes que debe tener en cuenta al diseñar índices que tienen que admitir consultas y búsquedas rápidas.

### Almacenamiento de varios tipos en un índice

Un índice de Elasticsearch puede contener varios tipos. Es mejor evitar este enfoque y crear un índice independiente para cada tipo. Considere los siguientes puntos:

- Los diferentes tipos pueden especificar distintos analizadores y no siempre está claro qué analizador debe usar Elasticsearch si se realiza una consulta en el nivel de índice en lugar de en el nivel de tipo. Consulte la sección [Avoiding Type Gotchas](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) (Evitar trampas de tipos) para más información.

- Las particiones de índices que contienen varios tipos probablemente serán mayores que los de índices que contengan un único tipo. Cuanto mayor sea una partición, más esfuerzo requiere Elasticsearch para filtrar los datos cuando se realizan consultas.

- Si hay una discrepancia significativa entre los volúmenes de datos de los tipos, la información para un tipo puede estar escasamente distribuida entre varias particiones, lo que reduce la eficacia de las búsquedas que recuperan estos datos.

    ![](./media/guidance-elasticsearch/query-performance1.png)

    ***Figura 1. Efectos de compartir un índice entre tipos***

    En la figura 1 se describe este escenario. En la parte superior del diagrama, documentos de tipo A y B comparten el mismo índice. Hay más documentos de tipo A que de tipo B. Las búsquedas del tipo A implicarán consultas en las cuatro particiones. La parte inferior del diagrama muestra el efecto si se crean índices independientes para cada tipo. En este caso, las búsquedas del tipo A solo requieren acceso a dos particiones.

- Las particiones pequeñas se pueden distribuir más uniformemente que las particiones grandes, con lo que a Elasticsearch le resulta más sencillo distribuir la carga entre los nodos.

- Los distintos tipos pueden tener diferentes períodos de retención. Puede ser difícil de archivar los datos antiguos que comparten particiones con datos activos.


Sin embargo, en algunas circunstancias el uso compartido de un índice entre los tipos puede ser eficaz si:

- Las búsquedas abarcan regularmente tipos contenidos en el mismo índice.

- Los tipos tienen solo un número pequeño de documentos cada uno; el mantenimiento de un conjunto independiente de particiones para cada tipo puede ser una sobrecarga significativa en este caso.


### Optimización de tipos de índices

Un índice de Elasticsearch contiene una copia de los documentos JSON originales que se utilizaron para rellenarlo. Esta información se almacena en el campo [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) de cada elemento indexado. Estos datos no se pueden buscar pero, de forma predeterminada, los devuelven las solicitudes *get* y *search*. Sin embargo, este campo produce una sobrecarga y ocupa almacenamiento, con lo que las particiones son más grandes y se aumenta el volumen de E/S realizada. Puede deshabilitar el campo *\_source* por tipo:

```http
PUT my_index
{
  "mappings": {
    "my_type": {
      "_source": {
        "enabled": false
      }
    }
  }
}
```
Si se deshabilita este campo, también se quita la capacidad de realizar las siguientes operaciones:

- Actualización de datos en el índice con la API *update*.

- Realización de búsquedas que devuelven datos resaltados.

- Reindexación de un índice de Elasticsearch directamente en otro.

- Cambio de las asignaciones o configuración de análisis.

- Depuración de consultas mediante consulta del documento original.


### Reindexación de los datos

Finalmente, el número de particiones disponibles para un índice determina la capacidad del índice. Puede realizar una estimación inicial (e informada) de cuántas particiones serán necesarias, pero siempre debe considerar de antemano la estrategia de reindexación de documentación. En muchos casos, la reindexación puede ser una tarea pensada cuando aumentan los datos; no puede asignar inicialmente un gran número de particiones a un índice, en aras de la optimización de la búsqueda, pero asigne nuevas particiones a medida que se expanda el volumen de datos. En otros casos la reindexación debe realizarse de forma ad hoc si simplemente se muestra que las estimaciones sobre el crecimiento del volumen de datos son inexactas.

> [AZURE.NOTE] La reindexación podría no ser necesaria para aquellos datos que envejecen rápidamente. En este caso, una aplicación puede crear un nuevo índice para cada período de tiempo. Entre los ejemplos se incluyen registros de rendimiento o de datos de auditoría que podrían almacenarse en un índice nuevo cada día.

<!-- -->

La reindexación implica eficazmente la creación de un nuevo índice de datos en uno anterior y, a continuación, la supresión del índice antiguo. Si un índice es grande, este proceso puede tardar tiempo y puede que necesite asegurarse de que los datos se pueden seguir buscando durante este período. Por este motivo, debe crear un [alias para cada índice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) y las consultas deben recuperar los datos a través de estos alias. Al reindexar, mantenga el alias apuntando al índice antiguo y luego cámbielo para que haga referencia al nuevo índice una vez completada la reindexación. Este enfoque también es útil para tener acceso a datos basados en el tiempo que crean un índice nuevo cada día. Para acceder a los datos actuales, utilice un alias que continuará en el nuevo índice cuando se crea.

### Administración de asignaciones

Elasticsearch utiliza asignaciones para determinar cómo interpretar los datos que se producen en cada campo en un documento. Cada tipo tiene su propia asignación, que define eficazmente un esquema para ese tipo. Elasticsearch utiliza esta información para generar índices invertidos para cada campo en los documentos de un tipo. En cualquier documento, cada campo tiene un tipo de datos (como *string*, *date* o *long*) y un valor. Puede especificar las asignaciones de un índice cuando se crea el índice por primera vez o pueden deducirlas Elasticsearch cuando se agregan nuevos documentos a un tipo. Sin embargo, considere los siguientes puntos:

- Las asignaciones generadas dinámicamente pueden producir errores dependiendo de cómo se interpretan los campos cuando se agregan documentos a un índice. Por ejemplo, el documento 1 puede contener un campo A con un número y hace que Elasticsearch agregue una asignación que especifica que este campo es de tipo *long*. Si se agrega otro documento en el cual el campo A contiene datos no numéricos, se producirá un error. En este caso, el campo A debería probablemente interpretarse como una cadena cuando se agregó el primer documento. Especificar esta asignación cuando se crea el índice puede ayudar a evitar estos problemas.

- Diseñe sus documentos para evitar que se generen asignaciones excesivamente grandes, ya que se puede agregar una importante sobrecarga al realizar búsquedas, consumir mucha memoria y también hacer que las consultas no puedan encontrar datos. Adopte una convención de nomenclatura coherente para los campos de los documentos que comparten el mismo tipo. Por ejemplo, no utilice nombres de campos como "first\_name", "FirstName" y "forename" en distintos documentos. Utilice el mismo nombre de campo en cada documento. Además, no intente utilizar valores como claves (esto es un enfoque común en las bases de datos de familia de columnas, pero puede producir errores e ineficiencias con Elasticsearch). Para más información, consulte [Mapping Explosion](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion) (Explosión de asignación).

- Utilice *not\_analyzed* para evitar la tokenización donde corresponda. Por ejemplo, si un documento contiene un campo de cadena denominado *data* con el valor "ABC-DEF", puede intentar realizar una búsqueda de todos los documentos que coincidan con este valor de la manera siguiente:

  ```http
  GET /myindex/mydata/_search
  {
    "query" : {
      "filtered" : {
        "filter" : {
          "term" : {
            "data" : "ABC-DEF"
          }
        }
      }
    }
  }
  ```

    However, this search will fail to return the expected results due to the way in which the string ABC-DEF is tokenized when it is indexed; it will be effectively split into two tokens, ABC and DEF, by the hyphen. This feature is designed to support full text searching, but if you want the string to be interpreted as a single atomic item you should disable tokenization when the document is added to the index. You can use a mapping such as this:

  ```http
  PUT /myindex
  {
    "mappings" : {
      "mydata" : {
        "properties" : {
          "data" : {
            "type" : "string",
            "index" : "not_analyzed"
          }
        }
      }
    }
  }
  ```

  Para más información, consulte [Finding Exact Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text) (Búsqueda de valores exactos).


### Uso de los valores de documentos

Muchas de las consultas y agregaciones requieren que los datos se ordenen como parte de la operación de búsqueda. La ordenación requiere poder asignar uno o más términos a una lista de documentos. Para ayudar en este proceso, Elasticsearch puede cargar todos los valores de un campo usado como criterio de ordenación en memoria. Esta información se conoce como *fielddata*. La intención es que el almacenamiento en caché de la estructura fielddata en memoria consume menos E/S y podría ser más rápido que leer repetidamente los mismos datos desde el disco. Sin embargo, si un campo tiene cardinalidad alta, almacenar la estructura fielddata en memoria puede consumir mucho espacio de montón, posiblemente que afecta a la capacidad de realizar otras operaciones simultáneas o incluso agotar el almacenamiento causando un error en Elasticsearch.

Como alternativa, Elasticsearch también admite *valores de documento*. Un valor de documento es similar a un elemento de fielddata en memoria, salvo que se almacena en disco y se crea cuando los datos se almacenan en un índice (fielddata se construye dinámicamente cuando se realiza una consulta). Los valores de documento no consumen espacio de montón y, por tanto, son útiles para las consultas que ordenan o agregan datos por los campos que pueden contener un gran número de valores únicos. Además, la presión reducida del montón puede ayudar a compensar las diferencias de rendimiento entre recuperar datos del disco y leer de la memoria; es probable que se produzca con menos frecuencia una colección de elementos no utilizados y es menos probable que se vean afectadas otras operaciones simultáneas que usan la memoria.

Puede habilitar o deshabilitar los valores de documento en función de cada propiedad de un índice con el atributo *doc\_values*, tal como se muestra en el ejemplo siguiente:

```http
PUT /myindex
{
  "mappings" : {
    "mydata" : {
      "properties" : {
        "data" : {
          ...
          "doc_values": true
        }
      }
    }
  }
}
```
> [AZURE.NOTE] Los valores de documento están habilitados de forma predeterminada a partir de Elasticsearch versión 2.0.0.

El impacto exacto del uso de valores de documento suele ser muy específico para sus propios datos y escenarios de consulta, por tanto hay que estar preparado para llevar a cabo pruebas de rendimiento para establecer su utilidad. También hay que tener en cuenta que los valores de documento no funcionan con campos de cadenas analizadas. Para más información, consulte [Doc Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values) (Valores de documento).

### Uso de réplicas para reducir la contención de consultas

Una estrategia habitual para mejorar el rendimiento de las consultas es crear varias réplicas de cada índice. Las operaciones de recuperación de datos se pueden satisfacer mediante la captura de datos de una réplica. Sin embargo, esta estrategia puede afectar gravemente el rendimiento de las operaciones de ingesta de datos, por lo que debe utilizarse con cuidado en escenarios que impliquen cargas de trabajo mixtas. Además, esta estrategia solo es beneficiosa si las réplicas se distribuyen entre los nodos y no compiten por los recursos con particiones principales que forman parte del mismo índice. Recuerde que es posible aumentar o disminuir el número de réplicas de un índice de forma dinámica.

### Uso de la memoria caché de la solicitud de particiones

Elasticsearch puede almacenar en caché los datos locales solicitados por las consultas en cada partición de la memoria. Esto permite que las búsquedas que recuperan los mismos datos se ejecuten con más rapidez; los datos pueden leerse de la memoria en lugar de leerse del almacenamiento en disco. Por lo tanto, almacenar datos en caché de esta manera puede mejorar el rendimiento de algunas operaciones de búsqueda, a costa de reducir la memoria disponible para otras tareas que se realizan al mismo tiempo. También existe el riesgo de que los datos que se sirven desde la memoria caché no estén actualizados. Los datos de la memoria caché se invalidan cuando se actualiza la partición y los datos han cambiado; la frecuencia de las actualizaciones se rige por el valor de la configuración de *refresh\_interval* del índice.

De manera predeterminada, la caché de solicitudes de un índice está deshabilitada, pero puede habilitarla de la forma siguiente:

```http
PUT /myindex/_settings
{
  "index.requests.cache.enable": true
}
```

La memoria caché de la solicitud de particiones es más adecuada para la información que permanece relativamente estática, como datos históricos o de registro.

### Uso de nodos de cliente

Todas las consultas se procesan mediante el nodo que primero recibe la solicitud. Este nodo envía más solicitudes a todos los demás nodos que contienen particiones para los índices que se consultan y, después, acumula los resultados para devolver la respuesta. Si una consulta implica agregar datos o realizar cálculos complejos, el nodo inicial es responsable de realizar el procesamiento adecuado. Si el sistema tiene que admitir un número relativamente pequeño de consultas complejas, considere la posibilidad de crear un grupo de nodos de cliente para aliviar la carga de los nodos de datos. Por el contrario, si el sistema tiene que administrar un gran número de consultas sencillas, envíe estas solicitudes directamente a los nodos de datos y utilice un equilibrador de carga para distribuir las solicitudes de manera uniforme.

### Optimización de consultas

Los puntos siguientes resumen algunas sugerencias para maximizar el rendimiento de las consultas de Elasticsearch:

- Evite consultas que incluyan comodines siempre que sea posible.

- Si el mismo campo está sujeto a búsqueda de texto y coincidencia exacta, considere la posibilidad de almacenar los datos del campo en formularios analizados y no analizados. Realice búsquedas de texto completo contra el campo analizado y coincidencias exactas contra el campo no analizado.

- Devuelva solo los datos necesarios. Si tiene documentos grandes, pero una aplicación solo necesita la información contenida en un subconjunto de campos, entonces devuelva este subconjunto de las consultas en lugar de los documentos enteros. Esta estrategia puede reducir los requisitos de ancho de banda de red del clúster.

- Siempre que sea posible, utilice filtros en lugar de consultas al buscar datos. Un filtro simplemente determina si un documento cumple un criterio determinado, mientras que una consulta también calcula lo cerca que está una coincidencia de un documento (puntuación). Internamente, los valores generados por un filtro se almacenan como un mapa de bits que indica la coincidencia o no coincidencia de cada documento y se pueden almacenar en caché en Elasticsearch. Si el mismo criterio de filtro se produce posteriormente, el mapa de bits se puede recuperar de la caché y se utiliza para buscar rápidamente los documentos coincidentes. Para más información, consulte [Internal Filter Operation](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_internal_filter_operation) (Operaciones de filtro internas).

- Utilice filtros *bool* para realizar comparaciones estáticas y use solo filtros *and*, *or* y *not* para los filtros calculados dinámicamente, como los relacionados con el scripting o los filtros *geo-**.

- Si una consulta combina filtros *bool* con *and*, *or* o *not* con filtros *geo-**, coloque los filtros *and*/*or*/*not geo-** al final para que operen en el conjunto de datos más pequeño posible.

    Asimismo, utilice un elemento *post\_filter* para ejecutar operaciones de filtro costosas. Estos filtros se ejecutarán en último lugar.

- Utilice agregaciones en lugar de facetas. Evite calcular agregados que se analicen o que tengan muchos valores posibles.

    > **Nota**: Se han quitado las facetas en Elasticsearch versión 2.0.0.

- Utilice la agregación *cardinality* en lugar de la agregación *value\_count* a menos que su aplicación requiera un recuento exacto de elementos coincidentes. Un recuento exacto puede quedar obsoleto rápidamente y muchas aplicaciones requieren solo una aproximación razonable.

- Evite el scripting. Los scripts en consultas y filtros pueden ser costosos y los resultados no se almacenan en la caché. Los scripts de ejecución prolongada pueden consumir subprocesos de búsqueda de manera indefinida, lo que hace que las posteriores solicitudes se pongan en la cola. Si la cola se llena, se rechazarán las solicitudes adicionales.

## Pruebas y análisis de rendimiento de agregación y de búsqueda

En esta sección se describen los resultados de una serie de pruebas que se realizaron en distintas configuraciones de índice y de clúster. Se realizaron dos tipos de pruebas, como se describe a continuación:

- **La prueba* de *ingesta y consulta**. Esta prueba comenzó con un índice vacío que se rellenaba a medida que continuaba la prueba mediante la ejecución de operaciones de inserción masiva (cada operación agregaba 1000 documentos). Al mismo tiempo, varias consultas diseñadas para buscar documentos agregados durante el período anterior de 15 minutos y generar agregaciones se repitieron a intervalos de cinco segundos. Esta prueba normalmente se dejaba que se ejecutara durante 24 horas, para reproducir los efectos de una carga de trabajo complicada que comprende la ingesta de datos a gran escala con consultas casi en tiempo real.

- **La * prueba de *solo consulta**. Esta prueba es parecida a la de *ingesta y consulta*, a excepción de que la parte de ingesta se omite y el índice de cada nodo se rellena previamente con 100 millones de documentos. Se realiza un conjunto modificado de consultas; el elemento de tiempo que limitaba los documentos a los agregados en los últimos 15 minutos se ha quitado ya que ahora los datos son estáticos. Las pruebas se ejecutaron durante 90 minutos; se necesita menos tiempo para establecer un patrón de rendimiento debido a la cantidad de datos fija.

---

Cada documento del índice tenía el mismo esquema. Esta tabla resume los campos del esquema:

Nombre | Tipo | Notas |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organización | Cadena | La prueba genera 200 organizaciones únicas. |
 CustomField1 - CustomField5 |String |Hay cinco campos de cadena que se establecen en la cadena vacía.|
 DateTimeRecievedUtc |Timestamp |La fecha y la hora en que se agregó el documento.|
 Host |String |Este campo se establece en una cadena vacía.|
 HttpMethod |String |Este campo se establece en uno de los siguientes valores: "POST", "GET", "PUT".|
 HttpReferrer |String |Este campo se establece en una cadena vacía.|
 HttpRequest |String |Este campo se rellena con texto aleatorio entre 10 y 200 caracteres de longitud.|
 HttpUserAgent |String |Este campo se establece en una cadena vacía.|
 HttpVersion |String |Este campo se establece en una cadena vacía.|
 OrganizationName |Cadena |Este campo se establece en el mismo valor que el campo de la organización.|
 SourceIp |IP |Este campo contiene una dirección IP que indica el "origen" de los datos. |
 SourceIpAreaCode |Largo |Este campo se establece en 0.|
 SourceIpAsnNr |Cadena |Este campo se establece en "AS#####".|
 SourceIpBase10 |Largo |Este campo se establece en 500.|
 SourceIpCountryCode |Cadena |Este campo contiene un código de país de dos caracteres. |
 SourceIpCity |String |Este campo contiene una cadena que identifica una ciudad de un país. |
 SourceIpLatitude |Doble |Este campo contiene un valor aleatorio.|
 SourceIpLongitude |Doble |Este campo contiene un valor aleatorio.|
 SourceIpMetroCode |Largo |Este campo se establece en 0.|
 SourceIpPostalCode |Cadena |Este campo se establece en una cadena vacía.|
 SourceLatLong |Punto geográfico |Este campo se establece en un punto geográfico aleatorio.|
 SourcePort |String |Este campo se rellena con la representación de cadena de un número aleatorio.|
 TargetIp |IP |Se rellenará con una dirección IP aleatoria en el intervalo de 0.0.100.100 a 255.9.100.100.|
 SourcedFrom |String |Este campo se establece en la cadena "MonitoringCollector".|
 TargetPort |String |Este campo se rellena con la representación de cadena de un número aleatorio.|
 Rating |String |Este campo se rellena con uno de los 20 valores de cadena diferentes seleccionados al azar.|
 UseHumanReadableDateTimes |Booleano |Este campo se establece en false.|
 
Las consultas siguientes se ejecutaron como un lote en cada repetición de las pruebas. Los nombres en cursiva se utilizan para hacer referencia a estas consultas en el resto de este documento. Tenga en cuenta que se ha omitido el criterio de tiempo (documentos que se agregaron en los últimos 15 minutos) de las pruebas de *solo consulta*:

- ¿Cuántos documentos con cada valor de *Rating* se han incluido en los últimos 15 minutos (*Recuento por clasificación*)?

- ¿Cuántos documentos se agregaron en cada intervalo de 5 minutos durante los últimos 15 minutos (*Recuento a lo largo del tiempo*)?

- ¿Cuántos documentos de cada valor de *Rating* se han agregado para cada país en los últimos 15 minutos (*Visitas por país*)?

- ¿Qué 15 organizaciones aparecen con más frecuencia en los documentos agregados en los últimos 15 minutos (*15 organizaciones principales*)?

- ¿Cuántas organizaciones distintas aparecen en los documentos agregados en los últimos 15 minutos (*Organizaciones de recuento único*)?

- ¿Cuántos documentos se agregaron en los últimos 15 minutos (*Recuento total de visitas*)?

- ¿Cuántos valores de *SourceIp* diferentes aparecen en los documentos agregados en los últimos 15 minutos (*Recuento de IP único*)?


La definición del índice y los detalles de las consultas se describen en el [apéndice](#appendix-the-query-and-aggregation-performance-test).

Las pruebas se diseñaron para comprender los efectos de las siguientes variables:

- **Tipo de disco**. Las pruebas se realizaron en un clúster de seis nodos de máquinas virtuales D4 mediante almacenamiento estándar (HDD) y se repitió en un clúster de seis nodos de máquinas virtuales DS4 con almacenamiento premium (SSD).

- **Tamaño de la máquina: escalado vertical**. Las pruebas se realizaron en un clúster de seis nodos que comprendía máquinas virtuales DS3 (designadas como el clúster *pequeño*) y se repitió en un clúster de máquinas virtuales DS4 (el clúster *mediano*) y en un clúster de máquinas DS14 (el clúster *grande*). En la tabla siguiente se resumen las características clave de cada SKU de máquina virtual:

 Clúster | SKU de la máquina virtual | Número de núcleos | Número de discos de datos | RAM (GB) |
---------|---------------|-----------------|----------------------|----------|
 Pequeña | DS3 estándar | 4 | 8 | 14 |
 Mediano | DS4 estándar | 8 | 16 | 28 |
 Grande | DS14 estándar | 16 | 32 | 112 |

- **Tamaño de clúster: escalado horizontal**. Se realizaron pruebas en clústeres de máquinas virtuales DS14 que comprendían uno, tres y seis nodos.

- **Número de réplicas de índice**. Se realizaron pruebas utilizando índices configurados con una y dos réplicas.

- **Valores de documento**. Inicialmente las pruebas se realizaron con la configuración de índice *doc\_values* establecida en *true* (el valor predeterminado). Las pruebas seleccionadas se repitieron con *doc\_values* establecido en *false*.

- **Almacenamiento en caché**. Se realizaron pruebas con la caché de solicitud de particiones habilitada en el índice.

- **Número de particiones**. Las pruebas se repitieron utilizando diferentes números de particiones para establecer si las consultas se ejecutaban más eficazmente entre índices que contenían menos particiones pero más grandes o más particiones pero más pequeñas.


## Resultados de rendimiento: tipo de disco

El rendimiento del disco se evaluó mediante la ejecución de una prueba de *ingesta y consulta* en el clúster de seis nodos de máquinas virtuales D4 (con unidades de disco duro) y en el clúster de seis nodos de máquinas virtuales DS4 (con SSD). La configuración de Elasticsearch en ambos clústeres era la misma. Los datos se difundieron por los 16 discos en cada nodo y cada nodo tenía 14 GB de memoria RAM asignada a JVM que ejecutaba Elasticsearch; se dejó la memoria restante (también 14 GB) para uso del sistema operativo. Cada prueba se ejecutó durante 24 horas. Este período se seleccionó para permitir que los efectos del volumen creciente de datos se hicieran evidentes y que se estabilizara el sistema. En la siguiente tabla se resumen los resultados y se resaltan los tiempos de respuesta de las distintas operaciones que componen la prueba.

 Clúster | Operación o consulta | Tiempo de respuesta promedio (ms) |
---------|----------------------------|----------------------------|
 D4 | Ingesta de datos | 978 |
 | Recuento por clasificación | 103 |
 | Recuento a lo largo del tiempo | 134 |
 | Visitas por país | 199 |
 | 15 organizaciones principales | 137 |
 | Organizaciones de recuento único | 139 |
 | Recuento de IP único | 510 |
 | Recuento total de visitas | 89
 DS4 | Ingesta de datos | 511 |
 | Recuento por clasificación | 187 |
 | Recuento a lo largo del tiempo | 411 |
 | Visitas por país | 402 |
 | 15 organizaciones principales | 307 |
 | Organizaciones de recuento único | 320 |
 | Recuento de IP único | 841 |
 | Recuento total de visitas | 236 |

A primera vista, parecería que el clúster DS4 ha realizado consultas con menos eficiencia que el clúster D4, a veces duplicando (o incluso peor) el tiempo de respuesta. Sin embargo, esto no muestra la imagen completa. En la tabla siguiente se muestra el número de operaciones de ingesta realizadas por cada clúster (recuerde que cada operación carga 1000 documentos):

 Clúster | N.º de operaciones de ingesta |
---------|-------------------------|
 D4 | 264769 |
 DS4 | 503157 |

El clúster DS4 fue capaz de cargar casi dos veces más datos que el clúster D4 durante la prueba. Por lo tanto, al analizar los tiempos de respuesta para cada operación, también debe tener en cuenta cuántos documentos tiene que examinar cada consulta y cuántos documentos se devuelven. Estas son cifras dinámicas, ya que el volumen de los documentos en el índice aumenta continuamente. No se puede dividir simplemente 503137 entre 264769 (el número de operaciones de ingesta realizadas por cada clúster) y después multiplicar el resultado por el tiempo medio de respuesta para cada consulta realizada por el clúster D4 para dar una cifra comparativa, ya que se ignora la cantidad de E/S que se realiza simultáneamente en la operación de ingesta. En su lugar, debe medir la cantidad física de los datos que se escriben en el disco, y se leen de él, mientras se realiza la prueba. El plan de pruebas JMeter captura esta información para cada nodo. Los resultados resumidos fueron los siguientes:

 Clúster | Promedio de bytes escritos o leídos por cada operación |
---------|----------------------------------------------|
 D4 | 13471557 |
 DS4 | 24643470 |

Estos datos muestran que el clúster DS4 fue capaz de soportar una tasa de E/S de aproximadamente 1,8 veces la del clúster D4. Dado que, además de la naturaleza de los discos, todos los demás recursos son iguales, la diferencia puede deberse al uso de SSD en lugar de HDD.

Para ayudar a justificar esta conclusión, los gráficos siguientes muestran cómo se realizó la E/S a lo largo del tiempo por cada clúster:

![](./media/guidance-elasticsearch/query-performance2.png)

<!-- -->

***Figura 2. Actividad de disco para los clústeres D4 y DS4***

El gráfico del clúster D4 muestra una variación considerable, especialmente durante la primera mitad de la prueba. Esto fue probablemente debido a la limitación para reducir la velocidad de E/S. En las fases iniciales de la prueba, las consultas pueden ejecutarse rápidamente, ya que hay pocos datos para analizar. Los discos del clúster D4, por tanto, suelen estar funcionando cerca de su capacidad IOPS, aunque cada operación de E/S no puede devolver demasiados datos. El clúster DS4 es capaz de admitir una mayor tasa de IOPS y no presenta el mismo grado de limitación; las tasas de E/S son más regulares. Para respaldar esta teoría, el siguiente par de gráficos muestra cómo la E/S del disco ha bloqueado la CPU a lo largo del tiempo (los tiempos de espera de disco que se muestran en los gráficos son la proporción de tiempo que la CPU ha empleado en esperar la E/S):

![](./media/guidance-elasticsearch/query-performance3.png)

***Figura 3. Tiempos de espera de E/S de disco de la CPU para los clústeres D4 y DS4***

Es importante comprender que hay dos razones de peso por las que las operaciones de E/S bloquean la CPU:

- El subsistema de E/S se podría leer o escribir los datos hacia o desde el disco.

- El subsistema de E/S podría estar limitado por el entorno de host. Los discos de Azure implementados mediante unidades de disco duro ofrecen un rendimiento máximo de 500 IOPS y las unidades SSD de 5000 IOPS.


Para el clúster D4, el tiempo empleado en esperar la E/S durante la primera mitad de la prueba se correlaciona estrechamente de manera inversa con el gráfico que muestra las tasas de E/S; los períodos de baja E/S corresponden a los períodos de tiempo considerable que la CPU se encuentra bloqueada; esto indica que la E/S se está limitando. A medida que se agregan más datos al clúster, la situación cambia y, en la segunda mitad de los picos de prueba en E/S, los tiempos de espera se corresponden a los picos de rendimiento de E/S. En este punto, la CPU se bloquea al realizar la E/S real. De nuevo, con el clúster DS4, el tiempo dedicado a esperar la E/S es mucho más uniforme y cada pico coincide con un pico de rendimiento de E/S, en lugar de con un valle; esto implica que se produce muy poca limitación, o ninguna.

Hay otro factor a tener en cuenta. Durante la prueba, el clúster D4 genera 10584 errores de ingesta y 21 errores de consulta. La prueba en el clúster DS4 no había producido ningún error.

## Resultados de rendimiento: escalado vertical

Se realizaron pruebas de escalado vertical mediante la ejecución de pruebas en clústeres de seis nodos de máquinas virtuales DS3, DS4 y DS14. Estos SKU se seleccionaron porque una máquina virtual DS4 proporciona el doble de núcleos de CPU y memoria que una máquina DS3, y una máquina DS14 vuelve a duplicar los recursos de CPU al tiempo que proporciona cuatro veces la cantidad de memoria. En la tabla siguiente se comparan los aspectos clave de cada SKU:

 SKU | N.º de núcleos de CPU | Memoria (GB) | IOPS de disco máximo | Ancho de banda máximo (MB/s)|
------|-------------|-------------|---------------|--------------|
 DS3 | 4 | 14 | 12\.800| 128 |
 DS4 | 8 | 28 | 25\.600| 256 |
 DS14 | 16 | 112 | 50\.000| 512 |

En la tabla siguiente se resumen los resultados de ejecutar las pruebas en clústeres de tamaño pequeño (DS3), mediano (DS4) y grande (DS14). Cada máquina virtual usa SSD para almacenar los datos. Cada prueba se ejecutó durante 24 horas:

> **Nota**: La tabla informa del número de solicitudes correctas de cada tipo de consulta (no se incluyen los errores). El número de solicitudes que se han intentado para cada tipo de consulta es aproximadamente el mismo durante una ejecución de la prueba. El motivo es que el plan de prueba JMeter ejecuta una sola instancia de cada consulta (recuento por clasificación, recuento a lo largo del tiempo, visitas por país, 15 organizaciones principales, únicos de las organizaciones de recuento, recuento de IP único y recuento total de visitas) en una sola unidad que se conoce como *transacción de prueba* (esta transacción es independiente de la tarea que realiza la operación de ingesta, que se ejecuta mediante un subproceso independiente). Cada repetición del plan de pruebas realiza una transacción de prueba única. Por lo tanto, el número de transacciones de prueba completadas es una medida del tiempo de respuesta de la consulta más lenta en cada transacción.

| Clúster | Operación o consulta | Número de solicitudes | Tiempo de respuesta promedio (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Pequeño (DS3) | Ingesta de datos | 207 284 | 3328 |
| | Recuento por clasificación | 18 444 | 268 |
| | Recuento a lo largo del tiempo | 18 444 | 340 |
| | Visitas por país | 18 445 | 404 |
| | 15 organizaciones principales | 18 439 | 323 |
| | Organizaciones de recuento único | 18 437 | 338 |
| | Recuento de IP único | 18 442 | 468 |
| | Recuento total de visitas | 18 428 | 294   
|||||
| Mediano (DS4) | Ingesta de datos | 503157 | 511 |
| | Recuento por clasificación | 6958 | 187 |
| | Recuento a lo largo del tiempo | 6958 | 411 |
| | Visitas por país | 6958 | 402 |
| | 15 organizaciones principales | 6958 | 307 |
| | Organizaciones de recuento único | 6956 | 320 |
| | Recuento de IP único | 6955 | 841 |
| | Recuento total de visitas | 6958 | 236 |
|||||
| Grande (DS14) | Ingesta de datos | 502714 | 511 |
| | Recuento por clasificación | 7041 | 201 |
| | Recuento a lo largo del tiempo | 7040 | 298 |
| | Visitas por país | 7039 | 363 |
| | 15 organizaciones principales | 7038 | 244 |
| | Organizaciones de recuento único | 7037 | 283 |
| | Recuento de IP único | 7037 | 681 |
| | Recuento total de visitas | 7038 | 200 |

Estas ilustraciones muestran que, en esta prueba, el rendimiento de los clústeres DS4 y DS14 fue bastante similar. También parece que los tiempos de respuesta de las operaciones de consulta para el clúster DS3 se comparan favorablemente al principio, y que el número de operaciones de consulta realizadas se aleja bastante de los valores de los clústeres DS4 y DS14. Sin embargo, es necesario tener en cuenta la importancia de la tasa de ingesta y el número consiguiente de documentos que se buscan. En el clúster DS3 la ingesta está bastante más restringida y, al final de la prueba, la base de datos solo contenía casi un 40 % de los documentos leídos por cada uno de los otros dos clústeres. Esto podría ser debido a los recursos de procesamiento, la red y el ancho de banda de disco disponibles para una máquina virtual DS3 en comparación con los de una máquina virtual DS4 o DS14. Dado que una máquina virtual DS4 tiene el doble de recursos disponibles que una máquina virtual DS3, y una máquina virtual DS14 tiene el doble de recursos (y cuatro veces la memoria) que una máquina virtual DS4, aún queda una pregunta: ¿por qué la diferencia en las tasas de ingesta entre los clústeres DS4 y DS14 es considerablemente menor que entre los clústeres DS3 y DS4? Esto puede deberse a los límites de utilización y ancho de banda de red de las máquinas virtuales de Azure. En los gráficos siguientes se muestran estos datos para los tres clústeres:

![](./media/guidance-elasticsearch/query-performance4.png)

***Figura 4. Uso de red de los clústeres DS3, DS4 y DS14 al realizar la prueba de *ingesta y consulta* ***

<!-- -->

Los límites de ancho de banda de red disponible con máquinas virtuales de Azure no se publican y pueden variar, pero el hecho de que la actividad de red parezca haberse estabilizado en una media de 275 GBps tanto en la prueba del clúster DS4 como en la del clúster DS14 sugiere que dicho límite se ha alcanzado y que se ha convertido en el factor principal en la restricción del rendimiento. En el caso del clúster DS3, la actividad de red era considerablemente menor así que es más probable un rendimiento menor debido a restricciones en la disponibilidad de otros recursos.

Para aislar los efectos de las operaciones de ingesta y mostrar cómo el rendimiento de las consultas varía a medida que los nodos se escalan verticalmente, se realizó un conjunto de pruebas de solo consulta utilizando los mismos nodos. En la tabla siguiente se resumen los resultados obtenidos en cada clúster:

> [AZURE.NOTE] No se debe comparar el rendimiento y el número de solicitudes ejecutadas por las consultas en la prueba de *solo consulta* con las que se ejecutan en la prueba de *ingesta y consulta*. El motivo es que las consultas se han modificado y el volumen de documentos que intervienen es diferente.

| Clúster | Operación o consulta | Número de solicitudes | Tiempo de respuesta promedio (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Pequeño (DS3) | Recuento por clasificación | 464 | 11 758 |
| | Recuento a lo largo del tiempo | 464 | 14 699 |
| | Visitas por país | 463 | 14 075 |
| | 15 organizaciones principales | 464 | 11 856 |
| | Organizaciones de recuento único | 462 | 12 314 |
| | Recuento de IP único | 461 | 19 898 |
| | Recuento total de visitas | 462 | 8882  
|||||
| Mediano (DS4) | Recuento por clasificación | 1045 | 4489 |
| | Recuento a lo largo del tiempo | 1045 | 7292 |
| | Visitas por país | 1053 | 7564 |
| | 15 organizaciones principales | 1055 | 5066 |
| | Organizaciones de recuento único | 1051 | 5231 |
| | Recuento de IP único | 1051 | 9228 |
| | Recuento total de visitas | 1051 | 2180 |
|||||
| Grande (DS14) | Recuento por clasificación | 1842 | 1927 |
| | Recuento a lo largo del tiempo | 1839 | 4483 |
| | Visitas por país | 1838 | 4761 |
| | 15 organizaciones principales | 1842 | 2117 |
| | Organizaciones de recuento único | 1837 | 2393 |
| | Recuento de IP único | 1837 | 7159 |
| | Recuento total de visitas | 1837 | 642 |

Esta vez, la tendencia en los tiempos de respuesta medios entre los diferentes clústeres es más clara. La utilización de red está muy por debajo de los 2,75 GBps necesarios anteriormente por los clústeres DS4 y DS14 (lo que probablemente haya saturado la red en las pruebas de ingesta y consulta), y los 1,5 GBps del clúster DS3. De hecho, está más cerca de 200 Mbps en todos los casos, como se muestra en los gráficos siguientes:

![](./media/guidance-elasticsearch/query-performance5.png)

***Figura 5. Uso de red de los clústeres DS3, DS4 y DS14 al realizar la prueba de *solo consulta* ***

El factor de limitación en los clústeres DS3 y DS4 parece ser ahora la utilización de CPU, que se acerca al 100 % la mayoría del tiempo. En el clúster DS14 el uso de la CPU alcanza más del 80 % por término medio. Aún siendo todavía alto, resalta claramente las ventajas de tener más núcleos de CPU disponibles. La imagen siguiente representa los patrones de uso de CPU de los clústeres DS3, DS4 y DS14.

![](./media/guidance-elasticsearch/query-performance6.png)

***Figura 6. Uso de CPU de los clústeres DS3 y DS14 al realizar la prueba de *solo consulta* ***

## Resultados de rendimiento: escalado horizontal

Para ilustrar cómo se escala el sistema horizontalmente con el número de nodos, se ejecutaron pruebas mediante clústeres DS14 que comprenden uno, tres y seis nodos. Esta vez, solo se realizó la prueba de *solo consulta*, usando 100 millones de documentos y ejecutándola durante 90 minutos:

> [AZURE.NOTE] Para más información sobre cómo el escalado horizontal puede afectar al comportamiento de las operaciones de ingesta de datos, consulte el documento [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximización del rendimiento de la ingesta de datos con Elasticsearch en Azure).

| Clúster | Operación o consulta | Número de solicitudes | Tiempo de respuesta promedio (ms) |
|---------|----------------------------|--------------------|----------------------------|
| 1 nodo | Recuento por clasificación | 288 | 6216 |
| | Recuento a lo largo del tiempo | 288 | 28 933 |
| | Visitas por país | 288 | 29 455 |
| | 15 organizaciones principales | 288 | 9058 |
| | Organizaciones de recuento único | 287 | 19 916 |
| | Recuento de IP único | 284 | 54 203 |
| | Recuento total de visitas | 287 | 3333 |
|||||
| 3 nodos | Recuento por clasificación | 1194 | 3427 |
| | Recuento a lo largo del tiempo | 1194 | 5381 |
| | Visitas por país | 1191 | 6840 |
| | 15 organizaciones principales | 1196 | 3819 |
| | Organizaciones de recuento único | 1190 | 2938 |
| | Recuento de IP único | 1189 | 12 516 |
| | Recuento total de visitas | 1191 | 1272 |
|||||
| 6 nodos | Recuento por clasificación | 1842 | 1927 |
| | Recuento a lo largo del tiempo | 1839 | 4483 |
| | Visitas por país | 1838 | 4761 |
| | 15 organizaciones principales | 1842 | 2117 |
| | Organizaciones de recuento único | 1837 | 2393 |
| | Recuento de IP único | 1837 | 7159 |
| | Recuento total de visitas | 1837 | 642 |

El número de nodos marca una importante diferencia en el rendimiento de consultas del clúster, aunque de una manera no lineal; el clúster de tres nodos realiza casi cuatro veces más tantas consultas que el clúster de un único nodo, mientras que el clúster de seis nodos realiza seis veces más. Para ayudar a explicar esta falta de linealidad, los gráficos siguientes muestran cómo los tres clústeres han consumido CPU:

![](./media/guidance-elasticsearch/query-performance7.png)

***Figura 7. Uso de CPU de los clústeres de uno, tres y seis nodos al realizar la prueba de *solo consulta* ***

Los clústeres de uno y tres nodos están enlazados a la CPU, pero aunque la utilización de la CPU es alta en el clúster de seis nodos, hay capacidad de procesamiento de reserva disponible. En este caso, es probable que sean otros factores los que limiten el rendimiento. Para confirmar esto, pruebe con nueve y doce nodos, que es probable que muestren capacidad de procesamiento de reserva adicional.

Los datos de la tabla anterior también muestran cómo varían los tiempos medios de respuesta de las consultas. Este es el elemento que más información proporciona al probar cómo se escala un sistema para determinados tipos de consultas; algunas búsquedas son claramente bastantes más eficientes cuando se reparten entre más nodos que otras. Esto podría ser debido a la relación entre el número de nodos y el creciente número de documentos en el clúster; cada clúster contiene 100 millones de documentos. Al realizar búsquedas que implican la agregación de datos, Elasticsearch procesa y almacena en búfer los datos recuperados como parte del proceso de agregación en memoria en cada nodo. Si hay más nodos, hay menos datos para recuperar, almacenar en búfer y procesar en cada nodo.

## Resultados de rendimiento: número de réplicas

Las pruebas de *ingesta y consulta* se ejecutaron en un índice con una sola réplica. Las pruebas se repitieron en los clústeres DS4 y DS14 de seis nodos usando un índice configurado con dos réplicas. Todas las pruebas se ejecutaron durante 24 horas. En la siguiente tabla se muestran los resultados comparativos para una y dos réplicas:

| Clúster | Operación o consulta | Tiempo de respuesta medio (ms): 1 réplica | Tiempo de respuesta medio (ms): 2 réplicas | % de diferencia en el tiempo de respuesta |
|---------|----------------------------|----------------------------------------|-----------------------------------------|-------------------------------|
| DS4 | Ingesta de datos | 511 | 655 | +28 % |
| | Recuento por clasificación | 187 | 168 | -10 % |
| | Recuento a lo largo del tiempo | 411 | 309 | -25 % |
| | Visitas por país | 402 | 562 | +40 % |
| | 15 organizaciones principales | 307 | 366 | +19 % |
| | Organizaciones de recuento único | 320 | 378 | +18 % |
| | Recuento de IP único | 841 | 987 | +17 % |
| | Recuento total de visitas | 236 | 236 | +0 % |
||||||
| DS14 | Ingesta de datos | 511 | 618 | +21 % |
| | Recuento por clasificación | 201 | 275 | +37 % |
| | Recuento a lo largo del tiempo | 298 | 466 | +56 % |
| | Visitas por país | 363 | 529 | +46 % |
| | 15 organizaciones principales | 244 | 407 | +67 % |
| | Organizaciones de recuento único | 283 | 403 | +42 % |
| | Recuento de IP único | 681 | 823 | +21 % |
| | Recuento total de visitas | 200 | 221 | +11 % |

La tasa de ingesta disminuyó conforme el número de réplicas aumentaba. Este comportamiento es el esperado ya que Elasticsearch está escribiendo más copias de cada documento, lo que genera E/S de disco adicional. Esto se refleja en los gráficos del clúster DS14 en los índices con una y dos réplicas que se muestran en el siguiente diagrama. En el caso del índice con una réplica, la velocidad media de E/S era de 16896573 bytes por segundo. En el caso del índice con dos réplicas, la tasa media de E/S fue de 33986843 bytes por segundo; dos veces más.

![](./media/guidance-elasticsearch/query-performance8.png)

***Figura 8. Tasas de E/S en nodos con una y dos réplicas al realizar la prueba de *ingesta y consulta****

| Clúster | Consultar | Tiempo de respuesta medio (ms): 1 réplica | Tiempo de respuesta medio (ms): 2 réplicas |
|---------|----------------------------|----------------------------------------|-----------------------------------------|
| DS4 | Recuento por clasificación | 4489 | 4079 |
| | Recuento a lo largo del tiempo | 7292 | 6697 |
| | Visitas por país | 7564 | 7173 |
| | 15 organizaciones principales | 5066 | 4650 |
| | Organizaciones de recuento único | 5231 | 4691 |
| | Recuento de IP único | 9228 | 8752 |
| | Recuento total de visitas | 2180 | 1909 |
|||||
| DS14 | Recuento por clasificación | 1927 | 2330 |
| | Recuento a lo largo del tiempo | 4483 | 4381 |
| | Visitas por país | 4761 | 5341 |
| | 15 organizaciones principales | 2117 | 2560 |
| | Organizaciones de recuento único | 2393 | 2546 |
| | Recuento de IP único | 7159 | 7048 |
| | Recuento total de visitas | 642 | 708 |

Estos resultados muestran una mejora en el tiempo medio de respuesta para el clúster DS4, pero un aumento para el clúster DS14. Para ayudar a interpretar estos resultados, también debe considerar el número de consultas realizadas en cada prueba:

| Clúster | Consultar | Número realizado: 1 réplica | Número realizado: 2 réplicas |
|---------|----------------------------|------------------------------|-------------------------------|
| DS4 | Recuento por clasificación | 1054 | 1141 |
| | Recuento a lo largo del tiempo | 1054 | 1139 |
| | Visitas por país | 1053 | 1138 |
| | 15 organizaciones principales | 1055 | 1141 |
| | Organizaciones de recuento único | 1051 | 1136 |
| | Recuento de IP único | 1051 | 1135 |
| | Recuento total de visitas | 1051 | 1136 |
|||||
| DS14 | Recuento por clasificación | 1842 | 1718 |
| | Recuento a lo largo del tiempo | 1839 | 1716 |
| | Visitas por país | 1838 | 1714 |
| | 15 organizaciones principales | 1842 | 1718 |
| | Organizaciones de recuento único | 1837 | 1712 |
| | Recuento de IP único | 1837 | 1712 |
| | Recuento total de visitas | 1837 | 1712 |

Estos datos muestran que el número de consultas realizadas por el clúster DS4 aumentó a la par que el tiempo medio de respuesta disminuyó, pero de nuevo, lo contrario se cumple en el clúster DS14. Un factor importante es que el uso de CPU del clúster DS4 en las pruebas de una y dos réplicas se repartió de manera desigual; algunos nodos mostraron una utilización cercana al 100 %, mientras que otros tuvieron capacidad de procesamiento de reserva. Probablemente, la mejora del rendimiento sea debida al aumento en la capacidad de distribuir el procesamiento entre los nodos del clúster. En la siguiente imagen se muestra la variación en el procesamiento de la CPU entre las máquinas virtuales que se usan más y menos (nodos 4 y 3):

![](./media/guidance-elasticsearch/query-performance9.png)

***Figura 9. Uso de CPU en los nodos más y menos utilizados del clúster DS4 al realizar la prueba de *solo consulta* ***

Este no fue el caso para el clúster DS14. El uso de CPU en ambas pruebas fue menor en todos los nodos, y la disponibilidad de una segunda réplica se convirtió más en una sobrecarga que una ventaja:

![](./media/guidance-elasticsearch/query-performance10.png)

***Figura 10. Uso de CPU en los nodos más y menos utilizados del clúster DS14 al realizar la prueba de *solo consulta* ***

Estos resultados muestran la necesidad de realizar pruebas comparativas de su sistema detenidamente al decidir si va a utilizar varias réplicas. Siempre debe tener al menos una réplica de cada índice (a menos que esté dispuesto a arriesgarse a perder datos si un nodo da error), pero el uso de réplicas adicionales puede imponer una carga en el sistema a cambio de poco beneficio, todo depende de las cargas de trabajo y de los recursos de hardware disponibles para el clúster.

## Resultados de rendimiento: valores de documento

Las pruebas de *ingesta y consulta* se realizaron con valores de documento habilitados, lo que hace que Elasticsearch almacene los datos usados para ordenar los campos en el disco. Las pruebas se repitieron con valores de documentos deshabilitados, por lo que Elasticsearch construyó la estructura fielddata dinámicamente y la almacenó en la memoria caché. Todas las pruebas se ejecutaron durante 24 horas. En la tabla siguiente se comparan los tiempos de respuesta de las pruebas ejecutadas en los clústeres de seis nodos creados mediante las máquinas virtuales D4, DS4 y DS14 (el clúster D4 utiliza discos duros normales, mientras que los clústeres DS4 y DS14 utilizan unidades SSD).

| Clúster | Operación o consulta | Tiempo medio de respuesta (ms): valores de documento habilitados | Tiempo medio de respuesta (ms): valores de documento deshabilitados | % de diferencia en el tiempo de respuesta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| D4 | Ingesta de datos | 978 | 835 | -15 % |
| | Recuento por clasificación | 103 | 132 | +28 % |
| | Recuento a lo largo del tiempo | 134 | 189 | +41 % |
| | Visitas por país | 199 | 259 | +30 % |
| | 15 organizaciones principales | 137 | 184 | +34 % |
| | Organizaciones de recuento único | 139 | 197 | +42 % |
| | Recuento de IP único | 510 | 604 | +18 % |
| | Recuento total de visitas | 89 | 134 | +51 % |
||||||
| DS4 | Ingesta de datos | 511 | 581 | +14 % |
| | Recuento por clasificación | 187 | 190 | +2 % |
| | Recuento a lo largo del tiempo | 411 | 409 | -0,5 % |
| | Visitas por país | 402 | 414 | +3 % |
| | 15 organizaciones principales | 307 | 284 | -7 % |
| | Organizaciones de recuento único | 320 | 313 | -2 % |
| | Recuento de IP único | 841 | 955 | +14 % |
| | Recuento total de visitas | 236 | 281 | +19 % |
||||||
| DS14 | Ingesta de datos | 511 | 571 | +12 % |
| | Recuento por clasificación | 201 | 232 | +15 % |
| | Recuento a lo largo del tiempo | 298 | 341 | +14 % |
| | Visitas por país | 363 | 457 | +26 % |
| | 15 organizaciones principales | 244 | 338 | +39 % |
| | Organizaciones de recuento único | 283 | 350 | +24 % |
| | Recuento de IP único | 681 | 909 | +33 % |
| | Recuento total de visitas | 200 | 245 | +23 % |

En la tabla siguiente se compara el número de operaciones de ingesta realizadas en las pruebas:

| Clúster | N.º de operaciones de ingesta; valores de documento habilitados | N.º de operaciones de ingesta: valores de documento deshabilitados | % de diferencia en las operaciones de ingesta |
|---------|----------------------------------------------|-----------------------------------------------|-----------------------------------------|
| D4 | 264769 | 408 690 | +54 % |
| DS4 | 503 137 | 578 237 | +15 % |
| DS14 | 502714 | 586 472 | +17 % |

Las tasas de ingesta mejoradas se producen con los valores de documento deshabilitados, dado que se escriben menos datos en el disco cuando se insertan los documentos. La mejora de rendimiento es especialmente apreciable con la máquina virtual D4 que utiliza unidades de disco duro para almacenar los datos. En este caso, el tiempo de respuesta de las operaciones de ingesta también disminuyó un 15 % (consulte la primera tabla de esta sección). Esto podría ser debido a la menor presión sobre las unidades de disco duro, que es probable que se ejecutaran cercanas a sus límites de IOPS en la prueba con valores de documento habilitados; consulte la prueba de tipo de disco para más información. El gráfico siguiente compara el rendimiento de E/S de las máquinas virtuales D4 con valores de documento habilitados (valores contenidos en el disco) y valores de documento deshabilitados (valores almacenados en memoria):

![](./media/guidance-elasticsearch/query-performance11.png)

***Figura 11. Actividad de disco en el clúster D4 con valores de documento habilitados y deshabilitados***

En cambio, los valores de ingesta en las máquinas virtuales que utilizan unidades SSD muestran un pequeño aumento en el número de documentos pero también en el tiempo de respuesta de las operaciones de ingesta. Con una o dos pequeñas excepciones, los tiempos de respuesta de consulta también fueron peores. Resulta menos probable que las unidades SSD se ejecuten cercanas a sus límites de IOPS con valores de documento habilitados, así que los cambios en el rendimiento es más probable que sean debidos al aumento en la actividad de procesamiento y a la sobrecarga de la administración del montón JVM. Esto es evidente si se compara la utilización de CPU con valores de documento habilitados y deshabilitados. El gráfico siguiente resalta estos datos para el clúster DS4, donde la mayoría de la utilización de CPU se mueve entre la banda de 30-40 % con valores de documento habilitados, y en la banda de 40-50 % con valores de documento deshabilitados (el clúster DS14 mostraba una tendencia similar):

![](./media/guidance-elasticsearch/query-performance12.png)

***Figura 12. Uso de CPU en el clúster DS4 con valores de documento habilitados y deshabilitados***

Para distinguir los efectos de los valores de documento sobre el rendimiento de consulta de la ingesta de datos, se realizaron pares de pruebas de solo consulta para los clústeres DS4 y DS14 con valores de documento habilitados y deshabilitados. En la tabla siguiente se resumen los resultados de estas pruebas:

| Clúster | Operación o consulta | Tiempo medio de respuesta (ms): valores de documento habilitados | Tiempo medio de respuesta (ms): valores de documento deshabilitados | % de diferencia en el tiempo de respuesta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Recuento por clasificación | 4489 | 3736 | -16 % |
| | Recuento a lo largo del tiempo | 7293 | 5459 | -25 % |
| | Visitas por país | 7564 | 5930 | -22 % |
| | 15 organizaciones principales | 5066 | 3874 | -14 % |
| | Organizaciones de recuento único | 5231 | 4483 | -2 % |
| | Recuento de IP único | 9228 | 9474 | +3 % |
| | Recuento total de visitas | 2180 | 1218 | -44 % |
||||||
| DS14 | Recuento por clasificación | 1927 | 2144 | +11 % |
| | Recuento a lo largo del tiempo | 4483 | 4337 | -3 % |
| | Visitas por país | 4761 | 4840 | +2 % |
| | 15 organizaciones principales | 2117 | 2302 | +9 % |
| | Organizaciones de recuento único | 2393 | 2497 | +4 % |
| | Recuento de IP único | 7159 | 7639 | +7 % |
| | Recuento total de visitas | 642 | 633 | -1 % |

Recuerde que, con Elasticsearch 2.0 y versiones posteriores, los valores de documento están habilitados de forma predeterminada. En las pruebas que abarcan el clúster DS4, la deshabilitación de los valores de documento parece tener un efecto positivo en general, mientras que lo contrario se cumple por lo general en el clúster DS14 (los dos casos en los que el rendimiento es mejor con valores de documento deshabilitados son muy marginales).

En el clúster DS4, el uso de CPU en ambos casos fue cercano al 100 % durante las dos pruebas, lo que indica que el clúster estaba enlazado a la CPU. Sin embargo, el número de consultas procesadas disminuyó de 7369 a 5894 (20 %); consulte la tabla siguiente. Recuerde que si los valores de documento están deshabilitados, Elasticsearch generará dinámicamente elementos fielddata en la memoria, lo que hará que se consuma energía de la CPU. Esta configuración reduce la tasa de E/S de disco, pero aumenta la sobrecarga sobre las CPU que ya se ejecutan cercanas a sus capacidades máximas. Así que, en este caso, las consultas son más rápidas con valores de documento deshabilitados, pero también es verdad que hay menos consultas.

En las pruebas del clúster DS14 con y sin valores de documento, la actividad de la CPU fue alta, pero no del 100 %. El número de consultas realizadas fue ligeramente mayor (aproximadamente un 4 %) en las pruebas con valores de documento habilitados:

| Clúster | Consultar | Número realizado: valores de documento habilitados | Número realizado: valores de documento deshabilitados |
|---------|----------------------------|---------------------------------------|----------------------------------------|
| DS4 | Recuento por clasificación | 1054 | 845 |
| | Recuento a lo largo del tiempo | 1054 | 844 |
| | Visitas por país | 1053 | 842 |
| | 15 organizaciones principales | 1055 | 846 |
| | Organizaciones de recuento único | 1051 | 839 |
| | Recuento de IP único | 1051 | 839 |
| | Recuento total de visitas | 1051 | 839  
||||| |
| DS14 | Recuento por clasificación | 1772 | 1842 |
| | Recuento a lo largo del tiempo | 1772 | 1839 |
| | Visitas por país | 1770 | 1838 |
| | 15 organizaciones principales | 1773 | 1842 |
| | Organizaciones de recuento único | 1769 | 1837 |
| | Recuento de IP único | 1768 | 1837 |
| | Recuento total de visitas | 1769 | 1837 |

## Resultados de rendimiento: caché de solicitud de particiones

Para demostrar cómo los datos de almacenamiento en caché del índice en la memoria de cada nodo pueden afectar al rendimiento, se realizó la prueba de *ingesta y consulta* en un clúster de seis nodos DS4 y DS14 con el almacenamiento en caché del índice habilitado; consulte la sección [Uso de la caché de solicitudes de partición](#using-the-shard-request-cache) para más información. Los resultados se compararon con los generados en las pruebas anteriores usando el mismo índice pero con el almacenamiento en caché del índice deshabilitado. En la tabla siguiente se resumen las diferencias. Tenga en cuenta que los datos se han limitado para incluir solo los primeros 90 minutos de la prueba; en este punto, era evidente la tendencia comparativa y continuar con la prueba probablemente no habría producido ninguna información adicional:

| Clúster | Operación o consulta | Tiempo medio de respuesta (ms): caché de índice deshabilitada | Tiempo medio de respuesta (ms): caché de índice habilitada | % de diferencia en el tiempo de respuesta |
|---------|----------------------------|---------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Ingesta de datos | 504 | 3260 | +547 % |
| | Recuento por clasificación | 218 | 273 | +25 % |
| | Recuento a lo largo del tiempo | 450 | 314 | -30 % |
| | Visitas por país | 447 | 397 | -11 % |
| | 15 organizaciones principales | 342 | 317 | -7 % |
| | Organizaciones de recuento único | 370 | 324 | -12 % |
| | Recuento de IP único | 760 | 355 | -53 % |
| | Recuento total de visitas | 258 | 291 | +12 % |
||||||
| DS14 | Ingesta de datos | 503 | 3365 | +569 % |
| | Recuento por clasificación | 234 | 262 | +12 % |
| | Recuento a lo largo del tiempo | 357 | 298 | -17 % |
| | Visitas por país | 416 | 383 | -8 % |
| | 15 organizaciones principales | 272 | 324 | -7 % |
| | Organizaciones de recuento único | 330 | 321 | -3 % |
| | Recuento de IP único | 674 | 352 | -48 % |
| | Recuento total de visitas | 227 | 292 | +29 % |

Estos datos muestran dos puntos de interés:

-  En primer lugar, las tasas de ingesta de datos parecen disminuir enormemente al habilitar el almacenamiento en caché del índice.

-  En segundo lugar, el almacenamiento en caché del índice no mejora el tiempo de respuesta de todos los tipos de consulta y puede tener un efecto negativo sobre determinadas operaciones de agregación, como las realizadas por las consultas Recuento por clasificación y Recuento total de visitas.
 

Para comprender por qué el sistema tiene este comportamiento, debe tener en cuenta el número de consultas realizadas correctamente en cada caso durante las ejecuciones de las pruebas. En la tabla siguiente se resumen estos datos:

| Clúster | Operación o consulta | N.º de operaciones y consultas: caché de índice deshabilitada | N.º de operaciones y consultas: caché de índice habilitada |
|---------|----------------------------|-------------------------------------------------|------------------------------------------------|
| DS4 | Ingesta de datos | 38 611 | 13 232 |
| | Recuento por clasificación | 524 | 18 704 |
| | Recuento a lo largo del tiempo | 523 | 18 703 |
| | Visitas por país | 522 | 18 702 |
| | 15 organizaciones principales | 521 | 18 706 |
| | Organizaciones de recuento único | 521 | 18 700 |
| | Recuento de IP único | 521 | 18 699 |
| | Recuento total de visitas | 521 | 18 701  
|||| |
| DS14 | Ingesta de datos | 38 769 | 12 835 |
| | Recuento por clasificación | 528 | 19 239 |
| | Recuento a lo largo del tiempo | 528 | 19 239 |
| | Visitas por país | 528 | 19 238 |
| | 15 organizaciones principales | 527 | 19 240 |
| | Organizaciones de recuento único | 524 | 19 234 |
| | Recuento de IP único | 524 | 19 234 |
| | Recuento total de visitas | 527 | 19 236 |

Puede ver que aunque la tasa de ingesta cuando el almacenamiento en caché estaba habilitado era aproximadamente un 1/3 de cuando estaba deshabilitado, el número de consultas realizadas aumentó en un factor de 34. Las consultas ya no consumen tanta E/S de disco y no tienen que competir por los recursos de disco. Esto se refleja en los gráficos de la figura 13, donde se compara la actividad de E/S en los cuatro casos:

![](./media/guidance-elasticsearch/query-performance13.png)

***Figura 13. Actividad de E/S de disco en la prueba de *ingesta y consulta* con el almacenamiento en caché del índice habilitado y deshabilitado***

La reducción en la E/S de disco también significaba que la CPU pasaba menos tiempo esperando a que se completara la E/S. Esto se resalta en la figura 14:

![](./media/guidance-elasticsearch/query-performance14.png)

***Figura 14. Tiempo de CPU dedicado a esperar a que la E/S de disco finalice en la prueba de *ingesta y consulta* con el almacenamiento en caché del índice deshabilitado y habilitado***

La reducción en la E/S de disco significaba que Elasticsearch podía emplear una proporción mucho mayor de su tiempo en atender las consultas de datos almacenados en memoria. Esto aumentaba el uso de CPU, lo que se hace evidente si observa el uso de CPU en los cuatro casos. En los gráficos siguientes se muestra cómo el uso de CPU era más continuado con el almacenamiento en caché habilitado:

![](./media/guidance-elasticsearch/query-performance15.png)

***Figura 15. Uso de CPU en la prueba de *ingesta y consulta* con el almacenamiento en caché del índice deshabilitado y habilitado***

El volumen de E/S de red en ambos escenarios durante las pruebas fue muy similar. Las pruebas sin almacenamiento en caché mostraron una degradación gradual del rendimiento durante el período de prueba, pero el período más largo de ejecución de estas pruebas, 24 horas, mostró que se estabilizaba en 2,75 GBps aproximadamente. En la imagen siguiente se muestran estos datos para los clústeres DS4 (los datos de los clústeres DS14 fueron muy similares):

![](./media/guidance-elasticsearch/query-performance16.png)

***Figura 16. Volúmenes de tráfico de red en la prueba de *ingesta y consulta* con el almacenamiento en caché deshabilitado y habilitado***

Como se describe en la prueba de [escalado vertical](#performance-results-scaling-up), las restricciones en el ancho de banda de red con máquinas virtuales de Azure no se han publicado y pueden variar, pero los niveles moderados de actividad de disco y de CPU sugieren que el uso de la red puede ser el factor limitador en este escenario.

El almacenamiento en caché resulta más adecuado para escenarios donde los datos no cambian con mucha frecuencia. Para destacar el impacto del almacenamiento en caché en este escenario, las pruebas de *solo consulta* se realizaron con el almacenamiento en caché habilitado. Los resultados se muestran a continuación (estas pruebas se ejecutaron durante 90 minutos y los índices sometidos a prueba contenían 100 millones de documentos):

| Clúster | Consultar | Tiempo de respuesta promedio (ms) | N.º de consultas realizadas |
|---------|----------------------------|----------------------------|-------------------------|
| | | **Caché deshabilitada** | **Caché habilitada** |
| DS4 | Recuento por clasificación | 4489 | 210 |
| | Recuento a lo largo del tiempo | 7292 | 211 |
| | Visitas por país | 7564 | 231 |
| | 15 organizaciones principales | 5066 | 211 |
| | Organizaciones de recuento único | 5231 | 211 |
| | Recuento de IP único | 9228 | 218 |
| | Recuento total de visitas | 2180 | 210  
|||| |
| DS14 | Recuento por clasificación | 1927 | 211 |
| | Recuento a lo largo del tiempo | 4483 | 219 |
| | Visitas por país | 4761 | 236 |
| | 15 organizaciones principales | 2117 | 212 |
| | Organizaciones de recuento único | 2393 | 212 |
| | Recuento de IP único | 7159 | 220 |
| | Recuento total de visitas | 642 | 211 |

La variación en el rendimiento de las pruebas sin almacenamiento en caché se debe a la diferencia en los recursos disponibles entre las máquinas virtuales DS4 y DS14. En ambos casos de la prueba de almacenamiento en caché, el tiempo medio de respuesta disminuía considerablemente dado que los datos se recuperaban directamente de la memoria. También merece la pena mencionar que los tiempos de respuesta de las pruebas de almacenamiento en caché realizadas en los clústeres DS4 y DS14 fueron muy similares a pesar de la disparidad con los resultados no almacenados en la caché. También hay muy poca diferencia entre los tiempos de respuesta de cada consulta dentro de cada prueba; todas tardan aproximadamente 220 ms. Las tasas de E/S de disco y la utilización de CPU en ambos clústeres fueron muy bajos dado que una vez que todos los datos están en memoria apenas se necesita E/S o procesamiento. La tasa de E/S de red fue similar a la de las pruebas sin almacenamiento en caché, lo que confirma que el ancho de banda de red puede ser un factor limitador en esta prueba. Los gráficos siguientes presentan esta información para el clúster DS4. El perfil del clúster DS14 fue muy similar:

![](./media/guidance-elasticsearch/query-performance17.png)

***Figura 17. E/S de disco, utilización de CPU y utilización de red en la prueba de *solo consulta* con el almacenamiento en caché del índice habilitado***

Las cifras de la tabla anterior sugieren que el uso de la arquitectura DS14 muestra pocas ventajas respecto al uso de la DS4. De hecho, el número de muestras generadas por el clúster DS14 fue aproximadamente de un 5 % menos que el del clúster DS4, pero esto también podría ser debido a que las restricciones de red pueden variar ligeramente con el tiempo.

## Resultados de rendimiento: número de particiones

El objetivo de esta prueba era determinar si el número de particiones creadas para un índice tiene alguna relación con el rendimiento de consulta de ese índice.

Pruebas independientes realizadas anteriormente mostraron que la configuración de particiones de un índice puede afectar a la tasa de ingesta de datos. Estas pruebas se describen en el documento [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximización del rendimiento de la ingesta de datos con Elasticsearch en Azure). Las pruebas realizadas para determinar el rendimiento de consulta seguían una metodología similar, pero se restringieron a un clúster de seis nodos que se ejecutaba en hardware DS14. Este enfoque ayuda a minimizar el número de variables, de modo que cualquier diferencia en el rendimiento se debe atribuir al volumen de particiones.

La prueba de *solo consulta* se llevó a cabo en copias del mismo índice configurado con 7, 13, 23, 37 y 61 particiones principales. El índice contenía 100 millones de documentos y tenía una única réplica, que duplicaba el número de particiones en el clúster. Cada prueba se ejecutó durante 90 minutos. En la tabla siguiente se resumen los resultados. El tiempo medio de respuesta que se muestra es el tiempo de respuesta en la transacción de prueba JMeter que abarca el conjunto completo de consultas realizadas en cada iteración de la prueba. Consulte la nota de la sección [Resultados de rendimiento: escalado vertical](#performance-results-scaling-up) para más información:

| Número de particiones | Diseño de particiones (particiones por nodo, réplicas incluidas) | Número de consultas realizadas | Tiempo medio de respuesta (ms) |
|---------------------------|----------------------------------------------------|-----------------------------|------------------------|
| 7 (14, incluidas las réplicas) | 3-2-2-2-2-3 | 7461 | 40524 |
| 13 (26) | 5-4-5-4-4-4 | 7369 | 41 055 |
| 23 (46) | 7-8-8-7-8-8 | 14 193 | 21 283 |
| 37 (74) | 13-12-12-13-12-12 | 13 399 | 22 506 |
| 61 (122) | 20-21-20-20-21-20 | 14 743 | 20 445 |

Estos resultados indican que hay una diferencia importante en el rendimiento entre el clúster de 13(26) particiones y el clúster de 23(46) particiones, y es que el rendimiento casi se duplica y los tiempos de respuesta se reducen a la mitad. Esto se deba probablemente a la configuración de las máquinas virtuales y las estructuras que utiliza Elasticsearch para procesar las solicitudes de búsqueda. Las solicitudes de búsqueda se ponen en cola y cada una es administrada por un solo subproceso de búsqueda. El número de subprocesos de búsqueda creados por un nodo de Elasticsearch es una función del número de procesadores disponibles en el equipo que hospeda el nodo. Los resultados sugieren que con solo cuatro o cinco particiones en un nodo, los recursos de procesamiento no se utilizan completamente. Esto se puede confirmar si se examina la utilización de CPU mientras se ejecuta esta prueba. La siguiente imagen es una instantánea tomada de Marvel mientras se realizaba la prueba de 13(26) particiones:

![](./media/guidance-elasticsearch/query-performance18.png)

***Figura 18. Uso de CPU en la prueba de *solo consulta* en el clúster de 7(14) particiones***

Compare estas cifras con las de la prueba de 23(46) particiones:

![](./media/guidance-elasticsearch/query-performance19.png)

***Figura 19. Uso de CPU en la prueba de *solo consulta* en el clúster de 23(46) particiones***

En la prueba de 23(46) particiones, la utilización de CPU fue bastante más alta. Cada nodo contiene particiones siete u ocho particiones. La arquitectura DS14 proporciona 16 procesadores y Elasticsearch puede aprovechar mucho mejor este número de núcleos con las particiones adicionales. Las cifras de la tabla anterior sugieren que el aumento del número de particiones por encima de este punto puede mejorar ligeramente el rendimiento, pero debe compensar estas cifras frente a la sobrecarga adicional de mantener un gran volumen de particiones. De estas pruebas se deduce que el número óptimo de particiones por nodo equivale a la mitad del número de núcleos de procesador disponibles en cada nodo. Sin embargo, recuerde que estos resultados se obtuvieron al ejecutar solo consultas. Si el sistema importa los datos, también debe tener en cuenta cómo el particionamiento puede afectar al rendimiento de las operaciones de ingesta de datos. Para más información sobre este aspecto, consulte el documento [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximización del rendimiento de la ingesta de datos con Elasticsearch en Azure).

## Resumen

Elasticsearch proporciona muchas opciones que puede utilizar para estructurar índices y ajustarlos para permitir operaciones de consulta a gran escala. En este documento se resumen algunas configuraciones y técnicas comunes que puede utilizar para optimizar la base de datos con fines de consulta. Sin embargo, debe reconocer que hay que elegir entre optimizar una base de datos para admitir una recuperación más rápida o admitir la ingesta de un alto volumen de datos. En ocasiones, lo que es bueno para las consultas puede tener un efecto negativo sobre las operaciones de inserción y viceversa. En un sistema que se expone a cargas de trabajo mixtas, debe evaluar dónde se encuentra el equilibrio y ajustar los parámetros del sistema en consonancia.

Además, la posibilidad de aplicación de distintas configuraciones y técnicas puede variar dependiendo de la estructura de los datos y de las limitaciones (o no) del hardware en el que se construye el sistema. Muchas de las pruebas que se muestran en este documento ilustran cómo la selección de la plataforma de hardware puede afectar al rendimiento, y también cómo algunas estrategias pueden ser beneficiosas en ciertos casos, pero perjudiciales en otros. Lo importante es comprender las opciones disponibles y luego realizar rigurosas pruebas comparativas con sus propios datos para determinar la combinación más óptima.

Por último, recuerde que una base de datos de Elasticsearch no es necesariamente un elemento estático. Probablemente crecerá con el tiempo, así que puede ser necesario revisar periódicamente las estrategias que se utilizan para estructurar los datos. Por ejemplo, puede que sea necesario escalar verticalmente, escalar horizontalmente o volver a indexar los datos con particiones adicionales. A medida que el sistema aumenta de tamaño y complejidad, prepárese para probar continuamente el rendimiento a fin de asegurarse de que todavía cumple los contratos de nivel de servicio garantizados a sus clientes.

## Apéndice: la prueba de rendimiento de agregación y consulta

Este apéndice describe la prueba de rendimiento realizada en el clúster de Elasticsearch. Para realizar las pruebas, se ejecutó JMeter en un conjunto diferente de máquinas virtuales. Para obtener información detallada sobre la configuración del entorno de prueba, consulte el documento sobre la creación de un entorno de pruebas de rendimiento para Elasticsearch. Para realizar sus propias pruebas, puede crear su propio plan de pruebas de JMeter manualmente siguiendo las instrucciones de este apéndice, o puede usar los scripts de prueba automatizados disponibles aparte. Para más información, consulte el documento How-To: Run the Automated Elasticsearch Query Tests (Ejecución de las pruebas de consulta automatizadas de Elasticsearch).

La carga de trabajo de consulta de datos llevó a cabo el conjunto de consultas que se describe a continuación mediante la realización de una carga de documentos a gran escala al mismo tiempo (los datos se cargaron mediante una prueba JUnit, siguiendo el mismo enfoque de las pruebas de ingesta de datos que se describe en el documento [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximización del rendimiento de la ingesta de datos con Elasticsearch en Azure). El propósito de esta carga de trabajo era simular un entorno de producción donde se agregan datos nuevos constantemente mientras se realizan las búsquedas. Las consultas se estructuraron para recuperar solo los datos más recientes de los documentos agregados en los últimos 15 minutos.

Cada documento se almacenó en un único índice denominado *systembase*, y tenía el tipo *logs*. Puede usar la siguiente solicitud HTTP para crear el índice. Los valores de *number\_of\_replicas* y *number\_of\_shards* variaron con respecto a los valores que se muestran a continuación en muchas de las pruebas. Además, en las pruebas que se utilizaron fielddata en lugar de valores de documento, cada propiedad se anotaba con el atributo *"doc\_values": false*.

> **Importante**. El índice se quitó y se volvió a crear antes de cada ejecución de pruebas.

``` http
PUT /idx
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "doc": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}
```

Se realizaron las siguientes consultas en la prueba:
* ¿Cuántos documentos con cada valor de Rating se han incluido en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "bool": {
        "must": [
          {
            "range": {
              "DateTimeReceivedUtc": {
                "gte": "now-15m",
                "lte": "now"
              }
            }
          }
        ],
        "must_not": [],
        "should": []
      }
    },
    "from": 0,
    "size": 0,
    "aggs": {
      "2": {
        "terms": {
          "field": "Rating",
          "size": 5,
          "order": {
            "_count": "desc"
          }
        }
      }
    }
  }
  ```

* ¿Cuántos documentos se agregaron en cada intervalo de 5 minutos durante los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "bool": {
        "must": [
          {
            "range": {
              "DateTimeReceivedUtc": {
                "gte": "now-15m",
                "lte": "now"
              }
            }
          }
        ],
        "must_not": [],
        "should": []
      }
    },
    "from": 0,
    "size": 0,
    "sort": [],
    "aggs": {
      "2": {
        "date_histogram": {
          "field": "DateTimeReceivedUtc",
          "interval": "5m",
          "time_zone": "America/Los_Angeles",
          "min_doc_count": 1,
          "extended_bounds": {
            "min": "now-15m",
            "max": "now"
          }
        }
      }
    }
  }
  ```

* ¿Cuántos documentos de cada valor de Rating se agregaron para cada país en los últimos 15 minutos?

  ```HTTP
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "terms": {
          "field": "Rating",
          "size": 5,
          "order": {
            "_count": "desc"
          }
        },
        "aggs": {
          "3": {
            "terms": {
              "field": "SourceIpCountryCode",
              "size": 15,
              "order": {
                "_count": "desc"
              }
            }
          }
        }
      }
    }
  }
  ```

* ¿Qué 15 organizaciones aparecen con más frecuencia en los documentos agregados en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "terms": {
          "field": "Organization",
          "size": 15,
          "order": {
            "_count": "desc"
          }
        }
      }
    }
  }
  ```

* ¿Cuántas organizaciones distintas aparecen en los documentos agregados en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "cardinality": {
          "field": "Organization"
        }
      }
    }
  }
  ```

* ¿Cuántos documentos se agregaron en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "analyze_wildcard": true,
                    "query": "*"
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {}
  }
  ```

* ¿Cuántos valores de SourceIp diferentes aparecen en los documentos agregados en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "cardinality": {
          "field": "SourceIp"
        }
      }
    }
  }
  ```

<!---HONumber=AcomDC_0302_2016-->