<properties
	pageTitle="Uso de Diagnósticos de Azure en máquinas virtuales | Microsoft Azure"
	description="Use Diagnósticos de Azure para recopilar datos de las máquinas virtuales de Azure para realizar tareas de depuración, medición de rendimiento, supervisión, análisis de tráfico y más."
	services="virtual-machines"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>



# Habilitación de diagnósticos en máquinas virtuales de Azure

Consulte [Introducción a Diagnósticos de Azure](azure-diagnostics.md) para obtener información sobre Diagnósticos de Azure.

## Habilitación de Diagnósticos en una máquina virtual

En este tutorial se describe cómo instalar Diagnósticos de forma remota en una máquina virtual de Azure desde un equipo de desarrollo. También aprenderá a implementar una aplicación que se ejecuta en esa máquina virtual de Azure y emite datos de telemetría con la [clase EventSource][] de .NET. Diagnósticos de Azure se usa para recopilar la telemetría y almacenarla en una cuenta de almacenamiento de Azure.

### Requisitos previos
En este tutorial se supone que tiene una suscripción a Azure y usa Visual Studio 2013 con el SDK de Azure. Si no tiene una suscripción de Azure, puede registrarse para obtener una [prueba gratuita][]. Asegúrese de [instalar y configurar Azure PowerShell versión 0.8.7 o posterior][].

### Paso 1: crear una máquina virtual
1.	En el equipo del desarrollador, inicie Visual Studio 2013.
2.	En el **Explorador de servidores** de Visual Studio, expanda **Azure**, haga clic con el botón derecho en **Máquinas virtuales** y, a continuación, seleccione **Crear máquina virtual**.
3.	Seleccione su suscripción de Azure en el cuadro de diálogo **Elegir una suscripción** y haga clic en **Siguiente**.
4.	Seleccione **Windows Server 2012 R2 Datacenter, noviembre de 2014** en el cuadro de diálogo **Seleccionar una imagen de máquina virtual** y haga clic en **Siguiente**.
5.	En **Configuración básica de máquina virtual**, establezca el nombre de la máquina virtual en "wadexample". Establezca su nombre de usuario y contraseña de administrador y haga clic en **Siguiente**.
6.	En el cuadro de diálogo **Configuración del servicio en la nube**, cree un nuevo servicio en la nube denominado "wadexampleVM". Cree una nueva cuenta de almacenamiento con el nombre "wadexample" y haga clic en **Siguiente**.
7.	Haga clic en **Crear**.

### Paso 2: crear la aplicación
1.	En el equipo del desarrollador, inicie Visual Studio 2013.
2.	Cree una aplicación de consola de Visual C# nueva dirigida a .NET Framework 4.5. Asigne al proyecto el nombre "WadExampleVM". ![CloudServices\_diag\_new\_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.	Reemplace el contenido de Program.cs por el código siguiente. La clase **SampleEventSourceWriter**, implementa cuatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** y **HighFreq**. El primer parámetro del método WriteEvent define el identificador para el evento correspondiente. El método Run implementa un bucle infinito que llama a cada uno de los métodos de registro implementados en la clase **SampleEventSourceWriter** cada 10 segundos.

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
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

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

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
    	}
		}


4.	Guarde el archivo y seleccione **Compilar solución** en el menú **Compilar** para compilar el código.


### Paso 3: implementar la aplicación
1.	Haga clic con el botón derecho en el proyecto **WadExampleVM** del **Explorador de soluciones** y seleccione **Abrir carpeta en el Explorador de archivos**.
2.	Vaya a la carpeta *bin\\Debug* y copie todos los archivos (WadExampleVM.*)
3.	En el **Explorador de servidores**, haga clic con el botón derecho en la máquina virtual y elija **Conectar utilizando Escritorio remoto**.
4.	Una vez conectado a la máquina virtual, cree una carpeta con el nombre WadExampleVM y pegue los archivos de la aplicación en la carpeta.
5.	Inicie la aplicación WadExampleVM.exe. Debe ver una ventana de la consola en blanco.

### Paso 4: crear la configuración de Diagnósticos e instalar la extensión
1.	Descargue la definición del esquema del archivo de configuración público en su equipo de desarrollo ejecutando el comando de PowerShell siguiente:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.	Abra un nuevo archivo XML en Visual Studio, ya sea en un proyecto que ya tenga abierto o en una instancia de Visual Studio sin proyectos abiertos. En Visual Studio, seleccione **Agregar** -> **Nuevo elemento...** -> **Elementos de visual C#** -> **Datos** -> **Archivo XML**. Asigne al archivo el nombre "WadExample.xml".
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

### Paso 5: instalar Diagnósticos de forma remota en la máquina virtual de Azure
Los cmdlets de PowerShell para administrar Diagnósticos en una máquina virtual son: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension y Remove-AzureVMDiagnosticsExtension.

1.	En el equipo del desarrollador, abra Azure PowerShell.
2.	Ejecute el script para instalar de forma remota Diagnósticos en la máquina virtual (reemplace *StorageAccountKey* por la clave de cuenta de almacenamiento para la clave de almacenamiento wadexample):

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### Paso 6: consultar los datos de telemetría
En el **Explorador de servidores** de Visual Studio, vaya a la cuenta de almacenamiento wadexample. Cuando la máquina virtual lleve ejecutándose unos 5 minutos, debería ver las tablas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** y **WADSetOtherTable**. Haga doble clic en una de las tablas para ver la telemetría que se ha recopilado.

![CloudServices\_diag\_wadexamplevm\_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## Esquema del archivo de configuración

El archivo de configuración de Diagnósticos define valores que se usan para inicializar la configuración de diagnóstico al iniciar el agente de diagnóstico. Consulte en la [referencia de esquema más reciente](https://msdn.microsoft.com/library/azure/mt634524.aspx) los valores válidos y ejemplos.

## Solución de problemas

Consulte [Solución de problemas de Diagnósticos de Azure](azure-diagnostics-troubleshooting.md) para obtener más información.


## Pasos siguientes
[Vea una lista de artículos sobre Diagnósticos de Azure relacionados con máquinas virtuales](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) para cambiar los datos que se van a recopilar, solucionar problemas u obtener más información sobre los diagnósticos en general.


[clase EventSource]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[instalar y configurar Azure PowerShell versión 0.8.7 o posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-->