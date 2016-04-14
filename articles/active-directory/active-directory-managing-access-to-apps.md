<properties
  pageTitle="Administración del acceso a aplicaciones con Azure AD | Microsoft Azure"
  description="Describe cómo Azure Active Directory permite a las organizaciones especificar las aplicaciones a las que cada usuario tiene acceso."
  services="active-directory"
  documentationCenter=""
  authors="msStevenPo"
  manager="stevenpo"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="02/09/2016"
  ms.author="stevenpo"/>


# Administración del acceso a las aplicaciones

El acceso continuo a las aplicaciones, la evaluación del uso y la generación de informes siguen siendo un desafío después de que una aplicación se integra en el sistema de identidad de su organización. En muchos casos, el administrador de TI o el departamento de soporte técnico deben asumir un papel activo en la administración del acceso a las aplicaciones. En ocasiones, la asignación la realiza un equipo de TI general o departamental. En ocasiones, se pretende que la decisión de asignación se delegue en el responsable de la toma de decisiones, lo que requiere su aprobación antes de que TI realice la asignación. Otras organizaciones invierten en integración con un sistema automatizado existente de administración de identidades y acceso, como Control de acceso basado en rol (RBAC) o Control de acceso basado en atributos (ABAC). Tanto la integración como el desarrollo de reglas tienden a ser procesos especializados y caros. La supervisión o la generación de informes en cualquier enfoque de administración requieren su propia inversión aparte que resulta costosa y compleja.

## ¿Cómo ayuda Azure Active Directory?

 Azure AD proporciona una exhaustiva administración del acceso para aplicaciones configuradas, lo que permite a las organizaciones lograr fácilmente las directivas de acceso adecuadas: desde asignación automática y basada en atributos (escenarios ABAC o RBAC) hasta la delegación y la administración de administradores. Con Azure AD pueden conseguir fácilmente directivas complejas al combinar varios modelos de administración para una sola aplicación, e incluso puede volver a usar las reglas de administración entre aplicaciones con los mismos destinatarios.

 - [Adición de aplicaciones nuevas o existentes](active-directory-sso-integrate-saas-apps.md)


 La asignación de aplicaciones de Azure AD se centra en dos modos de asignación principales:

- **Asignación individual**: un administrador de TI con permisos de administrador global de directorios puede seleccionar cuentas de usuario individuales y concederles acceso a la aplicación.
- **Asignación basada en grupos (solo en la versión de Azure AD de pago)**: un administrador de TI con permisos de administrador global de directorios puede asignar un grupo a la aplicación. El acceso de usuarios específicos se determina en función de si son miembros del grupo en el momento de intentar el acceso a la aplicación. En este modo, un administrador puede crear en la práctica una regla de asignación que diga "cualquier miembro actual del grupo asignado tiene acceso a la aplicación". Con esta opción de asignación, los administradores pueden beneficiarse de cualquier opción de administración de grupos de Azure AD, como grupos dinámicos basados en atributos, grupos externos del sistema (por ejemplo, AD local o Workday), administrador o grupos administrados de autoservicio. Un único grupo se puede asignar fácilmente a varias aplicaciones, lo que garantiza que las aplicaciones con afinidad de asignación puedan compartir reglas de asignación y así reducirse la complejidad de la administración en general. Tenga en cuenta que no se admiten las pertenencias a grupos anidadas para la asignación basada en grupos a aplicaciones en este momento.

Con estos dos modos de asignación, los administradores pueden conseguir cualquier enfoque deseable de administración de asignaciones.

Con Azure AD, los informes de asignación y uso están totalmente integrados, lo que permite a los administradores informar fácilmente sobre el estado y los errores de asignación, e incluso del uso.

## Asignación de aplicaciones complejas con Azure AD

Considere una aplicación como Salesforce. En muchas organizaciones, Salesforce se usa principalmente en las organizaciones de ventas y marketing. A menudo, los miembros del equipo de marketing tienen acceso privilegiado a Salesforce, mientras que los miembros del equipo de ventas tienen acceso limitado. En muchos casos, una amplia población de trabajadores de la información tiene acceso restringido a la aplicación. Las excepciones a estas reglas complican el asunto. Con frecuencia, es prerrogativa de los equipos líderes de marketing o de ventas conceder acceso a un usuario o cambiar su rol con independencia de estas reglas genéricas.

Con Azure AD, las aplicaciones como Salesforce se pueden configurar previamente para el inicio de sesión único (SSO) y el aprovisionamiento automatizado. Después de configurar la aplicación, un administrador puede realizar la acción puntual de crear y asignar los grupos adecuados. En este ejemplo, un administrador puede ejecutar las siguientes asignaciones:

- Se pueden definir grupos de atributos para representar automáticamente a todos los miembros de los equipos de ventas y marketing con atributos como departamento o rol:
    - Todos los miembros de los grupos de marketing se asignarían al rol "marketing" en Salesforce.
    - Todos los miembros de los grupos de equipo de ventas se asignarían al rol "ventas" en Salesforce. Para afinar más, se podrían usar varios grupos que representan los equipos de ventas regionales asignados a diferentes roles de Salesforce.
- Para habilitar el mecanismo de excepciones, se podría crear un grupo de autoservicio para cada rol. Por ejemplo, el grupo de "excepción de marketing de Salesforce" se puede crear como un grupo de autoservicio. El grupo se puede asignar al rol de marketing de Salesforce y se puede convertir en propietarios al equipo de liderazgo de marketing. Los miembros del equipo de liderazgo de marketing podrían agregar o quitar usuarios, establecer una directiva de unión o incluso aprobar o rechazar solicitudes de unión de usuarios individuales. Esto es posible mediante una experiencia adecuada del trabajador de la información en la que los propietarios o miembros no necesitan aprendizaje especializado.

En este caso, todos los usuarios asignados se aprovisionarían automáticamente a Salesforce, ya que como se agregan a diferentes grupos, su asignación de roles se actualizaría en Salesforce. Los usuarios podrán detectar Salesforce y acceder a esta aplicación mediante el panel de acceso a las aplicaciones de Microsoft, los clientes web de Office, o incluso navegando a su página de inicio de sesión organizativa de Salesforce. Los administradores podrán ver fácilmente el estado de uso y asignación mediante los informes de Azure AD.

 Los administradores pueden emplear el [acceso condicional de Azure AD](active-directory-conditional-access.md) para establecer directivas de acceso para roles específicos. Estas directivas pueden incluir si se permite el acceso fuera del entorno corporativo e incluso los requisitos de Multi-Factor Authentication o de los dispositivo para obtener acceso en diversos casos.

## ¿Cómo puedo comenzar?

En primer lugar, si aún no usa Azure AD y es un administrador de TI:

 - [¡Pruébelo!](https://azure.microsoft.com/trial/get-started-active-directory/) Puede suscribirse a una evaluación gratuita de 30 días hoy e implementar la primera solución en la nube en menos de 5 minutos desde este vínculo.

Entre las características de Azure AD que permiten el uso compartido de las cuentas se incluyen las siguientes:

- [Asignación de grupos](active-directory-accessmanagement-self-service-group-management.md)
- Agregar aplicaciones a Azure AD
- Introducción a la asignación
- P+F sobre la asignación de aplicaciones
- [Panel/informes de uso de aplicaciones](active-directory-passwords-get-insights.md)

## ¿Dónde puedo obtener más información?

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Protección de aplicaciones con acceso condicional](active-directory-conditional-access.md)
- [Administración de grupos de autoservicio/SSAA](active-directory-accessmanagement-self-service-group-management.md)

<!---HONumber=AcomDC_0211_2016-->