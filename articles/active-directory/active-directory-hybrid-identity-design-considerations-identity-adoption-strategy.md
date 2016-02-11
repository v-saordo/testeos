<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: definición de una estrategia de adopción de identidades híbridas| Microsoft Azure"
	description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="billmath"/>


# Definición de una estrategia de adopción de identidad híbrida

En esta tarea, se definirá la estrategia de adopción de identidades híbridas para que una solución de identidad híbrida cumpla los requisitos empresariales que se trataron en:

- [Determinación de las necesidades empresariales](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Determinación de los requisitos de sincronización de directorios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Determinación de los requisitos de autenticación multifactor](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## Definición de una estrategia de necesidades empresariales
La primera tarea consiste en determinar las necesidades empresariales de las organizaciones. Dicha tarea puede ser muy amplia y se puede producir un arrastramiento del alcance si no se tiene cuidado. Al principio debe ser simple, pero recuerde siempre planear un diseño que acomode y facilite la realización de cambios en el futuro. Independientemente de que sea un diseño sencillo o extremadamente complejo, Azure Active Directory es la plataforma de Microsoft Identity que admite Office 365, Microsoft Online Services y aplicaciones habilitadas para la nube.

## Definición de una estrategia de integración
Microsoft tiene tres escenarios de integración principales que son las identidades de nube, las identidades sincronizadas y las identidades federadas. Debe planear la adopción de una de estas estrategias de integración. La estrategia que elija puede variar y las decisiones que tome a la hora de elegir una de ellas pueden incluir: el tipo de experiencia de usuario que se desea proporcionar, si se tiene alguna de las infraestructuras existentes ya establecida y cuál es el más rentable.
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Los escenarios definidos en la ilustración anterior son:

- **Identidades en la nube**: identidades que existen únicamente en la nube. En el caso de Azure AD, residirían específicamente en su directorio de Azure AD.
- **Sincronizada**: identidades que existen localmente y en la nube. Con Azure AD Connect, los usuarios se crean o se conectan con las cuentas de Azure AD existentes. El hash de la contraseña del usuario se sincroniza desde el entorno local a la nube en lo que se denomina un hash de contraseña. Cuando se usa sincronizado, lo único que debe tenerse en cuenta es que si hay algún usuario deshabilitado en el entorno local, pueden pasar hasta tres horas hasta que el estado de dicha cuenta se muestre en Azure AD. Esto se debe al intervalo de tiempo de la sincronización.
- **Federada**: estas identidades existen tanto localmente como en la nube. Con Azure AD Connect, los usuarios se crean o se conectan con las cuentas de Azure AD existentes.  
 
>[AZURE.NOTE]
Para obtener más información sobre las opciones de sincronización, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

La tabla siguiente le ayudará a determinar las ventajas y desventajas de cada una de las estrategias siguientes:

| Estrategia | Ventajas | Desventajas |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identidades en la nube** | Fáciles de administrar para organizaciones pequeñas. <br>No es preciso instalar nada localmente, no se necesita hardware adicional<br>Se deshabilitan fácilmente si el usuario abandona la compañía. | Los usuarios tendrán que iniciar sesión al acceder a las cargas de trabajo en la nube <br> Las contraseñas puede ser las mismas, o no, para las entidades identidades de nube y locales. |
| **Sincronizada** | La contraseña local autenticará tanto los directorios en la nube como los locales. <br>Más fácil de administrar para organizaciones pequeñas, medianas o grandes. <br>Los usuarios pueden tener inicio de sesión único (SSO) para algunos recursos. <br> El método de sincronización preferido por Microsoft. <br> Más fácil de administrar. | Algunos clientes pueden ser reacios a sincronizar sus directorios con la nube debido a las directivas de la compañía. |
| **Federada** | Los usuarios pueden tener inicio de sesión único (SSO). <br>Si algún usuario abandona la compañía, se puede deshabilitar y revocar el acceso inmediatamente.<br> Admite escenarios avanzados que no se puede lograr con la estrategia Sincronizada. | Más pasos para instalar y configurar.<br> Mayor mantenimiento.<br> Puede requerir hardware adicional para la infraestructura de STS.<br> Puede requerir hardware adicional para instalar el servidor de federación. Se requiere software adicional si se usa AD FS. <br>Requieren una configuración amplia para SSO. <br>Punto de error crítico si el servidor de federación está fuera de servicio, los usuarios no podrán autenticarse. |

### Experiencia del cliente
La estrategia que se use determinará la experiencia de inicio de sesión del usuario. Las siguientes tablas proporcionan información sobre la experiencia de inicio de sesión que deben esperar los usuarios. Tenga en cuenta que no todos los proveedores de identidades federadas admiten SSO en todos los escenarios.

**Aplicaciones de red privada y conectadas a un dominio**:
 

| | Identidad sincronizada | Identidad federada |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Exploradores web | Autenticación basada en formularios | inicio de sesión único, a veces es preciso especificar la identificación de la organización |
| Outlook | Se piden credenciales | Se piden credenciales |
| Skype Empresarial (Lync) | Se piden credenciales | inicio de sesión único para Lync, credenciales solicitadas para Exchange |
| SkyDrive Pro | Se piden credenciales | inicio de sesión único |
| Suscripción a Office Pro Plus | Se piden credenciales | inicio de sesión único |

**Orígenes externos o que no son de confianza**:

| | Identidad sincronizada | Identidad federada |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Exploradores web | Autenticación basada en formularios | Autenticación basada en formularios |
| Outlook, Skype Empresarial (Lync), Skydrive Pro, suscripción de Office| Se piden credenciales | Se piden credenciales |
| Exchange ActiveSync | Se piden credenciales | inicio de sesión único para Lync, credenciales solicitadas para Exchange |
| Aplicaciones móviles | Se piden credenciales | Se piden credenciales |

Si determinó en la tarea 1 que tiene un IdP de terceros o que va a usarlo para proporcionar la federación con Azure AD, es preciso que tenga en cuenta las siguientes capacidades admitidas: - Todos los proveedores de SAML 2.0 que sean compatibles con el perfil SP-Lite pueden admitir la autenticación en Azure AD y en las aplicaciones asociadas - Admite la autenticación pasiva, lo que facilita la autenticación en OWA, SPO, etc. - Se pueden admitir clientes de Exchange Online mediante el perfil de cliente mejorado (ECP) de SAML 2.0.

También debe conocer las capacidades que no estarán disponibles:

- Sin la compatibilidad con WS-Trust o Federación, los restantes clientes activos se interrumpirán
 - Esto significa que no hay ningún cliente de Lync, cliente de OneDrive, suscripción de Office u Office Mobile antes de 2016 de Office
- La transición de Office a autenticación pasiva les permitirá admitir los IdP de SAML 2.0, pero seguirá siendo compatible cliente a cliente


>[AZURE.NOTE]
Para ver la lista más actualizada, consulte el artículo http://aka.ms/ssoproviders.

## Definición de una estrategia de sincronización
En esta tarea definirá las herramientas que se usarán para sincronizar los datos locales de la organización con la nube y la topología que debe usar. Dado que la mayoría de las organizaciones usan Active Directory, se ofrece información detallada sobre el uso de Azure AD Connect para abordar las cuestiones anteriores. Para los entornos que carecen de Active Directory, hay información sobre la forma de usar FIM 2010 R2 o MIM 2016 como ayuda para planear esta estrategia. Sin embargo, las versiones futuras de Azure AD Connect admitirán directorios LDAP, por lo que, en función de la escala de tiempo, esta información puede resultar de ayuda.

###Herramientas de sincronización
A lo largo de los años existieron varias herramientas de sincronización y se usaron en distintos escenarios. En la actualidad, Azure AD Connect es la herramienta preferida para todos los escenarios compatibles. AAD Sync y DirSync se siguen usando, e incluso puede que se encuentren en su entorno ahora.

>[AZURE.NOTE]
Para obtener la información más reciente acerca de las capacidades admitidas de cada herramienta, consulte el artículo [Comparación de las herramientas para la integración de directorios](active-directory-aadconnect-get-started-tools-comparison.md).

### Topologías admitidas
Al definir una estrategia de sincronización, es preciso determinar la topología que se usa. En función de la información que se determinó en el paso 2 se puede especificar qué topología es la adecuada. El bosque único, la única topología de Azure AD, es la más común y consta de un único bosque de Active Directory y una única instancia de Azure AD. Dicha topología se va a usar en la mayoría de los escenarios y es la que se espera que haya cuando se usa la instalación rápida de Azure AD Connect, como se muestra en la ilustración siguiente.
 
![](./media/hybrid-id-design-considerations/single-forest.png) Escenario de bosque único Es muy común que las organizaciones grandes, e incluso las pequeñas, tengan varios bosques, como se muestra en la Ilustración 5.

>[AZURE.NOTE]
Para obtener más información acerca de las diferentes topologías locales y de Azure AD con sincronización con Azure AD Connect, consulte el artículo [Topologías de Azure AD Connect](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png)

Escenario de bosques múltiples

En este caso, debe tenerse en cuenta la topología única de Azure AD de bosques múltiples si se dan los siguientes factores:

- Los usuarios tienen una sola identidad en todos los bosques (esto se describe con mayor detalle en la sección sobre la identificación de usuarios de forma única, que encontrará más adelante).
- El usuario se autentica en el bosque en el que se encuentra su identidad
- Tanto el UPN como el delimitador de origen (identificador inmutable) procederán de este bosque
- Azure AD Connect tiene acceso a todos los bosques: esto significa que no es preciso conectarlo a ningún dominio y que puede colocarse en una DMZ, en caso de que así se facilite la operación.
- Los usuarios tienen un solo buzón.
- El bosque que aloja el buzón de un usuario tiene la mejor calidad de datos para los atributos que se ven en la lista global de direcciones (GAL) de Exchange.
- Si el usuario no tiene ningún buzón, puede usarse cualquiera de los bosques para aportar estos valores.
- Si tiene un buzón vinculado, hay también otra cuenta en otro bosque que se usa para el inicio de sesión.

>[AZURE.NOTE]
Los objetos que existen en tanto localmente como en la nube están conectados "conectados" mediante un identificador único. En el contexto de sincronización de directorios, este identificador único se conoce como SourceAnchor. En el contexto del inicio de sesión único, se conoce como ImmutableId. Para obtener más información sobre el uso de SourceAnchor, consulte [Conceptos de diseño de Azure AD Connect](active-directory-aadconnect-design-concepts.md#sourceanchor).

Si no se dan los factores anteriores y tiene más de una cuenta activa o más de un buzón, Azure AD Connect elegirá uno e ignorará el resto. Si tiene buzones vinculados, pero no hay otras cuentas, dichas cuentas no se exportarán a Azure AD y el usuario no será un miembro de ningún grupo. Esto es diferente de cómo lo era en el pasado con DirSync, ahora se admiten mejor los escenarios con bosques múltiples. En la ilustración siguiente, se muestra un escenario con bosques múltiples.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png)
 
**Escenario de Azure AD con bosques múltiples**

Se recomienda tener un solo directorio por organización en Azure AD, pero se admite que se mantenga una relación 1:1 entre un servidor de sincronización de Azure AD Connect y un directorio de Azure AD. En cada instancia de Azure AD, será precisa una instalación de Azure AD Connect. Además, a causa de su diseño, Azure AD está aislado, por lo que los usuarios de una instancia de Azure AD no podrán ver los usuarios de otra.

No solo es posible, sino que también se admite la conexión de una instancia local de Active Directory a varios directorios de Azure AD, como se muestra en la ilustración siguiente:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png)
 

**Escenario de filtrado de bosque único**

Para realizar esta operación, es preciso que se den los siguientes factores:

- Los servidores de sincronización de Azure AD Connect deben estar configurados para el filtrado, de modo que cada uno tenga un conjunto de objetos mutuamente excluyente. Esto se realiza, por ejemplo, mediante la definición del ámbito de cada servidor en un dominio o unidad organizativa concretos.
- Un dominio DNS solo se puede registrar en un único directorio de Azure AD, con el fin de que los UPN de los usuarios del entorno local de AD deban usar también espacios de nombres independientes.
- Los usuarios de una instancia de Azure AD solo podrán ver los usuarios de su instancia. No podrán ver los usuarios de las restantes instancias
- Solo uno de los directorios de Azure AD puede habilitar la implementación híbrida de Exchange con el entorno local de Active Directory.
- La exclusividad mutua también se aplica a la reescritura. Esto hace que algunas de las características de la reescritura no sean compatibles con esta topología, ya que estas asumen una única configuración local. Esto incluye:
 - Reescritura de grupos con la configuración predeterminada
 - Reescritura de dispositivos


Tenga en cuenta que los siguientes elementos no se admiten y no deben elegirse como implementación:

- No se admite tener varios servidores de sincronización de Azure AD Connect conectados al mismo directorio de Azure AD, aunque estén configurados para sincronizar conjuntos de objetos mutuamente excluyentes.
- No se admite la sincronización del mismo usuario con varios directorios de Azure AD. 
- Tampoco se admite realizar un cambio de configuración para que los usuarios de un directorio de Azure AD aparezcan como contactos en otro directorio de Azure AD. 
- Asimismo, no se admite la modificación de Azure AD Connect Sync para conectarse a varios directorios de Azure AD.
- Los directorios de Azure AD están aislados por diseño. No se admite el cambio de la configuración de Azure AD Connect Sync para leer datos de otro directorio de Azure AD en un intento por crear una GAL común y unificada entre los directorios. Tampoco se admite la exportación de usuarios como contactos a otro directorio AD local mediante Azure AD Connect Sync.


>[AZURE.NOTE]
Si una organización restringe la conexión de equipos de la red a Internet, este artículo muestra los puntos de conexión (FQDN, intervalos de direcciones IPv4 e IPv6) que se deben incluir en las lista de permitidos salientes y en la zona de sitios de confianza de Internet Explorer de los equipos cliente para asegurarse de que los equipos pueden usar Office 365 correctamente. Para obtener más información, consulte [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=es-ES&rs=es-ES&ad=US).

## Definición de una estrategia de Multi-Factor Authentication
En esta tarea definirá la estrategia de Multi-Factor Authentication que va a usar. Azure Multi-Factor Authentication está disponible en dos versiones diferentes. Una está basada en la nube y la otra es local con el servidor MFA de Azure. Basándose en la evaluación que realizó anteriormente, puede determinar qué solución es la correcta para su estrategia. Use la tabla siguiente para determinar qué opción de diseño cumple mejor el requisito de seguridad de su compañía:

Opciones de diseño multifactor:

| Activo que se protege | MFA en la nube | MFA local |
|---------------------------------------------------------------|------------------|----------------|
| Aplicaciones de Microsoft | yes | yes |
| Aplicaciones de SaaS en la galería de aplicaciones | yes | yes |
| Aplicaciones de IIS que se publican a través del proxy de aplicación de Azure AD | yes | yes |
| Aplicaciones IIS que no se publican a través del proxy de aplicación de Azure AD | no | yes |
| Acceso remoto como VPN o RDG | no | yes |

Aunque puede que eligiera una solución para su estrategia, será preciso que use la evaluación anterior en donde se encuentren los usuarios. Esto puede provocar un cambio de solución. La tabla siguiente puede ayudarle a decidirse:

| Ubicación del usuario | Opción de diseño preferida |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory | Multi-Factor Authentication en la nube |
| Azure AD y AD local mediante la federación con AD FS | Ambos |
| Azure AD y AD local con Azure AD Connect, sin sincronización de contraseñas | Ambos |
| Azure AD y AD local con Azure AD Connect, con sincronización de contraseñas | Ambos |
| AD local | Servidor Multi-Factor Authentication |

>[AZURE.NOTE]
También debe asegurarse de que la opción de diseño de Multi-Factor Authentication que seleccionó admite las características necesarias para el diseño. Para obtener más información, consulte [Selección de la solución de seguridad multifactor más adecuada](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure)

## Proveedor de Multi-Factor Authentication
La autenticación multifactor está disponible de forma predeterminada para los administradores globales que tienen un inquilino de Azure Active Directory. Sin embargo, si desea ampliar Multi-Factor Authentication a todos los usuarios o que los administradores globales puedan aprovechar características como el portal de administración, saludos personalizados e informes, debe adquirir y configurar un proveedor de Multi-Factor Authentication.

>[AZURE.NOTE]
También debe asegurarse de que la opción de diseño de Multi-Factor Authentication que seleccionó admite las características necesarias para el diseño.

##Pasos siguientes
[Determinación de los requisitos de protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## Consulte también
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0128_2016-->