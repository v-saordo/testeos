<properties 
	pageTitle="Uso de la red CDN de Azure del Servicio de aplicaciones de Azure" 
	description="Un tutorial que le enseña cómo implementar una aplicación web en el Servicio de aplicaciones de Azure que ofrece contenido de un extremo de la red CDN de Azure integrado" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="cephalin"/>


# Uso de la red CDN de Azure del Servicio de aplicaciones de Azure

[Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) puede integrarse con [CDN de Azure](/services/cdn/), agregándolo a las capacidades de escalado globales inherentes en [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) sirviendo el contenido del sitio web globalmente desde los nodos de servidor cercan de sus clientes ([aquí](http://msdn.microsoft.com/library/azure/gg680302.aspx) se puede encontrar una lista actualizada de todas las ubicaciones de nodo actuales). En escenarios como entrega de imágenes estáticas, esta integración puede aumentar considerablemente el rendimiento de Aplicaciones web del Servicio de aplicaciones de Azure y mejorar significativamente la experiencia de usuario de su aplicación web en todo el mundo.

La integración de Aplicaciones web con la red CDN de Azure le ofrece las siguientes ventajas:

- Integración de la implementación de contenido (imágenes, scripts y hojas de estilo) como parte del proceso de [implementación continua de su aplicación web](web-sites-publish-source-control.md)
- Actualización sencilla de los paquetes de NuGet de la aplicación web en el Servicio de aplicaciones de Azure, como versiones de jQuery o Bootstrap 
- Administración de la aplicación web y del contenido servido por CDN desde la misma interfaz de Visual Studio
- Integración de unión y minificación de ASP.NET con CDN de Azure

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Lo que va a crear ##

Se implementará una aplicación web en el Servicio de aplicaciones de Azure mediante la plantilla predeterminada de ASP.NET MVC en Visual Studio, agregaremos código para servir contenido desde una red CDN de Azure integrada (como una imagen, los resultados de las acciones de controlador y los archivos predeterminados de JavaScript y CSS) y también escribiremos código para configurar el mecanismo de reserva de los paquetes servidos en caso de desconexión de CDN.

## Qué necesita ##

Este tutorial cuenta con los siguientes requisitos previos:

-	Una [cuenta de Microsoft Azure activa](/account/)
-	Visual Studio 2015 con [SDK de Azure para .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) Si utiliza Visual Studio, los pasos pueden variar.

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure:
> + Puede [abrir una cuenta de Azure de manera gratuita](/pricing/free-trial/): obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este podrá mantener la cuenta y usar servicios gratuitos de Azure, tales como Aplicaciones web.
> + Puede [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/): su suscripción a Visual Studio le proporciona crédito todos los meses que puede usar con servicios de Azure de pago.
>
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Implementación de una aplicación web en Azure con un extremo de red CDN integrado ##

En esta sección, implementará la plantilla de aplicación predeterminada ASP.NET MVC de Visual Studio 2015 en el Servicio de aplicaciones y luego la integrará con un nuevo punto de conexión de red CDN. Siga las instrucciones que se describen a continuación:

1. En Visual Studio 2015, cree una nueva aplicación web ASP.NET desde la barra de menús. Para ello, vaya a **Archivo > Nuevo > Proyecto > Web > Aplicación web ASP.NET**. Asígnele un nombre y haga clic en **Aceptar**.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. Seleccione **MVC** y haga clic en **Aceptar**.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. Si aún no ha iniciado sesión en su cuenta de Azure, haga clic en el icono de la cuenta en la esquina superior derecha y siga el cuadro de diálogo para iniciar sesión en su cuenta de Azure. Cuando haya terminado, configure la aplicación como se muestra a continuación y luego haga clic en **Nuevo** para crear un nuevo plan del Servicio de aplicaciones para la aplicación.

	![](media/cdn-websites-with-cdn/3-configure-webapp.png)

5. Configure un nuevo plan del Servicio de aplicaciones en el cuadro de diálogo, como se muestra a continuación y haga clic en **Aceptar**.

	![](media/cdn-websites-with-cdn/4-app-service-plan.png)

8. Haga clic en **Crear** para crear la aplicación web.

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. Después de crear la aplicación ASP.NET, publíquela en Azure en el panel Actividad del Servicio de aplicaciones de Azue haciendo clic en **Publicar `<app name>` en esta aplicación web ahora**. Haga clic en **Publicar** para completar el proceso.

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	Verá la aplicación web publicada en el explorador cuando la publicación se complete.

1. Para crear un punto de conexión de CDN, inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **+ Nuevo** > **Multimedia y CDN** > **CDN**.

	![](media/cdn-websites-with-cdn/create-cdn-profile.png)

3. Especifique el **CDN**, la **Ubicación**, el **Grupo de recursos** y el **Plan de tarifa** y luego haga clic en **Crear**

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

4. En la hoja **Perfil de CDN**, haga clic en el botón **+ Punto de conexión**. Asígnele un nombre, seleccione **Aplicación web** en la lista desplegable **Tipo de origen** y la aplicación web en la lista desplegable **Nombre de host de origen** y luego haga clic en **Agregar**.

	![](media/cdn-websites-with-cdn/cdn-profile-blade.png)



	> [AZURE.NOTE] Después de crear el punto de conexión de CDN, la hoja **Punto de conexión** mostrará su URL de CDN y el dominio de origen con el que está integrado. Sin embargo, puede que pase un tiempo hasta que la configuración del extremo de red CDN se propague a las ubicaciones de todos los nodos de red CDN.

3. De vuelta en la hoja **Punto de conexión**, haga clic en el nombre del punto de conexión de CDN que acaba de crear.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. Elija el botón **Configurar**. En la hoja **Configurar**, seleccione **Almacenar en caché cada URL única** en la lista desplegable **Comportamiento del almacenamiento en caché de cadenas de consultas** y luego haga clic en el botón **Guardar**.


	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

Una vez realizada la habilitación, el mismo vínculo al que se accede con diferentes cadenas de consulta se almacenará en caché como entradas independientes.

>[AZURE.NOTE] Aunque habilitar la cadena de consulta no es necesario en esta sección del tutorial, querrá hacer esto lo antes posible por comodidad dado que cualquier cambio aquí realizado va a tardar en propagarse a todos los nodos de red CDN, y no deseará que el contenido no habilitado para la cadena de consulta colapse la caché de red CDN (la actualización del contenido de red CDN se tratará más adelante).

2. Ahora, vaya a la dirección del punto de conexión de CDN. Si el extremo está listo, debería ver la aplicación web. Si obtiene un error **HTTP 404**, el extremo de la red CDN no está listo. Puede que sea necesario esperar hasta una hora para que la configuración de la red CDN se propague a todos los nodos perimetrales. 

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. A continuación, intente obtener acceso al archivo **~/Content/bootstrap.css** en el proyecto ASP.NET. En la ventana del explorador, vaya a **http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css**. En nuestra instalación, esta URL es:

		http://az673227.azureedge.net/Content/bootstrap.css

	Esta URL corresponde a la siguiente URL de origen en el extremo de red CDN:

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	Cuando vaya a **http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css**, se le pedirá que descargue el archivo bootstrap.css procedente de su aplicación web de Azure.

	![](media/cdn-websites-with-cdn/12-file-access.png)

De igual forma, puede acceder a cualquier URL de acceso público en **http://*&lt;serviceName>*.cloudapp.net/**, directamente desde el extremo de CDN. Por ejemplo:

-	Un archivo .js desde la ruta /Script
-	Cualquier archivo de contenido desde la ruta /Content
-	Cualquier controlador/acción 
-	Si la cadena de consulta está habilitada en el extremo de red CDN, cualquier URL con cadenas de consulta
-	La aplicación web de Azure completa, si todo el contenido es público

Tenga en cuenta que puede que no siempre sea una buena idea (o por lo general una buena idea) servir una aplicación web de Azure completa a través de la red CDN de Azure. Estos son algunos de los puntos a tener en cuenta:

-	Este enfoque requiere que el sitio entero sea público, dado que CDN de Azure no puede servir contenido privado.
-	Si por algún motivo el extremo de red CDN se desconecta, ya sea por mantenimiento programado o debido a un error del usuario, la aplicación web entera se desconecta, a menos que los clientes se puedan redirigir a la dirección URL de origen **http://*&lt;sitename>*.azurewebsites.net/**.. 
-	Incluso con la configuración personalizada del control de la memoria caché (consulte [Configuración de las opciones de almacenamiento en caché para los archivos estáticos en la aplicación web de Azure](#configure-caching-options-for-static-files-in-your-azure-web-app)), un extremo de red CDN no mejora el rendimiento del contenido extremadamente dinámico. Si ha intentado cargar la página principal desde el extremo de red CDN como se ha mostrado anteriormente, tenga en cuenta que al menos tardará cinco segundos en cargarse la página principal predeterminada la primera vez, pese a ser una página bastante simple. Imagine entonces cómo sería la experiencia del cliente si esta página incluyera contenido dinámico que se debe actualizar cada minuto. Servir contenido dinámico desde un extremo de red CDN requiere una corta caducidad de la caché, lo que se traduce en errores frecuentes de la caché en el extremo de red CDN. Esto afecta al rendimiento de la aplicación web de Azure y frustra el propósito de una red CDN.

La alternativa es determinar caso por caso qué contenido se va a servir desde CDN de Azure en la aplicación web de Azure. Para tal fin, ya ha visto cómo acceder a archivos de contenido individuales desde el extremo de red CDN. Le mostraremos cómo servir una acción de controlador específica a través del extremo de red CDN en [Suministro de contenido de acciones de controlador a través de la red CDN de Azure](#serve-content-from-controller-actions-through-azure-cdn).

## Configuración de las opciones de almacenamiento en caché para los archivos estáticos de la aplicación web de Azure ##

Con la integración de la red CDN de Azure en la aplicación web de Azure, puede especificar cómo quiere que el contenido estático se almacene en la caché en el extremo de red CDN. Para ello, abra el archivo *Web.config* desde su proyecto ASP.NET (por ejemplo, **cdnwebapp**) y agregue un elemento `<staticContent>` a `<system.webServer>`. El XML siguiente configura la caché para que caduque en tres días.

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Una vez realizado esto, todos los archivos estáticos de la aplicación web de Azure cumplirán la misma regla en la memoria caché de red CDN. Para un control más granular de la configuración de la caché, agregue un archivo *Web.config* a una carpeta y agregue ahí su configuración. Por ejemplo, agregue un archivo *Web.config* a la carpeta *\\Content* y sustituya el contenido por el siguiente XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Esta configuración hace que todos los archivos estáticos de la carpeta *\\Content* se almacenen en la caché durante 15 días.

Para obtener más información sobre cómo configurar el elemento `<clientCache>`, vea [Caché de cliente clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

E esta sección , le mostraremos cómo puede configurar los valores de la caché para los resultados de las acciones de controlador en la memoria caché de red CDN.

## Suministro de contenido de acciones de controlador a través de la red CDN de Azure ##

Al integrar Aplicaciones Web con la red CDN de Azure, es relativamente fácil servir el contenido desde las acciones de controlador a través de la red CDN de Azure. Sin embargo, si decide suministrar la aplicación web de Azure completa a través de la red CDN, no será necesario hacerlo, puesto que todas las acciones de controlador están accesibles a través de dicha red. No obstante, debido a los motivos indicados en [Implementación de una aplicación web de Azure con un extremo de red CDN integrado](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), puede que decida no adoptar esta opción y optar por seleccionar la acción de controlador que desea servir desde la red CDN de Azure. [Maarten Balliauw](https://twitter.com/maartenballiauw) muestra cómo hacerlo con un divertido controlador MemeGenerator en [Reducción de la latencia en Web con la red CDN de Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Aquí simplemente lo vamos a reproducir.

Suponga que desea generar en la aplicación web memes basados en una imagen de Chuck Norris de joven (foto de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) como esta:

![](media/cdn-websites-with-cdn/cdn-5-memegenerator.PNG)

Tiene una acción `Index` sencilla que permite a los clientes especificar los superlativos de la imagen y que luego genera el meme una vez que publican la acción. Dado que se trata de Chuck Norris, esperará que esta página se haga ampliamente popular en todo el mundo. Este es un buen ejemplo de servir contenido dinámico con CDN de Azure.

Siga los pasos anteriores para configurar esta acción de controlador:

1. En la carpeta *\\Controllers*, cree un nuevo archivo .cs llamado *MemeGeneratorController.cs* y sustituya el contenido por el siguiente código. Sustituya la ruta de acceso al archivo por `~/Content/chuck.bmp` y el nombre de la red CDN por `yourCDNName`.


        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace cdnwebapp.Controllers
        {
          public class MemeGeneratorController : Controller
          {
            static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

            public ActionResult Index()
            {
              return View();
            }

            [HttpPost, ActionName("Index")]
            public ActionResult Index_Post(string top, string bottom)
            {
              var identifier = Guid.NewGuid().ToString();
              if (!Memes.ContainsKey(identifier))
              {
                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
              }

              return Content("<a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme</a>");
            }

            [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
            public ActionResult Show(string id)
            {
              Tuple<string, string> data = null;
              if (!Memes.TryGetValue(id, out data))
              {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
              }

              if (Debugger.IsAttached) // Preserve the debug experience
              {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
              }
              else // Get content from Azure CDN
              {
                return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
              }
            }

            [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
            public ActionResult Generate(string top, string bottom)
            {
              string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
              Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

              using (Graphics graphics = Graphics.FromImage(bitmap))
              {
                SizeF size = new SizeF();
                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                }
                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                }
              }
              MemoryStream ms = new MemoryStream();
              bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
              return File(ms.ToArray(), "image/png");
            }

            private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
            {
              // Compute actual size, shrink if needed
              while (true)
              {
                size = g.MeasureString(text, font);

                // It fits, back out
                if (size.Height < i.Height &&
                     size.Width < i.Width) { return font; }

                // Try a smaller font (90% of old size)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
              }
            }
          }
        }

2. Haga clic con el botón derecho en la acción `Index()` predeterminada y seleccione **Agregar vista**.

	![](media/cdn-websites-with-cdn/cdn-6-addview.PNG)

3.  Acepte la configuración que se indica a continuación y haga clic en **Agregar**.

	![](media/cdn-websites-with-cdn/cdn-7-configureview.PNG)

4. Abra el nuevo archivo *Views\\MemeGenerator\\Index.cshtml* y sustituya el contenido por el siguiente HTML simple para enviar los superlativos:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Publique de nuevo en la aplicación web de Azure y navegue a **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index** en el explorador.

Cuando envía los valores de formulario a `/MemeGenerator/Index`, el método de acción `Index_Post` devuelve un vínculo al método de acción `Show` con el identificador de entrada respectivo. Cuando hace clic en el vínculo, llega al siguiente código:

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
      Tuple<string, string> data = null;
      if (!Memes.TryGetValue(id, out data))
      {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
      }

      if (Debugger.IsAttached) // Preserve the debug experience
      {
        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
      }
      else // Get content from Azure CDN
      {
        return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
      }
    }

Si su depurador local está conectado, obtendrá la experiencia de depuración normal con una redirección local. Si se está ejecutando en la aplicación web de Azure, redirigirá a:

	http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde a la siguiente URL de origen en el extremo de red CDN:

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Después de aplicar la regla de reescritura de URL anterior, el archivo real que se almacena en caché en el extremo de red CDN es:

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Luego puede usar el atributo `OutputCacheAttribute` en el método `Generate` para especificar cómo se debería almacenar en caché el resultado de la acción, que atenderá CDN de Azure. El siguiente código especifica una caducidad de la caché de una hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Igualmente, puede servir contenido de cualquier acción de controlador de la aplicación web de Azure a través de su red CDN de Azure, con la opción de almacenamiento en caché deseada.

En la siguiente sección, le mostraremos cómo servir los scripts y CSS unidos y minificados a través de la red CDN de Azure.

## Integración de unión y minificación de ASP.NET con CDN de Azure ##

Los scripts y las hojas de estilo CSS cambian con poca frecuencia y son los principales candidatos para la caché de red CDN de Azure. Prestar servicio a toda la aplicación web a través de la red CDN de Azure es la manera más fácil de integrar unión y minificación con CDN de Azure. Sin embargo, como puede que elija en contra de este enfoque debido a las razones descritas en [Integración de un extremo de la red CDN de Azure con la aplicación web de Azure y suministro de contenido estático en las páginas web desde la red CDN de Azure](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), mostraremos cómo hacerlo conservando la experiencia de desarrollador deseada de unión y minificación de ASP.NET, como:

-	Gran experiencia en el modo de depuración
-	Implementación optimizada
-	Actualizaciones inmediatas a clientes de actualizaciones de versión de script/CSS
-	Mecanismo de reserva cuando el extremo de red CDN falla
-	Menor modificación del código

En el proyecto ASP.NET que creó en [Integración de un extremo de la red CDN de Azure con el sitio web de Azure y suministro de contenido estático en las páginas web desde la red CDN de Azure](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), abra *App\_Start\\BundleConfig.cs* y examine las llamadas de método `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

La primera instrucción `bundles.Add()` agrega un paquete de scripts en el directorio virtual `~/bundles/jquery`. A continuación, abra *Views\\Shared\_Layout.cshtml* para ver cómo se procesa la etiqueta del paquete de scripts. Encontrará la siguiente línea de código Razor:

    @Scripts.Render("~/bundles/jquery")

Cuando este código Razor se ejecute en la aplicación web de Azure, procesará una etiqueta `<script>` para el paquete de scripts similar a la siguiente:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Sin embargo, cuando se ejecute en Visual Studio mediante `F5`, procesará cada archivo de script del paquete de forma individual (en el caso anterior, solo hay un archivo de script en el paquete):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Esto permite depurar el código JavaScript de su entorno de desarrollo y, al mismo tiempo, reducir las conexiones cliente simultáneas (unión) y mejorar el rendimiento de la descarga de archivos (minificación) en producción. Es una excelente característica para preservar con la integración de red CDN de Azure. Además, como el paquete procesado ya contiene una cadena de versión generada automáticamente, querrá replicar esa funcionalidad para que cada vez que actualice su versión de jQuery a través de NuGet, se pueda actualizar en el cliente lo antes posible.

Siga estos pasos para la integración de la unión y minificación de ASP.NET con el extremo de red CDN.

1. De vuelta en *App\_Start\\BundleConfig.cs*, modifique los métodos `bundles.Add()` para usar un [constructor de paquetes](http://msdn.microsoft.com/library/jj646464.aspx) diferente, uno que especifique una dirección de CDN. Para ello, reemplace la definición del método `RegisterBundles` por el código siguiente:  
	
        public static void RegisterBundles(BundleCollection bundles)
        {
          bundles.UseCdn = true;
          var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
            .GetName().Version.ToString();
          var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;

          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                "~/Scripts/jquery-{version}.js"));

          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                "~/Scripts/jquery.validate*"));

          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizr")).Include(
                "~/Scripts/modernizr-*"));

          bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));

          bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
        }


	Asegúrese de sustituir `<yourCDNName>` por el nombre de su CDN de Azure.

	En otras palabras, ha configurado `bundles.UseCdn = true` y agregado una dirección URL de red CDN diseñada especialmente para cada paquete. Por ejemplo, el primer constructor del código:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	es igual que:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

	Este constructor indica que en la unión y minificación de ASP.NET se procesen archivos de script individuales cuando se depuren localmente, pero que se use la dirección de red CDN especificada para acceder al script en cuestión. Sin embargo, observe dos características importantes con esta URL de red CDN diseñada especialmente:
	
	- El origen de esta dirección URL de red CDN es `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`, que es en realidad el directorio virtual del paquete de scripts en la aplicación web.
	- Como está usando el constructor de red CDN, la etiqueta de script de red CDN del paquete ya no contiene la cadena de versión generada automáticamente en la URL procesada. Debe generar manualmente una cadena de versión única cada vez que se modifique el paquete de scripts con el fin de forzar un error de caché en la red CDN de Azure. Al mismo tiempo, esta cadena de versión única debe permanecer constante lo que dure la implementación para aumentar los aciertos de la caché en la red CDN de Azure después de implementarse el paquete.
	- La cadena de consulta v=<W.X.Y.Z> se extrae de *Properties\\AssemblyInfo.cs en el proyecto ASP.NET*. Puede tener un flujo de trabajo de implementación que incluya incrementar la versión de ensamblado cada vez que publica en Azure. O bien, puede modificar *Properties\\AssemblyInfo.cs* en su proyecto para incrementar automáticamente la cadena de versión cada vez que compila, mediante el carácter comodín '*'. Por ejemplo:

			[assembly: AssemblyVersion("1.0.0.*")]
	
	Cualquier otra estrategia para optimizar la generación de una cadena única durante una implementación funcionará aquí.

3. Vuelva a publicar la aplicación ASP.NET y obtenga acceso a la página principal.
 
4. Vea el código HTML de la página. Debería poder ver la dirección URL de red CDN procesada, con una cadena de versión única cada vez que vuelve a publicar los cambios en la aplicación web de Azure. Por ejemplo:
	
        ...
        <link href="http://az673227.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
        <script src="http://az673227.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
        ...
        <script src="http://az673227.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
        <script src="http://az673227.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
        ...

5. En Visual Studio, depure la aplicación de ASP.NET en Visual Studio escribiendo `F5`.,

6. Vea el código HTML de la página. Aún verá cada archivo de script procesado de forma individual para que pueda tener una experiencia de depuración coherente en Visual Studio.
	
        ...
        <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
        <script src="/Scripts/modernizr-2.6.2.js"></script>
        ...
        <script src="/Scripts/jquery-1.10.2.js"></script>
        <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
        ...    

## Mecanismo de reserva para URL de red CDN ##

Cuando el extremo de red CDN de Azure no funcione por cualquier motivo, querrá que su página web sea lo bastante inteligente como para acceder al servidor web de origen como opción de reserva para cargar JavaScript o Bootstrap. Es grave perder imágenes en la aplicación web debido a la falta de disponibilidad de la red CDN, pero es aún más grave perder la funcionalidad de página esencial que proporcionan los scripts y hojas de estilos.

La clase [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contiene una propiedad llamada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que le permite configurar el mecanismo de reserva en caso de error de red CDN. Para usar esta propiedad, siga estos pasos:

1. En el proyecto ASP.NET, abra *App\_Start\\BundleConfig.cs*, donde agregó una dirección URL de la red de entrega de contenido a cada [constructor de paquetes](http://msdn.microsoft.com/library/jj646464.aspx) y agregue el código `CdnFallbackExpression` en cuatro lugares, como se muestra, para agregar un mecanismo de reserva a los paquetes predeterminados.  
	
        public static void RegisterBundles(BundleCollection bundles)
        {
          var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
            .GetName().Version.ToString();
          var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
          bundles.UseCdn = true;

          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
                { CdnFallbackExpression = "window.jquery" }
                .Include("~/Scripts/jquery-{version}.js"));

          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
                { CdnFallbackExpression = "$.validator" }
                .Include("~/Scripts/jquery.validate*"));

          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
                { CdnFallbackExpression = "window.Modernizr" }
                .Include("~/Scripts/modernizr-*"));

          bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                { CdnFallbackExpression = "$.fn.modal" }
                .Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));

          bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
        }

	Cuando `CdnFallbackExpression` no tiene un valor null, el script se inserta en el código HTML para probar si el paquete está cargado correctamente y, en caso contrario, obtener acceso al paquete directamente desde el servidor web de origen. Esta propiedad se debe configurar como una expresión de JavaScript que prueba si el paquete de red CDN respectivo se ha cargado correctamente. La expresión necesaria para probar cada paquete es diferente según el contenido. En el caso de los paquetes predeterminados anteriores:
	
	- `window.jquery` se define en jquery-{version}.js
	- `$.validator` se define en jquery.validate.js
	- `window.Modernizr` se define en modernizer-{version}.js
	- `$.fn.modal` se define en bootstrap.js
	
	Habrá observado que no hemos configurado CdnFallbackExpression para el paquete `~/Cointent/css`. El motivo es que actualmente hay un [error en System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que inyecta una etiqueta `<script>` para la CSS de reserva en lugar de la etiqueta `<link>` esperada.
	
	Sin embargo, existe una buena [solución de reserva de paquetes de estilo](https://github.com/EmberConsultingGroup/StyleBundleFallback) que ofrece [Ember Consulting Group](https://github.com/EmberConsultingGroup).

2. Para usar la solución alternativa para CSS, cree un nuevo archivo .cs en la carpeta *App\_Start* del proyecto ASP.NET llamada *StyleBundleExtensions.cs*, y sustituya su contenido por el [código de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. En *App\_Start\\StyleFundleExtensions.cs*, cambie el nombre del espacio de nombres al espacio de nombres de la aplicación ASP.NET (por ejemplo **cdnwebapp**).

3. Vuelva a `App_Start\BundleConfig.cs` y reemplace la última instrucción `bundles.Add` por el código siguiente:

        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
          .IncludeFallback("~/Content/css", "sr-only", "width", "1px")
          .Include(
            "~/Content/bootstrap.css",
            "~/Content/site.css"));

	Este nuevo método de extensión usa la misma idea para inyectar el script en el código HTML a fin de comprobar si hay una coincidencia en el DOM para un nombre de clase, nombre de regla o valor de regla definidos en el paquete de CSS y, en caso de no encontrarla, recurre al servidor web de origen.

4. Vuelva a publicar en la aplicación web de Azure y obtenga acceso a la página principal.
5. Vea el código HTML de la página. Debería encontrar scripts inyectados simulares a los siguientes:    
	
	```
	...
	<link href="http://az673227.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i < len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az673227.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
			document.head.appendChild(meta);
			var value = window.getComputedStyle(meta).getPropertyValue('width');
			document.head.removeChild(meta);
			if (value !== '1px') {
                            document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
			}
		    }
		}
		return true;
            }())||document.write('<script src="/Content/css"><\\/script>');</script>

	<script src="http://az673227.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
 	<script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
	... 
	<script src="http://az673227.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
	<script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>

 	<script src="http://az673227.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
 	<script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
	...
	```

	Note that injected script for the CSS bundle still contains the errant remnant from the `CdnFallbackExpression` property in the line:

		}())||document.write('<script src="/Content/css"><\/script>');</script>

	But since the first part of the || expression will always return true (in the line directly above that), the document.write() function will never run.

6. Para probar si el script de reserva funciona, vuelva a la hoja del punto de conexión de CDN y haga clic en **Detener**.

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Actualice la ventana del explorador para la aplicación web de Azure. Ahora debería ver que todos los scripts y hojas de estilo están correctamente cargados.

## Más información 
- [Información general de la red de entrega de contenido (CDN) de Azure](../cdn/cdn-overview.md)
- [Entrega de contenido desde la red CDN de Azure en su aplicación web](../cdn/cdn-serve-content-from-cdn-in-your-web-application.md)
- [Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure](../cdn/cdn-cloud-service-with-cdn.md)
- [Unión y minificación de ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Uso de la red CDN en Azure](../cdn/cdn-how-to-use-cdn.md)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=AcomDC_0302_2016-->