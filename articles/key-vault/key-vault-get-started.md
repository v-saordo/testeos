<properties
	pageTitle="Introducción a Almacén de claves de Azure | Microsoft Azure"
	description="Use este tutorial para empezar a trabajar con el Almacén de claves de Azure para crear un contenedor reforzado en Azure en el que almacenar y administrar las claves criptográficas y los secretos en Azure."
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
	ms.date="02/23/2016"
	ms.author="cabailey"/>

# Introducción al Almacén de claves de Azure #
Almacén de claves de Azure está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios del Almacén de claves](../../../../pricing/details/key-vault/).

## Introducción  
Use este tutorial para empezar a trabajar con el Almacén de claves de Azure para crear un contenedor (un almacén) reforzado en Azure en el que almacenar y administrar las claves criptográficas y los secretos en Azure. Describe cómo usar Azure PowerShell para crear un almacén que contiene una clave o una contraseña que se puede usar con una aplicación de Azure. A continuación, se explica cómo usa una aplicación esa clave o contraseña.

**Tiempo estimado para completar el tutorial:** 20 minutos

>[AZURE.NOTE]  Este tutorial no incluye instrucciones sobre cómo escribir la aplicación de Azure incluida en uno de los pasos, concretamente cómo autorizar a una aplicación para que use una clave o un secreto del Almacén de claves.
>
>Actualmente, no es posible configurar el Almacén de claves de Azure en el portal de Azure. En su lugar, siga estas instrucciones de Azure PowerShell. O bien, para obtener instrucciones de la interfaz de la línea de comandos entre plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli.md).

Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](key-vault-whatis.md)

## Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

- Una suscripción a Microsoft Azure. Si no tiene, puede registrarse para obtener una [cuenta gratuita](../../../../pricing/free-trial).
- Azure PowerShell, **versión mínima: 1.1.0**. Para instalar Azure PowerShell y asociarla con su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Si ya instaló Azure PowerShell y no sabe la versión, en la consola de Azure PowerShell, escriba `(Get-Module azure -ListAvailable).Version`. Si tiene Azure PowerShell versiones 0.9.1 a 0.9.8 instalado, también puede usar este tutorial con algunos cambios menores. Por ejemplo, debe usar el comando `Switch-AzureMode AzureResourceManager` y algunos de los comandos de Almacén de claves de Azure que cambiaron. Para obtener una lista de los cmdlets del Almacén de claves para las versiones 0.9.1 a 0.9.8, consulte [Azure Key Vault Cmdlets](https://msdn.microsoft.com/library/azure/dn868052(v=azure.98).aspx) (Cmdlets de Almacén de claves de Azure). 
- Una aplicación que se configurará para utilizar la clave o contraseña creada en este tutorial. Hay una aplicación de ejemplo disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/es-ES/download/details.aspx?id=45343). Para obtener instrucciones, consulte el archivo Léame adjunto.


Este tutorial está diseñado para usuarios principiantes de Azure PowerShell, pero se presupone que conocen conceptos básicos tales como módulos, cmdlets y sesiones. Para obtener más información, consulte [Introducción a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet **Get-Help**.

	Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet **Add-AzureRmAccount**, escriba:

	Get-Help Login-AzureRmAccount -Detailed

Lea también los siguientes tutoriales para familiarizarse con el Administrador de recursos de Azure en Azure PowerShell:

- [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)
- [Uso de Azure PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Conexión a las suscripciones ##

Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:

    Login-AzureRmAccount 

Tenga en cuenta que si usa una instancia específica de Azure, por ejemplo, Azure Government, use el parámetro -Environment con este comando. Por ejemplo: `Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtendrá todas las suscripciones que están asociadas a esta cuenta y, de forma predeterminada, usará la primera.

Si tiene varias suscripciones y desea especificar una en concreto para que use el Almacén de claves de Azure, escriba lo siguiente para ver las suscripciones de su cuenta:

    Get-AzureRmSubscription

A continuación, para especificar la suscripción que se debe usar, escriba:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para obtener más información sobre cómo configurar PowerShell de Azure, consulte [Instalación y configuración de PowerShell de Azure](../powershell-install-configure.md).


## <a id="resource"></a>Creación de un nuevo grupo de recursos ##

Cuando se utiliza el Administrador de recursos de Azure, todos los recursos relacionados se crean dentro de un grupo de recursos. Crearemos un nuevo grupo de recursos denominado **ContosoResourceGroup** para este tutorial:

	New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Creación de un Almacén de claves ##

Use el cmdlet [New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736.aspx) para crear un almacén de claves. Este cmdlet tiene tres parámetros obligatorios: el **nombre del grupo de recursos**, el **nombre del Almacén de claves** y la **ubicación geográfica**.

Por ejemplo, si utiliza el nombre del almacén de **ContosoKeyVault**, el nombre del grupo de recursos **ContosoResourceGroup** y la ubicación **East Asia**, deberá escribir:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

El resultado de este cmdlet muestra las propiedades del Almacén de claves que acaba de crear. Las dos propiedades más importantes son:

- **Nombre de almacén**: en este ejemplo, el nombre es **ContosoKeyVault**. Utilizará este nombre para otros cmdlets del Almacén de claves.
- **URI de almacén**: en este ejemplo, es https://contosokeyvault.vault.azure.net/. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

Su cuenta de Azure ahora está autorizada para realizar operaciones en este Almacén de claves. Hasta el momento, nadie más lo está.

>[AZURE.NOTE]  Si ve el error **La suscripción no está registrada para usar el espacio de nombres 'Microsoft.KeyVault'** al intentar crear un almacén de claves nuevo, ejecute `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` y después vuelva a ejecutar el comando New-AzureRmKeyVault. Para más información, consulte [Register-AzureRmProvider](https://msdn.microsoft.com/library/mt679020.aspx).
>

## <a id="add"></a>Adición de una clave o un secreto al Almacén de claves ##

Si desea que el Almacén de claves de Azure cree una clave protegida mediante software, utilice el cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) y escriba lo siguiente:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Sin embargo, si ya tiene una clave protegida mediante software en un archivo .PFX guardada en la unidad C:\\ en un archivo denominado softkey.pfx que desea cargar en el Almacén de claves de Azure, escriba lo siguiente para establecer la variable **securepfxpwd** para una contraseña de **123** para el archivo .PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

A continuación, escriba lo siguiente para importar la clave desde el archivo .PFX, que protege la clave mediante software en el servicio de almacén de claves:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Ahora puede utilizar el URI para hacer referencia a esta clave que creó o cargó en el Almacén de claves de Azure. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obtener siempre la versión actual y **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obtener esta versión específica.

Para mostrar el URI para esta clave, escriba:

	$Key.key.kid

Para agregar un secreto, que es una contraseña denominada SQLPassword con el valor Pa$$w0rd, al Almacén de claves de Azure, convierta primero el valor Pa$$w0rd en una cadena segura escribiendo lo siguiente:

	$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

A continuación, escriba lo siguiente:

	$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Ahora puede hacer referencia a esta clave que agregó al Almacén de claves de Azure utilizando su URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obtener siempre la versión actual y **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión específica.

Para mostrar el URI para este secreto, escriba:

	$secret.Id

Veamos la clave o el secreto que acaba de crear:

- Para ver la clave, escriba: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Para ver el secreto, escriba: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Ahora, el Almacén de claves y la clave o el secreto están listos para que los usen las aplicaciones. Las aplicaciones deben contar con autorización para poder usarlos.

## <a id="register"></a>Registro de una aplicación con Azure Active Directory ##

Este paso lo haría normalmente un programador en un equipo independiente. No es específico del Almacén de claves de Azure, pero se incluye aquí a fin de que la información ofrecida sea lo más completa posible.


>[AZURE.IMPORTANT] Para finalizar el tutorial, la cuenta, el almacén y la aplicación que vaya a registrar en este paso deben estar en el mismo directorio de Azure.

Las aplicaciones que utilizan un Almacén de claves deben autenticarse utilizando un token de Azure Active Directory. Para ello, el propietario de la aplicación debe registrarla primero en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los valores siguientes:


- Un **identificador de aplicación** (también conocido como un "identificador del cliente") y una **clave de autenticación** (también conocida como "secreto compartido"). Para obtener un token, la aplicación debe presentar estos dos valores a Azure Active Directory. La forma en que se configura la aplicación para ello depende de la aplicación. Para la aplicación de ejemplo del Almacén de claves, el propietario de la aplicación establece estos valores en el archivo app.config.

Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el portal clásico de Azure.
2. A la izquierda, haga clic en **Active Directory**. A continuación, elija el directorio en el que se registrará la aplicación. <br> <br> **Nota:** debe seleccionar el mismo directorio que contiene la suscripción de Azure con la que se creó el Almacén de claves. Si no sabe qué directorio es, haga clic en **Configuración**, identifique la suscripción con la que se creó el Almacén de claves y anote el nombre del directorio que se muestra en la última columna.

3. Haga clic en **Aplicaciones**. Si no se han agregado aplicaciones a su directorio, esta página muestra solo el vínculo **Agregar una aplicación**. Haga clic en el vínculo, o como alternativa, puede hacer clic en **AGREGAR** en la barra de comandos.
4.	En el asistente **Agregar aplicación**, en la página **¿Qué desea hacer?**, haga clic en **Agregar una aplicación que mi organización está desarrollando**.
5.	En la página **Proporcione información sobre su aplicación**, especifique un nombre para la aplicación y, a continuación, elija **APLICACIÓN WEB Y/O API WEB** (valor predeterminado). Haga clic en el icono **Siguiente**.
6.	En la página **Propiedades de la aplicación**, especifique **URL de inicio de sesión** y **URI de Id. de aplicación** para la aplicación web. Si la aplicación no tiene estos valores, puede inventárselos en este paso (por ejemplo, podría especificar http://test1.contoso.com para ambos). No importa si estos sitios existen; lo importante es que el URI del identificador de la aplicación de cada aplicación sea diferente en cada aplicación del directorio. El directorio utiliza esta cadena para identificar la aplicación.
7.	Haga clic en el icono **Completar** para guardar los cambios en el Asistente.
8.	En la página de **inicio rápido**, haga clic en **CONFIGURAR**.
9.	Desplácese hasta la sección **Claves**, especifique la duración y, a continuación, haga clic en **Guardar**. La página se actualiza y muestra ahora un valor de clave. Debe configurar la aplicación con este valor de clave y el valor del **identificador del cliente**. (Las instrucciones para realizar esta configuración serán específicas para la aplicación).
10.	Copie el valor del identificador del cliente en esta página. Lo utilizará en el paso siguiente para definir los permisos del almacén.

## <a id="authorize"></a>Autorización de la aplicación para que use la clave o el secreto ##

Para autorizar a la aplicación a acceder a la clave o secreto del almacén, use el cmdlet [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx).

Por ejemplo, si el nombre del almacén es **ContosoKeyVault** y la aplicación que desea autorizar tiene el identificador de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed y desea que la aplicación tenga autorización para descifrar y firmar con claves en el almacén, ejecute lo siguiente:


	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Si desea autorizar a esa misma aplicación para leer los secretos en el almacén, ejecute lo siguiente:


	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Si desea utilizar un módulo de seguridad de hardware (HSM) ##

Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Los HSM tienen la validación FIPS 140-2 de nivel 2. Si este requisito no es relevante para usted, omita esta sección y vaya al paso [Eliminación del Almacén de claves junto con las claves y secretos asociados](#delete).

Para crear estas claves protegidas con HSM, debe contar con una [suscripción de almacén que admita claves protegidas mediante HSM](../../../pricing/free-trial). Además, esta funcionalidad no está disponible para Azure China.


Cuando cree el almacén, agregue el parámetro **-SKU**:


	New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



A este almacén, se pueden agregar claves protegidas mediante software (tal como se ha mostrado anteriormente) y claves protegidas con HSM. Para crear una clave protegida con HSM, establezca el parámetro **-Destination** en 'HSM':

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Puede utilizar el siguiente comando para importar una clave desde un archivo .PFX a su equipo. Este comando importa la clave a HSM en el servicio de Almacén de claves:

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


El comando siguiente importa un paquete BYOK ("traiga su propia clave"). Esto permite generar la clave en el HSM local y transferirla al HSM en el servicio del Almacén de claves, sin que la clave salga del límite del HSM:

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Para obtener instrucciones detalladas sobre cómo generar el paquete BYOK, consulte [Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Eliminación del Almacén de claves junto con las claves y secretos asociados ##

Si ya no necesita el almacén de claves, ni la clave o secreto que contiene, puede eliminarlo mediante el cmdlet [Remove-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485.aspx):

	Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

O bien puede eliminar un grupo de recursos de Azure completo, que incluye el Almacén de claves y otros recursos incluidos en dicho grupo:

	Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Otros cmdlets de Azure PowerShell ##

Estos son otros comandos que pueden resultar útiles para administrar el Almacén de claves de Azure.

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: este comando ofrece una presentación tabular de todas las claves y las propiedades seleccionadas.
- `$Keys[0]`: este comando muestra una lista completa de propiedades para la clave especificada.
- `Get-AzureKeyVaultSecret`: este comando muestra una presentación tabular de todos nombres de secretos y las propiedades que se elijan.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: ejemplo de cómo eliminar una clave específica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: ejemplo de cómo eliminar un secreto específico.


## <a id="next"></a>Pasos siguientes ##

Para obtener un tutorial de seguimiento que usa el Almacén de claves de Azure en una aplicación web, consulte [Uso del Almacén de claves de Azure desde una aplicación web](key-vault-use-from-web-application.md).

Para ver cómo se usa el almacén de claves, consulte [Registro del Almacén de claves de Azure](key-vault-logging.md).

Para obtener una lista de los cmdlets de Azure PowerShell más recientes para el Almacén de claves de Azure, consulte [Azure Key Vault Cmdlets](https://msdn.microsoft.com/library/azure/dn868052.aspx) (Cmdlets del Almacén de claves de Azure).
 

Para conocer las referencias de programación, consulte la [Guía del desarrollador del Almacén de claves de Azure](key-vault-developers-guide.md).

<!---HONumber=AcomDC_0224_2016-->