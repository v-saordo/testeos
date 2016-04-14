<properties
	pageTitle="Cómo generar y transferir claves protegidas con HSM para el Almacén de claves de Azure | Microsoft Azure"
	description="Use este artículo para ayudarle a planear, generar y, a continuación, transfiera sus propias claves protegidas con HSM para utilizar con el Almacén de clave de Azure."
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
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="cabailey"/>
#Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure

##Introducción

Para obtener una mayor seguridad, cuando utilice el Almacén de claves de Azure, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Con frecuencia este escenario se conoce también como *Aportar tu propia clave* , o BYOK. Los HSM tienen la validación FIPS 140-2 de nivel 2. El Almacén de claves de Azure usa la familia Thales nShield de HSM para proteger sus claves.

La información de este tema le ayudará a planear, generar y, a continuación, transferir sus propias claves protegidas por HSM para utilizarlas con el Almacén de claves de Azure.

Esta funcionalidad no está disponible para Azure China.

>[AZURE.NOTE] Para obtener más información sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de claves de Azure?](key-vault-whatis.md)
>
>Para ver tutorial introductorio, que incluye la creación de un almacén de claves para claves protegidas con HSM, consulte [Introducción al Almacén de claves de Azure](key-vault-get-started.md).

Obtenga más información acerca de cómo generar y transferir una clave protegida con HSM a través de Internet:

- Genere la clave desde una estación de trabajo sin conexión, lo que reduce la superficie de ataque.

- La clave se cifra con una clave de intercambio de claves (KEK), que permanece cifrada hasta que se transfiere a los HSM del Almacén de claves de Azure. Solo la versión cifrada de la clave deja la estación de trabajo original.

- El conjunto de herramientas establece las propiedades en su clave de inquilino que enlaza la clave con el espacio de seguridad del Almacén de claves de Azure. Por consiguiente, después de que los HSM del Almacén de claves de Azure reciban y descifren la clave, dichos HSM son los únicos los que puedan usarla. La clave no se puede exportar. Este enlace lo exigen los HSM de Thales.

- La clave de intercambio de claves (KEK) que se utiliza para cifrar la clave se genera dentro de los HSM del Almacén de clave de Azure y no es exportable. Los HSM exigen que no pueda haber una versión sin cifrar de la KEK fuera de los HSM. Además, el conjunto de herramientas incluye la atestación desde Thales de que la KEK no es exportable y se generó dentro de un HSM genuino que fabricó Thales.

- El conjunto de herramientas incluye la atestación desde Thales de que el espacio de seguridad del Almacén de claves de Azure también se generó en un HSM genuino que fabricó Thales. Así se demuestra que Microsoft usa hardware genuino.

- Microsoft usa KEK independientes, así como espacios de seguridad independientes en cada región geográfica, lo que garantiza que la clave se puede usar únicamente en centros de datos de la región en la que se cifró. Por ejemplo, una clave de un cliente europeo no se puede utilizar en centros de datos de América del Norte o Asia.

##Más información acerca de los HSM de Thales y los servicios de Microsoft

Thales e-Security es uno de los principales proveedores mundiales de soluciones de cifrado de datos y ciberseguridad para servicios financieros, tecnología punta, industria manufacturera, gobierno y sectores tecnológicos. Las soluciones de Thales cuentan con una trayectoria de 40 años en la protección de información corporativa y gubernamental, y las usan cuatro de las cinco mayores compañías de los sectores energético y aeroespacial, y 22 países de la OTAN y protegen más del 80 % de las operaciones de pago en todo el mundo.

Microsoft ha colaborado con Thales para mejorar la tecnología de vanguardia de los HSM. Estas mejoras te permiten obtener los beneficios típicos de los servicios hospedados sin tener que renunciar al control de las claves. En concreto, estas mejoras permiten que Microsoft administre los HSM para que no lo tengan que hacer sus usuarios. Al ser un servicio en la nube, el Almacén de claves de Azure se escala verticalmente en muy poco tiempo para cubrir los picos de uso de cualquier organización. Al mismo tiempo, la clave está protegida dentro de los HSM de Microsoft y se conserva el control sobre su ciclo de vida, ya que es el usuario el que genera la clave y la transfiere a los HSM de Microsoft.

##Implementación del método Aportar tu propia clave (BYOK) en el Almacén de claves de Azure

Utilice la siguiente información y procedimientos si va a generar su propia clave protegida con HSM y, a continuación, va a transferirla al Almacén de claves de Azure, el escenario de Aportar tu propia clave (BYOK).


##Requisitos previos de BYOK

En la tabla siguiente puede ver una lista de los requisitos previos del método Aportar tu propia clave (BYOK) para el Almacén de claves de Azure.

|Requisito|Más información|
|---|---|
|Una suscripción a Azure|Para crear un Almacén de claves de Azure, se necesita una suscripción a Azure: [Regístrese para obtener la versión de prueba gratuita](../../../../pricing/free-trial)|
|Un Almacén de claves de Azure que admita HSM|Para obtener más información sobre los niveles de servicio y las capacidades del Almacén de claves de Azure, consulte el sitio web [Precios de Almacén de claves](../../../../pricing/details/key-vault/).|
|HSM de Thales, tarjetas inteligentes y software compatible|Debe tener acceso al módulo de seguridad de hardware de Thales y al conocimiento operacional básico de los HSM de Thales. Para ver la lista de modelos compatibles o comprar un HSM, consulte [Módulo de seguridad de hardware de Thales](https://www.thales-esecurity.com/msrms/buy).|
|El siguiente hardware y software:<ol><li>Una estación de trabajo x64 sin conexión con un sistema operativo Windows 7, como mínimo, y el software Thales nShield, como mínimo la versión 11.50.<br/><br/>Si la estación de trabajo utiliza Windows 7, debe [instalar Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Una estación de trabajo conectada a Internet y con un sistema operativo Windows 7, como mínimo.</li><li>Una unidad USB u otro dispositivo de almacenamiento portátil que tenga al menos 16 MB de espacio disponible.</li></ol>|Por seguridad, se recomienda que la primera estación de trabajo no esté conectada a una red. Sin embargo, esto no es de obligado cumplimiento.<br/><br/>Tenga en cuenta que en las instrucciones que se indican a continuación, a esta estación de trabajo se la conoce como la estación de trabajo desconectada.</p></blockquote><br/>Además, si la clave de inquilino es para una red de producción, se recomienda usar una segunda estación de trabajo independiente para descargar el conjunto de herramientas y cargar la clave de inquilino. Sin embargo, para la prueba puede usar la misma estación de trabajo que la primera.<br/><br/>Tenga en cuenta que en las siguientes instrucciones a la segunda estación de trabajo se la conoce como la estación de trabajo conectada a Internet.</p></blockquote><br/>|

##Generación y transferencia de una clave a un HSM del Almacén de claves de Azure

Los cinco pasos siguientes se utilizan para generar y transferir una clave a un HSM del Almacén de claves de Azure:

- [Paso 1: preparación de la estación de trabajo conectada a Internet](#step-1-prepare-your-internet-connected-workstation)
- [Paso 2: preparación de la estación de trabajo desconectada](#step-2-prepare-your-disconnected-workstation)
- [Paso 3: generación de la clave](#step-3-generate-your-key)
- [Paso 4: preparación de la clave para la transferencia](#step-4-prepare-your-key-for-transfer)
- [Paso 5: transferencia de la clave al Almacén de claves de Azure](#step-5-transfer-your-key-to-azure-key-vault)

## Paso 1: preparación de la estación de trabajo conectada a Internet
En este primer paso, realice los siguientes procedimientos en la estación de trabajo conectada a Internet.


###Paso 1.1: instalación de Azure PowerShell

Desde la estación de trabajo conectada a Internet, descargue e instale el módulo de Azure PowerShell que incluye los cmdlets para administrar el Almacén de claves de Azure. Esto requiere una versión mínima de 0.8.13.

Para ver las instrucciones de instalación, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

###Paso 1.2: obtención del Id. de suscripción de Azure

Inicie una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:

		Add-AzureAccount
En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. A continuación, utilice el comando [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx):

		Get-AzureSubscription
En la salida, busque el identificador de la suscripción que va a utilizar para el Almacén de claves de Azure. Lo necesitará más adelante.

No cierre la ventana de Azure PowerShell.

###Paso 1.3: descarga del conjunto de herramientas BYOK para el Almacén de claves de Azure

Vaya al Centro de descarga de Microsoft y [descargue el conjunto de herramientas de BYOK para el Almacén de claves de Azure](http://www.microsoft.com/download/details.aspx?id=45345) de su región geográfica o instancia de Azure:

|Región geográfica o instancia de Azure|Nombre del paquete|Hash del paquete SHA-256|
|---|---|---|
|Norteamérica|KeyVault-BYOK-Tools-UnitedStates.zip|D9FDA9F5A34E1388CD6C9138E5B75B7051FB7D6B11F087AFE0553DC85CCF0E36|
|Europa|KeyVault-BYOK-Tools-Europe.zip|881DCA798305B8408C06BAE7B3EFBC1E9EA6113A8D6EC443464F3744896F32C3|
|Asia|KeyVault-BYOK-Tools-AsiaPacific.zip|0C76967B3AC76687E4EA47EB96174EE6B25AB24E3114E28A90D9B93A2E6ABF6E|
|América Latina|KeyVault-BYOK-Tools-LatinAmerica.zip|B38015990D4D1E522B8367FF78E78E0234BF9592663470426088C44C3CAAAF48|
|Japón|KeyVault-BYOK-Tools-Japan.zip|DB512CD9472FDE2FD610522847DF05E4D7CD49A296EE4A2DD74D43626624A113|
|Australia|KeyVault-BYOK-Tools-Australia.zip|8EBC69E58E809A67C036B50BB4F1130411AD87A7464E0D61A9E993C797915967|
|[Azure Government](../../../../features/gov/)|KeyVault-BYOK-Tools-USGovCloud.zip|4DE9B33990099E4197ED67D786316F628E5218FC1EB0C24DCAD8A1851FD345B8|

Para validar la integridad del conjunto de herramientas BYOK que descargó, use el cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) en la sesión de Azure PowerShell.

	Get-FileHash KeyVault-BYOK-Tools-*.zip

El conjunto de herramientas incluye:

- Un paquete de clave de intercambio de claves (KEK) cuyo nombre comienza por **BYOK-KEK-pkg-.**
- Un paquete de espacio de seguridad cuyo nombre comienza por **BYOK-SecurityWorld-pkg-.**
- Un script Python denominado v**erifykeypackage.py.**
- Un archivo ejecutable de línea de comandos denominado **KeyTransferRemote.exe** y asociado a las DLL.
- Un paquete redistribuible de Visual C++ denominado **vcredist\_x64.exe.**

Copie el paquete en una unidad USB u otro dispositivo de almacenamiento portátil.

##Paso 2: preparación de la estación de trabajo desconectada

En este segundo paso, realice los siguientes procedimientos en la estación de trabajo que no está conectada a una red (Internet o la red interna).


###Paso 2.1: preparación de la estación de trabajo desconectada con HSM de Thales

Instale el software nCipher (Thales) en un equipo de Windows y, a continuación, adjunte un HSM de Thales a dicho equipo.

Asegúrese de que las herramientas de Thales estén en su ruta de acceso (**%nfast\_home%\\bin** y **%nfast\_home%\\python\\bin**). Por ejemplo, escriba lo siguiente:

		set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Para obtener más información, consulte el Manual del usuario que se incluye en el HSM de Thales.

###Paso 2.2: instalación del conjunto de herramientas BYOK en la estación de trabajo desconectada

Copie el paquete del conjunto de herramientas BYOK de la unidad USB, u otro dispositivo de almacenamiento portátil, y, a continuación, haga lo siguiente:

1. Extraiga los archivos del paquete descargado en cualquier carpeta.
2. En esa carpeta, ejecute vcredist\_x64.exe.
3. Siga las instrucciones para instalar los componentes de tiempo de ejecución de Visual C++ para Visual Studio 2012.

##Paso 3: generación de la clave

En el tercer paso, realice los siguientes procedimientos en la estación de trabajo desconectada.

###Paso 3.1: creación de un espacio de seguridad

Inicie un símbolo del sistema y ejecute el programa new-world de Thales.

	new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Este programa crea un archivo de **espacio de seguridad** en %NFAST\_KMDATA%\\local\\world, que corresponde a la carpeta C:\\ProgramData\\nCipher\\Key Management Data\\local. Puede usar valores diferentes para el quórum, pero en nuestro ejemplo, se le pedirá que escriba tres tarjetas en blanco y PIN para cada una de ellos. A continuación, dos tarjetas cualquiera le proporcionarán acceso completo al espacio de seguridad. Estas tarjetas pasan a ser el **Conjunto de tarjetas de administrador** del nuevo espacio de seguridad.

A continuación, haga lo siguiente:

- Realice una copia de seguridad del archivo del espacio. Proteja dicho archivo, las tarjetas de administrador y sus PIN, y asegúrese de que nadie tiene acceso a más de una tarjeta.

###Paso 3.2: validación del paquete descargado

Este paso es opcional, pero se recomienda realizarlo para poder validar estos elementos:

- La clave de intercambio de claves que se incluye en el conjunto de herramientas se ha generado desde un HSM de Thales original.
- El hash del espacio de seguridad que se incluye en el conjunto de herramientas se ha generado en un HSM de Thales original.
- La clave de intercambio de claves no es exportable.

>[AZURE.NOTE]Para validar el paquete descargado, el HSM debe estar conectado, encendido y debe tener un espacio de seguridad (como el que acaba de crear).

Para validar el paquete descargado:

1.	Ejecute el script verifykeypackage.py, para lo que, en función de su región geográfica o instancia de Azure, debe escribir lo siguiente:
	- Norteamérica:

			python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
	- Europa:

			python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
	- Asia:

			python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
	- América Latina:

			python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
	- Japón:

			python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
	- Para Australia:

			python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
	- Para [Azure Government](../../../../features/gov/), que usa la instancia del gobierno de Estados Unidos de Azure:

			python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1

	>[AZURE.TIP]El software de Thales incluye Python en %NFAST\_HOME%\\python\\bin

2.	Confirme que ve lo siguiente, que indica que el resultado de la validación ha sido satisfactorio: **Result: SUCCESS**

Este script valida la cadena del firmante hasta la clave raíz de Thales. El hash de esta clave raíz está insertado en el script y su valor debe ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Este valor también se puede confirmar por separado. Para ello, es preciso visitar el [sitio web de Thales](http://www.thalesesec.com/).

Ya está listo para crear una nueva clave.

###Paso 3.3: creación de una nueva clave

Genere una clave con el programa **generatekey** de Thales.

Ejecute el comando siguiente para generar la clave:

	generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Cuando ejecute este comando, siga estas instrucciones:

- El parámetro *protect* debe establecerse en el valor **module**, como se muestra. Esto crea una clave protegida por el módulo. El conjunto de herramientas BYOK no es compatible con claves protegidas con OCS.

- Reemplace el valor de *contosokey* en **ident** y **plainname** por cualquier valor de cadena. Para minimizar gastos administrativos y reducir el riesgo de errores, se recomienda utilizar el mismo valor en ambos. El valor **ident** debe contener solo números, guiones y minúsculas.

- En este ejemplo, pubexp se deja en blanco (valor predeterminado), pero puede especificar valores concretos. Para obtener más información, consulte la documentación de Thales.

Este comando crea un archivo de clave con tokens en la carpeta %NFAST\_KMDATA%\\local cuyo nombre comienza por **key\_simple\_**, seguido del valor ident que se especificó en el comando. Por ejemplo: **key\_simple\_contosokey**. Este archivo contiene una clave cifrada.

Realice una copia del archivo tokenizado en una ubicación segura.

>[AZURE.IMPORTANT] Cuando posteriormente transfiera la clave al Almacén de claves de Azure, Microsoft no podrá exportar esta clave, por lo que resulta extremadamente importante que realice una copia de seguridad de la misma y del espacio de seguridad. Póngase en contacto con Thales si necesita guía y procedimientos recomendados para realizar copias de seguridad de una clave.

Ya está listo para transferir la clave al Almacén de claves de Azure.

##Paso 4: preparación de la clave para la transferencia

En este paso, realice los siguientes procedimientos en la estación de trabajo desconectada.

###Paso 4.1: creación de una copia de la clave con menos permisos

Para reducir los permisos en su clave, desde un símbolo del sistema, ejecute uno de los siguientes comandos, en función de su región geográfica o instancia de Azure:

- Norteamérica:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Europa:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Asia:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- América Latina:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Japón:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Para Australia:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Para [Azure Government](../../../../features/gov/), que usa la instancia del Gobierno de los Estados Unidos de Azure:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1

Cuando ejecute este comando, reemplace *contosokey* por el valor que especificó en el **Paso 3.3: Creación de una nueva clave** del paso [Generación de la clave](#step-3-generate-your-key).

Se le pedirá que conectar sus tarjetas de administrador del espacio de seguridad.

Cuando se complete el comando, verá **Result: SUCCESS** y la copia de la clave con menos permisos estará en el archivo denominado key\_xferacId\_<contosokey>.

###Paso 4.2: inspección de la nueva copia de la clave

Opcionalmente, ejecute las utilidades de Thales para confirmar los permisos mínimos en la nueva clave:

- aclprint.py:

		"%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

		"%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Cuando ejecute estos comandos, reemplace contosokey por el mismo valor que especificó en el **Paso 3.3: Creación de una nueva clave** del paso [Generación de la clave](#step-3-generate-your-key).

###Paso 4.3: cifrado de la clave mediante la clave de intercambio de claves de Microsoft

Ejecute uno de los comandos siguientes, dependiendo de su región geográfica o la instancia de Azure:

- Norteamérica:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Europa:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Asia:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- América Latina:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Japón:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para Australia:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para [Azure Government](../../../../features/gov/), que usa la instancia del Gobierno de los Estados Unidos de Azure:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Cuando ejecute este comando, siga estas instrucciones:

- Reemplace *contosokey* por el identificador que usó para generar la clave en el **Paso 3.3: Creación de una nueva clave** del paso [Generación de la clave](#step-3-generate-your-key).

- Reemplace *SubscriptionID*por el Id. de la suscripción de Azure que contiene el almacén de claves. Este valor lo recuperó anteriormente, en el **Paso 1.2: obtención del Id. de suscripción de Azure** en el paso [Preparación de la estación de trabajo conectada a Internet](#step-1-prepare-your-internet-connected-workstation).

- Reemplace *ContosoFirstHSMKey* por una etiqueta que se usará para el nombre del archivo de salida.

Cuando la operación se complete correctamente, se mostrará **Result: SUCCESS** y habrá un nuevo archivo en la carpeta actual que tendrá el siguiente nombre: TransferPackage-*ContosoFirstHSMkey*.byok.

###Paso 4.4: copiar del paquete de transferencia de claves a la estación de trabajo conectada a Internet

Utilice una unidad USB u otro dispositivo de almacenamiento portátil para copiar el archivo de salida del paso anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) a la estación de trabajo conectada a Internet.

##Paso 5: transferencia de la clave al Almacén de claves de Azure

En este paso final, en la estación de trabajo conectada a Internet, use el cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) para cargar el paquete de transferencia de claves que copió de la estación de trabajo desconectada en el HSM del Almacén de claves de Azure:

	Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Si la carga se realiza correctamente, verá que se muestran las propiedades de la clave que acaba de agregar.


##Pasos siguientes

Ahora puede usar esta clave protegida con HSM en el almacén de claves. Para obtener más información, consulte la sección **Si desea usar un módulo de seguridad de hardware (HSM)** del tutorial[ Introducción al Almacén de claves de Azure](key-vault-get-started.md).

<!---HONumber=AcomDC_0204_2016-->