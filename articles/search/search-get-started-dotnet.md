<properties
	pageTitle="Introducción a su primera aplicación de Búsqueda de Azure en .NET | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Tutorial sobre cómo crear una solución de Visual Studio usando la biblioteca de cliente .NET desde el SDK de .NET para Búsqueda de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Comenzar con la primera aplicación de Búsqueda de Azure en .NET
> [AZURE.SELECTOR]
- [.NET](search-get-started-dotnet.md)
- [Portal](search-get-started-portal.md)
 
Aprenda a crear una aplicación de búsqueda de .NET personalizada en Visual Studio 2013 o posterior que usa Búsqueda de Azure para la experiencia de búsqueda. Este tutorial utiliza tanto el [SDK de .NET de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx), como la API de REST del servicio Búsqueda de Azure.

Para ejecutar este ejemplo, necesitará un servicio Búsqueda de Azure, al que puede suscribirse en el [Portal de Azure](https://portal.azure.com). Para obtener instrucciones detalladas, consulte [Creación de un servicio Búsqueda de Azure en el Portal de Azure](search-create-service-portal.md).

## Acerca de los datos

Esta aplicación de ejemplo usa los datos del [Servicio geológico de Estados Unidos (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados por el estado de Rhode Island, para reducir el tamaño del conjunto de datos. Vamos a usar estos datos para crear una aplicación de búsqueda que devuelva edificios de referencia, como hospitales y escuelas, además de características geológicas como ríos, lagos y montes.

En esta aplicación, el programa **DataIndexer** compila y carga el índice mediante un [indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx), con lo que recupera el conjunto de datos de USGS filtrado de una Base de datos SQL de Azure pública. En el código fuente se proporcionan las credenciales e información de conexión al origen de datos en línea. No es necesario realizar ninguna otra configuración.

> [AZURE.NOTE] Se aplicó un filtro a este conjunto de datos para no sobrepasar el límite de 10.000 documentos del nivel de precios gratuito. Si utiliza un plan de tarifa diferente, este límite no se aplica. Para obtener más información acerca de la capacidad de cada nivel de precios, consulte [Límites y restricciones](search-limits-quotas-capacity.md).

<a id="sub-2"></a>
## Buscar el nombre del servicio y las claves de API del servicio Búsqueda de Azure ##

Después de crear el servicio, vuelva al portal para obtener la dirección URL o la `api-key`. Las conexiones con el servicio de búsqueda requieren que tenga la URL y una `api-key` para autenticar la llamada.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra de salto, haga clic en **Servicio de búsqueda** para enumerar todos los servicios de Búsqueda de Azure aprovisionados para la suscripción.
3. Seleccione el servicio que desea utilizar.
4. En el panel del servicio verá mosaicos con información esencial, así como el icono de llave para tener acceso a las claves de administrador.

   ![][3]

3. Copie la dirección URL del servicio y una clave de administración. Las necesitará más adelante, cuando las agregue a los archivos app.config y web.config en los proyectos de Visual Studio.

## Iniciar un nuevo proyecto y solución

Esta solución incluye dos proyectos:

- **DataIndexer**, una aplicación de consola de Visual C# que se usa para cargar datos.
- **SimpleSearchMVCApp**, una aplicación web ASP.NET MVC de Visual C# que se usa para consultar y devolver resultados de búsqueda.

En este paso, creará ambos proyectos.

1. Inicie **Visual Studio** > **Nuevo proyecto** > **Visual C#** > **Aplicación de consola**.
2. Asigne el nombre **DataIndexer** al proyecto y, después, asigne el nombre **AzureSearchDotNetDemo** a la solución.
3. En el Explorador de soluciones, en la solución, haga clic con el botón derecho en **Agregar** > **Nuevo proyecto** > **Visual C#** > **Aplicación web ASP.NET**.
4. Asigne el nombre **SimpleSearchMVCApp** al proyecto.
5. En el nuevo proyecto de ASP.NET, elija la plantilla MVC y desactive las opciones para evitar crear artefactos de programa que no va a usar en este tutorial.

   Desactive las casillas de hospedaje de Azure y pruebas unitarias, y establezca la autenticación en ninguna.

   ![][10]

Cuando haya terminado de crear los proyectos, la solución tendrá un aspecto similar al del ejemplo siguiente.

   ![][4]

## Instalar la biblioteca de cliente .NET y actualizar otros paquetes

1. Haga clic con el botón secundario en **Administrar paquetes de NuGet** en la solución, en el Explorador de soluciones.
2. Especifique **Actualizaciones** > **Solo estable** > **Actualizar todo**.

   ![][11]

3. Acepte las instalaciones de paquetes adicionales para que se instalen también todas las dependencias.

4. Después, instale la biblioteca de cliente .NET para Búsqueda de Azure. Asegúrese de especificar la búsqueda correctamente o no encontrará fácilmente el paquete. Haga clic con el botón secundario en **Administrar paquetes de NuGet** nuevamente.

5. Especifique **En línea** > **nuget.org** > **Solo estable** y, después, busque *azure.search*. Haga clic en **Instalar** para instalar la biblioteca.

   ![][12]

## Agregar una referencia de ensamblado para System.Configuration

**DataIndexer** usa **System.Configuration** para leer los valores de configuración de app.config.

1. Haga clic en **DataIndexer** > **Agregar** > **Referencia** > **Framework** > **System.Configuration**. Active la casilla.
2. Haga clic en **Aceptar**.

## Actualizar los archivos de configuración

Cada proyecto incluye archivos de configuración que especifican el nombre del servicio y la clave de API.

1. En **DataIndexer**, reemplace App.config por el siguiente ejemplo y actualice [SERVICE NAME] y [SERVICE KEY] con valores que sean válidos para el servicio. Tenga en cuenta que el nombre del servicio no es la dirección URL completa. Por ejemplo, si el extremo de servicio de Búsqueda es **https://mysearchsrv.search.microsoft.net*, el nombre de servicio que escribirá en App.config es *mysearchsrv*.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		    <appSettings>
		      <add key="SearchServiceName" value="[SEARCH SERVICE]" />
		      <add key="SearchServiceApiKey" value="[SEARCH SERVICE API KEY]" />
		    </appSettings>
		</configuration>

2. En **SimpleSearchMVCApp**, reemplace Web.config por el ejemplo siguiente y vuelva a actualizar [SERVICE NAME] y [SERVICE KEY] con valores que sean válidos para el servicio.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
			For more information on how to configure your ASP.NET application, please visit
			http://go.microsoft.com/fwlink/?LinkId=152368
			-->
		<configuration>
			<configSections>
				<!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
				<section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
			</configSections>
			<connectionStrings>
				<add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
			</connectionStrings>
			<appSettings>
				<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
				<add key="SearchServiceApiKey" value="[API KEY]" />

				<add key="webpages:Version" value="2.0.0.0" />
				<add key="webpages:Enabled" value="false" />
				<add key="PreserveLoginUrl" value="true" />
				<add key="ClientValidationEnabled" value="true" />
				<add key="UnobtrusiveJavaScriptEnabled" value="true" />
			</appSettings>
			<system.web>
				<httpRuntime targetFramework="4.5" />
				<compilation debug="true" targetFramework="4.5" />
				<authentication mode="Forms">
					<forms loginUrl="~/Account/Login" timeout="2880" />
				</authentication>
				<pages>
					<namespaces>
						<add namespace="System.Web.Helpers" />
						<add namespace="System.Web.Mvc" />
						<add namespace="System.Web.Mvc.Ajax" />
						<add namespace="System.Web.Mvc.Html" />
						<add namespace="System.Web.Optimization" />
						<add namespace="System.Web.Routing" />
						<add namespace="System.Web.WebPages" />
					</namespaces>
				</pages>
				<profile defaultProvider="DefaultProfileProvider">
					<providers>
						<add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</profile>
				<membership defaultProvider="DefaultMembershipProvider">
					<providers>
						<add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
					</providers>
				</membership>
				<roleManager defaultProvider="DefaultRoleProvider">
					<providers>
						<add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</roleManager>
				<!--
								If you are deploying to a cloud environment that has multiple web server instances,
								you should change session state mode from "InProc" to "Custom". In addition,
								change the connection string named "DefaultConnection" to connect to an instance
								of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
					-->
				<sessionState mode="InProc" customProvider="DefaultSessionProvider">
					<providers>
						<add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
					</providers>
				</sessionState>
			</system.web>
			<system.webServer>
				<validation validateIntegratedModeConfiguration="false" />
				<handlers>
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
					<remove name="ExtensionlessUrlHandler-Integrated-4.0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
					<remove name="OPTIONSVerbHandler" />
					<remove name="TRACEVerbHandler" />
				</handlers>
			</system.webServer>
			<entityFramework>
				<defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
					<parameters>
						<parameter value="v12.0" />
					</parameters>
				</defaultConnectionFactory>
			</entityFramework>
			<runtime>
				<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
					<dependentAssembly>
						<assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-7.0.0.0" newVersion="7.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
				</assemblyBinding>
			</runtime>
		</configuration>


## Modificar DataIndexer

Este programa es una aplicación de consola que se conecta con el servicio de búsqueda (tal y como se especifica en app.config), crea el índice y, después, lo carga con el conjunto de datos de USGS almacenado en una Base de datos SQL de Azure. En esta parte del tutorial usaremos un indexador.

Para poder ejecutar este programa, es preciso reemplazar **Program.cs**, que se usa para crear el índice y el indexador, cargar datos y escribir mensajes.

### Actualizar Program.cs

1. En el Explorador de soluciones, abra **DataIndexer** > **Program.cs**.
2. Reemplace el contenido de Program.cs por el código siguiente.

	    using System;
	    using System.Configuration;
	    using System.Threading;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace DataIndexer
	    {
	        class Program
	        {
	            private const string GeoNamesIndex = "geonames";
	            private const string UsgsDataSource = "usgs-datasource";
	            private const string UsgsIndexer = "usgs-indexer";
	
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            // This Sample shows how to delete, create, upload documents and query an index
	            static void Main(string[] args)
	            {
	                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                // Create an HTTP reference to the catalog index
	                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                _indexClient = _searchClient.Indexes.GetClient(GeoNamesIndex);
	
	                Console.WriteLine("{0}", "Deleting index, data source, and indexer...\n");
	                if (DeleteIndexingResources())
	                {
	                    Console.WriteLine("{0}", "Creating index...\n");
	                    CreateIndex();
	                    Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
	                    SyncDataFromAzureSQL();
	                }
	                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
	                Console.ReadKey();
	            }
	
	            private static bool DeleteIndexingResources()
	            {
	                // Delete the index, data source, and indexer.
	                try
	                {
	                    _searchClient.Indexes.Delete(GeoNamesIndex);
	                    _searchClient.DataSources.Delete(UsgsDataSource);
	                    _searchClient.Indexers.Delete(UsgsIndexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error deleting indexing resources: {0}\r\n", ex.Message);
	                    Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
	                    return false;
	                }
	
	                return true;
	            }
	
	            private static void CreateIndex()
	            {
	                // Create the Azure Search index based on the included schema
	                try
	                {
	                    var definition = new Index()
	                    {
	                        Name = GeoNamesIndex,
	                        Fields = new[] 
	                        { 
	                            new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
	                        }
	                    };
	
	                    _searchClient.Indexes.Create(definition);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating index: {0}\r\n", ex.Message);
	                }
	
	            }
	
	            private static void SyncDataFromAzureSQL()
	            {
	                // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
	                Console.WriteLine("{0}", "Creating Data Source...\n");
	                var dataSource =
	                    new DataSource()
	                    {
	                        Name = UsgsDataSource,
	                        Description = "USGS Dataset",
	                        Type = DataSourceType.AzureSql,
	                        Credentials = new DataSourceCredentials("Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;"),
	                        Container = new DataContainer("GeoNamesRI")
	                    };
	
	                try
	                {
	                    _searchClient.DataSources.Create(dataSource);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating data source: {0}", ex.Message);
	                    return;
	                }
	
	                Console.WriteLine("{0}", "Creating Indexer and syncing data...\n");
	
	                var indexer =
	                    new Indexer()
	                    {
	                        Name = UsgsIndexer,
	                        Description = "USGS data indexer",
	                        DataSourceName = dataSource.Name,
	                        TargetIndexName = GeoNamesIndex
	                    };
	
	                try
	                {
	                    _searchClient.Indexers.Create(indexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating and running indexer: {0}", ex.Message);
	                    return;
	                }
	
	                bool running = true;
	                Console.WriteLine("{0}", "Synchronization running...\n");
	                while (running)
	                {
	                    IndexerExecutionInfo status = null;
	
	                    try
	                    {
	                        status = _searchClient.Indexers.GetStatus(indexer.Name);
	                    }
	                    catch (Exception ex)
	                    {
	                        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
	                        return;
	                    }
	
	                    IndexerExecutionResult lastResult = status.LastResult;
	                    if (lastResult != null)
	                    {
	                        switch (lastResult.Status)
	                        {
	                            case IndexerExecutionStatus.InProgress:
	                                Console.WriteLine("{0}", "Synchronization running...\n");
	                                Thread.Sleep(1000);
	                                break;
	
	                            case IndexerExecutionStatus.Success:
	                                running = false;
	                                Console.WriteLine("Synchronized {0} rows...\n", lastResult.ItemCount);
	                                break;
	
	                            default:
	                                running = false;
	                                Console.WriteLine("Synchronization failed: {0}\n", lastResult.ErrorMessage);
	                                break;
	                        }
	                    }
	                }
	            }
	        }
	    }


## Generar y ejecutar DataIndexer

1. Haga clic con el botón secundario en el proyecto **DataIndexer**.
2. Compile el proyecto.
3. Presione **F5** para ejecutarlo.

Verá una ventana de consola con estos mensajes.

![][6]

En el portal, verá un nuevo índice **geonames**. Como el portal puede tardar varios minutos en ponerse al día, quizás quiera esperar un poco antes de comprobar los resultados.

![][7]

## Modificar SimpleSearchMVCApp

**SimpleSearchMVC** es una aplicación web que se ejecuta localmente en IIS Express. Proporciona un cuadro de búsqueda y muestra los resultados de búsqueda en una tabla.

Antes de ejecutar este programa, hará tres modificaciones:

- Reemplazar **HomeController.cs**, que se usa para aceptar la entrada del usuario. En este ejemplo, el término de búsqueda se almacena en una variable llamada *q*.

- Reemplazar **index.cshtml**, una página web que proporciona las entradas de términos de búsqueda y muestra los resultados de búsqueda.

- Agregar **FeatureSearch.cs**, una clase que crea el cliente de búsqueda y ejecuta la búsqueda.

### Actualizar HomeController.cs

Reemplace el código predeterminado por el siguiente.

	    using Microsoft.Azure.Search.Models;
	    using System;
	    using System.Collections.Generic;
	    using System.Linq;
	    using System.Web;
	    using System.Web.Mvc;
	
	    namespace SimpleSearchMVCApp.Controllers
	    {
	        public class HomeController : Controller
	        {
	            //
	            // GET: /Home/
	            private FeaturesSearch _featuresSearch = new FeaturesSearch();
	
	            public ActionResult Index()
	            {
	                return View();
	            }
	
	            public ActionResult Search(string q = "")
	            {
	                // If blank search, assume they want to search everything
	                if (string.IsNullOrWhiteSpace(q))
	                    q = "*";
	
	                return new JsonResult
	                {
	                    JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                    Data = _featuresSearch.Search(q).Results
	                };
	            }
	        }
	    }


### Actualizar Index.cshtml

Reemplace el código predeterminado por el siguiente.

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


### Agregue FeaturesSearch.cs

Agregue una clase que proporcione la funcionalidad de búsqueda a la aplicación.

1. En el Explorador de soluciones, haga clic en **SimpleSearchMVCApp** > **Agregar** > **Nuevo elemento** > **Código** > **Clase**.
2. Asigne el nombre **FeaturesSearch** a la clase.
3. Reemplace el código predeterminado por el siguiente.


	    using System;
	    using System.Configuration;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace SimpleSearchMVCApp
	    {
	        public class FeaturesSearch
	        {
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            public static string errorMessage;
	
	            static FeaturesSearch()
	            {
	                try
	                {
	                    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                    // Create an HTTP reference to the catalog index
	                    _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                    _indexClient = _searchClient.Indexes.GetClient("geonames");
	                }
	                catch (Exception e)
	                {
	                    errorMessage = e.Message.ToString();
	                }
	            }
	
	            public DocumentSearchResult Search(string searchText)
	            {
	                // Execute search based on query string
	                try
	                {
	                    SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
	                    return _indexClient.Documents.Search(searchText, sp);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
	                }
	                return null;
	            }
	
	        }
	    }


### Establecer SimpleSearchMVCApp como proyecto de inicio

Haga clic con el botón secundario en el proyecto **SimpleSearchMVCApp** para establecerlo como proyecto de inicio.

## Compilar y ejecutar SimpleSearchMVCApp

Al compilar y ejecutar este programa, en el explorador predeterminado verá una página web que proporciona un cuadro de búsqueda para acceder a los datos de USGS almacenados en el índice en el servicio Búsqueda de Azure.

![][8]

### Buscar en los datos de USGS

El conjunto de datos de USGS incluye los registros que son relevantes para el estado de Rhode Island. Si hace clic en **Search** en un cuadro de búsqueda vacío, obtendrá las 50 primeras entradas (es el valor predeterminado).

Al escribir un término de búsqueda se proporcionan al motor de búsqueda criterios para delimitar la búsqueda. Pruebe a escribir un nombre regional. *Roger Williams* fue el primer gobernador de Rhode Island. Hay numerosos parques, edificios y escuelas que llevan su nombre.

![][9]

También puede probar estas consultas, agregando o quitando frases u operadores para redefinir el ámbito de la búsqueda:

- Pawtucket OR Pembroke
- goose +cape -neck


## Pasos siguientes

Este es el primer tutorial de Búsqueda de Azure basado en .NET y en el conjunto de datos de USGS. Con el tiempo, ampliaremos este tutorial para mostrar otras características de búsqueda que podría querer usar en sus soluciones personalizadas.

Si ya tiene conocimientos sobre Búsqueda de Azure, puede usar este ejemplo como punto de partida para probar proveedores de sugerencias (escritura automática o autocompletar consultas), filtros y navegación por facetas. También puede mejorar la página de resultados de búsqueda si agrega recuentos y procesamiento por lotes de documentos para que los usuarios puedan navegar por las páginas de resultados.

¿Es la primera vez que usa Búsqueda de Azure? Le recomendamos que pruebe otros tutoriales para comprender mejor lo que puede crear. Visite nuestra [página de documentación](https://azure.microsoft.com/documentation/services/search/) para encontrar más recursos. También puede ver los vínculos en nuestra [lista de vídeos y tutoriales](search-video-demo-tutorial-list.md) para tener acceso a más información.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG

<!---HONumber=AcomDC_0224_2016-->