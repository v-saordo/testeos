<properties pageTitle="Trabajo con módulos Node.js" description="Aprenda a trabajar con módulos de Node.js al usar Sitios web Azure o Servicios en la nube." services="" documentationCenter="nodejs" authors="rmcmurray" manager="wpickett" editor=""/>

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/09/2016" ms.author="robmcm"/>





# Uso de módulos Node.js con aplicaciones de Azure

Este documento le proporciona orientación sobre el uso de módulos Node.js con aplicaciones hospedadas en Azure. Mediante este documento podrá asegurarse de que su aplicación usa una versión específica del módulo y de que usa módulos nativos con Azure.

Si ya está familiarizado con el uso de módulos Node.js, archivos **package.json** y **npm-shrinkwrap.json**, a continuación se muestra un resumen rápido del contenido de este artículo:

* Los sitios web de Azure entienden los archivos **package.json** y **npm-shrinkwrap.json** y pueden instalar módulos según las entradas de estos archivos.
* Servicios en la nube de Azure esperan que todos los módulos se instalen en el entorno de desarrollo y que el directorio **node\_modules** se incluya como parte del paquete de implementación.

> [AZURE.NOTE]No se tratan las máquinas virtuales de Azure en este artículo, ya que la experiencia de implementación en una VM depende del sistema operativo hospedado por la máquina virtual.

> [AZURE.NOTE]Es posible ofrecer compatibilidad para la instalación de módulos con archivos **package.json** o **npm-shrinkwrap.json** en Azure. Sin embargo, esto requiere la personalización de los scripts predeterminados que usan los proyectos de Servicio en la nube. Para obtener un ejemplo sobre como conseguir esto, consulte [Tarea de inicio de Azure para ejecutar la instalación de npm y evitar la implementación de módulos de nodos](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure).

##Módulos Node.js

Los módulos son paquetes JavaScript que pueden cargarse y que proporcionan una funcionalidad específica para su aplicación. Un módulo se instala normalmente con la herramienta de la línea de comandos **npm**. Sin embargo, algunos (como el módulo http) se proporcionan como parte del paquete Node.js principal.

Cuando se instalan los módulos, se almacenan en el directorio **node\_modules** en la raíz de la estructura del directorio de la aplicación. Cada uno de los módulos dentro del directorio **node\_modules** mantiene su propio directorio **node\_modules** que contiene los módulos de los que depende y esto se repite para cada módulo hasta el final de la cadena de dependencia. De este modo, los módulos instalados pueden disponer de los requisitos de la propia versión para los módulos de los que depende. Sin embargo, esto puede provocar una estructura de directorio bastante amplia.

Cuando se implementa el directorio **node\_modules** como parte de su aplicación, aumentará el tamaño de la implementación en comparación con el uso del archivo **package.json** o **npm-shrinkwrap.json**. Sin embargo, esto no garantiza que la versión de los módulos usados en la producción sea la misma que la de los usados en el desarrollo.

###Módulos nativos

Mientras que la mayoría de los módulos son archivos JavaScript sin formato, algunos módulos son imágenes binarias específicas de plataforma. Estos módulos se compilan en el momento de la instalación, normalmente mediante Python y node-gyp. Puesto que los servicios en la nube de Azure se basan en la carpeta **node\_modules** que se implementa como parte de la aplicación, los módulos nativos incluidos como parte de los módulos instalados deben funcionar en un servicio en la nube siempre que se haya instalado y compilado en un sistema de desarrollo de Windows.

Sitios web Azure no es compatible con todos los módulos nativos y podría presentar errores en el momento de compilar los que incluyen requisitos previos muy específicos. Mientras que algunos módulos conocidos, como MongoDB, tienen dependencias nativas opcionales y funcionan correctamente sin ellos, dos soluciones alternativas han demostrado funcionar correctamente con casi todos los módulos nativos actualmente disponibles:

* Ejecute **npm install** en una máquina con Windows que tenga instalados todos los requisitos previos del módulo nativo. A continuación, implemente la carpeta **node\_modules** creada como parte de la aplicación en Sitios web Azure.
* Sitios web Azure se puede configurar para ejecutar scripts de bash o shell personalizados durante la implementación, lo que brinda la oportunidad de ejecutar comandos personalizados y configurar de manera precisa la manera en que se ejecuta **npm install**. Para ver un vídeo que muestra cómo hacerlo, [Scripts de implementación de sitios web personalizados con Kudu].

###Uso de un archivo package.json

El archivo **package.json** sirve para especificar las dependencias de nivel superior que requiere la aplicación de manera que la plataforma de hospedaje pueda instalar las dependencias evitando la necesidad de incluir la carpeta **node\_packages** como parte de la implementación. Una vez implementada la aplicación, el comando **npm install** se usa para analizar el archivo **package.json** e instalar todas las dependencias enumeradas.

Durante el desarrollo, puede usar los parámetros **--save**, **--save-dev** o **--save-optional** cuando instale los módulos para agregar una entrada para el módulo al archivo **package.json** automáticamente. Para obtener más información, consulte [npm-install](https://npmjs.org/doc/install.html).

Un posible problema con el archivo **package.json** es que solo especifica la versión de dependencias de nivel superior. Cada módulo instalado puede o no especificar la versión de los módulos de los que depende y, por lo tanto, es posible que acabe con una cadena de dependencia distinta a la que usó en el desarrollo.

> [AZURE.NOTE]Cuando implemente un sitio web de Azure, si el archivo <b>package.json</b> hace referencia a un módulo nativo, verá un error parecido al siguiente cuando publique la aplicación con Git:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###Uso de un archivo npm-shrinkwrap.json

El archivo **npm-shrinkwrap.json** es un intento de solucionar las limitaciones de la versión del módulo del archivo **package.json**. Mientras que el archivo **package.json** solo incluye versiones para los módulos de nivel superior, el archivo **npm-shrinkwrap.json** contiene los requisitos de la versión para la cadena de dependencia del módulo completa.

Cuando la aplicación esté preparada para la producción, puede bloquear los requisitos de la versión y crear un archivo **npm-shrinkwrap.json** mediante el comando **npm shrinkwrap**. De esta forma, se usarán las versiones instaladas actualmente en la carpeta **node\_modules** y se registrarán en el archivo **npm-shrinkwrap.json**. Una vez implementada la aplicación en el entorno de hospedaje, se usa el comando **npm install** para analizar el archivo **npm-shrinkwrap.json** e instalar todas las dependencias enumeradas. Para obtener más información, consulte [npm-install](https://npmjs.org/doc/install.html).

> [AZURE.NOTE]Cuando implemente un sitio web de Azure, si el archivo <b>npm-shrinkwrap.json</b> hace referencia a un módulo nativo, verá un error parecido al siguiente cuando publique la aplicación con Git:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##Pasos siguientes

Ahora que sabe cómo usar los módulos Node.js con Azure, puede aprender a [especificar la versión de Node.js], [crear e implementar un sitio web de Node.js] y [usar la interfaz de la línea de comandos de Azure para Mac y Linux].

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[especificar la versión de Node.js]: nodejs-specify-node-version-azure-apps.md
[usar la interfaz de la línea de comandos de Azure para Mac y Linux]: xplat-cli-install.md
[crear e implementar un sitio web de Node.js]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Publishing with Git]: web-sites-publish-source-control.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Scripts de implementación de sitios web personalizados con Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/

<!---HONumber=AcomDC_0114_2016-->