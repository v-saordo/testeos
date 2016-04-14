<properties
	pageTitle="Programación de tareas back-end en un servicio móvil back-end de .NET | Microsoft Azure"
	description="Use el programador de Servicios móviles de Azure para definir trabajos back-end de .NET que se ejecutan según una programación."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>

# Programación de trabajos periódicos en Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [Javascript backend](mobile-services-schedule-recurring-tasks.md)

Este tema le muestra cómo usar la funcionalidad del programador de trabajos en el Portal de Azure clásico para definir el código de script de servidor que se ejecuta según la programación que establezca. En este caso, se realiza una comprobación periódica del script con un servicio remoto (Twitter) y se almacenan los resultados en una nueva tabla. Entre las demás tareas periódicas que pueden programarse se incluyen las siguientes:

+ Archivado de registros de datos antiguos o duplicados.
+ Solicitud y almacenamiento de datos externos, como tweets, entradas RSS e información de ubicación.
+ Procesamiento o cambio de tamaño de imágenes almacenadas.

Este tutorial le guiará en el uso del programador de trabajos para crear un trabajo programado que solicite datos de tweets de Twitter y almacene los tweets en una nueva tabla de actualizaciones:

>[AZURE.NOTE]Este tutorial usa la biblioteca de terceros LINQ to Twitter a fin de simplificar el acceso de OAuth 2.0 a las API de Twitter v1.1 . Para completar el tutorial, debe descargar e instalar el paquete de NuGet LINQ to Twitter. Para obtener más información, consulte el [proyecto de CodePlex de LINQ to Twitter].

##<a name="get-oauth-credentials"></a>Registro para obtener acceso a las API de Twitter v1.1 y almacenamiento de credenciales

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

&nbsp;&nbsp;7. En el Explorador de soluciones de Visual Studio, abra el archivo web.config del proyecto de servicio móvil, busque los valores de las claves `MS_TwitterConsumerKey` y `MS_TwitterConsumerSecret` de la aplicación y reemplácelos por los valores de clave de usuario y secreto de usuario de Twitter que ha establecido en el portal.

&nbsp;&nbsp;8. En la misma sección, agregue los siguientes nuevos valores de configuración, reemplazando los marcadores de posición por los valores de secreto de token de acceso y token de acceso de Twitter que ha establecido como valores de la aplicación en el portal:

	<add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
	<add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

El servicio móvil usa estos valores de configuración almacenados cuando se ejecuta en el equipo local, lo que le permite probar el trabajo programado antes de publicarlo. Sin embargo, al ejecutarse en Azure, el servicio móvil usa los valores establecidos en el portal e ignora esta configuración del proyecto.

##<a name="install-linq2twitter"></a>Descarga e instalación de la biblioteca LINQ en Twitter

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, busque `linq2twitter`, haga clic en **Instalar** en el paquete **linqtotwitter** y lea y acepte los contratos de licencia.

  	![][1]

  	De esta forma se agrega la biblioteca Linq to Twitter al proyecto de servicio móvil.

A continuación, tendrá que crear una nueva tabla en la que almacenar tweets.

##<a name="create-table"></a>Creación de la nueva tabla de actualizaciones

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la carpeta DataObjects, expanda **Agregar**, haga clic en **Clase**, escriba `Updates` en **Nombre** y luego haga clic en **Agregar**.

	De esta forma se crea un nuevo archivo de proyecto para la clase Updates.

2. Haga clic con el botón derecho en **Referencias** > **Agregar referencia...** > **Framework** en **Ensamblados**, active **System.ComponentModel.DataAnnotations** y haga clic en **Aceptar**.

	![][7]

	De esta forma, se agrega una nueva referencia de ensamblado.

2. En esta nueva clase, agregue la siguiente instrucción **using**:

		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. Sustituya la definición de clase **Updates** por el siguiente código:

		public class Updates
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. Expanda la carpeta Modelos, abra el archivo de contexto del modelo de datos (denominado *nombre\_servicio*Context.cs) y agregue la siguiente propiedad, que devuelve un objeto **DbSet** con tipo:

		public DbSet<Updates> Updates { get; set; }

	La tabla de actualizaciones (Updates), que se crea en la base de datos al obtener acceso a DbSet por primera vez, se usa en el servicio para almacenar los datos de tweets.

	>[AZURE.NOTE] Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear siempre que detecte un cambio del modelo de datos en la definición del modelo de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe usar Migraciones de Code First. El inicializador predeterminado no se puede usar con una base de datos SQL en Azure. Para obtener más información, vea [Uso de Migraciones de Code First para actualizar el modelo de datos](mobile-services-dotnet-backend-use-code-first-migrations.md).

A continuación, cree el trabajo programado que obtiene acceso a Twitter y almacena los datos de tweets en la nueva tabla de actualizaciones.

##<a name="add-job"></a>Creación de un nuevo trabajo programado

1. Expanda la carpeta ScheduledJobs y abra el archivo de proyecto SampleJob.cs.

	Esta clase, que se hereda de **ScheduledJob**, representa un trabajo que se puede programar en el Portal de Azure clásico para ejecutarse con una programación fija o a petición.

2. Reemplace el contenido de SampleJob.cs por el código siguiente.

		using System;
		using System.Linq;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Web.Http;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
		using LinqToTwitter;
		using todolistService.Models;
		using todolistService.DataObjects;

		namespace todolistService
		{
		    // A simple scheduled job which can be invoked manually by submitting an HTTP
		    // POST request to the path "/jobs/sample".
		    public class SampleJob : ScheduledJob
		    {
		        private todolistContext context;
		        private string accessToken;
		        private string accessTokenSecret;

		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
					CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);

		            // Create a new context with the supplied schema name.
		            context = new todolistContext();
		        }

		        public async override Task ExecuteAsync()
		        {
		            // Try to get the stored Twitter access token from app settings.
		            if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
		            Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
		            {
		                Services.Log.Error("Could not retrieve Twitter access credentials.");
		            }

		            // Create a new authorizer to access Twitter v1.1 APIs
		            // using single-user OAUth 2.0 credentials.
		            MvcAuthorizer auth = new MvcAuthorizer();
		            SingleUserInMemoryCredentialStore store =
		                new SingleUserInMemoryCredentialStore()
		            {
		                ConsumerKey = Services.Settings.TwitterConsumerKey,
		                ConsumerSecret = Services.Settings.TwitterConsumerSecret,
		                OAuthToken = accessToken,
		                OAuthTokenSecret = accessTokenSecret
		            };

		            // Set the credentials for the authorizer.
		            auth.CredentialStore = store;

		            // Create a new LINQ to Twitter context.
		            TwitterContext twitter = new TwitterContext(auth);

		            // Get the ID of the most recent stored tweet.
		            long lastTweetId = 0;
		            if (context.Updates.Count() > 0)
		            {
		                lastTweetId = (from u in context.Updates
		                               orderby u.TweetId descending
		                               select u).Take(1).SingleOrDefault()
		                                            .TweetId;
		            }

		            // Execute a search that returns a filtered result.
		            var response = await (from s in twitter.Search
		                                  where s.Type == SearchType.Search
		                                  && s.Query == "%23mobileservices"
		                                  && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
		                                  && s.ResultType == ResultType.Recent
		                                  select s).SingleOrDefaultAsync();

		            // Remove retweets and replies and log the number of tweets.
		            var filteredTweets = response.Statuses
		                .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
		            Services.Log.Info("Fetched " + filteredTweets.Count()
		                + " new tweets from Twitter.");

		            // Store new tweets in the Updates table.
		            foreach (Status tweet in filteredTweets)
		            {
		                Updates newTweet =
		                    new Updates
		                    {
		                        TweetId = Convert.ToInt64(tweet.StatusID),
		                        Text = tweet.Text,
		                        Author = tweet.User.Name,
		                        Date = tweet.CreatedAt
		                    };

		                context.Updates.Add(newTweet);
		            }

		            await context.SaveChangesAsync();
		        }
		        protected override void Dispose(bool disposing)
		        {
		            base.Dispose(disposing);
		            if (disposing)
		            {
		                context.Dispose();
		            }
		        }
		    }
		}

	En el código anterior, debe reemplazar las cadenas _todolistService_ y _todolistContext_ por el espacio de nombres y DbContext del proyecto descargado, que son *mobile&#95;service&#95;name*Service y *mobile&#95;service&#95;name*Context, respectivamente.

	En el código anterior, el método de invalidación **ExecuteAsync** llama a la API de consulta de Twitter mediante las credenciales almacenadas para solicitar los tweets recientes que contienen el hashtag `#mobileservices`. Las respuestas y tweets duplicados se quitan de los resultados antes de almacenarse en la tabla.

##<a name="run-job-locally"></a>Prueba local del trabajo programado

Los trabajos programados se pueden probar de forma local antes de publicarlos en Azure y registrarlos en el portal.

1. En Visual Studio, con el proyecto de servicio móvil establecido como proyecto de inicio, presione F5.

	De esta forma, se inicia dicho proyecto y se abre una nueva ventana del explorador con la página de bienvenida.

2. Haga clic en **probarlo** y luego en **POST jobs/{jobName}**.

	![][8]

4. Haga clic en **probar esto**, escriba `Sample` como valor del parámetro **{jobName}** y haga clic en **Enviar**.

	![][9]

	De este modo, se envía una solicitud POST al extremo del trabajo de ejemplo. En el servicio local, se inicia el método **ExecuteAsync**. Puede establecer un punto de interrupción en este método para depurar el código.

3. En el Explorador de servidores, expanda **Conexiones de datos**, **MSTableConnectionString** y **tablas**. Haga clic con el botón secundario en **Updates** y después haga clic en **Mostrar datos de tabla**.

	Los nuevos tweets se introducen como filas en la tabla de datos.

##<a name="register-job"></a>Publicación del servicio y registro del nuevo trabajo

El trabajo debe registrarse en la pestaña **Programador** para que los Servicios móviles puedan ejecutarlo en la programación que defina.

3. Vuelva a publicar el proyecto de servicio móvil en Azure.

4. En el [Portal de Azure clásico], haga clic en Servicios móviles y en su aplicación.

2. Haga clic en la pestaña **Programador** y, a continuación, en **+Crear**.

    >[AZURE.NOTE]Cuando ejecute el servicio móvil en el nivel <em>Gratis</em>, solo podrá ejecutar un trabajo programado a la vez. En los niveles de pago, puede ejecutar hasta diez trabajos programados a la vez.

3. En el cuadro de diálogo del programador, especifique _Sample_ para **Nombre de trabajo**, establezca el intervalo de programación y las unidades, y haga clic en el botón de comprobación.

   	![][4]

   	Se crea un nuevo trabajo llamado **Sample**.

4. Haga clic en el nuevo trabajo que acaba de crear y, después, haga clic en **Ejecutar una vez** para probar el script.

   	De esta forma, se ejecuta el trabajo mientras se mantiene deshabilitado en el programador. Desde esta página, puede habilitar el trabajo y cambiar su programación en cualquier momento.

	>[AZURE.NOTE]Puede seguir utilizándose una solicitud POST para iniciar el trabajo programado. Sin embargo, la autorización recae de forma predeterminada en el usuario, lo que significa que la solicitud debe incluir la clave de la aplicación en el encabezado.

4. (Opcional) En el [Portal de Azure clásico], haga clic en Administrar en la base de datos asociada con el servicio móvil.

    ![][6]

5. Asimismo, en el Portal de Azure clásico, ejecute una consulta para ver los cambios realizados por la aplicación. La consulta puede ser similar a la siguiente, pero use el nombre del servicio móvil como nombre de esquema en lugar de `todolist`.

        SELECT * FROM [todolist].[Updates]

Enhorabuena, ha creado correctamente un nuevo trabajo programado en el servicio móvil. Este trabajo se ejecutará como programado hasta que lo deshabilite o modifique.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[proyecto de CodePlex de LINQ to Twitter]: http://linqtotwitter.codeplex.com/

<!---HONumber=AcomDC_0211_2016-->