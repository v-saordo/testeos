<properties
   pageTitle="Planeación de la capacidad para las aplicaciones de Service Fabric | Microsoft Azure"
   description="Se describe cómo identificar el número de nodos de proceso necesarios para una aplicación de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/09/2016"
   ms.author="subramar"/>


# Planeación de la capacidad para las aplicaciones de Service Fabric


En este documento se ofrece información sobre cómo estimar la cantidad de recursos (CPU, RAM o almacenamiento en disco) que necesita para ejecutar las aplicaciones de Azure Service Fabric. Es normal que las necesidades de recursos cambien con el paso del tiempo. Normalmente necesitará menos recursos mientras desarrolla o prueba su servicio y luego una cantidad mayor cuando pase a producción y su aplicación se vuelva cada vez más popular. A la hora de diseñar una aplicación, es mejor pensar en las necesidades a largo plazo y elegir ahora aquello que permita a su servicio escalarse fácilmente para satisfacer las altas demandas de los clientes.

 Al crear un clúster de Service Fabric, decida qué tipos de máquinas virtuales (VM) lo compondrán. Cada máquina virtual viene con una cantidad limitada de recursos en forma de CPU (núcleos y velocidad), ancho de banda de red, RAM y almacenamiento en disco. A medida que crezca su servicio con el tiempo, puede actualizar a máquinas virtuales que ofrezcan mayores recursos o agregar más máquinas virtuales al clúster. Por supuesto, para esto último, debe diseñar su servicio inicialmente para que pueda aprovechar las nuevas máquinas virtuales que se agreguen de forma dinámica al clúster.

Algunos servicios administran pocos datos o ninguno en las propias máquinas virtuales. Por lo tanto, la planificación de capacidad de estos servicios debe centrarse principalmente en el rendimiento. Esto significa que debe considerar detenidamente el rendimiento de los algoritmos del código y la CPU de las máquinas virtuales (núcleos y velocidad) necesarios para ejecutar los algoritmos. Además, debe tener en cuenta el ancho de banda de red y, incluso con qué frecuencia se producen transferencias de red y la cantidad de datos que se transfieren. Si el servicio debe funcionar bien a medida que aumente su uso, puede agregar más máquinas virtuales al clúster y equilibrar la carga de las solicitudes de red entre todas las máquinas virtuales.

En el caso de los servicios que administran gran cantidad de datos en las máquinas virtuales, la planeación de la capacidad debe centrarse principalmente en el tamaño. Esto significa que debe considerar detenidamente la capacidad de la RAM y del almacenamiento en disco de la máquina virtual. El sistema de administración de memoria virtual de Windows crea espacio en disco, como la RAM, para el código de aplicación. Esto permite a las aplicaciones usar más memoria que la que está físicamente disponible en la máquina virtual. Con solo más RAM aumenta el rendimiento, dado que la máquina virtual puede mantener más almacenamiento en disco en la memoria RAM. Cuando vaya elegir una máquina virtual, seleccione una cuyo espacio en disco pueda contener todos los datos que quiera y elija un tamaño de RAM que le permita acceder a los datos a la velocidad deseada. Si los datos del servicio aumentan con el tiempo, puede agregar más máquinas virtuales al cluster y distribuir los datos entre todas las máquinas virtuales.

## Determinación del número de nodos que necesita

La creación de particiones para su servicio le permite escalar horizontalmente los datos del servicio (consulte [Creación de particiones para Service Fabric](service-fabric-concepts-partitioning.md) para obtener más detalles al respecto). Cada partición debe ajustarse a una sola máquina virtual, pero se pueden colocar varias particiones (pequeñas) en una sola máquina virtual. De modo que, tener un gran número de pequeñas particiones le proporciona una mayor flexibilidad que tener un número pequeño de particiones más grandes. El inconveniente es que el hecho de tener muchas particiones aumenta la sobrecarga de Service Fabric y no podrá realizar operaciones de transacciones entre ellas. También, existe la posibilidad de un mayor tráfico de red si su código de servicio necesita acceder con frecuencia a fragmentos de datos que residen en particiones diferentes. Al diseñar su servicio, debe sopesar detenidamente estas ventajas e inconvenientes hasta dar con una estrategia de partición efectiva.

Supongamos que la aplicación tiene un único servicio con estado que tiene un tamaño de almacén que se espera que crezca a DB\_Size GB en un año. Desea agregar más aplicaciones (y particiones) porque observa un crecimiento más allá de ese año. Para obtener el tamaño total de la base de datos entre todas las réplicas, debemos considerar también el factor de replicación, RF, que determina el número de réplicas para el servicio (el tamaño total de la base de datos entre todas las réplicas es el factor de replicación multiplicado por DB\_Size). Node\_Size representa el espacio en disco o la memoria RAM por cada nodo que desea usar para el servicio. Para obtener el mejor rendimiento, querrá que el tamaño de la base de datos (DB\_Size) se ajuste a la memoria del clúster y deseará invertir en un tamaño de nodo (Node\_Size) que esté próximo a la capacidad de RAM de la máquina virtual que ha elegido. Si asigna un valor Node\_Size mayor que la capacidad de RAM, estará confiando en la paginación del sistema operativo. Por lo tanto, su rendimiento puede no ser óptimo, pero puede que sea suficiente para su servicio.

El número de nodos necesario para obtener el máximo rendimiento se puede calcular de la manera siguiente:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## Consideración sobre el crecimiento

Puede calcular el número de nodos en función del valor de DB\_Size de crecimiento que espera del servicio, además del valor DB\_Size a partir del que empieza. Después, aumente el número de nodos a medida que el servicio crece, a fin de evitar un aprovisionamiento excesivo del número de nodos. Sin embargo, el número de particiones debería basarse en el número de nodos necesarios al ejecutar su servicio con un crecimiento máximo.

También es una buena idea contar con algunas máquinas de repuesto (exceso de capacidad) en cualquier momento para así poder hacer frente a picos inesperados o posibles errores de la infraestructura (por ejemplo, si algunas máquinas virtuales dejan de funcionar). Aunque para determinar esto se pueden usar los picos esperados, un buen punto de partida sería reservar algunas máquinas virtuales adicionales (entre un 5 y un 10 % adicional).

Todo lo anterior se refiere a un único servicio con estado. Si tiene varios de estos servicios, tendrá que agregar también a la ecuación el tamaño de base de datos (DB\_Size) asociado a los demás servicios, o calcular el número de nodos por separado para cada servicio con estado. El servicio puede tener réplicas o particiones que no están equilibradas. Algunas particiones pueden disponer de más datos que otras; por tanto, consulte el [artículo sobre particiones basado en procedimientos recomendados](service-fabric-concepts-partitioning.md) para obtener más información. Sin embargo, la ecuación anterior es independiente del número de particiones y réplicas, ya que Service Fabric se asegura de que las réplicas se distribuyan entre los nodos de una manera optimizada.


## Uso de una hoja de cálculo para calcular costos

A continuación vamos a usar la fórmula anterior con números reales. En la [hoja de cálculo de ejemplo](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) se muestra cómo planear la capacidad de una aplicación que contiene tres tipos de objetos de datos. Para cada objeto, estimamos su tamaño y cuántos objetos esperamos que tenga. También hemos seleccionado el número de réplicas que queremos de cada tipo de objeto. La hoja de cálculo calcula la cantidad total de memoria que se almacenará en el clúster.

A continuación, escribimos un tamaño de máquina virtual y un costo mensual. En función del tamaño de la máquina virtual, la hoja de cálculo nos dice el número mínimo de particiones entre las que debe dividir los datos para que quepan físicamente en los nodos. Puede que quiera un número mayor de particiones para adaptarse a las necesidades específicas de cálculo y tráfico de red de su aplicación. La hoja de cálculo muestra que el número de particiones que administran los objetos del perfil de usuario ha aumentado de uno a seis.

Ahora, en función de esta información, la hoja de cálculo muestra que podría obtener físicamente todos los datos con las particiones y réplicas deseadas en un clúster de 26 nodos. Sin embargo, este clúster estaría muy cargado, así que puede que quiera nodos adicionales para hacer frente a posible actualizaciones y errores de los nodos. La hoja de cálculo también muestra que tener más de 57 nodos no proporciona ningún valor adicional, ya que tendría nodos vacíos. De nuevo, puede que desee de todas formas llegar a 57 nodos en previsión de actualizaciones o errores de los nodos. Puede ajustar la hoja de cálculo para que coincida con las necesidades específicas de su aplicación.

![Hoja de cálculo para calcular costos][Image1]



## Pasos siguientes

Consulte [Creación de particiones de los servicios de Service Fabric][10] para obtener más información al respecto.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

<!---HONumber=AcomDC_0211_2016-->