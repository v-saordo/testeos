<properties
	pageTitle="Crear una instantánea de solo lectura de un blob | Microsoft Azure"
	description="Aprenda a crear una instantánea de un blob para hacer una copia de seguridad de los datos de blob en un momento determinado. Comprenda cómo se facturan las instantáneas y cómo usarlas para minimizar las cargas de capacidad."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="tamram"/>

# Creación de una instantánea de un blob

## Información general

Una instantánea es una versión de solo lectura de un blob que se ha realizado en un momento dado. Las instantáneas son útiles para realizar copias de seguridad de blobs. Cuando se cree una instantánea, puede leerla, copiarla o eliminarla, pero no puede modificarla.

Las instantáneas de un blob tienen el mismo nombre que el blob de base del cual se ha realizado la instantánea, con un valor de **DateTime** anexado para indicar el momento en el que se realizó la instantánea. Por ejemplo, si el URI de blob en páginas es `http://storagesample.core.blob.windows.net/mydrives/myvhd`, el URI de instantánea será similar a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. Todas las instantáneas de un blob comparten el URI y se distinguen solo por el valor de **DateTime** anexado.

Un blob puede tener cualquier número de instantáneas. Las instantáneas se conservan hasta que se eliminen explícitamente. Tenga en cuenta que una instantánea no puede durar más que su blob de origen. Puede enumerar las instantáneas asociadas al blob para llevar un seguimiento de las instantáneas actuales.

Cuando se crea una instantánea de un blob, las propiedades del sistema se copian en la instantánea con los mismos valores.

Las concesiones asociadas con el blob base no afectan la instantánea. No puede adquirir una concesión en una instantánea.

## Copiar instantáneas

Las operaciones de copia con blobs e instantáneas siguen estas reglas:

- Puede copiar una instantánea sobre su blob base. Si se mueve una instantánea a la posición del blob, puede restaurar una versión anterior de un blob. La instantánea se conserva, pero el blob base se sobrescribe con una copia que se puede escribir de la instantánea.

- Puede copiar una instantánea en un blob de destino con un nombre diferente. El blob resultante de destino es un blob que se puede escribir y no una instantánea.

- Cuando se copia un blob de origen, las instantáneas del blob de origen no se copian en el destino. Cuando un blob de destino se sobrescribe con una copia, las instantáneas asociadas al blob de destino en el momento en que se sobrescribió bajo su nombre no se modifican.

- Cuando se crea una instantánea de un blob en bloques, la lista de bloques confirmados del blob también se copia en la instantánea. No se copiarán los bloques sin confirmar.

## Especificar una condición de acceso

Puede especificar una condición de acceso de forma que la instantánea se cree solo si se cumple una condición. Para especificar una condición de acceso, use la propiedad **AccessCondition**. Si la condición especificada no se cumple, la instantánea no se creará y el servicio Blob devuelve el código de estado HTTPStatusCode.PreconditionFailed.

## Eliminar instantáneas

No se puede eliminar un blob con instantáneas a menos que las instantáneas también se eliminen. Puede eliminar las instantáneas individualmente o bien, indicar al servicio de almacenamiento que elimine todas las instantáneas cuando elimine el blob de origen. Si intenta eliminar un blob que todavía tiene instantáneas, recibirá un error.

## Instantáneas con Almacenamiento Premium de Azure
Las instantáneas con almacenamiento Premium, se atienen a las siguientes reglas:

- El número de instantáneas por blob en páginas está limitado a 100 en cuentas de almacenamiento premium. Si se supera ese límite, la operación de instantánea de blob devuelve el código de error 409 (**SnapshotCountExceeded**).

- Cada diez minutos, se podrá tomar una sola instantánea de un blob en páginas en una cuenta de almacenamiento premium. Si se supera esa frecuencia, la operación de instantánea de blob devuelve el código de error 409 (**SnaphotOperationRateExceeded**).

- No se puede leer la instantánea de un blob en páginas en una cuenta de almacenamiento premium a través de Get Blob. Si llama a Get Blob en una instantánea de una cuenta de almacenamiento premium, este devuelve un código de error 400 (**InvalidOperation**). Sin embargo, puede llamar a Get Blob Properties y Get Blob Metadata en una instantánea.

- Para leer una instantánea, puede utilizar la operación Copy Blob para copiar una instantánea en otro blob en páginas de la cuenta. El blob de destino para la operación de copia no debe contener ninguna instantánea ya existente. Si el blob de destino tiene instantáneas, Copy Blob devuelve el código de error 409 (**SnapshotsPresent**).

## Devolver el URI absoluto para una instantánea

Este ejemplo de código de C# crea una nueva instantánea y escribe el URI absoluto de la ubicación principal.

    //Create the blob service client object.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## Descripción de cómo las instantáneas pueden incrementar los costes

Crear una instantánea, que es una copia de solo lectura de un blob, puede conllevar cargos adicionales de almacenamiento de datos en la cuenta. Cuando se diseñe la aplicación, es importante tener en cuenta cómo se pueden contraer gastos al objeto de minimizar los costes innecesarios.

### Consideraciones importantes sobre facturación

La lista siguiente incluye los puntos clave que hay que tener en cuenta a la hora de crear una instantánea.

- Los cargos se generan con bloques o páginas únicos, bien estén estos en el blob o en la instantánea. La cuenta no generará gastos adicionales para instantáneas asociadas a un blob hasta que actualice el blob en el que se basan. Después de actualizar el blob de base, divergirá respecto a sus instantáneas y se aplicarán cargos para los bloques o páginas únicos en cada uno de los blobs o instantáneas.

- Al reemplazar un bloque en un blob en bloques, ese bloque será considerado como un bloque único a la hora de aplicar cargos. Esto es así incluso aunque el bloque tenga el mismo identificador de bloque y los mismos datos que tiene en la instantánea. Cuando se vuelva a confirmar el bloque, divergirá de su equivalente en cualquier instantánea y se aplicarán cargos por sus datos. Lo mismo sucede con una página en un blob en páginas que se hubiera actualizado con datos idénticos.

- Si se reemplaza un blob en bloques llamando a los métodos **UploadFile**, **UploadText**, **UploadStream** o **UploadByteArray**, se reemplazan todos los bloques del blob. Si una instantánea está asociada a ese blob, todos los bloques en el blob e instantánea de base divergen ahora y se aplicarán cargos por todos los bloques en ambos blobs. Esto es así incluso si los datos del blob e instantánea de base siguen siendo idénticos.

- El servicio Blob de Azure no dispone de medios para determinar si dos bloques contienen datos idénticos. Cada bloque que se carga y confirma se trata como único, incluso si tiene los mismos datos y el mismo identificador de bloque. Dado que las cargas se incrementan con los bloques únicos, es importante considerar que cuando se actualiza un blob con una instantánea se generan bloques únicos adicionales y se aplican cargos.

> [AZURE.NOTE] Los procesos recomendados sugieren administrar las instantáneas con cuidado para evitar recargos. Se recomienda que administre las instantáneas de la siguiente manera:

> - Elimine y vuelva a crear las instantáneas asociadas a un blob siempre que lo actualice, incluso si lo hace con datos idénticos, a menos que el diseño de la aplicación requiera que se conserven las instantáneas. Si elimina y vuelve a crear las instantáneas del blob, podrá estar seguro de que el blob y las instantáneas no van a divergir.

> - Si está realizando el mantenimiento de las instantáneas de blob, evite llamar a **UploadFile**, **UploadText**, **UploadStream** o **UploadByteArray** para actualizar el blob, ya que esos métodos reemplazan todos los bloques del blob. En su lugar, actualice el menor número posible de bloques mediante los métodos **PutBlock** y **PutBlockList**.


### Escenarios de facturación de instantáneas


En las siguientes situaciones, se muestra cómo se ven incrementados los cargos para un blob en bloques y sus instantáneas.

En el escenario 1, el blob de base no se ha actualizado después de realizarse la instantánea, así que se aplicarán cargos únicamente para los bloques 1, 2 y 3.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

En el escenario 2, el blob de base está actualizado, pero no así la instantánea. Se ha actualizado el bloque 3 y, aunque contiene los mismos datos y el mismo identificador, no es igual que el bloque 3 de la instantánea. Como resultado, se aplicarán cargos a la cuenta por cuatro bloques.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

En el escenario 3, el blob de base está actualizado, pero no así la instantánea. El bloque 3 se ha reemplazado por el bloque 4 en el blob de base, pero la instantánea sigue reflejando el bloque 3. Como resultado, se aplicarán cargos a la cuenta por cuatro bloques.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

En la situación 4, el blob de base se ha actualizado totalmente y no contiene ninguno de los bloques originales. Como resultado, se aplicarán cargos a la cuenta por la totalidad de los ocho bloques únicos. Esta situación se puede dar si usa un método de actualización como **UploadFile**, **UploadText**, **UploadFromStream** o **UploadByteArray**, ya que estos métodos reemplazan todos los contenidos de un blob.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

<!---HONumber=AcomDC_0224_2016-->