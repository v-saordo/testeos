<properties
	pageTitle="Vista previa de Servicios de dominio de Azure Active Directory: preguntas más frecuentes | Microsoft Azure"
	description="Preguntas más frecuentes sobre Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Vista previa de Servicios de dominio de Active Directory: preguntas más frecuentes

Esta página responde a las preguntas más frecuentes acerca de la vista previa de Servicios de dominio de Azure Active Directory. Siga comprobando si hay actualizaciones.

### Guía de solución de problemas
Consulte nuestra [Guía de solución de problemas](active-directory-ds-troubleshooting.md) para obtener soluciones para los problemas habituales detectados al configurar o administrar los Servicios de dominio de Azure AD.


### Configuración

#### ¿Se pueden crear varios dominios para un único directorio de Azure AD?
No. Solo se puede crear un único dominio atendido por Servicios de dominio de Azure AD para un único directorio de Azure AD.

#### ¿Puedo hacer que Servicios de dominio de Azure AD esté disponible en varias redes virtuales dentro de mi suscripción?
El propio servicio no admite directamente este escenario. Servicios de dominio de Azure AD está disponible solo en una red virtual en cada momento. Sin embargo, puede configurar la conectividad entre varias redes virtuales con el fin de exponer Servicios de dominio de Azure AD a otras redes virtuales. Este artículo describe cómo puede [conectar redes virtuales en Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### ¿Puedo habilitar Servicios de dominio de Azure AD mediante PowerShell?
PowerShell y la implementación automatizada de Servicios de dominio de Azure AD no están disponible actualmente.

#### ¿Está Servicios de dominio de Azure AD disponible en el nuevo portal de Azure?
No. Los Servicios de dominio de Azure AD pueden configurarse únicamente en el Portal de administración de Azure anterior (es decir, https://manage.windowsazure.com). Esperamos ampliar la compatibilidad para el nuevo Portal de administración de Microsoft Azure (es decir, https://portal.azure.com) en el futuro.

#### ¿Puedo agregar controladores de dominio a un dominio administrado de Servicios de dominio de Azure AD?
No. El dominio de Servicios de dominio de Azure AD es un dominio administrado. No es necesario aprovisionar, configurar o administrar controladores de dominio para este dominio, Microsoft proporciona estas actividades de administración como un servicio. Por lo tanto, no podrá agregar controladores de dominio adicionales (ni de lectura y escritura ni de solo lectura) para el dominio administrado.


### Administración y operaciones

#### He habilitado Servicios de dominio de Azure AD ¿Qué cuenta de usuario utilizo para unir máquinas a este dominio?
Las cuentas de usuario que ha agregado al grupo administrativo (es decir, ‘Administradores de controladores de dominio de AAD’) podrían unir máquinas al domino. Además, a los usuarios de este grupo se les concede acceso mediante Escritorio remoto a las máquinas que se han unido al dominio.

#### ¿Puedo tener privilegios de administrador de dominio para el dominio proporcionado por Servicios de dominio de Azure AD?
No. Puesto que se trata de un servicio administrado, no se le proporcionarán privilegios administrativos en el dominio. Esto significa que tanto los privilegios de ‘administrador de dominio’ como de ‘administrador empresarial’ no estarán disponibles dentro del dominio. A los grupos de administradores de dominio o administradores empresariales existentes dentro del directorio de Azure AD tampoco se les concederá privilegios de administrador de dominio o empresarial en el dominio.

#### ¿Puedo modificar pertenencias a grupos mediante LDAP u otras herramientas administrativas de AD en dominios proporcionados por Servicios de dominio de Azure AD?
No. Las pertenencias a grupos en dominios ofrecidos por Servicios de dominio de Azure AD no se pueden modificar. Lo mismo se aplica para los atributos de usuario. Sin embargo, puede cambiar las pertenencias a grupos o los atributos de usuario en Azure AD o en el dominio local. Dichos cambios se sincronizarán automáticamente con Servicios de dominio de Azure AD.

#### ¿Puedo extender el esquema del dominio proporcionado por Servicios de dominio de Azure AD?
No. Microsoft administra el esquema del dominio administrado. No se admiten extensiones de esquema en los Servicios de dominio de Azure AD.


### Facturación y disponibilidad

#### ¿Es un servicio de pago?
El servicio está disponible a un precio reducido especial durante el período de vista previa pública. La facturación comenzará a practicarse a precio completo una vez que el servicio esté disponible con carácter general (GA). Consulte la página de precios para obtener más información.

#### ¿Hay una versión de prueba gratuita para el servicio?
Este servicio se incluye en evaluación gratuita de Azure. Puede suscribirse a un [mes de evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

#### ¿Puedo obtener Servicios de dominio de Azure AD como parte de Enterprise Mobility Suite (EMS)?
No, Servicios de dominio de Azure AD es un servicio de Azure de pago por uso y no forma parte de EMS. Servicios de dominio de Azure AD está disponibles para todos los SKU de Azure AD (es decir, Gratis, Básico y Premium) y se facturan por hora en función del uso.

#### ¿En qué regiones de Azure está disponible el servicio?
Consulte nuestra [página de regiones](active-directory-ds-regions.md) para obtener una lista de las regiones de Azure donde están disponibles los Servicios de dominio de Azure AD.

#### ¿Cuándo está disponible Servicios de dominio de Azure AD con carácter general?
Actualmente no podemos compartir escalas de tiempo sobre cuándo estará disponible el servicio con carácter general.

<!---HONumber=AcomDC_0128_2016-->