<properties
	pageTitle="Información general sobre las características de Lote de Azure | Microsoft Azure"
	description="Conozca las características del servicio Lote y sus API desde el punto de vista del desarrollo."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="02/25/2016"
	ms.author="yidingz;marsma"/>

# Información general de las características de Lote de Azure

Este artículo proporciona información general básica de las principales características de las API del servicio Lote de Azure. Tanto si desarrolla una solución informática distribuida mediante las API [REST de Lote][batch_rest_api] o [.NET de Lote][batch_net_api], usará muchas de las entidades y características que se describen a continuación.

> [AZURE.TIP] Si quiere información general técnica de alto nivel sobre el servicio Lote, consulte [Conceptos básicos sobre Lote de Azure](batch-technical-overview.md).

## <a name="workflow"></a>Flujo de trabajo del servicio Lote

El siguiente flujo de trabajo de alto nivel es el que se usa normalmente en todos los escenarios informáticos distribuidos desarrollados dentro del servicio Lote:

1. Cargue los *archivos de datos* que quiere usar en el escenario de cálculo en una cuenta de [almacenamiento de Azure][azure_storage]. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio Lote pueda tener acceso a ellos. Las tareas descargarán estos archivos en los [nodos de ejecución](#computenode) cuando se ejecutan.

2. Cargue los *archivos binarios* dependientes en la cuenta de almacenamiento. Estos archivos binarios incluyen el programa que van a ejecutar las tareas y cualquiera de sus ensamblados dependientes. Estos archivos también deben estar accesibles desde la cuenta de almacenamiento, por lo que las tareas pueden descargarlos en los nodos de ejecución.

3. Cree un [grupo](#pool) de nodos de ejecución. Cuando el grupo se crea, se especifica el [tamaño de los nodos de ejecución][cloud_service_sizes] que se usará y, cuando se ejecuta una tarea, se asigna un nodo en este grupo.

4. Cree un [trabajo](#job). Un trabajo le permite administrar una colección de tareas.

5. Agregue [tareas](#task) al trabajo. Cada tarea usa el programa que cargó para procesar la información de los archivos de datos que cargó en la cuenta de almacenamiento.

6. Supervise el progreso del trabajo y recupere los resultados.

> [AZURE.NOTE] Necesitará una [cuenta de Lote](batch-account-create-portal.md) para usar el servicio Lote, y casi todas las soluciones usarán una cuenta de [almacenamiento de Azure][azure_storage] para el almacenamiento y la recuperación de los archivos.

En las siguientes secciones, aprenderá sobre cada uno de los recursos mencionados en el flujo de trabajo anterior, así como muchas otras características de Lote que harán posible su escenario de cálculo distribuido.

## <a name="resource"></a> Recursos del servicio Lote

Cuando se usa el servicio Lote de Azure, se usan los siguientes recursos:

- [Cuenta](#account)
- [Nodo de ejecución](#computenode)
- [Grupo](#pool)
- [Trabajo](#job)
- [Task](#task)
	- [Tarea de inicio](#starttask)
	- [Trabajo de ManagerTask](#jobmanagertask)
	- [Tareas de preparación y liberación de trabajos](#jobpreprelease)
	- [Tareas de instancias múltiples](#multiinstance)
- [JobSchedule](#jobschedule)

### <a name="account"></a>Cuenta

Una cuenta de Lote es una entidad identificada de forma exclusiva en el servicio Lote. Todo el procesamiento se asocia con una cuenta de Lote. Al realizar operaciones con el servicio Lote, necesita el nombre y la clave de la cuenta. Para crear una cuenta de Lote, consulte [Creación y administración de una cuenta de Lote de Azure en el Portal de Azure](batch-account-create-portal.md).

### <a name="computenode"></a>Nodo de ejecución

Un nodo de ejecución es una máquina virtual de Azure dedicada a una carga de trabajo concreta de la aplicación. El tamaño de un nodo determina el número de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna al nodo. Un nodo puede tener cualquiera de los [tamaños de nodo de servicio en la nube][cloud_service_sizes], excepto A0.

Los nodos pueden ejecutar archivos ejecutables (.exe) y scripts, como archivos de comandos (.cmd), archivos por lotes (.bat) y scripts de PowerShell. Un nodo tiene también los siguientes atributos:

- En cada nodo de ejecución se crea una **estructura de carpetas** estándar y **variables de entorno** asociadas que detallan sus rutas de acceso. Consulte [Archivos y directorios](#files) para obtener más información.
- **Variables de entorno** disponibles para referencia por tareas.
- Configuración del **firewall** para controlar el acceso.
- Si es necesario el **acceso remoto** a un nodo de ejecución (por ejemplo, con fines de depuración), se puede obtener un archivo RDP que luego se puede usar para acceder al nodo mediante *Escritorio remoto*.

### <a name="pool"></a>Grupo

Un grupo es una colección de nodos en donde se ejecuta la aplicación. El usuario lo puede crear manualmente, o bien el servicio Lote lo crea automáticamente cuando se especifica el trabajo que se debe realizar. Puede crear y administrar un grupo que satisfaga las necesidades de su aplicación, y los grupos solo se pueden usar con la cuenta de Lote en la que se han creado. Una cuenta de Lote puede tener más de un grupo.

Los grupos de Lote de Azure se basan en la plataforma de proceso principal de Azure y proporcionan asignación a gran escala, instalación de aplicaciones, distribución de datos y supervisión de estado, además de ajuste flexible del número de nodos de ejecución en un grupo (escalado).

A cada nodo que se agrega a un grupo se le asigna un nombre y una dirección IP únicos. Cuando se quita un nodo de un grupo, los cambios realizados en el sistema operativo o los archivos se pierden, y su nombre y dirección IP se liberan para usarlos en un futuro. Cuando un nodo abandona un grupo, su vigencia finaliza.

Puede configurar un grupo que permita la comunicación entre los nodos que contiene. Si se solicita la comunicación dentro del grupo para un grupo, el servicio Lote habilita puertos superiores a 1100 en cada nodo del grupo. Cada nodo del grupo se configura para que permita conexiones entrantes solo a este intervalo de puertos, y solo si proceden de otros nodos del grupo. Si la aplicación no requiere comunicación entre los nodos, el servicio Lote puede asignar un número potencialmente alto de nodos al grupo desde muchos clústeres y centros de datos diferentes para permitir el aumento de la potencia del procesamiento paralelo.

Cuando se crea un grupo, puede especificar los siguientes atributos:

- **Tamaño de los nodos** del grupo
	- Se debe elegir el tamaño adecuado de los nodos, teniendo en cuenta las características y los requisitos de las aplicaciones que se van a ejecutar en ellos. El tamaño del nodo se selecciona normalmente suponiendo que no se ejecuta más de una tarea en el nodo a la vez. Considerar aspectos como que la aplicación sea multiproceso y cuánta memoria consume ayudará a determinar el tamaño de nodo más adecuado y rentable. En aquellos casos en los que sea necesario asignar varias tareas y ejecutar varias instancias de aplicaciones en paralelo, se elige normalmente un nodo más grande. Consulte "Directiva de programación de tareas" a continuación para obtener más información.
	- Todos los nodos de un grupo deben ser del mismo tamaño. Si se van a ejecutar diferentes aplicaciones con diferentes requisitos del sistema o niveles de carga, se deben crear grupos distintos.
	- Se pueden configurar todos los [tamaños de nodo de servicio en la nube][cloud_service_sizes] para un grupo, excepto A0.

- **Familia de sistema operativo** y **versión** que se ejecuta en los nodos
	- Al igual que con los roles de trabajo de Servicios en la nube, es posible especificar la *familia del SO* y la *versión del SO* (para obtener más información sobre los roles de trabajo, consulte la sección [Información sobre los servicios en la nube][about_cloud_services] en *Cálculo de las opciones de hospedaje proporcionadas por Azure*).
	- La familia del sistema operativo también determina qué versiones de .NET están instaladas con el sistema operativo.
	- Al igual que con los roles de trabajo, se recomienda que se especifique `*` para la versión del sistema operativo para que los nodos se actualicen automáticamente y no sea necesario realizar ningún trabajo en las versiones publicadas recientemente. El caso de uso principal para seleccionar una versión específica del sistema operativo es asegurarse de que se mantenga la compatibilidad de las aplicaciones y que se puedan realizar pruebas de compatibilidad con versiones anteriores antes de permitir la actualización de la versión. Una vez validada, se puede actualizar la versión del sistema operativo para el grupo e instalar la nueva imagen del sistema operativo. Cualquier tarea en ejecución se interrumpe y se vuelve a poner en cola.

- El **número objetivo de nodos** que deben estar disponibles para el grupo

- La **directiva de escalado** para el grupo
	- Además del número de nodos, también puede especificar una [fórmula de escalado automático](batch-automatic-scaling.md) para un grupo. El servicio Lote ejecutará la fórmula y ajustará el número de nodos dentro del grupo en función de diversos parámetros de grupo, trabajo y tarea que especifique.

- Directiva de **programación de tareas**
	- La opción de configuración del [número máximo de tareas por nodo](batch-parallel-node-tasks.md) determina la cantidad máxima de tareas que se pueden ejecutar en paralelo en cada nodo del grupo.
	- La configuración predeterminada permite únicamente que se ejecute un solo nodo de ejecución a la vez. Sin embargo, hay situaciones donde resulta ventajoso tener más de una tarea ejecutándose en un nodo al mismo tiempo. Por ejemplo, para aumentar la utilización del nodo si una aplicación debe esperar la E/S. Tener más de una aplicación ejecutándose a la vez aumentará el uso de CPU. Otro ejemplo es para reducir el número de nodos en el grupo. Esta situación podría reducir la cantidad de transferencia de datos necesaria para conjuntos de datos de referencia de gran tamaño; si un tamaño de nodo A1 es suficiente para una aplicación, se podría elegir entonces el tamaño de nodo A4 y configurar el grupo para 8 tareas paralelas, cada una con un núcleo.
	- También se puede especificar un "tipo de relleno" que determina si el servicio Lote distribuye las tareas uniformemente entre todos los nodos, o empaqueta cada nodo con el número máximo de tareas antes de asignarlas a otro nodo del grupo.

- **Estado de comunicación** de los nodos del grupo.
	- Un grupo se puede configurar para permitir la comunicación entre los nodos del grupo, lo que determina su infraestructura de red subyacente. Tenga en cuenta que esto también afecta a la colocación de los nodos en los clústeres.
	- En la mayoría de los escenarios, las tareas funcionan de forma independiente y no es necesario que se comuniquen entre sí, pero puede haber aplicaciones en las que las tareas deban comunicarse.

- **Tarea de inicio** para los nodos del grupo
	- Se puede especificar una *tarea de inicio* que se ejecute cada vez que un nodo de ejecución se una al grupo y cuando se reinicie un nodo. Con frecuencia esto se usa para instalar una aplicación que usarán las tareas que se ejecutan en el nodo.

### <a name="job"></a>Trabajo

Un trabajo es una colección de tareas y especifica cómo se realiza el cálculo en los nodos de ejecución de un grupo.

- El trabajo especifica el **grupo** en el que se va a ejecutar el trabajo. El grupo puede ser un grupo existente, se puede haber creado anteriormente para su uso por muchos trabajos, o bien se puede haber creado a petición para cada trabajo asociado con una programación de trabajo o para todos los trabajos asociados con una programación de trabajo.
- Se puede especificar una **prioridad de trabajo** opcional. Cuando se envía un trabajo con una prioridad más alta que la de otros trabajos actualmente en curso, las tareas del trabajo de prioridad más alta se insertan en la cola por delante de las tareas del trabajo de prioridad más baja. Las tareas de prioridad más baja que ya están en ejecución no se adelantan.
- Las **restricciones** del trabajo especifican determinados límites para sus trabajos.
	- Se puede asignar un **tiempo máximo** para los trabajos. Si los trabajos se ejecutan durante más tiempo del máximo especificado, se pondrá fin al trabajo y a todas las tareas asociadas.
	- Lote de Azure puede detectar tareas que hayan generado errores y probar a ejecutarlas de nuevo. Se puede especificar el **número máximo de reintentos de tarea** como una restricción, incluso si una tarea se reintenta siempre o no se reintenta nunca. Volver a intentar una tarea significa que la tarea se vuelve a poner en cola para ejecutarse de nuevo.
- Las tareas se pueden agregar al trabajo mediante la aplicación cliente, o bien se puede especificar una [tarea del Administrador de trabajos](#jobmanagertask). Una tarea del Administrador de trabajos usa la API de Lote y contiene la información necesaria para crear las tareas que se requieren para un trabajo. Estas tareas se ejecutan en uno de los nodos de ejecución del grupo. El servicio Lote controla específicamente las tareas del Administrador de trabajos: las pone en cola en cuanto se crea el trabajo y las reinicia si se produce un error. Se requiere una tarea del Administrador de trabajos para los trabajos creados con una programación de trabajos, ya que es la única manera de definir las tareas antes de que se cree una instancia del trabajo. Puede encontrar más información sobre las tareas del Administrador de trabajos a continuación.


### <a name="task"></a>Tarea

Una tarea es una unidad de cálculo que está asociada a un trabajo y se ejecuta en un nodo. Las tareas se asignan a un nodo para su ejecución o se ponen en cola hasta que quede libre un nodo. Una tarea usa los siguientes recursos:

- La aplicación especificada en la **línea de comandos** de la tarea.

- Los **archivos de recursos** que contienen los datos que se procesan. Estos archivos se copian automáticamente en el nodo del almacenamiento de blobs en una cuenta de almacenamiento de Azure. Para obtener más información, consulte a continuación [Archivos y directorios](#files).

- Las **variables de entorno** que requiere la aplicación. Para obtener más información, consulte a continuación [Configuración del entorno para las tareas](#environment).

- Las **restricciones** con las que debe realizarse el cálculo. Por ejemplo, el tiempo máximo en el que la tarea se puede ejecutar, el número máximo de veces que una tarea debe intentarse si se produce un error y el tiempo máximo que se conservan los archivos en el directorio de trabajo.

Además de las tareas que se pueden definir para realizar cálculos en un nodo, las siguientes tareas especiales también se proporcionan en el servicio Lote:

- [Tarea de inicio](#starttask)
- [Tareas del Administrador de trabajos](#jobmanagertask)
- [Tareas de preparación y liberación de trabajos](#jobmanagertask)
- [Tareas de instancias múltiples](#multiinstance)

#### <a name="starttask"></a>Tarea de inicio

Mediante la asociación de una **tareas de inicio** con un grupo, puede configurar el entorno operativo de sus nodos, realizar acciones, como instalar software, o iniciar procesos en segundo plano. La tarea de inicio se ejecuta cada vez que se inicia un nodo siempre y cuando dicho nodo permanezca en el grupo. Aquí se incluye también la primera vez que el nodo se agrega al grupo. Una de las principales ventajas de la tarea de inicio es que contiene toda la información necesaria para configurar los nodos de ejecución e instalar las aplicaciones requeridas para la ejecución de las tareas del trabajo. Por lo tanto, aumentar el número de nodos en un grupo es tan sencillo como especificar el nuevo número de nodos de destino; el servicio Lote ya tiene toda la información necesaria para configurar los nuevos nodos y tenerlos preparados para aceptar tareas.

Como con cualquier tarea por lotes, se puede especificar una **lista de archivos** en el [almacenamiento de Azure][azure_storage], además de una **línea de comandos** para ejecutar. El servicio Lote de Azure copia primero los archivos desde el almacenamiento de Azure y luego ejecuta la línea de comandos. En una tarea de inicio de grupo, la lista de archivos contiene normalmente el paquete o los archivos de la aplicación, pero podría incluir también datos de referencia que usarán todas las tareas que se ejecuten en los nodos de ejecución. La línea de comandos de la tarea de inicio podría ejecutar un script de PowerShell o realizar una operación `robocopy`, por ejemplo, para copiar los archivos de aplicación en la carpeta "compartida" y posteriormente ejecutar un MSI o `setup.exe`.

Habitualmente, es conveniente que el servicio Lote espere a que la tarea de inicio finalice antes de considerar que el nodo está listo para asignarle tareas, pero este procedimiento es configurable.

Si una tarea de inicio da error en un nodo de ejecución, el estado del nodo se actualiza para reflejar el error y el nodo estará disponible para asignarle tareas. Una tarea de inicio puede dar error si existe un problema al copiar sus archivos de recursos desde el almacenamiento, o si el proceso ejecutado mediante su línea de comandos devuelve un código de salida distinto de cero.

#### <a name="jobmanagertask"></a>Tarea del Administrador de trabajos

Una **tarea del Administrador de trabajos** se usa normalmente para controlar o supervisar la ejecución de trabajos. Por ejemplo, para crear y enviar las tareas de un trabajo, determinar las tareas adicionales que se ejecutarán y determinar cuándo un trabajo está completo. No obstante, una tarea del Administrador de trabajos no se limita a estas actividades, es una tarea completa que puede realizar cualquier acción que exija el trabajo. Por ejemplo una tarea del Administrador de trabajos podría descargar un archivo especificado como un parámetro, analizar el contenido de ese archivo y enviar tareas adicionales en función de ese contenido.

Una tarea del Administrador de trabajos se inicia antes que todas las demás tareas y proporciona las siguientes características:

- Se envía automáticamente como una tarea mediante el servicio Lote cuando se crea el trabajo.

- Está programada para ejecutarse antes que las otras tareas de un trabajo.

- Su nodo asociado es el último en eliminarse de un grupo cuando se reduce el tamaño del grupo.

- Su finalización se puede vincular a la finalización de todas las tareas en el trabajo.

- A la tarea del Administrador de trabajos se le otorga la más alta prioridad cuando es necesario reiniciarla. Si un nodo inactivo no está disponible, el servicio Lote puede finalizar una de las otras tareas en ejecución del grupo para dejar espacio para la ejecución de la tarea del Administrador de trabajos.

- Una tarea del Administrador de trabajos de un trabajo no tiene prioridad sobre las tareas de otros trabajos. Entre trabajos, solo se tienen en cuenta las prioridades de nivel de trabajo.

#### <a name="jobpreprelease"></a>Tareas de preparación y liberación de trabajos

El servicio Lote proporciona la tarea de preparación del trabajo para la configuración de ejecución previa al trabajo y la tarea de liberación del trabajo para el mantenimiento o la limpieza posteriores al trabajo.

- **Tarea de preparación del trabajo**: la tarea de preparación del trabajo se ejecuta en todos los nodos de ejecución programados para ejecutar tareas, antes de la ejecución de ninguna otra tarea de trabajo. Use la tarea de preparación del trabajo para, por ejemplo, copiar los datos compartidos por todas las tareas, pero que sean únicos para el trabajo.
- **Tarea de liberación del trabajo**: cuando un trabajo se ha completado, la tarea de liberación del trabajo se ejecuta en cada nodo del grupo que ejecutó al menos una tarea. Use la tarea de liberación del trabajo para eliminar los datos copiados por la tarea de preparación del trabajo, o comprima y cargue datos del registro de diagnóstico, por ejemplo.

Las tareas de preparación y liberación del trabajo le permiten especificar una línea de comandos que se ejecutará cuando se invoque la tarea y ofrecen características como descarga de archivos, ejecución con privilegios elevados, variables de entorno personalizadas, duración de ejecución máxima, número de reintentos y tiempo de retención de archivos.

Para obtener más información sobre las tareas de preparación y liberación del trabajo, consulte [Ejecución de las tareas de preparación y realización de trabajos en los nodos de ejecución de Lote de Azure](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Tareas de instancias múltiples

Una [tarea de instancias múltiples](batch-mpi.md) es una tarea que está configurada para ejecutarse en más de un nodo de proceso al mismo tiempo. Con tareas de instancias múltiples, puede habilitar escenarios de informática de alto rendimiento como, por ejemplo, la interfaz de paso de mensajes (MPI) que requiere tener un grupo de nodos de ejecución asignados juntos para procesar una carga de trabajo única.

Para obtener información detallada sobre la ejecución de trabajos de MPI en el servicio Lote mediante la biblioteca .NET del mismo, consulte [Uso de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Lote de Azure](batch-mpi.md).

### <a name="jobschedule"></a>Trabajos programados

Las programaciones de trabajos permiten crear tareas recurrentes dentro del servicio Lote. Una programación de trabajo especifica cuándo se deben ejecutar los trabajos e incluye las especificaciones de los trabajos que se van a ejecutar. Una programación de trabajo permite la especificación de la duración de la programación: por cuánto tiempo y cuándo es efectiva la programación y con qué frecuencia se deben crear trabajos durante ese período de tiempo.

## <a name="files"></a>Archivos y directorios

Cada tarea tiene un directorio de trabajo en el que se crean cero o más archivos y directorios para almacenar el programa que ejecuta la tarea, los datos que procesa y el resultado del procesamiento realizado por la tarea. Estos archivos y directorios están luego disponibles para su uso por parte de otras tareas durante la ejecución de un trabajo. Todas las tareas, archivos y directorios de un nodo pertenecen a una cuenta de usuario único.

El servicio Lote expone parte del sistema de archivos en un nodo como "directorio raíz". El directorio raíz está disponible para una tarea mediante el acceso a la variable de entorno `%AZ_BATCH_NODE_ROOT_DIR%`. Para obtener más información sobre el uso de variables de entorno, consulte [Configuración del entorno para las tareas](#environment).

![Estructura de directorio de nodo de ejecución][1]

El directorio raíz contiene la siguiente estructura de directorio:

- **Shared**: esta ubicación es un directorio compartido para todas las tareas que se ejecutan en un nodo, con independencia del trabajo. En el nodo, se tiene acceso al directorio compartido a través de `%AZ_BATCH_NODE_SHARED_DIR%`. Este directorio proporciona acceso de lectura y escritura a todas las tareas que se ejecutan en el nodo. Las tareas pueden crear, leer, actualizar y eliminar archivos de este directorio.

- **Startup**: una tarea de inicio usa esta ubicación como directorio de trabajo. Todos los archivos descargados por el servicio Lote para iniciar la tarea de inicio también se almacenan en este directorio. En el nodo, el directorio de inicio está disponible a través de la variable de entorno `%AZ_BATCH_NODE_START_DIR%`. La tarea de inicio puede crear, leer, actualizar y eliminar archivos de este directorio, y puede usar este directorio para configurar el sistema operativo.

- **Tasks**: se crea un directorio para cada tarea que se ejecuta en el nodo, a la que se tiene acceso a través de `%AZ_BATCH_TASK_DIR%`. Dentro de cada directorio de la tarea, el servicio Lote crea un directorio de trabajo (`wd`) cuya ruta de acceso único se especifica con la variable de entorno `%AZ_BATCH_TASK_WORKING_DIR%`. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos de este directorio, y este directorio se conserva en función de la restricción *RetentionTime* especificada para la tarea.
  - `stdout.txt` y `stderr.txt`: estos archivos se escriben en la carpeta de tareas durante la ejecución de la tarea.

Cuando se quita un nodo del grupo, se quitan todos los archivos que están almacenados en el nodo.

## <a name="lifetime"></a>Duración de nodo de grupo y ejecución

Al diseñar la solución de Lote de Azure, se debe tomar una decisión de diseño con respecto a cómo y cuándo se crean los grupos y cuánto tiempo se mantendrán disponibles los nodos de ejecución dentro de esos grupos.

Por una parte, se podría crear un grupo para cada trabajo cuando se envíe el trabajo y eliminar sus nodos a medida que se terminen de ejecutar las tareas. De esta manera se maximiza su uso dado que los nodos solo se asignan cuando es absolutamente necesario y se apagan en cuanto se vuelven inactivos. Aunque esto significa que el trabajo debe esperar a que se asignen los nodos, es importante tener en cuenta que las tareas se programarán para los nodos tan pronto estén disponibles de forma individual, se asignen y se haya completado la tarea de inicio. El servicio Lote *no* espera a que todos los nodos dentro de un grupo estén disponibles antes de asignar las tareas, lo que garantiza la utilización máxima de todos los nodos disponibles.

Por otro lado, si lo más importante es que los trabajos se inicien inmediatamente, se puede crear un grupo antes de tiempo y hacer que sus nodos estén disponibles antes de que se envíen los trabajos. En este escenario, las tareas de trabajo se pueden iniciar inmediatamente, pero los nodos pueden permanecer inactivos mientras esperan a que se asignen las tareas.

Un enfoque combinado, usado normalmente para controlar la carga variable pero continuada, es tener un grupo al que se envían varios trabajos, pero escalar o reducir el número de nodos verticalmente de acuerdo con la carga del trabajo (consulte a continuación *Escalado de aplicaciones*). Esto puede realizarse de manera reactiva, según la carga actual o de forma anticipada si se puede predecir la carga.

## <a name="scaling"></a>Escalado de aplicaciones

Con el [escalado automático](batch-automatic-scaling.md) puede hacer que el servicio Lote ajuste dinámicamente el número de nodos de proceso en un grupo según la carga de trabajo y el uso de los recursos de su escenario de proceso. Esto le permite reducir el costo general de la ejecución de la aplicación usando solo los recursos que necesita, y liberando los que no. Puede especificar la configuración del escalado automático para un grupo cuando se crea o habilitar el escalado más adelante, y puede actualizar la configuración del escalado en un grupo habilitado para escalado automático.

El escalado automático se realiza especificando una **fórmula de escalado automático** para un grupo. El servicio Lote usa esta fórmula para determinar el número objetivo de nodos del grupo para el siguiente intervalo de escalado (un intervalo que puede especificar).

Por ejemplo, es posible que un trabajo requiera que envíe un gran número de tareas para programar su ejecución. Puede asignar al grupo una fórmula de escalado que ajuste el número de nodos del grupo en función del número actual de tareas pendientes y de la tasa de finalización de esas tareas. El servicio Lote evalúa periódicamente la fórmula y cambia el tamaño del grupo en función de la carga de trabajo y de la configuración de la fórmula.

Una fórmula de escalado puede basarse en las siguientes métricas:

- **Métricas de tiempo**: basadas en las estadísticas recopiladas cada cinco minutos en el número especificado de horas.

- **Métricas de recursos**: basadas en el uso de CPU, ancho de banda y memoria y en el número de nodos.

- **Métricas de tareas**: basadas en el estado de las tareas, como Activo, Pendiente y Completado.

Cuando el escalado automático reduce el número de nodos de proceso en un grupo, deben tenerse en cuenta las tareas actualmente en ejecución. Para ello, la fórmula puede incluir una configuración de directiva de desasignación de nodos que especifica si las tareas en ejecución se detendrán inmediatamente o si pueden finalizar antes de quitar el nodo del grupo.

> [AZURE.TIP] Para maximizar el uso de los recursos de proceso, establezca en cero el número objetivo de nodos al final de un trabajo, pero permita que las tareas en ejecución finalicen.

Para obtener más información sobre cómo escalar automáticamente una aplicación, consulte [Escalado automático de nodos de ejecución en un grupo de Lote de Azure](batch-automatic-scaling.md).

## <a name="cert"></a>Seguridad con certificados

Normalmente necesitará usar certificados al cifrar o descifrar información confidencial para las tareas, como la clave de una [cuenta de almacenamiento de Azure][azure_storage]. Para ayudar en esta situación, se pueden instalar certificados en los nodos. Los secretos cifrados se pasan a las tareas mediante parámetros de línea de comandos o se insertan en uno de los recursos de tarea, y los certificados instalados se pueden usar para descifrarlos.

Use la operación [Agregar certificado][rest_add_cert] (API de REST de Lote) o el método [CertificateOperations.CreateCertificate][net_create_cert] (API de .NET de Lote) para agregar un certificado a una cuenta de Lote. Se puede asociar el certificado a un grupo nuevo o existente. Cuando se asocia un certificado a un grupo, el servicio Lote instala el certificado en cada nodo del grupo. El servicio Lote instala los certificados adecuados cuando se inicia el nodo, antes de iniciar ninguna tarea, lo cual incluye tareas de inicio y tareas del Administrador de trabajos.

## <a name="scheduling"></a>Prioridad de programación

Puede asignar una prioridad a los trabajos que se crean en Lote. El servicio Lote usa los valores de prioridad del trabajo para determinar el orden de programación de trabajos dentro de una cuenta. Los valores de prioridad pueden oscilar entre -1000 y 1000, siendo -1000 la prioridad más baja y 1000 la más alta. Puede actualizar la prioridad de un trabajo mediante la operación [Actualizar las propiedades de un trabajo][rest_update_job] (API de REST de Lote) o modificando la propiedad [CloudJob.Priority][net_cloudjob_priority] (API de .NET de Lote).

Dentro de la misma cuenta, los trabajos de mayor prioridad tienen primacía de programación sobre los trabajos con menor prioridad. Un trabajo con un valor de prioridad determinado en una cuenta no tiene primacía de programación sobre otro trabajo con un valor de prioridad inferior de una cuenta diferente.

La programación de trabajos entre los grupos es independiente. Entre grupos diferentes, no se garantiza que un trabajo con una prioridad más alta se programe antes si el grupo asociado tiene pocos nodos inactivos. En el mismo grupo, los trabajos con el mismo nivel de prioridad tienen la misma probabilidad de ser programados.

## <a name="environment"></a>Configuración del entorno para las tareas

Cada tarea que se ejecuta dentro de un trabajo de Lote tiene acceso al conjunto variables establecido por el servicio Lote (definido por el sistema, consulte la tabla a continuación), así como a las variables de entorno definidas por el usuario. Las aplicaciones y scripts ejecutados por las tareas en los nodos de ejecución tienen acceso a estas variables de entorno durante la ejecución en el nodo.

Establezca variables de entorno definidas por el usuario cuando use la operación [Agregar una tarea a un trabajo][rest_add_task] (API de REST de Lote) o modifique la propiedad [CloudTask.EnvironmentSettings][net_cloudtask_env] (API de .NET Lote) al agregar tareas a un trabajo.

Obtenga las variables de entorno de una tarea, tanto las definidas por el usuario como por el sistema, mediante la operación [Obtener información sobre una tarea][rest_get_task_info] (API de REST de Lote) o mediante el acceso a la propiedad [CloudTask.EnvironmentSettings][net_cloudtask_env] (API de .NET de Lote). Como se mencionó, los procesos que se ejecutan en un nodo de ejecución también pueden acceder a todas las variables de entorno mediante el uso de, por ejemplo, la conocida sintaxis `%VARIABLE_NAME%`.

Para cada tarea programada dentro de un trabajo, el siguiente conjunto de variables de entorno definidas por el sistema lo establece el servicio Lote:

| Nombre de la variable de entorno | Descripción |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | El nombre de la cuenta a la que pertenece la tarea. |
| `AZ_BATCH_JOB_ID` | El id. del trabajo al que pertenece la tarea. |
| `AZ_BATCH_JOB_PREP_DIR` | Ruta de acceso completa del directorio de tareas de preparación del trabajo en el nodo. |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | Ruta de acceso completa del directorio de trabajo de tareas de preparación del trabajo en el nodo. |
| `AZ_BATCH_NODE_ID` | El id. del nodo en el que se ejecuta la tarea. |
| `AZ_BATCH_NODE_ROOT_DIR` | Ruta de acceso completa del directorio raíz en el nodo. |
| `AZ_BATCH_NODE_SHARED_DIR` | Ruta de acceso completa del directorio compartido en el nodo. |
| `AZ_BATCH_NODE_STARTUP_DIR` | La ruta de acceso completa del directorio de la tarea de inicio del nodo de ejecución en el nodo. |
| `AZ_BATCH_POOL_ID` | El id. del grupo en el que se ejecuta la tarea. |
| `AZ_BATCH_TASK_DIR` | Ruta de acceso completa del directorio de tareas en el nodo. |
| `AZ_BATCH_TASK_ID` | El id. de la tarea actual. |
| `AZ_BATCH_TASK_WORKING_DIR` | Ruta de acceso completa del directorio de trabajo en el nodo. |

>[AZURE.NOTE] No se puede sobrescribir ninguna de las variables definidas por el sistema mencionadas antes, ya que son de solo lectura.

## <a name="errorhandling"></a>Control de errores

Esta operación puede ser necesaria para controlar los errores de las tareas y las aplicaciones en la solución de Lote.

### Control de errores de tareas
Los errores de las tareas se incluyen en estas categorías:

- **Errores de programación**
	- Si por cualquier motivo se produce un error en la transferencia de archivos especificada para una tarea, se establece un "error de programación" para la tarea.
	- Las causas de los errores de programación podrían ser que los archivos se han movido, la cuenta de almacenamiento ya no está disponible o a cualquier otro problema encontrado que impidió la copia correcta de los archivos en el nodo.
- **Errores de aplicación**
	- El proceso especificado por la línea de comandos de la tarea también puede presentar errores. Se considera que el proceso ha dado error cuando el proceso ejecutado por la tarea devuelve un código de salida distinto de cero.
	- Para los errores de aplicaciones, es posible configurar el servicio Lote para volver a intentar automáticamente la tarea hasta un número especificado de veces.
- **Errores de restricción**
	- Se puede establecer una restricción que especifique la duración máxima de ejecución de un trabajo o una tarea con *maxWallClockTime*. Esto puede ser útil para terminar las tareas "bloqueadas".
	- Cuando se supere la cantidad máxima de tiempo, la tarea se marcará como *completada* pero el código de salida se establecerá en `0xC000013A` y el campo *schedulingError* se marcará como `{ category:"ServerError", code="TaskEnded"}`.

### Depuración de errores de aplicación

Durante la ejecución, una aplicación produce resultados de diagnóstico que se pueden usar para la solución de problemas. Como se mencionó anteriormente en [Archivos y directorios](#files), el servicio Lote envía salidas stdout y stderr a los archivos `stdout.txt` y `stderr.txt` ubicados en el directorio de tareas en el nodo de ejecución. Mediante el uso de [ComputeNode.GetNodeFile][net_getfile_node] y [CloudTask.GetNodeFile][net_getfile_task] en la API de .NET de Lote, puede recuperar estos y otros archivos para solucionar problemas.

Se puede realizar incluso una depuración más amplia iniciando sesión en un nodo de ejecución mediante *Escritorio remoto*. También puede [obtener un archivo de protocolo de escritorio remoto de un nodo][rest_rdp] (API de REST de Lote) o usar el método [ComputeNode.GetRDPFile][net_rdp] (API de .NET Lote) para el inicio de sesión remoto.

>[AZURE.NOTE] Para conectarse a un nodo a través de RDP, primero debe crear un usuario en el nodo. Para ello, [Agregue una cuenta de usuario a un nodo][rest_create_user] en la API de REST o use el método [ComputeNode.CreateComputeNodeUser][net_create_user] en la API de .NET de Lote.

### Consideraciones sobre errores o interrupciones

En ocasiones las tareas pueden presentar errores o interrumpirse. La propia aplicación de la tarea puede dar error, el nodo en el que se ejecuta la tarea se puede reiniciar o el nodo podría eliminarse del grupo durante una operación de cambio de tamaño si la directiva de desasignación del grupo está configurada para eliminar los nodos inmediatamente sin esperar a que finalicen las tareas. En todos los casos, el servicio Lote puede volver a poner la tarea automáticamente en la cola para ejecutarla en otro nodo.

También es posible que un problema intermitente haga que una tarea deje de responder o tarde demasiado tiempo en ejecutarse. El tiempo de ejecución máximo se puede definir para una tarea. Si se supera este valor, el servicio Lote interrumpirá la aplicación de la tarea.

### Solución de problemas de nodos de proceso “incorrectos”

Si algunas de las tareas producen errores, el servicio o la aplicación de cliente de Lote pueden examinar los metadatos de las tareas con errores para identificar un nodo con un comportamiento incorrecto. Cada nodo de un grupo recibe un identificador único y el nodo en el que se ejecuta una tarea se incluye en los metadatos de la tarea. Una vez identificado, puede realizar varias acciones:

- **Reiniciar el nodo** ([REST][rest_reboot] | [.NET][net_reboot])

	Reiniciar el nodo a veces puede solucionar problemas latentes tales como procesos bloqueados. Tenga en cuenta que si su grupo usa una tarea de inicio o el trabajo usa una tarea de preparación de trabajo, se ejecutarán cuando el nodo se reinicie.

- **Restablecer la imagen inicial del nodo** ([REST][rest_reimage] | [.NET][net_reimage])

	Esto reinstala el sistema operativo en el nodo. Al igual que al reiniciar un nodo, las tareas de inicio y las tareas de preparación del trabajo se vuelven a ejecutar después de restablecer la imagen inicial del nodo.

- **Quitar el nodo del grupo** ([REST][rest_remove] | [.NET][net_remove])

	A veces es necesario quitar completamente el nodo del grupo.

- **Deshabilitar la programación de tareas en el nodo** ([REST][rest_offline] | [.NET][net_offline])

	Esto desconecta el nodo para que no se le asigne ninguna tarea adicional, pero permite que el nodo permanezca en ejecución y en el grupo. Esto permite seguir investigando la causa de los errores sin perder los datos de la tarea con errores, y sin que el nodo produzca más errores en la tarea. Por ejemplo, puede deshabilitar la programación de tareas en el nodo, luego iniciar una sesión remota para examinar los registros de eventos del nodo o solucionar otros problemas. Después de terminar la investigación, puede volver a conectar el nodo mediante la habilitación de la programación de tareas ([REST][rest_online], [.NET][net_online]), o realizar una de las otras acciones mencionadas anteriormente.

> [AZURE.IMPORTANT] Con cada acción anterior (reiniciar, restablecer la imagen inicial, quitar, deshabilitar la programación de tareas), puede especificar cómo se controlan las tareas que se están ejecutando en el nodo al realizar la acción. Por ejemplo, cuando se deshabilita la programación de tareas en un nodo con la biblioteca de cliente .NET de Lote, puede especificar un valor de enumeración [DisableComputeNodeSchedulingOption][net_offline_option] para especificar si se terminarán las tareas en ejecución (**Terminate**), si se volverán a poner en la cola para su programación en otros nodos (**Requeue**), o si se permitirá que las tareas en ejecución finalicen antes de realizar la acción (**TaskCompletion**).

## Pasos siguientes

- Cree su primera aplicación de Lote siguiendo los pasos que se describen en [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md).
- Descargue y compile el proyecto de ejemplo [Batch Explorer][batch_explorer_project] para usarlo mientras desarrolla sus soluciones de Lote. Mediante Batch Explorer puede realizar lo siguiente y mucho más:
  - Supervisar y manipular grupos, trabajos y tareas dentro de su cuenta de Lote
  - Descargar `stdout.txt`, `stderr.txt`, y otros archivos de los nodos
  - Crear usuarios en los nodos y descargar archivos RDP para el inicio de sesión remoto.

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: https://azure.microsoft.com/documentation/articles/fundamentals-application-models/#tell-me-about-cloud-services
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/es-ES/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

<!---HONumber=AcomDC_0302_2016-->