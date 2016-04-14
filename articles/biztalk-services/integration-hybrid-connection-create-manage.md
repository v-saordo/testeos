<properties 
	pageTitle="Creación y administración de conexiones híbridas| Microsoft Azure" 
	description="Obtenga información acerca de cómo crear una conexión híbrida, administrar la conexión e instalar el administrador de conexiones híbridas. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="mandia"/>


# Creación y administración de conexiones híbridas


## Información general de los pasos
1. Cree una conexión híbrida especificando el nombre de host o la dirección IP del recurso local de la red privada.
2. Vincule las Aplicaciones web de Azure o las Aplicaciones móviles de Azure a la conexión híbrida.
3. Instale el Administrador de conexiones híbridas en el recurso local y conéctese a la conexión híbrida específica. El portal de Azure ofrece una experiencia con un solo clic para la instalación y la conexión.
4. Administre las conexiones híbridas y sus claves de conexión.

En este tema se muestran estos pasos.


## <a name="CreateHybridConnection"></a>Creación de una conexión híbrida

Una conexión híbrida se puede crear en el Portal de Azure mediante Aplicaciones web **o** servicios de BizTalk.

**Para crear conexiones híbridas mediante Aplicaciones web**, consulte [Conexión de Aplicaciones web de Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md).

**Para crear conexiones híbridas en servicios de BizTalk**:

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 

	Si no tiene un Servicio de BizTalk existente, puede [Crear un servicio de BizTalk](biztalk-provision-services.md).
3. Seleccione la pestaña **Conexiones híbridas**: ![Pestaña Conexiones híbridas][HybridConnectionTab]

4. Seleccione **Crear una conexión híbrida** o seleccione el botón **AGREGAR** de la barra de tareas. Escriba lo siguiente:

	Propiedad | Descripción
--- | ---
Nombre | El nombre de la conexión híbrida debe ser único y no puede ser el mismo que el servicio de BizTalk. Puede escribir cualquier nombre pero sea concreto con su finalidad. Algunos ejemplos son:<br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer*
Nombre de host | Escriba el nombre de host completo, solo el nombre de host o la dirección IPv4 del recurso local. Entre los ejemplos se incluyen:<br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10
Port | Escriba el número de puerto del recurso local. Por ejemplo, si utiliza Aplicaciones web, escriba los puertos 80 o 443. Si utiliza SQL Server, escriba el puerto 1433.

5. Seleccione la marca de verificación para completar la configuración.

#### Información adicional

- Se pueden crear varias conexiones híbridas. Consulte [Servicios de BizTalk: Gráfico de ediciones](biztalk-editions-feature-chart.md) para ver el número de conexiones permitidas. 
- Todas las conexiones híbridas se crean con un par de cadenas de conexión: las claves de aplicación que ENVÍAN y las claves locales que ESCUCHAN. Cada par tiene una clave principal y una secundaria. 


## <a name="LinkWebSite"></a>Para vincular Aplicaciones web de Azure o Aplicaciones móviles de Azure

Para vincular las Aplicaciones web de Azure a una conexión híbrida existente, seleccione **usar una conexión híbrida existente** en la hoja Conexiones híbridas. Consulte [Conexión de Aplicaciones web de Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md).

Para vincular las Aplicaciones móviles de Azure a una conexión híbrida existente, seleccione **agregar una conexión híbrida** al cambiar o crear un Servicio móvil. Consulte [Servicios móviles de Azure y conexiones híbridas](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md).


## <a name="InstallHCM"></a>Instalación del Administrador de conexiones híbridas en un entorno local

Después de que se crea una conexión híbrida, instale el Administrador de conexiones híbridas en el recurso local. Este se puede descargar desde Aplicaciones web de Azure o desde el servicio de BizTalk. Pasos para los servicios de BizTalk:

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
3. Seleccione la pestaña **Conexiones híbridas**: ![Pestaña Conexiones híbridas][HybridConnectionTab]
4. En la barra de tareas, seleccione **Instalación local**: ![On-Premises Setup][HCOnPremSetup]
5. Seleccione **Instalar y configurar** para ejecutar o descargar el Administrador de conexiones híbridas en el sistema local. 
6. Seleccione la marca de verificación para iniciar la instalación. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### Información adicional
- Las conexiones híbridas admiten recursos locales instalados en los siguientes sistemas operativos:

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Después de instalar el Administrador de conexiones híbridas, tiene lugar lo siguiente:

	- La conexión híbrida hospedada en Azure se configura automáticamente para usar la cadena de conexión de la aplicación principal. 
	- El recurso local se configura automáticamente para usar la cadena de conexión local principal.

- El Administrador de conexiones híbridas debe usar una cadena de conexión local válida para la autorización. Las Aplicaciones web de Azure o las Aplicaciones móviles de Azure deben usar una cadena de conexión de aplicación válida para la autorización.
- Puede escalar las conexiones híbridas mediante la instalación de otra instancia del Administrador de conexiones híbridas en otro servidor. Configure el agente de escucha local para usar la misma dirección como el primer agente de escucha local. En esta situación, el tráfico es distribuido aleatoriamente (round robin) entre los agentes de escucha locales activos. 


## <a name="ManageHybridConnection"></a>Administración de conexiones híbridas
Para administrar las conexiones híbridas, puede:

- Usar el Portal de Azure para ir al servicio de BizTalk. 
- Usar [API de REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### Copia y regeneración de las cadenas de conexión híbridas

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
3. Seleccione la pestaña **Conexiones híbridas**: ![Pestaña Conexiones híbridas][HybridConnectionTab]
4. Seleccione la conexión híbrida. En la barra de tareas, seleccione **Administrar conexión**: ![Administrar opciones][HCManageConnection]

	**Administrar conexión** muestra las cadenas de conexión de aplicación y local. Puede copiar las cadenas de conexión o regenerar la clave de acceso usada en la cadena de conexión.

	**Si selecciona Regenerar**, se cambia la clave de acceso compartido que se usa en la cadena de conexión. Haga lo siguiente:
	- En el Portal de Azure clásico, seleccione **Sincronizar claves** en la aplicación de Azure.
	- Vuelva a ejecutar la **Instalación local**. Al volver a ejecutar la configuración local, el recurso local se configura automáticamente para usar la cadena de conexión principal actualizada.


#### Uso de la directiva de grupo para controlar los recursos locales utilizados por una conexión híbrida

1. Descargue las [plantillas administrativas del Administrador de conexiones híbridas](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraiga los archivos.
3. En el equipo que modifica la directiva de grupo, haga lo siguiente:  

	- Copie los archivos .ADMX en la carpeta *%WINROOT%\\PolicyDefinitions*.
	- Copie los archivos .ADML en la carpeta *%WINROOT%\\PolicyDefinitions\\es-ES*.

Una vez copiados, puede usar el Editor de directivas de grupo para cambiar la directiva.




## Pasos siguientes

[Conexión de Aplicaciones web de Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md) [Conexión a un servidor SQL Server local desde Aplicaciones web de Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) [Servicios móviles de Azure y conexiones híbridas](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md) [Introducción a las conexiones híbridas](integration-hybrid-connection-overview.md)


## Otras referencias

[API de REST para administrar los Servicios de BizTalk en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx) [Servicios de BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md) [Creación de un servicio de BizTalk mediante el Portal de Azure clásico](biztalk-provision-services.md) [Servicios de BizTalk: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=AcomDC_0302_2016-->