<properties
   pageTitle="Actualización de la aplicación: serialización de datos| Microsoft Azure"
   description="Prácticas recomendadas para la serialización de datos y cómo afectan a las sucesivas actualizaciones de la aplicación."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/04/2016"
   ms.author="jesseb"/>


# Información sobre cómo la serialización afecta a una actualización de aplicación

En una [actualización de la aplicación gradual](service-fabric-application-upgrade.md), la actualización se aplica a un subconjunto de nodos, un dominio de actualización a la vez. Durante este proceso, algunos dominios de actualización se incluirán en la versión más reciente de su aplicación, mientras que otros estarán en la versión anterior. Durante el lanzamiento, la nueva versión de la aplicación debe poder leer la versión anterior de sus datos, mientras que la versión anterior debe poder leer la nueva versión. Si el formato de datos no es compatible con las versiones anteriores y nuevas, es posible que se produzca un error en la actualización, o lo que es peor, que se pierdan o dañen datos. En este artículo se analiza qué constituye su formato de datos y ofrece prácticas recomendadas para garantizar que sus datos sean compatibles con las versiones anteriores y nuevas.


## ¿Qué conforma su formato de datos?

En Service Fabric de Azure, los datos que se conservan y replican proceden de sus clases de C#. Para las aplicaciones que usan[Colecciones fiables](service-fabric-reliable-services-reliable-collections.md), se trata de los objetos de los diccionarios y colas fiables. Para las aplicaciones que usan el modelo de [Reliable Actors](service-fabric-reliable-actors-introduction.md), se trata del estado de respaldo del actor. Estas clases de C# deben ser serializables para conservarse y replicarse. Por lo tanto, el formato de datos se define por medio de los campos y propiedades que se serializan y de cómo se produce esto. Por ejemplo, en `IReliableDictionary<int, MyClass>`, los datos son `int` y `MyClass` serializados.

### Cambios de código que dan como resultado un cambio de formato de datos

Como las clases de C# determinan el formato de datos, los cambios en las clases pueden producir un cambio del formato de datos. Hay que actuar con cautela para garantizar que una actualización gradual pueda controlar el cambio del formato de datos. Ejemplos que pueden provocar cambios del formato de datos:

- Adición o eliminación de campos o propiedades
- Cambio de nombre de campos o propiedades
- Cambio de los tipos de campos o propiedades
- Cambio del nombre de clase o espacio de nombres

### Contrato de datos como el serializador predeterminado

El serializador suele ser responsable de la lectura de los datos y de su deserialización en la versión actual, incluso si los datos están en una versión anterior o *más reciente*. El serializador predeterminado es el [serializador de contratos de datos](https://msdn.microsoft.com/library/ms733127.aspx), que tiene reglas de versiones bien definidas. Las Colecciones fiables permiten el reemplazo del serializador, pero los Reliable Actors no lo permiten. El serializador de datos tiene un rol importante en la habilitación de actualizaciones graduales. El serializador de contratos de datos es el serializador que se recomienda para las aplicaciones de Service Fabric.


## Cómo afecta el formato de datos a la actualización gradual

Durante una actualización gradual, hay dos escenarios principales donde el serializador puede encontrar una versión anterior o *más reciente* de sus datos:

1. Una vez que un nodo se actualice y empiece a crear copias de seguridad, el nuevo serializador cargará los datos que la versión anterior conservó en el disco.
2. Durante la actualización gradual, el clúster puede contener una mezcla de las versiones anteriores y nuevas de su código. Como las réplicas se pueden incluir en distintos dominios de actualización y las réplicas envían datos entre sí, la versión nueva como la anterior de los datos se pueden encontrar en la versión nueva o la anterior del serializador.

> [AZURE.NOTE] La "nueva versión" y "versión anterior" aquí hacen referencia a la versión de su código en ejecución. El "nuevo serializador" hace referencia al código del serializador que se ejecuta en la nueva versión de su aplicación. Los "nuevos datos" hacen referencia a la clase de C# serializada de la nueva versión de su aplicación.

Las dos versiones del formato de código y datos deben ser compatibles con las versiones anteriores y nuevas. Si no son compatibles, es posible que se produzca un error en la actualización o se pierdan datos. Es posible que se produzca un error en la actualización porque el código o serializador puede producir excepciones o un error cuando encuentra la otra versión. Es posible que se pierdan datos si, por ejemplo, se agregó una nueva propiedad, pero el serializador anterior la descarta durante la deserialización.

El contrato de datos es la solución recomendada para garantizar que sus datos sean compatibles. Tiene reglas de versiones bien definidas para agregar, quitar y cambiar campos. También tiene compatibilidad para tratar con campos desconocidos, enlazar con el proceso de serialización y deserialización, y para tratar con la herencia de clases. Para obtener más información, consulte [Uso de contrato de datos](https://msdn.microsoft.com/library/ms733127.aspx).


## Pasos siguientes

El procedimiento de [actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

El procedimiento de [actualización de aplicaciones usando Powershell](service-fabric-application-upgrade-tutorial-powershell.md) ofrece información para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte los [temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0211_2016-->