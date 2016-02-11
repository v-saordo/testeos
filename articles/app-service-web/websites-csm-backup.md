<properties
	pageTitle="Uso de REST para copia de seguridad y restauración de aplicaciones del Servicio de aplicaciones"
	description="Obtenga información acerca del uso de las llamadas de API de RESTful para la copia de seguridad y restauración de una aplicación en el Servicio de aplicaciones de Azure"
	services="app-service"
	documentationCenter=""
	authors="nking92"
	manager="edlauare"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/18/2015"
	ms.author="nicking"/>

# Uso de REST para copia de seguridad y restauración de aplicaciones del Servicio de aplicaciones
Se puede realizar una copia de seguridad de las [aplicaciones del Servicio de aplicaciones](https://azure.microsoft.com/services/app-service/web/) como blobs en Almacenamiento de Azure. La copia de seguridad también puede contener las bases de datos de la aplicación. Si la aplicación se ha eliminado accidentalmente alguna vez o si necesita revertirse a una versión anterior, es posible restaurarla desde cualquier copia de seguridad anterior. Las copias de seguridad se pueden realizar en cualquier momento a petición o se pueden programar a intervalos adecuados.

En este artículo se explicará cómo realizar copias de seguridad y restauraciones de una aplicación con solicitudes de API de RESTful. Si desea crear y administrar copias de seguridad de aplicaciones gráficamente mediante el Portal de Azure, consulte [Hacer copia de seguridad de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## Introducción
Para enviar solicitudes de REST, necesitará saber el **nombre**, **grupo de recursos** e **identificador de suscripción** de la aplicación. Para encontrar esta información, puede hacer clic en la aplicación, en la hoja **Servicio de aplicaciones** del [Portal de Azure](https://portal.azure.com). Para los ejemplos de este artículo, se va a configurar el sitio web `backuprestoreapiexamples.azurewebsites.net`. Se almacena en el grupo de recursos Default-Web-WestUS y se ejecuta en una suscripción con el identificador 00001111-2222-3333-4444-555566667777.

![Información del sitio web de ejemplo][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## Copia de seguridad y restauración de API de REST
Ahora, vamos a describir varios ejemplos de cómo usar la API de REST para la copia de seguridad y la restauración de una aplicación. Cada ejemplo incluye una dirección URL y un cuerpo de solicitud HTTP. La dirección URL de ejemplo contiene marcadores de posición entre llaves como, por ejemplo, {subscriptionId}. Debe reemplazarlos por la información correspondiente a su aplicación. Como referencia, proporcionamos una explicación de cada marcador de posición que aparece en las direcciones URL de ejemplo.

* subscriptionId: identificador de la suscripción de Azure que contiene la aplicación
* resourceGroupName: nombre del grupo de recursos que contiene la aplicación
* sitename: nombre de la aplicación
* backupId: identificador de la copia de seguridad de la aplicación

Para obtener la documentación completa de la API, entre ellos, varios parámetros opcionales que pueden incluirse en la solicitud HTTP, consulte el [Explorador de recursos de Azure r](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## Copia de seguridad de una aplicación a petición
Para realizar una copia de seguridad de una aplicación de inmediato, envíe una solicitud **POST** a `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{sitename}/backup/`.

Este es el aspecto de la dirección URL al utilizar nuestro sitio web de ejemplo `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/`

Debe proporcionar un objeto JSON en el cuerpo de la solicitud para especificar qué cuenta de almacenamiento se usa para almacenar la copia de seguridad. El objeto JSON debe tener una propiedad denominada **storageAccountUrl**, que contiene una [URL de SAS](../storage/storage-dotnet-shared-access-signature-part-1.md), que concede acceso de escritura al contenedor de Almacenamiento de Azure que contendrá el blob de copia de seguridad. Si desea hacer una copia de seguridad de las bases de datos, debe proporcionar igualmente una lista con los nombres, tipos y cadenas de conexión de las bases de datos de las que va a realizar una copia de seguridad.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Se iniciará de inmediato una copia de seguridad de la aplicación al recibir la solicitud. El proceso de copia de seguridad puede tardar mucho tiempo en completarse. La respuesta HTTP contendrá un identificador que puede usar en otra solicitud para ver el estado de la copia de seguridad. Este es un ejemplo del cuerpo de la respuesta HTTP a nuestra solicitud de copia de seguridad.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE]Se pueden encontrar mensajes de error en la propiedad de registro de la respuesta HTTP.

<a name="schedule-automatic-backups"></a>
## Programación de copias de seguridad automáticas
Además de la copia de seguridad de una aplicación a petición, también puede programar la ejecución de una copia de seguridad automática.

### Configuración de una nueva programación de copias de seguridad automáticas
Para configurar una programación de copias de seguridad, envíe una solicitud **PUT** a `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup`.

Este es el aspecto de la dirección URL de nuestro sitio web de ejemplo `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup`

El cuerpo de la solicitud debe tener un objeto JSON que especifique la configuración de la copia de seguridad. Este es un ejemplo con todos los parámetros necesarios.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

En este ejemplo se configura la ejecución de una copia de seguridad automática de la aplicación cada 7 días. Los parámetros **frequencyInterval** y **frequencyUnit** determinan con qué frecuencia se realizan las copias de seguridad. Los valores válidos para **frequencyUnit** son **hora** y **día**. Por ejemplo, para hacer una copia de seguridad de una aplicación cada 12 horas, establezca frequencyInterval en 12 y frequencyUnit en hora.

Las copias de seguridad anteriores se quitarán automáticamente de la cuenta de almacenamiento. Puede controlar la antigüedad de las copias de seguridad mediante la configuración del parámetro **retentionPeriodInDays**. Si desea tener siempre al menos una copia de seguridad guardada, independientemente de su antigüedad, establezca **keepAtLeastOneBackup** en true.

### Obtención de la programación de copias de seguridad automáticas
Para obtener una configuración de copia de seguridad de una aplicación, envíe una solicitud **POST** a la dirección URL ` https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup/list`.

La dirección URL para nuestro sitio de ejemplo es `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list`.

<a name="get-backup-status"></a>
## Obtención del estado de una copia de seguridad
En función de lo grande que sea la aplicación, una copia de seguridad puede tardar varios minutos en completarse. Las copias de seguridad pueden también provocar errores, alargar el tiempo de espera o completarse solo parcialmente. Para ver el estado de todas las copias de seguridad de una aplicación, envíe una solicitud **GET** a la dirección URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups`.

Para ver el estado de una copia de seguridad específica, envíe una solicitud GET a la dirección URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`.

Este es el aspecto de la dirección URL de nuestro sitio web de ejemplo `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

El cuerpo de respuesta contendrá un objeto JSON similar a este ejemplo.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

El estado de una copia de seguridad es un tipo enumerado. Mostramos, a continuación, todos los estados posibles.

* 0: En curso: la copia de seguridad se ha iniciado pero no se ha completado aún.
* 1: Error: la copia de seguridad no se ha realizado correctamente.
* 2: Correcta: la copia de seguridad se ha completado correctamente.
* 3: Cancelada: la copia de seguridad no se ha completado a tiempo y se ha cancelado.
* 4: Creada: la solicitud de copia de seguridad está en la cola pero no se ha iniciado aún.
* 5: Omitida: la copia de seguridad no ha continuado debido a que una programación ha desencadenado demasiadas copias de seguridad.
* 6: Parcialmente correcta: la copia de seguridad se ha realizado correctamente pero no se ha realizado una copia de seguridad de algunos archivos porque no se han podido leer. Esto suele suceder cuando se coloca un bloqueo exclusivo en los archivos.
* 7: Eliminación en curso: se ha solicitado la eliminación de la copia de seguridad pero no se ha eliminado aún.
* 8: Error en eliminación: no se ha podido eliminar la copia de seguridad. Esto puede ocurrir porque la dirección URL de SAS usada para crear la copia de seguridad ha caducado.
* 9: Eliminada: se ha eliminado correctamente la copia de seguridad.

<a name="restore-app"></a>
## Restauración de una aplicación desde una copia de seguridad
Si se ha eliminado la aplicación, o si desea revertir la aplicación a una versión anterior, puede restaurar la aplicación desde una copia de seguridad. Para invocar una restauración, envíe una solicitud **POST** a la dirección URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/restore`.

Este es el aspecto de la dirección URL de nuestro sitio web de ejemplo `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore`

En el cuerpo de la solicitud, envíe un objeto JSON que contiene las propiedades de la operación de restauración. Este es un ejemplo que contiene todas las propiedades necesarias:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### Restauración a una nueva aplicación
A veces, es posible que desee crear una nueva aplicación al restaurar una copia de seguridad, en lugar de sobrescribir una aplicación existente. Para ello, cambie la dirección URL de la solicitud a fin de que apunte a la nueva aplicación que desea crear y cambie la propiedad **overwrite** en JSON a **false**.

<a name="delete-app-backup"></a>
## Eliminación de una copia de seguridad de aplicación
Si desea eliminar una copia de seguridad, envíe una solicitud **DELETE** a la dirección URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`.

Este es el aspecto de la dirección URL de nuestro sitio web de ejemplo `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

<a name="manage-sas-url"></a>
## Administración de la dirección URL de SAS de una copia de seguridad
Servicio de aplicaciones de Azure intentará eliminar la copia de seguridad de Almacenamiento de Azure con la dirección URL de SAS proporcionada al crear la copia de seguridad. Si esta dirección URL de SAS ya no es válida, no se puede eliminar la copia de seguridad mediante la API de REST. Sin embargo, puede actualizar la dirección URL de SAS asociada a una copia de seguridad mediante el envío de una solicitud **POST** a la dirección URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/list`.

Este es el aspecto de la dirección URL de nuestro sitio web de ejemplo `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list`

En el cuerpo de la solicitud, envíe un objeto JSON que contiene la nueva dirección URL de SAS. Aquí tiene un ejemplo.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE]Por motivos de seguridad, no se devuelve la dirección URL de SAS asociada a una copia de seguridad al enviar una solicitud GET para una copia de seguridad específica. Si desea ver la dirección URL de SAS asociada a una copia de seguridad, envíe una solicitud POST a la misma dirección URL anterior e incluya solo un objeto JSON vacío en el cuerpo de la solicitud. La respuesta del servidor contendrá toda la información de la copia de seguridad, incluida su dirección URL de SAS.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

<!---HONumber=AcomDC_1223_2015-->