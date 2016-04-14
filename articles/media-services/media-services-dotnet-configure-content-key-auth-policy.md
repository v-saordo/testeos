<properties 
	pageTitle="Configuración de la directiva de autorización de claves mediante el SDK de Servicios multimedia para .NET" 
	description="Aprenda a configurar una directiva de autorización para una clave de contenido mediante .NET SDK de Servicios multimedia." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,Mingfeiy" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>



#Cifrado dinámico: configuración de la directiva de autorización de claves de contenido 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##Información general

Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y DRM de PlayReady. Los Servicios multimedia también proporcionan un servicio para entregar claves y licencias de PlayReady a los clientes autorizados.

Actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No puede cifrar el formato de streaming HDS ni descargas progresivas.

Si desea que Servicios multimedia cifre un recurso, debe asociar una clave de cifrado (**CommonEncryption** o **EnvelopeEncryption**) con el recurso (tal como se describe [aquí](media-services-dotnet-create-contentkey.md)) y configurar directivas de autorización para la clave (tal como se describe en este artículo).

Cuando un reproductor solicita una secuencia, los Servicios multimedia usan la clave especificada para cifrar de forma dinámica el contenido mediante AES o el cifrado de PlayReady. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. La directiva de autorización de claves de acceso podría tener una o más restricciones de autorización: **abrir** o restricción de **token**. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato de **tokens web simples** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) y **tokens web JSON** (JWT).

Los Servicios multimedia no proporcionan Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. El STS debe configurarse para crear un token firmado con las notificaciones especificadas de clave y el número que especificó en la configuración de restricción de token (como se describe en este artículo). El servicio de entrega de claves de los Servicios multimedia devolverá la clave de cifrado al cliente si el token es válido y las notificaciones del token coinciden con las configuradas para la clave de contenido.

Para obtener más información, consulte

[Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integración de la aplicación OWIN basada en MVC de Servicios multimedia de Azure con Azure Active Directory y restricción de la entrega de claves de contenido basada en notificaciones de JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Uso de ACS de Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

###Se aplican algunas consideraciones:

- Para poder usar el empaquetado dinámico y el cifrado dinámico, debe asegurarse de tener al menos una unidad reservada de streaming. Para obtener más información, consulte [Escalación de un servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints). 
- El recurso debe contener un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable. Para obtener más información, consulte [Codificación de un recurso](media-services-encode-asset.md).  
- Cargue y codifique sus recursos con la opción **AssetCreationOptions.StorageEncrypted**.
- Si planea tener varias claves de contenido que requieran la misma configuración de directiva, se recomienda encarecidamente crear una sola directiva de autorización y volverla a utilizar con varias claves de contenido.
- El servicio de entrega de claves almacena en caché ContentKeyAuthorizationPolicy y sus objetos relacionados (opciones y restricciones de directiva) durante 15 minutos. Si crea una entidad ContentKeyAuthorizationPolicy y especifica el uso de una restricción "Token", pruébela y, a continuación, actualice la directiva a la restricción "Open"; la directiva tardará aproximadamente 15 minutos antes de cambiar a la versión "Open" de la misma.
- Si agrega o actualiza la directiva de entrega de recursos, debe eliminar un localizador existente (si hay) y crear uno nuevo.


##Cifrado dinámico AES-128 

###Restricción open

La restricción open significa que el sistema entregará la clave a cualquier persona que realice una solicitud de clave. Esta restricción puede ser útil para realizar pruebas.

En el ejemplo siguiente se crea una directiva de autorización abierta y se agrega a la clave de contenido.
	
	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy             
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("Open Authorization Policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	        new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	        new ContentKeyAuthorizationPolicyRestriction
	        {
	            Name = "HLS Open Authorization Policy",
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	            Requirements = null // no requirements needed for HLS
	        };
	
	    restrictions.Add(restriction);
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	        "policy", 
	        ContentKeyDeliveryType.BaselineHttp, 
	        restrictions, 
	        "");
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	}


###Restricción de token

En esta sección se describe cómo crear una directiva de autorización de claves de contenido y asociarla a la clave de contenido. La directiva de autorización describe los requisitos de autorización que se deben cumplir para determinar si el usuario está autorizado para recibir la clave (por ejemplo, si la lista de "claves de comprobación" contiene la clave con la que se firmó el token).

Para configurar la opción de restricción de token, debe usar un archivo XML para describir los requisitos de autorización del token. El archivo XML de configuración de restricción de token debe cumplir el siguiente esquema XML.

####<a id="schema"></a>Esquema de restricción de token
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

Al configurar la directiva de restricción de **token**, debe especificar los parámetros de **clave de comprobación** principal, **emisor** y **público**. La **clave de comprobación principal** contiene la clave con la que se firmó el token y el **emisor** es el servicio de tokens seguros que emite el token. El **público** (a veces denominado **ámbito**) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

Al usar el **SDK de Servicios multimedia para .NET**, puede utilizar la clase **TokenRestrictionTemplate** para generar el token de restricción. En el ejemplo siguiente se crea una directiva de autorización con una restricción de token. En este ejemplo, el cliente tendría que presentar un token que contenga: una clave de firma (VerificationKey), un emisor de tokens y las notificaciones necesarias.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	            new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	            new ContentKeyAuthorizationPolicyRestriction
	            {
	                Name = "Token Authorization Policy",
	                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                Requirements = tokenTemplateString
	            };
	
	    restrictions.Add(restriction);
	
	    //You could have multiple options 
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	            "Token option for HLS",
	            ContentKeyDeliveryType.BaselineHttp,
	            restrictions,
	            null  // no key delivery data is needed for HLS
	            );
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

####<a id="test"></a>Token de prueba

Para obtener un token de prueba basado en la restricción de token que se usó para la directiva de autorización de claves, haga lo siguiente.
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


##Cifrado dinámico PlayReady. 

Los Servicios multimedia le permiten configurar los derechos y las restricciones que desee para que el tiempo de ejecución de PlayReady DRM las aplique cuando un usuario intente reproducir contenido protegido.

Al proteger su contenido con PlayReady, una de las cosas que debe especificar en la directiva de autorización es una cadena XML que defina la [plantilla de licencias PlayReady](media-services-playready-license-template-overview.md). En el SDK de Servicios multimedia para. NET, las clases **PlayReadyLicenseResponseTemplate** y **PlayReadyLicenseTemplate** le ayudarán a definir la plantilla de licencia de PlayReady.

###Restricción open
	
La restricción open significa que el sistema entregará la clave a cualquier persona que realice una solicitud de clave. Esta restricción puede ser útil para realizar pruebas.

En el ejemplo siguiente se crea una directiva de autorización abierta y se agrega a la clave de contenido.

	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy          
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Open", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	            Requirements = null
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	
	
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key.
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	}

###Restricción de token

Para configurar la opción de restricción de token, debe usar un archivo XML para describir los requisitos de autorización del token. El archivo XML de configuración de restricción de token debe cumplir el esquema XML que se muestra en [esta](#schema) sección.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Token Authorization Policy", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	            Requirements = tokenTemplateString, 
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	            
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
	
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	} 
	
    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
       
        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to playback the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


Para obtener un token de prueba basado en la restricción de token que se usó para la directiva de autorización de claves, consulte [esta](#test) sección.

##<a id="types"></a>Tipos usados al definir ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Pasos siguientes
Ahora que ha configurado la directiva de autorización de la clave de contenido, consulte el tema [Configuración de la directiva de entrega de recursos](media-services-dotnet-configure-asset-delivery-policy.md).
 

<!---HONumber=AcomDC_0211_2016-->