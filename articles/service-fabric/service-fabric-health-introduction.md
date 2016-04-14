<properties
   pageTitle="Supervisión del mantenimiento en Service Fabric | Microsoft Azure"
   description="Una introducción al modelo de supervisión del mantenimiento de Azure Service Fabric, que proporciona supervisión del clúster y de sus aplicaciones y servicios."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# Introducción a la supervisión del mantenimiento de Service Fabric
Azure Service Fabric presenta un modelo de mantenimiento que proporciona informes y datos de evaluación del mantenimiento completos, flexibles y extensibles. Dicho modelo incluye supervisión casi en tiempo real del estado tanto del clúster como de los servicios que se ejecutan en él. Puede obtener con facilidad información sobre el mantenimiento y realizar las acciones pertinentes para corregir posibles problemas antes de que se propaguen y causen interrupciones masivas. En el modelo típico, los servicios envían informes basados en sus vistas locales, y dicha información se agrega para proporcionar una vista general del nivel del clúster.

Los componentes de Service Fabric usan este modelo de estado para informar de su estado actual. Este mismo mecanismo puede usar para informar del mantenimiento de las aplicaciones. La calidad y exhaustividad de los informes de mantenimiento específicos para sus condiciones personalizadas determinarán la facilidad con la que podrá detectar y corregir los problemas de la aplicación en ejecución.

> [AZURE.NOTE] El subsistema de mantenimiento se inició para cubrir la necesidad de actualizaciones supervisadas. Service Fabric proporciona actualizaciones supervisadas que saben cómo actualizar un clúster o una aplicación sin tiempo de inactividad, con una intervención del usuario mínima o nula, y con disponibilidad completa no solo del clúster, sino también de la aplicación. Para ello, la actualización comprueba el mantenimiento según las directivas de actualización configuradas y solo permite que la actualización continúe cuando dicho mantenimiento respeta los umbrales deseados. De lo contrario, la actualización se revierte automáticamente o se pausa para ofrecer a los administradores una oportunidad de solucionar los problemas. Para más información sobre la actualización de aplicaciones, consulte [este artículo](service-fabric-application-upgrade.md).

## Almacén de estado
El Almacén de estado conserva la información relacionada con el mantenimiento de las entidades del clúster, con el fin de facilitar su recuperación y evaluación. Se implementa como un servicio con estado persistente de Service Fabric para garantizar alta disponibilidad y escalabilidad. El Almacén de estado forma parte de la aplicación **fabric:/System** y está disponible en cuanto el clúster entra en funcionamiento.

## Jerarquía y entidades de mantenimiento
Las entidades de mantenimiento se organizan en una jerarquía lógica que captura las interacciones y dependencias entre entidades diferentes. El Almacén de estado crea automáticamente tanto las entidades como la jerarquía en función de los informes que recibe de los componentes de Service Fabric.

Las entidades de mantenimiento son un reflejo las entidades de Service Fabric (por ejemplo, la **entidad de la aplicación de mantenimiento** coincide con una instancia de aplicación implementada en el clúster, mientras que la **entidad del nodo de mantenimiento** coincide con un nodo de clúster de Service Fabric). La jerarquía de mantenimiento captura las interacciones de las entidades del sistema y es la base para la evaluación avanzada del mantenimiento. Para obtener información sobre los conceptos clave de Service Fabric, consulte [Introducción técnica a Service Fabric](service-fabric-technical-overview.md). Para más información sobre la aplicación, consulte [Modelar una aplicación en Service Fabric](service-fabric-application-model.md).

Las entidades de mantenimiento y la jerarquía permiten depurar, supervisar y realizar informes tanto del clúster como de las aplicaciones. El modelo de mantenimiento proporciona una representación *pormenorizada* y precisa del mantenimiento de las numerosas piezas móviles del clúster.

![Entidades de mantenimiento.][1] Las entidades de mantenimiento, organizadas en una jerarquía basada en relaciones entre elementos primarios y secundarios.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Las entidades de mantenimiento son:

- **Clúster**. Representa el estado de un clúster de Service Fabric. Los informes de mantenimiento del clúster describen las condiciones que afectan a todo el clúster y no se pueden restringir a uno o más elementos secundarios incorrectos. Entre los ejemplos se incluye el cerebro de la división del clúster debida problema de creación de particiones de red o de comunicación.

- **Nodo**. Representa el estado de un nodo de Service Fabric. Los informes de mantenimiento del nodo describen las condiciones que afectan a la funcionalidad del nodo. Normalmente afectan a todas las entidades implementadas que se ejecutan en él. Entre los ejemplos se incluyen que un nodo se encuentre fuera del espacio del disco (u otra propiedad de toda la máquina como la memoria y las conexiones) que un nodo esté inactivo. La entidad del nodo se identifica por el nombre del nodo (cadena).

- **Aplicación**. Representa el estado de una instancia de aplicación que se ejecuta en el clúster. Los informes de mantenimiento de aplicación describen las condiciones que afectan el mantenimiento general de la aplicación. No se pueden restringir a elementos secundarios individuales (servicios o aplicaciones implementadas). Entre los ejemplos se incluye la interacción de un extremo a otro entre los diferentes servicios de la aplicación. La entidad de la aplicación se identifica mediante el nombre de la aplicación (identificador URI).

- **Servicio**. Representa el estado de un servicio que se ejecuta en el clúster. Los informes de mantenimiento de servicio describen las condiciones que afectan al mantenimiento general del servicio y no se pueden restringir a una partición o una réplica. Entre los ejemplos se incluye una configuración del servicio (como un puerto o un recurso compartido de archivos externo) que causa problemas en todas las particiones. La entidad de servicio se identifica mediante el nombre del servicio (identificador URI).

- **Partición**. Representa el estado de una partición de servicio. Los informes de mantenimiento de la partición describen las condiciones que afectan al conjunto completo de réplicas. Entre los ejemplos se incluye cuando el número de réplicas es inferior al recuento de destino y cuando la partición tiene una pérdida de cuórum. La entidad de la partición se identifica mediante el identificador de la partición (GUID).

- **Réplica**. Representa el estado de una réplica de servicio con estado o una instancia de servicio sin estado. Es la unidad guardián más pequeña y los componentes del sistema puede informar para una aplicación. En los servicios con estado, entre los ejemplos se incluye que una réplica principal genere un informe cuando no pueda replicar operaciones a replicas secundarios o cuando la replicación no se efectúa al ritmo esperado. Además, se puede notificar una instancia sin estado si se está quedando sin recursos o tiene problemas de conectividad. La entidad de réplica se identifica mediante el identificador de la partición (GUID) y el identificador de la réplica o instancia (largo).

- **DeployedApplication**. Representa el estado de a *aplicación que se ejecuta en un nodo*. Los informes de mantenimiento de la aplicación implementada describen las condiciones específicas de la aplicación en el nodo que no se pueden restringir a paquetes de servicio implementados en el mismo nodo. Entre los ejemplos se incluye cuando el paquete de aplicación no se puede descargar en ese nodo y aparece algún problema al configurar las entidades de seguridad de la aplicación en el nodo. La aplicación implementada se identifica mediante el nombre de la aplicación (identificador URI) y el nombre del nodo (cadena).

- **DeployedServicePackage**. Representa el estado de un paquete de servicio de una aplicación que se ejecuta en un nodo del clúster. Describe las condiciones específicas de un paquete de servicio que no afectan a los demás paquetes de servicio del mismo nodo para la misma aplicación. Entre los ejemplos se incluye un paquete de código del paquete de servicio que no se puede iniciar y un paquete de configuración que no se puede leer. El paquete de servicio implementado se identifica mediante el nombre de la aplicación (identificador URI), el nombre del nodo (cadena) y el nombre del manifiesto de servicio (cadena).

La granularidad del modelo de mantenimiento facilita la detección y corrección de problemas. Por ejemplo, si un servicio no responde, es viable informar de que la instancia de la aplicación es incorrecta. Sin embargo, esto no es ideal, ya que es posible que el problema no afecte a todos los servicios de la aplicación. El informe se debe aplicar al servicio incorrecto o a una partición secundaria concreta, en caso de que otra información apunte a dicha partición. Los datos aparecerán automáticamente en la jerarquía y una partición incorrecta se hará podrá ver en los niveles de servicio y de aplicación. Esto ayudará a identificar y resolver más rápidamente la causa principal del problema.

La jerarquía del mantenimiento se compone de relaciones entre elementos primarios y secundarios. Los clústeres se componen de nodos y aplicaciones. Las aplicaciones tienen servicios y aplicaciones implementadas. Las aplicaciones implementadas han implementado paquetes de servicio. Los servicios tienen particiones y cada partición tiene una o más réplicas. Hay una relación especial entre los nodos y las entidades implementadas. Si un nodo es incorrecto, tal como lo notifica su componente de sistema de autoridad (servicio Administrador de conmutación por error), afectará a las aplicaciones implementadas, a los paquetes de servicios y a las aplicaciones implementadas en él.

La jerarquía de estado representa el estado más reciente del sistema basándose en los informes de mantenimiento más recientes, que es información casi en tiempo real. Los guardianes internos y externos pueden generar informes sobre las mismas entidades según la lógica específica de la aplicación o las condiciones supervisadas personalizadas. Los informes de usuario coexisten con los informes del sistema.

Al diseñar un servicio en la nube grande, el tiempo que se invierte en la planeación de cómo informar y responder al mantenimiento puede facilitar la depuración, la supervisión y el posterior manejo del servicio.

## Estados de mantenimiento
Service Fabric usa tres estados de mantenimiento para describir si una entidad es correcta o no: Correcto, Advertencia y Error. Todos los informes que se envíen al Almacén de estado deben especificar uno de estos estados. El resultado de la evaluación de estado es uno de estos estados.

Los estados posibles de mantenimiento son:

- **Correcto**. La entidad es correcta. No hay ningún problema conocido notificado en ella o en sus elementos secundarios (en los casos aplicables).

- **Advertencia**. La entidad tiene algunos problemas, pero aún no es incorrecta (por ejemplo, ningún retraso inesperado causa problemas funcionales). En algunos casos, la condición de advertencia puede corregirse por sí sola sin ninguna intervención especial y resulta útil para proporcionar visibilidad en lo que está ocurriendo. En otros casos, la condición de advertencia puede dar lugar a un problema grave si no interviene el usuario.

- **Error**. La entidad es incorrecta. Se debe realizar una acción para corregir el estado de la entidad, ya que no puede funcionar correctamente.

- **Desconocido**. La entidad no existe en el Almacén de estado. Este resultado puede obtenerse a partir de las consultas distribuidas que combinan los resultados de varios componentes. Estas pueden incluir la consulta para obtener la lista de nodos de Service Fabric, que va a **FailoverManager** y **HealthManager**, o la consulta para obtener la lista de aplicaciones, que va a **ClusterManager** y **HealthManager**. Estas consultas combinan los resultados de varios componentes del sistema. Si otro componente del sistema tiene una entidad que aún no ha alcanzado el Almacén de estado o que se ha eliminado del Almacén de estado, la consulta combinada rellenará el resultado de mantenimiento con el estado de mantenimiento Desconocido.

## Directivas de mantenimiento
El Almacén de estado aplica directivas de mantenimiento para determinar si una entidad es correcta en función de sus informes y sus elementos secundarios.

> [AZURE.NOTE] Las directivas de mantenimiento se pueden especificar en el manifiesto de clúster (para la evaluación del mantenimiento del clúster y el nodo) o en el manifiesto de aplicación (para la evaluación de la aplicación y cualquiera de sus elementos secundarios). Las solicitudes de evaluación del mantenimiento también pueden pasar directivas de evaluación de mantenimiento personalizadas, que solo se usarán para dicha evaluación.

De manera predeterminada, Service Fabric aplica reglas estrictas (todo debe estar correcto) a la relación jerárquica entre elementos primarios y secundarios. Si uno solo de los elementos secundarios tiene un evento incorrecto, el elemento primario se considera incorrecto.

### Directiva de mantenimiento de clúster
La directiva de mantenimiento de clúster se usa para evaluar el estado del mantenimiento del clúster y los estados de mantenimiento del nodo. La directiva se puede definir en el manifiesto de clúster. Si no está presente, se utiliza la directiva predeterminada (cero errores tolerados). La directiva de mantenimiento de clúster contiene:

- **ConsiderWarningAsError**. Especifica si los informes de mantenimiento de advertencia se tratan como errores durante la evaluación del mantenimiento. Valor predeterminado: false.

- **MaxPercentUnhealthyApplications**. Especifica el porcentaje máximo tolerado de aplicaciones que pueden ser incorrectas antes de que el clúster se considere erróneo.

- **MaxPercentUnhealthyNodes**. Especifica el porcentaje máximo tolerado de nodos que pueden ser incorrectas antes de que el clúster se considere erróneo. En los clústeres grandes, siempre habrá nodos inactivos o inoperativos debido a reparaciones, por lo que este porcentaje debe configurarse para tolerar ese hecho.

Lo siguiente es un extracto de un manifiesto de clúster:

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="0" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
  </Section>
</FabricSettings>
```

### Directiva de mantenimiento de aplicación
La directiva de mantenimiento de aplicación describe la forma en que se realiza la evaluación de la agregación de los eventos y de los estados de los elementos secundarios en las aplicaciones y sus elementos secundarios. Se puede definir en el manifiesto de la aplicación, **ApplicationManifest.xml**, del paquete de aplicación. Si no se especifican directivas, Service Fabric asume que la entidad es incorrecta si tiene un informe de mantenimiento o un elemento secundario en los estados de mantenimiento Advertencia o Error. Las directivas configurables son:

- **ConsiderWarningAsError**. Especifica si los informes de mantenimiento de advertencia se tratan como errores durante la evaluación del mantenimiento. Valor predeterminado: false.

- **MaxPercentUnhealthyDeployedApplications**. Especifica el porcentaje máximo tolerado de aplicaciones implementadas que pueden ser incorrectas antes de que la aplicación se considere errónea. Dicho porcentaje se calcula dividiendo el número de aplicaciones implementadas incorrectas entre el número de nodos en que las aplicaciones están implementadas actualmente en el clúster. El cálculo se redondea hacia arriba para tolerar un error en números reducidos de nodos. Porcentaje de predeterminado: cero.

- **DefaultServiceTypeHealthPolicy**. Especifica la directiva de mantenimiento del tipo de servicio predeterminada, que reemplazará la directiva de mantenimiento predeterminada para todos los tipos de servicio de la aplicación.

- **ServiceTypeHealthPolicyMap**. Proporciona un mapa de las directivas de mantenimiento de servicios por tipo de servicio. Reemplazan las directivas de mantenimiento del tipo de servicio predeterminado en todos los tipos de servicio especificados. Por ejemplo, en una aplicación que contiene un tipo de servicio de pasarela sin estado y un tipo de servicio de motor con estado, las directivas de mantenimiento de los servicios con estado y sin estado se pueden configurar de forma diferente. Si especifica una directiva por tipo de servicio, podrá obtener un control más pormenorizado del mantenimiento del servicio.

### Directiva de mantenimiento de tipo de servicio
La directiva de mantenimiento de tipo de servicio especifica cómo evaluar y agregar los elementos secundarios de los servicios. La directiva contiene:

- **MaxPercentUnhealthyPartitionsPerService**. Especifica el porcentaje máximo tolerado de particiones incorrectas antes de que un servicio se considere incorrecto. Porcentaje de predeterminado: cero.

- **MaxPercentUnhealthyReplicasPerPartition**. Especifica el porcentaje máximo tolerado de réplicas incorrectas antes de que una partición se considere incorrecta. Porcentaje de predeterminado: cero.

- **MaxPercentUnhealthyServices**. Especifica el porcentaje máximo tolerado de servicios incorrectos antes de que la aplicación se considere incorrecta. Porcentaje de predeterminado: cero.

Lo siguiente es un extracto de un manifiesto de aplicación:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## Evaluación de estado
Los usuarios y servicios automatizados pueden evaluar el mantenimiento de cualquier entidad en cualquier momento. Para evaluar el mantenimiento de una entidad, el Almacén de estado agrega todos los informes de mantenimiento de la entidad y evalúa todos sus elementos secundarios (si procede). El algoritmo de agregación de mantenimiento usa las directivas de mantenimiento que especifican cómo se evalúan los informes de mantenimiento y cómo se agregan los estados de mantenimiento de los elementos secundarios (si procede).

### Agregación de informes de mantenimiento
Una entidad puede tener varios informes de mantenimiento enviados por informadores diferentes (componentes del sistema o guardianes) en diferentes propiedades. La agregación usa las directivas de mantenimiento asociadas, en concreto, el miembro ConsiderWarningAsError de la aplicación o la directiva de mantenimiento del clúster. Así se especifica cómo evaluar las advertencias.

El estado de mantenimiento agregado se desencadena por los *peores* informes de mantenimiento sobre la entidad. Si hay al menos un informe de mantenimiento que indica un error, el estado de mantenimiento agregado será Error.

![Agregación de informe de mantenimiento a informe de errores.][2]

Un informe de mantenimiento que indica un error desencadena que la entidad de mantenimiento pase al estado Error.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Si no hay informes de error, pero aparecen una o varias advertencias, el estado de mantenimiento agregado es Advertencia o Error, en función de la marca de la directiva ConsiderWarningAsError.

![Agregación de informe de mantenimiento a informe de advertencias y ConsiderWarningAsError establecido en false.][3]

Agregación del informe de mantenimiento con el informe de advertencia y ConsiderWarningAsError establecido en false (valor predeterminado).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Agregación de mantenimiento de elementos secundarios
El estado de mantenimiento agregado de una entidad refleja los estados de mantenimiento de los elementos secundarios (si procede). El algoritmo para la agregación de estados de mantenimiento de elementos secundarios usa las directivas de mantenimiento aplicables según el tipo de entidad.

![Agregación de mantenimiento de entidades secundarias.][4]

Agregación de elementos secundarios basada en directivas de mantenimiento.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Después de que el Almacén de estado haya evaluado todos los elementos secundarios, agrega sus estados de mantenimiento en función del porcentaje máximo configurado de los elementos secundarios incorrectos. Dicho dato se obtiene de la directiva según el tipo de entidad y de elemento secundario.

- Si el estado de todos los elementos secundarios es Correcto, el estado de mantenimiento agregado de los elementos secundarios es Correcto.

- Si los elementos secundarios tienen los estados Correcto y Advertencia, el estado de mantenimiento agregado de los elementos secundarios es Advertencia.

- Si hay elementos secundarios con los estados Error que no respetan el porcentaje máximo permitido de elementos secundarios incorrectos, el estado de mantenimiento agregado es Error.

- Si los elementos secundarios con estados de Error respetan el porcentaje máximo permitido de los elementos secundarios incorrectos, el estado de mantenimiento agregado es Advertencia.

## Informes de mantenimiento
Tanto los componentes del sistema como los guardianes internos o externos pueden generar informes de las entidades de Service Fabric. Los informadores realizan determinaciones *locales* del estado del mantenimiento de las entidades supervisadas en función de las condiciones que supervisan. No necesitan examinar ningún estado global ni agregar datos. Esto no es lo que se desea, ya que convertiría a los informadores en organismos complejos que necesitan examinar muchos datos para inferir la información que van a enviar.

Para enviar datos de mantenimiento al Almacén de estado, los informadores necesitan identificar la entidad afectada y crear un informe de mantenimiento. Luego, el informe se podrá enviar mediante la API con **FabricClient.HealthManager.ReportHealth**, mediante PowerShell o mediante REST.

### Informes de mantenimiento
Los informes de mantenimiento para cada una de las entidades del clúster contienen la siguiente información:

- **SourceId**. Cadena que identifica de forma única el informador del evento de estado.

- **Identificador de entidad**. Identifica la entidad en la que se aplica el informe. Es diferente según el [tipo de entidad](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Clúster. Ninguno.

  - Nodo. Nombre de nodo (cadena).

  - Aplicación. Nombre de aplicación (identificador URI). Representa el nombre de la instancia de aplicación implementada en el clúster.

  - Servicio. Nombre de servicio (identificador URI). Representa el nombre de la instancia de servicio implementada en el clúster.

  - Partición. Identificador de partición (GUID). Representa el identificador único de la partición.

  - Réplica. El identificador de réplica de servicio con estado o el identificador de instancia de servicio sin estado (INT64).

  - DeployedApplication. Nombre de aplicación (identificador URI) y nombre de nodo (cadena).

  - DeployedServicePackage. Nombre de aplicación (identificador URI), nombre de nodo (cadena) y nombre de manifiesto de servicio (cadena).

- **Propiedad**. Una *cadena* (no una enumeración fija) que permite al informador clasificar el evento de estado para una propiedad específica de la entidad. Por ejemplo, el informador A puede informar del mantenimiento de la propiedad de "almacenamiento" de Node01 y el informador B puede informar del mantenimiento de la propiedad de "conectividad" de Node01. En el Almacén de estado, estos informes se tratan como eventos de mantenimiento de la entidad Node01.

- **Descripción**. Una cadena que permite a un informador proporcionar información detallada sobre el evento de mantenimiento. **SourceId**, **Property** y **HealthState** deben describir el informe completamente. La descripción agrega información en lenguaje natural sobre el informe. Esto facilita la comprensión tanto a usuarios como a administradores.

- **HealthState**. Una [enumeración](service-fabric-health-introduction.md#health-states) que describe el estado de mantenimiento del informe. Los valores aceptados son Correcto, Advertencia y Error.

- **TimeToLive**. Intervalo de tiempo que indica cuánto tiempo es válido el informe de mantenimiento. Si se combina con **RemoveWhenExpired**, permite al Almacén de estado saber cómo evaluar los eventos expirados. De forma predeterminada, el valor es infinito y el informe es válido para siempre.

- **RemoveWhenExpired**. Un valor booleano. Si se establece en true, el informe de mantenimiento expirado se elimina automáticamente del Almacén de estado y no afecta a la evaluación del mantenimiento de la entidad. Se usa cuando el informe es válido solo durante un período de tiempo y el informador no necesita vaciarlo de manera explícita. También se utiliza para eliminar informes del Almacén de estado (por ejemplo, se cambia un guardián y deja de enviar informes con la propiedad y el origen anteriores). Puede enviar un informe con valor de TimeToLive pequeño, junto con RemoveWhenExpired para borrar cualquier estado anterior del Almacén de estado. Si el valor se establece en false, el informe expirado se trata como un error en la evaluación del mantenimiento. El valor false indica al Almacén de estado que el origen debe notificar periódicamente esta propiedad. Si no lo hace, debe haber algún error en el guardián. El mantenimiento del guardián se captura considerando el evento como error.

- **SequenceNumber**. Un entero positivo que debe ir en constante aumento y representa el orden de los informes. Lo usa el Almacén de estado para detectar informes obsoletos que se reciben tarde debido a retrasos en la red u otros problemas. Un informe se rechaza si el número de secuencia es menor o igual que el último número aplicado a la misma entidad, origen y propiedad. Si no se especifica, el número de secuencia se genera automáticamente. Solo es necesario colocar el número de secuencia cuando se informa de las transiciones de estado. En esta situación, el origen necesita recordar los informes que envió y conservar la información para la recuperación de conmutación por error.

Estos cuatro datos (SourceId, entity identifier, Property y HealthState) se requieren en todos los informes de mantenimiento. No se permite que la cadena de SourceId comience por el prefijo "**System.**", que se reserva para los informes del sistema. Para la misma entidad solo hay un informe del mismo origen y propiedad. Si se generan varios informes del mismo origen y propiedad, se invalidan entre sí, en el lado del cliente de mantenimiento (si se procesan por lotes) o en el lado del Almacén de estado. La sustitución se realiza basándose en los números de secuencia; los informes más recientes (con un número de secuencia mayor) reemplazan a los informes anteriores.

### Eventos de estado
Internamente, el Almacén de estado conserva los eventos de mantenimiento, que contienen toda la información de los informes, así como los metadatos adicionales. Aquí se incluye la hora en que el informe se dio al cliente de mantenimiento y la hora en que modificó en el servidor. Los eventos de mantenimiento los devuelven las [consultas de mantenimiento](service-fabric-view-entities-aggregated-health.md#health-queries).

Los metadatos agregados contienen:

- **SourceUtcTimestamp**. La hora en que el informe se dio al cliente de mantenimiento (hora universal coordinada)

- **LastModifiedUtcTimestamp**. La hora en que el informe se modificó por última vez en el servidor (hora universal coordinada)

- **IsExpired**. Marca que indica si el informe había expirado cuando el Almacén de estado ejecutó la consulta. Un evento ha expirado solo si RemoveWhenExpired está establecido en false. De lo contrario, la consulta no devuelve el evento y se elimina del almacén.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. La última hora de las transiciones correctas, con advertencia o con error. Estos campos muestran el historial de las transiciones del estado de mantenimiento del evento.

Los campos de la transición de estado se pueden usar para alertas inteligentes o información del "historial" de eventos de mantenimiento. Habilitan escenarios como:

- Alertar cuando una propiedad ha estado en los estados Advertencia o Error durante más de X minutos. Así se evita que se generen alertas cuando las condiciones son temporales. Por ejemplo, una alerta si el estado de mantenimiento ha sido Advertencia durante más de cinco minutos se puede traducir como (HealthState == Warning y Now - LastWarningTransitionTime > 5 minutes).

- Alertar solo sobre las condiciones que han cambiado en los últimos X minutos. Si el estado de un informe ya era Error antes de la hora especificada, se puede ignorar, porque ya se había señalado con anterioridad.

- Si una propiedad alterna entre Advertencia y Error, determine cuánto tiempo ha sido incorrecta (es decir, el estado no ha sido Correcto). Por ejemplo, una alerta si la propiedad no ha sido correcta durante más de cinco minutos se pueden traducir como (HealthState != Ok y Now - LastOkTransitionTime > 5 minutes).

## Ejemplo: informar y evaluar el mantenimiento de una aplicación
En el ejemplo siguiente se envía un informe de mantenimiento a través de PowerShell en la aplicación **fabric:/WordCount** desde el **MyWatchdog** de origen. El informe de mantenimiento contiene información sobre la disponibilidad de la propiedad de mantenimiento en un estado de mantenimiento de Error, con un valor de TimeToLive infinito. Luego consulta el mantenimiento de la aplicación, que devuelve los errores del estado de mantenimiento agregado y los eventos de mantenimiento notificados en la lista de eventos de mantenimiento.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 5102
                                  SentAt                : 4/15/2015 5:29:15 PM
                                  ReceivedAt            : 4/15/2015 5:29:15 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/15/2015 5:29:15 PM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 130736794527105907
                                  SentAt                : 4/16/2015 5:37:32 PM
                                  ReceivedAt            : 4/16/2015 5:37:32 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Error = 4/16/2015 5:37:32 PM

```

## Uso del modelo de estado
El modelo de mantenimiento permite escalar los servicios en la nube y la plataforma de Service Fabric subyacente, ya que las determinaciones de supervisión y mantenimiento se distribuyen entre los distintos monitores del clúster. Otros sistemas tienen un servicio centralizado único en el nivel de clúster que analiza toda la información *potencialmente* útil que emiten los servicios. Este enfoque dificulta su escalabilidad. Tampoco les permite recopilar información muy específica para ayudar a identificar problemas reales y potenciales tan próximos a la causa raíz como sea posible.

El modelo de mantenimiento se usa mucho para la supervisión y el diagnóstico, para evaluar el mantenimiento de clústeres y aplicaciones, y para las actualizaciones supervisadas. Otros servicios usan los datos de mantenimiento para realizar reparaciones automáticas, generar el historial de mantenimiento de los clústeres y emitir alertas en determinadas condiciones.

## Pasos siguientes
[Vista de los informes de estado de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Incorporación de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)

[Supervisión y diagnóstico de los servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->