<properties
	pageTitle="Configuración avanzada y extensiones de aplicación web del Servicio de aplicaciones de Azure"
	description="Las declaraciones de XML Document Transformation (XDT) se usan para transformar el archivo ApplicationHost.config en su aplicación web del Servicio de aplicaciones de Azure y agregar extensiones privadas para habilitar acciones de administración personalizadas."
	authors="cephalin"
	writer="cephalin"
	editor="mollybos"
	manager="wpickett"
	services="app-service"
	documentationCenter=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="cephalin"/>

# Configuración avanzada y extensiones de aplicación web del Servicio de aplicaciones de Azure

Con las declaraciones de [XML Document Transformation](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), puede transformar el archivo [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) en su aplicación web del Servicio de aplicaciones de Azure. También puede usar las declaraciones XDT a fin de agregar extensiones privadas para habilitar acciones de administración de la aplicación web personalizada. Este artículo incluye una extensión de aplicación web del administrador PHP de ejemplo que habilita la administración de la configuración de PHP a través de una interfaz web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configuración avanzada mediante ApplicationHost.config
La plataforma del Servicio de aplicaciones proporciona flexibilidad y control para la configuración de la aplicación web. Aunque el archivo de configuración ApplicationHost.config de IIS estándar no está disponible para la edición directa en el Servicio de aplicaciones, la plataforma es compatible con un modelo de transformación ApplicationHost.config declarativo basado en XML Document Transformation (XDT).

Para sacar provecho de esta funcionalidad de transformación, cree un archivo ApplicationHost.xdt con contenido XDT y colóquelo en la raíz del sitio (d:\\home\\site) en la [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). Puede que necesite reiniciar la aplicación web para que los cambios surtan efecto.

El siguiente ejemplo de applicationHost.xdt muestra cómo agregar una nueva variable de entorno personalizada en una aplicación web que use PHP 5.4.

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.webServer>
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
         				</environmentVariables>
      				</application>
    			</fastCgi>
  		</system.webServer>
	</configuration>


Hay un archivo de registro con información y estado de trasformación disponible en la raíz del FTP en LogFiles\\Transform.

Para ejemplos adicionales, vea [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Nota:**<br /> los elementos de la lista de módulos en `system.webServer` no pueden quitarse o reordenarse, aunque sí es posible agregar elementos a la lista.


##<a id="extend"></a> Ampliación de la aplicación web

###<a id="overview"></a> Información general de las extensiones de aplicación web privada

El Servicio de aplicaciones es compatible con extensiones de aplicación web como punto de extensibilidad para acciones administrativas. De hecho, algunas características de la plataforma del Servicio de aplicaciones se implementan como extensiones preinstaladas. Cuando las extensiones preinstaladas de la plataforma no se puedan modificar, puede crear y configurar extensiones privadas para su propia aplicación web. Esta funcionalidad también se basa en declaraciones XDT. Los pasos clave para la creación de una extensión de la aplicación web privada son los siguientes:

1. **Contenido** de la extensión de la aplicación web: crear todas las aplicaciones web compatibles con el Servicio de aplicaciones
2. **Declaración** de la extensión de la aplicación web: crear una aplicación ApplicationHost.xdt
3. **Implementación** de la extensión de la aplicación web: colocar contenido en la carpeta SiteExtensions en `root`

Los vínculos internos para la aplicación web deben apuntar a una ruta relacionada con la ruta de la aplicación especificada en el archivo ApplicationHost.xdt. Cualquier cambio en el archivo ApplicationHost.xdt requiere reciclar la aplicación web.

**Nota**: la información adicional para estos elementos clave se encuentra disponible en [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Se incluye un ejemplo detallado para mostrar los pasos para la creación y habilitación de una extensión de aplicación web privada. El código fuente para el administrador PHP siguiente puede descargarse en [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a> Ejemplo de extensión de aplicación web:

El administrador PHP es una extensión de la aplicación web que permite a los administradores de la aplicación web ver y configurar fácilmente su configuración PHP con una interfaz web en lugar de tener que modificar archivos .ini PHP directamente. Los archivos de configuración comunes para PHP incluyen el archivo php.ini ubicado en Archivos de programa y el archivo .user.ini ubicado en la carpeta raíz de la aplicación web. Puesto que el archivo php.ini no es directamente editable en la plataforma del Servicio de aplicaciones, la extensión del administrador PHP usa el archivo .user.ini para aplicar los cambios de configuración.

####<a id="PHPwebapp"></a> Aplicación web del administrador PHP

A continuación se muestra la página principal de la implementación del administrador PHP:

![TransformSitePHPUI][TransformSitePHPUI]

Como puede ver, una extensión de la aplicación web es como una aplicación web normal, pero con un archivo ApplicationHost.xdt adicional situado en la carpeta raíz de la aplicación web (puede encontrar más información sobre el archivo ApplicationHost.xdt disponible en la siguiente sección de este artículo).

La extensión del administrador PHP se creó mediante la plantilla de la aplicación ASP.NET MVC 4 Web Application de Visual Studio. La siguiente vista del Explorador de soluciones muestra la estructura de la extensión del administrador PHP.

![TransformSiteSolEx][TransformSiteSolEx]

La única lógica especial necesaria para la E/S del archivo es indicar dónde se encuentra el directorio wwwroot de la aplicación web. Puesto que se muestra el siguiente ejemplo de código, la variable de entorno "HOME" indica la ruta raíz de la aplicación web y la ruta wwwroot puede construirse anexando "site\\wwwroot":

	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
    		return userSettingsFile;
	}


Una vez que disponga de la ruta de acceso al directorio, puede usar las operaciones de E/S del archivo normal para leer y escribir en los archivos.

Un punto de advertencia en relación con las extensiones de la aplicación web hace referencia a la administración de los vínculos internos. Si dispone de vínculos en los archivos HTML que proporcionen rutas absolutas a vínculos internos en la aplicación web, debe asegurarse de que esos vínculos se anexen al nombre de extensión como su raíz. Esto es necesario porque la ruta del sitio para la extensión es ahora "/`[your-extension-name]`/" en lugar de solo "/", por lo que los vínculos internos deben actualizarse como corresponda. Por ejemplo, suponga que el código incluye un vínculo a lo siguiente:

`"<a href="/Home/Settings">PHP Settings</a>"`

Cuando el vínculo forma parte de una extensión de la aplicación web, el vínculo debe tener la siguiente forma:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Puede satisfacer este requisito usando solo rutas relativas en la aplicación web o, en caso de aplicaciones ASP.NET, usando el método `@Html.ActionLink` que crea los vínculos apropiados para usted.

####<a id="XDT"></a> Archivo applicationHost.xdt

El código de la extensión de la aplicación web se encuentra en %HOME%\\SiteExtensions[nombre-extensión]. Llamaremos a esto raíz de extensión.

Para registrar la extensión de la aplicación web con el archivo applicationHost.config, tendrá que colocar un archivo denominado ApplicationHost.xdt en la raíz de extensión. El contenido del archivo ApplicationHost.xdt debe ser el siguiente:

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

El nombre que seleccione como nombre de extensión debe tener el mismo nombre que la carpeta raíz de extensión.

Esto provoca la suma de una nueva ruta de aplicación a la lista de sitios `system.applicationHost` en el sitio SCM. El sitio SCM es un punto final de administración que especifica credenciales de acceso. Dispone de la dirección URL `https://[your-site-name].scm.azurewebsites.net`.

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\SiteExtensions[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

###<a id="deploy"></a> Implementación de extensión de aplicación web

Para instalar la extensión de la aplicación web, puede usar FTP para copiar todos los archivos de la aplicación web en la carpeta `\SiteExtensions[your-extension-name]` de la aplicación web en la que desea instalar la extensión. Asegúrese de copiar el archivo ApplicationHost.xdt en la ubicación también. Reinicie la aplicación web para habilitar la extensión.

Debe poder ver la extensión de la aplicación web en:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Tenga en cuenta que el aspecto de la dirección URL es parecido al de la dirección URL de su aplicación web, con la diferencia de que usa HTTPS y contiene ".scm".

Es posible deshabilitar todas las extensiones (no preinstaladas) privadas para su aplicación web durante el desarrollo y las investigaciones agregando una configuración de la aplicación con la clave `WEBSITE_PRIVATE_EXTENSIONS` y un valor de `0`.

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 

<!---HONumber=AcomDC_1217_2015-->