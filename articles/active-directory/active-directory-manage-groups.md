<properties
	pageTitle="Administración del acceso a los recursos con grupos de Azure Active Directory | Microsoft Azure"
	description="Cómo usar grupos en Azure Active Directory para la administración del acceso a recursos y aplicaciones de nube y locales."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="curtand"/>


# Administración del acceso a los recursos con grupos de Azure Active Directory

Azure Active Directory (Azure AD) es una completa solución de administración de identidades y acceso que proporciona un sólido conjunto de capacidades para administrar el acceso a aplicaciones locales y en la nube y recursos de Microsoft Online Services como Office 365 y todo un mundo de aplicaciones SaaS que no son de Microsoft.


> [AZURE.NOTE] Para usar Azure Active Directory, necesita una cuenta de Azure. Si aún no tiene ninguna, puede [registrarse para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).


Dentro de Azure Active Directory, una de las principales características es la capacidad para administrar el acceso a los recursos. Estos recursos pueden formar parte del directorio, como en el caso de los permisos para administrar objetos a través de roles en el directorio, o recursos externos al directorio, como las aplicaciones SaaS, servicios de Azure y sitios de SharePoint o publicados en modo local. Existen cuatro formas de asignar a un usuario derechos de acceso a un recurso:


1\. Asignación directa

El propietario de un recurso puede entonces asignar directamente usuarios al mismo.

2\. Pertenencia a grupos

El propietario de un recurso puede asignarle un grupo y, al hacerlo, conceder acceso al recurso a los miembros de ese grupo. El propietario del grupo puede administrar entonces la pertenencia dicho grupo. El propietario del recurso delega efectivamente en el propietario del grupo el permiso para asignar a usuarios a sus recursos.

3\. Uso de reglas

El propietario del recurso puede usar una regla para indicar a qué usuarios se les debe asignar acceso a un recurso. El resultado de la regla depende de los atributos utilizados en dicha regla y de sus valores para usuarios específicos; si se hace así, el propietario del recurso delega efectivamente los derechos de administración de acceso en el origen de autoridad de los atributos usados en la regla. Tenga en cuenta que el propietario del recurso sigue pudiendo administrar la regla propiamente dicha y que determina qué atributos y valores proporcionan acceso a su recurso.

4\. Autoridad externa

El acceso a un recurso se deriva de un origen externo, por ejemplo, un grupo que se sincroniza desde un origen de autoridad, como un directorio local o una aplicación de SaaS como WorkDay. El propietario del recurso asigna al grupo para proporcionar acceso al recurso y el origen externo administra a los miembros del grupo.

  ![Información general del diagrama de administración del acceso](./media/active-directory-access-management-groups/access-management-overview.png)


## Vea un vídeo que explica la administración del acceso

Puede ver un breve vídeo que explica más detalladamente este tema:

[Azure AD: Introducción a las pertenencias dinámicas para grupos](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups)

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## ¿Cómo funciona la administración de acceso en Azure Active Directory?
En el centro de la solución de administración de acceso de Azure Active Directory se encuentra el grupo de seguridad. Usar un grupo de seguridad para administrar el acceso a los recursos es un paradigma conocido, lo que permite contar con una forma flexible y fácil de entender de proporcionar acceso a un recurso para el grupo de usuarios previsto. El propietario de los recursos (o el administrador del directorio) puede asignar un grupo para proporcionar determinados derechos de acceso a los recursos que posee. Los miembros del grupo recibirán el derecho de acceso y el propietario del recurso puede delegar en otra persona el derecho de administración de la lista de miembros de un grupo como, por ejemplo, un administrador de departamento o un administrador de soporte técnico.

![Diagrama de administración de acceso de Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png) El propietario de un grupo también puede hacer que ese grupo tenga la posibilidad de realizar solicitudes de autoservicio. De esta forma, un usuario final puede buscar y encontrar el grupo, y solicitar unirse a él pidiendo efectivamente permiso para tener acceso a los recursos que se administran a través del grupo. El propietario del grupo puede configurar el grupo para que las solicitudes de pertenencia se aprueben automáticamente o requieran la aprobación por parte del propietario del grupo. Cuando un usuario realiza una solicitud para unirse a un grupo, la solicitud de pertenencia se reenvía a los propietarios del grupo. Si uno de los propietarios aprueba la solicitud, el usuario solicitante recibe una notificación y se une al grupo. Si uno de los propietarios rechaza la solicitud, el usuario solicitante recibe una notificación pero no se une al grupo.


## Introducción a administración de accesos
¿Ya está listo para comenzar? Pruebe algunas de las tareas básicas que se pueden hacer con los grupos de Azure AD. Utilice estas capacidades para proporcionar acceso especializado a distintos grupos de personas para los diferentes recursos de la organización. A continuación se incluye una lista de los primeros pasos básicos.


* [Crear una regla sencilla para configurar pertenencias dinámicas a un grupo](active-directory-accessmanagement-simplerulegroup.md)

* [Uso de un grupo para administrar el acceso a las aplicaciones SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Puesta a disposición de un grupo para el autoservicio del usuario final](active-directory-accessmanagement-self-service-group-management.md)

* [Sincronización de un grupo local con Azure mediante Azure AD Connect](active-directory-aadconnect.md)

* [Administración de propietarios de un grupo](active-directory-accessmanagement-managing-group-owners.md)


## Pasos siguientes para administración de accesos
Ahora que ha comprendido los conceptos básicos de la administración de accesos, presentamos algunas capacidades avanzadas adicionales disponibles en Azure Active Directory para administrar el acceso a sus aplicaciones y recursos.

* [Uso de una regla sencilla para crear un grupo](active-directory-accessmanagement-simplerulegroup.md)

* [Uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Administración de grupos de seguridad en Azure Active Directory](active-directory-accessmanagement-manage-groups.md)

* [Configuración de grupos dedicados en Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md)


## Más información
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

* [Referencia de la API Graph para grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

<!---HONumber=AcomDC_0128_2016-->