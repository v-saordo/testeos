<properties
	pageTitle="Azure AD Connect: instalación personalizada | Microsoft Azure"
	description="En este documento se explica las opciones de instalación personalizada de Azure AD Connect. Siga estas instrucciones para instalar Active Directory mediante Azure AD Connect."
	services="active-directory"
    keywords="qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD"
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
	ms.date="01/25/2016"
	ms.author="billmath;andkjell"/>

# Instalación personalizada de Azure AD Connect


La documentación siguiente proporciona información acerca del uso de la opción de instalación personalizada para Azure AD Connect. Puede utilizar esta opción si tiene opciones de configuración adicionales o necesita características opcionales que no se tratan en la instalación rápida.

## Documentación relacionada
Si no leyó la documentación que se encuentra en [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md), en la tabla siguiente se proporcionan vínculos a temas relacionados. Los tres primeros temas en negrita son necesarios antes de iniciar la instalación.

| Tema. | |
| --------- | --------- |
| **Descarga de Azure AD Connect** | [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware y requisitos previos** | [Azure AD Connect: Hardware y requisitos previos](active-directory-aadconnect-prerequisites.md) |
| **Cuentas usadas para la instalación** | [Permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |
| Instalación mediante configuración rápida | [Instalación rápida de Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Actualización desde DirSync | [Actualización desde la herramienta Sincronización de Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Después de la instalación | [Comprobación de la instalación y asignación de licencias ](active-directory-aadconnect-whats-next.md) |




## Instalación de los componentes necesarios

Al instalar los servicios de sincronización, puede dejar desactivada la sección de configuración opcional y Azure AD Connect configurará todo automáticamente. Esto incluye la configuración de una instancia de SQL Server 2012 Express y la creación de los grupos adecuados y asignación de permisos a los mismos. Si desea cambiar los valores predeterminados, puede utilizar la tabla siguiente para conocer las opciones de configuración opcionales que están disponibles.

![Componentes necesarios](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)


| Configuración opcional | Descripción |
| ------------- | ------------- |
| Usar un SQL Server existente | Permite especificar el nombre de SQL Server y el nombre de la instancia. Elija esta opción si ya dispone de un servidor de base de datos que le gustaría utilizar. Si SQL Server no tiene la exploración habilitada y debe especificar un número de puerto, en el cuadro **Nombre de instancia** escriba el nombre de la instancia seguido de una coma y un número de puerto. |
| Usar una cuenta de servicio existente | De forma predeterminada, Azure AD Connect creará una cuenta de servicio local para los servicios de sincronización que se van a utilizar. la contraseña se genera automáticamente y es desconocida para la persona que instala Azure AD Connect. Si utiliza un servidor SQL remoto, necesita una cuenta de servicio en el dominio y conocer la contraseña. En esos casos, especifique la cuenta de servicio para usar. Asegúrese de que el usuario que ejecuta la instalación es una SA en SQL, por lo que se puede crear un inicio de sesión para la cuenta de servicio. Consulte [Permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation) |
| Especificar grupos de sincronización personalizada | De forma predeterminada, Azure AD Connect creará cuatro grupos locales en el servidor cuando se instalen los servicios de sincronización. Estos grupos son: grupo Administradores, grupo Operadores, grupo Examinar y grupo Restablecimiento de contraseña. Si desea especificar sus propios grupos puede hacerlo aquí. Los grupos deben ser locales en el servidor y no se pueden encontrar en el dominio. |


## Inicio de sesión de usuario
Después de instalar los componentes necesarios, se le pedirá que especifique el método de inicio de sesión único que utilizarán los usuarios. En la tabla siguiente se proporciona una breve descripción de las opciones disponibles. Para obtener una descripción completa de los métodos de inicio de sesión, consulte [Inicio de sesión de usuario](active-directory-aadconnect-user-signin.md).

![Inicio de sesión de usuario](./media/active-directory-aadconnect-get-started-custom/usersignin.png)



Inicio de sesión único | Descripción
------------- | ------------- |
Sincronización de contraseñas |Los usuarios puedan iniciar sesión en los Servicios en la nube de Microsoft (como Office 365, Dynamics CRM y Windows InTune) con la misma contraseña que usan para iniciar sesión en la red local. La contraseña del usuario se sincroniza en Azure mediante un hash de contraseña y la autenticación se produce en la nube. Consulte [Sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) para obtener más información.
Federación con AD FS|Los usuarios puedan iniciar sesión en los Servicios en la nube de Microsoft (como Office 365, Dynamics CRM y Windows InTune) con la misma contraseña que usan para iniciar sesión en la red local. Los usuarios se redirigen a la instancia local de AD FS para iniciar sesión y la autenticación se realiza de forma local.
No configurar| No se instalará ni configurará ninguna característica. Elija esta opción si ya tiene un servidor de federación de terceros u otra solución existente ya instalada.



## Conectarse a Azure AD
En la pantalla Conectarse a Azure AD, especifique una cuenta de administrador global y una contraseña. Asegúrese de que esta cuenta no tenga habilitada Multi-Factor Authentication. Esto provocará un error de autenticación. Esta cuenta solo se usa para crear una cuenta de servicio en Azure AD y no se utiliza cuando el asistente haya finalizado.

![Inicio de sesión de usuario](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Si recibe un error y tiene problemas de conectividad, consulte [Troubleshoot connectivity issues with Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md) (Solución de problemas de conectividad con Azure AD Connect).

## Páginas bajo la sección Sincronización

### Conectar sus directorios
Para conectarse al Servicio de dominio de Active Directory, Azure AD Connect necesita las credenciales de una cuenta con permisos suficientes. Esta cuenta puede ser una cuenta de usuario normal porque solo tiene los permisos de lectura predeterminados. Sin embargo, según el escenario, puede que necesite permisos adicionales. Para obtener más información, consulte [Permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account).

![Inicio de sesión de usuario](./media/active-directory-aadconnect-get-started-custom/connectdir.png)


### Identificación de forma exclusiva de usuarios

La característica Correspondencia entre bosques permite definir cómo se representan los usuarios de los bosques de AD DS en Azure AD. Un usuario puede estar representado solo una vez en todos los bosques o tener una combinación de cuentas habilitadas y deshabilitadas.

![Inicio de sesión de usuario](./media/active-directory-aadconnect-get-started-custom/unique.png)


Configuración | Descripción
------------- | ------------- |
[Mis usuarios solo se representan una vez en todos los bosques](active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Todos los usuarios se crean como objetos individuales en Azure AD.<br> Los objetos no se combinan en el metaverso.
[Atributo Mail](active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Esta opción une a los usuarios y contactos si el atributo Mail tiene el mismo valor en bosques diferentes. Se recomienda utilizar esta opción cuando se han creado los contactos mediante GALSync.
[ObjectSID y msExchangeMasterAccountSID](active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest)|Esta opción une a un usuario habilitado en un bosque de cuentas con un usuario deshabilitado en un bosque de recursos de Exchange. Esto también se conoce como buzón vinculado en Exchange. Esta opción también se puede usar si solo usa Lync y Exchange no está presente en el bosque de recursos.
sAMAccountName y MailNickName|Esta opción combina atributos donde se espera que pueda encontrarse el identificador de inicio de sesión para el usuario.
Mi propio atributo|Esta opción le permite seleccionar su propio atributo. **Limitación:** asegúrese de seleccionar un atributo que ya exista en el metaverso. Si elige un atributo personalizado, que el asistente no podrá completarse.

- **Delimitador de origen**: el atributo sourceAnchor es un atributo que es inmutable durante la duración de un objeto de usuario. Es la clave principal que vincula el usuario local con el usuario de Azure AD. Puesto que no se puede cambiar el atributo, debe pensar en un atributo que sea adecuado usar. Un buen candidato es objectGUID. Este atributo no cambiará a menos que la cuenta de usuario se mueva entre bosques o dominios. En un entorno de varios bosque donde se muevan cuentas entre bosques, debe utilizarse otro atributo, como un atributo con el identificador de empleado. Atributos que hay que evitar son aquellos que podrían cambiar si combina una persona o cambian las asignaciones. No se pueden utilizar los atributos con un signo @, por lo que no se puede utilizar el correo electrónico y userPrincipalName. El atributo también distingue mayúsculas de minúsculas por lo que si mueve un objeto entre bosques, asegúrese de conservar las mayúsculas y minúsculas. Para los atributos binarios el valor está codificado en base 64, pero para otros tipos de atributo permanecerá en su estado sin codificar. En escenarios de federación y en algunas interfaces de Azure AD, este atributo se conoce también como immutableID. En los [conceptos de diseño](active-directory-aadconnect-design-concepts.md#sourceAnchor) encontrará más información sobre el delimitador de origen.

- **UserPrincipalName**: el atributo userPrincipalName es el atributo que los usuarios van a usar al iniciar sesión en Azure AD y Office 365. Los dominios utilizados, también conocidos como sufijo UPN, deben comprobarse en Azure AD antes de que se sincronicen los usuarios. Se recomienda encarecidamente mantener el atributo userPrincipalName predeterminado. Si este atributo no es enrutable y no se puede comprobar, se puede seleccionar otro atributo, por ejemplo, correo electrónico, como atributo que contiene el identificador de inicio de sesión. Este se conoce como **identificador alternativo**. El valor del atributo Alternate ID debe seguir el estándar RFC822. El atributo Alternate ID puede usarse como solución de inicio de sesión tanto con el inicio de sesión único (SSO) de contraseña como con el SSO de federación.

>[AZURE.WARNING] El uso de un identificador alternativo no es compatible con todas las cargas de trabajo de Office 365. Para obtener más información, vea [Configuración del identificador de inicio de sesión alternativo](https://technet.microsoft.com/library/dn659436.aspx).



### Filtrado de sincronización basado en grupos
La característica de filtrado en grupos le permite ejecutar una pequeña prueba piloto en un pequeño subconjunto de objetos creado en Azure AD y Office 365. Para utilizar esta característica, cree un grupo en Active Directory y agregue los usuarios y grupos que se deben sincronizar con Azure AD como miembros directos. Posteriormente puede agregar y quitar usuarios a este grupo para mantener la lista de objetos que deban estar presentes en Azure AD. Todos los objetos que quiere sincronizar deben ser un miembro directo del grupo. Esto incluirá usuarios, grupos, contactos y equipos o dispositivos. No se resolverán la pertenencia a grupos anidados ; un miembro del grupo solo incluirá el propio grupo y no sus miembros.

Para usar esta característica, en la ruta de acceso personalizada verá esta página: ![Filtrado de sincronización](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING] Esta característica está diseñada solo para admitir una implementación piloto y no debe utilizarse en una implementación de producción real.

En una implementación de producción completa va a ser difícil mantener un grupo único con todos los objetos para sincronizar. En su lugar, debería usar uno de los métodos de [Configuración del filtrado](active-directory-aadconnectsync-configure-filtering.md).

### Características opcionales

Esta pantalla le permite seleccionar las características opcionales para situaciones específicas. A continuación se ofrecen breves explicaciones de cada una de las características individuales.

![Características opcionales](./media/active-directory-aadconnect-get-started-custom/optional.png)

> [AZURE.WARNING] Si actualmente tiene activo DirSync o Azure AD Sync, no active ninguna de las características de escritura diferida en Azure AD Connect

Características opcionales | Descripción
-------------------    | ------------- |
Implementación híbrida de Exchange |La característica Implementación híbrida de Exchange permite la coexistencia de buzones de Exchange tanto en ámbito local como en Azure mediante la sincronización de un conjunto específico de [atributos](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) de Azure AD en el directorio local.
Aplicación Azure AD y filtro de atributos|Al habilitar la aplicación de Azure AD y el filtro de atributos, el conjunto de atributos sincronizados se puede adaptar a un conjunto específico de una página posterior del asistente. Esto abre dos páginas de configuración adicionales en el asistente.  
Sincronización de contraseñas | Puede habilitar esta opción si seleccionó la federación como solución de inicio de sesión. A continuación, la sincronización de contraseñas se puede usar como opción de copia de seguridad. Para obtener más información, consulte [Sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md).
Escritura diferida de contraseñas|Al habilitar la escritura diferida de contraseñas, los cambios de contraseña que se originan con Azure AD se volverán a escribir en su directorio local. Para obtener más información, consulte [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md).
Escritura diferida de grupos |Si utiliza la función **Grupos en Office 365**, puede tener estos grupos en Active Directory local como un grupo de distribución. Esta opción solo está disponible si dispone de Exchange en su Active Directory local. Para obtener más información, consulte [Escritura diferida de grupos](active-directory-aadconnect-feature-preview.md#group-writeback).
Escritura diferida de dispositivos | Permite realizar una escritura diferida de objetos de dispositivo en Azure AD para su Active Directory local para escenarios de acceso condicional. Para obtener más información, consulte [Habilitación de escritura diferida de dispositivos en Azure AD Connect](active-directory-aadconnect-get-started-custom-device-writeback.md).
Sincronización de atributos de las extensiones de directorios|Al habilitar la sincronización de atributos de las extensiones de directorios, los atributos adicionales especificados se sincronizarán con Azure AD. Para obtener más información, consulte [Extensiones de directorio](active-directory-aadconnect-feature-preview.md#directory-extensions).

### Aplicación Azure AD y filtro de atributos
Si desea limitar qué atributos sincronizar con Azure AD, empiece seleccionando los servicios que usa. Si configura esta página, cualquier servicio nuevo se debe seleccionar explícitamente volviendo a ejecutar el asistente para instalación.

![Características opcionales](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

En función de los servicios seleccionados en el paso anterior, esta página mostrará todos los atributos que se van a sincronizar. Esta lista es una combinación de todos los tipos de objeto que se están sincronizando. Si hay algunos atributos en concreto que no necesita sincronizar, puede anular la selección de los mismos. En la imagen siguiente se ha anulado la selección del atributo homePhone y no se sincronizará con Azure AD.

![Características opcionales](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

### Sincronización de atributos de Extensión de directorio (vista previa)
Con las extensiones de directorios puede extender el esquema en Azure AD con atributos personalizados agregados por la organización o con otros atributos de Active Directory. Para utilizar esta característica, seleccione "Sincronización de atributos de las extensiones de directorios" en la página "Características opcionales". Accederá a esta página donde podrá seleccionar los atributos adicionales.

![Filtrado de sincronización](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Para obtener más información, consulte [Extensiones de directorio](active-directory-aadconnect-feature-preview.md#directory-extensions).

## Configuración de federación con AD FS
La configuración de AD FS con Azure AD Connect es muy sencilla y solo se necesitan unos cuantos clics. Es necesario lo siguiente antes de la instalación.

- Un servidor Windows Server 2012 R2 para el servidor de federación con la administración remota habilitada.
- Un servidor Windows Server 2012 R2 para el servidor Proxy de aplicación web con la administración remota habilitada.
- Un certificado SSL para el nombre del servicio de federación que desea usar (por ejemplo, adfs.contoso.com)

### Creación de una nueva granja de AD FS o utilización de una granja de AD FS existente
Puede utilizar una granja de AD FS existente o crear una nueva granja de AD FS. Si decide crear una nueva, deberá proporcionar el certificado SSL. Si el certificado SSL está protegido mediante contraseña, se le pedirá que proporcione la contraseña.

![Granja de AD FS](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

**Nota:** si decide utilizar una granja de servidores existente de AD FS, omitirá algunas páginas y se le dirigirá directamente a la configuración de la relación de confianza entre la pantalla de AD FS y Azure AD.

### Especificación de los servidores de AD FS

Aquí deberá especificar los servidores específicos que desea instalar en AD FS. Puede agregar uno o más servidores según su necesidades de planeación de capacidad. Estos servidores deben estar unidos a un dominio de Active Directory antes de realizar esta configuración. Se recomienda instalar a un único servidor de AD FS para las implementaciones piloto y de prueba e implementar servidores adicionales mediante la apertura de Azure AD Connect y la implementación de AD FS en servidores adicionales para satisfacer las necesidades de escalabilidad.


> [AZURE.NOTE] Asegúrese de que todos los servidores están unidos a un dominio de AD antes de realizar esta configuración.

![Servidores de AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png)



### Especificación de los servidores Proxy de aplicación web
Aquí deberá especificar los servidores específicos que desee como servidores Proxy de aplicación web. El servidor Proxy de aplicación web se implementa en la red perimetral (a través de extranet) y admite solicitudes de autenticación desde la extranet. Puede agregar uno o más servidores según su necesidades de planeación de capacidad. Se recomienda instalar a un único servidor Proxy de aplicación web para las implementaciones piloto y de prueba e implementar servidores adicionales mediante la apertura de Azure AD Connect e implementar el servidor Proxy de aplicación web en servidores adicionales. Normalmente, se recomienda tener un número equivalente de servidores proxy para cumplir con la autenticación de la intranet.


> [AZURE.NOTE]
- Si la cuenta que está usando para instalar Azure AD Connect no es una cuenta de administración local en los servidores de AD FS, se le pedirán las credenciales de una cuenta con permisos suficientes. - Asegúrese de que hay conectividad HTTP/HTTPS entre el servidor de Azure AD Connect y el servidor Proxy de aplicación web antes de configurar este paso. - Además, asegúrese de que hay conectividad HTTP/HTTPS entre el Servidor de aplicaciones web y el servidor de AD FS para permitir que fluyan las solicitudes de autenticación.


![Aplicación web](./media/active-directory-aadconnect-get-started-custom/adfs3.png)


Se le pedirá que escriba las credenciales para que el Servidor de aplicaciones web pueda establecer una conexión segura con el servidor de AD FS. Estas credenciales deben ser un administrador local en el servidor de AD FS.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)


### Especificación de la cuenta de servicio para el servicio AD FS
El servicio de AD FS requiere una cuenta de servicio de dominio para autenticar usuarios y la información de usuario de búsqueda en Active Directory. Puede admitir dos tipos de cuentas de servicio:

- **Cuentas de servicio administradas de grupo**: se trata de un tipo de cuenta de servicio que se introdujo en Servicio de dominio de Active Directory con Windows Server 2012. Este tipo de cuenta proporciona servicios como AD FS para utilizar una única cuenta sin necesidad de actualizar la contraseña de la cuenta de forma periódica. Utilice esta opción si ya tiene controladores de dominio de Windows Server 2012 en el dominio al que pertenecerán los servidores de AD FS.
- **Cuenta de usuario de dominio**: en este tipo de cuenta deberá proporcionar una contraseña y actualizarla regularmente cuando cambia la contraseña. Utilice esta opción solo si no tiene controladores de dominio de Windows Server 2012 en el dominio al que pertenecerán los servidores de AD FS.

Azure AD Connect creará automáticamente la cuenta de servicio administrada de grupo si ha iniciado sesión como administrador de dominio.

![Cuenta de servicio de AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png)


### Selección del dominio de Azure AD que desee federar
Esta configuración se utiliza para configurar la relación de federación entre AD FS y Azure AD. Configura a AD FS para que emitir tokens de seguridad en Azure AD y configura Azure AD para confiar en los tokens de esta instancia de AD FS específica. Esta página solo permitirá configurar un único dominio la primera vez. Puede configurar dominios adicionales en cualquier momento si abre de nuevo Azure AD Connect y realiza esta tarea.


![Dominio de Azure AD](./media/active-directory-aadconnect-get-started-custom/adfs6.png)


### Realización de tareas adicionales para completar la configuración de federación
Las siguientes tareas adicionales deben estar completadas para finalizar la configuración de federación.

- Configure registros DNS para el nombre de servicio de federación de AD FS (por ejemplo, adfs.contoso.com) para la intranet (servidor DNS interno) y la extranet (DNS público a través de su registrador de dominios). Para el registro DNS de la intranet, asegúrese de que usa registros A y no registros CNAME. Esto es necesario para que funcione correctamente la autenticación de Windows desde la máquina unida al dominio.
- Si va a implementar más de un servidor de AD FS o servidor Proxy de aplicación web, asegúrese de que ha configurado el equilibrador de carga y que los registros DNS para el nombre de servicio de federación de AD FS (por ejemplo, adfs.contoso.com) indican el equilibrador de carga.
- Para que la autenticación integrada de Windows funcione para las aplicaciones del explorador que usan Internet Explorer en la intranet, asegúrese de que se agrega el nombre de servicio de federación de AD FS (por ejemplo, adfs.contoso.com) a la zona de intranet en Internet Explorer. Esto se puede controlar mediante la directiva de grupo e implementarlo en todos los equipos unidos a un dominio.


### Configuración opcional en el servicio de AD FS
Puede personalizar la imagen de logotipo y la ilustración para las páginas de inicio de sesión de AD FS si inicia sesión en AD FS y usa PSH para realizar esta configuración.

	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}

## Configurar y comprobar páginas
En esta página realmente se realizará la configuración.

### Modo provisional
Con el modo provisional es posible el proceso de instalación de un nuevo servidor de sincronización en paralelo con un servidor existente. Solo se admite para tener un servidor de sincronización conectado a un directorio en la nube. Pero si deseamos mover desde otro servidor, por ejemplo, un servidor que esté ejecutando DirSync, puede habilitar Azure AD Connect en modo provisional. Cuando se habilite, el motor de sincronización importará y sincronizará los datos de la forma habitual, pero no exportará nada a Azure AD y desactivará la sincronización de contraseñas y la escritura diferida de contraseñas.

![Filtrado de sincronización](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)


En modo provisional, es posible realizar los cambios necesarios en el motor de sincronización y revisar lo que se va a exportar. Cuando la configuración esté correcta, vuelva a ejecutar al Asistente para la instalación y deshabilite el modo provisional. Esto permitirá que los datos se exporten a Azure AD. Asegúrese de deshabilitar al otro servidor al mismo tiempo para que solo un servidor esté exportando activamente.

 Para obtener más información, consulte [Modo provisional](active-directory-aadconnectsync-operations.md#staging-mode).

### Comprobación de la configuración de la federación

Azure AD Connect comprobará la configuración de DNS al hacer clic en el botón Comprobar.

![Complete](./media/active-directory-aadconnect-get-started-custom/adfs7.png)


Además, realice los pasos de comprobación siguientes:

- Valide el inicio de sesión del explorador desde una máquina unida a un dominio de Internet Explorer desde la intranet: conéctese a https://myapps.microsoft.com y compruebe el inicio de sesión con su cuenta conectada.
- Valide el inicio de sesión del explorador desde cualquier dispositivo de la extranet: en un equipo doméstico o un dispositivo móvil, conéctese a https://myapps.microsoft.com y proporcione la credencial del identificador de inicio de sesión y contraseña.
- Valide el inicio de sesión de cliente enriquecido: conéctese a https://testconnectivity.microsoft.com, elija la pestaña 'Office 365' y elija 'Prueba de inicio de sesión único de Office 365'.


## Pasos siguientes
Una vez completada la instalación, cierre la sesión e inicie de sesión de nuevo en Windows antes de usar el Administrador del servicio de sincronización o el Editor de reglas de sincronización.

Ahora que ha instalado Azure AD Connect, puede [comprobar la instalación y asignar licencias](active-directory-aadconnect-whats-next.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->