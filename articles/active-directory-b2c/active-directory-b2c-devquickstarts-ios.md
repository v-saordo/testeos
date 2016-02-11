<properties pageTitle="Vista previa de Azure AD B2C: Llamada a una API web desde una aplicación iOS | Microsoft Azure" description="Este artículo le mostrará cómo crear una aplicación iOS de "Lista de tareas pendientes" que llama a una API web de node.js mediante tokens de portador de OAuth 2.0. Tanto la aplicación iOS como la API web usan Azure AD B2C para administrar identidades de usuario y autenticar usuarios." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="brandwe"/>

# Vista previa de Azure AD B2C: Llamada a una API web desde una aplicación iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure AD B2C, puede agregar unas características de administración de identidades de autoservicio eficaces a sus aplicaciones iOS y API web en unos cuantos pasos. Este artículo le mostrará cómo crear una aplicación iOS de "Lista de tareas pendientes" que llama a una API web de node.js con tokens de portador de OAuth 2.0. Tanto la aplicación iOS como la API web usan Azure AD B2C para administrar identidades de usuario y autenticar usuarios.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Este inicio rápido presenta un requisito previo de que hay que tener una API web protegida por Azure AD con B2C para poder funcionar completamente. Hemos creado uno para que lo pueda usar tanto para .Net como para node.js. Este tutorial supone que está configurado el ejemplo de API web de node.js. Consulte el [ejemplo de la API web de Azure Active Directory para node.js](active-directory-b2c-devquickstarts-api-node.md`).

> [AZURE.NOTE]
	Este artículo no trata de la implementación de registros, inicios de sesión y administración de perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado. Si no lo ha hecho ya, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para obtener información sobre los conceptos básicos de Azure AD B2C.

## 1\. Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos, etc. Si no tiene uno todavía, vaya a [crear un directorio de B2C](active-directory-b2c-get-started.md) antes de continuar.

## 2\. Creación de una aplicación

Ahora debe crear una aplicación en su directorio de B2C, que ofrece a Azure AD información que necesita para comunicarse de forma segura con su aplicación. Tanto la aplicación como la API web se representarán mediante un **Id. de aplicación** único en este caso, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de

- Incluir una **aplicación web/API web** en la aplicación.
- Escribir `http://localhost:3000/auth/openid/return` como **dirección URL de respuesta**: es la dirección URL predeterminada para este ejemplo de código.
- Crear un **Secreto de aplicación ** para la aplicación y copiarlo. Lo necesitará en breve.
- Escribir el **Id. de aplicación** asignado a la aplicación. También lo necesitará en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [**directiva**](active-directory-b2c-reference-policies.md). Esta aplicación contiene tres experiencias de identidad: registro, inicio de sesión e inicio de sesión con Facebook. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Al crear sus tres directivas, asegúrese de:

- Seleccionar **Nombre para mostrar** y algunos otros atributos de registro en la directiva de registro.
- Elegir las notificaciones de aplicación **Nombre para mostrar** e **Id. de objeto** en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará esos nombres de directivas en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando tenga tres directivas creadas correctamente, estará listo para crear su aplicación.

Tenga en cuenta que este artículo no trata de cómo usar las directivas que acaba de crear. Si quiere aprender cómo funcionan las directivas en Azure AD B2C, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Descargar el código

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Es necesaria la descarga del esqueleto para completar este tutorial.** Debido a la complejidad de la implementación de una aplicación totalmente funcional en iOS, el **esqueleto** tiene el código de experiencia de usuario que se ejecutará una vez completado el tutorial siguiente. Se trata de una medida de ahorro de tiempo para los desarrolladores. El código de experiencia de usuario no es relevante para el tema de cómo agregar B2C a una aplicación iOS.

La aplicación completada también estará [disponible como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) o en la rama `complete` del mismo repositorio.


Ahora cargue el podfile mediante cocoapods. Esto creará una nueva área de trabajo de XCode que cargará. Si no tiene CocoaPods, visite el [sitio web para configurar CocoaPods](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\. Configurar la aplicación de la tarea de iOS

Para que la aplicación de la tarea de iOS se comunique con Azure AD B2C, hay algunos parámetros comunes que debe proporcionar. En la carpeta `Microsoft Tasks`, abra el archivo `settings.plist` en la raíz del proyecto y reemplace los valores de la sección `<dict>`: Estos valores se usarán en toda la aplicación.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## 6\. Obtener tokens de acceso y llamar a la API de la tarea

En esta sección se mostrará cómo completar un intercambio de tokens de OAuth 2.0 en una aplicación web con las bibliotecas y marcos de trabajo de Microsoft. Si no está familiarizado con los **códigos de autorización** y los **tokens de acceso**, puede ser una buena idea consultar la [Referencia del protocolo OAuth 2.0](active-directory-b2c-reference-protocols.md).

#### Creación de archivos de encabezado con los métodos que vamos a usar

Necesitamos métodos para obtener un token con la directiva seleccionada y llamar luego a nuestro servidor de tarea. Vamos a configurarlos ahora.

Cree un archivo llamado `samplesWebAPIConnector.h` en /Microsoft Tasks en el proyecto XCode.

Agregue el siguiente código para definir lo que hay que hacer:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Verá que se trata de simples operaciones CRUD sobre nuestra API, así como un método `doPolicy` que le permite obtener un token con la directiva que quiera.

También verá que tenemos otros dos archivos de encabezado que tenemos que definir y que contendrán el elemento de tarea y los datos de la directiva. Vamos a crearlos ahora:

Cree un archivo llamado `samplesTaskItem.h` con el código siguiente:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Vamos a crear también un archivo `samplesPolicyData.h` para que contenga los datos de la directiva:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### Incorporación de una implementación para los elementos de tarea y directiva

Ahora que ya tenemos los archivos de encabezado, tenemos que escribir código para almacenar los datos que se van a usar en el ejemplo.

Cree un archivo llamado `samplesPolicyData.m` con el código siguiente:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

#### Escritura de código de configuración para la llamada a ADAL para iOS

El código rápido para almacenar los objetos de la interfaz de usuario está completado. Ahora debemos escribir el código para acceder a ADAL para iOS con los parámetros que incluimos en el archivo `settings.plist` a fin de obtener un token de acceso que proporcionaremos a nuestro servidor de tarea. Esto puede aparecer detallado, así que esté atento.

Todo nuestro trabajo se realizará en `samplesWebAPIConnector.m`.

En primer lugar, vamos a crear la implementación de `doPolicy()` que escribimos en el archivo de encabezado `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

Verá que el método es bastante sencillo. Toma como entrada el objeto `samplesPolicyData` creado hace unos momentos, el elemento primario ViewController y una devolución de llamada. La devolución de llamada es interesante y vamos a mostrarla paso a paso.

1. Verá que el `completionBlock` tiene ADProfileInfo como tipo que se devolverá con un objeto `userInfo`. ADProfileInfo es el tipo que contiene toda la respuesta del servidor, en notificaciones determinadas.
2. Verá `readApplicationSettings`. Este lee los datos que proporcionamos en `settings.plist`
3. Por último, tenemos un método `getClaimsWithPolicyClearingCache` bastante grande. Se trata de la llamada real a ADAL para iOS que tenemos que escribir. Lo haremos más adelante.

Ahora vamos a escribir el método grande `getClaimsWithPolicyClearingCache`. Es lo suficientemente grande como para que tenga su propia sección.

#### Creación de la llamada a ADAL para iOS

Si ha descargado el esqueleto desde GitHub, verá que ya tenemos algunos que nos ayudan con la aplicación de ejemplo. Siguen todos el patrón de `get(Claims|Token)With<verb>ClearningCache`. Si se usan las convenciones de Objetive C, esto es muy parecido al inglés. Por ejemplo, "get a Token with extra parameters I provide you and clear the cache" (obtener un token con parámetros adicionales que le proporciono y borrar la caché). Esto es `getTokenWithExtraParamsClearingCache()`. Bastante fácil.

Vamos a escribir "get Claims and a token With the policy I provide you and don't clear the cache" (obtener notificaciones y un token con la directiva que proporciono y no borrar la caché) o `getClaimsWithPolicyClearingCache`. Siempre obtenemos un token desde ADAL, por lo que no es necesario especificar "Claims and token" (notificaciones y token) en el método. Pero, a veces, es preferible tener solo el token sin la sobrecarga de analizar las notificaciones, por lo que proporcionamos un método sin notificaciones denominado `getTokenWithPolicyClearingCache` en el esqueleto.

Vamos a escribir el código ahora:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

La primera parte de esto debe resultarle familiar. Cargamos la configuración proporcionada en `Settings.plist` y la asignamos a `data`. Después, configuramos un `ADAuthenticationError` que tomará cualquier error que procede de ADAL para iOS. También creamos un `authContext` que configura nuestra llamada a ADAL. Le pasamos nuestra *autoridad* para comenzar. También ofrecemos a `authContext` una referencia a nuestro controlador primario para que podamos volver a él. Convertimos igualmente nuestro `redirectURI` que era una cadena de `settings.plist` en el tipo de NSURL que ADAL espera. Por último, configuramos un `correlationId` que es simplemente un UUID que puede seguir la llamada a través del cliente hasta el servidor y volver. Esto resulta útil para la depuración.

Ahora llegamos a la llamada real a ADAL y aquí es donde la llamada cambia de lo que aparecía en usos anteriores de ADAL para iOS:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Puede ver que la llamada es bastante sencilla.

**scopes**: se trata de los ámbitos que pasamos al servidor que queremos solicitar desde el servidor para el inicio de sesión del usuario. Para la vista previa de B2C, pasamos client\_id. Sin embargo, esto cambiará la lectura de ámbitos en el futuro. Entonces se actualizará este documento. **addtionalScopes**: son los ámbitos adicionales que podemos usar para la aplicación. Esto se usará en el futuro. **clientId**: identificador de aplicación obtenido en el portal. **redirectURI**: redireccionamiento al que se espera que se envíe de vuelta el token. **identifier**: modo de identificar al usuario, así podemos ver si hay un token que se puede usar en la caché en comparación con tener que solicitar siempre otro token al servidor. Verá que esto se transporta en un tipo denominado `ADUserIdentifier` y podemos especificar lo que queremos usar como id. Ha de usar el nombre de usuario. **promptBehavior**: esto está en desuso y debe ser AD\_PROMPT\_ALWAYS. **extraQueryParameters**: algo adicional que quiera pasar al servidor en formato codificado de dirección URL. **policy**: directiva que se invoca. La parte más importante de este tutorial.

Puede ver que en completionBlock pasamos el `ADAuthenticationResult` que tiene nuestro token y la información de perfil (si la llamada se realizó correctamente).

Con el código anterior puede adquirir un token para la directiva proporcionada. Usaremos este token para llamar a la API.

#### Creación de llamadas de tarea al servidor

Ahora que tenemos un token, tenemos que proporcionarlo a nuestra API para la autorización.

Si lo recuerda bien, tenemos tres métodos que debemos implementar:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

Nuestro `getTasksList` proporciona una matriz que representa las tareas del servidor. Nuestros `addTask` y `deleteTask` realizan la acción posterior y devuelven TRUE o FALSE si todo se realizó correctamente.

Vamos a escribir primero nuestro `getTaskList`:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

Queda fuera del ámbito de este tutorial analizar el código de tarea, pero quizá observó algo interesante: un método `craftRequest` que toma la dirección URL de la tarea. Este método es lo que usamos para crear la solicitud, con el token de acceso recibido, para el servidor. Vamos a escribirlo ahora.

Vamos a agregar el código siguiente al archivo 'samplesWebAPIConnector.m':

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Como puede ver, este toma un URI web, le agrega el token con el encabezado `Bearer` en HTTP y luego nos lo devuelve. Llamamos a la API `getTokenClearingCache`, lo que puede parecer extraño al principio, pero simplemente usamos esta llamada para obtener un token de la caché y asegurarnos de que aún es válido (las llamadas getToken* lo hacen automáticamente solicitándolo a ADAL). Usamos este código en cada llamada. Ahora volvamos a la creación de nuestros métodos adicionales de la tarea.

Vamos a escribir nuestro `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Este sigue el mismo patrón pero incorpora otro (y último) método que es necesario implementar: `convertTaskToDictionary`, que toma la matriz y la convierte en un objeto de diccionario que resulta más fácil de transformar en los parámetros de consulta que hay que pasar al servidor. Este código es muy sencillo:

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Por último, vamos a escribir nuestro `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Incorporación de cierre de sesión a la aplicación

Lo último que debemos hacer es implementar el cierre de sesión para la aplicación. Esto es bastante sencillo. De nuevo, dentro del archivo `sampleWebApiConnector.m`:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## 9\. Ejecutar la aplicación de ejemplo

Por último, cree y ejecute la aplicación en xCode. Regístrese o inicie sesión en la aplicación y cree tareas para el usuario que ha iniciado sesión. Cierre sesión y vuelva a iniciarla como otro usuario, y cree tareas para ese usuario.

Observe cómo se almacenan las tareas por usuario en la API, puesto que la API extrae la identidad del usuario del token de acceso que recibe.

Como referencia, el ejemplo finalizado [se proporciona en forma de archivo .zip aquí](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Pasos siguientes

Ahora puede pasar a temas más avanzados de B2C. También puede probar lo siguiente:

[Llamada a la API web de node.js desde una aplicación web de node.js >>]()

[Personalización de la experiencia de usuario de la aplicación B2C >>]()

<!---HONumber=AcomDC_0128_2016-->