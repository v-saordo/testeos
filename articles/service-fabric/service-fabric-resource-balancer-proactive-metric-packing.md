<properties
   pageTitle="Empaquetado de métrica proactivo | Microsoft Azure"
   description="Introducción al uso automático del empaquetado de métricas proactivo en el equilibrador de recursos"
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

# Empaquetado de métrica proactivo

Una configuración común para el equilibrador de recursos de Azure Service Fabric es lograr una utilización igual en cada métrica de cada nodo. El equilibrador de recursos también es responsable de encontrar un lugar para servicios cuando llegan las nuevas solicitudes de servicio. Si no hay suficiente espacio libre para insertar nuevas réplicas de servicio (todas las réplicas de todas las particiones de servicio), el equilibrador de recursos intentará crear espacio moviendo cargas de trabajo existentes. Aunque esto es perfectamente normal, en función de lo lleno que esté el clúster y del nivel de fragmentación de la carga de trabajo, puede tardar tiempo.

Por ejemplo, si el clúster se usa razonablemente y si el cliente desea agregar un servicio nuevo con una carga predeterminada grande (por ejemplo, máxima capacidad de nodos para una o más métricas), es posible que el equilibrador de recursos necesite mover muchas réplicas para colocar el nuevo servicio. Además, si los servicios son grandes y con estado, la ejecución de los movimientos necesarios podría tardar algún tiempo ya que es necesario copiar los datos. Ambas cuestiones pueden ampliar el tiempo de creación de un nuevo servicio. Aunque generalmente los servicios deben ser tolerantes a tiempos de creación lentos en ocasiones, algunas cargas de trabajo son menos tolerantes y deben crearse tan pronto como sea posible. En un estado estable, el equilibrador de recursos debe asegurarse de que el clúster esté "desfragmentado" para proporcionar una mayor probabilidad de que haya suficiente espacio para nuevas cargas de trabajo.

El mecanismo de empaquetado de métrica proactivo (también conocido como desfragmentación) se ejecuta como parte de la fase de equilibrio en el equilibrador de recursos. El objetivo es minimizar el tiempo de creación de servicio empaquetando cargas de trabajo en menos nodos, en lugar de distribuirlas durante el equilibrado. Cuando se configura una métrica para la desfragmentación, el equilibrador de recursos tiene como objetivo lograr la desviación estándar media máxima, en lugar de la desviación media estándar mínima usada durante el equilibrado.

Con la desviación máxima, el equilibrador de recursos intentará colocar tantos servicios como sea posible en algunos nodos manteniendo vacíos tantos nodos como sea posible. Además de eso, una de las restricciones básicas a la hora de colocar los nuevos servicios es que las réplicas no pueden estar en el mismo dominio de actualización o dominio de error. Como el objetivo es ser capaz de agregar rápidamente nuevos servicios, equilibrador de recursos debe tener como objetivo tener una desviación estándar mínima de la distribución de la carga entre los dominios de actualización y los dominios de error (suma de las cargas de los servicios por dominio de actualización/dominio de error). El resultado será la misma cantidad de espacio disponible por dominio de actualización o dominio de errores. La desfragmentación también respeta todas las demás restricciones del sistema como afinidad, restricciones de posición y capacidad de métrica de nodo.

## Configuración de clúster de equilibrador de recursos
Dentro del manifiesto de clúster, los siguientes valores de configuración definen el comportamiento general de la función del empaquetado de métricas en el equilibrador de recursos:

### DefragmentationMetrics

Las métricas de desfragmentación son métricas que el equilibrador de recursos debe considerar para el empaquetado/desfragmentación proactivo. Deben especificarse todas las métricas configuradas en esta lista (al igual que en las listas de umbral de actividad y equilibrio). Si la métrica se especifica con el valor "true", se tratará como una métrica de desfragmentación. Con el valor "false" (o si no se especifica la métrica de esta lista), no se considerará la métrica para la desfragmentación.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

Los umbrales de equilibrio rigen el grado de fragmentación que puede llegar a alcanzar el clúster con respecto a una métrica concreta antes de que el equilibrador de recursos ejecute la fase de equilibrio (que hará la lógica de empaquetado de métrica). Si la métrica se considera como métrica de desfragmentación, el umbral de equilibrio es la relación mínima entre los nodos utilizados al máximo y mínimo usados por dominio de actualización o de error que el equilibrador de recursos permite que exista antes de efectuar la desfragmentación en el clúster. Si cualquier dominio de actualización o error tiene esta relación inferior al umbral, se iniciará la fase de desfragmentación.

En la siguiente ilustración se muestran dos ejemplos, donde el umbral de equilibrio de la métrica correspondiente es 10.

![Ejemplos de umbrales de equilibrio][Image1]

Tenga en cuenta que en este momento, el "uso" en un nodo no tiene en cuenta el tamaño del nodo según lo determinado por la capacidad del nodo. Tiene en cuenta solo el uso absoluto del que se ha informado actualmente en el nodo para la métrica especificada.

Si la métrica no se especifica, el valor predeterminado es 1. En ese caso, la desfragmentación se realizará hasta que el clúster tenga al menos un nodo vacío en cada dominio de actualización y en cada dominio de error.

El valor 0 significa no tener en cuenta esta métrica durante la fase de equilibrio (tanto si se considera para la desfragmentación como si no).

El ejemplo de código siguiente muestra que los umbrales de equilibrio de las métricas se configuran en cada métrica individual a través del elemento FabricSettings situado dentro del manifiesto de clúster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Para obtener más información: [Arquitectura de equilibrador de recursos](service-fabric-resource-balancer-architecture.md)

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_1223_2015-->