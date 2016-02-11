<properties
   pageTitle="Uso de contadores de rendimiento en Diagnósticos de Azure | Microsoft Azure"
   description="Use contadores de rendimiento en una máquina virtual o Servicios en la nube de Azure para buscar cuellos de botella y ajustar el rendimiento."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2015"
   ms.author="robb" />

# Creación y uso de contadores de rendimiento en una aplicación de Azure

En este artículo se describen las ventajas y el procedimiento para usar contadores de rendimiento en su aplicación de Azure. Puede usarlos para recopilar datos, encontrar cuellos de botella y ajustar el rendimiento del sistema y de la aplicación.

También puede recopilar y usar los contadores de rendimiento disponibles para Windows Server, IIS y ASP.NET para determinar el estado de los roles web, los roles de trabajo y Máquinas virtuales de Azure. Además, puede crear y usar contadores de rendimiento personalizados.

Puede examinar los datos del contador de rendimiento;
1) Directamente en el host de la aplicación con la herramienta Monitor de rendimiento, a la que se accede mediante Escritorio remoto;
2) Con System Center Operations Manager mediante el módulo de administración de Azure;
3) Con otras herramientas de supervisión que acceden a los datos de diagnóstico transferidos al almacenamiento de Azure. Consulte [Guardar y ver datos de diagnóstico en el almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) para obtener más información.

Para obtener información sobre la supervisión del rendimiento de la aplicación en el [Portal de Azure clásico](http://manage.azure.com/), consulte [Supervisión de servicios en la nube](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Para obtener instrucciones más detalladas acerca de la creación de una estrategia de registro y seguimiento, así como del uso del diagnóstico y otras técnicas para solucionar problemas y optimizar las aplicaciones de Azure, consulte [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## Habilitación de la supervisión de contadores de rendimiento

Los contadores de rendimiento no están habilitados de forma predeterminada. La aplicación o una tarea de inicio debe modificar la configuración predeterminada del agente de diagnóstico para incluir los contadores de rendimiento específicos que desee supervisar para cada instancia de rol.

### Contadores de rendimiento disponibles para Microsoft Azure

Azure proporciona un subconjunto de los contadores de rendimiento disponibles para Windows Server, IIS y la pila de ASP.NET. En la tabla siguiente, se incluyen algunos de los contadores de rendimiento de especial interés para las aplicaciones de Azure.

|Categoría de contador: Objeto (instancia)|Nombre del contador |Referencia|
|---|---|---|
|Excepciones de .NET CLR (_Global_)|Número de excepciones producidas por segundo |Contadores de rendimiento de excepciones|
|Memoria de .NET CLR (_Global_) |% de tiempo del GC |Contadores de rendimiento de memoria|
|ASP.NET |Reinicios de la aplicación |Contadores de rendimiento para ASP.NET|
|ASP.NET |Tiempo de ejecución de solicitud |Contadores de rendimiento para ASP.NET|
|ASP.NET |Solicitudes desconectadas |Contadores de rendimiento para ASP.NET|
|ASP.NET |Reinicios del proceso de trabajo |Contadores de rendimiento para ASP.NET|
|Aplicaciones ASP.NET(__Total__)|Total de solicitudes |Contadores de rendimiento para ASP.NET|
|Aplicaciones ASP.NET(__Total__)|Solicitudes por segundo |Contadores de rendimiento para ASP.NET|
|ASP.NET v4.0.30319 |Tiempo de ejecución de solicitud |Contadores de rendimiento para ASP.NET|
|ASP.NET v4.0.30319 |Tiempo de espera de la solicitud |Contadores de rendimiento para ASP.NET|
|ASP.NET v4.0.30319 |Solicitudes actuales |Contadores de rendimiento para ASP.NET|
|ASP.NET v4.0.30319 |Solicitudes en cola |Contadores de rendimiento para ASP.NET|
|ASP.NET v4.0.30319 |Solicitudes rechazadas |Contadores de rendimiento para ASP.NET|
|Memoria |MB disponibles |Contadores de rendimiento de memoria|
|Memoria |Bytes confirmados |Contadores de rendimiento de memoria|
|Procesador(\_Total) |% de tiempo de procesador |Contadores de rendimiento para ASP.NET|
|TCPv4 |Errores de conexión |Objeto TCP|
|TCPv4 |Conexiones establecidas |Objeto TCP|
|TCPv4 |Conexiones reinicializadas |Objeto TCP|
|TCPv4 |Segmentos enviados/s |Objeto TCP|
|Interfaz de red(*) |Bytes recibidos/s |Objeto de interfaz de red|
|Interfaz de red(*) |Bytes enviados/seg. |Objeto de interfaz de red|
|Interfaz de red(Adaptador de red de bus de máquina virtual de Microsoft \_2)|Bytes recibidos/s|Objeto de interfaz de red|
|Interfaz de red(Adaptador de red de bus de máquina virtual de Microsoft \_2)|Bytes enviados/seg.|Objeto de interfaz de red|
|Interfaz de red(Adaptador de red de bus de máquina virtual de Microsoft \_2)|Total de bytes por segundo|Objeto de interfaz de red|

## Creación e incorporación de contadores de rendimiento personalizados a la aplicación

Azure admite la creación y la modificación de contadores de rendimiento personalizados para los roles web y de trabajo. Los contadores se pueden usar para seguir y supervisar el comportamiento específico de la aplicación. Puede crear y eliminar categorías y especificadores de contadores de rendimiento personalizados desde una tarea de inicio, un rol web o un rol de trabajo con permisos elevados.

>[AZURE.NOTE]El código que realiza cambios en los contadores de rendimiento personalizados debe poseer permisos elevados para ejecutarse. Si el código está en un rol web o de trabajo, el rol debe incluir la etiqueta <Runtime executionContext="elevated" /> en el archivo ServiceDefinition.csdef para inicializarse correctamente.

Puede enviar datos de contadores de rendimiento personalizados al almacenamiento de Azure mediante el agente de diagnóstico.

Los datos de contadores de rendimiento estándar los generan los procesos de Azure. La aplicación para el rol web o de trabajo debe crear los datos de contadores de rendimiento personalizados. Consulte [Tipos de contadores de rendimiento](https://msdn.microsoft.com/library/z573042h.aspx) para obtener información sobre los tipos de datos que se pueden almacenar en contadores de rendimiento personalizados. Consulte el [ejemplo PerformanceCounters](http://code.msdn.microsoft.com/azure/) para obtener un ejemplo que crea y establece datos de contadores de rendimiento personalizados en un rol web.

## Almacenamiento y visualización de datos de contadores de rendimiento

Azure almacena en caché los datos de contadores de rendimiento junto con otra información de diagnóstico. Estos datos están disponibles para la supervisión remota mientras se está ejecutando la instancia de rol mediante el acceso a Escritorio remoto para ver herramientas como Monitor de rendimiento. Para conservar los datos fuera de la instancia de rol, el agente de diagnóstico debe transferirlos al almacenamiento de Azure. Se puede configurar el límite de tamaño de los datos de contadores de rendimiento en caché en el agente de diagnóstico o se puede configurar como parte de un límite compartido para todos los datos de diagnóstico. Para obtener más información acerca de cómo establecer el tamaño del búfer, consulte [Propiedad DiagnosticMonitorConfiguration.OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) y [DirectoriesBufferConfiguration (Clase)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Consulte [Guardar y ver datos de diagnóstico en el almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) para obtener información general sobre la configuración del agente de diagnóstico para transferir datos a una cuenta de almacenamiento.

Cada instancia de contador de rendimiento configurada se registra a una velocidad de muestreo especificada, y los datos muestreados se transfieren a la cuenta de almacenamiento, ya sea mediante una solicitud de transferencia programada o una solicitud de transferencia a petición. Se pueden programar transferencias automáticas una vez por minuto. Los datos de contadores de rendimiento transferidos por el agente de diagnóstico se almacenan en una tabla, WADPerformanceCountersTable, en la cuenta de almacenamiento. Se puede acceder a esta tabla y se puede consultar con métodos de API de Almacenamiento de Azure estándar. Consulte el [ejemplo PerformanceCounters de Microsoft Azure](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) para obtener un ejemplo de cómo consultar y mostrar datos de contadores de rendimiento desde la tabla WADPerformanceCountersTable.

>[AZURE.NOTE]Según la frecuencia de transferencia del agente de diagnóstico y la latencia de la cola, puede que los datos de contadores de rendimiento más recientes en la cuenta de almacenamiento sean obsoletos desde hace varios minutos.

## Habilitación de contadores de rendimiento mediante el archivo de configuración de diagnóstico

Use el procedimiento siguiente para habilitar los contadores de rendimiento en su aplicación de Azure.

## Requisitos previos

En esta sección se da por supuesto que importó el monitor de diagnóstico en la aplicación y agregó el archivo de configuración de diagnóstico a la solución de Visual Studio (diagnostics.wadcfg en SDK 2.4 y versiones anteriores o diagnostics.wadcfgx en SDK 2.5 y versiones posteriores). Consulte los pasos 1 y 2 en [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](./cloud-services-dotnet-diagnostics.md) para obtener más información.

## Paso 1: Recopilación y almacenamiento de datos de contadores de rendimiento

Una vez que agregue el archivo de diagnóstico a su solución de Visual Studio, puede configurar la recopilación y el almacenamiento de los datos de contadores de rendimiento en una aplicación de Azure. Para ello, agregue contadores de rendimiento al archivo de diagnóstico. Los datos de diagnóstico, incluidos los contadores de rendimiento, se recopilan primero en la instancia. Luego, los datos se mantienen en la tabla WADPerformanceCountersTable del servicio de tablas de Azure, por lo que también deberá especificar la cuenta de almacenamiento de su aplicación. Si está probando su aplicación de manera local en el emulador de proceso, también puede almacenar datos de diagnóstico localmente en el emulador de almacenamiento. Antes de almacenar datos de diagnóstico, primero debe ir al [Portal de Azure clásico](http://manage.windowsazure.com/) y crear una cuenta de almacenamiento. Un procedimiento recomendado es ubicar la cuenta de almacenamiento en la misma ubicación geográfica que su aplicación de Azure, para así evitar pagar costes por ancho de banda externo y reducir la latencia.

### Incorporación de contadores de rendimiento al archivo de diagnóstico

Hay muchos contadores que puede usar. En el ejemplo siguiente, se muestran varios contadores de rendimiento recomendados para la supervisión de roles de trabajo y web.

Abra el archivo de diagnóstico (diagnostics.wadcfg en SDK 2.4 y versiones anteriores o diagnostics.wadcfgx en SDK 2.5 y versiones posteriores) y agregue lo siguiente al elemento DiagnosticMonitorConfiguration:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

El atributo bufferQuotaInMB, que especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para el tipo de recopilación de datos (registros de Azure, registros de IIS, etc.). El valor predeterminado es 0. Cuando se alcanza la cuota, se eliminan los datos más antiguos a medida que se agregan datos nuevos. La suma de todas las propiedades de bufferQuotaInMB debe ser mayor que el valor del atributo OverallQuotaInMB. Para obtener información más detallada acerca de cómo determinar cuánto almacenamiento será necesario para la recopilación de datos de diagnóstico, consulte la sección Configuración de WAD de [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

El atributo scheduledTransferPeriod, que especifica el intervalo entre las transferencias programadas de datos, redondeado al minuto más cercano. En los siguientes ejemplos está definido en PT30M (30 minutos). Definir el período de transferencia en un valor pequeño, como un minuto, afectará de manera negativa el rendimiento de la aplicación en producción, pero puede resultar útil para ver los diagnósticos rápidamente cuando está realizando pruebas. El período de transferencia programado debe ser lo suficientemente corto como para tener la seguridad de que los datos de diagnóstico no se sobrescribirán en la instancia, pero lo suficientemente largo como para no afectar el rendimiento de la aplicación.

El atributo counterSpecifier especifica el contador de rendimiento que se va a recopilar. El atributo sampleRate especifica la velocidad a la que el contador de rendimiento se debe muestrear, en este caso, 30 segundos.

Una vez agregados los contadores de rendimiento que desea recopilar, guarde los cambios en el archivo de diagnóstico. A continuación, deberá especificar la cuenta de almacenamiento en que se mantendrán los datos de diagnóstico.

### Especificar la cuenta de almacenamiento

Para mantener la información de diagnóstico en su cuenta de almacenamiento de Azure, debe especificar una cadena de conexión en su archivo de configuración de servicio (ServiceConfiguration.cscfg).

Para el SDK de Azure 2.5, se puede especificar la cuenta de almacenamiento en el archivo diagnostics.wadcfgx.

>[AZURE.NOTE]Estas instrucciones solo sirven para el SDK de Azure 2.4 y versiones anteriores. Para el SDK de Azure 2.5, se puede especificar la cuenta de almacenamiento en el archivo diagnostics.wadcfgx.

Para definir las cadenas de conexión:

1. Abra el archivo ServiceConfiguration.Cloud.cscfg con el editor de texto de su preferencia y defina la cadena de conexión para su almacenamiento. Los valores *AccountName* y *AccountKey* se encuentran en el Portal de Azure clásico, en el panel de la cuenta de almacenamiento, en Administrar claves.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Guarde el archivo ServiceConfiguration.Cloud.cscfg.

3. Abra el archivo ServiceConfiguration.Local.cscfg y compruebe que el valor UseDevelopmentStorage esté definido como verdadero.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Ahora que las cadenas de conexión están definidas, la aplicación mantendrá los datos de diagnóstico en la cuenta de almacenamiento cuando se implemente la aplicación.
4. Guarde y compile el proyecto y, a continuación, implemente la aplicación.

## Paso 2 (opcional): Creación de contadores de rendimiento personalizados

Además de los contadores de rendimiento predefinidos, puede agregar sus propios contadores de rendimiento personalizados para supervisar roles de trabajo o web. Los contadores de rendimiento personalizados se pueden utilizar para realizar un seguimiento y supervisar el comportamiento específico de la aplicación y se pueden crear o eliminar en una tarea de inicio, rol web o rol de trabajo con permisos elevados.

El agente de Diagnósticos de Azure actualiza la configuración de contador de rendimiento desde el archivo .wadcfg un minuto después del inicio. Si crea contadores de rendimiento personalizados en el método OnStart y las tareas de inicio tardan más de un minuto en ejecutarse, los contadores de rendimiento personalizados no estarán creados cuando el agente de Diagnósticos de Azure intente cargarlos. En este escenario, verá que Diagnósticos de Azure captura correctamente todos los datos de diagnóstico excepto los contadores de rendimiento personalizados. Para resolver este problema, cree los contadores de rendimiento en una tarea de inicio o mueva parte del trabajo de las tareas de inicio al método OnStart después de crear los contadores de rendimiento.

Lleve a cabo los siguientes pasos para crear un contador de rendimiento personalizado simple llamado "\\MyCustomCounterCategory\\MyButton1Counter":

1. Abra el archivo de definición de servicio (CSDEF) para su aplicación.
2. Agregue el elemento Runtime al elemento WebRole o WorkerRole para permitir la ejecución con privilegios elevados:

    ```
    <RuntimeexecutionContext="elevated"/>
    ```
3. Guarde el archivo .
4. Abra el archivo de diagnóstico (diagnostics.wadcfg en SDK 2.4 y versiones anteriores o diagnostics.wadcfgx en SDK 2.5 y versiones posteriores) y agregue lo siguiente a DiagnosticMonitorConfiguration. 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Guarde el archivo .
6. Cree la categoría de contador de rendimiento personalizada en el método OnStart del rol antes de invocar base.OnStart. Si todavía no existe, el siguiente ejemplo de C# crea una categoría personalizada:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Actualice los contadores dentro de la aplicación. En el siguiente ejemplo se actualiza un contador de rendimiento personalizado en eventos de Button1\_Click:

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Guarde el archivo .  

Ahora el monitor de diagnóstico de Azure recopilará datos de contadores de rendimiento personalizados.

## Paso 3: Consulta de datos de contadores de rendimiento

Una vez que la aplicación se ha implementado y está en ejecución, el monitor de diagnóstico comenzará a recopilar contadores de rendimiento y mantendrá esos datos en el almacenamiento de Azure. Use herramientas como el Explorador de servidores de Visual Studio, el [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/) o [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) de Cerebrata para ver los datos de contadores de rendimiento en la tabla WADPerformanceCountersTable. También puede consultar el servicio Tabla mediante programación con [C#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md) o [PHP](../storage/storage-php-how-to-use-table-storage.md).

El siguiente ejemplo de C# muestra una consulta simple contra la tabla WADPerformanceCountersTable y guarda los datos de diagnóstico en un archivo CSV. Una vez que los contadores de rendimiento se guardan en un archivo CSV, puede utilizar las capacidades de gráficos de Microsoft Excel o alguna otra herramienta para visualizar los datos. Asegúrese de agregar una referencia a Microsoft.WindowsAzure.Storage.dll, que está incluido en el SDK de Azure para .NET de octubre de 2012 y posterior. El ensamblado se instala en el directorio %Program Files%\\Microsoft SDKs\\Microsoft Azure.NET SDK\\version-num\\ref\\.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Las entidades se asignan a objetos C# utilizando una clase personalizada derivada de **TableEntity**. El siguiente código define una clase de entidad que representa un contador de rendimiento en la tabla **WADPerformanceCountersTable**.


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## Pasos siguientes

Ahora que ha aprendido los aspectos básicos de la recopilación de contadores de rendimiento, siga estos vínculos para implementar escenarios de solución de problemas más complejos.

[Prácticas recomendadas de solución de problemas para el desarrollo de aplicaciones para Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx)

[Supervisión de servicios en la nube](./how-to-monitor-a-cloud-service.md)

<!----HONumber=AcomDC_1203_2015-->