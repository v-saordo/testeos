<properties
   pageTitle="Creación de un clúster de Service Fabric en el Portal de Azure | Microsoft Azure"
   description="Crear un clúster de Service Fabric en el Portal de Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Creación de un clúster de Service Fabric en el Portal de Azure

Esta página le ayuda a configurar un clúster de Service Fabric. La suscripción debe contar con núcleos suficientes para implementar las máquinas virtuales de IaaS que compondrán este clúster.


## Creación de clústeres

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Haga clic en **+ Nuevo** para agregar una nueva plantilla de recursos. Busque la plantilla en **Marketplace** en **Todo**; se llama **Cluster de Service Fabric**.

    a. En el nivel superior, haga clic en **Marketplace**.

    b. En **Todo**, haga clic en **Fabric** y presione ENTRAR. A veces el filtro automático no funciona; asegúrese de presionar ENTRAR. ![Captura de pantalla de búsqueda de la plantilla de clúster de Service Fabric en el Portal de Azure.][SearchforServiceFabricClusterTemplate]

3. Seleccione **Clúster de Service Fabric** en la lista.

4. Vaya a la hoja **Clúster de Service Fabric**, haga clic en **Crear** y proporcione los detalles sobre el clúster.

5. En **Crear un nuevo grupo de recursos**, asigne al grupo de recursos el mismo nombre que al clúster. Esto es útil para encontrarlos más adelante, sobre todo para tratar de realizar cambios en la implementación o para eliminar el clúster.

    >[AZURE.NOTE] Aunque puede decidir utilizar un grupo de recursos existente, es conveniente crear uno nuevo. Esto facilita la eliminación de los clústeres que no son necesarios.

 	![Captura de pantalla de la creación de un grupo de recursos.][CreateRG]

6. No olvide seleccionar la **suscripción** en la que desea que se implemente el clúster, especialmente si tiene varias suscripciones.

7. Seleccione una **Ubicación** en la lista desplegable. El valor predeterminado es **Oeste de EE. UU**.

8. Configure el **Tipo de nodo**. El tipo de nodo puede considerarse equivalente a los roles en los servicios en la nube. Los tipos de nodos definen los tamaños de máquina virtual, el número de máquinas virtuales y sus propiedades. El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal (el primero que se define en el portal) debe tener al menos cinco máquinas virtuales. Para configurar el tipo de nodo:

	a. Seleccione el plan de tarifa y el tamaño de máquina virtual que necesita. El valor predeterminado es D4 estándar, pero si solo va a usar este clúster para probar la aplicación, puede seleccionar D2 o un tamaño más pequeño de máquina virtual.

	b. Elija el número de máquinas virtuales. Puede escalar verticalmente hacia arriba o hacia abajo el número de máquinas virtuales en un tipo de nodo más adelante, pero el primer tipo de nodo debe tener al menos cinco máquinas virtuales.

	c. Elija un nombre para el tipo de nodo (con una longitud de 1 a 12 caracteres y que contenga solamente letras y números).

	d. Elija el **nombre de usuario** y la **contraseña** del escritorio remoto de máquina virtual.

	e. Si necesita varios tipos de nodos del clúster, tenga en cuenta las cuestiones siguientes. (Si planea implementar un clúster con un solo tipo de nodo, vaya al paso 9).

	* Supongamos que desea implementar una aplicación que contiene un servicio front-end y un servicio back-end. Desea colocar el servicio front-end en máquinas virtuales más pequeñas (tamaños de VM como A2, D2, etc.) que tienen puertos abiertos a Internet. No obstante, desea colocar el servicio back-end, que es de cálculo intensivo, en máquinas virtuales más grandes (con tamaños de VM como D4, D6, D12, etc.) que no son accesibles a través de Internet.

	* Aunque se pueden colocar ambos servicios en un tipo de nodo, se recomienda colocarlos en un clúster con dos tipos de nodos. Cada tipo de nodo puede tener distintas propiedades como la conectividad a Internet, el tamaño de máquina virtual y el número de máquinas virtuales que se pueden escalar de forma independiente.

	* Defina un tipo de nodo que tenga al menos cinco VM en primer lugar. Los demás tipos de nodo pueden tener un mínimo de una máquina virtual.

  	![Captura de pantalla de creación de un tipo de nodo.][CreateNodeType]

9. Si tiene previsto implementar las aplicaciones en el clúster de forma inmediata, agregue los puertos que desea abrir a las aplicaciones en este tipo de nodo de los **Puertos de aplicación** (o en los tipos de nodos que haya creado). Puede agregar puertos al tipo de nodo posteriormente modificando el equilibrador de carga que está asociado a este tipo de nodo. (Agregue un sondeo y luego agregue el sondeo a las reglas del equilibrador de carga). Si lo hace ahora será más fácil, ya que la automatización de portal agregará los sondeos y las reglas que sean necesarios al equilibrador de carga:

	a. Puede encontrar los puertos de la aplicación en los manifiestos de servicio que forman parte del paquete de aplicación. Vaya a cada una de las aplicaciones, abra los manifiestos de servicio y tome nota de todos los puntos de conexión de entrada que las aplicaciones necesitan para comunicarse con el mundo exterior.

	b. Agregue todos los puertos, separados por comas, en el campo **Puntos de conexión de entrada de la aplicación**. El punto de conexión del cliente TCP es 19000 de forma predeterminada, por lo que no es necesario especificarlo. Por ejemplo, la aplicación de ejemplo WordCount necesita que el puerto 83 esté abierto. Se encuentra en el archivo servicemanifest.xml del paquete de aplicación. (Puede haber más de un archivo servicemanifest.xml).

    c. La mayoría de las aplicaciones de ejemplo usan los puertos 80 y 8081, así que agréguelos si planea implementar ejemplos en este clúster. ![Puertos][Ports]

10. No necesita configurar **Propiedades de colocación** porque el sistema agrega una propiedad de colocación predeterminada de "NodeTypeName". Puede agregar más si así lo requiere la aplicación.

## Configuración de seguridad

En la actualidad, Service Fabric solo admite la protección de clústeres mediante un certificado X509. Antes de comenzar este proceso, tendrá que cargar el certificado en el almacén de claves. Vea [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md) para más detalles sobre cómo hacerlo.

La protección de los clústeres es opcional pero es muy recomendable. Si decide no proteger el clúster, cambie el **Modo de seguridad** a **Ninguno**.

Puede encontrar documentación sobre las consideraciones e instrucciones de seguridad en [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md).

![Captura de pantalla de las configuraciones de seguridad del Portal de Azure.][SecurityConfigs]

## Opcional: Configuración de diagnóstico

De forma predeterminada, los diagnósticos se habilitan en el clúster para ayudar a solucionar los problemas. Si desea deshabilitar los diagnósticos:

1. Vaya a la hoja **Configuraciones de diagnósticos**.

2. Cambie el **estado** al valor **Desactivado**.

## Opcional: Establecimiento de la configuración del clúster de Service Fabric

Con esta opción avanzada, puede cambiar la configuración predeterminada para el clúster de Service Fabric. Se recomienda no cambiar los valores predeterminados a menos que esté seguro de que la aplicación o el clúster así lo requieren.

## Finalización de la creación del clúster

Para completar la creación del clúster, haga clic en **Resumen** para ver las configuraciones que ha proporcionado, o descargar la plantilla del Administrador de recursos de Azure que se utilizará para implementar el clúster. Una vez que haya proporcionado los valores de configuración obligatorios, se habilitará el botón **Crear** y podrá iniciar el proceso de creación del clúster al hacer clic en él.

Puede ver el progreso de creación en las notificaciones. (Haga clic en el icono de la "Campana" cerca de la barra de estado en la parte superior derecha de la pantalla). Si hizo clic en **anclar a Panel de inicio** al crear el clúster, verá **Implementando el clúster de Service Fabric** anclado en el panel de **Inicio**.

![Captura de pantalla de panel de inicio, donde se muestra "Implementando el clúster de Service Fabric".][Notifications]

## Visualización del estado del clúster

Una vez finalizada la implementación, puede inspeccionar el clúster en el portal:

1. Vaya a **Examinar** y haga clic en **Clústeres de Service Fabric**.

2. Busque el clúster y haga clic en él. ![Captura de pantalla de la identificación del clúster en el portal.][BrowseCluster]

3. Ahora puede ver los detalles del clúster en el panel, incluida la dirección de IP pública del clúster. Tenga en cuenta que al mantener el mouse sobre **Dirección IP pública del clúster** aparecerá un portapapeles en el que puede hacer clic para copiar la dirección. ![Captura de pantalla de los detalles del clúster en el panel.][ClusterDashboard]

  En la sección **Monitor de nodo** de la hoja del panel del clúster se indica el número de máquinas virtuales correctas e incorrectas. Puede encontrar más detalles sobre el estado del clúster en la [introducción al modelo de estado de Service Fabric](service-fabric-health-introduction.md).

>[AZURE.NOTE] Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos en todo momento con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, normalmente no es seguro apagar todas las máquinas del clúster a menos que antes haya realizado una [copia de seguridad completa del estado](service-fabric-reliable-services-backup-restore.md).

## Conexión al clúster e implementación de una aplicación

Con el clúster configurado, ahora puede conectarse y comenzar a implementar aplicaciones. Inicie Windows PowerShell en un equipo que tenga instalado el SDK de Service Fabric. Luego, para conectarse al clúster, ejecute uno de los siguientes conjuntos de comandos de PowerShell dependiendo de si creó un clúster seguro o no seguro:

- Opción 1: Conectarse a un clúster no seguro.

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

- Opción 2: Conectarse a un clúster seguro.

    1. Ejecute lo siguiente para configurar el certificado en el equipo que va a usar para ejecutar el comando de PowerShell "Connect-serviceFabricCluster".

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. Ejecute el siguiente comando de PowerShell para conectarse a un clúster seguro. Los detalles del certificado son los mismos que asignó en el portal.

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        Por ejemplo, el comando de PowerShell anterior debe ser similar al siguiente:

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

Ahora que ya está conectado, ejecute los siguientes comandos para implementar su aplicación, reemplazando las rutas de acceso que se muestran con las apropiadas para su equipo. En el ejemplo siguiente se implementa la aplicación de ejemplo de recuento de palabras:

1. Copie el paquete en el clúster al que se conectó anteriormente.

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. Registre el tipo de aplicación con Service Fabric.

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. Cree una nueva instancia del tipo de aplicación que acaba de registrar.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. Ahora abra el explorador que haya escogido y conecte con el punto de conexión en el que está escuchando la aplicación. Para nuestra aplicación de ejemplo WordCount, la dirección URL sería:

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## RDP en una instancia de conjunto de escala de máquinas virtuales (VMSS) o un nodo de clúster 

Cada uno de los tipos de nodos que especificó en los resultados de clúster de una configuración de realización de VMSS. Consulte [Cómo obtener RDP en la instancia VMSS](service-fabric-cluster-nodetypes.md) para obtener más información.

## Pasos siguientes

- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)
- [Introducción al modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md)
- [Cómo obtener RDP en la instancia VMSS](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=AcomDC_0224_2016-->