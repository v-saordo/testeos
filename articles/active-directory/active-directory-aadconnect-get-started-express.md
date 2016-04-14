<properties
	pageTitle="Introducción a la configuración rápida de Azure AD Connect | Microsoft Azure"
	description="Obtenga información acerca de cómo descargar, instalar y ejecutar el asistente para instalación de Azure AD Connect."
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
	ms.topic="get-started-article"
	ms.date="02/18/2016"
	ms.author="billmath;andkjell"/>

# Introducción a Azure AD Connect mediante la configuración rápida
La siguiente documentación le ayudará a empezar a trabajar con Azure Active Directory Connect. Esta documentación trata del uso de la instalación rápida para Azure AD Connect.

## Documentación relacionada
Si no leyó la documentación que se encuentra en [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md), en la tabla siguiente se proporcionan vínculos a temas relacionados. Los dos primeros temas en negrita son necesarios antes de iniciar la instalación.

| Tema. | |
| --------- | --------- |
| **Descarga de Azure AD Connect** | [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware y requisitos previos** | [Azure AD Connect: Hardware y requisitos previos](active-directory-aadconnect-prerequisites.md) |
| Instalación mediante configuración personalizada | [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Actualización desde DirSync | [Actualización desde la herramienta Sincronización de Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Después de la instalación | [Comprobación de la instalación y asignación de licencias ](active-directory-aadconnect-whats-next.md) |
| Cuentas usadas para la instalación | [Más información sobre permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |


## Instalación rápida de Azure AD Connect
Configuración rápida es la opción predeterminada que se selecciona y es uno de los escenarios más comunes. Al hacer esto, Azure AD Connect implementa la sincronización con la opción de sincronización de contraseña. Esto es para un solo bosque y permite que los usuarios utilicen su contraseña local para iniciar sesión en la nube. La Configuración rápida activa automáticamente una sincronización una vez completada la instalación (puede elegir no hacerlo). Con esta opción, ampliará su directorio local a la nube con tan solo unos pocos clics.

![Bienvenida a Azure AD Connect](./media/active-directory-aadconnect-get-started-express/welcome.png)

### Para instalar Azure AD Connect mediante la configuración rápida

1. Inicie sesión en el servidor en el que desea instalar Azure AD Connect en un administrador local. Debe ser el servidor que quiere que sea el servidor de sincronización.
2. Navegue hasta el archivo AzureADConnect.msi y haga doble clic en él.
3. En la pantalla de bienvenida, active la casilla que acepta los términos de licencia y haga clic en **Continuar**.
4. En la pantalla Configuración rápida, haga clic en **Usar configuración rápida**. ![Bienvenida a Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. En la pantalla Conectar a Azure AD, escriba el nombre de usuario y la contraseña de un administrador global de Azure para su Azure AD. Haga clic en **Siguiente**. ![Conexión a AAD](./media/active-directory-aadconnect-get-started-express/connectaad.png) Si recibe un error y tiene problemas de conectividad, consulte [Solución de problemas de conectividad con Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).
6. En la pantalla Conectar con AD DS, escriba el nombre de usuario y la contraseña para una cuenta de administrador de empresa. Haga clic en **Siguiente**. ![Bienvenida a Azure AD Connect](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. En la pantalla Listo para configurar, haga clic en **Instalar**.
	- En la página Listo para configurar, también puede desactivar la casilla **Inicie el proceso de sincronización en cuanto se complete la configuración inicial**. Si lo hace, el asistente configurará la sincronización, pero dejará la tarea deshabilitada por lo que no se ejecutará hasta que la habilite manualmente en el Programador de tareas. Una vez habilitada la tarea, la sincronización se ejecutará cada 30 minutos.
	- Opcionalmente, también puede configurar los servicios de sincronización para **Implementación híbrida de Exchange**; para ello, active la casilla correspondiente. Si no piensa tener buzones de Exchange locales y en la nube, no es necesario hacerlo. ![Bienvenida a Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
8. Una vez completada la instalación, haga clic en **Salir**.
9. Una vez completada la instalación, cierre la sesión e inicie de sesión de nuevo antes de utilizar Synchronization Service Manager o el Editor de reglas de sincronización.

Para ver un vídeo sobre el uso de la instalación exprés, consulte lo siguiente:

[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Pasos siguientes
Ahora que tiene instalado Azure AD Connect, puede [comprobar la instalación y asignar licencias](active-directory-aadconnect-whats-next.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0224_2016-->