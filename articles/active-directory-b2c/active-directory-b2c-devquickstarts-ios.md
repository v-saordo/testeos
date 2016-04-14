<properties pageTitle="Versión preliminar de Azure Active Directory B2C: llamar a una API web desde una aplicación iOS | Microsoft Azure" description="Este artículo le mostrará cómo crear una aplicación de "lista de tareas pendientes" para iOS que llame a una API web de Node.js mediante tokens de portador de OAuth 2.0. Tanto la aplicación iOS como la API web usan Azure Active Directory B2C para administrar las identidades de usuario y autenticar usuarios." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Versión preliminar AD B2C: llamar a una API web desde una aplicación iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C es posible agregar eficaces características de administración de identidades de autoservicio tanto a aplicaciones iOS como a API web en pocos pasos. En este artículo se muestra cómo crear una aplicación de "lista de tareas pendientes" para iOS que llama a una API web de Node.js mediante tokens de portador de OAuth 2.0. Tanto la aplicación iOS como la API web usan Azure AD B2C para administrar identidades de usuario y autenticar usuarios.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Para trabajar perfectamente, esta Guía de inicio rápido requiere tener una API web protegida por medio de Azure AD B2C. Hemos creado una tanto para .NET como para Node.js que puede usar. En este tutorial se asume que se ha configurado el ejemplo de API web de Node.js. Para más información, consulte el [ejemplo de API web de Azure Active Directory para Node.js ejemplo](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	Este artículo no trata sobre cómo implementar el inicio de sesión, el registro y la administración de perfiles con Azure AD B2C. Más bien se centra en la realización de llamadas a las API web después de que el usuario está autenticado. Si aún no lo ha hecho, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para obtener información sobre los conceptos básicos de Azure AD B2C.

## Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si no tiene ninguno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Tanto la aplicación como la API web se representarán mediante un único **identificador de aplicación** en este caso, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web/API web** en la aplicación.
- Escribir `http://localhost:3000/auth/openid/return` como **URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
- Crear un **secreto de aplicación** para la aplicación y copiarlo. Lo necesitará más adelante.
- Copiar el **identificador de aplicación** asignado a la aplicación. También lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas

En Azure AD B2C, cada experiencia de usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene tres experiencias de identidad: registro, inicio de sesión e inicio de sesión mediante Facebook. Es preciso que cree una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir el **nombre para mostrar** y los atributos de registro en la directiva de registro.
- Elegir las notificaciones de aplicación **Nombre para mostrar** e **Id. de objeto** en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará estos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Una vez creadas las tres directivas, está listo para compilar la aplicación.

Tenga en cuenta que este artículo no trata sobre cómo usar las directivas que acaba de crear. Para obtener información acerca del funcionamiento de las directivas en Azure AD B2C, comience con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Descargar el código

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Para completar este tutorial, es preciso descargar el esqueleto.** Dada la complejidad de la implementación de una aplicación totalmente funcional en iOS, el **esqueleto** tiene el código de UX que se ejecutará una vez completado el tutorial. Se trata de una medida de ahorro de tiempo para el desarrollador. El código de UX no es relevante para el tema de cómo agregar B2C a una aplicación iOS.

La aplicación completada también estará [disponible en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Luego, cargue `podfile` mediante CocoaPods. Así se creará una nueva área de trabajo de XCode que cargará. Si no tiene CocoaPods, [visite el sitio web para instalarlo](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Configurar la aplicación de la tarea de iOS

Para que la aplicación de la tarea de iOS se comunique con Azure AD B2C, es preciso especificar algunos parámetros comunes. En la carpeta `Microsoft Tasks`, abra el archivo `settings.plist` en la raíz del proyecto y reemplace los valores de la sección `<dict>`. Estos valores se usarán en toda la aplicación.

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

## Obtener tokens de acceso y llamar a la API de la tarea

En esta sección se examina cómo completar un intercambio de tokens de OAuth 2.0 en una aplicación web mediante las bibliotecas y marcos de Microsoft. Si no está familiarizado con los códigos de autorización y los tokens de acceso, puede obtener información en la [referencia del protocolo OAuth 2.0](active-directory-b2c-reference-protocols.md).

### Creación de archivos de encabezado mediante métodos

Necesitará métodos para obtener un token con la directiva seleccionada y llamar luego al servidor de tareas. Ahora puede configurarlos.

Cree un archivo llamado `samplesWebAPIConnector.h` en `/Microsoft Tasks` en el proyecto Xcode.

Agregue el siguiente código para definir lo que debe hacer:

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

Estas son operaciones de creación, lectura, actualización y eliminación (CRUD) simples en su API, así como un método `doPolicy`. Mediante este método se puede obtener un token con la directiva deseada.

Observe que es preciso definir los otros dos archivos de encabezado. Dichos archivos contendrán los datos de los elementos y directivas de la tarea. Créelos ahora:

Cree el archivo `samplesTaskItem.h` con el código siguiente:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Cree también un archivo `samplesPolicyData.h` para que contenga los datos de las directivas:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Incorporación de una implementación para los elementos de tarea y directiva

Una vez aplicados los archivos de encabezado, puede escribir código para almacenar los datos que va a utilizar en el ejemplo.

Cree el archivo `samplesPolicyData.m` con el código siguiente:

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

### Escritura de código de configuración de la llamada a ADAL para iOS

El código rápido para almacenar los objetos de la interfaz de usuario está completo. Luego, debe escribir código para acceder a la biblioteca de autenticación de Active Directory (ADAL) para iOS mediante los parámetros que coloque en `settings.plist`. Así obtendrá un token de acceso que podrá proporcionar a su servidor de tareas.

Todo su trabajo se realizará en `samplesWebAPIConnector.m`.

En primer lugar, cree la implementación de `doPolicy()` que escribió en el archivo de encabezado `samplesWebAPIConnector.h`:

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

El método es simple. Tome como entradas el objeto `samplesPolicyData` que creó, el `ViewController` primario y la devolución de llamada. La devolución de llamada es interesante, por lo que las examinaremos más detenidamente.

- Tenga en cuenta que `completionBlock` tiene `ADProfileInfo` como tipo que se devolverá mediante el uso de un objeto `userInfo`. `ADProfileInfo` es el tipo que contiene todas las respuestas del servidor, incluyendo las notificaciones.
- Tenga también en cuenta `readApplicationSettings`. Este método lee los datos que se proporcionaron en `settings.plist`
- Por último, tiene una método `getClaimsWithPolicyClearingCache` grande. Se trata de la llamada real a ADAL para iOS que debe escribir. Volveremos a este tema más adelante.

Luego escriba su método `getClaimsWithPolicyClearingCache` grande. Este método es suficientemente grande como para que merecer su propia sección.

### Creación de una llamada a ADAL para iOS

Después de descargar el esqueleto de GitHub, podrá ver que tenemos algunas de estas llamadas, que le ayudan con la aplicación de ejemplo. Siguen todos el patrón de `get(Claims|Token)With<verb>ClearningCache`. Las convenciones de Objetive C usan un lenguaje muy parecido al lenguaje cotidiano. Por ejemplo, "Get a token with extra parameters that I provide you and clear the cache" (Obtener el token con parámetros adicionales que proporciono y borrar la caché) es `getTokenWithExtraParamsClearingCache()`.

Va a escribir "Get claims and a token with the policy that I provide to you and don't clear the cache (Obtener notificaciones y un token con la directiva que proporciono y no borrar la caché)" o `getClaimsWithPolicyClearingCache`. Siempre se obtiene un token de ADAL, por lo que no es preciso especificar "claims and a token" (notificaciones y token) en el método. Sin embargo, a veces solo se desea el token, sin la sobrecarga de analizar las notificaciones, por lo que en el proyecto esqueleto también proporcionamos un método sin notificaciones denominado `getTokenWithPolicyClearingCache`.

Escriba el código ahora:

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

La primera parte de esto debe resultarle familiar.

- Cargar la configuración que se proporcionó en `settings.plist` y se asigna a `data`.
- Configurar `ADAuthenticationError`, que toma todos los errores procedentes de ADAL para iOS.
- Crear `authContext`, que configura la llamada a ADAL. Pásele su autoridad para comenzar.
- Dar a `authContext` una referencia al controlador primario para que pueda volver a él.
- Convertir `redirectURI`, que era una cadena de `settings.plist`, en el tipo de NSURL que ADAL espera.
- Configurar `correlationId`, que es un UUID que puede seguir la llamada a través del cliente hasta el servidor y la devolución de la misma. Esto resulta útil para la depuración.

A continuación, llegará a la llamada real al ADAL, que es donde la llamada cambia de lo que aparecería en los usos anteriores de ADAL para iOS:

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

Puede ver que la llamada es bastante simple.

`scopes`: los ámbitos que pasa al servidor que desea solicitar del servidor para cualquier usuario que inicie sesión. En el caso de la versión preliminar B2C, pasa `client_id`. Sin embargo, se espera que esto cambie en la lectura de ámbitos en el futuro. Nuestro plan es actualizar este documento entonces. `additionalScopes`: son ámbitos adicionales que se pueden usar en una aplicación. Se espera que se usen en el futuro. `clientId`: el identificador de aplicación que obtuvo en el portal. `redirectURI`: la redirección a la que se espera que se devuelva el token publicado. `identifier`: una manera de identificar a un usuario para poder ver si hay algún token que se puede usar en la memoria caché. Así se pedir siempre otro token al servidor. Esto se transporta en un tipo denominado `ADUserIdentifier` y puede especificar que desea usarlo como identificador. Debe usar `username`. `promptBehavior`: está en desuso. Debe ser `AD_PROMPT_ALWAYS`. `extraQueryParameters`: cualquier elemento adicional que desee pasar al servidor en formato con codificación URL. `policy`: la directiva que se invoca. Esta es la parte más importante del tutorial.

Puede ver en `completionBlock` que pasa `ADAuthenticationResult`. Aquí se encuentra la información tanto del token como de su perfil (si la llamada se realizó correctamente).

Con el código anterior puede adquirir un token para la directiva que proporcione. Dicho token se usará posteriormente para llamar a la API.

### Creación de llamadas a tareas en el servidor

Una vez que tenga un token, es preciso que se lo proporcione a su API para que reciba la autorización necesaria.

Para ello, es preciso implementar tres métodos:

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

`getTasksList` proporciona una matriz que representa las tareas del servidor. `addTask` y `deleteTask` realizan las acciones subsiguientes y devuelven `true` o `false` si todo se realizó correctamente.

Escriba `getTaskList` en primer lugar:

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

El examen del código de la tarea queda fuera del ámbito de este tutorial. No obstante, es posible que se haya percatado de algo interesante: un método `craftRequest` que toma la dirección URL de la tarea. Este método es lo que se usa para crear la solicitud para el servidor mediante el token de acceso recibido. Escríbalo ahora.

Agregue el siguiente código al archivo `samplesWebAPIConnector.m`:

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

Aquí se toma un identificador uniforme de recursos (URI) web, se le agrega el token mediante el encabezado `Bearer` en HTTP y luego se devuelve al usuario. Este llama a la API `getTokenClearingCache`. Todo esto puede parecer extraño, pero esta llamada simplemente se usa para obtener un token de la memoria caché y asegurarse de que sigue siendo válido. (Las llamadas de `getToken` lo hacen automáticamente y para ello preguntan a ADAL.) Este código se usará en todas las llamadas. Luego, realice los métodos adicionales de la tarea.

Escriba `addTask`:

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

Aquí se sigue el mismo patrón, pero también se presenta el método final que debe implementar: `convertTaskToDictionary`. Aquí se toma su matriz y se convierte en un objeto de diccionario, ya que este objeto se muta más fácilmente a los parámetros de consulta que debe pasar al servidor. El código es simple:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

A continuación, escriba `deleteTask`:

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

### Incorporación de un cierre de sesión a la aplicación

Lo último que hay que hacer es implementar el cierre de sesión en la aplicación. Es una operación muy sencilla. En el archivo `sampleWebApiConnector.m`:

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

## Ejecutar la aplicación de ejemplo

Por último, cree y ejecute la aplicación en xCode. Regístrese o inicie sesión en la aplicación y cree las tareas de un usuario que haya iniciado sesión. Cierre la sesión y vuelva a iniciarla como otro usuario y cree las tareas de dicho usuario.

Observe que las tareas se almacenan por usuario en la API, ya que la API extrae la identidad del usuario del token de acceso que recibe.

Como referencia, el ejemplo completo [se proporciona en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Pasos siguientes

Ahora puede pasar a temas más avanzados de B2C. Puede probar:

[Llamada a una API web de Node.js desde una aplicación web de Node.js]() (Llamada a una API web de Node.js desde una aplicación web de Node.js)

[Personalización de la experiencia de usuario en una aplicación B2C]()

<!---HONumber=AcomDC_0302_2016-->