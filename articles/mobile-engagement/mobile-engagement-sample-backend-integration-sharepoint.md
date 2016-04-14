<properties 
	pageTitle="Azure Mobile Engagement: Integración con back-end" 
	description="Conecte Azure Mobile Engagement con un back-end de SharePoint para crear campañas desde SharePoint" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

# Azure Mobile Engagement: Integración de la API

En un sistema de marketing automatizado, la creación y activación de las campañas de marketing también se realiza automáticamente. Con este fin, Azure Mobile Engagement permite crear estas campañas de marketing automatizadas usando las API también.

Generalmente, los usuarios usan la interfaz front-end de Azure Mobile Engagement para crear anuncios o encuestas como parte de sus campañas de marketing. Sin embargo, cuando las campañas de marketing maduran, es necesario aprovechar los datos que están bloqueados en los sistemas back-end (por ejemplo, los sistemas CRM o CMS como SharePoint) para poder crear una canalización completamente automatizada que crea campañas en Mobile Engagement dinámicamente basándose en los datos que fluyen desde los sistemas back-end.

![][5]

Este tutorial recorre este escenario en el que un usuario empresarial de SharePoint rellena una lista de SharePoint con datos de marketing y un proceso automatizado recoge elementos de la lista y se conecta con Mobile Engagement mediante las API de REST disponibles para crear una campaña de marketing a partir de los datos de SharePoint.
 
> [AZURE.IMPORTANT] En general, puede usar este ejemplo como punto de partida para comprender cómo llamar a cualquier API de REST de Mobile Engagement, porque explica con detalle los dos aspectos clave de las llamadas a las API: la autenticación y el paso de parámetros.

## Integración de SharePoint
1. Este es el aspecto de la lista de SharePoint de ejemplo. Se usan **Title**, **Category**, **NotificationTitle**, **Message** y **URL** para crear el anuncio. Hay una columna llamada **IsProcessed** que el proceso de automatización del ejemplo usa en forma de un programa de consola. Puede ejecutar esta consola del programa como un trabajo web de Azure para poder programarla o puede usar directamente el flujo de trabajo de SharePoint en el programa para crear y activar el anuncio cuando se inserta un elemento en la lista de SharePoint. En este ejemplo se usa el programa de consola que pasa por los elementos de la lista de SharePoint y crea un anuncio en Azure Mobile Engagement para cada uno de ellos y, por último, marca **IsProcessed** como true cuando el anuncio se crea correctamente.

	![][1]

2. Estamos usando el código del ejemplo *Autenticación remota en SharePoint Online con el modelo de objetos de cliente* [aquí](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) para realizar la autenticación en la lista de SharePoint.
 
3. Una vez autenticados, realizamos un recorrido por los elementos de la lista para encontrar los elementos recién creados (que tendrán **IsProcessed** = false).

 		static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## Integración de Mobile Engagement
1.  Una vez que encontramos un elemento que requiere procesamiento, extraemos la información necesaria para crear un anuncio a partir del elemento de lista, llamamos a `CreateAzMECampaign` para crearlo y, posteriormente, a `ActivateAzMECampaign` para activarlo. Estas son básicamente llamadas de la API de REST al back-end de Mobile Engagement. 

2.  Las API de REST de The Mobile Engagement requieren un **encabezado HTTP de autorización del esquema de autenticación básica** que se compone del `ApplicationId` y la `ApiKey`, que puede obtener del portal de Azure. Asegúrese de que está usando la clave de la sección **api keys** y *no* de la sección **dk keys**.

	![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Para crear una campaña de tipo announcement, consulte la [documentación](https://msdn.microsoft.com/library/azure/mt683750.aspx). Debe asegurarse de que está especificando la campaña `kind` como *announcement* y la [carga](https://msdn.microsoft.com/library/azure/mt683751.aspx), y páselo como FormUrlEncodedContent.

		static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Una vez creado el anuncio, verá algo parecido a lo siguiente en el portal de Mobile Engagement (tenga en cuenta que State=Draft y Activated = N/A)

	![][3]

5. `CreateAzMECampaign` crea una campaña de anuncio y devuelve su identificador al llamador. `ActivateAzMECampaign` necesita este identificador como parámetro para activar la campaña.

		static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Una vez activado el anuncio, verá algo parecido a lo siguiente en el portal de contratación de Mobile Engagement:

	![][4]

7. En cuanto la campaña se activa, los dispositivos que satisfacen los criterios de la campaña empezarán a ver notificaciones.

8. También podrá observar que el elemento de lista marcado con IsProcessed = false se ha establecido en true una vez creada la campaña de anuncio.

Este ejemplo crea una campaña de anuncio simple y especifica principalmente las propiedades necesarias. Puede personalizarla mucho más en el portal usado [esta](https://msdn.microsoft.com/library/azure/mt683751.aspx) información.

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 

<!---HONumber=AcomDC_0302_2016-->