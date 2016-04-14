<properties
	pageTitle="Control de acceso basado en rol en Servicios móviles con .NET y Azure Active Directory (Tienda Windows) | Microsoft Azure"
	description="Obtenga información sobre cómo controlar el acceso basado en rol de Azure Active Directory en su aplicación de la Tienda Windows mediante un servicio móvil con un back-end .NET."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="wesmc"/>

# Control de acceso basado en rol en Servicios móviles con JavaScript y Azure Active Directory

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##Información general

Control de acceso basado en roles (RBAC) es la práctica de asignar permisos a roles que pueden tener los usuarios. Define adecuadamente los límites de lo que pueden y no pueden realizar ciertas clases de usuarios. Este tutorial le enseñará cómo agregar RBAC básico a Servicios móviles de Azure.

En este tutorial, se muestra el control de acceso basado en roles comprobando la pertenencia de cada usuario al grupo Sales definido en Azure Active Directory (AAD). La comprobación de acceso se llevará a cabo con el backend .NET de Servicios Móviles mediante la [API de REST Graph] para Azure Active Directory. Solo los usuarios que pertenecen al grupo Sales podrán consultar los datos.


>[AZURE.NOTE]La finalidad de este tutorial es ampliar sus conocimientos sobre autenticación para incluir prácticas de autorización. Debe haber completado antes el tutorial [Incorporación de autenticación a la aplicación] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continúa la actualización de la aplicación TodoItem que se usa en el tutorial [Incorporación de autenticación a la aplicación].

##Requisitos previos

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Realización del tutorial [Incorporación de autenticación a la aplicación] con el proveedor de autenticación de Azure Active Directory.




##Generación de una clave para la aplicación integrada


Durante el tutorial [Incorporación de autenticación a la aplicación], creó un registro para la aplicación integrada cuando realizó el paso [Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección, generará una clave que se usará cuando se lea información de directorios con el identificador de cliente de esa aplicación integrada.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Creación de un grupo Sales con suscripción

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



##Creación de un atributo de autorización personalizado en el servicio móvil

En esta sección, va a crear un nuevo atributo de autorización personalizado que se puede usar para realizar comprobaciones de acceso en operaciones del servicio móvil. El atributo buscará un grupo de Active Directory basándose en el nombre de rol que se le ha pasado. Después realizará comprobaciones de acceso según la pertenencia al grupo.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de back-end de .NET de servicio móvil y elija **Administrar paquetes de NuGet**.

2. En el cuadro de diálogo Administrador de paquetes de NuGet, escriba **ADAL** en los criterios de búsqueda para buscar e instalar la **biblioteca de autenticación de Active Directory** para el servicio móvil. Este tutorial se ha probado recientemente con la versión 3.3.205061641-alpha (versión preliminar) del paquete ADAL.

3. En Visual Studio, haga clic con el botón derecho en el proyecto de servicio móvil y elija **Agregar** y luego **Nueva carpeta**. Llame a la nueva carpeta **Utilities**.

4. En Visual Studio, haga clic con el botón derecho en la nueva carpeta **Utilities** y agregue un archivo de clase nuevo denominado **AuthorizeAadRole.cs**.

    ![][0]

5. En el archivo AuthorizeAadRole.cs, agregue las siguientes instrucciones `using` al principio del archivo.

		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using System.Web.Http.Controllers;
		using System.Web.Http.Filters;
		using Newtonsoft.Json;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.IO;

6. En AuthorizeAadRole.cs, agregue el siguiente tipo enumerado al espacio de nombres Utilities. En este ejemplo, solo vamos a trabajar con el rol **Sales**. Los demás son solo ejemplos de grupos que podría usar.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. En AuthorizeAadRole.cs, agregue la siguiente definición de clase `AuthorizeAadRole` al espacio de nombres de Utilities.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
	        private ApiServices services = null;

	        // Constants used with ADAL and the Graph REST API for AAD
	        private const string AadInstance = "https://login.windows.net/{0}";
	        private const string GraphResourceId = "https://graph.windows.net/";
	        private const string APIVersion = "?api-version=2013-04-05";

	        // App settings pulled from the Mobile Service
	        private string tenantdomain;
	        private string clientid;
	        private string clientkey;
	        private Dictionary<int, string> groupIds = new Dictionary<int, string>();

	        private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

	        // private class used to serialize the Graph REST API web response
	        private class MembershipResponse
	        {
	            public bool value;
	        }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. En AuthorizeAadRole.cs, actualice el método `InitGroupIds` de la clase `AuthorizeAadRole` con el siguiente código. Este método crea una asignación de diccionario de los identificadores de grupo para cada rol.

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. En AuthorizeAadRole.cs, actualice el método `GetAADToken` de la clase `AuthorizeAadRole`. Este método usa la configuración de aplicación almacenada en el servicio móvil para obtener un token de acceso a la AAD desde ADAL.

    >[AZURE.NOTE]ADAL para .NET incluye una memoria caché de token de forma predeterminada para ayudar a mitigar el tráfico de red adicional en Active Directory. Sin embargo, puede escribir su propia implementación de la memoria caché o deshabilitar el almacenamiento en caché por completo. Para obtener más información, consulte [ADAL para .NET].

        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);

            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. En AuthorizeAadRole.cs, actualice el método `CheckMembership` de la clase `AuthorizeAadRole`. Este método recibe un identificador de objeto del usuario. A continuación, usa la API de REST de AAD Graph para comprobar ese identificador de objeto para ver si es un identificador de miembro para el grupo asociado al rol seleccionado en la clase `AuthorizeAadRole`

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. En AuthorizeAadRole.cs, actualice el método `OnAuthorization` de la clase `AuthorizeAadRole` con el siguiente código. Este código espera que el usuario que llama al servicio móvil se haya autenticado en AAD. A continuación, obtiene el Id. de objeto AAD del usuario y comprueba la pertenencia con el grupo de Active Directory que corresponde al rol.

    >[AZURE.NOTE]Puede buscar el grupo de Active Directory por el nombre. No obstante, muchas veces, es mejor almacenar el identificador del grupo como un valor de configuración de la aplicación de servicio móvil, porque es más probable que el nombre del grupo cambie, pero el identificador permanece igual.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. Guarde los cambios en AuthorizeAadRole.cs.

##Adición de comprobación del acceso basado en rol a las operaciones de base de datos

1. En Visual Studio, expanda la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs.

2. En TodoItemController.cs, agregue una instrucción `using` para el espacio de nombres de utilities que contiene el atributo de autorización personalizado.

        using todolistService.Utilities;

3. En TodoItemController.cs, puede agregar el atributo a la clase de controlador o a métodos individuales en función de cómo desee comprobar el acceso. Si desea que todas las operaciones de controlador comprueben el acceso basándose en el mismo rol, solo tiene que agregar el atributo a la clase. Para probar este tutorial, agregue el atributo a la clase del siguiente modo:

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    Si solo quisiera comprobar el acceso en operaciones insert, update y delete, establecería el atributo solo en esos métodos, del siguiente modo:

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. Guarde TodoItemController.cs y compile el servicio móvil para comprobar que no hay errores de sintaxis.
5. Publique el servicio móvil en su cuenta de Azure.


##Prueba del acceso del cliente

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Incorporación de autenticación a la aplicación]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registro para usar un inicio de sesión de Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[API de REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[ADAL para .NET]: https://msdn.microsoft.com/library/azure/jj573266.aspx

<!---HONumber=AcomDC_1210_2015-->