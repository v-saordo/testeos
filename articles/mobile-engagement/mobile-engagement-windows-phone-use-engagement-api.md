<properties 
	pageTitle="Cómo usar la API de Engagement en Windows Phone Silverlight" 
	description="Cómo usar la API de Engagement en Windows Phone Silverlight"	
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

#Cómo usar la API de Engagement en Windows Phone Silverlight

Este documento es un complemento al documento [Cómo integrar Mobile Engagement en su aplicación de Windows Phone Silverlight](../mobile-engagement-windows-phone-integrate-engagement/). En él se proporciona información detallada acerca de cómo usar la API de Engagement para informar de las estadísticas de la aplicación.

Si solo desea que Engagement informe sobre las sesiones, las actividades, los bloqueos y la información técnica de la aplicación, la manera más sencilla consiste en hacer que las subclases `PhoneApplicationPage` hereden de la clase `EngagementPage`.

Si desea hacer más, por ejemplo, si necesita informar de eventos, errores y trabajos específicos de la aplicación, o si debe informar de las actividades de la aplicación de manera diferente de la que se implementa en las clases `EngagementPage`, deberá usar la API de Engagement.

La API de Engagement la proporciona la clase `EngagementAgent`. Puede acceder a esos métodos a través de `EngagementAgent.Instance`.

Incluso si no se ha inicializado el módulo de agente, las llamadas a la API se aplazan y se ejecutará de nuevo cuando el agente está disponible.

##Conceptos de Engagement

En las siguientes secciones se detallan los conceptos de Mobile Engagement para la plataforma Windows Phone.

### `Session` y `Activity`

Una *actividad* suele estar asociada con una página de la aplicación. Es decir, la *actividad* se inicia cuando la página se muestra y se detiene cuando se cierra la página. Este es el caso cuando la integración del SDK de Engagement se realiza mediante la clase `EngagementPage`.

Sin embargo, las *actividades* también se pueden controlar manualmente mediante la API de Engagement. Esto permite dividir una página determinada en varias subpartes para obtener más detalles sobre el uso de esta página (por ejemplo, la frecuencia y la duración con las que se usan los cuadros de diálogo en esta página).

##Informes sobre actividades

### El usuario inicia una nueva actividad

#### Referencia

			void StartActivity(string name, Dictionary<object, object> extras = null)

Debe llamar a `StartActivity()` cada vez que cambie la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

> [AZURE.IMPORTANT] El SDK llaman automáticamente al método EndActivity cuando se cierra la aplicación. Por lo tanto, es MUY recomendable llamar al método StartActivity cada vez que cambie la actividad del usuario y NUNCA llamar al método EndActivity, ya que esto obliga la finalización de la sesión actual.

#### Ejemplo

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### El usuario finaliza su actividad actual

#### Referencia

			void EndActivity()

Cuando el usuario finaliza su última actividad, se debe llamar a `EndActivity()` al menos una vez. De esta manera se informa al SDK de Engagement de que el usuario está inactivo y que la sesión del usuario se debe cerrar cuando expire el tiempo de espera de la misma (si se llama a `StartActivity()` antes de que expire dicho tiempo de espera, la sesión simplemente continúa).

#### Ejemplo

			EngagementAgent.Instance.EndActivity();

##Informes de trabajos

### Iniciar un trabajo

#### Referencia

			void StartJob(string name, Dictionary<object, object> extras = null)

Puede usar el trabajo para hace un seguimiento de tareas determinadas durante un período de tiempo.

#### Ejemplo

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### Finalizar un trabajo

#### Referencia

			void EndJob(string name)

Cuando haya finalizado una tarea de la que un trabajo realiza el seguimiento, se debe llamar al método EndJob para este trabajo al proporcionar su nombre.

#### Ejemplo

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Informes de eventos

Hay tres tipos de eventos:

-   Eventos independientes
-   Eventos de sesión
-   Eventos de trabajo

### Eventos independientes

#### Referencia

			void SendEvent(string name, Dictionary<object, object> extras = null)

Los eventos independientes se pueden producir fuera del contexto de una sesión.

#### Ejemplo

			EngagementAgent.Instance.SendEvent("event", extra);

### Eventos de sesión

#### Referencia

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Los eventos de sesión se suelen usar para notificar las acciones que realiza el usuario durante su sesión.

#### Ejemplo

**Sin datos:**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Con datos:**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### Eventos de trabajo

#### Referencia

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Los eventos de trabajo se suelen usar para notificar las acciones que realiza un usuario durante un trabajo.

#### Ejemplo

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Informes de errores

Hay tres tipos de errores:

-   Errores independientes
-   Errores de sesión
-   Errores de trabajo

### Errores independientes

#### Referencia

			void SendError(string name, Dictionary<object, object> extras = null)

Al contrario de los errores de sesión, los errores independientes se pueden producir fuera del contexto de una sesión.

#### Ejemplo

			EngagementAgent.Instance.SendError("errorName", extras);

### Errores de sesión

#### Referencia

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Los errores de sesión suelen usarse para notificar los errores que afectan al usuario durante su sesión.

#### Ejemplo

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Errores de trabajo

#### Referencia

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Los errores pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual.

#### Ejemplo

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Informes de bloqueos

El agente proporciona dos métodos para tratar los bloqueos.

### Enviar una excepción

#### Referencia

			void SendCrash(Exception e, bool terminateSession = false)

#### Ejemplo

Puede enviar una excepción en cualquier momento al llamar a:

			EngagementAgent.Instance.SendCrash(aCatchedException);

También puede usar un parámetro opcional para finalizar la sesión de Engagement al mismo tiempo que se envía el bloqueo. Para ello, llame a:

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Si lo hace, la sesión y los trabajos se cerrarán después de enviar el bloqueo.

### Enviar una excepción no controlada

#### Referencia

			void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement también proporciona un método para enviar excepciones no controladas. Esto es especialmente útil cuando se usa dentro del controlador de eventos UnhandledException de Silverlight.

Este método **SIEMPRE** finalizará la sesión y los trabajos de Engagement después de su invocación.

#### Ejemplo

Puede usarlo para implementar su propio controlador UnhandledException (especialmente si deshabilitó la característica de informes de errores automáticos de Engagement). Por ejemplo, en el método `Application_UnhandledException` del archivo `App.xaml.cs`:

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
			{
			  // your own code
			
			  EngagementAgent.Instance.SendCrash(e);
			}

##OnActivated

### Referencia

			void OnActivated(ActivatedEventArgs e)

Cuando el usuario se desplaza hacia delante, alejándose de una aplicación, después de que se genere el evento Desactivado, el sistema operativo intentará poner la aplicación en un estado inactivo. A continuación, la aplicación se encuentra en un estado conocido como "extinción". En este proceso, la aplicación se finaliza, pero se conservan algunos datos sobre su estado, así como las páginas individuales dentro de la aplicación.

Tiene que insertar `EngagementAgent.Instance.OnActivated(e)` en el método `Application_Activated` desde el archivo App.xaml.cs para restablecer el agente de Engagement cuando la aplicación se haya puesto en estado de extinción.

### Ejemplo

			// Inside your App.xaml.cs file
			
			// Code to execute when the application is activated (brought to foreground)
			// This code will not execute when the application is first launched
			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			  EngagementAgent.Instance.OnActivated(e);
			}

##Identificador de dispositivo

			String GetDeviceId()

Puede obtener el identificador del dispositivo de Engagement mediante una llamada a este método.

##Parámetros adicionales

Es posible adjuntar datos arbitrarios a un evento, un error, una actividad o un trabajo. Estos datos se pueden estructurar mediante un diccionario. Las claves y los valores pueden ser de cualquier tipo.

Los datos adicionales se serializan, de modo que si desea insertar su propio tipo de datos adicionales, tendrá que agregar un contrato de datos para este tipo.

### Ejemplo

Creamos una nueva clase "Person".

			using System.Runtime.Serialization;
			
			namespace Engagement.Agent
			{
			  [DataContract]
			  public class Person
			  {
			    public Person(string name, int age)
			    {
			      Age = age;
			      Name = name;
			    }
			
			    // Properties
			
			    [DataMember]
			    public int Age
			    {
			      get;
			      set;
			    }
			
			    [DataMember]
			    public string Name
			    {
			      get;
			      set; 
			    }
			  }
			}

A continuación, agregaremos una instancia de `Person` a un dato adicional.

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Si coloca otros tipos de objetos, asegúrese de que el método ToString() se implementa para devolver una cadena legible.

### Límites

#### simétricas

Cada clave del objeto debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Tamaño

Los datos adicionales están limitados a **1024** caracteres por llamada.

##Información de la aplicación de informes

### Referencia

			void SendAppInfo(Dictionary<object, object> appInfos)

Puede notificar manualmente la información de seguimiento (o cualquier otro tipo de información específica de la aplicación) mediante la función SendAppInfo().

Tenga en cuenta que esta información se puede enviar de forma incremental: para un dispositivo dado solo se conservará el último valor de una clave determinada. Al igual que los datos adicionales de evento, use un elemento Dictionary<object, object> para adjuntar información.

### Ejemplo

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			{
			   {"subscription", "2013-12-07"},
			   {"premium", "true"}
			};
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Límites

#### simétricas

Cada clave del objeto debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Tamaño

La información de la aplicación está limitada a **1024** caracteres por llamada.

En el ejemplo anterior, el JSON que se envía al servidor tiene una longitud de 44 caracteres:

			{"subscription":"2013-12-07","premium":"true"}
 

<!---HONumber=AcomDC_0302_2016-->