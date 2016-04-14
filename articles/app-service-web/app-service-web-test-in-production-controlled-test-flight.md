<properties
	pageTitle="Implementación de la distribución de paquetes piloto (pruebas beta) en el Servicio de aplicaciones de Azure"
	description="Aprenda a distribuir paquetes piloto de nuevas características en las aplicaciones o a probar la versión beta de sus actualizaciones en este tutorial integral. Reúne las características del Servicio de aplicaciones como publicación continua, ranuras, enrutamiento de tráfico y la integración de Application Insights."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cephalin"/>
# Implementación de la distribución de paquetes piloto (pruebas beta) en el Servicio de aplicaciones de Azure

Este tutorial muestra cómo hacer *implementaciones de distribución de paquetes piloto* mediante la integración de las distintas capacidades del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y [Azure Application Insights](/services/application-insights/).

*La distribución de paquetes piloto* es un proceso de implementación que valida una nueva característica o cambio con un número limitado de clientes reales, y es una prueba importante en el escenario de producción. Es similar a las pruebas beta y a veces se conoce como "vuelo de prueba controlado". Muchas grandes empresas con una presencia web usan este enfoque para obtener validación temprana en las actualizaciones de sus aplicaciones dentro de su prácticas de [desarrollo ágil](https://en.wikipedia.org/wiki/Agile_software_development). El Servicio de aplicaciones de Azure le permite integrar la prueba en producción con publicación continua y con Application Insight para implementar el mismo escenario de DevOps. Las ventajas de este enfoque incluyen:

- **Obtención de información real a partir de comentarios _antes_ de que se publiquen las actualizaciones en el entorno de producción** (si hay algo mejor que recibir comentarios tan pronto como la versión se publique, es recibir comentarios antes de publicar). Puede probar las actualizaciones con tráfico y comportamientos reales de los usuarios tan pronto como desee dentro el ciclo de vida del producto.
- **Mejora [ del desarrollo continuo controlado por pruebas (CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development)** a través de la integración de pruebas en la producción, con integración continua e instrumentación con Application Insights, la validación de usuario se produce al principio y de forma automática dentro del ciclo de vida del producto. Esto ayuda a reducir las inversiones de tiempo de ejecución de pruebas manuales.
- **Optimización del flujo de trabajo de prueba**: mediante la automatización de la prueba en producción con instrumentación de supervisión continua, puede lograr en un único proceso los objetivos de distintos tipos de pruebas, como: [integración](https://en.wikipedia.org/wiki/Integration_testing), [regresión](https://en.wikipedia.org/wiki/Regression_testing), [facilidad de uso](https://en.wikipedia.org/wiki/Usability_testing), accesibilidad, localización, [rendimiento](https://en.wikipedia.org/wiki/Software_performance_testing), [seguridad](https://en.wikipedia.org/wiki/Security_testing), y [aceptación](https://en.wikipedia.org/wiki/Acceptance_testing).

En una implementación de distribución de paquetes piloto, no se trata solamente del enrutamiento de tráfico real. En este tipo de implementación lo que se pretende es tener una percepción y conocimientos sobre la misma lo antes posible, tanto si se trata de un error inesperado como de la degradación del rendimiento o de problemas de experiencia de usuario. Recuerde que se trata de los clientes reales. Así que para hacerlo bien, tiene que asegurarse de que ha configurado la implementación de distribución de paquetes piloto de forma que pueda recopilar todos los datos que necesita para tomar una decisión informada en el paso siguiente. Este tutorial muestra cómo recopilar datos con Application Insights, pero puede usar New Relic u otra tecnología que se adapte a su situación.

## Lo que hará

En este tutorial, obtendrá información sobre cómo reunir los escenarios siguientes para probar su aplicación de Servicio de la aplicaciones en producción:

- [Enrutar el tráfico de producción](app-service-web-test-in-production-get-start.md) a la aplicación beta
- [Instrumentar la aplicación](../application-insights/app-insights-web-track-usage.md) para obtener métricas útiles
- Implementar la aplicación beta y realizar un seguimiento de las métricas de aplicación de forma continua
- Comparar las métricas entre la aplicación de producción y la aplicación beta para ver de qué forma los cambios de código se traducen en resultados

## Qué necesita

-	Una cuenta de Azure
-	Una cuenta [GitHub](https://github.com/)
- Visual Studio de 2015, puede descargar la [edición Community](https://www.visualstudio.com/es-ES/products/visual-studio-express-vs.aspx).
-	Shell de Git (instalado con [GitHub para Windows](https://windows.github.com/)): esto le permite ejecutar comandos de PowerShell y Git en la misma sesión
-	Bits más recientes de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)
-	Conocimientos básicos de lo siguiente:
	-	Implementación de plantillas del [Administrador de recursos de Azure](../resource-group-overview.md) (consulte [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-deploy-complex-application-predictably.md))
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial: + Puede [abrir una cuenta de Azure gratis](/pricing/free-trial/): obtenga créditos que puede usar para probar los servicios de pago de Azure, e incluso cuando los haya agotado, podrá conservar la cuenta y usar los servicios de Azure gratis, como Aplicaciones web. + Puede [activar los beneficios de suscriptores de Visual Studio](/pricing/member-offers/msdn-benefits-details/): su suscripción a Visual Studio le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.
>
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Configuración de la aplicación web de producción

>[AZURE.NOTE] El script que se emplea en este tutorial configurará automáticamente la publicación continua desde el repositorio de GitHub. Esto requiere que las credenciales de GitHub ya estén almacenadas en Azure; en caso contrario, no se realizará la implementación con script al intentar definir la configuración del control de código fuente para las aplicaciones web.
>
>Para almacenar sus credenciales de GitHub en Azure, cree una aplicación web en el [Portal de Azure](https://portal.azure.com/) y [configure la implementación de GitHub](web-sites-publish-source-control.md#Step7). Solo tiene que hacer esto una vez.

En un escenario típico de DevOps, tiene una aplicación que se ejecuta directamente en Azure y quiere realizar cambios en ella mediante publicación continua. En este escenario, se implementará en producción una plantilla que haya desarrollado y probado.

1.	Cree su propia bifurcación del repositorio [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Para obtener información sobre la creación de la bifurcación, consulte [Bifurcación de un repositorio](https://help.github.com/articles/fork-a-repo/). Una vez creada la bifurcación, puede verla en el explorador.

	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Abra una sesión del Shell de Git. Si aún no tiene el Shell de Git, instale ahora [GitHub para Windows](https://windows.github.com/).
3.	Cree un clon local de la bifurcación ejecutando el comando siguiente:

        git clone https://github.com/<your_fork>/ToDoApp.git

4.	Cuando tenga el clon local, vaya a *&lt;raíz\_repositorio>*\\ARMTemplates y ejecute el script deploy.ps1 con un sufijo único, como se muestra a continuación:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.	Cuando se le solicite, escriba el nombre de usuario y la contraseña que quiera para el acceso a la base de datos. Recuerde las credenciales de la base de datos porque tendrá que especificarlas al actualizar el grupo de recursos.

	Debería ver el progreso de aprovisionamiento de varios recursos de Azure. Cuando finalice la implementación, el script iniciará la aplicación en el explorador y emitirá un bip. ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.	De nuevo en la sesión del Shell de Git, ejecute:

        .\swap –Name ToDoApp<your_suffix>

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.	Cuando finalice el script, vuelva a examinar la dirección del front-end (http://ToDoApp*&lt;your_suffix>*master.azurewebsites.net/) para ver la ejecución de la aplicación en producción.
5.	Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y observe lo que se crea.

	Podrá ver dos aplicaciones web en el mismo grupo de recursos, una con el sufijo `Api` en el nombre. Si observa la vista de grupo de recursos, también verá el servidor y Base de datos SQL, el plan de Servicio de aplicaciones y las ranuras de ensayo para las aplicaciones web. Examine los distintos recursos y compárelos con *&lt;raíz\_repositorio>*\\ARMTemplates\\ProdAndStage.json para ver cómo están configurados en la plantilla.

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Ha configurado la aplicación de producción. Ahora, imaginemos que recibe comentarios informándole de que la facilidad de uso de la aplicación es insuficiente. Por lo que decide investigar. Va a instrumentar la aplicación para que le proporcione información.

## Investigación: instrumentar la aplicación de cliente para supervisión y métricas

5. Abra *& lt; repository\_root >*\\src\\MultiChannelToDo.sln en Visual Studio.
6. Restaure todos los paquetes de Nuget haciendo clic con el botón derecho en la solución > **Administrar paquetes de NuGet para la solución** > **Restaurar**.
6. Haga clic con el botón derecho en **MultiChannelToDo.Web** > **Agregar Telemetría de Application Insights** > **Definir la configuración** > Cambie el grupo de recursos a ToDoApp * & lt; your\_suffix > * > **Agregar Application Insights al proyecto**.
7. En el Portal de Azure, abra la hoja para el recurso de Application Insight **MultiChannelToDo.Web**. A continuación, en la parte de **Estado de la aplicación**, haga clic en **Obtenga información sobre cómo recopilar datos de carga de página del explorador** > Copiar código.
7. Agregue el código de instrumentación JS copiado a *& lt; repository\_root >*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml, justo antes de la etiqueta `<heading>` de cierre. Debe contener la clave de instrumentación única del recurso de Application Insight.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. Envíe eventos personalizados a Application Insights para los clics del mouse agregando el código siguiente a la parte inferior del cuerpo:

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    Este fragmento de código de JavaScript envía un evento personalizado a Application Insights cada vez que un usuario hace clic en cualquier parte de la aplicación web.

12. En el Shell de Git, confirme e inserte los cambios en la bifurcación en GitHub. A continuación, espere a que los clientes actualicen el explorador.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.	Cambie los cambios de la aplicación implementados a producción:

        .\swap –Name ToDoApp<your_suffix>

13. Busque el recurso de Application Insights que configuró. Haga clic en Eventos personalizados

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    Si no ve las métricas para los eventos personalizados, espere unos minutos y haga clic en **Actualizar**.

Supongamos que ve un gráfico como el siguiente:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

Y la cuadrícula de eventos debajo de él:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

Según el código de aplicación ToDoApp, el evento **BUTTON** se corresponde con el botón Enviar y el evento **INPUT** se corresponde con el cuadro de texto. Hasta aquí, todo está claro. Sin embargo, parece que hay bastantes clics y muy pocos clics en los elementos de tareas pendientes (el evento **LI**).

Según esto, puede formular la hipótesis de que algunos usuarios no tienen claro en qué parte de la interfaz de usuario se puede hacer clic, y es porque el cursor se ha adaptado para la selección de texto cuando se mantiene el mouse sobre los elementos de lista y sus iconos.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Este podría ser un ejemplo inventado. De cualquier forma, realice una mejora en la aplicación y, a continuación, implemente una distribución de paquetes piloto para obtener comentarios sobre la facilidad de uso por parte de los clientes.

### Instrumentación de la aplicación del servidor para supervisión y métricas
Este es un tema tangencial, porque el escenario con el que se trabaja en este tutorial solo concierne a la aplicación cliente. De todas formas, para tener una visión completa se configurará también la aplicación del lado del servidor.

6. Haga clic con el botón derecho en **MultiChannelToDo** > **Agregar Telemetría de Application Insights** > **Definir la configuración** > Cambie el grupo de recursos a ToDoApp * & lt; your\_suffix > * > **Agregar Application Insights al proyecto**.
12. En el Shell de Git, confirme e inserte los cambios en la bifurcación en GitHub. A continuación, espere a que los clientes actualicen el explorador.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.	Cambie los cambios de la aplicación implementados a producción:

        .\swap –Name ToDoApp<your_suffix>

Eso es todo.

## Investigación: agregar etiquetas específicas de ranura a las métricas de la aplicación de cliente

En esta sección, configurará las diferentes ranuras de implementación para enviar telemetría específica de cada ranura al mismo recurso de Application Insights. De este modo, puede comparar los datos de telemetría entre el tráfico de las diferentes ranuras (entornos de implementación) para ver rápidamente el efecto de los cambios que se realizaron en la aplicación. Al mismo tiempo, puede separar el tráfico de producción del resto para poder continuar supervisando la aplicación de producción, según sea necesario.

Como está recopilando datos sobre el comportamiento del cliente, [agregará un inicializador de telemetría al código JavaScript](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer) en index.cshtml. Si desea probar el rendimiento del servidor, por ejemplo, puede hacer algo similar en el código del servidor (consulte [API de Application Insights para eventos y métricas personalizados](../application-insights/app-insights-api-custom-events-metrics.md)).

1. En primer lugar, agregue el código entre los dos comentarios `//` a continuación en el código JavaScript que agregó antes a la etiqueta `<heading>`.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Este código de inicializador hace que el objeto `appInsights` agregue una propiedad personalizada denominada `Environment` a cada dato de telemetría que envía.

2. A continuación, agregue esta propiedad personalizada como una [configuración de ranura](web-sites-staged-publishing.md#AboutConfiguration) para la aplicación web en Azure. Para ello, ejecute el siguiente comando en la sesión de Shell de Git:

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    El archivo Web.config del proyecto ya define el valor `environment` de la aplicación. Con esta configuración, al probar la aplicación localmente, las métricas se etiquetarán con `VS Debugger`. De todas formas, cuando inserte los cambios en Azure, Azure encontrará y usará el valor de la aplicación `environment` en la configuración de la aplicación web en su lugar, y las métricas se etiquetarán con `Production`.

3. Confirme e inserte los cambios de código para la bifurcación en GitHub, y espere a que los usuarios usen la nueva aplicación (es necesario actualizar el explorador). La nueva propiedad tarda unos 15 minutos en aparecer en el recurso `MultiChannelToDo.Web` de Application Insights.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. Ahora, vaya a la hoja **Eventos personalizados** de nuevo y filtre las métricas en `Environment=Production`. Verá todos los nuevos eventos personalizados en la ranura de producción con este filtro.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. Haga clic en el botón **Favoritos** para guardar la configuración actual del Explorador de métricas con un nombre del tipo **Eventos personalizados: producción**. Más adelante puede cambiar fácilmente entre esta vista y la vista de una ranura de implementación.

    > [AZURE.TIP] Para realizar análisis aún más eficaces, considere la posibilidad de la [integración de los recursos de Application Insights con Power BI](../application-insights/app-insights-export-power-bi.md).

### Incorporación de etiquetas específicas de ranura a las métricas de la aplicación de servidor
De nuevo, con el fin de tener una visión completa se configurará también la aplicación del lado del servidor. A diferencia de la aplicación de cliente que se instrumenta en JavaScript, las etiquetas específicas de ranura para la aplicación de servidor se instrumentan con código. NET.

1. Abra *& lt; repository\_root >*\\src\\MultiChannelToDo\\Global.asax.cs. Agregue el bloque de código siguiente, justo antes de cerrar la llave del espacio de nombres.

		namespace MultiChannelToDo
		{
				...

				// Begin new code
		    public class ConfigInitializer
		    : ITelemetryInitializer
		    {
		        void ITelemetryInitializer.Initialize(ITelemetry telemetry)
		        {
		            telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
		        }
		    }
				// End new code
		}

2. Corrija los errores de resolución de nombres mediante la incorporación al principio del archivo de las instrucciones `using` a continuación:

		using Microsoft.ApplicationInsights.Channel;
		using Microsoft.ApplicationInsights.Extensibility;

3. Agregue el código siguiente al principio del método `Application_Start()`:

		TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. Confirme e inserte los cambios de código para la bifurcación en GitHub, y espere a que los usuarios usen la nueva aplicación (es necesario actualizar el explorador). La nueva propiedad tarda unos 15 minutos en aparecer en el recurso `MultiChannelToDo` de Application Insights.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## Actualización: configurar la bifurcación de la versión beta

2. Abra *& lt; repository\_root >*\\ARMTemplates\\ProdAndStagetest.json y encuentre los recursos `appsettings` (busque `"name": "appsettings"`). Hay 4 de ellos, uno para cada ranura. 

2. Para cada recurso `appsettings`, agregue un valor de aplicación `"environment": "[parameters('slotName')]"` al final de la matriz `properties`. No olvide poner una coma al final de la línea anterior.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    Acaba de agregar el valor de aplicación `environment` a todas las ranuras de la plantilla.
    
2. En el mismo archivo, busque los recursos `slotconfignames` (buscar `"name": "slotconfignames"`). Hay 2 de ellos, uno para cada aplicación.

2. Para cada recurso `slotconfignames`, agregue `"environment"` al final de la matriz `appSettingNames`. No olvide poner una coma al final de la línea anterior.

    Acaba de hacer que el valor de aplicación `environment` se ajuste a la ranura de implementación correspondiente para ambas aplicaciones.

3. En su sesión del Git Shell, ejecute los comandos siguientes con el mismo sufijo de grupo de recursos que usó antes.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. Cuando se le pida, especifique las mismas credenciales de Base de datos SQL que antes. A continuación, cuando se le pida que actualice el grupo de recursos, escriba `Y`, y luego `ENTER`.

    Una vez que finalice el script, se conservan todos los recursos en el grupo de recursos original, pero se crea en él una nueva ranura llamada "beta" con la misma configuración que la ranura de "Ensayo" que se creó al principio.

    >[AZURE.NOTE] Este método de creación de diferentes entornos de implementación es diferente del método en [Agile Software Development con el Servicio de aplicaciones de Azure](app-service-agile-software-development.md). Aquí puede crear entornos de implementación con las ranuras de implementación, mientras que en el otro caso los entornos de implementación se crean con grupos de recursos. Administrar entornos de implementación con grupos de recursos le permite mantener el entorno de producción fuera del radio de acción de los desarrolladores, pero no es fácil realizar pruebas en producción, lo que sí es fácil de hacer con las ranuras.

Si lo desea, también puede crear una aplicación alfa ejecutando

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Para este tutorial, continúe usando la aplicación beta.

## Actualización: insertar las actualizaciones en la aplicación beta

Volver a la aplicación que desea mejorar.

1. Asegúrese de que se encuentra ahora en la bifurcación de la versión beta

        git checkout beta

2. En *& lt; repository\_root >*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml, encuentre la etiqueta `<li>` y agregue el atributo `style="cursor:pointer"`, como se muestra a continuación.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. confirme e inserte en Azure.

4. Compruebe que el cambio se refleja en la ranura de la versión beta, vaya a http://todoapp*&lt;your_suffix> *-beta.azurewebsites.net/. Si aún no ve el cambio, actualice el explorador para obtener el nuevo código de javascript.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Ahora que ya tiene el cambio ejecutándose en la ranura de la versión beta, está listo para realizar una implementación de distribución de paquetes piloto.

## Validación: enrutar el tráfico a la aplicación beta

En esta sección, enrutará el tráfico a la aplicación beta. Por motivos de claridad en la demostración, va a enrutar una parte significativa de tráfico de usuario a la aplicación. En realidad, la cantidad de tráfico que debe enrutar dependerá de la situación específica. Por ejemplo, si su sitio está en la escala de microsoft.com, probablemente necesite menos del uno por ciento del tráfico total para poder obtener datos útiles.

1. En su sesión del Git Shell, ejecute los siguientes comandos para enrutar la mitad del tráfico de producción a la ranura de versión beta:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  La propiedad `ReroutePercentage=50` especifica que el 50 % del tráfico de producción se enrutará a la URL de la aplicación beta (especificada por la propiedad `ActionHostName`).

2. Ahora, vaya a http://ToDoApp*&lt;your_suffix> *. azurewebsites.net. El 50 % del tráfico debería ahora redirigirse a la ranura de la versión beta.

3. En el recurso de Application Insights, filtre las métricas por entorno = "beta".

    > [AZURE.NOTE] Si guarda esta vista filtrada como otro favorito, es muy fácil alternar las vistas del explorador de métrica entre las vista de producción y de la versión beta.

Supongamos que en Application Insights ve algo similar a lo siguiente:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

No solo se muestra que se están haciendo muchos más clics en las etiquetas `<li>`, sino que parece haber un aumento importante de clics en las etiquetas `<li>`. Por tanto se puede concluir que los usuarios han descubierto que se puede hacer clic en las nuevas etiquetas `<li>` y ahora están borrando todas sus tareas completadas previamente en la aplicación.

Según los datos de la implementación de distribución de paquetes piloto, usted decide que la nueva interfaz de usuario está lista para su fase de producción.

## Puesta en marcha: mover el código nuevo a producción

Ahora ya está listo para mover la actualización a producción. Lo bueno es que ahora sabe que la actualización ya se ha validado _antes_ de que se inserte en producción. Ahora puede implementarlo con confianza. Puesto que realizó una actualización a la aplicación de cliente AngularJS, solo se valida el código de cliente. Si desea realizar cambios en la aplicación de la API de web de back-end, puede validar los cambios de forma similar y fácilmente.

1. En el Shell de Git, ejecute el comando siguiente para quitar la regla de enrutamiento de tráfico:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Ejecute los comandos Git:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. Espere unos minutos para que el nuevo código se implemente en la ranura de ensayo, a continuación, inicie http://ToDoApp*&lt;your_suffix> *-staging.azurewebsites.net para comprobar que la nueva actualización está preparada en la ranura de ensayo. Recuerde que la rama de la bifurcación principal está vinculado a la ranura de ensayo de la aplicación.

3. Ahora, cambie la ranura de ensayo a producción

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## Resumen ##

El Servicio de aplicaciones de Azure permite que las pequeñas y medianas empresas prueben con facilidad sus aplicaciones para clientes en producción, algo que tradicionalmente estaba reservado a las grandes empresas. Esperamos que este tutorial le haya proporcionado los conocimientos necesarios para reunir el Servicio de aplicaciones y Application Insights y posibilitar la implementación de la distribución de paquetes piloto, e incluso para otros escenarios de prueba en producción en su entorno de DevOps.

## Más recursos ##

-   [Agile Software Development con el Servicio de aplicaciones de Azure](app-service-agile-software-development.md)
-   [Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-staged-publishing.md)
-	[Implementación predecible de una aplicación compleja en Azure](app-service-deploy-complex-application-predictably.md)
-	[Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md)
-	[JSONLint: validador de JSON](http://jsonlint.com/)
-	[Creación de ramas de Git: combinación y creación de ramas básicas](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Azure PowerShell](../powershell-install-configure.md)
-	[Wiki de Project Kudu](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_0211_2016-->