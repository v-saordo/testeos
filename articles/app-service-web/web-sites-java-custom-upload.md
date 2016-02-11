<properties 
	pageTitle="Carga de una aplicación web de Java personalizada en Azure" 
	description="En este tutorial se muestra cómo cargar una aplicación web de Java personalizada en Aplicaciones web del Servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# Carga de una aplicación web de Java personalizada en Azure

En este tema se explica cómo cargar una aplicación web de Java personalizada en Aplicaciones web del [Servicio de aplicaciones de Azure]. Se incluye información que se aplica a cualquier aplicación web o sitio web de Java, así como algunos ejemplos de aplicaciones específicas.

Tenga en cuenta que Azure permite crear aplicaciones web de Java con la interfaz de usuario de configuración del Portal de Azure y Azure Marketplace, tal y como se documenta en [Creación de una aplicación web de Java en Servicio de aplicaciones de Azure](web-sites-java-get-started.md). Este tutorial se destina a escenarios en los que no desea usar la interfaz de usuario de configuración del Portal de Azure ni Azure Marketplace.

## Directrices de configuración

A continuación se describe la configuración esperada para las aplicaciones web de Java personalizadas en Azure.

- El puerto HTTP que utiliza el proceso de Java se asigna de manera dinámica. El proceso debe utilizar el puerto de la variable de entorno `HTTP_PLATFORM_PORT`.
- Se deben deshabilitar todos los puertos de escucha aparte del agente de escucha simple HTTP. En Tomcat, ello incluye los puertos de apagado, HTTPS y AJP.
- El contenedor debe estar configurado solo para el tráfico IPv4.
- En la configuración se debe establecer el comando **startup** para la aplicación.
- Las aplicaciones que requieren directorios con permiso de escritura tienen que estar ubicadas en el directorio de contenido de la aplicación web de Azure, que es **D:\\home**. La variable de entorno `HOME` hace referencia a D:\\home.  

Puede establecer las variables de entorno como se requiere en el archivo web.config.

## Configuración de web.config httpPlatform

La siguiente información describe el formato **httpPlatform** dentro de web.config.
                                 
**arguments** (valor predeterminado=""). Argumentos para el ejecutable o script especificado en la configuración de **processPath**.

Ejemplos (que se muestran con **processPath** incluido):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"


**processPath**: ruta de acceso al ejecutable o script que iniciará un proceso de escucha de las solicitudes HTTP.

Ejemplos:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (valor predeterminado=10). Número de veces que se permite que el proceso especificado en **processPath** se bloquee por minuto. Si se supera este límite, **HttpPlatformHandler** detendrá el inicio del proceso durante el resto del minuto.
                                    
**requestTimeout** (valor predeterminado="00:02:00"). Tiempo durante el cual **HttpPlatformHandler** esperará una respuesta del proceso de escucha en `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (valor predeterminado=10). Número de veces que **HttpPlatformHandler** intentará iniciar el proceso especificado en **processPath**. Consulte **startupTimeLimit** para obtener más detalles.

**startupTimeLimit** (valor predeterminado=10 segundos). Tiempo durante el cual **HttpPlatformHandler** esperará a que el ejecutable/script inicie un proceso que escucha en el puerto. Si se supera este límite de tiempo, **HttpPlatformHandler** cerrará el proceso e intentará volver a iniciarlo **startupRetryCount** veces.
                                                                                      
**stdoutLogEnabled** (valor predeterminado="true"). Si el valor es true, **stdout** y **stderr** para el proceso especificado en la configuración **processPath** serán redirigidos al archivo especificado en **stdoutLogFile** (consulte la sección **stdoutLogFile**).
                                    
**stdoutLogFile** (valor predeterminado="d:\\home\\LogFiles\\httpPlatformStdout.log"). Ruta de acceso absoluta del archivo por el cual se registrarán **stdout** y **stderr** desde el proceso especificado en **processPath**.
                                    
> [AZURE.NOTE]`%HTTP_PLATFORM_PORT%` es un marcador de posición especial que se debe especificar como parte de los **argumentos** o como parte de la lista **environmentVariables** de **httpPlatform**. Esto se reemplazará por un puerto generado internamente mediante **HttpPlatformHandler** de modo que el proceso que se especifica en **processPath** pueda escuchar en este puerto.

## Implementación

Las aplicaciones web basadas ​​en Java se pueden implementar fácilmente a través de la mayoría de los mismos medios que se utilizan con las aplicaciones web basadas en Internet Information Services (IIS). FTP, Git y Kudu son todas compatibles como mecanismos de implementación, así como la funcionalidad de SCM integrada para las aplicaciones web. WebDeploy funciona como un protocolo; sin embargo, debido a que Java no está desarrollado en Visual Studio, WebDeploy no se adapta a los casos de uso de la implementación de una aplicación web de Java.

## Ejemplos de configuración de aplicaciones

Para las siguientes aplicaciones, se proporciona un archivo web.config y la configuración de la aplicación como ejemplos para mostrar cómo habilitar la aplicación Java en Aplicaciones web del Servicio de aplicaciones.

### Tomcat
Si bien hay dos variaciones en Tomcat que se suministran con Aplicaciones web del Servicio de aplicaciones, todavía es posible cargar las instancias específicas de los clientes. A continuación se muestra un ejemplo de una instalación de Tomcat con una máquina virtual Java (JVM) diferente.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Por el lado de Tomcat, es necesario realizar algunos cambios en la configuración. Se debe editar server.xml para establecer:

-	Puerto de apagado = -1
-	Puerto del conector HTTP = {port.http}
-	Dirección del conector HTTP = "127.0.0.1"
-	Convierta en comentarios los conectores HTTPS y AJP
-	La configuración de IPv4 también se puede configurar en el archivo catalina.properties donde se puede agregar `java.net.preferIPv4Stack=true`
    
Las llamadas Direct3D no se admiten en Aplicaciones web del Servicio de aplicaciones. Para deshabilitarlas, agregue la siguiente opción de Java si la aplicación realiza llamadas de ese tipo: `-Dsun.java2d.d3d=false`

### Jetty

Como es el caso de Tomcat, los clientes pueden cargar sus propias instancias de Jetty. En el caso de ejecutar la instalación completa de Jetty, la configuración sería similar a la siguiente:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

La configuración de Jetty necesita cambiarse en el archivo start.ini para establecer `java.net.preferIPv4Stack=true`.

### Springboot
Para obtener una aplicación Springboot en ejecución necesita cargar el archivo JAR o WAR y agregar el siguiente archivo web.config. El archivo web.config se coloca en la carpeta wwwroot. En el archivo web.config, ajuste los argumentos para apuntar al archivo JAR. En el ejemplo siguiente el archivo JAR también se encuentra en la carpeta wwwroot.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
	        arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar ";%HOME%\site\wwwroot\my-web-project.jar";">
	    </httpPlatform>
	  </system.webServer>
	</configuration>


### Hudson

Nuestra prueba utilizó war de Hudson 3.1.2 y la instancia predeterminada de Tomcat 7.0.50, pero sin utilizar la interfaz de usuario para realizar la configuración. Debido a que Hudson es una herramienta de compilación de software, se recomienda instalarla en instancias dedicadas donde la marca **AlwaysOn** se pueda configurar en la aplicación web.

1. En el directorio raíz de la aplicación web, es decir, **d:\\home\\site\\wwwroot**, cree un directorio **webapps** (si todavía no existe) y coloque Hudson.war en **d:\\home\\site\\wwwroot\\webapps**.
2. Descargue apache maven 3.0.5 (compatible con Hudson) y colóquelo en **d:\\home\\site\\wwwroot**.
3. Cree web.config en **d:\\home\\site\\wwwroot** y pegue el siguiente contenido en él:
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    En este punto, se puede reiniciar la aplicación web para que los cambios surtan efecto. Conéctese con http://yourwebapp/hudson para iniciar Hudson.

4. Cuando Hudson se haya configurado, debería ver la siguiente pantalla:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Obtenga acceso a la página de configuración de Hudson: haga clic en **Manage Hudson** (Administrar Hudson) y, continuación, en **Configure System** (Configurar sistema).
6. Configure el JDK como se muestra a continuación:

	![Configuración de Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Configure Maven como se muestra a continuación:

	![Configuración de Maven](./media/web-sites-java-custom-upload/maven.png)

8. Guarde la configuración Hudson debe estar ahora configurado y listo para su uso.

Para obtener información adicional sobre Hudson, consulte [http://hudson-ci.org](http://hudson-ci.org).

### Liferay

Liferay es compatible con Aplicaciones web del Servicio de aplicaciones. Debido a que Liferay puede requerir una importante cantidad de memoria, la aplicación web necesita ejecutarse en un trabajo dedicado mediano o grande, que puede proporcionar suficiente memoria. Liferay también tarda varios minutos en iniciarse. Por esa razón, se recomienda que configure el sitio en **Always On**.

Con Liferay 6.1.2 Community Edition GA3 incluido con Tomcat, se editaron los siguientes archivos después de descargar Liferay:

**Server.xml**

- Cambie el puerto de apagado a -1.
- Cambie el conector HTTP a `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Comente el conector AJP.

En la carpeta **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes**, cree un archivo llamado **portal-ext.properties**. Este archivo debe contener una línea, como se muestra aquí:

    liferay.home=%HOME%/site/wwwroot/liferay

En el mismo nivel de directorio que la carpeta tomcat-7.0.40, cree un archivo llamado **web.config** con el contenido siguiente:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

En el bloque **httpPlatform**, **requestTimeout** se establece en set to "00:10:00". Se puede reducir, pero entonces es probable se vean algunos errores de tiempo de espera mientras Liferay arranca. Si se cambia este valor, se debe modificar también **connectionTimeout** en el archivo server.xml de tomcat.

Vale la pena señalar que la variable de entorno JRE\_HOME se especifica en web.config anterior para apuntar a la JDK de 64 bits. El valor predeterminado es 32 bits, pero debido a que Liferay puede requerir altos niveles de memoria, se recomienda utilizar el JDK de 64 bits.

Después de que realice estos cambios, reinicie la aplicación web que ejecuta Liferay y, a continuación, abra http://yourwebapp. El portal de Liferay está disponible en la raíz de la aplicación web.

## Pasos siguientes

Para obtener más información sobre Liferay, consulte [http://www.liferay.com](http://www.liferay.com).

Para obtener más información sobre Java, consulte el [Centro para desarrolladores de Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[Servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0114_2016-->