<properties
	pageTitle="Sincronización de Azure AD Connect: comprender y personalizar la sincronización | Microsoft Azure"
	description="Se explica cómo funciona la sincronización de Azure AD Connect y cómo personalizarla."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="markusvi;andkjell"/>


# Sincronización de Azure AD Connect: comprender y personalizar la sincronización
Los servicios de sincronización de Azure Active Directory Connect (Azure AD Connect Sync) son un componente principal de Azure AD Connect que se encarga de todas las operaciones que están relacionadas con la sincronización de datos de identidad entre el entorno local y Azure AD en la nube. Sincronización de Azure AD Connect es el sucesor de DirSync, Sincronización de Azure AD y Forefront Identity Manager con Conector Azure Active Directory configurado.

Este tema es la principal referencia para **sincronización de Azure AD Connect** (también denominado **motor de sincronización**) y muestra vínculos a todos los otros temas relacionados con él. Para obtener vínculos para Azure AD Connect, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## Temas de sincronización de Azure AD Connect

| Tema. | ¿Qué aspectos cubre y cuándo debe leerlo? |
| ----- | ----- |
| **Fundamentos de la sincronización de Azure AD Connect** ||
| [Comprensión de la arquitectura](active-directory-aadconnectsync-understanding-architecture.md) | Para aquellos que son nuevos en el motor de sincronización y quieren obtener información sobre la arquitectura y los términos empleados. |
| [Conceptos técnicos](active-directory-aadconnectsync-technical-concepts.md) | Una versión abreviada del tema de la arquitectura que explica brevemente los términos empleados. |
| [Topologías de Azure AD Connect](active-directory-aadconnect-topologies.md) | Describe las distintas topologías y escenarios que admite el motor de sincronización. |
| **Configuración personalizada** ||
| [Introducción a la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md)| Describe las reglas no incluidas y la configuración predeterminada. También describe cómo colaboran las reglas para que los escenarios no incluidos funcionen. |
| [Descripción de usuarios y contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continúa desde el tema anterior y describe cómo la configuración de los usuarios y contactos funciona conjuntamente, en particular en un entorno de bosques múltiples. |
| [Explicación de las expresiones declarativas de aprovisionamiento](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Explica en profundidad cómo funciona el modelo de configuración y la sintaxis del lenguaje de expresión. |
| [Prácticas recomendadas de cambio de la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Cuando conoce los detalles de los temas anteriores y necesita realizar cambios no incluidos en la configuración para trabajar con su escenario o sus necesidades específicas. |
| [Configuración del filtrado](active-directory-aadconnectsync-configure-filtering.md) | Describe las distintas opciones para limitar qué objetos se sincronizan con Azure AD y cómo configurar estos elementos paso a paso. |
| **Características** ||
| [Implementación de la sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) | Describe cómo funciona la sincronización de contraseñas, cómo implementarla y cómo utilizarla y solucionar problemas. |
| [Evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Describe la característica *evitar eliminaciones accidentales* y cómo configurarla. |
| **Operaciones** ||
| [Tareas y consideraciones operativas](active-directory-aadconnectsync-operations.md) | Describe las preocupaciones operativas, como la recuperación ante desastres. |
| **Más información y referencias** ||
| [Puertos](active-directory-aadconnect-ports.md) | Enumera los puertos que necesita abrir entre el motor de sincronización y los directorios locales y Azure AD. |
| [Atributos sincronizados con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Enumera todos los atributos que se sincronizan entre los AD locales y Azure AD. |
| [Referencia de funciones](active-directory-aadconnectsync-functions-reference.md) | Enumera todas las funciones disponibles en el aprovisionamiento declarativo. |

## Recursos adicionales

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->