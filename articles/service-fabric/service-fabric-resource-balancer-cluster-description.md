<properties
   pageTitle="Descripción del clúster del equilibrador de recursos| Microsoft Azure"
   description="Descripción de un clúster de Service Fabric mediante la especificación de dominios de error, dominios de actualización, propiedades de nodo y capacidades de nodo en el equilibrador de recursos."
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Descripción de un clúster de Service Fabric

El equilibrador de recursos de Azure Service Fabric proporciona varios mecanismos para describir un clúster. Durante el tiempo de ejecución, el equilibrador de recursos utiliza estos fragmentos de información para realizar servicios de manera que se garantice una alta disponibilidad de los servicios que se ejecutan en el clúster mientras se asegura asimismo la máxima utilización de los recursos del clúster. Las funciones del equilibrador de recursos que describen un clúster son dominios de error, dominios de actualización, propiedades de nodo y capacidades de nodo. Además, el equilibrador de recursos tiene algunas opciones de configuración que pueden ajustar su rendimiento.

## Conceptos clave

### Dominios de error

Dominios de error permiten a los administradores de clúster definir los nodos físicos que es probable que experimenten un fallo al mismo tiempo debido a las dependencias físicas compartidas como la energía y orígenes de redes. Los dominios de error suelen representar jerarquías que están relacionadas con estas dependencias compartidas, con más nodos con más probabilidades de fallar conjuntamente desde un punto superior del árbol de dominios de error. La siguiente ilustración muestra varios nodos que se organizan a través de dominios de error jerárquicos en el orden de blade, estantería y centro de datos.

![Nodos organizados a través de dominios de error][Image1]

 Durante el tiempo de ejecución, el Administrador de recursos de Azure considera los dominios de error del clúster e intenta propagar las réplicas de un servicio determinado para que estén en dominios de error independientes. Este proceso ayuda a garantizar, en caso de error de cualquier dominio de error, que no se ponga en peligro la disponibilidad del servicio y su estado. La siguiente ilustración muestra las réplicas de un servicio que se reparten en varios dominios de error, incluso aunque haya espacio suficiente para concentrarlos en tan solo uno o dos dominios.

![Réplicas de un servicio que se distribuye en dominios de error][Image2]

Los dominios de error se configuran en el manifiesto de clúster. Cada nodo se define como dentro de un dominio de error concreto. Durante el tiempo de ejecución, el Administrador de recursos combina los informes de todos los nodos para desarrollar una descripción general completa de todos los dominios de error del sistema.

### Dominios de actualización

Los dominios de actualización son otro fragmento de información consumido por el Administrador de recursos. Los dominios de actualización, como los dominios de error, describen conjuntos de nodos que se cierran para las actualizaciones aproximadamente al mismo tiempo. Los dominios de actualización no son jerárquicos y pueden considerarse etiquetas.

Mientras que los dominios de error se definen mediante la distribución física de los nodos del clúster, los dominios de actualización están determinados por los administradores de clústeres que se basan en las directivas. Las directivas están relacionadas con las actualizaciones del clúster. Cuantos más dominios de actualización existan, más granulares serán las actualizaciones gracias a la limitación del impacto en el clúster y los servicios en ejecución y evitando que los errores que afecten a un gran número de servicios. En función de otras directivas, como el tiempo que debe esperar Service Fabric después de actualizar un dominio antes de que Service Fabric se mueva al siguiente dominio, la presencia de más dominios de actualización también puede aumentar la cantidad de tiempo que se tarda en completar una actualización en el clúster.

Por estas razones, el Administrador de recursos recopila información de dominio de actualización y propaga réplicas entre los dominios de actualización de un clúster, como los dominios de error. Es posible que los dominios de actualización se correspondan o no uno a uno con los dominios de error, pero normalmente no se espera que se correspondan uno a uno. La siguiente ilustración muestra una capa de varios dominios de actualización encima de los dominios de error definidos previamente. El Administrador de recursos todavía propaga las réplicas entre dominios de modo que no se concentren réplicas en ningún dominio de error o actualización para garantizar una alta disponibilidad del servicio, a pesar de las actualizaciones o errores del clúster.

![Dominios de actualización][Image3]

Los dominios de actualización y de error se configuran como parte de la definición del nodo en el manifiesto de clúster, tal y como se muestra a continuación:

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
> [AZURE.NOTE]En las implementaciones de Azure, los dominios de error y los de actualización son asignados por Azure. Por lo tanto, la definición de los nodos y roles dentro de la opción de infraestructura de Azure no incluye la información del dominio de errores ni de actualizaciones.

### Propiedades del nodo
Las propiedades del nodo son pares de clave/valor definidos por el usuario que proporcionan metadatos adicionales para un nodo determinado. Entre los ejemplos de propiedades de nodos se incluye si el nodo tiene una unidad de disco duro o una tarjeta de vídeo, el número de ejes de su unidad de disco duro, núcleos y otras propiedades físicas.

Las propiedades del nodo también se pueden utilizar para especificar propiedades más abstractas para ayudar a efectuar decisiones sobre directivas. Por ejemplo, se puede asignar un "color" a varios nodos situados dentro de un clúster como forma de segmentar el clúster en secciones diferentes. El ejemplo de código siguiente muestra que se definen propiedades de nodo para nodos mediante el manifiesto de clúster como parte de las definiciones de tipos de nodo. Las propiedades de nodo pueden aplicarse a continuación a varios nodos del clúster.

Las propiedades de ubicación NodeName, NodeType, FaultDomain y UpgradeDomain tienen valores predeterminados. Service Fabric le proporciona valores predeterminados automáticamente para que pueda usarlos al crear el servicio. Los usuarios no deben especificar sus propias propiedades de colocación con estos mismos nombres.

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

Durante el tiempo de ejecución, el equilibrador de recursos usa la información de la propiedad del nodo para asegurarse de que los servicios que requieren las funcionalidades específicas se coloquen en los nodos correspondientes.

### Capacidades de nodo
Las capacidades de nodo son pares de clave/valor que definen el nombre y la cantidad de un recurso concreto que un nodo concreto tiene disponible para su consumo. El ejemplo de código siguiente muestra a un nodo que tiene capacidad para una métrica llamada "MemoryInMB" y que tiene 2048 MB de memoria disponible de forma predeterminada. Las capacidades se definen mediante el manifiesto de clúster, como las propiedades del nodo.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![Capacidad de nodo][Image4]

Debido a que los servicios que se ejecutan en un nodo pueden actualizar sus requisitos de capacidad a través de la carga de informes, el equilibrador de recursos también comprueba periódicamente si un nodo está dentro de los límites o sobrepasa la capacidad de cualquiera de sus métricas. Si es así, el equilibrador de recursos puede mover servicios a nodos menos cargados para reducir la contención de recursos y para mejorar el rendimiento y el uso global.

Tenga en cuenta que, aunque también se pueda enumerar una métrica determinada en la sección de propiedades del tipo de nodo, es mejor enumerarla como una capacidad si se trata de una propiedad del nodo que se puede consumir durante el tiempo de ejecución. Enumerarlo además como una propiedad permite a un servicio que depende de "requisitos mínimos de hardware" consultar el nodo con restricciones de posición. Esto podría ser necesario si el recurso es consumido por otros servicios durante el tiempo de ejecución. Asimismo es recomendable también incluirlo como una capacidad para que el equilibrador de recursos pueda realizar más acciones.

Cuando se crean nuevos servicios, el equilibrador de recursos de Service Fabric usa la información sobre la capacidad de los nodos existentes y el consumo de los servicios existentes para determinar si hay suficiente capacidad disponible para colocar todo el servicio nuevo. Si no hay capacidad suficiente, se rechaza la solicitud de crear servicio con un mensaje de error que indica un problema de capacidad.


### Configuraciones del equilibrador de recursos

Dentro del manifiesto de clúster, los siguientes valores de configuración definen el comportamiento general del equilibrador de recursos:

Los *umbrales de equilibrio* controlan el nivel de desequilibrio que puede alcanzar el clúster con respecto a una métrica concreta antes de que reaccione el equilibrador de recursos. Un umbral de equilibrio es la relación máxima entre los nodos más y menos utilizados que el equilibrador de recursos permite que existan antes de reequilibrar los clústeres.

En la siguiente ilustración se muestran dos ejemplos, donde el umbral de equilibrio de la métrica correspondiente es 10.

![Umbral de equilibrio][Image5]

Tenga en cuenta que en este momento, el "uso" en un nodo no tiene en cuenta el tamaño del nodo según lo determinado por la capacidad del nodo. Hace referencia al uso absoluto del que se ha informado actualmente en el nodo para la métrica especificada.

El siguiente ejemplo de código muestra que los umbrales de equilibrio de las métricas se configuran en cada métrica individual a través del elemento FabricSettings situado dentro del manifiesto de clúster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

Los *umbrales de actividad* actúan como una puerta sobre la frecuencia de ejecución del equilibrador de recursos mediante la limitación de los casos en que el equilibrador de recursos reacciona a cuando hay una cantidad absoluta de carga significativa. De este modo, si el clúster no está muy ocupado para una métrica concreta, el equilibrador de recursos no se ejecuta incluso si esa pequeña cantidad de métrica está muy desequilibrada dentro del clúster. Esta medida evita malgastar recursos reequilibrando el clúster para obtener una ganancia escasa. En la siguiente ilustración se muestra que el umbral de equilibrio de la métrica es 4 y que el umbral de actividad es 1536.

![Umbral de actividad][Image6]

Tenga en cuenta también que se deben superar los umbrales de actividad y equilibrio de los umbrales para que la misma métrica haga que el equilibrador de recursos se ejecute. La activación de una de las dos métricas independientes no hace que el equilibrador de recursos se ejecute.

El siguiente ejemplo de código muestra que, al igual que los umbrales de equilibrio, los umbrales de actividad se configuran por métrica a través del elemento FabricSettings dentro del manifiesto de clúster.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval: controla la frecuencia con la que el equilibrador de recursos examina la información que debe comprobar para detectar infracciones de restricciones. Las infracciones de restricciones incluyen restricciones de posición que no se cumplen, servicios con un número de instancias o réplicas inferior al requerido, nodos que superan la capacidad de algunas métricas, errores de sobrecarga o dominios de actualización, o desequilibrios de clúster. El equilibrador de recursos identifica las infracciones de restricciones mediante el equilibrio y los umbrales de actividad y su vista actual de la carga en los nodos del clúster. El intervalo de actualización se define en segundos, y el valor predeterminado es 1. La frecuencia de la comprobación de restricción y la colocación pueden controlarse alternativamente mediante dos nuevos intervalos (MinConstraintCheckInterval y MinPlacementInterval). Si se definen los parámetros nuevos, PLBRefreshInterval no se usará y no se podrá definir.

- MinConstraintCheckInterval: controla la frecuencia con la que el equilibrador de recursos examina la información que debe comprobar para detectar infracciones de restricciones. Las infracciones de restricciones incluyen restricciones de posición que no se cumplen, servicios con un número de instancias o réplicas inferior al requerido, nodos que superan la capacidad de algunas métricas, errores de sobrecarga o dominios de actualización, o desequilibrios de clúster. El equilibrador de recursos identifica las infracciones de restricciones mediante el equilibrio y los umbrales de actividad y su vista actual de la carga en los nodos del clúster. El intervalo de comprobación de restricción se define en segundos. Si no se define el intervalo de comprobación de restricción, su valor predeterminado será igual al valor de PLBRefreshInterval. (Ambos valores no pueden especificarse al mismo tiempo).

- MinPlacementInterval: controla la frecuencia con que el equilibrador de recursos comprueba si hay nuevas instancias o réplicas que deben ubicarse e intenta ubicarlas. El intervalo de selección de ubicación se define en segundos. Si no se define el intervalo de colocación, su valor predeterminado será igual al valor de PLBRefreshInterval. (Ambos valores no pueden especificarse al mismo tiempo).

- MinLoadBalancingInterval: establece la cantidad mínima de tiempo entre ciclos de equilibrio de recursos. Si el equilibrador de recursos llevó a cabo una acción según la información del análisis que se realiza durante el último intervalo de PLBRefreshInterval, no se vuelve a ejecutar durante al menos la misma cantidad de tiempo. Se define en segundos, y el valor predeterminado es 5.

Tenga en cuenta que estos valores son exigentes, pero ese equilibrio de carga global solo se produce en el clúster si se alcanzan los umbrales de equilibrio y la actividad de una métrica determinada. El siguiente ejemplo de código muestra que si no es necesario un equilibrio de carga activo y preciso para un clúster determinado, estos valores se pueden volver menos exigentes a través de la configuración dentro del elemento FabricSettings. En esta configuración de ejemplo, el tiempo mínimo entre dos comprobaciones de restricciones es de 10 segundos y el tiempo mínimo para la ubicación es de 5 segundos, mientras que el equilibrio de carga se producirá cada 5 minutos. Tenga en cuenta que PLBRefreshInterval no está definido en este caso.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

En la segunda configuración de ejemplo siguiente, tenemos PLBRefreshInterval y MinLoadBalancingInterval definido. Debido a que PLBRefreshInterval está establecido en 2 segundos, MinPlacementInterval y MinConstraintCheckInterval establecerán su valor en 2 segundos también.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Para obtener más información: [Arquitectura de equilibrador de recursos](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png

<!---HONumber=AcomDC_1223_2015-->