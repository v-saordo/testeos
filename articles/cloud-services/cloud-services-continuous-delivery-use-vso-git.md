<properties
	pageTitle="Entrega continua con Git y Visual Studio Team Services en Azure | Microsoft Azure" 
	description="Aprenda a configurar los proyectos de equipo de Visual Studio Team Services para usar Git para que se compilen y se implementen automáticamente en la característica aplicación web de Servicio de aplicaciones de Azure o en los servicios en la nube."
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="tarcher"/>

# Entrega continua a Azure con Visual Studio Team Services y Git

Puede usar proyectos de equipo de Visual Studio Team Services para hospedar en un repositorio Git el código fuente, y compilarlo e implementarlo automáticamente en aplicaciones web o servicios en la nube de Azure cada vez que se inserta una confirmación en el repositorio.

Necesitará Visual Studio 2013 y tener instalado el SDK de Azure. Si aún no tiene Visual Studio 2013, descárguelo; para ello, haga clic en el vínculo **Empezar de forma gratuita** [en www.visualstudio.com](http://www.visualstudio.com). Instale el SDK de Azure desde [aquí](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Necesita una cuenta de Visual Studio Team Services para completar este tutorial:
> puede [abrir una cuenta de Visual Studio Team Services de forma gratuita](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar un servicio en la nube que se compile e implemente automáticamente en Azure con Visual Studio Team Services, siga los pasos que aparecen a continuación:

## Paso 1: crear un repositorio Git

1. Si no tiene una cuenta de Visual Studio Team Services, puede obtenerla [aquí](http://go.microsoft.com/fwlink/?LinkId=397665). Cuando cree un proyecto de equipo, elija Git como el sistema de control de código fuente. Siga las instrucciones para conectar Visual Studio al proyecto de equipo.

2. En **Team Explorer**, haga clic en el vínculo **Clonar este repositorio**.

	![][3]

3. Especifique la ubicación de la copia local y elija el botón **Clonar**.

## Paso 2: crear un proyecto y confirmarlo en el repositorio

1. En **Team Explorer**, en la sección **Soluciones**, elija el vínculo **Nuevo** para crear un nuevo proyecto en el repositorio local.

	![][4]

2. Puede implementar una aplicación web o un servicio en la nube (aplicación de Azure) siguiendo los pasos que se ofrecen en este tutorial. Cree un proyecto nuevo de Servicio en la nube de Azure o un proyecto nuevo de MVC de ASP.NET. Asegúrese de que el proyecto tiene como destino .NET Framework 4, o cualquier versión posterior. Si va a crear un proyecto de servicio en la nube, agregue un rol web y un rol de trabajo de ASP.NET MVC. Si desea crear una aplicación web, elija la plantilla del proyecto **Aplicación web ASP.NET** y, a continuación, **MVC**. Para obtener más información, consulte [Creación de una aplicación web ASP.NET en el Servicio de aplicaciones de Azure](../web-sites-dotnet-get-started.md).

3. Abra el menú contextual de la solución y elija **Confirmar**.

	![][7]

4. Si es la primera vez que usa Git en Visual Studio Team Services, deberá proporcionar alguna información que le identifique en Git. En el área **Cambios pendientes** de **Team Explorer**, escriba su nombre de usuario y dirección de correo electrónico. Escriba un comentario para la confirmación y, a continuación, pulse el botón **Confirmar**.

	![][8]

5. Tenga en cuenta las opciones para incluir o excluir cambios concretos al realizar la protección. Si los cambios que desea están excluidos, elija **Incluir todos**.

6. Ahora ha confirmado los cambios en la copia local del repositorio. A continuación, sincronice dichos cambios con el servidor, para lo que debe elegir el vínculo **Sincronizar**.

## Paso 3: Conexión del proyecto a Azure

1. Ahora que tiene un repositorio Git en Visual Studio Team Services con código fuente, está en disposición de conectarlo a Azure. En el [Portal de Azure clásico](http://manage.windowsazure.com), seleccione el servicio en la nube o la aplicación web, o bien cree unos nuevos haciendo clic en el icono + situado en la parte inferior izquierda y seleccionando **Servicio en la nube** o **Aplicación web** y, a continuación, **Creación rápida**.

	![][9]

3. Para servicios en la nube, elija el vínculo **Configurar publicación con Visual Studio Team Services**. Para aplicaciones web, elija el vínculo **Configurar implementación desde control de código fuente**.

	![][10]

2. En el asistente, escriba el nombre de la cuenta de Visual Studio Team Services en el cuadro de texto y elija el vínculo **Autorizar ahora**. Puede que se le solicite que inicie sesión.

	![][11]

3. En el cuadro de diálogo emergente **Solicitud de conexión**, elija **Aceptar** para autorizar a Azure a que configure el proyecto del equipo en Visual Studio Team Services.

	![][12]

4. Si la autorización se realiza correctamente, verá una lista desplegable que contiene los proyectos del equipo de Visual Studio Team Services. Seleccione el nombre del proyecto de equipo que ha creado en los pasos anteriores y presione el botón de la marca de verificación del asistente.

	![][13]

	La próxima vez que inserte una confirmación en el repositorio, Visual Studio Team Services creará e implementará su proyecto en Azure.

## Paso 4: Desencadenamiento de una recompilación y nueva implementación del proyecto

1. En Visual Studio, abra un archivo y cámbielo. Por ejemplo, cambie el archivo `_Layout.cshtml` de la carpeta Views\\Shared de un rol web de MVC.

	![][17]

2. Edite el texto del pie de página del sitio y guarde el archivo.

	![][18]

3. En el **Explorador de soluciones**, abra el menú contextual del nodo de la solución, nodo del proyecto o archivo que cambió y elija **Confirmar**.

4. Escriba un comentario y elija **Confirmar**.

	![][20]

5. Elija el vínculo **Sincronizar**.

	![][38]

6. Elija el vínculo **Insertar** para insertar la confirmación en el repositorio en Visual Studio Team Services. (También puede usar el botón **Sincronizar** para copiar las confirmaciones en el repositorio. La diferencia es que **Sincronizar** también extrae los últimos cambios del repositorio).

	![][39]

7. Presione el botón **Inicio** para volver a la página principal de **Team Explorer**.

	![][21]

8. Elija **Compilaciones** para ver las compilaciones en curso.

	![][22]

	**Team Explorer** muestra que se desencadenó una compilación para su protección.

	![][23]

9. Para ver un registro detallado del progreso de la compilación, haga doble clic en el nombre de la compilación en curso.

10. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó al vincular a Azure con el asistente. Abra el menú contextual de la definición de la compilación y elija **Editar definición de compilación**.

	![][25]

11. En la pestaña **Desencadenador**, verá que la definición de compilación está configurada para compilar en cada protección de forma predeterminada. (Para un servicio en la nube, Visual Studio Team Services compila e implementa automáticamente la bifurcación principal en el entorno de ensayo. Aún así, tendrá que realizar un paso manual para implementar en el sitio activo. En el caso de una aplicación web que no tenga un entorno de ensayo, la bifurcación principal se implementa directamente en el sitio activo.

	![][26]

1. En la pestaña **Proceso**, puede ver que el entorno de implementación está configurado con el nombre del servicio en la nube o de la aplicación web.

	![][27]

1. Especifique los valores para las propiedades si desea que sean diferentes de los predeterminados. Las propiedades de la publicación de Azure se encuentran en la sección **Implementación** y es posible que también sea necesario definir los parámetros de MSBuild. Por ejemplo, en un proyecto de servicio en la nube, para especificar una configuración del servicio que no sea "Nube", establezca los parámetros de MSbuild en `/p:TargetProfile=[YourProfile]`, donde *[YourProfile]* se ajusta a un archivo de configuración de servicio con un nombre como ServiceConfiguration.*YourProfile*.cscfg.

	En la tabla siguiente se muestran las propiedades disponibles en la sección **Implementación**:

	|Propiedad|Valor predeterminado|
	|---|---|
	|Permitir certificados que no son de confianza|Si el valor es false, los certificados SSL deben estar firmados por una entidad de certificación raíz.|
	|Permitir actualización|Permite actualizar una implementación existente en lugar de crear una nueva. Conserva la dirección IP.|
	|No eliminar|Si el valor es true, no sobrescriba una implementación no relacionada existente (la actualización está permitida).|
	|Ruta de acceso a la configuración de implementación|La ruta de acceso al archivo .pubxml de una aplicación web, en relación con la carpeta raíz del repositorio. Se ignora para los servicios en la nube.|
	|Entorno de implementación de SharePoint|La misma que el nombre de servicio.|
	|Entorno de implementación de Azure|El nombre de la aplicación web o del servicio en la nube.|

1. Llegados a este punto, la compilación debería haber finalizado correctamente.

	![][28]

1. Si se hace doble clic en el nombre de la compilación, Visual Studio muestra un **resumen de la compilación**, que incluye los resultados de las pruebas de los proyectos de pruebas de las unidades asociadas.

	![][29]

1. En el [Portal de Azure clásico](http://manage.windowsazure.com) puede ver la implementación asociada en la pestaña **Implementaciones**, al seleccionar el entorno de ensayo.

	![][30]

1.	Vaya a la dirección URL del sitio. Para una aplicación web, elija el botón **Examinar** en el portal. Para un servicio en la nube, elija la dirección URL de la sección **Vista rápida** de la página **Panel** que muestra el entorno de ensayo.

	Las implementaciones de la integración continua para los servicios en la nube se publican de forma predeterminada en el entorno de ensayo. Para cambiarlo, seleccione **Producción** en **Entorno del servicio de nube alternativo**. Aquí es donde se encuentra la URL del sitio en la página del panel del servicio en la nube.

	![][31]

	Se abrirá una nueva pestaña del explorador para mostrar el sitio que se está ejecutando.

	![][32]

1.	Si realiza otros cambios en el proyecto, activará más compilaciones y acumulará varias implementaciones. La última de ellas marcada como Activa.

	![][33]

## Paso 5: volver a implementar una compilación anterior

Este paso es opcional. En el Portal de Azure clásico, elija una implementación anterior y pulse **Volver a implementar** para devolver el sitio a una protección anterior. Tenga en cuenta que esto desencadenará una nueva compilación en TFS y creará una nueva entrada en el historial de implementaciones.

![][34]

## Paso 6: cambiar la implementación de producción

Cuando esté listo, puede promover el entorno de ensayo al de producción, para lo que debe elegir **Intercambiar** en el Portal de Azure clásico. El entorno de ensayo recién implementado se promueve a Producción y el anterior entorno de Producción, si lo hubiera, pasa a entorno de ensayo. La implementación activa puede ser diferente para los entornos de producción y ensayo, pero el historial de implementación de las compilaciones recientes es el mismo independientemente del entorno.

![][35]

## Step 6: Deploy from a working branch.

Cuando usa Git, realiza cambios habitualmente en una bifurcación de trabajo y la integra en la bifurcación principal cuando el trabajo de desarrollo alcanza una fecha de finalización. Durante la fase de desarrollo de un proyecto, querrá compilar e implementar la bifurcación de trabajo en Azure.

1. En **Team Explorer**, elija el botón **Inicio** y, a continuación, el botón **Bifurcaciones**.

	![][40]

2. Haga clic en el vínculo **Nueva bifurcación**.

	![][41]

3. Escriba el nombre de la bifurcación, por ejemplo "trabajo", y elija **Crear bifurcación**. De esta forma se crea una nueva bifurcación local.

	![][42]

4. Publique la bifurcación. Elija el nombre de la bifurcación en **Bifurcaciones no publicadas** y elija **Publicar**.

	![][44]

6. De manera predeterminada, solo los cambios realizados en la bifurcación principal activan una compilación continua. Para configurar la compilación continua en una bifurcación de trabajo, elija la página **Compilaciones** de **Team Explorer** y elija **Editar definición de compilación**.

7. Abra la pestaña **Configuración de origen**. En **Bifurcaciones supervisadas para compilaciones de integración continua y graduales**, elija **Haga clic aquí para agregar una nueva fila**.

	![][47]

8. Especifique la bifurcación que ha creado, por ejemplo refs/heads/working.

	![][48]

9. Realice un cambio en el código, abra el menú contextual del archivo modificado y elija **Confirmar**.

	![][43]

10. Haga clic en el vínculo **Confirmaciones no sincronizadas** y elija el botón **Sincronizar** o el vínculo **Insertar** para copiar los cambios en la copia de la bifurcación de trabajo en Visual Studio Team Services.

	![][45]

11. Vaya a la vista **Compilaciones** y busque la compilación que se acaba de desencadenar para la bifurcación de trabajo.

## Pasos siguientes

Para ver más sugerencias sobre el uso de Git con Visual Studio Team Services, consulte [Desarrollo y uso compartido del código de Git con Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) y para obtener información sobre el uso de un repositorio de Git no administrado por Visual Studio Team Services para publicar en Azure, consulte [Implementación continua mediante GIT en el Servicio de aplicaciones de Azure](../web-sites-publish-source-control.md). Para obtener más información sobre Visual Studio Team Services, consulte [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG

<!---HONumber=AcomDC_1223_2015-->