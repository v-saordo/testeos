<properties 
	pageTitle="Conexión con la cuenta de Servicios multimedia mediante .NET" 
	description="En este tema muestra cómo conectarse con los Servicios multimedia mediante .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
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


# Conexión con la cuenta de Servicios multimedia con el SDK de Servicios multimedia para .NET.

> [AZURE.SELECTOR]
- [REST](media-services-rest-connect_programmatically.md)
- [.NET](media-services-dotnet-connect_programmatically.md)


En este tema se describe cómo obtener una conexión mediante programación con los Servicios multimedia de Microsoft Azure al programar con el SDK de Servicios multimedia para. NET.


## Conexión con los Servicios multimedia

Para conectarse a los Servicios multimedia mediante programación, debe haber configurado antes una cuenta de Azure, haber configurado los Servicios multimedia en esa cuenta y, por último, debe haber configurado un proyecto de Visual Studio para el desarrollo con el SDK de Servicios multimedia para. NET. Para obtener más información, consulte el programa de instalación para el desarrollo con el SDK de Servicios multimedia para .NET.

Al final del proceso de configuración de cuenta de Servicios multimedia, obtuvo los siguientes valores de conexión necesarios. Úselos para crear conexiones mediante programación a los Servicios multimedia.

- Nombre de la cuenta de Servicios multimedia.

- Clave de la cuenta de Servicios multimedia.

Para buscar estos valores, vaya al Portal de administración de Azure, seleccione la cuenta de Servicios multimedia y haga clic en el icono "**ADMINISTRAR CLAVES**" en la parte inferior de la ventana del portal. Al hacer clic en el icono junto a cada cuadro de texto, se copia el valor al Portapapeles del sistema.


## Creación de una instancia de CloudMediaContext

Para empezar a programar con Servicios multimedia, tiene que crear una instancia de **CloudMediaContext** que representa el contexto del servidor. **CloudMediaContext** incluye referencias a colecciones importantes, como trabajos, recursos, archivos, directivas de acceso y localizadores.

>[AZURE.NOTE] La clase **CloudMediaContext** no es segura para subprocesos. Debe crear un nuevo elemento CloudMediaContext por subproceso o por conjunto de operaciones.


CloudMediaContext tiene cinco sobrecargas de constructor. Se recomienda usar constructores que toman **MediaServicesCredentials** como un parámetro. Para obtener más información, consulte **Reutilización de los tokens del Servicio de control de acceso** que aparece a continuación.

En el ejemplo siguiente se usa el constructor CloudMediaContext(MediaServicesCredentials credentials) público:

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## Reutilización del Servicio de control de acceso de Azure

En esta sección se muestra cómo reutilizar los tokens del Servicio de control de acceso con el uso de constructores CloudMediaContext que toman MediaServicesCredentials como un parámetro.


[Control de acceso de Azure Active Directory](https://msdn.microsoft.com/library/hh147631.aspx) (también conocido como Servicio de control de acceso o ACS) es un servicio en la nube que proporciona una manera sencilla de autenticar y autorizar a los usuarios para que tengan acceso a sus aplicaciones web. Los Servicios multimedia de Microsoft Azure controlan el acceso a sus servicios aunque el protocolo de OAuth necesita un token de ACS. Los Servicios multimedia reciben los tokens de ACS desde un servidor de autorización.

Al desarrollar con el SDK de Servicios multimedia, puede optar por no tratar los tokens porque el código del SDK los administra. Sin embargo, permitir que el SDK administre por completo los tokens da lugar a solicitudes de token innecesarias. La solicitud de tokens lleva tiempo y consume los recursos del servidor y del cliente. Además, el servidor de ACS limita las solicitudes si la velocidad es demasiado alta. El límite es de 30 solicitudes por segundo. Consulte [Limitaciones del servicio ACS](https://msdn.microsoft.com/library/gg185909.aspx) para obtener más detalles.

A partir de la versión 3.0.0.0 del SDK de Servicios multimedia, puede reutilizar los tokens de ACS. Los constructores **CloudMediaContext** que toman **MediaServicesCredentials** como un parámetro permiten el uso compartido de los tokens de ACS entre varios contextos. La clase MediaServicesCredentials encapsula las credenciales de Servicios multimedia. Si un token de ACS está disponible y se conoce la hora de expiración, puede crear una nueva instancia de MediaServicesCredentials con el token y pasarla al constructor de CloudMediaContext. Tenga en cuenta que el SDK de Servicios multimedia actualiza automáticamente los tokens cuando estos expiran. Hay dos formas de reutilizar los tokens de ACS, tal como se muestra en los ejemplos siguientes.

- Puede almacenar en caché el objeto **MediaServicesCredentials** en memoria (por ejemplo, en una variable de clase estática). A continuación, pase el objeto almacenado en caché al constructor CloudMediaContext. El objeto MediaServicesCredentials contiene un token de ACS que se puede volver a utilizar si aún es válido. Si el token no es válido, se actualizará mediante el SDK de Servicios multimedia con las credenciales proporcionadas al constructor MediaServicesCredentials.

	Tenga en cuenta que el objeto **MediaServicesCredentials** obtiene un token válido después de llamar a RefreshToken. **CloudMediaContext** llama al método **RefreshToken** en el constructor. Si planea guardar los valores del token en un almacenamiento externo, asegúrese de comprobar si el valor de TokenExpiration es válido antes de guardar los datos del token. Si no es válido, llame a RefreshToken antes del almacenamiento en caché.

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- También puede almacenar en caché la cadena AccessToken y los valores de TokenExpiration. Los valores se pueden usar más adelante para crear un nuevo objeto MediaServicesCredentials con los datos del token almacenados en caché. Esto es especialmente útil para escenarios en que el token se puede compartir de forma segura entre varios procesos o equipos.

	Los siguientes fragmentos de código llaman a los métodos SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage y UpdateTokenDataInExternalStorageIfNeeded, que no se definen en este ejemplo. Puede definir estos métodos para almacenar, recuperar y actualizar datos del token en un almacenamiento externo.

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	Use los valores de símbolo (token) guardados para crear MediaServicesCredentials.


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	Actualice la copia del token en caso de que el SDK de Servicios multimedia actualizara el token.
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- Si tiene varias cuentas de Servicios multimedia (por ejemplo, para fines de uso compartido de carga o distribución geográfica), puede almacenar en caché objetos MediaServicesCredentials mediante la colección de System.Collections.Concurrent.ConcurrentDictionary (la colección de ConcurrentDictionary representa una colección segura para subprocesos de pares clave/valor a la que se pueden tener acceso varios subprocesos de forma simultánea). A continuación, puede usar el método GetOrAdd para obtener las credenciales en caché.

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## Conexión con una cuenta de Servicios multimedia ubicada en la región Norte de China

Si su cuenta se encuentra en la región Norte de China, use el constructor siguiente:

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Por ejemplo:


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## Almacenar valores de conexión en la configuración

Es muy recomendable almacenar los valores de conexión, especialmente aquellos valores confidenciales como el nombre de cuenta y la contraseña, en la configuración. Además, es una práctica recomendada cifrar los datos de configuración confidenciales. Puede cifrar el archivo de configuración completo mediante el sistema de cifrado de archivos (EFS) de Windows. Para habilitar EFS en un archivo, haga clic en el archivo, seleccione **Propiedades** y habilite el cifrado en la ficha de configuración **Opciones avanzadas**. O bien, puede crear una solución personalizada para cifrar las partes seleccionadas de un archivo de configuración mediante la configuración protegida. Consulte [Cifrar información de configuración mediante una configuración protegida](https://msdn.microsoft.com/library/53tyfkaw.aspx).

El siguiente archivo App.config contiene los valores de conexión necesarios. Los valores del elemento <appSettings> son los valores necesarios que obtuvo en el proceso de configuración de la cuenta de Servicios multimedia.

	<configuration>
	  <appSettings>
	    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
	    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
	  </appSettings>
	</configuration>


Para recuperar los valores de conexión de la configuración, use la clase **ConfigurationManager** y luego asigne los valores a los campos en el código:
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->