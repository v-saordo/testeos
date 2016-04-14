<properties
	pageTitle="Almacenamiento del código del proyecto back-end de JavaScript en el control de código fuente | Servicios móviles de Azure"
	description="Obtenga información acerca de cómo almacenar los módulos y los archivos de script de servidor en un repositorio Git local del equipo."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="glenga"/>

# Almacenamiento del código del proyecto de Servicios móviles en el control de código fuente

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-store-code-source-control.md)
- [Javascript backend](mobile-services-store-scripts-source-control.md)

Este tema le muestra cómo usar el control del código fuente proporcionado por Servicios móviles de Azure para almacenar los scripts del servidor. Los scripts y otros archivos de código de back-end de JavaScript pueden promoverse desde el repositorio local al servicio móvil de producción. También se muestra cómo definir código compartido que pueden requerir varios scripts y cómo usar el archivo package.json para agregar módulos Node.js al servicio móvil.

Para completar este tutorial, ya debe haber creado un servicio móvil al completar el tutorial [Introducción a Servicios móviles].

##<a name="enable-source-control"></a>Habilitación del control de código fuente en el servicio móvil

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Instalación de Git y creación del repositorio local

1. Instale Git en su equipo local.

	Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Installing Git] para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

	> [AZURE.NOTE]
	> Algunos sistemas operativos disponen de versiones de Git en línea de comandos y de GUI. Las instrucciones proporcionadas en este artículo utilizan la versión en línea de comandos.

2. Abra una línea de comandos, como **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.

3. Desde la línea de comandos, cambie al directorio donde almacenará los scripts. Por ejemplo: `cd SourceControl`.

4. Use el comando siguiente para crear una copia local del nuevo repositorio Git. Deberá reemplazar `<your_git_URL>` por la dirección URL del repositorio Git del servicio móvil:

		git clone <your_git_URL>

5. Cuando se le solicite, escriba el nombre de usuario y la contraseña que estableció al habilitar el control de código fuente en el servicio móvil. Después de realizar la autenticación correctamente, aparecerá una serie de respuestas, como la siguiente:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Desplácese al directorio desde el que ejecutó el comando `git clone` y observe la estructura correspondiente a continuación:

	![4][4]

	En este caso, se crea un nuevo directorio con el nombre del servicio móvil, que es el repositorio local para el servicio de datos.

7. Abra la subcarpeta .\\service\\table y observe que contiene un archivo TodoItem.json, que es una representación JSON de los permisos de funcionamiento de la tabla TodoItem.

	Tras definir scripts de servidor en esta tabla, también aparecerán uno o varios archivos denominados <code>TodoItem._&lt;operación&gt;_.js</code> que contienen los scripts para la operación de tabla determinada. Los scripts del programador y de la API personalizada se mantienen en carpetas independientes con esos nombres respectivos. Para obtener más información, consulte [Control de código fuente].

Ahora que ha creado su repositorio local, puede realizar cambios en los scripts del servidor e insertarlos en el servicio móvil.

##<a name="deploy-scripts"></a>Implementación de archivos de script actualizados en el servicio móvil

1. Desplácese a la subcarpeta .\\service\\table y, si aún no existe un archivo todoitem.insert.js, créelo en este momento.

2. Abra el nuevo archivo todoitem.insert.js en un editor de texto, pegue en él el código siguiente y guarde los cambios:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Este código se limita a escribir el elemento insertado en el registro. Si el archivo ya contiene código, solo tiene agregarle código válido de JavaScript, como una llamada a `console.log()` y, a continuación, guardar los cambios.

3. En el símbolo del sistema de Git, escriba el comando siguiente para empezar a realizar un seguimiento del nuevo archivo de scripts:

		$ git add .


4. Escriba el comando siguiente para confirmar los cambios:

		$ git commit -m "updated the insert script"

5. Escriba el comando siguiente para cargar los cambios en el repositorio remoto:

		$ git push origin master

	Aparecerá una serie de comandos que indica que la confirmación se ha implementado en el servicio móvil.

6. Nuevamente en el [Portal de Azure clásico], haga clic en la pestaña **Datos**, en la tabla **TodoItem** y en **Script**; a continuación, seleccione la operación **Insert**.
7.Observe que el script de operación de inserción que se muestra es el mismo que el del código de JavaScript que acaba de cargar en el repositorio.

##<a name="use-npm"></a>Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor

Servicios móviles proporciona acceso a todo el conjunto de módulos Node.js básicos, que puede usar en su código mediante la función **require**. Asimismo, el servicio móvil puede usar módulos Node.js que no formen parte del paquete Node.js básico, y el usuario puede incluso definir su propio código compartido como módulos Node.js. Para obtener más información acerca de la creación de módulos, consulte la sección [Módulos] en la documentación de referencia de API Node.js.

La manera recomendada de agregar módulos Node.js al servicio móvil es agregando referencias al archivo package.json del servicio. A continuación, agregará el módulo Node.js [node-uuid] al servicio móvil a través de la actualización del archivo package.json. Cuando la actualización se inserte en Azure, el servicio móvil se reiniciará y se instalará el módulo. Este módulo se usará después para generar un nuevo valor de GUID para la propiedad **uuid** en los elementos insertados.

2. Vaya a la carpeta `.\service` del repositorio Git local y abra el archivo package.json en un editor de texto; agregue el siguiente campo al objeto **dependencies**:

		"node-uuid": "~1.4.3"

	>[AZURE.NOTE]Esta actualización para el archivo package.json producirá un reinicio del servicio móvil después de insertar la confirmación.

4. Ahora, desplácese a la subcarpeta .\\service\\table, abra el archivo todoitem.insert.js y modifíquelo de la siguiente forma:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
		}

	Este código agrega una columna uuid a la tabla y la rellena con identificadores GUID únicos.

5. Al igual que en la sección anterior, escriba el siguiente comando en el símbolo del sistema de Git:

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master

	Al hacerlo, se agrega el nuevo archivo, se confirman los cambios y se insertan el nuevo módulo node-uuid y los cambios del script todoitem.insert.js en el servicio móvil.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, ya sabe cómo almacenar sus scripts en el control de código fuente. Considere la posibilidad de obtener más información sobre cómo trabajar con scripts de servidor y con las API personalizadas:

+ [Uso de scripts del servidor en Servicios móviles] 
	<br/>Muestra cómo trabajar con scripts del servidor, el programador de trabajos y las API personalizadas.

<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[Control de código fuente]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introducción a Servicios móviles]: mobile-services-ios-get-started.md
[Uso de scripts del servidor en Servicios móviles]: mobile-services-how-to-use-server-scripts.md
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Módulos]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!----HONumber=AcomDC_1210_2015-->