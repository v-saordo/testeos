
<properties
	pageTitle="Solución de problemas de pertenencia dinámica para grupos| Microsoft Azure"
	description="Un tema que muestra consejos para la solución de problemas para la pertenencia dinámica para grupos en Azure AD."
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


# Solución de problemas de pertenencias dinámicas a grupos

| Síntoma | Acción |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| He configurado una regla en un grupo pero las pertenencias no se actualizan en el grupo. | Compruebe que la opción **Habilitar la administración de grupos delegados** está establecida en Sí en la pestaña Configuración de directorio. Esta opción solo se verá si ha iniciado sesión como un usuario a quien se ha asignado una licencia de Active Directory Premium de Azure. Compruebe los valores de atributos de usuario en la regla: ¿hay usuarios que cumplen la regla? |
| He configurado una regla, pero ahora se han quitado los miembros existentes de la regla. | Este es el comportamiento esperado. Los miembros existentes del grupo se quitan cuando una regla se habilita o se cambia. Los usuarios devueltos tras la evaluación de la regla se agregan como miembros al grupo. |
| No veo los cambios en la pertenencia al instante cuando agrego o cambio una regla, ¿por qué pasa esto? | La evaluación de pertenencia dedicada se realiza periódicamente en un proceso asincrónico en segundo plano. La duración del proceso viene determinada por el número de usuarios del directorio y el tamaño del grupo creado como resultado de la regla. Normalmente, los directorios con un número pequeño de usuarios verán los cambios en la pertenencia al grupo en unos pocos minutos. Los directorios con un gran número de usuarios pueden tardar 30 minutos o más en completarse. |

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->