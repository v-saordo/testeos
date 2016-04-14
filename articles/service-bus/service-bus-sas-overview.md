<properties
   pageTitle="Información general sobre las firmas de acceso compartido | Microsoft Azure"
   description="¿Qué son las firmas acceso compartido, cómo funcionan y cómo usarlas desde Node, PHP y C#?"
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="darosa"/>

# Las firmas de acceso compartido

*Firmas de acceso compartido* (SAS) es el mecanismo de seguridad principal para Bus de servicio, incluidos centros de eventos, mensajería asíncrona (colas y temas) y mensajería retransmitida. Este artículo se tratan las firmas acceso compartido, cómo funcionan y cómo usarlas de forma independiente de plataforma.

## Información general de SAS

Firmas de acceso compartido son un mecanismo de autenticación basado en URI y valores hash seguros SHA-256. SAS es un mecanismo muy eficaz que se usa por todos los servicios del Bus de servicio. En el uso real, SAS tiene dos componentes: una *directiva de acceso compartido* y una *firma de acceso compartido* (a menudo denominada *token*).

Puede encontrar información más detallada sobre las firmas de acceso compartido con Bus de servicio en [Autenticación de firmas de acceso compartido con Bus de servicio](service-bus-shared-access-signature-authentication.md).

## Directiva de acceso compartido

Una cuestión importante de entender acerca de SAS es que todo empieza con una directiva. Para cada directiva, decida tres tipos de información: **nombre**, **ámbito** y **permisos**. El **nombre** es solo eso; un nombre único dentro de ese ámbito. El ámbito es bastante sencillo: es el URI del recurso en cuestión. Para un espacio de nombres del Bus de servicio, el ámbito es el nombre de dominio completo (FQDN), como **`https://<yournamespace>.servicebus.windows.net/`**.

Los permisos disponibles para una directiva son en gran parte explicativos:

  + Los métodos Send
  + Escuchar
  + Manage

Después de crear la directiva, se le asigna una *clave principal*y una *clave secundaria*. Son claves de alta seguridad criptográfica. No las pierda; siempre estarán disponibles en el [Portal de Azure clásico][]. Puede usar cualquiera de las claves generadas y regenerarlas en cualquier momento. Sin embargo, si regenera o cambia la clave principal en la directiva, se invalidará cualquier firma de acceso compartido creada a partir de ella.

Cuando se crea un espacio de nombres del Bus de servicio, se crea automáticamente una directiva para todo el espacio de nombres denominado **RootManageSharedAccessKey** y esta directiva tiene todos los permisos. No inicia sesión como **raíz**; por tanto, no use esta directiva a menos que exista realmente una buena razón. Puede crear directivas adicionales en la pestaña **Configurar** para el espacio de nombres en el portal. Es importante tener en cuenta que un nivel de árbol único en el Bus de servicio (espacio de nombres, cola, concentrador de eventos, etc.) solo puede tener hasta 12 directivas asociadas a él.

## Firma de acceso compartido (token)

La propia directiva no es el token de acceso para el Bus de servicio. Es el objeto desde el que se genera el token de acceso, mediante la clave principal o la clave secundaria. El token se genera elaborando cuidadosamente una cadena con el siguiente formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Donde `signature-string` es el hash SHA-256 del ámbito del token (**ámbito** como se describe en la sección anterior) con un CRLF anexado y una hora de expiración (en segundos desde la época: `00:00:00 UTC` el 1 de enero de 1970).

El hash es similar al siguiente pseudocódigo y devuelve 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Los valores que no son de hash se encuentran en la cadena **SharedAccessSignature** para que el destinatario puede calcular el hash con los mismos parámetros, para asegurarse de que devuelve el mismo resultado. El URI especifica el ámbito y el nombre de la clave identifica la directiva que se usará para calcular el hash. Esto es importante desde una perspectiva de seguridad. Si la firma no coincide con la que el destinatario calcula (Bus de servicio), se denegará el acceso. En este punto podemos estar seguros de que el remitente ha tenido acceso a la clave y que se le debería haber concedido los derechos especificados en la directiva.

## Generación de una firma desde una directiva

¿Cómo hace esto realmente en el código? Echemos un vistazo a varias de estas.

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## Uso de la firma de acceso compartido (en el nivel HTTP)
 
Ahora que sabe cómo crear firmas de acceso compartido para cualquier entidad del Bus de servicio, estará listo para llevar a cabo una solicitud HTTP POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
Recuerde que esto funciona para todo. Puede crear SAS para una cola, un tema, una suscripción, un concentrador de eventos o una retransmisión. Si usa una identidad por publicador para centros de eventos, sencillamente anexa `/publishers/< publisherid>`.

Si le da un token de SAS a un remitente o cliente, no tiene la clave directamente y no pueden invertir el hash para obtenerla. Como tal, tiene control sobre a lo que pueden tener acceso y durante cuánto tiempo. Algo importante de recordar es que si cambia la clave principal de la directiva, se invalidará cualquier firma de acceso compartido creada a partir de ella.

## Uso de la firma de acceso compartido (en el nivel AMQP)

En la sección anterior, vimos cómo usar el token SAS con una solicitud HTTP POST para enviar datos al Bus de servicio. Como sabe, puede obtener acceso al Bus de servicio mediante el protocolo AMQP (Protocolo de cola de mensajes avanzada) que es el protocolo principal y preferido que usar por motivos de rendimiento en muchos escenarios. El uso de tokens SAS con AMQP se describe en el siguiente documento [Seguridad basada en notificaciones de AMQP, versión 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está en fase de borrador desde 2013 pero que es compatible con Azure actualmente.

Antes de comenzar a enviar datos al Bus de servicio, el publicador tiene que enviar el token de SAS dentro de un mensaje de AMQP a un nodo de AMQP bien definido con el nombre **"$cbs"** (puede verlo como una cola especial utilizada por el servicio para adquirir y validar todos los tokens de SAS). El publicador tiene que especificar el campo **"ReplyTo"** dentro del mensaje de AMQP; este es el nodo en el que el servicio contestará al publicador con el resultado de la validación del token (un patrón sencillo de solicitud/respuesta entre el publicador y el servicio). Este nodo de respuesta se crea "sobre la marcha" en lo que respecta a "creación dinámica de nodo remoto" como describe la especificación de AMQP 1.0. Después de comprobar que el token SAS es válido, el publicador puede avanzar y comenzar a enviar datos al servicio.

Los siguientes pasos le mostrarán cómo enviar el token SAS con el protocolo AMQP mediante la biblioteca [AMQP.Net Lite](http://amqpnetlite.codeplex.com) útil si no puede utilizar el SDK de Bus de servicio oficial (por ejemplo, en WinRT, .Net Compact Framework, .Net Micro Framework y Mono) que se desarrolla en C&#35; Por supuesto, esta biblioteca es útil para comprender cómo funciona la seguridad basada en notificaciones en el nivel AMQP como pudo observar en el nivel HTTP (con una solicitud HTTP POST y el token SAS enviado dentro del encabezado "Autorización"). Sin embargo, no se preocupe. Si no necesita un conocimiento tan profundo sobre AMQP, puede usar el SDK del Bus de servicio con aplicaciones de .Net Framework que lo hará por usted o la biblioteca [Azure SB Lite](http://azuresblite.codeplex.com) para todas las demás plataformas (consulte a continuación).

### C&#35;

```
/// <summary>
/// Send Claim Based Security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

El método *PutCbsToken()* anterior recibe la *conexión* (instancia de clase de conexión de AMQP como proporciona la biblioteca .Net Lite de AMQP) que representa la conexión TCP al servicio y el parámetro *sasToken* que es el token de SAS que enviar. Nota: es importante que la conexión se cree con el **mecanismo de autenticación SASL establecido en EXTERNO** (y no el valor SIN FORMATO predeterminado con el nombre de usuario y la contraseña usados cuando no tiene que enviar el token de SAS).

A continuación, el publicador crea dos vínculos de AMQP para el envío del token de SAS y la recepción de la respuesta (resultado de validación del token) del servicio.

El mensaje de AMQP es un poco complejo porque dispone de una serie de propiedades y de más información que un mensaje simple. El token de SAS se coloca como el cuerpo del mensaje (mediante su constructor). La propiedad **"ReplyTo"** se establece en el nombre del nodo para recibir el resultado de la validación en el vínculo del receptor (puede cambiar su nombre como desee y el servicio lo creará dinámicamente). El servicio usa las últimas tres propiedades personalizadas/de aplicación para comprender qué variante de operación tiene que ejecutar. Como describe la especificación del borrador de CBS, deben ser el **nombre de operación** (es decir, "put-token"), el **tipo de token** que se coloca (es decir, "servicebus.windows.net:sastoken") y, finalmente, el **"nombre" de la audiencia** a la que se aplica el token (toda la entidad).

Después de enviar el token de SAS en el vínculo del remitente, el publicador tiene que leer la respuesta en el vínculo del receptor. La respuesta es un mensaje de AMQP simple con propiedades de la aplicación denominadas**"código de estado"** que pueden contener los mismos valores que un código de estado HTTP.

## Pasos siguientes

Vea la [referencia de la API de REST de Bus de servicio](https://msdn.microsoft.com/library/azure/hh780717.aspx) para obtener más información sobre lo que puede hacer con estos tokens de SAS.

Para obtener más información sobre la autenticación de Bus de servicio, vea [Autenticación y autorización de Bus de servicio](service-bus-authentication-and-authorization.md).

Puede encontrar más ejemplos de SAS en C# y Java Script en [esta entrada de blog](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1217_2015-->