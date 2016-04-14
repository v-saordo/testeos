<properties
	pageTitle="Actualización del script de PowerShell para el proyecto del grupo de recursos de Azure | Microsoft Azure"
	description="Se describen los pasos necesarios para actualizar manualmente el script de PowerShell que se incluye con los proyectos de implementación del grupo de recursos de Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/04/2016"
	ms.author="tarcher" />

# Actualización del script de PowerShell para el proyecto del grupo de recursos de Azure

El proyecto de implementación del grupo de recursos de Azure en Visual Studio usa un script de Azure PowerShell como ayuda para preparar e implementar los activos en Azure desde Visual Studio. La versión de Azure PowerShell que se usa en el script del proyecto es la 0.9.8. Sin embargo, se ha lanzado una nueva versión de Azure PowerShell, la 1.0.

Aunque la nueva versión de Azure PowerShell incorpora nuevas funcionalidades y emplea el Administrador de recursos de Azure como experiencia de primera clase, se realizaron cambios en varias funciones de cmdlet que hacen que la versión anterior del script sea incompatible. Sin embargo, puede modificar el script para corregir estos problemas y luego ejecutar el script fuera de Visual Studio en una ventana de comandos de PowerShell. En este artículo se ofrece información sobre qué correcciones se deben realizar y dónde. Además de las correcciones específicas, al final de este artículo se muestra una copia del script completamente modificado.

## Opciones de solución alternativa

Después de instalar el nuevo Azure PowerShell, se pueden producir problemas en dos direcciones en el proyecto del grupo de recursos de Azure.

- Como en el cuadro de diálogo de implementación de Visual Studio no se van a encontrar los nuevos cmdlets de Azure, se le pedirá que los instale. Sin embargo, como los módulos han cambiado de nombre en la nueva versión de los cmdlets, Visual Studio no podrá encontrarlos. Como consecuencia, se puede quedar atascado en un bucle si intenta instalarlos desde Visual Studio.

- Si ejecuta el script fuera de Visual Studio en la ventana de comandos de PowerShell, obtendrá el error:

	*El término 'Switch-AzureMode' no se reconoce como nombre de un cmdlet, función, archivo de script o programa ejecutable. Compruebe si escribió correctamente el nombre o, si incluyó una ruta de acceso, compruebe que dicha ruta es correcta e inténtelo de nuevo.*

Si el nuevo Azure PowerShell impide la implementación del proyecto del grupo de recursos de Azure, estas son las opciones para corregir el problema.

- Puede desinstalar la versión más reciente de Azure PowerShell y reinstalar la versión anterior (0.9.8) mediante el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).

- Puede realizar cambios específicos dirigidos en el script de PowerShell del proyecto de implementación para solucionar los problemas y luego ejecutar ese script manualmente en la ventana de comandos de PowerShell. No podrá ejecutar el script desde el menú **Implementar** de Visual Studio. Los pasos para actualizar el script de PowerShell se muestran más adelante en este artículo, así como una copia del script completamente actualizado.

## Actualización del script de Azure PowerShell
Estas instrucciones hacen referencia a los números de línea. Para habilitar la numeración de líneas en Visual Studio, consulte [Cómo: Mostrar los números de línea en el editor](https://msdn.microsoft.com/library/ms165340.aspx).

1. En la línea 61, reemplace el siguiente código:

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	por:

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
	```

1. En la línea 87, reemplace el siguiente código:

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	por:

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. En la línea 95, reemplace este código:

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	por el siguiente:

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	New-AzureRMResourceGroupDeployment `
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Verbose -Force
	```

## Script actualizado
Este es el script modificado con todas las actualizaciones mencionadas anteriormente realizadas.

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-Verbose -Force

```

<!---HONumber=AcomDC_0218_2016-->