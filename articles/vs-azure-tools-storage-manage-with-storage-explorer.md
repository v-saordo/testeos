<properties
	pageTitle="Administración de recursos de almacenamiento de Azure con el Explorador de almacenamiento (versión preliminar) | Microsoft Azure"
	description="Describe cómo utilizar el Explorador de almacenamiento de Microsoft Azure (versión preliminar) para crear y administrar recursos de almacenamiento de Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="01/30/2016"
	ms.author="tarcher" />

# Administración de recursos de almacenamiento de Azure con el Explorador de almacenamiento (versión preliminar)

El Explorador de almacenamiento de Microsoft Azure (versión preliminar) es una herramienta independiente que permite administrar fácilmente las cuentas de almacenamiento de Azure. Es útil en situaciones donde desea administrar rápidamente el almacenamiento fuera del Portal de Azure, por ejemplo, a la hora de desarrollar aplicaciones en Visual Studio. Esta versión preliminar permite trabajar fácilmente con el almacenamiento de blobs. Puede crear y eliminar contenedores, cargar, descargar y eliminar blobs y buscar en todos los contenedores y blobs. Las características avanzadas permiten a desarrolladores y operadores trabajar con las directivas y las claves SAS. Los desarrolladores de Windows también pueden usar el emulador de almacenamiento de Azure para probar su código con la cuenta de almacenamiento de desarrollo local.

Para ver o administrar los recursos de almacenamiento en el Explorador de almacenamiento, es necesario que tenga acceso a una cuenta de almacenamiento de Azure, bien en su suscripción o en una cuenta externa. Si no tiene ninguna, puede crear una cuenta de almacenamiento en un par de minutos. Si tiene una suscripción a MSDN, consulte [Ventajas de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). De lo contrario, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

## Administración de suscripciones y cuentas de Azure

Para ver los recursos de almacenamiento de Azure en el Explorador de almacenamiento, tendrá que iniciar sesión en una cuenta de Azure con una o más suscripciones activas. Si tiene más de una cuenta de Azure, puede agregarlas en el Explorador de almacenamiento y, a continuación, elegir las suscripciones que desea incluir en la vista de recursos del Explorador. Si no ha usado Azure antes, o no ha agregado las cuentas necesarias a Visual Studio, se le pedirá que inicie sesión en una cuenta de Azure.

### Incorporación de una cuenta de Azure al Explorador de almacenamiento

1.	Elija el icono de **Configuración** (engranaje) de la barra de herramientas del Explorador de almacenamiento.
1.	Seleccione el vínculo **Agregar una cuenta**. Inicie sesión en la cuenta de Azure cuyos recursos de almacenamiento desea examinar. La cuenta que acaba de agregar debería estar seleccionada en la lista desplegable de selección de cuenta. Todas las suscripciones para esa cuenta aparecen bajo la entrada de cuenta.

	![][0]

1.	Active las casillas de las suscripciones de cuenta que desea examinar y, luego, elija el botón **Aplicar**.

	![][1]

	Los recursos de almacenamiento de Azure para las suscripciones seleccionadas aparecen en el Explorador de almacenamiento.

### Asociación de un almacenamiento externo

1. Obtenga el nombre de cuenta y la clave para la cuenta de almacenamiento que desea adjuntar.
	1.	En el Portal de vista previa de Azure, elija la cuenta de almacenamiento para adjuntar.
	1.	En la sección **Administrar** del panel **Configuración** en el Portal de vista previa de Azure, elija el botón **Claves**.
	1.	Copie los valores de los campos **Nombre de la cuenta de almacenamiento** y **Clave de acceso principal**.

		![][2]

1.	En el menú contextual del nodo **Cuentas de almacenamiento** en el Explorador de almacenamiento, elija el comando **Asociar almacenamiento externo**.

	![][3]

1. Escriba el nombre de la cuenta de almacenamiento en el cuadro **Nombre de cuenta** y la clave de acceso principal en el cuadro **Clave de cuenta**. Elija el botón **Aceptar** para continuar.

	![][4]

	El almacenamiento externo aparece en el Explorador de almacenamiento.

	![][5]

1. Para quitar el almacenamiento externo, en el menú contextual del mismo elija el comando Desasociar.

	![][6]

## Visualizar y navegar por los recursos de almacenamiento

Para navegar a un recurso de almacenamiento de Azure y ver su información en el Explorador de almacenamiento, expanda el tipo de almacenamiento y, luego, elija el recurso. En las pestaña **Acciones** y **Propiedades** en la parte inferior del Explorador de almacenamiento encontrará información sobre el recurso seleccionado.

![][7]

-	La pestaña **Acciones** muestra las acciones que puede realizar en el Explorador de almacenamiento para el recurso de almacenamiento seleccionado, como abrir, copiar o eliminar. Las acciones también aparecen en el menú contextual del recurso.

-	La pestaña **Propiedades** muestra las propiedades del recurso, como su tipo, configuración regional, grupo de recursos al que está asociado y URL.

Todas las cuentas de almacenamiento tienen la acción **Abrir en el portal**. Cuando se selecciona esta acción, el Explorador de almacenamiento muestra la cuenta de almacenamiento seleccionada en el Portal de vista previa de Azure.

Según el recurso seleccionado, aparecen otras acciones y valores de propiedad adicionales. Por ejemplo, todos los nodos de contenedores de blobs, colas y tablas tienen una acción **Crear**. Los elementos individuales (como los contenedores de blobs) tienen acciones como **Abrir**, **Eliminar**, y **Obtener firma de acceso compartido**. Cuando se elige un blob de cuenta de almacenamiento, aparecen acciones para abrir el editor de blob.

## Búsqueda de cuentas de almacenamiento y contenedores de blobs

Para buscar cuentas de almacenamiento y contenedores de blobs con un nombre específico en las suscripciones de cuenta de Azure, escriba el nombre en el cuadro de **búsqueda** en el Explorador de almacenamiento.

![][8]

A medida que va escribiendo caracteres en el cuadro de **búsqueda**, irán apareciendo en el árbol de recursos las cuentas de almacenamiento o contenedores de blobs cuyos nombres coinciden con dichos caracteres. Para borrar la búsqueda, elija el botón **x** en el cuadro de **búsqueda**.

## Edición de las cuentas de almacenamiento

Para agregar o cambiar el contenido de una cuenta de almacenamiento, elija el comando **Abrir Editor** para ese tipo de almacenamiento. Puede elegir las acciones en el menú contextual del elemento seleccionado o en la pestaña **Acciones** en la parte inferior del Explorador de almacenamiento.

![][9]

Puede crear o eliminar contenedores de blobs, colas y tablas. También puede editar blobs en el Explorador de almacenamiento eligiendo la acción **Abrir editor de contenedor de blobs**.

### Edición de un contenedor de blobs

1.	Elija la acción **Abrir editor de contenedor de blobs**. El editor de contenedor de blobs aparece en el panel derecho.

	![][10]

1.	Elija el botón **Cargar** y, a continuación, elija el comando **Cargar archivos**.

	![][11]

	Si los archivos que desea cargar se encuentran en una única carpeta, puede elegir también el comando Cargar carpeta.

1. En el cuadro de diálogo **Cargar archivos**, seleccione el botón de puntos suspensivos (**...**) a la derecha del cuadro de archivos para seleccionar aquellos que desea cargar. A continuación, elija qué tipo de blob desea para cargarlo (en bloques, en páginas o anexado). Si lo desea, puede cargar los archivos en una carpeta en el contenedor de blobs. Escriba el nombre de la carpeta en el cuadro **Cargar en carpeta (opcional)**. Si la carpeta no existe, se creará

	![][12]

	En la siguiente captura de pantalla, se han cargado tres archivos de imagen en una carpeta nueva llamada **Mis archivos nuevos** en el contenedor de blobs **Imágenes**.

	![][13]

	Los botones de la barra de herramientas del editor de blobs le permiten seleccionar, descargar, abrir, copiar y eliminar archivos y mucho más. El panel **Actividad** en la parte inferior del cuadro de diálogo muestra si la operación se realizó correctamente, y le permite quitar de la vista solo las actividades que se llevaron a cabo sin problemas o vaciar completamente el panel. Elija el icono **+**, junto a los archivos cargados, para ver una lista detallada de los archivos cargados.

## Creación de una firma de acceso compartido (SAS)

Para algunas operaciones, puede que necesite una SAS para acceder a un recurso de almacenamiento. Puede crear una utilizando el Explorador de almacenamiento.

1.	Seleccione el elemento para el que desea crear una SAS y, a continuación, elija el comando **Obtener firma de acceso compartido** en el panel **Acciones** panel o en el menú contextual del elemento.

	![][14]

1.	En el cuadro de diálogo **Firma de acceso compartido**, seleccione la directiva, las fechas de inicio y de expiración y la zona horaria. Seleccione también las casillas de los niveles de acceso que desea aplicar al recurso, como solo lectura o lectura y escritura, etc. Cuando haya terminado, elija el botón **Crear** para crear la SAS.

	![][15]

1.	El cuadro de diálogo **Firma de acceso compartido** incluye el contenedor junto con la dirección URL y las QueryStrings que se pueden utilizar para obtener acceso al recurso de almacenamiento. Elija el botón **Copiar** para copiar las cadenas.

	![][16]

## Administración de las SAS y los permisos

Para controlar el acceso a los contenedores de blobs, puede elegir los comandos **Administrar la lista de control de acceso** y **Establecer el nivel de acceso público**.

-	Administrar la lista de control de acceso le permite agregar, editar y quitar directivas de acceso (que regulan si los usuarios pueden leer, escribir etc.) en el contenedor de blobs seleccionado.
-	Establecer el nivel de acceso público le permite determinar qué cantidad de acceso al recurso proporciona a los usuarios públicos.  

-

1.	Escoja el contenedor de blobs y, a continuación, elija el comando **Administrar la lista de control de acceso** en el menú contextual o en el panel **Acciones**.

	![][17]

1.	En el cuadro de diálogo **Lista de control de acceso**, seleccione el botón **Agregar** para añadir directivas de acceso. Elija una directiva de acceso y, a continuación, seleccione sus permisos. Cuando haya terminado, elija el botón **Guardar**.

	![][18]

1.	Para establecer un nivel de acceso para un contenedor de blobs, elíjalo en el Explorador de almacenamiento y, a continuación, seleccione el comando **Establecer el nivel de acceso público** en el menú contextual o en el panel **Acciones**.

	![][19]

1.	En el cuadro de diálogo **Establecer el nivel de acceso público del contenedor**, seleccione el botón de opción del nivel de acceso que desea conceder a los usuarios públicos y después elija el botón **Aplicar**.

	![][20]

## Pasos siguientes
Obtenga más información sobre las características de los servicios de Almacenamiento de Azure en los artículos que encontrará en [Introducción a Almacenamiento de Microsoft Azure](/storage/storage-introduction.md).

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount1c.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount2c.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External1c.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External2c.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External3c.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External4c.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External5c.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Navigatec.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Searchc.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit1c.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit2c.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit3c.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit4c.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit5c.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS1c.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS2c.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS3c.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS1c.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS2c.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS3c.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS4c.png

<!---HONumber=AcomDC_0204_2016-->