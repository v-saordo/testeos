<properties 
	pageTitle="Tutorial: Copia de datos de salida en una base de datos de SQL Server (Azure PowerShell)" 
	description="En este tutorial amplía el del uso de Azure PowerShell en el sentido de que la canalización copia los datos de salida a una base de datos de SQL Server."
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="spelluru"/>


# Tutorial: Copia de datos de salida en una base de datos de SQL Server local (Azure PowerShell)
En este tutorial, aprenderá a configurar el entorno para habilitar el proceso y trabajar con sus datos locales.
 
En el último paso del escenario de procesamiento de registro desde el primer tutorial con Partición -> Enriquecer -> Analizar flujo de trabajo, la salida de la eficacia de la campaña de marketing se ha copiado en una base de datos SQL de Azure. También puede mover estos datos a un SQL Server local para realizar análisis dentro de su organización.
 
Para copiar los datos de eficacia de la campaña de marketing de blobs de Azure a un SQL Server local, deberá crear servicios vinculados locales adicionales, una tabla y un proceso con el mismo conjunto de cmdlets que se introdujo en el primer tutorial.

**Debe** seguir los pasos del [Tutorial: mover y procesar archivos de registro mediante la Factoría de datos][datafactorytutorial] antes de realizar el tutorial de este artículo.

**(recomendado)** Revise y practique el tutorial del artículo [Habilitar la canalización para trabajar con datos locales][useonpremisesdatasources] para obtener un tutorial sobre cómo crear una canalización para mover datos de un SQL Server local a un almacén de blobs de Azure.


En este tutorial, realizará los siguientes pasos:

1. [Paso 1: crear una puerta de enlace de administración de datos](#OnPremStep1). La puerta de enlace de administración de datos es un agente cliente que proporciona acceso a orígenes de datos locales de la organización desde la nube. La puerta de enlace permite transferir datos entre un SQL Server local y almacenes de datos de Azure.	

	Debe tener al menos una puerta de enlace instalada en su entorno corporativo, así como registrarla con la factoría de datos de Azure antes de agregar la base de datos de SQL Server local como un servicio vinculado a una factoría de datos de Azure.

2. [Paso 2: crear un servicio vinculado para el SQL Server local](#OnPremStep2). En este paso, cree primero una base de datos y una tabla en el equipo de SQL Server local y, a continuación, cree el servicio vinculado: **OnPremSqlLinkedService**.
3. [Paso 3: crear la tabla y la canalización](#OnPremStep3). En este paso, creará una tabla **MarketingCampaignEffectivenessOnPremSQLTable** y la canalización **EgressDataToOnPremPipeline**. 

4. [Paso 4: supervisar la canalización y ver el resultado](#OnPremStep4). En este paso, supervisará los procesos, las tablas y los segmentos de datos mediante el Portal de Azure clásico.


## <a name="OnPremStep1"></a>Paso 1: crear una puerta de enlace de administración de datos

La puerta de enlace de administración de datos es un agente cliente que proporciona acceso a orígenes de datos locales de la organización desde la nube. La puerta de enlace permite transferir datos entre un SQL Server local y almacenes de datos de Azure.
  
Debe tener al menos una puerta de enlace instalada en su entorno corporativo, así como registrarla con la factoría de datos de Azure antes de agregar la base de datos de SQL Server local como un servicio vinculado a una factoría de datos de Azure.

Si tiene una puerta de enlace de datos existente que puede utilizar, omita este paso.

1.	Cree una puerta de enlace de datos lógica. En el **Portal de Azure**, haga clic en **Servicios vinculados** en la hoja **FACTORÍA DE DATOS**.
2.	Haga clic en **Agregar (+) puerta de enlace de datos** en la barra de comandos.  
3.	En la hoja **Nueva puerta de enlace de datos**, haga clic en **CREAR**.
4.	En la hoja **Crear**, escriba **MyGateway** como **nombre** de la puerta de enlace de datos.
5.	Haga clic en **ELEGIR UNA REGIÓN** y cámbiela si es necesario. 
6.	Haga clic en **Aceptar** en la hoja **Crear**. 
7.	Aparecerá la hoja **Configurar**. 
8.	En la hoja **Configurar**, haga clic en **Instalar directamente en este equipo**. Esto se descarga, instala y configura la puerta de enlace en el equipo y registra la puerta de enlace con el servicio. Si tiene una puerta de enlace existente instalada en el equipo que desea vincular a esta puerta de enlace lógica en el portal, utilice la clave de esta hoja para volver a registrar la puerta de enlace mediante la herramienta Administrador de configuración de puerta de enlace de administración de datos (vista previa).

	![Administrador de configuración de puerta de enlace de administración de datos][image-data-factory-datamanagementgateway-configuration-manager]

9. Haga clic en **Aceptar** para cerrar la hoja **Configurar** y en **Aceptar** para cerrar la hoja **Crear**. Espere hasta que el estado de **MyGateway** en la hoja **Servicios vinculados** cambie a **BIEN**. También puede iniciar la herramienta **Administrador de configuración de puerta de enlace de administración de datos (vista previa)** para confirmar que el nombre de la puerta de enlace coincide con el nombre en el portal y el **estado** es **Registrado**. Puede ser necesario cerrar y volver a abrir la hoja Servicios vinculados para ver el estado más reciente. La pantalla puede tardar unos minutos antes de actualizarse con el estado más reciente.

## <a name="OnPremStep2"></a> Paso 2: crear un servicio vinculado para el SQL Server local

En este paso, cree primero la base de datos necesaria y una tabla en el equipo de SQL Server local y, a continuación, cree el servicio vinculado.

### Preparación de la tabla y la base de datos local

Para empezar, deberá crear la base de datos de SQL Server, tabla, tipos definidos por el usuario y procedimientos almacenados. Se utilizarán para pasar los resultados de **MarketingCampaignEffectiveness** del blob de Azure a la base de datos de SQL Server.

1.	En el **Explorador de Windows**, vaya a la subcarpeta **OnPremises** en **C:\\ADFWalkthrough** (o la ubicación donde extrajo los ejemplos).
2.	Abra **prepareOnPremDatabase&Table.ps1** en su editor favorito, reemplace el texto resaltado con su información de SQL Server y guarde el archivo (proporcione los detalles de **la autenticación de SQL**). Para el tutorial, habilite la autenticación de SQL para la base de datos. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. En **Azure PowerShell**, vaya a la carpeta **C:\\ADFWalkthrough\\OnPremises**.
4.	Ejecute **prepareOnPremDatabase&Table.ps1 ** **(& entre dobles comillas o como se muestra a continuación)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Una vez que el script se ejecute correctamente, verá lo siguiente:
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### Creación del servicio vinculado

1.	En el **Portal de Azure**, haga clic en el icono **Servicios vinculados** en la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory**.
2.	En la hoja **Servicios vinculados**, haga clic en **Agregar (+) almacén de datos**.
3.	En la hoja **Nuevo almacén de datos**, escriba **OnPremSqlLinkedService** para el **nombre**. 
4.	Haga clic en **Tipo (configuración obligatoria)** y seleccione **SQL Server**. Debería ver las opciones de configuración **PUERTA DE ENLACE DE DATOS**, **Servidor**, **Base de datos** y **CREDENCIALES** en la hoja **Nuevo almacén de datos**. 
5.	Haga clic en **PUERTA DE ENLACE DE DATOS (configure las opciones obligatorias)** y seleccione el **MyGateway** que ha creado antes. 
6.	Escriba el **nombre** del servidor de base de datos que hospeda la base de datos **MarketingCampaigns**. 
7.	Escriba **MarketingCampaigns** para la base de datos. 
8.	Haga clic en **CREDENCIALES**. 
9.	En la hoja **Credenciales**, haga clic en la opción **Haga clic aquí para establecer las credenciales de forma segura**.
10.	Instala una aplicación de un clic por primera vez e inicia el cuadro de diálogo **Definición de credenciales**.
11.	En el cuadro de diálogo **Establecer credenciales**, escriba los valores de **Nombre de usuario** y **Contraseña**, y haga clic en **Aceptar**. Espere hasta que se cierre el cuadro de diálogo. 
12.	Haga clic en **Aceptar** en la hoja **Nuevo almacén de datos**. 
13.	En la hoja **Servicios vinculados**, confirme que **OnPremSqlLinkedService** aparece en la lista y que el **estado** del servicio vinculado es **Bueno**.

## <a name="OnPremStep3"></a> Paso 3: crear la tabla y la canalización

### Creación de la tabla lógica local

1.	En **Azure PowerShell**, cambie a la carpeta **C:\\ADFWalkthrough\\OnPremises**. 
2.	Use el cmdlet **New-AzureRmDataFactoryDataset** para crear las tablas para **MarketingCampaignEffectivenessOnPremSQLTable.json** de la manera siguiente.

			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Creación del proceso para copiar los datos del blob de Azure a SQL Server

1.	Use el cmdlet **New-AzureRmDataFactoryPipeline** para crear la canalización para **EgressDataToOnPremPipeline.json** de la manera siguiente.

			
		New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. Use el cmdlet **Set-AzureRmDataFactoryPipelineActivePeriod** para especificar el período activo para **EgressDataToOnPremPipeline**.

			
		Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	Presione **‘Y’** para continuar.
	
## <a name="OnPremStep4"></a> Paso 4: supervisar la canalización y ver el resultado

Ahora puede seguir los mismos pasos de la sección [Paso 6: Supervisión de tablas y canalizaciones](#MainStep6) para supervisar la nueva canalización y los segmentos de datos para la nueva tabla de ADF local.
 
Cuando vea que el estado de un segmento de la tabla **MarketingCampaignEffectivenessOnPremSQLTable** pasa a Listo, significa que la canalización ha completado la ejecución del segmento. Para ver los resultados, consulte la tabla **MarketingCampaignEffectiveness** en la base de datos **MarketingCampaigns** en su SQL Server.
 
¡Enhorabuena! Ha realizado correctamente el tutorial para usar el origen de datos local.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-move-data-between-onprem-and-cloud.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx


[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=AcomDC_0218_2016-->