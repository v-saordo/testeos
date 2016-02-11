<properties
	pageTitle="Aplicación web Node.js utilizando el servicio Tabla de Azure"
	description="Este tutorial le enseña a usar el servicio Tabla de Azure para almacenar datos desde una aplicación Node.js hospedada en Aplicaciones web del Servicio de aplicaciones de Azure."
	tags="azure-portal"
	services="app-service\web, storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="robmcm"/>

# Aplicación web Node.js utilizando el servicio Tabla de Azure

## Información general

En este tutorial aprenderá a utilizar el servicio Tabla que la administración de datos de Azure proporciona para almacenar y tener acceso a datos desde una aplicación [node] hospedada en Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). En este tutorial se asume que tiene alguna experiencia anterior en el uso de Node y [Git].

Aprenderá a:

* Usar npm (administrador de paquetes de Node) para instalar los módulos de Node

* Trabajar con el servicio Tabla de Azure

* Uso de la CLI de Azure para crear una aplicación web.

Al seguir este tutorial, podrá compilar una aplicación de "lista de tareas pendientes" basadas en web sencilla que permite crear, recuperar y completar tareas. Las tareas se almacenan en el servicio Tabla.

Esta es la aplicación completada:

![Página web que muestra una lista de tareas vacía][node-table-finished]

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Requisitos previos

Antes de seguir las instrucciones del presente artículo, asegúrese de tener instalados los siguientes elementos:

* [node] versión 0.10.24 o superior

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Crear una cuenta de almacenamiento

Cree una cuenta de Almacenamiento de Azure. La aplicación usará esta cuenta para almacenar los elementos de tareas pendientes.

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en el icono **Nuevo** situado en la parte inferior izquierda del portal y haga clic en **Datos + Almacenamiento** > **Almacenamiento**. Asigne un nombre único a la cuenta de almacenamiento y cree un [grupo de recursos](../resource-group-overview.md) nuevo para ella.

  	![Botón Nuevo](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)

	Una vez creada la cuenta de almacenamiento, el botón **Notificaciones** emitirá el mensaje **CORRECTO** en color verde y la hoja de la cuenta de almacenamiento se abrirá para mostrar que pertenece al nuevo grupo de recursos que ha creado.

5. En la hoja de la cuenta de almacenamiento, haga clic en **Configuración** > **Claves**. Copie la clave de acceso principal en el Portapapeles.

    ![Clave de acceso][portal-storage-access-keys]


##Instalación de módulos y generación de scaffolding

En esta sección podrá crear una nueva aplicación Node y usar npm para agregar paquetes de módulos. Para esta aplicación, usará los módulos [Express] y [Azure]. El módulo Express proporciona un marco de controlador de vista de modelo para Node, mientras que los módulos de Azure proporcionan conectividad al servicio Tabla.

### Instalar Express y generar scaffolding

1. Desde la línea de comandos, cree un nuevo directorio denominado **tasklist** y cambie a ese directorio.  

2. Escriba el siguiente comando para instalar el módulo Express.

		npm install express-generator@4.2.0 -g

    En función del sistema operativo, puede ser necesario poner 'sudo' antes del comando:

		sudo npm install express-generator@4.2.0 -g

    El resultado es similar al ejemplo siguiente:

		express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE] El parámetro '-g' instala el módulo globalmente. De este modo, podemos utilizar **express** para generar el scaffolding de la aplicación web sin tener que escribir información de ruta adicional.

4. Para crear el scaffolding para la aplicación, escriba el comando **express**:

        express

	El resultado de este comando es similar al ejemplo siguiente:

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www

		   install dependencies:
		     $ cd . && npm install

		   run the app:
		     $ DEBUG=my-application ./bin/www

	Ahora debe tener varios directorios y archivos nuevos en el directorio **tasklist**.

### Instalar módulos adicionales

Uno de los archivos que crea **express** es **package.json**. Este archivo contiene una lista de dependencias de módulo. Posteriormente, cuando implemente esta aplicación en Aplicaciones web del Servicio de aplicaciones, este archivo determinará los módulos que se deben instalar en Azure.

En la línea de comandos, escriba el siguiente comando para instalar los módulos que se describen en el archivo **package.json**: Puede que necesite usar 'sudo'.

    npm install

El resultado de este comando es similar al ejemplo siguiente:

	debug@0.7.4 node_modules\debug

	cookie-parser@1.0.1 node_modules\cookie-parser
	├── cookie-signature@1.0.3
	└── cookie@0.1.0

    [...]


A continuación, escriba el siguiente comando para instalar los módulos [azure], [node-uuid], [nconf] y [async]\:

	npm install azure-storage node-uuid async nconf --save

La marca **--save** agrega entradas para estos módulos en el archivo **package.json**.

El resultado de este comando es similar al ejemplo siguiente:

	async@0.9.0 node_modules\async

	node-uuid@1.4.1 node_modules\node-uuid

	nconf@0.6.9 node_modules\nconf
	├── ini@1.2.1
	├── async@0.2.9
	└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

	[...]


## Creación de la aplicación

Ahora estamos listos para compilar la aplicación.

### Crear un modelo

Un *modelo* es un objeto que representa los datos de la aplicación. Para la aplicación, el modelo solo es un objeto de tarea, que representa un elemento en la lista de tareas pendientes. Las tareas tienen los siguientes campos:

- PartitionKey
- RowKey
- name (string)
- category (string)
- completed (Boolean)

El servicio Tabla utiliza **PartitionKey** y **RowKey** como claves de tabla. Para obtener más información, consulte [Introducción al modelo de datos del servicio Tabla](https://msdn.microsoft.com/library/azure/dd179338.aspx).


1. En el directorio **tasklist**, cree un directorio nuevo con el nombre **models**.

2. En el directorio **models**, cree un archivo nuevo con el nombre **task.js**. Este archivo contendrá el modelo para las tareas que se crean con su aplicación.

3. Al comienzo del archivo **task.js**, agregue el siguiente código para hacer referencia a las bibliotecas requeridas:

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. Agregue el código siguiente para definir y exportar el objeto Task. Este objeto es el responsable de la conexión a la tabla.

  		module.exports = Task;

		function Task(storageClient, tableName, partitionKey) {
		  this.storageClient = storageClient;
		  this.tableName = tableName;
		  this.partitionKey = partitionKey;
		  this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
		    if(error) {
		      throw error;
		    }
		  });
		};

5. Agregue el siguiente código para definir métodos adicionales en el objeto Task, que permite interacciones con los datos almacenados en la tabla:

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. Guarde y cierre el archivo **task.js**.

### Creación de un controlador

Un *controlador* administra las solicitudes HTTP y procesa la respuesta HTML.

1. En el directorio **tasklist/routes**, cree un archivo nuevo con el nombre **tasklist.js** y ábralo en un editor de texto.

2. Agregue el siguiente código a **tasklist.js**. De este modo se cargan los módulos azure y async, que utiliza **tasklist.js**. También define la función **TaskList**, que pasa a una instancia del objeto **Task** que definimos anteriormente:

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

3. Defina un objeto **TaskList**.

		function TaskList(task) {
		  this.task = task;
		}


4. Agregue los siguientes métodos a **TaskList**:

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this;
		    var item = req.body.item;
		    self.task.addItem(item, function itemAdded(error) {
		      if(error) {
		        throw error;
		      }
		      res.redirect('/');
		    });
		  },

		  completeTask: function(req,res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.task.updateItem(completedTask, function itemsUpdated(error) {
		        if(error){
		          callback(error);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(error){
		      if(error) {
		        throw error;
		      } else {
		       res.redirect('/');
		      }
		    });
		  }
		}


### Modificar app.js

1. En el directorio **tasklist**, abra el archivo **app.js**. Este archivo se creó anteriormente al ejecutar el comando **express**.

2. Al principio del archivo, agregue lo siguiente para cargar el módulo azure, configurar el nombre de la tabla, la clave de partición y las credenciales de almacenamiento utilizadas en este ejemplo:

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json', search: true });
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE] nconf cargará los valores de configuración desde las variables de entorno o el archivo **config.json**, que crearemos más adelante.

3. En el archivo app.js, desplácese hacia abajo hasta ver la siguiente línea:

		app.use('/', routes);
		app.use('/users', users);

	Sustituya las líneas anteriores por el código que se muestra a continuación. De este modo se inicializará una instancia de <strong>Task</strong> con una conexión a su cuenta de almacenamiento. Esto se pasa a la <strong>TaskList</strong>, que lo utilizará para comunicarse con el servicio Tabla:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

4. Guarde el archivo **app.js**.

### Modificar la vista de índice

1. Abra el archivo **tasklist/views/index.jade** en un editor de texto.

2. Reemplace todo el contenido del archivo con el código siguiente. De esta manera se define una vista para mostrar las tareas existentes y se incluye un formulario para agregar tareas nuevas y marcar las existentes como terminadas.

		extends layout

		block content
		  h1= title
		  br

		  form(action="/completetask", method="post")
		    table.table.table-striped.table-bordered
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		      if (typeof tasks === "undefined")
		        tr
		          td
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="item[name]", type="textbox")
		    label Item Category:
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. Guarde y cierre el archivo **index.jade**.

### Modificar el diseño global

El archivo **layout.jade** del directorio **views** es una plantilla global para otros archivos **.jade**. En este paso podrá modificarlo para utilizar [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de herramientas que facilita el diseño de una aplicación web atractiva.

Descargue y extraiga los archivos para [Twitter Bootstrap](http://getbootstrap.com/). Copie el archivo **bootstrap.min.css** desde la carpeta Bootstrap **css** al directorio **public\\stylesheets** de su aplicación.

En la carpeta **views**, abra **layout.jade** y reemplace todo el contenido por lo siguiente:

	doctype html
	html
	  head
	    title= title
	    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
	    link(rel='stylesheet', href='/stylesheets/style.css')
	  body.app
	    nav.navbar.navbar-default
	      div.navbar-header
	      a.navbar-brand(href='/') My Tasks
	    block content

### Creación de un archivo de configuración

Para ejecutar la aplicación localmente, pondremos credenciales de Almacenamiento de Azure en un archivo de configuración. Cree un archivo denominado **config.json* *con el siguiente JSON:

	{
		"STORAGE_NAME": "<storage account name>",
		"STORAGE_KEY": "<storage access key>",
		"PARTITION_KEY": "mytasks",
		"TABLE_NAME": "tasks"
	}

Reemplace **nombre de cuenta de almacenamiento** por el nombre de la cuenta de almacenamiento creada anteriormente y **clave de acceso de almacenamiento** por la clave de acceso principal para la cuenta de almacenamiento. Por ejemplo:

	{
	    "STORAGE_NAME": "nodejsappstorage",
	    "STORAGE_KEY": "KG0oDd..."
	    "PARTITION_KEY": "mytasks",
	    "TABLE_NAME": "tasks"
	}

Guarde este archivo en *un nivel de directorio más alto* que el directorio **tasklist**, similar a lo siguiente:

	parent/
	  |-- config.json
	  |-- tasklist/

La razón para hacer esto es evitar la comprobación del archivo de configuración en el control de código fuente, donde puede ser público. Cuando se implementa la aplicación en Azure, usaremos las variables de entorno en lugar de un archivo de configuración.


## Ejecución de la aplicación de forma local

Lleve a cabo los siguientes pasos para probar la aplicación en su máquina local:

1. En la línea de comandos, cambie de directorio al directorio **tasklist**.

2. Utilice el siguiente comando para iniciar localmente la aplicación:

        npm start

3. Abra el explorador web y navegue a http://127.0.0.1:3000.

	Aparecerá una página web similar a la del ejemplo siguiente.

	![Página web que muestra una lista de tareas vacía][node-table-finished]

4. Para crear un nuevo elemento de tarea pendiente, escriba un nombre y una categoría y haga clic en **Agregar elemento**.

6. Para marcar una tarea como completa, marque **Completado** y haga clic en **Actualizar tareas**.

	![Imagen del elemento nuevo en la lista de tareas][node-table-list-items]

Aunque la aplicación se ejecuta localmente, almacena los datos en el servicio Tabla de Azure.

## Implementación de su aplicación en Azure

En los pasos de esta sección se usan las herramientas de línea de comandos de Azure para crear una nueva aplicación web en el Servicio de aplicaciones y después implementar la aplicación mediante Git. Para realizar estos pasos debe tener una suscripción a Azure.

> [AZURE.NOTE] Estos pasos también pueden llevarse a cabo usando el [Portal de Azure](https://portal.azure.com). Consulte [Compilación e implementación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure].
>
> Si esta es la primera aplicación web que crea, debe usar el Portal de Azure para implementarla.

Para empezar, instale la [CLI de Azure] escribiendo el siguiente comando desde la línea de comandos:

	npm install azure-cli -g

### Importación de la configuración de publicación

En este paso, descargará un archivo que contiene información acerca de su suscripción.

1. Escriba el comando siguiente:

		azure account download

	Este comando inicia un explorador y se desplaza a la página de descarga. Si se le solicita, inicie sesión con la cuenta asociada a su suscripción de Azure.

	<!-- ![The download page][download-publishing-settings] -->
	La descarga del archivo se inicia automáticamente; si esto no ocurre, puede hacer clic en el vínculo al comienzo de la página para descargar el archivo manualmente. Guarde el archivo y anote la ruta de acceso del archivo.

2. Escriba el siguiente comando para importar la configuración.

		azure account import <path-to-file>

	Especifique la ruta y el nombre de archivo del archivo de configuración de publicación que descargó en el paso anterior.

3. Después de importar la configuración, elimine el archivo de configuración de publicación. Ya no es necesario y contiene información confidencial relacionada con su suscripción de Azure.

### Crear una aplicación web del Servicio de aplicaciones

1. En la línea de comandos, cambie de directorio al directorio **tasklist**.

2. Utilice el comando siguiente para crear una nueva aplicación web.

		azure site create --git

	Se le pedirá que especifique el nombre de la aplicación web y la ubicación. Proporcione un nombre único y seleccione la misma ubicación geográfica que la cuenta de Almacenamiento de Azure.

	El parámetro `--git` crea un repositorio Git en Azure para esta aplicación web. También inicializa un repositorio Git en el directorio actual si no existe y agrega un [Git remoto] denominado 'azure', que se usa para publicar la aplicación en Azure. Finalmente, crea un archivo **web.config**, que contiene la configuración usada por Azure para hospedar aplicaciones Node. Si se omite el parámetro `--git`, pero el directorio contiene un repositorio de Git, el comando creará el “azure” remoto.

	Después de que este comando se haya completado, verá un resultado similar al siguiente. Observe que la línea que comienza por **Website created at** contiene la dirección URL de la aplicación web.

		info:   Executing command site create
		help:   Need a site name
		Name: TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	> [AZURE.NOTE] Si esta es la primera aplicación web del Servicio de aplicaciones de su suscripción, se le pedirá que use el Portal de Azure para crear la aplicación web. Para obtener más información, consulte [Desarrollo e implementación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure].

### Establecimiento de variables de entorno

En este paso, agregará las variables de entorno a la configuración de la aplicación web en Azure. En la línea de comandos, escriba lo siguiente:

	azure site appsetting add
		STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


Reemplace **<storage account name>** por el nombre de la cuenta de almacenamiento creada anteriormente y **<storage access key>** por la clave de acceso principal para la cuenta de almacenamiento. (Utilice los mismos valores que el archivo config.json que creó anteriormente).

También puede establecer las variables de entorno en el [Portal de Azure](https://portal.azure.com/):

1.  Abra la hoja de la aplicación web haciendo clic en **Examinar** > **Aplicaciones web** > el nombre de la aplicación web.

1.  En la hoja de la aplicación web, haga clic en **Toda la configuración** > **Configuración de la aplicación**.

  	<!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->

1.  Desplácese hacia abajo hasta la sección **Configuración de la aplicación** y agregue los pares clave/valor.

  	![Configuración de aplicaciones](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

1. Haga clic en **GUARDAR**.


### Publicación de la aplicación

Para publicar la aplicación, confirme los archivos de código en Git y, a continuación, inserte en azure/master.

1. Restablezca las credenciales de implementación.

		azure site deployment user set <name> <password>

2. Agregue y confirme los archivos de aplicación.

		git add .
		git commit -m "adding files"

3. Inserte la confirmación en la aplicación web del Servicio de aplicaciones:

		git push azure master

	Utilice **master** como bifurcación de destino. Al final de la implementación se verá una instrucción similar a la siguiente:

		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. Después de que se haya completado la operación de inserción, diríjase a la URL de la aplicación web que se devolvió anteriormente usando el comando `azure create site` para ver su aplicación.


## Pasos siguientes

Si bien los pasos de este artículo describen el uso del servicio Tabla para almacenar información, puede también usar MongoDB. Consulte [Aplicación web Node.js con MongoDB] para obtener más información.

## Recursos adicionales

[CLI de Azure]

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[Compilación e implementación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure]: web-sites-nodejs-develop-deploy-mac.md
[Desarrollo e implementación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure]: web-sites-nodejs-develop-deploy-mac.md
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[Azure Developer Center]: /develop/nodejs/

[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git remoto]: http://git-scm.com/docs/git-remote

[Aplicación web Node.js con MongoDB]: web-sites-nodejs-store-data-mongodb.md
[CLI de Azure]: ../xplat-cli-install.md

[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md
 
<!-- Image References -->

[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

<!---HONumber=AcomDC_0128_2016-->