<properties
	pageTitle="Compilación e implementación de una aplicación de API de Node.js en el Servicio de aplicaciones de Azure"
	description="Aprenda a crear un paquete de aplicación de API de Node.js y a implementarlo en el Servicio de aplicaciones de Azure."
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra "/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="11/27/2015"
	ms.author="bradygaster"/>

# Compilación e implementación de una aplicación de API de Node.js en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Requisitos previos
1. [Node.js](nodejs.org) que se ejecuta en la máquina de desarrollo (en este ejemplo se asume que tiene instalado Node.js versión 4.2.2)
1. Cuenta [GitHub](https://github.com/)
1. [Cuenta de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) de Microsoft Azure
1. Git instalado en la estación de trabajo de desarrollo local

## Instrucciones de instalación
Los comandos siguientes se deben ejecutar con la línea de comandos de Node.js. Mediante el generador Yo de Swaggerize, aplique la técnica scaffolding de la línea base del código de Node.js, que necesitará para atender las solicitudes HTTP definidas en un archivo JSON de Swagger.
 
1. Instale **yo** y los módulos NPM de **generator-swaggerize** de manera global.

        npm install -g yo
	    npm install -g generator-swaggerize
		
1. Clone el [repositorio de GitHub que contiene el código de ejemplo](https://github.com/Azure-Samples/app-service-api-node-contact-list).

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
				
1. Ejecute el comando para aplicar la técnica scaffolding a la API basada en el archivo **api.json** incluido con el código fuente. El archivo **api.json** es un archivo Swagger que representa la API real a la que está aplicando la técnica scaffolding mediante el comando "yo swaggerize" durante el paso siguiente.

        yo swaggerize
        
    **Nota:** API.json no es lo mismo que el archivo *apiapp.json* procedente del período de tiempo de la vista previa de Aplicaciones de API.

1. Swaggerize va aplicar la técnica scaffolding a los controladores y a la configuración de los metadatos de Swagger incluidos en **api.json**. Durante el proceso de scaffolding, se le formularán una serie de preguntas, como su nombre de usuario de GitHub y la dirección de correo electrónico. Esta información se usa para generar el archivo **package.json** en la carpeta de la aplicación. De todas las preguntas formuladas durante el proceso de scaffolding, la más importante es que se seleccione **Rápida** cuando se le solicite, dado que este ejemplo hará uso del motor de vista rápida para generar más adelante la página de ayuda de Swagger cuando la aplicación de API se ejecute en Azure (o de manera local).

	![Línea de comandos de swaggerize](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
1. Desplácese a la carpeta que contiene el código con técnica scaffolding (en este caso, la subcarpeta *ContactList*). A continuación, instale el módulo de NPM **jsonpath**.

        npm install --save jsonpath
        
    Verá los resultados de la instalación en la experiencia de línea de comandos.

    ![Instalación de Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Instale el módulo de NPM **swaggerize-ui**.

        npm install --save swaggerize-ui
        
    Verá los resultados de la instalación en la experiencia de línea de comandos.

    ![Instalación de la interfaz de usuario de swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

1. Copie la carpeta **lib** de la carpeta **start** a la carpeta **ContactList** creada por el procesador de scaffolding.

1. Reemplace el código del archivo **handlers/contacts.js** por el código siguiente. Este código usa los datos JSON almacenados en el archivo **lib/contacts.json** proporcionado por **lib/contactRepository.js**. El siguiente código nuevo contacts.js responderá a las solicitudes HTTP para obtener todos los contactos mediante este código.

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Reemplace el código del archivo **handlers/contacts/{id}.js** por el código siguiente, que va a usar **lib/contactRepository.js** para obtener el contacto solicitado por la solicitud HTTP y devolverlo como una carga de JSON.

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Reemplace el código de **server.js** por el código siguiente. Tenga en cuenta que los cambios realizados en el archivo server.js se resaltan con comentarios para que pueda ver los cambios realizados.

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth change
            app.setHost(undefined); // sixth and final change
        });

1. Active el servidor mediante el archivo ejecutable de línea de comandos de Node.js.

        node server.js

    Al ejecutar este comando, se inicia el servidor HTTP de Node.js y se empieza a atender a la API.

1. Cuando se desplace a ****http://localhost:8000/contacts**, aparecerá el resultado de JSON de la lista de contactos (o se le solicita que lo descargue, según el navegador).

    ![Todas las llamadas de la API de contactos](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Cuando vaya a ****http://localhost:8000/contacts/2**, verá el contacto representado por ese valor de identificador.

    ![Llamada de la API de contactos específica](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Los datos JSON de Swagger se envían a través del punto de conexión **/swagger**:

    ![Json de Swagger de contactos](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. La interfaz de usuario de Swagger se envían a través del punto de conexión **/docs**. En la interfaz de usuario de Swagger puede usar las características de cliente HTML enriquecidas para probar la API.

    ![Interfaz de usuario de Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## Creación de una nueva aplicación de API en el Portal de Azure
En esta sección le guiaremos por el proceso de creación de una aplicación de API nueva y vacía en Azure. Después, deberá conectar la aplicación a un repositorio de Git para que pueda habilitar la entrega continua de los cambios de código.

El repositorio de GitHub desde el que se clonó el código fuente no es el mismo repositorio en el que va a insertar el código para su implementación. El repositorio de GitHub de ejemplo incluye el estado "inicial" del código y ahora que aplicó la técnica scaffolding al estado "final" del código, debe insertar ese código solo en el repositorio de Git asociado a su aplicación de API. El primer paso será crear la aplicación de API con el Portal de Azure y después:

1. Vaya al [Portal de Azure](https://portal.azure.com/). 

1. Cree una nueva aplicación de API.

    ![Nuevo portal de aplicaciones de API](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

1. Puede agregar la nueva aplicación de API a un grupo de recursos existente o a un plan del Servicio de aplicaciones, o bien puede crear un nuevo grupo de recursos y un plan del Servicio de aplicaciones, tal como se muestra en la captura de pantalla siguiente.

    ![Hoja de creación de aplicaciones de API](media/app-service-api-nodejs-api-app/api-app-creation-blade.png)

1. Una vez creada la aplicación de API en el Portal, vaya a la hoja que contiene la configuración de su aplicación de API, tal como se muestra a continuación.

    ![Navegación del portal a Configuración](media/app-service-api-nodejs-api-app/portal-nav-to-settings.png)

1. Haga clic en el elemento de navegación **Credenciales de implementación** del menú Configuración. Cuando se abre la hoja, agregue un nombre de usuario y una contraseña que usará para publicar el código de Node.js en la aplicación de API. Después, haga clic en el botón **Guardar** situado en la hoja **Configurar credenciales de implementación**.

    ![Credenciales de implementación](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Una vez establecidas las credenciales de implementación, puede crear un repositorio de Git que esté asociado con el Servicio de aplicaciones. Cada vez que inserta código en este repositorio, el Servicio de aplicaciones de Azure recogerá los cambios y los implementará directamente en la instancia de la aplicación de API. Para crear un repositorio de Git para asociarlo a su sitio, haga clic en el elemento de menú **Implementación continua** en la hoja del menú Configuración, tal como se muestra a continuación. Después, seleccione la opción **Repositorio de Git local** en la hoja **Elegir origen**. A continuación, haga clic en el botón Aceptar para crear el repositorio de Git.

    ![Creación de repositorio de Git](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Una vez creado el repositorio de Git, se cambiará la hoja y mostrará las implementaciones activas. Como el repositorio es nuevo, no debe tener implementaciones activas en la lista.

    ![Sin implementaciones activas](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. El último paso será copiar la dirección URL de repositorio de Git desde el portal. Para ello, vaya a la hoja de la nueva aplicación de API y consulte la sección **Essentials** de la hoja. Debería ver la **URL de clonación de Git** en la sección Essentials. Junto a ella, se muestra un icono que copiará la dirección URL en el Portapapeles. Puede hacer clic en él para copiar la dirección URL (el botón aparece cuando pasa el mouse sobre la dirección URL), o seleccionar la dirección URL completa y copiarla en el Portapapeles.

    ![Obtención de la dirección URL de Git desde el Portal](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Nota**: necesitará la dirección URL de clonación de Git en el paso siguiente, por tanto asegúrese de guardarla de momento.

Ahora que dispone de una nueva aplicación de API con una copia de seguridad del repositorio de Git, puede insertar código en el repositorio y usar las características de implementación continua de Azure para implementar automáticamente los cambios.

## Implementación del código de aplicación de API en Azure
Con las características de entrega continua integradas que proporciona Servicio de aplicaciones de Azure, simplemente puede confirmar el código en un repositorio de Git asociado con el Servicio de aplicaciones y Azure recogerá su código fuente y lo implementará en la aplicación de API.

1. Copie la carpeta **final/src/ContactList** creada por el procesador de scaffolding swaggerize en el escritorio o en otra carpeta, ya que creará un nuevo repositorio Git local para el código que debe residir fuera del repositorio principal que clonó desde GitHub y que contiene el código de introducción. 

1. Use la experiencia de línea de comandos de Node.js para navegar a la nueva carpeta. Una vez allí, ejecute el comando siguiente para crear un nuevo repositorio de Git local.

        git init

    Este comando creará un repositorio de Git local y le mostrará una confirmación de que se inicializó el nuevo repositorio.

    ![Nuevo repositorio de Git local](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Use la experiencia de línea de comandos de Node.js para ejecutar el comando siguiente, que agregará un Git remoto al repositorio local. El repositorio remoto será el que acaba de crear y de asociar con su aplicación de API que se ejecuta en Azure.

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Nota**: reemplace la cadena "YOUR\_GIT\_CLONE\_URL\_HERE" anterior por su propia dirección URL de clonación de Git que copió anteriormente.

1. A continuación, ejecute los dos comandos siguientes desde la experiencia de línea de comandos de Node.js.

        git add .
        git commit -m "initial revision"

    Cuando haya completado estos dos comandos, debería ver algo parecido a la captura de pantalla siguiente en la ventana de línea de comandos.

    ![Salida de confirmación de Git](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Para insertar el código en Azure, que desencadenará una implementación a la aplicación de API, ejecute el comando siguiente en la línea de comandos de Node.js. Cuando se le solicite una contraseña, use la que utilizó anteriormente al crear las credenciales de implementación en el Portal de Azure.

        git push azure master

1. Si se desplaza de nuevo a la hoja **Implementación continua** de la aplicación de API, verá la implementación que se está produciendo.

    ![Implementación que está ocurriendo](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Simultáneamente, la línea de comandos Node.js reflejará el estado de la implementación mientras está ocurriendo.

    ![Implementación de Node Js que está ocurriendo](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

1. Cuando se haya completado la implementación, la hoja **Implementación continua** reflejará la implementación correcta de los cambios de código en la aplicación de API. Copie la **URL** en la sección **Essentials** de la hoja Aplicación de API.

    ![Implementación completada](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Con un cliente de la API de REST, como Postman o Fiddler (o el explorador web), proporcione la dirección URL de la llamada de API de los contactos, que debería estar en el punto de conexión **/contacts** de la aplicación de API.

    **Nota:** la dirección URL será similar a http://myapiapp.azurewebsites.net/contacts.

    Cuando se envía una solicitud GET a este punto de conexión, debe ver el resultado JSON de la aplicación de API.

    ![Postman que llega a la API](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

## Pasos siguientes

Ya ha creado e implementado correctamente su primera aplicación de API con Node.js. El siguiente tutorial de la serie de introducción a Aplicaciones de API muestra cómo [consumir aplicaciones de API desde clientes de JavaScript mediante CORS](app-service-api-cors-consume-javascript.md).

Para basarse en este ejemplo, puede agregar código a los controladores para almacenar los datos en una base de datos o en el disco de la instancia de la aplicación de API. Ahora que ya conectó la implementación continua, el cambio de la funcionalidad de la aplicación de API y la su ampliación es tan fácil como cambiar en insertar de código en el repositorio de Git.

<!---HONumber=AcomDC_0128_2016-->