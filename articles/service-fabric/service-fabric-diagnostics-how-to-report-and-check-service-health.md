<properties
   pageTitle="Notificación y comprobación del estado con Azure Service Fabric | Microsoft Azure"
   description="Conozca cómo se pueden enviar informes de estado desde el código de servicio y comprobar el estado del servicio mediante las herramientas de supervisión de estado que proporciona Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="toddabel"/>


# Notificación y comprobación del estado del servicio
Cuando los servicios se encuentran con problemas, su capacidad para responder y corregir cualquier incidente resultante así como las interrupciones depende de la capacidad de detectar los problemas rápidamente. Al informar problemas y errores en el administrador de estado de Service Fabric desde el código de servicio, puede usar las herramientas estándar de seguimiento de estado que proporciona Service Fabric para comprobar el estado de mantenimiento.

En este artículo se proporciona un ejemplo sobre cómo agregar un informe de estado a un servicio y muestra cómo se puede comprobar el estado de mantenimiento mediante las herramientas que proporciona Service Fabric. Este artículo pretende ser una introducción rápida a las capacidades de supervisión del estado de Service Fabric. Para obtener más información, puede leer la serie de artículos detallados sobre el estado, empezando por el vínculo al final de este documento.

## Requisitos previos
Debe tener instalado lo siguiente: *Visual Studio 2015 *SDK de Service Fabric

## Para implementar una aplicación y comprobar su estado
Para implementar una aplicación y comprobar su estado, siga estos pasos:

1. Inicie Visual Studio como administrador.

2. Cree un proyecto para un servicio con estado.

  ![Creación de una aplicación de Service Fabric con servicio con estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Presione **F5** para ejecutar la aplicación en modo de depuración. Se implementará la aplicación en el clúster local.

4. Cuando se ejecute la aplicación, inicie el Explorador de Service Fabric haciendo clic con el botón derecho en la aplicación de bandeja del sistema del Administrador de clústeres local y elija **Administrar clúster local en el menú contextual**.

![Inicio del explorador de Service Fabric desde la bandeja del sistema](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. El estado de la aplicación debe mostrarse como se muestra en la imagen siguiente. En este momento, la aplicación debe ser correcta y no tener errores.

  ![Aplicación correcta en el explorador de Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. También puede comprobar el estado mediante PowerShell. Puede comprobar el estado de la aplicación mediante ```Get-ServiceFabricApplicationHealth``` y el estado del servicio con ```Get-ServiceFabricServiceHealth```. El informe de estado para la misma aplicación en PowerShell tiene este aspecto. ![Aplicación correcta en PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Para agregar eventos de estado personalizados a su código de servicio
Las plantillas de proyecto de Visual Studio de Service Fabric contienen código de ejemplo. Los pasos siguientes muestran cómo puede informar sobre eventos de estado personalizados desde el código de servicio. Estos informes aparecerán automáticamente en las herramientas estándar para el seguimiento de estado que proporciona Service Fabric, como el Explorador de Service Fabric, vista del estado de Portal de Azure y PowerShell.

1. Vuelva a abrir la aplicación que acaba de crear en Visual Studio o cree otra con un servicio con estado a partir de las plantillas de Visual Studio.
2. Abra el archivo **Stateful1.cs**. Busque la declaración para `var myDictionary` y agregue el código siguiente justo después de la declaración `var myDictionary`. El objeto `fabricClient` creado aquí se usará más adelante para informar del estado.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Agregue también este espacio de nombres al archivo **Stateful1.cs**.

    ```csharp
    using System.Fabric;
    ```

4. Después busque la llamada `myDictionary.TryGetValueAsync` en el método *RunAsync*. Puede ver que se devuelve un `result` que contiene el valor actual del contador, ya que la lógica principal de esta aplicación es mantener el recuento en funcionamiento. Si se trata de una aplicación real y si la falta de resultados representa un error, es posible que queramos informar de ello al administrador de estado.
5. Para informar de un evento de estado por la falta de resultados que representa un error, agregue el código siguiente después de la llamada `myDictionary.TryGetValueAsync`. Informaremos el evento como un `StatefulServiceReplicaHealthReport`, ya que se está informando desde un servicio con estado. PartitionId y ReplicaId que se pasan al evento de informe le ayudarán a identificar el origen de este informe cuando lo vea en una de las herramientas de seguimiento de estado. Eso es importante porque un servicio implementado con estado puede tener varias particiones y cada partición puede tener varias réplicas. El parámetro `HealthInformation` almacena información sobre el problema de estado que se está informando. Agregue este espacio de nombres al archivo **Stateful1.cs**.

    ```csharp
    using System.Fabric.Health;
    ```

    Agregue este código después de la llamada `myDictionary.TryGetValueAsync`.

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Simulemos este error y veamos cómo se muestra en las herramientas de seguimiento de estado. Para simular el error, comente la primera línea en el código de notificación de estado que ha agregado anteriormente. Después de comentar la primera línea, el código tendrá el siguiente aspecto. Esto activará el informe de estado cada vez que se ejecute RunAsync. Después de realizar el cambio, ejecute la aplicación presionando **F5**.

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. Una vez ejecutada la aplicación, abra el explorador de Service Fabric para comprobar el estado de la aplicación. Esta vez el explorador de Service Fabric mostrará que el estado de la aplicación no es correcto. Esto es debido al error que se notificó en el código que se agregó antes. ![Aplicación no correcta en el explorador de Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Si selecciona la réplica principal en la vista de árbol del Explorador de Service Fabric, verá que muestra también el estado con el error. También muestra los detalles del informe de estado que se agregaron al parámetro `HealthInformation` en el código. También puede ver los mismos informes de estado en PowerShell, así como en el Portal de Azure. ![Estado de réplica del explorador de Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este informe permanecerá en el administrador de estado hasta que se sustituya por otro informe o se elimine esta réplica. Puesto que no se estableció TimeToLive para este informe de estado en el objeto HealthInformation, nunca expirará. Para consultar y notificar el estado, FabricClient debe estar en un proceso con privilegios de administrador.

## Pasos siguientes
[Profundización en el estado de Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0224_2016-->