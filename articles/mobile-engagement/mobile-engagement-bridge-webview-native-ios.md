<properties 
	pageTitle="Puente de WebView en iOS con el SDK de iOS para Mobile Engagement nativo" 
	description="Describe cómo crear un puente entre un componente WebView que ejecuta Javascript y el SDK de iOS para Mobile Engagement nativo"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="piyushjo" />

#Puente de WebView en iOS con el SDK de iOS para Mobile Engagement nativo

> [AZURE.SELECTOR]
- [Puente de Android](mobile-engagement-bridge-webview-native-android.md)
- [Puente de iOS](mobile-engagement-bridge-webview-native-ios.md)

Algunas aplicaciones móviles están diseñadas como una aplicación híbrida en la que la propia aplicación se desarrolla mediante tecnología Objective-C nativa de iOS, pero algunas o incluso todas las pantallas se representan en el componente WebView en iOS. Puede seguir utilizando el SDK de iOS para Mobile Engagement en estas aplicaciones: en este tutorial se describe cómo hacerlo.

Para ello, puede seguir dos planteamientos, aunque no se documentan en este artículo:

- El primero se describe en este [vínculo](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip), que requiere el registro de un `UIWebViewDelegate` en WebView y la realización de un cambio de ubicación y su posterior cancelación inmediata en Javascript. 
- El segundo se basa en esta [sesión WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), un planteamiento que es más limpio que el primero y que seguiremos en esta guía. Tenga en cuenta que este método solo funciona en iOS7 o una versión superior. 

Siga los pasos siguientes para el ejemplo de puente de iOS.

1. En primer lugar, debe asegurarse de que ha completado nuestro [tutorial de introducción](mobile-engagement-ios-get-started.md) para integrar el SDK de iOS para Mobile Engagement en la aplicación híbrida. Si lo desea, también puede habilitar el registro de pruebas del modo siguiente para ver los métodos SDK a medida que se desencadenen en WebView. 
    
		- (BOOL)application:(UIApplication ​*)application didFinishLaunchingWithOptions:(NSDictionary *​)launchOptions {
		   ....
     		[EngagementAgent setTestLogEnabled:YES];
		   ....
		}

2. Ahora, asegúrese de que la aplicación híbrida tiene una pantalla con el componente WebView. Puede agregarlo al `Main.storyboard` de la aplicación.

3. Asocie el WebView a su **ViewController** haciendo clic y arrastrando el WebView desde View Controller Scene hasta la pantalla de edición de `ViewController.h`, colocándolo justo debajo de la línea `@interface`.

4. Cuando lo haya hecho, aparecerá un cuadro de diálogo solicitando un nombre. Proporcione el nombre como **webView**. El archivo `ViewController.h` debería tener este aspecto:

		#import <UIKit/UIKit.h>
		#import "EngagementViewController.h"
		
		@interface ViewController : EngagementViewController
		@property (strong, nonatomic) IBOutlet UIWebView *webView;
		
		@end

5. El archivo `ViewController.m` se actualizará más adelante, pero antes se creará el archivo puente que crea un contenedor sobre algunos de los métodos del SDK de iOS para Mobile Engagement más utilizados. Cree un nuevo archivo de encabezado denominado **EngagementJsExports.h** que utilice el mecanismo `JSExport` descrito en la [sesión](https://developer.apple.com/videos/play/wwdc2013/615) anterior para exponer los métodos iOS nativos.

		#import <Foundation/Foundation.h>
		#import <JavaScriptCore/JavascriptCore.h>
		
		@protocol EngagementJsExports <JSExport>
		
		+ (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
		+ (void) startEngagementJob:(NSString*) name :(NSString*)extras;
		+ (void) endEngagementJob:(NSString*) name;
		+ (void) sendEngagementError:(NSString*) name :(NSString*)extras;
		+ (void) sendEngagementAppInfo:(NSString*) appInfo;
		
		@end

		@interface EngagementJs : NSObject <EngagementJsExports>

		@end

6. A continuación, crearemos la segunda parte del archivo puente. Cree un archivo llamado **EngagementJsExports.m** que contenga la implementación. Para ello, cree los contenedores reales llamando a los métodos del SDK de iOS para Mobile Engagement. Observe también que se está analizando el `extras` que se pasa desde Javascript en Webview y que se pone en un objeto `NSMutableDictionary` que se pasará con las llamadas al método del SDK para Engagement.

		#import <UIKit/UIKit.h>
		#import "EngagementAgent.h"
		#import "EngagementJsExports.h"
		
		@implementation EngagementJs
		
		+(void) sendEngagementEvent:(NSString​*)name :(NSString*​)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] sendEvent:name extras:extrasInput];
		}
		
		+ (void) startEngagementJob:(NSString*) name :(NSString*)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] startJob:name extras:extrasInput];
		}
		
		+ (void) endEngagementJob:(NSString*) name {
		   [[EngagementAgent shared] endJob:name];
		}
		
		+ (void) sendEngagementError:(NSString*) name :(NSString*)extras {
		   NSMutableDictionary* extrasInput = [self ParseExtras:extras];
		   [[EngagementAgent shared] sendError:name extras:extrasInput];
		}
		
		+ (void) sendEngagementAppInfo:(NSString*) appInfo {
		   NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
		   [[EngagementAgent shared] sendAppInfo:appInfoInput];
		}
		
		+ (NSMutableDictionary*) ParseExtras:(NSString*) input {
		   NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
		   NSError* error = nil;
		   NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
		   
		   return extras;
		}
		
		@end

5. Ahora, se regresa a **ViewController.m** y se actualiza con el código siguiente:
		
		#import <JavaScriptCore/JavaScriptCore.h>
		#import "ViewController.h"
		#import "EngagementJsExports.h"
		
		@interface ViewController ()
		
		@end
		
		@implementation ViewController
		
		- (void)viewDidLoad
		{
		   self.webView.delegate = self;
		   [super viewDidLoad];
		   [self loadWebView];
		}
		
		- (void)loadWebView {
		   NSBundle* mainBundle = [NSBundle mainBundle];
		   NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
		   
		   NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
		   [self.webView loadRequest:urlReq];
		}
		
		- (void)webViewDidFinishLoad:(UIWebView*)wv
		{
		   JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
		   
		   context[@"EngagementJs"] = [EngagementJs class];
		}
		
		- (void)webView:(UIWebView​*)wv didFailLoadWithError:(NSError*​)error
		{
		   NSLog(@"Error for WEBVIEW: %@", [error description]);
		}
		
		- (void)didReceiveMemoryWarning {
		   [super didReceiveMemoryWarning];
		   // Dispose of any resources that can be recreated.
		}
		
		@end

6. Tenga en cuenta los siguientes aspectos sobre el archivo **ViewController.m**:

	- En el método `loadWebView`, se está cargando un archivo HTML local denominado **LocalPage.html** cuyo código se examinará a continuación. 
	- En el método `webViewDidFinishLoad`, se está tomando `JsContext` y se está asociando nuestra clase de contenedor a este valor. Esto posibilitará la llamada a nuestros métodos de SDK de contenedor mediante el identificador **EngagementJs** desde WebView. 

7. Cree un archivo llamado **LocalPage.html** con el código siguiente:

		<!doctype html>
		<html>
		   <head>
		       <style type='text/css'>
		           html { font-family:Helvetica; color:#222; }
		           h1 { color:steelblue; font-size:22px; margin-top:16px; }
		           h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
		       </style>
		       
		       <script type="text/javascript">
		       
		       window.onerror = function(err)
		       {
		           alert('window.onerror: ' + err);
		       }
		       
		       function send(inputId)
		       {
		           var input = document.getElementById(inputId);
		           if(input)
		           {
		               var value = input.value;
		               // Example of how extras info can be passed with the Engagement logs
		               var extras = '{"CustomerId":"MS290011"}';
		           }
		           
		           if(value && value.length > 0)
		           {
		               switch(inputId)
		               {
		                   case "event":
		                   EngagementJs.sendEngagementEvent(value, extras);
		                   break;
		                   
		                   case "job":
		                   EngagementJs.startEngagementJob(value, extras);
		                   window.setTimeout(
		                   function(){
		                       EngagementJs.endEngagementJob(value);
		                   }, 10000 );
		                   break;
		
		                   case "error":
		                   EngagementJs.sendEngagementError(value, extras);
		                   break;
		                   
		                   case "appInfo":
		                   var appInfo = '{"customer_name":"' + value + '"}';
		                   EngagementJs.sendEngagementAppInfo(appInfo);
		                   break;
		               }
		           }
		       }
		       </script>
		       
		   </head>
		   <body>
		       <h1>Bridge Tester</h1>
		       
		       <div id='engagement'>
		           
		           <br/>
		           <h2>Event</h2>
		           <input type="text" id="event" size="35">
		           <button onclick="send('event')">Send</button>
		           
		           <br/>
		           <h2>Job</h2>
		           <input type="text" id="job" size="35">
		           <button onclick="send('job')">Send</button>
		           
		           <br/>
		           <h2>Error</h2>
		           <input type="text" id="error" size="35">
		           <button onclick="send('error')">Send</button
		           
		           <br/>
		           <h2>AppInfo</h2>
		           <input type="text" id="appInfo" size="35">
		           <button onclick="send('appInfo')">Send</button>
		       
		       </div>
		   </body>
		</html>

8. Tenga en cuenta los siguientes aspectos sobre el archivo HTML anterior:

	- 	Contiene un conjunto de cuadros de entrada donde puede proporcionar los datos que se utilizarán como nombres para los valores de Event, Job, Error, AppInfo. Al hacer clic en el botón situado junto a él, se realiza una llamada a Javascript, que finalmente llama a los métodos desde el archivo puente para pasar esta llamada al SDK de iOS para Mobile Engagement. 
	- 	Estamos añadiendo algo de información adicional estática a los eventos, trabajos e incluso errores para demostrar cómo podría ser este proceso. Esta información adicional se envía como una cadena JSON que, si observa el archivo `EngagementJsExports.m`, se analiza y se pasa junto con los eventos, las tareas y los errores que se envían. 
	- 	Un trabajo de Mobile Engagement comienza con el nombre que especifique en el cuadro de entrada, se ejecuta durante 10 segundos y luego se apaga. 
	- 	Se pasa una información de aplicación o una etiqueta de Mobile Engagement con 'customer\_name' como clave estática y el valor que especificó en la entrada como valor para la etiqueta. 
 
9. Ejecute la aplicación y verá lo siguiente. Ahora, proporcione un nombre para un evento de prueba similar al siguiente y haga clic en el botón **Send** (Enviar) que está al lado.

	![][1]

10. Ahora, si va a la pestaña **Monitor** (Supervisar) de la aplicación y busca **Events -> Details** (Eventos -> Detalles), verá que este evento se muestra junto con la información de aplicación estática que se envía.

	![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png

<!---HONumber=AcomDC_0302_2016-->