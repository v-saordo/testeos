<properties
	pageTitle="Comprobación del uso de aplicaciones en PowerApps Enterprise | Microsoft Azure"
	description="Vea todas las aplicaciones, API, usuarios, solicitudes de aplicación y permisos de actualización en el Portal de Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
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


# Administración y protección de PowerApps
Puede crear el entorno del Servicio de aplicaciones y agregar API y sus conexiones. Ahora, los usuarios de su organización pueden comenzar a utilizar estas API y conexiones. También puede administrar todas las aplicaciones creadas en la organización. Entre estas opciones se incluyen:

- Ver las distintas aplicaciones dentro del entorno del Servicio de aplicaciones, como PowerApps, aplicaciones web, aplicaciones lógicas, aplicaciones móviles, etc.
- Ver todas las API que aplicaciones específicas usan.
- Ver y administrar el acceso del usuario a las aplicaciones dentro del entorno del Servicio de aplicaciones. 
- Ver y administrar el acceso del usuario a las API y sus conexiones. 

Recuerde que puede agregar otras aplicaciones, incluidas aplicaciones web y aplicaciones lógicas, al entorno del Servicio de aplicaciones. Luego, puede abrir PowerApps Enterprise para ver y administrar estas aplicaciones.


## Incorporación de administradores de PowerApps
Una vez que PowerApps Enterprise está habilitado y listo para usarse, puede agregar administradores y supervisar otras aplicaciones dentro del entorno del Servicio de aplicaciones.

1. En el [Portal de Azure](https://portal.azure.com/), abra **PowerApps**.
2. Seleccione **Configuración**.
3. En **Configuración**, seleccione **Administrador**: ![][1]  
4. En **Usuarios**, seleccione **Agregar**.
5. Seleccione el rol **Propietario**: ![][2]  

	> [AZURE.IMPORTANT]Asegúrese de seleccionar el rol **Propietario** si asigna a alguna persona como administrador de PowerApps. Los otros roles que aparecen no otorgarán a los usuarios el acceso total para administrar PowerApps.

6. Seleccione los usuarios o grupos.
7. Seleccione **Aceptar** para completar los pasos.

Cuando agrega administradores a PowerApps, los usuarios y grupos que agrega como administradores:

- Pueden agregar a otros usuarios como administradores de PowerApps.
- Pueden administrar todas las aplicaciones, así como también el acceso de los usuarios.
- No pueden cambiar la facturación.

> [AZURE.IMPORTANT]Los administradores de PowerApps no puede hacer cambios en el Entorno del Servicio de aplicaciones hasta que se les otorgue el rol Propietario en el grupo de recursos del entorno del Servicio de aplicaciones. Para hacerlo, consulte [este artículo](powerapps-get-started-azure-portal.md).

Una vez que el rol Propietario se otorga en el grupo de recursos del entorno del Servicio de aplicaciones, los administradores de PowerApps también pueden:

- Crear y configurar las API y sus conexiones.
- Realizar cambios en la configuración de PowerApps, incluido el entorno del Servicio de aplicaciones.
- Agregar a otros usuarios y grupos y darles roles y permisos para las API, sus conexiones y el entorno del Servicio de aplicaciones. 


## Administración de PowerApps y otros tipos de aplicaciones
Una vez que habilite PowerApps y el entorno del Servicio de aplicaciones, puede agregar otras aplicaciones, como aplicaciones web y aplicaciones lógicas al mismo entorno del Servicio de aplicaciones. Después de hacerlo, las aplicaciones aparecerán en *Todas las aplicaciones* junto con las aplicaciones creadas en PowerApps. Puede hacer clic en cada tipo de aplicación para explorarlas.

### Ver y administrar PowerApps

1. En el [Portal de Azure](https://portal.azure.com/), abra **PowerApps**.
2. En el icono **Todas las aplicaciones**, seleccione **PowerApps**: ![][3]  
3. Seleccione una aplicación para ver sus detalles, los que incluyen:  
	- Las API que utiliza la aplicación.
	- Los usuarios y grupos que tienen acceso a la aplicación. 
	- El análisis de la aplicación (próximamente).

#### Agregar una aplicación

No puede agregar una aplicación en el Portal de Azure. Actualmente, debe ir al [Portal de PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715583).

#### Eliminar las aplicaciones creadas en PowerApps
Como administrador de PowerApps, puede eliminar cualquier aplicación, incluidas las creadas en PowerApps y otros tipos de aplicaciones en el entorno del Servicio de aplicaciones. Para eliminar la aplicación, seleccione el icono **Todas las aplicaciones**, seleccione la aplicación y, luego, seleccione **Eliminar**: ![][4]


#### Otorgar a los usuarios o grupos acceso para usar una aplicación
Como administrador de PowerApps, puede agregar usuarios y grupos a PowerApps o puede quitarlos.

1. En el [Portal de Azure](https://portal.azure.com/), abra **PowerApps**.
2. En el icono **Todas las aplicaciones**, seleccione **PowerApps**: ![][3]  
3. Seleccione una aplicación, como **Consola de servicio**. 
4. En **Configuración**, seleccione **Acceso de usuario a la aplicación**: ![][5]  
5. Seleccione **Agregar** para agregar un usuario o grupo nuevo. 
6. Seleccione un rol:  
	- Puede editar
	- Puede ver
7. Seleccione los usuarios o grupos.
8. Seleccione **Aceptar** para completar los pasos.

### Ver y administrar las aplicaciones lógicas

1. En el [Portal de Azure](https://portal.azure.com/), abra **PowerApps**.
2. En el icono **Todas las aplicaciones**, seleccione **Aplicaciones lógicas**: ![][8]  
3. Seleccione una aplicación lógica para ver los detalles de la aplicación. Asegúrese de seleccionar la suscripción correcta para que PowerApps muestre las aplicaciones lógicas adecuadas: ![][7]  

	> [AZURE.IMPORTANT]En la vista previa pública, puede ver algunas incoherencias entre la cantidad de aplicaciones lógicas que aparece en la hoja de exploración y la cantidad que aparece en la hoja principal de PowerApps. Se espera que esto sea así. El portal muestra todas las aplicaciones lógicas en todos los planes de hospedaje y no filtra las aplicaciones lógicas en el entorno de servicio de aplicaciones implementadas para PowerApps. Este comportamiento se solucionará en futuras actualizaciones.

	**Para obtener más información sobre las aplicaciones lógicas y cómo administrarlas, consulte [estas instrucciones](https://azure.microsoft.com/documentation/services/app-service/logic/).**

### Ver y administrar las aplicaciones web

1. En el [Portal de Azure](https://portal.azure.com/), abra **PowerApps**.
2. En el icono **Todas las aplicaciones**, seleccione **Aplicaciones web**: ![][9]  

	**Para obtener más información sobre las aplicaciones web y cómo administrarlas, consulte [estas instrucciones](https://azure.microsoft.com/documentation/services/app-service/web/).**

### Ver y administrar las aplicaciones móviles

1. En el [Portal de Azure](https://portal.azure.com/), abra **PowerApps**.
2. En el icono **Todas las aplicaciones**, consulte **Aplicaciones móviles**: ![][10]  

	**Para obtener más información sobre las aplicaciones móviles y cómo administrarlas, consulte [estas instrucciones](https://azure.microsoft.com/documentation/services/app-service/mobile/).**


## Revisión de las opciones de seguridad
Se utilizan distintos métodos de seguridad, en función de las operaciones que realiza. A continuación, aparece la información que necesita saber:

- **Administrador de suscripciones**: Estos administradores controlan la facturación y son responsables de registrar la empresa en PowerApps Enterprise. Solo los administradores de suscripciones pueden solicitar la habilitación de PowerApps dentro de la suscripción de Azure de su empresa. 

- **Acceso de usuario de tiempo de ejecución**: Estos son los tres tipos distintos de acceso de usuario de tiempo de ejecución:
	- **Acceso de usuario a la aplicación**: Este permiso controla si el usuario de la aplicación *puede editar* o *puede ver* la aplicación.
	- **Acceso de usuario a la API**: Este permiso controla el acceso de tiempo de ejecución. Si los usuarios cuentan con este permiso, pueden usar la API de su aplicación. Los usuarios tienen permiso o no para usar la API en tiempo de ejecución. 
	- **Acceso de usuario a la conexión**: los permisos de usuario de tiempo de ejecución disponibles para una conexión son *Puede ver* y *Puede editar*. Cuando agrega una API (o un perfil de conexión) y crea su conexión, otorga a los usuarios y grupos estos permisos específicos: ![][6]  

		Por ejemplo, en el caso del grupo de ventas de su empresa, puede otorgarle el permiso *Puede editar* con respecto a una conexión de una API de conector de SQL. Los usuarios que cuentan con permiso *Puede editar* podrán usar la conexión de sus aplicaciones, además de editar la configuración de la conexión. Los usuarios que cuentan con permiso *Puede ver* podrán usar la conexión de sus aplicaciones, pero no modificar la configuración de la conexión, como la cadena de conexión.

- **Control de acceso basado en rol** (RBAC): Muchas ofertas de Azure usan controles de acceso basado en rol para determinar quiénes pueden realizar qué operaciones. En PowerApps, RBAC se usa en un par de lugares:
	- Cuando ingresa por primera vez al Portal de PowerApps, puede agregar y administrar los usuarios que serán administradores de PowerApps. 
	- Cuando crea el entorno del Servicio de aplicaciones, agrega usuarios o grupos a PowerApps y también puede quitarlos. Por ejemplo, puede agregar grupos de administradores específicos dentro de la empresa al rol *Propietarios*, lo que les permite crear API y conexiones. Estas API y conexiones se agregan luego a las aplicaciones creadas en PowerApps.
	- Cuando agrega usuarios a aplicaciones como aplicaciones web, aplicaciones lógicas o aplicaciones móviles. Puede elegir el rol de estos usuarios.  
		
		Agregar usuarios y asignar roles es igual que usar el [control de acceso basado en rol](../role-based-access-control-configure.md) dentro de Azure. Algunos roles incluyen:

		Rol | Descripción
		--- | ---
		Colaborador | Administra todo, pero no puede otorgar acceso a los usuarios.
		Lector | Puede ver todo el contenido, pero no puede realizar cambios.
		Propietario | Puede administrar todo y otorgar acceso a los usuarios.

Con estos roles, puede otorgar al usuarioA el permiso **Puede ver** a una aplicación diaria de Twitter y al usuarioB el permiso **Puede editar** a la aplicación ShuttleBus. Puede otorgar al usuarioB acceso a todas las API. Realmente puede obtener el control detallado con estos derechos o agregar a todas las personas con un rol específico. En realidad, depende de las necesidades del negocio.


## Resumen y pasos siguientes
En este tema, se informan las distintas opciones para administrar PowerApps y los métodos de seguridad implementados en PowerApps.

Ahora que configuró la experiencia del Portal de Azure, puede comenzar a crear sus aplicaciones. Estos vínculos son buenos puntos de partida:

- [Creación de una aplicación a partir de una plantilla en PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715536) 
- [Creación de una aplicación a partir de datos en PowerApps](http://go.microsoft.com/fwlink/?LinkId=715539) 
- [Creación de una aplicación en PowerApps desde cero](http://go.microsoft.com/fwlink/p/?LinkId=715538)


[1]: ./media/powerapps-manage-monitor-usage/addadmin.png
[2]: ./media/powerapps-manage-monitor-usage/selectrole.png
[3]: ./media/powerapps-manage-monitor-usage/PowerApps.png
[4]: ./media/powerapps-manage-monitor-usage/deleteapp.png
[5]: ./media/powerapps-manage-monitor-usage/appuseraccess.png
[6]: ./media/powerapps-manage-monitor-usage/selectpermission.png
[7]: ./media/powerapps-manage-monitor-usage/alllogicapps.png
[8]: ./media/powerapps-manage-monitor-usage/logicapps.png
[9]: ./media/powerapps-manage-monitor-usage/webapps.png
[10]: ./media/powerapps-manage-monitor-usage/mobileapps.png

<!---HONumber=AcomDC_1203_2015-->