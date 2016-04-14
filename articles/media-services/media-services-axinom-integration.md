<properties 
	pageTitle="Uso de Axinom para entregar licencias de Widevine a Servicios multimedia de Azure" 
	description="En este artículo se describe cómo puede usar Servicios multimedia de Azure (AMS) para entregar una secuencia que se cifra dinámicamente por AMS con DRM tanto de PlayReady como Widevine. La licencia de PlayReady procede del servidor de licencias PlayReady de Servicios multimedia y la licencia de Widevine se entrega al servidor de licencias de Axinom." 
	services="media-services" 
	documentationCenter="" 
	authors="willzhan,Mingfeiy,rajputam,Juliako" 
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

#Uso de Axinom para entregar licencias de Widevine a Servicios multimedia de Azure  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##Información general

Servicios multimedia de Azure (AMS) agrega protección dinámica de Google Widevine (consulte el [blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obtener más información). Además, el Reproductor multimedia de Azure (AMP) también agrega compatibilidad con Widevine (consulte el documento sobre [AMP](http://amp.azure.net/libs/amp/latest/docs/) para obtener más información). Esto es un logro fundamental en el streaming de contenido de DASH protegido por CENC con Multi-native-DRM (PlayReady y Widevine) en los exploradores modernos equipados con MSE y EME.

A partir del SDK de Servicios multimedia para .NET versión 3.5.2, Servicios multimedia permite configurar la plantilla de licencia Widevine y obtener licencias de Widevine. También puede usar los siguientes asociados de AMS para ayudarle a entregar licencias de Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Este artículo describe cómo integrar y probar el servidor de licencias de Widevine administrado por Axinom. En concreto, trata:

- Configuración de cifrado común dinámico con Multi-DRM (PlayReady y Widevine) con las direcciones URL de adquisición de licencias correspondientes;
- Generación de un token JWT para cumplir los requisitos del servidor de licencias;
- Desarrollo de la aplicación Reproductor multimedia de Azure que controla la adquisición de licencias con la autenticación mediante token JWT;

El sistema completo y el flujo de la clave de contenido, el identificador de clave, inicialización de clave, token JTW y sus notificaciones pueden describirse mejor mediante el siguiente diagrama.

![DASH y CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##Protección de contenido

Para configurar la protección dinámica y la directiva de entrega de claves, consulte el blog de Mingfei: [How to configure Widevine packaging with Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services) (Cómo configurar el empaquetado de Widevine con Servicios multimedia de Azure).

Puede configurar la protección dinámica de CENC con Multi-DRM para streaming de DASH y tener los dos elementos siguientes:

1. Protección de PlayReady para MS Edge y IE11, que puede tener restricciones de autorización mediante token. La directiva con restricción de token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS) como Azure Active Directory.
1. Protección de Widevine para Chrome, que puede requerir autenticación mediante token con el token emitido por otro STS. 

Vea la sección [Generación de token JWT](media-services-axinom-integration.md#jwt-token-generation) para saber por qué Azure Active Directory no se puede usar como STS para el servidor de licencias Widevine de Axinom.

###Consideraciones

1. Debe usar la inicialización de clave especificada de Axinom (8888000000000000000000000000000000000000) y el identificador de clave generado o seleccionado para generar la clave de contenido y configurar el servicio de entrega de claves. El servidor de licencias de Axinom emitirá todas las licencias que contienen claves de contenido basadas en la misma inicialización de clave, que es válida para las pruebas y la producción.
1. Dirección URL de adquisición de licencias de Widevine para pruebas: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). Se permiten tanto HTTP como HTTS.

##Preparación del Reproductor multimedia de Azure

AMP v1.4.0 es compatible con la reproducción del contenido de AMS que está empaquetado dinámicamente con PlayReady y DRM de Widevine. Si el servidor de licencias de Widevine no requiere autenticación mediante token, no hay nada más que deba hacer para probar un contenido de DASH protegido por Widevine. El equipo de AMP proporciona un [ejemplo](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html) sencillo, en el que puede ver su funcionamiento en Edge e IE11 con PlayReady y Chrome con Widevine. El servidor de licencias de Widevine proporcionado por Axinom requiere autenticación mediante token JWT. El token JWT debe enviarse con la solicitud de licencia mediante un encabezado HTTP “X-AxDRM-Message”. Para ello, debe agregar el siguiente código de Javascript en la página web que hospeda AMP antes de establecer el origen:

	<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

El resto del código de AMP es una API estándar de AMP como en el documento de AMP que se encuentra [aquí](http://amp.azure.net/libs/amp/latest/docs/).

Tenga en cuenta que el código de Javascript anterior para configurar un encabezado de autorización personalizado sigue siendo un enfoque a corto plazo antes de que se publique el enfoque oficial a largo plazo en AMP.

##Generación de token JWT

El servidor de licencias de Widevine de Axinom para pruebas requiere autenticación mediante token JWT. Además, una de las notificaciones en el token JWT es de un tipo de objeto complejo en lugar del tipo de datos primitivo.

Lamentablemente, Azure AD solo puede emitir tokens JWT con tipos primitivos. De forma similar, la API de .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler y JwtPayload) solo permite especificar el tipo de objeto complejo como notificaciones. Sin embargo, las notificaciones todavía se serializan como cadena. Por lo tanto, no podemos usar ninguno de los dos para generar el token JWT para la solicitud de licencias de Widevine.


El [paquete de Nuget JWT](https://www.nuget.org/packages/JWT) de John Sheehan se adapta a nuestras necesidades, por lo que vamos a usar este paquete de Nuget.

A continuación se muestra el código para generar el token JWT con las notificaciones necesarias, en función de lo que requiere el servidor de licencias de Widevine de Axinom para las pruebas:

	
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.IdentityModel.Tokens;
	using System.IdentityModel.Protocols.WSTrust;
	using System.Security.Claims;
	
	namespace OpenIdConnectWeb.Utils
	{
	    public class JwtUtils
	    {
	        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
	        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
	        {
	            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
	
	            var payload = new Dictionary<string, object>()
	                         {
	                             { "version", 1 },
	                             { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
	                             { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
	                         };
	
	            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
	
	            return token;
	        }
	
	        //convert hex string to byte[]
	        public static byte[] ConvertHexStringToByteArray(string hexString)
	        {
	            if (hexString.Length % 2 != 0)
	            {
	                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
	            }
	
	            byte[] HexAsBytes = new byte[hexString.Length / 2];
	            for (int index = 0; index < HexAsBytes.Length; index++)
	            {
	                string byteValue = hexString.Substring(index * 2, 2);
	                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
	            }
	
	            return HexAsBytes;
	        }
	
	    }  
	
	}  

Servidor de licencias de Widevine de Axinom

	<add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
	<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
	<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
	<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###Consideraciones

1.	Aunque el servicio de entrega de licencias de AMS PlayReady requiere un “Bearer=” que preceda a un token de autenticación, el servidor de licencias de Widevine de Axinom no lo usa.
2.	La clave de comunicación de Axinom se usa como clave de firma. Tenga en cuenta que la clave es una cadena hexadecimal, sin embargo, debe tratarse como una serie de bytes y no como una cadena cuando se codifica. Esto se logra mediante el método ConvertHexStringToByteArray.

##Recuperación del identificador de clave

Quizá ha podido observar que en el código para generar un token JWT, se requiere el identificador de clave. Como el token JWT debe estar listo antes de cargar el reproductor de AMP, es necesario recuperar el identificador de clave para generar el token JWT.

Por supuesto, hay varias maneras de obtener de el identificador de clave. Por ejemplo, se puede almacenar el identificador de clave junto con los metadatos de contenido en una base de datos. O bien recuperar el identificador de clave del archivo DASH MPD (Descripción de presentación de medios). El código siguiente es para esta última opción.

	//get key_id from DASH MPD
	public static string GetKeyID(string dashUrl)
	{
	    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
	    {
	        dashUrl += "(format=mpd-time-csf)";
	    }
	
	    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
	    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
	    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
	    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
	    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
	    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
	    objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
	    objXmlNamespaceManager.PushScope();
	
	    XPathNodeIterator objXPathNodeIterator;
	    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
	
	    string key_id = string.Empty;
	    if (objXPathNodeIterator.MoveNext())
	    {
	        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
	    }
	
	    return key_id;
	}

##Resumen

Con la reciente incorporación de la compatibilidad de Widevine tanto en la protección de contenido de Servicios multimedia de Azure y en el Reproductor multimedia de Azure, podemos implementar el streaming de DASH + Multi-native-DRM (PlayReady + Widevine) con el servicio de licencias de PlayReady en AMS y el servidor de licencias de Widevine de Axinom para los siguientes exploradores actuales:

- Chrome
- Microsoft Edge en Windows 10
- IE 11 en Windows 8.1 y Windows 10
- Tanto Firefox (escritorio) como Safari en Mac (no iOS) se admiten también a través de Silverlight y la misma dirección URL con el Reproductor multimedia de Azure

Los parámetros siguientes son necesarios en la minisolución que saca partido del servidor de licencias de Widevine de Axinom. Excepto el identificador de clave, Axinom proporciona el resto de los parámetros en función de la configuración del servidor de Widevine.


Parámetro|Cómo se utiliza
---|---
Id. de clave de comunicación|Se debe incluir como valor de la notificación "com\_key\_id" en el token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) sección).
Clave de comunicación|Se debe usar como clave de firma de token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) sección).
Inicialización de clave|Se debe usar para generar la clave de contenido con cualquier id. de clave de contenido determinado (vea [esta](media-services-axinom-integration.md#content-protection) sección).
Dirección URL de adquisición de licencias de Widevine|Se debe usar en la configuración de la directiva de entrega de activos para el streaming de DASH (vea [esta](media-services-axinom-integration.md#content-protection) sección).
Id. de clave de contenido|Se debe incluir como parte del valor de notificación del mensaje de derechos de token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) sección). 


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###Agradecimientos 

Nos gustaría mencionar a las siguientes personas que han contribuido a crear este documento: Kristjan Jõgi de Axinom, Mingfei Yan y Amit Rajput.

<!---HONumber=AcomDC_0211_2016-->