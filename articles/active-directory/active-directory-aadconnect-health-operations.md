<properties
	pageTitle="Operaciones de Azure AD Connect Health."
	description="Este artículo describe las operaciones adicionales que pueden realizarse una vez que haya implementado Azure AD Connect Health."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath"/>

# Operaciones de Azure AD Connect Health

El tema siguiente describe las distintas operaciones que se pueden realizar con Azure AD Connect Health.

## Habilitación de notificaciones de correo electrónico
Puede configurar el Servicio de Azure AD Connect Health para enviar notificaciones por correo electrónico cuando se generen alertas que indiquen que el estado de la infraestructura de identidad no es correcto. Esto ocurrirá cuando se genere una alerta y también cuando se marque como resuelta. Siga las instrucciones que aparecen a continuación para configurar las notificaciones de correo electrónico.
>[AZURE.NOTE] Las notificaciones de correo electrónico están deshabilitadas de forma predeterminada.


### Para habilitar las notificaciones de correo electrónico de Azure AD Connect Health

1. Abra la hoja Alertas del servicio para el que desea recibir una notificación de correo electrónico.
2. Haga clic en el botón "Configuración de notificaciones" de la barra de acciones.
3. Ponga el modificador de la opción Notificación de correo electrónico en la posición ON (Activado).
4. Active la casilla para establecer que todos los administradores globales reciban notificaciones de correo electrónico.
5. Si desea recibir notificaciones de correo electrónico en otras direcciones de correo electrónico, puede especificarlas en el cuadro Destinatario de correo electrónico adicional. Para quitar una dirección de correo electrónico de esta lista, haga clic con el botón derecho en la entrada y seleccione Eliminar.
6. Para finalizar los cambios, haga clic en "Guardar". Los cambios entrarán en vigor solo después de seleccionar "Guardar".

## Eliminación de una instancia de servidor o servicio

### Eliminación de un servidor del Servicio de Azure AD Connect Health
En algunos casos, es posible que desee dejar de supervisar un servidor. Siga las instrucciones siguientes para quitar un servidor del Servicio de Azure AD Connect Health.

Cuando elimine un servidor, tenga en cuenta lo siguiente:

- Esta acción DETENDRÁ cualquier recopilación de datos de ese servidor. Este servidor se quitará del servicio de supervisión. Después de esta acción, no podrá ver nuevas alertas ni datos de análisis de uso o supervisión de este servidor.
- Esta acción NO desinstalará ni quitará el agente de mantenimiento del servidor. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en el servidor relacionados con el agente de mantenimiento.
- Esta acción NO eliminará los datos que ya se recopilaron desde este servidor. Los datos se eliminarán según lo estipulado en la Directiva de retención de datos de Microsoft Azure.
- Después de realizar esta acción, si desea comenzar a supervisar nuevamente el mismo servidor, deberá desinstalar el agente de mantenimiento en este servidor y volver a instalarlo.


#### Para eliminar un servidor del Servicio de Azure AD Connect Health

1. Seleccione el nombre del servidor que se va a quitar para abrir la hoja Servidor en la hoja Lista de servidores.
2. En la hoja Servidor, haga clic en el botón "Eliminar" de la barra de acciones.
3. Confirme la acción para eliminar el servidor; para ello, escriba el nombre del servidor en el cuadro de confirmación.
4. Haga clic en el botón "Eliminar".


### Eliminación de una instancia de servicio del Servicio de Azure AD Connect Health

En algunos casos, es posible que desee quitar una instancia de servicio. Siga las instrucciones siguientes para quitar una instancia de servicio del Servicio de Azure AD Connect Health.

Cuando elimine una instancia de servicio, tenga en cuenta lo siguiente:

- Esta acción quitará la instancia de servicio actual del servicio de supervisión.
- Esta acción NO desinstalará ni quitará el agente de mantenimiento de ninguno de los servidores que se supervisaron como parte de esta instancia de servicio. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en los servidores relacionados con el agente de mantenimiento.
- Todos los datos de esta instancia de servicio se eliminarán según lo estipulado en la Directiva de retención de datos de Microsoft Azure.
- Después de realizar esta acción, si desea comenzar a supervisar el servicio, desinstale el agente de mantenimiento en todos los servidores que se supervisarán y vuelva a instalarlo. Después de realizar esta acción, si desea comenzar a supervisar nuevamente el mismo servidor, deberá desinstalar el agente de mantenimiento en este servidor y volver a instalarlo.


#### Para eliminar una instancia de servicio del Servicio de Azure AD Connect Health

1. Seleccione el identificador del servicio (nombre de la granja) que desea quitar para abrir la Hoja Servicio en la hoja Lista de Servicios.
2. En la hoja Servidor, haga clic en el botón "Eliminar" de la barra de acciones.
3. Confirme el nombre del servicio; para ello, escríbalo en el cuadro de confirmación (por ejemplo: sts.contoso.com).
4. Haga clic en el botón "Eliminar". 
<br><br>


[//]: # "Inicio de la sección RBAC"
## Administración del acceso con control de acceso basado en rol
### Información general
El [control de acceso basado en rol](role-based-access-control-configure.md) para Azure AD Connect Health proporciona acceso al servicio Azure AD Connect Health a usuarios y/o grupos fuera de los administradores globales. Esto se logra mediante la asignación de roles a los usuarios y/o grupos previstos y proporciona un mecanismo para limitar los administradores globales dentro del directorio.

#### Roles
Azure AD Connect Health admite los siguientes roles integrados.

| Rol | Permisos |
| ----------- | ---------- |
| Propietario | Los propietarios pueden ***administrar el acceso*** (por ejemplo, asignar roles a un usuario y/o grupo), ***ver toda la información*** (por ejemplo, ver las alertas) desde el portal y ***cambiar la configuración*** (por ejemplo, notificaciones de correo electrónico) dentro de Azure AD Connect Health. <br>De forma predeterminada, a los administradores globales de Azure AD se les asigna este rol y esto no se puede cambiar. |
|Colaborador| Los colaboradores pueden ***ver toda la información*** (por ejemplo, ver las alertas) desde el portal y ***cambiar la configuración*** (por ejemplo, notificaciones de correo electrónico) dentro de Azure AD Connect Health.|
|Lector| Los lectores pueden ***ver toda la información*** (por ejemplo, ver las alertas) desde el portal dentro de Azure AD Connect Health.|

Todos los demás roles (como 'Administradores de acceso de usuario' o 'Usuarios del laboratorio DevTest'), incluso si están disponibles en la experiencia del portal, no afectan al acceso dentro de Azure AD Connect Health.

#### Ámbito de acceso

Azure AD Connect admite la administración de acceso a dos niveles:

- ***Todas las instancias de servicio***: este es el modo recomendado para la mayoría de los clientes y controla el acceso para todas las instancias de servicio (por ejemplo, una granja de servidores ADFS) en todos los tipos de rol que está supervisando Azure AD Connect Health.

- ***Instancia de servicio***: en algunos casos, puede que necesite separar el acceso según los tipos de rol o por una instancia de servicio. En este caso, puede administrar el acceso en el nivel de instancia de servicio.

El permiso se concede si un usuario final tiene acceso al nivel de directorio o de instancia de servicio.


### Cómo permitir el acceso a los usuarios o grupos a Azure AD Connect Health
#### Paso 1: Seleccionar el ámbito de acceso adecuado
Para permitir a un usuario acceder al nivel de *todas las instancias de servicio* dentro de Azure AD Connect Health, abra la hoja principal en Azure AD Connect Health.<br>
#### Paso 2: Agregar usuarios, grupos y asignar roles
1. Haga clic en la parte "Usuarios" de la sección Configurar.<br> 
![Hoja principal de RBAC de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Seleccione "Agregar".
3. Seleccione "Rol" como "Propietario"<br>
 ![Agregar usuario de RBAC de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Escriba el nombre o identificador del usuario o grupo de destino. Puede seleccionar uno o más usuarios o grupos al mismo tiempo. Haga clic en "Seleccionar". 
![Seleccionar usuario de RBAC de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Seleccione "Aceptar".<br>

6. Después de finalizar la asignación de roles, los usuarios y grupos aparecerán en la lista.<br> 
![Lista de usuarios de RBAC de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Estos pasos permitirán a los usuarios y grupos enumerados el acceso según sus roles asignados.
>[AZURE.NOTE]
- Los administradores globales siempre tienen acceso total a todas las operaciones, pero las cuentas de los administradores globales no estarán presentes en la lista anterior. La característica "Invitar a usuarios" NO se admite dentro de Azure AD Connect Health.

#### Paso 3: Compartir la ubicación de la hoja con usuarios o grupos
1. Después de asignar permisos, un usuario puede acceder a Azure AD Connect Health yendo a [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Una vez en la hoja, el usuario puede anclar dicha hoja o diferentes partes al panel simplemente haciendo clic en "Anclar al panel"<br> 
![Anclar hoja de RBAC de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Un usuario con el rol de "Lector" asignado no podrá realizar la operación "crear" para obtener la extensión de Azure AD Connect Health de Azure Marketplace. Este usuario todavía puede obtener la hoja visitando el vínculo anterior. Para usos posteriores, el usuario puede anclar la hoja en el panel.

### Eliminación de usuarios y/o grupos
Puede quitar un usuario o grupo agregado a la parte Control de acceso basado en rol de Azure AD Connect Health si hace clic con el botón derecho y selecciona Quitar.<br> 
![Quitar usuario de RBAC de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # "Fin de la sección RBAC"

## Vínculos relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=AcomDC_0128_2016-->