<properties 
	pageTitle="Puente de WebView en Android con el SDK de Android para Mobile Engagement nativo" 
	description="Describe cómo crear un puente entre un WebView que ejecuta Javascript y el SDK de Android para Mobile Engagement nativo."		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="piyushjo" />

#Puente de WebView en Android con el SDK de Android para Mobile Engagement nativo

> [AZURE.SELECTOR]
- [Puente de Android](mobile-engagement-bridge-webview-native-android.md)
- [Puente de iOS](mobile-engagement-bridge-webview-native-ios.md)

Algunas aplicaciones móviles están diseñadas como una aplicación híbrida en la que la propia aplicación se desarrolla mediante tecnología nativa de Android, pero algunas o incluso todas las pantallas se representan en el componente WebView en Android. Puede seguir utilizando el SDK de Android para Mobile Engagement en estas aplicaciones: en este tutorial se describe cómo hacerlo. El código de ejemplo siguiente se basa en la documentación de Android que puede encontrar [aquí](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Se describe cómo podría usarse este enfoque documentado a fin de implementar lo mismo para los métodos más utilizados para el SDK de Android para Mobile Engagement, como que Webview pueda también iniciar solicitudes de seguimiento de eventos, trabajos, errores e información de la aplicación desde una aplicación híbrida al tiempo que se canalizan a través de nuestro SDK de Android.

1. En primer lugar, debe asegurarse de que ha completado nuestro [tutorial de introducción](mobile-engagement-android-get-started.md) para integrar el SDK de Android para Mobile Engagement en la aplicación híbrida. Una vez hecho esto, su método `OnCreate` tendrá un aspecto similar al siguiente.  
    
		@Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	        engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
	        EngagementAgent.getInstance(this).init(engagementConfiguration);
	    }

2. Ahora, asegúrese de que la aplicación híbrida tiene una pantalla con el componente WebView. El código para ello será similar a la siguiente, donde vamos a cargar un archivo HTML local **Sample.html** en Webview en el método `onCreate` de la pantalla.

	    private void SetWebView() {
	        WebView myWebView = (WebView) findViewById(R.id.webview);
	        myWebView.loadUrl("file:///android_asset/Sample.html");
		}

		protected void onCreate(Bundle savedInstanceState) {
			...
			...
			SetWebView();
	    }

3. Ahora cree un archivo puente denominado **WebAppInterface**, que crea un contenedor sobre algunos métodos de SDK de Android para Mobile Engagement utilizados frecuentemente mediante el enfoque `@JavascriptInterface` descrito en la [documentación de Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

		import android.content.Context;
		import android.os.Bundle;
		import android.util.Log;
		import android.webkit.JavascriptInterface;
		
		import com.microsoft.azure.engagement.EngagementAgent;
		
		import org.json.JSONArray;
		import org.json.JSONObject;
		
		public class WebAppInterface {
		    Context mContext;
		
		    /** Instantiate the interface and set the context */
		    WebAppInterface(Context c) {
		        mContext = c;
		    }
		
		    @JavascriptInterface
		    public void sendEngagementEvent(String name, String extras ){
		        EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void startEngagementJob(String name, String extras ){
		        EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void endEngagementJob(String name){
		        EngagementAgent.getInstance(mContext).endJob(name);
		    }
		
		    @JavascriptInterface
		    public void sendEngagementError(String name, String extras ){
		        EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void sendEngagementAppInfo(String appInfo){
		        EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
		    }
		
		    public Bundle ParseExtras(String input) {
		        Bundle extras = new Bundle();
		
		        try {
		            JSONObject jObject = new JSONObject(input);
		            extras.putString(jObject.names().getString(0),
		                    jObject.get(jObject.names().getString(0)).toString());
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		        return extras;
		    }
		}  

4. Una vez creado el archivo puente anterior, es necesario asegurarse de que está asociado a nuestro componente Webview. Para ello, deberá modificar su método `SetWebview` para que quede como sigue:

	    private void SetWebView() {
	        WebView myWebView = (WebView) findViewById(R.id.webview);
	        myWebView.loadUrl("file:///android_asset/Sample.html");
	        WebSettings webSettings = myWebView.getSettings();
	        webSettings.setJavaScriptEnabled(true);
	        myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
	    }

5. En el fragmento de código anterior, se llamó a `addJavascriptInterface` para asociar nuestra clase de puente con nuestro componente Webview, y también se creó un identificador denominado **EngagementJs** para llamar a los métodos desde el archivo puente.

6. Ahora, cree el siguiente archivo denominado **Sample.html** en el proyecto en una carpeta denominada **assets** que se carga en Webview y donde se llamará a los métodos desde el archivo puente.

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
		              log('window.onerror: ' + err);
		            }
		
		            send = function(inputId)
		            {
		                var input = document.getElementById(inputId);
		                if(input)
		                {
		                    var value = input.value;
		                    // Example of how extras info can be passed with the Engagement logs
		                    var extras = '{"CustomerId":"MS290011"}';
		
		                    if(value && value.length > 0)
		                    {
		                        switch(inputId)
		                        {
		                            case "event":
		                            EngagementJs.sendEngagementEvent(value, extras);
		                            break;
		
		                            case "job":
		                            EngagementJs.startEngagementJob(value, extras);
		                            window.setTimeout( function()
		                            {
		                              EngagementJs.endEngagementJob(value);
		                            }, 10000 );
		                            break;
		
		                            case "error":
		                            EngagementJs.sendEngagementError(value, extras);
		                            break;
		
		                            case "appInfo":
		                            EngagementJs.sendEngagementAppInfo({"customer_name":value});
		                            break;
		                        }
		                    }
		                }
		            }
		        </script>
		    </head>
		    <body>
		        <h1>Bridge Tester</h1>
		        <div id='engagement'>
		            <h2>Event</h2>
		            <input type="text" id="event" size="35">
		            <button onclick="send('event')">Send</button>
		
		            <h2>Job</h2>
		            <input type="text" id="job" size="35">
		            <button onclick="send('job')">Send</button>
		
		            <h2>Error</h2>
		            <input type="text" id="error" size="35">
		            <button onclick="send('error')">Send</button>
		
		            <h2>AppInfo</h2>
		            <input type="text" id="appInfo" size="35">
		            <button onclick="send('appInfo')">Send</button>
		        </div>
		    </body>
		</html>

8. Tenga en cuenta los siguientes aspectos sobre el archivo HTML anterior:

	- 	Contiene un conjunto de cuadros de entrada donde puede proporcionar los datos que se utilizarán como nombres para los valores de Event, Job, Error, AppInfo. Al hacer clic en el botón situado junto a él, se realiza una llamada a Javascript, que finalmente llama a los métodos desde el archivo puente para pasar esta llamada al SDK de Android para Mobile Engagement. 
	- 	Estamos añadiendo algo de información adicional estática a los eventos, trabajos e incluso errores para demostrar cómo podría ser este proceso. Esta información adicional se envía como una cadena JSON que, si observa el archivo `WebAppInterface`, se analiza y se coloca en un dispositivo Android `Bundle` y se pasa junto con los eventos, los trabajos y los errores que se envían. 
	- 	Un trabajo de Mobile Engagement comienza con el nombre que especifique en el cuadro de entrada, se ejecuta durante 10 segundos y luego se apaga. 
	- 	Se pasa una información de aplicación o una etiqueta de Mobile Engagement con 'customer\_name' como clave estática y el valor que especificó en la entrada como valor para la etiqueta. 
 
9. Ejecute la aplicación y verá lo siguiente. Ahora, proporcione un nombre para un evento de prueba similar al siguiente y haga clic en el botón **Send** (Enviar) que se encuentra debajo.

	![][1]

10. Ahora, si va a la pestaña **Monitor** (Supervisar) de la aplicación y busca **Events -> Details** (Eventos -> Detalles), verá que este evento se muestra junto con la información de aplicación estática que se envía.

	![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png

<!---HONumber=AcomDC_0302_2016-->