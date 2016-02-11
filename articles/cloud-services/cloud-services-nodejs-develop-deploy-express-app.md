<properties 
	pageTitle="Aplicaciones web con Express (Node.js) | Microsoft Azure" 
	description="Este tutorial se basa en el tutorial sobre servicios en la nube y muestra cómo usar el módulo Express." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>






# Compilación de una aplicación web Node.js mediante Express en un servicio en la nube de Azure

Node.js incluye un conjunto mínimo de funcionalidad en el tiempo de ejecución del núcleo. Los desarrolladores suelen utilizar módulos de terceros para proporcionar funcionalidad adicional al desarrollar una aplicación Node.js. En este tutorial, aprenderá a crear una nueva aplicación mediante el módulo [Express][], que proporciona un marco MVC para la creación de aplicaciones web Node.js.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Un explorador web muestra la página de presentación de Express en Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##Creación de un proyecto de servicio en la nube

Realice los siguientes pasos para crear un nuevo proyecto de servicio en la nube con nombre "expressapp":

1. Desde el **menú Inicio** o la **pantalla Inicio**, busque **PowerShell de Azure**. Finalmente, haga clic con el botón secundario en **PowerShell de Azure** y seleccione **Ejecutar como administrador**.

	![Icono de Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. Cambie los directorios al directorio **c:\\node** y, a continuación, escriba los siguientes comandos para crear una nueva solución llamada **expressapp** y un rol web llamado **WebRole1**:

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

	> [AZURE.NOTE]De forma predeterminada, **Add-AzureNodeWebRole** usa una versión anterior de Node.js. La instrucción **Set-AzureServiceProjectRole** anterior indica a Azure que debe usar la v0.10.21 del nodo. Tenga en cuenta que los parámetros distinguen mayúsculas de minúsculas. Puede comprobar la versión correcta de Node.js que se ha seleccionado comprobando la propiedad **motores** en **WebRole1\\package.json**.

##Instalación de Express

1. Instale el generador Express mediante el siguiente comando:

		PS C:\node\expressapp> npm install express-generator -g

	La salida del comando npm debería parecerse a la siguiente.

	![Windows PowerShell muestra la salida del comando npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Cambie los directorios por el directorio **WebRole1** y use el comando express para generar una nueva aplicación:

        PS C:\node\expressapp\WebRole1> express

	Se le solicitará que sobrescriba la aplicación anterior. Escriba **y** o **yes** para continuar. Express generará el archivo app.js y una estructura de carpetas para compilar su aplicación.

	![La salida del comando express.](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Para instalar dependencias adicionales definidas en el archivo package.json, escriba el siguiente comando:

        PS C:\node\expressapp\WebRole1> npm install

	![Salida del comando npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Use el comando siguiente para copiar el archivo **bin/www** en **server.js**. Esto es para que el servicio en la nube pueda encontrar el punto de entrada de esta aplicación.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	Después de que se complete este comando, debe tener el archivo **server.js** en el directorio WebRole1.

7.  Modifique el archivo **server.js** para quitar uno de los caracteres '.' de la línea siguiente.

		var app = require('../app');

	Tras realizar esta modificación, la línea debería tener el aspecto siguiente.

		var app = require('./app');

	Este cambio es necesario porque el archivo se movió (anteriormente **bin/www**) al mismo directorio que el archivo de aplicación que se requiere. Después de realizar este cambio, guarde el archivo **server.js**.

8.  Use el siguiente comando para ejecutar la aplicación en el emulador de Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![Una página web que contiene la bienvenida a Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Modificación de la vista

Modifique ahora la vista para visualizar el mensaje "Welcome to Express in Azure".

1.  Escriba el siguiente comando para abrir el archivo index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![El contenido del archivo index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade es el motor de vistas predeterminado que utilizan las aplicaciones Express. Para obtener más información acerca del motor de vistas Jade, consulte [http://jade-lang.com][].

2.  Modifique la última línea de texto para anexarla **en Azure**.

	![El archivo index.jade, la última línea muestra: Bienvenido a #{title} en Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Guarde el archivo y salga de la aplicación Bloc de notas.

4.  Actualice el explorador para ver los cambios.

	![Ventana del explorador, la página contiene el texto Welcome to Express in Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Después de evaluar la aplicación, utilice el cmdlet **Stop-AzureEmulator** para detener el emulador.

##Publicación de la aplicación en Azure

En la ventana de Azure PowerShell, utilice el cmdlet **Publish-AzureServiceProject** para implementar la aplicación en un servicio en la nube.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Una vez finalizada la operación de implementación, el explorador abrirá y mostrará la página web.

![Un explorador web que muestra la página de Express. La URL indica que la página ya está hospedada en Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com

 

<!---HONumber=AcomDC_0114_2016-->