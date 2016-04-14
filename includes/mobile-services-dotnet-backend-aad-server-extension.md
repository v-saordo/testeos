### (Opcional) Configurar el servicio móvil de .NET para Azure Active Directory

>[AZURE.NOTE]Estos pasos son opcionales porque solo se aplican al proveedor de inicio de sesión de Azure Active Directory.

1. Instale el paquete [NuGet WindowsAzure.MobileServices.Backend.Security](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security).

2. En Visual Studio expanda App\_Start y abra WebApiConfig.cs. Agregue la siguiente instrucción `using` en la parte superior:

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. También en WebApiConfig.cs, agregue el código siguiente al método `Register`, inmediatamente después de que se cree una instancia de `options`:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=Oct15_HO3-->