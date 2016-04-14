<properties
   pageTitle="Cifrado de disco de Azure para máquinas virtuales IaaS Linux y Windows| Microsoft Azure"
   description="En este artículo se ofrece información general de Cifrado de disco de Microsoft Azure para máquinas virtuales IaaS con Windows y con Linux."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="devtiw"/>


#Vista previa de cifrado de disco de Azure para máquinas virtuales IaaS Linux y Windows

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

Microsoft Azure está muy comprometido a garantizar la privacidad y soberanía de los datos, y permite controlar los datos hospedados en Azure datos mediante varias tecnologías avanzadas que cifran, controlan y administran las claves de cifrado, y controlan y auditan el acceso de los datos. Esto proporciona a los clientes de Azure la flexibilidad necesaria para elegir la solución que mejor cubra sus necesidades empresariales. En este artículo, le presentaremos a una nueva solución de tecnología, "Cifrado de disco de Azure para máquinas virtuales IaaS Linux y Windows", que le ayudara a proteger sus datos para que cumplan los compromisos de seguridad y compatibilidad de su organización. Ofrece información detalladas sobre cómo usar las características de cifrado de disco de Azure, incluidos los escenarios admitidos y las experiencias de los usuarios.

**NOTA**: Algunas de las recomendaciones de este artículo pueden provocar un aumento del uso de datos, de la red o de recursos de proceso, lo que incrementará los costos de las licencias o suscripciones.

## Información general

Cifrado de disco de Azure es una nueva funcionalidad que permite cifrar los discos de las máquinas virtuales IaaS de Windows y Linux. Cifrado de disco Azure aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar del sector de Windows y la característica [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para ofrecer cifrado de volumen para el sistema operativo y los discos de datos. La solución se integra con el Almacén de [claves de Azure](https://azure.microsoft.com/documentation/services/key-vault/) para ayudarle a controlar y administrar las claves y secretos de cifrado de disco en su suscripción del almacén de claves, al mismo tiempo que garantiza que todos los datos de los discos de las máquinas virtuales se cifran en reposo en el almacenamiento de Azure.

### Escenarios de cifrado

La solución Cifrado de disco de Azure admite los tres siguientes escenarios de cifrado de cliente:

- Habilitación del cifrado en las nuevas máquinas virtuales de IaaS creadas a partir del VHD cifrado del cliente y las claves de cifrado

- Habilitación del cifrado en las nuevas máquinas virtuales de IaaS creadas a partir de la Galería de Azure

- Habilitación del cifrado en nuevas máquinas virtuales de IaaS que ya se ejecutan en Azure

La solución admite lo siguiente para las máquinas virtuales IaaS para la versión de la vista previa pública cuando se habilita en Microsoft Azure:

- Integración con el Almacén de claves de Azure

- [Máquinas virtuales IaaS de las series A, D y G](https://azure.microsoft.com/pricing/details/virtual-machines/) estándares

- Habilitar el cifrado en máquinas virtuales de IaaS creadas con el modelo del [Administrador de recursos de Azure](resource-group-overview.md)

- Todas las [regiones](https://azure.microsoft.com/regions/) públicas de Azure


La solución no es compatible con los siguientes escenarios, características y tecnologías en la versión de la vista previa pública:

- Máquinas virtuales básicas y máquinas virtuales IaaS de la serie DS estándar (almacenamiento Premium)

- Máquinas virtuales IaaS creadas mediante el método de creación de máquinas virtuales clásico

- Capacidad para deshabilitar el cifrado en la máquina virtual IaaS, habilitada mediante Cifrado de disco de Azure

- Integración con el Servicio de administración de claves local

- Windows Server Technical Preview 3

- Red Hat Enterprise Linux

- Archivos de Azure (recurso compartido de archivos de Azure), Network File System (NFS), volúmenes dinámicos, sistemas RAID basados en software


### Características de cifrado

Al habilitar e implementar Cifrado de disco de Azure para las máquinas virtuales IaaS de Azure, se habilitan las capacidades siguientes, en función de la configuración proporcionada:

- Cifrado de volumen del sistema operativo para proteger el volumen de arranque en reposo en el almacenamiento de cliente

- Cifrado de volúmenes de datos para proteger los volúmenes de datos en reposo en el almacenamiento de cliente

- Protección de las claves y secretos de cifrado en la suscripción del almacén de claves de Azure del cliente

- Informes de estado de cifrado de las máquinas virtuales IaaS cifradas

- Eliminación de las opciones de configuración de cifrado de disco de la máquina virtual de IaaS

El cifrado de disco de Azure para la solución Máquinas virtuales IaaS para Windows y Linux incluye la extensión de cifrado de disco para Windows, la extensión de cifrado del disco para Linux, cmdlets de PowerShell de cifrado de disco, cmdlets de CLI de cifrado de disco y plantillas de Administrador de recursos de Azure de cifrado de disco. La solución Cifrado de disco de Azure es compatible con las máquinas virtuales IaaS que ejecutan los sistemas operativos Windows o Linux. Para más información sobre los sistemas operativos compatibles, consulte la sección de requisitos previos a continuación.

No hay cargo alguno por el cifrado de discos de máquinas virtuales con Cifrado de disco de Azure durante la vista previa pública. También se espera que esto siga siendo así después de que Cifrado de disco esté disponible de forma general. Sin embargo, los precios están sujetos a cambios en función del mercado y del panorama de la competencia.

### Propuesta de valor

La solución Administración de cifrado de discos de Azure habilita las siguientes necesidades empresariales en la nube:

-   Las máquinas virtuales de IaaS se protegen en reposo con la tecnología de cifrado estándar del sector para cumplir los requisitos de seguridad y compatibilidad de organización.

-   Las máquinas virtuales de IaaS arrancan bajos las directivas y claves controladas por el cliente y pueden auditar su uso en el Almacén de claves.


### Flujo de trabajo de cifrado
Los pasos de alto nivel requeridos para habilitar el cifrado de disco en las máquinas virtuales de Windows y Linux son:

1. El cliente elige el escenario de cifrado entre los tres escenarios de cifrado anteriores

2. El cliente opta por habilitar el cifrado de disco mediante la plantilla ARM de Cifrado de disco de Azure, los cmdlets PS o el comando CLI y especifica la configuración del cifrado

    - En el escenario de VHD cifrado del cliente, el cliente carga el VHD cifrado en su cuenta de almacenamiento y el material de las claves de cifrado en su almacén de claves y proporciona la configuración de cifrado necesaria para habilitar el cifrado en una nueva máquina virtual IaaS

    - Tanto en las nuevas máquinas virtuales creadas desde la Galería de Azure como en las máquinas virtuales existente que ya se ejecutan en Azure, el cliente proporciona la configuración para habilitar el cifrado en la máquina virtual IaaS

3. El cliente concede acceso a la plataforma Azure para leer el material de la clave de cifrado (sistemas de claves de cifrado de BitLocker para Windows y frase de contraseña para Linux) de su almacén de claves para habilitar el cifrado en la máquina virtual IaaS

4. El cliente proporciona una identidad de Azure AD para escribir el material de la clave de cifrado en su almacén de claves para habilitar el cifrado en la máquina virtual IaaS para los escenarios 2 y 3 anteriores.

5.  Administración de servicios de Azure actualiza el modelo de servicio de la máquina virtual con la configuración del cifrado y del almacén de claves y aprovisiona la máquina virtual cifrada para el cliente

![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.JPG)

## Requisitos previos

Estos son los requisitos previos para habilitar Cifrado de disco de Azure en máquinas virtuales IaaS de Azure para los escenarios admitidos de la sección de información general

- Para crear recursos en Azure en las regiones admitidas, el usuario debe tener una suscripción válida de Azure activa

- Cifrado de disco de Azure es compatible en con las siguientes SKU de Windows Server: Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. La solución no es compatible con el sistema operativo Windows Server 2008. Windows Server Technical Preview no es compatible con la versión de vista previa pública.

**Nota**: En el caso de Windows Server 2008 R2, ES PRECISO instalar .Net Framework 4.5 antes de habilitar el cifrado en Azure. Se puede instalar desde Windows Update mediante la instalación de la actualización opcional "Microsoft .NET Framework 4.5.2 para sistemas basados en x64 con Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983))".

- Cifrado de disco de Azure es compatible con las siguientes SKU de Linux Server: Ubuntu, CentOS, SUSE y SUSE Linux Enterprise Server (SLES). Red Hat Enterprise Linux no es compatible con la versión de vista previa pública.

- Todos los recursos (por ejemplo: almacén de claves, cuenta de almacenamiento, máquina virtual, etc.) deben pertenecer a la misma región y suscripción de Azure.

**Nota**: Cifrado de disco de Azure requiere que el almacén de claves y las máquinas virtuales residan en la misma región de Azure. Si se configuran en regiones independientes, se producirá un error al habilitar la característica de cifrado de disco de Azure.

- Para instalar y configurar el Almacén de claves de Azure para el uso de Cifrado de discos de Azure, consulte la sección **Establecimiento y configuración del Almacén de claves de Azure para el uso de Cifrado de discos de Azure** en la sección *Requisitos previos* de este artículo.

- Para instalar y configurar la aplicación de Azure AD en Azure Active Directory para el uso de Cifrado de discos de Azure, consulte la sección **Instalación de la aplicación de Azure AD en Azure Active Directory** en la sección *Requisitos previos* de este artículo.

- Para instalar y configurar la directiva de acceso del Almacén de claves de la aplicación de Azure AD, consulte la sección **Establecimiento de la directiva de acceso del Almacén de claves para la aplicación de Azure AD** en la sección *Requisitos previos* de este artículo.

- Para preparar un VHD con Windows precifrado, consulte la sección **Preparación de un VHD con Windows precifrado** en el apéndice de este artículo.

- Para preparar un VHD con Linux precifrado, consulte la sección **Preparación de un VHD con Linux precifrado** en el apéndice de este artículo.

- La plataforma Azure necesita acceso a las claves de cifrado o secretos del Almacén de claves de Azure del cliente, con el fin de que estén disponibles para que la máquina virtual arranque y descifre el volumen del sistema operativo de la máquina virtual. Para conceder permisos a la Plataforma Azure para que tenga acceso al almacén de claves, debe establecerse la propiedad **enabledForDiskEncryption** en el almacén de claves de este requisito. Consulte la sección **Establecimiento y configuración del Almacén de claves de Azure para el uso de Cifrado de disco de Azure** en el apéndice de este artículo para obtener más detalles.

- El secreto del almacén de claves y las direcciones URL de la clave de cifrado de claves (KEK) deben tener versiones. Administración de servicios Azure exige esta restricción del control de versiones. En los ejemplos siguientes encontrará direcciones URL de KEK y de secretos válidas:

	- Ejemplo de dirección URL de secretos válida:

		*https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Ejemplo de dirección URL de KEK válida:

		*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Cifrado de disco de Azure no admite que los números de puerto se especifiquen como parte de las URL de KEK y del secreto del almacén de claves. A continuación encontrará ejemplos de direcciones URL de almacén de claves compatibles:

 	- Dirección URL de almacén de claves no aceptada

		*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Dirección URL de almacén de claves aceptada:

		*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Para habilitar la característica Cifrado de disco de Azure, las máquinas virtuales IaaS debe cumplir los siguientes requisitos de configuración de puntos de conexión de red:

	- La máquina virtual IaaS debe poder conectarse a un punto de conexión de Azure Active Directory [Login.windows.net] para que un token se conecte al Almacén de claves de Azure

	- La máquina virtual IaaS debe poder conectarse al punto de conexión del Almacén de claves de Azure para escribir las claves de cifrado en el almacén de claves del cliente

	- La máquina virtual IaaS debe poder conectarse al punto de conexión de almacenamiento de Azure que hospeda el repositorio de extensiones de Azure y la cuenta de almacenamiento de Azure que hospeda los archivos VHD

**Nota:** Si su directiva de seguridad limita el acceso desde máquinas virtuales de Azure a Internet, puede resolver el URI anterior al que necesita tener conectividad y configurar una regla concreta para permitir la conectividad de salida para las direcciones IP.

- Para ejecutar cualquiera de los cmdlets de PowerShell de Cifrado de discos de Azure, primero es preciso instalar la [versión 1.0.2 de Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v1.0.2-December2015):

	- Para instalar Azure PowerShell y asociarlo con una suscripción de Azure, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

	- En este documento se supone que conoce los conceptos básicos, como los módulos, los cmdlets y las sesiones. Para más información, consulte Introducción a [Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

**Nota:**no se admite el Cifrado de discos de Azure en el [SDK de Azure PowerShell versión 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016).

- Para ejecutar cualquiera de los comandos de la CLI de Azure y asociarlo a una suscripción de Azure, primero debe instalar la versión de la CLI de Azure:

	- Para instalar la CLI de Azure y asociarla con una suscripción de Azure, consulte [Instalación de la CLI de Azure](xplat-cli-install.md).

	- Si usa la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure, consulte [esto](azure-cli-arm-commands.md).

- La solución Cifrado de disco de Azure usa el protector de claves externas de BitLocker para máquinas virtuales IaaS de Windows. Si las máquinas virtuales están unidas en un dominio, no inserte directivas de grupo que exijan protectores de TPM. Consulte [este artículo](https://technet.microsoft.com/library/ee706521), donde encontrará información sobre la directiva de grupo para "Permitir BitLocker sin un TPM compatible".
- El script de PowerShell que constituye un requisito previo para el Cifrado de discos de Azure que permite crear la aplicación de Azure AD, crear el nuevo Almacén de claves o configurar el ya existente y habilitar el cifrado, se encuentra [aquí](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

### Establecimiento y configuración del Almacén de claves de Azure para el uso de Cifrado de disco de Azure

Cifrado de disco de Azure protege las claves de cifrado de disco y los secretos en su Almacén de claves de Azure. Siga los pasos de las secciones siguientes para configurar el almacén de claves para el uso de Cifrado de disco de Azure.

#### Creación de un almacén de claves nuevo
Para crear un almacén de claves nuevo, utilice una de las dos opciones siguientes:

- Use la plantilla de ARM "101-Create-KeyVault" que se encuentra [aquí](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Use los cmdlets del Almacén de claves de Azure PowerShell como se describe [aquí](key-vault-get-started.md)

**Nota:** Si ya tiene una configuración del almacén de claves para la suscripción, pase a la siguiente sección.

#### Aprovisionamiento de una clave de cifrado de claves (opcional)

Si desea usar una clave de cifrado de claves (KEK) para obtener una capa adicional de seguridad que encapsule las claves de cifrado de BitLocker, debe agregar una KEK a su almacén de claves para usarla en el proceso de aprovisionamiento. Use el cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) para crear una nueva clave de cifrado de claves en el almacén de claves. Para obtener información detallada, consulte la [documentación del almacén de claves](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

#### Establecimiento de permisos en el Almacén de claves para permitir que la plataforma Azure acceda a las claves y secretos

La plataforma Azure necesita acceso a las claves o secretos de cifrado del Almacén de claves de Azure del cliente, con el fin de que estén disponibles para que la máquina virtual arranque y descifre los volúmenes. Para conceder permisos a la plataforma Azure de forma que pueda acceder al almacén de claves, debe establecerse la propiedad *enabledForDiskEncryption* en el almacén de claves. Puede establecer la propiedad enabledForDiskEncryption en el almacén de claves con el cmdlet de PS del almacén de claves:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Puede establecer la propiedad *enabledForDiskEncryption* en el almacén de claves como se indicó anteriormente: Puede establecer la propiedad visitando https://resources.azure.com. Asegúrese de que las propiedades detalladas anteriormente se establecen correctamente, ya que en caso contrario, la implementación no se realizará correctamente.

#### Instalación de la aplicación de Azure AD en Azure Active Directory

Si es preciso habilitar el cifrado en una máquina virtual en ejecución en Azure, Cifrado de disco de Azure genera y escribe las claves de cifrado en su almacén de claves. La administración de claves de cifrado en el almacén de claves necesita la autenticación de Azure AD.

Para ello, se debe crear una aplicación de Azure AD. Los pasos detallados para registrar una aplicación se pueden encontrar aquí, en la sección sobre la obtención de una identidad para la aplicación de esta [entrada del blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Esta entrada también contiene varios ejemplos útiles sobre el aprovisionamiento y la configuración del almacén de claves. Para realizar la autenticación, se pueden usar la autenticación basada en secreto de cliente o la autenticación Azure AD basada en certificados del cliente.

##### Autenticación basada en secreto de cliente para Azure AD

Las secciones siguientes contienen los pasos necesarios para configurar una autenticación basada en secreto de cliente para Azure AD.

##### Creación de una nueva aplicación de Azure AD con Azure PowerShell

Use el siguiente cmdlet de PowerShell para crear una nueva aplicación de Azure AD:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Nota:** $azureAdApplication.ApplicationId es el valor ClientID de Azure AD y $aadClientSecret es el secreto de cliente que se deberá usar más adelante para habilitar ADE. Debe proteger el secreto de cliente de Azure AD adecuadamente.


##### Aprovisionamiento del identificador de cliente y secreto de Azure AD desde el Portal de administración de servicios de Azure

El secreto y el identificador de cliente de Azure AD también se pueden aprovisionar mediante el Portal de administración de servicios de Azure en https://manage.windowsazure.com; siga estos pasos para realizar dicha tarea:

1\.Haga clic en la pestaña Active Directory como se muestra en la ilustración siguiente:

![Cifrado de disco de Azure](./media/azure-security-disk-encryption\disk-encryption-fig3.JPG)

2\.Haga clic en Agregar aplicación y escriba el nombre de la aplicación como se muestra a continuación:

![Cifrado de disco de Azure](./media/azure-security-disk-encryption\disk-encryption-fig4.JPG)

3\.Haga clic en el botón de flecha y configure las propiedades de la aplicación como se muestra a continuación:

![Cifrado de disco de Azure](./media/azure-security-disk-encryption\disk-encryption-fig5.JPG)

4\. Haga clic en la marca de verificación de la esquina inferior izquierda para finalizar. Aparece la página de configuración de la aplicación. Observe que el identificador de cliente de Azure AD se encuentra en la parte inferior de la página como se muestra en la ilustración siguiente.

![Cifrado de disco de Azure](./media/azure-security-disk-encryption\disk-encryption-fig6.JPG)

5\.Guarde el secreto del cliente de Azure AD, para lo que debe hacer clic en el botón Guardar. Haga clic en el botón Guardar y anote el secreto del cuadro de texto de claves, que se trata del secreto del cliente de Azure AD. El secreto del cliente de Azure AD debe protegerse correctamente.

![Cifrado de disco de Azure](./media/azure-security-disk-encryption\disk-encryption-fig7.JPG)


**Nota:** El flujo anterior no se admite en el Portal.

##### Uso de una aplicación existente

Para ejecutar los siguientes comandos, se necesita el módulo Azure AD PowerShell, que puede obtenerse [aquí](https://technet.microsoft.com/library/jj151815.aspx).

**Nota:** Los siguientes comandos deben ejecutarse desde una ventana nueva de PowerShell. NO use Azure PowerShell o la ventana del Administrador de recursos de Azure para ejecutar estos comandos. La razón de esta recomendación es que estos cmdlets están en el módulo MSOnline o en Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Autenticación basada en certificado para Azure AD

Las secciones siguientes contienen los pasos necesarios para configurar una autenticación basada en certificado para Azure AD.

##### Creación de una nueva aplicación de Azure AD

Ejecute el siguiente cmdlet de PowerShell para crear una nueva aplicación de Azure AD:

**Nota:** Reemplace "yourpassword" en la cadena siguiente por la contraseña segura y proteja la contraseña.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Una vez finalizado este paso, cargue un archivo .pfx en el almacén de claves y habilite la directiva de acceso necesaria para implementar dicho certificado en una máquina virtual.

##### Uso de una aplicación de Azure AD existente
Si va a configurar una autenticación basada en certificado para una aplicación existente, use los siguientes cmdlets de PowerShell. Asegúrese de que los ejecuta desde una ventana nueva de PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Una vez finalizado este paso, cargue un archivo .pfx en el almacén de claves y habilite la directiva de acceso necesaria para implementar dicho certificado en una máquina virtual.

##### Carga de un archivo PFX en el almacén de claves
Para obtener una explicación detallada sobre el funcionamiento de este proceso, puede leer esta [entrada de blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Sin embargo, los siguientes cmdlets de PowerShell son todo lo que necesita para realizar esta tarea. Asegúrese de que los ejecuta desde la consola de Azure PowerShell:

**Nota:** Reemplace "yourpassword" en la cadena siguiente por la contraseña segura y proteja la contraseña.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### Implementación de un certificado del almacén de claves en una máquina virtual existente
Después de terminar de cargar el PFX, use los pasos siguientes para implementar un certificado del almacén de claves en una máquina virtual existente:

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


### Establecimiento de la directiva de acceso del almacén de clave para la aplicación de Azure AD

La aplicación de Azure AD necesita derechos de acceso a las claves o secretos del almacén. Use el cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) para conceder permisos a la aplicación, con el identificador de cliente (que se generó cuando se registró la aplicación) como valor del parámetro –ServicePrincipalName. Para ver algunos ejemplos al respecto, puede leer [esta entrada de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). A continuación también encontrará un ejemplo de cómo realizar esta tarea mediante PowerShell:

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

## Terminología

Utilice la tabla de terminología como referencia para comprender algunos de los términos comunes que usa esta tecnología:


| Terminología | Definición |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD | Azure AD es [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Cuenta de Azure AD es un requisito previo para autenticar, almacenar y recuperar secretos del almacén de claves. |
| Almacén de claves de Azure [AKV] | Almacén de claves de Azure es un servicio de administración de claves criptográficas basado en módulos de seguridad de hardware validados por FIPS para proteger las claves criptográficas y los secretos confidenciales de forma segura. Para obtener información más detallada, consulte la documentación del [almacén de claves](https://azure.microsoft.com/services/key-vault/). |
| ARM | Administrador de recursos de Azure |
| BitLocker | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) es una tecnología de cifrado de volúmenes de Windows reconocida por el sector que se usa para habilitar el cifrado de disco en máquinas virtuales IaaS de Windows |
| BEK | Las claves de cifrado de BitLocker se usan para cifrar el volumen de arranque del sistema operativo y los volúmenes de datos. Las claves de BitLocker se protegen en el Almacén de claves de Azure del cliente como secretos. |
| CLI | [Interfaz de la línea de comandos de Azure](xplat-cli-install.md) |
| DM-Crypt | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) es el subsistema de cifrado transparente de disco basado en Linux que se usa para habilitar el cifrado de disco en las máquinas virtuales IaaS de Linux |
| KEK | Clave de cifrado de claves es la clave asimétrica (RSA 2048) que se usa para encapsular el secreto, en caso de que se desee. Se puede proporcionar una clave protegida mediante HSM o una clave protegida mediante software. Para obtener información detallada, consulte la documentación del [Almacén de claves de Azure](https://azure.microsoft.com/services/key-vault/) |
| cmdlets de PS | [cmdlets de Azure PowerShell](powershell-install-configure.md) |

## Escenarios de implementación del cifrado de disco y experiencias de usuario

Hay muchos escenarios en que se puede habilitar el cifrado de disco y los pasos pueden variar en función del escenario. En las siguientes secciones se explicarán estos escenarios con mayor detalle.

### Habilitación del cifrado en las nuevas máquinas virtuales de IaaS creadas a partir de la Galería de Azure

El cifrado de disco se puede habilitar en las nuevas máquinas virtuales IaaS de Windows desde la Galería de Azure en Azure mediante la plantilla de ARM publicada [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Haga clic en el botón "Implementar en Azure" de la plantilla de inicio rápido de Azure, en la configuración de cifrado de entrada de la hoja de parámetros y en Aceptar. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato, y haga clic en el botón Crear para habilitar el cifrado en una máquina virtual IaaS nueva.

**Nota:** Esta plantilla crea una nueva máquina virtual con Windows cifrada mediante una imagen de la galería de Windows Server 2012.

En la tabla siguiente se puede ver información de los parámetros de las plantillas de ARM para las nuevas máquinas virtuales desde el escenario de la Galería de Azure que usa el identificador de cliente de Azure AD:

| Parámetro | Descripción|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | Nombre de usuario administrador para la máquina virtual. |
| adminPassword | Contraseña de usuario administrador para la máquina virtual. |
| newStorageAccountName | Nombre de la cuenta de almacenamiento que almacena el sistema operativo y los VHD de datos |
| vmSize | Tamaño de la máquina virtual. Actualmente, solo se admiten las series A, D y G estándar |
| virtualNetworkName | Nombre de la red virtual a la que debería pertenecer la NIC de la máquina virtual. |
| subnetName | Nombre de la subred en la red virtual a la que debería pertenecer la NIC de la máquina virtual. |
| AADClientID | Identificador de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves |
| AADClientSecret | Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves |
| keyVaultResourceID, ResourceID | Identificación del recurso del almacén de claves en ARM. Se puede obtener mediante el cmdlet de PowerShell: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId |
| keyVaultURL | Dirección URL del almacén de claves en la que se que debe cargar la clave de BitLocker. Se puede obtener mediante el cmdlet: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL | Dirección URL de la clave de cifrado de claves que se utiliza para cifrar la clave generada por BitLocker. Esto es opcional. |
| vmName | Nombre de la máquina virtual en que se va a realizar la operación de cifrado


**Nota:** KeyEncryptionKeyURL es un parámetro opcional. Puede aportar su propia KEK para proteger aún más la clave de cifrado de datos (frase de contraseña secreta) en el almacén de claves.

### Habilitación del cifrado en las nuevas máquinas virtuales de IaaS creadas a partir del VHD cifrado del cliente y las claves de cifrado

En este escenario se puede habilitar el cifrado mediante la plantilla de ARM, cmdlets de PowerShell o comandos de la CLI. En las siguientes secciones se explicarán más detalladamente los comandos de la CLI y la plantilla de ARM.

#### Uso de una plantilla de ARM

El cifrado de disco se puede habilitar en un VHD cifrado del cliente mediante la plantilla de ARM publicada [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Haga clic en el botón "Implementar en Azure" de la plantilla de inicio rápido de Azure, en la configuración de cifrado de entrada de la hoja de parámetros y en Aceptar. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato, y haga clic en el botón Crear para habilitar el cifrado en una máquina virtual IaaS nueva.

En la tabla siguiente se describen los detalles de los parámetros de la plantilla de ARM del escenario de VHD cifrado de cliente:

| Parámetro | Descripción|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Nombre de la cuenta de almacenamiento que almacena el VHD del sistema operativo cifrado. Esta cuenta de almacenamiento se debe haber creado en el mismo grupo de recursos y en la misma ubicación que la máquina virtual |
| osVhdUri | URI de VHD del sistema operativo de la cuenta de almacenamiento |
| osType | Tipo de producto de sistema operativo (Windows o Linux) |
| virtualNetworkName | Nombre de la red virtual a la que debería pertenecer la NIC de la máquina virtual. Se debe haber creado en el mismo grupo de recursos y en la misma ubicación que la máquina virtual |
| subnetName | Nombre de la subred en la red virtual a la que debería pertenecer la NIC de la máquina virtual. |
| vmSize | Tamaño de la máquina virtual. Actualmente, solo se admiten las series A, D y G estándar |
| keyVaultResourceID | ResourceID que identifica el recurso del almacén de claves en ARM. Se puede obtener mediante el cmdlet de PowerShell: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl | ​Dirección URL de la clave de cifrado de disco aprovisionada en el almacén de claves |
| keyVaultKekUrl | Dirección URL de la clave de cifrado de claves que se utiliza para cifrar la clave de cifrado de disco generada |
| ​vmName | ​Nombre de la máquina virtual IaaS   



####Uso de cmdlets de PowerShell

El cifrado de disco se puede habilitar en un VHD cifrado del cliente mediante la plantilla de cmdlets de PS publicada [aquí](https://msdn.microsoft.com/library/azure/mt603746.aspx).

####Uso de comandos de la CLI

Siga estos pasos para habilitar el cifrado de disco en este escenario con comandos de la CLI:

1. Establezca directivas de acceso en el almacén de claves:
	- Establezca ‘EnabledForDiskEncryption’ flag: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Establezca permisos para que la aplicación de Azure AD escriba secretos en KeyVault: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. Para habilitar el cifrado en una máquina virtual existente o en ejecución, escriba: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenga el estado del cifrado: *“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. Para habilitar el cifrado en una nueva máquina virtual desde el VHD cifrado del cliente, use los siguientes parámetros con el comando "azure vm create":
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Habilitación del cifrado en una máquina virtual IaaS con Windows existente o en ejecución en Azure

En este escenario, se puede habilitar el cifrado mediante la plantilla de ARM, cmdlets de PowerShell o comandos de la CLI. En las siguientes secciones se explicará más detalladamente su habilitación con comandos de la CLI y una plantilla de ARM.

#### Uso de una plantilla de ARM

El cifrado de disco se puede habilitar en una máquina virtual IaaS con Windows existente o en ejecución en Azure mediante la plantilla de ARM publicada [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Haga clic en el botón "Implementar en Azure" de la plantilla de inicio rápido de Azure, en la configuración de cifrado de entrada de la hoja de parámetros y en Aceptar. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato, y haga clic en el botón Crear para habilitar el cifrado en una máquina virtual IaaS existente o en ejecución.

En la tabla siguiente se puede ver información de los parámetros de las plantillas de ARM para un escenario de máquinas virtuales existentes o en ejecución mediante el identificador de cliente de Azure AD:

| Parámetro | Descripción|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​Identificador de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves |
| AADClientSecret | ​Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves |
| keyVaultName | Nombre del Almacén de claves en el que se debe cargar la clave de BitLocker. Se puede obtener mediante el cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | Dirección URL de la clave de cifrado de claves que se utiliza para cifrar la clave generada por BitLocker. Esto es opcional si selecciona “nokek” en la lista desplegable de UseExistingKek. Si selecciona "kek" en la lista desplegable UseExistingKek, debe proporcionar el valor de keyEncryptionKeyURL |
| ​volumeType | ​Tipo de volumen en que se realiza la operación de cifrado Los valores válidos son "SO", "Datos", "Todo" |
| sequenceVersion | Versión de la secuencia de la operación de BitLocker. Incremente el número de versión cada vez que se realice en una operación de cifrado de disco en la misma máquina virtual. |
| ​vmName | ​Nombre de la máquina virtual en que se va a realizar la operación de cifrado


**Nota:** KeyEncryptionKeyURL es un parámetro opcional. Puede aportar su propia KEK para proteger aún más la clave de cifrado de datos (decreto de cifrado de BitLocker) en el almacén de claves.

#### Uso de cmdlets de PowerShell

Consulte la [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) y la [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) de la entrada de blog **Exploración de Cifrado de disco de Azure con Azure PowerShell** para obtener información detallada sobre cómo habilitar el cifrado mediante Cifrado de disco de Azure con cmdlets de PS.

#### Uso de comandos de la CLI

Siga estos pasos siguientes para habilitar el cifrado en una máquina virtual IaaS con Windows existente o en ejecución en Azure mediante comandos de la CLI:

1. Establezca directivas de acceso en el almacén de claves:
	- Establezca ‘EnabledForDiskEncryption’ flag: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Establezca permisos para que la aplicación de Azure AD escriba secretos en KeyVault: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. Para habilitar el cifrado en una máquina virtual existente o en ejecución, escriba: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenga el estado del cifrado: *“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. Para habilitar el cifrado en una nueva máquina virtual desde el VHD cifrado del cliente, use los siguientes parámetros con el comando "azure vm create":
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Habilitación del cifrado en una máquina virtual IaaS con Linux existente o en ejecución en Azure

El cifrado de disco se puede habilitar en una máquina virtual IaaS con Linux existente o en ejecución en Azure mediante la plantilla de ARM publicada [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Haga clic en el botón "Implementar en Azure" de la plantilla de inicio rápido de Azure, en la configuración de cifrado de entrada de la hoja de parámetros y en Aceptar. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato, y haga clic en el botón Crear para habilitar el cifrado en una máquina virtual IaaS existente o en ejecución.

En la tabla siguiente se describen los detalles de los parámetros de las plantillas de ARM para un escenario de máquinas virtuales existentes o en ejecución mediante el identificador de cliente de Azure AD:

| Parámetro | Descripción|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​Identificador de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves |
| AADClientSecret | ​Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves |
| keyVaultName | Nombre del Almacén de claves en el que se debe cargar la clave de BitLocker. Se puede obtener mediante el cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | Dirección URL de la clave de cifrado de claves que se utiliza para cifrar la clave generada por BitLocker. Esto es opcional si selecciona “nokek” en la lista desplegable de UseExistingKek. Si selecciona "kek" en la lista desplegable UseExistingKek, debe proporcionar el valor de keyEncryptionKeyURL |
| ​volumeType | ​Tipo de volumen en que se realiza la operación de cifrado El válido valor que se admite es "Datos". Las máquinas virtuales con Linux no admiten la habilitación del cifrado en un volumen del sistema operativo en una máquina virtual con Linux en ejecución |
| sequenceVersion | Versión de la secuencia de la operación de BitLocker. Incremente el número de versión cada vez que se realice en una operación de cifrado de disco en la misma máquina virtual. |
| ​vmName | ​Nombre de la máquina virtual en que se va a realizar la operación de cifrado
| passPhrase | Escriba una frase de contraseña segura como clave de cifrado de datos |                                                                                                                                                                                                                                                      

**Nota:** KeyEncryptionKeyURL es un parámetro opcional. Puede aportar su propia KEK para proteger aún más la clave de cifrado de datos (frase de contraseña secreta) en el almacén de claves.

#### Comandos de la CLI

El cifrado de disco se puede habilitar en un VHD cifrado del cliente mediante el comando de la CLI que se instala desde [aquí](xplat-cli-install.md). Siga estos pasos siguientes para habilitar el cifrado en una máquina virtual IaaS con Linux existente o en ejecución en Azure mediante comandos de la CLI:

1. Establezca directivas de acceso en el almacén de claves:
	- Establezca ‘EnabledForDiskEncryption’ flag: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Establezca permisos para que la aplicación de Azure AD escriba secretos en KeyVault: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. Para habilitar el cifrado en una máquina virtual existente o en ejecución, escriba: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenga el estado del cifrado: “azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”
4. Para habilitar el cifrado en una nueva máquina virtual desde el VHD cifrado del cliente, use los siguientes parámetros con el comando "azure vm create".
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

### Obtención del estado de cifrado de una máquina virtual IaaS cifrada

El estado del cifrado se puede obtener mediante el Portal de administración de Azure, [cmdlets de PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) o comandos de la CLI. En las siguientes secciones se explica cómo usar los comandos de la CLI y el Portal de administración de Azure (vista previa) para obtener el estado del cifrado.

#### Obtención del estado del cifrado de una máquina virtual IaaS cifrada mediante el Portal de administración de Azure

Se puede obtener el estado del cifrado de la máquina virtual IaaS del Portal de administración de Azure. Inicie sesión en el Portal de Azure en https://portal.azure.com/, haga clic en el vínculo de máquinas virtuales en el menú izquierdo para acceder a la vista de resumen de las máquinas virtuales de su suscripción. Para filtrar la vista de máquinas virtuales, seleccione el nombre de la suscripción en la lista desplegable de suscripciones. Haga clic en las columnas de la parte superior del menú de la página de máquinas virtuales. Seleccione la columna Cifrado de disco en la hoja de elección de columna y haga clic en Actualizar. Debería ver la columna de cifrado de disco, que muestra el estado del cifrado, "Habilitado" o "No habilitado", de cada máquina virtual, como se muestra en la ilustración siguiente.

![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.JPG)

#### Obtención del estado del cifrado de una máquina virtual IaaS cifrada mediante el cmdlet de PS de cifrado de disco
El estado del cifrado de la máquina virtual IaaS se puede obtener desde el cmdlet de PS de cifrado de disco "Get-AzureRmVMDiskEncryptionStatus". Para obtener la configuración del cifrado de una máquina virtual, escriba en la sesión de Azure PowerShell:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName <yourResourceGroupName> -VMName <yourVMName>

    OsVolumeEncrypted: True
    OsVolumeEncryptionSettings : {
      "DiskEncryptionKey": {
       SecretUrl":"https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "SourceVault": {
            "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxxx/providers/Mi                            crosoft.KeyVault/vaults/xxxxxxx"
                                }
                            },
                    "KeyEncryptionKey": null
                             }
    DataVolumesEncrypted: True

Los valores de configuración de OSVolumeEncrypted y DataVolumesEncrypted se establecen en "True", lo que muestra que ambos volúmenes se cifran con Cifrado de disco de Azure. Consulte la [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) y la [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) de la entrada de blog **Exploración de Cifrado de disco de Azure con Azure PowerShell** para obtener información detallada sobre cómo habilitar el cifrado mediante Cifrado de disco de Azure con cmdlets de PS.

#### Obtención del estado del cifrado de la máquina virtual IaaS desde el comando de la CLI de cifrado de disco

El estado del cifrado de la máquina virtual IaaS se puede obtener desde el comando de la CLI de cifrado de disco *azure vm show-disk-encryption-status*. Para obtener la configuración del cifrado de una máquina virtual, escriba en la sesión de la CLI de Azure:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  


## Anexo

### su suscripción

Asegúrese de revisar la sección sobre requisitos previos de este documento antes de continuar. Después de asegurarse de que se cumplen todos los requisitos previos, siga estos pasos para conectarse a su suscripción:

1\.Inicie una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:

    Login-AzureRmAccount

2\.Si tiene varias suscripciones y desea especificar que se use una en concreto, escriba lo siguiente para ver las suscripciones de su cuenta:

    Get-AzureRmSubscription

3\.Para especificar la suscripción que desea usar, escriba:

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\.Para comprobar que la suscripción configurada es correcta, escriba:

    Get-AzureRmSubscription

5\.Para confirmar que están instalados los cmdlets de Cifrado de disco de Azure, escriba:

    Get-command *diskencryption*

6\.Debería ver el resultado siguiente, que confirma la instalación de PowerShell en Cifrado de disco de Azure:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                               	 Version    Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus       1.1.0      AzureRM.Compute                                                    
    Cmdlet       Remove-AzureRmVMDiskEncryptionExtension 1.1.0      AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension    1.1.0      AzureRM.Compute                                                     

### Preparación de un VHD con Windows precifrado
Las secciones siguientes son necesarias para preparar un VHD con Windows precifrado para implementarlo como VHD cifrado en IaaS de Azure. Los pasos se usan para preparar y arrancar una máquina virtual nueva con Windows (vhd) en Hyper-V o Azure.

#### Actualización de una directiva de grupo para permitir un módulo no TPM para la protección del sistema operativo
Es preciso configurar la opción de la directiva de grupos de BitLocker denominada Cifrado de unidad BitLocker, que se encuentra en Directiva de equipo local\\Configuración del equipo\\Plantillas administrativas\\Componentes de Windows. Cambie esta opción a: *Unidades del sistema operativo - Requerir autenticación adicional al iniciar - Permitir BitLocker sin un TPM compatible*, como se muestra en la ilustración siguiente:

![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.JPG)

#### Instalación de componentes de características de BitLocker
Para Windows Server 2012, y las versiones posteriores, use el comando siguiente:

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Para Windows Server 2008 R2, use el comando siguiente:

    ServerManagerCmd -install BitLockers

#### Preparar el volumen del sistema operativo para BitLocker con bdehdcfg

Ejecute el comando siguiente para comprimir la partición del sistema operativo y preparar la máquina para BitLocker.

    bdehdcfg -target c: shrink -quiet

#### Uso de BitLocker para proteger el volumen del sistema operativo
Use el comando [manage-bde](https://technet.microsoft.com/library/ff829849.aspx) para habilitar el cifrado en el volumen de arranque mediante un protector de claves externo y coloque la clave externa (archivo .bek) en la unidad o volumen externos. El cifrado se habilitará en el volumen de sistema o de arranque la próxima vez que se reinicie el equipo.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Nota:** La máquina virtual se debe preparar con un VHD de datos o recursos separado para obtener la clave externa mediante BitLocker.

#### Preparación de un VHD con Linux precifrado

##### Ubuntu 14.

1\.Cree un archivo en /usr/local/sbin/azure\_crypt\_key.sh con el contenido del script siguiente. Preste atención a KeyFileName, porque es el nombre que Azure ha puesto al archivo de frase de contraseña.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    sleep 2
    OPENED=0
    for SFS in /sys/block/sd*; do
        DEV=`basename $SFS`
        F=$SFS/${DEV}1/dev
        echo "> Trying device: $DEV ..." >&2
        mount /dev/${DEV}1 $MountPoint -t vfat -r >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\.Cambie la configuración de cifrado en */etc/crypttab*. Debería ser parecido a este:

    Sda5_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\.Si edita *azure\_crypt\_key.sh* en Windows y lo ha copiado en Linux, no olvide ejecutar *dos2unix /usr/local/sbin/azure\_crypt\_key.sh*. 4.Ejecute *update-initramfs -u -k all* para actualizar initramfs y que el script de claves surta efecto.

##### openSUSE 13.2.

1\.Edite /etc/dracut.conf add\_drivers += "vfat nls\_cp437 nls\_iso8859-1"

2\.Añada comentarios a estas líneas al final del archivo "/ usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #    inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator


3\.Anexe DRACUT\_SYSTEMD = 0 al principio del archivo “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” y cambie todas las instancias de “if [ -z "$DRACUT\_SYSTEMD" ]; then” por “if [ 1 ]; then”

4\.Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh y anéxelo después de “# Open LUKS device”

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
       echo "> Trying device:$SFS..." >&2
       mount ${SFS}1 $MountPoint -t vfat -r >&2
       if [ -f $MountPoint/$KeyFileName ]; then
          echo "> keyfile got..." >&2
          luksfile=$MountPoint/$KeyFileName
          break
       fi
    done

5\.Ejecute "dracut – f - v" para actualizar initrd

##### CentOS 7
1\.Edite /etc/dracut.conf add\_drivers += " vfat nls\_cp437 nls\_iso8859-1"

2\.Añada comentarios a estas líneas al final del archivo "/ usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\.Anexe DRACUT\_SYSTEMD = 0 al principio del archivo “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” y cambie todas las instancias de “if [ -z "$DRACUT\_SYSTEMD" ]; then” por “if [ 1 ]; then”

4\.Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh y anéxelo después de “# Open LUKS device”

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        luksfile=$MountPoint/$KeyFileName
        break
    fi
    done


5\.Ejecute “/usr/sbin/dracut -f -v” para actualizar initrd

###Carga de un VHD cifrado a una cuenta de almacenamiento de Azure
Cuando el cifrado de BitLocker o el cifrado DM-Crypt estén habilitados, es preciso cargar el VHD cifrado local en la cuenta de almacenamiento.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### Carga del secreto del cifrado de disco de la máquina virtual precifrada en el almacén de claves
El secreto del cifrado de disco que se obtuvo con anterioridad es necesario que se cargue como secreto en el almacén de claves.

#### Secreto del cifrado de disco no cifrado con una KEK
Utilice [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) para aprovisionar el secreto en el almacén de claves. En el caso de una máquina virtual con Windows, el archivo bek se codifica como cadena base64 y, a continuación, se carga en el almacén de claves mediante el cmdlet Set-AzureKeyVaultSecret. En Linux, la frase de contraseña se codifica como cadena base64 y, a continuación, se cargan en el almacén de claves. Además, asegúrese de que se establecen las siguientes etiquetas al crear el secreto en el almacén de claves.

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }

    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      )

    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"

    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"

    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"}

    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"

    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags


#### Secreto del cifrado de disco cifrado con una KEK

Opcionalmente, el secreto se puede cifrar con una clave de cifrado de claves antes de cargarlo en el almacén de claves. Utilice la [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de encapsulamiento para cifrar por primera vez el secreto mediante la clave de cifrado de claves. El resultado de esta operación de encapsulamiento es una cadena codificada en URL como base64 que luego se carga como secreto con el cmdlet [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx).


##Descargar esta guía
Puede descargar esta guía de la [Galería de TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## Para obtener más información
[Exploración de Cifrado de disco de Azure con Azure PowerShell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Exploración de Cifrado de disco de Azure con Azure PowerShell, parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0211_2016-->