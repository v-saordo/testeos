<properties
	pageTitle="Implementación y administración de copias de seguridad para Windows Server o cliente de Windows mediante PowerShell | Microsoft Azure"
	description="Obtenga información sobre cómo implementar y administrar Copia de seguridad de Azure mediante PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/22/2016" 
	ms.author="markgal"; "aashishr"; "jimpark"/>


# Implementación y administración de copias de seguridad en Azure para Windows Server o cliente de Windows mediante PowerShell
En este artículo se muestra cómo usar PowerShell para configurar la Copia de seguridad de Azure en un servidor o un cliente de Windows y para administrar copias de seguridad y recuperaciones.

## Azure PowerShell
En octubre de 2015, se lanzó Azure PowerShell 1.0. Esta versión sucedió a la versión 0.9.8 e introdujo algunos cambios importantes, sobre todo en el patrón de nombres de los cmdlets. Los cmdlets de 1.0 siguen el patrón de nomenclatura {verbo}-AzureRm{nombre}; en el que, los nombres de 0.9.8 no incluyen **Rm** (por ejemplo, New-AzureRmResourceGroup en lugar de New-AzureResourceGroup). Al usar Azure PowerShell 0.9.8, primero debe habilitar el modo de Administrador de recursos mediante la ejecución del comando **Switch-AzureMode AzureResourceManager**. Este comando no es necesario en la versión 1.0 o posteriores.

Si desea utilizar scripts escritos para los entornos de 0.9.8, de 1.0 o posterior, debe probar detenidamente los scripts en un entorno de preproducción antes de usarlos en producción para evitar un impacto inesperado.

[Descargue la versión de PowerShell más reciente](https://github.com/Azure/azure-powershell/releases) (la versión mínima necesaria es: 1.0.0)


[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]


## Creación de un almacén de copia de seguridad

> [AZURE.WARNING] La primera vez que los clientes usen Azure Backup deben registrar el proveedor de Azure Backup que se va a usar con su suscripción. Para ello, ejecute el siguiente comando: Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

Puede crear un nuevo almacén de copia de seguridad con el cmdlet **New-AzureRMBackupVault**. El almacén de copia de seguridad es un recurso ARM, por lo que necesita colocarlo dentro de un grupo de recursos. En una consola de Azure PowerShell, ejecute los comandos siguientes:

```
PS C:\> New-AzureResourceGroup –Name “test-rg” -Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Utilice el cmdlet **Get-AzureRMBackupVault** para enumerar los almacenes de copia de seguridad en una suscripción.


## Instalación del agente de Copia de seguridad de Azure
Antes de instalar el agente de copia de seguridad de Azure, necesitará tener el instalador descargado y disponible en el servidor de Windows. Puede obtener la versión más reciente del instalador en el [Centro de descarga de Microsoft ](http://aka.ms/azurebackup_agent) o en la página Panel del almacén de copia de seguridad. Guarde el instalador en una ubicación que tenga fácil acceso, como *C:\\Downloads*.

Para instalar el agente, ejecute el comando siguiente en una consola de PowerShell con privilegios elevados:

```
PS C:\> MARSAgentInstaller.exe /q
```

Esto instala el agente con todas las opciones predeterminadas. La instalación está unos minutos en segundo plano. Si no se especifica la opción */nu*, se abrirá la ventana de **Windows Update** al final de la instalación para comprobar si hay actualizaciones. Una vez instalado, el agente se mostrará en la lista de programas instalados.

Para ver la lista de programas instalados, vaya a **Panel de Control** > **programas** > **programas y características**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

### Opciones de instalación

Para ver todas las opciones disponibles a través de la línea de comandos, use el siguiente comando:

```
PS C:\> MARSAgentInstaller.exe /?
```

Las opciones disponibles incluyen:

| Opción | Detalles | Valor predeterminado |
| ---- | ----- | ----- |
| /q | Instalación desatendida | - | 
| /p:"ubicación" | Ruta de acceso a la carpeta de instalación del agente de Copia de seguridad de Azure. | C:\Archivos de programa\Microsoft Azure Recovery Services Agent |
| /s:"ubicación" | Ruta de acceso a la carpeta de caché del agente de Copia de seguridad de Azure. | C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch |
| /m | Participar en Microsoft Update | - |
| /nu | No comprobar si hay actualizaciones cuando finalice la instalación | - |
| /d | Desinstala el agente de Servicios de recuperación de Microsoft Azure | - | 
| /ph | Dirección de host del proxy | - | 
| /po | Número de puerto de host del proxy | - | 
| /pu | Nombre de usuario de host del proxy | - | 
| /pw | Contraseña del proxy | - |


## Registro con el servicio de Copia de seguridad de Azure
Para poder registrarse con el servicio de copia de seguridad de Azure, debe asegurarse de que se cumplen los [requisitos previos](backup-configure-vault.md). Debe:

- Disponer de una suscripción válida a Azure
- Disponer de un almacén de copia de seguridad

Para descargar las credenciales de almacén, ejecute el cmdlet **Get-AzureRMBackupVaultCredentials** en una consola de Azure PowerShell y almacénelo en una ubicación adecuada como *C:\\Descargas*.

```
PS C:\> $credspath = "C:"
PS C:\> $credsfilename = Get-AzureRMBackupVaultCredentials -Vault $backupvault -TargetLocation $credspath
PS C:\> $credsfilename
f5303a0b-fae4-4cdb-b44d-0e4c032dde26_backuprg_backuprn_2015-08-11--06-22-35.VaultCredentials
```

El registro de la máquina con el almacén de claves se realiza mediante el cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx):

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration -VaultCredentials $cred -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : West US

Machine registration succeeded.
```

> [AZURE.IMPORTANT] No use rutas de acceso relativas para especificar el archivo de credenciales del almacén de claves. Debe proporcionar una ruta de acceso absoluta como entrada para el cmdlet.

## Configuración de redes
Cuando la conectividad de la máquina de Windows a Internet se realiza a través de un servidor proxy, también se puede proporcionar la configuración del proxy al agente. En este ejemplo, no hay ningún servidor proxy y por tanto se borra explícitamente cualquier información relacionada con el proxy.

También puede controlar el uso de ancho de banda con las opciones de ```work hour bandwidth``` y ```non-work hour bandwidth``` para un conjunto determinado de días de la semana.

El establecimiento de los detalles de ancho de banda y del proxy se realiza mediante el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx):

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## Configuración de cifrado
Los datos de copia de seguridad enviados a Copia de seguridad de Azure están cifrados para proteger la confidencialidad de los datos. La frase de contraseña de cifrado es la "contraseña" que permite descifrar los datos en el momento de la restauración.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [AZURE.IMPORTANT] Mantenga la información de la frase de contraseña segura una vez establecida. No podrá restaurar los datos de Azure sin esta frase de contraseña.

## Realizar copias de seguridad de archivos y carpetas
Todas las copias de seguridad de servidores y clientes de Windows en Copia de seguridad de Azure se rigen por una directiva. La directiva consta de tres partes:

1. Un **programa de copia de seguridad** que especifica cuándo deben efectuarse y sincronizarse las copias de seguridad con el servicio.
2. Una **programación de retención** que especifica cuánto tiempo se conservarán los puntos de recuperación en Azure.
3. Una **especificación de inclusión o exclusión de archivo** que determina los elementos de los que se debe efectuar una copia de seguridad.

En este documento, dado que se está automatizando la copia de seguridad, supondremos que no se ha configurado nada. Comenzamos creando una nueva directiva de copia de seguridad mediante el cmdlet [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) y usándolo.

```
PS C:\> $newpolicy = New-OBPolicy
```

En este momento la directiva está vacía y se necesitan otros cmdlet para definir qué elementos se incluirán o excluirán, cuándo se ejecutarán las copias de seguridad y dónde se almacenarán las copias de seguridad.

### Configuración de la programación de la copia de seguridad
La primera de las tres partes de una directiva es la programación de copia de seguridad, que se crea usando el cmdlet [New-OBSchedule](https://technet.microsoft.com/library/hh770401). La programación de copia de seguridad define cuándo deben realizarse copias de seguridad. Al crear una programación se deben especificar dos parámetros de entrada:

- **Días de la semana** en los que se debe ejecutar la copia de seguridad. Puede ejecutar el trabajo de copia de seguridad en solo un día o cada día de la semana, o cualquier combinación intermedia.
- **Horas del día** a las que se debe ejecutar la copia de seguridad. Puede definir hasta tres horas distintas del día en las que se activará la copia de seguridad.

Por ejemplo, podría configurar una directiva de copia de seguridad que se ejecute a las 4 p.m. cada sábado y domingo.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

La programación de copia de seguridad debe estar asociada con una directiva y esto puede lograrse mediante el uso del cmdlet [Set-OBSchedule](https://technet.microsoft.com/library/hh770407).

```
PS C:\> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### Configuración de una directiva de retención
La directiva de retención define cuánto tiempo se conservan los puntos de recuperación de los trabajos de copia de seguridad. Al crear una nueva directiva de retención mediante el cmdlet [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425), puede especificar el número de días que los puntos de recuperación de copia de seguridad deben conservarse con Copia de seguridad de Azure. En el ejemplo siguiente se establece una directiva de retención de 7 días.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

La directiva de retención debe estar asociada con la directiva principal mediante el cmdlet [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### Incluir y excluir archivos de copia de seguridad
Un objeto ```OBFileSpec``` define los archivos incluidos y excluidos de una copia de seguridad. Se trata de un conjunto de reglas de ámbito de los archivos y carpetas protegidos de un equipo. Puede tener tantas reglas de inclusión o exclusión de archivos como se necesiten y asociarlas con una directiva. Al crear un nuevo objeto OBFileSpec, puede:

- Especificar los archivos y carpetas que se van a incluir
- Especificar los archivos y carpetas que se van a excluir
- Especificar la copia de seguridad recursiva de los datos en una carpeta (o) si se deben copiar solo los archivos de nivel superior en la carpeta especificada.

Esto último se consigue mediante la marca -NonRecursive del comando New-OBFileSpec.

En el ejemplo siguiente, crearemos copias de seguridad del volumen C: y D: y excluiremos los archivos binarios de sistema operativo de la carpeta de Windows y las carpetas temporales. Para ello, crearemos dos especificaciones de archivos mediante el cmdlet [New-OBFileSpec](https://technet.microsoft.com/library/hh770408): uno para la inclusión y otro para la exclusión. Una vez que se hayan creado las especificaciones de archivo, se asocian con la directiva mediante el cmdlet [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424).

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:", "D:")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### Aplicación de la directiva
Ahora el objeto de la directiva está finalizado y tiene una programación de copia de seguridad asociada, una directiva de retención y una lista de inclusión o exclusión de archivos. Ahora se puede confirmar esta directiva para ser usada por Copia de seguridad de Azure. Antes de aplicar la directiva recién creada, asegúrese de que no haya ninguna directiva de copia de seguridad existente asociada con el servidor mediante el uso del cmdlet [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415). Para eliminar la directiva, se le pedirá confirmación. Para omitir el uso de la confirmación, use la marca ```-Confirm:$false``` con el cmdlet.

```
PS C:\> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

La confirmación del objeto de la directiva se lleva a cabo usando el cmdlet [Set-OBPolicy](https://technet.microsoft.com/library/hh770421). Esto también requerirá confirmación. Para omitir el uso de la confirmación, use la marca ```-Confirm:$false``` con el cmdlet.

```
PS C:\> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
	}
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Puede ver los detalles de la directiva de copia de seguridad existente con el cmdlet [Get-OBPolicy](https://technet.microsoft.com/library/hh770406). Puede profundizar todavía más mediante el cmdlet [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) para la programación de copia de seguridad y el cmdlet [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) para las directivas de retención

```
PS C:\> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:\> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:\> Get-OBPolicy | Get-OBFileSpec
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### Realización de una copia de seguridad ad-hoc
Una vez establecida una directiva de copia de seguridad, las copias de seguridad se producirán en función de la programación. Desencadenar una copia de seguridad ad-hoc también es posible usando el cmdlet [Start-OBBackup](https://technet.microsoft.com/library/hh770426):

```
PS C:\> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes...
Preparing storage...
Estimating size of backup items...
Estimating size of backup items...
Transferring data...
Verifying backup...
Job completed.
The backup operation completed successfully.
```

## Restaurar datos de la Copia de seguridad de Azure
Esta sección le guiará por los pasos necesarios para automatizar la recuperación de datos de Copia de seguridad de Azure. Esto implica los pasos siguientes:

1. Seleccionar el volumen de origen
2. Elegir un punto de copia de seguridad desde el que efectuar la restauración
3. Selección de un elemento para restaurar
4. Desencadenar el proceso de restauración

### Selección del volumen de origen
Para restaurar un elemento de la Copia de seguridad de Azure, primero deberá identificar el origen del elemento. Dado que los comandos se están ejecutando en el contexto de un servidor o un cliente de Windows, el equipo ya se ha identificado. El siguiente paso para identificar el origen es identificar el volumen que lo contiene. Se puede recuperar una lista de los volúmenes u orígenes de los que se está efectuando una copia de seguridad desde esta máquina mediante la ejecución del cmdlet [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410). Este comando devuelve una matriz de todos los orígenes de los que se ha efectuado una copia de seguridad desde este servidor/cliente.

```
PS C:\> $source = Get-OBRecoverableSource
PS C:\> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### Elegir un punto de copia de seguridad para restaurar
La lista de puntos de copia de seguridad se puede recuperar ejecutando el cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) con los parámetros adecuados. En nuestro ejemplo, elegiremos el punto de copia de seguridad más reciente para el volumen de origen *D:* y lo usaremos para recuperar un archivo específico.

```
PS C:\> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```
El objeto ```$rps``` es una matriz de puntos de copia de seguridad. El primer elemento es el punto más reciente y el enésimo elemento es el punto más antiguo. Para elegir el punto más reciente, usaremos ```$rps[0]```.

### Selección de un elemento para restaurar
Para identificar el archivo o carpeta exacto que desea restaurar, use de forma recursiva el cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx). De esta forma se puede examinar la jerarquía de carpetas exclusivamente mediante el ```Get-OBRecoverableItem```.

En este ejemplo, si desea restaurar el archivo *finances.xls* se puede hacer referencia a este usando el objeto ```$filesFolders[1]```.

```
PS C:\> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:\> $filesFolders
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM

PS C:\> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:\> $filesFolders
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

También puede buscar elementos para restaurar usando el cmdlet ```Get-OBRecoverableItem```. En nuestro ejemplo, para buscar *finances.xls* podríamos obtener un identificador en el archivo mediante la ejecución de este comando:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### Desencadenar el proceso de restauración
Para desencadenar el proceso de restauración, primero es necesario especificar las opciones de recuperación. Esto puede hacerse mediante el uso del cmdlet [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx). En este ejemplo, supongamos que desea restaurar los archivos en *C:\\temp*. Supongamos también que deseamos omitir archivos que ya existen en la carpeta de destino *C:\\temp*. Para crear dicha opción de recuperación, use el siguiente comando:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Ahora, desencadene la restauración usando el comando [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) en el ```$item``` seleccionado desde la salida del cmdlet ```Get-OBRecoverableItem```:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## Desinstalación del agente de Copia de seguridad de Azure
La desinstalación del agente de Copia de seguridad de Azure se puede realizar con el comando siguiente:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Desinstalación de los archivos binarios del agente de la máquina tiene algunas consecuencias a tener en cuenta:

- Elimina el filtro de archivos de la máquina y se detiene el seguimiento de los cambios.
- Se elimina toda la información de directivas de la máquina, pero continúa almacenada en el servicio.
- Se eliminan todas las programaciones de copia de seguridad y no se realizan más copias de seguridad.

Sin embargo, los datos almacenados en Azure permanecen y se mantienen de acuerdo con la configuración de la directiva de retención establecida por usted. Los puntos más antiguos vencen automáticamente.

## Administración remota
Toda la administración relacionada con el agente, las políticas y los orígenes de datos de Copia de seguridad de Azure puede realizarse de forma remota mediante PowerShell. La máquina que se administrará de forma remota debe estar preparada correctamente.

De forma predeterminada, el servicio WinRM está configurado para iniciarse manualmente. El tipo de inicio debe establecerse en *Automatic* y se debe iniciar el servicio. Para comprobar que el servicio WinRM se está ejecutando, el valor de la propiedad Status debe ser *Running*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell debe configurarse para la comunicación remota.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

La máquina puede ahora administrarse de forma remota: empezando por la instalación del agente. Por ejemplo, el script siguiente copia al agente en la máquina remota y lo instala.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## Pasos siguientes
Para obtener más información sobre Copia de seguridad de Azure para Windows Server o cliente de Windows, consulte

- [Introducción a la Copia de seguridad de Azure](backup-configure-vault.md)
- [Copia de seguridad de servidores Windows](backup-azure-backup-windows-server.md)

<!---HONumber=AcomDC_0128_2016-->