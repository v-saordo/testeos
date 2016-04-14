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
	ms.topic="hero-article"
	ms.date="03/01/2016"
	ms.author="stepsic"/>

# Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS

| Referencia rápida |
| --------------- |
| [Lenguaje de definición de aplicaciones lógicas](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Documentación del conector de aplicaciones lógicas](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Foro de aplicaciones lógicas](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurelogicapps) |

En este tema se muestra cómo, en solo unos minutos, puede comenzar a trabajar con las [aplicaciones lógicas de Servicios de aplicaciones](app-service-logic-what-are-logic-apps.md). Recorreremos un flujo de trabajo que le permite entregar un conjunto de tweets que le interesa en un buzón de correo.

Para utilizar este escenario necesitará:

- Una suscripción de Azure
- Una cuenta de Twitter
- Una cuenta de Office 365

## Creación de una nueva aplicación lógica para enviarle tweets por correo electrónico

1. En el panel del Portal de Azure, seleccione **Marketplace**. 
2. En Todas, busque 'aplicaciones lógicas' y, a continuación, seleccione **Aplicación lógica (versión preliminar)**. También puede seleccionar **Nuevo**, **Web y móvil**, y seleccione **Aplicación lógica (versión preliminar)**. 
3. Escriba un nombre para la aplicación lógica, seleccione el plan del Servicio de aplicaciones y seleccione **Crear**. En este paso, estamos suponiendo que tiene un plan del Servicio de aplicaciones y está familiarizado con las propiedades necesarias. Si no, no se preocupe, puede obtener información inicial en [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). 

4. Cuando la aplicación lógica se abre por primera vez deberá tener un desencadenador. Busque **twitter** en el cuadro de búsqueda del desencadenador y selecciónelo.

7. Ahora tendrá que escribir la palabra clave que desea buscar en twitter. ![Búsqueda de Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Seleccione el signo más y elija **Agregar una acción** o **Agregar una condición**: ![Signo más](./media/app-service-logic-create-a-logic-app/plus.png)
6. Si selecciona **Agregar una acción**, se enumerarán todos los conectores con sus acciones disponibles. A continuación, puede elegir qué conector y qué acción desea agregar a la aplicación lógica. Por ejemplo, puede seleccionar **Office 365: enviar correo electrónico** u otras acciones de Office 365: ![Acciones](./media/app-service-logic-create-a-logic-app/actions.png)

7. Ahora tiene que rellenar los parámetros del correo electrónico que desee: ![Parámetros](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Por último, puede seleccionar **Guardar** para activar la aplicación lógica.

## Administrar la aplicación lógica tras la creación

Ahora la aplicación lógica está en funcionamiento. Cada vez que se ejecuta el flujo de trabajo programado, dicho flujo busca tweets con el hashtag específico. Cuando encuentra un tweet coincidente, lo coloca en Dropbox. Por último, verá cómo deshabilitar la aplicación o ver cómo marcha.

1. Haga clic en **Examinar** en el lado izquierdo de la pantalla y seleccione **Aplicaciones lógicas**.

2. Haga clic en la nueva aplicación lógica que acaba de crear para ver información general y el estado actual.

3. Para editar la nueva aplicación lógica, haga clic en **Desencadenadores y acciones**.

5. Para desactivar la aplicación, haga clic en **Deshabilitar** en la barra de comandos.

En menos de 5 minutos ha sido capaz de configurar una aplicación lógica sencilla que se ejecuta en la nube. Para obtener más información acerca del uso de las características de las aplicaciones lógicas, consulte [Uso de las características de aplicaciones lógicas]. Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Uso de las características de aplicaciones lógicas]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0302_2016-->