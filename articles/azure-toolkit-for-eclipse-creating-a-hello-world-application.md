<properties
    pageTitle="Creación de una aplicación Hola a todos para Azure en Eclipse"
    description="Aprenda a crear una aplicación Hola a todos sencilla mediante el kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="01/09/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# Creación de una aplicación Hola a todos para Azure en Eclipse #

Los siguientes pasos muestran cómo crear e implementar una aplicación JSP básica en Azure mediante el kit de herramientas de Azure para Eclipse. Se muestra un ejemplo de JSP por motivos de simplicidad, pero se aplicarían pasos muy similares para un servlet de Java en lo que respecta a la implementación de Azure.

La aplicación tendrá un aspecto similar al siguiente.

![][ic600360]

## Requisitos previos ##

* Un kit para desarrolladores de Java (JDK) v1.7 o superior.
* Eclipse IDE para Java EE Developers, Indigo o superior. Esto se puede descargar en <http://www.eclipse.org/downloads/>.
* Una distribución de un servidor de aplicaciones o servidor web basado en Java, como Apache Tomcat, GlassFish, JBoss Application Server, Jetty o IBM® WebSphere® Application Server Liberty Core.
* Una suscripción de Azure, que se puede adquirir en <http://azure.microsoft.com/pricing/purchase-options/>.
* El kit de herramientas de Azure para Eclipse. Para obtener más información, vea [Instalación del kit de herramientas de Azure para Eclipse][].

## Para crear una aplicación Hola a todos ##

En primer lugar, empezaremos con la creación de un proyecto Java.

*  Inicie Eclipse y, en el menú, haga clic en **Archivo**, **Nuevo** y **Dynamic Web Project**. (Si no ve **Dynamic Web Project** como proyecto disponible después de hacer clic en **Archivo**, **Nuevo**, a continuación lleve a cabo lo siguiente: haga clic en **Archivo**, **Nuevo** y **Proyecto**, expanda **Web**, y haga clic en **Dynamic Web Project** y **Siguiente**).
*  Para los fines de este tutorial, asigne el nombre **MyHelloWorld** al proyecto. (Asegúrese de utilizar este nombre; los pasos posteriores de este historial esperan que su archivo WAR tenga el nombre MyHelloWorld). La pantalla se parecerá a la siguiente:
    ![][ic589576]
* Haga clic en **Finalizar**
* En la vista del explorador de proyectos de Eclipse, expanda **MyHelloWorld**. Haga clic con el botón secundario en **WebContent**, luego haga clic en **Nuevo** y, a continuación, en **JSP File**.
* En el cuadro de diálogo **Nuevo archivo JSP**, asigne al archivo el nombre **index.jsp**. Mantenga la carpeta principal como **MyHelloWorld/WebContent**, tal como aparece a continuación:

    ![][ic659262]
* En el cuadro de diálogo **Seleccionar plantilla JSP**, para este tutorial, seleccione **Nuevo archivo JSP (html)** y haga clic en **Finalizar**.
* Cuando el archivo index.jsp se abra en Eclipse, agregue texto para que se muestre dinámicamente **Hola a todos** dentro del elemento `<body>` existente. Su contenido de `<body>` actualizado debería aparecer de la siguiente manera:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```.
* Guarde el archivo index.jsp.

## Para implementar de forma rápida y sencilla la aplicación en Azure ##

Tan pronto como tenga una aplicación web de Java lista para probar, puede usar el siguiente método abreviado directamente en la nube de Azure.

1. En el Explorador de proyectos de Eclipse, haga clic en **Hola a todos**.
1. En la barra de herramientas de Eclipse, haga clic en el botón **Publicar en la nube de Azure**,
    ![][ic710882].
1. Si va a publicar esta aplicación en Azure por primera vez y no ha creado un proyecto de implementación de Azure para esta aplicación antes, se creará un proyecto de implementación de Azure automáticamente. Debería ver el siguiente símbolo del sistema, que también incluye el paquete de JDK y el servidor de aplicaciones que se implementarán automáticamente para ejecutar la aplicación.
    ![][ic789598]
    Este enfoque de acceso directo permite probar de forma rápida y fácil la aplicación en Azure sin tener que configurar un JDK o servidor específicos que sean diferentes de los valores predeterminados. Si está satisfecho con los valores predeterminados, haga clic en **Aceptar** para continuar con los pasos siguientes.
    Sin embargo, si desea cambiar el JDK o el servidor de aplicaciones que se utilizará para la aplicación, puede hacerlo posteriormente editando el proyecto de implementación de Azure que se creó automáticamente para usted, o puede hacer clic en **Cancelar** ahora y leer la sección **Acerca de los proyectos de implementación de Azure** de este tutorial.
1. En el cuadro de diálogo **Publicar en Azure**:
    1. Si no hay aún ninguna suscripción que seleccionar en la lista **Suscripción**, siga estos pasos para importar la información de suscripción:
        1. Haga clic en **Importar desde el archivo PUBLISH-SETTINGS**.
        1. En el cuadro de diálogo **Importar información de suscripción**, haga clic en **Descargar archivo PUBLISH-SETTINGS**. Si todavía no ha iniciado sesión en su cuenta de Azure, se le solicitará que lo haga. A continuación, se le solicitará que guarde un archivo de configuración de publicación de Azure. Guárdelo en su equipo local.
        1. Todavía en el cuadro de diálogo **Importar información de suscripción**, haga clic en el botón **Examinar**, seleccione el archivo de configuración de publicación que guardó localmente en el paso anterior y, a continuación, haga clic en **Abrir**. La pantalla debe parecerse a la siguiente:
            ![][ic644267].
        1. Haga clic en **Aceptar**.
    1. En **Suscripción**, seleccione la suscripción que desee usar para la implementación.
    1. Para **Cuenta de almacenamiento**, seleccione la cuenta de almacenamiento que desee utilizar o haga clic en **Nuevo** para crear una nueva cuenta de almacenamiento.
    1. Para **Nombre de servicio**, seleccione el servicio en la nube que desee usar o haga clic en **Nuevo** para crear un nuevo servicio en la nube.
    1. Para **SO de destino**, seleccione la versión del sistema operativo que desee usar para la implementación.
    1. Para **Entorno de destino**, para fines de este tutorial, seleccione **Ensayo**. (Cuando esté listo para implementar en su sitio de producción, cámbielo a **Producción**.)
    1. Opcional: asegúrese de que la opción **Sobrescribir implementación anterior** esté activada si desea que la nueva implementación sobrescriba automáticamente la implementación anterior. Cuando habilite esta opción, no experimentará los problemas del "conflicto 409" cuando publique en la misma ubicación. 
        Tenga en cuenta que el cuadro de diálogo **Publicar en Azure** contiene una sección para **acceso remoto**. El acceso remoto no está habilitado de forma remota y no lo habilitaremos para este ejemplo. Para habilitar el acceso remoto, debe especificar el nombre de usuario y la contraseña que se utilizarán al iniciar la sesión de forma remota. Para obtener más información acerca del acceso remoto, consulte [Habilitación del acceso remoto para implementaciones de Azure en Eclipse][].
        El cuadro de diálogo **Publicar en Azure** tendrá un aspecto similar al siguiente:
        ![][ic719488]
1. Haga clic en **Publicar** para publicar en el entorno de ensayo.
    Cuando se le solicite que realice una compilación completa, haga clic en **Sí**.
    La primera compilación puede tardar varios minutos en completarse. Se mostrará un **registro de actividad de Azure** en la sección de vistas por pestañas de Eclipse.
    ![][ic719489]
    Puede usar este registro, así como la vista de **consola** para ver el progreso de la implementación. Una alternativa es iniciar sesión en el [Portal de administración de Azure][] y usar la sección de **servicios en la nube** para supervisar el estado.
1. Cuando la implementación se implementa correctamente, el **registro de actividad de Azure** mostrará el estado **Publicado**. Haga clic en **Publicado**, como se muestra en la siguiente imagen, y el explorador abrirá una instancia de la implementación.
    ![][ic719490]

Dado que se trata de una implementación en un entorno de ensayo, el nombre DNS tendrá el formato http://&lt;*guid*&gt;.cloudapp.net, y la dirección URL contendrá el nombre DNS con un sufijo para la aplicación. Por ejemplo: http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (La parte **MyHelloWorld** distingue mayúsculas de minúsculas). También puede ver el nombre DNS si hace clic en el nombre de la implementación en el Portal de administración de plataforma de Azure (dentro de la parte de Servicios en la nube del Portal de administración).

Aunque este tutorial era para una implementación en el entorno de ensayo, una implementación en producción sigue los mismos pasos, excepto en el cuadro de diálogo **Publicar en Azure**, seleccione **Producción** en lugar de **Ensayo** para el **Entorno de destino**. Una implementación de producción genera una dirección URL basada en el nombre DNS que elija, en lugar de un GUID como se usa para el ensayo.

>[AZURE.WARNING]En este momento, ha implementado la aplicación de Azure en la nube. Sin embargo, antes de continuar, tenga en cuenta que una aplicación implementada, incluso si no se está ejecutando, seguirá a acumulando tiempo facturable para su suscripción. Por lo tanto, es muy importante que elimine las implementaciones no deseadas de su suscripción de Azure.

## Acerca de los proyectos de implementación de Azure ##

Para implementar una o más aplicaciones Java en Azure, se necesita un proyecto de implementación de Azure. Desempeña el rol del "paquete" que las aplicaciones necesitan encapsular para la publicación en Azure.

Además de la información acerca de las aplicaciones, un proyecto de implementación de Azure también contiene información acerca de otros componentes principales de la implementación. Principalmente, el contenedor del servidor de aplicaciones en el que ejecutar la aplicación web y su tiempo de ejecución. Azure admite una gran selección de tiempos de ejecución y servidores de aplicación de Java para elegir.

Aunque el ejemplo usado aquí se ha simplificado en gran medida para fines educativos, un proyecto de implementación de Azure puede contener también otra información de configuración importante que le permita crear servicios en la nube casi arbitrariamente complejos, escalables, altamente disponibles y de varios niveles con sus aplicaciones. Puede habilitar la **afinidad de la sesión ("sesiones permanentes")**, el **almacenamiento en caché rápido**, la **depuración remota**, la **descarga SSL**, el **enrutamiento de puerto/firewall**, **el acceso remoto** y otras capacidades eficaces.

Si ha completado la sección anterior de este tutorial ("Para implementar de forma rápida y sencilla la aplicación en Azure"), ahora verá un nuevo proyecto de implementación de Azure en el Explorador de proyectos generado automáticamente y con el nombre "**MyHelloWorld\_onAzure**".

Podría haber iniciado este tutorial creando primero un proyecto de implementación de Azure vacío y luego agregando las aplicaciones a él. Es un proceso más largo, pero le ofrece más control sobre la configuración inicial desde el principio.

Para crear un nuevo proyecto de implementación de Azure desde cero, haga clic en el botón **Nuevo proyecto de implementación de Azure** ![][ic710876].

Independientemente de si se trabaja con un proyecto de implementación de Azure ya existente o crea uno desde cero, es posible cambiar en cualquier momento y de manera igualmente sencilla su configuración y los componentes, como el JDK o el servidor de aplicaciones.

Para cambiar el JDK o el servidor de aplicaciones, o la lista de aplicaciones en un proyecto de implementación de Azure existente:

1. Expanda el nodo del proyecto (por ejemplo, **MyHelloWorld\_onAzure**) en el Explorador de proyectos.
2. Haga clic con el botón derecho en **WorkerRole1**.
3. Expanda el submenú **Azure** en el menú contextual.
4. Haga clic en **Configuración del servidor**.

Independientemente de si inició estos pasos de configuración del servidor mediante la edición de un proyecto de implementación de Azure existente como se indicó anteriormente, o a través de la creación de uno nuevo desde cero, verá el mismo tipo de cuadros de diálogo que le permitirán configurar el JDK, el servidor y los componentes de aplicaciones. Para obtener más información acerca de cómo cambiar la configuración en los cuadros de diálogo, por ejemplo, para cambiar el JDK y el servidor de aplicaciones, y agregar o quitar aplicaciones en una implementación, vea el artículo [Propiedades de configuración de servidor][].

## Solo Windows: para implementar la aplicación en el emulador de proceso ##

>[AZURE.NOTE]El emulador de Azure solo está disponible en Windows. Omita esta sección si usa un sistema operativo distinto de Windows.

Si ha creado un nuevo proyecto de implementación de Azure siguiendo los pasos descritos anteriormente, es decir, implícitamente, mediante la publicación de la aplicación en Azure, el JDK y los servidores de aplicaciones se configuraron para la nube, pero no para la emulación local. Para preparar el proyecto para la prueba en el emulador local, siga estos pasos:

1. En el Explorador de proyectos de Eclipse, haga clic en **MyHelloWorld\_onAzure**.
1. Haga clic con el botón derecho en **WorkerRole1**.
1. Expanda el submenú **Azure** en el menú contextual.
1. Haga clic en **Configuración del servidor**.
1. En la pestaña **JDK**, compruebe si el kit de herramientas ha configurado previamente un JDK predeterminado. Si no es así, o si desea cambiar los valores predeterminados asumidos, asegúrese de que la casilla **Usar el JDK de esta ruta de acceso de archivo para la prueba localmente** está activada y se especifica la ubicación de instalación del JDK que desea usar. Si desea cambiarlo, haga clic en el botón **Examinar** y, con el control de cuadro de búsqueda, seleccione la ubicación del directorio del JDK que vaya a usar.
1. Haga clic en la pestaña **Servidor**.
1. En el cuadro de texto **Ruta de acceso del servidor local** en la parte inferior del cuadro de diálogo, escriba la ruta de acceso de un servidor instalado localmente que coincida con el tipo y el número de versión principal del servidor seleccionado en la parte superior del cuadro de diálogo, en la casilla **Implementar un servidor de este tipo**. Si desea usar un tipo diferente o la versión principal del servidor de aplicaciones, cambie la selección en esa casilla primero.
1. Haga clic en **Aceptar**.
1. En la barra de herramientas de Eclipse, haga clic en el botón **Ejecutar en Emulador de Azure**, botón ![][ic710879]. Si el botón **Ejecutar en Emulador de Azure** no está habilitado, asegúrese de que se seleccione **MyHelloWorld\_onAzure** en el Explorador de proyectos de Eclipse y asegúrese de que este se encuentre como ventana actual. Esto hará que primero comience una compilación completa del proyecto y, a continuación, se inicie la aplicación web de Java en el emulador de proceso. (Tenga en cuenta que, dependiendo de las características de rendimiento de su equipo, la primera compilación puede tardar algunos minutos, pero las compilaciones sucesivas se realizarán más rápido). Una vez que se haya completado el paso de la primera compilación, el Control de cuentas de usuario (UAC) de Windows le solicitará que permita que este comando realice cambios en el equipo. Haga clic en **Sí**.

>[AZURE.IMPORTANT] Si no ve la solicitud de UAC, active la barra de tareas de Windows para el icono UAC y haga clic primero. A veces no se muestra la solicitud de UAC en la ventana de nivel superior, sino que puede verse solo como icono de la barra de tareas.

1. Examine los resultados de la interfaz de usuario del emulador de proceso para determinar si hay algún problema con el proyecto. Según el contenido de la implementación, su aplicación puede tardar un par de minutos en iniciarse al completo en el emulador de proceso.
1. Inicie el explorador y use la dirección URL `http://localhost:8080/MyHelloWorld` como la dirección (la parte `MyHelloWorld` de la dirección URL distingue mayúsculas de minúsculas). La aplicación MyHelloWorld (la salida de index.jsp) debe ser similar a la siguiente imagen:
    ![][ic589579].

Cuando esté listo para detener la ejecución de la aplicación en el emulador de proceso, haga clic en el botón **Restablecer Emulador de Azure**, ![][ic710880] en la barra de herramientas de Eclipse.

## Para eliminar la implementación ##

Para eliminar la implementación en el kit de herramientas de Azure para Eclipse, asegúrese de que **MyHelloWorld\_onAzure** está seleccionado en el Explorador de proyectos de Eclipse y de que el explorador de proyectos de Eclipse esté en la ventana actual y luego haga clic en el botón **Anular publicación**, ![][ic710883], en la barra de herramientas de Eclipse. (Podría hacer la misma operación haciendo clic con el botón derecho en **MyHelloWorld\_onAzure** en el Explorador de proyectos de Eclipse, haciendo clic en **Azure** y luego haciendo clic en **Anular la implementación de nube de Azure**). Se mostrará el cuadro de diálogo **Anular publicación de proyecto de Azure**.

![][ic719491]

Seleccione el servicio en la nube y la suscripción que contienen la implementación, seleccione la implementación que desee eliminar y luego haga clic en **Anular publicación**.

(Una alternativa al uso del kit de herramientas para eliminar la implementación es usar la sección **Servicios en la nube** del Portal de administración de Azure: vaya a la implementación, selecciónela y luego haga clic en el botón **Eliminar**. Se detendrá el proceso y luego se eliminará la implementación. Si solo desea detener la implementación y no eliminarla, haga clic en el botón **Detener** en lugar de **Eliminar**, pero tal como se mencionó anteriormente, si no se elimina la implementación, se seguirán aplicando costos facturables para la implementación a pesar de que se haya detenido).

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Instalación del kit de herramientas de Azure para Eclipse][]

[Novedades del kit de herramientas de Azure para Eclipse][]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de administración de Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Habilitación del acceso remoto para implementaciones de Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Instalación del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propiedades de configuración de servidor]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Novedades del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png

<!---HONumber=AcomDC_0114_2016-->