<properties
   pageTitle="Guía de creación de particiones de datos | Microsoft Azure"
   description="Orientación sobre cómo separar las particiones para administrar y usar por separado."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/20/2015"
   ms.author="masashin"/>

# Guía de creación de particiones de datos

![](media/best-practices-data-partitioning/pnp-logo.png)

## Información general

En muchas soluciones a gran escala, los datos se dividen en particiones independientes que se pueden administrar y a las que se puede acceder por separado. Debe elegir cuidadosamente la estrategia de partición para maximizar los beneficios y minimizar los efectos adversos. La creación de particiones puede ayudar a mejorar la escalabilidad, a reducir la contención y a optimizar el rendimiento. Un beneficio colateral de la creación de particiones es que también puede proporcionar un mecanismo para dividir los datos por el patrón de uso; puede archivar datos más antiguos e inactivos (fríos) en un almacenamiento de datos más barato.

## ¿Por qué es recomendable crear particiones de datos?

La mayoría de los servicios y aplicaciones de nube almacenan y recuperan datos como parte de sus operaciones. El diseño de los almacenes de datos que utiliza una aplicación puede tener una incidencia significativa en el rendimiento y la escalabilidad de un sistema. Una técnica que se aplica normalmente en sistemas de gran escala es dividir los datos en particiones independientes.

> El término _creación de particiones_ usado en esta guía hace referencia al proceso de dividir físicamente los datos en almacenes de datos independientes. Esto no es el mismo que la creación de particiones de tablas de SQL Server, que es un concepto diferente.

Crear particiones de datos puede ofrecer una serie de ventajas. Por ejemplo, se puede aplicar para:

- **Mejorar la escalabilidad**. El escalado de un sistema de base de datos única permitirá alcanzar finalmente un límite de hardware físico. Dividir datos en varias particiones, cada una de las cuales se encuentra hospedada en un servidor independiente, permite al sistema escalar de manera casi indefinida.
- **Mejorar el rendimiento**. Las operaciones de acceso a datos en cada partición se realizan en un volumen de datos menor. Siempre que se cree una partición de los datos de forma adecuada, esto resulta mucho más eficaz. Las operaciones que afectan a más de una partición pueden ejecutarse en paralelo. Cada partición puede estar cerca de la aplicación que la utiliza para minimizar la latencia de red.
- **Mejorar la disponibilidad**. Separar los datos en varios servidores evita un punto único de error. Si se produce un error en un servidor o están realizando realizando tareas de mantenimiento planeada, solo estarán disponibles los datos de esa partición. Las operaciones en otras particiones pueden continuar. Aumentar el número de particiones reduce el impacto relativo de un error de servidor único reduciendo el porcentaje de los datos que no van a estar disponibles. Replicar cada partición puede reducir todavía más la posibilidad de que se produzca un error en una única partición que afecte a las operaciones. También permite la separación de datos críticos que deben presentar una alta disponibilidad de manera continua desde los datos de valor bajo (por ejemplo, archivos de registro) que presentan requisitos de disponibilidad inferiores.
- **Mejorar la seguridad**. Según la naturaleza de los datos y de cómo estén particionados, es posible separar los datos confidenciales y no confidenciales en particiones distintas y, por tanto, en diferentes servidores o almacenes de datos. Por lo tanto, es posible optimizar la seguridad de manera específica para los datos confidenciales.
- **Proporcionan flexibilidad operativa**. La creación de particiones ofrece muchas oportunidades para ajustar con precisión las operaciones, maximizar la eficacia administrativa y minimizar los costes. Algunos ejemplos son la definición de distintas estrategias de administración, supervisión, copias de seguridad y restauración y otras tareas administrativas en función de la importancia de los datos de cada partición.
- **Adaptación del almacén de datos al patrón de uso**. La creación de particiones permite la implementación de cada partición en un tipo de almacén de datos diferente en función de costo y las características integradas que ofrece el almacén de datos. Por ejemplo, podrían almacenarse datos binarios de grandes dimensiones en un almacén de datos blob, aunque se podrían mantener datos más estructurados en una base de datos de documentos. Para obtener más información, consulte [Creación de una solución de Polyglot] en la guía de patrones y prácticas [Acceso a datos para soluciones altamente escalables: mediante SQL, NoSQL y persistencia Polyglot] en el sitio web de Microsoft.

Algunos sistemas no implementan la creación de particiones porque se considera una sobrecarga en lugar de una ventaja. Entre las causas más comunes de este razonamiento se incluyen:

- Muchos sistemas de almacenamiento de datos no admiten combinaciones entre las particiones, y es posible que resulte difícil de mantener la integridad referencial en un sistema con particiones. A menudo es necesario implementar combinaciones y comprobaciones de integridad en el código de la aplicación (en la capa de partición), que puede dar lugar a E/S adicionales y al aumento de la complejidad de la aplicación.
- El mantenimiento de las particiones no siempre es una tarea trivial. En un sistema en el que los datos son volátiles, puede que necesite volver a equilibrar las particiones periódicamente para reducir la contención y las zonas activas.
- Algunas herramientas comunes no funcionan de manera natural con datos con particiones.

## Diseño de particiones

Los datos se pueden dividir en particiones de maneras diferentes: horizontal, vertical o funcionalmente. La estrategia que elija depende de la razón por la que efectúa la partición de los datos y los requisitos de las aplicaciones y servicios que usarán los datos.

> [AZURE.NOTE]Los esquemas de creación de particiones descritos en esta guía se explican de manera independiente a la tecnología de almacenamiento de datos subyacente. Se pueden aplicar a muchos tipos de almacenes de datos, incluidas las bases de datos relacionales y NoSQL.

### Estrategias de creación de particiones

Las tres estrategias típicas de creación de particiones de datos son:

- **Particiones horizontales** (a menudo denominadas _particionamiento_). En esta estrategia, cada partición es un almacén de datos por derecho propio, pero todas las particiones tienen el mismo esquema. Cada partición se conoce como _partición_ y contiene un subconjunto específico de los datos, como todos los pedidos de un conjunto específico de clientes en una aplicación de comercio electrónico.
- **Particiones verticales**. En esta estrategia cada partición contiene un subconjunto de los campos de elementos del almacén de datos. Los campos se dividen según su patrón de uso, como la colocación de los campos a los que se accede con frecuencia en una partición vertical y los campos a los que se accede con menos frecuencia en otro.
- **Creación de particiones funcional**. En esta estrategia, los datos se agregan en función de cómo los usa cada contexto limitado en el sistema. Por ejemplo, un sistema de comercio electrónico que implementa funciones empresariales independientes para la facturación y administración de inventarios de productos puede almacenar datos de la factura en una partición y datos del inventario de productos en la otra.

Es importante tener en cuenta que se pueden combinar las tres estrategias que se describen aquí. No son mutuamente excluyentes y debe considerarlas todas cuando se diseña un esquema de creación de particiones. Por ejemplo, puede dividir los datos en particiones y, a continuación, usar particiones verticales para subdividir todavía más los datos de cada partición. De forma similar, los datos de una partición funcional se pueden dividir en particiones (que pueden también dividirse verticalmente).

Sin embargo, los diferentes requisitos de cada estrategia pueden plantear una serie de problemas en conflicto que debe evaluar y equilibrar al diseñar un esquema de partición que cumpla los objetivos de rendimiento de procesamiento de datos globales para su sistema. Las secciones siguientes exploran cada una de las estrategias más detalladamente.

### Creación de particiones horizontales (particionamiento)

En la figura 1 se muestra una visión general de la creación de particiones horizontales o particionamiento. En este ejemplo, los datos del inventario de productos se dividen en particiones según la clave del producto. Cada partición contiene los datos de un intervalo contiguo de claves de partición (A-G y H-Z), organizadas alfabéticamente.

![](media/best-practices-data-partitioning/DataPartitioning01.png)

_Figura 1. - Creación de particiones horizontales de datos (particionamiento) basadas en una clave de partición_

El particionamiento le permite distribuir la carga entre más equipos, lo cual reduce la contención y mejora el rendimiento. Puede escalar el sistema agregando más particiones que se ejecutan en servidores adicionales.

El factor más importante al implementar esta estrategia de partición es la opción de clave de particionamiento. Puede resultar difícil cambiar la clave después de que el sistema está en funcionamiento. La clave debe asegurarse de que los datos están divididos en particiones para que la carga de trabajo sea lo más uniforme posible entre las particiones. Tenga en cuenta que las distintas particiones no tienen que contener volúmenes de datos similares. En su lugar, lo importante es equilibrar el número de solicitudes; algunas particiones pueden ser muy grandes, pero cada elemento es objeto de un número reducido de operaciones de acceso, mientras que otras particiones pueden ser menores, pero se tiene acceso con más frecuencia a cada elemento. También es importante asegurarse de que una sola partición no supere los límites de escala (en términos de capacidad y recursos de procesamiento) del almacén de datos que se utiliza para hospedar esa partición.

El esquema de particionamiento debe evitar también la creación de zonas activas (o particiones calientes) que pueden afectar al rendimiento y la disponibilidad. Por ejemplo, mediante un algoritmo hash de un identificador de cliente en lugar de la primera letra del nombre de un cliente evitará una distribución desequilibrada que se obtendría del uso de letras iniciales comunes y menos comunes. Se trata de una técnica habitual que ayuda a distribuir los datos de forma más equitativa entre las particiones.

La clave de particionamiento que elija debería minimizar cualquier requisito futuro para dividir particiones grandes en partes más pequeñas, fusionar particiones pequeñas en particiones mayores o cambiar el esquema que describe los datos almacenados en un conjunto de particiones. Estas operaciones pueden llevar mucho tiempo y pueden requerir desconectar una o más particiones sin conexión mientras se llevan a cabo. Si se replican las particiones, es posible conservar algunas de las réplicas en línea mientras otras se dividen, combinan o vuelven a configurar, pero es posible que el sistema necesite limitar las operaciones que se pueden realizar en los datos de esas particiones mientras se lleva a cabo la reconfiguración. Por ejemplo, los datos de las réplicas podrían marcarse como de solo lectura para limitar el ámbito de las incoherencias que podrían producirse mientras se están reestructurando las particiones.

> Para obtener información e instrucciones más detalladas acerca de muchas de estas consideraciones y técnicas de prácticas recomendadas de diseño de almacenes de datos que implementan particiones horizontales, consulte el [patrón de particionamiento]

### Particiones verticales

El uso más común para la partición vertical es reducir los costes de E/S y de rendimiento asociados con la recopilación de los elementos a los que se tiene acceso con más frecuencia. En la figura 2 se muestra una visión general de un ejemplo de particionamiento vertical, donde se mantienen diferentes propiedades para cada elemento de datos en diferentes particiones; se accede al nombre, la descripción y la información de precios de los productos con más frecuencia que el volumen disponible en existencias o la última fecha en la que se efectuó un pedido.

![](media/best-practices-data-partitioning/DataPartitioning02.png)

_Figura 2. - Creación de particiones verticales de datos por su patrón de uso_

En este ejemplo, la aplicación consulta periódicamente el nombre del producto, la descripción y el precio conjuntamente al mostrar los detalles de los productos a los clientes. El nivel de existencias y la fecha en la que se efectuó el último pedido del producto al fabricante se mantienen en una partición independiente porque estos dos elementos se utilizan conjuntamente. Este esquema de partición tiene la ventaja agregada de que los datos de movimiento relativamente lento (nombre de producto, descripción y precio) están separados de los datos más dinámicos (nivel de existencias y fecha del último pedido). Una aplicación puede encontrar beneficioso almacenar en la caché los datos de movimiento lento de la memoria si se tiene acceso a ellos con frecuencia.

Otro escenario típico de esta estrategia de partición es maximizar la seguridad de los datos confidenciales. Por ejemplo, al almacenar números de tarjetas de crédito y los números correspondientes de comprobación de la seguridad de la tarjeta correspondientes en particiones independientes.

La creación de particiones verticales también puede reducir la cantidad de acceso simultáneo necesario a los datos.

> La creación de particiones verticales funciona a nivel de entidad dentro de un almacén de datos, normalizando parcialmente una entidad para dividirla de un elemento _amplio_ en un conjunto de elementos _reducido_. Es ideal para almacenes de datos orientados a columnas como HBase y Cassandra. Si es poco probable que los datos de una colección de columnas cambien, también puede considerar el uso de almacenes de columnas en SQL Server.

### Creación de particiones funcional

Para los sistemas en los que es posible identificar un contexto limitado para cada área de negocio o servicio independiente en la aplicación, la creación de particiones funcional proporciona una técnica para mejorar el rendimiento de acceso a datos y el aislamiento. Otro uso común de la creación de particiones funcional es separar los datos de lectura y escritura de datos de solo lectura que se utilizan para elaborar informes. En la figura 3 se muestra una visión general de la creación de particiones donde se separan los datos de inventario de los datos del cliente.

![](media/best-practices-data-partitioning/DataPartitioning03.png)

_Figura 3. -Creación de particiones de datos funcionalmente por contexto limitado o subdominio_

Esta estrategia de creación de particiones puede ayudar a reducir la contención de acceso a datos a través de distintas partes del sistema.

## Diseño de particiones para obtener escalabilidad

Es fundamental considerar el tamaño y la carga de trabajo de cada partición y equilibrarlos para que los datos se distribuyan y logren alcanzar una máxima escalabilidad. Sin embargo, también debe particionar los datos para que no superen los límites de escala de un almacén de una única partición.

Lleve a cabo los pasos siguientes durante el diseño de particiones para obtener escalabilidad:

1. Analice la aplicación para comprender los patrones de acceso a datos, como el tamaño del conjunto de resultados devuelto por cada consulta, la frecuencia de acceso, la latencia inherente y los requisitos de procesamiento del lado del servidor. En muchos casos, unas cuantas entidades principales exigirán la mayoría de los recursos de procesamiento.
2. En función del análisis, determine los objetivos de escalabilidad actuales y futuros, como el tamaño de los datos y la carga de trabajo y distribuya los datos entre las particiones para cumplir el objetivo de escalabilidad. En la estrategia de creación de particiones horizontal, es importante elegir la partición adecuada para asegurarse de que la distribución es equilibrada. Para obtener más información, consulte el [patrón particionamiento].
3. Asegúrese de que los recursos disponibles de cada partición sean suficientes para controlar los requisitos de escalabilidad en términos de rendimiento y tamaño de datos. Por ejemplo, el nodo que aloja una partición puede imponer un límite en la cantidad de espacio de almacenamiento, potencia de procesamiento o ancho de banda de red que proporciona. Si los requisitos de almacenamiento y procesamiento de datos suelen superar estos límites, puede ser necesario refinar la estrategia de creación de particiones o dividir todavía más los datos. Por ejemplo, un enfoque de escalabilidad podría consistir en separar los datos de registro de las características principales de la aplicación mediante el uso de almacenes de datos independientes para evitar que los requisitos de almacenamiento de datos totales superen el límite de escalado del nodo. Si el número total de almacenes de datos supera el límite de nodos, puede ser necesario usar nodos de almacenamiento independientes.
4. Supervise el sistema en uso para comprobar que los datos se distribuyen según lo previsto y que las particiones pueden controlar la carga impuesta en ellos. Podría ser posible que el uso no coincida con el previsto por el análisis. En ese caso, es posible que se puedan volver a equilibrar las particiones. En caso contrario, puede ser necesario volver a diseñar algunas partes del sistema para obtener el equilibrio necesario.

Tenga en cuenta que algunos entornos de nube asignan recursos en términos de límites de infraestructura, y debe asegurarse de que los límites de su límite seleccionado proporcionen suficiente espacio para cualquier crecimiento previsto en el volumen de datos, en términos de ancho de banda, potencia de procesamiento y almacenamiento de datos. Por ejemplo, si usa almacenamiento en tablas Azure, una partición ocupada puede requerir más recursos de los que están disponibles para una sola partición para controlar las solicitudes (hay un límite en el volumen de solicitudes que pueden controlarse mediante una sola partición en un período de tiempo determinado (consulte la página [Objetivos de rendimiento y escalabilidad de almacenamiento de Azure] en el sitio web de Microsoft para obtener más detalles). En este caso, es posible que se necesite crear otra partición en la partición para repartir la carga. Si el tamaño total o el rendimiento de estas tablas supera la capacidad de una cuenta de almacenamiento, puede ser necesario crear cuentas de almacenamiento adicionales y repartir las tablas entre estas cuentas. Si el número de cuentas de almacenamiento supera el número de cuentas que están disponibles para una suscripción, es posible que resulte necesario usar varias suscripciones.

## Diseño de particiones para aumentar el rendimiento de las consultas

El rendimiento de las consultas a menudo puede ampliarse mediante el uso de conjuntos de datos más pequeños y la ejecución de consultas en paralelo. Cada partición debe contener una pequeña proporción de todo el conjunto de datos y esta reducción de volumen puede mejorar el rendimiento de las consultas. Sin embargo, la partición no es una alternativa para diseñar y configurar una base de datos de forma adecuada. Por ejemplo, asegúrese de que tiene los índices necesarios establecidos si está usando una base de datos relacional.

Al diseñar las particiones para aumentar el rendimiento de las consultas, siga estos pasos:

1. Examine los requisitos de la aplicación y el rendimiento:
	- Use los requisitos empresariales para determinar las consultas más importantes que siempre deben ejecutarse rápidamente.
	- Supervise el sistema para identificar las consultas que se ejecutan lentamente.
	- Establezca las consultas que se realizan con mayor frecuencia. Una sola instancia de cada consulta podría tener un coste mínimo, pero el consumo acumulado de recursos podría ser importante. Puede ser beneficioso separar los datos recuperados por estas consultas en una partición distinta o incluso una memoria caché.
2. Cree una partición en los datos que están provocando un rendimiento lento. Asegúrese de:
	- Limitar el tamaño de cada partición para que el tiempo de respuesta de la consulta se encuentre dentro del objetivo.
	- Diseñar la clave de partición de manera que la aplicación pueda encontrar fácilmente la partición si está implementando la creación de particiones horizontal. Esto evita la necesidad de tener que recorrer cada partición.
	- Tenga en cuenta la ubicación de una partición en la realización de las consultas. Si es posible, intente mantener los datos en particiones que estén geográficamente cerca de las aplicaciones y los usuarios que acceden a ellos.
3. Si una entidad tiene requisitos de rendimiento y de rendimiento de consultas, use la creación de particiones funcional basada en esa entidad. Si esto todavía no logra satisfacer los requisitos, aplique también la creación de particiones horizontal. En la mayoría de los casos bastará una estrategia de partición única, pero en algunos casos, resulta más eficaz combinar ambas estrategias.
4. Considere utilizar consultas asincrónicas que se ejecutan en paralelo en particiones para mejorar el rendimiento.

## Diseño de particiones para obtener disponibilidad

Crear particiones de datos puede mejorar la disponibilidad de aplicaciones asegurándose de que todo el conjunto de datos no constituye un punto de error único y que subconjuntos individuales del conjunto de datos pueden administrarse de forma independiente. La replicación de las particiones que contienen datos críticos también puede mejorar la disponibilidad.

Al diseñar e implementar las particiones, tenga en cuenta los siguientes factores que afectan a la disponibilidad:

- Lo importante que son los datos para las operaciones empresariales. Algunos datos pueden contener información empresarial crítica como detalles de facturas o transacciones bancarias. Otros datos pueden ser simplemente datos operativos menos críticos, como archivos de registro, seguimientos de rendimientos, etc. Después de identificar cada tipo de datos, considere lo siguiente:
	- Almacenar datos críticos en las particiones de alta disponibilidad con un plan de copia de seguridad adecuado.
	- Establecer mecanismos de administración y supervisión independientes o procedimientos para los diferentes elementos más importantes de cada conjunto de datos. Coloque los datos con el mismo nivel de importancia en la misma partición para que pueda efectuar una copia de seguridad de estos conjuntamente con una frecuencia adecuada. Por ejemplo, es posible que las particiones que contienen datos de transacciones bancarias necesiten copias de seguridad con más frecuencia que las particiones que contienen información de seguimiento o registro.
- Cómo se pueden administrar las particiones individuales. Diseñar particiones para admitir el mantenimiento y administración independientes ofrece varias ventajas. Por ejemplo:
	- Si se produce un error en una partición, puede recuperarse de forma independiente sin afectar a las instancias de aplicaciones que tienen acceso a datos de otras particiones.
	- Es posible que la creación de particiones de datos por área geográfica permita la realización de tareas de mantenimiento programadas en horas de poco tráfico para cada ubicación. Asegúrese de que las particiones no sean demasiado grandes para evitar que se complete cualquier tarea de mantenimiento planeada durante este período.
- Si va a replicar datos críticos en particiones. Esta estrategia puede mejorar la disponibilidad y rendimiento, aunque también puede originar problemas de coherencia. Se necesita tiempo para sincronizar los cambios realizados en los datos de una partición con cada réplica, y durante este período diferentes particiones contendrán distintos valores de datos.

## Problemas y consideraciones

Utilizar particiones agrega complejidad al diseño y desarrollo del sistema. Es importante considerar la creación de particiones como una parte fundamental del diseño del sistema, incluso si el sistema solo contiene una sola partición inicialmente. Llevar a cabo la creación de particiones como una idea posterior cuando el sistema empieza a sufrir problemas de rendimiento y escalabilidad solo aumenta la complejidad, ya que ahora probablemente dispone de un sistema activo que mantener. Para actualizar el sistema para incorporar la creación de particiones en este entorno se necesita modificar no solo la lógica de acceso a datos, sino que también puede implicar la migración de grandes cantidades de datos existentes para distribuirlos entre varias particiones, a menudo mientras los usuarios esperan poder seguir usando el sistema.

En algunos casos, la creación de particiones no se considera importante porque el conjunto de datos inicial es pequeño y puede controlarse fácilmente mediante un solo servidor. Esto puede ser cierto en un sistema que no se espera que aumente más allá de su tamaño inicial, pero muchos sistemas comerciales necesitan poder ampliarse a medida que el número de usuarios aumenta. Esta expansión normalmente viene acompañada de un crecimiento en el volumen de datos. También debe entender que la creación de particiones no siempre está disponible en almacenes de datos de gran tamaño. Por ejemplo, es posible que cientos de clientes accedan de manera intensa a un almacén de datos pequeño a la vez. Crear particiones de los datos en esta situación puede ayudar a reducir la contención y a mejorar el rendimiento.

Debe considerar los siguientes puntos cuando se diseña un esquema de partición de datos:

- Siempre que sea posible, mantenga los datos para las operaciones de base de datos más habituales juntos en cada partición para minimizar las operaciones de acceso a datos entre particiones. Realizar consultas en varias particiones puede llevar más tiempo que efectuar consultas solo dentro de una sola partición, pero la optimización de las particiones para un conjunto de consultas puede afectar adversamente a otros conjuntos de consultas. Para minimizar el tiempo de consulta en las particiones en las que no se puede evitar esto, ejecute consultas en paralelo en las particiones y agregue los resultados en la aplicación. Sin embargo, es posible que este enfoque no sea posible en algunos casos, como cuando es necesario obtener un resultado de una consulta y usarlo en la consulta siguiente.
- Si las consultas hacen uso de datos de referencia relativamente estáticos, como las tablas de códigos postales o listas de productos, considere la posibilidad de replicar estos datos en todas las particiones para reducir la necesidad de una operación de búsqueda diferente en otra partición. Este enfoque también puede reducir la probabilidad de que los datos de referencia se conviertan en un conjunto de datos "activo" sujeto a mucho tráfico de todo el sistema, aunque hay un coste adicional asociado con la sincronización de los cambios que podrían producirse en estos datos de referencia.
- Siempre que sea posible, minimice los requisitos de integridad referencial en las particiones verticales y funcionales. En estos esquemas, la propia aplicación es responsable de mantener la integridad referencial entre las particiones cuando se actualizan y consumen datos. Las consultas que deben combinar los datos de varias particiones se ejecutan más lentamente que las consultas que solo combinan datos de la misma partición porque la aplicación normalmente necesitará realizar consultas consecutivas basadas en una clave y, a continuación, en una clave externa. En su lugar, considere la posibilidad de replicar o anular la normalización de los datos pertinentes. Para minimizar el tiempo de consulta donde son necesarias las combinaciones entre particiones, ejecute consultas en paralelo en las particiones y combine los datos dentro de la aplicación.
- Tenga en cuenta el efecto que podría tener el esquema de creación de particiones en la coherencia de los datos en las particiones. Se debe evaluar si homogeneidad es en realidad un requisito. En su lugar, un enfoque común en la nube es implementar la coherencia eventual. Los datos de cada partición se actualizan por separado, y la lógica de la aplicación puede asumir la responsabilidad de garantizar que las actualizaciones se completen correctamente, además de controlar las incoherencias que pueden surgir de consultar datos mientras se está ejecutando una operación coherente. Para obtener más información acerca de cómo implementar la coherencia eventual, consulte la Guía de coherencia.(#insertlink#)
- Tenga en cuenta cómo localizarán las consultas la partición correcta. Si una consulta debe analizar todas las particiones para localizar los datos necesarios, habrá un impacto significativo en el rendimiento, incluso cuando se utilicen varias consultas en paralelo. Las consultas utilizadas con las estrategias de creación de particiones verticales y funcionales pueden especificar las particiones de manera natural. Sin embargo, cuando se utiliza la partición horizontal (particionamiento), buscar un elemento puede resultar difícil porque cada partición tiene el mismo esquema. Una solución típica de particionamiento consiste en mantener una asignación que puede usarse para buscar la ubicación de la partición para elementos específicos de datos. Esta asignación puede implementarse en la lógica de particionamiento de la aplicación o ser mantenida por el almacén de datos si admite el particionamiento transparente.
- Cuando se usa una estrategia de partición horizontal, considere la posibilidad de reequilibrar periódicamente las particiones para distribuir los datos uniformemente por tamaño y por carga de trabajo para minimizar las zonas interactivas, maximizar el rendimiento de las consultas y evitar las limitaciones de almacenamiento físico. Sin embargo, esta es una tarea compleja que requiere a menudo el uso de una herramienta personalizada o un proceso.
- Replicar cada partición proporciona protección adicional frente a errores. Si se produce un error en una única réplica, las consultas se pueden dirigir hacia una copia de trabajo.
- Si se alcanzan los límites físicos de una estrategia de creación de particiones, es posible que necesite ampliar la escalabilidad a un nivel diferente. Por ejemplo, si la creación de particiones se efectúa al nivel de la base de datos, puede implicar la ubicación o replicación de las particiones en varias bases de datos. Si la creación de particiones ya está al nivel de la base de datos y las limitaciones físicas son un problema, puede significar la ubicación o replicación de particiones en varias cuentas de hospedaje.
- Evite las transacciones que accedan a datos en varias particiones. Algunos almacenes de datos implementan la coherencia transaccional y la integridad de las operaciones que modifican datos, pero solo cuando se encuentra en una sola partición. Si necesita compatibilidad transaccional en varias particiones, probablemente necesitará implementar esto como parte de la lógica de la aplicación, porque la mayoría de los sistemas de creación de particiones no proporcionan compatibilidad nativa.

Todos los almacenes de datos requieren algunas operaciones de administración operativa y de supervisión de las actividades. Las tareas pueden oscilar entre la carga de datos, la realización de copias de seguridad y la restauración de datos, la reorganización de datos y asegurarse de que el sistema funciona de manera correcta y eficaz.

Tenga en cuenta los siguientes factores que afectan a la administración operativa:

- Tenga en cuenta cómo implementará la administración correspondiente y las tareas operativas cuando se particionen los datos, como la copia de seguridad y la restauración, el archivo de los datos, la supervisión del sistema y otras tareas administrativas. Por ejemplo, mantener la coherencia lógica durante las operaciones de copia de seguridad y restauración puede ser un desafío.
- Cómo se pueden cargar los datos en varias particiones y cómo se pueden agregar los datos nuevos procedentes de otros orígenes. Es posible que algunas herramientas y utilidades no admitan operaciones de datos particionados como la carga de datos en la partición correcta, y puede que esto requiera crear u obtener nuevas herramientas y utilidades.
- Cómo se archivarán y eliminarán los datos de forma regular (quizás mensualmente) para evitar un aumento excesivo de particiones. Puede ser necesario transformar los datos para que se adapten a un esquema de almacenamiento diferente.
- Considere la posibilidad de ejecutar un proceso periódico para localizar cualquier problema de integridad de datos como los datos de una partición que hace referencia a la información de otra, pero esta información no está disponible. El proceso podría intentar corregir estos problemas automáticamente o generar una alerta a un operador para corregir los problemas manualmente. Por ejemplo, en una aplicación de comercio electrónico, es posible que se mantenga la información de los pedidos en una partición, pero que los artículos de la línea que componen cada pedido se encuentren en la otra. El proceso de realización de pedidos necesita agregar datos a ambas particiones. Si este proceso falla, es posible que haya elementos de línea almacenados para los que no haya ningún pedido correspondiente.

Las tecnologías de almacenamiento de datos diferentes suelen proporcionan sus propias características para admitir la creación de particiones. Las secciones siguientes resumen las opciones implementadas por los almacenes de datos usados por las aplicaciones de Azure y describen las consideraciones para diseñar aplicaciones que pueden aprovechar al máximo estas características.

## Estrategias de creación de particiones de la Base de datos SQL de Azure

La Base de datos SQL de Azure es una base de datos como servicio relacional que se ejecuta en la nube. Se basa en Microsoft SQL Server. Una base de datos relacional divide la información en tablas y cada tabla contiene información sobre entidades como una serie de filas. Cada fila contiene columnas que contienen los datos de los campos individuales de una entidad. La página [Base de datos SQL de Azure] del sitio web de Microsoft ofrece documentación detallada sobre la creación y el uso de bases de datos SQL.

## Particiones horizontales con Base de datos elástica

Una sola Base de datos SQL tiene un límite en el volumen de datos que puede contener y el rendimiento está limitado por factores arquitectónicos y el número de conexiones simultáneas que admite. Base de datos SQL de Azure proporciona Base de datos elástica para permitir el escalado horizontal de una base de datos SQL. Mediante Base de datos elástica, puede dividir los datos en particiones repartidas entre varias bases de datos SQL y puede agregar o quitar particiones según el volumen de datos que necesite para administrar aumentos y disminuciones. El uso de Base de datos elástica también puede ayudar a reducir la contención al distribuir la carga entre las bases de datos.

> [AZURE.NOTE]Base de datos elástica está actualmente en versión preliminar a partir de diciembre de 2015. Es una sustitución de las federaciones de Base de datos SQL de Azure que se van a retirar. Las instalaciones existentes de federación de Base de datos SQL de Azure se pueden migrar a Base de datos elástica mediante la [utilidad de migración de federaciones]. Como alternativa, puede implementar su propio mecanismo de particionamiento si su escenario no se presta naturalmente a las características proporcionadas por Base de datos elástica.

Cada partición se implementa como una base de datos SQL. Una partición puede contener más de un conjunto de datos (denominado _shardlet_), y cada base de datos mantiene los metadatos que describen los shardlets que contiene. Un shardlet puede ser un único elemento de datos, o puede ser un grupo de elementos que comparte la misma clave de shardlet. Por ejemplo, si está particionando datos en una aplicación de varios inquilinos, la clave del shardlet podría ser el Id. de inquilino y todos los datos de un inquilino determinado se mantendrían como parte del mismo shardlet. Los datos de otros inquilinos se mantendrían en shardlets diferentes.

Es responsabilidad del programador asociar un conjunto de datos con una clave de shardlet. Una base de datos SQL independiente actúa como un administrador global de mapa de particiones que contiene una lista de bases de datos (particiones) que componen el sistema completo junto con información sobre los shardlets de cada base de datos. En primer lugar, una aplicación cliente que tiene acceso a datos se conecta a la base de datos de administrador global del mapa de particiones para obtener una copia de este (lista de particiones y shardlets) que almacena en la caché localmente. A continuación, la aplicación usa esta información para enrutar las solicitudes de datos a la partición apropiada. Esta funcionalidad se oculta detrás de una serie de API contenidas en la biblioteca de cliente de Base de datos elástica de Base de datos SQL de Azure, disponible como un paquete de NuGet. En la página [Información general de las características de Base de datos elástica] del sitio web de Microsoft se proporciona una introducción más completa a Base de datos elástica.

> [AZURE.NOTE]Puede replicar la base de datos de administrador global del mapa de particiones para reducir la latencia y mejorar la disponibilidad. Si implementa la base de datos mediante uno de los niveles de precios Premium puede configurar la replicación geográfica activa para copiar continuamente datos a bases de datos de diferentes regiones. Cree una copia de la base de datos en cada región en el que se encuentran los usuarios y configure la aplicación para conectarse a esta copia para obtener el mapa de particiones.

> Un enfoque alternativo es usar SQL Data Sync de Azure o una canalización de la Factoría de datos de Azure para replicar la base de datos de administrador de mapa de particiones entre regiones. Esta forma de replicación se ejecuta periódicamente y es más adecuada si el mapa de particiones no cambia con frecuencia. Además, la base de datos del administrador de asignación del mapa de particiones no debe crearse con un nivel de precios de Premium.

Base de datos elástica proporciona dos esquemas para asignar datos a shardlets y para almacenarlos en particiones:

- Un mapa de particiones de lista describe una asociación entre la clave única y un shardlet. Por ejemplo, en un sistema de varios inquilinos, los datos para cada inquilino podrían asociarse a una clave única y almacena en su propio shardlet. Para garantizar la privacidad y el aislamiento (para impedir que un inquilino agote los recursos de almacenamiento de datos disponibles para otros usuarios), cada shardlet puede mantenerse dentro de su propia partición.

![](media/best-practices-data-partitioning/PointShardlet.png)

_Figura 4. - Uso de un mapa de particiones de lista para almacenar datos de inquilino en particiones independientes_

- Una mapa de particiones de intervalo describe una asociación entre un conjunto de valores de claves contiguos y un shardlet. En el ejemplo de varios inquilinos que se ha descrito anteriormente, como alternativa a la implementación de shardlets específicos, puede agrupar los datos de un conjunto de inquilinos (cada uno con su propia clave) en el mismo shardlet. Este esquema es menos costoso que el primero (los inquilinos comparten los recursos de almacenamiento de datos), pero con riesgo de reducir el aislamiento y la privacidad de los datos.

![](media/best-practices-data-partitioning/RangeShardlet.png)

_Figura 5. - Usar un mapa de particiones de intervalo para almacenar los datos de un intervalo de inquilinos de una partición_

Tenga en cuenta que una sola partición puede contener los datos de varios shardlets. Por ejemplo, podría utilizar shardlets de lista para almacenar datos de varios inquilinos no contiguos en la misma partición. También puede mezclar shardlets de intervalo y de lista en la misma partición, aunque se tratarán a través de diferentes asignaciones en la base de datos de administrador global del mapa de particiones (la base de datos de administrador global del mapa de particiones puede contener varios mapas de particiones). En la Figura 6 se describe este enfoque.

![](media/best-practices-data-partitioning/MultipleShardMaps.png)

_Figura 6. - Implementación de varios mapas de particiones_

El esquema de partición que se implementa puede tener una incidencia significativa en el rendimiento del sistema y también afecta a la velocidad a la que las particiones deben agregarse o quitarse o a los datos particionados entre las particiones. Al utilizar Base de datos elástica para particionar los datos, debe tener en cuenta los aspectos siguientes:

- Agrupar los datos que se utilizan conjuntamente en la misma partición y evitar las operaciones que necesitan tener acceso a los datos almacenados en varias particiones. Tenga en cuenta que con Base de datos elástica una partición es una base de datos SQL por derecho propio, y Base de datos SQL de Azure no admite combinaciones entre bases de datos; estas operaciones tienen que realizarse en el cliente. Recuerde también que las restricciones de la integridad referencial de Base de datos SQL de Azure, desencadenadores y procedimientos almacenados en una base de datos no pueden hacer referencia a objetos de otra, por lo que no se debe diseñar un sistema que tenga dependencias entre las particiones. Sin embargo, una Base de datos SQL puede contener tablas con copias de los datos de referencia que se utilizan con frecuencia por parte de consultas y otras operaciones, y estas tablas no deben pertenecer a cualquier shardlet específico. Replicar estos datos entre particiones puede ayudar a eliminar la necesidad de combinar datos que se encuentren en varias bases de datos. Lo ideal es que dichos datos sean estáticos o lentos para minimizar el esfuerzo de replicación y reducir las posibilidades de que se vuelvan obsoletos.

	> [AZURE.NOTE]Aunque Base de datos SQL de Azure no admite combinaciones entre bases de datos, la API de Base de datos elástica permite realizar consultas entre particiones que pueden iterarse de forma transparente a través de los datos contenidos en todos los shardlets a los que hace referencia un mapa de particiones. La API de Base de datos elástica divide las consultas efectuadas entre particiones en una serie de consultas individuales (una para cada base de datos) y luego combina los resultados. Para más información, consulte la página [Consulta de varias particiones] en el sitio web de Microsoft.

- Los datos almacenados en shardlets que pertenecen al mismo mapa de partición deben tener el mismo esquema. Por ejemplo, no cree un mapa de particiones de lista que esté orientado a algunos shardlet que contengan datos del inquilino y otros shardlets que contengan información del producto. Esta regla no se aplica mediante Base de datos elástica, pero la administración y la consulta de datos se vuelven muy complejas si cada shardlet tiene un esquema diferente. En el ejemplo anteriormente citado, debe crear dos mapas de partición de lista; uno que haga referencia a los datos de inquilino y el otro que esté orientado a la información del producto. Recuerde que los datos que pertenecen a diferentes shardlets pueden almacenarse en la misma partición.

	> [AZURE.NOTE]La funcionalidad de consulta entre particiones de la API de Base de datos elástica depende de cada shardlet en el mapa de particiones que contiene el mismo esquema.
- Las operaciones transaccionales solamente se admiten para datos contenidos dentro de la misma partición y no entre las particiones. Las transacciones pueden abarcar shardlets siempre que formen parte de la misma partición. Por lo tanto, si la lógica de negocios debe realizar transacciones, almacene los datos afectados en la misma partición o implemente la coherencia eventual. Para obtener más información, consulte la guía de coherencia de datos.
- Coloque las particiones cerca de los usuarios que tienen acceso a los datos de esas particiones (particiones de geolocalización). Esta estrategia le ayudará a reducir la latencia.
- Evite tener una mezcla de particiones muy activas (zonas activas) y relativamente inactivas. Pruebe y distribuya la carga uniformemente entre las particiones. Esto puede requerir la aplicación de algoritmos hash a las claves de shardlet.
- Si está geolocalizando las particiones, asegúrese de que las claves hash se asignan a shardlets de particiones almacenadas cerca de los usuarios que tienen acceso a esos datos.
- Actualmente, solo se admite un conjunto limitado de tipos de datos SQL como claves de shardlet; _int, bigint, varbinary_ y _uniqueidentifier_. Los tipos _int_ y _bigint_ de SQL se corresponden a los tipos de datos _int_ y _long_ en C#, y tienen los mismos intervalos. El tipo _varbinary_ de SQL puede controlarse mediante el uso de una matriz _Byte_ en C# y el tipo _uniqueidentier_ de SQL se corresponde con la clase _Guid_ de .NET Framework.

Como su nombre implica, Base de datos elástica permite que un sistema agregue y quite particiones a medida que el volumen de datos disminuye y crece. Las API de la biblioteca de cliente de Base de datos elástica de Base de datos SQL de Azure permiten a una aplicación crear y eliminar particiones de forma dinámica (y actualizar el administrador del mapa de la particiones de forma transparente); sin embargo, eliminar una partición es una operación destructiva que también requiere la eliminación de todos los datos de esa partición. Si una aplicación necesita dividir una partición en dos particiones independientes o combinar particiones, Base de datos elástica proporciona un servicio independiente de división y combinación. Este servicio se ejecuta en un servicio hospedado en la nube (el desarrollador tiene que crear este servicio hospedado en la nube) y se encarga de migrar datos entre las particiones de forma segura. Para más información, consulte el tema [Escalado con la herramienta de división y combinación de Base de datos elástica] en el sitio web de Microsoft.

## Estrategias de creación de particiones para el almacenamiento de Azure

Almacenamiento de Azure proporciona tres abstracciones para administrar los datos:

- Almacenamiento de tablas, que implementa el almacenamiento escalable de estructuras. Una tabla contiene una colección de entidades, cada una de los cuales puede incluir un conjunto de propiedades y valores.
- Almacenamiento de blobs, que proporciona almacenamiento para objetos y archivos grandes.
- Colas de almacenamiento, que admiten la mensajería asincrónica confiable entre aplicaciones.

Almacenamiento de tablas y Almacenamiento de blobs son esencialmente almacenes de clave-valor optimizados para almacenar datos estructurados y no estructurados respectivamente. Las colas de almacenamiento proporcionan un mecanismo para crear aplicaciones de acoplamiento flexible y escalables. Almacenamiento de tablas, Almacenamiento de blobs y Colas de almacenamiento se crean dentro del contexto de una cuenta de almacenamiento de Azure. Las cuentas de almacenamiento de Azure admiten tres formas de redundancia:

- Almacenamiento con redundancia local, que mantiene tres copias de datos dentro de un único centro de datos. Esta forma de redundancia protege contra fallos de hardware, pero no frente a un desastre que abarca todo el centro de datos.
- Almacenamiento con redundancia de zona que mantiene tres copias de los datos repartidas entre diferentes centros de datos de la misma región (o entre dos regiones cercanas geográficamente). Esta forma de redundancia puede proteger frente a desastres que se producen dentro de un único centro de datos, pero no puede proteger frente a las desconexiones de red a gran escala que afectan a toda una región. Tenga en cuenta que el almacenamiento con redundancia de zona solo está disponible actualmente para blobs en bloques.
- Almacenamiento con redundancia geográfica, que mantiene seis copias de datos; tres copias en una región (su región local) y otras tres copias en una región remota. Esta forma de redundancia proporciona el máximo nivel de protección ante desastres.

Microsoft ha publicado los objetivos de escalabilidad de las cuentas de almacenamiento de Azure; consulte la página [Objetivos de rendimiento y escalabilidad de almacenamiento de Azure] en el sitio web de Microsoft. Actualmente, la capacidad de cuenta de almacenamiento total (el tamaño de los datos contenidos en el almacenamiento de tablas, el almacenamiento de blobs y los mensajes pendientes que se mantienen en la cola de almacenamiento) no pueden superar los 500TB. La tasa de solicitud máxima (tomando una entidad, blob o tamaño de mensaje de 1 KB) es 20 KB por segundo. Si su sistema es probable que supere estos límites, considere la posibilidad de crear particiones de la carga entre varias cuentas de almacenamiento; una sola suscripción de Azure puede crear hasta 100 cuentas de almacenamiento. Sin embargo, tenga en cuenta que estos límites pueden cambiar con el tiempo.

## Creación de particiones en el almacenamiento de tablas de Azure

Almacenamiento de tablas de Azure es un valor/clave almacenado diseñado en torno a la creación de particiones. Todas las entidades se almacenan en una partición y las particiones son administradas internamente por el almacenamiento de tablas de Azure. Cada entidad almacenada en una tabla debe proporcionar una clave de dos partes compuesta por:

- La clave de la partición. Se trata de un valor de cadena que determina en qué partición del almacenamiento de tablas de Azure colocará la entidad. Todas las entidades con la misma clave de partición se almacenarán en la misma partición.
- La clave de la fila. Se trata de otro valor de cadena que identifica la entidad dentro de la partición. Todas las entidades dentro de una partición se ordenan léxicamente, en orden ascendente, por parte de esta clave. La combinación de clave de fila/partición debe ser única para cada entidad y no puede superar 1 KB de longitud.

El resto de los datos de una entidad se compone de campos definidos por la aplicación. No se aplica ningún esquema concreto, y cada fila puede contener un conjunto diferente de campos definidos por la aplicación. La única limitación es que el tamaño máximo de una entidad (incluidas las claves de partición y fila) actualmente es de 1 MB. El tamaño máximo de una tabla es de 200 TB, aunque estas cifras se pueden cambiar en el futuro (consulte la página [Objetivos de rendimiento y escalabilidad del almacenamiento de Azure] en el sitio web de Microsoft para obtener la información más reciente acerca de estos límites. Si intenta almacenar entidades que superen esta capacidad, considere la posibilidad de dividirlas en varias tablas; use particiones verticales y divida los campos en los grupos a los que es más posible que se acceda conjuntamente.

En la figura 7 se muestra la estructura lógica de una cuenta de almacenamiento de ejemplo (datos Contoso) para una aplicación de comercio electrónico ficticia. Las cuentas de almacenamiento contienen tres tablas (información del cliente, información del producto e información del pedido), y cada tabla tiene varias particiones. En la tabla de información del cliente se particionan los datos según la ciudad en la que está ubicado el cliente y la clave de fila contiene el identificador de cliente. En la tabla de información del producto se particionan los productos por categoría de producto y la clave de fila contiene el número de producto. En la tabla de información del pedido se particionan los pedidos por la fecha en la que se efectuaron y la clave de fila especificada en el momento en el que se ha recibido el pedido. Tenga en cuenta que todos los datos se ordenan por la clave de fila de cada partición.

![](media/best-practices-data-partitioning/TableStorage.png)

_Figura 7. - Las tablas y las particiones de una cuenta de almacenamiento de ejemplo_

> [AZURE.NOTE]Almacenamiento de tablas de Azure también agrega un campo de marca de tiempo a cada entidad. El campo de marca de tiempo es mantenido por el almacenamiento de tablas y se actualiza cada vez que la entidad se modifica y se escribe de nuevo en una partición. El servicio de almacenamiento de tablas usa este campo para implementar la concurrencia optimista (cada vez que una aplicación vuelve a escribir una entidad en el almacenamiento de tablas, el servicio de almacenamiento de tablas compara el valor de la marca de tiempo de la entidad que se está escribiendo con el valor contenido en el almacenamiento de tablas, y si son diferentes, significa que otra aplicación debe haber modificado la entidad desde que se recuperó y la operación de escritura falla). No debe modificar este campo en su propio código y no debe especificar un valor para este campo cuando se cree una nueva entidad.

Almacenamiento de tablas de Azure usa la clave de partición para determinar cómo almacenar los datos. Si se agrega una entidad a una tabla con una clave de partición no usada anteriormente, el almacenamiento de tablas Azure creará una nueva partición para esta entidad. Otras entidades con la misma clave de partición se almacenarán en la misma partición. Este mecanismo implementa de forma efectiva una estrategia de escalado automático. Cada partición se almacenarán en un único servidor en un centro de datos de Azure (para ayudar a asegurarse de que las consultas que recuperan datos de una sola partición se ejecutan rápidamente), pero diferentes particiones se pueden distribuir en varios servidores. Además, un único servidor puede alojar varias particiones si estas particiones tienen un tamaño limitado.

Debe considerar los siguientes puntos cuando diseñe las entidades para el almacenamiento de tablas de Azure:

- La selección de valores de claves de fila y de partición debe basarse en la forma en que la que se accede a los datos. Debe elegir una combinación de claves de partición o fila que admita la mayoría de las consultas. Las consultas más eficaces recuperarán los datos especificando la clave de partición y la clave de fila. Las consultas que especifican una clave de partición y un intervalo de claves de fila pueden satisfacerse mediante la exploración de una sola partición; esto es relativamente rápido porque los datos se mantienen en el orden de las claves de filas. Es posible que las consultas que no especifican al menos la clave de partición requieran que el almacenamiento de tablas de Azure analice todas las particiones de los datos.

	> [AZURE.TIP]Si una entidad tiene una clave natural, a continuación, úsela como clave de partición y especifique una cadena vacía como clave de fila. Si una entidad tiene una clave compuesta que incluye dos propiedades, seleccione la propiedad que cambie más despacio como clave de partición y la otra como clave de fila. Si una entidad tiene más de dos propiedades de clave, use una concatenación de propiedades para proporcionar las claves de partición y fila.

- Si realiza regularmente las consultas que buscan datos con campos que no sean las claves de partición y fila, considere la posibilidad de implementar el [Patrón de tabla de índice].
- Si se generan claves de partición mediante una secuencia monotónica creciente o decreciente (por ejemplo, "0001", "0002", "0003",...) y cada partición contiene solo una cantidad limitada de datos, es posible que el almacenamiento de tablas de Azure agrupe físicamente estas particiones en el mismo servidor. Este mecanismo supone que es más probable que la aplicación realice consultas en un intervalo contiguo de particiones (consultas por rango) y está optimizada para este caso. Sin embargo, este enfoque puede provocar la aparición de puntos de acceso centrados en un solo servidor, ya que es probable que todas las inserciones de nuevas entidades se concentren en uno u otro extremo de los intervalos contiguos. También puede reducir la escalabilidad. Para distribuir la carga más uniformemente entre servidores, considere la posibilidad de aplicar un algoritmo hash a la clave de partición para que la secuencia sea más aleatoria.
- El almacenamiento de tablas de Azure admite operaciones transaccionales para entidades que pertenecen a la misma partición. Esto significa que una aplicación puede realizar varias operaciones de inserción, actualización, eliminación, sustitución o combinación como una unidad atómica (siempre que la transacción no incluya más de 100 entidades y que la carga de la solicitud no supere los 4 MB de tamaño). Las operaciones que abarcan varias particiones no son transaccionales y pueden requerirle implementar la coherencia eventual tal y como se describe en las instrucciones de coherencia de datos. Para obtener más información acerca del almacenamiento y las transacciones de tablas, visite la página [Realización de transacciones de grupos de entidades] en el sitio web de Microsoft.
- Preste mucha atención a la granularidad de la clave de partición:
	- El uso de la misma clave de partición para cada entidad hará que el servicio de almacenamiento de tablas cree una sola partición grande que se mantendrá en un servidor, impidiendo su escalado horizontal y centrando en su lugar la carga en un único servidor. Como resultado, este enfoque solo es adecuado para sistemas que administran un pequeño número de entidades. Sin embargo, este enfoque garantiza que todas las entidades puedan participar en transacciones de grupo de entidades.
	- Usar una clave de partición única para cada entidad hará que el servicio de almacenamiento de tablas cree una partición independiente para cada entidad, lo cual puede provocar un número elevado de particiones pequeñas (dependiendo del tamaño de las entidades). Este enfoque es más escalable que el uso de una sola clave de partición, pero las transacciones de grupos de entidades no son posibles y las consultas que capturan más de una entidad pueden implicar lecturas desde más de un servidor. Sin embargo, si la aplicación realiza consultas por rango, usar una secuencia monotónica para generar las claves de partición pueden ayudarle a optimizar estas consultas.
	- Compartir la clave de partición en un subconjunto de entidades permite agrupar entidades relacionadas en la misma partición. Se pueden realizar operaciones que implican entidades relacionadas con transacciones de grupo de entidades y las consultas que capturan un conjunto de entidades relacionadas pueden satisfacerse mediante el acceso a un único servidor.

Para información adicional sobre las particiones de datos en el Almacenamiento de tablas de Azure, consulte el artículo [Guía de diseño de tablas de Almacenamiento de Azure] en el sitio web de Microsoft.

## Creación de particiones en el almacenamiento de blobs de Azure

El almacenamiento de blobs de Azure le permite almacenar objetos binarios grandes, actualmente de hasta 200 GB de tamaño para blobs en bloques o 1 TB para los blobs en páginas (para obtener la información más reciente, visite la página [Objetivos de rendimiento y escalabilidad del almacenamiento de Azure] en el sitio web de Microsoft). Use blobs en bloques en escenarios como los de transmisión por secuencias donde sea necesario cargar o descargar grandes volúmenes de datos rápidamente. Use blobs en páginas para aplicaciones que requieran acceso aleatorio en lugar de acceso de serie a partes de los datos.

Cada blob (en bloque o en página) se mantiene en un contenedor de una cuenta de almacenamiento de Azure. Puede usar contenedores para agrupar blobs relacionados que tengan los mismos requisitos de seguridad en conjunto, aunque esta agrupación es más lógica que física. Dentro de un contenedor cada blob tiene un nombre único.

El almacenamiento de blobs se divide automáticamente en función del nombre de blob. Cada blob se mantiene en su propia partición y los blobs del mismo contenedor no comparten una partición. Esta arquitectura permite al almacenamiento de blobs de Azure equilibrar la carga entre servidores de forma transparente, ya que blobs diferentes dentro del mismo contenedor pueden distribuirse en servidores diferentes.

Las acciones de escritura de un solo bloque (blob en bloques) o página (blob en páginas) son atómicas, pero no las operaciones que abarcan bloques, páginas o blobs. Si necesita asegurar la coherencia cuando se realizan operaciones de escritura en bloques, páginas y blobs, deberá suscribir un bloqueo de escritura mediante el uso de una concesión de blob.

Almacenamiento de blobs de Azure admite velocidades de transferencia de hasta 60 MB por segundo o 500 solicitudes por segundo para cada blob. Si prevé que va a superar estos límites y los datos de blob están relativamente estáticos, considere la posibilidad de replicar blobs mediante el uso de la Red de entrega de contenido (CDN) de Azure. Para obtener más información, consulte la página [Uso de CDN para Azure] en el sitio web de Microsoft. Para obtener orientación y consideraciones adicionales, consulte el artículo de la Red de entrega de contenido (CDN).

## Creación de particiones en colas de almacenamiento de Azure

Las colas de almacenamiento de Azure le permiten implementar mensajería asincrónica entre procesos. Una cuenta de almacenamiento de Azure puede contener cualquier número de colas y cada una de ellas puede contener cualquier número de mensajes. La única limitación es el espacio disponible en la cuenta de almacenamiento. El tamaño máximo de un mensaje individual es de 64 KB. Si necesita mensajes de mayor tamaño, considere la posibilidad de usar Colas del Bus de servicio en su lugar.

Cada cola de almacenamiento tiene un nombre único dentro de la cuenta de almacenamiento en la que se encuentra. Las colas de particiones de Azure basadas en el nombre y todos los mensajes de la misma cola se almacenan en la misma partición, controlada por un solo servidor. Distintas colas pueden ser administradas por distintos servidores para ayudar a equilibrar la carga. La asignación de colas a servidores es transparente para las aplicaciones y usuarios. En una aplicación de gran escala, no use la misma cola de almacenamiento para todas las instancias de la aplicación, ya que este enfoque puede provocar que el servidor que hospeda la cola se convierta en un punto de conexión; use colas diferentes para distintas áreas funcionales de la aplicación. Las colas de almacenamiento de Azure no admiten transacciones, por lo que dirigir los mensajes a distintas colas debería tener poco impacto en la coherencia de los mensajes.

Una cola de almacenamiento de Azure puede controlar hasta 2.000 mensajes por segundo. Si necesita procesar los mensajes a una velocidad mayor que esta, puede crear varias colas. Por ejemplo, en una aplicación global, crear colas de almacenamiento independientes en cuentas de almacenamiento separadas para controlar las instancias de aplicación que se ejecutan en cada región.

## Estrategias de creación de particiones para el Bus de servicio de Azure

El Bus de servicio de Azure usa un agente de mensajes para controlar los mensajes enviados a una cola de Bus de servicio o un tema. De forma predeterminada, todos los mensajes enviados a una cola o tema se controlan mediante el mismo proceso de agente de mensaje. Esta arquitectura puede colocar una limitación en el rendimiento general de la cola de mensajes. Sin embargo, también se puede crear una partición de una cola o tema cuando se crea estableciendo la propiedad _EnablePartitioning_ de la descripción de la cola o tema en _true_. Una cola o tema particionado se divide en varios fragmentos, cada uno de ellos respaldado por un almacén de mensajes independiente y un agente de mensajes. Bus de servicio asume la responsabilidad de crear y administrar estos fragmentos. Cuando una aplicación envía un mensaje a una cola o tema particionado, Bus de servicio asigna el mensaje a un fragmento de esa cola o tema. Cuando una aplicación recibe un mensaje de una cola o suscripción, Bus de servicio comprueba cada fragmento para detectar el siguiente mensaje disponible y, a continuación, lo pasa a la aplicación para su procesamiento. Esta estructura le ayuda a distribuir la carga entre agentes y almacenes de mensaje, aumentar la escalabilidad y mejorar la disponibilidad; si el almacén o el agente de mensajes de un fragmento no está disponible temporalmente, Bus de servicio puede recuperar mensajes de uno de los fragmentos disponibles restantes.

Bus de servicio asigna un mensaje a un fragmento de la siguiente manera:

- Si el mensaje pertenece a una sesión, se envían todos los mensajes con el mismo valor para la propiedad \_ SessionId\_ al mismo fragmento.
- Si el mensaje no pertenece a una sesión pero el remitente ha especificado un valor para la propiedad _PartitionKey_, a continuación, todos los mensajes con el mismo valor _PartitionKey_ se envían al mismo fragmento.

	> [AZURE.NOTE]Si las propiedades _SessionId_ y _PartitionKey_ están especificadas, se deben establecer en el mismo valor; de lo contrario, se rechazará el mensaje.
- Si las propiedades _SessionId_ y _PartitionKey_ de un mensaje no se han especificado pero se habilita la detección de duplicados, se usará la propiedad _MessageId_. Todos los mensajes con el mismo _MessageId_ se dirigirán al mismo fragmento.
- Si los mensajes no incluyen una propiedad _SessionId, PartitionKey_ o _MessageId_, Bus de servicio asigna los mensajes a los fragmentos por turnos. Si un fragmento no está disponible, Bus de servicio pasará al siguiente. De este modo, un error temporal de la infraestructura de mensajería no provoca que la operación de envío de mensajes falle.

Debe tener en cuenta los siguientes puntos cuando decida crear una partición de una cola de mensajes de Bus de servicio o un tema, cómo o si realizarla:

- Los temas y colas de Bus de servicio se crean dentro del ámbito de un espacio de nombres de Bus de servicio. Actualmente Bus de servicio permite hasta 100 colas o temas particionados por espacio de nombres.
- Cada espacio de nombres del Bus de servicio impone cuotas en los recursos disponibles, como el número de suscripciones por tema, el número de solicitudes de y recepción simultáneas por segundo y el número máximo de conexiones simultáneas que pueden establecerse. Estas cuotas se documentan en el sitio web de Microsoft en la página [Cuotas de Bus de servicio]. Si espera que superar estos valores, cree espacios de nombres adicionales con sus propios temas y colas y distribuya el trabajo en estos espacios de nombres. Por ejemplo, en una aplicación global, cree espacios de nombres independientes en cada región y configure instancias de la aplicación para usar las colas y temas en el espacio de nombres más cercano.
- Los mensajes que se envían como parte de una transacción deben especificar una clave de partición. Esto puede ser un _SessionId, PartitionKey_ o _MessageId_. Todos los mensajes que se envían como parte de la misma transacción deben especificar la misma clave de partición porque deben tratarse mediante el mismo proceso de agente de mensaje. No se pueden enviar mensajes a diferentes colas o temas de dentro de la misma transacción.
- No se puede configurar una cola o tema particionado para eliminarse automáticamente cuando se vuelve inactivo.
- Si está creando soluciones multiplataforma o híbridas, no podrá utilizar actualmente colas o temas particionados mediante el Advanced Message Queuing Protocol (AMQP).

## Estrategias de creación de particiones para Azure DocumentDB

Azure DocumentDB es una base de datos NoSQL que puede almacenar documentos. Un documento en DocumentDB es una representación serializada mediante JSON de un objeto o de otro elemento de datos. No se aplican esquemas fijos, excepto que cada documento debe contener un identificador único.

Los documentos se organizan en colecciones. Una colección permite agrupar los documentos relacionados. Por ejemplo, en un sistema que mantiene entradas de blog, puede almacenar el contenido de cada entrada de blog como documento de una colección y crear colecciones para cada tipo de asunto. O bien, en una aplicación para varios inquilinos como un sistema que permite a los diferentes autores controlar y administrar sus propias publicaciones de blog, podría particionar los blogs por autor y crear una colección independiente para cada autor. El espacio de almacenamiento asignado a las colecciones es elástico y puede reducirse o crecer según sea necesario.

Las colecciones de documentos proporcionan un mecanismo natural para dividir los datos de una base de datos única. Internamente, una base de datos DocumentDB puede abarcar varios servidores y DocumentDB puede intentar distribuir la carga al distribuir colecciones entre servidores. La manera más sencilla de implementar las particiones es crear una colección para cada partición.

> [AZURE.NOTE]A cada DocumentDB se le asignan recursos en términos de un _nivel de rendimiento_. Los niveles de rendimiento están asociados con un límite de velocidad de _unidad de solicitud_ (RU). El límite de velocidad de RU especifica el volumen de los recursos que se reservarán para esa colección y está disponible para uso exclusivo de esa colección. El coste de una colección depende del nivel de rendimiento seleccionado para esa colección; cuanto mayor sea el nivel de rendimiento (y el límite de velocidad de RU) mayor será la carga. Puede ajustar el nivel de rendimiento de una colección mediante el portal de administración de Azure. Para obtener más información, consulte la página [Niveles de rendimiento en DocumentDB] en el sitio web de Microsoft.

Todas las bases de datos se crean en el contexto de una cuenta de DocumentDB. Una sola cuenta de DocumentDB puede contener varias bases de datos y especifica en qué región se crean las bases de datos. Cada cuenta DocumentDB también impone su propio control de acceso. Puede utilizar cuentas DocumentDB para localizar geográficamente particiones (colecciones dentro de bases de datos) cerca de los usuarios que necesitan tener acceso a ellas y aplicar restricciones de modo que solo esos usuarios puedan conectarse a ellas.

Cada cuenta DocumentDB tiene una cuota que limita el número de bases de datos, las colecciones que puede contener y la cantidad de almacenamiento de documentos disponible. Estos límites están sujetos a cambios, pero se describen en la página [Cuotas y límites de DocumentDB] en el sitio web de Microsoft. Teóricamente, es posible que si se implementa un sistema donde todas las particiones pertenecen a la misma base de datos, podría alcanzar el límite de capacidad de almacenamiento de información de la cuenta. En este caso, puede que necesite crear bases de datos y cuentas de DocumentDB adicionales y distribuir las particiones en estas bases de datos. Sin embargo, aunque no sea probable que alcance la capacidad de almacenamiento de una base de datos, una buena razón para utilizar varias bases de datos es que cada base de datos tiene su propio conjunto de usuarios y permisos. Puede usar este mecanismo para aislar el acceso a las colecciones en función de cada base de datos.

La figura 8 muestra la estructura de alto nivel de la arquitectura DocumentDB.

![](media/best-practices-data-partitioning/DocumentDBStructure.png)

_Figura 8. - La estructura de DocumentDB_

Es responsabilidad de la aplicación cliente dirigir las solicitudes a la partición apropiada, normalmente mediante la implementación de su propio mecanismo de asignación en función de algunos atributos de los datos que definen la clave de partición. La figura 9 muestra dos bases de datos DocumentDB, cada una con dos colecciones que actúan como particiones. Los datos se particionan por ID de inquilino y contiene los datos de un inquilino específico. Las bases de datos se crean en cuentas DocumenDB independientes que se encuentran en la misma región que los inquilinos cuyos datos contienen. La lógica de enrutamiento de la aplicación cliente usa el Id. de inquilino como la clave de partición.

![](media/best-practices-data-partitioning/DocumentDBPartitions.png)

_Figura 9. - Implementación de particionamiento con Azure DocumentDB_

Deberá tener en cuenta los siguientes puntos a la hora de decidir cómo particionar los datos con DocumentDB:

- Los recursos disponibles para una base de datos DocumentDB están sujetos a las limitaciones de cuota de la cuenta DocumentDB. Cada base de datos puede contener un número de colecciones (de nuevo, hay un límite) y cada colección está asociada a un nivel de rendimiento que rige el límite de velocidad de RU (rendimiento reservado) para esa colección. Para obtener más información, visite la página [Límites y cuotas de DocumentDB] en el sitio web de Microsoft.
- Cada documento debe tener un atributo que pueda usarse para identificar dicho documento dentro de la colección en la que se encuentra. Esto es diferente de la clave de partición que define en qué colección se encuentra el documento. Una colección puede contener un gran número de documentos, en teoría limitado solamente por la longitud máxima del Id. del documento. El identificador del documento puede contener hasta 255 caracteres.
- Todas las operaciones de un documento se realizan dentro del contexto de una transacción en el ámbito de la colección que contiene el documento. Si se produce un error en una operación, se revierte el trabajo que ha realizado. Mientras un documento está sujeto a una operación, los cambios realizados están sujetos a aislamiento de nivel de instantánea. Este mecanismo garantiza que, si por ejemplo, se produce un error en una solicitud para crear un nuevo documento, otro usuario que consulte la base de datos al mismo tiempo no verán un documento parcial que se haya eliminado.
- Las consultas de DocumentDB también tienen como ámbito el nivel de colección. Una sola consulta solo puede recuperar datos de una colección. Si necesita recuperar datos de varias colecciones, debe consultar cada colección individualmente y combinar los resultados en el código de aplicación.
- DocumentDB admite elementos programables que se pueden almacenar en una colección junto con los documentos: procedimientos almacenados, funciones definidas por el usuario y desencadenadores (escritos en JavaScript). Estos elementos pueden tener acceso a cualquier documento en la misma colección. Además, estos elementos se ejecutan dentro del ámbito de la transacción de ambiente (en el caso de un desencadenador que se activa como resultado de una operación de crear, eliminar o reemplazar realizada en un documento), o iniciando una nueva transacción (en el caso de un procedimiento almacenado que se ejecuta como resultado de una solicitud de cliente explícita). Si el código de un elemento programable produce una excepción, la transacción se revierte. Puede usar procedimientos almacenados y desencadenadores para mantener la integridad y la coherencia entre los documentos, pero estos documentos deben formar parte de la misma colección.
- Debe asegurarse de que no resulte probable que las colecciones que desea almacenar en las bases de datos de una cuenta de DocumentDB superen los límites de rendimiento definidos por los niveles de rendimiento de las colecciones. Estos límites se describen en la página [Administrar necesidades de capacidad de DocumentDB] en el sitio web de Microsoft. Si prevé alcanzar estos límites, considere la posibilidad de dividir las colecciones entre bases de datos en diferentes cuentas de DocumentDB para reducir la carga de cada colección.

## Estrategias de creación de particiones para la Búsqueda de Azure

La capacidad de búsqueda de datos suele ser el método principal de navegación y exploración proporcionado por muchas aplicaciones web, permitiendo a los usuarios encontrar rápidamente recursos (por ejemplo, los productos de una aplicación de comercio electrónico) en función de combinaciones de criterios de búsqueda. El servicio de Búsqueda de Azure proporciona capacidades de búsqueda de texto completo a través de contenido web e incluye características como las consultas sugeridas de escritura anticipada basadas en coincidencias cercanas y en la navegación por facetas. Se puede encontrar una descripción completa de estas funciones en la página [¿Qué es Búsqueda de Azure?] en el sitio web de Microsoft.

El servicio de búsqueda almacena contenido de búsqueda como documentos JSON en una base de datos. Usted define los índices que especifican los campos de búsqueda de estos documentos y proporciona estas definiciones al servicio de búsqueda. Cuando un usuario envía una solicitud de búsqueda, el servicio de búsqueda usa los índices adecuados para buscar elementos coincidentes.

Para reducir la contención, puede dividir el almacenamiento utilizado por el servicio de búsqueda en 1, 2, 3, 4, 6 o 12 particiones y cada partición se puede replicar hasta 6 veces. El producto del número de particiones multiplicado por el número de réplicas se denomina _unidad de búsqueda_ (SU). Una única instancia del servicio de búsqueda puede contener un máximo de 36 SU (una base de datos con 12 particiones solo admite un máximo de 3 réplicas). Se le facturará cada SU que se asigne a su servicio. A medida que crezca el volumen de contenido buscable o la tasa de solicitudes de búsqueda, puede agregar SU a una instancia existente del servicio de búsqueda para controlar la carga adicional. El propio servicio de búsqueda asume la responsabilidad de distribuir los documentos uniformemente entre las particiones y ninguna estrategia de partición manual es compatible actualmente.

Cada partición puede contener un máximo de 15 millones de documentos u ocupar 300 GB de espacio de almacenamiento (el valor que sea inferior, según el tamaño de los documentos y los índices). Puede crear hasta 50 índices. El rendimiento del servicio variará según la complejidad de los documentos, los índices disponibles y los efectos de la latencia de la red. De media, una única réplica (1SU) debe ser capaz de controlar 15 consultas por segundo (QPS), aunque debería realizar pruebas comparativas realizadas con sus propios datos para obtener una medida más precisa del rendimiento. Para más información, consulte la página [Límites de servicio en Búsqueda de Azure] en el sitio web de Microsoft.

> [AZURE.NOTE]Puede almacenar un conjunto limitado de tipos de datos en documentos que se pueden buscar; cadenas, booleanos, datos numéricos, datos de fecha y hora y algunos datos geográficos. Para obtener más detalles, consulte la página [Tipos de datos compatibles (Búsqueda de Azure)] en el sitio web de Microsoft.

Tiene control limitado sobre cómo divide en particiones el servicio de Búsqueda de Azure los datos de cada instancia del servicio. Sin embargo, en un entorno global puede mejorar el rendimiento y reducir la latencia y contención aún más mediante la partición del propio servicio usando cualquiera de las estrategias siguientes:

- Cree una instancia del servicio de búsqueda en cada región geográfica y asegúrese de que las aplicaciones cliente se dirigen hacia la instancia disponible más cercana. Esta estrategia requiere la replicación de las actualizaciones de contenido de búsqueda de manera oportuna en todas las instancias del servicio.
- Cree dos niveles del servicio de búsqueda; un servicio local en cada región que contenga los datos a los que los usuarios de esa región acceden con más frecuencia y un servicio global que abarca todos los datos. Los usuarios pueden dirigir las solicitudes al servicio local (para obtener resultados rápidos pero limitados) o al servicio global (para obtener resultados más lentos pero más completos). Este enfoque es más adecuado cuando hay una variación regional considerable en los datos que se están buscando.

## Estrategias de creación de particiones para Caché en Redis de Azure

Caché en Redis de Azure proporciona un servicio de almacenamiento en caché compartido en la nube que se basa en el almacén de datos de clave y valor de Redis. Como su nombre implica, Caché en Redis de Azure está pensado como una solución de almacenamiento en caché y tan solo debe usarse para almacenar datos transitorios, en lugar de como un almacén de datos permanente; las aplicaciones que usan Caché en Redis de Azure deben poder seguir funcionando si la memoria caché no está disponible. Caché en Redis de Azure admite la replicación principal y secundaria para proporcionar alta disponibilidad, pero actualmente limita el tamaño de caché máximo a 53 GB. Si necesita más espacio que este, deberá crear cachés adicionales. Para más información, visite la página [Caché en Redis de Azure] en el sitio web de Microsoft.

Crear particiones en un almacén de datos Redis implica dividir los datos entre instancias del servicio Redis. Cada instancia constituye una sola partición. Caché en Redis de Azure abstrae los servicios de Redis detrás de una fachada y no los expone directamente. La manera más sencilla de implementar la creación de particiones es crear varias memorias caché Redis de Azure y repartir los datos entre ellas. Puede asociar cada elemento de datos con un identificador (una clave de partición) que especifica en qué caché debe almacenarse. La lógica de la aplicación cliente puede usar este identificador para enrutar las solicitudes a la partición apropiada. Este esquema es muy sencillo, pero si se cambia el esquema de partición (si se crean cachés en Redis de Azure adicionales, por ejemplo), es posible que las aplicaciones cliente deban volver a configurarse.

Redis nativo (no Caché en Redis de Azure) admite particiones de servidor basadas en la agrupación en clústeres de Redis. En este enfoque, los datos se dividen uniformemente entre servidores mediante un mecanismo hash. Cada servidor Redis almacena metadatos que describen el intervalo de claves hash que contiene la partición, y también contiene información acerca de qué claves hash se encuentran en las particiones de otros servidores. Las aplicaciones cliente simplemente envían solicitudes a cualquiera de los servidores Redis participantes (probablemente el servidor más cercano). El servidor Redis examina la solicitud del cliente y, si se puede resolver localmente, llevará a cabo la operación solicitada. En caso contrario, reenviará la solicitud al servidor apropiado. Este modelo se implementa mediante el uso de clústeres de Redis y se describe con más detalle en la página [Tutorial de clúster Redis] en el sitio web de Redis. La agrupación en clústeres de Redis es transparente para las aplicaciones de cliente y se pueden agregar servidores Redis adicionales al clúster (y los datos se pueden volver a dividir en particiones) sin necesidad de volver a configurar los clientes.

> [AZURE.IMPORTANT]Actualmente Caché en Redis de Azure no admite la agrupación en clústeres Redis. Si desea implementar este enfoque con Azure deberá implementar sus propios servidores Redis instalando Redis en un conjunto de máquinas virtuales de Azure y configurarlos manualmente. La página [Ejecución de Redis en una máquina virtual CentOS Linux VM en Azure] del sitio web de Microsoft le guía a través de un ejemplo que muestra cómo crear y configurar un nodo Redis que se ejecuta como una máquina virtual de Azure VM.

La página [Creación de particiones: cómo dividir los datos entre varias instancias de Redis] del sitio web de Redis proporciona información adicional acerca de cómo implementar las particiones con Redis. En el resto de esta sección se supone que se está implementando las particiones del lado cliente o asistidas por el proxy.

Deberá tener en cuenta los siguientes puntos a la hora de decidir cómo particionar los datos con la Caché en Redis de Azure:

- Caché en Redis de Azure no está pensada para actuar como un almacén de datos permanentes, por lo que, independientemente del esquema de particiones que implemente, el código de la aplicación debe estar preparado para aceptar que los datos no se encuentran en la memoria caché y tiene que recuperarse desde otro lugar.
- Mantener juntos los datos a los que se accede con frecuencia en la misma partición. Redis es un almacén de claves/valores eficaz que proporciona varios mecanismos muy optimizados de estructuración de datos, que van desde cadenas simples (en realidad, datos binarios de hasta 512 MB de longitud) hasta tipos agregados como listas (que pueden actuar como colas y pilas), conjuntos (ordenados y no ordenados) y hashes (que pueden agrupar campos relacionados, como los elementos que representan los campos de un objeto). Los tipos de agregado permiten asociar muchos valores relacionados con la misma clave; una clave de Redis identifica una lista, conjunto o hash en lugar de los elementos de datos que contiene. Estos tipos están disponibles con Caché en Redis de Azure y se describen en la página [Tipos de datos] del sitio web de Redis. Por ejemplo, en la parte de un sistema de comercio electrónico que realiza el seguimiento de los pedidos realizados por los clientes, los detalles de cada cliente podrían almacenarse en un hash de Redis con clave utilizando el identificador de cliente. Cada valor hash podría contener una colección de identificadores de pedido para el cliente. Un conjunto Redis independiente podría contener los pedidos, estructurados como algoritmos hash, ordenados por medio del Id. de pedido. La figura 10 muestra esta estructura. Tenga en cuenta que Redis no implementa ningún tipo de integridad referencial, por lo que es responsabilidad del desarrollador mantener las relaciones entre clientes y pedidos.

![](media/best-practices-data-partitioning/RedisCustomersandOrders.png)

_Figura 10. -Estructura sugerida en el almacenamiento de Redis para registrar los pedidos de clientes y sus detalles_

> [AZURE.NOTE]En Redis, todas las claves son valores de datos binarios (como cadenas de Redis) y pueden contener hasta 512 MB de datos, por lo que en teoría una clave puede contener casi cualquier información. No obstante, debe adoptar una convención de nomenclatura coherente para las claves que sea descriptiva del tipo de datos y que identifique la entidad, pero que no sea demasiado larga. Un enfoque común es utilizar las claves de la forma "entity\_type:ID", como "cliente: 99" para indicar la clave de cliente con el Id. 99.

- Puede implementar las particiones verticales almacenando información relacionada en agregaciones diferentes en la misma base de datos. Por ejemplo, en una aplicación de comercio electrónico podría almacenar información a la que accede frecuentemente acerca de los productos en un hash de Redis y la información detallada usada con menos frecuencia en otro. Ambos valores hash podrían usar el mismo identificador de producto como parte de la clave, por ejemplo "producto:_nn_" donde _nn_ es el identificador del producto de la información del producto y "product\_details: _nn_" para los datos detallados. Esta estrategia puede ayudar a reducir el volumen de datos que es probable que recuperen la mayoría de las consultas.
- Volver a crear particiones de un almacén de datos Redis es una tarea compleja y lenta. La agrupación en clústeres Redis puede particionar datos automáticamente, pero esta función no está disponible con Caché en Redis de Azure. Por lo tanto, al diseñar el esquema de partición, debe esforzarse en dejar suficiente espacio libre en cada partición para permitir el crecimiento de datos esperado en el tiempo. Sin embargo, recuerde que Caché en Redis de Azure está diseñado para almacenar datos en la caché temporalmente y que los datos que se mantienen en la memoria caché pueden tener una duración limitada especificada como valor de período de vida (TTL). Para los datos relativamente volátiles, el TTL debe ser corto, pero para datos estáticos, el período de vida puede ser mucho más largo. Debe evitar almacenar grandes cantidades de datos de larga duración en la memoria caché si el volumen de estos datos es probable que llene la caché. Puede especificar una directiva de expulsión que provoque que Caché en Redis de Azure elimine datos si el espacio es crítico.

	> [AZURE.NOTE]Caché en Redis de Azure le permite especificar el tamaño máximo de la memoria caché (de 250 MB a 53 GB) seleccionando el nivel de precios adecuado. Sin embargo, una vez que se ha creado una Caché en Redis de Azure, no podrá aumentar (ni reducir) su tamaño.

- Los lotes y transacciones de Redis no pueden abarcar varias conexiones, por lo que todos los datos afectados por un lote o transacción deben permanecer en la misma base de datos (partición).

	> [AZURE.NOTE]Una secuencia de operaciones en una transacción Redis no es necesariamente atómica. Los comandos que componen una transacción se comprueban y se ponen en cola antes de la ejecución, y si se produce un error durante esta fase se descarta toda la cola. Sin embargo, una vez que la transacción se ha enviado correctamente, se ejecutarán los comandos en cola en secuencia. Si un comando produce un error, solo se anulará ese comando; todos los comandos anteriores y posteriores de la cola se ejecutarán. Si necesita realizar operaciones atómicas. Para obtener más información, visite la página [Transacciones] en el sitio web de Redis.

- Redis admite un número limitado de operaciones atómicas, y las únicas operaciones de este tipo que admiten varias claves y valores son MGET (que devuelve una colección de valores de una lista especificada de claves) y MSET (que puede almacenar una colección de valores de una lista específica de claves). Si necesita usar estas operaciones, los pares de clave/valor a los que hacen referencia los comandos MSET y MGET deben almacenarse en la misma base de datos.

## Reequilibrio de particiones

A medida que un sistema empieza a crecer y el patrón de uso se empieza a entender mejor, es posible que sea necesario ajustar el esquema de partición. Esto puede deberse a que las particiones individuales atraen un volumen de tráfico desproporcionado y se convierten populares, dando lugar a una contención excesiva. Además, es posible que haya subestimado el volumen de datos de algunas particiones, provocando su acercamiento a los límites de la capacidad de almacenamiento de estas particiones. Independientemente de la causa, a veces es necesario volver a equilibrar las particiones para distribuir la carga uniformemente.

En algunos casos, los sistemas de almacenamiento de datos que no exponen públicamente la manera en que los datos se asignan a los servidores pueden equilibrar automáticamente las particiones dentro de los límites de los recursos disponibles. En otras situaciones, el reequilibrio es una tarea administrativa que consta de dos fases:

1. Determinar la estrategia de partición nueva para determinar qué particiones es posible que necesite dividir (o posiblemente combinar) y cómo asignar los datos a estas nuevas particiones diseñando nuevas claves de partición.
2. Migrar los datos afectados desde el esquema de partición antiguo al nuevo conjunto de particiones.

> [AZURE.NOTE]La asignación de las colecciones de DocumentDB a los servidores es transparente, pero todavía podría alcanzar los límites de capacidad y rendimiento de almacenamiento de una cuenta de DocumentDB, en cuyo caso puede que necesite volver a diseñar su esquema de partición y migrar los datos.

Dependiendo de la tecnología de almacenamiento de datos y del diseño de su sistema de almacenamiento de datos, es posible que pueda migrar datos entre particiones mientras están en uso (migración en línea). Si esto no es posible, puede que necesite establecer las particiones afectadas como no disponibles temporalmente mientras se cambia la ubicación de los datos (migración sin conexión).

## Migración sin conexión

La migración sin conexión podría decirse que es el enfoque más sencillo, ya que reduce las posibilidades de que se produzca contención; los datos que se están migrando no deben cambiar mientras se mueven y reestructuran.

Conceptualmente, este proceso consta de los siguientes pasos:

1. Marcar la partición sin conexión,
2. Dividir/combinar y mover los datos a las nuevas particiones,
3. Comprobar los datos,
4. Establecer las nuevas particiones en línea,
5. Quitar la partición antigua.

Para conservar algo de disponibilidad, podría ser posible marcar la partición original como de solo lectura en el paso 1 en lugar de establecerla como no disponible. Esto permitiría a las aplicaciones leer los datos mientras se mueven, pero no cambiarlos.

## Migración en línea

La migración en línea es más difícil de realizar, pero es menos problemática para los usuarios, ya que los datos permanecen disponibles durante todo el procedimiento. El proceso es similar al usado por la migración sin conexión, salvo que la partición original no está marcada como sin conexión (paso 1). Según la granularidad del proceso de migración (elemento por elemento o partición por partición), es posible que el código de acceso a datos de las aplicaciones cliente tenga que controlar la lectura y escritura de los datos mantenidos en dos ubicaciones (la partición original y la nueva partición)

Para obtener un ejemplo de una solución que admita la migración en línea, consulte [Escalado con la herramienta de división y combinación de Base de datos elástica], que se documenta en línea en el sitio web de Microsoft.

## Orientación y patrones relacionados

Es posible que los siguientes modelos también resulten importantes para su escenario al considerar las estrategias para implementar la coherencia de los datos:

- En la página [Data Consistency Primer], disponible en el sitio web de Microsoft, se describen las estrategias para mantener la coherencia en un entorno distribuido, como la nube.
- La página [Guía de creación de particiones de datos] del sitio web de Microsoft proporciona una visión general de cómo diseñar particiones para cumplir varios criterios en una solución distribuida.
- El [patrón de particionamiento], descrito en el sitio web de Microsoft, resume algunas estrategias comunes para el particionamiento de los datos.
- El [Patrón de la tabla de índice] descrito en el sitio web de Microsoft ilustra cómo crear índices secundarios sobre los datos. Este enfoque permite que una aplicación recupere rápidamente los datos mediante el uso de consultas que no hacen referencia a la clave principal de una colección.
- El [Patrón de vista materializada] tratado en el sitio web de Microsoft describe cómo generar vistas rellenadas previamente que resuman los datos para admitir las operaciones de consulta rápida. Este enfoque puede ser útil en un almacén de datos con particiones si las particiones que contienen los datos que se están resumiendo se distribuyen en varios sitios.
- En el artículo [Uso de CDN para Azure] se proporcionan instrucciones adicionales sobre la configuración y el uso de CDN con Azure.

## Más información

- En la página [¿Qué es Base de datos SQL de Azure] del sitio web de Microsoft se ofrece documentación detallada en la que se describe la creación y el uso de bases de datos SQL.
- En la página [Información general de las características de Base de datos elástica] del sitio web de Microsoft se proporciona una introducción completa a Base de datos elástica.
- El tema [Escalado con la herramienta de división y combinación de Base de datos elástica] del sitio web de Microsoft contiene información sobre el uso del servicio de división y combinación para administrar particiones de Base de datos elástica.
- La página [Objetivos de rendimiento y escalabilidad de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dn249410.aspx) del sitio web de Microsoft documenta los límites de tamaño y rendimiento actuales del almacenamiento de Azure.
- La página [Realización de transacciones de grupos de entidades] del sitio web de Microsoft proporciona información detallada sobre la implementación de operaciones transaccionales sobre las entidades almacenadas en almacenamiento de tablas de Azure.
- El artículo [Guía de diseño de tablas de Almacenamiento de Azure] del sitio web de Microsoft contiene información detallada sobre el particionamiento de datos en Almacenamiento de tablas de Azure.
- En la página [Uso de CDN para Azure] del sitio web de Microsoft se describe cómo replicar los datos almacenados en el almacenamiento de blobs de Azure mediante el uso de la Red de entrega de contenido (CDN) de Azure.
- La página [Administración de la capacidad de DocumentDB] del sitio web de Microsoft contiene información sobre cómo Azure DocumentDB asigna recursos a bases de datos.
- La página [¿Qué es Búsqueda de Azure?] del sitio web de Microsoft proporciona una descripción completa de las funciones disponibles con el servicio Búsqueda de Azure.
- La página [Límites de servicio en Búsqueda de Azure] del sitio web de Microsoft contiene información sobre la capacidad de cada instancia del servicio Búsqueda de Azure.
- La página [Tipos de datos compatibles (Búsqueda de Azure)] del sitio web de Microsoft resume los tipos de datos que puede usar en los documentos en los que se pueden hacer búsquedas e índices.
- La página [Caché en Redis de Azure] del sitio web de Microsoft proporciona una introducción a Caché en Redis de Azure.
- La página [Creación de particiones: cómo dividir los datos entre varias instancias de Redis] del sitio web de Redis proporciona información acerca de cómo implementar las particiones con Redis.
- La página [Ejecución de Redis en una máquina virtual CentOS Linux VM en Azure] del sitio web de Microsoft le guía a través de un ejemplo que muestra cómo crear y configurar un nodo Redis que se ejecuta como una máquina virtual de Azure VM.
- La página [Tipos de datos] del sitio web de Redis describe los tipos de datos que están disponibles con Caché en Redis de Azure.

[Caché en Redis de Azure]: http://azure.microsoft.com/services/cache/
[Objetivos de rendimiento y escalabilidad de almacenamiento de Azure]: storage/storage-scalability-targets.md
[Objetivos de rendimiento y escalabilidad del almacenamiento de Azure]: storage/storage-scalability-targets.md
[Guía de diseño de tablas de Almacenamiento de Azure]: storage/storage-table-design-guide.md
[Creación de una solución de Polyglot]: https://msdn.microsoft.com/library/dn313279.aspx
[Acceso a datos para soluciones altamente escalables: mediante SQL, NoSQL y persistencia Polyglot]: https://msdn.microsoft.com/library/dn271399.aspx
[Data Consistency Primer]: http://aka.ms/Data-Consistency-Primer
[Guía de creación de particiones de datos]: https://msdn.microsoft.com/library/dn589795.aspx
[Tipos de datos]: http://redis.io/topics/data-types
[Cuotas y límites de DocumentDB]: documentdb/documentdb-limits.md
[Límites y cuotas de DocumentDB]: documentdb/documentdb-limits.md
[Información general de las características de Base de datos elástica]: sql-database/sql-database-elastic-scale-introduction.md
[utilidad de migración de federaciones]: https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1
[Patrón de la tabla de índice]: http://aka.ms/Index-Table-Pattern
[Patrón de tabla de índice]: http://aka.ms/Index-Table-Pattern
[Administración de la capacidad de DocumentDB]: documentdb/documentdb-manage.md
[Administrar necesidades de capacidad de DocumentDB]: documentdb/documentdb-manage.md
[Patrón de vista materializada]: http://aka.ms/Materialized-View-Pattern
[Consulta de varias particiones]: sql-database/sql-database-elastic-scale-multishard-querying.md
[Creación de particiones: cómo dividir los datos entre varias instancias de Redis]: http://redis.io/topics/partitioning
[Niveles de rendimiento en DocumentDB]: documentdb/documentdb-performance-levels.md
[Realización de transacciones de grupos de entidades]: https://msdn.microsoft.com/library/azure/dd894038.aspx
[Tutorial de clúster Redis]: http://redis.io/topics/cluster-tutorial
[Ejecución de Redis en una máquina virtual CentOS Linux VM en Azure]: http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx
[Escalado con la herramienta de división y combinación de Base de datos elástica]: sql-database/sql-database-elastic-scale-overview-split-and-merge.md
[Uso de CDN para Azure]: cdn/cdn-how-to-use-cdn.md
[Cuotas de Bus de servicio]: service-bus/service-bus-quotas.md
[Límites de servicio en Búsqueda de Azure]: search/search-limits-quotas-capacity.md
[patrón de particionamiento]: http://aka.ms/Sharding-Pattern
[patrón particionamiento]: http://aka.ms/Sharding-Pattern
[Tipos de datos compatibles (Búsqueda de Azure)]: https://msdn.microsoft.com/library/azure/dn798938.aspx
[Transacciones]: http://redis.io/topics/transactions
[¿Qué es Búsqueda de Azure?]: search/search-what-is-azure-search.md
[Base de datos SQL de Azure]: sql-database/sql-database-technical-overview.md
[¿Qué es Base de datos SQL de Azure]: sql-database/sql-database-technical-overview.md

<!---HONumber=AcomDC_1223_2015-->