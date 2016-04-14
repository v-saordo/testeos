<properties
   pageTitle="Pruebas de conmutación por error y caos | Microsoft Azure"
   description="Uso de los escenarios de prueba de conmutación por error y pruebas de caos de Service Fabric para inducir acciones de error y comprobar la confiabilidad de los servicios."
   services="service-fabric"
   documentationCenter=".net"
   authors="anmolah"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/03/2016"
   ms.author="anmola"/>

# Escenarios de Testability
Los grandes sistemas distribuidos, como infraestructuras de nube, son inherentemente poco confiables. Azure Service Fabric ofrece a los desarrolladores la capacidad de escribir servicios para ejecutarse sobre infraestructuras poco confiables. Para poder escribir servicios de alta calidad, los desarrolladores deben poder inducir tal infraestructura confiable para probar la estabilidad de sus servicios.

Service Fabric permite a los desarrolladores la capacidad de inducir acciones de error para probar los servicios en presencia de errores. Sin embargo, hasta ahora se obtendrán solo errores simulados dirigidos. Para realizar más pruebas, puede usar los escenarios de prueba en Service Fabric: una prueba de caos y una prueba de conmutación por error. Estos escenarios simulan errores continuos intercalados, tanto correctos como incorrectos, en todo el clúster durante períodos prolongados de tiempo. Una vez configurada una prueba con la tasa y el tipo de errores, se ejecuta como una herramienta de cliente, a través de las API de C# o de PowerShell para generar errores en el clúster y en el servicio.

## Prueba de caos
El escenario de caos genera errores en todo el clúster de Service Fabric. El escenario comprime los errores que se ven por lo general durante meses o años en unas pocas horas. Esta combinación de errores intercalados con una elevada tasa de errores encuentra casos excepcionales que de otra manera pasan desapercibidos. Esto conduce a una mejora considerable en la calidad del código del servicio.

### Errores simulados en la prueba de caos
 - Reinicio de un nodo
 - Reinicio de un paquete de código implementado
 - Eliminación de una réplica
 - Reinicio de una réplica
 - Desplazamiento de una réplica principal (opcional)
 - Desplazamiento de una réplica secundaria (opcional)

La prueba de caos ejecuta varias iteraciones de errores y las validaciones de clúster para el período de tiempo especificado. También se puede configurar el tiempo empleado por el clúster para que la estabilización y la validación sean correctas. Se produce un error en el escenario cuando se encuentra un error en la validación del clúster.

Por ejemplo, considere un conjunto de pruebas que se va a ejecutar durante una hora con un máximo de tres errores simultáneos. La prueba inducirá tres errores y después validará el mantenimiento del clúster. La prueba recorrerá en iteración el paso anterior hasta que el clúster pase a ser incorrecto o transcurra una hora. Si el clúster pasa a ser incorrecto en cualquier iteración, es decir, no se estabiliza dentro de un tiempo configurado, la prueba producirá un error con una excepción. Esta excepción indica que algo salió mal y que se necesita más investigación.

En su forma actual, el motor de generación de errores de la prueba de caos induce solo errores seguros. Esto significa que en ausencia de errores externos nunca se producirá una pérdida de quórum o de datos.

### Opciones de configuración importantes
 - **TimeToRun**: tiempo total en el que se ejecutará la prueba antes de finalizarse con éxito. La prueba puede finalizarse antes en lugar de un error de validación.
 - **MaxClusterStabilizationTimeout**: cantidad máxima de tiempo de espera para que el mantenimiento del clúster sea correcto antes de cancelar la prueba. Las comprobaciones realizadas son si el mantenimiento del clúster es correcto, el mantenimiento del servicio es correcto, se consigue el tamaño del conjunto de réplicas de destino para la partición de servicio y si no hay réplicas InBuild.
 - **MaxConcurrentFaults**: número máximo de errores simultáneos inducidos en cada iteración. Cuanto mayor sea el número, más agresiva será la prueba. Por lo tanto, dará como resultado combinaciones de conmutaciones por error y de transición más complejas. La prueba garantiza que en ausencia de errores externos no habrá pérdida de quórum o de datos, con independencia de lo elevado del número de esta configuración.
 - **EnableMoveReplicaFaults**: habilita o deshabilita los errores provocando el movimiento de las réplicas principales o secundarias. Estos errores están deshabilitados de forma predeterminada.
 - **WaitTimeBetweenIterations**: cantidad de tiempo de espera entre iteraciones, es decir, después de una ronda de errores y de su validación correspondiente.

### Ejecución de una prueba de caos
Ejemplo de C#

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## Prueba de conmutación por error

El escenario de prueba de conmutación por error es una versión del escenario de prueba de caos dirigida a una partición de servicio específica. Comprueba el efecto de la conmutación por error en una partición de servicio específica al mismo tiempo que deja sin afectar los otros servicios. Una vez configurada con la información de la partición de destino y otros parámetros, se ejecuta como una herramienta de cliente mediante las API de C# o PowerShell para generar errores para una partición de servicio. El escenario se itera por una secuencia de errores simulados y una validación de servicio mientras la lógica de negocios se ejecuta en paralelo para proporcionar una carga de trabajo. Un error en la validación de servicio indica un problema que necesita más investigación.

### Errores simulados en la prueba de conmutación por error
- Reinicio de un paquete de código implementado donde se hospeda la partición
- Eliminación de una instancia sin estado o de una réplica principal/secundaria
- Reinicio de una réplica principal/secundaria (si se conserva el servicio)
- Desplazamiento de una réplica principal
- Desplazamiento de una réplica secundaria
- Reinicio de la partición

La prueba de conmutación por error provoca un error seleccionado y después ejecuta la validación en el servicio para garantizar su estabilidad. La prueba de conmutación por error solo provoca un error a la ver en lugar de varios errores posibles en la prueba de caos. Si la partición de servicio no se estabiliza en el tiempo de espera configurado después del error, la prueba produce un error. La prueba provoca únicamente errores seguros. Esto significa que, en ausencia de errores externos, nunca se producirá una pérdida de quórum o de datos.

### Opciones de configuración importantes
 - **PartitionSelector**: objeto selector que especifica la partición a la que debe dirigirse.
 - **TimeToRun**: tiempo total en el que se ejecutará la prueba antes de finalizarse.
 - **MaxClusterStabilizationTimeout**: cantidad máxima de tiempo de espera para que el mantenimiento del clúster sea correcto antes que la prueba produzca un error. Las comprobaciones realizadas son si el mantenimiento del servicio es correcto, el tamaño del conjunto de réplicas de destino conseguido para todas las particiones y si no hay réplicas InBuild.
 - **WaitTimeBetweenFaults**: cantidad de tiempo de espera entre cada ciclo de error y validación.

### Ejecución de la prueba de conmutación por error
Ejemplo de C#

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario chaosScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```

<!---HONumber=AcomDC_0211_2016-->