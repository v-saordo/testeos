<properties 
	pageTitle="Integración del SDK de Windows Phone Silverlight Engagement" 
	description="Cómo integrar Azure Mobile Engagement con aplicaciones Windows Phone Silverlight" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Integración del SDK de Windows Phone Silverlight Engagement

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

En este procedimiento se describe la manera más sencilla de activar las funciones de análisis y supervisión de Azure Mobile Engagement en su aplicación de Windows Phone Silverlight.

Los siguientes pasos son suficientes para activar el informe de los registros necesarios para calcular todas las estadísticas en relación con los usuarios, las sesiones, las actividades, los bloqueos y los aspectos técnicos. El informe de los registros necesarios para calcular otras estadísticas, como eventos, errores y trabajos debe realizarse manualmente mediante la API de Engagement (vea [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) a continuación) debido a que estas estadísticas dependen de la aplicación.

##Versiones compatibles

El SDK de Mobile Engagement para Windows Silverlight solo se puede integrar en las aplicaciones destinadas a lo siguiente:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Si va a desarrollar para Windows Phone 8.1 (no Silverlight), consulte entonces el [procedimiento de integración de Windows Universal](mobile-engagement-windows-store-integrate-engagement.md).

##Instale el SDK de Mobile Engagement Silverlight

El SDK de Mobile Engagement para Windows Silverlight está disponible como un paquete de Nuget llamado *MicrosoftAzure.MobileEngagement*. Puede instalarlo desde el Administrador de paquetes nuget de Visual Studio.

##Agregar las capacidades

El SDK de Engagement necesita algunas capacidades del SDK de Windows Phone Silverlight para que funcione correctamente.

Abra el archivo `WMAppManifest.xml` y asegúrese de que en el panel `Capabilities` se han declarado las siguientes capacidades:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##Inicializar el SDK de Engagement

### Configuración de Engagement

La configuración de Engagement se centraliza en el archivo `Resources\EngagementConfiguration.xml` del proyecto.

Edite este archivo para especificar lo siguiente:

-   La cadena de conexión de la aplicación entre las etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificarla en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de Azure clásico.

### Inicialización de Engagement

Al crear un nuevo proyecto, se genera un archivo `App.xaml.cs`. Esta clase hereda de `Application` y contiene muchos métodos importantes. También se usará para inicializar el SDK de Engagement.

Modifique `App.xaml.cs`:

-   Agregue las instrucciones `using`:

		using Microsoft.Azure.Engagement;

-   Inserte `EngagementAgent.Instance.Init` en el método `Application_Launching`:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		  EngagementAgent.Instance.Init();
		}

-   Inserte `EngagementAgent.Instance.OnActivated` en el método `Application_Activated`:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		}

> [AZURE.WARNING] Se desaconseja encarecidamente agregar la inicialización de Engagement en otro lugar de la aplicación. Sin embargo, tenga en cuenta que el método `EngagementAgent.Instance.Init` se ejecuta en un subproceso dedicado y no en el subproceso de la interfaz de usuario.

##Informes básicos

### Método recomendado: sobrecargar las clases `PhoneApplicationPage`

Para activar el informe de todos los registros que Engagement necesita para calcular las estadísticas de usuarios, sesiones, actividades, bloqueos y aspectos técnicos, puede hacer que todas las subclases `PhoneApplicationPage` hereden de las clases `EngagementPage`.

Este es un ejemplo de cómo hacer esto para una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

#### Archivo de código fuente C#

Modifique el archivo `.xaml.cs` de página:

-   Agregue las instrucciones `using`:

		using Microsoft.Azure.Engagement;

-   Reemplace `PhoneApplicationPage` por `EngagementPage`:

**Sin Engagement:**

		namespace Example
		{
		  public partial class ExamplePage : PhoneApplicationPage
		  {
		    [...]
		  }
		}

**Con Engagement:**

		using Microsoft.Azure.Engagement;
		
		namespace Example
		{
		  public partial class ExamplePage : EngagementPage 
		  {
		    [...]
		  }
		}

> [AZURE.WARNING] Si la página hereda del método `OnNavigatedTo`, asegúrese de permitir la llamada `base.OnNavigatedTo(e)`. De lo contrario, no se informará la actividad. De hecho, `EngagementPage` llama a `StartActivity` dentro del método `OnNavigatedTo`.

#### Archivo XAML

Modifique el archivo `.xaml` de página:

-   Agregue a las declaraciones de espacios de nombres:

		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Reemplace `phone:PhoneApplicationPage` por `engagement:EngagementPage`:

**Sin Engagement:**

		<phone:PhoneApplicationPage>
		    <!-- layout -->
		</phone:PhoneApplicationPage>

**Con Engagement:**

		<engagement:EngagementPage 
		    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
		
		    <!-- layout -->
		</engagement:EngagementPage >

#### Invalidar el comportamiento predeterminado

De forma predeterminada, el nombre de clase de la página se informa como el nombre de actividad, sin más. Si la clase usa el sufijo "Página", Engagement también la quitará.

Si desea invalidar el comportamiento predeterminado para el nombre, simplemente agregue lo siguiente a su código:

		// in the .xaml.cs file
		protected override string GetEngagementPageName()
		{
		   /* your code */
		   return "new name";
		}

Si desea informar algunos datos adicionales con su actividad, puede agregar lo siguiente a su código:

		// in the .xaml.cs file
		protected override Dictionary<object,object> GetEngagementPageExtra()
		{
		   /* your code */
		   return extra;
		}

Estos métodos se invocan desde el método `OnNavigatedTo` de la página.

### Método alternativo: llamar a `StartActivity()` manualmente

Si no puede o no quiere sobrecargar las clases `PhoneApplicationPage`, en su lugar, puede iniciar las actividades mediante una llamada directa a los métodos `EngagementAgent`.

Recomendamos que llame a `StartActivity` dentro del método `OnNavigatedTo` de su PhoneApplicationPage.

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		   base.OnNavigatedTo(e);
		   EngagementAgent.Instance.StartActivity("MyPage");
		}

> [AZURE.IMPORTANT] Asegúrese de finalizar la sesión correctamente.
>
> El SDK de iOS llama automáticamente al método `EndActivity` cuando se cierra la aplicación. Por lo tanto, es **MUY** recomendable llamar al método `StartActivity` cada vez que cambie la actividad del usuario y no llamar **NUNCA** al método `EndActivity`. Este método envía un mensaje al servidor de Engagement indicando que el usuario actual ha salido de la aplicación, lo que afecta a todos los registros de la aplicación.

##Informes avanzados

Opcionalmente, es aconsejable informar eventos, errores y trabajos de aplicación específicos. Para ello, use los otros métodos que se encuentran en la clase `EngagementAgent`. La API de Engagement permite usar todas las capacidades avanzadas de Engagement.

Para obtener más información, consulte [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Phone Silverlight](../mobile-engagement-windows-phone-use-engagement-api/).

##Configuración avanzada

### Deshabilitar los informes automáticos de bloqueo

Puede deshabilitar la característica de informes automáticos de bloqueo de Engagement. A continuación, cuando se produce una excepción no controlada, Engagement no hará nada.

> [AZURE.WARNING] Si tiene previsto deshabilitar esta característica, tenga en cuenta que cuando se produzca un error no controlado en la aplicación, Engagement no enviará la información del bloqueo **NI** tampoco cerrará la sesión ni los trabajos.

Para deshabilitar los informes automáticos de bloqueo, personalice la configuración según la manera en que la declaró:

#### Desde el archivo `EngagementConfiguration.xml`

Establezca el informe de bloqueo en `false` entre las etiquetas `<reportCrash>` y `</reportCrash>`.

#### Desde el objeto `EngagementConfiguration` en tiempo de ejecución

Establezca el informe de bloqueo mediante el objeto EngagementConfiguration.

		/* Engagement configuration. */

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		/* Disable Engagement crash reporting. */ engagementConfiguration.Agent.ReportCrash = false;

### Modo de ráfaga

De forma predeterminada, el servicio de Engagement informa los registros en tiempo real. Si la aplicación informa los registros con mucha frecuencia, es mejor almacenar los registros en el búfer e informarlos todos a la vez de forma periódica (esto se denomina "modo de ráfaga").

Para ello, llame al método siguiente:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. En cualquier momento, si desea volver a activar el registro en tiempo real, simplemente llame al método sin ningún parámetro o con el valor 0.

El modo de ráfaga aumenta ligeramente la duración de la batería, pero afecta al monitor de Engagement: la duración de todas las sesiones y trabajos se redondeará al umbral de ráfaga (por lo tanto, es posible que las sesiones y los trabajos más cortos que el umbral de ráfaga no sean visibles). Se recomienda usar un umbral de ráfaga inferior a 30.000 (30 segundos). Tenga en cuenta que los registros guardados se limitan a 300 elementos. Si el envío es demasiado largo, puede perder algunos registros.

> [AZURE.WARNING] El umbral de ráfaga no se puede configurar en un período inferior a un segundo. Si intenta hacerlo, el SDK mostrará un seguimiento con el error y se restablecerá automáticamente en el valor predeterminado; es decir, cero segundos. Esto hará que el SDK informe los registros en tiempo real.
 

<!---HONumber=AcomDC_0302_2016-->