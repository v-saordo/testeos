<properties
   pageTitle="Creación de una aplicación de API ASP.NET 5 en Visual Studio Code"
   description="En este tutorial se explica cómo crear una aplicación de API ASP.NET 5 con Visual Studio Code."
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="erikre"/>

# Creación de una aplicación de API ASP.NET 5 en Visual Studio Code

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

En este tutorial se explica cómo crear una aplicación de API ASP.NET 5 con [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode). ASP.NET 5 es un rediseño significativo de ASP.NET. ASP.NET 5 es un nuevo marco de código abierto multiplataforma diseñado para crear modernas aplicaciones web basadas en la nube con .NET. Para obtener más información, consulte [Introducción a ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Para obtener información sobre las aplicaciones de API, consulte [¿Qué son las Aplicaciones de API?](app-service-api-apps-why-best-platform.md)

> [AZURE.NOTE] Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [registrarse para obtener una evaluación gratuita](/pricing/free-trial/) o bien [activar los beneficios de suscripción a MSDN](/pricing/member-offers/msdn-benefits-details/). También puede probar de forma gratuita los [ejemplos de aplicaciones del Servicio de aplicaciones](http://tryappservice.azure.com).

## Requisitos previos  

* Instalar y configurar [Visual Studio Code](http://code.visualstudio.com/Docs/setup).
* Instalar [Node.js](http://nodejs.org/download/).<br>
 	[Node](http://nodejs.org/) es una plataforma para crear aplicaciones de servidor rápidas y escalables mediante JavaScript. Node es el tiempo de ejecución (Node) y [npm](http://www.npmjs.com/) es el Administrador de paquetes para los módulos de Node. Utilizará npm para aplicar la técnica scaffolding a una aplicación de API ASP.NET 5 en este tutorial.

## Instalación de ASP.NET 5 y DNX
ASP.NET 5/DNX es una pila de .NET eficiente que sirve para crear aplicaciones web y de nube modernas que se ejecutan en OS X, Linux y Windows. Se ha desarrollado desde el principio para proporcionar un marco de desarrollo optimizado para las aplicaciones que se implementan en la nube o se ejecutan de forma local. Consta de componentes modulares con una sobrecarga mínima, para continuar disfrutando de flexibilidad al crear soluciones.

> [AZURE.NOTE] ASP.NET 5 y DNX (el entorno de ejecución .NET) en OS X y Linux están disponibles actualmente en versión de vista previa/beta preliminar.

Este tutorial está diseñado para ayudarle a comenzar a crear aplicaciones con las últimas versiones de desarrollo de ASP.NET 5 y DNX. Si desea disfrutar de una experiencia más estable y sólida, visite [http://www.asp.net/vnext](http://www.asp.net/vnext). Las instrucciones siguientes son específicas de Windows. Para obtener más instrucciones de instalación para Windows, Linux y OS X, consulte [Instalación de ASP.NET 5 y DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Para instalar el Administrador de versión de .NET (DNVM) en Windows, ejecute el comando siguiente en la ventana Comandos:

	<pre class="prettyprint">
@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
	</pre>
	De esta forma, se descargará el script DNVM y se ubicará en el perfil de usuario.

2. Necesitará cerrar la sesión después de escribir el comando anterior para que el cambio de la variable de entorno PATH surta efecto.
3. Compruebe la ubicación de DNVM; para ello, ejecute lo siguiente en la ventana Comandos: 

	<pre class="prettyprint">
where dnvm
	</pre>
	En la ventana Comandos se mostrará una ruta de acceso similar a la siguiente:

	![ubicación de dnvm](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. Ahora que ya dispone de DNVM, necesitará utilizar este script para descargar DNX a fin de poder ejecutar las aplicaciones. Ejecute el siguiente comando desde la ventana Comandos:

	<pre class="prettyprint">
dnvm upgrade
</pre>

5. Verifique el DNVM y consulte el tiempo de ejecución activo; para ello, escriba lo siguiente en la ventana Comandos:

	<pre class="prettyprint">
dnvm list
	</pre>
	En la ventana Comandos se mostrarán los detalles del tiempo de ejecución activo:

	![ubicación de dnvm](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

Para obtener instrucciones de instalación más detalladas para Windows, Linux y OS X, consulte [Instalación de ASP.NET 5 y DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Creación de la aplicación de API 

En esta sección se explica cómo aplicar la técnica scaffolding a una nueva aplicación de API ASP.NET. Usará el Administrador de paquetes de nodo (npm) para instalar [Yeoman](http://yeoman.io/), [Grunt](http://gruntjs.com/) y [Bower](http://bower.io/).

1. Después de instalar Visual Studio Code y Node.js, abra un símbolo del sistema con derechos administrativos y vaya a la ubicación donde desea que se ubiquen todos los proyectos de ASP.NET que usan VSCode.
2. En la ventana Comandos para instalar las herramientas de soporte y Yeoman, escriba lo siguiente:

	<pre class="prettyprint">
npm install -g yo grunt-cli generator-aspnet bower
</pre>

3. En la ventana Comandos para crear la carpeta del proyecto y aplicar la técnica scaffolding a la aplicación, escriba lo siguiente:

	<pre class="prettyprint">
yo aspnet
</pre>

4. Siga las instrucciones proporcionadas por el generador; para ello, desplácese y seleccione el tipo **aplicación Web API**.

	![Yoman - Generador de ASP.NET 5](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. Defina el nombre de la aplicación de API ASP.NET nueva como **ContactsList**. Este nombre se usará en el código más adelante en este tutorial. <br> 
	Yoman creará una carpeta nueva con nombre **ContactsList**, además de los archivos necesarios para la aplicación nueva.
6. Abra **Visual Studio Code**.<br>
	 Puede abrir VSCode desde la ventana Comandos; para ello, escriba **code .**.
7. En el menú **Archivo**, seleccione **Abrir carpeta** y seleccione la carpeta donde se encuentra la aplicación de API ASP.NET.

	![Cuadro de diálogo Seleccionar carpeta](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode cargará el proyecto y lo mostrará en la ventana **Exploración**.

	![Visualización del proyecto Contact en VSCode](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. En **VSCode**, desde el menú **Vista**, seleccione **Paleta de comandos**.
9. En la **Paleta de comandos**, escriba los comandos siguientes:

	<pre class="prettyprint">
dnx:dnu restore - (ContactsList)
	</pre>
	A medida que empiece a escribir, verá la línea de comandos completa en la lista.

	![Comando Restore](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	El comando Restore instala los paquetes de NuGet necesarios para ejecutar la aplicación. En la ventana Comandos, se mostrará el mensaje **Restauración completada** cuando el proceso haya finalizado.

## Implementación de la aplicación de API

Ahora modificará la aplicación **ContactsList**; para ello, agregará una clase **Contact** y una clase **ContactsController**.

1. Coloque el cursor sobre el nombre del proyecto **ContactsList** y agregue una nueva carpeta denominada *Models* mediante el icono de carpeta nueva.

	![Nueva carpeta Models](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. Haga clic con el botón secundario en la carpeta **Models** para agregar un nuevo archivo de clase denominado *Contact.cs* con el código siguiente:

	<pre class="prettyprint">
namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
}
</pre>

3. Haga clic en la carpeta **Controllers** y agregue un archivo *ContactsController.cs* para que aparezca como sigue:

	<pre class="prettyprint">
using System.Collections.Generic;
using Microsoft.AspNet.Mvc;
using ContactsList.Models;

namespace ContactsList.Controllers
{
    [Route("api/[controller]")]
    public class ContactsController : Controller
    {
        // GET: api/Contacts
        [HttpGet]
        public IEnumerable&lt;Contact> Get()
        {
            return new Contact[]{
                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
            };
        }
    }
}
</pre>

4. Asegúrese de que se guardan todos los archivos; para ello, seleccione **Archivo** > **Guardar todo**.
5. En la **Paleta de comandos**, escriba los comandos siguientes para ejecutar la aplicación de forma local:

	<pre class="prettyprint">
dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
</pre>En la ventana Comandos, aparecerá *Started*. Si en la ventana Comandos no aparece *Started*, compruebe la esquina inferior izquierda de VSCode para ver si hay errores en el proyecto.

5. Abra un explorador y vaya a la dirección URL siguiente:

	**http://localhost:5001/api/Contacts**

	A continuación, puede ver el contenido de *Contacts.json*. En el archivo podrá ver el siguiente contenido:

	![Contenido de json devuelto](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## Modificación de metadatos de la aplicación de API
Los metadatos que permiten la implementación de un proyecto de API ASP.NET como aplicación de API deben estar incluidos en un archivo *apiapp.json* en la raíz del proyecto.

1. En VSCode, haga clic con el botón secundario en la carpeta *wwwroot* y, a continuación, seleccione la opción **Nuevo archivo**.
2. Asigne al nuevo archivo el nombre *apiapp.json*.<br>
 	Asegúrese de que *apiapp.json* se encuentra en la carpeta *wwwroot*.
3. Agregue lo siguiente al archivo *apiapp.json*:

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "id": "ContactsList",
    "namespace": "microsoft.com",
    "gateway": "2015-01-14",
    "version": "1.0.0",
    "title": "ContactsList",
    "summary": "",
    "author": "",
    "endpoints": null
}
</pre>

En el archivo *apiapp.json* puede especificar un extremo para la definición dinámica JSON de la API Swagger, pero en este tutorial utilizará un archivo de definición estática de API. Para obtener un ejemplo que utiliza la generación dinámica de Swagger, consulte [Configuración de un proyecto Web API como una aplicación de API](app-service-dotnet-create-api-app-visual-studio.md).

## Incorporación de una definición de API estática Swagger
Para proporcionar un archivo de definición estática de API Swagger 2.0, debe crear una carpeta en la raíz web y colocar en ella el archivo de definición de API.

1. En VSCode, cree una carpeta denominada *metadatos* en la carpeta *wwwroot*.
2. Haga clic con el botón secundario en la nueva carpeta *metadatos* y agregue un nuevo archivo denominado *apiDefinition.swagger.json*. 
3. Agregue la siguiente sintaxis json al nuevo archivo:

	<pre class="prettyprint">
{
  "swagger": "2.0",
  "info": {
    "version": "v1",
    "title": "ContactsList"
  },
  "host": "MUST REPLACE THIS WITH YOUR HOST URL",
  "schemes": [
    "https"
  ],
  "paths": {
    "/api/Contacts": {
      "get": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Get",
        "consumes": [],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "type": "array",
              "items": {
                "$ref": "#/definitions/Contact"
              }
            }
          }
        },
        "deprecated": false
      },
      "post": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Post",
        "consumes": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml",
          "application/x-www-form-urlencoded"
        ],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "parameters": [
          {
            "name": "contact",
            "in": "body",
            "required": true,
            "schema": {
              "$ref": "#/definitions/Contact"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "$ref": "#/definitions/Object"
            }
          }
        },
        "deprecated": false
      }
    }
  },
  "definitions": {
    "Contact": {
      "type": "object",
      "properties": {
        "Id": {
          "format": "int32",
          "type": "integer"
        },
        "Name": {
          "type": "string"
        },
        "EmailAddress": {
          "type": "string"
        }
      }
    },
    "Object": {
      "type": "object",
      "properties": {}
    }
  }
}
</pre>

Más adelante en este tutorial, deberá reemplazar la cadena anterior de marcador de posición de dirección URL de host por la dirección URL de host de Azure que va a crear y copiar más tarde.

## Creación de una aplicación de API en el Portal de vista previa de Azure

> [AZURE.NOTE] Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [registrarse para obtener una evaluación gratuita](/pricing/free-trial/) o bien [activar los beneficios de suscripción a MSDN](/pricing/member-offers/msdn-benefits-details/). También puede probar de forma gratuita los [ejemplos de aplicaciones del Servicio de aplicaciones](http://tryappservice.azure.com).

1. Inicie sesión en el [portal de vista previa de Azure](https://portal.azure.com).

2. Haga clic en **NUEVO** en la parte superior izquierda del portal.

3. Haga clic en **Web + móvil > Aplicación de API**.

	![Nueva aplicación de API de Azure](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. Escriba un valor para **Nombre**, como ContactsList.

5. Seleccione un plan de Servicio de aplicaciones o cree uno nuevo. Si crea un nuevo plan, seleccione el nivel de precios, la ubicación y otras opciones.

	![Nueva hoja de aplicación de API de Azure](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. Haga clic en **Crear**.

	![Hoja Aplicación de API](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	Si deja seleccionada la casilla **Anclar a Panel de inicio** al crear la aplicación, puede localizar fácilmente la aplicación haciendo clic en **Inicio** o **Examinar**. Si ha desactivado la casilla, haga clic en **Notificaciones** a la izquierda para ver el estado de creación de la aplicación de API y en la notificación para ir a la hoja de la nueva aplicación de API.

7. Haga clic en **Configuración > Configuración de la aplicación**.

8. Establezca el nivel de acceso en **Público (anónimo)**.

9. Haga clic en **Guardar**.

	![Configuración de aplicaciones web](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## Habilitación de la publicación Git desde la nueva aplicación de API

Git es un sistema de control de versión distribuida que puede utilizar para implementar la aplicación web de Azure. Almacenará el código que escriba para su aplicación de API en un repositorio Git local y lo implementará en Azure enviando los cambios a un repositorio remoto. Este método de implementación es una característica de aplicaciones web del Servicio de aplicaciones que puede utilizar en una aplicación de API al basarse las aplicaciones de API en aplicaciones web: una aplicación de API del Servicio de aplicaciones de Azure es una aplicación web con características adicionales para hospedar servicios web.

En el portal, administra las características específicas de las aplicaciones de API en la hoja **Aplicación de API**, así como las características que se comparten con aplicaciones web en la hoja **Host de aplicación API**. Por lo tanto, en esta sección, se va a la hoja **Host de aplicación API** para configurar la característica de implementación de Git.

1. En la hoja Aplicación de API, haga clic en **Host de aplicación API**.

	![Host de aplicación de API de Azure](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. Busque la sección **Implementación** de la hoja **Aplicación de API** y haga clic en **Configurar la implementación continua**. Puede que necesite desplazarse para ver esta parte de la hoja.

	![Host de aplicación de API de Azure](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. Haga clic en **Elegir origen > Repositorio de Git local**.

5. Haga clic en **Aceptar**.

	![Repositorio de Git local de Azure](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. Si no ha configurado previamente las credenciales de implementación para publicar una aplicación de API u otra aplicación del Servicio de aplicaciones, configúrelas ahora:

	* Haga clic en **Establecer credenciales de implementación**.

	* Cree un nombre de usuario y una contraseña. Necesitará esta contraseña más tarde al configurar Git.

	* Haga clic en **Guardar**.

	![Credenciales de implementación de Azure](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. En la hoja **Host de aplicación API** , haga clic en **Configuración > Propiedades**. La URL del repositorio Git remoto en el que implementará se muestra en "DIRECCIÓN URL DE GIT".

8. Copie la **Dirección URL de Git** para utilizarla más tarde en el tutorial.

	![Dirección URL de Git de Azure](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. Además, en la hoja **Aplicación de API**, copie la **dirección URL** que usará para actualizar el valor de "host" en el archivo *apiDefinition.swagger.json*.

	![Dirección URL de Azure](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. En VSCode, abra el archivo *apiDefinition.swagger.json* y reemplace el valor del host "MUST REPLACE THIS WITH YOUR HOST URL" por la **dirección URL** que copió en el paso anterior.
11. Asimismo, quite los caracteres "https://" del principio del valor de host.
12. Guarde los cambios en el archivo *apiDefinition.swagger.json*.

## Publicación de la aplicación de API en el Servicio de aplicaciones de API

En esta sección, se crea un repositorio Git local e inserta desde ese repositorio en Azure para implementar su aplicación de ejemplo en la aplicación de API que se ejecuta en el Servicio de aplicaciones de Azure.

1. En VSCode en la barra de navegación de la izquierda, seleccione la opción de Git.
2. Si Git no está instalado, instálelo eligiendo uno de los vínculos proporcionados ([Chocolatey](https://chocolatey.org/packages/git) o [git scm.com](http://git-scm.com/downloads)). Si no está familiarizado con Git, elija **git-scm.com** y seleccione la opción para usar Git con GitBash desde el símbolo del sistema de Windows. 
3. Una vez instalado Git, reinicie VSCode y seleccione la opción de Git en la barra izquierda.
4. En VSCode, seleccione **Inicializar repositorio Git** para asegurarse de que el área de trabajo está debajo del control de código fuente de git. 

	![Inicializar Git](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. Agregue un mensaje de confirmación y seleccione la casilla **Confirmar todo**.

	![Confirmar todo de GIT](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. Busque y abra **GitBash**. Como alternativa, puede utilizar el símbolo del sistema de Windows.
7. En **GitBash**, cambie las carpetas a la carpeta del proyecto de VSCode. Por ejemplo:

	<pre class="prettyprint">
cd c:\VSCodeProjects\ContactsList
</pre>

8. Cree una referencia remota para enviar actualizaciones a la aplicación web (host de aplicación de API) que creó anteriormente, con la dirección URL de Git (terminada en “.git”) que copió anteriormente:

	<pre class="prettyprint">
git remote add azure [URL del repositorio remoto]
</pre>

9. Envíe los cambios a Azure escribiendo el siguiente comando:

	<pre class="prettyprint">
git push azure master
	</pre>
	Se le solicitará la contraseña que ha creado anteriormente. **Nota: La contraseña no será visible.**

	La salida del comando anterior finaliza con un mensaje que indica que la implementación se ha realizado correctamente:

	<pre class="prettyprint">
remote: Deployment successful.
To https://user@testsite.scm.azurewebsites.net/testsite.git
[new branch]      master -> master
</pre>

> [AZURE.NOTE] Si realiza cambios en la aplicación, puede volver a publicarla; para ello, seleccione la casilla **Confirmar todo** en VSCode y, a continuación, escriba el comando **git push azure master** en **GitBash**.

## Visualización de la definición de la API en el portal de vista previa de Azure
Ahora que ha implementado una API en su aplicación de API, puede ver la definición de la API en el portal de vista previa de Azure. Comenzará reiniciando la puerta de enlace, que permite que Azure reconozca que ha cambiado la definición de la API de una aplicación de API. La puerta de enlace es una aplicación web que controla la administración de API y la autorización de las aplicaciones de API en un grupo de recursos.

1. En el portal de vista previa de Azure, vaya a la hoja **Aplicación de API** para la aplicación de API que creó anteriormente y haga clic en el vínculo **Puerta de enlace**.
2. En la hoja **PUERTA DE ENLACE**, haga clic en **Reiniciar**. Ahora puede cerrar esta hoja.
3. En la hoja **APLICACIÓN DE API**, haga clic en **Reiniciar**. 
4. En la hoja **APLICACIÓN DE API**, haga clic en **Definición de API**.<br>
 	La hoja Definición de API muestra dos métodos. Si no ve los métodos GET y POST de forma inmediata, espere unos segundos para que Azure actualice la aplicación. A continuación, haga clic en **Definición de API** en la hoja **APLICACIÓN DE API**.

## Ejecución de la aplicación en Azure
En el portal de vista previa de Azure, vaya a la hoja **HOST DE APLICACIÓN DE API** para su aplicación de API y haga clic en **Examinar**. A continuación, agregue **api/Contacts** al final de la dirección URL para ver los detalles de contacto.


## Conclusión
En este tutorial a aprendido crear una aplicación de API en Visual Studio Code. Para obtener más información sobre Visual Studio Code, consulte [Visual Studio Code.](https://code.visualstudio.com/Docs/). Para obtener información sobre las aplicaciones de API, consulte [¿Qué son las Aplicaciones de API?](app-service-api-apps-why-best-platform.md)
 

<!---HONumber=AcomDC_0128_2016-->