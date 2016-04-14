<properties
   pageTitle="Descripción general de la configuración de Reliable Services de Azure Service Fabric | Microsoft Azure"
   description="Obtenga información sobre cómo configurar Reliable Services con estado en Service Fabric."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="sumukhs"/>

# Configuración de Reliable Services con estado
Puede modificar la configuración predeterminada de Reliable Services con estado mediante el paquete de configuración (Config) o la implementación del servicio (código).

+ **Config**: la configuración mediante el paquete de configuración se realiza cambiando el archivo Settings.xml generado en la raíz del paquete de Visual Studio, en la carpeta Config de cada servicio de la aplicación.
+ **Código**: la configuración mediante código se realiza invalidando StatefulService.CreateReliableStateManager y creando un objeto ReliableStateManager mediante un objeto ReliableStateManagerConfiguration con el conjunto de opciones adecuado.

De forma predeterminada, el tiempo de ejecución de Azure Service Fabric busca los nombres de sección predefinidos en el archivo Settings.xml y usa los valores de configuración mientras crea los componentes en tiempo de ejecución subyacentes.

>[AZURE.NOTE] **No** elimine los nombres de sección de las siguientes configuraciones en el archivo Settings.xml que se genera en la solución de Visual Studio, a menos que planee configurar el servicio mediante código. Cambiar los nombres del paquete de configuración o la sección requiere un cambio de código al configurar ReliableStateManager.


## Configuración de seguridad del replicador
Las configuraciones de seguridad del replicador se usan para proteger el canal de comunicación que se usa durante la replicación. Esto significa que los servicios no podrán ver el tráfico de replicación del otro, lo que garantiza que los datos de alta disponibilidad también están seguros. De forma predeterminada, una sección de configuración de seguridad vacía impide la seguridad de la replicación.

### Nombre de sección predeterminado
ReplicatorSecurityConfig

>[AZURE.NOTE] Para cambiar este nombre de sección, reemplace el parámetro replicatorSecuritySectionName por el constructor ReliableStateManagerConfiguration al crear el ReliableStateManager para este servicio.


## Configuración de replicador
Las configuraciones de replicador configuran el replicador responsable de hacer que el estado de Reliable Service con estado sea muy confiable replicando y conservando el estado localmente. La configuración predeterminada es generada por la plantilla de Visual Studio y debe ser suficiente. En esta sección se habla sobre las configuraciones adicionales que están disponibles para optimizar el replicador.

### Nombre de sección predeterminado
ReplicatorConfig

>[AZURE.NOTE] Para cambiar este nombre de sección, reemplace el parámetro replicatorSettingsSectionName por el constructor ReliableStateManagerConfiguration al crear el ReliableStateManager para este servicio.


### Nombres de configuración
|Nombre|Unidad|Valor predeterminado|Comentarios|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0,05|Período de tiempo durante el que el replicador del secundario espera después de recibir una operación antes de enviar una confirmación al principal. El resto de confirmaciones que se enviarán para las operaciones que se procesan dentro de este intervalo se envían como una respuesta.|
|ReplicatorEndpoint|N/D|Ningún valor predeterminado: parámetro obligatorio|Dirección IP y puerto que usará el replicador principal y secundario para comunicarse con otros replicadores del conjunto de réplicas. Esto debe hacer referencia a un punto de conexión de recursos de TCP en el manifiesto de servicio. Consulte [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md) para más información sobre cómo definir los recursos de punto de conexión en un manifiesto de servicio. |
|MaxPrimaryReplicationQueueSize|Número de operaciones|8192|Número máximo de operaciones de la cola principal. Una operación se libera después de que el replicador principal reciba una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2.|
|MaxSecondaryReplicationQueueSize|Número de operaciones|16384|Número máximo de operaciones de la cola secundaria. Una operación se libera después de que su estado pase a ser de alta disponibilidad mediante persistencia. Este valor debe ser mayor que 64 y una potencia de 2.|
|CheckpointThresholdInMB|MB|200|Cantidad del espacio del archivo de registro después de que se compruebe el estado.|
|MaxRecordSizeInKB|KB|1024|Tamaño del registro de mayor tamaño el replicador que puede escribir en el registro. Este valor debe ser un múltiplo de 4 y superior a 16.|
|OptimizeLogForLowerDiskUsage|Booleano|true|Cuando su valor es true, el registro se configura para que el archivo de registro específico de la réplica se cree usando un archivo disperso de NTFS. Esto reduce el uso del espacio en disco real del archivo. Cuando su valor es false, el archivo se crea con asignaciones fijas que ofrecen el mejor rendimiento de escritura.|
|MaxRecordSizeInKB|KB|1024|Tamaño del registro de mayor tamaño el replicador que puede escribir en el registro. Este valor debe ser un múltiplo de 4 y superior a 16.|
|SharedLogId|guid|""|Especifica un guid único que debe usarse para identificar el archivo de registro compartido que se usa con esta réplica. Normalmente, los servicios no deben usar esta opción de configuración. Sin embargo, si se especifica SharedLogId, también se debe especificar SharedLogPath.|
|SharedLogPath|Nombre de ruta de acceso completo|""|Especifica la ruta de acceso completa donde se creará el archivo de registro compartido para esta réplica. Normalmente, los servicios no deben usar esta opción de configuración. Sin embargo, si se especifica SharedLogPath, también se debe especificar SharedLogId.|


## Ejemplo de configuración mediante código
```csharp
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            new ReliableStateManagerReplicatorSettings
            {
                RetryInterval = TimeSpan.FromSeconds(3)
            }));
}
```


## Archivo de configuración de ejemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


## Comentarios
BatchAcknowledgementInterval controla la latencia de replicación. Un valor de "0" ofrecerá la menor latencia posible, a costa del rendimiento (como deben enviarse y procesarse más mensajes de confirmación, cada uno con menos confirmaciones). Cuanto mayor sea el valor de BatchAcknowledgementInterval, mayor será el rendimiento general de la replicación a costa de una mayor latencia de la operación. Esto se traduce directamente en la latencia de transacciones confirmadas.

El valor de CheckpointThresholdInMB controla la cantidad de espacio en disco que el replicador puede usar para almacenar información de estado en el archivo de registro específico de la réplica. Aumentar este valor a un valor mayor que el predeterminado puede acelerar los tiempos de reconfiguración cuando se agrega una nueva réplica al conjunto. Esto se debe a la transferencia de estado parcial que tiene lugar debido a la disponibilidad de mayor cantidad de historial de operaciones en el registro. Esto podría aumentar el tiempo de recuperación de una réplica después de un error.

Si establece OptimizeForLowerDiskUsage en true, el espacio del archivo de registro se sobreaprovisionará para que las réplicas activas puedan almacenar más información de estado en sus archivos de registro, mientras que las réplicas inactivas usarán menos espacio en disco. Esto permite hospedar más réplicas en un nodo. Si establece OptimizeForLowerDiskUsage en false, la información de estado se escribe en los archivos de registro más rápidamente.

El parámetro MaxRecordSizeInKB define el tamaño máximo de un registro que el replicador puede escribir en el archivo de registro. En la mayoría de los casos, el tamaño predeterminado de 1024 KB del registro es óptimo. Sin embargo, si el servicio hace que elementos de datos de mayor tamaño formen parte de la información de estado, es posible que este valor se tenga que aumentar. Cambiar MaxRecordSizeInKB para que tenga un tamaño inferior a 1024 presenta pocas ventajas, ya que los registros más pequeños solamente usan el espacio necesario para el registro más pequeño. Se espera que este valor solo tenga que cambiarse en raras ocasiones.

Los parámetros SharedLogId y SharedLogPath siempre se usan en conjunto para obligar a un servicio a usar un registro compartido independiente del registro compartido predeterminado del nodo. Para obtener una mayor eficacia, todos los servicios posibles deben especificar el mismo registro compartido. Para reducir la contención del movimiento de encabezados, los archivos de registro compartidos deben colocarse en discos que se usen únicamente para el archivo de registro compartido. Se espera que este valor solo tenga que cambiarse en raras ocasiones.

<!---HONumber=AcomDC_0204_2016-->