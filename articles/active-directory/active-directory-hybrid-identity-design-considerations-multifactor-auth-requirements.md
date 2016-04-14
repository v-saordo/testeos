<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: determinación de los requisitos de autenticación multifactor| Microsoft Azure"
	description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."
	documentationCenter=""
	services="active-directory"
	authors="femila"
	manager="billmath"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="12/07/2015"
	ms.author="billmath"/>

# Determinación de los requisitos de autenticación multifactor para la solución de identidad híbrida

En este mundo de la movilidad en el que los usuarios acceden a datos y aplicaciones en la nube desde cualquier dispositivo, la seguridad de la información se ha convertido en algo primordial. Todos los días hay un nuevo titular sobre una infracción de la seguridad. Aunque no existe ninguna garantía contra tales infracciones, la autenticación multifactor ofrece una capa de seguridad adicional para ayudar a evitarlas. Para comenzar, evalúe los requisitos de las organizaciones de autenticación multifactor. Es decir, que intenta proteger la organización. Esta evaluación es importante para definir los requisitos técnicos para configurar y habilitar a los usuarios de las organizaciones para la autenticación multifactor.

>[AZURE.NOTE]
Si no está familiarizado con MFA y cómo funciona, se recomienda encarecidamente que lea el artículo [¿Qué es Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md) antes de continuar leyendo esta sección.

Asegúrese de responder a las siguientes preguntas:

- ¿Intenta su empresa proteger aplicaciones de Microsoft? 
- ¿Cómo se publican estas aplicaciones?
- ¿Proporciona su empresa acceso remoto para permitir a los empleados el acceso a aplicaciones locales?

En caso afirmativo, ¿qué tipo de acceso remoto? También debe evaluar dónde se ubicarán los usuarios que están teniendo acceso a estas aplicaciones. Esta evaluación es otro paso importante para definir la estrategia adecuada de autenticación multifactor. Asegúrese de responder a las siguientes preguntas:

- ¿Dónde se van a encontrar los usuarios?
- ¿Pueden estar en cualquier parte?
- ¿Desea su empresa establecer restricciones de acuerdo con la ubicación del usuario?

Una vez que comprenda estos requisitos, es importante evaluar también los requisitos del usuario de autenticación multifactor. Esta evaluación es importante porque definirá los requisitos para implementar la autenticación multifactor. Asegúrese de responder a las siguientes preguntas:

- ¿Están los usuarios familiarizados con la autenticación multifactor?
- ¿Algunos usuarios deben proporcionar autenticación adicional?  
 - En caso afirmativo, ¿siempre, cuando proceden de redes externas o cuando acceden a aplicaciones específicas, o en otras condiciones?
- ¿Necesitarán los usuarios entrenamiento sobre cómo configurar e implementar la autenticación multifactor?
- ¿Cuáles son los principales escenarios en los que su empresa quiere habilitar la autenticación multifactor para sus usuarios?

Después de responder a las preguntas anteriores, podrá comprender si la autenticación multifactor ya está implementada de forma local. Esta evaluación es importante para definir los requisitos técnicos para configurar y habilitar a los usuarios de las organizaciones para la autenticación multifactor. Asegúrese de responder a las siguientes preguntas:

- ¿Necesita su empresa proteger las cuentas con privilegios con MFA?
- ¿Necesita su empresa habilitar MFA en ciertas aplicaciones por motivos de cumplimiento?
- ¿Necesita habilitar su empresa MFA para todos los usuarios elegidos de estas aplicaciones o solo para los administradores?
- ¿Necesita tiene MFA siempre habilitado o solo cuando los usuarios se registran fuera de la red corporativa?


## Pasos siguientes
[Definición de una estrategia de adopción de identidad híbrida](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## Consulte también
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0204_2016-->