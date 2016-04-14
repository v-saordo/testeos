<properties 
	pageTitle="Realización de una llamada telefónica desde Twilio (Java) | Microsoft Azure" 
	description="Obtenga información acerca de cómo realizar una llamada de teléfono desde una página web mediante Twilio en una aplicación Java en Azure." 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Realización de una llamada telefónica con Twilio en una aplicación Java en Azure 

El siguiente ejemplo muestra cómo se puede usar Twilio para realizar una llamada desde una página web hospedada en Azure. La aplicación resultante le pedirá al usuario los valores de una llamada telefónica, como se muestra en la siguiente captura de pantalla.

![Formulario de llamada de Azure con Twilio y Java][twilio_java]

Tendrá que hacer lo siguiente para utilizar el código de este tema:

1. Adquiera una cuenta de Twilio y un token de autenticación. Para empezar con Twilio, evalúe los precios en [http://www.twilio.com/pricing][twilio_pricing]. Puede registrarse en [https://www.twilio.com/try-twilio][try_twilio]. Para obtener información acerca de la API proporcionada por Twilio, consulte [http://www.twilio.com/api][twilio_api].
2. Obtenga el JAR de Twilio. En [https://github.com/twilio/twilio-java][twilio_java_github] puede descargar el código fuente de GitHub y crear su propio archivo JAR, o bien puede descargar un archivo JAR previamente generado (con o sin dependencias). El código de este tema se escribió usando JAR TwilioJava 3.3.8 con dependencias previamente compilado.
3. Agregue el JAR a su ruta de acceso de compilación Java.
4. Si está usando Eclipse para crear esta aplicación Java, incluya el JAR de Twilio en su archivo de implementación de la aplicación (WAR) utilizando la característica de ensamblado de implementación de Eclipse. Si no va a usar Eclipse para crear esta aplicación Java, asegúrese de que el JAR de Twilio se incluya dentro del mismo rol de Azure que la aplicación Java y se agregue a la ruta de clase de la aplicación.
5. Asegúrese de que el keystore cacerts contenga el certificado de la entidad de certificación Equifax Secure con la huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (el número de serie es 35:DE:F4:CF y la huella digital SHA1 es D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Este es el certificado de la entidad de certificación (CA) para el servicio [https://api.twilio.com][twilio_api_service], al que se llama cuando se utilizan las API de Twilio. Para obtener información acerca de cómo agregar este certificado CA a su almacén de certificados CA de JDK, consulte [Incorporación de un certificado al almacén de certificados CA de Java][add_ca_cert].

Además, se recomienda estar familiarizado con la información que encontrará en [Creación de una aplicación Hello World con el kit de herramientas de Azure para Eclipse][azure_java_eclipse_hello_world] o con otras técnicas para hospedar aplicaciones Java en Azure si no usa Eclipse.

## Creación de un formulario web para hacer una llamada

El siguiente código muestra cómo crear un formulario web para recuperar datos de usuario para hacer una llamada. Para este ejemplo, se creó un nuevo proyecto web dinámico, llamado **TwilioCloud**, y se agregó **callform.jsp** como archivo JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## código para realizar la llamada
El siguiente código, que se llama cuando el usuario completa el formulario mostrado por callform.jsp, crea el mensaje de llamada y genera la llamada. Para los fines de este ejemplo, el archivo JSP se llama **makecall.jsp** y se agregó al proyecto **TwilioCloud**. (Use la cuenta de Twilio y el token de autenticación en lugar de los valores de marcador de posición asignados a **accountSID** y **authToken** en el código que aparece a continuación).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Además de hacer la llamada, makecall.jsp muestra el extremo de Twilio, la versión de API y el estado de la llamada. Un ejemplo es la siguiente captura de pantalla:

![Respuesta de llamada de Azure con Twilio y Java][twilio_java_response]

## Ejecución de la aplicación
A continuación le indicamos los pasos de alto nivel que deberá dar para ejecutar su aplicación; los detalles de estos pasos se pueden encontrar en [Creación de una aplicación Hello World con el kit de herramientas de Azure para Eclipse][azure_java_eclipse_hello_world].

1. Exporte su TwilioCloud WAR a la carpeta de Azure **approot**. 
2. Modifique **startup.cm**d para descomprimir el TwilioCloud WAR.
3. Compile la aplicación para el emulador de proceso.
4. Comience su implementación en el emulador de proceso.
5. Abra un explorador y ejecute ****http://localhost:8080/TwilioCloud/callform.jsp**.
6. Escriba valores en el formulario, haga clic en **Make this call** y, a continuación, vea los resultados en makecall.jsp.

Cuando esté listo para realizar implementaciones en Azure, vuelva a compilar para realizar la implementación en la nube, implemente en Azure y ejecute http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp en el explorador (reemplace *your\_hosted\_name* por su valor).

## Pasos siguientes
Este código se ha proporcionado para mostrar la funcionalidad básica usando Twilio en Java en Azure. Antes de implementarlo en Azure en producción, es posible que desee agregar más controles de errores u otras características. Por ejemplo:

* En lugar de usar un formulario web, puede usar blobs de almacenamiento de Azure o una base de datos SQL para almacenar los números de teléfono y el texto de llamada. Para obtener más información acerca de cómo usar los blobs de Almacenamiento de Azure en Java, consulte [Uso del servicio de almacenamiento de blobs desde Java][howto_blob_storage_java]. Para obtener más información acerca de cómo usar Base de datos SQL en Java, consulte [Uso de Base de datos SQL en Java][howto_sql_azure_java].
* Puede usar **RoleEnvironment.getConfigurationSettings** para recuperar el identificador de la cuenta de Twilio y el token de autenticación desde los ajustes de configuración de su implementación, en vez de codificar de forma rígida los valores en el makecall.jsp. Para obtener información sobre la clase **RoleEnvironment**, consulte [Uso de la biblioteca en tiempo de ejecución del servicio de Azure en JSP][azure_runtime_jsp] y la documentación del paquete de tiempo de ejecución de servicio de Azure en [http://dl.windowsazure.com/javadoc][azure_javadoc].
* El código makecall.jsp asigna una URL proporcionada por Twilio, [http://twimlets.com/message][twimlet_message_url], a la variable **Url**. Esta URL proporciona una respuesta del Lenguaje de marcado de Twilio (TwiML) que informa a Twilio acerca de cómo proceder con la llamada. Por ejemplo, la TwiML que se devuelve puede contener un verbo **&lt;Say&gt;**; esto resultará en un texto de viva voz que el destinatario de la llamada podrá escuchar. En lugar de usar la dirección URL proporcionada por Twilio, podría construir su propio servicio para responder a la solicitud de Twilio; para obtener más información, consulte [Uso de Twilio para capacidades de voz y SMS en Java][howto_twilio_voice_sms_java]. Puede encontrar más información sobre TwiML en [http://www.twilio.com/docs/api/twiml][twiml], y más información sobre **&lt;Say&gt;** y otros verbos de Twilio en [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lea las directrices de seguridad de Twilio en [https://www.twilio.com/docs/security][twilio_docs_security].

Para obtener información adicional acerca de Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## Otras referencias
* [Uso de Twilio para capacidades de voz y SMS en Java][howto_twilio_voice_sms_java]
* [Incorporación de un certificado al almacén de certificados CA de Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg

<!---HONumber=Oct15_HO3-->