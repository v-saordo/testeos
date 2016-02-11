<properties
   pageTitle="Copia de seguridad de Azure: copia de seguridad sin conexión o propagación inicial mediante el servicio Importación/Exportación de Azure"
   description="Descubra cómo la copia de seguridad de Azure permite enviar datos fuera de la red mediante el servicio de importación y exportación de Azure. Este artículo explica la propagación sin conexión de los datos de copia de seguridad iniciales mediante el servicio de importación y exportación de Azure."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="shreeshd"
   editor=""/>
<tags  ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="11/25/2015" ms.author="aashishr"; "jimpark"/>

# Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure

Copia de seguridad de Azure está profundamente integrada en el servicio Importación/Exportación de Azure, que permite transferir rápidamente los datos de copia de seguridad inicial. Si tiene TB de datos de copia de seguridad inicial que se deben transferir a través de una red de latencia alta y ancho de banda bajo, puede usar el servicio Importación/Exportación de Azure para enviar la copia de seguridad inicial de una o varias unidades de disco duros a un centro de datos de Azure. Este artículo proporciona información general de los pasos necesarios para completar este flujo de trabajo.

## Información general

Con Copia de seguridad e Importación/Exportación de Azure, es simple y directo cargar los datos en Azure sin conexión a través de discos. En lugar de transferir la copia inicial completa a través de la red, los datos de copia de seguridad se escriben en una *ubicación de ensayo*. La ubicación de ensayo puede ser un almacenamiento de conexión directa o un recurso compartido de red. Una vez completada la copia inicial, usando la *Herramienta de importación/exportación*, estos datos se escriben en una unidad SATA que finalmente se envía al centro de datos de Azure. Según el tamaño de la copia de seguridad inicial, una o varias unidades SATA pueden necesitarse para completar esta operación. La herramienta de importación y exportación tiene en cuenta estos escenarios. Una vez escritas las copias de seguridad en el disco, pueden enviarse a la ubicación de centro de datos más cercana para cargarla en Azure. A continuación, Copia de seguridad de Azure, copia los datos de copia de seguridad en el almacén de copia de seguridad y las copias de seguridad incrementales se programan.

## Requisitos previos

1. Es importante familiarizarse con el flujo de trabajo de exportación de importación de Azure que aparece [aquí](../storage-import-export-service.md).
2. Antes de iniciar el flujo de trabajo, asegúrese de que se ha creado un almacén de copia de seguridad de Azure, de que las credenciales de almacén se han descargado, el agente de Copia de seguridad de Azure se ha instalado en el servidor/cliente de Windows o en System Center Data Protection Manager (SCDPM) y la máquina está registrada con el almacén de copia de seguridad de Azure.
3. Descargue la configuración del archivo de publicación de Azure [aquí](https://manage.windowsazure.com/publishsettings) en la máquina desde la que piensa hacer copia de seguridad de los datos.
4. Prepare una *ubicación de ensayo* que podría ser un recurso compartido de red o la unidad de disco adicional en la máquina. Asegúrese de que la ubicación de ensayo tiene suficiente espacio en disco para almacenar la copia inicial. Por ejemplo, si intenta realizar una copia de seguridad en un servidor de archivos de 500 GB, asegúrese de que el área de ensayo es de al menos 500 GB (aunque se usará una cantidad menor). El área de ensayo es de almacenamiento transitorio, y se utiliza temporalmente durante este flujo de trabajo.
5. Escritor de unidad SATA externo y una unidad SATA externa de 3,5 pulgadas. El servicio de importación y exportación solo admite unidades de disco duro SATA II/III de 3,5 pulgadas. No se admiten unidades de disco duro con un tamaño superior a 6 TB. Es posible conectar un disco SATA II/III externo a la mayoría de los equipos con un adaptador USB SATA II/III. Consulte la documentación de importación y exportación para el conjunto más reciente de las unidades compatibles con el servicio.
6. Habilite BitLocker en la máquina a la que está conectado el sistema de escritura de la unidad SATA.
7. Descargue la herramienta de importación y exportación [aquí](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) en la máquina a la que está conectado el sistema de escritura de la unidad SATA.

## Flujo de trabajo
La información proporcionada en esta sección es para completar el flujo de trabajo de **Copia de seguridad sin conexión**, por lo que los datos se pueden entregar a un centro de datos de Azure y cargarse en el almacenamiento de Azure. Si tiene alguna pregunta sobre el servicio de importación o cualquier aspecto del proceso, vea la documentación sobre la [Información general del servicio de importación](../storage-import-export-service.md) a la que se ha hecho referencia anteriormente.

### Iniciar la copia de seguridad sin conexión

1. Como parte de la programación de una copia de seguridad, aparecerá la pantalla siguiente (en Windows Server, el cliente Windows o SCDPM).

    ![ImportScreen](./media/backup-azure-backup-import-export/importscreen.png)

    La pantalla correspondiente en SCDPM tiene el siguiente aspecto. <br/> ![Pantalla de importación de DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    donde:

    - **Ubicación de ensayo**: hace referencia a la ubicación de almacenamiento temporal en la que se escribe la copia de seguridad inicial. Podría tratarse de un recurso compartido de red o en la máquina local.
    - **Nombre del trabajo de importación de Azure**: como parte de la finalización de este flujo de trabajo, necesitará crear un *trabajo de importación* en el Portal de Azure (lo cual se describe al final del documento). Especifique también qué plan desea usar más adelante en el Portal de Azure.
    - **Configuración de publicación de Azure**: es un archivo XML que contiene información sobre el perfil de suscripción. También contiene credenciales protegidas asociadas a su suscripción. Es posible descargar el archivo [aquí](https://manage.windowsazure.com/publishsettings). Proporcione la ruta de acceso local al archivo de configuración de publicación.
    - **Identificador de suscripción de Azure**: proporcione el identificador de suscripción de Azure con el que desea iniciar el trabajo de importación de Azure. Si tiene varias suscripciones de Azure, use el identificador asociado al trabajo de importación.
    - **Cuenta de almacenamiento de Azure**: escriba el nombre de la cuenta de almacenamiento de Azure que se asociará a este trabajo de importación.
    - **Contenedor de almacenamiento de azure**: escriba el nombre del blob de almacenamiento de destino donde se importarán los datos de este trabajo.

2. Complete el flujo de trabajo y seleccione **Hacer copia de seguridad ahora** en el MMC de Copia de seguridad de Azure para iniciar la copia de seguridad sin conexión. La copia de seguridad inicial se escribe en el área de ensayo como parte de este paso.

    ![Realizar copia de seguridad ahora](./media/backup-azure-backup-import-export/backupnow.png)

    El flujo de trabajo correspondiente en SCDPM se habilita haciendo clic en el **Grupo de protección** y eligiendo la opción **Crear punto de recuperación**. Tras esto, se debe elegir la opción **Protección en línea**.

    ![Realizar copia de seguridad de DPM ahora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Una vez completada la operación, se crea un archivo *. AIBBlob* y un archivo *. BaseBlob* en la ubicación de ensayo.

    ![Salida](./media/backup-azure-backup-import-export/opbackupnow.png)

### Preparar la unidad de disco SATA

1. Descargue la [herramienta de importación y exportación](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) de Microsoft Azure en la *máquina de copia*. Asegúrese de que la ubicación de ensayo (en el paso anterior) es accesible desde la máquina e la que tiene previsto ejecutar el siguiente conjunto de comandos. Si es necesario, la máquina de copia puede ser la misma que la máquina de origen.

2. Descomprima el archivo *WAImportExport.zip*. Ejecute la herramienta *WAImportExport* que formateará la unidad SATA, escriba los datos de copia de seguridad en esa unidad y cífrelos. Antes de ejecutar el siguiente comando, asegúrese de que BitLocker está habilitado en la máquina. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*


| Parámetro | Descripción
|-------------|-------------|
| /j:<*JournalFile*>| La ruta de acceso al archivo de diario. Cada unidad debe tener exactamente un archivo de diario. Tenga en cuenta que el archivo de diario no debe residir en la unidad de destino. La extensión de archivo de diario es .jrn y se crea como parte de la ejecución de este comando.|
|/id:<*SessionId*> | El identificador de sesión identifica una *sesión de copia*. Se utiliza para asegurar la recuperación precisa de una sesión de copia interrumpida. Los archivos que se copian en una sesión de copia se almacenan en un directorio con el mismo nombre que el identificador de sesión en la unidad de destino.|
| /sk:<*StorageAccountKey*> | La clave de cuenta para la cuenta de almacenamiento en la que se importarán los datos. |
| /BlobType | Especifique **PageBlob**, este flujo de trabajo solo tendrá éxito si se especifica la opción PageBlob. Esta no es la opción predeterminada y se debería mencionar en este comando. |
|/t:<*TargetDriveLetter*> | La letra de unidad del disco duro de destino para la sesión de copia actual, sin los dos puntos finales.|
|/format | Especifique este parámetro cuando la unidad debe tener formato; de lo contrario, omítalo. Antes de que la herramienta aplique formato a la unidad, se le pedirá una confirmación desde la consola. Para suprimir la confirmación, especifique el parámetro /silentmode.|
|/encrypt | Este parámetro se especifica cuando la unidad todavía no se ha cifrado con BitLocker y debe cifrarse mediante la herramienta. Si la unidad ya se ha cifrado con BitLocker, a continuación, omita este parámetro y especifique el parámetro /bk, proporcionando la clave de BitLocker existente. Si especifica el parámetro/format, también debe especificar el parámetro /encrypt. |
|/srcdir:<*SourceDirectory*> | El directorio de origen que contiene archivos que se copiarán en la unidad de destino. La ruta del directorio debe ser una ruta absoluta (no una ruta de acceso relativa).|
|/dstdir:<*DestinationBlobVirtualDirectory*> | La ruta de acceso al directorio virtual de destino en la cuenta de almacenamiento de Microsoft Azure. Asegúrese de utilizar nombres de contenedor válidos al especificar los directorios virtuales de destino o blobs. Tenga en cuenta que los nombres de contenedor deben estar en minúsculas.|

  >[AZURE.NOTE]Un archivo de diario se creará en la carpeta WAImportExport, que captura la información completa del flujo de trabajo. Necesitará este archivo cuando al crear un trabajo de importación en el portal de Azure.

  ![Salida de PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Crear trabajo de importación en el portal de Azure
1. Diríjase a su cuenta de almacenamiento en el [Portal de administración](https://manage.windowsazure.com/) y haga clic en **Importación/Exportación** y luego en **Crear trabajo de importación** en el panel de tareas.

    ![Portal](./media/backup-azure-backup-import-export/azureportal.png)

2. En el paso 1 del asistente, indique que ha preparado su unidad y que tiene el archivo de diario de la unidad disponible. En el paso 2 del asistente, proporcione la información de contacto de la persona responsable de este trabajo de importación.
3. En el paso 3, cargue los archivos de diario de unidad que haya obtenido durante la sección anterior.
4. En el paso 4, escriba un nombre descriptivo para el trabajo de importación. Tenga en cuenta que el nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. El nombre elegido le servirá para realizar un seguimiento de sus trabajos mientras que estén en progreso y una vez se hayan completado.
5. A continuación, seleccione en la lista la región de su centro de datos. La región del centro de datos indicará el centro de datos y la dirección donde debe enviar el paquete.

    ![CD](./media/backup-azure-backup-import-export/dc.png)

6. En el paso 5, seleccione en la lista el transportista para la devolución y, a continuación, escriba el número de cuenta del transportista. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación.

7. Envíe el disco y escriba el número de seguimiento para realizar un seguimiento del estado del envío. Una vez que el disco llega al centro de datos, se copia en la cuenta de almacenamiento y el estado se actualiza.

    ![Estado completo](./media/backup-azure-backup-import-export/complete.png)

### Completar el flujo de trabajo
Una vez que los datos de copia de seguridad iniciales están disponibles en la cuenta de almacenamiento, el agente de copia de seguridad de Azure copia el contenido de los datos desde esta cuenta a la cuenta de almacenamiento de copia de seguridad de varios inquilinos. En la siguiente copia de seguridad de programación, el agente de Copia de seguridad de Azure realiza la copia de seguridad incremental sobre la copia de seguridad inicial.

## Pasos siguientes
- Para las preguntas sobre el flujo de trabajo de importación y exportación de Azure, vea este [artículo](../storage-import-export-service.md).
- Vea las [Preguntas más frecuentes](backup-azure-backup-faq.md) sobre la copia de seguridad sin conexión de Azure si tiene alguna pregunta sobre el flujo de trabajo.

<!---HONumber=AcomDC_1203_2015-->