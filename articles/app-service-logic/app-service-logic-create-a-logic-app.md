<properties
	pageTitle="Crear una aplicación lógica | Microsoft Azure"
	description="Aprenda a crear una aplicación lógica mediante la conexión de servicios de SaaS"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/12/2016"
	ms.author="stepsic"/>

# Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS

| Referencia rápida |
| --------------- |
| [Lenguaje de definición de aplicaciones lógicas](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Documentación del conector de aplicaciones lógicas](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Foro de aplicaciones lógicas](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurelogicapps) |

En este tema se muestra cómo, en solo unos minutos, puede comenzar a trabajar con las [aplicaciones lógicas de Servicios de aplicaciones](app-service-logic-what-are-logic-apps.md). Recorreremos un flujo de trabajo que permite entregar un conjunto de tweets en el que está interesado a una carpeta de Dropbox.

Para utilizar este escenario necesitará:

- Una suscripción de Azure
- Una cuenta de Twitter
- Una cuenta de Dropbox

<!--- TODO: Add try it now information here -->

## Obtener conectores

En primer lugar, deberá crear los dos conectores que se van a usar: el [conector de Dropbox](app-service-logic-connector-dropbox.md) y el [conector de Twitter](app-service-logic-connector-twitter.md). Debido a ciertas restricciones en la API de Twitter, también necesitaremos registrarnos para una aplicación gratuita con Twitter. Para crearlos:

1. Inicie sesión en el Portal de Azure.

2. Haga clic en [Marketplace](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps/) en la pantalla principal y busque Twitter (o [haga clic aquí](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2/)).

3. Seleccione el **conector de Twitter** y haga clic **Crear**. Aparecerá una vista con toda la configuración. Puede dejar el nombre como **Conector Twitter**.
4. Seleccione **Configuración del paquete**: aquí deberá especificar la información de su aplicación de Twitter. Puede configurar una aplicación gratuita siguiendo estos pasos:
	1. Vaya a la [página de registro de la aplicación de Twitter](http://apps.twitter.com).
	2. Cree una nueva aplicación.
	3. Asígnele un nombre y una descripción Puede especificar cualquier dirección URL para el sitio web y cualquier dirección URL para la dirección URL de devolución de llamada (no dejar en blanco).
	4. Una vez registrado, copie la **Clave de consumidor** de Twitter en el campo **clientId** de Azure y el **Secreto de consumidor** de Twitter en **clientSecret.**
	5. Haga clic en **Aceptar** en el panel de Azure para volver a las demás configuraciones de API.

5. Escriba un nombre de plan en **Crear nuevo plan de servicio de aplicaciones**.

	>[AZURE.NOTE]En los pasos de esta sección se supone que va a crear un nuevo plan de servicio de aplicaciones. Si usa un plan de servicio de aplicaciones existente, haga clic en **Seleccionar existente**, seleccione el plan existente y, a continuación, vaya al último paso de esta sección. Necesita un plan para hospedar todas las aplicaciones.

6.  Seleccione un **nivel de precios** para su nuevo plan.

	>[AZURE.NOTE]De forma predeterminada, se muestran únicamente los planes recomendados para aplicaciones lógicas. Haga clic en **Ver todos** para ver todos los planes disponibles. Al ejecutar una aplicación lógica en el nivel Gratis, solo puede realizar ejecuciones cada hora y usar hasta 1.000 acciones por mes.

7. Cree un **grupo de recursos** para el flujo.

	Los grupos de recursos actúan como contenedores para las aplicaciones. Todos los recursos de la aplicación residirán en el mismo grupo de recursos.

8. Si tiene más de una suscripción de Azure, elija la que va a usar.

9. Elija la **ubicación** para ejecutar la aplicación lógica.

	![Creación de vista de aplicación de API](./media/app-service-logic-create-a-logic-app/gallery.png)

10. Haga clic en **Crear**. El paso de aprovisionamiento puede tardar un minuto o dos.

11. Ahora repita el proceso con [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2/).

## Iniciar la aplicación lógica

Ahora debe crear una nueva aplicación lógica:

1. Haga clic en **+ Nuevo** en la parte inferior izquierda de la pantalla, expanda **Web + Móvil** y haga clic en **Aplicación lógica**.

 	Esto muestra la vista Crear aplicación lógica, donde se proporciona alguna configuración básica para empezar.

	![Creación de vista de aplicación lógica](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. En **Nombre** escriba un nombre significativo para la aplicación lógica.

3. Elija el **plan de servicio de aplicaciones** que usó al crear los conectores. Esto elige automáticamente la ubicación, la suscripción y el grupo de recursos.

Esto se encarga de la configuración básica, pero no haga clic en **Crear**. A continuación, agregará los desencadenadores y acciones al flujo de trabajo.

## Agregar un desencadenador

Los desencadenadores son los que permiten que la aplicación lógica se ejecute. A continuación, agregará un desencadenador de periodicidad, que inicia el flujo de trabajo en una programación predefinida.

1. Todavía en la hoja **Crear aplicación lógica**, haga clic en **Desencadenadores y acciones**.

	Se muestra un diseñador de pantalla completa con el flujo y algunas plantillas con las que comenzar.
	
2. En este tutorial vamos a **partir de cero**. Siempre puede usar una plantilla si cree que puede resultar útil.
    
    Ahora, en el lado derecho hay una lista de todos los servicios que podrían tener desencadenadores.

3. En la sección superior, haga clic en **Periodicidad**.

	Esto agrega un cuadro donde puede especificar la configuración de periodicidad.

	![Periodicidad](./media/app-service-logic-create-a-logic-app/recurrence.png)

4.  Elija una opción de periodicidad en **Frecuencia** e **Intervalo** (por ejemplo, una vez cada hora) y, a continuación, haga clic en la marca de verificación verde.

Ahora, agregará una acción al flujo.

## Agregar una acción de Twitter

Las acciones son lo que hace el flujo de trabajo. Puede tener cualquier número de acciones y organizarlas de forma que la información de una acción se pase a la siguiente.

1. En el panel de la derecha, haga clic en **Conector Twitter**.

2. Una vez cargado, haga clic en el botón **Autorizar**, inicie sesión en su cuenta de Twitter y haga clic en **Autorizar aplicación**.

	Esto concede acceso al conector a su cuenta de Twitter. Se muestra una lista de las posibles operaciones proporcionadas por el conector Twitter.

	![Acciones](./media/app-service-logic-create-a-logic-app/actions.png)

	> [AZURE.NOTE] El botón **Autorizar** usa seguridad OAUTH para conectarse a servicios de SaaS, como Twitter. Más información sobre OAUTH en [Seguridad OAUTH](app-service-logic-oauth-security.md).

3. Haga clic en **Buscar tweets**, a continuación en **Especificar una consulta**, escriba algo como `#MicrosoftAzure` y haga clic en la marca de verificación verde.

	![Búsqueda de Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

El conector Twitter ahora forma parte del flujo de trabajo.

## Agregar una acción de Dropbox y creación de la aplicación

El último paso es agregar una acción que cargue unos tweets a un archivo de Dropbox.

1. En el panel de la derecha, haga clic en **Conector Dropbox**.

2. Tras completar el aprovisionamiento, haga clic en **Autorizar**, inicie sesión en su cuenta de Dropbox y haga clic en **Permitir**.

	![Autorización del conector de Dropbox.](./media/app-service-logic-create-a-logic-app/authorize.png)

	Esto concede acceso al conector a su cuenta de Dropbox. Se muestra una lista de las posibles operaciones proporcionadas por el conector Dropbox.

4. Haga clic en **Cargar archivo**.

	Esto muestra la configuración del conector Dropbox, que se debe establecer para pasar los datos de la búsqueda de Twitter a Dropbox.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. En el campo **Ruta de acceso al archivo**, escriba `/tweet.txt`.

4. En el campo **Contenido**, haga clic en el botón `...` y haga clic en la opción **Texto del tweet**.

	Esto introduce el valor `@first(body('twitterconnector')).TweetText` en el cuadro de texto. Este valor generado contiene las siguientes partes:

	Parte de contenido | Descripción
	------------------------------------------ | ------------
	 `@` | Indica que ha introducido una función, en lugar de un valor real.
	`actions('twitterconnector').outputs.body` | Obtiene los tweets devueltos por la consulta del conector Twitter.
	`first()` | La acción de búsqueda de tweets devuelve una lista, pero el usuario solo desea cargar un archivo
	`.TweetText` | Selecciona la propiedad de mensaje del tweet.

5. Haga clic en la marca de verificación verde para guardar la configuración del conector.

5. Ahora que el diseño se ha completado, haga clic en **Vista Código** en la parte superior izquierda del diseñador y tenga en cuenta que este es el código JSON que define el flujo de trabajo que acaba de crear en el diseñador. Trataremos este código más en el [siguiente tema](Use logic app features).

6. Haga clic en el botón **Aceptar** en la parte inferior de la pantalla y, a continuación, haga clic en el botón **Crear**.

	Esto crea la nueva aplicación lógica.

## Administrar la aplicación lógica tras la creación

Ahora la aplicación lógica está en funcionamiento. Cada vez que se ejecuta el flujo de trabajo programado, dicho flujo busca tweets con el hashtag específico. Cuando encuentra un tweet coincidente, lo coloca en Dropbox. Por último, verá cómo deshabilitar la aplicación o ver cómo marcha.

1. Haga clic en **Examinar** en el lado izquierdo de la pantalla y seleccione **Aplicaciones lógicas**.

2. Haga clic en la nueva aplicación lógica que acaba de crear para ver información general y el estado actual.

3. Para editar la nueva aplicación lógica, haga clic en **Desencadenadores y acciones**.

5. Para desactivar la aplicación, haga clic en **Deshabilitar** en la barra de comandos.

En menos de 5 minutos ha sido capaz de configurar una aplicación lógica sencilla que se ejecuta en la nube. Para obtener más información acerca del uso de las características de las aplicaciones lógicas, consulte [Uso de las características de aplicaciones lógicas]. Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[Uso de las características de aplicaciones lógicas]: app-service-logic-use-logic-app-features.md

<!-----HONumber=AcomDC_0128_2016-->