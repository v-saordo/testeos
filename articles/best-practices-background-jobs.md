<properties
   pageTitle="Orientación sobre los trabajos en segundo plano | Microsoft Azure"
   description="Orientación sobre las tareas en segundo plano que se ejecutan independientemente de la interfaz de usuario."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="masashin"/>

# Orientación sobre los trabajos de segundo plano

![](media/best-practices-background-jobs/pnp-logo.png)


## Información general

Muchos tipos de aplicaciones requieren tareas en segundo plano que se ejecutan independientemente de la interfaz de usuario (UI). Entre algunos ejemplos se incluyen trabajos por lotes, tareas de procesamiento intensivos y procesos de ejecución prolongada, como flujos de trabajo. Los trabajos en segundo plano se pueden ejecutar sin intervención del usuario. La aplicación puede iniciar el trabajo y seguir procesando solicitudes interactivas de los usuarios. Esto puede ayudar a reducir la carga en la interfaz de usuario de la aplicación, lo que puede mejorar la disponibilidad y reducir los tiempos de respuesta interactiva.

Por ejemplo, si una aplicación necesita generar vistas en miniatura de imágenes cargadas por los usuarios, puede hacerlo como un trabajo en segundo plano y guardar la vista en miniatura en el almacenamiento una vez completado el proceso, sin que el usuario tenga que esperar hasta que el proceso se complete. Del mismo modo, un usuario que haga un pedido puede iniciar un flujo de trabajo en segundo plano que procese el pedido, mientras que la interfaz de usuario permite al usuario seguir explorando la aplicación web. Cuando se complete el trabajo en segundo plano, puede actualizar los datos de pedidos almacenados y enviar un correo electrónico al usuario que confirma el pedido.

Al considerar si una tarea se debe implementar como un trabajo en segundo plano, el criterio principal es si la tarea se puede ejecutar sin la interacción del usuario y sin que la interfaz de usuario tenga que esperar a que el trabajo se complete. Las tareas que requieran la interacción del usuario o que la interfaz de usuario espere mientras se completan pueden no ser adecuadas como trabajos en segundo plano.

## Tipos de trabajos en segundo plano

Normalmente, los trabajos en segundo plano tienen una o varias de las siguientes características:

- Trabajos de uso intensivo de la CPU, tales como cálculos matemáticos, análisis de modelo estructural, etc.
- Trabajos E/S intensivas, tal como la ejecución de una serie de transacciones de almacenamiento o la indización de archivos.
- Trabajos por lotes, tales como actualizaciones de datos por la noche o procesamiento programado.
- Flujos de trabajo de ejecución prolongada, tal como la realización de pedidos o el aprovisionamiento de servicios y sistemas.
- Procesamiento de datos confidenciales en el que la tarea se entrega a una ubicación más segura para su procesamiento. Por ejemplo, quizás no desee procesar datos confidenciales en una aplicación web y, en su lugar, usar un patrón, como por ejemplo [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx), para transferir los datos a un proceso en segundo plano aislado con acceso a almacenamiento protegido.

## Desencadenadores

Los trabajos en segundo plano se pueden iniciar de varias maneras diferentes. De hecho, todos ellas pertenecen a una de las siguientes categorías:

- [**Desencadenadores impulsados por eventos**](#event-driven-triggers). La tarea se inicia en respuesta a un evento, normalmente una acción realizada por un usuario o un paso de un flujo de trabajo.
- [**Desencadenadores impulsados por programación**](#schedule-driven-triggers). La tarea se invoca según una programación basada en un temporizador. Esto puede ser una programación periódica o una invocación única especificada para una fecha posterior.

### Desencadenadores impulsados por eventos

La invocación impulsada por eventos usa un desencadenador para iniciar la tarea en segundo plano. Entre algunos ejemplos del uso de desencadenadores impulsados por eventos se incluyen:

- La interfaz de usuario u otro trabajo coloca un mensaje en una cola. El mensaje contiene datos sobre una acción que se ha producido, por ejemplo, el usuario realiza un pedido. La tarea en segundo plano escucha en esta cola y detecta la llegada de un nuevo mensaje. Lee el mensaje y usa los datos que este contiene como la entrada para el trabajo en segundo plano.
- La interfaz de usuario u otro trabajo guarda o actualiza un valor en el almacenamiento. La tarea en segundo plano supervisa el almacenamiento y detecta los cambios. Lee los datos y los usa como la entrada para el trabajo en segundo plano.
- La interfaz de usuario u otro trabajo hace una solicitud a un extremo, como un URI HTTPS o una API expuesta como servicio web. Pasa los datos necesarios para completar la tarea en segundo plano como parte de la solicitud. El extremo o servicio web invoca la tarea en segundo plano, que usa los datos como entrada.

Entre algunos ejemplos típicos de las tareas adecuadas para la invocación impulsada por eventos se incluyen procesamiento de imágenes, flujos de trabajo, envío de información a servicios remotos, envío de mensajes de correo electrónico, aprovisionamiento de nuevos usuarios en aplicaciones de varios inquilinos etc.

### Desencadenadores impulsados por programación

La invocación impulsada por programación usa un temporizador para iniciar la tarea en segundo plano. Entre algunos ejemplos del uso de desencadenadores impulsados por programación se incluyen:

- Un temporizador que se ejecuta localmente en la aplicación o como parte del sistema operativo de la aplicación invoca una tarea en segundo plano de forma periódica.
- Un temporizador que se ejecuta en otra aplicación o un servicio de temporizador, tal como Programador de Azure, envía una solicitud a una API o servicio de forma periódica. La API o servicio web invoca la tarea en segundo plano.
- Un proceso o aplicación independiente inicia un temporizador que provoca la invocación de tarea en segundo plano después de un retraso de tiempo especificado o en un momento determinado.

Entre algunos ejemplos típicos las tareas adecuadas para la invocación impulsada por programación se incluyen rutinas de procesamiento por lotes, tal como la actualización de listas de productos relacionados para los usuarios basadas en su comportamiento reciente, tareas rutinarias de procesamiento de datos, como la actualización de índices o la generación de resultados acumulados, análisis de datos para informes diarios, limpieza de retención de datos, comprobaciones de coherencia de datos, etc.

Si usa una tarea impulsada por programación que se debe ejecutar como una sola instancia, tenga en cuenta lo siguiente:

- Si se escala la instancia de proceso que ejecuta el programador (por ejemplo, una máquina virtual que usa Tareas programadas de Windows), tendrá varias instancias del programador en ejecución y estas podrían iniciar varias instancias de la tarea.
- Si las tareas se ejecutan durante más tiempo que el período entre eventos del programador, el programador puede iniciar otra instancia de la tarea mientras se siga ejecutando la anterior.

## Devolución de resultados

Los trabajos en segundo plano se ejecutan de forma asincrónica en un proceso independiente o incluso en una ubicación independiente, desde la interfaz de usuario o el proceso que invocó la tarea en segundo plano. Idealmente, las tareas en segundo plano son operaciones tipo "desencadenar y olvidar" y su progreso de ejecución no tiene ningún impacto en la interfaz de usuario o el proceso de llamada. Esto significa que el proceso de llamada no espera la finalización de las tareas y, por tanto, no puede detectar automáticamente cuándo finaliza la tarea. Si necesita que una tarea de segundo plano comunique con la tarea de llamada para indicar el progreso o la finalización, debe implementar un mecanismo para ello. A continuación, se indican algunos ejemplos:

- Escribir el valor del indicador de estado en el almacenamiento accesible para la tarea de interfaz de usuario o la llamada, que puede supervisar o comprobar este valor cuando sea necesario. Otros datos que la tarea en segundo plano debe devolver a la llamada se pueden colocar en el mismo almacenamiento.
- Establecer una cola de respuesta en la que escucha la interfaz de usuario o la llamada. La tarea en segundo plano puede enviar mensajes a la cola, indicando el estado y la finalización. Los datos que la tarea en segundo plano debe devolver a la llamada pueden colocarse en los mensajes. Si usa Bus de servicio de Azure, puede usar las propiedades **ReplyTo** y **CorrelationId** para implementar esta capacidad. Para más información, consulte el artículo sobre la [correlación en mensajería asíncrona de Bus de servicio](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Exponer una API o extremo desde la tarea en segundo plano a la que pueda acceder la interfaz de usuario o la llamada para obtener información sobre el estado. Los datos que la tarea en segundo plano debe devolver a la llamada pueden incluirse en la respuesta.
- Haga que la tarea en segundo plano realice una devolución de llamada a la interfaz de usuario o llamada a través de una API para indicar el estado en puntos predefinidos o en el momento de finalización. Esto podría hacerse a través de eventos generados localmente o un mecanismo de publicación y suscripción. Los datos que debe devolver la tarea en segundo plano a la llamada se pueden incluir en la carga de la solicitud o evento.

## Entorno de hospedaje

Puede hospedar tareas en segundo plano con una gama de diferentes servicios de plataforma de Azure:

- [**Aplicaciones web y WebJobs de Azure**](#azure-web-apps-and-webjobs). Puede usar WebJobs para ejecutar trabajos personalizados basados en distintos tipos de scripts o programas ejecutables en el contexto de una aplicación web.
- [**Roles web y de trabajo de Servicios en la nube de Azure**](#azure-cloud-services-web-and-worker-roles). Puede escribir código dentro de un rol que se ejecuta como una tarea en segundo plano.
- [**Máquinas virtuales de Azure**](#azure-virtual-machines). Si tiene un servicio de Windows o quiere usar el Programador de tareas de Windows, es común hospedar las tareas en segundo plano dentro de una máquina virtual dedicada.

En las siguientes secciones se describe cada una de estas opciones con más detalle y se incluyen consideraciones para ayudarle a elegir la opción adecuada.

### Aplicaciones web y WebJobs de Azure

Puede usar WebJobs de Azure para ejecutar trabajos personalizados como tareas en segundo plano dentro de una aplicación web de Azure. WebJobs se ejecuta dentro del contexto de una aplicación web como un proceso continuo o en respuesta a un evento desencadenador desde el Programador de Azure o a factores externos, como cambios en los blobs de almacenamiento y colas de mensajes. Los trabajos se pueden iniciar y detener a petición, y cerrarse correctamente. Si se produce un error continuamente al ejecutar un trabajo web, este se reinicia automáticamente. Las acciones de error y reintento son configurables.

Al configurar un trabajo web:

- Si quiere que el trabajo responda a un desencadenador impulsado por eventos, debe configurarse como **Ejecutar continuamente**. El script o programa se almacena en la carpeta denominada site/wwwroot/app\_data/jobs/continuous.
- Si quiere que el trabajo responsa a un desencadenador impulsado por programación, debe configurarse como **Ejecutar de acuerdo con una programación**. El script o programa se almacena en la carpeta denominada site/wwwroot/app\_data/jobs/triggered.
- Si elige la opción **Ejecutar a petición** al configurar un trabajo, se ejecutará el mismo código que la opción **Ejecutar de acuerdo con una programación** cuando se inicia.

WebJobs de Azure se ejecuta en el recinto de la aplicación web, lo que significa que pueden tener acceso a variables de entorno y compartir información como cadenas de conexión con la aplicación web. El trabajo tiene acceso al identificador único del equipo que ejecuta el trabajo. La cadena de conexión denominada **AzureWebJobsStorage** proporciona acceso a colas de almacenamiento, blobs y tablas de Azure para los datos de aplicación, y al Bus de servicio para la comunicación y la mensajería. La cadena de conexión denominada **AzureWebJobsDashboard** proporciona acceso a los archivos de registro de acciones del trabajo.

WebJobs de Azure tiene las siguientes características:

- **Seguridad**: los trabajos web se protegen mediante las credenciales de implementación de la aplicación web.
- **Tipos de archivo admitidos**: los trabajos web se pueden definir con scripts (.cmd), archivos por lotes (.bat), scripts de PowerShell (. ps1), scripts de shell de bash (.sh), scripts PHP (.php), scripts de Python (.py), código JavaScript (.js) y programas ejecutables (.exe, .jar etc.).
- **Implementación**: los scripts y ejecutables se pueden implementar con el portal de Azure, y crear e implementar con el complemento [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) para Visual Studio o [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs), a través del [SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk-get-started.md) o bien copiándolos directamente en las siguientes ubicaciones:
  - En el caso de la ejecución desencadenada: site/wwwroot/app\_data/jobs/triggered/{nombre del trabajo}
  - En el caso de la ejecución continua: site/wwwroot/app\_data/jobs/continuous/{nombre del trabajo}
- **Registro**: Console.Out se considera (marca) como INFO y Console.Error como ERROR. La información de supervisión y diagnóstico es accesible a través del portal de Azure y los archivos de registro se pueden descargar directamente del sitio. Se guardan en las siguientes ubicaciones:
  - Ejecución desencadena: Vfs/data/jobs/continuous/NombreTrabajo
  - Ejecución continua: Vfs/data/jobs/triggered/NombreTrabajo
- **Configuración**: los trabajos web se pueden configurar a través del portal, la API de REST y PowerShell. Para proporcionar la información de configuración de un trabajo se puede usar un archivo de configuración denominado settings.job en el mismo directorio raíz que el script de trabajo. Por ejemplo:
  - { "stopping\_wait\_time": 60 }
  - { "is\_singleton": true }

### Consideraciones

- De forma predeterminada, los trabajos web se escalan con la aplicación web. Sin embargo, los trabajos se pueden configurar para que se ejecuten en la instancia única al establecer la propiedad de configuración **is\_singleton** en true. Los trabajos web de instancia única son útiles para las tareas que no quiera escalar o ejecutar como varias instancias simultáneamente, por ejemplo, la reindización, el análisis de datos y tareas similares.
- Para minimizar el impacto de los trabajos en el rendimiento de la aplicación web, considere la posibilidad de crear una instancia vacía de aplicación web de Azure en un nuevo Plan del servicio de aplicaciones para hospedar los trabajos web que pueden tardar tiempo en ejecutarse o que consumen muchos recursos.

### Más información

- En [Recursos recomendados de WebJobs de Azure](websites-webjobs-resources.md) se indican varios recursos de utilidad, descargas y ejemplos para trabajos web.

## Roles web y de trabajo de Servicios en la nube de Azure

Las tareas en segundo plano se pueden ejecutar dentro de un rol web o en un rol de trabajo independiente. La decisión de si se debe usar un rol de trabajado debe basarse en la consideración de escalabilidad y los requisitos de elasticidad, la duración de la tarea, la cadencia de la versión, la seguridad, la tolerancia a errores, la contención, la complejidad y la arquitectura lógica. Para más información, consulte el artículo sobre el [patrón de consolidación de recursos de proceso](http://msdn.microsoft.com/library/dn589778.aspx).

Hay varias maneras de implementar tareas en segundo plano dentro de un rol de Servicios en la nube:

- Crear una implementación de la clase **RoleEntryPoint** en el rol y usar sus métodos para ejecutar tareas en segundo plano. Las tareas se ejecutan en el contexto de WaIISHost.exe y pueden usar el método **GetSetting** de la clase **CloudConfigurationManager** para cargar opciones de configuración. Para más información, consulte el artículo sobre el [ciclo de vida (Servicios en la nube)](#lifecycle-cloud-services).
- Use las tareas de inicio para ejecutar tareas en segundo plano al iniciarse la aplicación. Para obligar a las tareas seguir ejecutándose en segundo plano, establezca la propiedad **taskType** en **background** (si no lo hace, el proceso de inicio de la aplicación se detendrá y esperará a que finalice la tarea). Para más información, consulte [Ejecutar tareas de inicio en Azure](cloud-services-startup-tasks.md).
- Use el SDK de WebJobs para implementar tareas en segundo plano como trabajos web que se inician como tarea de inicio. Para más información, consulte [Introducción al SDK de Azure WebJobs](websites-dotnet-webjobs-sdk-get-started.md).
- Use una tarea de inicio para instalar un servicio de Windows que ejecuta una o más tareas en segundo plano. Debe establecer la propiedad **taskType** en **background** para que el servicio se ejecute en segundo plano. Para más información, consulte [Ejecutar tareas de inicio en Azure](cloud-services-startup-tasks.md).

### Ejecutar tareas en segundo plano en el rol web

La principal ventaja de ejecutar tareas en segundo plano en el rol web es el ahorro en costos de hospedaje porque no hay ningún requisito que exige implementar funciones adicionales.

### Ejecutar tareas en segundo plano en un rol de trabajo

Ejecutar tareas en segundo plano en un rol de trabajo tiene varias ventajas:

- Permite administrar el escalado por separado para cada tipo de rol. Por ejemplo, puede que necesite más instancias de un rol web para admitir la carga actual, pero menos instancias del rol de trabajo que ejecuta tareas en segundo plano. Escalar instancias de proceso de tarea de segundo plano por separado desde los roles de la interfaz de usuario puede reducir el costo de hospedaje, al tiempo que mantiene un rendimiento aceptable.
- Descarga la sobrecarga de procesamiento de las tareas en segundo plano del rol web. El rol web que proporciona la interfaz de usuario puede seguir respondiendo y es posible que implique la necesidad de un menor número de instancias para admitir un volumen determinado de las solicitudes de usuarios.
- Permite implementar la separación de preocupaciones. Cada tipo de rol puede implementar un conjunto específico de tareas claramente definidas y relacionadas. Esto facilita el diseño y mantenimiento del código porque hay menos interdependencia de código y funcionalidad entre cada rol.
- Puede ayudar a aislar los datos y procesos confidenciales. Por ejemplo, los roles de web que implementan la interfaz de usuario no necesitan tener acceso a los datos que administra y controla un rol de trabajo. Esto puede resultar de utilidad a la hora de reforzar la seguridad, especialmente cuando se usa un patrón como el [patrón Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx).  

### Consideraciones

Tenga en cuenta los siguientes puntos a la hora de elegir cómo y dónde implementar tareas en segundo plano cuando se usan los roles web y de trabajo de Servicios en la nube:

- El hospedaje de las tareas en segundo plano en un rol web rol existente puede ahorrar el costo de la ejecución de un rol de trabajo independiente solo para estas tareas. Sin embargo, es probable que afecten al rendimiento y la disponibilidad de la aplicación si hay contención de procesamiento y otros recursos. El uso de un rol de trabajo independiente protege al rol web ante el impacto de las tareas en segundo plano de larga ejecución o que hacen un uso intensivo de los recursos.
- Si hospeda tareas en segundo plano con la clase **RoleEntryPoint**, puede mover esto fácilmente a otro rol. Por ejemplo, si crea la clase en un rol web y más adelante decide que necesita ejecutar las tareas en un rol de trabajo, puede mover la implementación de la clase **RoleEntryPoint** al rol de trabajo.
- Las tareas de inicio están diseñadas para ejecutar un programa o un script. La implementación de un trabajo en segundo plano como un programa ejecutable puede ser más difícil, especialmente si también requiere la implementación de ensamblados dependientes. Puede ser más fácil implementar y usar un script para definir un trabajo en segundo plano cuando se usan tareas de inicio.
- Las excepciones que provocan un error en una tarea en segundo plano tienen un impacto diferente según la manera en que se hospeda:
  - Si usa el método de la clase **RoleEntryPoint**, una tarea con error hará que el rol se reinicie para que la tarea se reinicie automáticamente. Esto puede afectar a la disponibilidad de la aplicación. Para evitar esto, asegúrese de incluir una gestión sólida de excepciones dentro de la clase **RoleEntryPoint** y todas las tareas en segundo plano. Use código para reiniciar las tareas con error cuando resulte adecuado y lance la excepción para reiniciar el rol únicamente si no puede recuperarse correctamente tras un error dentro del código.
  - Si usa tareas de inicio, es su responsabilidad administrar la ejecución de la tarea y de la comprobación cuando si se produce un error.
- La administración y supervisión de tareas de inicio son más difíciles que usar el método de la clase **RoleEntryPoint**. Sin embargo, en el SDK de WebJobs de Azure se incluye un panel para facilitar la administración de trabajos web que se inician a través de las tareas de inicio.

### Más información

- [Patrón de consolidación de recursos de proceso](http://msdn.microsoft.com/library/dn589778.aspx)
- [Introducción al SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk-get-started.md)

## Máquinas virtuales de Azure

Las tareas en segundo plano se pueden implementar de forma que se las impida implementarse en Aplicaciones web o Servicios en la nube de Azure, o puede que estas opciones no sean convenientes. Entre algunos ejemplos típicos se incluye los servicios de Windows y las utilidades y programas ejecutables de terceros. También se pueden incluir los programas escritos para un entorno de ejecución diferente al que hospeda la aplicación. Por ejemplo, puede ser un programa de Unix o Linux que quiera ejecutar desde una aplicación de Windows o. NET. Puede elegir entre una variedad de sistemas operativos para una máquina virtual de Azure y ejecutar el servicio o ejecutable en esa máquina virtual.

Para ayudarle a elegir cuándo usar Máquinas virtuales, consulte [Comparación entre Servicios de aplicaciones, Servicios en la nube y Máquinas virtuales de Azure](choose-web-site-cloud-service-vm.md). Para obtener información sobre las opciones de Máquinas virtuales, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para más información sobre los sistemas operativos y las imágenes preconfiguradas disponibles para Máquinas virtuales, consulte [Galería de Máquinas virtuales de Azure](https://azure.microsoft.com/gallery/virtual-machines/).

Para iniciar la tarea en segundo plano en una máquina virtual independiente, tiene varias opciones:

- Puede ejecutar la tarea a petición directamente desde la aplicación mediante el envío de una solicitud a un extremo que la tarea expone, pasando los datos que requiera la tarea. Este extremo invoca la tarea.
- Puede configurar la tarea para que se ejecute según una programación con un programador o temporizador disponible en el sistema operativo elegido. Por ejemplo, en Windows, puede usar el Programador de tareas de Windows para ejecutar scripts y tareas, o bien, si tiene instalado SQL Server en la máquina virtual, puede usar el Agente SQL Server para ejecutar scripts y tareas.
- Puede usar el Programador de Azure para iniciar la tarea al agregar un mensaje a una cola en la que escucha la tarea o al enviar una solicitud a una API que la tarea expone.

Consulte la sección anterior [Desencadenadores](#triggers) para más información sobre cómo iniciar tareas en segundo plano.

### Consideraciones

Tenga en cuenta los siguientes puntos a la hora de decidir si va a implementar tareas en segundo plano en una máquina virtual de Azure:

- El hospedaje de tareas en segundo plano en una máquina virtual de Azure diferente proporciona flexibilidad y permite un control preciso sobre la iniciación, ejecución, programación y asignación de recursos. Sin embargo, aumentará el costo de tiempo de ejecución si una máquina virtual se debe implementar solo para ejecutar tareas en segundo plano.
- No hay funcionalidad que permita supervisar las tareas en el portal de Azure ni tampoco capacidad de reinicio automatizado para las tareas con error. Sin embargo, puede supervisar el estado básico de la máquina virtual y administrarlo con los [cmdlets de administración de servicios de Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). No obstante, no hay funcionalidad para controlar los procesos y subprocesos en los nodos de proceso. Normalmente, el uso de una máquina virtual requiere un esfuerzo adicional para implementar un mecanismo que recopile datos de instrumentación en la tarea y del sistema operativo en la máquina virtual. Una solución que podría ser adecuada es usar [System Center Management Pack para Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Considere la posibilidad de crear sondeos de supervisión que se exponen a través de extremos HTTP. El código para estos sondeos podría realizar comprobaciones de estado, recopilar información operativa y estadísticas o intercalar información de error y devolverla a una aplicación de administración. Para más información, consulte el artículo sobre el [patrón de supervisión del extremo de estado](http://msdn.microsoft.com/library/dn589789.aspx).

### Más información

- [Máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/) en Azure
- [Preguntas más frecuentes sobre Máquinas virtuales de Azure](virtual-machines-questions.md)

## Consideraciones de diseño

Hay varios factores fundamentales que se deben tener en cuenta a la hora de diseñar tareas en segundo plano. En las siguientes secciones se describen la creación de particiones, los conflictos y la coordinación.

## Creación de particiones

Si decide incluir tareas en segundo plano dentro de una instancia de proceso existente (por ejemplo, una aplicación web, un rol web, un rol de trabajo existente o una máquina virtual), debe tener en cuenta cómo afectará a los atributos de calidad de la instancia de proceso y la tarea de segundo plano propiamente dicha. Estos factores le ayudarán a decidir si las tareas se deben colocalizar con la instancia de proceso existente o si se deben separar en una instancia de proceso independiente:

- **Disponibilidad**: es posible que las tareas en segundo plano tengan que tener el mismo nivel de disponibilidad que otras partes de la aplicación, en particular, la interfaz de usuario y otras partes directamente implicadas en la interacción con el usuario. Las tareas en segundo plano pueden ser más tolerantes a la latencia, errores de reintento de conexión y otros factores que afectan a la disponibilidad, ya que las operaciones se pueden poner en cola. Sin embargo, debe haber capacidad suficiente como para impedir la creación de copias de seguridad de las solicitudes que podrían bloquear las colas y afectar a la aplicación en su totalidad.
- **Escalabilidad**: es probable que las tareas en segundo tengan requisitos de escalabilidad diferentes a los de la interfaz de usuario y las partes interactivas de la aplicación. El escalado de la interfaz de usuario podría ser necesario para satisfacer los picos de demanda, mientras que las tareas en segundo plano pendientes podrían completarse en horarios de menor actividad usando un menor número de instancias de proceso.
- **Resistencia**: el error de una instancia de proceso que hospeda solo tareas en segundo plano puede no afectar gravemente a la aplicación en su totalidad si las solicitudes para estas tareas se pueden poner en cola o posponer hasta que la tarea esté disponible de nuevo. Si la instancia de proceso y las tareas se pueden iniciar dentro de un intervalo adecuado, es posible que los usuarios de la aplicación no se vean afectados.
- **Seguridad**: las tareas en segundo plano pueden tener distintos requisitos o restricciones de seguridad que los de la interfaz de usuario u otras partes de la aplicación. Al usar una instancia de proceso independiente, puede especificar un entorno de seguridad diferente para las tareas. También puede usar patrones, como Gatekeeper, para aislar las instancias de proceso de segundo plano de la interfaz de usuario con el fin de maximizar la seguridad y la separación.
- **Rendimiento**: puede elegir el tipo de instancia de proceso para las tareas de segundo plano de modo que satisfagan específicamente los requisitos de rendimiento de las tareas. Esto puede significar el uso de una opción de proceso menos costosa si las tareas no requieren las mismas capacidades de procesamiento que las de la interfaz de usuario o una instancia mayor si requieren recursos y capacidades adicionales.
- **Capacidad de administración**: las tareas en segundo plano pueden tener un ritmo de desarrollo e implementación diferente de los del código de aplicación principal o la interfaz de usuario. Su implementación en una instancia de proceso independiente puede simplificar las actualizaciones y el control de versiones.
- **Costo**: la adición de instancias de proceso para ejecutar tareas en segundo plano aumenta los costos de hospedaje. Debe considerar detenidamente el equilibrio entre la capacidad adicional y estos costos adicionales.

Para más información, consulte los artículos sobre el [patrón de elección de líder](http://msdn.microsoft.com/library/dn568104.aspx) y el [patrón de consumidores en competencia](http://msdn.microsoft.com/library/dn568101.aspx).

## Conflictos

Si tiene varias instancias de un trabajo en segundo plano, es posible que compitan entre sí para tener acceso a los recursos y servicios, como las bases de datos y el almacenamiento. Este acceso simultáneo puede producir contención de recursos, que puede causar conflictos en la disponibilidad de los servicios y la integridad de los datos en el almacenamiento. La contención de recursos se puede resolver al emplear un método de bloqueo pesimista para impedir que las instancias en competencia de una tarea accedan simultáneamente a un servicio o dañen los datos.

Otro método para resolver conflictos es definir tareas en segundo plano como un singleton, de modo que solo habrá una instancia en ejecución a la vez. Sin embargo, esto elimina las ventajas de confiabilidad y rendimiento que podría ofrecer una configuración de varias instancias, especialmente si la interfaz de usuario puede proporcionar suficiente trabajo para mantener ocupada más de una tarea en segundo plano. Es fundamental asegurarse de que la tarea en segundo plano pueda reiniciarse automáticamente y que tenga suficiente capacidad como para hacer frente a los picos de demanda. Esto puede lograrse mediante la asignación de una instancia de proceso con suficientes recursos, la implementación de un mecanismo de puesta en cola que pueda almacenar las solicitudes de ejecución posterior cuando disminuya la demanda o una combinación de estas técnicas.

## Coordinación

Las tareas en segundo plano pueden ser complejas y requerir la ejecución de varias tareas individuales para generar un resultado o satisfacer todos los requisitos. Es habitual en estos escenarios dividir la tarea en pasos o subtareas discretos más pequeños que varios consumidores pueden ejecutar. Los trabajos de varios pasos pueden ser más eficaces y más flexibles porque los pasos individuales pueden reutilizarse en varios trabajos. También facilitan la adición, eliminación o modificación del orden de los pasos.

La coordinación de varias tareas y pasos puede suponer un reto, pero hay tres patrones comunes que puede usar y que le guiarán por el proceso de implementación de una solución:

- **Descomponer una tarea en varios pasos reutilizables**. Es posible que una aplicación tenga que realizar diversas tareas de complejidad variable en la información que procesa. Un método sencillo pero inflexible para implementar esta aplicación podría ser realizar este procesamiento como un módulo monolítico. Sin embargo, este método probablemente reducirá las posibilidades de refactorización del código, su optimización o su reutilización si partes del mismo procesamiento se necesitan en otro lugar dentro de la aplicación. Para más información, consulte el artículo sobre el [patrón de canalizaciones y filtros](http://msdn.microsoft.com/library/dn568100.aspx).
- **Administrar la ejecución de los pasos de una tarea**. Una aplicación puede realizar tareas que forman una serie de pasos, algunos de los cuales pueden invocar servicios remotos o acceder a recursos remotos. Los pasos individuales pueden ser independientes entre sí, pero están organizados por la lógica de la aplicación que implementa la tarea. Para más información, consulte el artículo sobre el [patrón de supervisor de programador de agentes](http://msdn.microsoft.com/library/dn589780.aspx).
- **Administrar la recuperación de pasos de una tarea con error**. Una aplicación puede necesitar deshacer el trabajo realizado por una serie de pasos, que conjuntamente definen una operación coherente, si uno o varios de los pasos termina en error. Para más información, consulte el artículo sobre el [patrón de transacción de compensación](http://msdn.microsoft.com/library/dn589804.aspx).

## Ciclo de vida (Servicios en la nube)

 Si decide implementar trabajos en segundo plano para aplicaciones de Servicios en la nube que usan roles web y de trabajo mediante la clase **RoleEntryPoint**, es importante comprender el ciclo de vida de esta clase para poder usarla correctamente.

Los roles web y de trabajo pasan por un conjunto de fases como al iniciarse, ejecutarse y detenerse. La clase **RoleEntryPoint** expone una serie de eventos que indican cuándo se producen estas fases. Estos se usan para inicializar, ejecutar y detener las tareas en segundo plano personalizadas. El ciclo completo es el siguiente:

- Azure carga el ensamblado de roles y busca en él una clase que deriva de **RoleEntryPoint**.
- Si encuentra esta clase, llama a **RoleEntryPoint.OnStart()**. Invalide este método para inicializar las tareas en segundo plano.
- Una vez completado el método **OnStart**, Azure llama a **Application\_Start ()** en archivo Global de la aplicación si está presente (por ejemplo, Global.asax en un rol web que ejecuta ASP.NET).
- Azure llama a **RoleEntryPoint.Run()** en un nuevo subproceso en primer plano que se ejecuta en paralelo con **OnStart()**. Invalide este método para iniciar las tareas en segundo plano.
- Cuando finalice el método Run, Azure llama primero a **Application\_End()** en el archivo Global de la aplicación si está presente y luego llamada a **RoleEntryPoint.OnStop()**. Invalide el método **OnStop** para detener las tareas en segundo plano, limpiar los recursos, eliminar objetos y cerrar las conexiones que las tareas podrían haber usado.
- Se detiene el proceso de host del rol de trabajado de Azure. En este punto, el rol se reciclará y se reiniciará.

Para obtener más detalles y un ejemplo del uso de los métodos de la clase **RoleEntryPoint**, consulte el artículo sobre el [patrón de consolidación de recursos de proceso](http://msdn.microsoft.com/library/dn589778.aspx).

## Consideraciones

Tenga en cuenta los siguientes puntos al planear cómo se ejecutarán las tareas en segundo plano en un rol web o de trabajo:

- La implementación predeterminada del método **Run** en la clase **RoleEntryPoint** contiene una llamada a **Timeout.Infinite** que mantiene el rol activo de manera indefinida. Si invalida el método **Run** (esto suele ser necesario para ejecutar tareas en segundo plano), no debe permitir que el código salga del método, a menos que quiera reciclar la instancia de rol.
- En una implementación típica del método **Run** se incluye código para iniciar cada una de las tareas en segundo plano, así como una construcción de bucle que comprueba periódicamente el estado de todas las tareas en segundo plano. Puede reiniciar las que tengan errores o supervisar en búsqueda de tokens de cancelación que indican que los trabajos se han completado.
- Si una tarea en segundo plano lanza una excepción no controlada, dicha tarea debería reciclarse a la vez que permite que las otras tareas en segundo plano del rol sigan en ejecución. Sin embargo, si la excepción se debe a daños en los objetos fuera de la tarea, tal como almacenamiento compartido, la excepción debería controlarla la clase **RoleEntryPoint**, se deberían cancelar todas las tareas y se debería permitir que el método **Run** finalice. Azure entonces reiniciará el rol.
- Use el método **OnStop** para pausar o detener las tareas en segundo plano y limpiar los recursos. Esto puede implicar la detención de tareas de ejecución prolongada o de varios pasos, y es esencial tener en cuenta cómo se puede hacer esto para evitar incoherencias en los datos. Si una instancia de rol se detiene por algún motivo que no sea un cierre iniciado por el usuario, el código que se ejecuta en el método **OnStop** debe completarse dentro de cinco minutos antes de que se finalice forzosamente. Asegúrese de que el código pueda completarse en ese tiempo o que pueda tolerar que no se ejecute hasta completarse.  
- El equilibrador de carga de Azure comienza a dirigir el tráfico a la instancia de rol cuando el método **RoleEntryPoint.OnStart** devuelve true. Por lo tanto, considere la posibilidad de colocar el código de inicialización en el método **OnStart** para que las instancias de rol que no se inicializan correctamente no reciban tráfico.
- Puede usar tareas de inicio además de los métodos de la clase **RoleEntryPoint**. Debería usar tareas de inicio para inicializar las opciones de configuración que necesite cambiar en el equilibrador de carga de Azure porque estas tareas se ejecutarán antes de que el rol reciba las solicitudes. Para más información, consulte [Ejecutar tareas de inicio en Azure](cloud-services-startup-tasks.md).
- Si se produce un error en una tarea de inicio, es posible que obligue a que el rol se reinicie continuamente. Esto puede impedir la realización de un intercambio VIP a una versión de ensayo anterior porque el intercambio requiere acceso exclusivo a al rol y esto no se puede obtener mientras se reinicia el rol. Para resolver este problema:
	-  Agregue el siguiente código al principio de los métodos **OnStart** y **Run** en el rol:

	```C#
	var freeze = CloudConfigurationManager.GetSetting("Freeze");
	if (freeze != null)
	{
		if (Boolean.Parse(freeze))
	  	{
		    Thread.Sleep(System.Threading.Timeout.Infinite);
		}
	}
	```

   - Agregue la definición de opción **Inmovilizar** como un valor booleano a los archivos ServiceDefinition.csdef y ServiceConfiguration.*.cscfg para el rol y establézcala en **false**. Si el rol entra en un modo de reinicio repetido, puede cambiar la opción en **true** para inmovilizar la ejecución del rol y permitir que intercambie con una versión anterior.

## Consideraciones de resistencia

Las tareas en segundo plano deben ser resistentes para proporcionar servicios confiables a la aplicación. Al planear y diseñar tareas en segundo plano, tenga en cuenta los siguientes puntos:

- Las tareas en segundo plano deben poder controlar correctamente los reinicios de rol o servicio sin dañar los datos ni introducir incoherencias en la aplicación. Para las tareas de ejecución prolongada o de varios pasos, considere el uso de _puntos de comprobación_ al guardar el estado de los trabajos en el almacenamiento persistente o como mensajes en una cola, si fuera apropiado. Por ejemplo, puede conservar la información de estado de un mensaje en una cola y actualizar esta información de estado en incrementos con el progreso de la tarea para que esta se pueda procesar desde el último punto de comprobación correcto conocido en lugar de reiniciar desde el principio. Al usar las colas del Bus de servicio de Azure, puede usar sesiones de mensajes para habilitar el mismo escenario. Las sesiones le permiten guardar y recuperar el estado de procesamiento de la aplicación mediante los métodos [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) y [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx). Para más información sobre el diseño de procesos y flujos de trabajo confiables de varios pasos, consulte el artículo sobre el [patrón de supervisor de agente de programador](http://msdn.microsoft.com/library/dn589780.aspx).
- Cuando se usan roles web o de trabajo para hospedar varias tareas en segundo plano, diseñe la invalidación del método **Run** para supervisar las tareas detenidas o con errores y reiniciarlas. Cuando esto no sea práctico, y usa un rol de trabajado, obligue el reinicio del rol de trabajo mediante la salida del método **Run**.
- Al usar las colas para comunicarse con las tareas en segundo plano, las colas pueden actuar como un búfer para almacenar las solicitudes enviadas a las tareas cuando la aplicación se encuentra con una mayor carga que la habitual. Esto permite que las tareas se pongan al día con la interfaz de usuario durante los períodos de menor actividad. También significa que el reciclaje del rol no bloqueará la interfaz de usuario. Para más información, consulte [Patrón de equilibrio de carga basado en colas](http://msdn.microsoft.com/library/dn589783.aspx). Si algunas tareas son más importantes que otras, considere la posibilidad de implementar el [patrón de cola de prioridad](http://msdn.microsoft.com/library/dn589794.aspx) para asegurarse de que estas tareas se ejecuten antes que las tareas menos importantes.
- Las tareas en segundo plano que procesan los mensajes, o que estos inician, se deben diseñar para controlar las incoherencias, tales como los mensajes que llegan sin orden, los mensajes que produzcan un error de forma repetida (con frecuencia denominados _mensajes dudosos_) y los mensajes que se entregan más de una vez. Tenga en cuenta lo siguiente.
  - Los mensajes que se deben procesar en un orden específico, tales como los que cambian los datos según su valor existente (por ejemplo, al agregar un valor a un valor existente), pueden no llegar en el orden original en que se enviaron. Como alternativa, las pueden controlar instancias diferentes de una tarea en segundo plano en un orden diferente debido a cargas variables en cada instancia. Los mensajes que se deben procesar en un orden específico deben incluir un número de secuencia, clave o algún otro indicador que las tareas en segundo plano pueden usar para garantizar su procesamiento en el orden correcto. Si usa Bus de servicio de Azure, puede usar sesiones de mensajes para garantizar el orden de entrega. Sin embargo, suele ser más eficaz cuando sea posible diseñar el proceso de modo que el orden de los mensajes no sea importante.
  - Normalmente, una tarea en segundo plano inspeccionará los mensajes en la cola, lo que los oculta temporalmente para otros consumidores de mensaje y luego elimina los mensajes después de que se hayan procesado correctamente. Si se produce un error en una tarea en segundo plano al procesar un mensaje, ese mensaje volverá a aparecer en la cola cuando expire el tiempo de espera de inspección y lo procesará otra instancia de la tarea o durante el próximo ciclo de procesamiento de esta instancia. Si el mensaje produce un error sistemáticamente en el consumidor, bloqueará la tarea, la cola y, en última instancia, la aplicación en sí cuando se llene la cola. Por lo tanto, es fundamental detectar y quitar los mensajes dudosos de la cola. Usa Bus de servicio de Azure, los mensajes que produzcan un error se pueden mover automática o manualmente a una cola de mensajes fallidos asociada.
  - Las colas son mecanismos de _al menos_ una entrega garantizada, pero pueden entregar el mismo mensaje más de una vez. Además, si se produce un error en una tarea en segundo plano después de procesar un mensaje pero antes de su eliminación de la cola, el mensaje estará disponible para nuevo procesamiento. Las tareas en segundo plano deben ser idempotentes, lo que significa que el procesamiento del mismo mensaje varias veces no produce un error o incoherencia en los datos de la aplicación. Algunas operaciones son naturalmente idempotentes, tal como establecer un valor almacenado en un nuevo valor específico. Sin embargo, las operaciones como, por ejemplo, agregar un valor a un valor almacenado existente sin comprobar que el valor almacenado sigue siendo el mismo que cuando se envió originalmente el mensaje, provocarán incoherencias. Las colas de Bus de servicio de Azure pueden configurarse para quitar automáticamente los mensajes duplicados.
  - Algunos sistemas de mensajería, como las colas de almacenamiento de Azure y las colas de Bus de servicio de Azure, admiten una propiedad de recuento de eliminación de la cola que indica el número de veces que se ha leído un mensaje de la cola. Esto puede ser de utilidad en el tratamiento de mensajes dudosos y repetidos. Para más información, consulte los artículos sobre el [manual de mensajería asincrónica](http://msdn.microsoft.com/library/dn589781.aspx) y [patrones de idempotencia](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## Consideraciones de escalado y rendimiento

Las tareas en segundo plano deben ofrecer un rendimiento suficiente como para asegurarse de que no bloqueen la aplicación ni provoquen incoherencias debido al funcionamiento diferido cuando el sistema está bajo carga. Normalmente, el rendimiento se mejora al escalar las instancias de proceso que hospedan las tareas en segundo plano. Al planear y diseñar tareas en segundo plano, tenga en cuenta los siguientes puntos en relación con la escalabilidad y el rendimiento:

- Azure admite el escalado automático (escalar horizontalmente y reducir horizontalmente) en función de la demanda y carga actuales o según una programación predefinida, para Aplicaciones web, roles web y de trabajo de Servicios en la nube e implementaciones hospedadas de Máquinas virtuales. Use esta característica para garantizar que la aplicación en su conjunto tiene capacidades de rendimiento suficientes a la vez que se minimizan los costos de tiempo de ejecución.
- En las tareas en segundo plano con una capacidad de rendimiento diferente de las demás partes de una aplicación de Servicios en la nube (por ejemplo, la interfaz de usuario o los componentes, como la capa de acceso a datos), el hospedaje de las tareas en segundo plano en un rol de trabajo independiente permite que los roles de la interfaz de usuario y de tarea en segundo plano se escalen independientemente para administrar la carga. Si varias tareas en segundo plano tienen capacidades de rendimiento muy diferentes entre sí, considere la posibilidad de dividirlas en roles de trabajado independientes y escalar cada tipo de rol de forma independiente. No obstante, tenga en cuenta que esto puede aumentar los costos de tiempo de ejecución en comparación con la combinación de todas las tareas en menos roles.
- Es posible que el simple escalado de los roles no sea suficiente para impedir la pérdida de rendimiento bajo carga. También es posible que necesite escalar las colas de almacenamiento y otros recursos para impedir que un único punto de la cadena de procesamiento general se convierta en un cuello de botella. Asimismo, considere otras limitaciones, como por ejemplo, el rendimiento máximo de almacenamiento y otros servicios en los que dependen la aplicación y las tareas en segundo plano.
- Las tareas en segundo plano deben diseñarse para el escalado. Por ejemplo, debe poder detectar dinámicamente el número de colas de almacenamiento en uso para escuchar en la cola adecuada o enviar mensajes a ella.
- De manera predeterminada, los trabajos web se escalan con su instancia asociada de Aplicaciones web de Azure. Sin embargo, si quiere que un trabajo web se ejecute solo como una única instancia, puede crear un archivo Settings.job que contenga los datos JSON **{"is\_singleton": true}**. Esto obliga a Azure a ejecutar solo una instancia del trabajo web, incluso si hay varias instancias de la aplicación web asociada, lo que puede ser una técnica útil para los trabajos programados que se deben ejecutar como una sola instancia.

## Patrones relacionados

- [Manual de mensajería asincrónica](http://msdn.microsoft.com/library/dn589781.aspx)
- [Instrucciones de escalado automático](http://msdn.microsoft.com/library/dn589774.aspx)
- [Patrón de transacción de compensación](http://msdn.microsoft.com/library/dn589804.aspx)
- [Patrón de consumidores de la competencia](http://msdn.microsoft.com/library/dn568101.aspx)
- [Orientación sobre la creación de particiones de proceso](http://msdn.microsoft.com/library/dn589773.aspx)
- [Patrón de consolidación de recursos de proceso](http://msdn.microsoft.com/library/dn589778.aspx)
- [Patrón de Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx)
- [Patrón de elección de líder](http://msdn.microsoft.com/library/dn568104.aspx)
- [Patrón de canalizaciones y filtros](http://msdn.microsoft.com/library/dn568100.aspx)
- [Patrón de cola de prioridad](http://msdn.microsoft.com/library/dn589794.aspx)
- [Patrón de equilibrio de carga basado en colas](http://msdn.microsoft.com/library/dn589783.aspx)
- [Patrón de supervisor de agente de programador](http://msdn.microsoft.com/library/dn589780.aspx)

## Más información

- [Escalado de aplicaciones de Azure con roles de trabajo](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Ejecutar tareas en segundo plano](http://msdn.microsoft.com/library/ff803365.aspx)
- [Ciclo de vida de inicio de roles de Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (entrada de blog)
- [Ciclo de vida del rol de Servicios en la nube de Azure](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vídeo)
- [Introducción al SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk-get-started.md)
- [Colas de Azure y colas de Bus de servicio: comparación y diferencias](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Cómo habilitar diagnósticos en un Servicio en la nube](cloud-services-dotnet-diagnostics.md)

<!---HONumber=AcomDC_0128_2016-->