<properties
   pageTitle="Ejecución de ElasticSearch en Azure | Microsoft Azure"
   description="Instalación, configuración y ejecución de Elasticsearch en Azure."
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
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Ejecución de ElasticSearch en Azure

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

## Información general

Elasticsearch es una base de datos y un motor de búsqueda de código abierto de gran escalabilidad. Es adecuado para situaciones que requieren el análisis y el descubrimiento rápidos de información contenida en grandes conjuntos de datos. Entre los escenarios habituales se incluyen los siguientes:

- **Búsqueda de texto libre a gran escala**, con la que se pueden encontrar y recuperar rápidamente documentos que coinciden con combinaciones de términos de búsqueda.

- **Registro de eventos**, en que la información puede provenir de diversos orígenes. Es posible que se deban analizar los datos para determinar cómo una sucesión de eventos ha llevado a una conclusión específica.

- **Almacenamiento de datos capturados desde dispositivos remotos y otros orígenes**. Los datos podrían contener distinta información, pero un requisito común es que esta se presente en una serie de paneles para permitir que un operador comprenda el estado del sistema en general. Las aplicaciones también pueden usar la información para tomar decisiones rápidas sobre el flujo de datos y las operaciones empresariales que deben realizarse como resultado.

- **Control de existencias**, en el que se registran los cambios de inventario a medida que se venden los bienes. Los sistemas empresariales pueden usar esta información para notificar niveles de existencias a los usuarios y para llevar a cabo nuevos pedidos de existencias si hay escasez de un producto. Los analistas pueden examinar los datos en busca de tendencias para ayudar a determinar qué productos se venden bien según en qué circunstancias.

- Análisis **financiero**, cuando la información de mercado se recibe casi en tiempo real. Se pueden generar paneles que indican el rendimiento al minuto de diversos instrumentos financieros y que después pueden usarse como apoyo para adoptar decisiones de compraventa.

En este documento, se proporciona una introducción breve a la estructura general de Elasticsearch y después se describe cómo implementar un clúster de Elasticsearch con Azure. El documento se centra en los procedimientos recomendados para implementar un clúster de Elasticsearch, especialmente en los diferentes requisitos de rendimiento funcional y administración del sistema, y se tiene en cuenta cómo los requisitos deberían condicionar la configuración y la topología que seleccione.

> [AZURE.NOTE] En esta guía, se da por supuesto que conoce los conceptos básicos de [Elasticsearch][].

## Estructura de Elasticsearch

Elasticsearch es una base de datos de documentos altamente optimizada para actuar como motor de búsqueda. Los documentos se serializan en formato JSON. Los datos se mantienen en índices, que se implementan mediante [Apache Lucene][], aunque se ocultan los detalles mediante abstracción y no es necesario comprender totalmente Lucene para usar Elasticsearch.

### Clústeres, nodos, índices y particiones

Elasticsearch implementa una arquitectura en clúster que usa el particionamiento para distribuir datos entre varios nodos, y la replicación para proporcionar alta disponibilidad.

Los documentos se almacenan en índices. El usuario puede especificar qué campos de un documento se usan para identificarlo de forma exclusiva en un índice, o bien el sistema puede generar un campo de clave y valores automáticamente. El índice se utiliza para organizar los documentos físicamente, además de ser el medio principal de localizarlos. Además, Elasticsearch crea automáticamente un conjunto de estructuras adicionales que sirven de índices invertidos en los campos restantes para permitir realizar búsquedas rápidas y análisis dentro de una colección.

Un índice consta de un conjunto de particiones. Los documentos se distribuyen de forma uniforme entre las particiones mediante un mecanismo hash basándose en los valores de clave de índice y el número de particiones en el índice; una vez que se asigna un documento a una partición, no se moverá de ella a menos que se cambie su clave de índice. Elasticsearch distribuye particiones entre todos los nodos de datos disponibles en un clúster; es posible que inicialmente un solo nodo contenga una o varias particiones que pertenezcan al mismo índice, pero a medida que se agreguen nodos nuevos al clúster, Elasticsearch reubica las particiones para garantizar una carga equilibrada en el sistema. Este mismo proceso se aplica cuando se quitan nodos.

Los índices se pueden replicar. En este caso, se copia cada partición del índice. Elasticsearch garantiza que cada partición original de un índice (denominada "partición principal") y su réplica siempre residan en diferentes nodos.

> [AZURE.NOTE] El número de particiones de un índice no se puede cambiar con facilidad una vez creado este, aunque se pueden agregar réplicas.

Cuando se agrega o se modifica un documento, todas las operaciones de escritura se llevan a cabo en primer lugar en la partición principal y después en cada réplica. De forma predeterminada, este proceso se realiza de forma sincrónica como ayuda para garantizar la coherencia. Elasticsearch usa la simultaneidad optimista con control de versiones cuando escribe datos. Las operaciones de lectura se pueden completar con la partición principal o cualquiera de sus réplicas.

En la ilustración siguiente, se muestran los aspectos fundamentales de un clúster de Elasticsearch que consta de tres nodos. Se ha creado un índice compuesto por dos particiones principales con dos réplicas para cada una (seis particiones en total).

![](media/guidance-elasticsearch/general-cluster1.png)

*Clúster simple de Elasticsearch compuesto por dos nodos principales y dos conjuntos de réplicas*

En este clúster, la partición principal 1 y la partición principal 2 se encuentran en nodos distintos para facilitar el equilibrio de la carga entre ellas. Las réplicas se distribuyen de forma similar. Si se produce un error en un solo nodo, los demás nodos disponen de suficiente información para que el sistema siga funcionando. Si es necesario, Elasticsearch promoverá una partición de réplica para convertirla en principal si la partición principal correspondiente no está disponible. Cuando se comienza a ejecutar un nodo, este puede iniciar un nuevo clúster (si es el primer nodo del clúster) o unirse a uno existente. El clúster al que pertenece un nodo está determinado por la configuración `cluster.name` en el archivo elasticsearch.yml.

### Roles de los nodos

Los nodos de un clúster de Elasticsearch pueden desempeñar los siguientes roles:

- **Nodo de datos** que puede incluir una o más particiones que contienen datos de índice.

- **Nodo cliente** que no contiene datos de índice, pero que controla las solicitudes entrantes realizadas por aplicaciones cliente ante el nodo de datos adecuado.
 
- **Nodo maestro** que no contiene datos de índice pero que lleva a cabo operaciones de administración del clúster, como mantener y distribuir información de enrutamiento en el clúster (la lista de qué nodos contienen qué particiones), determinar qué nodos están disponibles, reubicar particiones según aparezcan y desaparezcan nodos, y coordinar la recuperación tras producirse un error en un nodo. Se pueden configurar varios nodos como maestros, pero realmente solo se elegirá uno para que se encargue de las funciones de maestro. Si se produce un error en este nodo, se elige uno de entre los otros nodos maestros válidos, el cual asume dichas funciones.

De forma predeterminada, los nodos de Elasticsearch desempeñan los tres roles (para permitirle crear un clúster completo de trabajo en un solo equipo con fines de desarrollo y prueba de concepto), pero puede cambiar sus operaciones mediante las configuraciones *node.data* y *node.master* en el archivo *elasticsearch.yml*, como se muestra a continuación:

```yaml
# Configuration for a data node
node.data: true
node.master: false
```

```yaml
# Configuration for a client node
node.data: false
node.master: false
```

```yaml
# Configuration for a master node
node.data: false
node.master: true
```

> [AZURE.NOTE] El nodo maestro que se elija es esencial para el bienestar del clúster. Los demás nodos hacen ping a él con regularidad para garantizar que sigue estando disponible. Si el nodo maestro elegido también actúa como nodo de datos, existe la posibilidad de que esté ocupado y no responda a estos pings. En esta situación, se considera que se ha producido un error en el maestro y se elige en su lugar a uno de los otros nodos maestros.
> 
> Si el maestro original realmente aún está disponible, esto podría dar lugar a la existencia de dos maestros elegidos, lo que produce un problema de "cerebro dividido" que puede causar daños en los datos y otros problemas.
> 
> En el documento [Configuración de resistencia y recuperación en Elasticsearch en Azure][], se describe cómo debe configurar el clúster para reducir las posibilidades de que esto ocurra. Sin embargo, en última instancia es una buena estrategia que un clúster de moderado a grande use nodos maestros dedicados que no se responsabilicen de la administración de datos.

Los nodos de un clúster comparten información sobre los demás nodos del clúster (mediante el [protocolo de propagación][]) y sobre qué particiones contienen. Las aplicaciones cliente que almacenan y capturan datos se pueden conectar a cualquier nodo de un clúster y las solicitudes se redirigirán de forma transparente al nodo correcto. Cuando un aplicación cliente solicita datos del clúster, el primer nodo en recibir la solicitud se encarga de dirigir la operación, de comunicarse con cada nodo pertinente para capturar los datos y, después, de agregar el resultado antes de devolverlo a la aplicación cliente.

El uso de nodos cliente para controlar las solicitudes libera a los nodos de datos de estas tareas de dispersión y recopilación, y les permite dedicar su tiempo a suministrar datos. Para impedir que las aplicaciones cliente se comuniquen accidentalmente con nodos de datos (forzándoles a actuar como nodos cliente), deshabilite el transporte HTTP para los nodos de datos:

```yaml
http.enabled: false
```

Los nodos de datos siguen pudiendo comunicarse con otros nodos de datos, nodos cliente y nodos maestros dedicados en la misma red mediante el módulo de transporte de Elasticsearch (que usa sockets TCP para conectarse directamente entre nodos), pero las aplicaciones cliente solo pueden conectarse a nodos cliente a través de HTTP. En la ilustración siguiente, se muestra una topología compuesta por una mezcla de un nodo maestro dedicado, nodos de datos y nodos cliente en un clúster de Elasticsearch.

![](media/guidance-elasticsearch/general-cluster2.png)

*Clúster de Elasticsearch con distintos tipos de nodos*

### Costos y ventajas del uso de nodos cliente

Cuando una aplicación envía una consulta a un clúster de Elasticsearch, el nodo al que se conecta la aplicación es responsable de dirigir el proceso de consulta. El nodo reenvía la solicitud a cada nodo de datos, recopila los resultados y devuelve la información acumulada a la aplicación. Si una consulta incluye agregaciones y otros cálculos, el nodo al que se conecta la aplicación realiza las operaciones necesarias después de recuperar los datos de cada uno de los demás nodos. Este proceso de dispersión y recopilación puede consumir importantes recursos de memoria y procesamiento.

El uso de nodos cliente dedicados para realizar estas tareas permite que los nodos de datos se centren en la administración y el almacenamiento de datos. Como resultado, muchos de los escenarios que implican consultas complejas y agregaciones pueden beneficiarse del uso de nodos cliente dedicados. Sin embargo, el impacto de usarlos probablemente variará según el escenario, la carga de trabajo y el tamaño del clúster.

Por ejemplo, las cargas de trabajo de ingesta de datos pueden ser menos eficientes si se usan nodos cliente a causa del "salto" de red adicional que se necesita al almacenar datos. En un clúster de 3 nodos con 6 particiones, si el sistema no está configurado con nodos cliente dedicados, siento todos los factores de entorno y las cargas de nodo iguales, existe una probabilidad entre tres de que una aplicación que almacene o modifique datos se conecte directamente a la partición más apropiada, lo que elimina la necesidad de realizar otro salto de red en un tercio de los casos.

Por otro lado, las cargas de trabajo que realizan agregaciones complejas podrían beneficiarse del uso de clientes dedicados, ya que solo habrá un nodo responsable de cada conjunto de operaciones de dispersión y recopilación realizadas. En un entorno de cargas de trabajo mixtas, debe estar preparado para ejecutar pruebas de rendimiento para evaluar el impacto de usar nodos cliente en las cargas de trabajo específicas.

> [AZURE.NOTE] Consulte el documento [Ajuste de la agregación de datos y del rendimiento de qQuery con Elasticsearch en Azure][] para más información sobre este proceso de optimización.

### Conexión a un clúster

Elasticsearch expone una serie de API de REST para crear aplicaciones cliente y enviar solicitudes a un clúster. Si está desarrollando aplicaciones con .NET Framework, existen dos API de niveles más altos: [Elasticsearch.Net y NEST][].

Si va a crear aplicaciones cliente mediante Java, puede usar la [API de cliente de nodo][] para crear nodos cliente de forma dinámica y agregarlos al clúster. La creación dinámica de nodos cliente es cómoda si el sistema usa un número relativamente pequeño de conexiones de larga duración. El nodo maestro proporciona a los nodos cliente creados mediante la API de nodo el mapa de enrutamiento del clúster (los detalles de qué nodos contienen qué particiones). Esta información permite que la aplicación de Java se conecte directamente a los nodos correspondientes al indexar o consultar datos, lo que reduce el número de saltos que podrían ser necesarios cuando se usan otras API.

El costo de este enfoque es la sobrecarga de inscribir el nodo cliente en el clúster. Si un gran número de nodos cliente aparecen y desaparecen rápidamente, el impacto de mantener y distribuir el mapa de enrutamiento del clúster puede ser considerable.

**Equilibrio de carga de conexión**

Elasticsearch habilita varios mecanismos para implementar el equilibrio de carga de conexión. En la siguiente lista, se resumen algunos enfoques habituales:

**Equilibrio de carga basado en el cliente**: si está creando aplicaciones cliente que usan las API Elasticsearch.Net o NEST, puede usar un grupo de conexiones para distribuir mediante round robin las solicitudes de conexión entre los nodos, lo que ayuda a equilibrar la carga de solicitudes sin necesidad de un equilibrador de carga externo. En el siguiente fragmento de código, se muestra cómo crear un objeto *ElasticsearchClient* configurado con las direcciones de tres nodos. Las solicitudes de la aplicación cliente se distribuirán entre estos nodos:

```csharp
// C#
var node1 = new Uri("http://node1.example.com:9200");
var node2 = new Uri("http://node2.example.com:9200");
var node3 = new Uri("http://node3.example.com:9200");

var connectionPool = new SniffingConnectionPool(new[] {node1, node2, node3});
var config = new ConnectionConfiguration(connectionPool);
var client = new ElasticsearchClient(config);
```

> [AZURE.NOTE] Hay una funcionalidad similar disponible para las aplicaciones de Java a través de la [API de cliente de transporte][].

**Equilibrio de carga basado en el servidor**: puede usar un equilibrador de carga distinto para distribuir las solicitudes a los nodos. Este enfoque ofrece la ventaja de la transparencia de las direcciones; no es necesario configurar las aplicaciones cliente con los detalles de cada nodo, lo que permite agregar, quitar o reubicar nodos sin modificar ningún código de cliente. En la ilustración siguiente, se muestra una configuración que usa un equilibrador de carga para enrutar solicitudes a un conjunto de nodos cliente, aunque se puede utilizar la misma estrategia para conectarse directamente a los nodos de datos si no se emplean nodos cliente.

![](media/guidance-elasticsearch/general-clientappinstances.png)

*Instancias de aplicación cliente conectadas a un clúster de Elasticsearch a través del Equilibrador de carga de Azure*

> [AZURE.NOTE] Puede usar el [Equilibrador de carga de Azure][] para exponer el clúster a la Internet pública, o bien puede usar un [equilibrador de carga interno][] si las aplicaciones cliente y el clúster están contenidos por completo en la misma red virtual privada.

**Equilibrio de carga personalizado**: puede usar [nginx][] como servidor proxy inverso en lugar del Equilibrador de carga de Azure. Nginx proporciona varios métodos de equilibrio de carga, como round robin, con menores conexiones (se enruta una solicitud al destino con el menor número conexiones actuales) y hash basado en la dirección IP del cliente.

> [AZURE.NOTE] Puede implementar un servidor nginx como máquina virtual de Azure. Para mantener la disponibilidad, debe crear al menos dos servidores nginx en el mismo conjunto de disponibilidad de Azure.

Debe considerar los siguientes puntos al decidir si utilizar el equilibrio de carga y qué implementación va a usar:

- La conexión al mismo nodo para controlar todas las solicitudes de todas las instancias de una aplicación puede hacer que ese nodo se convierta en un cuello de botella. A medida que el número de subprocesos disponibles en el nodo se agote, las solicitudes se pondrán en cola y es posible que se rechacen si la longitud de la cola resulta excesiva (no codifique los detalles de conexión de un solo nodo de forma rígida en código de la aplicación que pueda implementarse en muchos usuarios).

- La forma en que el mecanismo de round robin de las API Elasticsearch.Net, NEST y de cliente de transporte controla las solicitudes de conexión con error es reintentando la conexión con el siguiente nodo disponible en el grupo de conexiones. La conexión a un nodo del grupo que no responde se puede marcar temporalmente como *muerta*; puede que cobre vida de nuevo más adelante, y el grupo puede hacer ping al nodo para determinar si se ha vuelto a activar.

- El Equilibrador de carga de Azure puede redirigir de forma transparente solicitudes a nodos basándose en una serie de factores (dirección IP del cliente, puerto del cliente, dirección IP de destino, puerto de destino, tipo de protocolo). Según esta estrategia, lo más probable es que se dirija una instancia de una aplicación cliente ejecutada en un equipo determinado al mismo nodo de Elasticsearch. En función de la configuración de sondeo del equilibrador de carga, si se produce un error en el servicio de Elasticsearch en este nodo pero la máquina virtual en sí sigue ejecutándose, todas las conexiones a este nodo agotarán el tiempo de espera, mientras que puede que las conexiones de otras instancias de cliente a otros nodos sigan funcionando correctamente.

- Se puede configurar el Equilibrador de carga de Azure para que quite un nodo de la rotación si no responde adecuadamente a las solicitudes de sondeo de estado realizadas por el equilibrador de carga.

### Detección de nodos

Elasticsearch se basa en la comunicación punto a punto, por lo que la detección de otros nodos en un clúster es una parte importante del ciclo de vida de un nodo. La detección de nodos permite que se agreguen de forma dinámica nuevos nodos de datos a un clúster, lo que a su vez permite que el clúster se escale horizontalmente de forma transparente. Además, si un nodo de datos no responde a las solicitudes de comunicaciones de otros nodos, un nodo maestro puede decidir que se ha producido un error en el nodo de datos y adoptar las medidas necesarias para volver a asignar las particiones que contuviera a otros nodos de datos operativos.

La detección de nodos en Elasticsearch se controla mediante un módulo de detección. El módulo de detección es un complemento que se puede modificar para que use un mecanismo de detección diferente. El módulo de detección predeterminado ([Zen][]) hace que un nodo emita solicitudes de ping para buscar otros nodos en la misma red. Si los otros nodos responden, intercambian información mediante el protocolo de propagación. Entonces, un nodo maestro puede distribuir las particiones al nuevo nodo (si es un nodo de datos) y volver a equilibrar el clúster.

El módulo de detección Zen también controla el proceso de elección de maestro y el protocolo para detectar errores de nodo.

Antes de la versión 2.0 de Elasticsearch, el módulo de detección Zen usaba comunicaciones de multidifusión para permitir que los nodos se pusieran en contacto entre ellos. Esto facilita enormemente la introducción de un nuevo nodo en un clúster, pero también puede causar problemas de seguridad si otra instalación de Elasticsearch en la misma red usa el mismo nombre de clúster; la nueva instalación se considera parte del mismo clúster y es posible que se dirija a las particiones a nodos en esta instalación.

Además, si está ejecutando nodos de Elasticsearch como máquinas virtuales de Azure, no se admite la mensajería de multidifusión. Por estas razones, debe configurar el módulo de detección Zen para que use la mensajería de unidifusión y proporcionar una lista de nodos de contacto válidos en el archivo de configuración elasticsearch.yml.

> [AZURE.NOTE] Con Elasticsearch 2.0 y versiones posteriores, la multidifusión ya no es el mecanismo de detección predeterminado.

Si hospeda un clúster de Elasticsearch dentro de una red virtual de Azure, puede especificar que la dirección IP privada asignada por DHCP que se proporciona a cada máquina virtual del clúster permanezca asignada (estática). Puede configurar la mensajería de unidifusión en el módulo de detección Zen con estas direcciones IP estáticas. Si está usando máquinas virtuales con direcciones IP dinámicas, tenga en cuenta que, si una máquina virtual se detiene y se reinicia, es posible que se le asigne una nueva dirección IP, lo que dificultaría la detección. Para enfrentarse a este escenario, puede cambiar el módulo de detección Zen por el [complemento de nube de Azure][]. Este complemento usa la API de Azure para implementar el mecanismo de detección, que se basa en la información de suscripción de Azure.

> [AZURE.NOTE] La versión actual del complemento de nube de Azure requiere que se instale el certificado de administración para su suscripción de Azure en el almacén de claves de Java en el nodo de Elasticsearch y que se proporcionen la ubicación y las credenciales para acceder al almacén de claves en el archivo elasticsearch.yml. Este archivo se almacena en texto no cifrado, por lo que es de vital importancia que se asegure de que solo acceda a él la cuenta que ejecuta el servicio de Elasticsearch.
> 
> Además, es posible que este enfoque no sea compatible con las implementaciones del Administrador de recursos de Azure (ARM). Por estas razones, se recomienda que use direcciones IP estáticas para los nodos maestros y que use estos nodos para implementar la mensajería de unidifusión del módulo de detección Zen en el clúster. En la configuración siguiente (tomada del archivo elasticsearch.yml para un nodo de datos de ejemplo), las direcciones IP de host hacen referencia a nodos maestros del clúster:

```yaml
discovery.zen.ping.multicast.enabled: false  
discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]
```

## Instrucciones generales para el sistema

Elasticsearch se puede ejecutar en diversos equipos, desde un único portátil hasta un clúster de servidores de gama alta. Sin embargo, cuantos más recursos disponibles de memoria, potencia de computación y discos rápidos haya, mejor será el rendimiento. En las siguientes secciones, se ofrece un resumen de los requisitos básicos de hardware y software para ejecutar Elasticsearch.

### Requisitos de memoria

Elasticsearch intenta almacenar los datos en memoria por velocidad. Un servidor de producción que hospede un nodo para una implementación comercial típica de tamaño moderado o para empresa en Azure debe tener entre 14 y 28 GB de RAM (máquinas virtuales D3 o D4). **Distribuya la carga entre más nodos en lugar de crear nodos con más memoria** (se ha demostrado en algunos experimentos que el uso de nodos mayores con más memoria puede prolongar los tiempos de recuperación en caso de error). Sin embargo, aunque la creación de clústeres con un gran número de nodos pequeños puede aumentar la disponibilidad y el rendimiento, también eleva el esfuerzo precisado para administrar y mantener dicho sistema.

**Asigne el 50 % de la memoria disponible en un servidor al montón de Elasticsearch**. Si usa Linux, establezca la variable de entorno ES\_HEAP\_SIZE antes de ejecutar Elasticsearch. Como alternativa, si usa Windows o Linux, puede especificar el tamaño de la memoria en los parámetros `Xmx` y `Xms` cuando inicie Elasticsearch; establezca ambos en el mismo valor para evitar que la Máquina Virtual Java (JVM) cambie el tamaño del montón en tiempo de ejecución. En todo caso, **no asigne más de 30 GB**. Use la memoria restante para la caché de archivos del sistema operativo.

> [AZURE.NOTE] Elasticsearch usa la biblioteca de Lucene para crear y administrar índices. Las estructuras de Lucene usan un formato basado en disco y el almacenamiento de estas estructuras en la memoria caché del sistema de archivos mejorará considerablemente el rendimiento.

Tenga en cuenta que el tamaño óptimo máximo del montón para Java en un equipo de 64 bits queda justo por encima de los 30 GB. Más allá, Java pasa a usar un mecanismo extendido para hacer referencia a objetos del montón que aumenta los requisitos de memoria para cada objeto y reduce el rendimiento.

Por otra parte, puede que el recolector de elementos no utilizados predeterminado de Java (marcado y limpieza simultáneos) presente un rendimiento inferior al óptimo si el tamaño del montón es superior a 30 GB. Actualmente, no se recomienda cambiar a un recolector de elementos no utilizados diferente, ya que Elasticsearch y Lucene solo se han probado con el predeterminado.

No exceda la asignación de memoria ya que el cambio de la memoria principal al disco afectará gravemente el rendimiento. Si es posible, deshabilite el intercambio completamente (los detalles dependen del sistema operativo). Si esto no es posible, habilite la configuración *mlockall* en el archivo de configuración de Elasticsearch (elasticsearch.yml) de la forma siguiente:

```yaml
bootstrap.mlockall: true
```

Esta opción de configuración hace que la JVM bloquee su memoria y evite que se intercambie por el sistema operativo.

### Requisitos de disco y sistema de archivos

Use discos de datos con respaldo de almacenamiento Premium para almacenar particiones. Se debe ajustar el tamaño de los discos para contener la máxima cantidad de datos previstos en sus particiones, aunque es posible agregar discos adicionales más adelante. Puede extender una partición por varios discos en un nodo.

> [AZURE.NOTE] Elasticsearch comprime los datos de los campos almacenados mediante el algoritmo LZ4 y, en Elasticsearch 2.0 y versiones posteriores, se puede cambiar el tipo de compresión. Puede cambiar el algoritmo de compresión a DEFLATE tal como lo usan las utilidades *zip* y *gzip*. Esta técnica de compresión utiliza muchos recursos, pero se debe considerar el uso de índices fríos, como datos de registro archivados. Este enfoque puede ayudar a reducir el tamaño del índice.

No es esencial que todos los nodos de un clúster tengan el mismo diseño de disco y capacidad. Sin embargo, un nodo con una capacidad de disco muy grande en comparación con otros nodos de un clúster atraerá más datos y requerirá una capacidad de procesamiento mayor para tratar estos datos. Por lo tanto, el nodo puede convertirse en "caliente" comparado con otros nodos lo que, a su vez, puede afectar al rendimiento.

Si es posible, utilice RAID 0 (división). Otras formas de RAID que implementan la creación de reflejos y la paridad no son necesarias, ya que Elasticsearch proporciona su propia solución de alta disponibilidad mediante las réplicas.

> [AZURE.NOTE] Antes de Elasticsearch 2.0.0, también se podía implementar la división en el nivel de software si se especificaban varios directorios en la opción de configuración *path.data*. En Elasticsearch 2.0.0, ya no se admite esta forma de división. Por el contrario, pueden asignarse varias particiones a diferentes rutas de acceso, pero todos los archivos de una sola partición se escribirán en la misma ruta de acceso. **Si se requiere la división, hay que dividir los datos en el nivel de hardware o del sistema operativo**.

Para maximizar el rendimiento del almacenamiento, cada **máquina virtual debe tener una cuenta de almacenamiento Premium dedicada**.

La biblioteca de Lucene puede usar un gran número de archivos para almacenar datos de índice y Elasticsearch puede abrir un número significativo de sockets para la comunicación entre los nodos y con los clientes. Asegúrese de que el sistema operativo está configurado para admitir un número suficiente de descriptores de archivo abiertos (hasta 64 000 si hay suficiente memoria disponible). Tenga en cuenta que la configuración predeterminada para muchas distribuciones de Linux limita el número de descriptores de archivos abiertos en 1024, lo que es muy poco.

Elasticsearch utiliza una combinación de combinación de E/S asignada a la memoria (mmap) IO y E/S de Java New IO (NIO) para optimizar el acceso simultáneo a los archivos de datos y a los índices. Si usa Linux, debe configurar el sistema operativo para asegurarse de que hay suficiente memoria virtual disponible con espacio para áreas de asignación de memoria de 256 K.

> [AZURE.NOTE] Muchas distribuciones Linux usan de forma predeterminada el programador de Cola completamente razonable (CFQ) al organizar la escritura de datos en disco. Este programador no está optimizado para unidades SSD. Considere la posibilidad de volver a configurar el sistema operativo para usar el programador NOOP o el programador de fecha límite, que son más eficaces para unidades SSD.

### Requisitos de la CPU

Las máquinas virtuales de Azure están disponibles en una variedad de configuraciones de CPU y admiten entre 1 y 32 núcleos. Para un nodo de datos, un buen punto de partida es considerar una máquina virtual estándar de la serie DS y seleccionar las SKU DS3 (4 núcleos) o DS4 (8 núcleos). La serie DS3 también proporciona 14 GB de RAM, mientras que la DS4 incluye 28 GB.

La serie GS (para almacenamiento Premium) y la serie G (para almacenamiento estándar) usan procesadores Xeon E5 V3, que pueden resultar útiles para cargas de trabajo de proceso intensivo, como las agregaciones a gran escala. Para la información más reciente, visite [Tamaños de máquinas virtuales][].

### Requisitos de red

Elasticsearch requiere un ancho de banda de red entre 1 y 10 Gbps, según el tamaño y la volatilidad de los clústeres que implementa. Elasticsearch migra las particiones entre nodos cuando se agregan más nodos a un clúster. Elasticsearch supone que el tiempo de comunicación entre todos los nodos es más o menos equivalente y no tiene en cuenta las ubicaciones relativas de particiones mantenidas en esos nodos. Además, la replicación puede representar una importante E/S de red entre particiones. Por estas razones, **evite crear clústeres en nodos que estén en diferentes regiones**.

### Requisitos de software

Puede ejecutar Elasticsearch en Windows o en Linux. El servicio Elasticsearch se implementa como una biblioteca jar de Java y tiene dependencias en otras bibliotecas de Java que se incluyen en el paquete de Elasticsearch. Debe instalar la máquina virtual de Java de Java 7 (actualización 55 o posterior) o Java 8 (actualización 20 o posterior) para ejecutar Elasticsearch.

> [AZURE.NOTE] Aparte de los parámetros de memoria *Xmx* y *Xms* (especificados como opciones de línea de comandos en el motor de Elasticsearch, consulte [Requisitos de memoria][]), no modifique los valores de configuración predeterminados de la Máquina virtual Java. Elasticsearch se ha diseñado usando los valores predeterminados; si se cambian, Elasticsearch se puede desajustar y registrar un rendimiento deficiente.

### Implementación de ElasticSearch en Azure

Aunque no es difícil implementar una instancia única de Elasticsearch, la creación de un número de nodos y la instalación y configuración de Elasticsearch en cada uno de ellos puede ser un proceso lento y propenso a errores. Si va a ejecutar Elasticsearch en máquinas virtuales de Azure, tiene dos opciones que pueden ayudar a reducir las posibilidades de errores.

- Usar la [plantilla de Azure Resource Manager (ARM)](http://azure.microsoft.com/documentation/templates/elasticsearch/) para crear el clúster. Esta plantilla está totalmente parametrizada para que pueda especificar el tamaño y el nivel de rendimiento de las máquinas virtuales que implementan los nodos, el número de discos que se van a usar y otros factores comunes. La plantilla puede crear un clúster basado en Windows Server 2012 o Ubuntu Linux 14.0.4.

- Usar scripts que se puedan automatizar o ejecutar en modo desatendido. Los scripts que pueden crear e implementar un clúster de Elasticsearch están disponibles en el sitio de [plantillas de inicio rápido de Azure][].

## Escalabilidad y tamaño de nodos y clústeres 

Elasticsearch permite un número de topologías de implementación, diseñadas para admitir distintos requisitos y niveles de escala. En esta sección se describen algunas topologías comunes, así como las consideraciones para implementar clústeres basados en estas topologías.

### Topologías de Elasticsearch

En la ilustración siguiente, se muestra un punto de partida para diseñar una topología de Elasticsearch para Azure:

![](media/guidance-elasticsearch/general-startingpoint.png)

*Punto de partida sugerido para crear un clúster de Elasticsearch con Azure*

Esta topología consta de seis nodos de datos junto con tres nodos cliente y tres nodos maestros (solo se elegirá un nodo maestro, los otros dos se elegirán en caso de que el maestro seleccionado produzca un error). Cada nodo se implementa como una máquina virtual independiente. Las aplicaciones web de Azure se dirigen a los nodos cliente a través de un equilibrador de carga.

En este ejemplo, todos los nodos y las aplicaciones web residen en la misma red virtual de Azure, que los aísla eficazmente del mundo exterior. Si el clúster debe estar disponible externamente (posiblemente como parte de una solución híbrida que incorpore clientes locales), podrá usar el Equilibrador de carga de Azure para proporcionar una dirección IP pública, pero deberá tomar precauciones de seguridad adicionales para impedir el acceso no autorizado al clúster.

La máquina "JumpBox" opcional es una máquina virtual que solo está disponible para los administradores. Esta máquina virtual tiene una conexión de red a la red virtual de Azure, pero también una conexión de red con orientación externa para permitir el inicio de sesión de administrador desde una red externa (este inicio de sesión debe protegerse con una contraseña segura o un certificado). El administrador puede iniciar sesión en la máquina JumpBox y, después, conectarse desde allí directamente a cualquiera de los nodos del clúster.

Otros enfoques incluyen el uso de una VPN de sitio a sitio entre una organización y la red virtual o circuitos [ExpressRoute][] para conectarse a la red virtual. Estos mecanismos permiten el acceso administrativo al clúster sin exponer al clúster a la red Internet pública.

Para mantener la disponibilidad de la máquina virtual, los nodos de datos se agrupan en el mismo conjunto de disponibilidad de Azure. De igual forma, se conservan los nodos cliente en otro conjunto de disponibilidad y los nodos maestro se almacenan en un tercer conjunto de disponibilidad.

Esta topología es relativamente fácil de escalar horizontalmente; basta con agregar más nodos del tipo adecuado y asegurarse de que estén configurados con el mismo nombre de clúster en el archivo elasticsearch.yml. Los nodos cliente también deben agregarse al grupo de back-end para el Equilibrador de carga de Azure.

**Clústeres de localización geográfica**

**No extienda los nodos de un clúster entre regiones, ya que esto puede afectar al rendimiento de la comunicación entre nodos** (consulte [Requisitos de red][]). La localización geográfica de los datos cerca de los usuarios en distintas regiones requiere la creación de varios clústeres. En esta situación, necesitará tener en cuenta cómo sincronizar los clústeres, o incluso si hacerlo. Entre las posibles soluciones se incluyen:

Los [nodos tribales][] se parecen a un nodo cliente, salvo en que pueden participar en varios clústeres de Elasticsearch y verlos todos como un gran clúster. Los clústeres siguen administrando localmente los datos (no se propagan las actualizaciones a través de límites de clúster), pero todos los datos están visibles. Un nodo tribal puede consultar, crear y administrar los documentos de cualquier clúster.

Las restricciones principales consisten en que no se puede utilizar un nodo tribal para crear un nuevo índice y los nombres de índice deben ser exclusivos en todos los clústeres. Por lo tanto, es importante tener en cuenta cómo se denominarán los índices al diseñar clústeres a los que vaya a acceder desde los nodos tribales.

Mediante este mecanismo, los clústeres pueden contener aquellos datos a los que con más probabilidad accederán las aplicaciones cliente locales, pero estos clientes pueden acceder y modificar datos remotos aunque con una posible latencia extendida. En la ilustración siguiente, se muestra un ejemplo de esta topología. Está resaltado el nodo tribal del clúster 1. Los demás clústeres también pueden tener nodos tribales, aunque no se muestran en el diagrama:

![](media/guidance-elasticsearch/general-tribenode.png)

*Aplicación de cliente que accede a varios clústeres a través de un nodo tribal*

En este ejemplo, la aplicación cliente se conecta al nodo tribal en el clúster 1 (ubicado en la misma región), pero este nodo está configurado para poder acceder al clúster 2 y al clúster 3, que pueden estar ubicados en diferentes regiones. La aplicación cliente puede enviar solicitudes que recuperan o modifican datos en alguno de los clústeres.

> [AZURE.NOTE] Los nodos tribales requieren la detección de multidifusión para conectarse a los clústeres, lo que puede presentar un problema de seguridad. Consulte la sección [Detección de nodos][] para más detalles.

- Implementación de la replicación geográfica entre clústeres. En este enfoque, se propagan los cambios realizados en cada clúster casi en tiempo real para clústeres ubicados en otros centros de datos. Están disponibles complementos de terceros para Elasticsearch que admiten esta funcionalidad, como el [complemento de cambios de PubNub][].

- Uso del [módulo de restauración y de instantáneas de Elasticsearch][]. Si los datos son lentos y solo se modifican en un único clúster, puede considerar el uso de instantáneas para realizar una copia periódica de los datos. Después, puede restaurarlas en otros clústeres (las instantáneas pueden almacenarse en Almacenamiento de blobs de Azure si ha instalado el [complemento de nube de Azure][]). Sin embargo, esta solución no funciona bien con datos que cambian rápidamente o si se pueden cambiar en más de un clúster.

**Topologías de pequeña escala**

Las topologías a gran escala que componen clústeres de nodos maestro, cliente y de datos dedicados podrían no ser adecuadas para todos los escenarios. Si está creando un sistema de desarrollo o de producción a pequeña escala, tenga en cuenta el clúster de tres nodos que se muestra en la siguiente ilustración.

Las aplicaciones cliente se conectan directamente a cualquier nodo de datos disponibles en el clúster. El clúster contiene tres particiones etiquetadas como P1-P3 (para permitir el crecimiento) más réplicas etiquetadas como R1-R3. El uso de tres nodos permite a Elasticsearch distribuir las particiones y las réplicas de modo que si un único nodo produce un error no se perderá ningún dato.

![](media/guidance-elasticsearch/general-threenodecluster.png)

*Clúster de tres nodos con tres particiones y réplicas*

Si se ejecuta una instalación de desarrollo en un equipo independiente, puede configurar un clúster con un único nodo, que actúa como maestro, cliente y almacenamiento de datos. Como alternativa, puede iniciar varios nodos que se ejecutan como un clúster en el mismo equipo iniciando más de una instancia de Elasticsearch. En la ilustración siguiente se muestra un ejemplo.

![](media/guidance-elasticsearch/general-developmentconfiguration.png)

*Configuración de desarrollo que ejecuta varios nodos de Elasticsearch en la misma máquina*

Tenga en cuenta que no se recomienda ninguna de estas configuraciones independientes para un entorno de producción, ya que pueden causar contención a menos que la máquina de desarrollo tenga una cantidad significativa de memoria y varios discos rápidos. Además, no proporcionan ninguna garantía de alta disponibilidad; si se produce un error en la máquina, se perderán todos los nodos.

### Escalado de un clúster y de nodos de datos

Puede escalar Elasticsearch en dos dimensiones: verticalmente (mediante máquinas más grandes y más potentes) y horizontalmente (mediante el reparto de la carga entre las máquinas).

**Escalado vertical de nodos de datos de Elasticsearch**

Si hospeda un clúster de Elasticsearch mediante el uso de máquinas virtuales de Azure, cada nodo puede corresponder a una máquina virtual. El límite de escalabilidad vertical de un nodo depende en gran medida de la SKU de la máquina virtual y de las restricciones generales aplicadas a las cuentas de almacenamiento individuales y a las suscripciones de Azure.

En la página [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](azure-subscription-service-limits/), se describen estos límites con detalle, pero en lo que respecta a la creación de un clúster de Elasticsearch, los elementos de la lista siguiente son los más pertinentes.

- Cada cuenta de almacenamiento está limitada a 20 000 IOPS; cada máquina virtual del clúster debe hacer uso de una cuenta de almacenamiento (preferiblemente Premium) dedicada.

- El número de nodos de datos en una red virtual. Si no utiliza el Administrador de recursos de Azure (ARM), hay un límite de 2048 instancias de máquinas virtuales por red virtual. Si bien esto debería ser suficiente para muchos de los casos, si tiene una configuración muy grande con miles de nodos, esto puede ser una limitación.

- Número de cuentas de almacenamiento por suscripción y región. Puede crear hasta 100 cuentas de almacenamiento por suscripción de Azure en cada región. Las cuentas de almacenamiento se usan para contener discos virtuales y cada una tiene un límite de espacio de 500 TB.

- Número de núcleos por suscripción. El límite predeterminado es 20 núcleos por suscripción, pero se puede aumentar a hasta 10 000 núcleos si se solicita un aumento del límite mediante una incidencia de soporte técnico.

- La cantidad de memoria por tamaño de máquina virtual. Las máquinas virtuales de menor tamaño tienen una cantidad limitada de memoria disponible (las máquinas D1 tienen 3,5 GB y las máquinas D2 tienen 7 GB). Estas máquinas podrían no ser adecuadas en aquellos escenarios que requieran Elasticsearch para almacenar en caché grandes cantidades de datos con el fin de lograr un buen rendimiento (mediante la agregación de datos o el análisis de un gran número de documentos durante la ingesta de datos, por ejemplo).

- El número máximo de discos por tamaño de máquina virtual. Esta restricción puede limitar el tamaño y el rendimiento de un clúster. Menos discos significa que se pueden mantener menos datos y que puede disminuir el rendimiento al tener menos discos disponibles para la división.

- El número de dominios de actualización o dominios de error por conjunto de disponibilidad. Si crea máquinas virtuales con ARM, cada conjunto de disponibilidad se puede asignar hasta a tres dominios de error y a 20 dominios de actualización. Esta limitación puede afectar a la resistencia de un clúster grande que está sujeto a frecuentes actualizaciones sucesivas.

Además, no se recomienda usar máquinas virtuales con más de 64 GB de memoria; como se describe en la sección [Requisitos de memoria][], no debe asignar más de 30 GB de RAM en cada máquina virtual a la JVM y permitir que el sistema operativo utilice la memoria restante para el búfer de E/S:

Con estas restricciones en mente, siempre debe distribuir los discos virtuales para las máquinas virtuales en un clúster entre las cuentas de almacenamiento para reducir las posibilidades de limitación de E/S. En un clúster muy grande, debe diseñar la infraestructura lógica y dividirla en particiones funcionales independientes. Por ejemplo, debe dividir el clúster entre las suscripciones, aunque este proceso puede dar lugar a complicaciones adicionales debido a la necesidad de conectar redes virtuales.

**Escalado horizontal de un clúster de Elasticsearch**

Internamente en Elasticsearch, el límite de escalabilidad horizontal se determina por el número de particiones definidas para cada índice. Inicialmente, se pueden asignar muchas particiones al mismo nodo de un clúster, pero a medida que el volumen de datos crece, se pueden agregar nodos y particiones adicionales que se pueden distribuir por estos nodos. En teoría, solo cuando el número de nodos alcanza el número de particiones, el sistema dejará de escalar horizontalmente.

Al igual que con el escalado vertical, hay algunos problemas que se deben considerar al contemplar la implementación del escalado horizontal, como:

- El número máximo de máquinas virtuales que se pueden conectar en una red virtual de Azure. Esto puede limitar la escalabilidad horizontal en un clúster muy grande. Puede crear un clúster de nodos que abarca más de una red virtual para sortear este límite, pero este enfoque puede provocar un rendimiento reducido debido a la falta de proximidad de cada nodo con sus homólogos.

- El número de discos por tamaño de máquina virtual. Las distintas series y las SKU admiten diferentes números de discos conectados. Además, también puede usar el almacenamiento efímero incluido con la máquina virtual para proporcionar un límite de almacenamiento de datos más rápido, aunque hay implicaciones de resistencia y recuperación que se deben considerar (para más información, consulte el documento de Configuración, pruebas y análisis de resistencia y recuperación de Elasticsearch). La serie D, la serie DS, la serie Dv2 y la serie GS de máquinas virtuales usan unidades SSD para el almacenamiento efímero.

Podría usar conjuntos de escalado de Azure para iniciar y detener las máquinas virtuales según la demanda (consulte [Escalado automático de máquinas en un conjunto de escalado de máquinas virtuales][] para más detalles). Sin embargo, este enfoque podría no ser adecuado en un clúster de Elasticsearch por las razones siguientes:

- Este enfoque es más adecuado para máquinas virtuales sin estado. Cada vez que agrega o quita un nodo de un clúster de Elasticsearch, se reasignan las particiones para equilibrar la carga y este proceso puede generar grandes volúmenes de tráfico de red y de E/S de disco, y puede afectar gravemente a las velocidades de ingesta de datos. Debe evaluar si esta sobrecarga compensa el beneficio del procesamiento adicional y de los recursos de memoria que están disponibles al iniciar dinámicamente más máquinas virtuales.

- El inicio de la máquina virtual no es inmediato y puede tardar varios minutos antes de que las máquinas virtuales adicionales estén disponibles o se apaguen. El escalado de esta manera solo debe usarse para controlar cambios continuados a petición.

- Después de escalado horizontal, ¿realmente necesita volver a escalar? Quitar una máquina virtual de un clúster de Elasticsearch puede ser un proceso que consume muchos recursos que requieren que Elasticsearch recupere las particiones y las réplicas que se encuentran en dicha máquina virtual y vuelve a crearlas en uno o varios de los nodos restantes. Quitar varias máquinas virtuales al mismo tiempo podría poner en peligro la integridad del clúster, dificultando la recuperación. Además, muchas implementaciones de Elasticsearch aumentan con el tiempo, pero la naturaleza de los datos es tal que no suele reducir el volumen. Es posible eliminar manualmente los documentos y estos también se pueden configurar con un TTL (período de vida) después de que expiren y se quiten, pero en muchos casos, es probable que el espacio asignado anteriormente lo reutilizarán rápidamente los documentos nuevos o modificados. Podría producirse fragmentación dentro de un índice cuando se quiten o se modifiquen documentos, en cuyo caso puede usar la API [Optimize][] de HTTP para Elasticsearch (Elasticsearch 2.0.0 y versiones anteriores) o la API [Force Merge][] (Elasticsearch 2.1.0 y versiones posteriores) para realizar la desfragmentación.

### Determinación del número de particiones de un índice

El número de nodos de un clúster puede variar con el tiempo, pero el número de particiones de un índice es fijo una vez que se crea el índice. Para agregar o quitar particiones, es necesario volver a indexar los datos: el proceso de crear un nuevo índice con el número necesario de particiones y luego copiar los datos del índice antiguo al nuevo (puede usar alias para aislar a los usuarios del hecho de que los datos se han indexado de nuevo). Para más detalles, consulte el documento [Ajuste de la agregación de datos y del rendimiento de qQuery con Elasticsearch en Azure][]. Por lo tanto, es importante determinar el número de particiones que es probable que requieran de forma anticipada la creación del primer índice del clúster. Para establecer este número, puede realizar los siguientes pasos:

- Cree un clúster de un solo nodo con la misma configuración de hardware que piensa usar para la implementación en producción.

- Cree un índice que coincida con la estructura que tiene pensado utilizar en producción. Asigne a este índice una sola partición y ninguna réplica.

- Agregue una cantidad específica de datos de producción realistas al índice.

- Realice consultas, agregaciones y otras cargas de trabajo típicas contra el índice y mida el rendimiento y el tiempo de respuesta.

- Si el rendimiento y el tiempo de respuesta se encuentran dentro de los límites aceptables, repita el proceso desde el paso 3 (agregar más datos).

- Cuando parezca que se ha alcanzado la capacidad de la partición (los tiempos de respuesta y el rendimiento comienzan a volverse inaceptables), tome nota del volumen de documentos.

- Extrapole la capacidad de una sola partición al número anticipado de documentos en producción para calcular el número necesario de particiones (debe incluir algún margen de error en estos cálculos, dado que la extrapolación no es una ciencia exacta).

> [AZURE.NOTE] Recuerde que cada partición se implementa como un índice de Lucene que consume memoria, energía de CPU e identificadores de archivo. Cuantas más particiones tenga, más recursos necesitará.

Además, crear más particiones puede aumentar la escalabilidad (según el escenario y las cargas de trabajo) y puede incrementar el rendimiento de la ingesta de datos; sin embargo, podría reducir la eficacia de muchas consultas. De forma predeterminada, la consulta interrogará a cada partición utilizada por un índice (puede usar el [enrutamiento personalizado][] para modificar este comportamiento si conoce en qué particiones se encuentran los datos que necesita).

Con este proceso solo se puede generar una estimación del número de particiones y puede que no se conozca el volumen de documentos esperados en producción. En este caso, debe determinar el volumen inicial (como anteriormente) y la tasa de crecimiento previsto. Cree un número adecuado de particiones que puedan controlar el crecimiento de los datos del período hasta que esté dispuesto a volver a indexar la base de datos.

Otras estrategias usadas en escenarios como la administración y el registro de eventos incluyen el uso de índices graduales; crear un nuevo índice para los datos introducidos cada día y acceder a este índice mediante un alias que se cambia diariamente para que apunte al índice más reciente. Este enfoque permite eliminar de forma más fácil los datos antiguos (puede eliminar los índices que contienen información que ya no sea necesaria) y mantiene el volumen de datos en un tamaño manejable.

Tenga en cuenta que el número de nodos no tiene que coincidir con el número de particiones. Por ejemplo, si crea 50 particiones, puede distribuirlas inicialmente entre 10 nodos y luego agregar más nodos para escalar el sistema horizontalmente a medida que el volumen de trabajo aumente. Evite crear un número de particiones extremadamente grande en un número pequeño de nodos (1000 particiones repartidas entre 2 nodos, por ejemplo). Aunque el sistema podría escalar en teoría a 1000 nodos con esta configuración, con la ejecución de 500 particiones en un solo nodo existe el riesgo de recortar el rendimiento en el nodo.

> [AZURE.NOTE] En sistemas con una gran cantidad de ingesta de datos, considere el uso de un número primo de particiones. El algoritmo predeterminado que utiliza Elasticsearch para enrutar los documentos a las particiones produce una extensión incluso más grande en este caso.

### Seguridad

De forma predeterminada, Elasticsearch implementa una seguridad mínima y no proporciona ningún medio de autenticación ni autorización. Estos aspectos requieren configurar el sistema operativo y la red subyacentes y usar complementos y utilidades de terceros. Algunos ejemplos son [Shield][] y [Search Guard][].

> [AZURE.NOTE] Shield es un complemento proporcionado por Elastic para la autenticación de usuario, el cifrado de datos, el control de acceso basado en rol, el filtrado IP y la auditoría. Puede que sea necesario configurar el sistema operativo subyacente para implementar medidas de seguridad adicionales, como el cifrado de disco.

En un sistema de producción, debe considerar lo siguiente:

- Cómo impedir el acceso no autorizado al clúster.
- Cómo identificar y autenticar a los usuarios.
- Cómo autorizar las operaciones que los usuarios autenticados pueden realizar.
- Cómo proteger el clúster de operaciones dañinas o malintencionadas.
- Cómo proteger los datos frente al acceso no autorizado.
- Cómo cumplir los requisitos reglamentarios en materia de seguridad de los datos comerciales (si procede).

### Protección del acceso al clúster

Elasticsearch es un servicio de red. Los nodos de un clúster Elasticsearch escuchan las solicitudes de cliente entrantes mediante HTTP y se comunican entre sí mediante un canal TCP. Para evitar que clientes o servicios no autorizados puedan enviar solicitudes a través de rutas de acceso HTTP y TCP, debe realizar los pasos necesarios. Considere los siguientes aspectos:

Defina grupos de seguridad de red para limitar el tráfico de red entrante y saliente de una red virtual o una máquina virtual solo a puertos específicos.

Cambie los puertos predeterminados usados para el acceso web de cliente (9200) y el acceso de red mediante programación (9300). Utilice un firewall para proteger cada nodo del tráfico malintencionado de Internet.

Según la ubicación y la conectividad de los clientes, coloque el clúster en una subred privada sin acceso directo a Internet. Si el clúster se debe exponer fuera de la subred, enrute todas las solicitudes a través de un servidor bastión o un proxy lo suficientemente reforzados para proteger el clúster.

Si debe ofrecer acceso directo a los nodos, utilice el complemento Jetty de Elasticsearch para proporcionar conectividad, autenticación y registro de conexiones SSL. Como alternativa, configure un servidor proxy nginx y configure la autenticación HTTPS.

> [AZURE.NOTE] Con un servidor proxy como nginx, también puede restringir el acceso a la funcionalidad. Por ejemplo, puede configurar nginx para permitir solo las solicitudes al punto de conexión \_search si debe impedir que los clientes realicen otras operaciones.

Si necesita una seguridad de acceso a la red más completa, utilice los complementos Shield o Search Guard.

### Identificación y autenticación de usuarios

Se deben autenticar todas las solicitudes realizadas por los clientes al clúster. Además, debe impedir que los nodos no autorizados se unan al clúster ya que pueden proporcionar una puerta trasera de entrada al sistema que burle la autenticación.

Existen complementos de Elasticsearch que pueden realizar diferentes tipos de autenticación, como:

- **Autenticación básica HTTP**. Cada uno de ellos incluye nombres de usuario y contraseñas. Todas las solicitudes deben estar cifradas mediante SSL/TLS o un nivel de protección equivalente.

- **Integración de LDAP y Active Directory**. Este enfoque requiere que se asigne a los clientes roles en grupos de LDAP o AD.

- **Autenticación nativa** mediante identidades definidas dentro del clúster de Elasticsearch en sí.

- **Autenticación TLS** dentro de un clúster para autenticar todos los nodos.

- **Filtrado de IP**, para impedir que los clientes de subredes no autorizadas se conecten y que los nodos de estas subredes se unan al clúster.

### Autorización de solicitudes de cliente

La autorización depende del complemento de Elasticsearch utilizado para proporcionar este servicio. Por ejemplo, un complemento que ofrece autenticación básica normalmente proporciona características que definen el nivel de autenticación, mientras que un complemento que usa LDAP o AD suele asociar clientes a roles y luego asignar derechos de acceso a esos roles. Cuando use un complemento determinado, debe considerar los siguientes puntos:

- ¿Necesita restringir las operaciones que puede realizar un cliente? Por ejemplo, ¿debería poder un cliente supervisar el estado del clúster o crear y eliminar índices?

- ¿Se debe restringir al cliente a índices específicos? Esto es útil en una situación multinquilino donde se puede asignar a los inquilinos su propio conjunto específico de índices y estos índices deben ser inaccesibles para otros inquilinos.

- ¿Debe el cliente poder leer y escribir datos en un índice? Un cliente puede realizar búsquedas que recuperan datos mediante un índice pero se le debe impedir agregar o eliminar datos de ese índice, por ejemplo.

Actualmente, la mayoría de los complementos de seguridad abarcan operaciones en el nivel de clúster o de índice, y no de subconjuntos de documentos dentro de los índices. Esto es así por motivos de eficiencia. Por lo tanto, no es fácil limitar las solicitudes a documentos específicos dentro de un índice único. Si necesita este nivel de granularidad, guarde los documentos en índices independientes y utilice alias que agrupen los índices.

Por ejemplo, en un sistema de personal, si el usuario A necesita acceso a todos los documentos que contienen información sobre los empleados del departamento X, el usuario B a todos los documentos que contienen información sobre los empleados del departamento Y y el usuario C a todos los documentos que contienen información sobre los empleados de ambos departamentos, cree dos índices (para el departamento X y el departamento Y) y un alias que haga referencia a ambos. Conceda al usuario A acceso de lectura al primer índice, al usuario B acceso de lectura al segundo índice y al usuario C acceso de lectura a ambos índices mediante el alias. Para más información, consulte [Faking Index per User with Aliases][] (Falsificación de un indice por usuario con alias).

### Protección del clúster

El clúster puede volverse vulnerable al uso indebido si no está protegido cuidadosamente:

**Deshabilite el scripting de consulta dinámica en las consultas de Elasticsearch**, ya que puede ocasionar vulnerabilidades de seguridad. Utilice scripts nativos en lugar de scripting de consulta; un script nativo es un complemento de Elasticsearch escrito en Java y compilado en un archivo JAR.

El scripting de consulta dinámica está ahora deshabilitado de forma predeterminada; no lo habilite de nuevo hasta que tenga una muy buena razón para ello.

**Evite exponer las búsquedas de cadenas de consulta a los usuarios**. La búsqueda de cadenas de consulta permite a los usuarios realizar consultas con un uso intensivo de recursos sin problemas. Estas búsquedas podrían afectar gravemente al rendimiento del clúster y pueden dejar el sistema abierto a ataques de denegación de servicio. Además, la búsqueda de cadenas de consulta puede exponer información posiblemente privada.

**Evite operaciones que consuman mucha memoria**, ya que pueden provocar excepciones de memoria insuficiente que den lugar a errores de Elasticsearch en un nodo. Las operaciones de uso intensivo de recursos de ejecución prolongada se pueden usar también para implementar ataques de denegación de servicio. Algunos ejemplos son:

Evite las solicitudes de búsqueda que intentan cargar campos muy grandes en la memoria (si una consulta aplica ordenaciones, scripts o facetas en estos campos), como:

- Búsquedas que consultan varios índices al mismo tiempo.

- Búsquedas que recuperan un gran número de campos. Estas búsquedas pueden agotar la memoria al provocar que una gran cantidad de datos de campo se almacenen en caché. De forma predeterminada, la memoria caché de datos de campo tiene un tamaño ilimitado, pero puede establecer las propiedades [indices.fielddata.cache.*] (https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html) del archivo de configuración elasticsearch.yml para limitar los recursos disponibles. También puede configurar el [interruptor de datos de campo][] para ayudar a impedir que los datos almacenados en caché de un solo campo agoten la memoria y el [interruptor de solicitudes][] para evitar que las consultas monopolicen la memoria. El costo de configurar estos parámetros es que aumenta la probabilidad de que algunas consultas den error o se agote el tiempo de espera.
 
> [AZURE.NOTE] Mediante [valores de documento][], puede reducir los requisitos de memoria de los índices y guardar campos de datos en un disco en lugar de cargarlos en memoria. Esto puede ayudar a reducir las posibilidades de memoria agotada en un nodo, pero a cambio de una reducción en la velocidad.

> [AZURE.NOTE] Elasticsearch siempre asume que tiene memoria suficiente para realizar su carga de trabajo actual. Si no es así, el servicio de Elasticsearch puede bloquearse. Elasticsearch proporciona puntos de conexión que devuelven información sobre el uso de recursos (las [API cat][] HTTP), y debe supervisar esta información con mucho cuidado.

**Se espera demasiado tiempo a que se vacíe un segmento de memoria en curso**. Esto puede agotar el espacio de búfer en memoria. Si es necesario, [configure el registro de transacciones][] para reducir los umbrales con los que los datos se vacían en el disco.

**Creación de índices con grandes cantidades de metadatos**. Un índice que contenga documentos con grandes variaciones en los nombres de campo puede consumir mucha memoria. Para más información, consulte [Mapping Explosion][] (Explosión de asignación).
  
La definición de una operación de larga ejecución o de uso intensivo de consultas es enormemente específica del escenario. La carga de trabajo esperada normalmente por un clúster podría tener un perfil completamente diferente de la carga de trabajo en otro. Determinar qué operaciones son inaceptables requiere investigar y probar exhaustivamente las aplicaciones.

Sea proactivo, detecte y detenga las actividades malintencionadas antes de que provoquen daños importantes o pérdida de datos. Considere el uso de un sistema de supervisión y notificación de seguridad que pueda detectar rápidamente patrones inusuales de acceso a los datos y generar alertas cuando, por ejemplo, las solicitudes de inicio de sesión de usuario presenten errores, nodos inesperados se unan al clúster o lo abandonen o las operaciones estén tardando más de lo esperado. Entre las herramientas que pueden realizar estas tareas, se incluye [Watcher][] de Elasticsearch.

### Protección de los datos

Puede proteger los datos en proceso mediante SSL/TLS, pero Elasticsearch no ofrece ninguna forma integrada de cifrado de datos para la información que se almacena en el disco. Recuerde que esta información se mantiene en archivos de datos ordinarios y que cualquier usuario con acceso a estos archivos puede poner en peligro los datos que contienen, al copiarlos, por ejemplo, en su propio clúster. Considere los siguientes puntos:

- Proteja los archivos que utiliza Elasticsearch para contener los datos. No permita acceso arbitrario de lectura y escritura a identidades distintas del servicio Elasticsearch.

- Cifre los datos contenidos en estos archivos mediante un sistema de archivos de cifrado.

> [AZURE.NOTE] Azure ahora admite el cifrado de disco para máquinas virtuales Windows y Linux. Para más información, consulte [Versión preliminar del Cifrado de discos de Azure para máquinas virtuales IaaS de Windows y Linux][].

### Cumplimiento de los requisitos reglamentarios

Los requisitos reglamentarios tienen que ver principalmente con auditar las operaciones para mantener un historial de eventos y garantizar la privacidad de estas operaciones con el fin de tratar de impedir que las supervise (y reproduzca) un organismo externo. En concreto, debe considerar los siguientes puntos:

- Cómo realizar un seguimiento de todas las solicitudes (correctas o no) y todos los intentos de acceso al sistema.

- Cómo cifrar las comunicaciones realizadas por los clientes al clúster, así como las comunicaciones de nodo a nodo realizadas por el clúster. Debe implementar SSL/TLS en todas las comunicaciones del clúster. Elasticsearch también admite cifrados conectables si su organización tiene requisitos distintos de los disponibles mediante SSL/TLS.

- Almacene todos los datos de auditoría de forma segura. El volumen de información de auditoría puede crecer muy rápidamente y se debe proteger de forma segura para evitar que se manipule dicha información.

- Archive los datos de auditoría de forma segura.

### Supervisión

La supervisión es importante tanto en el nivel de sistema operativo como en el nivel de Elasticsearch.

Puede realizar la supervisión en el nivel de sistema operativo mediante herramientas específicas del sistema operativo. En Windows, esto incluye elementos como el Monitor de rendimiento con los contadores de rendimiento adecuados, mientras que en Linux se pueden usar herramientas como *vmstat*, *iostat* y *top*. Los elementos principales para supervisar en el nivel de sistema operativo incluyen el uso de CPU, los volúmenes de E/S de disco, los tiempos de espera de E/S de disco y el tráfico de red. En un clúster de Elasticsearch bien ajustado, el uso de la CPU por el proceso de Elasticsearch debe ser alto y los tiempos de espera de E/S de disco deben ser mínimos.

En el nivel de software, debe supervisar el rendimiento y los tiempos de respuesta de las solicitudes, junto con los detalles de las solicitudes con error. Elasticsearch proporciona varias API que puede usar para examinar el rendimiento de distintos aspectos de un clúster. Las dos API más importantes son *\_cluster/health* y *\_nodes/stats*. La API *\_cluster/health* puede utilizarse para proporcionar una instantánea del estado general del clúster, así como para proporcionar información detallada para cada índice, como se muestra en el ejemplo siguiente:

`GET _cluster/health?level=indices`

El resultado de ejemplo que se muestra a continuación se generó mediante esta API:

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

Este clúster contiene dos índices denominados *systwo* y *sysfour*. Las estadísticas clave que se deben supervisar en cada índice son el estado, las particiones\_activas y las particiones\_sin\_ asignar. El estado debe ser verde, el número de active\_shards debe reflejar el valor de number\_of\_shards y number\_of\_replicas, y el valor de unassigned\_shards deben ser cero.

Si el estado es "rojo", parte del índice falta o está dañado. Para comprobar esto, vea si la configuración *active\_shards* es inferior a *number\_of\_shards* - (*number\_of\_replicas* + 1) y unassigned\_shards es un valor distinto de cero. Tenga en cuenta que el estado amarillo indica que un índice está en un estado transitorio, bien como resultado de agregar más réplicas o de reubicar particiones. El estado debería cambiar a verde cuando se haya completado la transición.

Si permanece en amarillo por un período de tiempo prolongado o cambia a rojo, compruebe si se ha producido algún evento de E/S significativo (como un error de disco o de red) en el nivel del sistema operativo.

La API \_nodes/stats emite información detallada sobre cada nodo del clúster:

`GET _nodes/stats`

Los resultados generados incluyen detalles sobre cómo se almacenan los índices en cada nodo (como los tamaños y los números de documento), el tiempo dedicado a realizar indexación, consulta, búsqueda y combinación, el almacenamiento en caché, la información de sistema operativo y proceso, estadísticas sobre la JVM (como el rendimiento de la recopilación de elementos no utilizados) y los grupos de subprocesos. Para más información, consulte [Monitoring Individual Nodes][] (Supervisión de nodos individuales).

Si una parte importante de las solicitudes de Elasticsearch dan error con los mensajes *EsRejectedExecutionException*, significa que Elasticsearch no puede mantener el ritmo del trabajo que recibe. En esta situación, debe identificar el cuello de botella que hace que Elasticsearch se quede atrás. Considere los siguientes aspectos:

- Si el cuello de botella se debe a una restricción de recursos, como memoria insuficiente asignada a la JVM que provoca un número excesivo de recolecciones de elementos no utilizados, considere entonces la posibilidad de asignar más recursos (en este caso, configure la JVM para que use más memoria, hasta el 50 % del almacenamiento disponible en el nodo; consulte [Requisitos de memoria][]).

- Si el clúster muestra tiempos de espera de E/S largos y las estadísticas de combinación recopiladas para un índice mediante la API \_node/stats contienen valores grandes, entonces el índice experimenta numerosas operaciones de escritura. Revise los puntos presentados en la sección [Optimización de recursos para las operaciones de indexación](guidance-elasticsearch-tuning-data-ingestion-performance.md#optimizing-resources-for-indexing-operations) para optimizar el rendimiento de la indexación.

- Limite las aplicaciones cliente que realizan operaciones de ingesta de datos y determine el efecto que esto tiene sobre el rendimiento. Si este enfoque muestra mejoras significativas, considere mantener la limitación o escalar horizontalmente distribuyendo la carga de los índices con numerosas operaciones de escritura entre más nodos. Para más información, consulte el documento [Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure][].

- Si las estadísticas de búsqueda de un índice indican que las consultas están tardando mucho tiempo, considere cómo se optimizan las consultas. Para más información, consulte la sección sobre la [optimización de consultas][]. Tenga en cuenta que puede utilizar los valores *query\_time\_in\_millis* y *query\_total* notificados en las estadísticas de búsqueda para calcular una guía aproximada de la eficiencia en las consultas; la ecuación *query\_time\_in\_millis*/*query\_total* le proporcionará un tiempo medio para cada consulta.

### Herramientas para la supervisión de Elasticsearch

Existen numerosas herramientas disponibles para realizar la supervisión diaria de Elasticsearch en el entorno de producción. Estas herramientas usan normalmente las API de Elasticsearch subyacentes para recopilar información y presentar los detalles de una manera que sea más fácil de observar que los datos sin procesar. Algunos ejemplos comunes son [Elasticsearch-Head][], [Bigdesk][], [Kopf][] y [Marvel][].

Elasticsearch-Head, Bigdesk y Kopf se ejecutan como complementos del software Elasticsearch. Las versiones más recientes de Marvel se pueden ejecutar de forma independiente, pero pueden necesitar [Kibana][] para proporcionar un entorno de captura y hospedaje de datos. La ventaja de usar Marvel con Kibana es que puede implementar la supervisión en un entorno diferente del clúster de Elasticsearch, lo que le permite explorar los problemas con Elasticsearch que de otra forma no sería posible si las herramientas de supervisión se ejecutaran como parte del software Elasticsearch. Por ejemplo, si Elasticsearch presenta errores de forma repetida o se ejecuta muy despacio, también se utilizarán herramientas que se ejecutan como complementos de Elasticsearch, lo que hará que la supervisión y el diagnóstico sean más difíciles.

En el nivel del sistema operativo, puede usar herramientas como la característica Análisis de registros de [Azure Operations Management Suite][] o [Diagnósticos de Azure con el Portal de Azure][] para capturar datos de rendimiento para las máquinas virtuales que hospedan nodos de Elasticsearch. Otro enfoque es usar [Logstash][] para capturar datos de rendimiento y registro, almacenar esta información en un clúster de Elasticsearch independiente (no utilice el mismo que para su aplicación) y luego usar Kibana para visualizar los datos. Para más información, consulte [Microsoft Azure Diagnostics with ELK][] (Diagnósticos de Microsoft Azure con ELK).

### Herramientas para pruebas de rendimiento de Elasticsearch

Si está comparando el rendimiento de Elasticsearch o sometiendo un clúster a pruebas de rendimiento, hay otras herramientas disponibles. Estas herramientas están pensadas para utilizarse en un entorno de desarrollo o prueba, y no en producción. Un ejemplo que se usa con frecuencia es [Apache JMeter][].

JMeter se utiliza para realizar comparaciones de rendimiento y otras pruebas de carga que se describen en los documentos relacionados con esta guía. En el documento [Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure][], se describe con detalle cómo se configuró y usó JMeter.

## Pasos siguientes

- [Elasticsearch: The Definitive Guide (Elasticsearch: la guía definitiva).](https://www.elastic.co/guide/en/elasticsearch/guide/master/index.html)

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementing a JMeter Test Plan for Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajuste de la agregación de datos y del rendimiento de qQuery con Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuración de resistencia y recuperación en Elasticsearch en Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Running the Automated Elasticsearch Resiliency Tests]: guidance-elasticsearch-configuring-resilience-and-recovery

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[Escalado automático de máquinas en un conjunto de escalado de máquinas virtuales]: virtual-machines-vmss-walkthrough/
[Versión preliminar del Cifrado de discos de Azure para máquinas virtuales IaaS de Windows y Linux]: azure-security-disk-encryption/
[Equilibrador de carga de Azure]: load-balancer-overview/
[ExpressRoute]: expressroute-introduction/
[equilibrador de carga interno]: load-balancer-internal-overview/
[Tamaños de máquinas virtuales]: virtual-machines-size-specs/

[Requisitos de memoria]: #memory-requirements
[Requisitos de red]: #network-requirements
[Detección de nodos]: #node-discovery
[optimización de consultas]: #query-tuning

[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[complemento de nube de Azure]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Diagnósticos de Azure con el Portal de Azure]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Azure Operations Management Suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[plantillas de inicio rápido de Azure]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[API cat]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[configure el registro de transacciones]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[enrutamiento personalizado]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[valores de documento]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Elasticsearch-Head]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net y NEST]: http://nest.azurewebsites.net/
[módulo de restauración y de instantáneas de Elasticsearch]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[Faking Index per User with Aliases]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[interruptor de datos de campo]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#fielddata-circuit-breaker
[Force Merge]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[protocolo de propagación]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[Mapping Explosion]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[Microsoft Azure Diagnostics with ELK]: https://github.com/mspnp/semantic-logging/tree/elk
[Monitoring Individual Nodes]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[API de cliente de nodo]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/node-client.html
[Optimize]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[complemento de cambios de PubNub]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[interruptor de solicitudes]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#request-circuit-breaker
[Search Guard]: https://github.com/floragunncom/search-guard
[Shield]: https://www.elastic.co/products/shield
[API de cliente de transporte]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[nodos tribales]: https://www.elastic.co/blog/tribe-node
[Watcher]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html

<!---HONumber=AcomDC_0224_2016-->