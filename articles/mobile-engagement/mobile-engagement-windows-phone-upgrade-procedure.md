<properties 
	pageTitle="Procedimientos de actualización del SDK de Windows Phone Silverlight" 
	description="Procedimientos de actualización de SDK de Windows Phone Silverlight para Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Procedimientos de actualización del SDK de Windows Phone Silverlight

Si ya integró una versión anterior de nuestro SDK en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK. Por ejemplo, si migra desde 0.10.1 a 0.11.0, primero debe seguir el procedimiento "de 0.9.0 a 0.10.1" y luego el procedimiento "de 0.10.1 a 0.11.0".

##De 1.1.1 a 2.0.0

A continuación se describe cómo migrar una integración del SDK desde el servicio Capptain que ofrece Capptain SAS en una aplicación con la tecnología de Azure Mobile Engagement.

> [Azure.IMPORTANT] Capptain y Mobile Engagement no son los mismos servicios, y el procedimiento que se indica a continuación destaca únicamente cómo migrar la aplicación cliente. La migración del SDK en la aplicación NO migrará los datos desde los servidores Capptain a los servidores Mobile Engagement.

Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.1.1 en primer lugar luego aplique el siguiente procedimiento.

### Paquete de NuGet

Reemplace **Capptain.WindowsPhone** por el paquete Nuget **MicrosoftAzure.MobileEngagement**.

### Aplicar Mobile Engagement

El SDK usa el término `Engagement`. Debe actualizar el proyecto para que coincida con este cambio.

Debe desinstalar el paquete NuGet de Capptain actual. Tenga en cuenta que se van a quitar todos los cambios de la carpeta recursos de Capptain. Si desea conservar los archivos, haga una copia de ellos.

Después, instale el nuevo paquete NuGet de Microsoft Azure Mobile Engagement en el proyecto. Puede encontrarlo directamente en [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Esta acción reemplaza todos los archivos de recursos que Engagement usa y agrega el nuevo archivo DLL de Engagement a las referencias del proyecto.

Tendrá que limpiar las referencias del proyecto al eliminar de referencias del archivo DLL de Capptain. Si no lo hace, la versión de Capptain entrará en conflicto y se producirán errores.

Si tiene recursos Capptain personalizados, copie el contenido de los archivos antiguos y péguelos en los nuevos archivos de Engagement. Tenga en cuenta que se deben actualizar tanto los archivos xaml como los archivos cs.

Una vez completados estos pasos, solo tendrá que reemplazar las referencias de Capptain antiguas por las nuevas referencias de Engagement.

1. Se deben actualizar todos los espacios de nombres de Capptain.

	Antes de la migración:
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	Después de la migración:
	
		using Microsoft.Azure.Engagement;

2. Todas las clases de Capptain que contengan "Capptain" deberían contener "Engagement".

	Antes de la migración:
	
		public sealed partial class MainPage : CapptainPage
		{
		  protected override string GetCapptainPageName()
		  {
		    return "Capptain Demo";
		  }
		  ...
		}
	
	Después de la migración:
	
		public sealed partial class MainPage : EngagementPage
		{
		  protected override string GetEngagementPageName()
		  {
		    return "Engagement Demo";
		  }
		  ...
		}

3. En el caso de los archivos xaml, también cambian el espacio de nombres y los atributos de Capptain.

	Antes de la migración:
	
		<capptain:CapptainPage
		...
		xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
		...
		</capptain:CapptainPage>
	
	Después de la migración:
	
		<engagement:EngagementPage
		...
		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
		...
		</engagement:EngagementPage>

4. En el caso de otros recursos, como imágenes Capptain, tenga en cuenta que su nombre también habrá cambiado para usar "Engagement".

### Identificador de aplicación o clave SDK

Engagement usa una cadena de conexión. No es necesario especificar un identificador de aplicación y una clave SDK con Mobile Engagement, solo tiene que especificar una cadena de conexión. Esta se puede configurar en el archivo EngagementConfiguration.

La configuración de Engagement se puede establecer en el archivo `Resources\EngagementConfiguration.xml` del proyecto.

Edite este archivo para especificar lo siguiente:

-   La cadena de conexión de la aplicación entre las etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificarla en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de Engagement:

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		
		/* Initialize Engagement angent with above configuration. */
		EngagementAgent.Instance.Init(engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de Azure clásico.

### Cambio de nombre de elementos

Todos los elementos con el nombre *capptain* han cambiado a *engagement*. De forma similar, *Capptain* cambió a *Engagement*.

Ejemplos de elementos Capptain que se usan habitualmente:

-   CapptainConfiguration ahora se llama EngagementConfiguration
-   CapptainAgent ahora se llama EngagementAgent
-   CapptainReach ahora se llama EngagementReach
-   CapptainHttpConfig ahora se llama EngagementHttpConfig
-   GetCapptainPageName ahora se llama GetEngagementPageName

Tenga en cuenta que el cambio de nombre también afecta a los métodos invalidados.



 

<!---HONumber=AcomDC_0302_2016-->