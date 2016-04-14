<properties
   pageTitle="Información general sobre el modelo de programación de Reliable Services de Service Fabric | Microsoft Azure"
   description="Obtenga información sobre el modelo de programación de un servicio fiable de Service Fabric y comience a escribir sus propios servicios."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="masnider;jesseb"/>

# Información general sobre Reliable Services
Azure Service Fabric simplifica la escritura y administración de los servicios de Reliable Services con y sin estado. En este documento se hablará acerca de:

- El modelo de programación de Reliable Services para los servicios sin estado y con estado.
- Las opciones que debe elegir al escribir un servicio de Reliable Services.
- Algunos escenarios y ejemplos de cuándo utilizar Reliable Services y cómo se escriben.

Servicios fiables es uno de los modelos de programación disponibles en Service Fabric. Para más información sobre el modelo de programación de Reliable Actors, consulte [Introducción a Reliable Actors de Service Fabric](service-fabric-reliable-actors-introduction.md).

En Service Fabric, un servicio se compone de configuración, código de aplicación y estado (opcionalmente).

Service Fabric administra la duración de los servicios, desde el aprovisionamiento hasta la implementación, mediante actualizaciones y eliminaciones, a través de la [administración de aplicaciones de Service Fabric](service-fabric-deploy-remove-applications.md).

## ¿Qué son los servicios de Reliable Services?
Los servicios de confianza le ofrecen un modelo de programación de nivel superior, sencillo y eficaz para ayudarle a expresar lo que es importante para su aplicación. Con el modelo de programación de Reliable Services, obtiene lo siguiente:

- En los servicios con estado, el modelo de programación de Reliable Services permite almacenar de forma coherente y confiable su derecho de estado dentro de su servicio mediante Reliable Collections. Se trata de un conjunto sencillo de clases de colecciones de alta disponibilidad que le resultará familiar a cualquiera que haya usado colecciones de C#. Tradicionalmente, los servicios necesitaban sistemas externos para efectuar la administración de estados de confianza. Con Reliable Collections, puede almacenar el estado junto a su proceso con la misma alta disponibilidad y confiabilidad que cabe esperar de los almacenes externos de alta disponibilidad, y con las mejoras de latencia adicional que colocan juntos el proceso y el estado proporcionados.

- Un modelo sencillo para ejecutar su propio código parecido a los modelos de programación que está acostumbrado a utilizar. El código tiene un punto de entrada bien definido y un ciclo de vida de fácil administración.

- Un modelo de comunicación acoplable. Utilice el transporte de su elección, por ejemplo, HTTP con [API web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, etc. Reliable Services proporciona excelentes opciones ya integradas, o puede proporcionar las suyas propias.

## ¿Qué hace que Reliable Services sea diferente?
Reliable Services de Service Fabric es diferente de los servicios que haya podido escribir anteriormente. Service Fabric proporciona confiabilidad, disponibilidad, coherencia y escalabilidad.

- **Confiabilidad**: su servicio permanecerá activo incluso en entornos poco confiables en los que es posible que las máquinas generen errores o encuentren problemas de red.

- **Disponibilidad**: su servicio estará accesible y responderá. (Esto no significa que no pueda tener servicios que no se pueden encontrar o comunicarse con ellos desde fuera).

- **Escalabilidad**: los servicios se desacoplan de hardware específico y pueden ampliarse o reducirse según sea necesario mediante la adición o eliminación de hardware o recursos virtuales. Los servicios se particionan fácilmente (especialmente en el caso con estado) para asegurarse de que las partes independientes del servicio pueden escalarse y responder a errores de forma independiente. Finalmente, Service Fabric fomenta que los servicios sean ligeros al permitir que se provisionen miles de servicios en un único proceso, en lugar de requerir o dedicar instancias completas de sistema operativo a una única instancia de una carga de trabajo determinada.

- **Coherencia**: se puede garantizar que toda información almacenada en este servicio es coherente (esto solo se aplica a los servicios con estado, de los que hablaremos detenidamente más adelante).

## Ciclo de vida del servicio
Tanto si el servicio está con estado como sin estado, Reliable Services proporciona un ciclo de vida simple que permite conectar rápidamente el código y empezar a trabajar. Solo hay uno o dos métodos que se deben implementar para poner su servicio en funcionamiento.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners**: aquí es donde el servicio define la pila de comunicación que quiere usar. La pila de comunicación, como [API web](service-fabric-reliable-services-communication-webapi.md), es lo que define los puntos de conexión de escucha del servicio (cómo se comunicarán con él los clientes). También define cómo los mensajes que aparecen acaban interactuando con el resto del código de servicio.

- **RunAsync**: aquí es donde el servicio ejecuta su lógica empresarial. El token de cancelación que se proporciona es una señal de cuándo debe detenerse ese trabajo. Por ejemplo, si tiene un servicio que necesita extraer mensajes constantemente de una cola de Reliable Queues y procesarlos, aquí sería donde se produciría ese trabajo.

### Inicio del servicio

Los eventos principales del ciclo de vida de un servicio de Reliable Services son:

1. Se construye el objeto de servicio (aquello que se deriva del servicio sin estado o del servicio con estado).

2. Se llama al método `CreateServiceReplicaListeners`/`CreateServiceInstanceListeners`, de forma que se le da al servicio la oportunidad de devolver uno o varios agentes de escucha de su elección.
  - Tenga en cuenta que esto es opcional, aunque la mayoría de los servicios expondrán algún punto de conexión directamente.

3. Una vez creados los agentes de escucha de comunicación, se abren.
  - Los agentes de escucha de comunicación disponen de un método llamado `OpenAsync()`, al que se llama en este punto y que devuelve la dirección de escucha del servicio. Si el servicio de Reliable Services usa uno de los ICommunicationListener integrados, esta operación se controla automáticamente.

4. Una vez que el agente de escucha de comunicación está abierto, se llama al método `RunAsync()` del servicio principal.
  - Tenga en cuenta que `RunAsync()` es opcional. Si el servicio realiza todo su trabajo en respuesta solo a las llamadas del usuario, no es necesario que implemente `RunAsync()`.

### Cierre del servicio

Cuando el servicio se está cerrando (porque se va a eliminar, actualizar o mover), se refleja el orden de llamada: primero se cancela el token de cancelación que mantiene `RunAsync()`; luego se llama a `CloseAsync()` en los agentes de escucha de comunicación.

Existen algunas cuestiones importantes que se deben tener en cuenta sobre el cierre de los servicios con estado:

- Service Fabric no promoverá otra réplica de su servicio a estado principal hasta que `CloseAsync` y `RunAsync` hayan devuelto los resultados. Si está utilizando un agente de escucha de comunicación integrado, el método `CloseAsync` se controla automáticamente.

- Aunque no hay un límite de tiempo para que se devuelvan resultados de estos métodos, se pierde inmediatamente la posibilidad de escribir en Reliable Collections y, por lo tanto, no se puede realizar ningún trabajo real. Se recomienda que devuelva resultados lo antes posible tras recibir la solicitud de cancelación.

## Servicios de ejemplo
Tras conocer este modelo de programación, echemos un vistazo rápido a dos servicios diferentes para ver cómo encajan estas piezas.

### Servicios fiables sin estado
Un servicio sin estado es aquel en el que literalmente no se mantiene ningún estado en el servicio, o en el que el estado que se encuentra presente es completamente desechable y no requiere sincronización, replicación, persistencia o alta disponibilidad.

Por ejemplo, tomemos una calculadora que no tiene memoria y que recibe todos los términos y las operaciones que debe realizar a la vez.

En este caso, el valor de RunAsync() del servicio puede estar vacío, ya que no hay ningún procesamiento de tareas en segundo plano que necesite hacer el servicio. Cuando se cree el servicio de calculadora, devolverá un ICommunicationListener (por ejemplo, [API web](service-fabric-reliable-services-communication-webapi.md)), que abre un punto de conexión de escucha en algún puerto. Este punto de conexión de escucha se enlazará con los diferentes métodos (por ejemplo: "Add (n1, n2)") que definen la API pública de la calculadora.

Cuando se realiza una llamada desde un cliente, se invoca el método adecuado y el servicio de calculadora realiza las operaciones en los datos proporcionados y devuelve el resultado. No almacena ningún estado.

El hecho de no almacenar ningún estado interno hace que esta calculadora de ejemplo sea muy sencilla. Pero la mayoría de los servicios no son realmente sin estado. En su lugar, externalizan su estado a algún otro almacén. (Por ejemplo, cualquier aplicación web que se basa en mantener el estado de sesión en un almacén de respaldo o una caché no es completamente sin estado).

Un ejemplo común de cómo se utilizan los servicios sin estado en Service Fabric es un extremo que expone la API pública para una aplicación web. El servicio front-end se comunica entonces con los servicios con estado para completar una solicitud de usuario. En este caso, las llamadas de los clientes se dirigen a un puerto conocido, como el 80, en el que se escucha el servicio sin estado. Este servicio sin estado recibe la llamada y determina si esta procede de una entidad de confianza, así como a qué servicio va destinada. Luego, el servicio sin estado reenvía la llamada a la partición correcta del servicio con estado y espera una respuesta. Cuando el servicio sin estado recibe una respuesta, responde al cliente original.

### Servicios fiables con estado
Un servicio con estado es el que debe tener alguna parte del estado coherente y presente para que el servicio funcione. Considere un servicio que calcula constantemente una media acumulada de algún valor basándose en las actualizaciones que recibe. Para ello, debe disponer del conjunto actual de solicitudes entrantes que necesite procesar, así como del promedio actual. Cualquier servicio que recupera, procesa y almacena la información en un almacén externo (por ejemplo, en la actualidad un almacén de tablas o blobs de Azure) es con estado. Simplemente mantiene su estado en el almacén de estado externo.

Actualmente la mayoría de servicios almacena su estado externamente debido a que el almacén externo es lo que proporciona confiabilidad, disponibilidad, escalabilidad y coherencia para ese estado. En Service Fabric, los servicios con estado no necesitan almacenar su estado externamente debido a que Service Fabric se encarga de estos requisitos, tanto por lo que se refiere al código de servicio como al estado del servicio.

Supongamos que queremos escribir un servicio que toma solicitudes de una serie de conversiones que deben realizarse en una imagen y la imagen que es necesario convertir. Para este servicio, se devolvería un agente de escucha de comunicación (supongamos que una API web) que abre un puerto de comunicaciones y permite envíos a través de una API como `ConvertImage(Image i, IList<Conversion> conversions)`. En esta API, el servicio podría tomar la información y almacenar la solicitud en una cola de Reliable Queues y, a continuación, devolver algún token al cliente, por lo que podría realizar un seguimiento de la solicitud (dado que las solicitudes podrían tardar algún tiempo).

En este servicio, RunAsync podría ser más complejo. El servicio tendría un bucle dentro de su RunAsync que extrae las solicitudes de IReliableQueue, realiza las conversiones enumeradas y almacena los resultados en IReliableDictionary para que cuando el cliente regrese pueda obtener sus imágenes convertidas. Para asegurarse de que incluso si algo no va bien la imagen no se pierda, este servicio de Reliable Services podría extraer de la cola, realizar las conversiones y almacenar el resultado en una transacción. En este caso, el mensaje se quita en realidad solo de la cola y los resultados se almacenan en el diccionario de resultados cuando se completan las conversiones. Si algo va mal a mitad del proceso (por ejemplo, la máquina en la que se ejecuta esta instancia del código no funciona), la solicitud permanece en la cola a la espera de volver a ser procesada.

Algo a tener en cuenta con respecto a este servicio es que parece un servicio .NET normal. La única diferencia es que las estructuras de datos que se van a utilizar (IReliableQueue y IReliableDictionary) son proporcionadas por Service Fabric, de ahí que se haga que sean altamente disponibles, confiables y coherentes.

## Cuándo utilizar las API de Reliable Services
Considere el uso de API de Reliable Services si sus necesidades de servicios de aplicaciones tienen estas características:

- Debe proporcionar el comportamiento de la aplicación en varias unidades de estado (por ejemplo, pedidos y artículos de líneas de pedido)

- El estado de la aplicación puede modelarse naturalmente como Reliable Dictionaries y Queues.

- El estado debe ser de alta disponibilidad con acceso de latencia baja.

- La aplicación debe controlar la simultaneidad o la granularidad de las operaciones de transacción a través de una o más instancias de Reliable Collections.

- Desea administrar las comunicaciones o controlar el esquema de partición del servicio.

- El código necesita un entorno de tiempo de ejecución de subproceso libre.

- La aplicación debe crear o destruir instancias de Reliable Dictionaries o Queues en tiempo de ejecución de forma dinámica.

- Debe controlar mediante programación la copia de seguridad proporcionada por Service Fabric y restaurar las características del estado de su servicio*.

- La aplicación necesita mantener el historial de cambios para sus unidades de estado*.

- Desea desarrollar o consumir proveedores de estado personalizados desarrollados por terceros*.

> [AZURE.NOTE]*Funciones disponibles en SDK con carácter general.


## Pasos siguientes
+ [Introducción a Reliable Services de Service Fabric de Microsoft Azure](service-fabric-reliable-services-quick-start.md)
+ [Uso avanzado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
+ [El modelo de programación de Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!---HONumber=AcomDC_0114_2016-->