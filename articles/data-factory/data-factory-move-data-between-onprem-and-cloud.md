<properties 
	pageTitle="Movimiento de datos entre una ubicación local y la nube mediante Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover datos entre local y la nube mediante Data Management Gateway y Factoría de datos de Azure." 
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
	ms.date="01/21/2016" 
	ms.author="spelluru"/>

# Movimiento de datos entre orígenes locales y la nube con Data Management Gateway
Uno de los desafíos de la integración de datos moderna es mover datos sin problemas entre ubicación la local y la nube. Factoría de datos hace que esta integración con la puerta de enlace de administración de datos sea perfecta. La puerta de enlace de administración de la factoría de datos es un agente que puede instalar de forma local para permitir que las canalizaciones híbridas.

Este artículo proporciona información general de la integración de los almacenes de datos locales y los almacenes de datos en la nube mediante la factoría de datos. Este artículo se basa en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) y otros artículos de conceptos básicos de Factoría de datos. La información general siguiente supone que está familiarizado con conceptos de la factoría de datos como canalizaciones, actividades, conjuntos de datos y actividad de copia.

La puerta de enlace de datos proporciona las siguientes capacidades:

1.	Modelo de orígenes de datos local y orígenes de datos en la nube en la misma factoría de datos y movimiento de datos.
2.	Tiene un solo panel de vidrio para la supervisión y administración con visibilidad del estado de la puerta de enlace mediante el panel basado en la nube de Factoría de datos.
3.	Administrar el acceso a orígenes de datos locales de forma segura.
	1. No es necesario ningún cambio en el firewall corporativo. La puerta de enlace solo hace conexiones salientes basadas en HTTP para abrir Internet.
	2. Cifrar las credenciales de los almacenes de datos locales con su certificado.
4.	Mover datos de manera eficaz: los datos se transfieren en paralelo, con resistencia a problemas de red intermitentes mediante una lógica automática de reintentos.

## Consideraciones para utilizar Data Management Gateway
1.	Una sola instancia de Data Management Gateway se puede usar para varios orígenes de datos locales, pero tenga en cuenta que **una sola puerta de enlace está asociada a una sola Factoría de datos de Azure** y no puede compartirse con otra factoría de datos.
2.	**Solo puede haber una instancia de Data Management Gateway** instalada en un solo equipo. Supongamos que tiene dos factorías de datos que necesitan tener acceso a orígenes de datos locales. Debe instalar puertas de enlace en dos equipos locales donde cada puerta de enlace esté asociada a una factoría de datos diferente.
3.	La **puerta de enlace no necesita estar en el mismo equipo que el origen de datos** pero, si está cerca del origen de datos, reduce el tiempo de conexión de la puerta de enlace al origen de datos. Le recomendamos que instale la puerta de enlace en un equipo diferente del que hospeda el origen de datos local para que la puerta de enlace no compita por los recursos con el origen de datos.
4.	Puede tener **varias puertas de enlace en diferentes equipos conectados al mismo origen de datos local**. Por ejemplo, puede que tenga dos puertas de enlace que atienden a dos factorías de datos pero el mismo origen de datos local está registrado con las dos factorías de datos.
5.	Si ya tiene una puerta de enlace instalada en el equipo que atiende a un escenario de **Power BI** , instale una **puerta de enlace independiente para Factoría de datos de Azure** en otro equipo.
6.	Debe **usar la puerta de enlace, incluso cuando use ExpressRoute**. 
7.	Debe tratar el origen de datos como un origen de datos local (que está detrás de un firewall) incluso si usa **ExpressRoute** y **usar la puerta de enlace** para establecer la conectividad entre el servicio y el origen de datos. 

## Instalación de Data Management Gateway

### Instalación de la puerta de enlace: requisitos previos
1.	Las versiones de **sistema operativo** compatibles son Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.
2.	La **configuración** recomendada del equipo de la puerta de enlace es de al menos 2 GHz, 4 núcleos, 8 GB de RAM y disco de 80 GB.
3.	Si el equipo host está en hibernación, la puerta de enlace no podrá responder a las solicitudes de datos. Por tanto, configure un **plan de energía** adecuado en el equipo antes de instalar la puerta de enlace. La instalación de la puerta de enlace emite un mensaje si el equipo está configurado para la hibernación.

Puesto que las ejecuciones de la actividad de copia suceden con una frecuencia determinada, el uso de recursos (CPU, memoria) en el equipo también sigue el mismo patrón con las horas pico y los tiempos de inactividad. El uso de recursos también depende en gran medida de la cantidad de datos que se mueven. Cuando hay varios trabajos de copia en curso, observará que el uso de los recursos aumenta durante las horas pico. Aunque arriba se muestra la configuración mínima, siempre es mejor tener una configuración con más recursos que la configuración mínima descrita, en función de la carga específica para el movimiento de datos.

### Instalación
Se puede instalar Data Management Gateway con la descarga de un paquete de instalación MSI desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). El MSI también puede usarse para actualizar el Data Management Gateway existente a la versión más reciente, con toda la configuración conservada. Para encontrar el vínculo al paquete MSI en el Portal de Azure, siga el tutorial paso a paso que aparece a continuación.


### Procedimientos recomendados de instalación:
1.	Configurar el plan de energía en el equipo host para la puerta de enlace, de forma que el equipo no hiberne. Si el equipo host está en hibernación, la puerta de enlace no podrá responder a las solicitudes de datos.
2.	Se debe hacer una copia de seguridad del certificado asociado a la puerta de enlace.

## Actualización de Data Management Gateway
De forma predeterminada, Data Management Gateway se actualiza automáticamente cuando hay disponible una versión más reciente de la puerta de enlace. La puerta de enlace no se actualiza hasta que se completan todas las tareas programadas y deja de procesar tareas hasta que se complete la operación de actualización. Si se produce un error en la actualización, la puerta de enlace se revierte a la versión anterior.

Verá la hora de actualización programada en el portal en la hoja de propiedades de la puerta de enlace, en la página principal del Administrador de configuración de Data Management Gateway y en el mensaje de notificación de la bandeja del sistema. Tiene la opción de instalar la actualización inmediatamente o de esperar a que la puerta de enlace se actualice automáticamente a la hora programada. Por ejemplo, la captura de pantalla siguiente muestra el mensaje de notificación que aparece en el Administrador de configuración de Data Management Gateway junto con el botón de actualización en el que se hace clic para instalarla inmediatamente.

![Actualización en el Administrador de configuración de Data Management Gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-config-manager.png)

El mensaje de notificación en la bandeja del sistema tiene el siguiente aspecto:

![Mensaje de la bandeja del sistema](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-tray-message.png)

Verá el estado de la operación de actualización (manual o automática) en la bandeja del sistema. La próxima vez que abra el Administrador de configuración de Data Management Gateway verá un mensaje en la barra de notificación que indica que se ha actualizado la puerta de enlace, junto con un vínculo al [tema con las novedades](data-factory-gateway-release-notes.md).

La pestaña Actualizar del Administrador de configuración de Data Management Gateway muestra la programación de actualización, así como la última vez que se instaló o actualizó la puerta de enlace. Si la actualización automática está deshabilitada, se muestra un mensaje al respecto, pero no podrá habilitar la característica en la pestaña, sino que tendrá que usar para ello el cmdlet.
  

## Iconos y notificaciones de la bandeja del sistema
En la siguiente imagen, se muestran algunos de los iconos de la bandeja que verá.

![Iconos de la bandeja del sistema](./media/data-factory-move-data-between-onprem-and-cloud/gateway-tray-icons.png)

Si mueve el cursor sobre el mensaje de notificación o el icono en la bandeja del sistema, verá detalles sobre el estado de la puerta de enlace o de la operación de actualización en una ventana emergente.

## Para habilitar o deshabilitar la característica de actualización automática
Se puede habilitar o deshabilitar la característica de actualización automática de la forma siguiente:

1. Inicie Windows PowerShell en el equipo de la puerta de enlace. 
2. Cambie a la carpeta C:\\Archivos de programa\\Microsoft Data Management Gateway\\1.0\\PowerShellScript.
3. Ejecute el siguiente comando para desactivar (deshabilitar) la característica de actualización automática.   

		.\GatewayAutoUpdateToggle.ps1  -off

4. Para volver a activarla:
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Consideraciones de puertos y seguridad
Existen dos firewalls que debe tener en cuenta: el **firewall corporativo** que se ejecuta en el enrutador central de la organización y **Firewall de Windows**, configurado como demonio en el equipo local donde está instalada la puerta de enlace.

![Firewalls](./media/data-factory-move-data-between-onprem-and-cloud/firewalls.png)

### Conexión de la puerta de enlace con servicios en la nube
Para mantener la conectividad de la puerta de enlace con Factoría de datos de Azure y otros servicios en la nube, debe asegurarse de que la regla de salida para los puertos **TCP** **80** y **443** esté configurada. Además, cuenta con la opción de habilitar los puertos del **9350** al **9354**, usados por el Bus de servicio de Microsoft Azure para establecer una conexión entre Factoría de datos de Azure y Data Management Gateway. Esto puede mejorar el rendimiento de la comunicación entre ellos.

En el firewall corporativo, debe configurar los siguientes dominios y puertos de salida:

| Nombres de dominio | Puertos | Descripción |
| ------ | --------- | ------------ |
| **.servicebus.windows.net | 443, 80 | Escuchas en Retransmisión de bus de servicio a través de TCP (requiere 443 para adquirir el token de Control de acceso) |
| *.servicebus.windows.net | 9350-9354 | Retransmisión de bus de servicio opcional a través de TCP |
| *.core.windows.net | 443 | HTTPS |
| *.clouddatahub.net | 443 | HTTPS |
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS |

En el nivel de Firewall de Windows, normalmente se habilitan estos puertos de salida. De lo contrario, puede configurar los puertos y dominios según corresponda en el equipo de la puerta de enlace.

### Configuración de credenciales
Se usará el puerto de entrada **8050** en la aplicación **Configuración de credenciales** para retransmitir las credenciales a la puerta de enlace cuando configure un servicio vinculado local en el Portal de Azure (detalles más adelante en este artículo). Durante la instalación de la puerta de enlace, la instalación de Data Management Gateway lo abre de forma predeterminada en el equipo de la puerta de enlace.
 
Si se usa un firewall de terceros, puede abrir manualmente el puerto 8050. Si se presenta un problema de firewall durante la instalación de la puerta de enlace, puede intentar usar el comando siguiente para instalarla sin configurar el firewall.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Si decide no abrir el puerto 8050 en el equipo de la puerta de enlace, para configurar un servicio vinculado local, debe usar mecanismos diferentes de la aplicación **Configuración de credenciales** para configurar las credenciales del almacén de datos. Por ejemplo, puede usar el cmdlet de PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Consulte en la sección [Configuración de credenciales y seguridad](#setting-credentials-and-security) cómo configurar las credenciales del almacén de datos.

**Para copiar datos de un almacén de datos de origen a un almacén de datos receptor:**

Debe asegurarse de que las reglas de firewall se habilitan correctamente en el firewall corporativo, en el Firewall de Windows en la máquina de puerta de enlace y en el mismo almacén de datos. Esto permite que la puerta de enlace se conecte tanto al origen como al receptor correctamente. Debe habilitar las reglas de cada almacén de datos que está implicado en la operación de copia.

Por ejemplo, para copiar de **un almacén de datos local a un receptor de Base de datos SQL de Azure o un receptor de Almacenamiento de datos SQL de Azure**, necesita permitir la comunicación **TCP** saliente en el puerto **1433** tanto para el Firewall de Windows como para el firewall corporativo, y debe establecer la configuración del firewall del servidor SQL de Azure para agregar la dirección IP del equipo de la puerta de enlace a la lista de direcciones IP permitidas.

### Consideraciones acerca del servidor proxy
De forma predeterminada, Data Management Gateway aprovechará la configuración de proxy de Internet Explorer y usará credenciales predeterminadas para tener acceso a él. Si no se adapta a sus necesidades, puede establecer la **configuración del servidor proxy**, tal como se muestra a continuación, para asegurarse de que la puerta de enlace se pueda conectar a Factoría de datos de Azure:

1.	Después de instalar Data Management Gateway, en Explorador de archivos, haga una copia de “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config” para hacer una copia de seguridad del archivo original.
2.	Inicie Notepad.exe como administrador y abra el archivo de texto “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”. Encontrará la etiqueta predeterminada para system.net de la forma siguiente:

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	Después, puede agregar los detalles del servidor proxy, por ejemplo, la dirección de proxy dentro de esa etiqueta primaria, por ejemplo:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	Se permiten propiedades adicionales dentro de la etiqueta proxy para especificar la configuración requerida, como scriptLocation. Consulte [<proxy> (Elemento, Configuración de red)](https://msdn.microsoft.com/library/sa91de1e.aspx) para ver la sintaxis.

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Guarde el archivo de configuración en la ubicación original; a continuación, reinicie el servicio Data Management Gateway para aplicar los cambios. Para hacerlo, vaya a **Inicio** > **Services.msc** o, en el **Administrador de configuración de Data Management Gateway**, haga clic en el botón **Detener servicio** y finalmente haga clic en **Iniciar servicio**. Si el servicio no se inicia, probablemente sea debido a que se ha agregado una sintaxis de etiqueta XML incorrecta en el archivo de configuración de aplicación que se ha modificado.

Además de los puntos anteriores, también tiene que asegurarse de que Microsoft Azure se encuentra en la lista blanca de su empresa. Se puede descargar una lista de direcciones IP válidas de Microsoft Azure en el [ Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### Posibles síntomas de problemas relacionados con el firewall y el servidor proxy
Si se producen errores como los siguientes, es probable que sea debido a una configuración incorrecta del servidor proxy o del firewall, que impide que Data Management Gateway se conecte a Factoría de datos de Azure para autenticarse a sí mismo. Consulte la sección anterior para garantizar que el firewall y el servidor proxy están configurados correctamente.

1.	Al intentar registrar la puerta de enlace, recibirá el siguiente error: "Error al registrar la clave de la puerta de enlace. Antes de volver a intentar registrar la clave de la puerta de enlace, confirme que Data Management Gateway está en estado conectado y el servicio host de Data Management Gateway se ha iniciado."
2.	Al abrir el Administrador de configuración, verá el estado como "Desconectado" o "Conectando". Al ver los registros de eventos de Windows, bajo "Visor de eventos" > "Registros de aplicaciones y servicios" > "Data Management Gateway" aparecen mensajes de error como " No es posible conectar con el servidor remoto " o "Un componente de Data Management Gateway ha dejado de responder y se reiniciará automáticamente. Nombre del componente: puerta de enlace."

## Solución de problemas de puerta de enlace


- Puede obtener más información en los registros de la puerta de enlace de los registros de eventos de Windows. Puede encontrarlos mediante el **Visor de eventos** de Windows en **Registros de aplicaciones y servicios** > **Data Management Gateway**. Cuando soluciones problemas relacionados con la puerta de enlace consulte los eventos de error en el Visor de eventos.
- Si la puerta de enlace deja de funcionar después de **haber cambiado el certificado**, reinicie (detenga e inicie) el **Servicio Data Management Gateway** mediante la herramienta Administrador de configuración de Microsoft Data Management Gateway o el applet del panel de control Servicios. Si todavía ve un error, puede que tenga que dar permisos explícitos para el usuario del servicio de Data Management Gateway para que tenga acceso al certificado en el Administrador de certificados (certmgr.msc). La cuenta de usuario predeterminada para el servicio es: **NT Service\\DIAHostService**. 
- Si ve errores relacionados con el controlador o la conexión del almacén de datos, inicie **Administrador de configuración de Data Management Gateway** en el equipo de la puerta de enlace, cambie a la pestaña **Diagnóstico**, seleccione o especifique los valores adecuados en los campos del grupo **Probar la conexión a un origen de datos local con esta puerta de enlace** y haga clic en **Probar conexión** para ver si se puede conectar al origen de datos local desde el equipo de la puerta de enlace con la información de conexión y las credenciales. Si la conexión de prueba sigue sin funcionar después de instalar un controlador, reinicie la puerta de enlace para recoger los cambios más recientes.  

	![Probar conexión](./media/data-factory-move-data-between-onprem-and-cloud/TestConnection.png)
		
## Tutorial: Uso de Data Management Gateway 
En este tutorial se crea una factoría de datos con una canalización que mueve los datos de una base de datos de SQL Server local a un blob de Azure.

### Paso 1: Creación de una factoría de datos de Azure
En este paso, use el Portal de Azure para crear una instancia de Factoría de datos de Azure denominada **ADFTutorialOnPremDF**. También puede crear una factoría de datos mediante cmdlets de la Factoría de datos de Azure.

1.	Tras iniciar sesión en el [Portal de Azure](https://portal.azure.com), haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **Análisis de datos** en la hoja **Crear** y haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

	![New->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. En la hoja **Nueva factoría de datos**:
	1. Escriba **ADFTutorialOnPremDF** en el **nombre**.
	2. Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y seleccione **ADFTutorialResourceGroup**. Puede seleccionar un grupo de recursos existente o crear uno nuevo. Para crear un nuevo grupo de recursos:
		1. Haga clic en **Crear un nuevo grupo de recursos**.
		2. En la hoja **Crear grupo de recursos**, escriba un **nombre** para el grupo de recursos y haga clic en **Aceptar**.

7. Observe que está seleccionada la opción **Agregar al Panel de inicio** en la hoja **Nueva factoría de datos**.

	![Agregar al Panel de inicio](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. En la hoja **Nueva factoría de datos**, haga clic en **Crear**.

	El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre de la factoría de datos "ADFTutorialOnPremDF" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearla de nuevo. Use este nombre en lugar de ADFTutorialFactory al realizar los restantes pasos de este tutorial.

9. Para buscar las notificaciones desde el proceso de creación, haga clic en el botón **Notificaciones** que se encuentra en la barra de título, como se muestra en la siguiente imagen. Haga clic en él de nuevo para cerrar la ventana de notificaciones.

	![Centro de NOTIFICACIONES](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Una vez completada la creación, verá la hoja **Factoría de datos** como se muestra a continuación:

	![Página principal de Factoría de datos](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### Paso 2: Creación de una instancia de Data Management Gateway
5. En la hoja **FACTORÍA DE DATOS**, haga clic en la ventana **Crear e implementar** para iniciar el **Editor** para la factoría de datos.

	![Mosaico Crear e implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	En el Editor de la Factoría de datos, haga clic en **... (puntos suspensivos)** en la barra de herramientas y luego haga clic en **Nueva puerta de enlace de datos**. 

	![Nueva puerta de enlace de datos en la barra de herramientas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. En la hoja **Crear**, escriba **adftutorialgateway** para el **nombre** y haga clic en **Aceptar**. 	

	![Hoja Crear puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. En la hoja **Configurar**, haga clic en **Instalar directamente en este equipo**. Esto descarga el paquete de instalación de la puerta de enlace, instala, configura y registra la puerta de enlace en el equipo.

	> [AZURE.NOTE] 
	Utilice Internet Explorer o un explorador web compatible con Microsoft ClickOnce.
	> 
	> Si usa Chrome, vaya a la [Chrome Web Store](https://chrome.google.com/webstore/), busque la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instálela.
	>  
	> Debe hacer lo mismo para Firefox (complemento de instalación). Haga clic en el botón **Abrir menú** de la barra de herramientas (**tres líneas horizontales** en la esquina superior derecha), haga clic en **Complementos**, busque la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instálela.

	![Puerta de enlace: hoja Configurar](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Esta es la forma más sencilla (un clic) para descargar, instalar, configurar y registrar la puerta de enlace en un solo paso. Puede ver que la aplicación **Administrador de configuración de Microsoft Data Management Gateway** está instalada en el equipo. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	También puede descargar e instalar la puerta de enlace manualmente con los vínculos de esta hoja y registrarla usando la clave que se muestra en el cuadro de texto **REGISTRAR CON LA CLAVE**.
	
	Consulte las secciones del principio de este artículo para obtener información detallada de la puerta de enlace, incluidos procedimientos recomendados y algunas consideraciones importantes.

	>[AZURE.NOTE] Debe ser administrador del equipo local para instalar y configurar correctamente Data Management Gateway. Puede agregar usuarios adicionales al grupo local de Windows Usuarios de usuarios de Data Management Gateway. Los miembros de este grupo podrán usar la herramienta Administrador de configuración de Data Management Gateway para configurar la puerta de enlace.

5. Espere un par de minutos e inicie la aplicación **Administración de configuración de Data Management Gateway** en el equipo. En la ventana **Búsqueda**, escriba **Data Management Gateway** para tener acceso a esta utilidad. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	![Administrador de configuración de puertas de enlace](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Espere hasta que los valores se hayan establecido como se indica a continuación:
	1. El **Estado** está establecido en **Iniciado**.
	2. **Nombre de la puerta de enlace** está establecido en **adftutorialgateway**.
	3. **Nombre de instancia** está establecido en **adftutorialgateway**.
	4. **Registro** está establecido en **Registrado**.
	5. La barra de estado de la parte inferior muestra **Conectado al servicio en la nube Data Management Gateway** junto con una **marca de verificación verde**.

8. Cambie a la pestaña **Certificados**. El certificado especificado en esta pestaña se usa para cifrar y descifrar las credenciales para el almacén de datos local que especifique en el portal. Haga clic en **Cambiar** para usar su propio certificado. De forma predeterminada, la puerta de enlace usa el certificado generado automáticamente por el servicio Factoría de datos.

	![Configuración del certificado de puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. (Opcional) Cambie a la pestaña **Diagnósticos**, active la opción **Habilitar el registro detallado para solucionar problemas** si desea habilitar el registro detallado que puede usar para solucionar los problemas de la puerta de enlace. La información de registro se encuentra en el **Visor de sucesos**, en el nodo **Registros de aplicaciones y servicios** -> **Data Management Gateway**. 

	![Ficha Diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	También puede usar esta página para **probar la conexión** a un origen de datos local mediante la puerta de enlace.
10. En el Portal de Azure, haga clic en **Aceptar** en la hoja **Configurar** y, después, en la hoja **Nueva puerta de enlace de datos**.
6. Debería ver **adftutorialgateway** en **Puertas de enlace de datos** en la vista de árbol de la izquierda. Si hace clic en ella, debería ver el JSON asociado. 
	

### Paso 3: Crear servicios vinculados 
En este paso, creará dos servicios vinculados: **StorageLinkedService** y **SqlServerLinkedService**. El servicio **SqlServerLinkedService** vincula una base de datos de SQL Server local y el servicio vinculado **StorageLinkedService** vincula un almacén de blobs de Azure a la factoría de datos. Más adelante en este tutorial creará una canalización que copia datos de la base de datos de SQL Server local al almacén de blobs de Azure.

#### Adición de un servicio vinculado a una base de datos de SQL Server local
1.	En el **Editor de la Factoría de datos**, haga clic en **Nuevo almacén de datos** en la barra de herramientas y seleccione **SQL Server**. 

	![Nuevo servicio vinculado de SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	En el **Editor de JSON**, haga lo siguiente: 
	1. Para el **gatewayName**, especifique **adftutorialgateway**.	
	2. Si está usando la autenticación de Windows:
		1. En **connectionString**: 
			1. Establezca **Seguridad integrada** en **true**.
			2. Especifique el **nombre de servidor** de la base de datos y el **nombre de la base de datos**. 
			2. Quite **Id. de usuario** y **Contraseña**. 
		3. Especifique el nombre de usuario y la contraseña en las propiedades **userName** y **password**.
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication>",
            		"password": "<Specify password for the user account>"
        		}

	4. Si está usando la autenticación de SQL:
		1. Especifique el **nombre del servidor** de la base de datos, el **nombre de la base de datos**, el **Id. de usuario** y la **Contraseña** en **connectionString**.       
		2. Quite las últimas dos propiedades JSON (**userName** y **password**) de JSON.
		3. Quite la **, (coma)** al final de la línea que especifica el valor de la propiedad **gatewayName**. 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	   
2.	Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado de SQL Server.

#### Adición de un servicio vinculado para una cuenta de almacenamiento de Azure
 
1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo almacén de datos** en la barra de comandos y haga clic en **Almacenamiento de Azure**.
2. Escriba el nombre de la cuenta de almacenamiento de Azure en **Nombre de cuenta**.
3. Escriba la clave de su cuenta de almacenamiento de Azure en **Clave de cuenta**.
4. Haga clic en **Implementar** para implementar **StorageLinkedService**.
   
 
### Paso 4: Crear conjuntos de datos de entrada y salida
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => almacenamiento de blobs de Azure). Antes de crear conjuntos de datos o tablas (conjuntos de datos rectangulares), debe hacer lo siguiente (después de la lista, se detallan los pasos):

- Cree una tabla con el nombre **emp** en la base de datos de SQL Server que agregó como servicio vinculado a la factoría de datos e inserte un par de entradas de ejemplo en la tabla.
- Cree un contenedor de blobs llamado **adftutorial** en la cuenta de almacenamiento de blobs de Azure que agregó como un servicio vinculado a la factoría de datos.

### Preparación de la instancia local de SQL Server para el tutorial

1. En la base de datos que especificó para el servicio vinculado de SQL Server local (**SqlServerLinkedService**), use el siguiente script de SQL para crear la tabla **emp** en la base de datos.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Inserte algún ejemplo en la tabla:


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Creación de la tabla de entrada

1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Tabla de SQL Server**. 
2.	Reemplace el script JSON del panel derecho por el texto siguiente:    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	Tenga en cuenta lo siguiente:
	
	- **type** está establecido como **SqlServerTable**.
	- **tableName** está establecido en **emp**.
	- **linkedServiceName** está establecido en **SqlServerLinkedService** (creó este servicio vinculado en el paso 2).
	- Para una tabla de entrada no generada por otra canalización en Factoría de datos de Azure, tiene que especificar la propiedad **external** en **true**. Indica que los datos de entrada se han producido fuera del servicio Factoría de datos de Azure. Opcionalmente, puede especificar las directivas de datos externos mediante el elemento **externalData** en la sección **Policy**.    

	Consulte la [documentación de referencia de scripting con JSON][json-script-reference] para obtener información detallada acerca de las propiedades JSON.

2. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA TABLA SE IMPLEMENTÓ CORRECTAMENTE**.


### Creación de la tabla de salida

1.	En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Almacenamiento de blobs de Azure**.
2.	Reemplace el script JSON del panel derecho por el texto siguiente: 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	Tenga en cuenta lo siguiente:
	
	- **type** está establecido en **AzureBlob**.
	- **linkedServiceName** está establecido en **StorageLinkedService** (creó este servicio vinculado en el paso 2).
	- **folderPath** está establecido en **adftutorial/outfromonpremdf**, donde outfromonpremdf es la carpeta del contenedor adftutorial. Solo tiene que crear el contenedor **adftutorial**.
	- El elemento **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval** en **1**). El servicio Factoría de datos generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure. 

	Si no especifica **fileName** para una **tabla de entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Puede ver algunos archivos de ejemplo en [tutorial][adf-tutorial].
 
	Si no especifica un valor **fileName** para una tabla de salida, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad partitionedBy. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Consulte la [documentación de referencia de scripting con JSON][json-script-reference] para obtener información detallada acerca de las propiedades JSON.

2.	Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA TABLA SE IMPLEMENTÓ CORRECTAMENTE**.
  

### Paso 5: Crear y ejecutar una canalización
En este paso, va a crear una **canalización** con una **actividad de copia** que usa **EmpOnPremSQLTable** como entrada y **OutputBlobTable** como salida.

1.	En la hoja **FACTORÍA DE DATOS**, haga clic en la ventana **Crear e implementar** para iniciar el **Editor** para la factoría de datos.

	![Mosaico Crear e implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.	Haga clic en **Nueva canalización** en la barra de comandos. Si no ve el botón, haga clic en **... (puntos suspensivos)** para expandir la barra de comandos.
2.	Reemplace el script JSON del panel derecho por el texto siguiente:   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	Tenga en cuenta lo siguiente:
 
	- En la sección de actividades, solo hay una actividad cuyo **type** está establecido en **Copy**.
	- La **entrada** de la actividad está establecida en **EmpOnPremSQLTable** y la **salida** de la actividad está establecida en **OutputBlobTable**.
	- En la sección **transformation**, **SqlSource** está especificado como **tipo de origen** y **BlobSink** está especificado como **tipo de receptor**.
- La consulta SQL **select * from emp** está especificada para la propiedad **sqlReaderQuery** de **SqlSource**.

	Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **end** es opcional, pero se utilizará en este tutorial.
	
	Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de la propiedad **end**.
	
	Está definiendo la duración del procesamiento de los segmentos de datos según las propiedades de **disponibilidad** que se definieron para cada tabla de la Factoría de datos de Azure.
	
	En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
2. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA CANALIZACIÓN SE IMPLEMENTÓ CORRECTAMENTE**.
5. Ahora, cierre la hoja **Editor** haciendo clic en **X**. Vuelva a hacer clic en **X** para cerrar la hoja ADFTutorialDataFactory con la barra de herramientas y la vista de árbol. Si ve el mensaje que indica que **se descartarán las modificaciones no guardadas**, haga clic en **Aceptar**.
6. Debe volver a la hoja **FACTORÍA DE DATOS** para **ADFTutorialDataFactory**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.

#### Visualización de la factoría de datos en una vista de diagrama 
1. En el **Portal de Azure**, haga clic en el icono **Diagrama** en la página principal de Factoría de datos **ADFTutorialOnPremDF**.

	![Vínculo de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Debería ver un diagrama similar al siguiente:

	![Vista de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	Puede acercar, alejar, acercar al 100%, ampliar para ajustar, colocar automáticamente canalizaciones y tablas, y mostrar información de linaje (resalta elementos ascendentes y descendentes de los elementos seleccionados). Puede hacer doble clic en un objeto (tabla o canalización de entrada o salida) para ver sus propiedades.

### Paso 6: Supervisar los conjuntos de datos y las canalizaciones
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener más información sobre la supervisión, consulte [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md).

1. Vaya al **Portal de Azure** (si lo ha cerrado).
2. Si la hoja de **ADFTutorialOnPremDF** no está abierta, ábrala haciendo clic en **ADFTutorialOnPremDF** en el **Panel de inicio**.
3. Deben mostrarse el **recuento** y los **nombres** de las tablas y la canalización que creó en esta hoja.

	![Página principal de Factoría de datos](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Ahora haga clic en el icono **Conjuntos de datos**.
5. En la hoja **Conjuntos de datos**, haga clic en **EmpOnPremSQLTable**.

	![Segmentos EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Tenga en cuenta que ya se han producido los segmentos de datos hasta el momento actual y están **listos**. Esto ocurre porque ha insertado los datos en la base de datos de SQL Server y están ahí todo el tiempo. Confirme que no aparecen segmentos en la sección de **segmentos problemáticos** de la parte inferior.


	Las listas **Segmentos actualizados recientemente** y **Segmentos erróneos recientes** se ordenan por la **HORA DE LA ÚLTIMA ACTUALIZACIÓN**. En las situaciones siguientes, se cambia la hora de actualización de un segmento.
    

	-  El estado del segmento se actualiza manualmente, por ejemplo, con **Set-AzureRmDataFactorySliceStatus** (o) al hacer clic en **EJECUTAR** en la hoja **SEGMENTO** para el segmento.
	-  El segmento cambia de estado debido a una ejecución (por ejemplo, una ejecución se inició, una ejecución finalizó y produjo un error, una ejecución finalizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** para ver la lista más amplia de segmentos. Haga clic en **Filtro** en la barra de herramientas para filtrar los segmentos.
	
	Para ver los intervalos de datos ordenados por las horas de inicio y finalización, haga clic en el icono **Segmentos de datos (por hora del segmento)**.

7. A continuación, en la hoja **Conjuntos de datos**, haga clic en **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Confirme que se han producido los segmentos hasta el momento actual y que están **listos**. Espere hasta que los estados de los segmentos hasta el momento actual estén establecidos en **Listo**.
9. Haga clic en cualquier segmento de datos de la lista. Debe aparecer la hoja **SEGMENTO DE DATOS**.

	![Hoja Segmento de datos](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.

10. Haga clic en la **ejecución de la actividad** de la lista en la parte inferior para ver los **detalles de la ejecución de la actividad**.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Haga clic en **X** para cerrar todas las hojas hasta que
12. regrese a la hoja principal de **ADFTutorialOnPremDF**.
14. (Opcional) Haga clic en **Canalizaciones**, elija **ADFTutorialOnPremDF** y obtenga detalles de las tablas de entrada (**Consumido**) o las tablas de salida (**Producido**).
15. Use herramientas como el **Explorador de almacenamiento de Azure** para comprobar la salida.

	![Explorador de almacenamiento de Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## Movimiento de la puerta de enlace de un equipo a otro
En esta sección se proporcionan pasos para mover el cliente de puerta de enlace de un equipo a otro equipo.

2. En el portal, vaya a la **página principal de Factoría de datos** y haga clic en el icono **Servicios vinculados**. 

	![Vínculo de puertas de enlace de datos](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. Seleccione la puerta de enlace en la sección **PUERTAS DE ENLACE DE DATOS** de la hoja **Servicios vinculados**.
	
	![Hoja Servicios vinculados con puerta de enlace seleccionada](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. En la hoja **Puerta de enlace de datos**, haga clic en **Descargar e instalar la puerta de enlace de datos**.
	
	![Vínculo de puerta de enlace de descarga](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. En la hoja **Configurar**, haga clic en **Descargar e instalar la puerta de enlace de datos** y siga las instrucciones para instalar la puerta de enlace de datos en la máquina. 

	![Hoja Configurar](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. Mantenga el **Administrador de configuración de Microsoft Data Management Gateway** abierto. 
 
	![Administrador de configuración](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. En la hoja **Configurar** del portal, haga clic en **Volver a crear clave** en la barra de comandos y haga clic en **Sí** para el mensaje de advertencia. Haga clic en el **botón Copiar** junto al texto de la clave para copiar la clave en el portapapeles. Tenga en cuenta que la puerta de enlace en la máquina antigua dejará de funcionar tan pronto como vuelva a crear la clave.  
	
	![Volver a crear clave](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. Pegue la **clave** en el cuadro de texto en la página **Registrar puerta de enlace** del **Administrador de configuración de Data Management Gateway** en la máquina. (Opcional) Haga clic en la casilla **Mostrar clave de puerta de enlace** para ver el texto de la clave.
 
	![Copiar clave y registrar](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. Haga clic en **Registrar** para registrar la puerta de enlace con el servicio en la nube.
10. En la página **Especificar certificado**, haga clic en **Examinar** para seleccionar el mismo certificado que se usó con la puerta de enlace anterior, escriba la **contraseña** y haga clic en **Finalizar**. 
 
	![Especificar certificado](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	Puede exportar un certificado desde la puerta de enlace anterior haciendo lo siguiente: inicie el Administrador de configuración de Data Management Gateway en la máquina antigua, pase a la pestaña **Certificado**, haga clic en el botón **Exportar** y siga las instrucciones. 
10. Después del registro correcto de la puerta de enlace, debe ver **Registro** establecido en **Registrado** y **Estado** establecido en **Iniciado** en la página principal del administrador de configuración de puertas de enlace. 

## Configuración de credenciales y seguridad

También puede crear un servicio vinculado de SQL Server mediante la hoja de Servicios vinculados en lugar de usar el editor de la factoría de datos.
 
3.	En la página de inicio de Factoría de datos, haga clic en el icono **Servicios vinculados**. 
4.	En la hoja **Servicios vinculados**, haga clic en **Nuevo almacén de datos** en la barra de comandos. 
4.	Escriba **SqlServerLinkedService** para el **nombre**. 
2.	Haga clic en flecha junto a **Tipo** y seleccione **SQL Server**.

	![Creación de un nuevo almacén de datos](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.	Debe definir más configuraciones en la opción **Tipo**.
4.	Para el valor **Puerta de enlace de datos**, seleccione la puerta de enlace que acaba de crear. 

	![Configuración de SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.	Escriba el nombre del servidor de base de datos en la opción **Servidor**.
5.	Escriba el nombre de la base de datos para el valor **Base de datos**.
6.	Haga clic en flecha junto a **Credenciales**.

	![Hoja Credenciales](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.	En la hoja **Credenciales**, haga clic en la opción **Haga clic aquí para establecer las credenciales**.
8.	En el cuadro de diálogo **Establecer credenciales**, realice lo siguiente:

	![Cuadro de diálogo Establecer credenciales](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png) 
	1.	Seleccione la **autenticación** que desea que use el servicio Factoría de datos para conectarse a la base de datos. 
	2.	Escriba el nombre del usuario que tiene acceso a la base de datos para el valor **NOMBRE DE USUARIO**. 
	3.	Escriba la contraseña para el usuario para el valor **CONTRASEÑA**. 
	4.	Haga clic en **Aceptar** para cerrar el cuadro de diálogo.  
4. Haga clic en **Aceptar** para cerrar la hoja **Credenciales**. 
5. Haga clic en **Aceptar** en la hoja **Nuevo almacén de datos**. 	
6. Confirme que el estado de **SqlServerLinkedService** está establecido en En línea en la hoja Servicios vinculados.
	![Estado del servicio vinculado de SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

Si tiene acceso al portal desde un equipo diferente del equipo de la puerta de enlace, debe asegurarse de que la aplicación del Administrador de credenciales puede conectarse al equipo de la puerta de enlace. Si la aplicación no puede ponerse en contacto con el equipo de la puerta de enlace, no podrá establecer las credenciales del origen de datos ni probar la conexión al origen de datos.

Cuando usa la aplicación "Establecer credenciales" iniciada desde el Portal de Azure para establecer credenciales para un origen de datos local, el portal cifra las credenciales con el certificado especificado en la pestaña Certificado del Administrador de configuración de Data Management Gateway en el equipo de puerta de enlace.

Si desea un enfoque basado en API para cifrar las credenciales, puede usar el cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) de PowerShell para cifrar las credenciales. El cmdlet usa el certificado cuyo uso tiene configurado esa puerta de enlace para cifrar las credenciales. Puede usar las credenciales cifradas devueltas por este cmdlet y agregarlas al elemento EncryptedCredential de connectionString en el archivo JSON que se va a emplear con el cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) o en el fragmento de código JSON en el Editor de la Factoría de datos en el portal.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**Nota:** si usa la aplicación "Establecer credenciales", esta establece automáticamente las credenciales cifradas en el servicio vinculado, como se mostró anteriormente.

Hay otro enfoque para establecer las credenciales mediante el Editor de la Factoría de datos. Si crea un servicio vinculado de SQL Server mediante el editor y escribe las credenciales en texto sin formato, las credenciales se cifran mediante un certificado que posee el servicio Factoría de datos, NO el certificado que la puerta de enlace está configurada para usar. Aunque este enfoque puede resultar un poco más rápido, en algunos casos es menos seguro. Por lo tanto, se recomienda seguir este enfoque solo para fines de desarrollo y pruebas.


## Creación y registro de Data Management Gateway mediante Azure PowerShell 
En esta sección se explica cómo crear y registrar una puerta de enlace usando cmdlets de Azure PowerShell.

1. Inicie **Azure PowerShell** en modo de administrador. 
2. Los cmdlets de Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**. Ejecute el siguiente comando para cambiar al modo **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Use el cmdlet **New-AzureRmDataFactoryGateway** para crear una puerta de enlace lógica como se indica a continuación:

		New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Ejemplo de comando y salida**:


		PS C:\> New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. Use el cmdlet **New-AzureRmDataFactoryGatewayKey** para generar una clave de registro para la puerta de enlace recién creada y almacene la clave en una variable local **$Key**:

		New-AzureRmDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Ejemplo de comando y salida:**


		PS C:\> $Key = New-AzureRmDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. En Azure PowerShell, cambie a la carpeta **C:\\\Archivos de programa\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** y ejecute el script **RegisterGateway.ps1** asociado a la variable local **$Key** como se muestra en el siguiente comando para registrar el agente cliente instalado en su máquina con la puerta de enlace lógica que creó antes.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Use el cmdlet **Get-AzureRmDataFactoryGateway** para obtener la lista de puertas de enlace de su factoría de datos. Cuando el **Estado** es **En línea**, significa que la puerta de enlace está lista para usarla.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Puede quitar una puerta de enlace con el cmdlet **Remove-AzureRmDataFactoryGateway** y actualizar la descripción de una puerta de enlace con los cmdlets **Set-AzureRmDataFactoryGateway**. Para ver la sintaxis y otros detalles de estos cmdlets, consulte la documentación de referencia de los cmdlets de Factoría de datos.


## Flujo de datos de copia mediante Data Management Gateway
Al usar una actividad de copia en una canalización de datos para introducir datos locales en la nube para su posterior procesamiento, o bien exportar los datos de resultados en la nube de nuevo a un almacén de datos local, la actividad de copia usa internamente una puerta de enlace para transferir los datos de origen de un origen de datos local a la nube y viceversa.

A continuación se muestra el flujo de datos de alto nivel y el resumen de los pasos para copiar con una puerta de enlace de datos:
![Flujo de datos mediante la puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	El desarrollador de datos crea una nueva puerta de enlace para una Factoría de datos de Azure mediante el [Portal de Azure](https://portal.azure.com) o un [cmdlet de PowerShell](https://msdn.microsoft.com/library/dn820234.aspx). 
2.	El desarrollador de datos usa el panel "Servicios vinculado" para definir un nuevo servicio vinculado para un almacén de datos local con la puerta de enlace. Como parte de la configuración de los datos de servicios vinculados el desarrollador usa la aplicación Establecer credenciales, como se muestra en el tutorial paso a paso para especificar las credenciales y los tipos de autenticación. El cuadro de diálogo de la aplicación Establecer credenciales se comunicará con el almacén de datos para probar la conexión y la puerta de enlace para guardar las credenciales.
3.	La puerta de enlace cifrará las credenciales con el certificado asociado a la puerta de enlace (suministrado por el desarrollador de datos), antes de guardar las credenciales en la nube.
4.	El servicio de movimiento de la factoría de datos se comunica con la puerta de enlace para la programación y administración de trabajos a través de un canal de control que usa una cola de bus compartida del servicio de Azure. Cuando es necesario un trabajo de actividad de copia, la factoría de datos pone en cola la solicitud junto con la información de credenciales. La puerta de enlace inicia el trabajo después de sondear la cola.
5.	La puerta de enlace descifra las credenciales con el mismo certificado y, a continuación, se conecta al almacén de datos local con el tipo de autenticación adecuado.
6.	La puerta de enlace copia datos desde el almacén local a un almacenamiento en la nube o desde un almacenamiento en la nube a un almacén de datos local según cómo esté configurada la actividad de copia en la canalización de datos. Nota: Para este paso, la puerta de enlace se comunica directamente con el servicio de almacenamiento basado en la nube (Blob de Azure, SQL de Azure, etc.) a través del canal seguro (HTTPS).

<!---HONumber=AcomDC_0204_2016-->