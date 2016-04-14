<properties
   pageTitle="Configuración de resistencia y recuperación en Elasticsearch en Azure"
   description="Consideraciones relacionadas con la resistencia y recuperación de Elasticsearch."
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
   
# Configuración de resistencia y recuperación en Elasticsearch en Azure

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

Una característica clave de Elasticsearch es el soporte que proporciona para aumentar la resistencia en caso de errores de nodo o eventos de partición de red. La replicación es la manera más obvia en la que se puede mejorar la resistencia de cualquier clúster, ya que permite a Elasticsearch garantizar que hay más de una copia disponible de cualquier elemento de datos en nodos diferentes en caso de no poder acceder a un nodo concreto. Si un nodo deja de estar disponible temporalmente, otros nodos que contienen réplicas de datos del nodo que falta pueden proporcionar estos datos hasta que se resuelva el problema. En caso de un problema a largo plazo, el nodo que falta se puede reemplazar con uno nuevo y Elasticsearch podrá restaurar los datos en el nuevo nodo a partir de las réplicas.

En este documento se resumen las opciones de resistencia y recuperación disponibles con Elasticsearch cuando se hospeda en Azure y se describen algunos aspectos importantes de un clúster de Elasticsearch que debe tener en cuenta para minimizar las posibilidades de pérdida de datos y los tiempos de recuperación de datos extendidos.

Este artículo también muestra algunas pruebas de ejemplo que se realizaron para mostrar los efectos de diferentes tipos de errores en un clúster de Elasticsearch y cómo responde el sistema cuando se recupera.

Un clúster de Elasticsearch usa réplicas para mantener la disponibilidad y mejorar el rendimiento de lectura. Las réplicas se deben almacenar en distintas máquinas virtuales de las particiones principales que se replican. La intención es que si la máquina virtual que hospeda un nodo de datos da error o deja de estar disponible, el sistema pueda continuar funcionando con las máquinas virtuales que contienen las réplicas.

## Uso de nodos maestros dedicados

Un nodo en un clúster de Elasticsearch se elige como nodo maestro. El propósito de este nodo es realizar operaciones de administración de clúster, como:

- Detectar los nodos erróneos y empezar a utilizar las réplicas,

- Reubicar las particiones para equilibrar la carga de trabajo del nodo y

- Recuperar las particiones cuando el nodo vuelva a estar en línea.

Debe considerar el uso de nodos maestros dedicados en clústeres importantes y asegurarse de que hay 3 nodos dedicados cuyo único rol es ser maestro. Esta configuración reduce la cantidad de trabajo intensivo de recursos que realizan estos nodos (ya que no almacenan datos ni controlan las consultas) y ayuda a mejorar la estabilidad del clúster. Solo se puede elegir uno de estos nodos, pero los demás contendrán una copia del estado del sistema y pueden asumir este papel en caso de error del nodo maestro seleccionado.

## Control de alta disponibilidad con Azure: dominios de actualización y dominios de error 

Varias máquinas virtuales distintas pueden compartir el mismo hardware físico. En un centro de datos de Azure, un único bastidor puede hospedar varias máquinas virtuales y todas ellas comparten la misma fuente de energía y un conmutador de red. Por tanto, un único error en el nivel de bastidor puede afectar a varias máquinas virtuales. Azure utiliza el concepto de dominios de error (FD) para probar y propagar este riesgo. Un dominio de error se corresponde aproximadamente con un grupo de máquinas virtuales que comparten el mismo bastidor. Para asegurarse de que un error en el nivel de bastidor no bloquea simultáneamente un nodo y los nodos que contienen todas sus réplicas, debe asegurarse de que las máquinas virtuales estén distribuidas entre varios dominios de error.

De forma similar, se pueden desactivar las máquinas virtuales mediante el [controlador de tejido de Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) para realizar las operaciones planeadas de mantenimiento y las actualizaciones del sistema operativo. Azure asigna máquinas virtuales a dominios de actualización (UD). Cuando se produce un evento de mantenimiento planeado, este solo afecta a las máquinas virtuales de un único dominio de actualización en un momento dado; las máquinas virtuales de otros dominios siguen ejecutándose hasta que las máquinas virtuales del dominio que se está actualizando vuelven a estar en línea. Por lo tanto, también debe asegurarse de que las máquinas virtuales que hospedan los nodos y sus réplicas pertenezcan a diferentes dominios de actualización siempre que sea posible.

> [AZURE.NOTE] Para más información sobre dominios de error y dominios de actualización, consulte [Administración de la disponibilidad de las máquinas virtuales][].

No se puede asignar explícitamente una máquina virtual a un dominio de error o de actualización específico ya que esta asignación se controla mediante Azure al crear las máquinas virtuales. Consulte el documento [Administración de la disponibilidad de las máquinas virtuales][] para más información. Sin embargo, puede especificar que se deben crear máquinas virtuales como parte de un conjunto de disponibilidad (AS). Las máquinas virtuales del mismo conjunto de disponibilidad se propagarán entre los dominios de actualización y los de error. Si crea máquinas virtuales manualmente, Azure asociará cada conjunto de disponibilidad con a dos dominios de error y cinco dominios de actualización y se asignarán máquinas a estos dominios, repitiendo esta operación a medida que se aprovisionan máquinas virtuales adicionales, como sigue:

- La primera máquina virtual aprovisionada en el conjunto de disponibilidad se colocará en el primer dominio de error (FD 0) y en el primer dominio de actualización (UD 0).
- La segunda máquina virtual aprovisionada en el conjunto de disponibilidad se colocará en el FD 1 y el UD 1.
- La tercera máquina virtual aprovisionada en el conjunto de disponibilidad se colocará en el FD 0 y el UD 2. La cuarta VM aprovisionada en el conjunto de disponibilidad se colocará en el FD 1 y el UD 3.
- La quinta máquina virtual aprovisionada en el conjunto de disponibilidad se colocará en el FD 0 y el UD 4.
- La sexta máquina virtual aprovisionada en el conjunto de disponibilidad se colocará en el FD 1 y el UD 0.
- La séptima máquina virtual aprovisionada en el conjunto de disponibilidad se colocará en el FD 0 y el UD 1.

> [AZURE.IMPORTANT] Si crea máquinas virtuales con Azure Resource Manager (ARM), cada conjunto de disponibilidad se puede asignar a un máximo de 3 dominios de error y 20 dominios de actualización. Se trata de un motivo convincente para utilizar ARM.

Por lo general, coloque todas las máquinas virtuales que tienen la misma finalidad en el mismo conjunto de disponibilidad, pero cree conjuntos diferentes para máquinas virtuales con funciones diferentes. Con Elasticsearch, esto significa que debe considerar la creación de conjuntos de disponibilidad diferentes al menos para:

- Las máquinas virtuales que hospedan nodos de datos.
- Las máquinas virtuales que hospedan los nodos cliente (si se utilizan).
- Las máquinas virtuales que hospedan los nodos maestros.

Además, debe asegurarse de que cada nodo de un clúster es consciente de los dominios de actualización y los de error a los que pertenece. Esta información permite garantizar que Elasticsearch no creará particiones y sus réplicas en los mismos dominios de error y de actualización, con lo que se minimizará el riesgo de desactivar una partición y sus réplicas al mismo tiempo. Puede configurar un nodo de Elasticsearch para que refleje la distribución del hardware del clúster mediante la configuración de [Shard Allocation Awareness](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness) (Reconocimiento de la asignación de particiones). Por ejemplo, podría definir un par de atributos de nodo personalizados denominados *faultDomain* y *updateDomain* en el archivo elasticsearch.yml, como sigue:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

En este caso, los atributos se establecen mediante los valores contenidos en variables de entorno *\\${FAULTDOMAIN}* y *\\${UPDATEDOMAIN}* cuando se inicia Elasticsearch. También debe agregar las siguientes entradas en el archivo Elasticsearch.yml para indicar que *faultDomain* y *updateDomain* son atributos de reconocimiento de asignación y especificar los conjuntos de valores aceptables para estos atributos:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Puede usar el reconocimiento de la asignación de particiones junto con [Shard Allocation Filtering](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) (Filtrado de asignaciones de particiones) para especificar qué nodos pueden hospedar particiones de cualquier índice dado.

Si necesita ampliar el número de dominios de error y dominios de actualización en un conjunto de disponibilidad, puede crear máquinas virtuales en conjuntos adicionales. Sin embargo, debe saber que los nodos de diferentes conjuntos de disponibilidad se pueden desactivar para realizar operaciones de mantenimiento al mismo tiempo. Asegúrese de que cada partición y al menos una de sus réplicas se encuentran en el mismo conjunto de disponibilidad.

> [AZURE.NOTE] Actualmente hay un límite de 100 máquinas virtuales por conjunto de disponibilidad. Para más información, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

### Copia de seguridad y restauración

El uso de réplicas no proporciona una protección completa frente a errores graves (por ejemplo, la eliminación accidental de todo el clúster). Asegúrese de que realiza una copia de seguridad de los datos de un clúster con regularidad y que tiene una estrategia probada y experimentada para restaurar el sistema a partir de estas copias de seguridad.

Utilice las [API de Snapshot and Restore](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) (Instantánea y restauración) de Elasticsearch para realizar las copias de seguridad y restaurar los índices. Las instantáneas se pueden guardar en un sistema de archivos compartido. Como alternativa, hay complementos disponibles para grabar las instantáneas en HDFS (con el complemento [HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) o en Almacenamiento de Azure (con el [complemento Servicios en la nube de Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

Debe considerar los siguientes puntos cuando seleccione el mecanismo de almacenamiento de instantáneas:

- Puede usar [Almacenamiento de archivos de Azure](https://azure.microsoft.com/services/storage/files/) para implementar un sistema de archivos compartido que sea accesible desde todos los nodos.

- Solo puede utilizar el complemento HDFS si ejecuta Elasticsearch junto con Hadoop.

- El complemento HDFS requiere que deshabilite al administrador de seguridad de Java que se ejecuta dentro de la instancia de Elasticsearch de la JVM.

- El complemento HDFS admite cualquier sistema de archivos compatible con HDFS siempre que se utilice la configuración correcta de Hadoop con Elasticsearch.

  
## Control de conectividad intermitente entre nodos

Problemas de red intermitentes, una máquina virtual que se reinicia tras un mantenimiento rutinario del centro de datos y otros eventos similares pueden provocar que los nodos sean inaccesibles temporalmente. En estas situaciones, en las que es probable que el evento sea de corta duración, la sobrecarga que supone reequilibrar las particiones aparece dos veces en rápida sucesión (una vez cuando se detecta el error y otra cuando el nodo se hace visible para el maestro) y puede convertirse en una sobrecarga considerable que afecte al rendimiento. Puede impedir que la inaccesibilidad temporal del nodo haga que el nodo maestro reequilibre el clúster estableciendo la propiedad *delayed\_timeout* para un índice o para todos los índices. En el ejemplo siguiente se establece el retraso en 5 minutos:

```http
PUT /_all/settings
{
	"settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

Para más información, consulte [Delaying allocation when a node leaves](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html) (Retraso de la asignación cuando un nodo abandona el grupo).

En una red que es propensa a interrupciones, también puede modificar los parámetros que configuran un maestro para detectar cuando otro nodo deja de estar disponible. Estos parámetros forman parte del módulo [Zen Discovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) (Descubrimiento Zen) proporcionado junto con Elasticsearch y los puede establecer en el archivo Elasticsearch.yml. Por ejemplo, el parámetro *discovery.zen.fd.ping.retries* especifica cuántas veces intentará un nodo maestro hacer ping a otro nodo del clúster antes de decidir que se ha producido un error. El valor predeterminado de este parámetro es 3, pero puede modificarlo como sigue:

```yaml
discovery.zen.fd.ping_retries: 6
```

## Control de la recuperación

Cuando se restaura la conectividad a un nodo después de un error, todas las particiones de ese nodo deben recuperarse para poder actualizarlas. De forma predeterminada, Elasticsearch recupera las particiones en el orden siguiente:

- Por fecha de creación del índice en sentido inverso. Se recuperan los índices más recientes antes que los más antiguos.

- Por el nombre del índice en sentido inverso. Aquellos índices que tienen nombres alfanuméricos más grandes que otros se restaurarán en primer lugar.

Si algunos índices son más importantes que otros pero no cumplen estos criterios, puede invalidar la prioridad de índices estableciendo la propiedad *index.priority*. Los índices que tienen un valor superior para esta propiedad, se recuperarán antes que los índices con un valor inferior:

```http
PUT low_priority_index
{
	"settings": {
		"index.priority": 1
	}
}

PUT high_priority_index
{
	"settings": {
		"index.priority": 10
	}
}
```

Para más información, consulte [Index Recovery Priorization](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization) (Prioridad en la recuperación de índices).

Puede supervisar el proceso de recuperación para uno o más índices mediante la API *\_recovery*:

```http
GET /high_priority_index/_recovery?pretty=true
```

Para más información, consulte [Indices Recovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery) (Recuperación de índices).

> [AZURE.NOTE] Un clúster con particiones que requieren recuperación tendrá un estado en *amarillo* para indicar que no todas las particiones están actualmente disponibles. Cuando todas las particiones estén disponibles, el estado del clúster debe volver a ser *verde*. Un clúster con un estado en *rojo* indica que una o más particiones faltan físicamente; puede que sea necesario restaurar datos desde una copia de seguridad.

## Prevención del síndrome de cerebro dividido 

Este síndrome puede ocurrir si se produce un error en las conexiones entre los nodos. Si no se puede acceder a un nodo maestro desde parte del clúster, se llevará a cabo una elección en el segmento de red que permanece disponible y otro nodo se convertirá en el maestro. En un clúster mal configurado, es posible que cada parte del clúster tenga diferentes nodos maestros lo cual genera incoherencias de datos o daños. Este fenómeno se conoce como *síndrome del cerebro dividido*.

Puede reducir las posibilidades de que aparezca este síndrome mediante la configuración de la propiedad *minimum\_master\_nodes* del módulo de detección en el archivo elasticsearch.yml. Esta propiedad especifica el número de nodos que debe estar disponible para habilitar la elección de un maestro. En el ejemplo siguiente se establece el valor de esta propiedad en 2:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Este valor debe establecerse en la mitad más uno del número de nodos que pueda cumplir el rol de maestro. Por ejemplo, si el clúster tiene 3 nodos maestros, *minimum\_master\_nodes* se debe establecer en 2 y si tiene 5 nodos maestros, *minimum\_master\_nodes* se debe establecer en 3. Idealmente, debe haber un número impar de nodos maestros.

**Nota:** Es posible que se produzca un síndrome de cerebro dividido si se inician varios nodos maestros en el mismo clúster simultáneamente. Aunque este caso es poco frecuente, puede evitarlo iniciando nodos en serie con un breve retraso (de 5 segundos) entre cada uno de ellos.

## Control de las actualizaciones graduales

Si va a realizar una actualización de software a nodos usted mismo (por ejemplo, la migración a una versión más reciente o la realización de una revisión), debe trabajar en nodos individuales lo cual requiere que los desconecte al tiempo que mantiene el resto del clúster disponible. En esta situación, considere la posibilidad de implementar el proceso siguiente:

Asegúrese de que la reasignación de particiones se retrasa lo suficiente para impedir que el nodo maestro elegido reequilibre las particiones a partir de un nodo que falta en el resto del clúster. De forma predeterminada, la reasignación de particiones se retrasa durante 1 minuto, pero puede aumentar la duración si es probable que un nodo no esté disponible durante un período más largo. El ejemplo siguiente aumenta el retraso en 5 minutos:

```http
PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

> [AZURE.IMPORTANT] También puede deshabilitar la reasignación de particiones completamente estableciendo la propiedad *cluster.routing.allocation.enable* del clúster en *Ninguna*. Sin embargo, debe evitar el uso de este enfoque si es posible que se creen nuevos índices mientras el nodo está sin conexión, ya que esto puede causar errores en la asignación de índices lo cual hará que el clúster tenga un estado en rojo.

Detenga Elasticsearch en el nodo que desea mantener. Si Elasticsearch se ejecuta como un servicio, puede detener el proceso de una manera controlada mediante un comando del sistema operativo. En el ejemplo siguiente se muestra cómo detener el servicio de Elasticsearch en un único nodo que se ejecuta en Ubuntu:

```bash
service elasticsearch stop
```

Como alternativa, puede utilizar la API de cierre directamente en el nodo:

```http
POST /_cluster/nodes/_local/_shutdown
```

Realice las operaciones de mantenimiento necesarias en el nodo y, a continuación, reinicie el nodo y espere a que vuelva a estar activo para unirse al clúster.

Vuelva a habilitar la asignación de particiones:

```http
PUT /_cluster/settings
{
	"transient": {
		"cluster.routing.allocation.enable": "all"
	}
}
```

> [AZURE.NOTE] Si necesita mantener más de un nodo, repita los pasos 2, 3 y 4 en cada nodo antes de volver a habilitar la asignación de particiones.

Si es posible, deje de indexar nuevos datos durante este proceso. Esto le ayudará a minimizar el tiempo de recuperación cuando los nodos vuelvan a estar en línea y vuelvan a unirse al clúster.

Tenga cuidado con las actualizaciones automatizadas para elementos como la JVM (sería ideal que deshabilitara las actualizaciones automáticas para estos elementos), especialmente si ejecuta Elasticsearch en Windows. El agente de actualización de Java puede descargar la versión más reciente automáticamente, pero puede que tenga que reiniciar Elasticsearch para que la actualización surta efecto. Esto puede provocar pérdida temporal no coordinada de nodos, según cómo esté configurado el agente de actualización de Java. Esto también puede resultar en instancias diferentes de Elasticsearch en el mismo clúster que ejecutan versiones distintas de la JVM, lo cual puede provocar problemas de compatibilidad.

## Pruebas y análisis de recuperación y resistencia de Elasticsearch

Esta sección describe una serie de pruebas que se realizaron para evaluar la resistencia y la recuperación de un clúster de Elasticsearch que incluye tres nodos maestros y tres nodos de datos.

Se probaron cuatro escenarios:

1.  Error de nodo y reinicio sin pérdida de datos. Se detiene un nodo de datos y se reinicia tras 5 minutos. Se había configurado Elasticsearch para que no se reasignaran las particiones que faltaban en este intervalo, por lo que no se incurrió en ninguna E/S adicional por mover particiones. Cuando se reinicia el nodo, el proceso de recuperación vuelve a actualizar las particiones de ese nodo.

2.  Error de nodo con pérdida de datos grave. Se detiene un nodo de datos y se borran los datos que contiene para simular un error de disco grave. Después, se reinicia el nodo (después de 5 minutos), que actúa de forma eficaz como reemplazo del nodo original. El proceso de recuperación requiere que se vuelvan a generar los datos que faltan para este nodo y puede suponer la reubicación de las particiones que se mantienen en otros nodos.

3.  Error de nodo y reinicio sin pérdida de datos, pero con reasignación de particiones. Se detiene un nodo de datos y se reasignan las particiones que contiene a otros nodos. Después, se reinicia el nodo y se hacen más reasignaciones para volver a equilibrar el clúster.

4.  Actualizaciones graduales. Se detiene cada nodo del clúster y se reinicia tras un breve intervalo para simular el reinicio de las máquinas después de una actualización de software. Solo se detiene un nodo en cualquier momento dado. Las particiones no se reasignan mientras un nodo esté inactivo.

Cada escenario estaba sujeto a la misma carga de trabajo que incluía una mezcla de tareas de recopilación de datos, agregaciones y consultas de filtro, cuando los nodos se desconectaron y recuperaron. Las operaciones de inserción masiva de la carga de trabajo almacenaron cada una 1000 documentos y se realizaron mediante un índice mientras que las agregaciones y las consultas de filtro utilizaron un índice independiente que contenía varios millones de documentos. Esto se realizó para habilitar el rendimiento de las consultas que se debían evaluar por separado a partir de las inserciones masivas. Cada índice incluía de cinco particiones y una réplica.

Las secciones siguientes resumen los resultados de estas pruebas, teniendo en cuenta cualquier degradación del rendimiento mientras un nodo está sin conexión o se está recuperando y los errores que se han notificado. Los resultados se muestran de forma gráfica, resaltando los puntos en el que uno o más nodos faltan y estimando el tiempo necesario que el sistema necesita para recuperarse totalmente y lograr un nivel de rendimiento similar al que había antes de que los nodos que se desconectaran.

> [AZURE.NOTE] Las herramientas de ejecución de pruebas utilizadas para realizar estas pruebas están disponible en línea. Puede adaptar y utilizar estas herramientas para comprobar la resistencia y la capacidad de recuperación de sus propias configuraciones de clúster. Para más información, consulte [Ejecución de pruebas automatizadas de resistencia de Elasticsearch][].

## Error de nodo y reinicio sin pérdida de datos: resultados

<!-- TODO; reformat this pdf for display inline -->

Los resultados de esta prueba se muestran en el archivo [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario1.pdf). Los gráficos muestran el perfil de rendimiento de la carga de trabajo y los recursos físicos para cada nodo del clúster. La parte inicial de los gráficos muestra que el sistema se ejecuta normalmente durante 20 minutos aproximadamente, momento en que se apaga el nodo 0 durante 5 minutos antes de volver a reiniciarse. Se muestran las estadísticas para un período de 20 minutos adicionales; el sistema tarda aproximadamente 10 minutos en recuperarse y estabilizarse. Esto se ilustra mediante las tasas de transacción y los tiempos de respuesta para las diferentes cargas de trabajo.

Tenga en cuenta los siguientes puntos:

- Durante la prueba, no se notificó ningún error. No se ha perdido ningún dato y todas las operaciones se completaron correctamente.

- La tasa de transacción para los tres tipos de operación (inserción masiva, consulta de funciones agregadas y consulta de filtro) se redujo y los tiempos de respuesta promedio aumentaron mientras el nodo 0 estaba sin conexión.

- Durante el período de recuperación, las tasas de transacción y los tiempos de respuesta de las operaciones de consulta de funciones agregadas y consulta de filtro se restauraron gradualmente. El rendimiento de la operación de inserción masiva se recuperó durante un breve espacio de tiempo antes de disminuir. Sin embargo, es probable que esto se deba al volumen de datos que causó el crecimiento del índice utilizado por la inserción masiva y puede observarse cómo las tasas de transacción para esta operación se ralentizaron antes incluso de que se desconectara el nodo 0.

- El gráfico de utilización de CPU del nodo 0 muestra una actividad reducida durante la fase de recuperación, lo cual se debe al incremento de la actividad del disco y de la red causado por el mecanismo de recuperación. El nodo tiene para ponerse al día con los datos que se han perdido mientras estaba sin conexión y actualizar las particiones que contiene.

- Las particiones de los índices no se distribuyen exactamente igual entre todos los nodos. Hay dos índices que incluyen 5 particiones y 1 réplica cada uno lo cual supone un total de 20 particiones. Dos nodos, por tanto, contendrán 6 particiones mientras que los otros dos tendrán 7 cada uno. Esto es evidente en los gráficos de uso de CPU durante el período inicial de 20 minutos. El nodo 0 está menos ocupado que los otros dos. Una vez completada la recuperación, parece producirse algún cambio ya que el nodo 2 se convierte en el nodo ligeramente más cargado.

    
## Error de nodo con pérdida de datos grave: resultados

<!-- TODO; reformat this pdf for display inline -->

Los resultados de esta prueba se muestran en el archivo [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario2.pdf). Al igual que en la primera prueba, la parte inicial de los gráficos muestra que el sistema se ejecuta normalmente durante 20 minutos aproximadamente, momento en que se apaga el nodo 0 durante 5 minutos. Durante este intervalo, se quitan los datos de Elasticsearch de este nodo, simulando una pérdida grave de datos, antes de volver a reiniciarlo. La recuperación completa tarda aparentemente de 12 a 15 minutos antes de que se restauren los niveles de rendimiento observados antes de la prueba.

Tenga en cuenta los siguientes puntos:

- Durante la prueba, no se notificó ningún error. No se ha perdido ningún dato y todas las operaciones se completaron correctamente.

- La tasa de transacción para los tres tipos de operación (inserción masiva, consulta de funciones agregadas y consulta de filtro) se redujo y los tiempos de respuesta promedio aumentaron mientras el nodo 0 estaba sin conexión. En este momento, el perfil de rendimiento de la prueba es similar al del primer escenario. Esto no es sorprendente ya que, hasta este punto, los escenarios son idénticos.

- Durante el período de recuperación, se han restaurado las tasas de transacción y los tiempos de respuesta, aunque durante este tiempo hubo mucha más volatilidad en las cifras. Esto se debe probablemente al trabajo adicional que están realizando los nodos del clúster para proporcionar los datos para restaurar las particiones que faltan. Este trabajo adicional es evidente en los gráficos de uso de CPU, actividad del disco y actividad de la red.

- El gráfico de uso de CPU para los nodos 0 y 1 muestra actividad reducida durante la fase de recuperación, lo cual se debe a la mayor actividad de disco y de red provocada por el proceso de recuperación. En el primer escenario, solo el nodo que se estaba recuperando mostraba este comportamiento, sin embargo, en este escenario parece probable que la mayoría de los datos que faltan del nodo 0 se restaurarán desde el nodo 1.

- La actividad de E/S del nodo 0 realmente se ha reducido en comparación con el primer escenario. Esto puede deberse a la eficiencia de E/S que supone el copiar simplemente los datos de una partición completa, en lugar de copiar la serie de solicitudes de E/S más pequeñas necesarias para actualizar una partición existente.

- La actividad de red para los tres nodos indica ráfagas de actividad a medida que se transmiten y reciben los datos entre los nodos. En el escenario 1, solo el nodo 0 mostraba tanta actividad de red, pero esta actividad parecía mantenerse durante un período más largo. De nuevo, esta diferencia se puede deber a la eficiencia que supone transmitir todos los datos de una partición como una única solicitud en lugar de una serie de solicitudes menores recibidas durante la recuperación de una partición.

## Error de nodo y reinicio con reasignación de particiones: resultados

<!-- TODO; reformat this pdf for display inline -->

Los resultados de esta prueba se muestran en el archivo [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario3.pdf). Al igual que en la primera prueba, la parte inicial de los gráficos muestra que el sistema se ejecuta normalmente durante 20 minutos aproximadamente, momento en que se apaga el nodo 0 durante 5 minutos. En este momento, el clúster de Elasticsearch intenta volver a crear las particiones que faltan y reequilibrar las particiones entre los nodos restantes. Después de 5 minutos el nodo 0 vuelve a estar en línea y una vez más el clúster tiene que reequilibrar las particiones. El rendimiento se restaura después de 12 a 15 minutos.

Tenga en cuenta los siguientes puntos:

- Durante la prueba, no se notificó ningún error. No se ha perdido ningún dato y todas las operaciones se completaron correctamente.

- La tasa de transacción para los tres tipos de operación (inserción masiva, consulta de funciones agregadas y consulta de filtro) se redujo y los tiempos de respuesta promedio aumentaron significativamente mientras el nodo 0 estaba sin conexión en comparación con las dos pruebas anteriores. Esto se debe al aumento de actividad del clúster al volver a crear las particiones que faltan y reequilibrar el clúster tal como lo demuestran unas mayores cifras de actividad de disco y de red de los nodos 1 y 2 durante este período.

- Durante el período posterior a que el nodo 0 vuelva a estar en línea, los tiempos de respuesta y las tasas de transacción permanecen volátiles.

- Los gráficos de uso de CPU y actividad del disco para el nodo 0 muestran una acción inicial muy reducida durante la fase de recuperación. Esto es porque en este momento, el nodo 0 no está proporcionando ningún dato. Tras un período de aproximadamente 5 minutos, el nodo realiza ráfagas de acción tal y como lo demuestra el aumento repentino de la actividad de CPU, disco y red. Esto se debe probablemente a la redistribución de particiones entre los nodos realizada por el clúster. El nodo 0 muestra, a continuación, la actividad normal.
  
## Actualizaciones graduales: resultados

<!-- TODO; reformat this pdf for display inline -->

Los resultados de esta prueba, que se pueden ver en el archivo [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario4.pdf), muestran cómo se desconecta cada nodo y, a continuación, vuelven a conectarse sucesivamente. Cada nodo se apaga durante 5 minutos antes de reiniciarse, momento en el cual se detiene el siguiente nodo de la secuencia.

Tenga en cuenta los siguientes puntos:

- Mientras se reanuda el ciclo de cada nodo, el rendimiento en términos de procesamiento y tiempos de respuesta se mantiene razonablemente estable.

- La actividad de disco aumenta para cada nodo durante un breve espacio de tiempo cuando vuelve a estar en línea. Esto se debe posiblemente al proceso de recuperación en el que se actualizan los cambios que se han producido mientras el nodo estaba desconectado.

- Cuando un nodo se desconecta, se producen picos de actividad de red en los nodos restantes. También se producen picos cuando se reinicia un nodo.

- Después de que se recicla el último nodo, el sistema entra en un período de volatilidad significativa. Esto se debe posiblemente a que el proceso de recuperación necesita sincronizar los cambios en todos los nodos y asegurarse de que todas las réplicas y sus particiones correspondientes son coherentes. En un determinado momento, este esfuerzo provoca que las operaciones de inserción masiva entren en un tiempo de espera y se produzcan errores. Los errores notificados para cada caso fueron:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Una experimentación posterior demostró que introducir un retraso de unos pocos minutos entre los ciclos de cada nodo eliminaba este error, por lo que probablemente se debía a la contención entre el proceso de recuperación que intentaba restaurar varios nodos simultáneamente y las operaciones de inserción masiva que intentaban almacenar miles de documentos nuevos.


## Resumen

Las pruebas realizadas indican que:

- Elasticsearch era altamente resistente a los modos de error más comunes que se pueden producir en un clúster.

- Elasticsearch se puede recuperar rápidamente si un clúster bien diseñado sufre una pérdida de datos grave en un nodo. Esto puede ocurrir si configura Elasticsearch para que guarde datos en un almacenamiento efímero y se vuelve a aprovisionar el nodo después del reinicio. Estos resultados muestran que incluso en este caso, los riesgos de usar un almacenamiento efímero son probablemente menores que las ventajas de rendimiento que este tipo de almacenamiento proporciona.

- En los tres primeros casos, no se produjeron errores durante las cargas de trabajo provocadas por las operaciones simultáneas de inserción masiva, agregado de funciones y consultas de filtro durante el tiempo que un nodo estaba desconectado y volvía a recuperarse.

- Solo en el escenario 4 se indica una posible pérdida de datos y esta pérdida solo afectaba a los datos nuevos que se agregan. Es conveniente en aplicaciones que realizan la recopilación de datos mitigar esta posibilidad al reintentar las operaciones de inserción con errores ya que el tipo de error notificado es posiblemente transitorio.

- Los resultados de la prueba 4 también muestran que si está realizando un mantenimiento planeado de los nodos de un clúster, el rendimiento se beneficiará si permite un período de varios minutos entre el ciclo de un nodo y el del siguiente. En una situación no planeada (por ejemplo, cuando el centro de datos recicla nodos después de realizar una actualización del sistema operativo), tendrá un menor control sobre cómo y cuándo se desconectan y se reinician los nodos. La contención que aparece cuando Elasticsearch intenta recuperar el estado del clúster después de las interrupciones secuenciales de los nodos puede producir errores y tiempos de espera.

[Administración de la disponibilidad de las máquinas virtuales]: ../articles/virtual-machines/virtual-machines-manage-availability.md
[Ejecución de pruebas automatizadas de resistencia de Elasticsearch]: guidance-elasticsearch-running-automated-resilience-tests.md

<!---HONumber=AcomDC_0224_2016-->