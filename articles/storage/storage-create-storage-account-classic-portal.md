<properties
	pageTitle="Creación, administración o eliminación de una cuenta de almacenamiento en el Portal de Azure clásico | Microsoft Azure"
	description="Cree una nueva cuenta de almacenamiento, administre las claves de acceso de la misma o elimínela en el Portal de Azure. Obtenga información acerca de las cuentas de almacenamiento estándar y premium."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/14/2016"
	ms.author="robinsh"/>


# Acerca de las cuentas de almacenamiento de Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Información general

Una cuenta de almacenamiento de Azure proporciona acceso a los servicios de Azure de Blob, Cola, Tabla y Archivo del Almacenamiento de Azure. La cuenta de almacenamiento ofrece el espacio de nombres exclusivo para los objetos de datos de Almacenamiento de Azure. De forma predeterminada, los datos de su cuenta están disponibles solo para usted, el propietario de la cuenta.

Existen dos tipos de cuentas de almacenamiento:

- Una cuenta de almacenamiento estándar incluye el almacenamiento de blobs, tablas, colas y archivos.
- Una cuenta de almacenamiento premium actualmente solo admite los discos de máquinas virtuales de Azure. Consulte [Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](storage-premium-storage.md) para una introducción detallada del Almacenamiento premium.

## Facturación de la cuenta de almacenamiento

El uso de Almacenamiento de Azure se le factura según su cuenta de almacenamiento. Los costos de almacenamiento se basan en cuatro factores: capacidad de almacenamiento, esquema de replicación, transacciones de almacenamiento y salida de datos.

- La capacidad de almacenamiento se refiere a cuánto de la asignación correspondiente a cuentas de almacenamiento utiliza para almacenar datos. El coste de simplemente almacenar los datos está determinado por la cantidad de datos que almacena y la manera en que se replican.
- La replicación determina cuántas copias de los datos se conservan al mismo tiempo, y en qué ubicaciones.
- Las transacciones se refieren a todas las operaciones de lectura y escritura en Almacenamiento de Azure.
- La salida de los datos se refiere a los datos transferidos fuera de una región de Azure. Cuando una aplicación que no está en ejecución en la misma región tiene acceso a los datos en su cuenta de almacenamiento, se le cobra por la salida de los datos, independientemente de si la aplicación es un servicio en la nube u otro tipo de aplicación. (En el caso de los servicios de Azure, puede llevar a cabo pasos para agrupar sus datos y servicios en los mismos centros de datos para reducir o eliminar los cargos por concepto de salida de los datos).  

La página [Detalles de precios de Azure](https://azure.microsoft.com/pricing/details/storage) proporciona información detallada sobre los precios de la capacidad, la replicación y las transacciones de almacenamiento. La página [Detalles de precios de transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/) proporciona información detallada sobre los precios para la salida de datos.

Para obtener más información acerca de los objetivos de capacidad y rendimiento de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Almacenamiento de Azure](storage-scalability-targets.md).

> [AZURE.NOTE] Al crear una máquina virtual de Azure, se crea automáticamente una cuenta de almacenamiento en la ubicación de implementación si todavía no tiene una cuenta de almacenamiento en esa ubicación. Por tanto, no es necesario aplicar los pasos descritos a continuación para crear una cuenta de almacenamiento para los discos de máquinas virtuales. El nombre de la cuenta de almacenamiento se basará en el nombre de la máquina virtual. Consulte la [documentación de máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obtener más detalles.

## Crear una cuenta de almacenamiento

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

2. En la parte inferior de la página, haga clic en **Nuevo** en el panel de tareas. Elija **Servicios de datos** | **Almacenamiento** y después haga clic en **Creación rápida**.

	![Nueva cuenta de almacenamiento](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. En **Dirección URL**, escriba un nombre para la cuenta de almacenamiento.

	> [AZURE.NOTE] Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.
	>  
	> El nombre de la cuenta de almacenamiento debe ser único dentro de Azure. El Portal de Azure clásico indicará si ya existe el nombre de la cuenta de almacenamiento que seleccione.

	Consulte [Puntos de conexión de cuenta de almacenamiento](#storage-account-endpoints) más adelante para obtener información detallada sobre la forma en que se usará este nombre para dirigir los objetos en Almacenamiento de Azure.

4. En **Ubicación/Grupo de afinidad**, seleccione una ubicación para la cuenta de almacenamiento que esté próxima a usted o a los clientes. Si otro servicio de Azure va a obtener acceso a los datos de la cuenta de almacenamiento, como un servicio en la nube o una máquina virtual de Azure, debe seleccionar un grupo de afinidad en la lista para agrupar la cuenta de almacenamiento en el mismo centro de datos que otros servicios de Azure que utiliza, a fin de mejorar el rendimiento y reducir los costes.

	Tenga en cuenta que debe seleccionar un grupo de afinidad cuando se crea la cuenta de almacenamiento. No puede mover una cuenta existente a un grupo de afinidad. Para obtener información sobre los grupos de afinidad, consulte [Coubicación de servicios con un grupo de afinidad](#service-co-location-with-an-affinity-group) que se muestra a continuación.

	>[AZURE.IMPORTANT] Para determinar qué ubicaciones están disponibles para su suscripción, puede llamar a la operación [List all resource providers](https://msdn.microsoft.com/library/azure/dn790524.aspx) (Enumerar todos los proveedores de recursos). Para enumerar los proveedores de listas de PowerShell, llame a [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). En. NET, use el método [List](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) de la clase ProviderOperationsExtensions.
	>
	>Además, consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services) para obtener más información acerca de qué servicios están disponibles en la región.


5. Si tiene más de una suscripción a Azure, aparecerá el campo **Suscripción**. En **Suscripción**, escriba la suscripción a Azure con la que desea usar la cuenta de almacenamiento.

6. En **Replicación**, seleccione el nivel deseado de replicación para la cuenta de almacenamiento. La opción de replicación recomendada es la replicación con redundancia geográfica, que ofrece la durabilidad máxima de los datos. Para obtener más información sobre las opciones de replicación del Almacenamiento de Azure, consulte [Replicación de Almacenamiento de Azure](storage-redundancy.md).

6. Haga clic en **Crear cuenta de almacenamiento**.

	Su cuenta de almacenamiento puede tardar unos minutos en crearse. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del Portal de Azure clásico. Una vez que se ha creado la cuenta de almacenamiento, la nueva cuenta de almacenamiento tiene un estado **En línea** y está lista para usarse.

![Página de almacenamiento](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### Extremos de la cuenta de almacenamiento

Cada objeto que se almacena en el Almacenamiento de Azure tiene una dirección URL única. El nombre de la cuenta de almacenamiento forma el subdominio de esa dirección. La combinación de nombre de subdominio y dominio, específica de cada servicio, forma un *punto de conexión* para la cuenta de almacenamiento.

Por ejemplo, si la cuenta de almacenamiento se llama *mystorageaccount*, los extremos predeterminados para la cuenta de almacenamiento son:

- Servicio BLOB: http://*mystorageaccount*.blob.core.windows.net

- Servicio Tabla: http://*mystorageaccount*.table.core.windows.net

- Servicio Cola: http://*mystorageaccount*.queue.core.windows.net

- Servicio de archivos: http://*mystorageaccount*.file.core.windows.net

Puede ver los extremos de la cuenta de almacenamiento en el panel de almacenamiento del [Portal de Azure clásico](https://manage.windowsazure.com) después de haber creado la cuenta.

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

También puede configurar un nombre de dominio personalizado para usarlo con la cuenta de almacenamiento. Consulte [Configurar un nombre de dominio personalizado para el punto de conexión de Almacenamiento de blobs](storage-custom-domain-name.md) para conocer más detalles.

### Coubicación de servicios con un grupo de afinidad

Un *grupo de afinidad* es un grupo geográfico de los servicios de Azure y máquinas virtuales con la cuenta de almacenamiento de Azure. Un grupo de afinidad puede mejorar el rendimiento del servicio al ubicar cargas de trabajo de equipos en el mismo centro de datos o cerca de la audiencia de usuarios de destino. Además, no se aplicarán costes de facturación en la salida cuando el acceso a los datos de una cuenta de almacenamiento tenga lugar desde otro servicio que forma parte del mismo grupo de afinidad.

> [AZURE.NOTE]  Para crear un grupo de afinidad, abra el área <b>Configuración</b> del [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en <b>Grupos de afinidad</b> y, después, haga clic en el botón <b>Agregar un grupo de afinidad</b> o en <b>Agregar</b>. Además puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Azure. Consulte <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">Operaciones en grupos de afinidad</a> para obtener más información.

## Vista, copia y regeneración de las claves de acceso de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

> [AZURE.NOTE] Se recomienda no compartir con nadie las claves de acceso de almacenamiento. Para permitir el acceso a los recursos de almacenamiento sin proporcionar sus claves de acceso, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso de su cuenta durante un intervalo que defina y con los permisos que especifique. Para más información, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).

En el [Portal de Azure clásico](https://manage.windowsazure.com), use **Administrar claves** en el panel o la página **Almacenamiento** para ver, copiar y regenerar las claves de acceso de almacenamiento que se usan para tener acceso a los servicios Blob, Tabla y Cola.

### Copia de una clave de acceso de almacenamiento  

Puede usar **Administrar claves** para copiar una clave de acceso de almacenamiento para usarla en una cadena de conexión. La cadena de conexión requiere el uso del nombre de la cuenta de almacenamiento y una clave en la autenticación. Para más información sobre la configuración de cadenas de conexión para tener acceso a los servicios de Almacenamiento de Azure, consulte [Configuración de las cadenas de conexión de Almacenamiento de Azure](storage-configure-connection-string.md).

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Almacenamiento** y, luego, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Administrar claves**.

 	Se abre **Administrar claves de acceso**.

	![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Para copiar una clave de acceso de almacenamiento, seleccione el texto de la clave. A continuación, haga clic con el botón derecho y haga clic en **Copiar**.

### Nueva generación de las claves de acceso de almacenamiento
Debe cambiar las claves de acceso de su cuenta de almacenamiento periódicamente para ayudar a mantener seguras las conexiones de almacenamiento. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso.

> [AZURE.WARNING] La nueva generación de sus claves de acceso afecta a las máquinas virtuales, los servicios multimedia y cualquier aplicación que dependa de la cuenta de almacenamiento. Todos los clientes que usan la clave de acceso para acceder a la cuenta de almacenamiento deben estar actualizados para usar la nueva clave.

**Máquinas virtuales**: si su cuenta de almacenamiento contiene una máquina virtual que se está ejecutando, tendrá que volver a implementar todas las máquinas virtuales después de generar de nuevo las claves de acceso. Para evitar una nueva implementación, apague las máquinas virtuales antes de generar de nuevo las claves de acceso.

**Servicios multimedia**: si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las claves de acceso con los servicios multimedia después de regenerar las claves.

**Aplicaciones**: si tiene aplicaciones web o servicios en la nube que usan la cuenta de almacenamiento, perderá las conexiones si regenera las claves, a menos que las convierta. A continuación se muestra el proceso:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.

2. Vuelva a generar la clave de acceso primaria para su cuenta de almacenamiento. En el [Portal de Azure clásico](https://manage.windowsazure.com), en el panel o en la página **Configurar**, haga clic en **Administrar claves**. Haga clic en **Regenerar** debajo de la clave de acceso primaria y, a continuación, en **Sí** para confirmar que desea generar una clave nueva.

3. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

4. Vuelva a generar la clave de acceso secundaria.

## de una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no utiliza, use **Eliminar** en el panel o en la página **Configurar**. **Eliminar** elimina toda la cuenta de almacenamiento, incluidos todos los blobs, tablas y colas de la cuenta.

> [AZURE.WARNING] No es posible restaurar una cuenta de almacenamiento eliminada ni recuperar el contenido que contenía antes de la eliminación. Asegúrese de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta. Esto también es verdad para los recursos de la cuenta: cuando se elimina un blob, tabla, cola o archivo, este se eliminará definitivamente.
>
> Si su cuenta de almacenamiento contiene archivos VHD para una máquina virtual de Azure, deberá entonces eliminar todas las imágenes y los discos que usan esos archivos VHD para poder eliminar la cuenta de almacenamiento. Primero, detenga la máquina virtual si se está ejecutando y, a continuación, elimínela. Para eliminar los discos, diríjase a la pestaña **Discos** y elimine todos los discos que se encuentran allí. Para eliminar imágenes, vaya a la pestaña **Imágenes** y elimine todas las imágenes almacenadas en la cuenta.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Almacenamiento**.

2. Haga clic en cualquier lugar de la entrada de la cuenta de almacenamiento excepto en el nombre y, a continuación, haga clic en **Eliminar**.

	 -O bien-

	Haga clic en el nombre de la cuenta de almacenamiento para abrir el panel y, a continuación, haga clic en **Eliminar**.

3. Haga clic en **Sí** para confirmar que desea eliminar la cuenta de almacenamiento.

## Pasos siguientes

- Para más información acerca del Almacenamiento de Azure, consulte la [documentación sobre Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/).
- Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0218_2016-->