## Obtener un token de administrador de recursos

Azure Active Directory debe autenticar todas las tareas que se realizan en los recursos mediante el Administrador de recursos de Azure. En este ejemplo se usa la autenticación por contraseña, para otros enfoques, consulte [Solicitudes de autenticación del Administrador de recursos de Azure][lnk-authenticate-arm].

1. Agregue el código siguiente al método **Main** en Program.cs para recuperar un token de Azure AD mediante el identificador y la contraseña de la aplicación.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Cree un objeto **ResourceManagementClient** que use el token, agregando el código siguiente al final del método **Main**:

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Cree u obtenga una referencia al grupo de recursos que está usando:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

<!---HONumber=AcomDC_0218_2016-->