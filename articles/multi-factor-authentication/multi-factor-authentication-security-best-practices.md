<properties 
	pageTitle="Prácticas recomendadas para usar Azure MFA" 
	description="Este documento proporciona las prácticas recomendadas de uso de Azure MFA con cuentas de Azure" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Prácticas recomendadas de seguridad para usar Azure Multi-Factor Authentication con cuentas de Azure AD

Cuando se trata de mejorar el proceso de autenticación, la autenticación multifactor es la opción preferida por la mayoría de las organizaciones. Azure Multi-Factor Authentication (MFA) permite a las empresas cumplir sus requisitos de seguridad y cumplimiento al tiempo que proporciona una experiencia de inicio de sesión simple para sus usuarios. Este artículo aborda algunas recomendaciones que conviene tener en cuenta al planear la adopción de Azure MFA.

## Prácticas recomendadas para Azure Multi-Factor Authentication en la nube
Con el fin de proporcionar a todos los usuarios la autenticación multifactor y aprovechar las ventajas de las características extendidas que ofrece Azure Multi-Factor Authentication, deberá habilitar la Azure Multi-Factor Authentication en todos los usuarios. Esto se logra mediante uno de los siguientes:

- Azure AD Premium o Enterprise Mobility Suite 
- Proveedor de Multi-Factor Authentication

### Azure AD Premium/Enterprise Mobility Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

El primer paso recomendado para adoptar Azure MFA en la nube con Azure AD Premium o Enterprise Mobility Suite es asignar licencias a los usuarios. Azure Multi-Factor Authentication forma parte de estas series y como tal su organización no necesita nada adicional para ampliar la capacidad de la autenticación multifactor a todos los usuarios.
 
Al configurar Multi-Factor Authentication, tenga en cuenta lo siguiente:

- No necesita crear un Proveedor de Multi-Factor Authentication. Azure AD Premium y Enterprise Mobility Suite incluyen Azure Multi-Factor Authentication. Si crea un Proveedor de Multi-Factor Authentication podría facturársele dos veces.
- Azure AD Connect solo es un requisito si va a sincronizar su entorno de Active Directory local con un directorio de Azure AD. Si solo usa un directorio de Azure AD que no está sincronizado con una instancia local de Active Directory, no necesita Azure AD Connect.


### Proveedor de Multi-Factor Authentication

![Proveedor de Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si no tiene Azure AD Premium o Enterprise Mobility Suite, el primer paso recomendado para adoptar Azure MFA en la nube es crear un Proveedor de Multi-Factor Authentication. Aunque MFA está disponible de forma predeterminada para los administradores globales con Azure Active Directory, al implementar MFA para su organización debe ampliar la capacidad de la autenticación multifactor a todos los usuarios; también necesita un Proveedor de Multi-Factor Authentication. Al seleccionar el Proveedor de Multi-Factor Authentication, debe seleccionar un directorio y tener en cuenta lo siguiente:

- No es necesario tener un directorio de Azure AD para crear un Proveedor de autenticación multifactor. 
- Deberá asociar el Proveedor de Multi-Factor Authentication con el directorio de Azure AD si desea ampliar la autenticación multifactor a todos los usuarios y/o desea que sus administradores globales puedan usar características como el portal de administración, saludos personalizados e informes.
- DirSync o Sincronización de AAD son solo un requisito si va a sincronizar su entorno de Active Directory local con un directorio de Azure AD. Si solo utiliza un directorio de Azure AD que no está sincronizado con una instancia local de Active Directory, no necesita DirSync o Sincronización de AAD.
- Si tiene Azure AD Premium o Enterprise Mobility Suite, no necesita crear un Proveedor de autenticación multifactor. Solo necesita asignar una licencia a un usuario y, a continuación, puede empezar a activar MFA para los usuarios.
- Asegúrese de elegir el modelo de uso correcto para su empresa (por autenticación o por usuario habilitado), una vez seleccionado el modelo de uso no se puede cambiar.

### Cuenta de usuario
Al habilitar MFA para los usuarios, las cuentas de usuario pueden estar en uno de tres estados principales: deshabilitado, habilitado o forzado. Use las directrices siguientes para asegurarse de usar la opción más adecuada para su implementación:

- Cuando el estado del usuario se establece como deshabilitado, dicho usuario no usa la autenticación multifactor. Este es el estado predeterminado.
- Cuando el estado del usuario se establece en habilitado, significa que el usuario está habilitado, pero no ha completado el proceso de registro. Se le pedirá que complete el proceso en el inicio de sesión siguiente. Esta configuración no afecta a las aplicaciones que no son de explorador. Todas las aplicaciones continuarán funcionando hasta que se complete el proceso de registro.
- Cuando el estado del usuario se establezca en forzado, significa que el usuario puede haber completado el registro o no. Si ha completado el proceso de registro, está utilizando la autenticación multifactor. De lo contrario, se le pedirá que complete el proceso de registro en el inicio de sesión siguiente. Esta configuración afecta a las aplicaciones que no son de explorador. Estas aplicaciones no funcionarán hasta que se creen y usen contraseñas de aplicación.
- Use la plantilla User Notification disponible en el artículo [Introducción a Azure Multi-Factor Authentication en la nube](multi-factor-authentication-get-started-cloud.md) para enviar un correo electrónico a los usuarios en relación a la adopción de MFA.

### Compatibilidad

Puesto que la mayoría de los usuarios están acostumbrados a usar solo las contraseñas para autenticar, es importante que su compañía de a conocer este proceso a todos los usuarios. Este conocimiento puede reducir la probabilidad de que los usuarios llamen al departamento de soporte técnico para problemas poco importantes relacionados con MFA. Sin embargo, hay algunos escenarios en los que es necesario deshabilitar temporalmente MFA. Use las directrices siguientes para entender cómo administrar estos escenarios:

- Asegúrese de que el personal de soporte técnico está capacitado para administrar escenarios en los que la aplicación móvil o el teléfono no está recibiendo una notificación o una llamada de teléfono y por ese motivo el usuario no puede iniciar sesión. Pueden habilitar la opción de omisión una única vez para permitir a un usuario autenticarse una sola vez al omitir la autenticación multifactor. La omisión es temporal y expira una vez que ha pasado el número especificado de segundos. 
- Si es necesario, puede aprovechar la capacidad de direcciones IP de confianza de Azure MFA. Esta característica permite a los administradores de un inquilino administrado o federado la capacidad de omitir la autenticación multifactor para los usuarios que inician sesión desde la intranet local de la empresa. Las características están disponibles para los inquilinos de Azure AD que tienen licencias de Azure AD Premium, Enterprise Mobility Suite o Azure Multi-Factor Authentication.


## Prácticas recomendadas para Azure Multi-Factor Authentication local
Si su empresa ha decidido aprovechar su propia infraestructura para habilitar MFA, será necesario implementar un servidor de Azure Multi-Factor Authentication local. En el diagrama siguiente se muestran los componentes del servidor MFA:

![Proveedor de Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/server.png) * No se instala de forma predeterminada **Instalado pero no habilitado de forma predeterminada


El servidor de Azure Multi-Factor Authentication puede usarse para proteger recursos en la nube y locales a los que se tiene acceso mediante las cuentas de Azure AD. Sin embargo esto solo puede lograrse mediante la federación. Es decir, debe tener AD FS y tenerlo federado con el inquilino de Azure AD. Al configurar el servidor de Multi-Factor Authentication, tenga en cuenta lo siguiente:

- Si está protegiendo recursos de Azure AD mediante Servicios de federación de Active Directory, el primer factor de autenticación se realiza de manera local mediante AD FS y el segundo factor se realiza localmente respondiendo a la notificación.
- No es necesario que Servidor Azure Multi-Factor Authentication esté instalado en el servidor de federación de AD FS. Sin embargo, el adaptador de Multi-Factor Authentication para AD FS debe estar instalado en un Windows Server 2012 R2 que ejecute AD FS. Puede instalar el servidor en un equipo diferente, siempre y cuando sea una versión compatible e instale al adaptador de AD FS por separado en el servidor de federación de AD FS. Consulte el procedimiento siguiente para obtener instrucciones acerca de cómo instalar el adaptador por separado.
- El asistente para la instalación del adaptador de AD FS de Multi-Factor Authentication crea un grupo de seguridad denominado PhoneFactor Admins en Active Directory y, a continuación, agrega la cuenta de servicio de AD FS del servicio de federación a este grupo. Se recomienda comprobar en el controlador de dominio que el grupo PhoneFactor Admins está creado y que la cuenta de servicio de AD FS sea un miembro de este grupo. Si es necesario, agregue la cuenta de servicio de AD FS manualmente al grupo PhoneFactor Admins en el controlador de dominio.

### Portal de usuario
Este portal se ejecuta en un sitio web de Internet Information Server (IIS), que permite capacidades de autoservicio y proporciona un conjunto completo de capacidades de administración de usuarios. Use las directrices siguientes para configurar este componente:

- Se requiere IIS 6 o superior
- ASP.NET v2.0.507207 debe estar instalado y registrado
- Este servidor se puede implementar en una red perimetral.



### Contraseñas de aplicación
Si su organización está federada mediante SSO con Azure AD y va a usar Azure MFA, a continuación, tenga en cuenta lo siguiente al usar las contraseñas de aplicación (recuerde que esto solo se aplica cuando se usa la federación (SSO)):

- La contraseña de aplicación se comprueba mediante Azure AD y, por tanto, omite la federación. La federación solo se usa activamente al configurar la contraseña de aplicación.
- Para los usuarios federados (SSO), las contraseñas se almacenarán en el Id. de organización. Si el usuario abandona la empresa, esta información tiene que fluir al identificador de la organización usando DirSync en tiempo real. La deshabilitación o eliminación de la cuenta puede tardar hasta 3 horas en sincronizarse, retrasando la deshabilitación o eliminación de contraseña de aplicación en Azure AD.
- La configuración del control de acceso de cliente local no se cumple con la contraseña de aplicación
- No hay ningún registro o capacidad de auditoría en la autenticación local que esté disponible para la contraseña de aplicación
- Es necesaria una mayor formación del usuario final para el cliente de Microsoft Lync 2013. 
- Algunos diseños de arquitectura avanzada pueden requerir el uso de una combinación de nombre de usuario y contraseñas de la organización y de contraseñas de aplicación cuando se usa la autenticación multifactor con clientes, en función de dónde se autentiquen. Para los clientes que se autentican en una infraestructura local, usaría un nombre de usuario y una contraseña de la organización. Para los clientes que se autentican en Azure AD, usaría la contraseña de aplicación.
- De forma predeterminada, los usuarios no pueden crear contraseñas de aplicación, si su compañía lo requiere o si necesita que los usuarios puedan crear una contraseña de aplicación en algunos escenarios, asegúrese de que está seleccionada la opción Permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones que no son de explorador.

## Consideraciones adicionales
Use la lista siguiente para conocer algunas consideraciones adicionales y procedimientos recomendados para cada componente que se implementará de forma local:

Método|Descripción
:------------- | :------------- | 
[Servicio de federación de Active Directory](multi-factor-authentication-get-started-adfs.md)|Información sobre cómo configurar la Azure Multi-Factor Authentication con AD FS.
[Autenticación de RADIUS](multi-factor-authentication-get-started-server-radius.md)| Información sobre la instalación y configuración del servidor de MFA de Azure con RADIUS.
[Autenticación de IIS](multi-factor-authentication-get-started-server-iis.md)|Información sobre la instalación y configuración del servidor de MFA de Azure con IIS.
[Autenticación de Windows](multi-factor-authentication-get-started-server-windows.md)| Información sobre la instalación y configuración del servidor de MFA de Azure con la autenticación de Windows.
[Autenticación LDAP](multi-factor-authentication-get-started-server-ldap.md)|Información sobre la instalación y configuración del servidor de MFA de Azure con la autenticación LDAP.
[Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS](multi-factor-authentication-get-started-server-rdg.md)| Información sobre la instalación y configuración del servidor de MFA de Azure con la puerta de enlace de escritorio remoto mediante RADIUS.
[Sincronización con Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Información sobre la instalación y configuración de la sincronización entre Active Directory y el servidor de MFA de Azure.
[Implementación del servicio web móvil de la aplicación móvil del servidor de Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md)|Información sobre la instalación y configuración del servicio web del servidor de Azure MFA.
[Configuración avanzada de VPN con Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md)|Información acerca de la configuración de dispositivos Cisco ASA, Citrix Netscaler y Juniper/Pulse Secure VPN mediante LDAP o RADIUS. 


## Recursos adicionales
Si bien este artículo resalta algunas prácticas recomendadas para Azure MFA, existen otros recursos que también puede usar al planear la implementación de MFA. La lista siguiente tiene algunos artículos clave que pueden ayudarle durante este proceso:

- [Informes en Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
- [La experiencia de configuración para Azure Multi-Factor Authentication ](multi-factor-authentication-end-user-first-time.md)
- [P+F sobre Azure Multi-Factor Authentication ](multi-factor-authentication-faq.md)

<!---HONumber=AcomDC_0218_2016-->