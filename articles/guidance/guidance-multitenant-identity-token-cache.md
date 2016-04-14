<properties
   pageTitle="Almacenamiento en caché de tokens de acceso en una aplicación multiinquilino | Microsoft Azure"
   description="Almacenamiento en caché de tokens de acceso utilizados para invocar una API web de back-end"
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


# Almacenamiento en caché de tokens de acceso en una aplicación multiinquilino

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

Es relativamente costoso obtener un token acceso de OAuth, porque requiere una solicitud HTTP al punto de conexión del token. Por lo tanto, es bueno almacenar tokens en caché siempre que sea posible. La [biblioteca de autenticación de Azure AD][ADAL] (ADAL) almacena automáticamente en caché los tokens obtenidos de Azure AD, incluidos los tokens de actualización.

ADAL proporciona una implementación de caché de tokens predeterminada. Sin embargo, esta caché de tokens está diseñada para aplicaciones cliente nativas y _no_ es adecuada para aplicaciones web:

-	Es una instancia estática y no es segura para subprocesos.
-	No se escala a un gran número de usuarios, ya que los tokens de todos los usuarios van en el mismo diccionario.
-	No se puede compartir entre servidores web en una granja.

En su lugar, debe implementar una caché de tokens personalizada que se derive de la clase `TokenCache` de ADAL pero que sea adecuada para un entorno de servidor y proporcione el nivel de aislamiento deseable entre los tokens de los diferentes usuarios.

La clase `TokenCache` almacena un diccionario de tokens, indexado por emisor, recurso, identificador de cliente y usuario. Una caché de tokens personalizada debe escribir este diccionario en una memoria auxiliar, como una caché en Redis.

En la aplicación Tailspin Survey, la clase `DistributedTokenCache` implementa la caché del token. Esta implementación usa la abstracción [IDistributedCache][distributed-cache] de ASP.NET Core 1.0. De este modo, cualquier implementación `IDistributedCache` se puede usar como memoria auxiliar.

-	De forma predeterminada, la aplicación Surveys utiliza una caché en Redis.
-	En el caso de un servidor web de instancia única, puede utilizar [la caché en memoria][in-memory-cache] de ASP.NET Core 1.0 (esto también es una buena opción para ejecutar la aplicación localmente durante el desarrollo).

> [AZURE.NOTE] Actualmente no se admite la caché en Redis para .NET Core.

`DistributedTokenCache` almacena los datos de la memoria caché como pares de clave/valor en la memoria auxiliar. La clave es el identificador de usuario más el identificador de cliente, por lo que la memoria auxiliar contiene datos de caché independientes para cada combinación única de usuario/cliente.

![Memoria caché de tokens](media/guidance-multitenant-identity/token-cache.png)

Las particiones de la memoria auxiliar las realiza el usuario. Para cada solicitud HTTP, los tokens de ese usuario se leen desde la memoria auxiliar y se cargan en el diccionario `TokenCache`. Si Redis se utiliza como memoria auxiliar, cada una de las instancias de servidor de una granja de servidores lee/escribe en la misma caché, y este enfoque se escala a muchos usuarios.

## Cifrado de tokens almacenados en caché

Los tokens son datos confidenciales, ya que conceden acceso a los recursos de un usuario (además, a diferencia de una contraseña de usuario, no es posible almacenar simplemente un hash del token). Por lo tanto, es fundamental proteger los tokens y evitar que estos corran riesgos. La memoria caché con el respaldo de una memoria auxiliar en Redis está protegida por una contraseña, pero si alguien obtiene la contraseña, podría obtener todos los tokens de acceso almacenados en caché. Por ese motivo, `DistributedTokenCache` cifra todo lo que se escribe en la memoria auxiliar. El cifrado se realiza mediante las API de [protección de datos][data-protection] de ASP.NET Core 1.0.

> [AZURE.NOTE] Si se implementa en Sitios web de Azure, se realiza una copia de seguridad de las claves de cifrado en el almacenamiento de red y estas se sincronizan en todos los equipos (consulte [Key Management][key-management] (Administración de claves)). De forma predeterminada, las claves no se cifran cuando se ejecutan en Sitios web de Azure, pero puede [habilitar el cifrado mediante un certificado X.509][x509-cert-encryption].


## Implementación de DistributedTokenCache

La clase [DistributedTokenCache][DistributedTokenCache] se deriva de la clase [TokenCache][tokencache-class] de ADAL.

En el constructor, la clase `DistributedTokenCache` crea una clave para el usuario actual y la carga la memoria caché desde la memoria auxiliar:

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

La clave se crea concatenando el identificador de usuario y el identificador de cliente. Ambos se toman de las notificaciones encontradas en `ClaimsPrincipal` del usuario:

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

Para cargar los datos de la memoria caché, lea el blob serializado desde la memoria auxiliar y llame a `TokenCache.Deserialize` para convertir el blob en datos de memoria caché.

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

Cada vez que ADAL acceda a la memoria caché, se activará un evento `AfterAccess`. Si han cambiado los datos de la memoria caché, la propiedad `HasStateChanged` es true. En ese caso, actualice la memoria auxiliar para reflejar el cambio y, a continuación, establezca `HasStateChanged` en false.

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache envía otros dos eventos:

- `BeforeWrite`. Se le llama inmediatamente antes de que ADAL escriba en la memoria caché. Puede utilizar esto para implementar una estrategia de simultaneidad
- `BeforeAccess`. Se le llama inmediatamente antes de que ADAL lea desde la memoria caché. Aquí puede volver a cargar la memoria caché para obtener la versión más reciente.

En nuestro caso, decidimos no administrar estos dos eventos.

- Para la simultaneidad, la última escritura tiene prioridad. Eso está bien, porque los tokens se almacenan por separado para cada usuario + cliente, por lo que solo podría ocurrir un conflicto si el mismo usuario tuviera dos inicios de sesión abiertos simultáneamente.
- En el caso de la lectura, cargamos la memoria caché en cada solicitud. Las solicitudes tienen poca vigencia. Si se modifica la memoria caché en ese tiempo, la siguiente solicitud recogerá el nuevo valor.

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[forma parte de una serie]: guidance-multitenant-identity.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->