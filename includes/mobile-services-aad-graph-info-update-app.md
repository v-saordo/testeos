

Las instrucciones siguientes se aplican a la actualización de una aplicación cliente de la Tienda Windows, pero puede probar esto en cualquiera de las otras plataformas compatibles con Servicios móviles de Azure.


1. En Visual Studio, abra MainPage.xaml.cs y agregue la siguiente instrucción `using` en la parte superior del archivo.
 
        using System.Net.Http;

2. En MainPage.xaml.cs, agregue la siguiente definición de clase al espacio de nombres para ayudar a serializar la información del usuario.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. En MainPage.xaml.cs, actualice el método `AuthenticateAsync` para llamar a la API personalizada a fin de devolver información adicional acerca del usuario desde AAD.

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    // Change 'MobileService' to the name of your MobileServiceClient instance.
                    // Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


4. Guarde los cambios y compile el servicio para comprobar que no existen errores de sintaxis.

<!---HONumber=Oct15_HO3-->