<properties
	pageTitle="Introducción a iOS de Azure AD | Microsoft Azure"
	description="Cómo crear una aplicación iOS que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="brandwe"/>

# Integrar Azure AD en una aplicación iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD proporciona la biblioteca de autenticación de Active Directory (ADAL) para los clientes de iOS que necesitan tener acceso a recursos protegidos. El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes de Objective-C que:

-	Obtenga tokens de acceso para llamar a la API de gráficos de Azure AD utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Buscar un directorio para los usuarios con un alias determinado

Para crear la aplicación de trabajo completa, deberá:

2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL
5. Usar ADAL para obtener tokens de Azure AD

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## *1. Determinación de cuál será el URI de redireccionamiento para iOS*

Para iniciar de forma segura sus aplicaciones en ciertos escenarios SSO, es necesaria la creación de un **URI de redireccionamiento** en un formato determinado. Un URI de redirección se utiliza para garantizar que los tokens se devuelven a la aplicación correcta que los solicitó.

El formato de iOS para un URI de redireccionamiento es:

```
<app-scheme>://<bundle-id>
```

- 	**aap esquema**: se registra en el proyecto de XCode. Es el modo en que otras aplicaciones pueden llamarlo. Puede encontrarlo en Info.plist -> Tipos de URL -> Identificador de URL. Debe crear uno si aún no tiene ninguno configurado.
- 	**bundle-id**: es el identificador de paquete que se encuentra en la "identity" de la configuración del proyecto en XCode.

Un ejemplo de este código de inicio rápido sería: ******msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## *2. Registro de la aplicación DirectorySearcher*
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API Graph de Azure AD:

-	Inicie sesión en el Portal de administración de Azure.
-	En el panel de navegación izquierdo, haga clic en **Active Directory**.
-	Seleccione el inquilino en el que va a registrar la aplicación.
-	Haga clic en la pestaña **Aplicaciones** y en **Agregar** en el cajón inferior.
-	Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**.
    -	El **nombre** de la aplicación servirá de descripción de la aplicación para los usuarios finales.
    -	El **URI de redirección** es una combinación de esquema y cadena que utilizará Azure AD para devolver las respuestas de los tokens. Escriba un valor específico para su aplicación basado en la información anterior.
-	Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña **Configurar**.
- También en la pestaña **Configurar**, busque la sección "Permisos para otras aplicaciones". Para la aplicación "Azure Active Directory", agregue el permiso de **acceso al directorio de la organización** en **Permisos delegados**. Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## *3. Instalación y configuración de ADAL*
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad. Para que ADAL pueda comunicarse con Azure AD, tiene que proporcionarle información sobre el registro de la aplicación. Comience agregando ADAL al proyecto de DirectorySearcher con Cocapods.

```
$ vi Podfile
```
Agregue lo siguiente a este podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Ahora cargue el podfile mediante cocoapods. Esto creará una nueva área de trabajo de XCode que cargará.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-	En el proyecto QuickStart, abra el archivo plist `settings.plist`. Reemplace los valores de los elementos de la sección para que reflejen los valores especificados en el Portal de Azure. El código hará referencia a estos valores cada vez que use ADAL.
    -	`tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
    -	`clientId` es el identificador de cliente de la aplicación que copió del portal.
    -	El `redirectUri` es la URL de redirección que ha registrado en el portal.

## *4. Uso de ADAL para obtener tokens de AAD*
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a un completionBlock `+(void) getToken : ` y ADAL se encarga del resto.

-	En el proyecto `QuickStart`, abra `GraphAPICaller.m` y busque el comentario `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` en la parte superior. Este es el lugar en el que se pasan a ADAL mediante CompletionBlock las coordenadas necesarias para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Ahora tenemos que utilizar este token para buscar usuarios en el gráfico. Busque el comentario `// TODO: implement SearchUsersList`. Este método realiza una solicitud GET a la API Graph de Azure AD para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado. Sin embargo, para realizar una consulta a la API Graph, tiene que incluir un access\_token en el encabezado `Authorization` de la solicitud, que es donde entra ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
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

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)

                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];

                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];

                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];

                         [Users addObject:s];
                     }

                     completionBlock(Users, nil);
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
- Cuando la aplicación solicita un token mediante una llamada a `getToken(...)`, ADAL intentará devolver un token sin solicitar al usuario las credenciales. Si ADAL determina que el usuario debe iniciar sesión para obtener un token, mostrará un cuadro de diálogo de inicio de sesión, recopilará las credenciales del usuario y devolverá un token tras una autenticación correcta. Si ADAL no puede devolver un token por alguna razón, mostrará una `AdalException`.
- Observe que el objeto `AuthenticationResult` contiene un objeto `tokenCacheStoreItem` que puede usarse para recopilar información puede necesitar la aplicación. En QuickStart, `tokenCacheStoreItem` se utiliza para determinar si ya se ha producido la autenticación.


## Paso 5: compilación y ejecución de la aplicación



¡Enhorabuena! Ahora tiene una aplicación de iOS operativa que tiene la capacidad de autenticar usuarios, realizar llamadas seguras a las API web que usan OAuth 2.0 y obtener información básica sobre el usuario. Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios. Ejecute la aplicación QuickStart e inicie sesión con uno de esos usuarios. Busque otros usuarios según su UPN. Cierre la aplicación y vuelva a ejecutarla. Observe cómo la sesión del usuario permanece intacta.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación. Se encarga de todo el trabajo duro: administración de la caché, compatibilidad con el protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de los tokens caducados, etc. Todo lo que necesita saber es una única llamada de API, `getToken`.

Como referencia, [aquí](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) puede ver el ejemplo finalizado (sin sus valores de configuración). Ahora puede pasar a otros escenarios. También puede probar lo siguiente:

[Protección de una API Web Node.JS con Azure AD >>](../active-directory-devquickstarts-webapi-nodejst.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->