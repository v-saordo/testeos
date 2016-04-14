<properties
   pageTitle="Uso de aserción de cliente para obtener tokens de acceso de Azure AD | Microsoft Azure"
   description="Uso de aserción de cliente para obtener tokens de acceso de Azure AD."
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

# Uso de aserción de cliente para obtener tokens de acceso de Azure AD

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

## Información preliminar:

Al usar el flujo de código de autorización o flujo híbrido en OpenID Connect, el cliente intercambia un código de autorización para un token de acceso. Durante este paso, el cliente tiene que autenticarse en el servidor.

![Secreto del cliente](media/guidance-multitenant-identity/client-secret.png)

Una manera de autenticar el cliente es mediante un secreto de cliente. Así es como la aplicación [Tailspin encuestas][Surveys] está configurada de forma predeterminada.

Esta es una solicitud de ejemplo desde el cliente al IDP, que solicita un token de acceso. Tenga en cuenta el parámetro `client_secret`.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

El secreto es una cadena, por lo que debe asegurarse de no perder el valor. La práctica recomendada es mantener el secreto de cliente fuera del control de código fuente. Cuando implementa en Azure, almacene el secreto en una [configuración de la aplicación][configure-web-app].

Sin embargo, cualquier persona con acceso a la suscripción de Azure puede ver la configuración de la aplicación. Además, siempre existe la tentación de proteger los secretos en el control de código fuente (por ejemplo, en los scripts de implementación), compartirlos por correo electrónico, etc.

Para obtener seguridad adicional, puede usar _aserción cliente_ en lugar de un secreto de cliente. Con aserción de cliente, el cliente utiliza un certificado X.509 para demostrar que la solicitud de token procedía del cliente. El certificado de cliente se instala en el servidor web. Por lo general, será más fácil restringir el acceso al certificado que garantizar que nadie revela inadvertidamente un secreto de cliente.

A continuación figura una solicitud de token utilizando aserción de cliente:

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Observe que el parámetro `client_secret` ya no se usa. En su lugar, el parámetro `client_assertion` contiene un token de JWT que se firmó con el certificado de cliente. El parámetro `client_assertion_type` especifica el tipo de aserción; en este caso, el token de JWT. El servidor valida el token de JWT. Si el token de JWT no es válido, la solicitud de token devuelve un error.

> [AZURE.NOTE] Los certificados X.509 no son la única forma de aserción de cliente; nos centraremos en ella aquí porque es compatible con Azure AD.

## Uso de aserción de cliente en la aplicación Surveys

En esta sección se muestra cómo configurar la aplicación Tailspin Surveys para usar aserción de cliente. En estos pasos, generará un certificado autofirmado que es adecuado para el desarrollo, pero no para su uso en producción.

1. Ejecute el script de PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] de la siguiente manera:

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Para el parámetro `Subject`, escriba cualquier nombre, como "surveysapp". El script genera un certificado autofirmado y lo almacena en el almacén de certificados "Usuario actual/Personal".

2. La salida del script es un fragmento de código JSON. Agréguelo al manifiesto de aplicación de la aplic. web, de la siguiente manera:

    1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal] y vaya a su directorio de Azure AD.

    2. Haga clic en **Aplicaciones**.

    3. Seleccione la aplicación Surveys.

    4.	Haga clic en **Administrar manifiesto** y seleccione **Descargar manifiesto**.

    5.	Abra el archivo JSON de manifiesto con un editor de texto. Pegue la salida del script en la propiedad `keyCredentials`. El aspecto debe ser similar al siguiente:

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.	Guarde los cambios en el archivo JSON.

    7.	Vuelva al portal. Haga clic en **Administrar manifiesto** > **Cargar manifiesto** y cargue el archivo JSON.

3. Ejecute el siguiente comando para obtener la huella digital del certificado.

    ```
    certutil -store -user my [subject]
    ```

    donde `[subject]` es el valor que especificó para Subject en el script de PowerShell. La huella digital se muestra en "Cert hash (sha1)". Quite los espacios entre los números hexadecimales.

4. Actualice los secretos de la aplicación. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto Tailspin.Surveys.Web y seleccione **Administrar secretos de usuario**. Agregue una entrada para "Asymmetric" en "AzureAd", tal como se muestra a continuación:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    Debe establecer `ValidationRequired` en false, porque el certificado no estaba firmado por una entidad CA raíz. En producción, use un certificado firmado por una entidad CA y establezca `ValidationRequired` en true.

    Elimine también la entrada de `ClientSecret` porque no se necesita con aserción de cliente.

5. En Startup.cs, busque el código que registra `ICredentialService`. Quite la marca de comentario de la línea que usa `CertificateCredentialService` y marque como comentario la línea que usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

En tiempo de ejecución, la aplicación web lee el certificado del almacén de certificados. El certificado debe instalarse en el mismo equipo que la aplic. web.

## Recursos adicionales

- [Uso de certificados en las aplicaciones de Sitios web Azure][using-certs-in-websites]
- [RFC 7521][RFC7521]. Define el mecanismo general para enviar una aserción de cliente.
- [RFC 7523][RFC7523]. Define cómo usar tokens de JWT para una aserción de cliente.


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[forma parte de una serie]: guidance-multitenant-identity.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->