<properties
	pageTitle="Introducción a la autenticación personalizada | Microsoft Azure"
	description="Obtenga información acerca de cómo autenticar usuarios con un nombre de usuario y una contraseña."
	documentationCenter="Mobile"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="mahender"/>

# Introducción a la autenticación personalizada

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


## Información general
En este tema se muestra cómo autenticar usuarios en el backend .NET de Servicios móviles de Azure emitiendo su propio token de autenticación para Servicios móviles. En este tutorial, agregará autenticación al proyecto de inicio rápido mediante un nombre de usuario y una contraseña personalizados para la aplicación.

>[AZURE.NOTE] En este tutorial se muestra un método avanzado para autenticar sus servicios móviles con credenciales personalizadas. En muchas aplicaciones, sin embargo, será más adecuado usar los proveedores de identidades sociales integrados para permitir a los usuarios que inicien sesión a través de Facebook, Twitter, Google, una cuenta Microsoft y Azure Active Directory. Si esta es la primera vez que usa autenticación en Servicios móviles, consulte el tutorial [Agregar autenticación a su aplicación].

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

>[AZURE.IMPORTANT] El objetivo de este tutorial es mostrar cómo se emite un token de autenticación para Servicios móviles. Esto no debe interpretarse como una guía de seguridad. A la hora de desarrollar una aplicación, debe ser consciente de las implicaciones de seguridad que conlleva el almacenamiento de contraseñas y debe disponer de una estrategia para controlar ataques por fuerza bruta.

## Configuración de la tabla de cuentas

Puesto que autenticación personalizada y no depende de otro proveedor de identidades, deberá almacenar la información de inicio de sesión de los usuarios. En esta sección, creará una tabla para las cuentas y configurará mecanismos de seguridad básicos. La tabla de cuentas contendrá los nombres de usuario y las contraseñas con sal y algoritmos hash. También puede incluir otros datos de los usuarios si es necesario.

1. En la carpeta **DataObjects** de su proyecto de backend, agregue una nueva entidad denominada `Account`.

2. Agregue la siguiente instrucción `using`:

		using Microsoft.WindowsAzure.Mobile.Service;

3. Sustituya la definición de clase por el siguiente código:

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }

    Esto representa una fila en una tabla nueva de Cuenta, que contiene el nombre de usuario, el valor salt del usuario y la contraseña almacenada de manera segura.

2. En la carpeta **Models**, encontrará una clase **DbContext** derivada con el nombre de su servicio móvil. Abra el contexto e incluya el código siguiente para agregar la tabla de cuentas al modelo de datos:

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]Los fragmentos de código de este tutorial usan `todoContext` como nombre del contexto. Debe actualizar los fragmentos de código para el contexto del proyecto. A continuación, va a configurar las funciones de seguridad para trabajar con estos datos.

5. Cree una clase denominada `CustomLoginProviderUtils` y agréguele la siguiente declaración `using`:

		using System.Security.Cryptography;

6. Agregue los siguientes métodos de código a la nueva clase:


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

	Esto le permite generar una nueva sal larga, agrega la capacidad para aplicar un algoritmo hash a una contraseña con sal y ofrece un modo seguro de comparar dos algoritmos hash.

## Creación del extremo de registro

Llegado este punto, dispone de todo lo necesario para comenzar a crear cuentas de usuario. En esta sección, configurará un extremo de registro para administrar nuevas solicitudes de registro. Aquí es donde impondrá nuevas directivas de nombre de usuario y contraseña, y se asegurará de que el nombre de usuario no esté ya en uso. Después almacenará la información de usuario de forma segura en la base de datos.

1. Cree la siguiente nueva clase para representar un intento de registro entrante:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Si necesita recopilar y almacenar otra información durante el registro, debe hacerlo aquí.

2. En el proyecto de back-end del servicio móvil, haga clic con el botón derecho en **Controladores**, haga clic en **Agregar** y **Controlador**, cree un nuevo **Controlador de Servicios móviles de Microsoft Azure personalizado** denominado `CustomRegistrationController` y, a continuación, agregue las siguientes instrucciones `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	En el código anterior, reemplace el marcador de posición por el espacio de nombres del proyecto.

4. Sustituya la definición de clase por el siguiente código:

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomRegistrationController : ApiController
	    {
	        public ApiServices Services { get; set; }

	        // POST api/CustomRegistration
	        public HttpResponseMessage Post(RegistrationRequest registrationRequest)
	        {
	            if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
	            }
	            else if (registrationRequest.password.Length < 8)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
	            }

	            todoContext context = new todoContext();
	            Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
	            }
	            else
	            {
	                byte[] salt = CustomLoginProviderUtils.generateSalt();
	                Account newAccount = new Account
	                {
	                    Id = Guid.NewGuid().ToString(),
	                    Username = registrationRequest.username,
	                    Salt = salt,
	                    SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
	                };
	                context.Accounts.Add(newAccount);
	                context.SaveChanges();
	                return this.Request.CreateResponse(HttpStatusCode.Created);
	            }
	        }
	    }

    No olvide reemplazar la variable *todoContext* por el nombre del **DbContext** de su proyecto. Tenga en cuenta que este controlador usa el siguiente atributo para permitir todo el tráfico a este extremo:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]A este extremo de registro puede tener acceso cualquier cliente a través de HTTP. Antes de publicar este servicio en un entorno de producción, debe implementar algún tipo de esquema para validar los registros, como una comprobación por correo electrónico o SMS. Esto puede ayudar a evitar que un usuario malintencionado cree registros fraudulentos.

## Creación del proveedor de inicio de sesión

Una de las construcciones básicas de la canalización de autenticación de Servicios móviles es el **LoginProvider**. En esta sección, creará su propio `CustomLoginProvider`. No estará conectado a la canalización como los proveedores integrados, pero proporcionará una funcionalidad muy práctica. Si usa Visual Studio 2013, quizás necesite instalar el paquete NuGet `WindowsAzure.MobileServices.Backend.Security` para agregar referencias a la clase `LoginProvider`.

1. Cree una nueva clase, `CustomLoginProvider`, que se derivará de **LoginProvider**, y agregue las siguientes instrucciones `using`:

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;

3. sustituya la definición de clase **CustomLoginProvider** por el código siguiente:

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       Si intenta generar el proyecto ahora se producirá un error. `LoginProvider` tiene tres métodos abstractos que necesita implementar, lo que hacer más adelante.

2. Cree una nueva clase denominada `CustomLoginProviderCredentials` en el mismo archivo de código.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	Esta clase representa información acerca del usuario y estará disponible en el backend a través de [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx). Si va a agregar notificaciones personalizadas, asegúrese de que se capturan en este objeto.

3. Agregue la siguiente implementación del método abstracto `ConfigureMiddleware` a **CustomLoginProvider**.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	Este método no está implementado porque **CustomLoginProvider** no se integra en la canalización de autenticación.

4. Agregue la siguiente implementación del método abstracto `ParseCredentials` a **CustomLoginProvider**.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	Este método permitirá al backend deserializar información de usuario de un token de autenticación entrante.

5. Agregue la siguiente implementación del método abstracto `CreateCredentials` a **CustomLoginProvider**.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

	Este método convierte un elemento [ClaimsIdentity] en un objeto [ProviderCredentials] que se usa en la fase de emisión de tokens de autenticación. De nuevo, puede capturar otras notificaciones adicionales en este método.

6. Abra el archivo de proyecto WebApiConfig.cs en la carpeta App\_Start y se crea la siguiente línea de código después de **ConfigOptions**:

		options.LoginProviders.Add(typeof(CustomLoginProvider));



## Creación del extremo de inicio de sesión

A continuación, cree un extremo para que sus usuarios puedan iniciar sesión. El nombre de usuario y la contraseña que reciba se comprobarán con la base de datos. En primer lugar, se aplica la sal del usuario, después se aplica un algoritmo hash a la contraseña y se comprueba si el valor entrante coincide con el de la base de datos. Si coincide, puede crear un objeto [ClaimsIdentity] y pasarlo al **CustomLoginProvider**. La aplicación de cliente recibe entonces un identificador de usuario y un token de autenticación para obtener acceso al servicio móvil.

1. En el proyecto de back-end del servicio móvil, cree la siguiente clase nueva `LoginRequest`:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	Esta clase representa un intento de inicio de sesión entrante.

2. Cree la siguiente clase `CustomLoginResult` nueva:

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }

	    }

	Esta clase representa un inicio de sesión correcto con el identificador de usuario y el token de autenticación. Tenga en cuenta que esta clase tiene la misma forma que la clase MobileServiceUser en el cliente, lo que facilita la entrega de la respuesta de inicio de sesión en un cliente con establecimiento fuerte de tipos.

2. Haga clic con el botón derecho en **Controladores**, haga clic en **Agregar** y **Controlador**, cree un nuevo **Controlador de Servicios móviles de Microsoft Azure personalizado** denominado `CustomLoginController` y, a continuación, agregue las siguientes instrucciones `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. Sustituya la definición de clase **CustomLoginController** por el código siguiente:

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }

	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);

	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       No olvide reemplazar la variable *todoContext* por el nombre del **DbContext** de su proyecto. Tenga en cuenta que este controlador usa el siguiente atributo para permitir todo el tráfico a este extremo:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT] El que utilice `CustomLoginController` en producción debe contener también una estrategia de detección de ataques por fuerza bruta. De lo contrario, la solución de inicio de sesión puede ser vulnerable a ataques.

## Configuración del servicio móvil para exigir autenticación

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## Prueba del flujo de inicio de sesión con un cliente de prueba

En la aplicación de cliente, deberá desarrollar una pantalla de inicio de sesión personalizada que tome nombres de usuario y contraseñas y los envíe como carga JSON a los extremos de registro e inicio de sesión. Para completar este tutorial, usará únicamente el cliente de prueba integrado para el extremo .NET de Servicios móviles.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de servicio móvil y, a continuación, haga clic en **Depurar** e **Iniciar nueva instancia**.

	Esto inicia una nueva instancia de depuración de su proyecto de back-end del servicio móvil. Después de que el servicio se inicie correctamente, verá una página de inicio con el mensaje **Este servicio móvil está funcionando**.

2. En la página de inicio del servicio, haga clic en **Pruébelo** y, a continuación, escriba la contraseña definida para la configuración de la aplicación **MS\_ApplicationKey** en el archivo web.config con un nombre de usuario en blanco en el cuadro de diálogo de autenticación.

3. En la página de ayuda, haga clic en el extremo **CustomRegistration** y, a continuación, haga clic en **Probar esto**.

    ![][2]

4. En el cuerpo, reemplace las cadenas de ejemplo por un nombre de usuario y una contraseña que cumplan los criterios que especificó anteriormente y, a continuación, haga clic en **Enviar**.

    ![][3]

	La respuesta debe ser **201/Creado**.

5. Haga clic en el botón Atrás del explorador y repita los pasos 2 y 3 para el extremo **CustomLogin** con el mismo nombre de usuario y contraseña que ha registrado en el paso anterior.

    ![][4]

	Debería recibir el mensaje de respuesta con un cuerpo que contenga un objeto JSON de **usuario** que disponga de *userId* y un *authenticationToken*, que es el token de autenticación de Servicios móviles generado por la autenticación personalizada. Este token es suficiente para conceder a la aplicación cliente acceso al extremo TodoItem.

	Haga una copia del valor *authenticationToken*. Esto se usará para tener acceso al extremo de TodoItem restringido.

6. Haga clic en el botón Atrás del explorador y, a continuación, en la página de documentación de la API, haga clic en **GetTables** y en **Probar esto**.

7. En el cuadro de diálogo de la solicitud GET, haga clic en el signo más junto a **Encabezados**, escriba el valor `X-ZUMO-AUTH` en el cuadro de la izquierda, pegue el valor *authenticationToken* copiado en el cuadro de la derecha y, a continuación, haga clic en **Enviar**.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	El servicio móvil debe conceder acceso al extremo y devolver un estado **200/OK** junto con una lista de TodoItems en la tabla.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT] Si elige publicar también este proyecto de servicio móvil en Azure para realizar pruebas, recuerde que los proveedores de inicio de sesión y autenticación será vulnerables a ataques. Asegúrese de que estén protegidos correctamente o que los datos de prueba que se está protegiendo no sean importantes para usted. Adopte precauciones antes de usar un esquema de autenticación personalizado para proteger un servicio de producción.

## Inicie sesión mediante autenticación personalizada desde el cliente

En esta sección se describen los pasos necesarios para tener acceso a los extremos de autenticación personalizada desde el cliente para obtener el token de autenticación necesario para tener acceso al servicio móvil. Dado que el código de cliente específico que necesita depende de su cliente, las instrucciones proporcionadas aquí son independientes de la plataforma.

>[AZURE.NOTE] Las bibliotecas de los clientes de Servicios móviles se comunican con el servicio mediante HTTPS. Dado que esta solución requiere que envíe contraseñas como texto sin formato, asegúrese de usar HTTPS cuando llame a estos extremos mediante solicitudes REST directas.

1. Cree los elementos necesarios de la interfaz de usuario en la aplicación cliente para permitir que los usuarios escriban un nombre de usuario y una contraseña.

2. Use el método **invokeApi** adecuado en **MobileServiceClient** en la biblioteca de cliente para llamar al extremo **CustomRegistration**, pasando el nombre de usuario proporcionado por el tiempo de ejecución y la contraseña en el cuerpo del mensaje.

	Solo necesita llamar al extremo **CustomRegistration** una vez para crear una cuenta para un usuario determinado, siempre y cuando tenga la información de inicio de sesión de usuario en la tabla de cuentas. Para obtener ejemplos acerca de cómo llamar a una API personalizada en las diferentes plataformas de cliente admitidas, consulte el artículo [API personalizada en Servicios móviles de Azure: SDK de cliente](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

	> [AZURE.IMPORTANT] Dado que este paso de aprovisionamiento de usuario se produce solo una vez, considere la posibilidad de crear la cuenta de usuario de algún modo fuera de banda. Para un extremo de registro público, también debe considerar la implementación de un proceso de comprobación basado en correo electrónico o SMS o alguna otra medida de seguridad para evitar la generación de las cuentas fraudulentas. Puede usar Twilio para enviar mensajes SMS desde Servicios móviles. También puede usar SendGrid para enviar correos electrónicos desde Servicios móviles. Para obtener más información sobre el uso de SendGrid, consulte [Envío de correo electrónico desde Servicios móviles con SendGrid](store-sendgrid-mobile-services-send-email-scripts.md).

3. Vuelva a usar el método **invokeApi** adecuado, esta vez para llamar al extremo **CustomLogin**, pasando el nombre de usuario proporcionado por el tiempo de ejecución y la contraseña en el cuerpo del mensaje.

	Esta vez, debe capturar los valores *userId* y *authenticationToken* devueltos en el objeto de respuesta después de un inicio de sesión correcto.

4. Use los valores *userId* y *authenticationToken* devueltos para crear un nuevo objeto **MobileServiceUser** y establecerlo como el usuario actual para su instancia **MobileServiceClient**, como se muestra en el tema [Incorporación de autenticación a la aplicación existente](mobile-services-dotnet-backend-ios-get-started-users.md). Dado que el resultado de CustomLogin tiene la misma forma que el objeto **MobileServiceUser**, deberá realizar una conversión directa del resultado.

De este modo finaliza este tutorial.


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Agregar autenticación a su aplicación]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Introducción a los Servicios móviles]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx

<!---HONumber=AcomDC_0211_2016-->