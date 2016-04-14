<properties
	pageTitle="Registro del Almacén de claves de Azure | Microsoft Azure"
	description="Use este tutorial para tener ayuda para empezar a trabajar con el registro del Almacén de claves de Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/08/2016"
	ms.author="cabailey"/>

# Registro del Almacén de claves de Azure #
Almacén de claves de Azure está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios del Almacén de claves](../../../../pricing/details/key-vault/).

## Introducción  
Después de haber creado uno o varios almacenes de claves, es probable que desee supervisar cómo y cuándo se accede a los almacenes de claves y quién lo hace. Puede hacerlo al habilitar el registro del Almacén de claves, que guarda la información en una cuenta de almacenamiento de Azure proporcionada por el usuario. Un nuevo contenedor llamado **insights-logs-auditevent** se crea automáticamente para su cuenta de almacenamiento especificada, y puede utilizar esta misma cuenta para recopilar registros de varios almacenes de claves.

Puede acceder a la información de registro como máximo, 10 minutos después de la operación del Almacén de claves. En la mayoría de los casos, será más rápido que esto. Es decisión suya administrar los registros en la cuenta de almacenamiento:

- Utilice los métodos de control de acceso de Azure estándar para proteger los registros mediante la restricción de quién puede tener acceso a ellos.
- Elimine los registros que ya no desee mantener en la cuenta de almacenamiento. 

Use este tutorial para tener ayuda para empezar a trabajar con el registro del Almacén de claves de Azure, para crear la cuenta de almacenamiento, habilitar el registro e interpretar la información de registro recopilada.


>[AZURE.NOTE]Este tutorial no incluye instrucciones sobre cómo crear almacenes de claves, claves o secretos. Para obtener información, consulte [Introducción al Almacén de claves de Azure](key-vault-get-started.md). O bien, para obtener instrucciones de la interfaz de la línea de comandos entre plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli.md).
>
>Actualmente, no es posible configurar el Almacén de claves de Azure en el portal de Azure. En su lugar, siga estas instrucciones de Azure PowerShell.

Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](key-vault-whatis.md)

## Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

- Un Almacén de claves existente que ha utilizado.  
- Azure PowerShell, **versión mínima: 1.0.1**. Para instalar Azure PowerShell y asociarla con su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Si ya instaló Azure PowerShell y no sabe la versión, en la consola de Azure PowerShell, escriba `(Get-Module azure -ListAvailable).Version`.  
- Suficiente almacenamiento en Azure para sus registros del Almacén de claves.


## <a id="connect"></a>Conexión a las suscripciones ##

Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:

    Login-AzureRmAccount 

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtendrá todas las suscripciones que están asociadas a esta cuenta y, de forma predeterminada, usará la primera.

Si tiene varias suscripciones, es posible que deba especificar la que se usó para crear el Almacén de claves de Azure. Escriba lo siguiente para ver las suscripciones de su cuenta:

    Get-AzureRmSubscription

A continuación, para especificar la suscripción asociada al Almacén de claves que registrará, escriba:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para obtener más información sobre cómo configurar PowerShell de Azure, consulte [Instalación y configuración de PowerShell de Azure](../powershell-install-configure.md).


## <a id="storage"></a>Creación de una cuenta de almacenamiento nueva para los registros ##

Aunque puede usar una cuenta de almacenamiento existente para sus registros, crearemos una cuenta de almacenamiento que se dedicará a los registros del Almacén de claves. Por comodidad para cuando tengamos que especificarlos más adelante, almacenaremos los detalles en una variable denominada **sa**.

Para una mayor facilidad de administración, también usaremos el grupo de recursos que contiene el Almacén de claves. Desde el [tutorial de introducción](key-vault-get-started.md), este grupo de recursos se denomina **ContosoResourceGroup**, y seguiremos usando la ubicación Este de Asia. Sustituya estos valores para los suyos propios, según corresponda:

	$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]Si decide usar una cuenta de almacenamiento existente, ésta debe usar la misma suscripción que el Almacén de claves y el modelo de implementación del Administrador de recursos, en lugar del modelo de implementación clásica.

## <a id="identify"></a>Identificación del Almacén de claves para los registros ##

En nuestro tutorial de introducción, el nombre de nuestro Almacén de claves era **ContosoKeyVault**, por lo que seguiremos usando ese nombre y almacenaremos los detalles en una variable denominada **kv**:

	$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Habilitación del registro ##

Para habilitar el registro del Almacén de claves, usaremos el cmdlet Set-AzureRmDiagnosticSetting, junto con las variables que hemos creado para nuestra nueva cuenta de almacenamiento y nuestro Almacén de claves. También estableceremos la marca **-Enabled** en **$true**, y estableceremos la categoría en AuditEvent (la única categoría para el registro del Almacén de claves):

   
	Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent


El resultado de esto incluye:

**Registros**

**Enabled : True**

**Category : AuditEvent**

Esto confirma que el registro está habilitado ahora para el Almacén de claves y guarda la información en su cuenta de almacenamiento.

Qué se registra:

- Se registran todas las solicitudes de API de REST autenticadas, incluidas las solicitudes con error debido a permisos de acceso, errores del sistema o solicitudes incorrectas.
- Las operaciones en el Almacén de claves, incluidas la creación, la eliminación, la definición de políticas de acceso del Almacén de claves y la actualización de los atributos del Almacén de claves, como las etiquetas.
- Las operaciones en claves y secretos del Almacén de claves, incluidas la creación, la modificación o la eliminación de estas claves o secretos; operaciones como firmar, comprobar, cifrar, descifrar, encapsular y desencapsular claves, obtener secretos, generar listas de claves y secretos y sus versiones.

Las solicitudes no autenticadas no se registran.

## <a id="access"></a>Acceso a los registros ##

Los registros del Almacén de claves se almacenan en el contenedor **insights-logs-auditevent** de la cuenta de almacenamiento proporcionada. Para mostrar una lista de todos los blobs de este contenedor, escriba:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

El resultado será similar al siguiente.

****Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Name**

**----**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****
 

Como puede ver en esta salida, los blobs siguen una convención de nomenclatura: **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

Los valores de fecha y hora usan UTC.

Puesto que la misma cuenta de almacenamiento puede usarse para recopilar registros de varios recursos, el identificador de recurso completo en el nombre del blob es muy útil para acceder o descargar solo los blobs que necesita. Pero antes de hacerlo, primero explicaremos cómo descargar todos los blobs.

En primer lugar, cree una carpeta para descargar los blobs. Por ejemplo:

	New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

A continuación, obtenga una lista de todos los blobs:

	$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Canalice esta lista mediante 'Get-AzureStorageBlobContent' para descargar los blobs en la carpeta de destino:

	$blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Al ejecutar este segundo comando, el delimitador **/** en los nombres de los blobs crea una estructura de carpeta completa en la carpeta de destino; esta estructura se usará para descargar y almacenar los blobs como archivos.

Para descargar blobs de forma selectiva, utilice caracteres comodín. Por ejemplo:

- Si tiene varios almacenes de claves y desea descargar los registros solo para un Almacén de claves, denominado CONTOSOKEYVAULT3:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Si tiene varios grupos de recursos y desea descargar los registros solo para un grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Si desea descargar todos los registros del mes de enero de 2016, use `-Blob '*/year=2016/m=01/*'`:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Ahora está listo para comenzar a ver lo que está en los registros. Pero antes de pasar a eso, hay otros dos parámetros de Get-AzureRmDiagnosticSetting que puede ser necesario conocer:

- Para consultar la configuración del estado de diagnóstico para el recurso del Almacén de claves: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
 
- Para deshabilitar el registro para el recurso del Almacén de claves: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpretación de los registros del Almacén de claves ##

Los blobs individuales se almacenan como texto, con formato de blob JSON. A continuación se muestra una entrada del registro de ejemplo cuando se ejecuta `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

	{
    	"records": 
    	[
        	{
        	    "time": "2016-01-05T01:32:01.2691226Z",
        	    "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            	"operationName": "VaultGet",
            	"operationVersion": "2015-06-01",
            	"category": "AuditEvent",
            	"resultType": "Success",
            	"resultSignature": "OK",
            	"resultDescription": "",
            	"durationMs": "78",
            	"callerIpAddress": "104.40.82.76",
            	"correlationId": "",
            	"identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
            	"properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
        	}
    	]
	}


En la tabla siguiente se muestran los nombres y las descripciones de los campos.


| Nombre del campo | Descripción |
| ------------- |-------------|
| Twitter en tiempo | Fecha y hora (UTC).|
| resourceId | Identificador de recursos del Administrador de recursos de Azure Para los registros del Almacén de claves, siempre es el identificador de recurso de Almacén de claves.|
| operationName | Nombre de la operación, como se documenta en la tabla siguiente.|
| operationVersion | Se trata de la versión de API de REST solicitada por el cliente.|
| categoría | Para los registros del Almacén de claves, AuditEvent es el único valor disponible.|
| resultType | Resultado de la solicitud de API de REST.|
| resultSignature | Estado de HTTP|
| resultDescription | Descripción adicional acerca del resultado, cuando esté disponible.|
| durationMs | Tiempo que tardó en atender la solicitud de API de REST, en milisegundos. Esto no incluye la latencia de red, por lo que el tiempo que se mide en el cliente podría no coincidir con este tiempo.|
| callerIpAddress | Dirección IP del cliente que realizó la solicitud.|
| correlationId | Un GUID opcional que el cliente puede pasar para correlacionar los registros del lado cliente con los registros del lado servicio (el Almacén de claves).|
| identidad | Identidad del token que se ha presentado al realizar la solicitud de API de REST. Suele ser un "usuario", una "entidad de servicio" o una combinación de "usuario + appId" como en el caso de una solicitud procedente de un cmdlet de Azure PowerShell.|
| propiedades | Este campo contendrá información diferente en función de la operación (operationName). En la mayoría de los casos, contiene información del cliente (la cadena useragent pasada por el cliente), el URI de la solicitud de API de REST exacta y el código de estado HTTP. Además, cuando se devuelve un objeto como resultado de una solicitud (por ejemplo, KeyCreate o VaultGet) también contiene el URI de la clave (como "id"), el URI del almacén o el URI del secreto.|

 


Los valores del campo **operationName** están en formato ObjectVerb. Por ejemplo:

- Todas las operaciones del Almacén de claves tienen el formato 'Vault`<action>`', como `VaultGet` y `VaultCreate`. 

- Todas las operaciones de las claves tienen el formato 'Key`<action>`', como `KeySign` y `KeyList`.

- Todas las operaciones del secreto tienen el formato 'Secret`<action>`', como `SecretGet` y `SecretListVersions`.

En la tabla siguiente se muestra el operationName y el comando de API de REST correspondiente.

| operationName | Comando de API de REST |
| ------------- |-------------|
| Autenticación | Mediante un punto de conexión de Azure Active Directory|
| VaultGet | [Obtener información acerca de un almacén de claves](https://msdn.microsoft.com/es-ES/library/azure/mt620026.aspx)|
| VaultPut | [Crear o actualizar un almacén de claves](https://msdn.microsoft.com/es-ES/library/azure/mt620025.aspx)|
| VaultDelete | [Eliminar un almacén de claves](https://msdn.microsoft.com/es-ES/library/azure/mt620022.aspx)|
| VaultPatch | [Crear o actualizar un almacén de claves](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList | [Lista de todos los almacenes de claves en un grupo de recursos](https://msdn.microsoft.com/es-ES/library/azure/mt620027.aspx)|
| KeyCreate | [Crear una clave](https://msdn.microsoft.com/es-ES/library/azure/dn903634.aspx)|
| KeyGet | [Obtener información sobre una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878080.aspx)|
| KeyImport | [Importar una clave a un almacén](https://msdn.microsoft.com/es-ES/library/azure/dn903626.aspx)|
| KeyBackup | [Copia de seguridad de una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878058.aspx).|
| KeyDelete | [Eliminar una clave](https://msdn.microsoft.com/es-ES/library/azure/dn903611.aspx)|
| KeyRestore | [Restauración de una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878106.aspx)|
| KeySign | [Firma con una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878096.aspx)|
| KeyVerify | [Comprobación con una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878082.aspx)|
| KeyWrap | [Encapsulamiento de una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878066.aspx)|
| KeyUnwrap | [Desencapsulamiento de una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878079.aspx)|
| KeyEncrypt | [Cifrar con una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878060.aspx)|
| KeyDecrypt | [Descifrado con una clave](https://msdn.microsoft.com/es-ES/library/azure/dn878097.aspx)|
| KeyUpdate | [Actualizar una clave](https://msdn.microsoft.com/es-ES/library/azure/dn903616.aspx)|
| KeyList | [Enumeración de las claves en un almacén](https://msdn.microsoft.com/es-ES/library/azure/dn903629.aspx)|
| KeyListVersions | [Enumerar las versiones de una clave](https://msdn.microsoft.com/es-ES/library/azure/dn986822.aspx)|
| SecretSet | [Crear un secreto](https://msdn.microsoft.com/es-ES/library/azure/dn903618.aspx)|
| SecretGet | [Obtención de información acerca de un secreto](https://msdn.microsoft.com/es-ES/library/azure/dn903633.aspx)|
| SecretUpdate | [Actualizar un secreto](https://msdn.microsoft.com/es-ES/library/azure/dn986818.aspx)|
| SecretDelete | [Eliminar un secreto](https://msdn.microsoft.com/es-ES/library/azure/dn903613.aspx)|
| SecretList | [Enumeración de secretos en un almacén](https://msdn.microsoft.com/es-ES/library/azure/dn903614.aspx)|
| SecretListVersions | [Enumeración de versiones de un secreto](https://msdn.microsoft.com/es-ES/library/azure/dn986824.aspx)|




## <a id="next"></a>Pasos siguientes ##

Para ver un tutorial que usa el Almacén de claves de Azure en una aplicación web, consulte [Uso del Almacén de claves de Azure desde una aplicación web](key-vault-use-from-web-application.md).

Para conocer las referencias de programación, consulte la [Guía del desarrollador del Almacén de claves de Azure](key-vault-developers-guide.md).

Para obtener una lista de los cmdlets de Azure PowerShell 1.0 para Almacén de claves de Azure, consulte [Azure Key Vault Cmdlets](https://msdn.microsoft.com/library/azure/dn868052.aspx) (Cmdlets de Almacén de claves de Azure).
 

<!---HONumber=AcomDC_0114_2016-->