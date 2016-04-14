El [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) admite la implementación continua en Aplicaciones web de herramientas de control de código fuente y de repositorio como BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial y TFS. Estas herramientas se pueden utilizar para mantener el contenido y el código de una aplicación y, a continuación, aplicar rápida y fácilmente los cambios realizados en la aplicación web de Azure en el momento que se desee.

En este artículo, aprenderá a utilizar Git para publicar directamente desde su equipo local en Aplicaciones web (en Azure, este método de publicación se denomina **Git local**). También aprenderá a habilitar la implementación continua desde sitios repositorio como BitBucket, CodePlex, Dropbox, GitHub o Mercurial. Para obtener información acerca de la utilización de TFS para la implementación continua, consulte [Entrega continua a Azure con Visual Studio Team Services].

> [AZURE.NOTE] Muchos de los comandos de Git que se describen en este artículo se ejecutan automáticamente al crear una aplicación web con las [Herramientas de la línea de comandos de Azure para Mac y Linux](/develop/nodejs/how-to-guides/command-line-tools/).

## <a id="Step1"></a>Paso 1: Instalación de Git

Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Installing Git] para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

> [AZURE.NOTE] Algunos sistemas operativos disponen de versiones de Git en línea de comandos y de GUI. Las instrucciones proporcionadas en este artículo utilizan la versión en línea de comandos.

## <a id="Step2"></a>Paso 2: Creación de un repositorio local

Realice las tareas siguientes al crear un nuevo repositorio Git.

1. Crear un directorio llamado MyGitRepository que contenga el repositorio de Git y los archivos de la aplicación web.

2. Abra una herramienta de línea de comandos como, por ejemplo, **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.

3. Desde la línea de comandos, cambie al directorio MyGitRepository.

		cd MyGitRepository

4. Utilice el comando siguiente para inicializar un nuevo repositorio Git:

		git init

	Debería devolver un mensaje como **Se inicializó un repositorio Git vacío en [ruta de acceso]**.

## <a id="Step3"></a>Paso 3: Incorporación de una página web

Aplicaciones web admite aplicaciones creadas en varios lenguajes de programación. Para este ejemplo, se utilizará un archivo .html estático.

1. Con un editor de texto, cree un nuevo archivo denominado **index.html** en el directorio raíz del repositorio Git (el directorio MyGitRepository que ha creado anteriormente).

2. Agregue el texto siguiente como contenido para el archivo index.htm y guárdelo.

		Hello Git!

3. Desde la línea de comandos, compruebe que está en la raíz de su repositorio Git. A continuación, utilice el comando siguiente para agregar el archivo **index.html** al repositorio:

		git add index.html 

	> [AZURE.NOTE] Puede encontrar la ayuda para cualquier comando git escribiendo -help o --help tras el comando. Por ejemplo, para ver las opciones de parámetro para el comando "add", escriba "git add -help" para obtener ayuda para la línea de comandos o "git add --help" para recibir ayuda más detallada.

4. A continuación, confirme los cambios en el repositorio mediante el siguiente comando:

		git commit -m "Adding index.html to the repository"

	Debería ver una salida similar a la siguiente:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

## <a id="Step4"></a>Habilitación del repositorio de aplicaciones web

Lleve a cabo los pasos siguientes para habilitar un repositorio de Git para su aplicación web.

1. Inicie sesión en el [Portal de Azure].

2. En la hoja de la aplicación web, haga clic en **Configuración > Implementación continua**. Haga clic en **Elegir origen**, **Repositorio de Git local** y, a continuación, en **Aceptar**.

	![Repositorio de Git local](./media/publishing-with-git/azure1-local-git.png)

4. Si esta es la primera vez que configura un repositorio en Azure, tendrá que crear unas credenciales de inicio de sesión para él. Las usará para iniciar sesión en el repositorio de Azure y aplicar cambios desde su repositorio Git local. En la hoja de la aplicación web, haga clic en **Configuración > Credenciales de implementación** y configure el nombre de usuario y la contraseña para la implementación. Cuando haya terminado, haga clic en **Aceptar**.

	![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>Implementación del proyecto

* [Inserción de archivos locales en Azure (Git local)](#Step6)
* [Implementación de archivos desde un sitio web de repositorio como BitBucket, CodePlex, Dropbox, GitHub o Mercurial](#Step7)
* [Implementación de una solución de Visual Studio desde BitBucket, CodePlex, Dropbox, GitHub o Mercurial](#Step75)

Siga los pasos que se indican a continuación para publicar una aplicación web en Azure mediante un Git local.

1. En la hoja de la aplicación web, haga clic en **Configuración > Propiedades** para la **Dirección URL de Git**.

	![](./media/publishing-with-git/azure3-repo-details.png)

	**Dirección URL de Git** es la referencia remota en la que se realizará la implementación desde el repositorio local. Usará esta dirección URL en los pasos siguientes.

1. Utilizando la línea de comandos, compruebe que está en la raíz de su repositorio Git que contiene el archivo index.html creado anteriormente.

2. Use `git remote` para agregar la referencia remota que aparecía en **Dirección URL de Git** en el paso 1. El comando será similar al siguiente:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE] El comando **remote** agrega una referencia con nombre a un repositorio remoto. En este ejemplo, se crea una referencia llamada "azure" para el repositorio de la aplicación web.

1. Utilice los comandos siguientes de la línea de comandos para insertar el contenido actual del repositorio desde el repositorio local al repositorio remoto "azure":

		git push azure master

	Se le solicitará la contraseña que ha creado antes al restablecer las credenciales de implementación en el portal. Escriba la contraseña (tenga en cuenta que Gitbash no envía los asteriscos a la consola mientras escribe la contraseña). Debería ver una salida similar a la siguiente:

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [AZURE.NOTE] El repositorio creado para la aplicación espera que las solicitudes de inserción tengan como destino la bifurcación <strong>principal</strong> de su repositorio, que se usará como contenido de la aplicación web.

2. Vuelva a la hoja de la aplicación web en el Portal de Azure. **No se encuentra implementación** debe cambiarse a **Implementación activa** con una entrada de registro de la última inserción.

	![](./media/publishing-with-git/azure4-deployed.png)

2. Haga clic en el vínculo que aparece debajo de **URL** en la parte superior de la hoja de la aplicación web para comprobar que se ha implementado el archivo **index.html**. Se mostrará una página que contiene "Hello Git!".

	![Página web con "Hello Git!"][hello-git]

3. Con un editor de texto, cambie el archivo **index.html** para que contenga "Yay!" y, a continuación, guarde el archivo.

4. Utilice los comandos siguientes desde la línea de comandos para **agregar** y **confirmar** los cambios y, a continuación, **insertar** los cambios en el repositorio remoto:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Una vez completado el comando **push**, actualice el explorador (puede tener que presionar Ctrl+F5 para que el explorador se actualice correctamente) y observe que el contenido de la página ahora refleja el último cambio confirmado.

### <a id="Step7"></a>Implementación de archivos desde un sitio de repositorio como BitBucket, CodePlex, Dropbox, GitHub o Mercurial

La inserción de archivos locales en Azure mediante Git local permite insertar manualmente las actualizaciones de un proyecto local en una aplicación web en Azure, mientras se implementan los resultados de BitBucket, CodePlex, Dropbox, GitHub o Mercurial en un proceso de implementación continua en el que Azure extraerá las últimas actualizaciones del proyecto.

Aunque la consecuencia es que con ambos métodos el proyecto se implementa en Aplicaciones web, la implementación continua es útil cuando hay muchas personas trabajando en un proyecto y se desea estar seguro de que siempre se publica la versión más reciente, independientemente de quién haya realizado la última actualización. La implementación continua también es útil si está utilizando una de las herramientas anteriormente mencionadas como repositorio central para su aplicación.

Implementar archivos de GitHub, CodePlex o BitBucket requiere que haya publicado su proyecto local en uno de estos servicios. Para obtener más información sobre cómo publicar el proyecto a estos servicios, consulte [Creación de un repositorio (GitHub)], [Uso de Git con CodePlex], [Creación de un repositorio (BitBucket)], [Uso de Dropbox para compartir repositorios de Git] o [Inicio rápido: Mercurial].

1. En primer lugar, coloque los archivos de la aplicación web en el repositorio seleccionado que se utilizará para la implementación continua.

2. En la hoja de la aplicación web del Portal, haga clic en **Configuración > Entrega continua**. Haga clic en **Elegir origen** y haga clic, por ejemplo, en **GitHub**.

	![](./media/publishing-with-git/azure6-setup-github.png)
	
2. En la hoja **Implementación continua**, haga clic en **Autorización** y, a continuación, en **Autorizar**. El Portal de Azure le redirigirá al sitio repositorio para finalizar el proceso de autorización.

4. Cuando haya terminado, vuelva al Portal de Azure y haga clic en **Aceptar** en la hoja **Autorización**.

5. En la hoja **Implementación continua**, elija la organización, proyecto y rama desde los que desea realizar la implementación. Cuando haya terminado, haga clic en **Aceptar**.
  
	![](./media/publishing-with-git/azure7-setup-github-configure.png)

	> [AZURE.NOTE] Al habilitar la implementación continua con GitHub o BitBucket, se mostrarán tanto los proyectos públicos como privados.

Azure crea una asociación con el repositorio seleccionado y extrae los archivos de la rama seleccionada. Una vez que el proceso finaliza, la sección **Implementación** de la hoja de la aplicación web mostrará el mensaje **Implementación activa**, que indica que la implementación se ha realizado correctamente.

7. En este punto, el proyecto se ha implementado desde el repositorio elegido en la aplicación web. Para comprobar que la aplicación web está activa, haga clic en la **dirección URL** de la parte superior del portal. El explorador debería navegar a la aplicación web.

8. Para comprobar que se está produciendo la implementación continua desde el repositorio elegido, inserte un cambio en el repositorio. El sitio web debería actualizarse para reflejar los cambios poco después de que finalice la inserción en el repositorio. Puede comprobar que la actualización se ha extraído en la hoja **Implementaciones** de la aplicación web.

### <a id="Step75"></a>Implementación de una solución de Visual Studio desde BitBucket, CodePlex, Dropbox, GitHub o Mercurial

La inserción de una solución de Visual Studio en Aplicaciones web en el Servicio de aplicaciones de Azure es tan fácil como insertar un archivo index.html. El proceso de implementación de Aplicaciones web simplifica todos los detalles, incluyendo la restauración de las dependencias de NuGet y la compilación de los archivos binarios de la aplicación. Puede seguir los procedimientos recomendados del control de origen de mantener el código solo en el repositorio de Git y dejar que la implementación de Aplicaciones web se encargue del resto.

Los pasos para insertar su solución de Visual Studio en Aplicaciones web son los mismos de la [sección anterior](#Step7), siempre y cuando haya configurado la solución y el repositorio como sigue:

-	En la raíz del repositorio, agregue un archivo `.gitignore`, a continuación, especifique todos los archivos y carpetas que desea excluir del repositorio, como las carpetas `Obj`, `Bin` y `packages` (consulte la [documentación de gitignore](http://git-scm.com/docs/gitignore) para obtener información de formato). Por ejemplo:

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE] Si usa GitHub, puede generar de forma opcional un archivo .gitignore específico de Visual Studio cuando crea el repositorio, en el cual se incluyen todos los archivos temporales comunes, los resultados de compilación, etc. Puede entonces personalizarlos para cubrir sus necesidades.

-	Agregue el árbol de directorios de la solución entero a su repositorio, con el archivo .sln en la raíz del repositorio.

Una vez que ha configurado el repositorio como se ha descrito y ha configurado la aplicación web en Azure para la publicación continua desde uno de los repositorios de Git conectados, puede desarrollar la aplicación ASP.NET localmente en Visual Studio e implementar continuamente el código simplemente insertando los cambios en el repositorio de Git conectado.

## Deshabilitación de la implementación continua

La implementación continua se puede deshabilitar en la hoja **Implementaciones**. En la hoja de la aplicación web, haga clic en **Configuración > Implementación continua**. A continuación, haga clic en **Desconectar**.

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)

Después de responder **Sí** al mensaje de confirmación, puede volver a la hoja de la aplicación web y hacer clic en **Configuración > Implementación continua** si quiere configurar la publicación desde otro origen.

## <a id="Step8"></a>Solución de problemas

Estos son los errores o problemas que suelen aparecer al utilizar Git para publicar en una aplicación web en Azure:

****

**Síntoma**: no es posible tener acceso a ’[siteURL]’: error al conectarse a [scmAddress]

**Causa**: este error puede producirse si la aplicación web no está en funcionamiento.

**Resolución**: Inicie la aplicación web en el Portal de Azure. La implementación de Git no funcionará a menos que se esté ejecutando la aplicación web.


****

**Síntoma**: no se pudo resolver el host "nombre de host".

**Causa**: este error puede ocurrir si la información de dirección introducida al crear el repositorio remoto "azure" no era correcta.

**Resolución**: use el comando `git remote -v` para obtener un listado de todos los remotos, junto con la dirección URL asociada. Compruebe que la URL para el repositorio correcto "azure" es correcta. Si lo necesita, suprima y vuelva a crear este repositorio remoto utilizando la URL correcta.

****

**Síntoma**: no hay referencias en común y no se ha especificado nada; sin hacer nada. Quizá hay que especificar una rama como "principal".

**Causa**: este error puede ocurrir si no especifica una rama al realizar una operación de inserción en Git y no hay establecido el valor push.default utilizado por Git.

**Resolución**: vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo:

	git push azure master

****

**Síntoma**: src refspec [branchname] no coincide con ninguna.

**Causa**: este error puede tener lugar si intenta insertar una rama que no es la principal en el repositorio remoto "azure".

**Resolución**: vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo:

	git push azure master

****

**Síntoma**: Error: los cambios se han confirmado en el repositorio remoto, pero la aplicación web no se ha actualizado.

**Causa**: este error puede ocurrir si está implementando una aplicación Node.js que contiene un archivo package.json que especifica módulos requeridos adicionales.

**Resolución**: los mensajes adicionales que contienen "npm ERR!" deberían registrarse antes de este error y pueden proporcionar contexto adicional sobre el error. A continuación se indican las causas conocidas de este error y el mensaje 'npm ERR!' correspondiente:

* **Archivo package.json con estructura incorrecta**: npm ERR! No se pudieron leer las dependencias.

* **Módulo nativo que no tiene una distribución binaria para Windows**:

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		OR

	* npm ERR! [modulename@version] preinstall: `make || gmake`


## Recursos adicionales

* [Uso de PowerShell para Azure]
* [Uso de las herramientas de línea de comandos de Azure para Mac y Linux]
* [Documentación de Git]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Portal de Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Uso de PowerShell para Azure]: ../articles/install-configure-powershell.md
[Uso de las herramientas de línea de comandos de Azure para Mac y Linux]: ../articles/xplat-cli-install.md
[Documentación de Git]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Creación de un repositorio (GitHub)]: https://help.github.com/articles/create-a-repo
[Uso de Git con CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Creación de un repositorio (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Inicio rápido: Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Uso de Dropbox para compartir repositorios de Git]: https://gist.github.com/trey/2722927
[Entrega continua a Azure con Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=AcomDC_0211_2016-->