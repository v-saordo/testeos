<properties
	pageTitle="Creación, administración o eliminación de una cuenta de almacenamiento en el Portal de Azure | Microsoft Azure"
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

## Extremos de la cuenta de almacenamiento

Cada objeto que se almacena en el Almacenamiento de Azure tiene una dirección URL única. El nombre de la cuenta de almacenamiento forma el subdominio de esa dirección. La combinación de nombre de subdominio y dominio, específica de cada servicio, forma un *punto de conexión* para la cuenta de almacenamiento.

Por ejemplo, si la cuenta de almacenamiento se llama *mystorageaccount*, los extremos predeterminados para la cuenta de almacenamiento son:

- Servicio BLOB: http://*mystorageaccount*.blob.core.windows.net

- Servicio Tabla: http://*mystorageaccount*.table.core.windows.net

- Servicio Cola: http://*mystorageaccount*.queue.core.windows.net

- Servicio de archivos: http://*mystorageaccount*.file.core.windows.net

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

También puede configurar un nombre de dominio personalizado para usarlo con la cuenta de almacenamiento. Para las cuentas de almacenamiento clásicas, consulte [Configurar un nombre de dominio personalizado para el punto de conexión de Almacenamiento de blobs](storage-custom-domain-name.md) para conocer más detalles. Para las cuentas de almacenamiento del ARM, esta funcionalidad no se ha agregado al [Portal de Azure](https://portal.azure.com) aún, pero se puede configurar con PowerShell. Para más información, consulte el cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx).

## Crear una cuenta de almacenamiento

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, seleccione **Nuevo** -> **Datos + almacenamiento** -> **Cuenta de almacenamiento**.

3. Seleccione un modelo de implementación: **Administrador de recursos** o **Clásico**. **Administrador de recursos** es el modelo de implementación recomendado. Para obtener más información, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](../resource-manager-deployment-model.md).

4. Escriba un nombre para la cuenta de almacenamiento.

	> [AZURE.NOTE] Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.
	>  
	> El nombre de la cuenta de almacenamiento debe ser único dentro de Azure. El Portal de Azure indicará si ya existe el nombre de la cuenta de almacenamiento que seleccione.

	Consulte [Puntos de conexión de cuenta de almacenamiento](#storage-account-endpoints) más adelante para obtener información detallada sobre la forma en que se usará este nombre para dirigir los objetos en Almacenamiento de Azure.

5. Especifique el tipo de cuenta de almacenamiento que se creará. El tipo de cuenta de almacenamiento determina cómo se replica la cuenta de almacenamiento y si es una cuenta de almacenamiento estándar o una cuenta de almacenamiento premium.

	El tipo de cuenta de almacenamiento predeterminado es **RAGRS estándar**, que es una cuenta de almacenamiento estándar con replicación con redundancia geográfica de acceso de lectura. Este tipo de cuenta de almacenamiento se replica en una región secundaria que se encuentra a cientos de millas de la región primaria y proporciona acceso de lectura a la ubicación secundaria.

	Para obtener más información sobre las opciones de replicación del Almacenamiento de Azure, consulte [Replicación de Almacenamiento de Azure](storage-redundancy.md). Para más información sobre las cuentas de almacenamiento estándar y premium, consulte [Introducción a Almacenamiento de Microsoft Azure](storage-introduction.md) y [Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md)

6. Indique si desea habilitar los diagnósticos para la cuenta de almacenamiento. Los diagnósticos incluyen métricas y registros de análisis de almacenamiento.

7. Si tiene más de una suscripción a Azure, aparecerá el campo **Suscripción**. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

8. Especifique un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para más información acerca de los grupos de recursos, consulte [Uso del Portal de Azure para administrar los recursos de Azure](../azure-portal/resource-group-portal.md).

9. Seleccione la ubicación geográfica para la cuenta de almacenamiento.

10. Haga clic en **Crear** para crear la cuenta de almacenamiento.

## Administración de las claves de acceso de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

> [AZURE.NOTE] Se recomienda no compartir con nadie las claves de acceso de almacenamiento. Para permitir el acceso a los recursos de almacenamiento sin proporcionar sus claves de acceso, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso de su cuenta durante un intervalo que defina y con los permisos que especifique. Para más información, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).

### Visualización y copia de las claves de acceso de almacenamiento

En el [Portal de Azure](https://portal.azure.com), diríjase a su cuenta de almacenamiento y haga clic en el icono de **claves** para ver, copiar y regenerar las claves de acceso de la cuenta. La hoja de **claves de acceso** también incluye cadenas de conexión configuradas previamente que usan claves principales y secundarias que puede copiar para usarlas en las aplicaciones.

### Nueva generación de las claves de acceso de almacenamiento

Recomendamos que cambie las claves de acceso de su cuenta de almacenamiento periódicamente para ayudar a mantener seguras las conexiones de almacenamiento. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso.

> [AZURE.WARNING] La nueva generación de sus claves de acceso afecta a las máquinas virtuales, los servicios multimedia y cualquier aplicación que dependa de la cuenta de almacenamiento. Todos los clientes que usan la clave de acceso para acceder a la cuenta de almacenamiento deben estar actualizados para usar la nueva clave.

**Máquinas virtuales**: si su cuenta de almacenamiento contiene una máquina virtual que se está ejecutando, tendrá que volver a implementar todas las máquinas virtuales después de generar de nuevo las claves de acceso. Para evitar una nueva implementación, apague las máquinas virtuales antes de generar de nuevo las claves de acceso.

**Servicios multimedia**: si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las claves de acceso con los servicios multimedia después de regenerar las claves.

**Aplicaciones**: si tiene aplicaciones web o servicios en la nube que usan la cuenta de almacenamiento, perderá las conexiones si regenera las claves, a menos que las convierta.

Este es el proceso de rotación de las claves de acceso de almacenamiento:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.

2. Vuelva a generar la clave de acceso primaria para su cuenta de almacenamiento. En la hoja de **claves de acceso**, haga clic en **Regenerar clave1** y luego haga clic en **Sí** para confirmar que desea generar una nueva clave.

3. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

4. Vuelva a generar la clave de acceso secundaria de la misma forma.

## Eliminar una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no se está usando, vaya a la cuenta de almacenamiento en el [Portal de Azure](https://portal.azure.com) y haga clic en **Eliminar**. Si se elimina la cuenta de almacenamiento, se elimina toda la cuenta, incluidos todos los datos de la cuenta.

> [AZURE.WARNING] No es posible restaurar una cuenta de almacenamiento eliminada ni recuperar el contenido que contenía antes de la eliminación. Asegúrese de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta. Esto también es verdad para los recursos de la cuenta: cuando se elimina un blob, tabla, cola o archivo, este se eliminará definitivamente.

Para eliminar una cuenta de almacenamiento que está asociada a una máquina virtual de Azure, primero debe asegurarse de que se han eliminado los discos de la máquina virtual. Si no elimina primero los discos de las máquinas virtuales, cuando intente eliminar la cuenta de almacenamiento, verá un mensaje de error similar al siguiente:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Si la cuenta de almacenamiento utiliza el modelo de implementación clásico, puede quitar el disco de máquina virtual siguiendo estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com):

1. Navegue hasta el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Vaya a la pestaña de máquinas virtuales.
3. Haga clic en la pestaña Discos.
4. Seleccione el disco de datos y luego haga clic en Eliminar disco.
5. Para eliminar imágenes de discos, vaya a la pestaña Imágenes y elimine todas las imágenes almacenadas en la cuenta.

Para más información, consulte la [documentación de máquinas virtuales de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## Pasos siguientes

- [Replicación de almacenamiento de Azure](storage-redundancy.md)
- [Configuración de las cadenas de conexión de Almacenamiento de Azure](storage-configure-connection-string.md)
- [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md)
- Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).

<!---HONumber=AcomDC_0218_2016-->