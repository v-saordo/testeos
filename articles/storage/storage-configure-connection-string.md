<properties 
	pageTitle="Configurar una cadena de conexión a Almacenamiento de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo configurar una cadena de conexión para una cuenta de Almacenamiento de Azure Una cadena de conexión incluye la información necesaria para autenticar el acceso mediante programación a los recursos en una cuenta de almacenamiento. La cadena de conexión puede encapsular su clave de acceso de cuenta para una cuenta de la que es propietario, o puede incluir una firma de acceso compartido para tener acceso a los recursos en una cuenta sin la clave de acceso."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="tamram"/>

# Configuración de las cadenas de conexión de Almacenamiento de Azure

## Información general

Una cadena de conexión incluye la información necesaria para tener acceso a Almacenamiento de Azure de manera programática. La aplicación utiliza la cadena de conexión para proporcionar a Almacenamiento de Azure la información necesaria para autenticar el acceso.

Puede configurar una cadena de conexión de las maneras siguientes:

- Conéctese al emulador de almacenamiento de Azure mientras comprueba localmente el servicio o aplicación.
- Conéctese a una cuenta de almacenamiento en Azure mediante los extremos predeterminados para los servicios de almacenamiento o mediante los extremos específicos que haya definido.
- Obtenga acceso a recursos en una cuenta de almacenamiento a través de una firma de acceso compartido (SAS).

## Almacenamiento de la cadena de conexión

La aplicación deberá almacenar la cadena de conexión para autenticar el acceso a Almacenamiento de Azure cuando se está ejecutando. Tiene diversas opciones para almacenar la cadena de conexión:

- Para una aplicación que se ejecuta en el escritorio o en un dispositivo, puede almacenar la cadena de conexión en un archivo app.config o en otro archivo de configuración. Si está utilizando un archivo app.config, agregue la cadena de conexión a la sección **AppSettings**.
- Para una aplicación que se ejecute en un servicio en la nube de Azure, puede guardar la cadena de conexión en el [archivo de esquema de configuración de servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Agregue la cadena de conexión a la sección **ConfigurationSettings** del archivo de configuración del servicio.

Almacenar la cadena de conexión dentro de un archivo de configuración facilita la actualización de la cadena de conexión para cambiar entre el emulador de almacenamiento y una cuenta de almacenamiento de Azure en la nube. Solo necesitará modificar la cadena de conexión para apuntar a la cuenta de almacenamiento.

Puede usar la clase [Administrador de configuración de Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para obtener acceso a la cadena de conexión en tiempo de ejecución independientemente de dónde se ejecute la aplicación.

## Creación de una cadena de conexión para el emulador de almacenamiento

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte [Uso de Emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md) para obtener más información sobre el emulador de almacenamiento.

## Creación de una cadena de conexión para una cuenta de Almacenamiento de Azure

Para crear una cadena de conexión para su cuenta de Almacenamiento de Azure, use el formato de cadena de conexión que aparece a continuación. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS (recomendado), reemplace `myAccountName` por el nombre de la cuenta de almacenamiento y reemplace `myAccountKey` por la clave de acceso a la cuenta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por ejemplo, la cadena de conexión tendrá un aspecto similar a la siguiente cadena de conexión de ejemplo:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>

> [AZURE.NOTE] Almacenamiento de Azure admite HTTP y HTTPS en una cadena de conexión; sin embargo, se recomienda encarecidamente utilizar HTTPS.

## Creación de una cadena de conexión para un extremo de almacenamiento explícito

Puede especificar de manera explícita los extremos del servicio en la cadena de conexión si:

- Ha asignado un nombre de dominio personalizado para la cuenta de almacenamiento con el servicio Blob.
- Dispone de una firma de acceso compartido para el acceso a los recursos de almacenamiento en una cuenta de almacenamiento.

Para crear una cadena de conexión que especifique un extremo de Blob explícito, especifique el extremo de servicio completo para cada servicio, incluida la especificación de protocolo (HTTP o HTTPS) con el formato siguiente:

	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	[credentials]


Debe especificar al menos un extremo de servicio, pero no es necesario que los especifique todos. Por ejemplo, si va a crear una cadena de conexión para su uso con un extremo de blob personalizado, es opcional especificar los extremos de la cola y de la tabla. Tenga en cuenta que si opta por omitir los extremos de la cola y la tabla de la cadena de conexión, no podrá tener acceso a los servicios Cola y Tabla desde el código utilizando dicha cadena de conexión.

Cuando especifica de manera explícita los extremos de servicio de la cadena de conexión, tiene dos opciones para especificar `credentials` en la cadena anterior:

- Puede especificar el nombre y la clave de cuenta: `AccountName=myAccountName;AccountKey=myAccountKey`
- Puede especificar una firma de acceso compartido: `SharedAccessSignature=base64Signature`

### Especificación de un extremo de blob con un nombre de dominio personalizado

Si ha registrado un nombre de dominio personalizado para usarlo con el servicio Blob, puede interesarle configurar explícitamente el extremo el blob en la cadena de conexión. El valor final que aparece en la cadena de conexión se utiliza para construir los URI de solicitud para el servicio Blob e indica el formato de los URI que se devuelven para el código.

Por ejemplo, una cadena de conexión para un extremo de blob en un dominio personalizado podría ser similar a:

	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=<account-key>


### Especificación de un extremo de blob con una firma de acceso compartido

Puede crear una cadena de conexión con extremos explícitos para permitir el acceso a recursos de almacenamiento a través de una firma de acceso compartida. En este caso, puede especificar una firma de acceso compartido como parte de la cadena de conexión, en lugar de las credenciales del nombre y clave de la cuenta. El token de firma de acceso compartido encapsula la información sobre el recurso al que se va a tener acceso, el período de tiempo durante el cual estará disponible y los permisos que se van a conceder. Para obtener más información acerca de las firmas de acceso compartido, consulte [Delegación del acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

Para crear una cadena de conexión que incluya una firma de acceso compartido, especifique la cadena con el siguiente formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

El extremo puede ser el extremo del servicio predeterminado o un extremo personalizado. `base64Signature` corresponde a la parte de la firma de una firma de acceso compartido. La firma es un HMAC calculado sobre una cadena para firmar y una clave válidas con el algoritmo SHA256, el resultado es un Base64 codificado.

### Creación de una cadena de conexión con un sufijo de extremo

Para crear una cadena de conexión para un servicio de almacenamiento en regiones o instancias con distintos sufijos de extremo, como para Azure China o Azure Governance, utilice el siguiente formato de cadena de conexión. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS, reemplace `myAccountName` por el nombre de la cuenta de almacenamiento, reemplace `myAccountKey` por su clave de acceso a la cuenta y reemplace `mySuffix` por el sufijo de URI:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Por ejemplo, la cadena de conexión debe tener un aspecto similar a la siguiente cadena de conexión de ejemplo:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

<!---HONumber=AcomDC_0218_2016-->