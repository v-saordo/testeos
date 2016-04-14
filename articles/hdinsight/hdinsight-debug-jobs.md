<properties
	pageTitle="Depuración de Hadoop en HDInsight: ver registros e interpretar mensajes de error | Microsoft Azure"
	description="Conozca los mensajes de error que puede recibir cuando administre HDInsight con PowerShell, así como los pasos que debe seguir para la recuperación."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="jgao"/>

# Analizar los registros de HDInsight

Cada clúster de Hadoop en HDInsight de Azure tiene una cuenta de almacenamiento de Azure que se usa como sistema de archivos predeterminado. La cuenta de almacenamiento se conoce como la cuenta de almacenamiento predeterminada. El clúster usa el almacenamiento de tablas de Azure y el almacenamiento de blobs de la cuenta de almacenamiento predeterminada para almacenar sus registros. Para averiguar cuál es la cuenta de almacenamiento predeterminada de su clúster, consulte [Administración de clústeres de Hadoop en HDInsight](hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Los registros conservan la cuenta de almacenamiento incluso después de que se elimine el clúster.

##Registros escritos en tablas de Azure

Los registros que se escriben en las tablas de Azure proporcionan un nivel de información sobre lo que ocurre en un clúster de HDInsight.

Al crear un clúster de HDInsight, se crean automáticamente seis tablas para los clústeres basados en Linux en el almacenamiento de tablas predeterminado:

- hdinsightagentlog
- syslog
- daemonlog
- hadoopservicelog
- ambariserverlog
- ambariagentlog

Se crean tres tablas para los clústeres basados en Windows:

- setuplog: registro de eventos y excepciones detectados en el aprovisionamiento y la configuración de los clústeres de HDInsight.
- hadoopinstalllog: registro de eventos y excepciones detectados durante la instalación de Hadoop en el clúster. Esta tabla puede ser útil para depurar problemas relacionados con los clústeres que se crearon con parámetros personalizados.
- hadoopservicelog: registro de eventos y excepciones registrados por todos los servicios de Hadoop. Esta tabla puede ser útil para depurar problemas relacionados con errores de trabajo en clústeres de HDInsight.

Los nombres de archivo de la tabla son **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Estas tablas contienen los siguientes campos:

- ClusterDnsName
- ComponentName
- EventTimestamp
- Host
- MALoggingHash
- Message
- N
- PreciseTimeStamp
- Rol
- RowIndex
- Inquilino
- TIMESTAMP
- TraceLevel

### Herramientas para acceder a los registros

Hay muchas herramientas disponibles para acceder a los datos de estas tablas:

-  Visual Studio
-  Explorador de almacenamiento de Azure
-  Power Query para Excel

#### Uso de Power Query para Excel

Puede instalar Power Query desde [www.microsoft.com/es-es/download/details.aspx?id=39379](http://www.microsoft.com/es-ES/download/details.aspx?id=39379). Consulte los requisitos del sistema en la página de descarga.

**Cómo usar Power Query para abrir y analizar el registro de servicio**

1. Abra **Microsoft Excel**.
2. En el menú de **Power Query**, haga clic en **De Azure** y, luego, en **Desde un almacenamiento de tablas de Microsoft Azure **.
 
	![Power Query de Excel de Hadoop de HDInsight: abrir el almacenamiento de tablas de Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Escriba el nombre de la cuenta de almacenamiento. Puede ser el nombre corto o el FQDN.
4. Escriba la clave de la cuenta de almacenamiento. Verá una lista de tablas:

	![Registros de Hadoop de HDInsight almacenados en el almacenamiento de tablas de Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Haga clic con el botón derecho en la tabla hadoopservicelog, en el panel **Navegador**, y seleccione **Editar**. Verá cuatro columnas. Opcionalmente, puede eliminar las columnas **Clave de partición**, **Clave de fila** y **Marca de tiempo** seleccionándolas y haciendo clic en **Quitar columnas** en las opciones de la cinta.
6. Haga clic en el icono Expandir, en la columna Contenido, para elegir las columnas que desea importar en la hoja de cálculo de Excel. Para esta demostración, se ha elegido TraceLevel y ComponentName, ya que pueden aportar información básica sobre qué componentes tenían problemas.

	![Registros de Hadoop de HDInsight: elegir columnas](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Haga clic en **Aceptar** para importar los datos.
8. Seleccione las columnas **TraceLevel**, Rol y **ComponentName** y luego haga clic en el control **Agrupar por** en la cinta.
9. Haga clic en **Aceptar** en el cuadro de diálogo Agrupar por.
10. Haga clic en **Aplicar y cerrar**.
 
Ahora puede usar Excel para filtrar y ordenar según sea necesario. Obviamente, puede interesarle incluir otras columnas (por ejemplo, Mensaje) para profundizar en los problemas cuando se produzcan, pero si selecciona y agrupa las columnas descritas anteriormente obtendrá una idea general adecuada de lo que ocurre en los servicios de Hadoop. La misma idea se puede aplicar a las tablas setuplog y hadoopinstalllog.

#### Usar Visual Studio

**Cómo usar Visual Studio**

1. Abra Visual Studio.
2. En el menú **Ver**, haga clic en **Cloud Explorer**. También puede hacer clic simplemente en **CTRL+\\, CTRL+X**.
3. En **Cloud Explorer**, seleccione **Tipos de recursos**. La otra opción disponible es **Grupos de recursos**.
4. Expanda **Cuentas de almacenamiento**, la cuenta de almacenamiento predeterminada de su clúster y **Tablas**.
5. Haga doble clic en hadoopservicelog.
6. Agregue un filtro. Por ejemplo:
	
		TraceLevel eq 'ERROR'

	![Registros de Hadoop de HDInsight: elegir columnas](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)

	Para obtener más información sobre cómo construir filtros, consulte [Construcción de cadenas de filtro para el Diseñador de tablas](https://msdn.microsoft.com/library/azure/ff683669.aspx).
 
##Registros escritos en el almacenamiento de blobs de Azure

[Los registros que se escriben en las tablas de Azure](#log-written-to-azure-tables) proporcionan un nivel de información sobre lo que ocurre en un clúster de HDInsight. Sin embargo, estas tablas no proporcionan registros de nivel de tarea, que pueden ser útiles para obtener detalles sobre los problemas cuando se producen. Para proporcionar este nivel de detalle superior, los clústeres de HDInsight están configurados para escribir registros de tareas en su cuenta de almacenamiento de blobs para cualquier trabajo que se envíe a través de Templeton. En la práctica, esto hace referencia a los trabajos enviados mediante los cmdlets de Microsoft Azure PowerShell o las API de envío de trabajos de .NET, no a los trabajos enviados a través de RDP o el acceso de línea de comandos al clúster.

Para ver los registros, consulte [Acceso a registros de aplicación de YARN en HDInsight basado en Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md).

Para obtener más información sobre los registros de aplicación, consulte [Simplifying user-logs management and access in YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) (Simplificar la administración de registros de usuario y el acceso en YARN).
 
 
## Visualización de los registros de trabajo y del estado del clúster

###Acceder a la interfaz de usuario de Hadoop

En el Portal de Azure, haga clic en un nombre de clúster de HDInsight para abrir la hoja del clúster. En la hoja del clúster, haga clic en **Panel de inicio**.

![Inicie el panel del clúster](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard.png)

Cuando se le pida, escriba las credenciales de administrador para el clúster. En la consola de consultas que se abre, haga clic en **IU de Hadoop**.

![Inicie la IU de Hadoop](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard-hadoop-ui.png)


###Acceder a la interfaz de usuario de Yarn

En el Portal de Azure, haga clic en un nombre de clúster de HDInsight para abrir la hoja del clúster. En la hoja del clúster, haga clic en **Panel de inicio**. Cuando se le pida, escriba las credenciales de administrador para el clúster. En la consola de consulta que se abre, haga clic en **IU de YARN**.

Puede utilizar la IU de YARN para hacer lo siguiente:

* **Obtener el estado del clúster**. En el panel izquierdo, expanda **Clúster** y haga clic en **Acerca de**. Esto presenta detalles del estado del clúster, como memoria total asignada, núcleos usados, estado del administrador de recursos de clúster, versión del clúster, etc.

    ![Inicie el panel del clúster](./media/hdinsight-debug-jobs/hdi-debug-yarn-cluster-state.png)

* **Obtener el estado del nodo**. En el panel izquierdo, expanda **Clúster** y haga clic en **Nodos**. Esto enumera todos los nodos del clúster, la dirección HTTP de cada nodo, los recursos asignados a cada nodo, etc.

* **Supervisar el estado de los trabajos**. En el panel izquierdo, expanda **Clúster** y haga clic en **Aplicaciones** para enumerar todos los trabajos del clúster. Si desea ver los trabajos que se encuentran en un estado específico (como nuevo, enviado, en ejecución, etc.), haga clic en el vínculo apropiado en **Aplicaciones**. Además, puede hacer clic en el nombre del trabajo para obtener más información sobre el trabajo, incluyendo la salida, los registros, etc.

###Acceder a la interfaz de usuario de HBase

En el Portal de Azure, haga clic en un nombre de clúster de HBase de HDInsight para abrir la hoja del clúster. En la hoja del clúster, haga clic en **Panel de inicio**. Cuando se le pida, escriba las credenciales de administrador para el clúster. En la consola de consultas que se abre, haga clic en **Interfaz de usuario de HBase**.

## Códigos de error de HDInsight

Con los mensajes de error incluidos en esta sección pretendemos ayudar a los usuarios de Hadoop en HDInsight de Azure a comprender algunas situaciones de error con las que se pueden encontrar al administrar el servicio con Azure PowerShell, así como asesorarlos sobre los pasos que pueden seguir para recuperarse del error.

Algunos de estos mensajes de error también podrían aparecer en el Portal de Azure cuando se usa para administrar clústeres de HDInsight. Sin embargo, no es posible presentar de forma tan pormenorizada otros mensajes de error que pueden aparecer allí debido a las restricciones que afectan a las acciones de subsanación posibles en este contexto. Otros mensajes de error se asocian a los contextos en que la mitigación resulta obvia.

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **Descripción**: proporcione los datos de la base de datos SQL de Azure de al menos un componente a fin de utilizar la configuración personalizada para las tiendas de metadatos de Hive y Oozie.
- **Mitigación**: el usuario debe facilitar una tienda de metadatos SQL de Azure válida y volver a enviar la solicitud.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **Descripción**: no se pudo crear un clúster en la región *nombredelaregión*. Utilice una región de HDInsight válida y vuelva a enviar la solicitud.
- **Mitigación**: el cliente debe crear el clúster en una región que actualmente lo admita: sudeste asiático, Europa occidental, norte de Europa y este u oeste de Estados Unidos.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **Descripción**: el servidor no encuentra el registro de clúster solicitado.  
- **Mitigación**: vuelva a intentarlo.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **Descripción**: el nombre DNS del clúster *nombreDNS* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.  
- **Mitigación**: asegúrese de que se haya utilizado un nombre DNS válido para el clúster que empiece y acabe con un carácter alfanumérico y que no contenga ningún carácter especial, aparte del guión (-), y, a continuación, vuelva a intentarlo.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **Descripción**: el nombre de clúster *nombredeclúster* no está disponible. Elija otro nombre.  
- **Mitigación**: el usuario debe especificar un nombre de clúster que sea único y que no esté ya en uso, y, a continuación, volver a intentarlo. Si el usuario está usando el Portal, la IU le informará durante los pasos de creación si el nombre de clúster ya está en uso.


### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **Descripción**: la contraseña del clúster no es válida. La contraseña debe cumplir estos requisitos: tener al menos 10 caracteres, incluir como mínimo un número, una letra mayúscula, una letra minúscula y un carácter especial, no contener espacios, y no estar formada a partir del nombre de usuario.  
- **Mitigación**: proporcione una contraseña de clúster válida y vuelva a intentarlo.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **Descripción**: el nombre de usuario del clúster no es válido. Asegúrese de que no contenga caracteres especiales ni espacios.  
- **Mitigación**: proporcione un nombre de usuario de clúster válido y vuelva a intentarlo.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **Descripción**: el nombre DNS del clúster *nombreDNSdelclúster* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.  
- **Mitigación**: proporcione un nombre de usuario de clúster DNS válido y vuelva a intentarlo.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **Descripción**: el nombre de contenedor del URI *URIdecontenedor* y el nombre DNS *nombreDNS* del cuerpo de la solicitud deben coincidir.  
- **Mitigación**: asegúrese de que el nombre del contenedor y el nombre DNS coincidan y vuelva a intentarlo.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **Descripción**: la configuración de clúster no es válida. No se encuentra ninguna definición de nodo de datos en el tamaño de nodo.  
- **Mitigación**: vuelva a intentarlo.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
- **Descripción**: error al eliminar la implementación del clúster.  
- **Mitigación**: vuelva a intentar la eliminación.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **Descripción**: error de configuración del servicio. No se encuentra la información de asignación de DNS requerida.  
- **Mitigación**: elimine el clúster y cree uno nuevo.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **Descripción**: intento de duplicación de un contenedor de clúster. Existe un registro con el nombre *nombredelcontenedor*, pero las propiedades Etag no coinciden.
- **Mitigación**: proporcione un nombre exclusivo para el contenedor y vuelva a intentarlo.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **Descripción**: el servicio hospedado *nombredelserviciohospedado* ya incluye un clúster. Los servicios hospedados no pueden contener varios clústeres.  
- **Mitigación**: hospede el clúster en otro servicio hospedado.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **Descripción**: el servidor no pudo actualizar el estado de la implementación del clúster.  
- **Mitigación**: vuelva a intentarlo. Si esto ocurre varias veces, póngase en contacto con CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **Descripción**: el clúster *nombredelclúster* se eliminó durante el mantenimiento. Vuelva a crearlo.
- **Mitigación**: vuelva a crear el clúster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **Descripción**: configuración de clúster no válida. Configuración de nodo principal requerida no encontrada en los tamaños de nodo.
- **Mitigación**: vuelva a intentarlo.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **Descripción**: no se puede crear el servicio hospedado *nombredelserviciohospedado*. Vuelva a intentarlo.  
- **Mitigación**: vuelva a intentarlo.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **Descripción**: el servicio hospedado *nombredelserviciohospedado* ya incluye una implementación de producción. Los servicios hospedados no pueden contener varias implementaciones de producción. Vuelva a intentarlo con un nombre de clúster diferente.
- **Mitigación**: utilice un nombre de clúster diferente y vuelva a intentarlo.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **Descripción**: no se encuentra el servicio hospedado *nombredelserviciohospedado* del clúster.  
- **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **Descripción**: el servicio hospedado *nombredelserviciohospedado* no tiene asociada ninguna implementación.  
- **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **Descripción**: al identificador de suscripción *identificadordelasuscripción* no le quedan recursos principales para crear el clúster *nombredelclúster*. Necesario: *recursosrequeridos*, Disponible: *recursosdisponibles*.  
- **Mitigación**: libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **Descripción**: el identificador de suscripción *identificadordelasuscripción* no tiene cuota para un nuevo servicio hospedado y, por tanto, no puede crear el clúster *nombredelclúster*.  
- **Mitigación**: libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **Descripción**: se produjo un error interno en el servidor. Vuelva a intentarlo.  
- **Mitigación**: vuelva a intentarlo.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **Descripción**: la ubicación de almacenamiento de Azure *nombrederegióndedatos* no es válida. Asegúrese de que la región sea correcta y vuelva a intentarlo.
- **Mitigación**: elija una ubicación de almacenamiento compatible con HDInsight, compruebe que el clúster esté colocalizado y vuelva a intentarlo.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **Descripción**: tamaño de máquina virtual no válido para los nodos de datos. Únicamente el tamaño de máquina virtual grande es compatible con todos los nodos de datos.  
- **Mitigación**: especifique el tamaño de nodo admitido para el nodo de datos y vuelva a intentarlo.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **Descripción**: tamaño de máquina virtual no válido para un nodo principal. Únicamente el tamaño de máquina virtual extragrande es compatible con el nodo principal.  
- **Mitigación**: especifique el tamaño de nodo admitido para el nodo principal y vuelva a intentarlo.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **Descripción**: el identificador de suscripción *identificadordelasuscripción* que se está utilizando no dispone de suficientes permisos para ejecutar la operación de eliminación del clúster *nombredelclúster*.  
- **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **Descripción**: el nombre del contenedor de blobs de la cuenta de almacenamiento externa *nombredelcontenedor* no es válido. Asegúrese de que el nombre comience con una letra y solo contenga minúsculas, números y guiones.  
- **Mitigación**: especifique un nombre de contenedor de blobs de cuenta de almacenamiento válido y vuelva a intentarlo.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **Descripción**: para configurar la cuenta de almacenamiento externa *nombredelacuentadealmacenamiento* es necesario establecer primero los datos de la clave secreta.  
- **Mitigación**: especifique una clave secreta válida para la cuenta de almacenamiento y vuelva a intentarlo.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **Descripción**: el encabezado de la versión *encabezadodelaversión* no tiene el formato válido de aaaa-mm-dd.  
- **Mitigación**: especifique un formato válido para el encabezado de la versión y vuelva a intentarlo.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **Descripción**: configuración de clúster no válida. Se encontró más de una configuración de nodo principal.  
- **Mitigación**: edite la configuración para que solo se especifique un nodo principal.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **Descripción**: la operación no se pudo completar en el tiempo permitido, o bien se alcanzó el número máximo de intentos. Vuelva a intentarlo.  
- **Mitigación**: vuelva a intentarlo.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **Descripción**: el parámetro *nombredelparámetro* no puede ser nulo ni estar vacío.  
- **Mitigación**: especifique un valor válido para el parámetro.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **Descripción**: al menos uno de los datos de la solicitud de creación de clúster no es válido. Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.  
- **Mitigación**: asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **Descripción**: la funcionalidad de región no está disponible para la región *nombredelaregión* y el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: especifique una región compatible con los clústeres de HDInsight. Las regiones admitidas son: sudeste asiático, Europa occidental, norte y oeste de Europa y este u oeste de Estados Unidos.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **Descripción**: la cuenta de almacenamiento *nombredelacuentadealmacenamiento* se encuentra en la región *nombredelaregiónactual*. Debería coincidir con la región del clúster *nombredelaregióndelclúster*.  
- **Mitigación**: especifique una cuenta de almacenamiento situada en la misma región que el clúster, o bien, si la cuenta de almacenamiento ya contiene datos, cree un nuevo clúster en la misma región que la cuenta de almacenamiento existente. Si está usando el Portal, la IU le informará de este problema con antelación.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **Descripción**: el identificador de suscripción proporcionado *identificadordelasuscripción* no está activo.  
- **Mitigación**: vuelva a activar la suscripción u obtenga una nueva suscripción válida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **Descripción**: no se encuentra el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: compruebe que el identificador de la suscripción sea válido y vuelva a intentarlo.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **Descripción**: no se puede resolver el DNS *URLdelDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
- **Mitigación**: proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida; entre otras cosas, debe empezar por **http://* y terminar en *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **Descripción**: no se puede comprobar la ubicación del recurso *URLdeDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
- **Mitigación**: proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida; entre otras cosas, debe empezar por **http://* y terminar en *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **Descripción**: la funcionalidad de versión no está disponible para la versión *versiónespecificada* y el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: elija una versión disponible y vuelva a intentarlo.

### <a id="VersionNotSupported"></a>VersionNotSupported
- **Descripción**: la versión *versiónespecificada* no es compatible.
- **Mitigación**: elija una versión compatible y vuelva a intentarlo.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **Descripción**: la versión *versiónespecificada* no está disponible en la región de Azure *regiónespecificada*.  
- **Mitigación**: elija una versión admitida en la región y vuelva a intentarlo.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **Descripción**: configuración de clúster no válida. No se encuentra la configuración de cuenta WASB requerida en las cuentas externas.  
- **Mitigación**: compruebe que la cuenta exista y que esté bien especificada en la configuración, y luego, vuelva a intentarlo.

## Pasos siguientes

[Usar las vistas de Ambari para depurar trabajos de Tez en HDInsight](hdinsight-debug-ambari-tez-view.md) [Habilitar los volcados de montón de los servicios de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md) [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md)

<!---HONumber=AcomDC_0218_2016-->