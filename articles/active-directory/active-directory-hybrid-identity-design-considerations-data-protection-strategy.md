<properties
	pageTitle="Consideraciones sobre el diseño de identidades híbridas de Azure Active Directory: definir la estrategia de protección de datos| Microsoft Azure"
	description="Definirá la estrategia de protección de datos para que una solución de identidad híbrida cumpla los requisitos empresariales que se definen."
	documentationCenter=""
	services="active-directory"
	authors="yuridio"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="yuridio"/>


# Definición de una estrategia de protección de datos para soluciones de identidad híbrida

En esta tarea, se definirá la estrategia de protección de datos para que una solución de identidad híbrida cumpla los requisitos empresariales que se definen en:

- [Determinación de los requisitos de protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) 
- [Determinación de los requisitos de administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Determinación de los requisitos de control de acceso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Determinación de los requisitos de respuesta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## Definición de las opciones de protección de datos
Como se explicó en [Determinación de los requisitos de sincronización de directorios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD puede sincronizar con los Servicios de dominio de Active Directory (AD DS) ubicados localmente. Esta integración permite a las organizaciones sacar provecho de Azure AD para comprobar las credenciales de los usuarios cuando intentan obtener acceso a los recursos corporativos. Esto se puede hacer en dos escenarios: datos en reposo locales y en la nube. El acceso a los datos de Azure AD requiere autenticación del usuario mediante un servicio de token de seguridad (STS).

Una vez autenticado, el nombre principal de usuario (UPN) se lee del token de autenticación y se determinan tanto la partición replicada como el contenedor que corresponde al dominio del usuario. El sistema de autorización usa la información sobre la existencia, estado habilitado y rol del usuario se usa para determinar si el acceso solicitado al inquilino de destino está autorizado para este usuario en esta sesión. Determinadas acciones autorizados (específicamente, crear usuario, restablecimiento de contraseña) crean una traza de auditoría que un administrador de inquilinos puede usar para administrar los esfuerzos de cumplimiento o investigaciones.

Es posible que mover datos desde un centro de datos local a almacenamiento de Azure a través de una conexión a Internet no siempre sea factibles debido al volumen de datos, a la disponibilidad del ancho de banda o a otras consideraciones. El [servicio de importación y exportación de almacenamiento de Azure](../storage/storage-import-export-service.md) ofrece una opción basada en hardware para colocar o recuperar grandes volúmenes de datos en el almacenamiento de blobs. Le permite enviar unidades de disco duro [cifradas mediante BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) directamente a un centro de datos de Azure donde operadores en la nube cargarán el contenido en su cuenta de almacenamiento o pueden descargar los datos de Azure a sus unidades de disco para que vuelvan a usted. En este proceso solo se aceptan discos cifrados (para lo que se usa una clave BitLocker que genera el propio servicio durante la configuración del trabajo). La clave de BitLocker se proporciona a Azure por separado, con lo que se proporciona el uso compartido de claves fuera de banda.

Dado que los datos en tránsito pueden tener lugar en distintos escenarios, también es relevante saber que Microsoft Azure usa [redes virtuales](https://azure.microsoft.com/documentation/services/virtual-network/) para aislar el tráfico de los inquilinos entre ellos, para lo que emplea medidas como firewalls de nivel de invitado y host, filtrado de paquetes IP, bloqueo de puertos y puntos de conexión HTTPS. Sin embargo, también se cifran la mayoría de las comunicaciones internas de Azure, incluida las de infraestructura con infraestructura e infraestructura con cliente (local). Otro escenario importante es las comunicaciones dentro de los centros de datos de Azure; Microsoft administra redes para asegurarse de que ninguna máquina virtual puede suplantar o escuchar en la dirección IP de otra. TLS/SSL se usa al acceder a almacenamiento de Azure o a bases de datos SQL, o bien al conectarse a Servicios en la nube. En ese caso, el administrador de clientes es el responsable de obtener un certificado TLS/SSL e implementarlo en la infraestructura de sus inquilinos. El movimiento del tráfico de datos entre máquinas virtuales en la misma implementación o entre inquilinos en una sola implementación mediante Red virtual de Microsoft Azure se puede proteger mediante protocolos de comunicación cifrada como HTTPS o SSL/TLS, entre otros.

En función de cómo respondiera a las preguntas de [Determinación de los requisitos de protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), podría determinar cómo desea proteger los datos y de qué forma le ayudará la solución de identidad híbrida a realizar dicha tarea. La tabla muestra las opciones compatibles con Azure que están disponibles para cada escenario de protección de datos.


| Opciones de protección de datos | En reposo en la nube | En reposo localmente | En tránsito |
|---------------------------------|----------------------|---------------------|------------|
| Cifrado BitLocker de unidades | X | X | |
| SQL Server para cifrar bases de datos | X | X | |
| Cifrado de VM a VM | | | X |
| SSL/TLS | | | X |
| VPN | | | X |


>[AZURE.NOTE]
Para obtener más información acerca de las certificaciones con las que es compatible cada servicio de Azure, consulte [Cumplimiento por característica](https://azure.microsoft.com/support/trust-center/services/) en [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) . Dado que las opciones de protección de datos usan un enfoque multicapa, la comparación entre ellas no se puede aplicar a esta tarea. Asegúrese de que saca provecho de todas las opciones disponibles para cada estado en que van a estar los datos.

## Definición de las opciones de administración de contenido
Una ventaja de usar Azure AD para administrar una infraestructura de identidad híbrida es que el proceso es completamente transparente desde la perspectiva del usuario final. El usuario intentará acceder a un recurso compartido, pero el recurso requiere autenticación, por lo que el usuario tendrá que enviar una solicitud de autenticación a Azure AD para obtener el token y acceso al recurso. Todo este proceso se produce en segundo plano, sin interacción del usuario. También se puede conceder permiso a un [grupo](active-directory-manage-groups.md#getting-started-with-access-management) de usuarios para que puedan realizar determinadas acciones comunes.

Las organizaciones a las que preocupa la privacidad de los datos requieren la clasificación de datos para su solución. Si su infraestructura local actual ya usa la clasificación de datos, es posible sacar provecho de Azure AD como repositorio principal para la identidad del usuario. Una herramienta común que se usa localmente para la clasificación de datos se denomina [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) para Windows Server 2012 R2. Esta herramienta puede ayudar a identificar, clasificar y proteger los datos de los servidores de archivos de su nube privada. También es posible sacar provecho de la [clasificación de archivos automática](https://technet.microsoft.com/library/hh831672.aspx) en Windows Server 2012 para lograrlo.

Si su organización no tiene la clasificación de los datos implementada, pero necesita proteger archivos confidenciales sin agregar nuevos servidores localmente, puede usar el [servicio Azure Rights Management](https://technet.microsoft.com/library/JJ585026.aspx) de Microsoft. Azure RMS usa las directivas de cifrado, identidad y autorización para ayudarle a proteger sus archivos y mensajes de correo electrónico, y funciona en varios dispositivos (teléfonos, tabletas y PC). Dado que Azure RMS es un servicio en la nube, no hay necesidad de configurar explícitamente relaciones de confianza con otras organizaciones para poder compartir contenido protegido con ellas. Si ya tienen un directorio de Office 365 o Azure AD, la colaboración entre organizaciones se admite automáticamente. También se pueden sincronizar solo los atributos de directorio que Azure RMS necesita para admitir una identidad común para las cuentas de Active Directory locales, mediante el uso de los servicios de sincronización de Azure Active Directory (sincronización de AAD) o Azure AD Connect.

Una parte fundamental de la administración de contenido es saber quién tiene acceso a cada recurso; por lo tanto, es importante que la solución de administración de identidades tenga una gran capacidad de registro. Azure AD proporciona registro durante 30 días. Dicho registro incluye:

- Cambios en la pertenencia a un rol (por ejemplo, un usuario agregado al rol de administrador global)
- Actualizaciones de credenciales (por ejemplo, cambios de contraseña)
- Administración de dominios (por ejemplo, comprobar un dominio personalizado o eliminar un dominio)
- Adición o eliminación de aplicaciones
- Administración de usuarios (por ejemplo, agregar, eliminar o actualizar un usuario)
- Adición o eliminación de licencias

>[AZURE.NOTE]
Para obtener más información acerca de las capacidades de registro de Azure, consulte el documento [Microsoft Azure Security and Audit Log Management](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) (Seguridad de Microsoft Azure y administración del registro de auditoría). En función de cómo respondiera a las preguntas de [Determinación de los requisitos de administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), podría determinar cómo desea administrar el contenido en una solución de identidad híbrida. Aunque todas las opciones que se exponen en la tabla 6 pueden integrarse con Azure AD, es importante definir la que sea más adecuada para sus necesidades empresariales.

| Opciones de administración de contenido | Ventajas | Desventajas |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralizada localmente (servidor de Active Directory Rights Management) | Control total sobre la infraestructura de servidor responsable de clasificar los datos <br> Capacidad integrada en Windows Server, sin necesidad de licencias adicionales o suscripción <br> Puede integrarse con Azure AD en un escenario híbrido <br> Admite las capacidades de Information Rights Management (IRM) en Microsoft Online Services como Exchange Online y SharePoint Online, así como Office 365 <br> Admite productos de servidor de Microsoft locales, como Exchange Server , SharePoint Server y servidores de archivo que ejecutan Windows Server e infraestructura de clasificación de archivos (FCI). | Mayor mantenimiento (estar al día de las actualizaciones, configuración y potenciales actualizaciones), ya que TI posee el servidor <br> Requiere una infraestructura de servidor local<br> No saca provecho a las capacidades de Azure de forma nativa |
| Centralizada en la nube (Azure RMS) | Más fácil de administrar, en comparación con la solución local <br> Puede integrarse con AD DS en un escenario híbrido <br> Completamente integrada con Azure AD <br> No requiere un servidor local para implementar el servicio <br> Admite productos de servidor de Microsoft locales, como Exchange Server , SharePoint Server y servidores de archivo que ejecutan Windows Server e infraestructura de clasificación de archivos (FCI) <br> TI, puede tener un control completo sobre la clave de su inquilino con capacidad de BYOK. | Su organización debe tener una suscripción de nube que admita RMS <br> Su organización debe tener un directorio de Azure AD para admitir la autenticación de usuario de RMS |
| Híbrida (Azure RMS integrado con Active Directory Rights Management Server local) | Este escenario acumula las ventajas de ambos, local centralizado y en la nube. | Su organización debe tener una suscripción de nube que admita RMS <br> Su organización debe tener un directorio de Azure AD para admitir la autenticación de usuarios para RMS <br> Requiere una conexión entre el servicio en la nube de Azure y la infraestructura local |

## Definición de opciones de control de acceso
Al sacar provecho de las capacidades de autenticación, autorización y control de acceso disponibles en Azure AD, podrá permitir a su empresa usar un repositorio central de identidades, al mismo tiempo que permite que los usuarios y asociados usen el inicio de sesión único (SSO), como se muestra en la figura siguiente:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Administración centralizada e integración total con otros directorios

Azure Active Directory ofrece un inicio de sesión único a miles de aplicaciones SaaS y aplicaciones web locales. Para obtener más información sobre los SSO de terceros que Microsoft probó, consulte el artículo [Lista de compatibilidad de federación de Azure Active Directory: proveedores de identidades de terceros que se pueden usar para implementar el inicio de sesión único ](https://msdn.microsoft.com/library/azure/jj679342.aspx). Esta capacidad permite a la organización implementar varios escenarios de B2B sin perder el control de la administración de identidades y acceso. Sin embargo, durante el proceso de diseño de B2B es importante conocer el método de autenticación que usará el asociado y validarlo, en caso de que sea compatible con Azure. Actualmente, estos son los métodos compatibles con Azure AD:

- Lenguaje de marcado de aserción de seguridad (SAML)
- OAuth
- Kerberos
- Tokens
- Certificados


>[AZURE.NOTE]
Para obtener más información sobre cada protocolo y sus capacidades en Azure, consulte [Protocolos de autenticación de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx). Gracias a la compatibilidad con Azure AD, las aplicaciones empresariales pueden usar la misma experiencia de autenticación fácil de Servicios móviles para permitir a los empleados iniciar sesión en sus aplicaciones móviles con sus credenciales corporativas de Active Directory. Con esta característica, Azure AD se admite como proveedor de identidades en Servicios móviles, junto con los restantes proveedores de identidades que ya son compatibles (entre los que incluyen cuentas de Microsoft, identificador de Facebook, identificador de Google e identificador de Twitter). Si las aplicaciones locales usan la credencial del usuario ubicada en el AD DS de la compañía, el acceso de los asociados y los usuarios que provienen de la nube debe ser transparente. Puede administrar el control de acceso condicional del usuario a las aplicaciones web (basadas en la nube), la API web, servicios en la nube de Microsoft, aplicaciones SaaS de terceros y las aplicaciones cliente nativas (móviles), y obtener las ventajas de seguridad, auditoría, generación de informes en un mismo lugar. Sin embargo, se recomienda validarlo en un entorno que no sea de producción o con una cantidad limitada de usuarios.

>[AZURE.TIP]
Es importante mencionar que Azure AD no tiene una directiva de grupo, mientras que AD DS la tiene. Para aplicar una directiva para los dispositivos, es preciso tener una solución de administración de dispositivos móviles como [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).

Una vez que el usuario se autentica mediante Azure AD, es importante evaluar el nivel de acceso que tendrá el usuario. El nivel de acceso que tendrá el usuario en un recurso puede variar; aunque Azure AD puede agregar una capa de seguridad adicional mediante el control del acceso a algunos recursos, también es preciso tener en cuenta que el propio recurso también puede tener su propia lista de control de acceso independiente, como el control de acceso para los archivos ubicados en un servidor de archivos. La siguiente ilustración resume los niveles de control de acceso que puede haber en un escenario híbrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Cada una de las interacciones del diagrama que se muestra en la Ilustración X representa un escenario de control de acceso que se pueden cubrir con Azure AD. A continuación encontrará una descripción de cada escenario:

1. Acceso condicional a las aplicaciones hospedadas localmente: puede usar dispositivos registrados con directivas de acceso en las aplicaciones configuradas para usar AD FS con Windows Server 2012 R2. Para obtener más información sobre cómo configurar el acceso condicional localmente, consulte [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](active-directory-conditional-access-on-premises-setup.md). 
2. Control de acceso al Portal de administración de Azure: Azure también tiene la capacidad de controlar el acceso al Portal de administración mediante el uso de RBAC (Control de acceso basado en roles). Este método permite a la empresa restringir la cantidad de operaciones que una persona puede hacer una vez que tenga acceso al Portal de administración de Azure. Si se usa RBAC para controlar el acceso al portal, los administradores de TI pueden delegar el acceso con los siguientes enfoques de administración de acceso:

 - Asignación de roles basada en grupo: se puede asignar acceso a los grupos de Azure AD que se puedan sincronizar desde Active Directory local. Esto le permite aprovechar las inversiones que su organización realizó en herramientas y procesos para administrar grupos. También puede usar la característica de administración de grupos delegados de Azure AD Premium.
 - Sacar provecho a los roles integrados de Azure: se pueden usar tres roles (Propietario, Colaborador y Lector) para asegurarse de que los usuarios y grupos tienen permiso para realizar solo las tareas que necesitan para sus trabajos. 
 - Acceso granular a los recursos: puede asignar roles a los usuarios y grupos para una suscripción concreta, un grupo de recursos o un recurso individual de Azure como un sitio web o una base de datos. De esta forma, puede asegurarse de que los usuarios tengan acceso a todos los recursos que necesitan y ningún acceso a los que no necesitan administrar.

 >[AZURE.NOTE]
  Para obtener más información detallada sobre esta capacidad, consulte [Control de acceso basado en rol en el Portal de vista previa de Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/). Los desarrolladores que compilan aplicaciones y desean personalizar el control de acceso a ellas, también pueden usar los roles de aplicación de Azure AD para la autorización. Para usar esta capacidad, revise el [ejemplo WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) de creación de una aplicación.

3. Acceso condicional para aplicaciones de Office 365 con Microsoft Intune: los administradores de TI pueden aprovisionar directivas de dispositivos de acceso condicional para proteger los recursos corporativos, al tiempo que permiten que los trabajadores de la información con dispositivos compatibles tengan acceso a los servicios. Para obtener más información, vea [Directivas de dispositivos de acceso condicional para servicios de Office 365](active-directory-conditional-access-device-policies.md).

4. Acceso condicional para aplicaciones SaaS: [esta característica](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) permite configurar reglas de acceso a Multi-Factor Authentication por aplicación y la capacidad de bloquear el acceso a los usuarios que no estén en una red de confianza. La regla de autenticación multifactor puede aplicarse a todos los usuarios que están asignados a la aplicación o solo a los que pertenecen a grupos de seguridad especificados. Los usuarios pueden excluirse del requisito de autenticación multifactor si van a tener acceso a la aplicación desde una dirección IP que se encuentre dentro de la red de la organización.

Dado que las opciones de control de acceso usan un enfoque multicapa, la comparación entre ellas no se puede aplicar a esta tarea. Asegúrese de que saque provecho a todas las opciones disponibles para cada escenario que requiera que controle el acceso a sus recursos.

## Definición de las opciones de respuesta ante incidentes
Azure AD puede ayudar a TI a identificar potenciales riesgos de seguridad en el entorno mediante la supervisión de la actividad del usuario, TI puede sacar provecho a la capacidad de informes de acceso y uso de Azure AD para obtener visibilidad de la integridad y seguridad del directorio de la organización. Con esta información, un administrador de TI puede determinar mejor dónde puede haber posibles riesgos de seguridad, con el fin de que puedan planear adecuadamente la mitigación de dichos riesgos. La [suscripción a Azure AD Premium](articles/active-directory-get-started-premium.md) tiene un conjunto de informes de seguridad que pueden permitir al departamento de TI obtener esta información. Los [informes de Azure AD](active-directory-view-access-usage-reports.md) se clasifican como se muestra a continuación:

- **Informes de anomalías**: contienen eventos de inicio de sesión que se consideran anómalos. Nuestro objetivo es que sea consciente de dicha actividad y que pueda tomar una decisión sobre si un evento es sospechoso. 
- **Informe de aplicaciones integradas**: proporciona información sobre cómo se usan en la organización las aplicaciones en la nube. Azure Active Directory ofrece integración con miles de aplicaciones en la nube. 
- **Informes de errores**: indican errores que se pueden producir al aprovisionar cuentas a aplicaciones externas.
- **Informes específicos del usuario**: muestran los datos de actividad de dispositivo o de inicio de sesión de un usuario concreto.
- **Registros de actividad**: contienen un registro de todos los eventos auditados en las últimas 24 horas, los últimos 7 días o los últimos 30 días, así como los cambios en la actividad del grupo y la actividad de registro y de restablecimiento de contraseña.

>[AZURE.TIP]
Otro informe que también puede ayudar al equipo de respuesta ante incidentes que trabaja en un caso es el informe de [usuarios con credenciales perdidas](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx). Este informe muestra las coincidencias entre la lista de credenciales perdidas y su inquilino.

Otros importantes informes integrados en Azure AD que pueden usarse durante la investigación de la respuesta ante un incidentes son:

- **Actividad de restablecimiento de contraseña**: proporciona al administrador información sobre si el restablecimiento de contraseña se usa activamente en la organización.
- **Actividad de registro de restablecimiento de contraseña**: proporciona información sobre los usuarios que registraron sus métodos para restablecer la contraseña y los métodos que seleccionados.
- **Actividad de grupo**: proporciona un historial de los cambios en el grupo (por ejemplo, los usuarios agregados o eliminados) que se iniciaron en el Panel de acceso.

Además de la capacidad de generación de informes principales disponible en Azure AD Premium, de la que se puede sacar provecho durante un proceso de investigación de respuesta ante incidentes, TI también puede sacar provecho del informe de auditoría para obtener información como:

- Cambios en la pertenencia a un rol (por ejemplo, un usuario agregado al rol de administrador global)
- Actualizaciones de credenciales (por ejemplo, cambios de contraseña)
- Administración de dominios (por ejemplo, comprobar un dominio personalizado o eliminar un dominio)
- Adición o eliminación de aplicaciones
- Administración de usuarios (por ejemplo, agregar, eliminar o actualizar un usuario)
- Adición o eliminación de licencias

Dado que las opciones para la respuesta ante incidentes usan un enfoque multicapa, la comparación entre ellas no se puede aplicar a esta tarea. Asegúrese de que saca provecho de todas las opciones disponibles para cada escenario que requiere que se use la capacidad de generación de informes de Azure AD como parte del proceso de respuesta ante incidentes de su compañía.

## Pasos siguientes
[Determinación de las tareas de administración de identidades híbridas](active-directory-hybrid-identity-design-considerations-hybridId-management-tasks.md)


## Otras referencias
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0128_2016-->