<properties
	pageTitle="Administración del Almacén de claves mediante CLI | Microsoft Azure"
	description="Use este tutorial para automatizar las tareas comunes en el Almacén de clave mediante el uso de la CLI."
	services="key-vault"
	documentationCenter=""
	authors="BrucePerlerMS"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="bruceper"/>

# Administración del Almacén de claves mediante CLI #
Almacén de claves de Azure está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios del Almacén de claves](../../../../pricing/details/key-vault/).

## Introducción  
Use este tutorial para empezar a trabajar con el Almacén de claves de Azure para crear un contenedor (un almacén) reforzado en Azure en el que almacenar y administrar las claves criptográficas y los secretos en Azure. Se describe el proceso para usar la interfaz de la línea de comandos entre plataformas de Azure fin de crear un almacén en el que se guardará una clave o una contraseña para usarla con una aplicación de Azure. A continuación, se explica cómo utiliza una aplicación esa clave o contraseña.

**Tiempo estimado para completar el tutorial:** 20 minutos

>[AZURE.NOTE]  Este tutorial no incluye instrucciones sobre cómo escribir la aplicación de Azure incluida en uno de los pasos, en el que se muestra cómo autorizar a una aplicación para que use una clave o un secreto del Almacén de claves.
>
>Actualmente, no es posible configurar el Almacén de claves de Azure en el portal de Azure. Por el contrario, use la interfaz de la línea de comandos entre plataformas de Azure. O bien, para obtener instrucciones de Azure PowerShell, consulte [este tutorial equivalente](key-vault-get-started.md).

Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](key-vault-whatis.md)

## Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

- Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para obtener una versión de [evaluación gratuita](../../../pricing/free-trial).
- Versión 0.9.1 o una posterior de la interfaz de la línea de comandos. Para instalar la última versión y conectarla a su suscripción de Azure, vea [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure](xplat-cli-install.md).
- Una aplicación que se configurará para utilizar la clave o contraseña creada en este tutorial. Hay una aplicación de ejemplo disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obtener instrucciones, consulte el archivo Léame adjunto.

## Obtención de ayuda con la interfaz de la línea de comandos entre plataformas de Azure

Este tutorial asume que está familiarizado con la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema).

Los parámetros --help o -h se pueden usar para ver la ayuda de comandos específicos. También puede usar el formato azure help [command] [options] para devolver la misma información. Por ejemplo, todos los comandos siguientes devuelven la misma información:

    azure account set --help

    azure account set -h

    azure help account set

Si duda de qué parámetros necesita un comando, consulte la ayuda usando --help, -h o azure help [command].

También puede leer los tutoriales siguientes para familiarizarse con el Administrador de recursos de Azure en la interfaz de la línea de comandos entre plataformas de Azure:

- [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure.](xplat-cli-install.md)
- [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](xplat-cli-azure-resource-manager.md)


## Conectarse a sus suscripciones

Para iniciar sesión usando una cuenta profesional, utilice el comando siguiente:

    azure login -u username -p password

o si desea iniciar sesión escribiendo interactivamente

    azure login

>[AZURE.NOTE]  El método de inicio de sesión solo funciona con la cuenta organizativa. Una cuenta profesional es un usuario administrado por su organización y definido en su inquilino de Azure Active Directory de la organización.


Si actualmente no tiene una cuenta profesional y usa una cuenta Microsoft para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiendo los pasos que se indican a continuación.

1.	Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y haga clic en Active Directory.
2.	Si no hay ningún directorio, seleccione Create your directory y proporcione la información que se le pida.
3.	Seleccione su directorio y agregue un nuevo usuario. Este nuevo usuario es una cuenta profesional. Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información para usarla en otro paso.
4.	En el portal, seleccione Configuración y, a continuación, Administradores. Seleccione Agregar y agregue el usuario nuevo como coadministrador. Así permite a la cuenta profesional administrar su suscripción de Azure.
5.	Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarla usando la nueva cuenta profesional. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.

Para obtener más información acerca de cómo usar una cuenta profesional con Microsoft Azure, consulte [Inicio de sesión como organización en Microsoft Azure](sign-up-organization.md).

Si tiene varias suscripciones y desea especificar una en concreto para que use el Almacén de claves de Azure, escriba lo siguiente para ver las suscripciones de su cuenta:

    azure account list

A continuación, para especificar la suscripción que se debe usar, escriba:

    azure account set <subscription name>

Para obtener más información sobre la configuración de la interfaz de la línea de comandos entre plataformas de Azure, vea [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure](xplat-cli-install.md).


## Cambio al Administrador de recursos de Azure

El Almacén de claves necesita el Administrador de recursos de Azure. Por lo tanto, escriba lo siguiente para cambiar al modo del Administrador de recursos de Azure:

    azure config mode arm

## Creación de un nuevo grupo de recursos

Cuando se utiliza el Administrador de recursos de Azure, todos los recursos relacionados se crean dentro de un grupo de recursos. Crearemos un nuevo grupo de recursos denominado 'ContosoResourceGroup' para este tutorial.

    azure group create 'ContosoResourceGroup' 'East Asia'

El primer parámetro es el nombre del grupo de recursos y el segundo parámetro es la ubicación. Para la ubicación, use el comando `azure location list` para identificar cómo se debe especificar una ubicación alternativa a la usada en este ejemplo. Si necesita más información, escriba: `azure help location`



## Creación de un Almacén de claves

Utilice el comando `azure keyvault create` para crear un Almacén de claves. Este script tiene tres parámetros obligatorios: el nombre del grupo de recursos, el nombre del Almacén de claves y la ubicación geográfica.

Por ejemplo, si utiliza el nombre del almacén de ContosoKeyVault, el nombre del grupo de recursos ContosoResourceGroup y la ubicación East Asia, deberá escribir:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

El resultado de este comando muestra las propiedades del Almacén de claves que acaba de crear. Las dos propiedades más importantes son:

- **Name**: en este ejemplo, el nombre es ContosoKeyVault. Utilizará este nombre para otros cmdlets del Almacén de claves.
- **vaultUri**: en este ejemplo, es https://contosokeyvault.vault.azure.net. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

Su cuenta de Azure ahora está autorizada para realizar operaciones en este Almacén de claves. Hasta el momento, nadie más lo está.


## Adición de una clave o un secreto al Almacén de claves

Si desea que el Almacén de claves de Azure cree una clave protegida mediante software, utilice el comando `azure key create` y escriba lo siguiente:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Sin embargo, si tiene una clave existente en un archivo .pem guardado como archivo local en un archivo denominado softkey.pem que desea cargar en el Almacén de claves de Azure, escriba lo siguiente para importar la clave desde el archivo .PEM, que protege la clave de software en el servicio de Almacén de claves:

    azure keyvault key import --vaultName 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Ahora puede utilizar el URI para hacer referencia a la clave que creó o cargó en el Almacén de claves de Azure. Use ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obtener siempre la versión actual y ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obtener esta versión específica.

Para agregar un secreto, que es una contraseña denominada SQLPassword con el valor Pa$$w0rd, al Almacén de claves de Azure, escriba lo siguiente:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Ahora puede hacer referencia a esta clave que agregó al Almacén de claves de Azure utilizando su URI. Use ****https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obtener siempre la versión actual y ****https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión específica.

Veamos la clave o el secreto que acaba de crear:

- Para ver la clave, escriba: `azure keyvault key list --vault-name 'ContosoKeyVault'`
- Para ver el secreto, escriba: `azure keyvault secret list --vault-name 'ContosoKeyVault'`


## Registro de una aplicación con Azure Active Directory

Este paso lo haría normalmente un programador en un equipo independiente. No es específico del Almacén de claves de Azure, pero se incluye aquí a fin de que la información ofrecida sea lo más completa posible.


>[AZURE.IMPORTANT] Para finalizar el tutorial, la cuenta, el almacén y la aplicación que vaya a registrar en este paso deben estar en el mismo directorio de Azure.

Las aplicaciones que utilizan un Almacén de claves deben autenticarse utilizando un token de Azure Active Directory. Para ello, el propietario de la aplicación debe registrarla primero en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los valores siguientes:


- Un **identificador de aplicación** (también conocido como un "identificador del cliente") y una **clave de autenticación** (también conocida como "secreto compartido"). Para obtener un token, la aplicación debe presentar estos dos valores a Azure Active Directory. La forma en que se configura la aplicación para ello depende de la aplicación. Para la aplicación de ejemplo del Almacén de claves, el propietario de la aplicación establece estos valores en el archivo app.config.



Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el Portal de Azure.
2. A la izquierda, haga clic en **Active Directory**. A continuación, elija el directorio en el que se registrará la aplicación. <br> <br> Nota: debe seleccionar el mismo directorio que contiene la suscripción de Azure con la que se creó el Almacén de claves. Si no sabe qué directorio es, haga clic en **Configuración**, identifique la suscripción con la que se creó el Almacén de claves y anote el nombre del directorio que se muestra en la última columna.

3. Haga clic en **Aplicaciones**. Si no se han agregado aplicaciones a su directorio, esta página muestra solo el vínculo **Agregar una aplicación**. Haga clic en el vínculo, o como alternativa, puede hacer clic en **Agregar** en la barra de comandos.
4.	En el asistente **Agregar aplicación**, en la página **¿Qué desea hacer?**, haga clic en **Agregar una aplicación que mi organización está desarrollando**.
5.	En la página **Proporcione información sobre su aplicación**, especifique un nombre para la aplicación y elija **Aplicación web y/o API** (valor predeterminado). Haga clic en el icono Siguiente.
6.	En la página **Propiedades de la aplicación**, especifique **URL de inicio de sesión** y **URI de Id. de aplicación ** para la aplicación web. Si la aplicación no tiene estos valores, puede inventárselos en este paso (por ejemplo, podría especificar http://test1.contoso.com para ambos). No importa si estos sitios existen; lo importante es que el URI del identificador de la aplicación de cada aplicación sea diferente en cada aplicación del directorio. El directorio utiliza esta cadena para identificar la aplicación.
7.	Haga clic en el icono Completar para guardar los cambios en el Asistente.
8.	En la página de inicio rápido, haga clic en **Configurar**.
9.	Desplácese hasta la sección **Claves**, especifique la duración y, a continuación, haga clic en **Guardar**. La página se actualiza y muestra ahora un valor de clave. Debe configurar la aplicación con este valor de clave y el valor del **identificador del cliente**. (Las instrucciones para realizar esta configuración serán específicas para la aplicación).
10.	Copie el valor del identificador del cliente en esta página. Lo utilizará en el paso siguiente para definir los permisos del almacén.




## Autorización de la aplicación para que use la clave o el secreto

Para que la aplicación pueda acceder a la clave o el secreto en el almacén, use el comando `azure keyvault set-policy`.

Por ejemplo, si el nombre del almacén es ContosoKeyVault y la aplicación que desea autorizar tiene el identificador de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed y desea que la aplicación tenga autorización para descifrar y firmar con claves en el almacén, ejecute lo siguiente:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

Si desea autorizar a esa misma aplicación para leer los secretos en el almacén, ejecute lo siguiente:

	azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## Si desea utilizar un módulo de seguridad de hardware (HSM) ##

Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Los HSM tienen la validación FIPS 140-2 de nivel 2. Si este requisito no es relevante para usted, omita esta sección y vaya al paso [Eliminación del Almacén de claves junto con las claves y secretos asociados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para crear estas claves protegidas con HSM, debe contar con una suscripción de almacén que admita claves protegidas mediante HSM.

Cuando cree el almacén de claves, agregue el parámetro 'sku':

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

A este almacén, se pueden agregar claves protegidas mediante software (tal como se ha mostrado anteriormente) y claves protegidas con HSM. Para crear una clave protegida con HSM, establezca el parámetro Destination en 'HSM':

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Puede utilizar el siguiente comando para importar una clave desde un archivo .pem a su equipo. Este comando importa la clave a HSM en el servicio de Almacén de claves:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

El comando siguiente importa un paquete BYOK ("traiga su propia clave"). Esto permite generar la clave en el HSM local y transferirla al HSM en el servicio del Almacén de claves, sin que la clave salga del límite del HSM:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Para obtener instrucciones detalladas sobre cómo generar este paquete BYOK, consulte [Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure](key-vault-hsm-protected-keys.md).


## Eliminación del Almacén de claves junto con las claves y secretos asociados

Si ya no necesita el Almacén de claves ni la clave o el secreto que contiene, puede eliminar el Almacén de claves utilizando el comando azure keyvault delete:

    azure keyvault delete --vault-name 'ContosoKeyVault'

O bien puede eliminar un grupo de recursos de Azure completo, que incluye el Almacén de claves y otros recursos incluidos en dicho grupo:

    azure group delete --name 'ContosoResourceGroup'


## Otros comandos de la interfaz de la línea de comandos entre plataformas de Azure

Estos son otros comandos que pueden resultar útiles para administrar el Almacén de claves de Azure.

Este comando ofrece una presentación tabular de todas las claves y las propiedades seleccionadas.

    azure keyvault key list --vault-name 'ContosoKeyVault'

Este comando muestra una lista completa de propiedades para la clave especificada.

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Este comando muestra una presentación tabular de todos nombres de secretos y las propiedades que se elijan.

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Ejemplo de cómo quitar una clave específica:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Ejemplo de cómo quitar un secreto específico:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## Pasos siguientes

Para conocer las referencias de programación, consulte la [Guía del desarrollador del Almacén de claves de Azure](key-vault-developers-guide.md).

<!---HONumber=AcomDC_0302_2016-->