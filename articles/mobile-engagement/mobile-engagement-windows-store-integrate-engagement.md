<properties 
	pageTitle="Integración del SDK de Windows Universal Apps Engagement" 
	description="Cómo integrar Azure Mobile Engagement con aplicaciones Windows Universal" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

# Integración del SDK de Windows Universal Apps Engagement

> [AZURE.SELECTOR] 
- [Windows universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

En este procedimiento se describe la manera más sencilla de activar las funciones de análisis y supervisión de Engagement en su aplicación de Windows Universal.

Los siguientes pasos son suficientes para activar el informe de los registros necesarios para calcular todas las estadísticas en relación con los usuarios, las sesiones, las actividades, los bloqueos y los aspectos técnicos. El informe de los registros necesarios para calcular otras estadísticas, como eventos, errores y trabajos debe realizarse manualmente mediante la API de Engagement (vea [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)) debido a que estas estadísticas dependen de la aplicación.

## Versiones compatibles

El SDK de Mobile Engagement para aplicaciones Windows Universal solo se puede integrar en las aplicaciones Windows en tiempo de ejecución y aplicaciones de Plataforma universal de Windows destinadas a lo siguiente:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (familias de escritorio y portátiles)

> [AZURE.NOTE] Si va a desarrollar para Windows Phone Silverlight, vea entonces el [procedimiento de integración de Windows Universal Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).

## Instale el SDK de aplicaciones Mobile Engagement Universal

### Todas las plataformas

El SDK de Mobile Engagement para la aplicación Windows Universal está disponible como paquete de Nuget llamado *MicrosoftAzure.MobileEngagement*. Puede instalarlo desde el Administrador de paquetes nuget de Visual Studio.

### Windows 8.x y Windows Phone 8.1

NuGet implementa automáticamente los recursos SDK en la carpeta `Resources` en la raíz de su proyecto de aplicación.

### Aplicaciones de la Plataforma universal de Windows de Windows 10

NuGet no implementa automáticamente los recursos SDK en su aplicación UWP todavía. Tiene que hacerlo manualmente hasta que la implementación de recursos se vuelva a insertar en NuGet:

1.  Abra el Explorador de archivos.
2.  Vaya a la siguiente ubicación (**x.x.x** es la versión de Engagement que está instalando): *%USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\**x.x.x**\\content\\win81*
3.  Arrastre y coloque la carpeta **Recursos** del explorador de archivos en la raíz del proyecto en Visual Studio.
4.  En Visual Studio, seleccione el proyecto y active el icono **Mostrar todos los archivos** en la parte superior del **Explorador de soluciones**.
5.  Algunos archivos no se incluyen en el proyecto. Para importarlos a la vez, haga clic con el botón derecho en la carpeta **Recursos**, **Excluir del proyecto** y, a continuación, haga clic de nuevo con el botón derecho en la carpeta **Recursos**, **Incluir en el proyecto** para volver a incluir toda la carpeta. Todos los archivos de la carpeta **Recursos** estarán ahora incluidos en su proyecto.

## Agregar las capacidades

El SDK de Engagement necesita algunas capacidades del SDK de Windows para que funcione correctamente.

Abra el archivo `Package.appxmanifest` y asegúrese de que en el panel se han declarado las siguientes capacidades:

-   `Internet (Client)`

## Inicializar el SDK de Engagement

### Configuración de Engagement

La configuración de Engagement se centraliza en el archivo `Resources\EngagementConfiguration.xml` del proyecto.

Edite este archivo para especificar lo siguiente:

-   La cadena de conexión de la aplicación entre las etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificarla en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de Engagement:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de Azure clásico.

### Inicialización de Engagement

Al crear un nuevo proyecto, se genera un archivo `App.xaml.cs`. Esta clase hereda de `Application` y contiene muchos métodos importantes. También se usará para inicializar el SDK de Engagement.

Modifique `App.xaml.cs`:

-   Agregue las instrucciones `using`:

		using Microsoft.Azure.Engagement;

-   Defina un método para compartir la inicialización de Engagement una vez para todas las llamadas:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
		
		  // or
		
		  EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Llame a `InitEngagement` en el método `OnLaunched`:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
          InitEngagement(e);
		}

-   Cuando la aplicación se inicia mediante un esquema personalizado, otra aplicación o la línea de comandos, se llama al método `OnActivated`. También debe iniciar el SDK de Engagement cuando se activa la aplicación. Para ello, invalide el método `OnActivated`:

		protected override void OnActivated(IActivatedEventArgs args)
		{
          InitEngagement(args);
		}

> [AZURE.IMPORTANT] Se desaconseja encarecidamente agregar la inicialización de Engagement en otro lugar de la aplicación.

## Informes básicos

### Método recomendado: sobrecargar las clases `Page`

Para activar el informe de todos los registros que Engagement necesita para calcular las estadísticas de usuarios, sesiones, actividades, bloqueos y aspectos técnicos, puede hacer que todas las subclases `Page` hereden de las clases `EngagementPage`.

Este es un ejemplo de cómo hacer esto para una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

#### Archivo de código fuente C#

Modifique el archivo `.xaml.cs` de página:

-   Agregue las instrucciones `using`:

		using Microsoft.Azure.Engagement;

-   Reemplace `Page` por `EngagementPage`:

**Sin Engagement:**
	
		namespace Example
		{
		  public sealed partial class ExamplePage : Page
		  {
		    [...]
		  }
		}

**Con Engagement:**

		using Microsoft.Azure.Engagement;
		
		namespace Example
		{
		  public sealed partial class ExamplePage : EngagementPage 
		  {
		    [...]
		  }
		}

> [AZURE.IMPORTANT] Si la página invalida el método `OnNavigatedTo`, no olvide llamar a `base.OnNavigatedTo(e)`. De lo contrario, no se informará de la actividad (`EngagementPage` llama a `StartActivity` en su método `OnNavigatedTo`).

#### Archivo XAML

Modifique el archivo `.xaml` de página:

-   Agregue a las declaraciones de espacios de nombres:

		xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Reemplace `Page` por `engagement:EngagementPage`:

**Sin Engagement:**

		<Page>
		    <!-- layout -->
		    ...
		</Page>

**Con Engagement:**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		    <!-- layout -->
		    ...
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

Si no puede o no quiere sobrecargar las clases `Page`, en su lugar, puede iniciar las actividades mediante una llamada directa a los métodos `EngagementAgent`.

Recomendamos que llame a `StartActivity` dentro del método `OnNavigatedTo` de su página.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT]  Asegúrese de finalizar la sesión correctamente.
> 
> El SDK de Windows Universal llama automáticamente al método `EndActivity` cuando se cierra la aplicación. Por lo tanto, es **MUY** recomendable llamar al método `StartActivity` cada vez que cambie la actividad del usuario y no llamar **NUNCA** al método `EndActivity`. Este método envía un mensaje al servidor de Engagement indicando que el usuario actual ha salido de la aplicación, lo que afecta a todos los registros de la aplicación.

## Informes avanzados

Opcionalmente, es aconsejable informar eventos, errores y trabajos de aplicación específicos. Para ello, use los otros métodos que se encuentran en la clase `EngagementAgent`. La API de Engagement permite usar todas las capacidades avanzadas de Engagement.

Para obtener más información, consulte [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación Windows Universal](../mobile-engagement-windows-store-use-engagement-api/).

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
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		
		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### Modo de ráfaga

De forma predeterminada, el servicio de Engagement informa los registros en tiempo real. Si la aplicación informa los registros con mucha frecuencia, es mejor almacenar los registros en el búfer e informarlos todos a la vez de forma periódica (esto se denomina "modo de ráfaga").

Para ello, llame al método siguiente:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. En cualquier momento, si desea volver a activar el registro en tiempo real, simplemente llame al método sin ningún parámetro o con el valor 0.

El modo de ráfaga aumenta ligeramente la duración de la batería, pero afecta al monitor de Engagement: la duración de todas las sesiones y trabajos se redondeará al umbral de ráfaga (por lo tanto, es posible que las sesiones y los trabajos más cortos que el umbral de ráfaga no sean visibles). Se recomienda usar un umbral de ráfaga inferior a 30.000 (30 segundos). Tenga en cuenta que los registros guardados se limitan a 300 elementos. Si el envío es demasiado largo, puede perder algunos registros.

> [AZURE.WARNING] El umbral de ráfaga no se puede configurar en un período inferior a un segundo. Si intenta hacerlo, el SDK mostrará un seguimiento con el error y se restablecerá automáticamente en el valor predeterminado; es decir, cero segundos. Esto hará que el SDK informe los registros en tiempo real.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview
 

<!---HONumber=AcomDC_0302_2016-->