<properties
   pageTitle="Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure | Microsoft Azure"
   description="Maximización del rendimiento de la ingesta de datos con Elasticsearch en Azure."
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

# Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

## Información general

Un aspecto importante al crear cualquier base de datos de búsqueda es determinar la mejor manera de estructurar el sistema para introducir los datos de búsqueda rápida y eficaz. Las consideraciones acerca de este requisito se refieren no solo la elección de la infraestructura en el que implementa el sistema, sino también a las diversas optimizaciones que puede usar para ayudar a garantizar que el sistema puede hacer frente a los niveles de entrada de datos previstos.

Este documento describe las opciones de implementación y configuración que debe tener en cuenta para implementar un clúster Elasticsearch que espera una alta tasa de ingesta de datos. Para proporcionar datos sólidos con fines ilustrativos, este documento también muestra los resultados de las pruebas comparativas realizadas con varias configuraciones con una carga de trabajo de ingesta de grandes volúmenes de datos simple. Se describen los detalles de la carga de trabajo en el [Apéndice](#appendix-the-bulk-load-data-ingestion-performance-test) al final de este documento.

El propósito de las pruebas comparativas no era generar cifras de rendimiento absoluto para la ejecución de Elasticsearch ni recomendar una topología determinada, sino ilustrar los métodos que puede usar para evaluar el rendimiento, ajustar el tamaño de los nodos de datos e implementar clústeres que pueden satisfacer sus propios requisitos de rendimiento.

Al cambiar el tamaño de sus propios sistemas, es importante probar el rendimiento exhaustivamente según sus propias cargas de trabajo. Recopile la telemetría que le permite obtener información acerca de la configuración de hardware óptima que se usará y los factores de escalado horizontal que se deben tener en cuenta. En concreto, debe:

- Tener en cuenta el tamaño total de la carga enviada y no solo el número de elementos de cada solicitud de inserción masiva. Un número menor de elementos masivos grandes en cada solicitud podría mejor que un número mayor, según los recursos disponibles para procesar cada solicitud.

Puede supervisar los efectos de modificar la solicitud de inserción masiva con Maravilla, con los contadores *readbytes*/*writebytes* E/S con JMeter, y con herramientas del sistema operativo como *iostat* y *vmstat* en Ubuntu.

- Realizar pruebas de rendimiento y recopilar datos de telemetría para medir el procesamiento de CPU y los tiempos de espera de E/S, la latencia de disco, el rendimiento y los tiempos de respuesta. Esta información puede ayudar a identificar posibles cuellos de botella y evaluar los costos y beneficios de usar el Almacenamiento premium. Tener en cuenta que el uso de CPU y disco puede no ser ni siquiera en todos los nodos, según la forma en que las particiones y las réplicas estén distribuidas en el clúster (algunos nodos pueden contener más particiones que otros).

- Tener en cuenta cómo el número de solicitudes simultáneas para la carga de trabajo se distribuirá en el clúster y evalúe el impacto de usar distintos números de nodos para controlar esta carga de trabajo.

- Tener en cuenta cómo podrían aumentar las cargas de trabajo a medida que crece el negocio. Evaluar el impacto de este crecimiento en los costos de las máquinas virtuales y el almacenamiento usado por los nodos.

- Saber que el uso de un clúster con un número de nodos mayor con discos normales puede resultar más económico si su escenario requiere un gran número de solicitudes y la infraestructura de disco mantiene un rendimiento que satisface sus contratos de nivel de servicio. Sin embargo, el aumento del número de nodos puede introducir una sobrecarga en forma de comunicaciones y sincronizaciones entre nodos adicionales.

- Comprender que un número de núcleos por nodo mayor puede generar más tráfico en el disco, ya que se pueden procesar más documentos. En este caso, mida el uso del disco para evaluar si el subsistema de E/S puede convertirse en un cuello de botella y determinar las ventajas de usar el Almacenamiento premium.

- Probar y analizar las ventajas y desventajas con un número de nodos mayor con menos núcleos frente a menos nodos con más núcleos. Tener en cuenta que el aumento del número de réplicas eleva la demanda en el clúster y puede requerir agregar nodos.

- Tener en cuenta que el uso de discos efímeros puede requerir que los índices se tienen que recuperar con más frecuencia.

- Medir el uso del volumen de almacenamiento de información para evaluar la capacidad y la infrautilización del almacenamiento. Por ejemplo, en nuestro escenario almacenamos 1500 millones de documentos con 350 GB de almacenamiento.

- Medir las velocidades de transferencia para las cargas de trabajo y tener en cuenta qué probabilidades tiene de obtener el límite total de transferencia de la tasa de E/S para cualquier cuenta de almacenamiento dada en la que ha creado discos virtuales.

## Diseño de nodos e índices

En un sistema que debe admitir la ingesta de datos a gran escala, pregúntese lo siguiente:

- **¿Son los datos relativamente estáticos o se mueven rápidamente?** Cuanto más dinámicos sean los datos, mayor será la sobrecarga de mantenimiento para Elasticsearch. Si los datos se replican, cada réplica se mantiene de forma sincrónica. Los datos que se mueven rápidamente y que solo tienen una duración limitada o que se pueden reconstruir fácilmente podrían beneficiarse de deshabilitar completamente la replicación. Esta opción se trata con más detalle en la sección sobre [cómo ajustar la ingesta de datos a gran escala](#tuning-large-scale-data-ingestion).

- **¿Qué grado de actualización necesita que tengan los datos detectados mediante la búsqueda?** Para mantener el rendimiento, Elasticsearch almacena en el búfer tantos datos de la memoria como sea posible. Esto significa que no todos los cambios están inmediatamente disponibles para las solicitudes de búsqueda. El proceso mediante el cual Elasticsearch conserva los cambios y hace que estén visibles se describe en línea en el documento [Making Changes Persistent](https://www.elastic.co/guide/en/elasticsearch/guide/current/translog.html#translog) (Convertir cambios en persistentes).

    La velocidad a la que los datos pasan a estar visibles depende de la opción de configuración *refresh\_interval* del índice correspondiente. De forma predeterminada, este intervalo se establece en 1 segundo. Sin embargo, no todas las situaciones requieren actualizaciones para que esto se produzca rápidamente. Por ejemplo, los índices que registran datos de registro pueden necesitar hacer frente a una entrada rápida y continua de información que es necesario introducir rápidamente, pero no requiere que la información esté disponible inmediatamente para realizar consultas. En este caso, considere la posibilidad de reducir la frecuencia de las actualizaciones. Esta característica también se describe en la sección sobre el [ajuste de la ingesta de datos a gran escala.](#tuning-large-scale-data-ingestion)

- **¿Con qué rapidez es probable que aumenten los datos?** La capacidad de índice se determina mediante el número de particiones que se especifica cuando se crea el índice. Para permitir el crecimiento, especifique un número adecuado de particiones (el valor predeterminado es cinco). Si el índice se crea inicialmente en un solo nodo, las cinco particiones estarán en ese nodo, pero a medida que el volumen de datos crezca, se podrán agregar nodos adicionales y Elasticsearch distribuirá dinámicamente las particiones entre los nodos. Sin embargo, cada partición tiene una sobrecarga; todas las búsquedas en un índice consultarán todas las particiones, por lo que crear un gran número de particiones para una pequeña cantidad de datos puede ralentizar las recuperaciones de datos (evite el escenario [Kagillion particiones](https://www.elastic.co/guide/en/elasticsearch/guide/current/kagillion-shards.html)).

    Algunas cargas de trabajo (como el registro) pueden crear un nuevo índice cada día y, si observa que el número de particiones es insuficiente para el volumen de datos, debe cambiarlo antes de crear el siguiente índice (los índices existentes no se verán afectados). Si debe distribuir los datos existentes en más particiones, una posibilidad es volver a indexar la información; crear un nuevo índice con la configuración adecuada y copiar los datos en él. Este proceso puede hacerse de forma transparente para las aplicaciones mediante [alias de índices](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html).

- **¿Es necesario dividir los datos entre los usuarios en un escenario multiinquilino?** Puede crear índices independientes para cada usuario, pero esto puede resultar caro si cada usuario solo tiene una cantidad de datos moderada. En su lugar, considere la posibilidad de crear [índices compartidos](https://www.elastic.co/guide/en/elasticsearch/guide/current/shared-index.html) y usar [alias basados en filtros](https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html) para dirigir las solicitudes a los datos por usuario. Para mantener los datos de un usuario juntos en la misma partición, reemplace la configuración de enrutamiento predeterminada para el índice y enrute los datos de acuerdo con algún atributo de identificación del usuario.

- **Los datos tienen una duración larga o corta?** Si utiliza un conjunto de máquinas virtuales de Azure para implementar un clúster de Elasticsearch, puede almacenar los datos efímeros en un disco del sistema de recursos local en lugar de en una unidad conectada. El uso una SKU de la máquina virtual que utiliza una SSD para el disco de recursos puede mejorar el rendimiento de E/S. Sin embargo, cualquier información contenida en el disco de recursos es temporal y se puede perder si se reinicia la máquina virtual (consulte la sección sobre cuándo se perderán los datos de una unidad temporal del documento [Understanding the temporary drive on Microsoft Azure Virtual Machines](http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx) (Descripción de la unidad temporal en máquinas virtuales de Microsoft Azure) para más información). Si necesita conservar datos entre reinicios, cree discos de datos que contengan esta información y conéctelos a la máquina virtual.

- **Qué grado de actividad tienen los datos?** Los discos duros virtuales de Azure están sujetos a una limitación si la actividad de lectura y escritura supera los parámetros especificados (actualmente 500 IOPS para un disco conectado a una máquina virtual de nivel estándar y 5000 IOPS para un disco de Almacenamiento premium).

    Para reducir las posibilidades de limitación y aumentar el rendimiento de E/S, podría crear varios discos de datos para cada máquina virtual y configurar Elasticsearch para repartir los datos en estos discos, como se describe en [Requisitos de disco y sistema de archivos](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

    Debe seleccionar una configuración de hardware que ayude a minimizar el número de operaciones de lectura de E/S de disco al asegurarse de que hay suficiente memoria disponible para almacenar en caché los datos a los que se accede con frecuencia. Esto se describe en la sección [Memory Requirements](guidance-elasticsearch-running-on-azure.md#memory-requirements) (Requisitos de memoria) del documento sobre la implementación de Elasticsearch en Azure.

- **¿Qué tipo de carga de trabajo necesitará admitir cada nodo?** Elasticsearch se beneficia de tener memoria disponible en la que almacenar datos en caché (en forma de una caché del sistema de archivos) y para el montón de JVM, como se describe en la sección [Memory Requirements](guidance-elasticsearch-running-on-azure.md#memory-requirements) (Requisitos de memoria) del documento sobre la implementación de Elasticsearch en Azure.

    La cantidad de memoria, el número de núcleos de CPU y la cantidad de discos disponibles se definen mediante la SKU de la máquina virtual. Para más información, consulte la página [Precios de Máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/) en el sitio web de Azure.

### Opciones de la máquina virtual

Puede aprovisionar máquinas virtuales de Azure mediante diferentes SKU. Los recursos disponibles para una máquina virtual de Azure dependen de la SKU seleccionada. Cada SKU ofrece una combinación diferente de núcleos, memoria y almacenamiento. Debe seleccionar un tamaño de máquina virtual adecuado, que controle la carga de trabajo esperada pero que también resulte rentable. Comience con una configuración que satisfaga los requisitos actuales (realice pruebas comparativas para comprobarlo, como se describe más adelante en este documento). Puede escalar un clúster más adelante mediante la adición de más máquinas virtuales que se ejecuten en nodos de Elasticsearch.

La página [Tamaños de máquinas virtuales](virtual-machines-size-specs/) del sitio web de Azure documenta las diversas opciones y las SKU disponibles para las máquinas virtuales.

Se debe comparar el tamaño y los recursos de una máquina virtual con el rol que asumirán los nodos que se ejecutan en la máquina virtual.

Para un nodo de datos:

- Asigne hasta 30 GB o el 50 % de la memoria RAM disponible al montón de Java, lo que sea menor. Deje el resto en el sistema operativo que usarlo para almacenar los archivos en la memoria caché. Si usa Linux, para especificar la cantidad de memoria que se asignará al montón de Java establezca la variable de entorno ES\_HEAP\_SIZE antes de ejecutar Elasticsearch. Si utiliza Windows o Linux, puede estipular el tamaño de la memoria con los parámetros *Xmx* y *Xms* al iniciar Elasticsearch.

    Según la carga de trabajo, menos máquinas virtuales grandes pueden no ser tan eficaces para el rendimiento como con un mayor número de máquinas virtuales de tamaños moderado; debe realizar las pruebas que puedan medir las ventajas y desventajas del tráfico de red adicional y el mantenimiento implicado frente a los costos de aumentar el número de núcleos disponibles y la contención del disco reducida en cada nodo.

- Use almacenamiento premium para almacenar datos de Elasticsearch. Esto se describe con más detalle en la sección [Opciones de almacenamiento](#storage-options).

- Use varios discos (del mismo tamaño) y reparta los datos en estos discos. La SKU de las máquinas virtuales determinará el número máximo de discos de datos que puede conectar. Para más información, consulte [Requisitos de disco y sistema de archivos](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

- Utilice una SKU de CPU con varios núcleos; al menos 2 núcleos, preferiblemente 4 o más.

Para un nodo cliente:

- No asigna el almacenamiento en disco para los datos de Elasticsearch; los clientes dedicados no almacenan datos en el disco.

- Asegúrese de que la memoria adecuada está disponible para controlar las cargas de trabajo. Las solicitudes de inserción masiva se leen en memoria antes de enviar los datos a los diversos nodos de datos, y los resultados de las consultas y las agregaciones se acumulan en la memoria antes de devolverse a la aplicación cliente. Realice pruebas comparativas de sus propias cargas de trabajo y supervise el uso de memoria mediante una herramienta como Marvel o la [información de JVM](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_jvm_section) que se devuelve mediante la API *nodo/estadísticas* (`GET _nodes/stats`) para evaluar los requisitos óptimos. En concreto, supervise la métrica *heap\_used\_percent* para cada nodo e intente mantener el tamaño del montón por debajo del 75 % del espacio disponible.

- Asegúrese de que existen suficientes núcleos de CPU para recibir y procesar el volumen de solicitudes esperado. Las solicitudes se ponen en cola cuando se reciben antes del procesamiento, y el volumen de elementos que se pueden poner en cola es una función del número de núcleos de CPU en cada nodo. Puede supervisar las longitudes de cola mediante los datos de la [información del grupo de subprocesos](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_threadpool_section) devuelta por la API de nodo/estadísticas.

    Si el recuento *rechazado* para una cola indica que se rechazan las solicitudes, significa que se está empezando a crear un cuello de botella en el clúster. Esto puede deberse al ancho de banda de la CPU, pero también puede deberse a otros factores, como la falta de memoria o un rendimiento de E/S lento, por lo tanto, use esta información junto con otras estadísticas para ayudar a determinar la causa raíz.

    Los nodos cliente pueden ser necesarios o no, en función de las cargas de trabajo. Las cargas de trabajo de ingesta de datos no suelen beneficiarse del uso de clientes dedicados, mientras que algunas búsquedas y agregaciones se pueden ejecutar más rápidamente; esté preparado para realizar pruebas comparativas de sus propios escenarios.

    Los nodos cliente son útiles principalmente para aplicaciones que utilizan la API Cliente de transporte para conectarse al clúster. También puede utilizar la API Cliente de nodo, que crea dinámicamente un cliente dedicado para la aplicación con los recursos del entorno de host de la aplicación. Si las aplicaciones utilizan la API Cliente de nodo, puede no ser necesario que el clúster contenga nodos cliente dedicados preconfigurados.
    
    Sin embargo, tenga en cuenta que un nodo creado mediante la API de nodo cliente es un miembro de primera clase del clúster y, como tal, participa en el chatter de red con otros nodos; iniciar y detener los nodos cliente con frecuencia puede crear ruido innecesario en todo el clúster.

Para un nodo maestro:

- No asigne el almacenamiento en disco para los datos de Elasticsearch; los nodos maestros dedicados no almacenan datos en el disco.

- Los requisitos de CPU deben ser mínimos.

- Los requisitos de memoria dependen del tamaño del clúster. La información sobre el estado del clúster se mantiene en la memoria. Para clústeres pequeños, la cantidad de memoria necesaria es mínima, pero para un clúster grande y muy activo, donde se crean los índices con frecuencia y las particiones se mueven, la cantidad de información de estado puede aumentar considerablemente. Supervise el tamaño del montón de la Máquina virtual Java para determinar si necesita agregar más memoria.

> [AZURE.NOTE]  Para la confiabilidad del clúster, cree siempre varios nodos maestros y configure los nodos restantes para evitar la posibilidad de que se produzca un síndrome de cerebro dividido. Idealmente, debe ser un número impar de nodos maestros. Este tema se describe con más detalle en el documento [Configuración de resistencia y recuperación en Elasticsearch en Azure][].

### Opciones de almacenamiento

Hay una serie de opciones de almacenamiento en las máquinas virtuales de Azure con diferentes ventajas e inconvenientes que afectan al costo, el rendimiento, la disponibilidad y la recuperación y que debe considerar atentamente.

Tenga en cuenta que debe almacenar datos de Elasticsearch en discos de datos dedicados. Esto le ayudará a reducir la contención con el sistema operativo y asegurarse de que los volúmenes de E/S de Elasticsearch grandes no compiten con las funciones del sistema operativo por los recursos de E/S.

Los discos de Azure están sujetos a restricciones de rendimiento. Si averigua que un clúster se somete a ráfagas de actividad periódicas, es posible que las solicitudes de E/S se limiten. Para evitarlo, ajuste el diseño para equilibrar el tamaño del documento en Elasticsearch de acuerdo con el volumen de solicitudes que es probable que reciba cada disco.

Los discos basados en almacenamiento estándar admiten una tasa máxima de solicitudes de 500 IOPS, mientras que los discos basados en almacenamiento premium pueden funcionar hasta a 5000 IOPS. Los discos de Almacenamiento premium solo están disponibles para las series DS y GS de máquinas virtuales. Las velocidades de IOPS de disco máximas para las [máquinas virtuales de Azure están documentadas en línea](virtual-machines-size-specs/).

**Discos de datos persistentes**

Los discos de datos persistentes son los discos duros virtuales respaldados por Almacenamiento de Azure. Si es necesario volver a crear la máquina virtual después de un error grave, los discos duros virtuales existentes pueden conectase fácilmente a la nueva máquina virtual. Los discos duros virtuales se pueden crear en función del almacenamiento estándar (discos duros) o el Almacenamiento premium (SSD). Si desea usar SSD, debe crear máquinas virtuales con la serie DS o superior. Las máquinas DS cuestan lo mismo que las máquinas virtuales de serie D equivalente, pero el uso del Almacenamiento premium conlleva un cargo adicional.

En casos donde la velocidad de transferencia máxima por disco no es suficiente para la carga de trabajo esperada, considere la posibilidad de crear varios discos de datos y permitir a Elasticsearch [distribuir datos en estos discos](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements), o bien de implementar el nivel del sistema [seccionado de RAID 0 con discos virtuales](virtual-machines-linux-configure-raid/).

> [AZURE.NOTE] La experiencia en Microsoft ha demostrado que el uso de RAID 0 es especialmente beneficioso para suavizar los efectos de E/S de cargas de trabajo *con picos* que generan ráfagas de actividad frecuentes.

Utilice almacenamiento premium localmente redundante (o localmente redundante para cargas de trabajo de perfil bajo o de control de calidad) para las cuentas de almacenamiento que contienen los discos; no es necesario replicar entre geografías y zonas para conseguir alta disponibilidad en Elasticsearch.

**Discos efímeros**

El uso de discos persistentes basados en SSD requiere la creación de máquinas virtuales que admitan el Almacenamiento premium. Esto tiene una implicación de precio. El uso del disco efímero local para almacenar los datos de Elasticsearch puede ser una solución rentable para los nodos de tamaño moderado que requieren hasta 800 GB de almacenamiento aproximadamente. En la serie D estándar de máquinas virtuales, se implementan discos efímeros mediante unidades SSD que proporcionan mayor rendimiento y latencia mucho más baja que los discos normales

Al utilizar Elasticsearch, el rendimiento puede ser equivalente al uso de almacenamiento premium sin incurrir en el costo. Para más información, consulte la sección [Addressing Disk Latency Issues](#addressing-disk-latency-issues) (Solución de problemas de latencia de disco).

El tamaño de la máquina virtual limita la cantidad de espacio disponible en el almacenamiento efímero, como se describe en el documento [D-Series Performance Expectations](https://azure.microsoft.com/blog/d-series-performance-expectations/) (Expectativas de rendimiento de la serie D).

Por ejemplo, una máquina virtual Standard\_D1 proporciona 50 GB de almacenamiento efímero, una máquina virtual Standard\_D2 tiene 100 GB de almacenamiento efímero y una máquina virtual Standard\_D14 proporciona 800 GB de espacio efímero. En clústeres donde los nodos solo necesitan esta cantidad de espacio, el uso de una máquina virtual de la serie D con almacenamiento efímero puede ser una opción rentable.

Debe equilibrar el aumento del rendimiento disponible con el almacenamiento efímero en relación al tiempo y los costos implicados en la recuperación de estos datos después de reiniciar el equipo. El contenido del disco efímero se pierde si se mueve la máquina virtual a otro servidor host, si se actualiza el host, o si el host sufre un error de hardware. Si los datos en sí tienen una duración limitada, esta pérdida de datos podría ser tolerable. Para los datos más antiguos, es posible volver a generar un índice o recuperar la información que falta desde una copia de seguridad. Es posible minimizar la pérdida potencial mediante el uso de réplicas que se mantiene en otras máquinas virtuales.

> [AZURE.NOTE] No utilice una **sola** máquina virtual para mantener datos de producción críticos. Si se produce un error en el nodo, todos los datos dejarán de estar disponibles. Para la información crítica, asegúrese de que los datos se replican en al menos otro nodo.

**Archivos de Azure**

El [servicio de archivos de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) proporciona acceso a los archivos compartidos mediante Almacenamiento de Azure. Puede crear recursos compartidos de archivos que, a continuación, puede montar en máquinas virtuales de Azure. Se pueden montar varias máquinas virtuales en el mismo recurso compartido de archivos, lo que les permite acceder a los mismos datos.

Por motivos de rendimiento, no se recomienda utilizar recursos compartidos de archivos para que contengan datos de Elasticsearch que no es necesario compartir entre los nodos; los discos de datos regulares son más adecuados para este propósito. Los recursos compartidos de archivos se pueden usar para crear [índices de réplica paralelos](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-shadow-replicas.html) de Elasticsearch. Sin embargo, esta característica es experimental actualmente y no debe implementarse en un entorno de producción en este momento. Por este motivo, los índices paralelos no se explican más adelante en esta guía.

**Opciones de red**

Azure implementa un esquema de redes compartido. Las máquinas virtuales que utilizan los mismos bastidores de hardware compiten por los recursos de red. Por lo tanto, el ancho de banda de red disponible puede variar según la hora del día y el ciclo de trabajo diario que se ejecuta en máquinas virtuales que comparten la misma infraestructura de red física. Tiene poco control sobre estos factores. Es importante comprender que el rendimiento de la red es probable que fluctúe con el tiempo; por tanto, ajuste las expectativas del usuario de acuerdo a ello.

## Escalado vertical de nodos para la compatibilidad con la ingesta de datos a gran escala

Puede crear clústeres de Elasticsearch mediante hardware razonablemente moderado y luego escalar vertical u horizontalmente a medida que aumenta el volumen de datos y el número de solicitudes. Con Azure, puede escalar verticalmente mediante la ejecución en máquinas virtuales más grandes y más caras, o bien escalar horizontalmente mediante el uso de máquinas virtuales adicionales más pequeñas y baratas.

También puede realizar una combinación de ambas estrategias. No hay ninguna solución única para todos, por tanto, para evaluar el mejor enfoque para una situación determinada, debe estar preparado para llevar a cabo una serie de pruebas de rendimiento.

Esta sección está relacionada con el enfoque de escalado vertical; el escalado horizontal se describe en la sección de [conclusiones del escalado horizontal](#scaling-out-conclusions). En esta sección se describen los resultados de una serie de pruebas comparativas realizadas con un conjunto de clústeres de Elasticsearch que incluyen máquinas virtuales con distintos tamaños. Los clústeres se han designado como pequeños, medianos y grandes. La tabla siguiente resume los recursos asignados a las máquinas virtuales en cada clúster.

| Clúster | SKU de la máquina virtual | Número de núcleos | Número de discos de datos | RAM |
|---------|-------------|-----------------|----------------------|------|
| Pequeña | D2 estándar | 2 | 4 | 7 GB |
| Mediano | D3 estándar | 4 | 8 | 14 GB |
| Grande | D4 estándar | 8 | 16 | 28 GB |

Cada clúster de Elasticsearch contenía 3 nodos de datos. Estos nodos de datos controlaban las solicitudes del cliente y administraban el procesamiento de datos; no se utilizaron nodos cliente independientes porque ofrecían pocas ventajas para el escenario de ingesta de datos utilizado por las pruebas. El clúster también contenía tres nodos maestros, uno de los cuales fue elegido por Elasticsearch para coordinar el clúster.

Las pruebas se realizaron utilizando ElasticSearch 1.7.3. Las pruebas se realizaron inicialmente en clústeres en los que se ejecutaba Ubuntu Linux 14.0.4 y, a continuación, se repitieron con Windows Server 2012. Los detalles de la carga de trabajo ejecutada en las pruebas se describen en el [Apéndice](#appendix-the-bulk-load-data-ingestion-performance-test), al final de este documento.

### Rendimiento de ingesta de datos: Ubuntu Linux 14.0.4

En la tabla siguiente se resumen los resultados de las pruebas durante dos horas para cada configuración general:

| Configuración | N.º de muestras | Tiempo de respuesta promedio (ms) | Rendimiento (operaciones/s) |
|---------------|-----------|----------------------------|---------------------------|
| Pequeña | 67057 | 636 | 9\.3 |
| Mediano | 123482 | 692 | 17\.2 |
| Grande | 197085 | 839 | 27\.4 |

El rendimiento y la cantidad de muestras procesadas para las tres configuraciones tiene una relación aproximada de 1:2:3. Sin embargo, los recursos de memoria, los núcleos de CPU y los discos disponibles tienen una relación 1:2:4. Se pensó que merecía la pena investigar los detalles de rendimiento de bajo nivel de los nodos del clúster para determinar por qué éste podría ser el caso. Esta información puede ayudarle a determinar si hay límites de escalado vertical y cuándo puede ser mejor considerar el escalado horizontal.

### Determinación de los factores de limitación: uso de la red

Elasticsearch depende de tener suficiente ancho de banda de red para admitir la entrada de solicitudes de cliente, así como la información de sincronización que fluye entre los nodos del clúster. Tal como se resaltó anteriormente, tiene un control limitado sobre la disponibilidad del ancho de banda, que depende de muchas variables, como el centro de datos en uso y la carga de red actual de otras máquinas virtuales que comparten la misma infraestructura de red. De todas formas, merece la pena examinar la actividad de la red para cada clúster aunque solo sea para comprobar que el volumen de tráfico no es excesivo. El siguiente gráfico muestra una comparación del tráfico de red recibido por el nodo 2 en cada uno de los clústeres (los volúmenes de los demás nodos en cada clúster era muy similar).

![](media/guidance-elasticsearch/data-ingestion-image1.png)

El promedio de bytes recibido por segundo para el nodo 2 en cada configuración de clúster, durante el período de dos horas fue como sigue:

| Configuración | Número promedio de bytes recibidos/seg. |
|---------------|--------------------------------------|
| Pequeña | 3993640,3 |
| Mediano | 7311689,9 |
| Grande | 11893874\.2 |

Las pruebas se llevaron a cabo mientras el sistema se estaba ejecutando en **estado estable**. En situaciones en las que se produce un reequilibrio del índice o se está produciendo una recuperación de nodo, las transmisiones de datos entre los nodos que contienen las particiones principal y de réplica, pueden generar un tráfico de red significativo. Los efectos de este proceso se describen con más detalle en el documento [Configuración de resistencia y recuperación en Elasticsearch en Azure][].

### Determinación de los factores de limitación: Uso de CPU

La velocidad a la que se administran las solicitudes se rige, al menos parcialmente, por la capacidad de procesamiento disponible. Elasticsearch acepta solicitudes de inserción masiva en la cola de inserción masiva. Cada nodo tiene un conjunto de colas de inserción masiva determinadas por el número de procesadores disponibles. De forma predeterminada, hay una cola para cada procesador y cada una de ellas puede contener hasta 50 solicitudes pendientes, antes de llegar al punto en el que empiezan a rechazarse.

Las aplicaciones deben enviar solicitudes a una velocidad que no cause el desbordamiento de las colas. El número de elementos en cada cola en cualquier momento dado, será una función de la velocidad a la que las aplicaciones cliente envían las solicitudes y la velocidad a la que Elasticsearch recupera y procesa estas mismas solicitudes. Por esta razón, una estadística importante recogida se refiere a la tasa de error y se resume en la tabla siguiente.

| Configuración | Muestras totales | N.º de errores | Tasa de errores |
|---------------|---------------|-----------|------------|
| Pequeña | 67057 | 0 | 0,00 % |
| Mediano | 123483 | 1 | 0,0008 % |
| Grande | 200702 | 3617 | 1,8 % |

Cada uno de estos errores se produjo por la siguiente excepción de Java:

```
org.elasticsearch.action.support.replication.TransportShardReplicationOperationAction$PrimaryPhase$1@75a30c1b]; ]
[219]: index [systembase], type [logs], id [AVEAioKb2TRSNcPa_8YG], message [RemoteTransportException[[esdatavm2][inet[/10.0.1.5:9300]][indices:data/write/bulk[s]]]; nested: EsRejectedExecutionException[rejected execution (queue capacity 50)
```

Aumentar el número de colas o la longitud de cada cola puede reducir el número de errores, pero este enfoque solo funciona para ráfagas de corta duración. Hacer esto mientras se ejecuta una serie de tareas de ingesta de datos de forma sostenida, simplemente retrasará el punto en el que empiezan a aparecer los errores. Además, este cambio no mejorará el rendimiento y es probable que dañe el tiempo de respuesta de las aplicaciones cliente, ya que las solicitudes permanecerán en cola más tiempo antes de empezar a procesarse.

La estructura predeterminada del índice de cinco particiones con una réplica (10 particiones en total), da como resultado un modesto desequilibrio en la carga entre los nodos de un clúster; dos nodos contendrán tres particiones, mientras que el otro nodo contendrá cuatro. El nodo más ocupado es probablemente el elemento que más restringe el rendimiento, razón por la que se ha seleccionado este nodo en cada caso.

Los gráficos a continuación ilustran el uso de CPU para el nodo más ocupado de cada clúster.

![](media/guidance-elasticsearch/data-ingestion-image2.png)

![](media/guidance-elasticsearch/data-ingestion-image3.png)

![](media/guidance-elasticsearch/data-ingestion-image4.png)

En el caso de los clústeres pequeño, mediano y grande, el uso medio de CPU para estos nodos ha sido 75,01 %, 64,93 % y 64,64 %. La utilización raramente alcanza el 100 %, y desciende a medida que aumenta el tamaño de los nodos y la potencia de CPU disponible. La potencia de CPU, por tanto, es improbable que sea un factor que limite el rendimiento del clúster grande.

### Determinación de los factores de limitación: Memoria

El uso de la memoria es otro aspecto importante que puede influir en el rendimiento. Para las pruebas, se le asignó a Elasticsearch el 50 % de la memoria disponible, en consonancia con las [recomendaciones documentadas](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#_give_half_your_memory_to_lucene). Mientras se ejecutaban las pruebas, se supervisó JVM para detectar el exceso de actividad de recolección de elementos no utilizados (una indicación de falta de memoria de montón). En todos los casos, el tamaño del montón era estable y JVM mostró un nivel bajo de recolección de elementos no utilizados. La captura de pantalla siguiente muestra una instantánea de Marvel, resaltando las estadísticas claves de JVM durante un breve período mientras se realizaba la prueba en el clúster grande.

![](media/guidance-elasticsearch/data-ingestion-image5.png)

***Memoria JVM y actividad de recolección de elementos no utilizados en el clúster grande.***

### Determinación de los factores de limitación: Tasas de E/S del disco

La última característica física en el servidor que puede restringir el rendimiento, es el rendimiento del subsistema de E/S de disco. El gráfico siguiente compara la actividad del disco en términos de bytes escritos para los nodos más ocupados de cada clúster.

![](media/guidance-elasticsearch/data-ingestion-image6.png)

La tabla siguiente muestra el promedio de bytes escrito por segundo para el nodo 2 en cada configuración de clúster durante el período de dos horas:

| Configuración | El número del promedio de bytes escritos/seg. |
|---------------|-------------------------------------|
| Pequeña | 25502361,94 |
| Mediano | 48856124,5 |
| Grande | 88137675,46 |

El volumen de los datos escritos aumenta con el número de solicitudes procesadas por un clúster, pero las tasas de E/S se encuentran dentro de los límites de Almacenamiento de Azure (los discos creados mediante el uso de Almacenamiento de Azure pueden admitir una tasa sostenida de 10s a 100s MB/s, dependiendo de si se utiliza el almacenamiento estándar o premium). El examen de la cantidad de tiempo empleado en la espera de E/S de disco ayuda a explicar por qué el rendimiento del disco se sitúa muy por debajo del máximo teórico. Los gráficos y la tabla siguiente muestran estas estadísticas para los mismos tres nodos:

> [AZURE.NOTE] El tiempo de espera de disco se mide supervisando el porcentaje de tiempo de CPU durante el que se bloquean los procesadores esperando por la finalización de las operaciones de E/S.

![](media/guidance-elasticsearch/data-ingestion-image7.png)

![](media/guidance-elasticsearch/data-ingestion-image8.png)

![](media/guidance-elasticsearch/data-ingestion-image9.png)

| Configuración | Tiempo de CPU de espera promedio del disco (%) |
|---------------|--------------------------------|
| Pequeña | 21,04 |
| Mediano | 14,48 |
| Grande | 15,84 |

Estos datos indican que se ha dedicado una proporción considerable de tiempo de CPU (entre casi 16 % y 21 %) esperando a que las operaciones E/S del disco finalicen. Esto limita la capacidad de Elasticsearch para procesar las solicitudes y almacenar los datos.

Durante la ejecución de prueba, el clúster grande insertó más de **quinientos millones de documentos**. Al continuar con la prueba se mostró que los tiempos de espera aumentaron significativamente cuando la base de datos contenía más de seiscientos millones de documentos. Las razones para este comportamiento no se investigaron a fondo, pero puede deberse a la fragmentación del disco que provoca un aumento en la latencia del mismo.

Aumentar el tamaño del clúster en más nodos puede ayudar a mitigar los efectos de este comportamiento. En casos extremos puede ser necesario desfragmentar un disco que muestre tiempos excesivos de E/S. Pero desfragmentar un disco de gran tamaño puede tardar un tiempo considerable (posiblemente más de 48 horas para una unidad de disco duro virtual de 2 TB), mientras que simplemente volver a formatear la unidad y permitir a Elasticsearch recuperar los datos que faltan a partir particiones de réplica puede ser un enfoque más rentable.

### Solución de los problemas de latencia de disco

Las pruebas se realizaron inicialmente con máquinas virtuales configuradas con discos estándares. Un disco estándar se basa en discos duros y como consecuencia, es susceptible de sufrir problemas de latencia de rotación y otros cuellos de botella que pueden restringir las velocidades de E/S. Azure también proporciona almacenamiento premium en el que los discos se crean mediante dispositivos SSD. Estos dispositivos no tienen latencia de rotación y como resultado, deben proporcionar mejores velocidades de E/S.

La tabla siguiente compara los resultados de reemplazar discos estándares con discos premium en el clúster grande (las máquinas virtuales D4 estándar en el clúster grande se reemplazaron con máquinas virtuales DS4 estándar; el número de núcleos, memoria y discos era el mismo en ambos casos, la única diferencia es que las máquinas virtuales de DS4 usan SSD).

| Configuración | N.º de muestras | Tiempo de respuesta promedio (ms) | Rendimiento (operaciones/s) |
|------------------|-----------|----------------------------|---------------------------|
| Grande: estándar | 197085 | 839 | 27\.4 |
| Grande: Premium | 255985 | 581 | 35,6 |

Los tiempos de respuesta fueron considerablemente mejores, lo que resulta en un rendimiento medio mucho más cercano a 4x que el del clúster pequeño. Esto se encuentra más en consonancia con los recursos disponibles en una máquina virtual DS4 estándar. El uso promedio de CPU en el nodo más ocupado del clúster (nodo 1 en este caso) aumentó, ya que pasó menos tiempo esperando que se terminasen las operaciones E/S:

![](media/guidance-elasticsearch/data-ingestion-image10.png)

La reducción en el tiempo de espera de disco se hace evidente cuando considera el siguiente gráfico, en el que se muestra que para el nodo más ocupado esta estadística descendió alrededor del 1 % de media:

![](media/guidance-elasticsearch/data-ingestion-image11.png)

Sin embargo esta mejora tiene un precio. El número de errores de ingesta aumentó en un factor de 10 a 35797 (12,3 %). De nuevo, la mayoría de estos errores fueron el resultado del desbordamiento de la cola de inserción masiva. Dado que ahora el hardware parece funcionar más próximo a su capacidad, puede ser necesario agregar más nodos o limitar la tasa de las inserciones masivas para reducir el volumen de errores. Estos problemas se explican más adelante en este documento.

### Pruebas con almacenamiento efímero

Las mismas pruebas se repitieron en un clúster de máquinas virtuales D4 usando almacenamiento efímero. En máquinas virtuales D4, el almacenamiento efímero se implementa como un SSD único de 400 GB. El número de muestras que se procesaron, el tiempo de respuesta y el rendimiento fueron todos muy similares a las cifras recogidas para el clúster basado en las máquinas virtuales DS14 con almacenamiento premium.

| Configuración | N.º de muestras | Tiempo de respuesta promedio (ms) | Rendimiento (operaciones/s) |
|-----------------------------------|-----------|----------------------------|---------------------------|
| Grande: Premium | 255985 | 581 | 35,6 |
| Grande: estándar (disco efímero) | 255626 | 585 | 35,5 |

La tasa de errores también fue similar (33862 errores en 289488 solicitudes, en total: 11,7 %).

Los gráficos siguientes muestran las estadísticas del uso de CPU y de espera de disco para el nodo más ocupado en el clúster (nodo 2 esta vez):

![](media/guidance-elasticsearch/data-ingestion-image12.png)

![](media/guidance-elasticsearch/data-ingestion-image13.png)

En este caso, solo en términos de rendimiento, el uso del almacenamiento efímero puede considerarse una solución más rentable que el uso de almacenamiento premium.

### Rendimiento de ingesta de datos: Windows Server 2012

Las mismas pruebas se repitieron utilizando un conjunto de clústeres Elasticsearch con nodos que ejecutan Windows Server 2012. La intención de estas pruebas era establecer qué efectos, si los hubiera, podría tener la elección del sistema operativo en el rendimiento del clúster.

Para ilustrar la escalabilidad de Elasticsearch en Windows, la siguiente tabla muestra el rendimiento y tiempos de respuesta obtenidos por las configuraciones de clúster pequeño, mediano y grande. Tenga en cuenta que estas pruebas se realizaron con Elasticsearch configurado para utilizar el almacenamiento efímero SSD, como ya habían mostrado las pruebas con Ubuntu que la latencia de disco era probablemente un factor muy importante para lograr un rendimiento máximo:

| Configuración | N.º de muestras | Tiempo de respuesta promedio (ms) | Rendimiento (operaciones/s) |
|---------------|-----------|----------------------------|---------------------------|
| Pequeña | 90295 | 476 | 12,5 |
| Mediano | 169243 | 508 | 23,5 |
| Grande | 257115 | 613 | 35,6 |

Estos resultados indican cómo Elasticsearch se adapta al tamaño de la máquina virtual y los recursos disponibles en Windows.

Las tablas siguientes comparan los resultados para el clúster grande en Ubuntu y Windows:

| Sistema operativo | N.º de muestras | Tiempo de respuesta promedio (ms) | Rendimiento (operaciones/s) | Tasa de errores (%) |
|------------------|-----------|----------------------------|---------------------------|----------------|
| Ubuntu | 255626 | 585 | 35,5 | 11,7 |
| Windows | 257115 | 613 | 35,6 | 7,2 |

El rendimiento fue consistente con el de los clústeres grandes de Ubuntu, aunque el tiempo de respuesta fue ligeramente superior, lo que puede explicarse por la tasa de error inferior (los errores notifican más rápidamente que las operaciones correctas, por lo que tienen un tiempo de respuesta inferior).

El uso de CPU, notificado por las herramientas de supervisión de Windows, fue ligeramente superior al del registrado para Ubuntu. De todas forma, debe tratar las comparaciones directas de mediciones como estas entre sistemas operativos con extrema precaución, debido a la manera en la que diferentes sistemas operativos informan sobre estas estadísticas. Además, la información sobre la latencia de disco en términos de tiempo de CPU empleado en esperar por E/S,no está disponible de la misma manera en la que está para Ubuntu. Lo importante es que el uso de CPU ha sido alto, lo que indica que el tiempo de espera empleado para E/S fue bajo:

![](media/guidance-elasticsearch/data-ingestion-image14.png)

### Escalado vertical: conclusiones

El rendimiento de Elasticsearch para un clúster bien ajustado es probable que sea equivalente en Windows y Ubuntu, y se escala verticalmente siguiendo un patrón similar en ambos sistemas operativos. Para obtener el mejor rendimiento, **utilice almacenamiento premium para mantener los datos de Elasticsearch**.

## Escalado horizontal de clústeres para la compatibilidad con la ingesta de datos a gran escala

El escalado horizontal es el enfoque complementario al escalado vertical investigado en la sección anterior. Una característica importante de Elasticsearch es la escalabilidad horizontal inherente que está integrada en el software. Aumentar el tamaño de un clúster es simplemente una cuestión de agregar más nodos. No es necesario realizar ninguna operación manual para redistribuir los índices o particiones ya que estas tareas se controlan automáticamente, aunque hay una serie de opciones de configuración disponibles a través de las que puede influir en este proceso.

Agregar más nodos ayuda a mejorar el rendimiento al repartir la carga entre más máquinas. Al agregar más nodos, es posible que tenga que considerar volver a indexar los datos para aumentar el número de particiones disponibles. Puede adelantar este proceso hasta cierto punto mediante la creación de índices que tienen más particiones que nodos disponibles hay inicialmente. Cuando se agregan más nodos, las particiones se pueden distribuir.

Además de aprovechar la escalabilidad horizontal de Elasticsearch, existen otras razones para implementar índices que tienen más particiones que nodos. Cada partición se implementa como una estructura de datos independiente (un índice [Lucene](https://lucene.apache.org/)), y tiene sus propios mecanismos internos para mantener la coherencia y controlar la simultaneidad. Crear varias particiones ayuda a aumentar el paralelismo dentro de un nodo y puede mejorar el rendimiento.

Sin embargo, mantener el rendimiento al tiempo que se escala es un ejercicio de equilibrio. Cuantos más nodos y particiones contenga un clúster, mayor esfuerzo se requiere para sincronizar el trabajo realizado por el clúster, lo que puede reducir el rendimiento. Para cualquier carga de trabajo determinada hay un punto ideal que maximiza el rendimiento de la ingesta a la vez que minimiza la sobrecarga de mantenimiento. Este punto ideal será muy dependiente de la naturaleza de la carga de trabajo y del clúster. En concreto, del volumen, tamaño y contenido de los documentos, de la velocidad a la que se produce la ingesta y del hardware en el que se ejecuta el sistema.

Esta sección resume los resultados de las investigaciones sobre el tamaño de los clústeres diseñados para admitir la carga de trabajo utilizada por las pruebas de rendimiento que se han descrito anteriormente. Se realizó la misma prueba en clústeres con máquinas virtuales basándose en el tamaño grande de máquina virtual (D4 estándar con 8 núcleos de CPU, 16 discos de datos y 28 GB de RAM) que ejecuta Ubuntu Linux 14.0.4, pero configurada con distintos números de nodos y particiones. Los resultados no pretenden ser definitivos, ya que se aplican a un único escenario específico, pero pueden actuar como un buen punto de partida para ayudarle a analizar la escalabilidad horizontal de los clústeres y generar los números de la relación óptima entre particiones y nodos que satisfagan mejor sus requisitos.

### Resultados de línea de base: 3 nodos

Para obtener unas cifras de línea de base, se ejecutó la prueba de rendimiento de ingesta de datos en un clúster de 3 nodos con 5 particiones y 1 réplica. Esta es la configuración predeterminada para un índice de Elasticsearch. En esta configuración, Elasticsearch distribuye 2 particiones principales en 2 de los nodos y la partición principal restante se almacena en el tercer nodo. La tabla siguiente resume el rendimiento en términos de operaciones de ingesta en bloque por segundo y el número de documentos que se almacenaron correctamente en la prueba.

> [AZURE.NOTE] En las tablas siguientes en esta sección, la distribución de las particiones principales se presenta como un número para cada nodo separado por guiones. Por ejemplo, se describe el diseño de 5 particiones y 3 nodos como 2-2-1. El diseño de particiones de réplica no se incluye, estas siguen un esquema similar a las particiones principales.

| Configuración | N.º de documentos | Rendimiento (operaciones por segundo) | Diseño de particiones |
|---------------|--------------|-----------------------------|--------------|
| 5 particiones | 200560412 | 27,86 | 2-2-1 |

### Resultados de 6 nodos

La prueba se repitió en un clúster de 6 nodos. El propósito de estas pruebas era intentar determinar con mayor precisión los efectos de almacenar más de una partición en un nodo.

| Configuración | N.º de documentos | Rendimiento (operaciones por segundo) | Diseño de particiones |
|---------------|--------------|-----------------------------|--------------|
| 4 particiones | 227360412 | 31,58 | 1-1-0-1-1-0 |
| 7 particiones | 268013252 | 37,22 | 2-1-1-1-1-1 |
| 10 particiones | 258065854 | 35,84 | 1-2-2-2-1-2 |
| 11 particiones | 279788157 | 38,86 | 2-2-2-1-2-2 |
| 12 particiones | 257628504 | 35,78 | 2-2-2-2-2-2 |
| 13 particiones | 300126822 | 41,68 | 2-2-2-2-2-3 |

Estos resultados parecen indicar las siguientes tendencias:

* Más particiones por nodo mejoran el rendimiento. Con un número reducido de particiones por nodo creado para estas pruebas, se esperaba este fenómeno por razones descritas anteriormente.

* Un número impar de particiones proporciona mejor rendimiento que un número par. Las razones de esto son menos claras, pero *puede* ser que el algoritmo de enrutamiento que utiliza Elasticsearch esté mejor capacitado para distribuir los datos entre las particiones en este caso, dando lugar a una carga más uniforme por nodo.

Para probar estas hipótesis, se realizaron varias pruebas más con un mayor número de particiones. Siguiendo los consejos verbales de Elasticsearch, se decidió usar un número primo de particiones para cada prueba ya que estas le proporcionan una distribución de números impares razonable para el intervalo en cuestión.

| Configuración | N.º de documentos | Rendimiento (operaciones por segundo) | Diseño de particiones |
|---------------|--------------|-----------------------------|-------------------|
| 23 particiones | 312844185 | 43,45 | 4-4-4-3-4-4 |
| 31 particiones | 309930777 | 43,05 | 5-5-5-5-6-5 |
| 43 particiones | 316357076 | 43,94 | 8-7-7-7-7-7 |
| 61 particiones | 305072556 | 42,37 | 10-11-10-10-10-10 |
| 91 particiones | 291073519 | 40,43 | 15-15-16-15-15-15 |
| 119 particiones | 273596325 | 38,00 | 20-20-20-20-20-19 |

Estos resultados sugieren que se ha alcanzado un punto crítico en aproximadamente 23 particiones. Después de este punto, el aumento del número de particiones provocó una pequeña degradación del rendimiento (el rendimiento de 43 particiones posiblemente es una anomalía).

### Resultados de 9 nodos

Las pruebas se repitieron usando un clúster de 9 nodos, con un número primo de particiones.

| Configuración | N.º de documentos | Rendimiento (operaciones por segundo) | Diseño de particiones |
|---------------|--------------|-----------------------------|----------------------------|
| 17 particiones | 325165364 | 45,16 | 2-2-2-2-2-2-2-2-1 |
| 19 particiones | 331272619 | 46,01 | 2-2-2-2-2-2-2-2-3 |
| 29 particiones | 349682551 | 48,57 | 3-3-3-4-3-3-3-4-3 |
| 37 particiones | 352764546 | 49,00 | 4-4-4-4-4-4-4-4-5 |
| 47 particiones | 343684074 | 47,73 | 5-5-5-6-5-5-5-6-5 |
| 89 particiones | 336248667 | 46,70 | 10-10-10-10-10-10-10-10-9 |
| 181 particiones | 297919131 | 41,38 | 20-20-20-20-20-20-20-20-21 |

Estos resultados mostraron un patrón similar, con un punto crítico alrededor de las 37 particiones.

### Escalado horizontal: conclusiones

Realizando una extrapolación aproximada, los resultados de las pruebas con 6 nodos y 9 nodos indican que, para este escenario específico, el número ideal de particiones para maximizar el rendimiento fue 4n +/-1, donde n es el número de nodos. Esto *puede* ser una función del número de subprocesos de inserción masiva disponibles, que a su vez depende del número de núcleos de CPU. La lógica se expone a continuación. Consulte el documento [Multidocument Patterns](https://www.elastic.co/guide/en/elasticsearch/guide/current/distrib-multi-doc.html#distrib-multi-doc) (Patrones de multidocumento) para más información:

- Cada solicitud de inserción masiva enviada por la aplicación cliente se recibe en un único nodo de datos.

- El nodo de datos crea una nueva solicitud de inserción masiva para cada partición principal afectada por la solicitud original y las reenvía a los demás nodos, en paralelo.

- Como cada partición principal está escrita, se envía otra solicitud a cada réplica para esa partición. La partición principal espera a que la solicitud que se envía a la réplica se complete antes de finalizar.

De forma predeterminada, Elasticsearch crea un subproceso de inserción masiva para cada núcleo de CPU disponible en una máquina virtual. En el caso de las máquinas virtuales D4 que se han utilizado en esta prueba, cada CPU incluía 8 núcleos, por lo que se crearon 8 subprocesos de inserción masiva. El índice utilizado ocupa 4 (en un caso 5) particiones principales en cada nodo, pero también había 4 (5) réplicas en cada nodo. Insertar datos en estas particiones y réplicas podría consumir hasta 8 subprocesos en cada nodo por solicitud, lo que coincide con el número disponible. Aumentar o reducir el número de particiones puede provocar ineficiencia en los subprocesos, ya que los subprocesos pueden quedarse no ocupados o las solicitudes se ponen en cola. Sin embargo, sin experimentación adicional esto es simplemente una teoría y no se pueden realizar afirmaciones definitivas.

Las pruebas también muestran otro punto importante. En este escenario, el aumento del número de nodos puede mejorar el rendimiento de la ingesta de datos, pero los resultados no necesariamente escalan de formar linear. La realización de más pruebas con clústeres de 12 y 15 nodos, podría mostrar el punto a partir del que el escalado horizontal aporta pocas ventajas adicionales. Si este número de nodos proporciona espacio de almacenamiento insuficiente, puede ser necesario volver a la estrategia de escalado vertical y empezar a usar discos más grandes o mayor cantidad de ellos basándose en almacenamiento premium.

> [AZURE.IMPORTANT] No asuma que la proporción 4n+/-1 es una fórmula mágica que va a funcionar siempre para cada clúster. Si dispone de más o menos núcleos de CPU, la configuración óptima de particiones podría ser diferente. Los resultados se basaban en una carga de trabajo específica que realizó solo ingesta de datos. Para cargas de trabajo que también incluyen una combinación de consultas y agregaciones, los resultados podrían ser muy diversos.

> Además, la carga de trabajo de ingesta de datos usó un índice único. En muchas situaciones, es probable que los datos se distribuyan entre varios índices dando lugar a distintos patrones o usos de los recursos.

> Lo importante de este ejercicio es comprender el método usado, más que centrarse en los resultados obtenidos. Debe estar preparado para realizar su propia evaluación de escalabilidad según sus propia cargas de trabajo, lo que le permitirá obtener información más aplicable a su propio escenario.

## Ajuste de la ingesta de datos a gran escala

Elasticsearch es altamente configurable, con muchos conmutadores y opciones que puede usar para optimizar el rendimiento de escenarios y casos de uso específicos. Esta sección describe algunos ejemplos comunes. Tenga en cuenta que la flexibilidad que ofrece Elasticsearch en este sentido viene acompañada de una advertencia: es muy fácil desajustar Elasticsearch y empeorar el rendimiento. Al realizar el ajuste, haga un solo cambio de cada vez y mida siempre los efectos de cualquier cambio para asegurarse de que no son perjudiciales para el sistema.

### Optimización de recursos para las operaciones de indexación

La lista siguiente describe algunos puntos que debe tener en cuenta al ajustar un clúster Elasticsearch para que admita la ingesta de datos a gran escala. En el caso de los dos primeros elementos, es muy probable que tenga un efecto inmediato perceptible en el rendimiento, mientras que en el resto el efecto es más marginal, dependiendo de la carga de trabajo:

*  Los nuevos documentos agregados a un índice solo son visibles para las búsquedas cuando se actualiza el índice. Actualizar un índice es una operación costosa, por lo que solo se realiza periódicamente en lugar de hacerlo cada vez que se crea un documento. El intervalo de actualización predeterminado es de 1 segundo. Si se están realizando operaciones masivas, considere la posibilidad de deshabilitar temporalmente las actualizaciones de índice; establezca el índice *refresh\_interval* en -1.

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"refresh_interval": -1
		}
	}
	```

	Para hacer visibles los datos al final de la operación, realice una actualización manualmente usando la API [*\_refresh*](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html). Consulte [Bulk Indexing Usage](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html#bulk) (Uso de indexación masiva) para más información. Más adelante puede encontrar información detallada sobre el [impacto de cambiar el intervalo de actualización en la ingesta de datos](#the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance).

* Si se replica un índice, cada operación de indexación (creación, actualización o eliminación de un documento) se repite en las particiones de réplica que se produzcan en la partición principal. Considere la posibilidad de deshabilitar la replicación durante las operaciones de importación masiva y, a continuación, vuelva a habilitarla una vez completada la importación:

    ```http
	PUT /my_busy_index
	{
		"settings" : {
			"number_of_replicas": 0
		}
	}
	```

	Cuando vuelva a habilitar la replicación, Elasticsearch realiza a una transferencia de red byte por byte de los datos del índice a cada réplica. Esto es más eficiente que repetir el proceso de indexación documento por documento en cada nodo. El riesgo es que los datos pueden perderse de los fallos del nodo principal al realizar la importación masiva, pero la recuperación puede ser simplemente una cuestión de volver a iniciar la importación. Más adelante se describe el [impacto de la replicación en el rendimiento de la ingesta de datos](#the-impact-of-replicas-on-data-ingestion-performance).

* Elasticsearch intenta equilibrar los recursos disponibles entre aquellos necesarios para realizar consultas y los necesarios para la ingesta de datos. Como resultado, puede limitar el rendimiento de la ingesta de datos (los eventos de limitación se registran en el registro de Elasticsearch). Esta restricción está pensada para evitar que se creen al mismo tiempo un gran número de segmentos de índice, con las consiguientes demandas de combinar y guardar en disco, un proceso que puede monopolizar los recursos. Si el sistema no está realizando consultas en ese momento, puede deshabilitar la limitación de ingesta de datos. Esto debe permitir maximizar el rendimiento de la indexación. Puede deshabilitar la limitación para todo un clúster como sigue:

	```http
	PUT /_cluster/settings
	{
		"transient" : {
			"indices.store.throttle.type": "none"
		}
	}
	```

    Una vez terminada la ingesta, vuelva a establecer el tipo de limitación del clúster en *"merge"*. También tenga en cuenta que deshabilitar la limitación puede provocar inestabilidad en el clúster, así que asegúrese de que tiene los procedimientos adecuados para recuperar el clúster si fuese necesario.

* Elasticsearch reserva una parte de la memoria de montón para operaciones de indexación; el resto se usa principalmente para las consultas y búsquedas. El propósito de estos búferes es reducir el número de operaciones de E/S de disco, con el fin de realizar menos escrituras más grandes, en lugar de más escrituras más pequeñas. La proporción predeterminada de memoria de montón asignada es 10 %. Si indexa un gran volumen de datos este valor puede ser insuficiente. Para sistemas que admiten la ingesta de datos de gran volumen, debería permitir hasta 512 MB de memoria para cada partición activa en el nodo. Por ejemplo, si ejecuta Elasticsearch en máquinas virtuales D4 (28 GB de RAM) y ha asignado el 50 % de la memoria disponible a JVM (14 GB), tendrá 1,4 GB disponibles para su uso por las operaciones de indexación. Si un nodo contiene 3 particiones activas, esta configuración es probablemente suficiente. Sin embargo, si un nodo contiene más particiones, considere la posibilidad de aumentar el valor del parámetro *indices.memory.index\_buffer\_size* en el archivo de configuración elasticsearch.yml. Para más información, consulte [Performance Considerations for Elasticsearch Indexing](https://www.elastic.co/blog/performance-considerations-elasticsearch-indexing) (Consideraciones de rendimiento para la indexación de Elasticsearch).

    Asignar más de 512 MB por partición activa probablemente no mejorará el rendimiento de la indexación, pero sin embargo puede ser perjudicial ya que deja menos memoria disponible para realizar otras tareas. También tenga en cuenta que asignar más espacio de montón para los búferes de índice quita memoria para otras operaciones como la búsqueda y agregación de datos, y además puede ralentizar el rendimiento de las operaciones de consulta.

* Elasticsearch restringe el número de subprocesos (el valor predeterminado es 8) que pueden realizar simultáneamente operaciones de indexación en una partición. Si un nodo contiene solo un pequeño número de particiones, considere la posibilidad de aumentar el valor de *index\_concurrency* para un índice que esté sujeto a un gran volumen de operaciones de indexación o que sea el destino de una inserción masiva, como se indica a continuación:

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"index_concurrency": 20
		}
	}
	```

* Si está realizando un gran número de operaciones masivas y de indexación durante un breve período de tiempo, puede aumentar el número de subprocesos *index* y *bulk* disponibles en el grupo de subprocesos y ampliar el tamaño de la cola de *inserción masiva* para cada nodo de datos. Esto permitirá poner en cola más solicitudes en lugar de descartarlas. Para más información, consulte [Thread Pool](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-threadpool.html) (Grupo de subprocesos). Si está realizando altos niveles de ingesta de datos de forma sostenida, no se recomienda aumentar el número de subprocesos masivos, en su lugar cree nodos adicionales y utilice el particionamiento para distribuir la carga de indexación entre los nodos. Como alternativa, considere la posibilidad de enviar lotes de inserción masiva en serie en lugar de en paralelo ya que esto actuará como un mecanismo de limitación natural que puede reducir las posibilidades de error debido a un desbordamiento de la cola de inserción masiva.

### Impacto de cambiar el intervalo de actualización del índice en el rendimiento de la ingesta de datos

El intervalo de actualización controla la velocidad a la que los datos introducidos están visibles para las consultas y las agregaciones, pero las actualizaciones frecuentes pueden afectar al rendimiento de las operaciones de ingesta de datos. El intervalo de actualización predeterminado es de 1 segundo. Puede deshabilitar completamente la actualización, pero esto podría no ser adecuado para su carga de trabajo. Puede probar diferentes intervalos y establecer un punto idóneo que equilibre el rendimiento de la ingesta y la necesidad de presentar información actualizada.

Como ejemplo del impacto, la prueba de rendimiento de la ingesta de datos se repitió en un clúster de Elasticsearch que consta de 7 particiones repartidos entre 3 nodos de datos. El índice tenía una única réplica. Cada nodo de datos se basaba en una máquina virtual D4 (28 GB de RAM, 8 núcleos de procesador) con almacenamiento efímero respaldado por SSD para guardar los datos. Cada prueba se ejecutó durante 1 hora.

En esta prueba, la frecuencia de actualización se estableció en el valor predeterminado de 1 segundo. La siguiente tabla muestra los tiempos de respuesta y el rendimiento de esta prueba en comparación con una ejecución independiente en la que la frecuencia de actualización se redujo a una vez cada 30 segundos.

| Frecuencia de actualización | N.º de muestras | Tiempo medio de respuesta: operaciones correctas (ms) | Rendimiento: operaciones correctas (operaciones/s) |
|--------------|------------|----------------------------------------------------|---------------------------------------------------|
| 1 segundo | 93755 | 460 | 26,0 |
| 30 segundos | 117758 | 365 | 32,7 |

En esta prueba, al reducir la frecuencia de actualización, el rendimiento mejoró un 18 % y el tiempo medio de respuesta se redujo un 21 %. Los siguientes gráficos generados con Marvel ilustran la razón principal de esta diferencia. En la siguiente ilustración se muestra la actividad de combinación de índice que se produjo con el intervalo de actualización establecido en 1 segundo y 30 segundos.

Las combinaciones de índices se realizan para evitar que el número de segmentos del índice en memoria sea demasiado numeroso. Un intervalo de actualización de un segundo genera un gran número de segmentos pequeños que tienen que combinarse con frecuencia, mientras que un intervalo de actualización de 30 segundos genera menos segmentos grandes que se pueden combinar de manera óptima.

![](media/guidance-elasticsearch/data-ingestion-image15.png)

***Actividad de combinación de índices para una frecuencia de actualización del índice de 1 segundo***

![](media/guidance-elasticsearch/data-ingestion-image16.png)

***Actividad de combinación de índices para una frecuencia de actualización del índice de 30 segundos***

### Impacto de las réplicas en el rendimiento de la ingesta de datos

Las réplicas son una característica esencial de cualquier clúster resistente y, si no se usan, se arriesga a perder información si se produce un error en un nodo. Sin embargo, las réplicas aumentan la cantidad de disco y E/S de red que se realizan y pueden ser perjudiciales para la velocidad a la que se introducen los datos. Por los motivos que se ha descrito anteriormente, puede resultar útil deshabilitar temporalmente las réplicas durante el tiempo que duran las operaciones de carga de datos a gran escala.

Las pruebas de rendimiento de la ingesta de datos se repitieron con tres configuraciones:

* Con un clúster sin réplicas.

* Con un clúster con una réplica.

* Con un clúster con dos réplicas.

En todos los casos, el clúster constaba de siete particiones repartidas entre tres nodos y se ejecutó en máquinas virtuales configuradas tal y como se describió en el conjunto de pruebas anterior. El índice de prueba usó un intervalo de actualización de 30 segundos.

En la tabla siguiente se resumen los tiempos de respuesta y el rendimiento de cada prueba con fines de comparación:

| Configuración | N.º de muestras | Tiempo medio de respuesta: operaciones correctas (ms) | Rendimiento: operaciones correctas (operaciones/s) | N.º de errores de ingesta de datos |
|---------------|------------|----------------------------------------------------|---------------------------------------------------|--------------------------|
| 0 réplicas | 215451 | 200 | 59,8 | 0 |
| 1 réplica | 117758 | 365 | 32,7 | 0 |
| 2 réplicas | 94218 | 453 | 26,1 | 194262 |


La disminución del rendimiento cuando el número de réplicas aumenta está clara, pero también debe observar el gran volumen de errores de ingesta de datos en la tercera prueba. Los mensajes generados por estos errores indicaron que se debían a que el desbordamiento de la cola de inserción masiva provocó que se rechazaran las solicitudes. Estos rechazos se produjeron muy rápidamente, de ahí su gran número.

> [AZURE.NOTE] Los resultados de la tercera prueba resaltan la importancia de usar una estrategia de reintentos inteligentes cuando se producen errores transitorios como este: una interrupción durante un breve período para permitir que la cola de inserción masiva se purgue antes de volver a intentar repetir la operación de inserción masiva.

Los siguientes conjuntos de gráficos comparan los tiempos de respuesta durante las pruebas. En cada caso, el primer gráfico muestra los tiempos de respuesta generales, mientras que el segundo gráfico detalla los tiempos de respuesta de las operaciones más rápidas (tenga en cuenta que la escala del primer gráfico es diez veces mayor que la del segundo). Puede ver cómo varía el perfil de los tiempos de respuesta en las tres pruebas.

Sin réplicas, la mayoría de las operaciones tardaron entre 75 ms y 750 ms, siendo los tiempos de respuesta más rápidos de unos 25 ms:

![](media/guidance-elasticsearch/data-ingestion-image17.png)

Con una réplica, el tiempo de respuesta operativa más frecuente estaba en el intervalo de 125 ms a 1250 ms. Las respuestas más rápidas tardaron aproximadamente 75 ms, aunque hubo menos de estas respuestas rápidas que en el caso sin réplicas. También hubo muchas más respuestas que tardaron bastante más que los casos más comunes, más de 1250 ms:

![](media/guidance-elasticsearch/data-ingestion-image18.png)

Con dos réplicas, el intervalo de tiempo de respuesta más lleno fue el de 200 ms a 1500 ms, pero hubo muchos menos resultados por debajo del intervalo mínimo que en la prueba con una réplica. Sin embargo, el patrón de resultados por encima del límite superior fue muy similar al de la prueba con una réplica. Esto suele deberse a los efectos del desbordamiento de la cola de inserción masiva (que supera una longitud de cola de 50 solicitudes). El trabajo adicional necesario para mantener dos réplicas hace que la cola se desborde con más frecuencia, lo que impide que las operaciones de ingesta tengan tiempos de respuesta excesivos; las operaciones se rechazan rápidamente en lugar de tardar más tiempo lo que puede producir posiblemente excepciones de tiempo de espera o afectar a la capacidad de respuesta de las aplicaciones cliente (este es el propósito del mecanismo de la cola de inserción masiva):

![](media/guidance-elasticsearch/data-ingestion-image19.png)

<span id="_The_Impact_of_1" class="anchor"><span id="_Impact_of_Increasing" class="anchor"></span></span>Con Marvel puede ver el efecto del número de réplicas en la cola de índice masivo. En la siguiente ilustración se muestran los datos de Marvel que representan cómo se llenó la cola de inserción masiva durante la prueba. La longitud media de la cola fue de unas 40 solicitudes, pero ráfagas periódicas provocaron su desbordamiento y, como resultado, se rechazaron las solicitudes:

![](media/guidance-elasticsearch/data-ingestion-image20.png)

***Tamaño de la cola de índice masivo y número de solicitudes rechazadas con dos réplicas.***

Compare esto con la siguiente ilustración, que muestra los resultados de una única réplica. El motor de Elasticsearch fue capaz de procesar las solicitudes con la rapidez suficiente para mantener la longitud media de la cola en unas 25, y en ningún momento la longitud de la cola superó las 50 solicitudes para que no se rechazara ningún trabajo.

![](media/guidance-elasticsearch/data-ingestion-image21.png)

***Tamaño de la cola de índice masivo y número de solicitudes rechazadas con una réplica.***

## Procedimientos recomendados para clientes que envían datos a Elasticsearch

Hay numerosos aspectos del rendimiento implicados, no solo internamente en el sistema sino también cómo las aplicaciones cliente usan el sistema. Elasticsearch ofrece muchas características que el proceso de ingesta de datos puede usar; generar identificadores únicos para los documentos, realizar análisis de documentos e incluso usar scripts para transformar los datos según se almacenan son solo algunos ejemplos. Sin embargo, estas funciones se agregan todas a la carga en el motor de Elasticsearch y, en muchos casos, las aplicaciones de cliente pueden realizarlas de forma más eficaz antes de la transmisión.

> [AZURE.NOTE] Esta lista de procedimientos recomendados está indicada principalmente para introducir nuevos datos en lugar de modificar datos existentes ya almacenados en un índice. Elasticsearch realiza las cargas de trabajo de ingesta como operaciones de anexión, mientras que las modificaciones de datos se realizan como operaciones de eliminación o anexión. Esto es porque los documentos en un índice son inmutables y, por lo tanto, para modificar un documento es necesario reemplazar todo el documento con una nueva versión. Puede realizar una solicitud HTTP PUT para sobrescribir un documento existente, o puede usar la API *update* de Elasticsearch que abstrae una consulta para recuperar un documento existente, combina los cambios y, a continuación, realiza una operación PUT para almacenar el nuevo documento.

Además, considere la posibilidad de implementar los siguientes procedimientos cuando corresponda:

* Deshabilite el análisis de texto para los campos de índice que no es necesario analizar. El análisis implica aplicar tokens al texto para que las consultas puedan buscar términos específicos. Sin embargo, puede ser una tarea con un uso intensivo de la CPU; sea selectivo. Si usa Elasticsearch para almacenar los datos de registro, puede ser útil aplicar tokens a los mensajes de registro detallado para permitir búsquedas complejas. Es probable que no se puedan aplicar tokens a otros campos, como aquellos que contienen identificadores o códigos de error, (¿con qué frecuencia solicitará los detalles de todos los mensajes cuyo código de error contenga un “3”, por ejemplo?) El código siguiente deshabilita el análisis de los campos *name* y *hostip* en el tipo *logs* del índice *systembase*:

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			...
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			...
		}
	}
	```

* Deshabilite el campo *\_all* de un índice si no es necesario. El campo *\_all* concatena los valores de los otros campos en el documento para su análisis e indexación. Es útil para realizar consultas que pueden coincidir con cualquier campo de un documento. Si se espera que los clientes coincidan con campos con nombre, habilitar *\_all* da lugar simplemente a sobrecarga de la CPU y del almacenamiento. En el ejemplo siguiente se muestra cómo deshabilitar el campo *\_all* para el tipo *logs* en el índice *systembase*.

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			"_all": {
				"enabled" : false
			},
			...,
		...
		}
	}
	```

    Tenga en cuenta que puede crear una versión selectiva de *\_all* que solo contenga información de campos específicos. Para más información, consulte [Disabling the \_all Field](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html#disabling-all-field) (Deshabilitación del campo \_all).

* Evite las asignaciones dinámicas en los índices. La asignación dinámica es una característica eficaz, pero agregar nuevos campos a un índice existente requiere coordinar los cambios en la estructura del índice en todos los nodos, lo que puede hacer que el índice se bloquee temporalmente. La asignación dinámica también puede provocar un aumento vertiginoso en el número de campos y el consiguiente volumen de metadatos de índice si no se usa con cuidado. A su vez, esto produce mayores requisitos de almacenamiento y de E/S tanto para la ingesta de datos como para realizar consultas. Estos dos problemas afectarán al rendimiento. Considere la posibilidad de deshabilitar la asignación dinámica y defina sus estructuras de índice explícitamente. Para más información, consulte [Dynamic Field Mapping](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#dynamic-field-mapping) (Asignación dinámica de campos).

* Comprenda cómo equilibrar la carga de trabajo para cumplir los requisitos en conflicto. Debe considerar siempre que la ingesta de datos puede tener un impacto significativo en el rendimiento de otras operaciones simultáneas, como los usuarios que realizan consultas. La ingesta de datos podría estar sujeta a ráfagas repentinas y, si el sistema intenta consumir todos los datos que llegan inmediatamente, el flujo de entrada puede provocar que las tasas de consulta se reduzcan al mínimo. Para intentar evitar esta situación, Elasticsearch regula la velocidad a la que se procesan las solicitudes de ingesta mediante la cola de inserción masiva (para más información, consulte la sección [Determinación de los factores de limitación: uso de CPU](#determining-limiting-factors-cpu-utilization)), pero este mecanismo realmente debe tratarse como último recurso; si el código de aplicación no está preparado para administrar las solicitudes rechazadas, se arriesga a perder datos. En su lugar, considere usar un patrón, como [Queue-Based Load Levelling](https://msdn.microsoft.com/library/dn589783.aspx) (Nivelado de la carga basado en la cola) para controlar la velocidad a la que se pasan los datos a Elasticsearch.

* Asegúrese de que el clúster tenga suficientes recursos para administrar la carga de trabajo, especialmente si los índices están configurados con varias réplicas.

* Use la API de inserción masiva para cargar grandes lotes de documentos. Dimensione las solicitudes masivas adecuadamente. A veces, los lotes más grandes no son mejores para el rendimiento y pueden hacer que los subprocesos de Elasticsearch y otros recursos se sobrecarguen, lo que retrasaría otras operaciones simultáneas. Los documentos de un lote de inserción masiva se mantienen en memoria en el nodo de coordinación mientras se realiza la operación; el tamaño físico de cada lote es más importante que el número de documentos. No hay ninguna regla absoluta en cuanto a lo que constituye el tamaño del lote ideal, aunque la documentación de Elasticsearch recomienda usar entre 5 MB y 15 MB como punto de partida para sus propias investigaciones. Realice pruebas de rendimiento para establecer el tamaño de lote óptimo para sus propios escenarios y carga de trabajo.

* Asegúrese de que las solicitudes de inserción masiva se distribuyan entre los nodos en lugar de dirigirlas a un solo nodo. Dirigir todas las solicitudes a un solo nodo puede provocar que se agote la memoria, porque cada solicitud de inserción masiva que se está procesando se almacena en memoria en el nodo. También puede aumentar la latencia de red mientras las solicitudes se redirigen a otros nodos.

* Elasticsearch usa un cuórum compuesto en su mayoría de nodos principales y de réplica al escribir datos. Una operación de escritura no se completa hasta que el cuórum notifica que el proceso se realizó correctamente. Este enfoque ayuda a garantizar que no se escribirán datos si la mayoría de los nodos no están disponibles debido a un evento de partición (error) de la red. El uso de un cuórum puede ralentizar el rendimiento de las operaciones de escritura. Para deshabilitar la escritura basada en cuórum, establezca el parámetro *consistency* en *one* al escribir datos. En el ejemplo siguiente se agrega un nuevo documento pero se completa en cuanto se completa la escritura en la partición principal.

	```http
	PUT /my_index/my_data/104?consistency=one
	{
		"name": "Bert",
		"age": 23
	}
	```

	Tenga en cuenta que, al igual que con la replicación asincrónica, desactivar la escritura basada en cuórum puede provocar incoherencias entre la partición principal y cada una de las réplicas.

* Cuando se usa el cuórum, Elasticsearch esperará si no hay suficientes nodos antes de determinar que se debe anular una operación de escritura porque no se puede alcanzar un cuórum. Este período de espera viene determinado por el parámetro de consulta de tiempo de espera (el valor predeterminado es 1 minuto). Puede modificar esta configuración mediante el parámetro de consulta de tiempo de espera. El ejemplo siguiente crea un nuevo documento y espera un máximo de 5 segundos a que el cuórum responda antes de anular:

	```http
	PUT /my_index/my_data/104?timeout=5s
	{
		"name": "Sid",
		"age": 27
	}
	```

	Elasticsearch también le permite usar sus propios números de versión [generados externamente](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types).

* Considere la posibilidad de deshabilitar el campo *\_source* de un índice. Este campo contiene una copia del documento JSON original que se usó al almacenar un documento. Guardar este campo implica un costo adicional de almacenamiento y de E/S de disco. Sin embargo, estos costos pueden ser marginales dependiendo de la estructura del documento, y también debe tener en cuenta que deshabilitar el campo *\_source* impide que un cliente pueda realizar las siguientes operaciones:

	* Usar la API Update para modificar un documento.
	* Realizar resaltados sobre la marcha al ejecutar las consultas.
	* Volver a indexar datos.
	* Depurar consultas y agregaciones mediante consulta del documento original.

	En el ejemplo siguiente se deshabilita el campo *\_source* para el tipo *logs* en el índice *systembase*.

  ```http
  PUT /systembase
  {
		"settings" : {
			...
		},
		"logs" : {
			"_source": {
				"enabled": false
			},
			...,
		...
		}
  }
  ```

## Directrices generales para realizar pruebas de rendimiento de la ingesta de datos con Elasticsearch

Los siguientes puntos resaltan algunos de los elementos que debe tener en cuenta al ejecutar pruebas de rendimiento con Elasticsearch y analizar los resultados.

* Las pruebas de rendimiento son necesariamente lentas y costosas. Como mínimo, recopile estadísticas que midan las velocidades de transferencia en disco y en la red, uso de CPU, tiempos de espera de la CPU y latencia del disco (si es posible). Esto le proporcionará información rápida sobre sus trabajos de pruebas con un buen retorno de inversión.

* Aproveche la funcionalidad de scripting proporcionada por la herramienta de pruebas de carga para recopilar métricas que de otra forma no estarían disponibles. Por ejemplo, Linux tiene una gran cantidad de estadísticas de rendimiento de buena calidad y confiables que puede recopilar con utilidades como *vmstat* e *iostat*. Puede usar scripts con JMeter para capturar estos datos como parte de un plan de pruebas.

* La ingeniería del rendimiento consiste principalmente en analizar estadísticas basadas en datos confiables y repetibles. No se detenga en métricas generales que no le proporcionarán la información necesaria. Familiarícese con los datos e incorpore la ingeniería de rendimiento al proceso de operaciones de desarrollo con un retorno rápido de la información. Mire siempre las estadísticas comparando las tendencias y resultados o configuraciones anteriores. Al hacerlo de forma regular, generará datos que comprenderá, serán repetibles con sus cargas de trabajo, y con ellos podrá evaluar los efectos de los cambios en la configuración y la implementación.

* Use una herramienta como Marvel para supervisar el rendimiento del clúster y de los nodos durante las pruebas para más información. JMeter puede ser eficaz para la captura de datos sin procesar para su análisis posterior, pero Marvel puede proporcionarle una idea en tiempo real sobre cómo marcha el rendimiento y las posibles causas de problemas técnicos y ralentizaciones. Además, muchas herramientas de pruebas de carga no proporcionan visibilidad sobre las métricas internas de Elasticsearch. Use y compare el rendimiento de la indexación, los recuentos de segmentos de combinación, las estadísticas de GC y los tiempos de limitación disponibles en las estadísticas de índice. Repita este análisis de forma periódica.

* Compare las estadísticas de la herramienta de pruebas de carga con las estadísticas del nodo en Marvel (tráfico de disco y de red, uso de CPU, uso de la memoria y uso del grupo de subprocesos) para entender el patrón de correlación entre las cifras indicadas por la infraestructura y las estadísticas específicas de Elasticsearch.

* Como regla general, considere *una partición un nodo* como línea de base para las pruebas de rendimiento y para evaluar los costos de la aplicación mediante la adición de nodos. Sin embargo, no dependa completamente de extrapolar el rendimiento basado en un número pequeño de nodos y particiones. Los costos de sincronización y comunicación del clúster pueden tener efectos imprevisibles cuanto mayor sea el número de nodos y particiones.

* Eche un vistazo a la asignación de particiones entre los nodos para comparar las estadísticas. Algunos nodos tendrán menos réplicas y particiones, lo que creará un desequilibrio en el uso de los recursos.

* Si va a realizar pruebas de carga, aumente el número de subprocesos que la herramienta de prueba usa para enviar trabajo al clúster hasta que se produzcan errores. Para obtener pruebas con un rendimiento sostenible, considere la posibilidad de mantener su nivel de prueba por debajo de un *límite* acordado. Si la tasa de errores supera el límite, los errores incurrirán en costos de recursos de back-end debido a su recuperación. En estas situaciones, el rendimiento disminuirá inevitablemente.

* Para simular cómo reacciona el sistema a una ráfaga de actividad inesperadamente grande, considere la posibilidad de ejecutar pruebas que generen una tasa de errores superior al límite. Esto le dará las cifras de rendimiento no solo en términos de capacidad, sino también el costo de recuperación.

* Use varios documentos para evaluar su perfil de rendimiento, y recicle los documentos siguiendo los patrones de carga de trabajo. Tenga en cuenta que a medida que se agreguen más documentos, el perfil del rendimiento puede cambiar.

* Tenga en cuenta los SLA de IOPS y los límites de las tasas de transferencia del almacenamiento que está usando. Diferentes tipos de almacenamiento (SSD, unidades de disco) tienen tasas de transferencia diferente.

* Recuerde que el rendimiento de la CPU puede caer no solo debido a la actividad de disco y de red, también porque las aplicaciones back-end pueden usan mecanismos de bloqueo y comunicación con el procesamiento distribuido que podría provocar una infrautilización del procesador.

* Ejecute las pruebas de rendimiento durante al menos dos horas (no unos minutos). La indexación puede afectar al rendimiento de maneras que podrían no ser visibles inmediatamente. Por ejemplo, las estadísticas de recopilación de elementos no utilizados JVM y las combinaciones de indexación pueden cambiar el perfil de rendimiento con el tiempo.

* Piense en cómo las actualizaciones del índice podrían afectar al rendimiento de la ingesta y limitar un clúster.

## Resumen

Es importante que comprenda cómo escalar la solución a medida que los volúmenes de datos y el número de solicitudes aumentan. Elasticsearch en Azure permite el escalado vertical y horizontal; se pueden ejecutar en máquinas virtuales más grandes con más recursos, y puede distribuir un clúster de Elasticsearch en una red de máquinas virtuales. La variedad de opciones puede resultar desconcertante; ¿es más rentable implementar un clúster en un gran número de máquinas virtuales pequeñas, en un clúster con un número pequeño de máquinas virtuales grandes, o algún punto intermedio? Además, ¿cuántas particiones debe contener cada índice y cuáles son las contrapartidas de la ingesta de datos frente al rendimiento de las consultas? La manera en que se distribuyen las particiones entre los nodos puede tener un impacto significativo en el rendimiento de la ingesta de datos. Usar más particiones puede reducir la cantidad de contención interna que se produce dentro de una partición, pero debe sopesar esta ventaja con la sobrecarga que usar varias particiones puede imponer en un clúster. Para responder a estas preguntas de forma eficaz, debe estar preparado para probar el sistema para determinar la estrategia más adecuada.

Para las cargas de trabajo de ingesta de datos, el rendimiento del subsistema de E/S de disco es un factor crítico. El uso de SSD puede mejorar el rendimiento porque reduce la latencia de disco de las operaciones de escritura. Si no necesita grandes cantidades de espacio en disco en un nodo, considere la posibilidad de usar máquinas virtuales estándar con almacenamiento efímero en lugar de tener más máquinas virtuales más costosas que admiten almacenamiento premium.

## Apéndice: Prueba de rendimiento de la ingesta de datos de carga masiva

Este apéndice describe la prueba de rendimiento realizada en el clúster de Elasticsearch. Para realizar las pruebas, se ejecutó JMeter en un conjunto diferente de máquinas virtuales. Se describen detalles de la configuración del entorno de prueba en [Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure][]. Para realizar sus propias pruebas, puede crear su propio plan de pruebas de JMeter manualmente, o puede usar los scripts de pruebas automatizadas disponibles por separado. Consulte [Running the Automated Elasticsearch Performance Tests][] (Ejecución de las pruebas de rendimiento automatizadas de Elasticsearch) para más información.

La carga de trabajo de ingesta de datos realizó una carga de documentos a gran escala mediante la API de inserción masiva. El propósito de este índice era simular un repositorio que recibe datos de registro que representan eventos del sistema para su posterior análisis y búsqueda. Cada documento se almacenó en un único índice denominado *systembase*, y tenía el tipo *logs*. Todos los documentos tenían el mismo esquema fijo que se describe en la siguiente tabla:

| Campo | Datatype | Ejemplo |
|---------------|---------------------|-----------------------------------|
| @timestamp | datetime | 2013-12-11T08:01:45.000Z |
| name | cadena | checkout.payment |
| message | cadena | Mensaje de solicitud entrante |
| severityCode | integer | 1 |
| severity | cadena | info |
| hostname | cadena | sixshot |
| hostip | string (dirección ip) | 10\.0.0.4 |
| pid | int | 123 |
| tid | int | 4325 |
| appId | string (uuid) | {00000000-0000-0000-000000000000} |
| appName | cadena | mytestapp |
| appVersion | cadena | 0\.1.0.1234 |
| type | int | 5 |
| subtype | int | 1 |
| correlationId | guid | {00000000-0000-0000-000000000000} |
| SO | cadena | Linux |
| osVersion | cadena | 4\.1.1 |
| parameters | [ ] | {key:value,key:value} |

Puede usar la siguiente solicitud para crear el índice. En muchas de las pruebas, las opciones de configuración *number\_of\_replicas*, *refresh\_interval* y *number\_of\_shards* varían con respecto a los valores que se muestran más abajo.

> [AZURE.IMPORTANT] El índice se quitó y se volvió a crear antes de cada ejecución de pruebas.

```http
PUT /systembase
{
	"settings" : {
		"number_of_replicas": 1,
		"refresh_interval": "30s",
		"number_of_shards": "5"
	},
	"logs" : {
		"properties" : {
			"@timestamp": {
			"type": "date",
			"index" : "not_analyzed"
			},
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"message": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"severityCode": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"severity": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostname": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"pid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"tid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"appId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appName": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appVersion": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"type": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"subtype": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"correlationId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"os": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"osVersion": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"parameters": {
				"type": "string",     
				"index" : "not_analyzed"
			}
		}
	}
}
```

Cada lote de inserción masiva constaba de 1.000 documentos. Cada documento se generó a partir de una combinación de valores aleatorios para los campos *severityCode*, *hostname*, *hostip*, *pid*, *tid*, *appName*, *appVersion*, *type*, *subtype* y *correlationId*, y una selección aleatoria de texto de un conjunto fijo de términos para los campos *name*, *message*, *severity*, *os*, *osVersion*, *parameters*, *data1* y *data2*. El número de instancias de aplicación de cliente usado para cargar los datos se seleccionó con cuidado para maximizar el volumen de entradas correctas. Las pruebas se ejecutaron durante dos horas para que el clúster pudiera liquidar y reducir la influencia de problemas temporales en los resultados generales. En este momento, algunas pruebas cargaron casi 1,5 millones de documentos.

Los datos se generaron dinámicamente con una muestra de solicitud JUnit personalizada que se agregó a un grupo de subprocesos en un plan de pruebas de JMeter. El código JUnit se creó mediante la plantilla Test Case de JUnit en el IDE de Eclipse.

> [AZURE.NOTE] Para obtener información sobre cómo crear una prueba JUnit para JMeter, consulte [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance][] (Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch).

El siguiente fragmento muestra el código de Java para probar Elasticsearch 1.7.3. Tenga en cuenta que la clase de prueba JUnit en este ejemplo se denomina *ElasticSearchLoadTest2*:

```java
/* Java */
package elasticsearchtest2;

	import static org.junit.Assert.*;

	import org.junit.*;

	import java.util.*;

	import java.io.*;

	import org.elasticsearch.action.bulk.*;
	import org.elasticsearch.common.transport.*;
	import org.elasticsearch.client.transport.*;
	import org.elasticsearch.common.settings.*;
	import org.elasticsearch.common.xcontent.*;

	public class ElasticsearchLoadTest2 {

		private String [] names={"checkout","order","search","payment"};
		private String [] messages={"Incoming request from code","incoming operation succeeded with code","Operation completed time","transaction performed"};
		private String [] severity={"info","warning","transaction","verbose"};
		private String [] apps={"4D24BD62-20BF-4D74-B6DC-31313ABADB82","5D24BD62-20BF-4D74-B6DC-31313ABADB82","6D24BD62-20BF-4D74-B6DC-31313ABADB82","7D24BD62-20BF-4D74-B6DC-31313ABADB82"};

		private String hostname = "";
		private String indexstr = "";
		private String typestr = "";
		private int port = 0;
		private int itemsPerInsert = 0;
		private String clustername = "";
		private static Random rand=new Random();

		@Before
		public void setUp() throws Exception {
		}

		public ElasticsearchLoadTest2(String paras) {
		* Paras is a string containing a set of comma separated values for:
			hostname
			indexstr
			typestr
			port
			clustername
			node
			itemsPerInsert
		*/

			// Note: No checking/validation is performed

			String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
			String[] items = paras.split(delims);

			hostname = items[0];
			indexstr = items[1];
			typestr = items[2];
			port = Integer.parseInt(items[3]);
			clustername = items[4];
			itemsPerInsert = Integer.parseInt(items[5]);

			if (itemsPerInsert == 0)
				itemsPerInsert = 1000;
			}

		@After
		public void tearDown() throws Exception {
		}

		@Test
		public void BulkBigInsertTest() throws IOException {

			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();
				Random random = new Random();
				char[] exmarks = new char[12000];
				Arrays.fill(exmarks, 'x');
				String dataString = new String(exmarks);

				for(int i=1; i &lt; itemsPerInsert; i++){
					random.nextInt(10);
					int host=random.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[random.nextInt(names.length)])
						.field("message", messages[random.nextInt(messages.length)])
						.field("severityCode", random.nextInt(10))
						.field("severity", severity[random.nextInt(severity.length)])
						.field("hostname", "Hostname"+host)
						.field("hostip", "10.1.0."+host)
						.field("pid",random.nextInt(10))
						.field("tid",random.nextInt(10))
						.field("appId", apps[random.nextInt(apps.length)])
						.field("appName", "application" + host)
						.field("appVersion", random.nextInt(5))
						.field("type", random.nextInt(6))
						.field("subtype", random.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
						.field("data1",dataString)
						.field("data2",dataString)
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}

		@Test
		public void BulkDataInsertTest() throws IOException {
			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();

				for(int i=1; i&lt; itemsPerInsert; i++){
					rand.nextInt(10);
					int host=rand.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[rand.nextInt(names.length)])
						.field("message", messages[rand.nextInt(messages.length)])
						.field("severityCode", rand.nextInt(10))
						.field("severity", severity[rand.nextInt(severity.length)])
						.field("hostname", "Hostname" + host)
						.field("hostip", "10.1.0."+host)
						.field("pid",rand.nextInt(10))
						.field("tid",rand.nextInt(10))
						.field("appId", apps[rand.nextInt(apps.length)])
						.field("appName", "application"+host)
						.field("appVersion", rand.nextInt(5))
						.field("type", rand.nextInt(6))
						.field("subtype", rand.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}
	}
```

Las matrices *String* privadas *names*, *messages*, *severity* y *apps* contienen un pequeño conjunto de valores del que los elementos se seleccionan aleatoriamente. Los elementos de datos restantes para cada documento se generan en tiempo de ejecución.

El constructor que toma el parámetro *String* se invoca desde JMeter y los valores pasados en la cadena se especifican como parte de la configuración de la muestra de solicitud JUnit. Para esta prueba JUnit, se espera que el parámetro *String* contenga la siguiente información:

* **Hostname**. Este es el nombre o la dirección IP del equilibrador de carga de Azure. El equilibrador de carga intenta distribuir la solicitud entre los nodos de datos del clúster. Si no usa un equilibrador de carga, puede especificar la dirección de un nodo del clúster, pero todas las solicitudes se dirigirán a ese nodo, lo que podría ocasionar que se convierta en un cuello de botella.

* **Indexstr**. Este es el nombre del índice al que se agregan los datos generados por la prueba JUnit. Si creó el índice tal y como se describió anteriormente, este valor debe ser *systembase*.

* **Typestr**. Este es el tipo del índice en el que se almacenan los datos. Si el índice se creó tal y como se describió anteriormente, este valor debe ser *logs*.

* **Port**. Este es el puerto al que conectarse en el host. En la mayoría de los casos se debe establecer en 9300 (el puerto usado por Elasticsearch para escuchar las solicitudes de la API de cliente; el puerto 9200 solo se usa para las solicitudes HTTP).

* **Clustername**. Nombre del clúster de Elasticsearch que contiene el índice.

* **ItemsPerInsert**. Parámetro numérico que indica el número de documentos que desea agregar en cada lote de inserción masiva. El tamaño del lote predeterminado es 1000.

Especifique los datos de la cadena del constructor en la página de la solicitud JUnit que se usa para configurar la muestra JUnit en JMeter. La imagen siguiente muestra un ejemplo:

![](media/guidance-elasticsearch/data-ingestion-image22.png)

Los métodos *BulkInsertTest* y *BigBulkInsertTest* realizan el trabajo real de generar y cargar los datos. Ambos métodos son muy similares; se conectan al clúster de Elasticsearch y, a continuación, crean un lote de documentos (según lo determinado por el parámetro *ItemsPerInsert* de la cadena de constructor). Los documentos se agregan al índice mediante la API de inserción masiva de Elasticsearch. La diferencia entre los dos métodos es que los campos de cadena *data1* y *data2* de cada documento se omiten de la carga en el método *BulkInsertTest*, pero se rellenan con cadenas de 12 000 caracteres en el método *BigBulkInsertTest*. Tenga en cuenta que para seleccionar cuál de estos métodos se ejecutará, se usa el cuadro *Test Method* (Método de prueba) en la página de solicitud JUnit en JMeter (resaltado en la ilustración anterior).

> [AZURE.NOTE] El código de ejemplo presentado aquí usa la biblioteca de cliente de transporte de Elasticsearch 1.7.3. Si está usando Elasticsearch 2.0.0 o versiones posteriores, debe usar la biblioteca adecuada para la versión seleccionada. Para más información sobre la biblioteca de cliente de transporte de Elasticsearch 2.0.0, consulte la página [Transport Client](https://www.elastic.co/guide/en/elasticsearch/client/java-api/2.0/transport-client.html) (Cliente de transporte) en el sitio web de Elasticsearch.

[Configuración de resistencia y recuperación en Elasticsearch en Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->