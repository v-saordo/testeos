<properties
   pageTitle="Información general sobre Testability | Microsoft Azure"
   description="Este artículo describe el subsistema de Testability en Service Fabric para inducir errores y ejecutar escenarios de prueba en los servicios."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="rsinha"/>

# Información general sobre Testability

El subsistema Testability está diseñado para probar los servicios basados en Microsoft Azure Service Fabric. Con Testability, podrá provocar errores significativos y escenarios de ejecución. Estos errores y escenarios ejercen y validan los numerosos estados y transiciones que experimentará un servicio durante su vigencia, y todo ello de forma segura, controlada y uniforme.

Testability proporciona acciones y escenarios que posibilitan esas capacidades. Las acciones son los errores individuales que se dirigen a un servicio para probarlo. Un programador del servicio puede utilizar como bloques de creación para escribir escenarios complicados. Por ejemplo:

  * Reiniciar un nodo para simular cualquier número de situaciones en que un equipo o máquina virtual se reinicia.

  * Mover una réplica de un servicio con estado para simular el equilibrio de carga, la conmutación por error o la actualización de la aplicación.

  * Invocar la pérdida de quórum en un servicio con estado para crear una situación en la que las operaciones de escritura no pueden continuar porque no hay suficientes réplicas "secundarias" o "de copia de seguridad" para aceptar los datos nuevos.

  * Invocar la pérdida de datos en un servicio con estado para crear una situación en la que se borra completamente todo el estado en memoria.

Los escenarios son operaciones complejas compuestas por una o varias acciones. Como estas acciones son comandos de PowerShell y llamadas a la API de C#, pueden tomar cualquier forma: servicios de ejecución prolongada, comandos de PowerShell, aplicaciones de línea de comandos, etc. En Testability se incluyen dos escenarios de serie:

  * Escenario de caos
  * Escenario de conmutación por error

Testability expone API tanto de PowerShell como de C#. Esto proporciona al desarrollador de servicios mayor agilidad con el scripting de PowerShell y un mayor control con las API de C# según sea necesario.

## Importancia de Testability

Service Fabric facilita considerablemente el trabajo de escribir y administrar aplicaciones escalables distribuidas. El subsistema de Testability en Service Fabric facilita la realización de pruebas de una aplicación distribuida. Durante las pruebas deben resolverse tres problemas principales:

1. Simular o generar errores que pueden producirse en los escenarios del mundo real: uno de los aspectos importantes de Service Fabric es que permite a las aplicaciones distribuidas recuperarse de varios errores. Sin embargo, para probar que la aplicación puede recuperarse de estos errores se necesita un mecanismo que simule o genere estos errores reales en un entorno de prueba controlado.

2. La capacidad de generar los errores correlacionados: los errores básicos del sistema, como errores de red o errores del equipo, son fáciles de generar de forma individual. Generar un número significativo de escenarios que pueden ocurrir en el mundo real como resultado de las interacciones de estos errores individuales no es trivial.

3. Experiencia unificada a través de varios niveles de desarrollo e implementación: existen muchos sistemas de inserción de errores que pueden realizar varios tipos de errores. Sin embargo, la experiencia en todos ellos es deficiente al pasar de los escenarios de desarrollo one box a ejecutar las mismas pruebas en entornos de prueba grandes y a utilizarlas para pruebas en producción.

Aunque existen varios mecanismos para resolver estos problemas, falta un sistema que haga lo mismo con las garantías requeridas, desde un entorno del desarrollador one box hasta la prueba en clústeres de producción. El subsistema de Testability ayuda a los desarrolladores de aplicaciones a concentrarse en probar su lógica de negocios. Testability proporciona todas las capacidades necesarias para probar la interacción del servicio con el sistema distribuido subyacente.

### Simulación/generación de escenarios de error reales

Para probar la solidez de un sistema distribuido frente a errores, se necesita un mecanismo para generar errores. Aunque en teoría parezca fácil generar un error como la pérdida de actividad de un nodo, de esta acción se deriva el mismo conjunto de problemas de coherencia que Service Fabric intenta resolver. Por ejemplo, si se desea apagar un nodo, este es el flujo de trabajo requerido:

1. Desde el cliente, generar una solicitud de apagado del nodo.

2. Enviar la solicitud al nodo correcto.

    a. Si no se encuentra el nodo, debería aparecer un error.

    b. Si se encuentra el nodo, debería devolverse solo si el nodo está apagado.

Para comprobar el error desde la perspectiva de la prueba, esta necesita saber que cuando se provoca el error, este efectivamente sucede. La garantía que proporciona Service Fabric es que el nodo se quedará fuera de servicio o ya estará fuera de servicio cuando el comando alcance el nodo. En cualquier caso, la prueba debe ser capaz de deducir correctamente el estado y realizar, o no, su validación correctamente. Un sistema implementado fuera de Service Fabric para realizar el mismo conjunto de errores podría detectar un gran número de problemas de red, hardware y software, lo que impediría que proporcionase las garantías indicadas anteriormente. Si surgen los problemas indicados anteriormente, Service Fabric reconfigurará el estado del clúster para solucionar los problemas y, por tanto, el subsistema de Testability aún podría ofrecer el conjunto correcto de garantías.

### Generación de los eventos y los escenarios requeridos

Aunque no es fácil simular un error real de forma coherente, la capacidad para generar errores correlacionados es aún más difícil. Por ejemplo, una pérdida de datos se produce en un servicio con estado guardado cuando ocurre lo siguiente:

1. En la replicación solo se detecta un quórum de escritura de las réplicas. Todas las réplicas secundarias se retrasan, con respecto a la principal.

2. El cuórum de escritura pasa a estar fuera de servicio porque las réplicas se quedan fuera de servicio (debido a que el paquete de código o el nodo están fuera de servicio).

3. El cuórum de escritura no puede generarse a partir de la copia de seguridad porque se han perdido los datos de las réplicas (debido a que el disco está dañado o al restablecimiento de la imagen inicial de la máquina).

Estos errores correlacionados suceden en el mundo real, aunque no con tanta frecuencia como los errores individuales. La capacidad de probar estos escenarios antes de que se produzcan en producción es fundamental. Incluso más importante es la capacidad para simular estas situaciones con cargas de trabajo de producción en circunstancias controladas (en medio del día con todos los ingenieros en sus puestos de trabajo). Esto es realmente preferible a provocarlas por primera vez en producción a las 2:00 AM.

### Experiencia unificada en distintos entornos

Tradicionalmente se han creado tres conjuntos diferentes de experiencias: una para el entorno de desarrollo, otra para las pruebas y otra para producción. Este era el modelo:

1. En el entorno de desarrollo, producir transiciones de estado que permitan realizar pruebas unitarias de los métodos individuales.

2. En el entorno de prueba, producir errores que permitan pruebas completas que provoquen distintos escenarios de error.

3. Mantener el entorno de producción intacto para impedir errores no naturales y asegurarse de que se produce una respuesta humana extremadamente rápida ante cualquier error.

En Service Fabric, a través del subsistema de Testability, proponemos cambiarlo para que se use la misma metodología desde el entorno de desarrollo hasta la producción. Hay dos formas de lograrlo:

1. Para provocar errores controlados, use las API de Testability desde un entorno one box hasta los clústeres de producción.

2. Para poner al clúster en una situación que provoque la inducción automática de errores, use el subsistema de Testability para generar errores automáticos. El control de la tasa de errores a través de la configuración permite que el mismo servicio se pruebe de manera diferente en distintos entornos.

Con Service Fabric, aunque la escala de errores sería diferente en los distintos entornos, el mecanismo real sería idéntico. Esto hace posible el uso de un código mucho más rápido para el proceso de la implementación y la capacidad de probar los servicios con carga real.

## Uso de Testability

### Uso de Testability en C##

En System.Fabric.Testability.dll están presentes las características de Testability. Este archivo dll se encuentra en el paquete de NuGet Microsoft.ServiceFabric.Testability.nupack. Para utilizar las características de Testability, incluya el paquete de NuGet como referencia en el proyecto.

### Uso de Testability en PowerShell

Para usar PowerShell de Testability, debe instalar el MSI en tiempo de ejecución. Una vez instalado el MSI, el módulo ServiceFabricTestability PowerShell se carga automáticamente para que los desarrolladores lo usen.

## Conclusión

Para crear verdaderos servicios de escala en la nube, es fundamental asegurarse, tanto antes como después de la implementación, de que los servicios pueden resistir errores reales. En el actual mundo de servicios, también es muy importante la capacidad de innovar y mover código a producción rápidamente. Testability de Service Fabric ayuda a los desarrolladores a hacer exactamente eso.

## Pasos siguientes

- [Acciones de Testability](service-fabric-testability-actions.md)
- [Escenarios de Testability](service-fabric-testability-actions.md)
- Procedimientos para probar un servicio
  - [Simulación de errores durante las cargas de trabajo del servicio](service-fabric-testability-workload-tests.md)
  - [Errores de comunicación entre servicios](service-fabric-testability-scenarios-service-communication.md)

<!---HONumber=AcomDC_0204_2016-->