<properties
   pageTitle="Uso de Almacén de claves para proteger los secretos de la aplicación | Microsoft Azure"
   description="Uso del servicio Almacén de claves para almacenar secretos de la aplicación"
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

# Uso de Almacén de claves de Azure para proteger los secretos de la aplicación

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

## Información general

Es habitual tener opciones de configuración de la aplicación que son confidenciales y deben protegerse, por ejemplo:

- Cadenas de conexión de base de datos
- Contraseñas
- Claves de cifrado

Como procedimiento recomendado de seguridad, no debe almacenar nunca estos secretos en el control de código fuente. Se filtran muy fácilmente, aunque el repositorio de código fuente sea privado. Y no se trata solo de proteger los secretos frente al público general. En proyectos de mayor tamaño, es posible que desee restringir qué desarrolladores y operadores pueden tener acceso a los secretos de producción. (La configuración de los entornos de prueba o desarrollo son diferentes).

Una opción más segura es almacenar estos secretos en [Almacén de claves de Azure][KeyVault]. Almacén de claves es un servicio hospedado en la nube para administrar claves criptográficas y otros secretos. Este artículo muestra cómo usar Almacén de claves para almacenar las opciones de configuración de la aplicación.

En la aplicación [Tailspin Surveys][Surveys], las siguientes opciones de configuración son secretas:

- La cadena de conexión de base de datos.
- La cadena de conexión de Redis.
- El secreto de cliente de la aplicación web.

Para almacenar secretos de configuración en Almacén de claves, la aplicación Surveys implementa un proveedor de configuración personalizado que se enlaza en el [sistema de configuración][configuration] de ASP.NET Core 1.0. El proveedor personalizado lee la configuración de Almacén de claves en el inicio.

La aplicación Surveys carga las opciones de configuración desde las siguientes ubicaciones:

- El archivo appsettings.json
- El [almacén de secretos del usuario][user-secrets] (solo entorno de desarrollo; para pruebas)
- El entorno de hospedaje (configuración de la aplicación en aplicaciones web de Azure)
- Almacén de claves

Cada uno de estos invalida al anterior, por lo que la configuración almacenada en Almacén de claves tiene prioridad.

> [AZURE.NOTE] De forma predeterminada, el proveedor de configuración de Almacén de claves está deshabilitado. No es necesario para ejecutar la aplicación localmente. Lo habilitará en una implementación de producción.

> [AZURE.NOTE] El proveedor de Almacén de claves no se admite actualmente en .NET Core, ya que requiere el paquete [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault].

En el inicio, la aplicación lee la configuración de cada proveedor de configuración registrado y los usa para rellenar un objeto de opciones fuertemente tipado. (Para más información, consulte [Using Options and configuration objects][options] (Uso de opciones y objetos de configuración)).

## Implementación

La clase [KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] es un proveedor de configuración que se conecta al [sistema de configuración][configuration] de ASP.NET Core 1.0.

Para usar `KeyVaultConfigurationProvider`, llame al método de extensión `AddKeyVaultSecrets` en la clase de inicio:

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Observe que `KeyVaultConfigurationProvider` requiere algunas opciones de configuración, que deben almacenarse en uno de los demás orígenes de configuración.

Cuando se inicia la aplicación, `KeyVaultConfigurationProvider` enumera todos los secretos del almacén de claves. Para cada secreto, busca una etiqueta llamada 'ConfigKey'. El valor de la etiqueta es el nombre de la opción de configuración.

> [AZURE.NOTE] [Tags][key-tags] son metadatos opcionales que se almacenan con una clave. Las etiquetas se usan aquí porque los nombres de clave no pueden contener el signo de dos puntos (:).

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] Consulte [KeyVaultConfigurationProvider.cs].

## Configuración del almacén de claves en la aplicación Surveys

Requisitos previos:

- Instale los [cmdlets de Azure Resource Manager][azure-rm-cmdlets].
- Configure la aplicación Surveys, tal y como se describe en [Running the Surveys application][readme] (Ejecución de la aplicación Surveys).

Pasos generales:

1. Configurar un usuario administrador en el inquilino.
2. Configurar un certificado de cliente.
3. Cree un almacén de claves.
4. Agregar las opciones de configuración al almacén de claves.
5. Quitar las marcas de comentarios del código que habilita el almacén de claves.
6. Actualizar los secretos de usuario de la aplicación.

### Configuración de un usuario administrador

> [AZURE.NOTE] Para crear un almacén de claves, debe usar una cuenta que pueda administrar su suscripción de Azure. Además, cualquier aplicación que autorice a leer desde el almacén de claves debe registrarse en el mismo inquilino que esa cuenta.

Con esto, se asegura de que pueda crear un almacén de claves mientras tiene una sesión iniciada como un usuario del inquilino donde está registrada la aplicación Surveys.

En primer lugar, cambie el directorio asociado a la suscripción de Azure

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].

2. Haga clic en **Configuración**.

    ![Settings](media/guidance-multitenant-identity/settings.png)

3. Seleccione su suscripción a Azure.

4. Haga clic en **Editar directorio** en la parte inferior del portal.

    ![Settings](media/guidance-multitenant-identity/edit-directory.png)

5. En "Cambiar el directorio asociado", seleccione el inquilino de Azure AD donde está registrada la aplicación Surveys.

    ![Settings](media/guidance-multitenant-identity/edit-directory2.png)

6. Haga clic en el botón de flecha y complete el cuadro de diálogo.

Cree un usuario administrador en el inquilino de Azure AD donde está registrada la aplicación Surveys.

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].

2. Seleccione el inquilino de Azure AD donde está registrada la aplicación.

3. Haga clic en **Usuarios** > **Agregar usuario**.

4. En el cuadro de diálogo **Agregar usuario**, asigne el usuario al rol de administrador global.

Agregue el usuario administrador como coadministrador de su suscripción de Azure.

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].

2. Haga clic en **Configuración** y seleccione su suscripción a Azure.

3. Haga clic en **Administradores**.

4. Haga clic en **Agregar** en la parte inferior del portal.

5. Escriba el correo electrónico del usuario administrador que creó anteriormente.

6. Active la casilla de la suscripción.

7. Haga clic en la marca de verificación para completar el cuadro de diálogo.

![Adición de un coadministrador](media/guidance-multitenant-identity/co-admin.png)


### Configuración de un certificado de cliente

1. Ejecute el script de PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] de la siguiente manera:
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Para el parámetro `Subject`, escriba cualquier nombre, como "surveysapp". El script genera un certificado autofirmado y lo almacena en el almacén de certificados "usuario actual/Personal".

2. La salida del script es un fragmento de código JSON. Agréguelo al manifiesto de la aplicación web, de la siguiente manera:

    1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal] y vaya a su directorio de Azure AD.

    2. Haga clic en **Aplicaciones**.

    3. Seleccione la aplicación Surveys.

    4.	Haga clic en **Administrar manifiesto** y seleccione **Descargar manifiesto**.

    5.	Abra el archivo JSON de manifiesto con un editor de texto. Pegue la salida del script en la propiedad `keyCredentials`. El archivo debe tener un aspecto similar al siguiente:
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

3. Agregue el mismo fragmento de código JSON al manifiesto de la aplicación de API web (Surveys.WebAPI).

4. Ejecute el siguiente comando para obtener la huella digital del certificado.
    ```
    certutil -store -user my [subject]
    ```
    donde `[subject]` es el valor que especificó para el sujeto en el script de PowerShell. La huella digital se muestra en "Cert hash (SHA1)". Quite los espacios entre los números hexadecimales.

Más adelante usará la huella digital.

### Creación de un Almacén de claves

1. Ejecute el script de PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] de la siguiente manera:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Cuando se le pidan las credenciales, inicie sesión como el usuario de Azure AD que creó anteriormente. El script crea un nuevo grupo de recursos y un nuevo almacén de claves dentro de ese grupo de recursos.

    Nota: para el parámetro -Location, puede usar el siguiente comando de PowerShell para obtener una lista de regiones válidas:

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Ejecute SetupKeyVault.ps de nuevo, con los parámetros siguientes:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    donde

    - nombre de almacén de claves = el nombre que asignó al almacén de claves en el paso anterior.
    - identificador de cliente de aplicación web = identificador de cliente de la aplicación web Surveys.
    - identificador de cliente de API web = identificador de cliente de la aplicación Surveys.WebAPI.

    Ejemplo:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] Puede obtener ambas en el [Portal de administración de Azure][azure-management-portal]. Seleccione el inquilino de Azure AD, seleccione la aplicación y haga clic en **Configurar**.

    Este script autoriza a la aplicación web y a la API web a recuperar los secretos de su almacén de claves. Para más información, consulte [Introducción al Almacén de claves de Azure][authorize-app].

### Adición de opciones de configuración al almacén de claves.

1. Ejecute SetupKeyVault.ps como sigue:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    donde

    - nombre de almacén de claves = el nombre que asignó al almacén de claves en el paso anterior.
    - Nombre DNS de Redis = nombre de DNS de la instancia de caché Redis.
    - Clave de acceso de Redis = clave de acceso para la instancia de caché Redis.

    Este comando agrega un secreto al almacén de claves. El secreto es un par de nombre y valor, más una etiqueta:

    -	La aplicación no usa el nombre de clave, pero debe ser único en el almacén de claves.
    -	El valor es el de la opción de configuración, en este caso, la cadena de conexión de Redis.
    -	la etiqueta "ConfigKey" contiene el nombre de la clave de configuración.

2. En este punto, es una buena idea comprobar si se guardaron correctamente los secretos en el almacén de claves. Ejecute el siguiente comando de PowerShell:

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    El resultado debe mostrar el valor del secreto además de algunos metadatos:

    ![Salida de PowerShell](media/guidance-multitenant-identity/get-secret.png)

3. Ejecute SetupKeyVault.ps de nuevo para agregar la cadena de conexión de base de datos:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    donde `<<DB connection string>>` es el valor de la cadena de conexión de base de datos.

    Para las pruebas con la base de datos local, copie la cadena de conexión del archivo Tailspin.Surveys.Web/appsettings.json. Si lo hace, asegúrese de cambiar la doble barra diagonal inversa ('\\\ ') por una sola barra diagonal inversa. La doble barra diagonal inversa es un carácter de escape en el archivo JSON.

    Ejemplo:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### Quitar las marcas de comentarios del código que habilita el almacén de claves

1. Abra la solución Tailspin.Surveys.

2. En [Tailspin.Surveys.Web/Startup.cs][web-startup], busque el siguiente bloque de código y quite las marcas de comentarios.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. En [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup], busque el siguiente bloque de código y quite las marcas de comentarios.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. En [Tailspin.Surveys.Web/Startup.cs][web-startup], busque el código que registra `ICredentialService`. Quite las marcas de comentarios de la línea que usa `CertificateCredentialService` y marque como comentario la línea que usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Este cambio permite a la aplicación web usar [aserciones de cliente][client-assertion] para obtener tokens de acceso de OAuth. Con las aserciones de cliente, no es necesario un secreto de cliente de OAuth. También puede almacenar el secreto del cliente en el almacén de claves. Sin embargo, ambos usan un certificado de cliente por lo que, si habilita el almacén de claves, es recomendable habilitar también la aserción de cliente.

### Actualización de los secretos del usuario

En el Explorador de soluciones, haga clic con el botón derecho en el proyecto Tailspin.Surveys.Web y seleccione **Administrar secretos de usuario**. En el archivo secrets.json, elimine el código JSON existente y pegue lo siguiente:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Reemplace las entradas entre [corchetes] por los valores correctos.

- `AzureAd:ClientId`: identificador de cliente de la aplicación Surveys.
- `AzureAd:WebApiResourceId`: URI del identificador de aplicación que especificó cuando creó la aplicación Surveys.WebAPI en Azure AD.
- `Asymmetric:CertificateThumbprint`: huella digital del certificado que obtuvo anteriormente, cuando creó el certificado de cliente.
- `KeyVault:Name`: nombre de su almacén de claves.

> [AZURE.NOTE] `Asymmetric:ValidationRequired` es false porque el certificado que creó anteriormente no estaba firmado por una entidad emisora raíz. En producción, use un certificado firmado por una entidad emisora y establezca `ValidationRequired` en true.

Guarde el archivo actualizado secrets.json.

Después, en el Explorador de soluciones, haga clic con el botón derecho en el proyecto Tailspin.Surveys.WebApi y seleccione **Administrar secretos de usuario**. Elimine el código JSON existente y pegue lo siguiente:

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Reemplace las entradas entre [corchetes] y guarde el archivo secrets.json.

> [AZURE.NOTE] Para la API web, asegúrese de usar el identificador de cliente para la aplicación Surveys.WebAPI, no la aplicación Surveys.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[key-tags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[forma parte de una serie]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->