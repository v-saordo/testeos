<properties
	pageTitle="Agile Software Development con el Servicio de aplicaciones de Azure"
	description="Aprenda a crear aplicaciones complejas de gran escala con el Servicio de aplicaciones de Azure de forma que admita Agile Software Development."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin"/>


# Agile Software Development con el Servicio de aplicaciones de Azure #

En este tutorial, aprenderá a crear aplicaciones complejas de gran escala con el [Servicio de aplicaciones de Azure](/services/app-service/) de manera que admita [ Agile Software Development](https://en.wikipedia.org/wiki/Agile_software_development). Se supone que ya conoce la [Implementación de aplicaciones complejas con criterio previsible en Azure](app-service-deploy-complex-application-predictably.md).

Las limitaciones de los procesos técnicos a menudo pueden ser un obstáculo para una implementación correcta de metodologías ágiles. El Servicio de aplicaciones de Azure, con características tales como [publicación continua](web-sites-publish-source-control.md), [entornos de ensayo](web-sites-staged-publishing.md) (ranuras) y [supervisión](web-sites-monitor.md), cuando se acompaña acertadamente de la orquestación y la administración de implementación del [Administrador de recursos de Azure](resource-group-overview.md), puede formar parte de una gran solución para los desarrolladores que adoptan Agile Software Development.

La tabla siguiente es una breve lista de los requisitos asociados al desarrollo ágil y la forma en que Servicios de Azure habilita cada uno de ellos.

| Requisito | Habilitación de Azure |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| - Compilación con cada confirmación<br>- Compilación automática y rápida | Cuando se configura con implementación continua, el Servicio de aplicaciones de Azure puede funcionar como compilaciones de ejecución en directo que se basan en una rama de desarrollo. Cada vez que el código se inserta en la rama, se compila y ejecuta automáticamente en directo en Azure.|
| - Creación de pruebas automáticas de compilaciones | Se pueden implementar pruebas de carga, pruebas web, etc. con la plantilla de Administrador de recursos de Azure.|
| - Realización de pruebas en un clon del entorno de producción | Las plantillas de Administrador de recursos de Azure se pueden usar para crear clones del entorno de producción de Azure (que incluye configuración de aplicaciones, plantillas de cadenas de conexión, escalado, etc.) para realizar pruebas rápidamente y con criterio previsible.|
| - Fácil visualización de los resultados de la última compilación | La implementación continua en Azure desde un repositorio significa que se puede probar el nuevo código en una aplicación en directo inmediatamente después de confirmar los cambios. |
| -Confirmación en la rama principal cada día<br>- Automatización de la implementación | La integración continua de una aplicación de producción con la rama principal de un repositorio implementa automáticamente cada confirmación/combinación en la rama principal para producción. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Lo que hará ##

Recorrerá un flujo de trabajo típico de desarrollo-prueba-ensayo-producción para publicar los nuevos cambios en la aplicación de ejemplo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), que consta de dos [aplicaciones web](/services/app-service/web/), una que es un front-end (FE) y la otra que es un back-end (BE) de API web, y una [base de datos SQL](/services/sql-database/). Trabajará con la arquitectura de implementación que se muestra a continuación:

![](./media/app-service-agile-software-development/what-1-architecture.png)

Para traducir la imagen en palabras:

-	La arquitectura de implementación se divide en tres entornos distintos (o [grupos de recursos](resource-group-overview.md) en Azure), cada uno con su correspondiente [plan de Servicio de aplicaciones](azure-web-sites-web-hosting-plans-in-depth-overview.md), configuración de [escalado](web-sites-scale.md) y base de datos SQL. 
-	Cada entorno se puede administrar por separado. Incluso pueden existir en distintas suscripciones.
-	El ensayo y la producción se implementan como dos ranuras de la misma aplicación de Servicio de aplicaciones. La rama principal se configura para la integración continua con la ranura de ensayo.
-	Cuando una confirmación en la rama principal se comprueba en la ranura de ensayo (con datos de producción), la aplicación de ensayo comprobada se cambia a la ranura de producción [sin tiempo de inactividad](web-sites-staged-publishing.md).

El entorno de producción y ensayo se define mediante la plantilla que se encuentra en [*&lt;raíz\_repositorio>*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Los entornos de desarrollo y pruebas se definen mediante la plantilla que se encuentra en [*&lt;raíz\_repositorio>*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

También usará la estrategia de ramas típica, donde el código se mueve de la rama de desarrollo a la rama de pruebas, y luego a la rama principal (con calidad creciente, por así decirlo).

![](./media/app-service-agile-software-development/what-2-branches.png)

## Qué necesita ##

-	Una cuenta de Azure
-	Una cuenta [GitHub](https://github.com/)
-	Shell de Git (instalado con [GitHub para Windows](https://windows.github.com/)): esto le permite ejecutar comandos de PowerShell y Git en la misma sesión 
-	Bits más recientes de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi)
-	Conocimientos básicos de lo siguiente:
	-	Implementación de plantillas de [Administrador de recursos de Azure](resource-group-overview.md) (vea también [Implementación predecible de una aplicación compleja en Azure](app-service-deploy-complex-application-predictably.md))
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial: + Puede [abrir una cuenta de Azure gratis](/pricing/free-trial/): obtenga créditos que puede usar para probar los servicios de pago de Azure, e incluso cuando los haya agotado, podrá conservar la cuenta y usar los servicios de Azure gratis, como Aplicaciones web. + Puede [activar los beneficios de suscriptores de Visual Studio](/pricing/member-offers/msdn-benefits-details/): su suscripción a Visual Studio le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.
>
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Configuración del entorno de producción ##

>[AZURE.NOTE] El script que se emplea en este tutorial configurará automáticamente la publicación continua desde el repositorio de GitHub. Esto requiere que las credenciales de GitHub ya estén almacenadas en Azure; en caso contrario, no se realizará la implementación con script al intentar definir la configuración del control de código fuente para las aplicaciones web.
>
>Para almacenar sus credenciales de GitHub en Azure, cree una aplicación web en el [Portal de Azure](https://portal.azure.com/) y [configure la implementación de GitHub](web-sites-publish-source-control.md#Step7). Solo tiene que hacer esto una vez.

En un escenario típico de DevOps, tiene una aplicación que se ejecuta directamente en Azure y quiere realizar cambios en ella mediante publicación continua. En este escenario, tiene una plantilla que ya desarrolló, probó y usó para implementar el entorno de producción. La configurará en esta sección.

1.	Cree su propia bifurcación del repositorio [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Para obtener información sobre la creación de la bifurcación, consulte [Bifurcación de un repositorio](https://help.github.com/articles/fork-a-repo/). Una vez creada la bifurcación, puede verla en el explorador.
 
	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Abra una sesión del Shell de Git. Si aún no tiene el Shell de Git, instale ahora [GitHub para Windows](https://windows.github.com/).

3.	Cree un clon local de la bifurcación ejecutando el comando siguiente:

		git clone https://github.com/<your_fork>/ToDoApp.git 

4.	Cuando tenga el clon local, vaya a *&lt;raíz\_repositorio>*\\ARMTemplates y ejecute el script deploy.ps1 de la manera siguiente:

		.\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.	Cuando se le solicite, escriba el nombre de usuario y la contraseña que quiera para el acceso a la base de datos.

	Debería ver el progreso de aprovisionamiento de varios recursos de Azure. Cuando finalice la implementación, el script iniciará la aplicación en el explorador y emitirá un bip descriptivo.

	![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
	>[AZURE.TIP] Eche un vistazo a *&lt;raíz\_repositorio>*\\ARMTemplates\\Deploy.ps1 para ver cómo genera recursos con identificadores únicos. Puede usar el mismo enfoque para crear clones de la misma implementación sin preocuparse por los nombres de recursos en conflicto.
 
6.	De nuevo en la sesión del Shell de Git, ejecute:

		.\swap –Name ToDoApp<unique_string>master

	![](./media/app-service-agile-software-development/production-4-swap.png)

7.	Cuando finalice el script, vuelva a examinar la dirección del front-end (http://ToDoApp*&lt;unique_string>*master.azurewebsites.net/) para ver la ejecución de la aplicación en producción.
 
5.	Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y observe lo que se crea.

	Podrá ver dos aplicaciones web en el mismo grupo de recursos, una con el sufijo `Api` en el nombre. Si observa la vista de grupo de recursos, también verá el servidor y Base de datos SQL, el plan de Servicio de aplicaciones y las ranuras de ensayo para las aplicaciones web. Examine los distintos recursos y compárelos con *&lt;raíz\_repositorio>*\\ARMTemplates\\ProdAndStage.json para ver cómo están configurados en la plantilla.

	![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

Ya tiene configurado el entorno de producción. Luego, iniciará una nueva actualización para la aplicación.

## Creación de ramas de desarrollo y prueba ##

Ahora que tiene una aplicación compleja en ejecución en producción en Azure, realizará una actualización a la aplicación de acuerdo con la metodología ágil. En esta sección, creará las ramas de desarrollo y prueba que necesitará para realizar las actualizaciones necesarias.

1.	En primer lugar, cree el entorno de prueba. En la sesión del Shell de Git, ejecute los siguientes comandos para crear el entorno para una nueva rama denominada **NewUpdate**. 

		git checkout -b NewUpdate
		git push origin NewUpdate 
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.	Cuando se le solicite, escriba el nombre de usuario y la contraseña que quiera para el acceso a la base de datos.

	Cuando finalice la implementación, el script iniciará la aplicación en el explorador y emitirá un bip descriptivo. Y de este modo, ahora tiene una nueva rama con su propio entorno de prueba. Dedique unos minutos a repasar algunas cosas acerca de este entorno de prueba:

	-	Puede crearlo en cualquier suscripción de Azure. Esto significa que el entorno de producción se puede administrar independientemente del entorno de prueba.
	-	El entorno de prueba se ejecuta en directo en Azure.
	-	El entorno de prueba es idéntico al entorno de producción, excepto en la configuración de escalado y las ranuras de ensayo. Puede saberlo porque son las únicas diferencias entre ProdandStage.json y Dev.json.
	-	Puede administrar su entorno de prueba en su propio plan de Servicio de aplicaciones, con un nivel de precio diferente (como **Gratis**).
	-	Eliminar este entorno de prueba será tan sencillo como eliminar el grupo de recursos. Encontrará información sobre cómo hacerlo [más adelante](#delete).

2.	Continúe con la creación de una rama de desarrollo ejecutando los comandos siguientes:

		git checkout -b Dev
		git push origin Dev
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.	Cuando se le solicite, escriba el nombre de usuario y la contraseña que quiera para el acceso a la base de datos.

	Dedique unos minutos a repasar algunas cosas acerca de este entorno de desarrollo:

	-	El entorno de desarrollo tiene una configuración idéntica al entorno de prueba porque se implementa con la misma plantilla.
	-	Cada entorno de desarrollo puede crearse en la suscripción de Azure del desarrollador, dejando que el entorno de prueba se administre por separado.
	-	El entorno de desarrollo se ejecuta en directo en Azure.
	-	Eliminar este entorno de desarrollo es tan sencillo como eliminar el grupo de recursos. Encontrará información sobre cómo hacerlo [más adelante](#delete).

>[AZURE.NOTE] Si tiene varios desarrolladores que trabajan en la nueva actualización, cada uno de ellos puede crear fácilmente una rama y el entorno de desarrollo dedicado haciendo lo siguiente:
>
>1.	Crear su propia rama del repositorio de GitHub (vea [Bifurcación de un repositorio](https://help.github.com/articles/fork-a-repo/)).
>2.	Clonar la bifurcación en su equipo local
>3.	Ejecutar los mismos comandos para crear su propia rama de desarrollo y el entorno.

Al terminar, la bifurcación de GitHub debe tener tres ramas:

![](./media/app-service-agile-software-development/test-1-github-view.png)

Y usted debe tener seis aplicaciones web (tres conjuntos de dos) en tres grupos de recursos independientes:

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE] Tenga en cuenta que ProdandStage.json especifica el entorno de producción que usa el nivel de precios **Estándar**, que es el adecuado para la escalabilidad de la aplicación de producción.

## Compilación y prueba de cada confirmación ##

Los archivos de plantilla ProdAndStage.json y Dev.json ya especifican los parámetros de control de código fuente, lo que, de forma predeterminada, configura la publicación continua para la aplicación web. Por lo tanto, cada confirmación en la rama de GitHub desencadena una implementación automática en Azure de esa rama. Veamos cómo funciona ahora su instalación.

1.	Asegúrese de que se encuentra en la rama de desarrollo del repositorio local. Para ello, ejecute el siguiente comando en el Shell de Git:

		git checkout Dev

2.	Realice un simple cambio en la capa de interfaz de usuario de la aplicación cambiando el código para que use listas de [arranque](http://getbootstrap.com/components/). Abra *&lt;raíz\_repositorio>*\\src\\MultiChannelToDo.Web\\index.cshtml y realice el cambio resaltado a continuación:

	![](./media/app-service-agile-software-development/commit-1-changes.png)

	>[AZURE.NOTE] Si no puede leer la imagen anterior:
	>
	>- En la línea 18, cambie `check-list` a `list-group`.
	>- En la línea 19, cambie `class="check-list-item"` a `class="list-group-item"`.

3.	Guarde el cambio. De nuevo en el Shell de Git, ejecute los siguientes comandos:

		cd <repository_root>
		git add .
		git commit -m "changed to bootstrap style"
		git push origin Dev
 
	Estos comandos Git son similares a "comprobar el código" en otro sistema de control de código fuente, como TFS. Al ejecutar `git push`, la nueva confirmación desencadena una inserción automática de código en Azure, que luego vuelve a compilar la aplicación para que refleje el cambio en el entorno de desarrollo.

4.	Para comprobar que se produjo esta inserción de código en el entorno de desarrollo, vaya a la hoja de aplicación web de su entorno de desarrollo y examine la sección **Implementación**. Debe poder ver el último mensaje de confirmación en dicha sesión.

	![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.	Desde allí, haga clic en **Examinar** para ver el nuevo cambio en la aplicación en directo en Azure.

	![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

	Se trata de un cambio menor a la aplicación. Sin embargo, muchas veces los nuevos cambios a una aplicación web compleja tienen efectos secundarios no intencionados y no deseados. La posibilidad de probar fácilmente cada confirmación en compilaciones directo le permite detectar estos problemas antes de que sus clientes los vean.

En este momento, debe sentirse cómodo al darse cuenta de que, como desarrollador del proyecto **NewUpdate**, le resultará fácil crear un entorno de desarrollo para sí mismo y luego compilar cada confirmación y probar cada compilación.

## Combinación del código en el entorno de prueba ##

Cuando esté preparado para insertar el código de la rama de desarrollo en la rama NewUpdate, este es el proceso estándar de Git:

1.	Combine las nuevas confirmaciones para NewUpdate en la rama de desarrollo en GitHub, por ejemplo, las confirmaciones creadas por otros desarrolladores. Cualquier nueva confirmación en GitHub desencadenará una inserción y compilación de código en el entorno de desarrollo. Puede comprobar después que su código en la rama de desarrollo trabaja con los bits más recientes de la rama NewUpdate.

2.	Combine todas las nuevas confirmaciones de la rama de desarrollo en la rama NewUpdate de GitHub. Esta acción desencadena una inserción y compilación de código en el entorno de prueba.

De nuevo, tenga en cuenta que dado que la implementación continua ya está configurada en estas ramas de Git, no tendrá que realizar ninguna otra acción como, por ejemplo, ejecutar compilaciones de integración. Basta con que realice los procedimientos estándar de control de código fuente con Git y Azure realizará automáticamente todos los procesos de compilación.

Ahora insertaremos su código en la rama **NewUpdate**. En el Shell de Git, ejecute los siguientes comandos:

	git checkout NewUpdate
	git pull origin NewUpdate
	git merge Dev
	git push origin NewUpdate

¡Ya está!

Vaya a la hoja de aplicación web de su entorno de prueba para comprobar que la nueva confirmación (combinada en la rama NewUpdate) está insertada ahora en el entorno de prueba. Después, haga clic en **Examinar** para ver que el cambio de estilo se ejecuta ahora directamente en Azure.

## Implementación de la actualización en producción ##

La inserción de código en el entorno de ensayo y producción no debe resultar diferente de lo que ya hizo al insertar código en el entorno de prueba. Es así de sencillo.

En el Shell de Git, ejecute los siguientes comandos:

	git checkout master
	git pull origin master
	git merge NewUpdate
	git push origin master

Recuerde que, en función del modo en que se configure el entorno de ensayo y producción en ProdandStage.json, se inserta el nuevo código en la ranura de **ensayo** y se ejecuta allí. Si se navega a la dirección URL de la ranura de ensayo, verá que el nuevo código se ejecuta allí. Para ello, ejecute el cmdlet `Show-AzureWebsite` en el Shell de Git.

	Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
Y ahora, tras haber comprobado la actualización en la ranura de ensayo, lo único que queda por hacer es cambiarla a producción. En el Shell de Git, basta con que ejecute los siguientes comandos:

	cd <repository_root>\ARMTemplates
	.\swap.ps1 -Name ToDoApp<unique_string>master

¡Enhorabuena! Ya publicó correctamente una nueva actualización para la aplicación web de producción. Es más, lo hizo creando fácilmente entornos de desarrollo y prueba, y compilando y probando cada confirmación. Estos son los bloques de creación fundamentales de Agile Software Development.

<a name="delete"></a>
## Eliminación de entornos de desarrollo y prueba ##

Dado que diseñó deliberadamente sus entornos de desarrollo y prueba para que sean grupos de recursos independientes, es muy fácil eliminarlos. Para eliminar los que creó en este tutorial, las ramas de GitHub y los artefactos de Azure, basta con que ejecute los siguientes comandos en el Shell de Git:

	git branch -d Dev
	git push origin :Dev
	git branch -d NewUpdate
	git push origin :NewUpdate
	Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
	Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## Resumen ##

Agile Software Development es un componente indispensable para muchas empresas que desean adoptar Azure como su plataforma de aplicaciones. En este tutorial, aprendió a crear y eliminar cómodamente réplicas exactas o casi réplicas del entorno de producción, incluso con aplicaciones complejas. También aprendió a aprovechar esta posibilidad para crear un proceso de desarrollo capaz de compilar y probar cada una de las confirmaciones en Azure. Es de esperar que este tutorial le muestre cómo puede usar mejor el Servicio de aplicaciones de Azure y el Administrador de recursos de Azure juntos para crear una solución de DevOps que proporcione metodologías ágiles. Luego, puede basarse en este escenario para realizar técnicas de DevOps avanzadas como [pruebas en producción](app-service-web-test-in-production-get-start.md). Para ver un escenario común de pruebas en producción, consulte [Implementación de la distribución de paquetes piloto (pruebas beta) en el Servicio de aplicaciones de Azure](app-service-web-test-in-production-controlled-test-flight.md).

## Más recursos ##

-	[Implementación predecible de una aplicación compleja en Azure](app-service-deploy-complex-application-predictably.md)
-	[Desarrollo ágil en la práctica: trucos y sugerencias para un ciclo de desarrollo actualizado](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-	[Estrategias de implementación avanzada para Aplicaciones web de Azure con plantillas de Administrador de recursos](http://channel9.msdn.com/Events/Build/2015/2-620)
-	[Creación de plantillas de Administrador de recursos de Azure](resource-group-authoring-templates.md)
-	[JSONLint: validador de JSON](http://jsonlint.com/)
-	[ARMClient: configurar publicación de GitHub para el sitio](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-	[Creación de ramas de Git: combinación y creación de ramas básicas](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Blog de David Ebbo](http://blog.davidebbo.com/)
-	[Azure PowerShell](powershell-install-configure.md)
-	[Herramientas de línea de comandos multiplataforma de Azure](xplat-cli-install.md)
-	[Creación o edición de usuarios en Azure AD](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-	[Wiki de Project Kudu](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_0128_2016-->