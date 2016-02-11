<properties 
	pageTitle="Restauración de una aplicación en el Servicio de aplicaciones de Azure" 
	description="Obtenga información sobre cómo restaurar la aplicación desde una copia de seguridad." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="cephalin"/>

# Restauración de una aplicación en el Servicio de aplicaciones de Azure

En este artículo se muestra cómo restaurar una aplicación del Servicio de aplicaciones de la que haya hecho previamente una copia de seguridad usando la característica de copias de seguridad del [Servicio de aplicaciones](app-service-value-prop-what-is). Para obtener más información, consulte la sección sobre [copias de seguridad del Servicio de aplicaciones](web-sites-backup.md).

La característica de restauración del Servicio de aplicaciones le permite restaurar la aplicación con sus bases de datos vinculadas (base de datos SQL o MySQL) a petición a un estado anterior o crear una nueva aplicación basada en una copia de seguridad de la aplicación original. La creación de una nueva aplicación que se ejecuta en paralelo con la versión más reciente puede resultar útil para la prueba A/B.

La característica de restauración del Servicio de aplicaciones, disponible en la hoja **Copias de seguridad** del [Portal de Azure](https://portal.azure.com), solo está disponible en los planes de tarifa estándar y premium. Para obtener información sobre cómo escalar la aplicación con las capas estándar o premium, consulte [Escalado de una aplicación en el Servicio de aplicaciones de Azure](web-sites-scale.md). Tenga en cuenta que el nivel premium permite realizar un mayor número de copias de seguridad diarias que el nivel estándar.

<a name="PreviousBackup"></a>
## Para restaurar una aplicación a partir de una copia de seguridad realizada anteriormente

1. En la hoja **Configuración** de la aplicación del Portal de Azure, haga clic en la opción **Copias de seguridad** para mostrar la hoja **Copias de seguridad**. A continuación, haga clic en **Restaurar ahora** en la barra de comandos. 
	
	![Elegir Restaurar ahora][ChooseRestoreNow]

3. En la hoja **Restaurar**, seleccione primero el origen de la copia de seguridad.

	![](./media/web-sites-restore/021ChooseSource.png)
	
	La opción **Copia de seguridad de aplicaciones** muestra todas las copias de seguridad que crea la propia aplicación directamente, ya que son las únicas de las que las aplicaciones son conscientes. Puede seleccionar una opción con facilidad. La opción **Almacenamiento** permite seleccionar el archivo ZIP de copia de seguridad real de la cuenta de almacenamiento y el contenedor en que está configurado en la hoja **Copias de seguridad**. Si hay archivos de copia de seguridad de otras aplicaciones del contenedor, también puede seleccionarlos para la restauración.

4. A continuación, especifique el destino de la restauración de la aplicación en **Destino de restauración**.

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING] Si elige **Sobrescribir**, se borrarán todos los datos relacionados con la aplicación existente. Antes de hacer clic en **Aceptar**, asegúrese de que es exactamente lo que desea.
	
	Puede seleccionar **Aplicación existente** para restaurar la copia de seguridad de la aplicación a otra aplicación en el mismo grupo de recursos. Antes de utilizar esta opción, debe haber creado primero otra aplicación en el grupo de recursos con una configuración de base de datos reflejada en la definida en la copia de seguridad de la aplicación.
	
5. Haga clic en **Aceptar**.

<a name="StorageAccount"></a>
## Descarga o eliminación de una copia de seguridad de una cuenta de almacenamiento
	
1. En la hoja principal **Examinar** del Portal de Azure, seleccione **Cuentas de almacenamiento**.
	
	Se mostrará una lista de las cuentas de almacenamiento existentes.
	
2. Seleccione la cuenta de almacenamiento que contiene la copia de seguridad que desea descargar o eliminar.
	
	Aparecerá la hoja **Almacenamiento**.

3. Seleccione la parte **Contenedores** de la hoja **Almacenamiento** para mostrar la hoja **Contenedores**.
	
	Aparecerá una lista de contenedores. Esta lista también mostrará la dirección URL y la fecha de la última modificación de este contenedor.
	
	![Ver contenedores][ViewContainers]

4. En la lista, seleccione el contenedor y muestre la hoja en la que aparece una lista de nombres de archivo, junto con el tamaño de cada archivo.
	
5. Al seleccionar un archivo, puede elegir **Descargar** o **Eliminar el archivo**. Tenga en cuenta que hay dos tipos de archivos principales: los archivos .zip y .xml.

<a name="OperationLogs"></a>
## Visualización de los registros de auditoría
	
1. Para ver detalles sobre si se ha realizado correctamente o no la operación de restauración de la aplicación, seleccione la parte **Registro de auditoría** de la hoja principal **Examinar**. 
	
	La hoja **Registro de auditoría** muestra todas las operaciones, junto con el nivel, estado, recursos y detalles de hora.
	
2. Desplácese por la hoja para buscar las operaciones relacionadas con la aplicación.
3. Para ver detalles adicionales sobre una operación, seleccione la operación en la lista.
	
La hoja de detalles mostrará la información disponible relacionada con la operación.
	
>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Pasos siguientes

También puede crear una copia de seguridad y restaurar las aplicaciones del Servicio de aplicaciones mediante la API de REST (consulte [Uso de REST para copia de seguridad y restauración de aplicaciones del Servicio de aplicaciones](websites-csm-backup.md)).

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 

<!---HONumber=AcomDC_0128_2016-->