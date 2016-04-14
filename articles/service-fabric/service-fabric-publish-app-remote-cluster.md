<properties
    pageTitle="Publicar una aplicación en un clúster remoto con Visual Studio | Microsoft Azure"
    description="Obtenga información sobre cómo publicar una aplicación en un clúster de Service Fabric remoto con Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="12/06/2015"
    ms.author="cawa" />

# Publicación de una aplicación en un clúster remoto con Visual Studio

La extensión Azure Service Fabric para Visual Studio proporciona una forma fácil repetible, que permite ejecutar scripts para la publicación de una aplicación en un clúster de Service Fabric.

## Artefactos necesarios para la publicación

### Deploy-FabricApplication.ps1

Se trata de un script de PowerShell que usa una ruta de acceso del perfil de publicación como un parámetro para la publicación de aplicaciones de Service Fabric. Puesto que este script forma parte de la aplicación, puede modificarlo tanto como sea necesario para la aplicación.

### Perfiles de publicación

Una carpeta en el proyecto de aplicación de Service Fabric llamada **PublishProfiles** contiene archivos XML que almacenan información esencial para publicar una aplicación, como:

- Parámetros de conexión del clúster de Service Fabric
- Ruta de acceso a un archivo de parámetros de aplicación
- Configuración de actualización

De forma predeterminada, la aplicación incluirá dos perfiles de publicación: Local.xml y Cloud.xml. Para agregar más perfiles, copie y pegue uno de los archivos predeterminados.

### Archivos de parámetros de la aplicación

Una carpeta del proyecto de aplicación de Service Fabric denominada **ApplicationParameters** contiene archivos XML para los valores de los parámetros de manifiesto de aplicación especificados por el usuario. Los archivos de manifiesto de la aplicación pueden tener parámetros para que se puedan usar otros valores para la configuración de implementación. Para más información acerca de la parametrización de aplicaciones, consulte [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE]Para los servicios de actor, debe compilar el proyecto antes de intentar modificar el archivo en un editor o con el cuadro de diálogo Publicar. Esto se debe a que parte de los archivos de manifiesto se generará durante la compilación.

## Publicación de una aplicación desde el cuadro de diálogo Publicar aplicación de Service Fabric

En los pasos siguientes se muestra cómo publicar una aplicación con el cuadro de diálogo **Publicar aplicación con Service Fabric** que proporcionan las herramientas de Service Fabric de Visual Studio.

1. En el menú contextual del proyecto de aplicación de Service Fabric, elija **Publicar...** para ver el cuadro de diálogo **Publicar aplicación de Service Fabric**.

    ![Cuadro de diálogo **Publicar aplicación de Service Fabric**][0]

    El archivo que se seleccione en el cuadro de lista desplegable **Perfil objetivo** será donde se guarden todas las configuraciones, excepto las **versiones del manifiesto**. Puede volver a usar un perfil existente o a crear uno nuevo, para lo que debe elegir **<Administrar perfiles...>** en el cuadro de lista desplegable **Perfil objetivo**. Cuando se elige un perfil de publicación, su contenido aparece en los campos correspondientes del cuadro de diálogo. Para guardar los cambios en cualquier momento, elija el vínculo **Guardar perfil**.

2. La sección **Punto de conexión** le permite especificar un punto de conexión de la publicación del clúster de Service Fabric local o remoto. Para agregar o cambiar el punto de conexión, haga clic en el botón **Seleccionar...** El cuadro de diálogo **Seleccionar clúster de Service Fabric** muestra los puntos de conexión disponibles del clúster de Service Fabric en los que puede publicar con su suscripción de Azure. Tenga en cuenta que si no ha iniciado sesión en Visual Studio, se le pedirá que lo haga.

    Use el cuadro de diálogo de selección de clúster para elegir entre el conjunto de suscripciones y clústeres disponibles.

    ![Cuadro de diálogo **Seleccionar clúster de Service Fabric**][1]

    >[AZURE.NOTE]Si desea publicar en un punto de conexión arbitrario (por ejemplo, un party cluster), consulte la sección **Publicación en un punto de conexión de clúster arbitrario** más adelante.

    Una vez elegido un punto de conexión, Visual Studio valida la conexión con el clúster de Service Fabric seleccionado. Si el clúster no está protegido, Visual Studio podrá conectarse a él inmediatamente. Sin embargo, si el clúster está protegido, deberá instalar un certificado en el equipo local antes de continuar. Para más información, consulte [Configuración de las conexiones seguras en un clúster de Service Fabric desde Visual Studio](service-fabric-visualstudio-configure-secure-connections.md). Cuando haya terminado, elija el botón **Aceptar**. El clúster seleccionado aparece en el cuadro de diálogo **Publicar aplicación de Service Fabric**.

3. En el cuadro de lista desplegable **Archivo de parámetros de la aplicación**, vaya a un archivo de parámetros de la aplicación. En un archivo de parámetros de la aplicación se incluyen los valores especificados por el usuario para los parámetros del archivo de manifiesto de aplicación. Para agregar o cambiar un parámetro, elija el botón **Editar**. Escriba o cambie el valor del parámetro en la cuadrícula **Parámetros**. Cuando haya terminado, haga clic en el botón **Guardar**.

    ![Cuadro de diálogo **Editar parámetros**][2]

4. Use la casilla **Actualizar la aplicación** para especificar si la acción de publicación es una actualización. Las acciones de publicación de actualización difieren de las acciones de publicación habituales. Consulte [Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md) para ver una lista de las diferencias. Para configurar las opciones de actualización, elija el vínculo **Configurar opciones de actualización**. Se muestra el editor de parámetros de actualización. Para más información sobre los parámetros de actualización, consulte [Configuración de la actualización de una aplicación de Service Fabric en Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

5. Seleccione el botón **Versiones de manifiesto...** para ver el cuadro de diálogo **Editar versiones**. Debe actualizar la aplicación y las versiones de servicio para que tenga lugar la actualización. Para saber cómo afectan las versiones de la aplicación y del manifiesto de servicio a un proceso de actualización, consulte [Tutorial sobre la actualización de la aplicación de Service Fabric con Visual Studio](service-fabric-application-upgrade-tutorial.md).

    ![Cuadro de diálogo **Editar versiones**][3]

    Si las versiones de la aplicación y del servicio usan el control de versiones semántico como 1.0.0 o valores numéricos con el formato 1.0.0.0, seleccione la opción **Actualizar automáticamente versiones de aplicación y de servicio**. Si elige esta opción, los números de versión del servicio y de la aplicación se actualizan automáticamente cada vez que se actualiza una versión del código, configuración o paquete de datos. Si prefiere editar manualmente las versiones, desactive la casilla para deshabilitar esta característica.

    >[AZURE.NOTE]Para que todas las entradas de paquete aparezcan para un proyecto de actor, primero cree el proyecto para generar las entradas en los archivos de manifiesto de servicio.

6. Cuando haya terminado de especificar todos los valores necesarios, elija el botón **Publicar** para publicar su aplicación en el clúster de Service Fabric seleccionado. La configuración especificada se aplica al proceso de publicación.

## Publicación en un punto de conexión de clúster arbitrario (incluidos los clústeres de Party Cluster)

En Visual Studio, la publicación se ha optimizado y ahora es posible publicar en clústeres remotos asociados a una suscripción de Azure. Sin embargo, es posible publicar en puntos de conexión arbitrarios (como clústeres de Party Cluster de Service Fabric) editando directamente el archivo XML del perfil de publicación. Como ya se ha indicado, de manera predeterminada se proporcionan dos perfiles de publicación, **Local.xml** y **Cloud.xml**, pero se pueden crear perfiles adicionales para diferentes entornos. Por ejemplo, se puede crear un perfil para publicar en party clusters, al que se podría denominar **Party.xml**.

Si se va a conectar a un clúster no protegido, lo único que hace falta es el punto de conexión del clúster, como `partycluster1.eastus.cloudapp.azure.com:19000`. En ese caso, el punto de conexión del perfil de publicación sería similar al siguiente:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Si se va a conectar a un clúster protegido, también necesitará especificar los detalles del certificado de cliente desde el almacén local que se usará para la autenticación. Para obtener más información, consulte [Configuración de las conexiones seguras en un clúster de Service Fabric desde Visual Studio](service-fabric-visualstudio-configure-secure-connections.md).

  Una vez configurado el perfil de publicación, puede hacer referencia a él en el cuadro de diálogo de publicación, tal como se muestra a continuación.

  ![Nuevo perfil de publicación en el cuadro de diálogo de publicación][4]

  Tenga en cuenta que en este caso, el nuevo perfil de publicación apunta a uno de los archivos de parámetros predeterminados de la aplicación. Esto es lo mejor si desea publicar la misma configuración de la aplicación en varios entornos. Por el contrario, en los casos en los que quiere tener configuraciones diferentes para cada entorno en que desee publicar, sería lógico crear un archivo de parámetros de aplicación para cada uno de ellos.

## Pasos siguientes

Para información sobre cómo automatizar el proceso de publicación en un entorno de integración continua, consulte [Configuración de la integración continua de una aplicación de Service Fabric mediante Visual Studio Team Services (VSTS)](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png

<!---HONumber=AcomDC_0114_2016-->