<properties
   pageTitle="Introducción a la implementación y actualización de aplicaciones en un clúster local | Microsoft Azure"
   description="Configurar un clúster local de Service Fabric, implementar en él una aplicación existente y actualizar dicha aplicación."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/26/2016"
   ms.author="seanmck"/>

# Introducción a la implementación y actualización de aplicaciones en un clúster local
El SDK de Service Fabric de Azure incluye un completo entorno de desarrollo local y puede usar para empezar a trabajar rápidamente con aplicaciones de implementación y administración en un clúster local. En este artículo, se crea un clúster local, se implementa en él una aplicación existente y, a continuación, se actualiza dicha aplicación a una nueva versión, y todo ello desde Windows PowerShell.

> [AZURE.NOTE] En este artículo se asume que ya [configuró un entorno de desarrollo](service-fabric-get-started.md).

## Creación de un clúster local
Un clúster de Service Fabric representa un conjunto de recursos de hardware en los que se pueden implementar aplicaciones. Normalmente, un clúster se compone de cualquier número entre cinco y varios miles de máquinas. Sin embargo, el SDK de Service Fabric incluye una configuración de clúster que se puede ejecutar en una única máquina.

Es importante comprender que el clúster local de Service Fabric no es un emulador o simulador. Ejecuta el mismo código de plataforma que se encuentra en clústeres de varias máquinas. La única diferencia es que ejecuta en una única máquina los procesos de plataforma que suelen estar dispersos en cinco máquinas.

El SDK proporciona dos maneras de configurar un clúster local: un script de Windows PowerShell y la aplicación de bandeja del sistema del Administrador de clústeres locales. En este tutorial, usaremos el script de PowerShell.

> [AZURE.NOTE] Si creó un clúster local mediante la implementación de una aplicación desde Visual Studio, puede omitir esta sección.


1. Inicie una ventana nueva de PowerShell como administrador.

2. Ejecute el script de instalación del clúster desde la carpeta del SDK:

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    La instalación del clúster tardará unos minutos. Una vez finalizada la instalación, debería ver la salida de aspecto similar al siguiente:

    ![Salida de instalación de clúster][cluster-setup-success]

    Ya está listo para intentar implementar una aplicación en el clúster.

## Implementar una aplicación
El SDK de Service Fabric incluye un amplio conjunto de marcos y herramientas para desarrolladores para crear aplicaciones. Si está interesado en aprender a crear aplicaciones en Visual Studio, consulte [Creación de la primera aplicación de Service Fabric en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

En este tutorial, se usará una aplicación de ejemplo existente (denominada WordCount), con el fin de que nos podamos centrar en los aspectos de la administración de la plataforma, entre los que se incluyen la implementación, supervisión y actualización.


1. Inicie una ventana nueva de PowerShell como administrador.

2. Importe el módulo de PowerShell del SDK de Service Fabric.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Cree un directorio para almacenar la aplicación que va a descargar e implementar, como por ejemplo C:\\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Descargue la aplicación WordCount](http://aka.ms/servicefabric-wordcountapp) en la ubicación que creó.

5. Conecte con el clúster local:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Invoque el comando de implementación del SDK para crear una aplicación nueva, y especifique un nombre y una ruta de acceso al paquete de aplicación.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Si todo va bien, debería ver un resultado como el siguiente:

    ![Implementación de una aplicación en el clúster local][deploy-app-to-local-cluster]

7. Para ver la aplicación en acción, inicie el explorador y navegue a [http://localhost:8081/wordcount/índex](http://localhost:8081/wordcount/index). Puede ver algo así:

    ![Interfaz de usuario de aplicación implementada][deployed-app-ui]

    La aplicación WordCount es muy simple. Incluye código JavaScript del lado cliente para generar "palabras" aleatorias de cinco caracteres, que, posteriormente, se retransmiten a la aplicación mediante ASP.NET Web API. Un servicio con estado realiza el seguimiento del recuento de palabras. Se crean particiones basadas en el primer carácter de la palabra.

    La aplicación que hemos implementado contiene cuatro particiones. Por tanto las palabras que empiezan de la A a la G se almacenan en la primera partición, las que comienzan de la H a la N en la segunda partición, y así sucesivamente.

## Visualización de los detalles y estado de la aplicación
Ahora que hemos implementado la aplicación, echemos un vistazo a algunos de los detalles de la aplicación en PowerShell.

1. Consulte todas las aplicaciones implementadas en el clúster:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Si solo implementó la aplicación WordCount, verá algo parecido a esto:

    ![Consultar todas las aplicaciones implementadas en PowerShell][ps-getsfapp]

2. Vaya al siguiente nivel, para lo que debe consultar el conjunto de servicios incluidos en la aplicación WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Enumeración de servicios de la aplicación en PowerShell][ps-getsfsvc]

    Tenga en cuenta que la aplicación consta de dos servicios: el front-end web y el servicio con estado que administra las palabras.

3. Por último, eche un vistazo a la lista de particiones de WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Ver las particiones de servicio en PowerShell][ps-getsfpartitions]

    El conjunto de comandos que acaba de usar, al igual que todos los comandos de PowerShell de Service Fabric, está disponibles para cualquier clúster al que se pueda conectar, tanto local como remoto.

    Si desea interactuar con el clúster de una forma más visual, puede usar la herramienta Explorador de Service Fabric basada en web, para lo que debe navegar a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) en el explorador.

    ![Ver detalles de aplicación en el Explorador de Service Fabric][sfx-service-overview]

    > [AZURE.NOTE] Para más información acerca del Explorador de Service Fabric, consulte [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md)

## Actualizar una aplicación
Service Fabric proporciona actualizaciones sin tiempo de inactividad mediante la supervisión del estado de la aplicación cuando se implementa en el clúster. Vamos realizar una actualización simple de la aplicación WordCount.

La nueva versión de la aplicación ahora contará solo las palabras que comiencen por una vocal. Cuando se implemente la actualización, veremos dos cambios en el comportamiento de la aplicación. En primer lugar, la velocidad a la que crece el recuento debe reducirse, ya que se cuentan menos palabras. En segundo lugar, dado que la primera partición tiene dos vocales (A y E) y las restantes particiones contienen solo una, su recuento debería finalmente debería empezar a superar a los demás.

1. [Descargue el paquete de WordCount v2](http://aka.ms/servicefabric-wordcountappv2) a la misma ubicación en que descargó el paquete de v1.

2. Vuelva a la ventana de PowerShell y use el comando de actualización del SDK para registrar la nueva versión en el clúster. Después, empiece a actualizar la aplicación fabric:/WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Debería ver el resultado de PowerShell, cuyo aspecto similar al siguiente, al comenzar la actualización.

    ![Progreso de la actualización en PowerShell][ps-appupgradeprogress]

3. Mientras que la actualización se lleva a cabo, le resultará más fácil supervisar su estado desde el Explorador de Service Fabric. Inicie una ventana del explorador y navegue a [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Haga clic en **Aplicaciones** en el árbol de la izquierda y, a continuación, elija **Actualizaciones en curso**.

    ![Progreso de la actualización en el Explorador de Service Fabric][sfx-upgradeprogress]

    Tenga en cuenta que el indicador de progreso de la actualización representa el estado de la actualización en los dominios de actualización del clúster. Mientras se realiza la actualización en cada dominio, se realizan comprobaciones de mantenimiento para asegurarse de que la aplicación se comporta correctamente.

4. Si vuelve a ejecutar la consulta anterior en el conjunto de servicios incluidos en la aplicación fabric:/WordCount, observará que aunque la versión de WordCountService cambió, la versión de WordCountWebService no lo hizo:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Consultar servicios de aplicación después de la actualización][ps-getsfsvc-postupgrade]

    Esto destaca cómo Service Fabric administra las actualizaciones de aplicaciones. Toca solo el conjunto de servicios (o los paquetes de código/configuración de dichos servicios) que cambiaron, lo que hace que el proceso de actualización sea más rápido y confiable.

5. Por último, vuelva al explorador para observar el comportamiento de la nueva versión de la aplicación. Como se esperaba, el recuento avanza más lentamente y la primera partición termina con un poco más del volumen.

    ![Ver la nueva versión de la aplicación en el explorador][deployed-app-ui-v2]

## Pasos siguientes
- Tras la implementación y actualización de algunas aplicaciones pregeneradas, puede [intentar compilar la suya propia en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Todas las acciones realizadas en el clúster local en este artículo se pueden realizar también en un [clúster de Azure](service-fabric-cluster-creation-via-portal.md).
- La actualización realizada en este artículo era muy básica. Consulte la [documentación de actualización](service-fabric-application-upgrade.md) para más información acerca de la eficacia y flexibilidad de las actualizaciones de Service Fabric.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png

<!---HONumber=AcomDC_0302_2016-->