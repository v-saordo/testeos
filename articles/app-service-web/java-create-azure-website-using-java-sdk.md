<properties 
	pageTitle="Creación de una aplicación web en el Servicio de aplicaciones de Azure mediante el SDK de Azure para Java" 
	description="Obtenga información acerca de cómo crear una aplicación web en el Servicio de aplicaciones de Azure mediante programación usando el SDK de Azure para Java." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter="Java" 
	authors="donntrenton" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/12/2015" 
	ms.author="v-donntr"/>


# Creación de una aplicación web en el Servicio de aplicaciones de Azure mediante el SDK de Azure para Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## Información general

Este tutorial muestra cómo crear un SDK de Azure para la aplicación Java que crea una aplicación web en el [servicio de aplicaciones de Azure][]. A continuación, se explica cómo implementar una aplicación en ella. Consta de dos partes:

- En la parte 1, se describe cómo crear una aplicación Java que crea una aplicación web.
- En la parte 2, se explica cómo crear una aplicación JSP sencilla ("Hello World") y, a continuación, se describe el uso de un cliente FTP para implementar código en un servicio de aplicaciones.


## Requisitos previos

### Instalaciones de software

El código de aplicación de AzureWebDemo de este artículo se escribió con el SDK 0.7.0 de Azure Java, que se instala mediante el [instalador de plataforma web (WebPI)][]. Además, asegúrese de utilizar la versión más reciente del [kit de herramientas de Azure para Eclipse][]. Después de instalar el SDK, actualice las dependencias del proyecto de Eclipse ejecutando **Update index** (Actualizar índice) en **Maven Repositories** (Repositorios de Maven). A continuación, vuelva a agregar la versión más reciente de cada paquete en la ventana **Dependencies** (Dependencias). Para comprobar la versión del software instalado en Eclipse, haga clic en **Help (Ayuda) > Installation Details (Detalles de la instalación)**. Debe tener al menos las siguientes versiones:

- Paquete para bibliotecas de Microsoft Azure para Java 0.7.0.20150309
- IDE de Eclipse para desarrolladores de Java EE 4.4.2.20150219


### Creación y configuración de recursos en la nube en Azure

Antes de comenzar este procedimiento, debe tener una suscripción activa de Azure y configurar un Active Directory (AD) predeterminado en Azure.


### Creación de un Active Directory (AD) en Azure

Si no tiene todavía un Active Directory (AD) en su suscripción de Azure, inicie sesión en el [portal clásico de Azure][] con su cuenta de Microsoft. Si tiene varias suscripciones, haga clic en **Suscripciones** y elija el directorio predeterminado de la suscripción que desee usar en este proyecto. A continuación, haga clic en **Aplicar** para acceder a esa vista de suscripción.

1. Elija **Active Directory** en el menú de la izquierda. **Haga clic en Nuevo > Directorio > Creación personalizada**.

2. En **Agregar directorio**, elija **Crear nuevo directorio**.

3. En **Nombre**, escriba un nombre para el directorio.

4. En **Dominio**, escriba un nombre de dominio. Se trata de un nombre de dominio básico que se incluye de forma predeterminada en el directorio. Tiene el formato `<domain_name>.onmicrosoft.com`. Puede asignarle un nombre basado en el nombre del directorio o en otro nombre de dominio que posea. Más adelante, puede agregar otro nombre de dominio que ya use su organización.

5. En **País o región**, elija la configuración regional que corresponda.

Para obtener más información sobre AD, consulte [¿Qué es un directorio de Azure AD?][]


### Creación de un certificado de administración para Azure

El SDK de Azure para Java usa certificados de administración para autenticar con las suscripciones de Azure. Se trata de certificados X.509 v3 que se utilizan para autenticar una aplicación cliente que utiliza la API de administración de servicios para actuar en nombre del propietario de la suscripción a fin de administrar los recursos de suscripción.

El código de este procedimiento utiliza un certificado autofirmado para autenticarse con Azure. Para este procedimiento, deberá crear un certificado y cargarlo previamente en el [portal clásico de Azure][]. Esto implica los pasos siguientes:

- Generar un archivo PFX que represente el certificado de cliente y guardarlo localmente.
- Generar un certificado de administración (archivo CER) a partir del archivo PFX.
- Cargar el archivo CER en su suscripción de Azure.
- Convertir el archivo PFX en JKS, ya que Java utiliza ese formato para la autenticación mediante certificados.
- Escribir el código de autenticación de la aplicación, que hace referencia al archivo JKS local.

Al completar este procedimiento, el certificado CER residirá en su suscripción de Azure y el certificado JKS, en la unidad local. Para obtener más información sobre la administración de certificados, consulte [Crear y cargar un certificado de administración para Azure][].


#### Creación de un certificado

Para crear su propio certificado autofirmado, abra una consola de comandos en el sistema operativo y ejecute los siguientes comandos.

> **Nota:** el equipo en el que se ejecute este comando debe tener instalado el JDK. Además, la ruta a la herramienta de generación de claves (keytool) depende de la ubicación en la que se instale el JDK. Para obtener más información, consulte [Keytool: herramienta para administrar certificados y claves][] en la documentación en línea de Java.

Para crear el archivo .pfx:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Para crear el archivo .cer:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

donde:

- `<java-install-dir>` es la ruta al directorio donde se instaló Java.
- `<keystore-id>` es el identificador de entrada del almacén de claves (por ejemplo, `AzureRemoteAccess`).
- `<cert-store-dir>` es la ruta al directorio en el que desea almacenar los certificados (por ejemplo, `C:/Certificates`).
- `<cert-file-name>` es el nombre del archivo de certificado (por ejemplo, `AzureWebDemoCert`).
- `<password>` es la contraseña elegida para proteger el certificado. Debe tener 6 caracteres como mínimo. Se puede optar por no escribir ninguna contraseña, aunque no se recomienda.
- `<dname>` es el nombre distintivo X.500 que debe asociarse con el alias. Se utiliza en los campos del asunto y del emisor en el certificado autofirmado.

Para obtener más información, consulte [Crear y cargar un certificado de administración para Azure][].


#### Carga del certificado

Para cargar un certificado autofirmado en Azure, vaya a la página **Configuración** en el portal de clásico. A continuación, haga clic en la pestaña **Certificados de administración**. Haga clic en **Cargar**, en la parte inferior de la página, y desplácese hasta la ubicación del archivo CER que creó previamente.


#### Conversión del archivo PFX en JKS

Como administrador, acceda al símbolo del sistema de Windows. Cambie al directorio que contiene los certificados y ejecute el comando siguiente, donde `<java-install-dir>` es el directorio donde instaló Java en su equipo:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Cuando se le pida, escriba la contraseña del almacén de claves de destino. Se trata de la contraseña para el archivo JKS.

2. Cuando se le pida, escriba la contraseña del almacén de claves de origen. Esta es la contraseña que especificó para el archivo PFX.

Las dos contraseñas no deben ser iguales. Se puede optar por no escribir ninguna contraseña, aunque no se recomienda.


## Compilación de una aplicación para crear aplicaciones web

### Creación del área de trabajo de Eclipse y el proyecto de Maven

En esta sección, creará un área de trabajo y un proyecto de Maven para la aplicación de creación de aplicaciones web, denominada AzureWebDemo.

1. Cree un nuevo proyecto de Maven. Haga clic en **File (Archivo) > New (Nuevo) > Maven Project (Proyecto de Maven)**. En **New Maven Project** (Nuevo proyecto de Maven), elija **Create a simple project** (Crear proyecto sencillo) y, a continuación, elija **Use default workspace location** (Usar ubicación de espacio de trabajo predeterminada).

2. En la segunda página de **New Maven Project** (Nuevo proyecto de Maven), especifique lo siguiente:

    - Group ID (Identificador del grupo): `com.<username>.azure.webdemo`
    - Artifact ID (Identificador de artefacto): AzureWebDemo
    - Version (Versión): 0.0.1-SNAPSHOT
    - Packaging (Empaquetado): jar
    - Name (Nombre): AzureWebDemo

    Haga clic en **Finalizar**

3. Abra el nuevo archivo pom.xml del proyecto en el Explorador de proyectos. Elija la pestaña **Dependencies** (Dependencias). Como se trata de un nuevo proyecto, todavía no se muestra ningún paquete.

4. Abra la vista Maven Repositories (Repositorios de Maven). **Haga clic en Window (Ventana) > Show View (Mostrar vista) > Other (Otro) > Maven > Maven Repositories (Repositorios de Maven)** y haga clic en **OK** (Aceptar). La vista **Maven Repositories** (Repositorios de Maven) aparece en la parte inferior de IDE.

5. Abra **Global Repositories** (Repositorios globales), haga clic con el botón derecho en el repositorio **Central** y elija **Rebuild Index** (Volver a generar el índice).

    ![][1]
    
    Este paso puede tardar varios minutos, según la velocidad de la conexión. Cuando se vuelve a generar el índice, deberían mostrarse los paquetes de Microsoft Azure en el repositorio **central** de Maven.

6. En **Dependencies** (Dependencias), haga clic en **Add** (Agregar). En **Enter Group ID...** (Especificar identificador de grupo), escriba `azure-management`. Elija los paquetes para la administración de base y la administración de aplicaciones web del Servicio de aplicaciones:

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Nota:** si va a actualizar las dependencias después de que se haya lanzado una nueva versión, tendrá que volver a agregar cada una de las dependencias en esta lista. Después de hacer clic en **Add** (Agregar) y elegir cada dependencia, esta aparece con el nuevo número de versión en la lista **Dependencies** (Dependencias).

Haga clic en **Aceptar**. Los paquetes de Azure aparecen entonces en la lista **Dependencies** (Dependencias).


### Escritura de código Java para crear una aplicación web mediante una llamada al SDK de Azure

A continuación, escriba el código que efectúa la llamada a las API en el SDK de Azure para que Java cree la aplicación web de Servicio de aplicaciones.

1. Cree una clase de Java que contenga el código de punto de entrada principal. En el Explorador de soluciones, haga clic con el botón derecho en el nodo del proyecto y elija **Nueva > Clase**.

2. En **Nueva clase de Java**, asigne un nombre a la clase `WebCreator` y active la casilla de verificación **public static void main**. Las selecciones deberían aparecer de la siguiente forma:

    ![][2]

3. Haga clic en **Finalizar** El archivo WebCreator.java aparece en el Explorador de proyectos.


### Llamada a la API de Azure para crear una aplicación web de Servicio de la aplicaciones


#### Adición de las importaciones necesarias

En WebCreator.java, agregue las siguientes importaciones. Estas importaciones proporcionan acceso a las clases en las bibliotecas de administración para utilizar las API de Azure:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### Definición de la clase de punto de entrada principal

Dado que el propósito de la aplicación AzureWebDemo es crear una aplicación web de Servicio de aplicaciones, asigne un nombre a la clase principal para esta aplicación `WebAppCreator`. Esta clase proporciona el código de punto de entrada principal que efectúa la llamada a la API de administración de Servicios de Azure para crear la aplicación web.

Agregue las siguientes definiciones de parámetros para la aplicación web y el espacio web. Deberá proporcionar su propia información de certificado y de identificador de suscripción de Azure.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

donde:

- `<subscription-id>` es el identificador de suscripción de Azure en el que desea crear el recurso.
- `<certificate-store-path>` es la ruta y el nombre de archivo del archivo JKS en el directorio del almacén de certificados local. Por ejemplo, `C:/Certificates/CertificateName.jks` para Linux y `C:\Certificates\CertificateName.jks` para Windows.
- `<certificate-password>` es la contraseña que se especificó al crear el certificado de JKS.
- `webAppName` puede ser cualquier nombre que elija. En este procedimiento, se utiliza el nombre `WebDemoWebApp`. El nombre de dominio completo es la `webAppName` con el `domainName` anexado, por lo que, en este caso, el dominio completo es `webdemowebapp.azurewebsites.net`.
- `domainName` debe especificarse del modo indicado anteriormente.
- `webSpaceName` debe ser uno de los valores definidos en la clase [WebSpaceNames][].
- `appServicePlanName` debe especificarse del modo indicado anteriormente.

> **Nota:** cada vez que ejecute esta aplicación, deberá cambiar el valor de `webAppName` y `appServicePlanName` (o eliminar la aplicación web en el Portal de Azure) antes de ejecutar la aplicación de nuevo. De lo contrario, se producirá un error de ejecución porque ya existe el mismo recurso en Azure.


#### Definición del método de creación web

A continuación, defina un método para crear la aplicación web. Este método, `createWebApp`, especifica los parámetros de la aplicación web y el espacio web. También crea y configura el cliente de administración de aplicaciones web del Servicio de aplicaciones, que se define mediante el objeto [WebSiteManagementClient][]. El cliente de administración es fundamental para crear aplicaciones web. Proporciona servicios web RESTful que permiten a las aplicaciones administrar aplicaciones web (realizar operaciones tales como crear, actualizar y eliminar) mediante una llamada a la API de administración de servicios.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

El código dará como resultado el estado de HTTP de la respuesta indicando si es correcto o erróneo. Si es correcto, se generará el nombre de la aplicación web creada.


#### Definición del método main()

Especifique el código del método main() que efectúa la llamada a createWebApp() para crear la aplicación web.

Por último, efectúe una llamada a `createWebApp` desde `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### Ejecución de la aplicación y verificación de la creación de aplicaciones web

Para verificar que la aplicación puede ejecutarse, haga clic en **Ejecutar > Ejecutar**. Cuando la aplicación complete la ejecución, verá el siguiente resultado en la consola de Eclipse:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Inicie sesión en el portal clásico de Azure y haga clic en **Aplicaciones web**. La nueva aplicación web debería aparecer en la lista Aplicaciones web dentro de unos minutos.


## Implementación de una aplicación en la aplicación web

Después de ejecutar AzureWebDemo y crear la nueva aplicación web, inicie sesión en el portal clásico, haga clic en **Aplicaciones web** y elija **WebDemoWebApp** en la lista **Aplicaciones web**. En la página del panel de la aplicación web, haga clic en **Examinar** (o en la dirección URL, `webdemowebapp.azurewebsites.net`) para acceder a ella. Se mostrará una página con un marcador de posición en blanco, porque todavía no hay contenido publicado en la aplicación web.

A continuación, creará una aplicación "Hello World" y la implementará en la aplicación web.


### Creación de una aplicación Hello World en JSP

#### Creación de la aplicación

Para demostrar cómo se implementa una aplicación en la web, el siguiente procedimiento describe cómo se crea una aplicación "Hello World" sencilla de Java y cómo se carga en la aplicación web del Servicio de aplicaciones que se creó con la aplicación.

1. Haga clic en **Archivo > Nuevo > Proyecto web dinámico**. Asígnele el nombre `JSPHello`. No es necesario cambiar ninguna otra configuración en este cuadro de diálogo. Haga clic en **Finalizar**

    ![][3]

2. En el Explorador de proyectos, expanda el proyecto **JSPHello**, haga clic en **WebContent**. A continuación, haga clic en **Nuevo > Archivo JSP**. En el cuadro de diálogo Nuevo archivo JSP, asigne al archivo el nombre `index.jsp`. Haga clic en **Siguiente**.

3. En el cuadro de diálogo **Seleccionar plantilla JSP**, elija **Nuevo archivo JSP (html)** y haga clic en **Finalizar**.

4. En index.jsp, agregue el siguiente código en las secciones de las etiquetas `<head>` y `<body>`:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### Ejecución de la aplicación Hello World en el host local

Antes de ejecutar esta aplicación, deberá configurar algunas propiedades.

1. En el Explorador de soluciones, haga clic en el proyecto **JSPHello** y elija **Propiedades**.

2. En el cuadro de diálogo **Propiedades**, elija **Ruta de compilación de Java**, elija la pestaña **Ordenar y exportar**, elija **Biblioteca del sistema JRE**, a continuación, haga clic en **Arriba** para moverla a la parte superior de la lista.

    ![][4]

3. También, en el cuadro de diálogo **Propiedades**, elija **Tiempo de ejecución de destino** y haga clic en **Nuevo**.

4. En el cuadro de diálogo **Entorno de ejecución del nuevo servidor**, elija un servidor, como **Apache Tomcat v7.0**, y haga clic en **Siguiente**. En el cuadro de diálogo **Tomcat Server**, configure el valor de **Nombre** como `Apache Tomcat v7.0`. El valor de **Directorio de instalación de Tomcat** debe ser el directorio en el que se instaló la versión del servidor de Tomcat que desea usar.

    ![][5]

    Haga clic en **Finalizar**

5. Volverá a la página **Tiempo de ejecución de destino** del cuadro de diálogo **Propiedades**. Elija **Apache Tomcat v7.0** y haga clic en **Aceptar**.

    ![][6]

6. En Eclipse, en el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar). En el cuadro de diálogo **Run As** (Ejecutar como), elija **Run on Server** (Ejecutar en el servidor). En el cuadro de diálogo **Run on Server** (Ejecutar en el servidor), elija **Tomcat v7.0 Server**:

    ![][7]

    Haga clic en **Finalizar**

7. Cuando se ejecuta la aplicación, la página **JSPHello** debería aparecer en una ventana del host local en Eclipse (`http://localhost:8080/JSPHello/`), con el mensaje siguiente:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Exportación de la aplicación como WAR

Exporte los archivos de proyecto web como un archivo web (WAR) para que pueda implementarlo en la aplicación web. Los siguientes archivos del proyecto web residen en la carpeta WebContent:

    META-INF
    WEB-INF
    index.jsp

1. Haga clic con el botón derecho en la carpeta WebContent y elija **Exportar**.

2. En el cuadro de diálogo **Exportar selección**, haga clic en **Web > WAR**. Después, haga clic en **Siguiente**.

3. En el cuadro de diálogo **Exportar WAR**, elija el directorio de origen del proyecto actual e incluya el nombre del archivo WAR al final. Por ejemplo:

    `<project-path>/JSPHello/src/JSPHello.war`

Para obtener más información sobre cómo implementar archivos WAR, consulte [Adición de una aplicación Java a las aplicaciones web del Servicio de aplicaciones de Azure](web-sites-java-add-app.md).


### Implementación de la aplicación Hello World mediante FTP

Elija un cliente FTP de terceros para publicar la aplicación. En este procedimiento, se describen dos opciones: la consola Kudu integrada en Azure y FileZilla, una herramienta popular con una cómoda interfaz de usuario gráfica.

> **Nota:** el Kit de herramientas de Azure para Eclipse admite la implementación en cuentas de almacenamiento y servicios en la nube, pero no admite actualmente la implementación en aplicaciones web. Puede realizar implementaciones en cuentas de almacenamiento y servicios en la nube mediante un proyecto de implementación de Azure, tal y como se describe en [Creación de una aplicación Hola a todos para Azure en Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), pero no en aplicaciones web. Utilice otros métodos, como GitHub o FTP, para transferir archivos a su aplicación web.

> **Nota:** no se recomienda utilizar FTP desde el símbolo del sistema de Windows (la utilidad línea de comandos FTP.EXE que se incluye con Windows). Los clientes FTP que utilizan FTP activo, como FTP.EXE, a menudo no funcionan a través de firewalls. El FTP activo especifica una dirección interna basada en LAN a la que es difícil que pueda conectarse un servidor FTP.

Para obtener más información acerca de la implementación en una aplicación web del Servicio de aplicaciones mediante FTP, consulte los temas siguientes:

- [Implementación mediante una utilidad FTP](web-sites-deploy.md)


#### Configurar credenciales de implementación

Debe haber ejecutado la aplicación **AzureWebDemo** para crear una aplicación web. Los archivos se transfieren a esta ubicación.

1. Inicie sesión en el portal clásico y haga clic en **Aplicaciones web**. Asegúrese de que **WebDemoWebApp** aparezca en la lista de aplicaciones web. Debe estar en ejecución. Haga clic en **WebDemoWebApp** para abrir la página **Panel**.

2. En la página **Panel**, en **Vista rápida**, haga clic en **Configurar credenciales de implementación** (si ya cuenta con credenciales de implementación, la opción será **Restablezca sus credenciales de implementación**).

    Credenciales de implementación asociadas con una cuenta de Microsoft. Deberá especificar un nombre de usuario y una contraseña que pueda utilizar para realizar la implementación con Git y FTP. Puede usar estas credenciales para realizar la implementación en cualquier aplicación web en todas las suscripciones de Azure asociadas a su cuenta de Microsoft. Especifique las credenciales de implementación de Git y FTP en el cuadro de diálogo. Tome nota del nombre de usuario y la contraseña para usarlos en el futuro.


#### Obtención de la información de conexión para FTP

Si desea utilizar FTP para implementar archivos de aplicación en la aplicación web recién creada, deberá obtener información de conexión. Hay dos maneras de obtener información de conexión. Una de ellas es visitar la página **Panel** de la aplicación web. La otra manera es descargar el perfil de publicación de la aplicación web. El perfil de publicación es un archivo XML que proporciona información como las credenciales de inicio de sesión y el nombre de host FTP para sus aplicaciones web en el Servicio de aplicaciones de Azure. Puede utilizar este nombre de usuario y esta contraseña para realizar la implementación en cualquier aplicación web en todas las suscripciones asociadas a la cuenta de Azure, no solo en esta.

Para obtener información de conexión de FTP a partir de las hojas de la aplicación web en el [Portal Azure][]\:

1. En **Essentials**, busque y copie el **nombre de host del FTP**. Se trata de un URI similar a `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. En **Essentials**, busque y copie el **nombre de usuario de implementación/FTP**. Tendrá el formato *nombreaplicaciónweb\\nombreusuarioimplementación*, por ejemplo `WebDemoWebApp\deployer77`.

Para obtener información sobre la conexión FTP desde el perfil de publicación:

1. En la hoja de la aplicación web, haga clic en **Obtener perfil de publicación**. Se descargará un archivo .publishsettings en la unidad local.

2. Abra el archivo .publishsettings en un editor XML o un editor de texto y busque el elemento `<publishProfile>` que contiene `publishMethod="FTP"`. Debería tener este aspecto:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Tenga en cuenta que la configuración `publishProfile` de la aplicación web se asigna a la configuración del administrador del sitio de FileZilla de esta forma:

- `publishUrl` es el mismo valor que el **Nombre de host FTP**, el valor establecido en **Host**.
- `publishMethod="FTP"` significa que se establece el valor **Protocolo** en **FTP - Protocolo de transferencia de archivos**. El valor de **Cifrado** debe ser **Usar FTP sin formato**.
- `userName` y `userPWD` son claves para los valores reales del nombre de usuario y la contraseña que especificó al restablecer las credenciales de la implementación. `userName` es el mismo que **Implementación/Usuario FTP**. Se asignan a **Usuario** y **Contraseña** en FileZilla.
- `ftpPassiveMode="True"` significa que el sitio FTP utiliza la transferencia FTP pasiva. Elija **Pasivo** en la pestaña **Transferir configuración**.


#### Configuración de la aplicación web para hospedar una aplicación Java

Antes de publicar la aplicación, deberá cambiar algunos valores de configuración para que la aplicación web pueda hospedar una aplicación Java.

1. En el portal clásico, vaya a la página **Panel** de la aplicación web y haga clic en **Configurar**. En la página **Configurar**, especifique la configuración siguiente.

2. En **Versión de Java**, el valor predeterminado es **Desactivar**. Elija la versión de Java a la que se dirige su aplicación, por ejemplo 1.7.0\_51. Después de hacer esto, asegúrese de que **Contenedor web** esté establecido en una versión del servidor de Tomcat.

3. En **Documentos predeterminados**, agregue index.jsp y colóquelo en la parte superior de la lista. (El archivo predeterminado para las aplicaciones web es hostingstart.html).

4. Haga clic en **Guardar**.


#### Publicación de la aplicación mediante Kudu

Una manera de publicar la aplicación consiste en utilizar la consola de depuración Kudu integrada en Azure. Kudu es conocido por su estabilidad y coherencia con las aplicaciones web del Servicio de aplicaciones y el servidor de Tomcat. Puede obtener acceso a la consola para la aplicación web a través de una dirección URL de la forma siguiente:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Para este procedimiento, la consola Kudu se encuentra en la siguiente dirección URL. Acceda a esta ubicación:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. En el menú superior, elija **Consola de depuración > CMD**.

3. En la línea de comandos de la consola, vaya a `/site/wwwroot` (o haga clic en `site` y en `wwwroot`, en la parte superior de la página de la vista de directorios):

    `cd /site/wwwroot`

4. Después de especificar un valor en **Versión de Java**, el servidor de Tomcat debe crear un directorio de aplicaciones web. En la línea de comandos de la consola, acceda al directorio de aplicaciones web:

    `mkdir webapps`

    `cd webapps`

5. Arrastre JSPHello.war desde `<project-path>/JSPHello/src/` y colóquelo en la vista de directorio Kudu, en `/site/wwwroot/webapps`. No lo arrastre hasta el área "Arrastre aquí para cargar y comprimir", porque Tomcat lo descomprimirá.

  ![][8]

En primer lugar, JSPHello.war aparece en el área de directorio por sí solo:

  ![][9]

Poco tiempo después (probablemente, menos de 5 minutos), el servidor de Tomcat descomprimirá el archivo WAR en un directorio JSPHello desempaquetado. Haga clic en el directorio raíz para ver si index.jsp se ha descomprimido y copiado allí. Si es así, acceda de nuevo al directorio de aplicaciones web para ver si se ha creado el directorio JSPHello desempaquetado. Si no ve estos elementos, espere y repita el procedimiento.

  ![][10]


#### Publicación de la aplicación mediante FileZilla (opcional)

Otra herramienta que puede usar para publicar la aplicación es FileZilla, un cliente FTP de terceros popular con una cómoda interfaz de usuario gráfica. Puede descargar e instalar FileZilla desde [http://filezilla-project.org/](http://filezilla-project.org/) en caso de que no lo tenga. Para obtener más información sobre cómo utilizar el cliente, consulte la [documentación de FileZilla ](https://wiki.filezilla-project.org/Documentation) y esta entrada de blog en [Clientes FTP - Parte 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. En FileZilla, haga clic en **File (Archivo) > Site Manager (Administrador de sitios)**.
2. En el cuadro de diálogo **Site Manager** (Administrador de sitios), haga clic en **New Site** (Nuevo sitio). Aparecerá un nuevo sitio FTP en blanco en **Select Entry** (Seleccionar entrada), en el que se le solicitará que proporcione un nombre. Para este procedimiento, asígnele el nombre `AzureWebDemo-FTP`.

    En la pestaña **General**, especifique la siguiente configuración: - **Host:** especifique el **Nombre de host FTP** que copió del panel. - **Port** (Puerto): deje este campo en blanco, ya que se trata de una transferencia pasiva y el servidor será el que determine el puerto que se debe usar. - **Protocol** (Protocolo): es el protocolo de transferencia de archivo FTP. - **Encryption** (Cifrado): use el modo FTP sin cifrar. - **Logon Type** (Tipo de inicio de sesión): especifique Normal. - **User** (Usuario): especifique el usuario FTP o la implementación que copió del panel. Se trata del nombre de usuario FTP completo, que tiene la forma *nombreaplicaciónweb\\nombreusuario*.- **Password** (Contraseña): escriba la contraseña que especificó al configurar las credenciales de la implementación.

    En la pestaña **Transfer Settings** (Configuración de transferencia), elija **Passive** (Pasivo).

3. Haga clic en **Conectar**. Si la configuración es correcta, la consola de FileZilla mostrará el mensaje `Status: Connected` y emitirá un comando `LIST` para mostrar el contenido del directorio.

4. En el panel del sitio **Local**, elija el directorio de origen en el que reside el archivo JSPHello.war; la ruta será similar a la siguiente:

    `<project-path>/JSPHello/src/`

5. En el panel del sitio **Remote** (Remoto), elija la carpeta de destino. El archivo WAR se implementará en el directorio `webapps`, en la raíz de la aplicación web. Vaya a `/site/wwwroot`, haga clic con el botón derecho en `wwwroot` y elija **Create directory** (Crear directorio). Asigne al directorio el nombre `webapps` y acceda a él.

6. Transfiera JSPHello.war a `/site/wwwroot/webapps`. Elija JSPHello.war en la lista de archivos **Local**, haga clic con el botón derecho en él y elija **Upload** (Cargar). Deberá mostrarse en `/site/wwwroot/webapps`.

7. Después de copiar JSPHello.war en el directorio de aplicaciones web, el servidor de Tomcat desempaquetará automáticamente (descomprimirá) los archivos en el archivo WAR. Aunque el servidor de Tomcat comienza a desempaquetar casi inmediatamente, puede pasar mucho tiempo (posiblemente horas) para que los archivos aparezcan en el cliente FTP.


#### Ejecución de la aplicación Hello World en la aplicación web

1. Una vez que cargue el archivo WAR y verifique que el servidor de Tomcat ha creado un directorio `JSPHello` desempaquetado, vaya a `http://webdemowebapp.azurewebsites.net/JSPHello` para ejecutar la aplicación.

    > **Nota:** si hace clic en **Examinar** desde el portal clásico, es posible que se muestre la página web predeterminada, en la que se indica que la aplicación web de Java se ha creado correctamente. Tendrá que actualizar la página web para poder ver el resultado de la aplicación en lugar de la página web predeterminada.

2. Cuando se ejecuta la aplicación, debería mostrarse una página web con el siguiente resultado:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Limpieza de los recursos de Azure

Este procedimiento crea una aplicación web de Servicio de aplicaciones. Se le facturará por el recurso durante toda su existencia. A menos que vaya a continuar usando la aplicación web para propósitos de pruebas o desarrollo, debería plantearse si desea detenerla o eliminarla. Una aplicación web detenida generará un gasto pequeño, pero podrá volver a iniciarla en cualquier momento. Al eliminar una aplicación web, se borran todos los datos que se hayan cargado en ella.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[instalador de plataforma web (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[kit de herramientas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[portal clásico de Azure]: https://manage.windowsazure.com
[¿Qué es un directorio de Azure AD?]: http://technet.microsoft.com/library/jj573650.aspx
[Crear y cargar un certificado de administración para Azure]: http://msdn.microsoft.com/library/azure/gg551722.aspx
[Keytool: herramienta para administrar certificados y claves]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Portal Azure]: https://portal.azure.com

<!---HONumber=Oct15_HO3-->