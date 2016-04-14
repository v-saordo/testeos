<properties
    pageTitle="Propiedades de roles de Azure"
    description="Aprenda a usar el kit de herramientas de Azure para Eclipse para configurar roles de Azure."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# Propiedades de roles de Azure #

En el kit de herramientas de Azure para Eclipse se pueden establecer varias opciones de configuración para un rol de Azure.

## Configuración de propiedades de roles de Azure ##

La configuración de las propiedades de los roles de Azure se realiza desde los cuadros de diálogo de propiedades del rol de trabajo. Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse y seleccione el submenú **Azure**. (Si no ve el rol en el Explorador de proyectos de Eclipse, expanda el proyecto de Azure en el Explorador de proyectos.)

![][ic789599]

Las distintas propiedades que se pueden establecer desde los cuadros de diálogo de **Propiedades** se describen en este tema. Tenga en cuenta que muchas de las propiedades se rellenan automáticamente al crear un nuevo proyecto de implementación de Azure.

Las siguientes páginas de propiedades están disponibles para los roles de Azure.

* [Propiedades de máquina virtual](#virtual_machine_properties)
* [Propiedades de almacenamiento en caché](#caching_properties)
* [Propiedades de certificados](#certificates_properties)
* [Propiedades de componentes](#components_properties)
* [Propiedades de depuración](#debugging_properties)
* [Propiedades de puntos de conexión](#endpoints_properties)
* [Propiedades de variables de entorno](#environment_variables_properties)
* [Propiedades de equilibrio de carga y de afinidad de la sesión (conocidas también como "sesiones temporales")](#session_affinity_properties)
* [Propiedades de almacenamiento local](#local_storage_properties)
* [Propiedades de configuración de servidor](#server_configuration_properties)
* [Propiedades de la descarga de SSL](#ssl_offloading_properties)
	
<a name="virtual_machine_properties"></a>
### Propiedades de máquina virtual ###

Haga clic en el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure**, y, a continuación, en **Propiedades** y tendrá la capacidad de cambiar tanto el tamaño de la máquina virtual como el número de instancias, como se muestra en la siguiente imagen.

![][ic719499]

>[AZURE.NOTE]Solo Windows: si se asigna a varias instancias un valor mayor que 1 y, además, se configura un servidor de aplicaciones, el kit de herramientas solo permitirá que se ejecute una instancia de rol en el emulador, independientemente de cuál sea el valor. Esto es para evitar conflictos de enlaces de puerto entre las distintas instancias del servidor (por ejemplo, que todas intenten enlazar con el puerto 8080) cuando se ejecutan en el mismo equipo. La configuración del número de instancias deseadas se mantiene, pero solo entra en vigor cuando se realiza la implementación en la nube.

<a name="caching_properties"></a>
### Propiedades de almacenamiento en caché ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Almacenamiento en caché**. En este cuadro de diálogo, es posible habilitar memorias caché colocadas y compatibles con Memcache con nombre, lo que le permite acelerar las aplicaciones web.

![][ic719483]

En la página de propiedades de **Almacenamiento en caché**, puede especificar la configuración global de los siguientes factores:

* Si el almacenamiento en caché colocado está habilitado.
* El tamaño de la caché como un porcentaje de la memoria.
* El nombre de la cuenta de almacenamiento para guardar el estado de la caché cuando la aplicación se ejecuta como un servicio en la nube, o bien la opción ninguno si no desea guardar el estado de la memoria caché. (El nombre de la cuenta de almacenamiento cuando la aplicación se ejecuta en el emulador de proceso.) Si establece el nombre de la cuenta de almacenamiento en **(automático)** (que es el valor predeterminado), la configuración del almacenamiento en caché usará automáticamente la cuenta de almacenamiento que se selecciona en el cuadro de diálogo **Publicar en Azure**.

>[AZURE.NOTE]El valor **(automático)** solo tendrá el efecto deseado solo si la implementación se publica la mediante el Asistente para publicación del kit de herramientas de Eclipse. Si se publica el archivo .cspkg de forma manual con un mecanismo externo, como el [Portal de administración de Azure][], la implementación no funcionará correctamente.

El siguiente cuadro de diálogo muestra las propiedades de una memoria caché.

![][ic719501]

* **Nombre:** el nombre de la caché colocada.
* **Número de puerto:** el número de puerto que se va a usar para la caché.
* **Directiva de caducidad:** uno de los siguientes valores que especifica cuándo expira una clave en la memoria caché.
    * **Absoluta:** la clave expira cuando se alcanza el tiempo que se especifica en **Minutos de vida**.
    * **Nunca expira:** la clave no tiene un tiempo de expiración.
    * **Ventana deslizante:** la clave expira si no se ha accedido a ella durante el tiempo que se especifica en **Minutes to live**; cada vez que se accede a ella, se restablece el reloj de expiración.
* **Minutos de vida:** número máximo de minutos que dura una clave de Memcache, sujeto a la directiva de expiración.
* **Alta disponibilidad con copias de seguridad replicadas en distintas instancias de rol:** si esta opción está habilitada, ayuda a proporcionar alta disponibilidad mediante copias de seguridad replicadas en distintas instancias de rol. Tenga en cuenta que para que la implementación de esta característica funcione es preciso que estén en vigor al menos dos instancias de rol.

Para agregar una nueva memoria caché, haga clic en el botón **Agregar** de la página de propiedades **Almacenamiento en caché** y se abrirá el cuadro de diálogo **Configurar caché con nombre**. Especifique los valores de las propiedades que se han descrito anteriormente.

Para modificar una caché con nombre, selecciónela y haga clic en el botón **Editar** de la página de propiedades **Almacenamiento en caché**. Se abrirá un cuadro de diálogo en el que puede modificar las propiedades de la memoria caché. Haga clic en **Aceptar** para guardar los valores de la memoria caché.

Para eliminar una memoria caché, selecciónela y haga clic en el botón **Quitar**de la página de propiedades **Almacenamiento en caché** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Para más información sobre cómo usar el almacenamiento en caché, consulte [Uso de almacenamiento en caché colocado][].

<a name="certificates_properties"></a>
### Propiedades de certificados ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Certificados**.

![][ic710964]

En este cuadro de diálogo, puede agregar o quitar certificados a los que se haga en el proyecto de Eclipse. Tenga en cuenta que los certificados que se enumeran aquí no se almacenan automáticamente en ningún almacén de claves de Java y, por consiguiente, no están disponibles automáticamente para cualquier uso en una aplicación de Java. Solo se registran en Azure para que se puedan precargar en el almacén de certificados de Windows de las máquinas virtuales que ejecutan la implementación y para que, posteriormente, los use otro software de Windows. Actualmente, la única característica del kit de herramientas que usa los certificados a los que se hace referencia de esta forma en el cuadro de diálogo **Certificados** es [SSL Offloading][], debido a su dependencia de Internet Information Services (IIS) y Enrutamiento de solicitud de aplicaciones (ARR), que requieren que el certificado correcto esté disponible de esta manera.

Al implementar el proyecto en Azure mediante el Asistente para publicación, se le solicitará que apunte a los archivos de intercambio de información personal (PFX) correspondientes a estos certificados, junto con sus contraseñas, para cargarlos automáticamente en el servicio de Azure, pero solo si ha no se han cargado previamente.

<a name="components_properties"></a>
### Propiedades de componentes ###

Abra el menú contextual del rol del panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Componentes**. En este cuadro de diálogo, puede agregar, modificar o quitar los componentes de su rol, así como cambiar el orden en que se procesan.

![][ic719502]

La característica de los componentes permite agregar dependencias al proyecto de implementación de Azure, como proyectos de aplicaciones Java, archivos especiales y las instrucciones de línea de comandos ejecutables que necesita la implementación.

Para cada componente, se puede especificar:

* El paso que debe seguirse al importar el componente en el proyecto de implementación de Azure cuando este se genere.
* El paso que debe seguirse al implementar dicho componente en la nube de Azure.

>[AZURE.NOTE]Al especificar archivos de componente o líneas de comandos, tenga en cuenta que la implementación se publicará en una máquina virtual de Windows, por lo que los pasos personalizados deben ser válidos para cualquier sistema operativo Windows.

Los componentes tienen las siguientes propiedades:

* **Importar:** método que indica la forma en que el componente se importará en el proyecto cuando se compila el proyecto. Puede ser uno de los siguientes valores:
    * **copy:** se copia el componente de la ruta de acceso local especificada por la propiedad **De** el directorio **approot** del rol.
    * **EAR:** el componente es un archivo EAR (Java Enterprise Archive) importado de un Enterprise Application Project en la ruta de acceso local especificada por la propiedad **De**. (Esto lo detecta automáticamente el kit de herramientas en función de la naturaleza del proyecto de esa ubicación.)
    * **JAR:** el componente es un archivo JAR (Java Archive) y se importa de un proyecto de Java en la ruta de acceso local especificada por la propiedad **De**. (Esto lo detecta automáticamente el kit de herramientas en función de la naturaleza del proyecto de esa ubicación.)
    * **none:** se realiza ninguna acción para importar el componente. Esto se puede aplicar cuando se asuma que el componente ya esta presente en el directorio **approot** del rol o cuando el componente es simplemente una instrucción de línea de comandos ejecutable, como se especifica en la propiedad **As** cuando el método **Implementar** sea **exec**.
    * **WAR:** el componente es un archivo WAR (Java Web Application Archive) y se importa de un proyecto web dinámico en la ruta de acceso local especificada por la propiedad **De**. (Esto lo detecta automáticamente el kit de herramientas en función de la naturaleza del proyecto de esa ubicación.)
    * **zip:** el componente es un archivo zip y se importa mediante la compresión del directorio o archivo especificado por la propiedad **De**.
* **De:** ruta de origen en el equipo local a la carpeta o archivo que representan los elementos que desea importar en la implementación. Las variables de entorno de Windows se pueden usar en esta propiedad. Todos los componentes importables se importarán en el directorio **approot** del rol al generar el proyecto.
	
	Tenga en cuenta que tiene la capacidad de implementar un componente de una descarga al realizar la implementación en la nube (no en el emulador de proceso). Consulte la información relacionada que aparece a continuación relativa a la adición de componentes.
	
* **Como:** nombre de archivo bajo el que se importará el componente en el directorio **approot** del rol y, en último término, se implementará en la nube de Azure. Deje esta propiedad en blanco para que el nombre sea el mismo que en la máquina local. (En el caso de los componentes ejecutables, es decir, aquellos en los que el método **Implementar** está establecido en **exec**, puede tratarse de una instrucción arbitraria de la línea de comandos de Windows.)

	>[AZURE.IMPORTANT]Si usa caracteres de espacio para este valor, se controlarán de manera diferente en función del método de implementación. Si el método de implementación es **exec**, los espacios se interpretarán como separadores de argumentos de la línea de comandos, no como parte del nombre de archivo. En los restantes métodos de implementación, los espacios se interpretarán como parte del nombre de archivo.
	
* **Implementar:** método que indica la acción que se aplica al componente cuando se inicia la implementación. Puede ser uno de los siguientes valores:
    * **copy:** el componente se copia en la ruta de acceso de destino especificada por la propiedad **Para**.
    * **exec:** el componente es una instrucción ejecutable de línea de comandos de Windows que se ejecuta en el contexto de la ruta de acceso especificada por la propiedad **Para** en el momento en que se inicia la implementación.
    * **none:** no se aplican acciones al componente cuando se inicia la implementación.
    * **zip:** el componente se descomprime en la ruta de acceso de destino especificada por la propiedad **Para**. Este método solo está disponible cuando la propiedad **Importar** es **zip**.
* **Para:** ruta de destino en la máquina virtual donde se implementará el componente. Las variables de entorno de Windows se pueden usar en esta propiedad y las rutas de acceso del archivo son relativas a **approot**.
	
Para agregar un nuevo componente, haga clic en el botón **Agregar** de la página de propiedades **Componentes** y se abrirá el cuadro de diálogo **Componente de rol de Azure**. Especifique los valores de las propiedades que se han descrito anteriormente.

A continuación se muestra un ejemplo para agregar un nuevo componente WAR.

![][ic719503]

Al realizar la implementación en la nube (no en el emulador de proceso), si desea implementar el componente a partir de una descarga, asegúrese que la casilla **Cuando esté en la nube, en lugar de incluir en el paquete, implementar desde** está activada. Si desea realizar la descarga desde su cuenta de almacenamiento de Azure, seleccione dicha cuenta en la lista desplegable **Cuenta de almacenamiento** (puede hacer clic en el vínculo **Cuentas** para modificar lo que hay en la lista), con lo que se rellenará parcialmente el campo **URL** y, a continuación, rellene el resto de la dirección URL. Si no desea usar el almacenamiento de Azure, seleccione **(ninguno)** en la lista desplegable **Cuenta de almacenamiento** y escriba la dirección URL del componente en el campo **URL**. Especifique uno de los siguientes métodos:

* **copy:** el componente de descarga se copia en la ruta de acceso de destino especificada por la ruta **Al directorio**.
* **same:** se usa el mismo método para **Implementar desde descarga** que para **Implementar desde paquete**.
* **zip:** el componente de descarga se descarga en la ruta de acceso de destino especificada por la ruta **Al directorio**.

Para modificar un componente, selecciónelo y haga clic en el botón **Editar** de la página de propiedades **Componentes**. Se abrirá un cuadro de diálogo que le permite modificar las propiedades del componente. Haga clic en **Aceptar** para guardar los valores del componente.

Para eliminar un componente, selecciónelo y haga clic en el botón **Quitar**de la página de propiedades **Componentes** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Las reglas se procesan en el orden de la lista. Utilice los botones **Subir** y **Bajar** para organizar el orden.

>[AZURE.NOTE]Esta función de configuración de servidor también se basa en los componentes. Estos componentes no se pueden quitar ni modificar sin quitar la configuración del servidor correspondiente. Se le preguntará al respecto al realizar cambios en dichos componentes.

<a name="debugging_properties"></a>
### Propiedades de depuración ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Depuración**. Desde este cuadro de diálogo puede habilitar o deshabilitar la depuración remota, así como crear configuraciones de depuración, como se muestra en la siguiente imagen.

![][ic719504]

Para más información acerca de la depuración, consulte [Depuración de aplicaciones de Azure en Eclipse][].

<a name="endpoints_properties"></a>
### Propiedades de puntos de conexión ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Extremos**. Desde este cuadro de diálogo puede crear un punto de conexión, así como editar o quitar un punto de conexión, como se muestra en la siguiente imagen.

![][ic719505]

Para agregar un punto de conexión, haga clic en el botón **Agregar** de la página de propiedades **Extremos** y se abrirá el cuadro de diálogo **Agregar extremo**.

![][ic710897]

Escriba el nombre del punto de conexión, seleccione el tipo (**Entrada**, **Interno** o **InstanceInput**) y especifique los puertos público y privado. Presione **Aceptar** para guardar los nuevos valores del punto de conexión.

En función del punto de conexión, puede usar intervalos de puertos de la siguiente manera:

* En el punto de conexión de una instancia de entrada, el puerto público puede ser un intervalo de puertos (por ejemplo, **2000-2010**) y el puerto privado es un valor fijo.
* En un punto de conexión interno, no se usa el puerto público y el puerto privado puede ser un intervalo, dejarse en blanco o mostrar un asterisco para indicar que Azure lo establece automáticamente.
* En los puntos de conexión internos, el puerto público solo puede ser un valor fijo y el puerto privado puede ser un valor fijo, dejarse en blanco o mostrar un asterisco para indicar que Azure lo establece automáticamente.

Si desea usar un número de puerto individual en lugar de un intervalo, deje el cuadro de texto para el final del espacio en blanco del intervalo.

En los puertos que se establecen en automático, si necesita determinar qué puerto se usa en tiempo de ejecución, la aplicación puede usar la API del tiempo de ejecución del servicio de Azure, que se documenta en el [resumen del paquete com.microsoft.windowsazure.serviceruntime][].

Para ver cómo se pueden usar los puntos de conexión de entrada de instancias como ayuda para la depuración de una implementación de instancias múltiples, consulte [Depuración de una instancia de rol específica en una implementación de varias instancias][].

Para modificar un punto de conexión, selecciónelo y haga clic en el botón **Editar** de la página de propiedades **Extremos**. Se abrirá un cuadro de diálogo que le permite modificar el nombre y tipo del punto de conexión, así como sus puertos público y privado. Presione **Aceptar** para guardar los valores modificados del punto de conexión.

Para eliminar un punto de conexión, selecciónelo y haga clic en el botón **Quitar**de la página de propiedades **Extremos** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Para configurar correctamente algunas de las características (como el almacenamiento en caché, la depuración remota, la afinidad de sesiones o la descarga de SSL) habilitadas por el usuario en un rol, el kit de herramientas puede configurar automáticamente los puntos de conexión especiales que se enumerarán junto con los puntos de conexión definidos por el usuario. El kit de herramientas evita que los usuarios editen o eliminen los puntos de conexión generados automáticamente, siempre que la función asociada esté habilitada.

<a name="environment_variables_properties"></a>
### Propiedades de variables de entorno ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Variables de entorno**. Desde este cuadro de diálogo puede crear una variable de entorno, así como modificar o quitar una variable de entorno, como se muestra en la siguiente imagen.

![][ic719506]

Las variables de entorno están disponibles para el script de inicio al iniciar el rol.

>[AZURE.NOTE]Al especificar variables de entorno, tenga en cuenta que la implementación se publicará en una máquina virtual de Windows, por lo que las variables de entorno deben ser válidas para cualquier sistema operativo Windows.

Como ejemplo de variable de entorno disponible cuando se inicia el rol, cree una nueva variable de entorno haciendo clic en el botón **Agregar**. A continuación, se muestra variable de entorno denominada **MyRoleVersion** que se creó y a la que se asignó el valor **1.0**.

![][ic659268]

En el código jsp, puede mostrar el valor mediante el método `System.getenv`:

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Lo que genera este resultado cuando se ejecuta la aplicación:

![][ic552233]

Para modificar una variable de entorno, selecciónela y haga clic en el botón **Editar** de la página de propiedades **Variables de entorno**. Se abrirá un cuadro de diálogo que le permite modificar las propiedades de las variables de entorno. Presione **Aceptar** para guardar los valores de las variables de entorno.

Para eliminar una variable de entorno, selecciónela y haga clic en el botón **Quitar** de la página de propiedades **Variables de entorno** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

Para configurar correctamente algunas de las características (como el configuración de servidor, la depuración remota o el almacenamiento local) habilitadas por el usuario en un rol, el kit de herramientas puede configurar automáticamente las variables de entorno especiales que se enumerarán junto con las variables de entorno definidas por el usuario. El kit de herramientas evita que los usuarios editen o eliminen las variables de entorno generadas automáticamente, siempre que la función asociada esté habilitada.

<a name="session_affinity_properties"></a>
### Propiedades de equilibrio de carga y de afinidad de la sesión (conocida también como "sesiones temporales") ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Equilibrio de carga**. Desde este cuadro diálogo se puede habilitar o deshabilitar la afinidad de la sesión, como se muestra en la siguiente imagen.

![][ic719492]

Para más información relacionada, consulte [Enable Session Affinity][]. Además, observe el comportamiento de esta característica en el contexto de la descarga de SSL, como se describe en [SSL Offloading][].

<a name="local_storage_properties"></a>
### Propiedades de almacenamiento local ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Almacenamiento local**. Desde este cuadro de diálogo se puede crear, modificar o quitar el almacenamiento local temporal de la máquina virtual en que se ejecuta su aplicación. Se pueden establecer valores específicos para el tamaño del almacenamiento local, así como si el contenido se conserva cuando se recicla el rol, como se muestra en la siguiente imagen.

![][ic719508]

Opcionalmente, también puede especificar una variable de entorno que se corresponda con el almacenamiento local.

De manera predeterminada, todo lo que se implementa en Azure se coloca (y se descomprime) en la carpeta **approot** de la instancia de rol. Aunque las implementaciones más básicas se ajustarán a dicho directorio después de descomprimirse, el espacio asignado al directorio **approot** es limitado y no está bien definido (menos de 1 GB es una regla general razonable). Por consiguiente, para asegurarse de que Azure asigna espacio en disco suficiente para implementaciones mayores que podrían no caber en la carpeta **approot**, debe configurar un recurso de almacenamiento local desde el cuadro de diálogo **Almacenamiento local**. Para conocer una manera fácil de hacerlo, consulte [Deploying Large Deployments][].

Puede hacer referencia fácilmente al recurso de almacenamiento desde los scripts de inicio (por ejemplo, **startup.cmd**) mediante la variable de entorno que el kit de herramientas asocia automáticamente con el recurso, como se muestra en el cuadro de diálogo **Almacenamiento local**. Dicha variable de entorno contendrá la ruta de acceso completa del recurso local que ha configurado en el momento en que se ejecuta el script de inicio.

Para modificar un recurso de almacenamiento local, selecciónelo y haga clic en el botón **Editar** de la página de propiedades **Almacenamiento Local**. Se abrirá un cuadro de diálogo que le permite modificar las propiedades del recurso de almacenamiento local. Presione **Aceptar** para guardar los valores del recurso de almacenamiento local.

Para eliminar un recurso de almacenamiento local, selecciónelo y haga clic en el botón **Quitar** de la página de propiedades **Almacenamiento Local** y, a continuación, haga clic en **Sí** para confirmar la eliminación.

<a name="server_configuration_properties"></a>
### Propiedades de configuración de servidor ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Configuración del servidor**. Desde este cuadro de diálogo se puede agregar, quitar y modificar el servidor de aplicaciones JDK y Java que usa la implementación, así como agregar o quitar las aplicaciones (como los archivos WAR, JAR o EAR) que usa la implementación.

### Configuración de JDK ###

Este cuadro de diálogo permite especificar el paquete de JDK que se usará para la implementación. Si usa Eclipse en Windows, puede especificar el paquete de JDK que se usará localmente cuando se ejecute en el emulador de Azure y tiene la opción de implementar dicha instalación local en Azure. En los sistemas operativos distintos de Windows, no se puede aplicar la configuración de JDK del emulador y no se puede implementar el JDK instalado localmente, ya que no es compatible con Windows. Sin embargo, independientemente del sistema operativo que use, siempre puede elegir los paquetes de JDK de terceros que desea implementar en Azure, o bien apuntar a su propio paquete de JDK compatible con Windows desde una ubicación de descarga alternativa.

A continuación se muestra un ejemplo de cómo se puede especificar un JDK en Windows:

![][ic780647]

Si usa Eclipse en Windows, puede especificar el JDK que se usará con el emulador de proceso; para ello, asegúrese de que se ha marcado; Para ello, asegúrese de que se ha activado **Usar JDK de esta ruta de archivo para realizar pruebas localmente** en la sección **Implementación del emulador**. A continuación, especifique la ruta de acceso local a su JDK; puede examinar otro JDK si el que desea usar no se selecciona automáticamente. También tiene la opción de implementar su JDK en su servicio en la nube de Azure; para ello, seleccione la opción **Implementar mi JDK local (carga automática en almacenamiento en la nube)** en la sección **Implementación de la nube**.

Nota: En sistemas operativos distintos de Windows, la configuración de **Implementación del emulador** y la opción **Implementar mi JDK local** no están disponibles. En el siguiente ejemplo se muestra cómo se especifica un JDK en Mac o en otro sistema operativo de distinto de Windows:

![][ic789643]

Independientemente del sistema operativo en el que se encuentre, tiene las dos opciones de **Implementación de la nube** siguientes para el origen y el tipo de su paquete de JDK:

* **Implementar un paquete JDK de terceros disponible en Azure** 
* **Implementar desde una descarga personalizada** 

Si usa la opción **Implementar un paquete JDK de terceros disponible en Azure**:

1. Active la casilla **Implementar un paquete JDK de terceros disponible en Azure**.
1. En la lista desplegable, seleccione el paquete de JDK de terceros que esté disponible en Azure.
1. La pestaña **JDK** tendrá un aspecto similar al siguiente en Windows: ![][ic780648] Y tendrá un aspecto similar al siguiente en Mac OS o en otros sistemas operativos distintos de Windows: ![][ic789643]
1. Haga clic en **Aceptar** para guardar los cambios.
1. Cuando se le pida que acepte el contrato de licencia del proveedor del paquete de JDK de terceros, revise los términos de la licencia. Si acepta dichos términos, haga clic en **Sí** para cerrar el cuadro de diálogo **Aceptar el contrato de licencia**. Tenga en cuenta que la lógica subyacente que indica qué elementos aparecen en la lista desplegable de la opción **Implementar un paquete JDK de terceros disponible en Azure** se puede personalizar. Para personalizar los elementos, en el cuadro de diálogo **JDK**, haga clic en el vínculo **Personalizar**. Se cerrará la página de propiedades **JDK** y se abrirá el archivo **componentsets.xml** en Eclipse, que posteriormente podrá modificar según sea necesario. La documentación de **componentsets.xml** se incluye en el propio archivo **componentsets.xml**.

Si usa la opción **Implementar un JDK desde una descarga personalizada**:

1. Cree un archivo ZIP del directorio de instalación de JDK y asegúrese de que el nodo del directorio es el elemento secundario de la estructura del archivo ZIP, no su contenido. Anote el nombre del directorio, ya que lo necesitará más adelante, y tenga en cuenta que esta instalación de JDK se implementará en una máquina virtual de Windows.
1. Cargue el archivo ZIP en su cuenta de almacenamiento de Azure como un blob. Para ello, puede usar cualquier herramienta externa para cargar blobs en Almacenamiento de Azure. Se recomienda usar un blob privado. Anote de la dirección URL del blob del contenido del archivo ZIP.
1. Active la casilla **Implementar un JDK desde una descarga personalizada**. Si desea realizar la descarga desde su cuenta de almacenamiento de Azure, seleccione dicha cuenta en la lista desplegable **Cuenta de almacenamiento** (puede hacer clic en el vínculo **Cuentas** para modificar lo que hay en la lista), con lo que se rellenará parcialmente el campo **URL** y, a continuación, rellene el resto de la dirección URL. Si no desea usar Almacenamiento de Azure, seleccione **(none)** en la lista desplegable **Cuenta de almacenamiento** y escriba la dirección URL de la descarga de JDK en el campo **URL**. Si usa el almacenamiento de Azure, los nombres de blob de la dirección URL deben escribirse en minúscula.
1. Asegúrese de que el cuadro de texto **JAVA\_HOME** hace referencia al nombre de directorio correcto. De forma predeterminada, hará referencia al mismo nombre de directorio de JDK que el valor que eligió para su uso local. Pero si el directorio que se incluye en el archivo ZIP tiene un nombre distinto (por ejemplo, porque se use una versión diferente), actualice el nombre del directorio en el cuadro de texto **JAVA\_HOME** en consecuencia, ya que esta configuración se usará en la nube (no en el emulador de proceso).
1. Haga clic en **Aceptar** para guardar los cambios.

Eso es todo. A partir de ese momento, si realiza alguna compilación para la nube, observará que el tamaño del paquete será mucho menor, que el proceso de compilación normalmente tardará menos tiempo y que la propia implementación al publicar en la nube también tardará menos tiempo. Tenga en cuenta que las opciones **Implementar mi JDK local (carga automática en almacenamiento en la nube)** o **Implementar desde una descarga personalizada** solo entrarán en vigor cuando la aplicación se implemente en la nube. No tienen ningún efecto en su experiencia con el emulador de proceso; la versión local de los componentes se seguirá usando cuando la implementación se realice en el emulador de proceso.

### Configuración del servidor ###

A continuación se muestra un ejemplo de cómo se puede especificar un servidor de aplicaciones.

![][ic796926]

Compruebe que la casilla **Implementar un servidor de este tipo** está activada y, a continuación, elija el tipo de servidor de aplicaciones que desea usar.

Para especificar el servidor que se usará para la implementación en la nube, puede aprovechar las siguientes opciones:

1. **Implementar un servidor de terceros disponible en Azure**: esta opción es especialmente aplicable a escenarios de desarrollo y pruebas, donde la prioridad es la eficacia y simplicidad de la implementación, y el servidor no requiere una configuración personalizada. O bien, cuando se desea usar uno de los servidores como punto de partida, pero se incluyen los pasos de personalización de servidor pertinentes en la lógica de inicio de la implementación.
1. **Implementar desde una descarga personalizada**: es especialmente aplicable en escenarios de producción, cuando se tiene un servidor especialmente configurado y preparado que se desea usar en la nube.
1. **Implementar mi instalación de servidor local**: se aplica especialmente si la configuración de instalación del servidor local está personalizada para el usuario. Si se elige esta opción, también es preciso especificar la ruta de acceso del servidor local en el cuadro de texto **Ruta de acceso del servidor local**.

Si usa la opción **Implementar un servidor de terceros disponible en Azure**:

1. Active la casilla **Implementar un servidor de terceros disponible en Azure**.
1. En el menú desplegable, seleccione el software de servidor que desee usar en la implementación en la nube. Tenga en cuenta que si ya ha especificado el tipo de servidor que va a usar, solo podrá elegir un servidor de nube de la misma familia que el especificado. Sin embargo, si no lo ha especificado, podrá elegir cualquiera de los servidores disponibles actualmente en Azure y el tipo de servidor se seleccionará automáticamente.
1. Haga clic en **Aceptar** para guardar los cambios.

Si usa la opción **Implementar desde una descarga personalizada**:

1. Asegúrese de que ha seleccionado el tipo de servidor según los pasos anteriores. Esto indica al complemento cómo implementar el servidor desde una descarga personalizada, ya que debe pertenecer a la misma familia que el tipo de servidor seleccionado.
1. Active la casilla **Implementar desde una descarga personalizada**. Si desea realizar la descarga desde su cuenta de almacenamiento de Azure, seleccione dicha cuenta en la lista desplegable **Cuenta de almacenamiento** (puede hacer clic en el vínculo **Cuentas** para modificar lo que hay en la lista), con lo que se rellenará parcialmente el campo **URL** y, a continuación, rellene el resto de la dirección URL del archivo ZIP de descarga del servidor (si se usan Almacenamiento de Azure o nombres de blob en la URL, esta debe escribirse en minúsculas). Si no desea usar el almacenamiento de Azure, seleccione **(none)** en la lista desplegable **Cuenta de almacenamiento** y escriba la dirección URL del archivo ZIP de descarga del servidor en el campo **URL**. El archivo ZIP contendría una carpeta secundaria que representa el directorio de instalación del servidor de aplicaciones. Por ejemplo, si usa un archivo zip para Apache Tomcat 7.0.35, dicho archivo contendría la carpeta secundaria que representa el directorio de instalación, como **tomcat-apache-7.0.35**. 
1. Especifique el valor de la variable de entorno del directorio de inicio. De manera predeterminada, será el valor que se usa para el servidor de aplicaciones local, en caso de haber alguno, pero si el servidor de aplicaciones en la nube es distinto del local, se puede especificar otro valor. Sin embargo, es preciso asegurarse de que el servidor de aplicaciones en la nube es de la misma familia que el tipo de servidor seleccionado anteriormente. Si actualiza el archivo zip del servidor de aplicaciones de nube en el futuro, puede cambiar manualmente la configuración del directorio particular, o bien, hacer que vuelva a coincidir con la configuración local (si también cambió el servidor de aplicaciones local).
1. Haga clic en **Aceptar** para guardar los cambios.

La lógica subyacente que indica qué elementos aparecen en la pestaña **Server** de la página de propiedades **Configuración del servidor** se puede personalizar. Se trata de una característica avanzada que podría necesitar si sus necesidades van más allá de los valores predeterminados o si desea agregar otros servidores. Para personalizar la lógica, en el cuadro de diálogo **Servidor**, haga clic en el vínculo **Personalizar**. Así se cerrará la página de propiedades **Configuración del servidor** y se abrirá abra el archivo **componentsets.xml** en Eclipse, que posteriormente puede modificarse según sea necesario para extender la plantilla de configuración del servidor. La documentación de **componentsets.xml** se incluye en el propio archivo **componentsets.xml**.

Si usa la opción **Implementar mi servidor local (carga automática en almacenamiento en la nube)**:

1. Active la casilla **Implementar mi servidor local (carga automática en almacenamiento en la nube)**.
1. En la lista desplegable **cuenta de almacenamiento**, seleccione **(automático)**. Si especifica **(automático)**, el kit de herramientas de Eclipse usará para el servidor la misma cuenta de almacenamiento que la que seleccione para la implementación en el cuadro de diálogo **Publicar en Azure**.
1. Haga clic en **Aceptar** para guardar los cambios.

Seleccione una ruta de instalación del servidor en el equipo en el cuadro de texto **Ruta de acceso del servidor Local** si se cumple cualquiera de las condiciones siguientes:

* Desea probar la implementación en el emulador (se aplica solo a Windows).
* Desea implementar en la nube el servidor instalado localmente.
* Desea usar una descarga de servidor personalizado propia en la nube, en cuyo caso, asegúrese también de que la opción **Implementar mi servidor local (carga automática en almacenamiento en la nube)** está seleccionada.

Si ninguna de las opciones anteriores se aplican a su situación, la configuración del servidor local es opcional.

### Configuración de aplicaciones ###

A continuación se muestra un ejemplo de cómo se puede especificar una aplicación.

![][ic719512]

Haga clic en **Agregar** para agregar otra aplicación o en **Quitar** para quitar una aplicación. Por eficiencia, si desea usar una descarga como origen de una aplicación al implementarla en la nube, use las [propiedades de los componentes](#components_properties) para especificar una dirección URL, cuenta de almacenamiento, etc.

A partir de la versión de abril de 2014, las aplicaciones se cargan automáticamente en la cuenta de almacenamiento (del contenedor **eclipsedeploy**) seleccionada para la implementación. La lógica de inicio de la implementación contiene un paso que primero descarga las aplicaciones desde la cuenta de almacenamiento. Esto significa que puede actualizar las aplicaciones en la implementación sin que sea preciso volver a generar e implementar todo el paquete mediante la carga manual de las versiones más recientes de la aplicación directamente en dicha cuenta de almacenamiento (por ejemplo, mediante el uso del Portal de Azure) y la sustitución de los archivos WAR cargados originalmente por parte del kit de herramientas. A continuación, simplemente inicie el reciclado de todas las instancias de rol mediante el Portal de administración de Azure o de las utilidades de la línea de comandos. (Actualmente no se admite la activación del reciclado del rol directamente desde el kit de herramientas de Eclipse.)

### Notas sobre la configuración del servidor ###

Los cambios realizados mediante la página de propiedades **Configuración del servidor** se reflejan en los elementos `<component>` del archivo package.xml.

Cuando use las opciones **Carga automática…** o **Implementar desde descarga…** para el JDK o el servidor de aplicaciones y vaya a compilar para la nube (no en el emulador de proceso) y esté conectado a la red, es posible que vea mensajes de compilación como el siguiente en la salida de la consola a medida que el compilador Ant comprueba la disponibilidad de la descarga:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...`

Si seleccionó la opción **Implementar desde descarga…**, puede mostrarse la siguiente advertencia, pero la compilación no se detendrá:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).`

Esta advertencia es la única indicación de que no se ha comprobado la disponibilidad de la descarga. Por tanto, si por cualquier motivo se produce una implementación en la nube, compruebe si ha recibido dicha advertencia.

Si desea deshabilitar la comprobación de la descarga (por ejemplo, si considera que ralentiza innecesariamente la compilación), establezca el atributo `verifydownloads` en `false` en el elemento `<windowsazurepackage>` de package.xml:

`<windowsazurepackage verifydownloads="false" ...>`

Si seleccionó la opción **Carga automática…**, en la ventana de consola verá mensajes de la compilación que informan sobre el progreso de la carga cada 5 segundos, siempre que sea necesaria una carga.

<a name="ssl_offloading_properties"></a>
### Propiedades de la descarga de SSL ###

Abra el menú contextual del rol en el panel del Explorador de proyectos de Eclipse, haga clic en **Azure** y, a continuación, en **Descarga de SSL**.

![][ic719481]

Desde este cuadro de diálogo se puede habilitar la descarga de SSL, lo que permite habilitar fácilmente la compatibilidad con el protocolo HTTPS en cualquier implementación de Java en Azure sin necesidad de configurar SSL en el servidor de aplicaciones Java. Para más información, consulte [Descarga de SSL][] y [Uso de la descarga de SSL][].

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Instalación del Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hola a todos para Azure en Eclipse][]

[Propiedades del proyecto de Azure][]

[Lista de cuentas de almacenamiento de Azure][]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de administración de Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Propiedades del proyecto de Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Lista de cuentas de almacenamiento de Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[resumen del paquete com.microsoft.windowsazure.serviceruntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Creación de una aplicación Hola a todos para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Depuración de una instancia de rol específica en una implementación de varias instancias]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Depuración de aplicaciones de Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Deploying Large Deployments]: http://go.microsoft.com/fwlink/?LinkID=699536
[Uso de almacenamiento en caché colocado]: http://go.microsoft.com/fwlink/?LinkID=699542
[Uso de la descarga de SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[Instalación del Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Enable Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699549
[Descarga de SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!---HONumber=AcomDC_0114_2016-->