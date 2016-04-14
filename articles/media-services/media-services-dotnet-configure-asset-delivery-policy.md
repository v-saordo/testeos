<properties 
	pageTitle="Configuración de directivas de entrega de activos con .NET SDK" 
	description="En este tema se muestra cómo configurar distintas directivas de entrega de activos con .NET SDK de Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,Mingfeiy" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>

#Configuración de directivas de entrega de activos con .NET SDK
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##Información general

Si tiene pensado entregar activos cifrados, uno de los pasos del flujo de trabajo de entrega de contenido de Servicios multimedia consiste en configurar directivas de entrega para los activos. La directiva de entrega de recursos indica a los Servicios multimedia cómo desea usted que se entregue el recurso: en qué protocolo de streaming se debe empaquetar de forma dinámica el recurso (por ejemplo, MPEG DASH, HLS, Smooth Streaming o todos) o si desea o no cifrar de forma dinámica el recurso y de qué manera (cifrado de sobre o común).

En este tema se explica por qué y cómo crear y configurar directivas de entrega de recursos.

>[AZURE.NOTE]Para poder usar el empaquetado dinámico y el cifrado dinámico, debe asegurarse de tener al menos una unidad de escalación (denominada también unidad de streaming). Para obtener más información, consulte [Escalación de un servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints).
>
>Además, el recurso debe contener un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable.

Puede aplicar diferentes directivas al mismo recurso. Por ejemplo, puede aplicar cifrado PlayReady a Smooth Streaming y cifrado de sobre AES a MPEG DASH y HLS. Se bloqueará la transmisión para todos los protocolos que no estén definidos en una directiva de entrega (por ejemplo, si agrega una sola directiva que solo especifica HLS como el protocolo). La excepción a esta regla se produce en el caso de que no haya definido ninguna directiva de entrega de recursos. En tal caso, todos los protocolos estarán habilitados sin cifrar.

Tenga en cuenta que si desea entregar un recurso de almacenamiento cifrado, debe configurar la directiva de entrega de recursos. Antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada. Por ejemplo, para entregar el recurso cifrado con la clave de cifrado de sobre de Estándar de cifrado avanzado (AES), establezca el tipo de directiva en **DynamicEnvelopeEncryption**. Para quitar el cifrado de almacenamiento y transmitir el recurso sin cifrar, establezca el tipo de directiva en **NoDynamicEncryption**. A continuación se muestran ejemplos de configuración de estos tipos de directiva.

Según como configure la directiva de entrega de recursos podrá empaquetar de forma dinámica, cifrar de forma dinámica y transmitir los protocolos de streaming siguientes: secuencias Smooth Streaming, HLS, MPEG DASH y HDS.

En la lista siguiente se muestran los formatos usados para transmitir Smooth Streaming, HLS, DASH y HDS.

Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Para obtener instrucciones sobre cómo publicar un recurso y generar una dirección URL de streaming, vea [Creación de una dirección URL de streaming](media-services-deliver-streaming-content.md).

##Consideraciones

- No puede eliminar una entidad AssetDeliveryPolicy asociada a un activo mientras hay un localizador OnDemand (transmisión) para ese activo. Se recomienda quitar la directiva del activo antes de eliminar la directiva.
- No se puede crear un localizador de transmisión en un activo cifrado de almacenamiento cuando no se establece ninguna directiva de entrega de activos. Si el activo no está cifrado para almacenamiento, el sistema le permitirá crear un localizador y transmitir el activo sin cifrar, sin una directiva de entrega de activos.
- Puede tener varias directivas de entrega de activos asociadas a un único activo, pero solo se puede especificar una forma de controlar un AssetDeliveryProtocol determinado, es decir, si intenta vincular dos directivas de entrega que especifican el protocolo AssetDeliveryProtocol.SmoothStreaming que producirá un error porque el sistema no sabe cuál quiere que aplique cuando un cliente realiza una solicitud de Smooth Streaming.  
- Si tiene un activo con un localizador de transmisión existente, no puede vincular una nueva directiva al activo (puede desvincular una directiva existente del activo o actualizar una directiva de entrega asociada al activo). Primero debe quitar el localizador de transmisión, ajustar las directivas y volver a crear el localizador de transmisión. Puede usar el mismo locatorId al volver a crear el localizador de transmisión, pero debe asegurarse de que no causará problemas para los clientes ya que se puede almacenar en caché el contenido por el origen o una red CDN de nivel inferior.  


##Directiva de entrega de recursos sin cifrar 

El método **ConfigureClearAssetDeliveryPolicy** siguiente especifica que no se aplique el cifrado dinámico y se entregue la secuencia en cualquiera de los siguientes protocolos: MPEG DASH, HLS y Smooth Streaming. Querrá aplicar esta directiva a los activos cifrados de almacenamiento.
  
Para obtener información sobre los valores que puede especificar al crear una entidad AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types).

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

##Directiva de entrega de recursos DynamicCommonEncryption 


El método siguiente **CreateAssetDeliveryPolicy** crea la directiva **AssetDeliveryPolicy** que se configura para aplicar el cifrado común dinámico (**DynamicCommonEncryption**) a un protocolo de Smooth Streaming (no se aplicará la transmisión en otros protocolos). El método toma dos parámetros: **Asset** (el recurso al que desea aplicar la directiva de entrega) y **IContentKey** (la clave de contenido de tipo **CommonEncryption**. Para obtener más información, consulte: [Creación de una clave de contenido](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obtener información sobre los valores que puede especificar al crear una entidad AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types).


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Los Servicios multimedia de Azure también permiten agregar el cifrado de Widevine. En el ejemplo siguiente se muestra cómo se agrega tanto PlayReady como Widevine a la directiva de entrega de recursos.


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        Uri widevineURl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineURl.ToString()},

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Al cifrar con Widevine, solo podría entregar con DASH. Asegúrese de especificar DASH en el protocolo de entrega de activos.


##Directiva de entrega de recursos DynamicEnvelopeEncryption 

El método siguiente **CreateAssetDeliveryPolicy** crea el **AssetDeliveryPolicy** que se configura para aplicar el cifrado de sobre dinámico (**DynamicEnvelopeEncryption**) para protocolos HLS y DASH (no se aplicará la transmisión en otros protocolos). El método toma dos parámetros: **Asset** (el recurso al que desea aplicar la directiva de entrega) y **IContentKey** (la clave de contenido de tipo **EnvelopeEncryption**. Para obtener más información, consulte: [Creación de una clave de contenido](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Para obtener información sobre los valores que puede especificar al crear una entidad AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types).

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Tipos usados al definir AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->