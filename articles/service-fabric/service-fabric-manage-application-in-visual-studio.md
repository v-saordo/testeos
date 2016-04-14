<properties
   pageTitle="Administración de las aplicaciones en Visual Studio | Microsoft Azure"
   description="Use Visual Studio para crear, desarrollar, empaquetar, implementar y depurar las aplicaciones y servicios de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="jesseb"/>

# Uso de Visual Studio para simplificar la escritura y la administración de las aplicaciones de Service Fabric

Puede administrar los servicios y aplicaciones de Service Fabric de Azure a través de Visual Studio. Cuando haya [configurado su entorno de desarrollo](service-fabric-get-started.md), puede usar Visual Studio para crear aplicaciones de Service Fabric, agregar servicios, o empaquetar, registrar e implementar aplicaciones en el clúster de desarrollo local.

Para administrar una aplicación, en el Explorador de soluciones, haga clic con el botón secundario en su proyecto de aplicación.

![Administrar la aplicación de Service Fabric haciendo clic con el botón secundario en el proyecto de aplicación][manageservicefabric]

## Implementar la aplicación de Service Fabric

La implementación de una aplicación combina los pasos siguientes en una operación sencilla:

1. Creación del paquete de aplicación
2. Carga del paquete de aplicación en el almacén de imágenes
3. Registro del tipo de aplicación
4. Eliminación de cualquier instancia de aplicación en ejecución
5. Creación de una nueva instancia de aplicación

En Visual Studio, al presionar **F5** también se implementará su aplicación y se asociará el depurador a todas las instancias de aplicación. Puede usar **CTRL+F5** para implementar una aplicación sin depurar o bien, publicar en un clúster local o remoto mediante el perfil de publicación. Para más información, consulte [Publicación de una aplicación en un clúster remoto con Visual Studio](service-fabric-publish-app-remote-cluster.md).

### Conservar datos entre series de pruebas

A menudo, probará servicios localmente agregando la entrada de datos de prueba, modificando algunos bloques de código y depurando de nuevo localmente. Las herramientas de Service Fabric de Visual Studio ofrecen una útil propiedad llamada **Mantener datos al inicio** para mantener los datos que se especificaron en la sesión anterior y permitirle volver a usarlos.

#### Habilitar la propiedad Mantener datos al inicio

1. En el menú de acceso directo del proyecto de la aplicación, elija **Propiedades** (o presione la tecla **F4**).
1. En la ventana **Propiedades**, establezca la propiedad **Mantener datos al inicio** en **Sí**.

	![Establecer la propiedad Mantener datos al inicio][preservedata]

Cuando ejecute la aplicación de nuevo, el script de implementación tratará ahora la implementación como una actualización con el modo automático no supervisado para actualizar rápidamente la aplicación a una versión más reciente con una cadena de fecha anexada. El proceso de actualización conserva todos los datos especificados en una sesión de depuración anterior.

![Ejemplo de la nueva versión de aplicación con la fecha anexada][preservedate]

Los datos se conservan aprovechando la capacidad de actualización de la plataforma de Service Fabric. Para más información sobre la actualización de una aplicación, consulte [Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md).

## Agregue un servicio a su aplicación Service Fabric

Puede agregar nuevos servicios de tejido a su aplicación para ampliar su funcionalidad. Para garantizar que el servicio se incluye en el paquete de aplicación, agregue el servicio a través del elemento de menú **Nuevo servicio del tejido...**.

![Agregar un nuevo Service Fabric a la aplicación][newservice]

Seleccione un tipo de proyecto de Service Fabric para agregarlo a la aplicación y especifique un nombre para el servicio. Vea [Elección de un marco para el servicio](service-fabric-choose-framework.md) para ayudarle a decidir qué tipo de servicio quiere usar.

![Seleccionar un tipo de proyecto de Service Fabric para agregarlo a la aplicación][addserviceproject]

El nuevo servicio se agregará a la solución y al paquete de aplicación existente. Se agregarán las referencias del servicio y una instancia predeterminada del servicio al manifiesto de aplicación. El servicio se creará e iniciará la próxima vez que implemente la aplicación.

![Se agregará el nuevo servicio a su manifiesto de aplicación][newserviceapplicationmanifest]

## Empaquetar la aplicación de Service Fabric

Para implementar la aplicación y sus servicios en un clúster, debe crear una paquete de aplicación. El paquete organiza el manifiesto de aplicación, los manifiestos de servicio y otros archivos necesarios en un diseño específico. Visual Studio configura y administra el paquete en la carpeta del proyecto de aplicación, en el directorio 'pkg'. Haga clic en **Paquete** en el menú contextual **Aplicación** para crear o actualizar el paquete de aplicación. Puede que quiera hacerlo si implementa la aplicación mediante scripts de PowerShell personalizados.

## Eliminación de una aplicación

Puede desaprovisionar un tipo de aplicación desde el clúster local mediante el Explorador de Service Fabric. Al explorador del clúster se puede obtener acceso desde el punto de conexión de la puerta de enlace HTTP del clúster (normalmente, 19080 o 19007), por ejemplo, http://localhost:19080/Explorer. De esta manera se revertirán los pasos de implementación descritos anteriormente:

1. Eliminar cualquier instancia de aplicación en ejecución
2. Anular el registro del tipo de aplicación

![Eliminación de una aplicación](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

- [Modelo de aplicación de Service Fabric](service-fabric-application-model.md)
- [Implementación de la aplicación de Service Fabric](service-fabric-deploy-remove-applications.md)
- [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md)
- [Depuración de la aplicación de Service Fabric](service-fabric-debugging-your-application.md)
- [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png

<!---HONumber=AcomDC_0204_2016-->