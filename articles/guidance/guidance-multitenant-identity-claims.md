<properties
   pageTitle="Trabajo con identidades basadas en notificaciones en aplicaciones multiinquilino | Microsoft Azure"
   description="Uso de las notificaciones para la autorización y validación del emisor"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Trabajo con identidades basadas en notificaciones en aplicaciones multiinquilino

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

## Notificaciones en Azure AD

Cuando un usuario inicia sesión, Azure AD envía un token de identificador que contiene un conjunto de notificaciones sobre el usuario. Una notificación es simplemente un fragmento de información que se expresa como un par clave-valor. Por ejemplo, `email`=`bob@contoso.com`. Las notificaciones tienen un emisor (en este caso, Azure AD) que es la entidad que autentica al usuario y crea las notificaciones. Usted confía en las notificaciones porque confía en el emisor. (Y a la inversa, ¡si no confía en el emisor, no confía en las notificaciones!)

En un alto nivel:

1.	El usuario autentica.
2.	El IDP envía un conjunto de notificaciones.
3.	La aplicación normaliza o aumenta las notificaciones (opcionales).
4.	La aplicación usa las notificaciones para tomar decisiones de autorización.

En OpenID Connect, el [parámetro de ámbito] de la solicitud de autenticación controla el conjunto de notificaciones que se obtienen. Sin embargo, Azure AD emite un conjunto limitado de notificaciones a través de OpenID Connect; vea [Tipos de notificaciones y tokens admitidos]. Si desea obtener más información sobre el usuario, debe usar la API Graph de Azure AD.

Estas son algunas de las notificaciones de AAD de las que normalmente podría encargarse una aplicación:

Tipo de notificación en el token de identificador. |	Descripción
-----------------------|--------------
aud | Para quién se emitió el token. Será el identificador de cliente de la aplicación. Por lo general, no es necesario preocuparse por esta notificación porque el middleware la valida automáticamente. Ejemplo: `"91464657-d17a-4327-91f3-2ed99386406f"`
groups | Una lista de grupos de AAD de los que el usuario es miembro. Ejemplo: `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
iss | El [emisor] del token OIDC. Ejemplo: `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
name | Nombre para mostrar del usuario. Ejemplo: `"Alice A."`
oid | El identificador de objeto para el usuario en AAD. Este valor es el identificador inmutable y no reutilizable del usuario. Utilice este valor, no el correo electrónico, como un identificador único para los usuarios; las direcciones de correo electrónico pueden cambiar. Si utiliza la API Graph de Azure AD en su aplicación, el identificador de objeto es el valor que se usa para consultar la información de perfil. Ejemplo: `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
roles | Una lista de roles de aplicación para el usuario. Ejemplo: `["SurveyCreator"]`
tid | Identificador de inquilino. Este valor es un identificador único para el inquilino en Azure AD. Ejemplo: `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique\_name | Un nombre del usuario para mostrar en lenguaje natural. Ejemplo: `"alice@contoso.com"`
upn | Nombre principal de usuario. Ejemplo: `"alice@contoso.com"`

Esta tabla enumera los tipos de notificación tal y como aparecen en el token de identificador. En ASP.NET Core 1.0, el middleware OpenID Connect convierte algunos de los tipos de notificación cuando rellena la colección de notificaciones para la entidad de seguridad de usuario:

-	oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`
-	tid > `http://schemas.microsoft.com/identity/claims/tenantid`
-	unique\_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-	upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## Transformaciones de notificaciones

Durante el flujo de autenticación, puede que desee modificar las notificaciones que obtiene del IDP. En ASP.NET Core 1.0, puede realizar la transformación de notificaciones dentro del evento **AuthenticationValidated** desde el middleware OpenID Connect. Vea [Authentication events (Eventos de autenticación)].

Todas las notificaciones que agregue durante **AuthenticationValidated** se almacenan en la cookie de autenticación de sesión. No se hacen retroceder a Azure AD.

Estos son algunos ejemplos de transformación de notificaciones:

-	**Normalización de notificaciones**, o hacer que las notificaciones sean coherentes entre los usuarios. Esto es especialmente importante si recibe notificaciones de varios IDP, que podrían usar diferentes tipos de notificación para información similar. Por ejemplo, Azure AD envía una notificación "upn" que contiene el correo electrónico del usuario. Otros IDP podrían enviar una notificación "email". El código siguiente convierte la notificación "upn" en una notificación "email":

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Agregar **valores de notificación predeterminados** para notificaciones que no están presentes (por ejemplo, asignando un usuario a un rol predeterminado). En algunos casos esto puede simplificar la lógica de autorización.
- Agregar **tipos de notificación personalizados** con información específica de la aplicación sobre el usuario. Por ejemplo, podría almacenar información sobre el usuario en una base de datos. Podría agregar una notificación personalizada con esta información la incidencia de autenticación. La notificación se almacena en una cookie, por lo que solo necesita obtenerla de la base de datos una vez por cada inicio de sesión. Por otro lado, también desea evitar la creación de cookies excesivamente grandes, por lo que debe tener en cuenta el equilibrio entre el tamaño de las cookies y las búsquedas en la base de datos.   

Una vez completado el flujo de autenticación, las notificaciones están disponibles en `HttpContext.User`. En ese momento, debe tratarlas como una colección de solo lectura; por ejemplo, usarlas para tomar decisiones de autorización.

## Validación del emisor
En OpenID Connect, la notificación del emisor ("iss") identifica el IDP que emitió el token de identificador. Parte del flujo de autenticación de OIDC es comprobar que la notificación del emisor coincide con el emisor real. El middleware OIDC se encarga de ello.

En Azure AD, el valor de emisor es único por inquilino de AD (`https://sts.windows.net/<tenantID>`). Por lo tanto, una aplicación debe hacer una comprobación adicional para asegurarse de que el emisor representa a un inquilino al que se le permite iniciar sesión en la aplicación.

Para una aplicación de un solo inquilino, basta con comprobar que el emisor es su propio inquilino. De hecho, el middleware OIDC hace esto automáticamente de forma predeterminada. En una aplicación de varios inquilinos, necesita permitir varios emisores, correspondientes a los diferentes inquilinos. A continuación se expone un enfoque general para usar:

-	En las opciones de middleware OIDC, establezca **ValidateIssuer** en false. Esto desactiva la comprobación automática.
-	Cuando un inquilino se suscriba, almacene el inquilino y el emisor en la base de datos de usuario.
-	Cada vez que un usuario inicie sesión, busque el emisor en la base de datos. Si no se encuentra el emisor, significa que el inquilino no se ha suscrito. Es posible redirigirlos a una página de suscripción.
-  También puede generar una lista negra de determinados inquilinos; por ejemplo, para los clientes que no pagaron su suscripción.

Para obtener información más detallada, consulte [Sign-up and tenant onboarding (Suscripción e incorporación de inquilinos)].

## Uso de notificaciones para la autorización

Con notificaciones, la identidad de un usuario ya no es una entidad monolítica. Por ejemplo, un usuario podría tener dirección de correo electrónico, número de teléfono, cumpleaños, sexo, etc. Quizás el IDP del usuario almacene toda esta información. Pero al autenticar al usuario, normalmente obtiene un subconjunto de esta información como notificaciones. En este modelo, la identidad del usuario es simplemente un conjunto de notificaciones. Cuando tome decisiones de autorización sobre un usuario, buscará determinados conjuntos de notificaciones. En otras palabras, la pregunta "¿Puede el usuario X realizar la acción Y?" finalmente se convierte en "¿Tiene el usuario X la notificación Z?".

Estos son algunos patrones básicos para la comprobación de notificaciones.

-  Para comprobar que el usuario tiene una notificación determinada con un valor concreto:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Este código comprueba si el usuario tiene una notificación de rol con el valor "Admin". Administra correctamente el caso donde el usuario no tiene notificación de rol o varias notificaciones de rol.

    La clase **ClaimTypes** clase define las constantes para los tipos de notificación usados con frecuencia. Sin embargo, puede usar cualquier valor de cadena para el tipo de notificación.

-	Para obtener un único valor para un tipo de notificación, cuando se espera que haya como máximo un valor:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-	Para obtener todos los valores para un tipo de notificación:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Para obtener más detalles sobre el uso de notificaciones en autorización, vea [Autorización].

## Recursos adicionales

- [Autorización basada en notificaciones]


<!-- Links -->
[forma parte de una serie]: guidance-multitenant-identity.md
[parámetro de ámbito]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Tipos de notificaciones y tokens admitidos]: ../active-directory/active-directory-token-and-claims.md
[emisor]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Authentication events (Eventos de autenticación)]: guidance-multitenant-identity-authenticate.md#authentication-events
[Sign-up and tenant onboarding (Suscripción e incorporación de inquilinos)]: guidance-multitenant-identity-signup.md
[Autorización]: guidance-multitenant-identity-authorize.md
[Autorización basada en notificaciones]: https://docs.asp.net/en/latest/security/authorization/claims.html
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->