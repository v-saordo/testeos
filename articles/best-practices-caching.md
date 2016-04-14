<properties
   pageTitle="Orientación sobre el almacenamiento en caché | Microsoft Azure"
   description="Orientación sobre el almacenamiento en caché para mejorar el rendimiento y la escalabilidad."
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
   ms.date="12/18/2015"
   ms.author="masashin"/>

# Guía sobre el almacenamiento en caché

![](media/best-practices-caching/pnp-logo.png)

El almacenamiento en caché es una técnica común que tiene como objetivo la mejora del rendimiento y de la escalabilidad de un sistema copiando temporalmente los datos a los que se accede con frecuencia para almacenamiento rápido situado cerca de la aplicación. Si este almacenamiento de datos rápido se encuentra más cerca de la aplicación que del primer origen, el almacenamiento en caché puede mejorar de manera significativa los tiempos de respuesta para las aplicaciones cliente sirviendo datos con mayor rapidez. El almacenamiento en caché es más eficaz cuando una instancia de cliente lee repetidamente los mismos datos, especialmente si los datos permanecen relativamente estáticos y el almacén de datos original es lento en relación con la velocidad de la memoria caché, está sujeto a un alto nivel de contención o se encuentra lejos cuando la latencia de red puede provocar que el acceso sea lento.

## Almacenamiento en caché en aplicaciones distribuidas

Las aplicaciones distribuidas normalmente implementan ambas de las siguientes estrategias, o una de ellas, al almacenar datos en caché:

- Mediante una caché privada, donde los datos se guardan localmente en el equipo que ejecuta una instancia de una aplicación o un servicio.
- Mediante una caché compartida, que actúa como un origen común al que se puede acceder mediante varios procesos y/o máquinas.

En ambos casos, el almacenamiento en caché podría realizarse en el lado del cliente (por el proceso que proporciona la interfaz de usuario para un sistema, como una aplicación de escritorio o de explorador web) y en el lado del servidor (por el proceso que proporciona los servicios empresariales que se ejecutan de forma remota).

### Almacenamiento en caché privado

El tipo más básico de caché es un almacén en memoria, guardado en el espacio de direcciones de un único proceso y al que se accede directamente por el código que se ejecuta en ese proceso. Este tipo de caché es muy rápido de acceder y puede proporcionar una estrategia muy eficaz para almacenar cantidades modestas de datos estáticos ya que el tamaño de una memoria caché suele estar restringido por la cantidad de memoria disponible en la máquina que hospeda el proceso. Si necesita almacenar en caché más información de lo que es posible físicamente en la memoria, puede escribir datos en caché en el sistema de archivos local. Esto será necesariamente más lento que acceder a datos retenidos en memoria, pero aún así debería ser más rápido y más confiable que recuperar datos a través de una red.

Si tiene varias instancias de una aplicación que usa este modelo de ejecución simultánea, cada instancia de la aplicación tendrá su propia caché independiente con su propia copia de datos.

Debería pensar en una memoria caché como una instantánea de los datos originales en algún momento del pasado. Si estos datos no son estáticos, es probable que las diferentes instancias de aplicación contengan versiones diferentes de los datos en sus cachés. Por lo tanto, la misma consulta realizada por estas instancias podría devolver resultados diferentes, como se muestra en la Figura 1.

![Uso de una caché en memoria en instancias diferentes de una aplicación](media/best-practices-caching/Figure1.png)

_Figura 1: Uso de una memoria caché en instancias diferentes de una aplicación_

### Almacenamiento en caché compartido

El uso de una memoria caché compartida puede ayudar a aliviar la preocupación de que los datos puedan ser diferentes en cada caché, como podría ocurrir con el almacenamiento en caché en memoria. El almacenamiento en caché compartido garantiza que diferentes instancias de aplicación vean la misma vista de datos en caché ubicando la memoria caché en una ubicación independiente, normalmente hospedada como parte de un servicio independiente, como se muestra en la Figura 2.

![Uso de una caché compartida\_](media/best-practices-caching/Figure2.png)

_Figura 2: Uso de una memoria caché compartida_

Una ventaja importante de usar el enfoque de almacenamiento en caché compartido es la escalabilidad que puede ayudar a proporcionar. Muchos servicios de memoria caché compartida se implementan mediante un clúster de servidores y utilizan software que distribuye los datos en el clúster de forma transparente. Una instancia de aplicación simplemente envía una solicitud al servicio de caché y la infraestructura subyacente es responsable de determinar la ubicación de los datos en caché en el clúster. Puede escalar fácilmente la memoria caché al agregar más servidores.

Las desventajas del enfoque de almacenamiento en caché compartido son que la memoria caché es de acceso más lento porque ya no se conserva localmente para cada instancia de aplicación y el requisito de implementar un servicio de caché independiente puede agregar complejidad a la solución.

## Consideraciones para usar el almacenamiento en caché

En las secciones siguientes se describe con más detalle las consideraciones para diseñar y usar una memoria caché.

### ¿Cuando se deberían almacenar los datos en caché?

El almacenamiento en caché puede mejorar considerablemente el rendimiento, la escalabilidad y la disponibilidad. Cuanto más datos tenga, y mayor sea el número de usuarios que necesitan acceder a estos datos, mayores serán las ventajas del almacenamiento en caché al reducir la latencia y la contención asociadas a la administración de grandes volúmenes de solicitudes simultáneas en el almacén de datos originales. Por ejemplo, una base de datos puede admitir un número limitado de conexiones simultáneas, pero la recuperación de datos de una memoria caché compartida en lugar de la base de datos subyacente permite a una aplicación cliente acceder a estos datos, incluso si el número de conexiones disponibles está actualmente agotado. Además, si la base de datos deja de estar disponible, es posible que las aplicaciones cliente puedan continuar con los datos contenidos en la memoria caché.

Debería considerar el almacenamiento en caché de los datos que se leen con frecuencia pero que se modifican con poca frecuencia (los datos tienen una proporción alta de operaciones de lectura en comparación con las operaciones de escritura). Sin embargo, no debería usar la memoria caché como el almacén autoritativo de la información crítica; debe asegurarse de que todos los cambios que su aplicación no puede permitirse perder se guardan siempre en un almacén de datos persistentes. De esta manera, si la memoria caché no está disponible, su aplicación puede continuar funcionando usando el almacén de datos y no perderá información importante.

### Tipos de datos y estrategias de rellenado de caché

La clave para usar de forma eficaz una memoria caché reside en la determinación de los datos más adecuados para la caché y de su almacenamiento en caché en el momento adecuado. Los datos se pueden agregar a la memoria caché a petición la primera vez que los recupera una aplicación, de manera que la aplicación necesita capturar los datos solo una vez del almacén de datos y los accesos posteriores se pueden satisfacer mediante el uso de la memoria caché.

O bien, una memoria caché puede rellenarse total o parcialmente con datos de antemano, normalmente al iniciar la aplicación (un método conocido como propagación). Sin embargo, es posible que no sea aconsejable implementar la propagación para una caché de gran tamaño ya que este enfoque puede imponer una carga alta y repentina en el almacén de datos original cuando la aplicación comienza a ejecutarse.

A menudo, un análisis de los patrones de uso puede ayudarle a decidir si desea rellenar previamente total o parcialmente una memoria caché así como a elegir los datos que se deben almacenar en caché. Por ejemplo, probablemente sería útil propagar la memoria caché con los datos de perfil de usuario estáticos para los clientes que usan la aplicación con regularidad (quizá todos los días), pero no para los clientes que usan la aplicación solo una vez a la semana.

El almacenamiento en caché funciona normalmente bien con datos inmutables o que cambian con poca frecuencia. Entre los ejemplos se incluyen la información de referencia como la información de precios y productos en una aplicación de comercio electrónico, o recursos estáticos compartidos que son costosos de construir. Algunos de estos datos, o todos ellos, pueden cargarse en la memoria caché al iniciarse la aplicación para minimizar la demanda de recursos y mejorar el rendimiento. Puede que también sea adecuado tener un proceso en segundo plano que actualice periódicamente la referencia de datos en la memoria caché para asegurarse de que está actualizada, o actualiza la caché cuando cambian los datos de referencia.

El almacenamiento en caché puede ser menos útil para los datos dinámicos, aunque hay algunas excepciones a esta consideración (vea la sección Almacenamiento de datos altamente dinámicos que aparece posteriormente en esta orientación para obtener más información). Cuando los datos originales cambian con frecuencia, la información almacenada en caché puede llegar a ser obsoleta muy rápidamente o la sobrecarga del mantenimiento de la memoria caché sincronizada con el almacén de datos original reduce la eficacia del almacenamiento en caché. Tenga en cuenta que una memoria caché no tiene que incluir los datos completos para una entidad. Por ejemplo, si un elemento de datos representa un objeto con múltiples valores como un cliente de banco con un nombre, una dirección y un saldo de cuenta, algunos de estos elementos pueden permanecer estáticos (el nombre y la dirección), mientras que otros (como el saldo de la cuenta) pueden ser más dinámicos. En estas situaciones, podría ser útil almacenar en caché las partes estáticas de los datos y recuperar solo (o calcular) la información restante como y cuando sea necesario.

El análisis de uso y las pruebas de rendimiento deben llevarse a cabo para determinar si es adecuado el rellenado previo o la carga a petición de la memoria caché, o una combinación de ambos. La decisión debe basarse en una combinación de la volatilidad y el patrón de uso de los datos. El análisis de rendimiento y el uso de la memoria caché es especialmente importante en las aplicaciones que encuentran cargas pesadas y deben ser altamente escalables. Por ejemplo, en escenarios altamente escalables, puede tener sentido inicializar la memoria caché para reducir la carga en el almacén de datos en las horas punta.

El almacenamiento en caché también se puede usar para evitar repetir cálculos mientras se ejecuta la aplicación. Si una operación transforma datos o realiza un cálculo complicado, puede guardar los resultados de la operación en la memoria caché. Si se requiere el mismo cálculo posteriormente, la aplicación puede recuperar simplemente los resultados de la memoria caché.

Una aplicación puede modificar los datos incluidos en una caché pero debería considerar la caché como un almacén de datos transitorios que podrían desaparecer en cualquier momento. No almacene datos valiosos solo en la memoria caché; asegúrese de mantener también la información en el almacén de datos original. De este modo, si la caché debe estar disponible, se minimiza la posibilidad de perder datos.

### Almacenamiento de datos altamente dinámicos

El almacenamiento de información que cambia rápidamente en un almacén de datos persistentes puede imponer una sobrecarga en el sistema. Por ejemplo, un dispositivo que informa continuamente del estado o de alguna otra medida. Si una aplicación decide no almacenar en caché estos datos según la base de que la información almacenada en caché casi siempre quedará obsoleta, la misma consideración podría ser verdadera al almacenar y recuperar esta información desde el almacén de datos; en el tiempo necesario para guardar y recuperar estos datos, podría haber cambiado. En una situación como ésta, considere las ventajas de almacenar la información dinámica directamente en la memoria caché en lugar del almacén de datos persistente. Si los datos son no críticos y no requieren que se audite, no importa si se pierde el cambio ocasional.

### Administración de la expiración de los datos en una memoria caché

En la mayoría de los casos, los datos almacenados en una memoria caché son una copia de los datos que se encuentran en el almacén de datos original. Los datos del almacén de datos original pueden cambiar después de haberse almacenado en caché, lo que hace que los datos almacenados en memoria caché se vuelvan obsoletos. Muchos sistemas de almacenamiento en caché le habilitan para configurar la memoria caché para expirar datos y reducir el período para el que los datos pueden estar desfasados.

Cuando los datos almacenados en caché expiran, se quitan de la memoria caché y la aplicación debe recuperar los datos del almacén de datos original (puede volver a colocar la información recién capturada en la caché). Puede establecer una directiva de expiración predeterminada al configurar la memoria caché. En muchos servicios de caché también puede estipular el período de expiración para objetos individuales cuando los almacena mediante programación en la memoria caché (algunas memorias caché le permiten especificar el período de expiración como un valor absoluto o como un valor de variable que hace que se quite el elemento de la memoria caché si no es accesible dentro del tiempo especificado. Esta configuración invalida cualquier directiva de expiración de toda la memoria caché, pero solo para los objetos especificados.

> [AZURE.NOTE] Considere detenidamente el período de expiración de la memoria caché y los objetos que contiene. Si hace que sea demasiado breve, los objetos expirarán con demasiada rapidez y reducirá las ventajas del uso de la memoria caché. Si hace que el periodo sea demasiado largo, se arriesgan a que los datos se vuelvan obsoletos.

También es posible que se pueda rellenar la memoria caché si se permite que los datos permanezcan residentes durante mucho tiempo. En este caso, las solicitudes para agregar nuevos elementos a la memoria caché podrían provocar que se forzara la eliminación de algunos elementos en un proceso conocido como expulsión. Los servicios de caché normalmente expulsan los datos menos usados recientemente (LRU), pero normalmente puede invalidar esta directiva y evitar que se expulsen los elementos. Sin embargo, si adopta este enfoque se arriesga a que la memoria caché supere la memoria caché que tiene disponible y se producirá un error con una excepción de una aplicación que intenta agregar un elemento a la caché.

Algunas implementaciones de almacenamiento en caché pueden proporcionar directivas de expulsión adicionales. Suelen incluir la directiva de más usados recientemente (en la expectativa de que los datos no se requerirán de nuevo), la directiva de primero en entrar primero en salir (los datos más antiguos se expulsan primero), o la eliminación explícita basándose en un evento desencadenado (como los datos que se están modificando).

### Invalidación de datos en una caché del lado cliente

Los datos almacenados en una caché del lado cliente se suelen considerar que están fuera de los auspicios del servicio que ofrece los datos al cliente; un servicio no puede forzar directamente un cliente para que agregue o quite información de una caché del lado cliente. Esto significa que es posible que un cliente que usa una caché mal configurada (por ejemplo, las directivas de expiración no se han implementado correctamente) continúe con información obsoleta almacenada en caché localmente cuando ha cambiado la información del origen de datos original.

Si está creando una aplicación web que sirve datos a través de una conexión HTTP, puede forzar implícitamente un cliente web (por ejemplo, un explorador o un proxy web) para capturar la información más reciente, si se actualiza un recurso cambiando el URI de ese recurso. Los clientes web normalmente usan el URI de un recurso como la clave en la caché del cliente, por lo que el cambio del URI hace que el cliente web ignore cualquier versión de un recurso almacenada en caché anteriormente y que capture la nueva versión en su lugar.

## Administración de la simultaneidad en una memoria caché

Las memorias caché a menudo están diseñadas para ser compartidas por varias instancias de una aplicación. Cada instancia de aplicación puede leer y modificar los datos de la memoria caché. Por consiguiente, los mismos problemas de simultaneidad que surgen con cualquier almacén de datos compartidos también son aplicables a una memoria caché. En una situación donde una aplicación necesita modificar los datos almacenados en la memoria caché, puede que necesite asegurarse de que las actualizaciones realizadas por una instancia de la aplicación no sobrescriben a ciegas los cambios realizados por otra instancia.

En función de la naturaleza de los datos y de la probabilidad de las colisiones, puede adoptar uno de dos enfoques para simultaneidad:

- __Optimista.__ La aplicación comprueba si han cambiado los datos de la memoria caché desde que se recuperaron, inmediatamente antes de actualizarlos. Si los datos siguen siendo los mismos, se pueden realizar el cambio. De lo contrario, la aplicación tiene que decidir si desea actualizarlos (la lógica de negocios que impulsa esta decisión será específica de la aplicación). Este enfoque es adecuado para situaciones en las que las actualizaciones no son frecuentes o donde es improbable que se produzcan colisiones.
- __Pesimista.__ La aplicación bloquea los datos en la memoria caché cuando los recupera para impedir que otra instancia cambie los datos. Este proceso garantiza que no se puedan realizar las colisiones, pero podrían bloquear otras instancias que deben procesar los mismos datos. La simultaneidad pesimista puede afectar a la escalabilidad de la solución y debe usarse únicamente para las operaciones de corta duración. Este enfoque puede ser adecuado en situaciones donde las colisiones son más probables, especialmente si una aplicación actualiza varios elementos de la memoria caché y debe asegurarse de que estos cambios se aplican de forma coherente.

### Implementación de alta disponibilidad, escalabilidad y mejora del rendimiento

Una caché no debe ser el repositorio principal de datos; este es el rol del almacén de datos original desde el que se rellena la memoria caché. El almacén de datos original es responsable de garantizar la persistencia de los datos.

Tenga cuidado de no introducir dependencias críticas en la disponibilidad de un servicio de caché compartida en sus soluciones. Una aplicación debe ser capaz de seguir funcionando si el servicio que ofrece la memoria caché compartida no está disponible; la aplicación no debe bloquearse o producir un error mientras espera la reanudación del servicio de caché. Por tanto, la aplicación debe estar preparada para detectar la disponibilidad del servicio de caché y revertir al almacén de datos original si la memoria caché no está accesible. El [patrón de interruptor](http://msdn.microsoft.com/library/dn589784.aspx) es útil para controlar este escenario. Se puede recuperar el servicio que ofrece la memoria caché y una vez que se encuentre disponible, la memoria caché se puede volver a llenar a medida que se leen datos del almacén de datos original, siguiendo una estrategia como el [patrón de Cache-Aside](http://msdn.microsoft.com/library/dn589799.aspx).

Sin embargo, retroceder al almacén de datos original si la memoria caché no está disponible temporalmente puede tener un impacto de escalabilidad en el sistema; mientras se está recuperando el almacén de datos, el almacén de datos original podría inundarse de solicitudes de datos, lo que daría lugar a tiempos de espera y conexiones con errores. Una estrategia que debería considerar es implementar una memoria caché local y privada en cada instancia de una aplicación junto con la memoria caché compartida a la que tienen acceso todas las instancias de la aplicación. Cuando la aplicación recupera un elemento, puede comprobar primero en su memoria caché local, a continuación, en la memoria caché compartida y finalmente en el almacén de datos originales. La memoria caché local se pueden rellenar con los datos de la memoria caché compartida, o la base de datos si la memoria caché compartida no está disponible. Este enfoque requiere una cuidadosa configuración para evitar que la memoria caché local se vuelva demasiado obsoleta con respecto a la memoria caché compartida, pero actúa como un búfer si la memoria caché compartida es inaccesible. En la Figura 3 se muestra esta estructura.

![Uso de una memoria caché local y privada con una caché compartida\_](media/best-practices-caching/Caching3.png) 
_Figura 3: Uso de una memoria caché local y privada con una memoria caché compartida_

Para admitir cachés de gran tamaño con datos de duración relativamente larga, algunos servicios de caché ofrecen una opción de alta disponibilidad que implementa la conmutación automática por error si la memoria caché dejar de estar disponible. Este enfoque normalmente implica la réplica de los datos en caché que se almacenan en un servidor de caché principal en un servidor de caché secundario y el cambio al servidor secundario si el servicio principal genera error o se pierde la conectividad. Para reducir la latencia asociada a la escritura en varios destinos, cuando se escriben datos en la memoria caché del servidor principal, la replicación en el servidor secundario puede producirse de forma asincrónica. Este enfoque lleva a la posibilidad de que se pueda perder parte de la información almacenada en caché en el caso de un error, pero la proporción de estos datos debe ser pequeña en comparación con el tamaño total de la memoria caché.

Si una memoria caché compartida es grande, puede ser beneficioso crear particiones de los datos en caché en los nodos para reducir las posibilidades de contención y mejorar la escalabilidad. Muchas cachés compartidas admiten la capacidad de agregar (y de quitar) nodos dinámicamente y de reequilibrar los datos entre las particiones. Este enfoque puede implicar la agrupación en clústeres por la que la colección de nodos se presenta a las aplicaciones cliente como una memoria caché única y sin problemas, pero internamente los datos se dispersan entre nodos siguiendo alguna estrategia de distribución predefinida que equilibra la carga de manera uniforme. En el [documento de guía de creación de particiones de datos](http://msdn.microsoft.com/library/dn589795.aspx) del sitio web de Microsoft se ofrece más información sobre las posibles estrategias de creación de particiones.

La agrupación en clústeres también puede agregar más disponibilidad de la memoria caché; si se produce un error en un nodo, el resto de la memoria caché seguirá siendo accesible. La agrupación en clústeres se usa con frecuencia junto con la replicación y la conmutación por error; cada nodo se puede replicar y la réplica volver a poner en línea rápidamente si se genera un error en el nodo.

Muchas operaciones de lectura y escritura implicarán probablemente objetos o valores de datos únicos. Sin embargo, puede haber ocasiones en que es necesario almacenar o recuperar rápidamente grandes volúmenes de datos. Por ejemplo, la propagación de una memoria caché podría implicar la escritura de cientos o miles de elementos en la memoria caché o que una aplicación pueda necesitar recuperar un gran número de elementos relacionados de la memoria caché como parte de la misma solicitud. Muchas cachés a gran escala ofrecen operaciones por lotes para estos fines, lo que permite a una aplicación de cliente empaquetar un gran volumen de elementos en una única solicitud y la reducción de la sobrecarga asociada a la realización de un gran número de pequeñas solicitudes.

## Almacenamiento en caché y coherencia eventual

El patrón Cache-Aside depende de la instancia de la aplicación que rellena la caché que tiene acceso a la versión más reciente y coherente de los datos. En un sistema que implementa la coherencia eventual (como un almacén de datos replicados), puede que este no sea el caso. Una instancia de una aplicación podría modificar un elemento de datos e invalidar la versión almacenada en caché de ese elemento. Otra instancia de la aplicación puede intentar leer este elemento de la caché que produce a un error de caché, por lo que lee los datos del almacén de datos y los agrega a la caché. Sin embargo, si el almacén de datos no se ha sincronizado por completo con las demás réplicas, la instancia de la aplicación podría leer y rellenar la caché con el valor antiguo.

Para más información sobre cómo administrar la coherencia de los datos, vea la página [Información básica de la coherencia de datos](http://msdn.microsoft.com/library/dn589800.aspx) en el sitio web de Microsoft.

### Protección de los datos almacenados en caché

Sea cual sea el servicio de caché que usa, debe considerar cómo proteger los datos contenidos en la memoria caché del acceso no autorizado. Hay dos cuestiones principales:

- La privacidad de los datos en la memoria caché.
- La privacidad de los datos a medida que fluyen entre la memoria caché y la aplicación que usa la memoria caché.

Para proteger los datos de la memoria caché, el servicio de caché puede implementar un mecanismo de autenticación que requiere que las aplicaciones se identifiquen y un esquema de autorización que especifica qué identidades pueden acceder a los datos de la memoria caché y las operaciones (lectura y escritura) que estas identidades pueden realizar. Para reducir los gastos generales asociados a la lectura y la escritura de datos, una vez que se ha concedido acceso de lectura/o escritura a la memoria caché, dicha identidad puede usar los datos de la memoria caché. Si necesita restringir el acceso a los subconjuntos de los datos en caché, puede:

- Dividir la memoria caché en particiones (mediante el uso de distintos servidores de caché) y conceder acceso únicamente a las identidades para las particiones a las que deberían tener permiso para utilizar, o
- Cifrar los datos de cada subconjunto con claves diferentes y proporcionar únicamente las claves de cifrado para las identidades que deben tener acceso a cada subconjunto. Para una aplicación cliente puede seguir siendo posible recuperar todos los datos de la memoria caché, pero solo podrá descifrar los datos para los que tiene las claves.

Para proteger los datos a medida que fluyen dentro y fuera de la memoria caché, depende de las características de seguridad ofrecidas por la infraestructura de red que las aplicaciones cliente usan para conectarse a la caché. Si la memoria caché se implementa mediante un servidor local en la misma organización que hospeda las aplicaciones cliente, el aislamiento de la propia red puede no requerir que lleve a cabo pasos adicionales. Si la memoria caché se encuentra ubicada de manera remota y requiere una conexión TCP o HTTP a través de una red pública (como Internet), debería considerar la posibilidad de implementar SSL.

## Consideraciones para implementar el almacenamiento en caché con Microsoft Azure

Azure ofrece la Caché en Redis de Azure. Se trata de una implementación de la caché en Redis de código fuente que se ejecuta como servicio en un centro de datos de Azure. Ofrece un servicio de almacenamiento en caché al que se puede acceder desde cualquier aplicación de Azure, ya se implementa la aplicación como servicio en la nube, un sitio web o dentro de una máquina virtual de Azure. Las memorias caché pueden compartirse entre aplicaciones cliente que dispongan de la clave de acceso adecuado.

Redis es una solución de almacenamiento en caché de alto rendimiento que ofrece disponibilidad, escalabilidad y seguridad. Normalmente se ejecuta como un servicio distribuido en una o varias máquinas e intenta almacenar tanta información como sea posible en la memoria para garantizar un acceso rápido. Esta arquitectura está pensada para ofrecer baja latencia y alto rendimiento al reducir la necesidad de realizar operaciones lentas de E/S.

La caché en Redis de Azure es compatible con muchas de las distintas API que se usan por las aplicaciones cliente. Si tiene aplicaciones existentes que ya usan Redis localmente, la Caché en Redis de Azure ofrece una ruta de acceso de migración rápida para el almacenamiento en caché en la nube.

> [AZURE.NOTE] Azure también ofrece el servicio de caché administrado. Este servicio se basa en el motor de la caché de AppFabric de Microsoft. Le permite crear una caché distribuida que se puede compartir entre aplicaciones de acoplamiento flexible. La memoria caché se hospeda en servidores de alto rendimiento que se ejecutan en un centro de datos de Azure. Sin embargo, ya no se recomienda esta opción y solo se proporciona para admitir aplicaciones existentes que se han creado para usarla. Para todo el desarrollo nuevo, use la Caché en Redis de Azure.
>
> Además, Azure admite el almacenamiento en caché en rol. Esta característica le permite crear una memoria caché específica para un servicio en la nube. La caché se hospeda en instancias de un rol web o de trabajo y solo se puede acceder a ella mediante roles que funcionan como parte de la misma unidad de implementación del servicio en la nube (una unidad de implementación es el conjunto de instancias de rol implementadas como un servicio en la nube para una región específica). La memoria caché está agrupada y todas las instancias del rol dentro de la misma unidad de implementación que hospedan la memoria caché se convierten en parte del mismo clúster de caché. Sin embargo, ya no se recomienda esta opción y solo se proporciona para admitir aplicaciones existentes que se han creado para usarla. Para todo el desarrollo nuevo, use la Caché en Redis de Azure.
>
> Tanto el Servicio de caché administrado de Azure como el Caché en rol de Azure actualmente están programados para su retirada el 16 de noviembre de 2016. Se recomienda que migre a la Caché en Redis de Azure con vistas a prepararse para la mencionada retirada. Para más información, visite la página [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](redis-cache/cache-faq.md#what-redis-cache-offering-and-size-should-i-use) en el sitio web de Microsoft.


### Características de Redis

Redis es más que un servidor de caché simple; ofrece una base de datos en memoria distribuida con un conjunto extenso de comandos que admite muchos escenarios comunes, como se describe en la sección Casos de uso para el almacenamiento en caché en Redis más adelante en este documento. En esta sección se resumen algunas de las características clave que ofrece Redis.

### Redis como base de datos en memoria

Redis admite las operaciones de lectura y escritura. A diferencia de muchas memorias caché (que se deben considerar como almacenes de datos transitorios), se pueden protegerse la escrituras del error del sistema haciendo que se almacenan periódicamente en un archivo de instantánea local o en un archivo de registro de solo anexión. Todas las escrituras son asincrónicas y no bloquean a los clientes de la lectura y escritura de datos. Cuando Redis empieza a ejecutarse, lee los datos del archivo de registro o de instantánea y lo usa para construir la caché en memoria. Para obtener más información, vea [Persistencia de Redis](http://redis.io/topics/persistence) en el sitio web de Redis.

> [AZURE.NOTE] Redis no garantiza que todas las escrituras se guardarán en el caso de un error grave, pero en el peor de los casos solo debería perder unos segundos de datos. Recuerde que una memoria caché no está diseñada para actuar como un origen de datos autoritativo y que es responsabilidad de las aplicaciones que usan la memoria caché asegurarse de que los datos críticos se guardan correctamente en un almacén de datos adecuado. Para obtener más información, vea el patrón de Cache-Aside.

#### Tipos de datos de Redis

Redis es un almacén de valor-clave, donde los valores pueden contener estructuras de datos complejos o tipos simples, como valores hash, listas y conjuntos. Admite un conjunto de operaciones atómicas en estos tipos de datos. Las claves pueden ser permanentes o estar etiquetados con un tiempo limitado de vida en cuyo momento la clave y su valor correspondiente se quitan automáticamente de la memoria caché. Para obtener más información acerca de los valores y las claves de Redis, visite la página [Introducción a las abstracciones y lo tipos de datos de Redis](http://redis.io/topics/data-types-intro) en el sitio web de Redis.

#### Agrupación en clústeres y replicación de Redis

Redis admite la replicación de maestro/subordinado para ayudar a garantizar la disponibilidad y el mantenimiento del rendimiento; las operaciones de escritura a un nodo maestro de Redis se replican a uno o más nodos subordinados y las operaciones de lectura se pueden atender por el maestro o cualquiera de los subordinados. En el caso de una partición de red, los subordinados pueden continuar sirviendo datos y luego volver a sincronizar de manera transparente con el maestro cuando se restablece la conexión. Para obtener más información, visite la página [Replicación](http://redis.io/topics/replication) en el sitio web de Redis.

Redis también ofrece agrupación en clústeres, lo que le permite crear particiones de datos de manera transparente en particiones entre servidores y distribuir la carga. Esta característica mejora la escalabilidad puesto que permite agregar nuevos servidores de Redis y la creación de particiones de los datos a medida que aumenta el tamaño de la caché. Además, cada servidor del clúster se puede replicar mediante la replicación de maestro/subordinado para garantizar la disponibilidad en cada nodo del clúster. Para obtener más información sobre la agrupación en clústeres y el particionamiento, visite la [página del Tutorial del clúster de Redis](http://redis.io/topics/cluster-tutorial) en el sitio web de Redis.

### Uso de la memoria Redis

Una caché de Redis tiene un tamaño limitado según los recursos disponibles en el equipo host. Al configurar un servidor de Redis, puede especificar la cantidad máxima de memoria que puede usar. Una clave en una caché de Redis puede configurarse con un tiempo de expiración, tras el cual se quita automáticamente de la memoria caché. Esta característica puede ayudar a impedir que la memoria caché se rellene con datos obsoletos o antiguos.

Conforme se rellena la memoria, Redis puede expulsar automáticamente las claves y sus valores siguiendo varias directivas. El valor predeterminado es LRU (menos usados recientemente), pero también puede seleccionar otras directivas como las claves de expulsión de manera aleatoria o la completa desactivación de la expulsión (en cuyo caso, los intentos de agregar elementos a la caché generará error si se completa). La página [Uso de Redis como una caché de LRU](http://redis.io/topics/lru-cache) ofrece más información.

### Lotes y transacciones de Redis

Redis habilita una aplicación cliente para que envíe una serie de operaciones que leer y escriben datos en la memoria caché como una transacción atómica. Se garantiza que todos los comandos de la transacción se ejecutan secuencialmente y que ningún comando emitido por otros clientes simultáneos se entrelazará entre ellos. Sin embargo, estas no son verdaderas transacciones como las realizaría una base de datos relacional. El procesamiento de las transacciones consta de dos etapas; la cola de comandos y la ejecución de comandos. Durante la fase de puesta en cola de comandos, el cliente envía los comandos que componen la transacción. Si se produce algún tipo de error en este momento (por ejemplo, un error de sintaxis o un número incorrecto de parámetros), Redis rechazará el procesamiento de toda la transacción y la descartará. Durante la fase de ejecución, Redis lleva a cabo cada comando en cola en secuencia. Si se produce un error de un comando durante esta fase, Redis continuará con el siguiente comando en cola y no revertirá los efectos de los comandos que ya se han ejecutado. Esta forma simplificada de transacción ayuda a mantener el rendimiento y a evitar los problemas de rendimiento provocados por la contención. Redis no implementa una forma de bloqueo optimista para ayudar a mantener la coherencia. Para obtener información detallada sobre las transacciones y el bloqueo con Redis, visite la [página Transacciones](http://redis.io/topics/transactions) del sitio web de Redis.

Redis también admite el procesamiento por lotes no transaccional de solicitudes. El protocolo Redis que usan los clientes para enviar comandos a un servidor de Redis permite a un cliente enviar una serie de operaciones como parte de la misma solicitud. Esto puede ayudar a reducir la fragmentación de paquetes en la red. Cuando se procesa el lote, se lleva a cabo cada comando. A diferencia de una transacción, si alguno de estos comandos tiene un formato incorrecto se rechazará, pero los comandos restantes se llevarán a cabo. Tampoco hay ninguna garantía en el orden en que se procesarán los comandos del lote.

### Seguridad de Redis

Redis se centra exclusivamente en ofrecer un acceso rápido a los datos y está diseñado para ejecutarse dentro de un entorno de confianza y para que solo accedan a él los clientes de confianza. Redis solo admite un modelo de seguridad limitada basado en la autenticación de contraseña (es posible quitar la autenticación por completo, aunque no se recomienda esto). Todos los clientes autenticados comparten la misma contraseña global y tienen acceso a los mismo recursos. Si necesita seguridad de inicio de sesión más completa, debe implementar su propio nivel de seguridad delante del servidor de Redis y todas las solicitudes de cliente deben pasar por este nivel adicional; Redis no se debería exponer directamente a los clientes no autenticados o que no son de confianza.

Puede restringir el acceso a los comandos deshabilitándolos o cambiándolos de nombre (y ofreciendo solo a los clientes con privilegios los nuevos nombres).

Redis no admite directamente ninguna forma de cifrado de datos, por lo que toda la codificación debe realizarse por las aplicaciones cliente. Además, Redis no ofrece ninguna forma de seguridad de transporte, por lo que si necesita proteger datos conforme fluyen por la red, debe implementar un proxy SSL.

Para obtener más información, visite la página [Seguridad de Redis](http://redis.io/topics/security) en el sitio web de Redis.

> [AZURE.NOTE] Caché en Redis de Azure proporciona su propio nivel de seguridad a través del cual se conectan los clientes; los servidores de Redis subyacentes no se exponen a la red pública.

### Uso de la caché en Redis de Azure

La caché en Redis de Azure ofrece acceso a servidores de Redis que se ejecutan en servidores hospedados en un centro de datos de Azure; actúa como una fachada que ofrece seguridad y control de acceso. Puede aprovisionar una caché mediante el portal de Administración de Azure. El portal ofrece varias configuraciones predefinidas, que van desde una caché de 53 GB que se ejecuta como un servicio dedicado que admite comunicaciones de SSL (para privacidad) y replicación de maestro/subordinado con un SLA del 99,9 % de disponibilidad, hasta una caché de 250 MB sin replicación (ninguna garantía de disponibilidad) que se ejecuta en hardware compartido.

Mediante el portal de administración de Azure, también puede configurar la directiva de expulsión de la caché y controlar el acceso a la caché agregando usuarios a los roles ofrecidos: Propietario, Colaborador, Lector. Estos roles definen las operaciones que los miembros pueden realizar. Por ejemplo, los miembros del rol Propietario tienen control completo sobre la caché (incluida la seguridad) y su contenido, los miembros del rol Colaborador pueden leer y escribir información en la caché y los miembros del rol Lector solo pueden recuperar datos de la caché.

La mayoría de las tareas administrativas se llevan a cabo a través del portal de administración de Azure y por este motivo muchos de los comandos administrativos disponibles en la versión estándar de Redis no están disponibles, incluida la capacidad de modificar la configuración mediante programación, apagar el servidor de Redis, configurar esclavos adicionales o forzar el proceso de guardado de los datos en el disco.

El portal de administración de Azure incluye una visualización gráfica adecuada que le permite supervisar el rendimiento de la memoria caché. Por ejemplo, puede ver el número de conexiones que se realizan, el número de solicitudes llevadas a cabo, el volumen de lecturas y escrituras, y el número de aciertos frente a errores de caché. Con esta información puede determinar la eficacia de la memoria caché y, en caso necesario, cambia a una configuración diferente o cambiar la directiva de expulsión. Además, puede crear alertas que envíen mensajes de correo electrónico a un administrador si una o más métricas críticas quedan fuera de un intervalo esperado. Por ejemplo, si el número de errores de caché supera un valor especificado en la última hora, un administrador podría recibir una alerta ya que la memoria caché podría ser demasiado pequeña o los datos se podrían expulsar demasiado rápido.

También puede supervisar la CPU, la memoria y el uso de la red para la memoria caché.

Para obtener más información y ejemplos en los que se muestra cómo crear y configurar una caché en Redis de Azure, visite la página [En torno a Caché en Redis de Azure](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) en el blog de Azure.

## Estado de la sesión del almacenamiento en caché y salida HTML

Si está creando aplicaciones web ASP.NET que se ejecutan mediante el uso de roles web de Azure, puede guardar información del estado de sesión y salida HTML en una Caché en Redis de Azure. El proveedor de estados de sesión para Caché en Redis de Azure le permite compartir información de sesión entre diferentes instancias de una aplicación web ASP.NET y es muy útil en situaciones de granja de servidores web donde no está disponible la afinidad cliente-servidor y los datos de sesión de almacenamiento en caché en memoria no serían adecuados.

El uso del proveedor de estados de sesión con Caché en Redis de Azure ofrece varias ventajas, entre las que se incluyen:

- Puede compartir el estado de sesión entre un gran número de instancias de una aplicación web ASP.NET y ofrecer una mejor escalabilidad.
- Admite el acceso simultáneo y controlado a los mismos datos de estado de sesión para múltiples lectores y un escritor único y
- Puede usar la comprensión para ahorrar memoria y mejorar el rendimiento de la red.

Para obtener más información, visite la página [Proveedor de estados de sesión de ASP.NET para Caché en Redis de Azure](redis-cache/cache-asp.net-session-state-provider.md) en el sitio web de Microsoft.

> [AZURE.NOTE] No use el proveedor de estados de sesión para Caché en Redis de Azure para las aplicaciones ASP.NET que se ejecutan fuera del entorno de Azure. La latencia del acceso a la memoria caché desde fuera de Azure puede eliminar las ventajas del rendimiento del almacenamiento en caché de los datos.

De forma similar, el proveedor de caché de resultados para caché en Redis de Azure permite guardar las respuestas HTTP generadas por una aplicación web ASP.NET. El uso del proveedor de caché de resultados con caché en Redis de Azure puede mejorar los tiempos de respuesta de las aplicaciones que representan salida HTML compleja; las instancias de aplicación que generan respuestas similares pueden usar los fragmentos de salida compartidos en la memoria caché en lugar de generar este código HTML de salida nuevo. Para obtener más información, visite la página [Proveedor de cachés de resultados de ASP.NET para Caché en Redis de Azure](redis-cache/cache-asp.net-output-cache-provider.md) en el sitio web de Microsoft.

## Creación de una caché en Redis personalizada

La memoria caché en Redis de Azure actúa como una fachada para los servidores de Redis subyacentes. Actualmente admite un conjunto fijo de configuraciones pero no se ofrece para la agrupación en clústeres de Redis. Si requiere una configuración avanzada que no está cubierta por la caché en Redis de Azure (como una caché mayor de 53 GB) puede crear y hospedar sus propios servidores Redis con máquinas virtuales de Azure. Este es un proceso potencialmente complejo ya que puede necesitar crear varias VM para que actúe como nodos maestros y subordinados si desea implementar la replicación. Además, si desea crear un clúster, necesitará varios maestros y servidores subordinados; una topología de replicación mínima y agrupada en clúster, que ofrece un alto grado de disponibilidad y escalabilidad consta de al menos 6 máquinas virtuales organizadas como 3 parejas de servidores maestro/subordinado (un clúster debe contener al menos 3 nodos maestros). Cada par maestro/subordinado debe encontrarse junto para minimizar la latencia, pero cada conjunto de pares se pueden ejecutar en diferentes centros de datos de Azure ubicados en distintas regiones si desea colocar los datos almacenados en memoria caché cerca las aplicaciones que tienen más probabilidades de usarlos. La página [Ejecución de Redis en una máquina virtual Linux de CentOS en Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) del sitio web de Microsoft le guía a través de un ejemplo que muestra cómo crear y configurar un nodo Redis que se ejecuta como una máquina virtual de Azure.

Tenga en cuenta que si implementa su propia caché en Redis de esta manera, usted es responsable de supervisar, administrar y proteger el servicio.

## Creación de particiones de una caché en Redis

La creación de particiones de la memoria caché implica la división de la memoria caché en varios equipos. Esta estructura le ofrece varias ventajas sobre el uso de un único servidor de caché, incluidos:

- La creación de una caché que es mucho mayor de lo que se puede almacenar en un servidor único.
- La distribución de datos entre servidores, mejorando la disponibilidad. Si se produce un error en un servidor o deja de estar accesible, solo dejarán de estar disponibles los datos que contiene; todavía se podrá acceder a los datos de los servidores restantes. Para una memoria caché, esto no es algo fundamental ya que los datos almacenados en memoria caché son solo una copia transitoria de los datos contenidos en una base de datos y los datos almacenados en caché de un servidor que se vuelve inaccesible se pueden almacenar en caché en un servidor diferente en su lugar.
- El reparto de la carga entre servidores, lo que mejora el rendimiento y la escalabilidad.
- La colocación geográfica de los datos cerca de los usuarios que acceden a ellos, reduciendo la latencia.

Para una memoria caché, la forma más común de crear particiones es mediante el particionamiento. En esta estrategia, cada partición es una caché en Redis en su propio derecho. Los datos se dirigen a una partición específica mediante el uso de lógica de particionamiento, que puede usar una variedad de enfoques para distribuir los datos. El [patrón de particionamiento](http://msdn.microsoft.com/library/dn589797.aspx) ofrece más información sobre la implementación del particionamiento.

Para implementar la creación de particiones en una caché en Redis, puede adoptar uno de los enfoques siguientes:

- _Enrutamiento de consultas del lado servidor._ En esta técnica, una aplicación cliente envía una solicitud a cualquiera de los servidores de Redis que componen la memoria caché (probablemente, el servidor más cercano). Cada servidor Redis almacena metadatos que describen la partición que contiene y también incluye información acerca de qué claves particiones se encuentran en otros servidores. El servidor Redis examina la solicitud del cliente y, si se puede resolver localmente, llevará a cabo la operación solicitada. En caso contrario, reenviará la solicitud al servidor apropiado. Este modelo se implementa mediante la agrupación en clústeres de Redis y se describe con más detalle en la página [Tutorial de clúster Redis](http://redis.io/topics/cluster-tutorial) en el sitio web de Redis. La agrupación en clústeres de Redis es transparente para las aplicaciones de cliente y se pueden agregar servidores Redis al clúster (y los datos se pueden volver a dividir en particiones) sin necesidad de volver a configurar los clientes.

- _Creación de particiones del lado cliente._ En este modelo, la aplicación cliente contiene lógica (posiblemente en forma de una biblioteca) que enruta solicitudes al servidor de Redis adecuado. Este enfoque se puede usar con Caché en Redis de Azure; crear varias cachés en Redis de Azure (una para cada partición de datos) e implementar la lógica del lado cliente que enruta las solicitudes a la caché correcta. Si cambia el esquema creación de particiones(si se crean cachés en Redis de Azure adicionales, por ejemplo), es posible que las aplicaciones cliente deban volver a configurarse.

- _Creación de particiones asistida por proxy._ En este esquema, las aplicaciones cliente envían solicitudes a un servicio proxy intermediario que comprende cómo se particionan los datos y luego enruta la solicitud al servidor de Redis adecuado. Este enfoque también se puede usar con la Caché en Redis de Azure; el servicio proxy se podría implementar como un servicio en la nube de Azure. Este enfoque requiere un nivel adicional de complejidad para implementar el servicio y las solicitudes pueden tardar más tiempo en llevarse a cabo que el uso de la creación de particiones del lado cliente.

La página [Creación de particiones: cómo dividir los datos entre varias instancias de Redis](http://redis.io/topics/partitioning) del sitio web de Redis ofrece más información acerca de cómo implementar la creación de particiones con Redis.

### Implementación de las aplicaciones del cliente de caché en Redis

Redis admite las aplicaciones de cliente escritas en numeroso lenguajes de programación. Si va a crear nuevas aplicaciones con .NET Framework, el enfoque recomendado es usar la biblioteca de cliente de StackExchange.Redis. Esta biblioteca ofrece un modelo de objeto de .NET Framework que abstrae los detalles para conectarse a un servidor de Redis, enviar comandos y recibir respuestas. Está disponible en Visual Studio como paquete NuGet. Puede usar esta misma biblioteca para conectarse a una caché en Redis de Azure o una caché de Redis personalizada hospedada en una máquina virtual.

Para conectarse a un servidor de Redis, use el método estático `Connect` de la clase `ConnectionMultiplexer`. La conexión que crea este método está diseñada para usarse en toda la duración de la aplicación cliente y la misma conexión se puede usar por varios subprocesos simultáneos; no vuelva a conectarse y a desconectarse cada vez que realice una operación de Redis ya que esto puede degradar el rendimiento. Puede especificar los parámetros de conexión, como la dirección del host de Redis y la contraseña. Si usa la caché en Redis de Azure, use como contraseña la clave principal o la secundaria generada para la Caché en Redis de Azure mediante el portal de administración de Azure.

Cuando se haya conectado al servidor de Redis, puede obtener un identificador de la base de datos de Redis que actúa como la caché. La conexión de Redis ofrece el método `GetDatabase` para lograrlo. A continuación, puede recuperar los elementos de la caché y almacenar datos en la memoria caché mediante los métodos `StringGet` y `StringSet`. Estos métodos esperan una clave como parámetro y devuelven el elemento en la caché que tiene un valor coincidente (`StringGet`) o agregan el elemento a la caché con esta clave (`StringSet`).

En función de la ubicación del servidor Redis, muchas operaciones pueden sufrir alguna latencia mientras se transmite una solicitud al servidor y se devuelve una respuesta al cliente. La biblioteca de StackExchange proporciona versiones asincrónicas de muchos de los métodos que expone para ayudar a que las aplicaciones cliente siga respondiendo. Estos métodos admiten el [patrón asíncrono basado en tareas](http://msdn.microsoft.com/library/hh873175.aspx) en .NET Framework.

El fragmento de código siguiente muestra un método denominado `RetrieveItem` que muestra un ejemplo de una implementación del patrón cache-aside basado en Redis y la biblioteca de StackExchange. El método toma un valor de clave de cadena e intenta recuperar el elemento correspondiente de la memoria caché de Redis llamando al método `StringGetAsync` (la versión asincrónica de `StringGet`). Si no se encuentra el elemento, se captura del origen de datos subyacente mediante el método `GetItemFromDataSourceAsync` (que es un método local y no forma parte de la biblioteca de StackExchange) y luego se agrega a la memoria caché mediante el método `StringSetAsync` para que se pueda recuperar más rápidamente la próxima vez.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Los métodos `StringGet` y `StringSet` no se limitan a recuperar o almacenar valores de cadena; pueden tomar cualquier elemento serializado como una matriz de bytes. Si necesita guardar un objeto .NET, puede serializarlo como una secuencia de bytes y usar el método StringSet para escribirlo en la caché. Del mismo modo, puede leer un objeto de la caché mediante el método StringGet y deserializarlo como un objeto. NET. El código siguiente muestra un conjunto de métodos de extensión para la interfaz de IDatabase (el método GetDatabase de una conexión de Redis devuelve un objeto `IDatabase`) y algo del código de ejemplo que usa estos métodos para leer y escribir un objeto BlogPost en la memoria caché:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

El código siguiente muestra un método denominado `RetrieveBlogPost` que usa estos métodos de extensión para leer y escribir un objeto `BlogPost` serializable en la memoria caché que sigue al patrón cache-aside:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis admite la canalización de comandos si una aplicación cliente envía varias solicitudes asincrónicas. Redis puede multiplexar las solicitudes con la misma conexión, en lugar de recibir y responder a comandos en una secuencia estricta. Este enfoque ayuda a reducir la latencia haciendo un uso más eficaz de la red. El siguiente fragmento de código muestra un ejemplo que recupera los detalles de los dos clientes simultáneamente. El código envía dos solicitudes y después realiza algún otro procesamiento (no mostrado) antes de esperar para recibir los resultados. El método Wait del objeto de caché es similar al método Task.Wait de .NET Framework:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

La página [Documentación de la Caché en Redis de Azure](https://azure.microsoft.com/documentation/services/cache/) del sitio web de Microsoft ofrece proporciona más información sobre cómo escribir aplicaciones cliente que pueden usar la memoria caché en Redis de Azure. Hay información adicional disponible en la [página Uso básico](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) del sitio web de StackExchange.Redis y la página [Canalizaciones y multiplexadores](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) del mismo sitio web ofrece más información sobre la canalización y las operaciones asíncronas con Redis y la biblioteca de StackExchange. La sección Casos de uso para el almacenamiento en caché de Redis más adelante en esta guía proporciona ejemplos de algunas de las técnicas más avanzadas que se pueden aplicar a los datos almacenados en una caché de Redis.

## Casos de uso para el almacenamiento en caché en Redis

El uso más sencillo de Redis para almacenar en caché hace referencia al almacenamiento de pares clave/valor donde el valor es una cadena no interpretada de longitud arbitraria que puede contener cualquier dato binarios (es esencialmente una matriz de bytes que se puede tratar como una cadena). Este escenario se ilustró en la sección Implementación de las aplicaciones cliente de la caché en Redis anteriormente en esta guía. Tenga en cuenta que las claves también contienen datos no interpretados, para que pueda usar cualquier información binaria como la clave, aunque cuanto más larga sea la clave mayor será el espacio que ocupará para almacenarse y más tiempo se tardarán en realizar las operaciones de búsqueda. Para facilidad de uso y de mantenimiento, diseñe cuidadosamente su espacio de claves y use claves significativas (pero no detalladas). Por ejemplo, use claves estructuradas como "cliente: 100" para representar la clave para el cliente con id. 100 en lugar de simplemente "100". Este esquema le habilita para distinguir con facilidad entre valores que almacenan tipos de datos diferentes. Por ejemplo, también puede usar la clave "orders:100" para representar la clave para el pedido con el id. 100.

Además de cadenas binarias unidimensionales, un valor en un par clave/valor de Redis también puede contener información más estructurada, incluidas listas, conjuntos (ordenados y sin clasificar) y algoritmos hash. Redis ofrece un conjunto de comandos completo que puede manipular estos tipos y muchos de estos comandos están disponibles para las aplicaciones de .NET Framework a través de una biblioteca de cliente como StackExchange. La página [Introducción a las abstracciones y a los tipos de datos de Redis y](http://redis.io/topics/data-types-intro) del sitio web de Redis ofrece una visión general más detallada de estos tipos y de los comandos que puede usar para manipularlos.

En esta sección se resumen algunos casos de uso comunes para estos tipos de datos y comandos.

### Realización de operaciones atómicas y por lotes

Redis admite una serie de operaciones atómicas de get y set en valores de cadena. Estas operaciones eliminan los posibles peligros de carrera que pueden producirse al usar los comandos `GET` y `SET` independientes. Entre las operaciones disponibles se incluyen:

- `INCR`, `INCRBY`, `DECR`, y `DECRBY` que realizan las operaciones atómicas de incremento y decremento en valores de datos numéricos enteros. La biblioteca de StackExchange ofrece versiones sobrecargadas de los métodos `IDatabase.StringIncrementAsync` y `IDatabase.StringDecrementAsync` para realizar estas operaciones y devuelven el valor resultante almacenado en la memoria caché. El siguiente fragmento de código muestra cómo usar estos métodos:

   ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET` que recupera el valor asociado a una clave y lo cambia a un nuevo valor. La biblioteca de StackExchange hace que esta operación esté disponible a través del método `IDatabase.StringGetSetAsync`. El siguiente fragmento de código muestra un ejemplo de este método. Este código devuelve el valor actual asociado a la clave "data:counter" del ejemplo anterior y restablece el valor para esta clave en cero, todo ello como parte de la misma operación:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` y `MSET`, que pueden devolver o cambiar un conjunto de valores de cadena como una sola operación. Los métodos `IDatabase.StringGetAsync` y `IDatabase.StringSetAsync` están sobrecargados para admitir esta funcionalidad, como se muestra en el ejemplo siguiente:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key/value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key/value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

También puede combinar varias operaciones en una sola transacción de Redis como se describe en la sección Lotes y transacciones de Redis de esta guía. La biblioteca de StackExchange ofrece compatibilidad para las transacciones a través de la interfaz de `ITransaction`. Puede crear un objeto ITransaction mediante el método IDatabase.CreateTransaction e invocar comandos para la transacción mediante el objeto `ITransaction` proporcionado por los métodos. La interfaz `ITransaction` ofrece acceso a un conjunto similar de métodos como la interfaz `IDatabase` con la excepción de que todos los métodos son asincrónicos; solo se llevan a cabo cuando se invoca el método `ITransaction.Execute`. El valor devuelto por el método execute indica si la transacción se creó correctamente (true) o no (false).

En el siguiente fragmento de código se muestra un ejemplo que incrementa y disminuye dos contadores como parte de la misma transacción:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Recuerde que las transacciones de Redis son transacciones diferentes en bases de datos relacionales. El método Execute simplemente pone en cola todos los comandos que componen la transacción para su ejecución, y si alguno de ellos está mal formado, se anula la transacción. Si todos los comandos se han puesto en cola correctamente, cada comando se ejecutará de forma asincrónica. Si cualquier comando genera un error, los demás continuarán procesándose. Si necesita comprobar que un comando ha finalizado correctamente, debe capturar los resultados del comando mediante la propiedad Result de la tarea correspondiente, como se muestra en el ejemplo anterior. La lectura de la propiedad Result se bloqueará hasta que se complete la tarea.

Para obtener más información, vea la página [Transacciones en Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) del sitio web de StackExchange.Redis.

Para realizar operaciones por lotes, puede usar la interfaz de IBatch de la biblioteca de StackExchange. La interfaz ofrece acceso a un conjunto similar de métodos como la interfaz de IDatabase con la excepción de que todos los métodos son asincrónicos. Crea un objeto IBatch mediante el método IDatabase.CreateBatch luego ejecuta el lote mediante el método IBatch.Execute, como se muestra en el ejemplo siguiente. Este código simplemente establece un valor de cadena y aumenta y disminuye los mismos contadores usados en el ejemplo anterior y muestra los resultados:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

Es importante comprender que, a diferencia de una transacción, si un comando de un lote genera error porque tiene un formato incorrecto, los otros comandos pueden seguir ejecutándose; el método IBatch.Execute no devuelve ninguna indicación de éxito o error.

### Realización operaciones de caché de fire and forget

Redis admite operaciones de fire and forget mediante marcadores de comando. En esta situación, el cliente simplemente inicia una operación, pero no tiene interés en el resultado y no espera a que se complete el comando. En el ejemplo siguiente se muestra cómo realizar el comando INCR como una operación fire and forget:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### Expiración automática de claves

Al almacenar un elemento en una caché de Redis, puede especificar un tiempo de espera tras el cual el elemento se quitará automáticamente de la memoria caché. También puede consultar de cuánto tiempo dispone una clave antes de expirar usando el comando `TTL`; este comando está disponible para las aplicaciones de StackExchange mediante el método IDatabase.KeyTimeToLive.

El siguiente fragmento de código muestra un ejemplo del establecimiento de un tiempo de expiración de 20 segundos en una clave y de la consulta de la vigencia restante de la clave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

También puede establecer el tiempo de expiración en una fecha y hora específicas mediante el comando EXPIRE, disponible en la biblioteca de StackExchange como el método KeyExpireAsync:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Sugerencia:_ puede quitar manualmente un elemento de la memoria caché usando el comando DEL, que está disponible a través de la biblioteca de StackExchange como el método IDatabase.KeyDeleteAsync.

### Uso de etiquetas para correlacionar elementos en caché

Un conjunto de Redis es una colección de varios elementos que comparten una sola clave. Puede crear un conjunto con el comando SADD. Puede recuperar los elementos de un conjunto mediante el comando SMEMBERS. La biblioteca de StackExchange implementa el comando SADD a través del método IDatabase.SetAddAsync y el comando SMEMBERS con el método IDatabase.SetMembersAsync. También puede combinar los conjuntos existentes para crear nuevos conjuntos con los comandos SDIFF (diferencia de conjuntos), SINTER (intersección de conjuntos) y SUNION (unión de conjuntos). La biblioteca de StackExchange unifica estas operaciones en el método IDatabase.SetCombineAsync; el primer parámetro para este método especifica la operación set que se va a realizar.

Los fragmentos de código siguientes muestran de qué manera los conjuntos pueden ser útiles para almacenar y recuperar rápidamente las colecciones de elementos relacionados. Este código usa el tipo de BlogPost descrito en la sección Implementación de las aplicaciones cliente de caché en Redis. Un objeto BlogPost contiene cuatro campos: un id., un título, una puntuación de clasificación y una colección de etiquetas. El primer fragmento de código siguiente muestra los datos de ejemplo que se usan para rellenar una lista C# de objetos BlogPost:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

Puede almacenar las etiquetas para cada objeto BlogPost como un conjunto en una caché en Redis y asociar cada conjunto con el id. de la BlogPost. Esto permite que una aplicación busque rápidamente todas las etiquetas que pertenecen a una entrada de blog específica. Para habilitar la búsqueda en la dirección opuesta y encontrar todas las entradas que comparten una etiqueta específica, puede crear otro conjunto que contiene la entradas de blog que hacen referencia al id. de etiqueta de la clave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Estas estructuras le permiten realizar muchas consultas comunes de manera muy eficaz. Por ejemplo, puede buscar y mostrar todas las etiquetas para la entrada de blog 1 de la siguiente manera:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Puede encontrar todas las etiquetas que son comunes a la entrada de blog 1 y a la entrada de blog 2 realizando una operación de intersección de conjuntos, de la siguiente manera:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Además, puede encontrar todas las entradas de blog que contienen una etiqueta específica:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### Búsqueda de elementos a los que se ha accedido recientemente

Un problema común requerido por muchas aplicaciones es encontrar los elementos a los que se ha accedido recientemente. Por ejemplo, puede que un sitio de blog desee mostrar información acerca de las entradas de blog leídas más recientemente. Puede implementar esta funcionalidad mediante una lista de Redis. Una lista de Redis contiene varios elementos que comparten la misma clave, pero la lista actúa como una cola de dos extremos. Puede insertar elementos en cualquier extremo de la lista mediante los comandos LPUSH (inserción izquierda) y RPUSH (inserción derecha). Puede recuperar elementos de cualquier extremo de la lista con los comandos LPOP y RPOP. También puede devolver un conjunto de elementos mediante los comandos LRANGE y RRANGE. Los fragmentos de código siguientes muestran cómo realizar estas operaciones mediante la biblioteca de StackExchange. Este código usa el tipo de BlogPost de los ejemplos anteriores. Cuando un usuario lee una entrada de blog, el título de la entrada de blog se inserta en una lista asociada a la clave "blog:recent\_posts" de la caché de Redis mediante el método IDatabase.ListLeftPushAsync:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

Conforme se leen más entradas del blog, sus títulos se insertan en la misma lista. La lista está ordenada por la secuencia en la que se han agregado; las entradas de blog leídas más recientemente se encuentran hacia el extremo izquierdo de la lista (si se lee la misma entrada de blog más de una vez, tendrá varias entradas en la lista). Puede mostrar los títulos de las entradas leídas más recientemente mediante el método IDatabase.ListRange. Este método toma la clave que contiene la lista, un punto de partida y un punto final. El código siguiente recupera los títulos de las 10 entradas de blog (elementos del 0 al 9) en el extremo que se encuentra más a la izquierda de la lista:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Tenga en cuenta que ListRangeAsync no quita elementos de la lista; para ello puede usar los métodos IDatabase.ListLeftPopAsync e IDatabase.ListRightPopAsync.

Para evitar que la lista crezca de manera indefinida, puede seleccionar elementos periódicamente recortando la lista. El fragmento de código siguiente elimina todos los elementos de la lista que se encuentran más a la izquierda excepto 5:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### Implementación de un panel de relleno

De forma predeterminada, los elementos de un conjunto no se mantienen en ningún orden específico. Puede crear un conjunto ordenado mediante el comando ZADD (el método IDatabase.SortedSetAdd de la biblioteca de StackExchange). Los elementos se ordenan mediante el uso de un valor numérico denominado puntuación que se proporciona como parámetro para el comando. El siguiente fragmento de código agrega el título de una entrada de blog a una lista ordenada. En el ejemplo, cada entrada de blog también tiene un campo de puntuación que contiene la clasificación de la entrada de blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Puede recuperar los títulos de entrada de blog y las puntuaciones en sentido ascendente de puntuación mediante el método IDatabase.SortedSetRangeByRankWithScores:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE] La biblioteca de StackExchange también ofrece el método IDatabase.SortedSetRangeByRankAsync que devuelve los datos en orden de puntuación, pero no devuelve las puntuaciones.

También puede recuperar los elementos en orden descendente de puntuaciones y limitar el número de elementos devueltos proporcionando parámetros adicionales al método IDatabase.SortedSetRangeByRankWithScoresAsync. En el ejemplo siguiente se muestran los títulos y las puntuaciones de las 10 entradas de blog clasificadas en primer lugar:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

En el ejemplo siguiente se usa el método IDatabase.SortedSetRangeByScoreWithScoresAsync que puede usar para limitar los elementos devueltos a aquellos que se encuentran dentro de un intervalo de puntuación determinada:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### Mensajería mediante canales

Además de actuar como una caché de datos, un servidor de Redis proporciona mensajería a través de un mecanismo de publicador y suscriptor de alto rendimiento. Las aplicaciones cliente pueden suscribirse a un canal y otros servicios o aplicaciones pueden publicar mensajes en el canal. Las aplicaciones de suscripción recibirán entonces estos mensajes y podrán procesarlos.

Para suscribirse al canal, Redis ofrece el comando SUBSCRIBE. Este comando espera el nombre de uno o más canales en los que la aplicación aceptará mensajes. La biblioteca de StackExchange incluye la interfaz de ISubscription que habilita a una aplicación de .NET Framework para suscribirse y publicar en los canales. Cree un objeto ISubscription mediante el método GetSubscriber de la conexión con el servidor de Redis y después escuche mensajes en un canal mediante el método SubscribeAsync de este objeto. En el ejemplo de código siguiente se muestra cómo suscribirse a un canal denominado "messages:blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

El primer parámetro para el método Subscribe es el nombre del canal. Este nombre sigue las mismas convenciones que la usada por las claves en la memoria caché y puede contener cualquier dato binario, aunque es conveniente usar cadenas relativamente cortas y descriptivas para ayudar a garantizar un buen rendimiento y mantenimiento. También debe tener en cuenta que el espacio de nombres usado por los canales es independiente del que usan las claves, por lo que puede tener canales y claves que tengan el mismo nombre, aunque esto puede hacer su código de aplicación más difícil de mantener.

El segundo parámetro es un delegado de acción. Este delegado se ejecuta de forma asincrónica siempre que aparece un mensaje nuevo en el canal. En este ejemplo simplemente se muestra el mensaje en la consola (el mensaje incluirá el título de una entrada de blog).

Para publicar en un canal, una aplicación puede usar el comando PUBLISH de Redis. La biblioteca de StackExchange ofrece el método IServer.PublishAsync para realizar esta operación. El siguiente fragmento de código muestra cómo publicar un mensaje en el canal "messages:blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Hay varios puntos que deben comprender acerca del mecanismo de publicación o suscripción:

- Varios suscriptores pueden suscribirse al mismo canal y todos recibirán los mensajes publicados en ese canal.
- Los suscriptores solo reciben mensajes que se han publicado después de haberse suscrito. Los canales no se almacenan en búfer y cuando se publica un mensaje, la infraestructura de Redis envía el mensaje a cada suscriptor y luego lo elimina.
- De forma predeterminada, los suscriptores reciben los mensajes en el orden en que se envían. En un sistema muy activo, con un gran número de mensajes y muchos suscriptores y publicadores, la entrega secuencial garantizada de mensajes puede ralentizar el rendimiento del sistema. Si cada mensaje es independiente y el orden es irrelevante, puede habilitar el procesamiento simultáneo por el sistema de Redis que puede ayudar a mejorar la capacidad de respuesta. Puede lograrlo en un cliente de StackExchange estableciendo la PreserveAsyncOrder de la conexión usada por el suscriptor en false: 
```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## Patrones relacionados y orientación

El siguiente patrón también puede ser pertinente para su escenario al implementar el almacenamiento caché en sus aplicaciones:

- [Patrón Cache-Aside](http://msdn.microsoft.com/library/dn589799.aspx): este patrón describe cómo cargar datos a petición en una memoria caché desde un almacén de datos. Este patrón también ayuda a mantener la coherencia entre los datos almacenados en la memoria caché y los datos del almacén de datos original.
- El [patrón de particionamiento](http://msdn.microsoft.com/library/dn589797.aspx) ofrece información sobre la implementación de la creación de particiones horizontal para ayudar a mejorar la escalabilidad al almacenar y tener acceso a grandes volúmenes de datos.

## Más información

- La página [Clase MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) del sitio web de Microsoft.
- La página [Documentación de la Caché en Redis de Azure](https://azure.microsoft.com/documentation/services/cache/) del sitio web de Microsoft.
- La página [Preguntas más frecuentes de Caché en Redis de Azure](redis-cache/cache-faq.md) del sitio web de Microsoft.
- La página [Modelo de configuración de](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) del sitio web de Microsoft.
- La página [Modelo asincrónico basado en tareas](http://msdn.microsoft.com/library/hh873175.aspx) del sitio web de Microsoft.
- La página [Canalizaciones y multiplexores](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) del repositorio de GitHub de StackExchange.Redis.
- La página [Persistencia de Redis](http://redis.io/topics/persistence) del sitio web de Redis.
- La [página de replicación](http://redis.io/topics/replication) del sitio web de Redis.
- La página [Tutorial del clúster de Redis](http://redis.io/topics/cluster-tutorial) del sitio web de Redis.
- La página [Creación de particiones: cómo dividir datos entre varias instancias de Redis](http://redis.io/topics/partitioning) del sitio web de Redis.
- La página [Uso de Redis como caché de LRU](http://redis.io/topics/lru-cache) del sitio web de Redis.
- La [página de transacciones](http://redis.io/topics/transactions) del sitio web de Redis.
- La página [Seguridad de Redis](http://redis.io/topics/security) del sitio web de Redis.
- La página [En torno a la Caché en Redis de Azure](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) del blog de Azure.
- La página [Ejecución de Redis en una máquina virtual Linux de CentOS en Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) en el sitio web de Microsoft.
- La página [Proveedor de estados de sesión de ASP.NET para Caché en Redis de Azure](redis-cache/cache-asp.net-session-state-provider.md) del sitio web de Microsoft.
- La página [Proveedor de caché de salida de ASP.NET para Caché en Redis de Azure](redis-cache/cache-asp.net-output-cache-provider.md) del sitio web de Microsoft.
- La página [Introducción a las abstracciones y los tipos de datos de Redis](http://redis.io/topics/data-types-intro) del sitio web de Redis.
- La página [Uso básico](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) página del sitio web de StackExchange.Redis.
- La página [Transacciones en Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) del repositorio de StackExchange.Redis.
- La [Guía de creación de particiones de datos](http://msdn.microsoft.com/library/dn589795.aspx) del sitio web de Microsoft.

<!---HONumber=AcomDC_0128_2016-->