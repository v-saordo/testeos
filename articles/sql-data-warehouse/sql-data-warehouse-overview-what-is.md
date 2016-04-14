<properties
   pageTitle="Qué es Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Base de datos distribuida de clase empresarial, capaz de procesar volúmenes de petabytes de datos relacionales y no relacionales. Es el primer almacenamiento de datos en la nube de la industria que aumenta, reduce y hace una pausa en segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# ¿Qué es Almacenamiento de datos SQL de Azure?

El Almacenamiento de datos SQL de Azure es una base de datos distribuida de clase empresarial, capaz de procesar volúmenes masivos de datos relacionales y no relacionales. Es el primer almacenamiento de datos en la nube del sector que combina unas capacidades de SQL demostradas con la posibilidad de aumentar, reducir y hacer una pausa en segundos. El Almacenamiento de datos SQL está también profundamente enraizado en Azure y se implementa fácilmente en segundos. Además, el servicio está totalmente administrado y evita tener que perder el tiempo en revisiones de software, mantenimiento y realización de copias de seguridad. Las copias de seguridad integradas y automáticas del Almacenamiento de datos SQL admiten tolerancia a errores y autoservicio de restauración.

Cuando hemos creado el Almacenamiento de datos SQL, nos hemos centrado en algunos atributos clave para asegurarnos de sacar el máximo partido de Azure y crear un almacenamiento de datos que pueda satisfacer cualquier carga de trabajo de la empresa.

## Optimizado

### Arquitectura de almacenamiento de datos

En su núcleo, el Almacenamiento de datos SQL usa la arquitectura de procesamiento paralelo masivo (MPP) de Microsoft, que se diseñó originalmente para ejecutar algunos de los mayores almacenamientos de datos empresariales locales. Esta arquitectura aprovecha las mejoras en el rendimiento del almacenamiento de datos integrado, y permite también al Almacenamiento de datos SQL escalar y paralelizar con facilidad el proceso de consultas SQL complejas. Además, la arquitectura del Almacenamiento de datos SQL está diseñada para sacar partido de su presencia en Azure. Al combinarse estos dos aspectos, la arquitectura consta de cuatro componentes clave:

![Arquitectura de Almacenamiento de datos SQL][1]

- **Nodo de control:** se conecta al nodo de control al usar el Almacenamiento de datos SQL con cualquier herramienta de inteligencia empresarial, carga o desarrollo. En el Almacenamiento de datos SQL, el nodo de control es una Base de datos SQL que, al conectarse, adopta la apariencia de una Base de datos SQL estándar. Sin embargo, bajo la superficie, coordina todo el movimiento de datos y los procesos que se llevan a cabo en el sistema. Cuando se emite un comando al nodo de control, se divide en un conjunto de consultas que se pasarán a los nodos de proceso del servicio.

- **Nodos de proceso:** al igual que el nodo de control, los nodos de proceso del Almacenamiento de datos SQL se activan mediante Bases de datos SQL. Su función es servir como capacidad de proceso del servicio. En segundo plano, cada vez que se cargan datos en el Almacenamiento de datos SQL, se distribuyen entre los nodos del servicio. A continuación, cada vez que el nodo de control recibe un comando, lo divide en partes para los nodos de proceso, que operan sobre los datos correspondientes. Después de completar su proceso, los nodos de proceso pasan los resultados parciales al nodo de control que combina, a continuación, los resultados antes de devolver una respuesta.

- **Almacenamiento:** todo el almacenamiento para el Almacenamiento de datos SQL está constituido por Blobs de almacenamiento de Azure estándar. Esto significa que al interactuar con datos, los nodos de proceso escriben y leen directamente en los Blobs. La capacidad de almacenamiento de Azure de expandirse de forma transparente y casi ilimitada nos permite cambiar automáticamente la escala de almacenamiento y hacerlo de forma separada del proceso. El Almacenamiento de Azure también permite hacer que el almacenamiento sea persistente al escalarlo o ponerlo en pausa, simplificar el proceso de copia de seguridad y restauración, y tener un almacenamiento más seguro y tolerante a errores.

- **Servicios de movimiento de datos:** el último elemento que lo mantiene todo unido en el Almacenamiento de datos SQL son nuestros servicios de movimiento de datos. Los servicios de movimiento de datos permiten que el nodo de control se comunique con todos los nodos de proceso y les pase datos. También permite que los nodos de proceso se pasen datos entre sí, lo que les proporciona acceso a los datos de otros nodos de proceso y les permite obtener los datos que necesitan para realizar combinaciones y agregaciones.

### Optimizaciones de motor

Este enfoque MPP permite al Almacenamiento de datos SQL usar un enfoque "divide y vencerás", tal como se describió anteriormente al solucionar los problemas que plantean los grandes volúmenes de datos. Como los datos del Almacenamiento de datos SQL se dividen y distribuyen entre los nodos de proceso del servicio, cada nodo de proceso es capaz de operar en su parte de datos en paralelo. Por último, los resultados se pasan al nodo de control y se agregan antes de pasarlos a los usuarios. A este enfoque también contribuyen algunas optimizaciones del rendimiento específicas del almacenamiento de datos:

- El Almacenamiento de datos SQL usa un optimizador de consultas avanzadas y un conjunto de estadísticas complejas en todos los datos del servicio para crear sus planes de consulta. Gracias a la información sobre la distribución y el tamaño de los datos, el servicio es capaz de optimizar las consultas distribuidas en función de la evaluación del costo de las operaciones de consulta específicas.

- Además de crear planes de consulta óptimos, el Almacenamiento de datos SQL incorpora algoritmos y técnicas avanzados que mueven los datos entre los recursos de proceso, según sea necesario, para realizar la consulta de forma eficaz. Estas operaciones están integradas en los servicios de movimiento de datos del almacén de datos y las optimizaciones se producen automáticamente.

- La inclusión de índices de almacén de columnas agrupados en Almacenamiento de datos SQL es también fundamental para conseguir un rendimiento rápido de las consultas. Mediante el uso del almacenamiento basado en columnas, el Almacenamiento de datos SQL obtiene hasta 5 veces más ganancias de compresión que el almacenamiento tradicional basado en filas y hasta 10 veces el rendimiento de la consulta. Las consultas del almacenamiento de datos funcionan a la perfección en índices de almacén de columnas porque a menudo examinan toda la tabla o la partición entera de una tabla y minimizan el impacto que provoca el movimiento de datos para pasos de consulta.

## Escalable

La arquitectura del Almacenamiento de datos SQL presenta el proceso y el almacenamiento por separado, lo que permite a cada uno escalar de manera independiente. La estructura de implementación rápida y sencilla de la Base de datos SQL permite disponer de procesos adicionales en un instante. Como complemento, está el uso de Blobs de almacenamiento de Azure. Los blobs no solo nos ofrecen un almacenamiento estable y replicado, sino que proporcionan igualmente la infraestructura para una expansión más fluida y menos costosa. Mediante esta combinación de almacenamiento de escala en la nube y de proceso de Azure, el Almacenamiento de datos SQL le permite pagar por un almacenamiento de rendimiento de consultas conforme lo necesita cuando lo necesita. Cambiar la cantidad del proceso es tan fácil como mover un control deslizante en el Portal de Azure clásico hacia la izquierda o la derecha, aunque también se puede programar o agregar a una carga de trabajo con T-SQL y PowerShell.

Junto con la capacidad de controlar totalmente la cantidad del proceso con independencia del almacenamiento, el Almacenamiento de datos SQL le permite realizar una pausa completa en el almacenamiento de datos. A la vez que mantiene el almacenamiento en su sitio, se lanzan todos los procesos al grupo principal de Azure, lo que supone un ahorro de dinero inmediato. Cuando sea necesario, solo tendrá que reanudar el proceso y tener los datos y el proceso disponibles para su carga de trabajo.

El uso de proceso en el Almacenamiento de datos SQL se mide en unidades de almacenamiento de datos (DWU) SQL. Los DWU son una medición de la potencia subyacente que tiene su almacenamiento de datos y están diseñados para garantizar que tenga una cantidad de rendimiento estándar asociada al almacenamiento en un momento dado. En concreto, usamos DWU para asegurarnos de que:

- Puede escalar de forma eficaz el almacenamiento de datos sin preocuparse por el hardware o software subyacente.

- Puede entender el rendimiento que verá en un nivel de DWU antes de cambiar el tamaño del almacenamiento de datos.

- Puede cambiar o mover el hardware y software subyacentes de la instancia sin que esto afecte al rendimiento de su carga de trabajo.

- Podemos hacer ajustes en la arquitectura subyacente del servicio sin que el rendimiento de la carga de trabajo se vea afectado.

- Según vamos mejorando rápidamente el rendimiento en el Almacenamiento de datos SQL, podemos garantizar que lo hacemos de forma que sea escalable y que afecte de manera uniforme al sistema.

### Unidades de almacenamiento de datos

En concreto, consideramos las unidades de almacenamiento de datos como una medición de tres métricas precisas que pensamos que están muy correlacionadas con el rendimiento de la carga de trabajo del almacenamiento de datos. Nuestro objetivo es que, para la disponibilidad general, estas métricas clave de carga de trabajo aumentarán linealmente con las DWU que ha elegido para el almacenamiento de datos.

**Examen y agregación:** esta métrica de carga de trabajo toma una consulta de almacenamiento de datos estándar que examina un gran número de filas y realiza después una agregación compleja. Se trata de una operación con un gran consumo de E/S y CPU.

**Carga:** esta métrica mide la capacidad de insertar datos en el servicio. Las cargas se completan con PolyBase que carga un conjunto de datos representativo de un Blob de almacenamiento de Azure. Esta métrica está diseñada para resaltar los aspectos de red y de CPU del servicio.

**CREATE TABLE AS SELECT (CTAS):** CTAS mide la capacidad de crear una copia de una tabla. Esto implica la lectura de datos del almacenamiento, su distribución entre los nodos del dispositivo y su escritura de nuevo en el almacenamiento. Se trata de una operación con un gran consumo de CPU y red.

### Cuándo se debe escalar

En general, queremos que las DWU sean sencillas. Cuando necesite resultados más rápidos, aumente el número de DWU y pague por un mayor rendimiento. Cuando necesite menos capacidad de proceso, reduzca el número de DWU y pague solo por lo que necesita. El momento para pensar en cambiar el número de DWU puede ser uno de los siguientes:

- Cuando no es necesario ejecutar consultas, tal vez por la noche o los fines de semana, ponga en pausa los recursos de proceso para cancelar todas las consultas en ejecución y eliminar todas las DWU asignadas a su almacenamiento de datos.

- Al realizar una operación de carga o de transformación de gran cantidad de datos, quizá desee escalar verticalmente para que los datos estén disponibles con mayor rapidez.

- Para entender el valor ideal de su DWU, intente escalar verticalmente hacia arriba y hacia abajo, y ejecute algunas consultas después de cargar los datos. Puesto que el escalado se realiza rápidamente, puede probar distintos niveles de rendimiento sin dedicarle más de una hora.

> [AZURE.NOTE] Tenga en cuenta que debido a la arquitectura o al Almacenamiento de datos SQL, puede que no vea el escalado del rendimiento esperado en volúmenes de datos más reducidos. Se recomienda comenzar con volúmenes de datos a partir de 1 TB o más a fin de obtener unos resultados de pruebas de rendimiento exactos.

## Integrado

El Almacenamiento de datos SQL se basa en el motor de base de datos relacional probado de SQL Server e incluye muchas de las características que se esperan de un almacenamiento de datos empresarial. Si ya conoce Transact-SQL, le resultará muy sencillo transferir sus conocimientos a Almacenamiento de datos SQL. Ya sea un usuario avanzado o principiante, los ejemplos que aparecen en la documentación le servirán de apoyo para comenzar. En general, puede pensar en la forma en que hemos construido los elementos de lenguaje del Almacenamiento de datos SQL del modo siguiente:

- El Almacenamiento de datos SQL usa la sintaxis de Transact-SQL (TSQL) de SQL Server para numerosas operaciones y admite un amplio conjunto de construcciones SQL tradicionales como procedimientos almacenados, funciones definidas por el usuario, partición de tablas, índices e intercalaciones.

- El Almacenamiento de datos SQL también contiene una serie de características innovadoras de SQL Server como índices de almacén de columnas en clúster, integración de PolyBase y auditoría de datos (completa con valoración de amenazas).

- Como el Almacenamiento de datos SQL está aún en desarrollo, es posible que algunos elementos del lenguaje TSQL que son menos habituales en las cargas de trabajo de almacenamiento de datos o son más recientes en SQL Server no estén disponibles en este momento. Consulte la documentación sobre migración para obtener más información relativa a este tema.

Con la uniformidad de Transact-SQL y las características comunes existentes entre SQL Server, Almacenamiento de datos SQL, Base de datos SQL y Sistema de plataforma de análisis, es posible desarrollar una solución que se ajuste a las necesidades de los datos. Puede decidir dónde guardar los datos, basándose en los requisitos de escala, seguridad y rendimiento y, a continuación, transferir los datos según sea necesario entre distintos sistemas.

Además de adoptar el área expuesta de TSQL de SQL Server, el Almacenamiento de datos SQL también se integra con numerosas herramientas ya conocidas por los usuarios de SQL Server. En concreto, nos hemos centrado en integrar algunas categorías de herramientas con el Almacenamiento de datos SQL, entre ellas:

**Herramientas tradicionales de SQL Server:** hay una integración completa con SQL Server Analysis Services, Integration Services y Reporting Services disponible con el Almacenamiento de datos SQL.

**Herramientas basadas en la nube:** el Almacenamiento de datos SQL se puede usar junto algunas herramientas nuevas de Azure y presenta una profunda integración con Factoría de datos de Azure, análisis de transmisiones, aprendizaje automático y Power BI.

**Herramientas de terceros:** un gran número de proveedores de herramientas de terceros ha certificado la integración de sus herramientas con el Almacenamiento de datos SQL. Consulte la lista completa.

## Híbrido

El uso del Almacenamiento de datos SQL con PolyBase proporciona a los usuarios una capacidad sin precedentes para mover datos en su ecosistema, lo que libera la capacidad de configurar escenarios híbridos avanzados con orígenes de datos no relacionales y locales.

Polybase es fácil de usar y permite aprovechar datos de orígenes diferentes con los mismos comandos conocidos de T-SQL. Polybase permite consultar los datos no relacionales que se mantienen en el Almacenamiento de blobs de Azure al igual que una tabla normal. Utilice Polybase para consultar los datos no relacionales o para importar datos no relacionales en Almacenamiento de datos SQL.

- PolyBase utiliza tablas externas para tener acceso a datos no relacionales. Las definiciones de tabla se almacenan en el Almacenamiento de datos SQL, y SQL y las herramientas pueden acceder a ellas del mismo modo que se accede a datos relacionales normales.

- Polybase es independiente en su integración. Presenta las mismas características y funcionalidad para todos los orígenes compatibles. Los datos leídos por Polybase pueden estar en una variedad de formatos, incluidos archivos delimitados o ORC.

- PolyBase se puede usar para acceder al Almacenamiento de blobs, que sirve también como almacenamiento para un clúster de HD Insight, lo que proporciona un acceso innovador a los mismos datos con herramientas relacionales y no relacionales.


## Pasos siguientes

Ahora que ya conoce un poco el Almacenamiento de datos SQL, obtenga información acerca de la [carga de trabajo de almacenamiento de datos], el [aprovisionamiento] y la carga de [datos de ejemplo] para comenzar.

>[AZURE.NOTE] Queremos mejorar este artículo. Si elige responder que no a la pregunta de si le resultó útil este artículo, incluya una sugerencia breve sobre lo que falta o cómo piensa que se podría mejorar el artículo. Gracias de antemano.

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[carga de trabajo de almacenamiento de datos]: ./sql-data-warehouse-overview-workload.md
[datos de ejemplo]: ./sql-data-warehouse-get-started-load-samples.md
[aprovisionamiento]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->