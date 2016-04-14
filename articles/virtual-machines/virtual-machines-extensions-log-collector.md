<properties
   pageTitle="Extensión de máquina virtual de AzureLogCollector | Microsoft Azure"
   description="Describe la extensión de máquina virtual de AzureLogCollector, que recopila todos los archivos de registro y los reúne en una ubicación de Almacenamiento de Azure."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="powershell"
   ms.topic="article"
   ms.tgt_pltfrm="nad"
   ms.workload="infrastructure"
   ms.date="11/12/2015"
   ms.author="rasquill"/>


# AzureLogCollector Extension

El diagnóstico de problemas con un servicio en la nube de Microsoft Azure requiere la recopilación de archivos de registro del servicio en máquinas virtuales cuando se producen los problemas. Puede usar la extensión AzureLogCollector a petición para realizar una recopilación única de registros provenientes de una o más máquinas virtuales del Servicio en la nube (desde roles web y roles de trabajo) y transferir los archivos recopilados a una cuenta de almacenamiento de Azure, sin iniciar sesión de manera remota en ninguna de las máquinas virtuales.
> [AZURE.NOTE]Puede encontrar descripciones para la mayoría de la información registrada en http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Existen dos modos de recopilación que dependen de los tipos de archivos que se van a recopilar. -Solo registros del agente invitado de Azure (GA). Este modo de recopilación incluye todos los registros relacionados con los agentes invitados de Azure y otros componentes de Azure. -Todos los registros (completo). Este modo de recopilación recopilará todos los archivos en modo GA además de:

  - los registros de eventos de aplicación y del sistema
  
  - registros de errores HTTP
  
  - Registros IIS
  
  - Registros de configuración
  
  - otros registros del sistema

En ambos modos de recopilación, pueden especificarse carpetas de recopilación de datos adicionales mediante una colección de la estructura siguiente:

- **Name**: el nombre de la colección, que se usará como el nombre de la subcarpeta dentro del archivo zip que se va a recopilar.

- **Location**: la ruta de acceso a la carpeta en la máquina virtual donde se recopilará el archivo.

- **SearchPattern**: el patrón de los nombres de archivos que se van a recopilar. El valor predeterminado es "*".

- **Recursive**: si los archivos se recopilarán de forma recursiva en la carpeta.

## Requisitos previos

- Debe tener una cuenta de almacenamiento como extensión para guardar los archivos zip generados.
- Asegúrese de que está usando los cmdlets de Azure PowerShell V0.8.0 o superior. Para más información, consulte [Descargas de Azure](https://azure.microsoft.com/downloads/).

## Adición de la extensión

Puede usar los cmdlets de [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) o las [API de REST de administración de servicios](https://msdn.microsoft.com/library/ee460799.aspx) para agregar la extensión AzureLogCollector.

Para Servicios en la nube, se puede usar el cmdlet de Azure Powershell existente, **Set-AzureServiceExtension**, para habilitar la extensión en instancias del rol del servicio en la nube. Cada vez que esta extensión se habilita a través de este cmdlet, se desencadena la recopilación de registros en las instancias del rol seleccionado de los roles seleccionados.

Para Máquinas virtuales, se puede usar el cmdlet de Azure Powershell existente, **Set-AzureVMExtension**, para habilitar la extensión en Máquinas virtuales. Cada vez que esta extensión se habilita a través de los cmdlets, se desencadena la recopilación de registros en cada instancia.

Internamente, esta extensión usa las clases PublicConfiguration y PrivateConfiguration basadas en JSON. A continuación se describe el diseño de un JSON de ejemplo para la configuración pública y privada.

### PublicConfiguration

    {
        "Instances":  "*",
        "Mode":  "Full",
        "SasUri":  "SasUri to your storage account with sp=wl",
        "AdditionalData":  
        [
          {
                  "Name":  "StorageData",
                  "Location":  "%roleroot%storage",
                  "SearchPattern":  "*.*",
                  "Recursive":  "true"
          },
          {
                "Name":  "CustomDataFolder2",
                "Location":  "c:\customFolder",
                "SearchPattern":  "*.log",
                "Recursive":  "false"
          },
        ]
    }

### PrivateConfiguration

    {
    
    }

> [AZURE.NOTE]Esta extensión no necesita la clase **privateConfiguration**. Basta con proporcionar una estructura vacía para el argumento **-PrivateConfiguration**.

Puede seguir uno de los dos pasos siguientes para agregar AzureLogCollector a una o varias instancias de un servicio en la nube o una máquina virtual de los roles seleccionados, que desencadena las recopilaciones en cada máquina virtual que ejecute y envíe los archivos recopilados a la cuenta de Azure especificada.

## Agregar como extensión de servicio

1. Siga las instrucciones para conectar Azure PowerShell a la suscripción.

2. Especifique el nombre de servicio, la ranura, los roles y las instancias de rol a los que desea agregar y habilitar la extensión AzureLogCollector.

        #Specify your cloud service name
        $ServiceName = 'extensiontest2'
        
        #Specify the slot. 'Production' or 'Staging'   
        $slot = 'Production'
        
        #Specified the roles on which the extension will be installed and enabled
        $roles = @("WorkerRole1","WebRole1")
        
        #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
        $instances = @("*")
        
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"

3. Especifique la carpeta de datos adicional en la que se recopilarán los archivos (este paso es opcional).

        #add one location
        $a1 = New-Object PSObject 
        
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"  
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
        
        $AdditionalDataList+= $a1
              #more locations can be added....
  
    > [AZURE.NOTE] Puede usar el token `%roleroot%` para especificar la unidad raíz del rol, ya que no usa una unidad fija.

4. Proporcione el nombre de la cuenta de almacenamiento de Azure y la clave en la que se cargarán los archivos recopilados.

        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'

5. Llame a SetAzureServiceLogCollector.ps1 (incluido al final del artículo) como se indica a continuación para habilitar la extensión AzureLogCollector para un servicio en la nube. Una vez completada la ejecución, puede encontrar el archivo cargado en `https://YouareStorageAccountName.blob.core.windows.net/vmlogs`

        .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList

A continuación se indica la definición de los parámetros pasados al script. (También se copia a continuación).

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
      [Parameter(Mandatory=$true)]  
    [string]   $ServiceName,
      
    [Parameter(Mandatory=$false)] 
    [string[]] $Roles ,
      
    [Parameter(Mandatory=$false)] 
    [string[]] $Instances,
      
    [Parameter(Mandatory=$false)] 
    [string]   $Slot = 'Production',
      
    [Parameter(Mandatory=$false)] 
    [string]   $Mode = 'Full',
      
    [Parameter(Mandatory=$false)] 
    [string]   $StorageAccountName,
      
    [Parameter(Mandatory=$false)] 
    [string]   $StorageAccountKey,
      
    [Parameter(Mandatory=$false)] 
    [PSObject[]] $AdditionDataLocationList = $null
    )

- *ServiceName*: el nombre del servicio en la nube.

- *Roles*: una lista de roles, como "WebRole1" o "WorkerRole1".

- *Instances*: una lista de los nombres de instancias de rol separados por coma. Use la cadena comodín ("*") para todas las instancias de rol.

- *Slot*: nombre de la ranura. “Production” o “Staging”.

- *Mode*: modo de recopilación. “Full” o “GA”.

- *StorageAccountName*: nombre de la cuenta de almacenamiento de Azure para almacenar los datos recopilados.

- *StorageAccountKey*: nombre de la clave de la cuenta de almacenamiento de Azure.

- *AdditionalDataLocationList*: lista de la siguiente estructura:

      { String Name, String Location, String SearchPattern, Bool Recursive }
             
            
## Agregar como extensión de servicio de máquina virtual

Siga las instrucciones para conectar Azure PowerShell a la suscripción.

1. Especifique el nombre del servicio, la máquina virtual y el modo de recopilación.

        #Specify your cloud service name
        $ServiceName = 'YourCloudServiceName'
        
        #Specify the VM name
        $VMName = "'YourVMName'"
        
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
        
        Specify the additional data folder for which files will be collected (this step is optional).
        
        #add one location
        $a1 = New-Object PSObject 
        
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"  
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
        
        $AdditionalDataList+= $a1
              #more locations can be added....

2. Proporcione el nombre de la cuenta de almacenamiento de Azure y la clave en la que se cargarán los archivos recopilados.

        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
        
3. Llame a SetAzureVMLogCollector.ps1 (incluido al final del artículo) como se indica a continuación para habilitar la extensión AzureLogCollector para un servicio en la nube. Una vez completada la ejecución, puede encontrar el archivo cargado en https://YouareStorageAccountName.blob.core.windows.net/vmlogs

  
A continuación se indica la definición de los parámetros pasados al script. (También se copia a continuación).

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
        [Parameter(Mandatory=$true)]  
      [string]   $ServiceName,
        
      [Parameter(Mandatory=$false)] 
      [string] $VMName ,
        
        [Parameter(Mandatory=$false)] 
      [string]   $Mode = 'Full',
        
      [Parameter(Mandatory=$false)] 
      [string]   $StorageAccountName,
        
      [Parameter(Mandatory=$false)] 
      [string]   $StorageAccountKey,
        
      [Parameter(Mandatory=$false)] 
      [PSObject[]] $AdditionDataLocationList = $null
      )

- ServiceName: nombre del servicio en la nube.

- VMName: nombre de la máquina virtual.

- Mode: modo de recopilación. “Full” o “GA”.

- StorageAccountName: nombre de la cuenta de almacenamiento de Azure para almacenar los datos recopilados.

- StorageAccountKey: nombre de la clave de la cuenta de almacenamiento de Azure.

- AdditionalDataLocationList: lista de la siguiente estructura:
      
```
      {
        String Name,
        String Location,
        String SearchPattern,
        Bool   Recursive  
      }
```

## Archivos del script de PowerShell de extensión

SetAzureServiceLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
                  [Parameter(Mandatory=$true)]  
                  [string]   $ServiceName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string[]] $Roles ,
                  
                  [Parameter(Mandatory=$false)] 
                  [string[]] $Instances = '*',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Slot = 'Production',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Mode = 'Full',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountKey,
                  
                  [Parameter(Mandatory=$false)] 
                  [PSObject[]] $AdditionDataLocationList = $null
            )
    
    $publicConfig = New-Object PSObject
      
    if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
    {
        $instanceText = $Instances[0] 
        for ($i = 1;$i -lt $Instances.Count;$i++)
        { 
              $instanceText = $instanceText+ "," + $Instances[$i]  
          }
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
    }
    else  #For all instances if not specified.  The value should be a space or *
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
    }
    
    if ($Mode -ne $null ) 
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }
    
    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey 
      
    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName} 
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."  
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }
      
    $ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri
    
    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )  
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }
    
    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
    "publicConfig is:  $publicConfigJSON"
      
    #we just provide a empty privateConfig object
    $privateconfig = "{
    }"
    
    if ($Roles -ne $null)
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose 
    }
    else
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose 
    }
    
    #
    #This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
    #
    $SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
    $SasUri = $SasUri + "&restype=container&comp=list"
    Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"


SetAzureVMLogCollector.ps1


    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
                  [Parameter(Mandatory=$true)]  
                  [string]   $ServiceName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string] $VMName ,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Mode = 'Full',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountKey,
                  
                  [Parameter(Mandatory=$false)] 
                  [PSObject[]] $AdditionDataLocationList = $null
            )
    
    $publicConfig = New-Object PSObject
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"
    
    if ($Mode -ne $null ) 
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }
    
    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey 
      
    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName} 
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."  
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }
      
    $ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri
    
    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )  
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }
    
    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
      
    Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"
    
    #
    #we just provide a empty privateConfig object
    #
    $privateconfig = "{
    }"
    
    if ($VMName -ne $null ) 
    {
          $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
          $VM.VM.OSVirtualHardDisk.OS
          
          if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
          {
                Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose 
                
                #
                #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
                #the presence of SubstatusList field.   
                #
                $Completed = $false
                while ($Completed -ne $true)
                {
                        $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                        $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}
                        
                        if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                        {
                            Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                              $Completed = $true
                        }
                        elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                        {
                              $Completed = $false
                              Write-Output "Waiting for operation to complete..."
                        }
                        else
                        {
                              $Completed = $true
                              Write-Output "Operation completed."
            
                        $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                              $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)
    
                      # 
                            # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                              #
                              $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                              $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context
    
                            Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"
                              
                              #
                              #This is an optional step:  Remove the extension after we are done
                              #
                              Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose  
                              
                        }
                        Start-Sleep -s 5
                }
          }
          else
          {
              Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
          }
          
    }
    else
    {
      Write-Output "VM name is not specified, the extension cannot be enabled"
    }
    
## Pasos siguientes

Ahora puede examinar o copiar los registros desde una ubicación muy sencilla.

<!---HONumber=AcomDC_0128_2016-->