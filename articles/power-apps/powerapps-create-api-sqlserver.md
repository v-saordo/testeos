<properties
	pageTitle="Agregar la API de SQL Server a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de SQL Server en el entorno del Servicio de aplicaciones de la organización y agregar una conexión a datos localmente"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>


<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# Crear una nueva API de SQL Server en el entorno del Servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa. 
2. Seleccione **Examinar** en la barra de tareas: ![][14]  
3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][15]  
4. En **PowerApps**, seleccione **Administrar APIs**.
5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API.
6. Escriba un **nombre** descriptivo para la API. Por ejemplo, si va a agregar la API de SQL Server para la demostración, puede asignarle el nombre *SQLServerDemo*.  	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **SQL Server**. 
8. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de SQL Server en el entorno de Servicio de aplicaciones.

## Configurar la conectividad con SQL Server localmente

Puede conectarse a SQL Server localmente. Para establecer esta conectividad híbrida, puede aprovechar las soluciones de redes híbridas existentes de Azure, entre las que se incluyen:

- [ExpressRoute](../expressroute-introduction.md)
- [VPN de sitio a sitio](../vpn-gateway-create-site-to-site-rm-powershell.md)
- [Conectividad de punto a sitio](../vpn-gateway-point-to-site-create.md)  

	> [AZURE.NOTE]Cada entorno del Servicio de aplicaciones tiene una red virtual asociada. Puede establecer esta conectividad de red en esta red virtual.  
- [Hybrid connections](../web-sites-hybrid-connection-get-started.md)  

	> [AZURE.NOTE]Todas las API registradas en el entorno del Servicio de aplicaciones tienen una aplicación web correspondiente. Puede establecer conexiones híbridas desde esta aplicación web igual que desde cualquier otra aplicación web.
	
En el ejemplo siguiente se muestra cómo crear una conexión híbrida:

1. Seleccione la API de SQL Server que acaba de crear y seleccione el grupo de recursos. En este ejemplo, seleccione la API denominada *sqlconnectordemo* y seleccione el grupo de recursos *DedicatedAses*: ![Grupos de recursos](./media/powerapps-create-api-sqlserver/sqlapi.png)

2.  Seleccione el icono **Recursos** y, a continuación, seleccione la aplicación web con el mismo nombre que la API de SQL Server. En este ejemplo, seleccione *sqlconnectordemo*: ![Aplicación web Sql](./media/powerapps-create-api-sqlserver/sqlwebapp.png)

3.  En **Configuración**, seleccione **Redes**. Seleccione **Configurar los puntos de conexión híbrida** y, a continuación, siga [estas instrucciones](../web-sites-hybrid-connection-get-started.md) para crear la conexión híbrida: ![Redes](./media/powerapps-create-api-sqlserver/network.png)

Una vez creada la conexión híbrida y conectada, se habrá habilitado la conexión al servidor local. A continuación, cree la conexión a los datos y conceda acceso a los usuarios: ![Conexión híbrida](./media/powerapps-create-api-sqlserver/hybridconn.png)

## Crear conexión para la API de SQL Server

1. En el Portal de Azure, abra PowerApps y seleccione **Administrar API**. Se mostrará una lista de las API configuradas: ![](./media/powerapps-create-api-sqlserver/apilist.png)

2. Seleccione la API que desee. En este ejemplo, seleccione **SQLServerDemo** y **Conexiones**.

3. En Conexiones, seleccione **Agregar conexión**: ![](./media/powerapps-create-api-sqlserver/addconnection.png)

4. Escriba un nombre para la conexión e introduzca la cadena de conexión. Para escribir la cadena de conexión se requiere conocer algunas propiedades específicas acerca del servicio al que va a conectar. Por ejemplo, si se conecta a un SQL Server local, debe conocer el nombre de usuario, la contraseña y otras propiedades necesarias para realizar correctamente la conexión.

5. Seleccione **Agregar** para guardar los cambios.

## Resumen y pasos siguientes
En este tema, ha agregado la API de SQL Server para conectarse a SQL Server localmente. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)


[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->