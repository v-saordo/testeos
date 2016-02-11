<properties 
	pageTitle="Administración de aplicaciones de API" 
	description="Aprenda a administrar aplicaciones de API del Servicio de aplicaciones de Azure mediante el portal de Azure y el Explorador de servidores de Visual Studio." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Administración de aplicaciones de API en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

En este artículo, se muestra cómo usar el [portal de vista previa de Azure](https://portal.azure.com/) para realizar tareas de supervisión y administración de aplicaciones de API. Estas son algunas de las tareas que puede realizar:

- Configurar la autenticación 
- Activar el escalado automático
- Ver registros
- Ver cuántas solicitudes se realizan y la cantidad de datos que utiliza una aplicación de API
- Crear una copia de seguridad de una aplicación de API y crear alertas
- Configurar la seguridad de acceso basada en funciones

El artículo también muestra cómo realizar algunas tareas de administración en la ventana Explorador de servidores en Visual Studio.

## Hojas de puerta de enlace y aplicaciones de API en el Portal de vista previa de Azure

En el Servicio de aplicaciones de Azure, una aplicación de API es una [aplicación web](../app-service-web/app-service-web-overview.md) que tiene características adicionales para hospedar servicios web. En el Portal de Azure, hay una hoja de **aplicación de API** para administrar las características específicas de la API y una hoja de **host de aplicación de API** para administrar la aplicación web subyacente.

Cada grupo de recursos que contiene al menos una aplicación de API incluye también una *puerta de enlace*. La puerta de enlace actúa como un proxy, controlando la autenticación y otras funciones administrativas para todas las aplicaciones de API en un grupo de recursos. Al igual que una aplicación de API, una puerta de enlace es una aplicación web con funcionalidad adicional, por lo que también hay dos hojas en el portal para administrar la puerta de enlace: una hoja de **puerta de enlace** para funciones específicas de la puerta de enlace y una hoja de **host de puerta de enlace** para administrar la aplicación web subyacente.

### Tareas que solo se pueden realizar en la hoja de la aplicación de API

La hoja de **aplicación de API** se utiliza para las siguientes tareas:

- Configurar el nivel de acceso: haga clic en **Configuración > Configuración de la aplicación**. El valor predeterminado es interno, lo que significa que solo las aplicaciones de API del mismo grupo de recursos pueden llamar a la aplicación de API. Para obtener más información, consulte [Protección de una aplicación de API](app-service-api-dotnet-add-authentication.md).   
- Configurar la directiva de actualización: haga clic en **Configuración > Configuración de la aplicación**. El valor predeterminado es **Activado**. Esto significa que cuando se publica una nueva versión de la aplicación de API en Marketplace, la aplicación de API se actualiza automáticamente a la nueva versión si se trata de un cambio no radical.  
- Configurar la autenticación para llamadas salientes desde la aplicación de API: haga clic en **Configuración > Autenticación**. Si la aplicación de API realiza llamadas a un servicio externo que requiere autenticación, los valores de configuración requeridos se introducen aquí. Por ejemplo, un conector de Dropbox requiere un identificador de cliente y un secreto de cliente para tener acceso al servicio de Dropbox.
- Configurar [RBAC](../role-based-access-control-configure.md): haga clic en **Configuración > Usuarios**. El acceso de usuario que configure aquí determina solo quién puede tener acceso a las características específicas de la aplicación de API. Para configurar RBAC para las características de la aplicación web, utilice la hoja de **host de aplicación de API**. Normalmente deseará mantener sincronizados los valores de RBAC para la aplicación de API y el host de la aplicación de API. Si asigna a alguien acceso a la aplicación de API pero no al host de la aplicación de API, no podrá utilizar las características de la hoja de **aplicación de API** que realmente guardan relación con el host de la aplicación de API.
- Ver definición de API: haga clic en **Definición de API** en la sección **Resumen** para ver una lista de los métodos expuestos por la aplicación de API.
- [Instalación del Administrador de conexiones híbridas](../app-service-logic/app-service-logic-hybrid-connection-manager.md). El Administrador de conexiones híbridas permite conectarse a un sistema local, como SQL Server o SAP. Esta conectividad híbrida usa el Bus de servicio de Azure para conectarse y controlar la seguridad entre los recursos de Azure y los recursos locales.

### Tareas que puede realizar en la hoja de la aplicación de API y en la hoja del host de aplicación de API 

La hoja de **aplicación de API** le permite realizar muchas tareas que pertenecen a la aplicación web subyacente. Por ejemplo, puede realizar las tareas siguientes:

* Detener, iniciar y reiniciar la aplicación web que hospeda la aplicación de API.  
- Seleccione **Errores y solicitudes** para agregar distintas métricas de rendimiento, incluidos códigos de error HTTP conocidos, como los códigos de estado 200, 400 o 500 HTTP.
- Vea los tiempos de respuesta, cuántas solicitudes se realizan a la aplicación de API, así como la cantidad de datos que entra y sale. 
- Cree alertas de correo electrónico si una métrica supera un umbral que elija. 
- Conozca la cantidad de **CPU** que utiliza la aplicación de API, revise la **cuota de uso** actual en MB y conozca el uso de datos máximo en función de su nivel de coste.
- Vea el **gasto estimado** para determinar el coste potencial de ejecutar su aplicación de API.
- Vea los registros de la aplicación y otros registros IIS, incluidos los registros FREB y los del servidor.
- Seleccione **Procesos** para abrir el Explorador de procesos. Esta acción muestra las instancias web y sus propiedades, incluidos el uso de memoria y el número de subprocesos.

Sin embargo, estas tareas también se pueden realizar mediante la hoja del **host de aplicación de API**. Este es el motivo por el cual las dos hojas comparten gran parte de la interfaz de usuario. Por ejemplo, los botones **Detener**, **Iniciar** y **Reiniciar** de la hoja de **aplicación de API** tienen exactamente el mismo efecto que los botones **Detener**, **Iniciar** y **Reiniciar** de la hoja de host de **aplicación de API**. Del mismo modo, la información de supervisión proporcionada en la hoja de **aplicación de API** coincide con la que muestra la hoja de **host de aplicación de API**.

Las únicas funciones proporcionadas en la hoja de **aplicación de API** que no están duplicadas en la hoja de **host de aplicación de API** se enumeran en la sección anterior.

### Tareas que solo se pueden realizar en la hoja de host de aplicación de API

La hoja de **host de aplicación de API** se utiliza para todas las tareas que haría para cualquier aplicación web.

### Tareas que solo se pueden realizar en la hoja de la puerta de enlace

La hoja de **puerta de enlace** se utiliza para las siguientes tareas:

- Configurar el proveedor de autenticación para las llamadas entrantes a aplicaciones de API: haga clic en **Configuración > Identidad**. Si la puerta de enlace debe autenticar a los usuarios antes de permitirles llamar a las aplicaciones de API en el grupo de recursos, los valores de configuración requeridos se introducen aquí. Para obtener más información, consulte [Configuración y prueba de un conector SaaS en el Servicio de aplicaciones de Azure](app-service-api-connnect-your-app-to-saas-connector.md). 
- Configurar [RBAC](../role-based-access-control-configure.md): haga clic en **Configuración > Usuarios**. El acceso de usuario que configure aquí determina solo quién puede tener acceso a las características específicas de la puerta de enlace, no a las características compartidas con todas las aplicaciones web.

### Tareas que puede realizar en la hoja de la puerta de enlace y en la hoja del host de la puerta de enlace 

Las hojas de la puerta de enlace y del host de puerta de enlace comparten la interfaz de usuario del mismo modo que las hojas de la aplicación de API y los hosts de aplicaciones de API.

### Tareas que solo se pueden realizar en la hoja del host de puerta de enlace

La hoja de **host de puerta de enlace** se utiliza para todas las tareas que haría para cualquier aplicación web.

## <a id="navigate"></a>Cómo desplazarse a las hojas de puerta de enlace y la aplicación de API 

Una manera de obtener acceso a la hoja de **aplicación de API** es a través de la característica de exploración del portal de Azure. En la página principal del portal, haga clic en **Examinar > Aplicaciones de API** para ver todas las aplicaciones de API que se pueden administrar.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

### Navegación hasta la hoja de aplicación de API

Al hacer clic en una fila en la hoja de la lista de **aplicaciones de API**, el portal muestra la hoja de **aplicación de API**.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

### Navegación hasta la hoja de host de aplicación de API

Para obtener acceso a la hoja de **host de aplicación de API**, haga clic en **Host de aplicación de API** en la hoja de **aplicación de API**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

### Navegación hasta la hoja de puerta de enlace

Para obtener acceso a la hoja de **puerta de enlace**, haga clic en el vínculo **Puerta de enlace** en la hoja de **aplicación de API**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

### Navegación hasta la hoja de host de puerta de enlace

Para obtener acceso a la hoja de **host de puerta de enlace**, haga clic en el vínculo **Host de puerta de enlace** en la hoja de **puerta de enlace**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Acceso a aplicaciones de API en el Explorador de servidores de Visual Studio

En el **Explorador de servidores** de Visual Studio puede iniciar una sesión de depuración remota, ver registros de streaming y hacer clic en una entrada de menú que abre la hoja de aplicación de API en el portal.

Para obtener una aplicación de API en el Explorador de servidores, haga clic en **Azure > Servicio de aplicaciones > [nombre del grupo de recursos] > [nombre de la aplicación de API]**, como se muestra en la ilustración.

![](./media/app-service-api-manage-in-portal/se.png)

## Pasos siguientes

En este artículo, se muestra cómo usar el portal de Azure para realizar tareas de administración de aplicaciones de API. Para las aplicaciones de API que haya instalado desde la Galería de aplicaciones de API, consulte también [Administración y supervisión de conectores y aplicaciones de API integrados](../app-service-logic/app-service-logic-monitor-your-connectors.md).

Para obtener información acerca de cómo administrar aplicaciones de API mediante la línea de comandos, consulte los artículos de la sección **Automatizar** del menú que aparece en el lado izquierdo del artículo (en las ventanas del explorador anchas) o en la parte superior del artículo (en las ventanas del explorador estrechas).

<!---HONumber=AcomDC_0114_2016-->