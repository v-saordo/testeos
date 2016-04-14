<properties
	pageTitle="Cómo usar Diagnósticos de Azure (.NET) con Servicios en la nube | Microsoft Azure"
	description="Use Diagnósticos de Azure para recopilar datos de los Servicios en la nube de Azure para realizar tareas de depuración, medición de rendimiento, supervisión, análisis de tráfico y más."
	services="cloud-services"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="robb"/>



# Habilitación de diagnósticos de Azure en servicios en la nube de Azure

Consulte [Introducción a Diagnósticos de Azure](../azure-diagnostics.md) para obtener información sobre Diagnósticos de Azure.


## Habilitación de Diagnósticos en un rol de trabajo

En este tutorial se describe cómo implementar un rol de trabajo de Azure que emite datos de telemetría con la clase EventSource de .NET. Diagnósticos de Azure se usa para recopilar datos de telemetría y almacenarla en una cuenta de almacenamiento de Azure. Al crear un rol de trabajo, Visual Studio habilita automáticamente Diagnósticos 1.0 como parte de la solución en los SDK de Azure para .NET 2.4 y versiones anteriores. En las instrucciones siguientes se describe el proceso para crear el rol de trabajo, deshabilitar Diagnósticos 1.0 de la solución e implementar Diagnósticos 1.2 o 1.3 en el rol de trabajo.

### Requisitos previos
En este artículo se supone que tiene una suscripción a Azure y usa Visual Studio 2013 con el SDK de Azure. Si no tiene una suscripción de Azure, puede registrarse para obtener una [prueba gratuita][]. Asegúrese de [instalar y configurar Azure PowerShell versión 0.8.7 o posterior][].

### Paso 1: crear roles de trabajo
1.	Inicie **Visual Studio 2013**.
2.	Cree un nuevo proyecto de **Servicio en la nube de Azure** desde la plantilla **Nube** que tiene como destino .NET Framework 4.5. Asigne al proyecto el nombre "WadExample" y haga clic en Aceptar.
3.	Seleccione **Rol de trabajo** y haga clic en Aceptar. Se creará el proyecto.
4.	En el **Explorador de soluciones**, haga doble clic en el archivo de propiedades **WorkerRole1**.
5.	En la pestaña **Configuración**, desactive **Habilitar Diagnósticos** para deshabilitar Diagnósticos 1.0 (SDK de Azure SDK 2.4 y anteriores).
6.	Compile la solución para comprobar que no hay errores.

### Paso 2: instrumentar el código
Reemplace el contenido de WorkerRole.cs por el código siguiente. La clase SampleEventSourceWriter, heredada de la [clase EventSource][], implementa cuatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** y **HighFreq**. El primer parámetro del método **WriteEvent** define el identificador para el evento correspondiente. El método Run implementa un bucle infinito que llama a cada uno de los métodos de registro implementados en la clase **SampleEventSourceWriter** cada 10 segundos.

	using Microsoft.WindowsAzure.ServiceRuntime;
	using System;
	using System.Diagnostics;
	using System.Diagnostics.Tracing;
	using System.Net;
	using System.Threading;

	namespace WorkerRole1
	{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
	}


### Paso 3: implementar roles de trabajo
1.	Implemente su rol de trabajo en Azure desde Visual Studio seleccionando el proyecto **WadExample** en el Explorador de soluciones y luego **Publicar** en el menú **Compilar**.
2.	Elija su suscripción.
3.	En el cuadro de diálogo **Configuración de publicación de Microsoft Azure**, seleccione **Crear nuevo…**.
4.	En el cuadro de diálogo **Crear servicio en la nube y cuenta de almacenamiento**, escriba un **Nombre** (por ejemplo, "WadExample") y seleccione una región o un grupo de afinidad.
5.	Establezca el **Entorno** en **Ensayo**.
6.	Modifique cualquier otro parámetro de **Configuración** según sea necesario y haga clic en **Publicar**.
7.	Una vez finalizada la implementación, compruebe en el Portal de Azure clásico que el servicio en la nube está en el estado **En ejecución**.

### Paso 4: crear el archivo de configuración de Diagnósticos e instalar la extensión
1.	Descargue la definición del esquema del archivo de configuración público ejecutando el comando de PowerShell siguiente: 2. (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.	Agregue un archivo XML al proyecto **WorkerRole1** haciendo clic con el botón derecho en el proyecto **WorkerRole1** y seleccione **Agregar** -> **Nuevo elemento… ->** -> **Elementos de Visual C# ->** -> **Datos** -> **Archivo XML**. Asigne al archivo el nombre "WadExample.xml".

	![CloudServices\_diag\_add\_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	Asocie WadConfig.xsd al archivo de configuración. Asegúrese de que la ventana del editor de WadExample es la ventana activa. Presione **F4** para abrir la ventana **Propiedades**. Haga clic en la propiedad **Esquemas** de la ventana **Propiedades**. Haga clic en **…** en la propiedad **Esquemas**. Haga clic en **Agregar…** y vaya a la ubicación en la que ha guardado el archivo XSD y seleccione el archivo WadConfig.xsd. Haga clic en **Aceptar**.
4.	Reemplace el contenido del archivo de configuración WadExample.xml por el siguiente archivo XM y guarde el archivo. Este archivo de configuración define un par de contadores de rendimiento para recopilar: uno para la utilización de la CPU y el otro para la utilización de memoria. A continuación, la configuración define los cuatro eventos correspondientes a los métodos de la clase SampleEventSourceWriter.

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### Paso 5: instalar Diagnósticos en roles de trabajo
Los cmdlets de PowerShell para administrar Diagnósticos en un rol web o de trabajo son: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension y Remove-AzureServiceDiagnosticsExtension.

1.	Abra Azure PowerShell.
2.	Ejecute el script para instalar Diagnósticos en su rol de trabajo (reemplace *StorageAccountKey* por la clave de cuenta de almacenamiento para la clave de almacenamiento wadexample):

```
	$storage_name = "wadexample"
	$key = "<StorageAccountKey>"
	$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
	$service_name="wadexample"
	$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
	Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### Paso 6: consultar los datos de telemetría
En el **Explorador de servidores** de Visual Studio, vaya a la cuenta de almacenamiento wadexample. Cuando el servicio en la nube lleve ejecutándose unos 5 minutos, debería ver las tablas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** y **WADSetOtherTable**. Haga doble clic en una de las tablas para ver la telemetría que se ha recopilado. ![CloudServices\_diag\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)


## Esquema del archivo de configuración

El archivo de configuración de Diagnósticos define valores que se usan para inicializar la configuración de diagnóstico al iniciar el agente de diagnósticos. Consulte en la [referencia de esquema más reciente](https://msdn.microsoft.com/library/azure/mt634524.aspx) los valores válidos y ejemplos.

## Solución de problemas

Si tiene problemas, consulte [Solución de problemas de Diagnósticos de Azure](../azure-diagnostics-troubleshooting.md) para obtener ayuda relacionada con problemas comunes.

## Pasos siguientes
[Vea una lista de artículos sobre Diagnósticos de Azure relacionados con máquinas virtuales](azure-diagnostics.md#cloud-services) para cambiar los datos que se van a recopilar, solucionar problemas u obtener más información sobre los diagnósticos en general.


[clase EventSource]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[instalar y configurar Azure PowerShell versión 0.8.7 o posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-->