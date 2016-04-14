<properties
   pageTitle="Exploración y administración de recursos de almacenamiento con el Explorador de servidores | Microsoft Azure"
   description="Exploración y administración de recursos de almacenamiento con el Explorador de servidores"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/27/2016"
   ms.author="tarcher" />

# Exploración y administración de recursos de almacenamiento con el Explorador de servidores

Si ha instalado Azure Tools para Microsoft Visual Studio, puede ver datos de blob, cola y tabla desde las cuentas de almacenamiento para Azure. El nodo Almacenamiento de Azure en el Explorador de servidores muestra datos que están en la cuenta del emulador de almacenamiento local y las otras cuentas de almacenamiento de Azure.

Para ver el Explorador de servidores en Visual Studio, en la barra de menús, elija **Ver**, **Explorador de servidores**. El nodo de almacenamiento muestra todas las cuentas de almacenamiento que existen en cada suscripción/certificado de Azure a los que esté conectado. Si no aparece la cuenta de almacenamiento, puede agregarla siguiendo las instrucciones [más adelante en este tema](#add-storage-accounts-by-using-server-explorer).

A partir del SDK de Azure versión 2.7, también puede usar el nuevo Cloud Explorer para ver y administrar los recursos de Azure. Consulte [Administración de recursos de Azure con Cloud Explorer](https://msdn.microsoft.com/library/azure/mt185741.aspx) para obtener más información.


## Visualización y administración de recursos de almacenamiento en Visual Studio

El Explorador de servidores muestra automáticamente una lista de blobs, colas y tablas en la cuenta del emulador de almacenamiento. La cuenta del emulador de almacenamiento aparece en el Explorador de servidores bajo el nodo Almacenamiento en el nodo **Desarrollo**.

Para ver los recursos de la cuenta del emulador de almacenamiento, expanda el nodo **Desarrollo**. Si el emulador de almacenamiento aún no está iniciado cuando expanda el nodo **Desarrollo**, se iniciará automáticamente. Esto puede tardar varios segundos. Puede continuar trabajando en otras zonas de Visual Studio mientras se inicia el emulador de almacenamiento.

Para ver los recursos en una cuenta de almacenamiento, expanda el nodo de la cuenta de almacenamiento en el Explorador de servidores. Aparecen los siguientes subnodos:

- Blobs

- Colas

- Tablas

## Trabajo con recursos de blob

El nodo Blobs muestra una lista de contenedores para la cuenta de almacenamiento seleccionada. Los contenedores de blobs incluyen archivos de blob, y puede organizar estos blobs en carpetas y subcarpetas. Para obtener más información, consulte [Uso del almacenamiento de blobs en .NET](..storage/storage-dotnet-how-to-use-blobs/).

### Para crear un contenedor de blobs

1. Abra el menú contextual del nodo **Blobs** y elija **Crear contenedor de blobs**.

1. Escriba el nombre del contenedor nuevo en el cuadro de diálogo **Crear contenedor de blobs** y después elija **Aceptar**.

    ![Agregar un nuevo contenedor de blobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744153.bmp)

    >[AZURE.NOTE] El nombre del contenedor de blobs debe comenzar con una letra minúscula (a–z) o un número (0-9).

### Para eliminar un contenedor de blobs

- Abra el menú contextual del contenedor de blobs que desee quitar y elija **Eliminar**.

### Para mostrar una lista de los elementos contenidos en un contenedor de blobs

- Abra el menú contextual para un nombre de contenedor de blobs de la lista y elija **Ver contenedor de blob**.

    Cuando se ve el contenido de un contenedor de blobs, aparece en una pestaña conocida como vista del contenedor de blobs.

    ![VST\_SE\_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Puede realizar las siguientes operaciones en blobs mediante los botones en la esquina superior derecha de la vista del contenedor de blobs:

    - Escribir un valor de filtro y aplicarlo

    - Actualizar la lista de blobs del contenedor

    - Cargar un archivo

    - Eliminar un blob

      >[AZURE.NOTE] Cuando elimina un archivo de un contenedor de blobs, no se elimina el archivo subyacente; solo se quita del contenedor de blobs.

    - Abrir un blob

    - Guardar un blob en el equipo local

### Para crear una carpeta o una subcarpeta en un contenedor de blobs

1. Elija el contenedor de blobs en el Explorador de servidores. En la ventana del contenedor, elija el botón **Cargar blob**.

    ![Cargar un archivo en una carpeta de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. En el cuadro de diálogo **Cargar nuevo archivo**, seleccione el botón **Examinar** para especificar el archivo que desea cargar y después escriba un nombre de carpeta en el cuadro **Carpeta (opcional)**.

    Puede agregar subcarpetas en carpetas de contenedor siguiendo el mismo procedimiento. Si no especifica un nombre de carpeta, el archivo se cargará en el nivel superior del contenedor de blobs. El archivo aparece en la carpeta especificada en el contenedor.

    ![Carpeta agregada a un contenedor de blobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Haga doble clic en la carpeta o presione ENTRAR para ver el contenido de la carpeta. Cuando esté en la carpeta del contenedor, puede navegar hasta la raíz del contenedor eligiendo el botón **Abrir directorio primario** (flecha arriba).

### Para eliminar una carpeta de contenedor

 - Elimine todos los archivos de la carpeta

    >[AZURE.NOTE] Dado que las carpetas en los contenedores de blob son virtuales, no puede crear una carpeta vacía ni eliminar una carpeta para eliminar los archivos que contiene. Primero debe eliminar todo el contenido de una carpeta para eliminar la carpeta.

### Para filtrar los blobs de un contenedor

Puede filtrar los blobs que se muestran especificando un prefijo común.

Por ejemplo, si escribe el prefijo `hello` en el cuadro de texto del filtro y después elige el botón **Ejecutar** (**!**), solo aparecen los blobs que comienzan con "hello".

![VST\_SE\_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE] El campo de filtro distingue mayúsculas de minúsculas y no admite el filtrado con caracteres comodín. Los blobs solo se pueden filtrar por prefijo. El prefijo puede incluir un delimitador si usa uno para organizar los blobs en una jerarquía virtual. Por ejemplo, el filtrado por el prefijo HelloFabric/ devuelve todos los blobs que comienzan con esa cadena.

### Para descargar datos de blob

- En el **Explorador de servidores**, abra el menú contextual para uno o más blobs y después elija **Abrir**, o bien elija el nombre del blob y después el botón **Abrir** o haga doble clic en el nombre del blob.

    Puede ver el progreso de la descarga del blob en la ventana **Registro de actividad de Microsoft Azure**.

    El blob se abre en el editor predeterminado para ese tipo de archivo. Si el sistema operativo reconoce el tipo de archivo, el archivo se abre en una aplicación instalada localmente; de lo contrario, se le pedirá que elija una aplicación que sea adecuada para el tipo de archivo del blob. El archivo local que se crea cuando descarga un blob está marcado como de solo lectura.

    Los datos de blob se almacenan en caché localmente y se cotejan con la hora de la última modificación del blob en el servicio BLOB. Si el blob se ha actualizado desde que se descargó por última vez, se descargará de nuevo; de lo contrario, el blob se cargará desde el disco local. De forma predeterminada, el blob se descarga en un directorio temporal. Para descargar blobs en un directorio específico, abra el menú contextual para los nombres de blob seleccionados y elija **Guardar como**. Cuando se guarda un blob de esta manera, no se abre el archivo blob y el archivo local se crea con atributos de lectura y escritura.

### Para cargar blobs

- Elija el botón **Cargar blob** cuando el contenedor esté abierto para su visualización en la vista del contenedor de blobs.

    Puede elegir uno o varios archivos para cargar, así como cargar archivos de cualquier tipo. Puede ver el progreso de la carga en la ventana **Registro de actividad de Microsoft Azure**. Para obtener más información acerca de cómo trabajar con datos de blob, vea [Uso del almacenamiento de blobs de .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### Para ver los registros transferidos a blobs

- Si usa Diagnósticos de Azure para registrar los datos de la aplicación de Azure y ha transferido los registros a la cuenta de almacenamiento, verá los contenedores creados por Azure para estos registros. Ver estos registros en el Explorador de servidores es una manera fácil de identificar los problemas de la aplicación, especialmente si se ha implementado en Azure. Para obtener más información sobre Diagnósticos de Azure, vea [Recopilar datos de registro mediante Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Para obtener la dirección URL de un blob

- Abra el menú contextual del blob y después elija **Copiar dirección URL**.

### Para editar un blob

- Seleccione el blob y después elija el botón **Abrir blob**.

    El archivo se descarga en una ubicación temporal y se abre en el equipo local. Debe cargar el blob de nuevo después de realizar cambios.

## Trabajo con recursos de cola

Las colas de servicios de almacenamiento se hospedan en una cuenta de almacenamiento de Azure y se pueden usar para permitir que los roles del servicio en la nube se comuniquen entre sí y con otros servicios mediante un mecanismo de transferencia de mensajes. Puede acceder a la cola mediante programación a través de un servicio en la nube y a través de un servicio web para los clientes externos. También puede acceder a la cola directamente mediante el Explorador de servidores en Visual Studio.

Cuando se desarrolla un servicio en la nube que use colas, es recomendable usar Visual Studio para crear colas y trabajar con ellas de forma interactiva mientras desarrolla y prueba el código.

En el Explorador de servidores, puede ver las colas de una cuenta de almacenamiento, crear y eliminar colas, abrir una cola para ver sus mensajes y agregar mensajes a una cola. Cuando se abre una cola para su visualización, puede ver los mensajes individuales y puede realizar las siguientes acciones en la cola mediante los botones en la esquina superior izquierda:

- Actualizar la vista de la cola

- Agregar un mensaje a la cola

- Quitar el primer mensaje de la cola

- Borrar la cola completa

En la siguiente imagen se muestra una cola que contiene dos mensajes.

![Ver una cola](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Para obtener más información acerca de las colas de servicios de almacenamiento, vea [Uso del almacenamiento de colas de .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Para obtener información sobre el servicio web para colas de servicios de almacenamiento, vea [Conceptos del servicio Cola](http://go.microsoft.com/fwlink/?LinkId=264788). Para obtener información acerca de cómo enviar mensajes a una cola de servicios de almacenamiento mediante Visual Studio, vea [Enviar mensajes a una cola de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE] Las colas de servicios de almacenamiento son distintas de las colas del Bus de servicio. Para obtener más información sobre las colas del Bus de servicio, consulte Colas, temas y suscripciones del Bus de servicio.

## Trabajo con recursos de tabla

El servicio de almacenamiento de tablas de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.

### Creación de una tabla

1. En el Explorador de servidores, seleccione el nodo **Tablas** de la cuenta de almacenamiento y después elija **Crear tabla**.

1. En el cuadro de diálogo **Crear tabla**, escriba un nombre para la tabla.

### Para ver los datos de tabla

1. En el Explorador de servidores, abra el nodo **Azure** y después el nodo **Almacenamiento**.

1. Abra el nodo de la cuenta de almacenamiento que le interese y después abra el nodo **Tablas** para ver una lista de tablas para la cuenta de almacenamiento.

1. Abra el menú contextual para una tabla y después elija **Ver tabla**.

    ![Una tabla de Azure en el Explorador de soluciones](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

La tabla se organiza por entidades (mostradas en filas) y propiedades (mostradas en columnas). Por ejemplo, en la siguiente ilustración se muestran las entidades que aparecen en el **Diseñador de tablas**:

### Para editar los datos de tabla

1. En el **Diseñador de tablas**, abra el menú contextual de una entidad (una sola fila) o una propiedad (una sola celda) y después elija **Editar**.

    ![Agregar o editar una entidad de tabla](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    No es necesario que las entidades de una sola tabla compartan el mismo conjunto de propiedades (columnas). Tenga en cuenta las siguientes restricciones de visualización y edición de datos de la tabla. -No puede ver o editar datos binarios (tipo byte), pero se puede almacenar en una tabla.

    - No se pueden editar los valores **PartitionKey** ni **RowKey**, porque el almacenamiento de tablas en Azure no admite esa operación.

    - No se puede crear una propiedad denominada Timestamp; los servicios de Almacenamiento de Azure usan una propiedad con ese nombre.

    - Si escribe un valor DateTime, debe seguir un formato adecuado para la configuración regional y de idioma del equipo (por ejemplo, MM/DD/AAAA HH:MM:SS [AM|PM] para Inglés de Estados Unidos).

### Para agregar entidades

1. En el **Diseñador de tablas**, elija el botón **Agregar entidad**, que está cerca de la esquina superior derecha de la vista de tabla.

    ![Agregar entidad](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. En el cuadro de diálogo **Agregar entidad**, escriba los valores de las propiedades **PartitionKey** y **RowKey**.

    ![Cuadro de diálogo Agregar entidad](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Especifique los valores con cuidado porque no se pueden cambiar después de cerrar el cuadro de diálogo a menos que elimine la entidad y vuelva a agregarla.

### Para filtrar entidades

Puede personalizar el conjunto de entidades que aparecen en una tabla si usa el generador de consultas.

1. Para abrir el generador de consultas, abra una tabla para su visualización.

1. Elija el botón más a la derecha de la barra de herramientas de la vista de tabla.

    Aparece el cuadro de diálogo **Generador de consultas**. En la siguiente ilustración, se muestra una consulta que se está generando en el generador de consultas.

    ![Generador de consultas](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Cuando haya terminado de generar la consulta, cierre el cuadro de diálogo. El formato de texto de la consulta resultante aparece en un cuadro de texto como un filtro de WCF Data Services.

1. Para ejecutar la consulta, elija el icono de triángulo verde.

    También puede filtrar los datos de la entidad que aparece en el **Diseñador de tablas** si escribe una cadena de filtro de WCF Data Services directamente en el campo de filtro. Este tipo de cadena es similar a una cláusula WHERE de SQL, pero se envía al servidor como solicitud HTTP. Para obtener información acerca de cómo construir cadenas de filtro, consulte [Crear cadenas de filtro para el Diseñador de tablas](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    En la siguiente ilustración se muestra un ejemplo de una cadena de filtro válida:

    ![VST\_SE\_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### Actualización de los datos de almacenamiento

Cuando el Explorador de servidores se conecta a una cuenta de almacenamiento u obtiene datos de ella, se puede tardar hasta un minuto en completar la operación. Si no se puede conectar, la operación podría agotar el tiempo de espera. Mientras se recuperan datos, puede continuar trabajando en otras zonas de Visual Studio. Para cancelar la operación si está tardando demasiado, elija el botón **Detener actualización** en la barra de herramientas del Explorador de servidores.

#### Para actualizar los datos del contenedor de blobs

- Seleccione el nodo **Blobs** bajo **Almacenamiento** y elija el botón **Actualizar** en la barra de herramientas del Explorador de servidores.

- Para actualizar la lista de blobs que se muestra, elija el botón **Ejecutar**.

#### Para actualizar los datos de tabla

- Seleccione el nodo **Tablas** bajo **Almacenamiento** y elija el botón **Actualizar**.

- Para actualizar la lista de entidades que se muestra en el **Diseñador de tablas**, elija el botón **Ejecutar** en el **Diseñador de tablas**.

#### Para actualizar los datos de cola

- Seleccione el nodo **Colas** y elija el botón **Actualizar**.

#### Para actualizar todos los elementos de una cuenta de almacenamiento

- Elija el nombre de la cuenta y después seleccione el botón **Actualizar** en la barra de herramientas del Explorador de servidores.

### Adición de cuentas de almacenamiento mediante el Explorador de servidores

Existen dos formas de agregar cuentas de almacenamiento mediante el Explorador de servidores. Puede crear una nueva cuenta de almacenamiento en su suscripción de Azure o puede asociar una cuenta de almacenamiento existente.

#### Para crear una nueva cuenta de almacenamiento mediante el Explorador de servidores

1. En el Explorador de servidores, abra el menú contextual del nodo Almacenamiento y después elija Crear cuenta de almacenamiento.

    ![Crear una nueva cuenta de almacenamiento de Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Seleccione o escriba la siguiente información para la nueva cuenta de almacenamiento en el cuadro de diálogo **Crear cuenta de almacenamiento**.

    - Suscripción de Azure a la que desee agregar la cuenta de almacenamiento.

    - Nombre que quiere usar para la nueva cuenta de almacenamiento.

    - La región o el grupo de afinidad (por ejemplo, Oeste de EE. UU. o Asia oriental).

    - El tipo de replicación que desea usar para la cuenta de almacenamiento, por ejemplo, con redundancia geográfica.

1. Seleccione **Create**.

    La nueva cuenta de almacenamiento aparecerá en la lista **Almacenamiento** del Explorador de soluciones.

#### Para asociar una nueva cuenta de almacenamiento mediante el Explorador de servidores

1. En el Explorador de servidores, abra el menú contextual del nodo de almacenamiento de Azure y después elija **Asociar almacenamiento externo**.

    ![Agregar una cuenta de almacenamiento existente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Seleccione o escriba la siguiente información para la nueva cuenta de almacenamiento en el cuadro de diálogo **Crear cuenta de almacenamiento**.

    - El nombre de la cuenta de almacenamiento existente que desea asociar. Puede escribir un nombre o seleccionarlo en la lista.

    - La clave para la cuenta de almacenamiento seleccionada. Este valor normalmente se proporciona automáticamente cuando se selecciona una cuenta de almacenamiento. Si desea que Visual Studio recuerde la clave de la cuenta de almacenamiento, active la casilla Recordar clave de cuenta.

    - El protocolo que se va a usar para conectarse a la cuenta de almacenamiento, como HTTP, HTTPS o un extremo personalizado. Consulte [Configurar las cadenas de conexión de Azure](https://msdn.microsoft.com/library/azure/ee758697.aspx) para obtener más información acerca de los extremos personalizados.

### Para ver los extremos secundarios

- Si ha creado una cuenta de almacenamiento con la opción de replicación **Redundancia geográfica con acceso de lectura**, puede ver sus extremos secundarios. Abra el menú contextual del nombre de cuenta y elija **Propiedades**.

    ![Extremos secundarios de almacenamiento](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### Para quitar una cuenta de almacenamiento del Explorador de servidores

- En el Explorador de servidores, abra el menú contextual del nombre de cuenta y elija **Eliminar**. Si elimina una cuenta de almacenamiento, también se quita cualquier información de clave guardada para esa cuenta.

    >[AZURE.NOTE] Si elimina una cuenta de almacenamiento del Explorador de servidores, esto no afecta a la cuenta de almacenamiento ni a ningún dato que contenga; simplemente se quita la referencia del Explorador de servidores. Para eliminar de forma permanente una cuenta de almacenamiento, use el Portal de administración de Azure.

## Pasos siguientes

Para obtener más información sobre cómo usar los servicios de almacenamiento de Azure, vea [Acceso a los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).

<!---HONumber=AcomDC_0204_2016-->