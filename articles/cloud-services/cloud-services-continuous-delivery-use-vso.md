<properties
	pageTitle="Entrega continua con Visual Studio Team Services en Azure | Microsoft Azure"
	description="Aprenda a configurar los proyectos de equipo de Visual Studio Team Services para que se compilen y se implementen automáticamente en la característica aplicación web de Servicio de aplicaciones de Azure o en los servicios en la nube."
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

# Entrega continua a Azure con Visual Studio Team Services

Puede configurar los proyectos de equipo de Visual Studio Team Services para que se compilen y se implementen automáticamente en las aplicaciones web de Azure o en los servicios en la nube. Para obtener información sobre cómo configurar un sistema de compilación e implementación continuas con Team Foundation Server *local*, consulte [Entrega continua para Servicios en la nube de Azure](cloud-services-dotnet-continuous-delivery.md).

En este tutorial se supone que tiene instalados Visual Studio 2013 y el SDK de Azure. Si aún no tiene Visual Studio 2013, descárguelo; para ello, haga clic en el vínculo **Empezar de forma gratuita** en [www.visualstudio.com](http://www.visualstudio.com). Instale el SDK de Azure desde [aquí](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Necesita una cuenta en línea de Visual Studio para completar este tutorial:
> Puede [abrir una cuenta de Visual Studio Online de forma gratuita](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar un servicio en la nube que se compile e implemente automáticamente en Azure con Visual Studio Team Services, siga los pasos que aparecen a continuación:

## Paso 1: Creación de un proyecto de equipo

Siga las instrucciones que se describen [aquí](http://go.microsoft.com/fwlink/?LinkId=512980) para crear el proyecto de equipo y vincularlo a Visual Studio. En este tutorial se supone que usa Team Foundation Version Control (TFVC) como solución de control de código fuente. Si desea usar Git para el control de versiones, consulte [la versión Git de este tutorial](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## Paso 2: Protección de un proyecto para el control de código fuente

1. En Visual Studio, abra la solución que desee implementar o cree una nueva.
Puede implementar una aplicación web o un servicio en la nube (aplicación de Azure) siguiendo los pasos que se ofrecen en este tutorial.
Si desea crear una nueva solución, cree un proyecto de Servicio de nube de Azure o un proyecto de MVC de ASP.NET. Asegúrese de que el proyecto se dirige a .NET Framework 4 o 4.5.
Si está creando un proyecto de Servicio de nube, agregue un rol web de MVC de ASP.NET y un rol de trabajo y seleccione una aplicación de Internet para el rol web. Cuando se le solicite, elija **Aplicación de Internet**.
Si desea crear una aplicación web, seleccione la plantilla de proyecto de aplicación web ASP.NET y, a continuación, MVC. Consulte [Crear una aplicación web de ASP.NET en el servicio de aplicaciones de Azure](../web-sites-dotnet-get-started.md)

	> [AZURE.NOTE]Actualmente, Visual Studio Team Services solo admite las implementaciones de integración continua de las aplicaciones web de Visual Studio. Los proyectos de sitio web están fuera del alcance.

1. Abra el menú contextual de la solución y elija **Agregar solución al control de código fuente**.

	![][5]

1. Acepte o cambie los valores predeterminados y elija el botón **Aceptar**. Una vez terminado el proceso, aparecerán los iconos de control de código fuente en el **Explorador de soluciones**.

	![][6]

1. Abra el menú contextual de la solución y elija **Proteger**.

	![][7]

1. En el área **Cambios pendientes** de **Team Explorer**, escriba un comentario para la protección y elija el botón **Proteger**.

	![][8]

	Tenga en cuenta las opciones para incluir o excluir cambios concretos al realizar la protección. Si los cambios deseados se excluyen, elija el vínculo **Incluir todo**.

	![][9]

## Paso 3: Conexión del proyecto a Azure

1. Ahora que tiene un proyecto de equipo de VSTS con código fuente en él, está en disposición de conectar el proyecto de equipo a Azure. En el [Portal de Azure clásico](http://manage.windowsazure.com), seleccione el servicio en la nube o la aplicación web, o bien cree unos nuevos haciendo clic en el icono **+** situado en la parte inferior izquierda y seleccionando **Servicio en la nube** o **Aplicación web** y, a continuación, **Creación rápida**. Haga clic en el vínculo **Configurar publicación con Visual Studio Team Services**.

	![][10]

1. En el asistente, escriba el nombre de la cuenta de Visual Studio Team Services en el cuadro de texto y haga clic en el vínculo **Autorizar ahora**. Puede que se le solicite que inicie sesión.

	![][11]

1. En el cuadro de diálogo emergente **Solicitud de conexión**, elija el botón **Aceptar** para autorizar a Azure a configurar su proyecto de equipo en VSTS.

	![][12]

1. Si la autorización se realiza correctamente, verá una lista desplegable que contiene los proyectos de equipo de Visual Studio Team Services. Elija el nombre del proyecto de equipo que creó en los pasos anteriores y luego elija el botón con la marca de verificación del asistente.

	![][13]

1. Una vez que el proyecto se haya vinculado, verá algunas instrucciones para proteger los cambios en el proyecto de equipo de Visual Studio Team Services. La próxima vez que se registre, Visual Studio Team Services compilará e implementará el proyecto en Azure. Para probarlo ahora, haga clic en el vínculo **Proteger desde Visual Studio** y, luego, en **Iniciar Visual Studio** (o el botón equivalente de **Visual Studio** situado en la parte inferior de la pantalla del portal).

	![][14]

## Paso 4: Desencadenamiento de una recompilación y nueva implementación del proyecto

1. En **Team Explorer** de Visual Studio, elija el vínculo **Explorador de control de código fuente**.

	![][15]

1. Vaya hasta su archivo de solución y ábralo.

	![][16]

1. En el **Explorador de soluciones**, abra un archivo y cámbielo. Por ejemplo, cambie el archivo `_Layout.cshtml` de la carpeta Views\\Shared de un rol web de MVC.

	![][17]

1. Modifique el logotipo del sitio y elija **Ctrl+S** para guardar el archivo.

	![][18]

1. En **Team Explorer**, haga clic en el vínculo **Cambios pendientes**.

	![][19]

1. Escriba un comentario y, luego, elija el botón **Proteger**.

	![][20]

1. Presione el botón **Inicio** para volver a la página principal de **Team Explorer**.

	![][21]

1. Haga clic en el vínculo **Compilaciones** para ver las compilaciones en curso.

	![][22]

	**Team Explorer** indica que se ha desencadenado una compilación para su protección.

	![][23]

1. Haga doble clic en el nombre de la compilación en curso para ver un registro detallado a medida que avanza la compilación.

	![][24]

1. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó al vincular TFS a Azure con el asistente. Abra el menú contextual de la definición de compilación y elija **Editar definición de compilación**.

	![][25]

	En la pestaña **Desencadenador**, verá que la definición de compilación se ha configurado para compilar en cada protección de forma predeterminada.

	![][26]

	En la pestaña **Proceso**, puede ver que el entorno de implementación se ha configurado con el nombre del servicio en la nube o de la aplicación web. Si trabaja con aplicaciones web, verá propiedades diferentes a las que se muestran aquí.

	![][27]

1. Especifique los valores para las propiedades si desea que sean diferentes de los predeterminados. Las propiedades para la publicación de Azure se encuentran en la sección **Implementación**.

	En la tabla siguiente se muestran las propiedades disponibles en la sección **Implementación**:

	|Propiedad|Valor predeterminado|
	|---|---|
	|Permitir certificados que no son de confianza|Si el valor es false, los certificados SSL deben estar firmados por una entidad de certificación raíz.|
	|Permitir actualización|Permite actualizar una implementación existente en lugar de crear una nueva. Conserva la dirección IP.|
	|No eliminar|Si el valor es true, no sobrescriba una implementación no relacionada existente (la actualización está permitida).|
	|Ruta de acceso a la configuración de implementación|La ruta de acceso al archivo .pubxml de una aplicación web, en relación con la carpeta raíz del repositorio. Se ignora para los servicios en la nube.|
	|Entorno de implementación de SharePoint|La misma que el nombre de servicio.|
	|Entorno de implementación de Azure|El nombre de la aplicación web o del servicio en la nube.|

1. Si usa varias configuraciones de servicio (archivos .cscfg), puede especificar la configuración de servicio que desee en el valor **Compilación, Avanzada, Argumentos de MSBuild**. Por ejemplo, para usar ServiceConfiguration.Test.cscfg, establezca la opción de la línea de argumentos de MSBuild.`/p:TargetProfile=Test`

	![][38]

	Llegados a este punto, la compilación debería haber finalizado correctamente.

	![][28]

1. Si se hace doble clic en el nombre de la compilación, Visual Studio muestra un **Resumen de la compilación** en el que se incluyen los resultados de las pruebas de los proyectos de pruebas unitarias asociados.

	![][29]

1. En el [Portal de Azure clásico](http://manage.windowsazure.com) puede ver la implementación asociada en la pestaña **Implementaciones**, al seleccionar el entorno de ensayo.

	![][30]

1.	Vaya a la dirección URL del sitio. Para una aplicación web, simplemente haga clic en el botón **Examinar** de la barra de comandos. Para un servicio en la nube, elija la dirección URL de la sección **Vista rápida** de la página **Panel** que muestra el entorno de ensayo de un servicio en la nube. Las implementaciones de la integración continua para los servicios en la nube se publican de forma predeterminada en el entorno de ensayo. Puede cambiarlo si configura la propiedad **Entorno del servicio de nube alternativo** en **Producción**. Esta captura de pantalla indica en qué parte del sitio en la página del panel del servicio en la nube está la dirección URL:

	![][31]

	Se abrirá una nueva pestaña del explorador para mostrar el sitio que se está ejecutando.

	![][32]

	Para los servicios en la nube, si realiza otros cambios en el proyecto, debe desencadenar más compilaciones y acumular varias implementaciones. La última de ellas marcada como Activa.

	![][33]

## Paso 5: Nueva implementación de una compilación anterior

Este paso se aplica a los servicios en la nube y es opcional. En el Portal de Azure clásico, seleccione una implementación anterior y haga clic en el botón **Volver a implementar** para devolver el sitio a una protección anterior. Tenga en cuenta que esto desencadenará una nueva compilación en TFS y creará una nueva entrada en el historial de implementaciones.

![][34]

## Paso 6: cambiar la implementación de producción

Este paso solo se aplica a los servicios en la nube, no a las aplicaciones web. Cuando esté preparado, puede promover el entorno de ensayo al de producción haciendo clic en el botón **Intercambiar** del Portal de Azure clásico. El entorno de ensayo recién implementado se promueve a Producción y el anterior entorno de Producción, si lo hubiera, pasa a entorno de ensayo. La implementación activa puede ser diferente para los entornos de producción y ensayo, pero el historial de implementación de las compilaciones recientes es el mismo independientemente del entorno.

![][35]

## Paso 7: Ejecución de pruebas unitarias

Este paso se aplica únicamente a las aplicaciones web, no a los servicios en la nube. Para establecer una prueba de calidad en su implementación, puede ejecutar pruebas unitarias y, en caso de error, puede detener la implementación.

1.  En Visual Studio, agregue un proyecto de prueba unitaria.

	![][39]

1.  Agregue referencias de proyecto al proyecto que desee probar.

	![][40]

1.  Agregue algunas pruebas unitarias. Para empezar, haga una prueba ficticia que siempre se supere.

		```
		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;

		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}
		```

1.  Edite la definición de la compilación, elija la pestaña **Proceso** y expanda el nodo **Prueba**.

1.  Establezca **Suspender compilación tras error de pruebas** en True. Esto significa que la implementación no se producirá a menos que se superen las pruebas.

	![][41]

1.  Ponga en cola una nueva compilación.

	![][42]

	![][43]

1. Mientras se desarrolla la compilación, compruebe su progreso.

	![][44]

	![][45]

1. Una vez terminada la compilación, compruebe los resultados de la prueba.

	![][46]

	![][47]

1.  Pruebe a crear una prueba que provoque un error. Agregue una nueva prueba copiando la primera, cámbiele el nombre y comente que la línea de código que indica NotImplementedException es una excepción esperada.

		```
		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}
		```

1. Registre el cambio para poner en cola una nueva compilación.

	![][48]

1. Consulte los resultados de la prueba para ver los detalles del error.

	![][49]

	![][50]

## Pasos siguientes
Para obtener más información sobre las pruebas unitarias en Visual Studio Team Services, consulte [Ejecución de pruebas unitarias en una compilación](http://go.microsoft.com/fwlink/p/?LinkId=510474). Si usa Git, consulte [Uso compartido del código en Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Implementación continua mediante GIT en el Servicio de aplicaciones de Azure](../web-sites-publish-source-control.md). Para obtener más información sobre Visual Studio Team Services, consulte [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG

<!---HONumber=AcomDC_1223_2015-->