<properties
   pageTitle="Escalabilidad de los servicios de Service Fabric | Microsoft Azure"
   description="Describe cómo escalar servicios de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="aprameyr"/>

# Escalación de aplicaciones de Service Fabric
Azure Service Fabric facilita la creación de aplicaciones escalables, al equilibrar la carga de servicios, particiones y réplicas en todos los nodos de un clúster. Esto habilita la utilización máxima de recursos.

La escala alta para aplicaciones de Service Fabric se puede lograr de dos maneras:

1. Ajuste de escala en el nivel de partición

2. Ajuste de escala en el nivel de nombre de servicio

## Ajuste de escala en el nivel de partición
Service Fabric admite la creación de particiones de un servicio individual en varias particiones más pequeñas. En la [Información general de la creación de particiones](service-fabric-concepts-partitioning.md) se ofrece información sobre los tipos de esquemas de particiones que se admiten. Las réplicas de cada partición se extienden entre los nodos en un clúster. Considere la posibilidad de que un servicio que usa un esquema de particiones de intervalo con una clave baja de 0, una clave alta de 99 y 4 particiones. En un clúster de 3 nodos, el servicio podría estar dispuesto con cuatro réplicas que comparten los recursos en cada nodo, como se muestra aquí.

![Diseño de partición con tres nodos](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

El aumento del número de nodos permitirá a Service Fabric usar los recursos en los nuevos nodos moviendo algunas de las réplicas a nodos vacíos. Si se aumenta el número de nodos a cuatro, el servicio tendrá ahora tres réplicas ejecutándose en cada nodo (de particiones diferentes), lo que permite un mejor rendimiento y uso de recursos.

![Diseño de partición con cuatro nodos](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## Ajuste de escala en el nivel de nombre de servicio
Una instancia de servicio es una instancia específica de un nombre de aplicación y un nombre de tipo de servicio (consulte el [Ciclo de vida de aplicaciones de Service Fabric](service-fabric-application-lifecycle.md)). Durante la creación del servicio, especifique el esquema de partición (consulte [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)) que se va a usar.

El primer nivel de ajuste de escala es por nombre de servicio. Puede crear nuevas instancias de un servicio, con niveles diferentes de particiones, a medidas que sus instancias de servicio anteriores pasen a estar ocupadas. Esto permite que los consumidores del nuevo servicio usen las instancias de servicio menos ocupadas en lugar de las más ocupadas.

Una opción para aumentar la capacidad, o para aumentar o disminuir los recuentos de particiones es crear una nueva instancia de servicio con un nuevo esquema de particiones. Sin embargo, esto agrega complejidad, ya que los clientes de consumo necesitan saber cuándo y cómo usar un servicio con un nombre diferente.

### Escenario de ejemplo: fechas incrustadas
Un escenario posible sería usar la información de fecha como parte del nombre del servicio. Por ejemplo, podría usar la instancia de servicio con un nombre específico para todos los clientes que se incorporaron en 2013 y otro nombre para los clientes que se incorporaron en 2014. Este esquema de nombres permite el aumento mediante programación en los nombres según la fecha (conforme se aproxima el 2014, la instancia de servicio para el 2014 puede crearse a petición).

Sin embargo, este enfoque se basa en los clientes que usan la información de nombres específica de la aplicación que está fuera del ámbito de los conocimientos de Service Fabric.

- *Uso de una convención de nomenclatura*: en 2013, cuando la aplicación se activa, se crea un servicio denominado fabric:/app/service2013. Próximo al segundo trimestre de 2013 crea otro servicio denominado fabric:/app/service2014. Ambos servicios son del mismo tipo. En este enfoque, su cliente tendrá que tener una lógica para crear el nombre de servicio adecuado según el año.

- *Uso de un servicio de búsqueda*: otro patrón es proporcionar un servicio de búsqueda secundaria, que puede proporcionar el nombre del servicio para una clave que desee. El servicio de búsqueda puede crear a continuación nuevas instancias de servicio. El propio servicio de búsqueda no conserva los datos de la aplicación, sino únicamente los datos de los nombres de servicio que crea. Así pues, en el ejemplo anterior basado en el año, el cliente se pondría en contacto primero con el servicio de búsqueda para averiguar el nombre del servicio que administra datos para un año determinado y después usaría ese nombre de servicio para realizar la operación real. El resultado de la primera búsqueda se puede almacenar en caché.

## Pasos siguientes

Para información sobre los conceptos de Service Fabric, vea lo siguiente:

- [Disponibilidad de los servicios de Service Fabric](service-fabric-availability-services.md)

- [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)

- [Definición y administración del estado](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0128_2016-->