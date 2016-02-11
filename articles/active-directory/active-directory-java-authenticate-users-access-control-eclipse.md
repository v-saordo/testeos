<properties
    pageTitle="Uso del control de acceso (Java) - Microsoft Azure"
    description="Obtenga información acerca de cómo desarrollar y usar el Control de acceso con Java en Azure."
	services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="01/09/2016" 
    ms.author="robmcm" />

# Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse

Esta guía le mostrará cómo usar el servicio de control de acceso de Azure (ACS) dentro del Kit de herramientas de Azure para Eclipse. Para obtener más información sobre ACS, consulte la sección [Pasos siguientes](#next_steps).

> [AZURE.NOTE]El filtro de control de los servicios de acceso de Azure es una Community Technology Preview. Como versión preliminar de software, no cuenta formalmente con el respaldo de Microsoft.

## ¿Qué es ACS?

La mayoría de los desarrolladores ni son expertos en identidad ni generalmente quieren dedicar demasiado tiempo a desarrollar mecanismos de autenticación y autorización para sus aplicaciones y servicios. ACS es un servicio de Azure que ofrece un sencillo método de autenticación de usuarios que necesitan tener acceso a sus servicios y aplicaciones web sin tener que tomar en consideración una lógica de autenticación compleja en su código.

En ACS están disponibles las características siguientes:

-   Integración con Windows Identity Foundation (WIF).
-   Compatibilidad con los proveedores de identidades (IP) habituales, entre los que se incluyen Windows Live ID, Google, Yahoo! y Facebook.
-   Compatibilidad con los servicios de federación de Active Directory (AD FS) 2.0.
-   Un servicio de administración basado en un protocolo de datos abierto (OData) que proporciona acceso programático a la configuración de ACS.
-   Un portal de administración que concede acceso administrativo a la configuración de ACS.

Para obtener más información acerca de ACS, consulte [Access Control Service 2.0][].

## Conceptos

Azure ACS se ha creado sobre los principios de la identidad basada en notificaciones, un enfoque coherente para crear mecanismos de autenticación para las aplicaciones que se ejecutan en el entorno local o en la nube. La identidad basada en notificaciones proporciona un método común para que las aplicaciones y los servicios obtengan la información necesaria acerca de la identidad de los usuarios de la organización, de otras organizaciones y en Internet.

Para completar las tareas de esta guía, deberá comprender los siguientes conceptos:

**Cliente**: en el contexto de esta guía de procedimientos, es un explorador que intenta obtener acceso a su aplicación web.

**Aplicación de usuarios de confianza (RP)**: una aplicación RP es un servicio o sitio web que externaliza la autenticación a una autoridad externa. En la jerga de la identidad, se dice que los RP confían en dicha autoridad. En esta guía se explica cómo configurar su aplicación para que confíe en ACS.

**Token**: un token es una colección de datos de seguridad que normalmente se emite después de la autenticación correcta de un usuario. Contiene un conjunto de *notificaciones*, atributos del usuario autenticado. Una notificación puede representar el nombre de un usuario, un identificador para el rol al que pertenece un usuario, la edad del usuario, etc. Normalmente se firma un token de manera digital, lo que significa que siempre se podrá rastrear a quien lo emite y no se podrá cambiar su contenido. Un usuario tiene acceso a una aplicación RP presentando un token válido emitido por una autoridad en la que confía la aplicación RP.

**Proveedor de identidades (IP)**: un proveedor de identidades es una autoridad que autentica las identidades de los usuarios y emite los tokens de seguridad. El trabajo real de emitir tokens se implementa a través de un servicio especial llamado servicio de token de seguridad (STS). Ejemplos típicos de proveedores de identidades incluyen Windows Live ID; Facebook, repositorios de usuarios profesionales (como Active Directory), etc. Cuando ACS se ha configurado para confiar en un IP, el sistema aceptará y validará los tokens que emite dicho IP. ACS puede confiar en varios IP a la vez, lo que significa que cuando su aplicación confía en ACS, puede ofrecer de manera inmediata su aplicación a todos los usuarios autenticados por todos los IP en los que ACS confía en su nombre.

**Proveedor de federación (FP)**: los IP conocen directamente a los usuarios, los autentican usando sus credenciales y emiten notificaciones sobre lo que saben de ellos. Un proveedor de federación (FP) es un tipo de entidad diferente: en lugar de autenticar a los usuarios directamente, actúa como un intermediario y negocia la autenticación entre un RP y uno o más IP. Tanto los IP como los FP emiten tokens de seguridad, por lo tanto, ambos utilizan los servicios de token de seguridad (STS). ACS es un FP.

**Motor de reglas de ACS**: la lógica que se utiliza para transformar los tokens entrantes procedentes de IP de confianza en tokens que pueda consumir el RP se codifica en forma de simples reglas de transformación de notificaciones. ACS incluye un motor de reglas que se ocupa de aplicar cualquier lógica de transformación que se haya especificado para su RP.

**Espacio de nombres de ACS**: un espacio de nombres es una partición de nivel superior de ACS que se utiliza para organizar su configuración. Un espacio de nombres contiene una lista de los IP en los que confía, las aplicaciones de RP a las que desea servir, las reglas con las que espera que el motor de reglas procese los tokens entrantes, etc. Un espacio de nombres expone varios extremos que la aplicación y el desarrollador utilizarán para hacer que ACS realice su función.

La siguiente ilustración muestra cómo funciona la autenticación de ACS con una aplicación web:

![Diagrama de flujo de ACS][acs_flow]

1.  El cliente (en este caso, un explorador) solicita una página del RP.
2.  Como la solicitud no se ha autenticado todavía, el RP redirige al usuario a la autoridad en la que confía, es decir, ACS. El ACS presenta al usuario la selección de IP especificados para este RP. El usuario selecciona el IP adecuado.
3.  El cliente examina la página de autenticación del IP y pide al usuario que inicie sesión.
4.  Cuando el cliente se haya autenticado (por ejemplo, cuando haya escrito las credenciales de identidad), el IP emitirá un token de seguridad.
5.  Después de haber emitido un token de seguridad, el IP redirige el cliente a ACS y el cliente envía a ACS el token de seguridad que emite el IP.
6.  ACS valida el token de seguridad emitido por el IP, introduce las notificaciones de identidad de este token en el motor de reglas de ACS, calcula las notificaciones de identidad resultantes y emite un nuevo token de seguridad que contenga estas notificaciones.
7.  ACS redirige el cliente al RP. El cliente envía el nuevo token de seguridad emitido por ACS al RP. El RP valida la firma del token de seguridad emitido por ACS, valida las notificaciones en este token y devuelve la página que solicitó originalmente.

## Requisitos previos

Necesitará lo siguiente para completar las tareas de esta guía:

- Un kit para desarrolladores de Java (JDK) v1.6 o superior.
- Eclipse IDE para Java EE Developers, Indigo o superior. Esto se puede descargar en <http://www.eclipse.org/downloads/>. 
- Una distribución de un servidor de aplicaciones o servidor web basado en Java, como Apache Tomcat, GlassFish, JBoss Application Server o Jetty.
- Una suscripción de Azure, que se puede adquirir en <http://www.microsoft.com/windowsazure/offers/>.
- El Kit de herramientas de Azure para Eclipse, versión de abril de 2014 o posterior. Para más información, consulte [Instalación del kit de herramientas de Azure para Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Un certificado X.509 para utilizar con la aplicación. Necesitará este certificado en formato de certificado público (.cer) e intercambio de información personal (.PFX). (Las opciones para crear este certificado se describirán más adelante en este tutorial).
- Estar familiarizado con el emulador de proceso de Azure y las técnicas de implementación analizadas en [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## Creación de un espacio de nombres de ACS

Para comenzar a utilizar el servicio de control de acceso (ACS) en Azure, debe crear un espacio de nombres de ACS. El espacio de nombres proporciona un ámbito único para dirigir los recursos de ACS desde dentro de su aplicación.

1. Inicie sesión en el [Portal de administración de Azure][].
2. Haga clic en **Active Directory**. 
3. Para crear un nuevo espacio de nombres de control de acceso, haga clic en **Nuevo**, **Servicios de aplicaciones**, **Control de acceso** y, a continuación, en **Creación rápida**. 
4. Escriba un nombre para el espacio de nombres. Azure verificará si el nombre es único.
5. Seleccione la región en la que se usa el espacio de nombres. Para obtener el máximo rendimiento, utilice la región en la que va a implementar su aplicación.
6. Si tiene varias suscripciones, seleccione la que desea utilizar para el espacio de nombres de ACS.
7. Haga clic en **Crear**.

Azure crea y activa el espacio de nombres. Espere hasta que el estado del nuevo espacio de nombres sea **Activo** antes de continuar.

## Incorporación de proveedores de identidades

En esta tarea, agregará IP para utilizarlos con su aplicación RP para la autenticación. Para los fines de esta demostración, esta tarea muestra cómo agregar Windows Live como un IP, pero podría utilizarse cualquiera de los IP que aparecen en el portal de administración de ACS.


1.  En el [Portal de administración de Azure][], haga clic en **Active Directory**, seleccione un espacio de nombres de control de acceso y, a continuación, haga clic en **Administrar**. Se abre el portal de administración de ACS.
2.  En el panel de navegación izquierdo del portal de administración de ACS, haga clic en **Proveedores de identidades**.
3.  Windows Live ID está habilitado de manera predeterminada y no se puede eliminar. Para los fines de este tutorial, solo se utiliza Windows Live ID. Sin embargo, en esta pantalla podría agregar otros IP haciendo clic en el botón **Agregar**.

Windows Live ID ahora está habilitado como un IP para su espacio de nombres de ACS. A continuación, especifique la aplicación web de Java (que se va a crear más adelante) como un RP.

## Incorporación de una aplicación de usuario de confianza

En esta tarea, configurará ACS para reconocer la aplicación web de Java como una aplicación RP válida.

1.  En el portal de administración de ACS, haga clic en **Aplicaciones de usuarios de confianza**.
2.  En la página **Aplicaciones de usuarios de confianza**, haga clic en **Agregar**.
3.  En la página **Adición de aplicación de usuarios de confianza**, haga lo siguiente:
    1.  En **Nombre**, escriba el nombre del RP. Para los fines de este tutorial, escriba **Azure Web App**.
    2.  En **Modo**, seleccione **Especificar la configuración manualmente**.
    3.  En **Territorio**, escriba el URI al que se aplica el token de seguridad que emitió ACS. Para esta tarea, escriba ****http://localhost:8080/**. ![El dominio de usuario de confianza para utilizar en el emulador de proceso][relying_party_realm_emulator]
4.  En **Dirección URL de retorno**, escriba la dirección URL a la cual ACS devuelve el token de seguridad. Para esta tarea, escriba ****http://localhost:8080/MyACSHelloWorld/index.jsp** ![La dirección URL de retorno de usuario de confianza para utilizar en el emulador de proceso][relying_party_return_url_emulator]
5.  Acepte los valores predeterminados en el resto de los campos.

4.  Haga clic en **Guardar**.

Ha configurado correctamente su aplicación web de Java para que cuando se ejecute en el emulador de proceso de Azure (en http://localhost:8080/) sea un RP en su espacio de nombres de ACS. A continuación, cree las reglas que ACS utiliza para procesar notificaciones para el RP.

## Creación de reglas

En esta tarea, definirá las reglas que controlan la manera en que las notificaciones se transmiten desde los IP a su RP. En lo que respecta a esta guía, simplemente configuraremos ACS para que copie los valores y tipos de notificación de introducción directamente en el token de salida, sin filtrarlos ni modificarlos.

1.  En la página principal del portal de administración de ACS, haga clic en **Grupos de reglas**.
2.  En la página **Grupos de reglas**, haga clic en **Grupo de reglas predeterminadas para Azure Web App**.
3.  En la página **Editar grupo de reglas**, haga clic en **Generar**.
4.  En la página **Generar reglas: Grupo de reglas predeterminado para Azure Web App**, asegúrese de que la opción Windows Live ID esté activada y, a continuación, haga clic en **Generar**.	
5.  En la página **Editar grupo de reglas**, haga clic en **Guardar**.

## Carga de un certificado en su espacio de nombres de ACS

En esta tarea, cargará un certificado .PFX que se utilizará para firmar solicitudes de token creadas por su espacio de nombres de ACS.

1.  En la página principal del portal de administración de ACS, haga clic en **Certificados y claves**.
2.  En la página **Certificados y claves**, haga clic en **Agregar** encima de **Firma de tokens**.
3.  En la página **Agregar certificado o clave de firma de tokens**:
    1. En la sección **Usado para**, haga clic en **Aplicación de usuario de confianza** y seleccione **Azure Web App** (que anteriormente definió como el nombre de su aplicación de usuario de confianza).
    2. En la sección **Tipo**, seleccione **Certificado X.509**.
    3. En la sección **Certificado**, haga clic en el botón de examinar y navegue al archivo del certificado X.509 que desea utilizar. Este será un archivo .PFX. Seleccione el archivo, haga clic en **Abrir** y, a continuación, escriba la contraseña del certificado en el cuadro de texto **Contraseña**. Observe que, para fines de prueba, puede utilizar un certificado autofirmado. Para crear un certificado autofirmado, utilice el botón **Nuevo** que aparece en el cuadro de diálogo **Biblioteca de filtros de ACS** (que se describe más adelante), o bien, puede usar la utilidad **encutil.exe** desde el [sitio web del proyecto][] del kit de inicio de Azure para Java.
    4. Asegúrese de que la opción **Hacer principal** esté activada. La página **Agregar clave o certificado de firma de tokens** debería ser similar a la siguiente. ![Agregar certificado de firma de tokens][add_token_signing_cert]
    5. Haga clic en **Guardar** para guardar la configuración y cierre la página **Agregar clave o certificado de firmas de tokens**

A continuación, revise la información que se encuentra en la página de integración de aplicaciones y copie el URI que necesitará para configurar su aplicación web de Java para usar ACS.

## Revisión de la página de integración de aplicaciones

Puede encontrar toda la información y el código que se necesita para configurar su aplicación web de Java (la aplicación RP) para trabajar con ACS en la página de integración de aplicaciones del portal de administración de ACS. Necesitará esta información cuando configure la aplicación web de Java para la autenticación federada.

1.  En el portal de administración de ACS, haga clic en **Integración de aplicaciones**.  
2.  En la página **Integración de aplicaciones**, haga clic en **Páginas de inicio de sesión**
3.  En la página **Integración de página de inicio de sesión**, haga clic en **Azure Web App**.

En la página **Integración de página de inicio de sesión: Azure Web App**, la dirección URL que aparece en **Opción 1: vínculo a una página de inicio de sesión hospedada de ACS** se utilizará en la aplicación web de Java. Necesitará este valor cuando agregue la biblioteca de filtros de servicios de control de acceso de Azure a su aplicación Java.

## Creación de una aplicación web de Java
1. En Eclipse, en el menú, haga clic en **Archivo**, haga clic en **Nuevo** y, a continuación, haga clic en **Dynamic Web Project**. (Si no ve **Dynamic Web Project** como proyecto disponible después de hacer clic en **Archivo**, **Nuevo**, a continuación lleve a cabo lo siguiente: haga clic en **Archivo**, haga clic en **Nuevo**, haga clic en **Proyecto**, expanda **Web**, haga clic en **Dynamic Web Project** y en **Siguiente**). Para los fines de este tutorial, asigne el nombre **MyACSHelloWorld** al proyecto. (Asegúrese de utilizar este nombre; los pasos posteriores de este historial esperan que su archivo WAR tenga el nombre MyACSHelloWorld). La pantalla se parecerá a la siguiente:

    ![Ejemplo: Crear un proyecto Hello World para ACS][create_acs_hello_world]

    Haga clic en **Finalizar**
2. En la vista del explorador de proyectos de Eclipse, expanda **MyACSHelloWorld**. Haga clic con el botón secundario en **WebContent**, luego haga clic en **Nuevo** y, a continuación, en **JSP File**.
3. En el cuadro de diálogo **Nuevo archivo JSP**, asigne al archivo el nombre **index.jsp**. Mantenga la carpeta principal como MyACSHelloWorld/WebContent, tal como aparece a continuación:

    ![Ejemplo: Agregar un archivo JSP para ACS][add_jsp_file_acs]

    Haga clic en **Siguiente**.

4. En el cuadro de diálogo **Seleccionar plantilla JSP**, elija **Nuevo archivo JSP (html)** y haga clic en **Finalizar**.
5. Cuando se abre el archivo index.jsp en Eclipse, agregue texto para que muestre **Hola mundo ACS** dentro del elemento `<body>` existente. Su contenido de `<body>` actualizado debería aparecer de la siguiente manera:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Guarde el archivo index.jsp.
  
## Incorporación de la biblioteca de filtros de ACS a su aplicación

1. En el explorador de proyectos de Eclipse, haga clic con el botón derecho en **MyACSHelloWorld** y, a continuación, haga clic en **Ruta de acceso de compilación** y, a continuación, en **Configurar ruta de acceso de compilación**.
2. En el cuadro de diálogo **Ruta de acceso de compilación de Java**, haga clic en la pestaña **Bibliotecas**.
3. Haga clic en **Agregar biblioteca**.
4. Haga clic en **Filtro de servicios de control de acceso de Azure (de MS Open Tech)** y, a continuación, en **Siguiente**. Aparecerá el cuadro de diálogo **Filtro de servicios de control de acceso de Azure**. (El campo **Ubicación** puede tener una ruta de acceso distinta, dependiendo de dónde instalara Eclipse; además, el número de versión podría ser distinto, dependiendo de las actualizaciones de software).

    ![Agregar biblioteca de filtros de ACS][add_acs_filter_lib]

5. Con el explorador abierto en la página **Integración de página de inicio de sesión** del Portal de administración, copie la dirección URL que aparece en el campo **Opción 1: vínculo a una página de inicio de sesión hospedada de ACS** y péguelo en el campo **Extremo de autenticación ACS** del cuadro de diálogo de Eclipse.
6. En el explorador, abra la página **Editar aplicación de usuario de confianza** del Portal de administración, copie la dirección URL que aparece en el campo **Territorio** y péguela en el campo **Territorio de usuario de confianza** del cuadro de diálogo de Eclipse.
7. En la sección **Seguridad** del cuadro de diálogo de Eclipse, si desea utilizar un certificado existente, haga clic en **Examinar**, navegue al certificado que desea utilizar, selecciónelo y haga clic en **Abrir**. O bien, si desea crear un certificado nuevo, haga clic en **Nuevo** para mostrar el cuadro de diálogo **Nuevo certificado** y, a continuación, especifique la contraseña, el nombre del certificado .cer y el nombre del archivo .pfx para el certificado nuevo.
8. Haga clic en **Insertar el certificado en el archivo WAR**. Al insertar el certificado de esta manera se incluye en su implementación sin que deba agregarlo manualmente como componente. (Si, por el contrario, debe almacenar el certificado de manera externa desde el archivo WAR, podría agregar el certificado como un componente de rol y desactivar la opción **Incrustar el certificado en el archivo WAR**).
9. [Opcional] Mantenga activada la opción **Requerir conexiones HTTPS**. Si define esta opción, deberá tener acceso a su aplicación mediante el protocolo HTTPS. Si no desea exigir conexiones HTTPS, desactive esta opción.
10. En una implementación en el emulador de proceso, su configuración de **Azure ACS Filter** será como la siguiente.

    ![Configuración del filtro de Azure ACS para una implementación en el emulador de proceso][add_acs_filter_lib_emulator]

11. Haga clic en **Finalizar**
12. Haga clic en **Sí** cuando aparezca un cuadro de diálogo que indique que se creará un archivo web.xml.
13. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Ruta de acceso de compilación de Java**.

## Implementación del emulador de proceso

1. En el explorador de proyectos de Eclipse, haga clic con el botón secundario en **MyACSHelloWorld** y, a continuación, haga clic en **Azure** y luego en **Paquete de Azure**
2. En **Nombre de proyecto**, escriba **MyAzureACSProject** y haga clic en **Siguiente**.
3. Seleccione un JDK y un servidor de aplicaciones. (Estos pasos aparecen detallados en el tutorial [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)).
4. Haga clic en **Finalizar**
5. Haga clic en el botón **Ejecutar Emulador de Azure**.
6. Una vez que la aplicación web de Java se inicie en el emulador de proceso, cierre todas las instancias del explorador (para que ninguna sesión actual del explorador interfiera con la prueba de inicio de sesión en ACS).
7. Ejecute la aplicación abriendo <http://localhost:8080/MyACSHelloWorld/> en el explorador (o <https://localhost:8080/MyACSHelloWorld/> si ha activado **Requerir conexiones HTTPS**). Se le solicitará un inicio de sesión de Windows Live ID y, a continuación, debería ir a la URL de retorno que se especificó para su aplicación de usuario de confianza.
99.  Cuando haya terminado de visualizar la aplicación, haga clic en el botón **Restablecer Emulador de Azure**.

## Implementación en Azure

Para implementar en Azure, deberá cambiar el dominio de usuario de confianza y la URL de retorno para su espacio de nombres de ACS.

1. Dentro del Portal de administración de Azure, en la página **Editar aplicación de usuario de confianza**, modifique **Territorio** para que sea la URL del sitio implementado. Reemplace **example** por el nombre DNS que especificó para la implementación.

    ![El dominio de usuario de confianza para utilizar en producción][relying_party_realm_production]

2. Modifique la **dirección URL de retorno** para que sea la dirección URL de la aplicación. Reemplace **example** por el nombre DNS que especificó para la implementación.

    ![La URL de retorno de usuario de confianza para utilizar en producción][relying_party_return_url_production]

3. Haga clic en **Guardar** para guardar los cambios actualizados en la URL de retorno y el dominio de usuario de confianza.
4. Mantenga abierta la página **Integración de página de inicio de sesión** en el explorador, porque deberá copiar información desde ella.
5. En el explorador de proyectos de Eclipse, haga clic con el botón derecho en **MyACSHelloWorld** y, a continuación, haga clic en **Ruta de acceso de compilación** y, a continuación, en **Configurar ruta de acceso de compilación**.
6. Haga clic en la pestaña **Bibliotecas**, **Azure Access Control Services Filter** y, a continuación, en **Editar**.
7. Con el explorador abierto en la página **Integración de página de inicio de sesión** del Portal de administración, copie la dirección URL que aparece en el campo **Opción 1: vínculo a una página de inicio de sesión hospedada de ACS** y péguelo en el campo **Extremo de autenticación ACS** del cuadro de diálogo de Eclipse.
8. En el explorador, abra la página **Editar aplicación de usuario de confianza** del Portal de administración, copie la dirección URL que aparece en el campo **Territorio** y péguela en el campo **Territorio de usuario de confianza** del cuadro de diálogo de Eclipse.
9. En la sección **Seguridad** del cuadro de diálogo de Eclipse, si desea utilizar un certificado existente, haga clic en **Examinar**, navegue al certificado que desea utilizar, selecciónelo y haga clic en **Abrir**. O bien, si desea crear un certificado nuevo, haga clic en **Nuevo** para mostrar el cuadro de diálogo **Nuevo certificado** y, a continuación, especifique la contraseña, el nombre del certificado .cer y el nombre del archivo .pfx para el certificado nuevo.
10. Si desea insertar el certificado en el archivo WAR, mantenga activada la opción **Insertar el certificado en el archivo WAR**.
11. [Opcional] Mantenga activada la opción **Requerir conexiones HTTPS**. Si define esta opción, deberá tener acceso a su aplicación mediante el protocolo HTTPS. Si no desea exigir conexiones HTTPS, desactive esta opción.
12. En una implementación en Azure, la configuración del filtro de ACS de Azure será como la siguiente.

    ![Configuración del filtro de ACS de Azure para una implementación en producción][add_acs_filter_lib_production]

13. Haga clic en **Finalizar** para cerrar el cuadro de diálogo **Editar biblioteca**.
14. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de MyACSHelloWorld**.
15. En Eclipse, haga clic en el botón **Publicar en Azure Cloud**. Siga las indicaciones, tal como lo hizo en la sección **Implementación de su aplicación en Azure** del tema [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). 

Una vez que se ha implementado la aplicación web, cierre las sesiones abiertas del explorador, ejecute la aplicación web y se le solicitará iniciar sesión con credenciales de Windows Live ID y, a continuación, irá a la URL de retorno de su aplicación de usuario de confianza.

Cuando haya terminado de utilizar la aplicación Hello World de ACS, recuerde eliminar la implementación (aprenda a eliminar una implementación en el tema [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)).


## <a name="next_steps"></a>Pasos siguientes

Para analizar el lenguaje de marcado de aserción de seguridad (SAML) que devuelve ACS a su aplicación, consulte [Visualización del SAML que devuelve el servicio de control de acceso de Azure][]. Para seguir explorando la funcionalidad de ACS y experimentar con escenarios más sofisticados, consulte [Access Control Service 2.0][].

Además, en este ejemplo se utilizó la opción **Insertar el certificado en el archivo WAR**. Esta opción facilita la implementación del certificado. Si, por el contrario, desea mantener el certificado de firma independiente del archivo WAR, puede utilizar la siguiente técnica:

1. En la sección **Seguridad** del cuadro de diálogo **Filtro de servicios de control de acceso de Azure**, escriba **${env.JAVA\_HOME}/mycert.cer** y desactive la opción **Insertar el certificado en el archivo WAR**. (Ajuste mycert.cer si el nombre del archivo de certificado es distinto). Haga clic en **Finalizar** para cerrar el cuadro de diálogo.
2. En el explorador de proyectos de Eclipse, expanda **MyAzureACSProject**, haga clic con el botón derecho en **WorkerRole1**, haga clic en **Propiedades**, expanda **Rol de azure** y, a continuación, haga clic en **Componentes**.
3. Haga clic en **Agregar**.
4. Dentro del cuadro de diálogo **Agregar componente**:
    1. En la sección **Importación**:
        1. Utilice el botón **Archivo** para navegar al certificado que desea usar. 
        2. En **Método**, seleccione **copiar**.
    2. En **Como nombre**, haga clic en el cuadro de texto y acepte el nombre predeterminado.
    3. En la sección **Implementación**:
        1. En **Método**, seleccione **copiar**.
        2. En **Directorio de destino**, escriba **%JAVA\_HOME%**.
    4. El cuadro de diálogo **Agregar componente** debe parecerse al siguiente.

        ![Agregar componente de certificado][add_cert_component]

    5. Haga clic en **Aceptar**.

En este punto, su certificado se incluiría en la implementación. Observe que, independientemente de si insertó el certificado en el archivo WAR o si lo agregó como componente a la implementación, necesita cargar el certificado en su espacio de nombres, tal como se describe en la sección [Carga de un certificado en su espacio de nombres de ACS][].

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Carga de un certificado en su espacio de nombres de ACS]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[sitio web del proyecto]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Visualización del SAML que devuelve el servicio de control de acceso de Azure]: /es-ES/develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portal de administración de Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 

<!---HONumber=AcomDC_0114_2016-->