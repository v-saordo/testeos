<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: determinación de los requisitos de identidad | Microsoft Azure"
	description="Identificar las necesidades empresariales de la compañía que le llevarán a definir los requisitos para el diseño de la identidad híbrida."
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
	ms.date="12/07/2015"
	ms.author="billmath"/>

# Determinación de los requisitos de identidad para la solución de identidad híbrida
El primer paso para diseñar una solución de identidad híbrida es determinar los requisitos de la organización que aprovechará esta solución. Un identidad híbrida se inicia como un rol de soporte (admite todas las demás soluciones de nube mediante la autenticación) y avanza proporcionando capacidades nuevas e interesantes que desbloquean nuevas cargas de trabajo para los usuarios. Estas cargas de trabajo o servicios que se quieran adoptar para los usuarios, determinan los requisitos para el diseño de la identidad híbrida. Estos servicios y cargas de trabajo tienen que aprovechar la identidad híbrida tanto a nivel local como en la nube.

Es necesario repasar estos aspectos claves del entorno empresarial para comprender cuáles son los requisitos en la actualidad, y qué tiene previsto la empresa para el futuro. Si no tiene visibilidad de la estrategia a largo plazo para el diseño de identidad híbrida, lo más probable es que la solución dejará de ser escalable a medida que el negocio crezca y sus necesidades cambien. El diagrama siguiente muestra un ejemplo de una arquitectura de identidad híbrida y las cargas de trabajo que se están desbloqueando para los usuarios. Esto no es más que un ejemplo de las nuevas posibilidades que se pueden desbloquear y proporcionar con una sólida estrategia de identidad híbrida.
 
Algunos componentes que forman parte de la arquitectura de identidad híbrida ![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## Determinación de las necesidades empresariales
Cada empresa tiene requisitos diferentes, incluso dentro de un mismo sector empresarial los requisitos reales suelen variar de una empresa a otra. Aún aprovechando las prácticas recomendadas del sector, al final son las necesidades empresariales específicas de la compañía las que priman a la hora de definir los requisitos para el diseño de identidad híbrida.

Para identificar las necesidades de su empresa asegúrese de que considera las respuestas a las siguientes preguntas:

- ¿Su empresa busca reducir los costos operativos de TI?
- ¿Su empresa busca proteger los activos de la nube (aplicaciones de SaaS, infraestructura)?
- ¿Su empresa quiere modernizar la infraestructura de TI?
  - ¿Son los usuarios más móviles y exigen de TI que se creen excepciones en la red perimetral para permitir diferentes tipos de tráfico y así poder tener acceso a recursos diferentes?
  - ¿Su empresa tiene aplicaciones heredadas que tienen que publicarse a esos usuarios modernos, pero que no son fáciles de volver a escribir?
  - ¿Su empresa necesita realizar todas estas tareas y tenerlo todo bajo control al mismo tiempo?
- ¿Su empresa busca proteger las identidades de los usuarios y reducir riesgos ofreciendo nuevas herramientas que aprovechen la experiencia que Microsoft Azure ofrece en seguridad local?
- ¿Su empresa intenta deshacerse de las temidas cuentas "externas" localmente, y quiere moverlas a la nube para que dejen de ser una amenaza latente dentro de su entorno local?

## Análisis de la infraestructura de identidad local
Ahora que ya tiene una idea con respecto a los requisitos empresariales de su compañía, tiene que evaluar su infraestructura de identidad local. Esta evaluación es importante para definir los requisitos técnicos con el fin de integrar la solución de identidad actual para el sistema de administración de identidades de nube. Asegúrese de responder a las siguientes preguntas:

- ¿Qué soluciones de autenticación y autorización usa su empresa localmente? 
- ¿Su empresa tiene actualmente servicios locales de sincronización?
- ¿Su empresa usa proveedores de identidades (IdP) de terceros?

También tiene que tener en cuenta los servicios en la nube que su compañía podría tener. Es muy importante realizar una evaluación para comprender la integración actual con modelos de SaaS, IaaS o PaaS en su entorno. Asegúrese de que responde a las preguntas siguientes durante esta evaluación: ¿Su empresa tiene una integración con un proveedor de servicios en la nube? En caso afirmativo ¿qué servicios se usan? ¿Está dicha integración actualmente en el entorno de producción o es un programa piloto?


>[AZURE.NOTE]Si no tiene una idea precisa de la asignación de todas las aplicaciones y servicios en la nube, puede usar la herramienta Cloud App Discovery. Esta herramienta puede proporcionar al departamento de TI visibilidad en la estructura empresarial de la organización y las aplicaciones de nube del consumidor. Gracias a esto, es más fácil que nunca detectar las zonas oscuras de TI en su empresa, con la obtención de información detallada sobre modelos de uso y los usuarios que acceden a las aplicaciones en la nube. Para obtener acceso a esta herramienta, acuda a [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## Evaluación de los requisitos de integración de identidades
A continuación, tiene que evaluar los requisitos de integración de identidades. Esta evaluación es importante para definir los requisitos técnicos en relación a cómo se autenticarán los usuarios, qué aspecto tendrá la presencia de la organización en la nube, cómo permitirá la organización la autorización y cómo va a ser la experiencia del usuario. Asegúrese de responder a las siguientes preguntas:

- ¿Su organización va a usar la federación, la autenticación estándar o ambas?
- ¿Es la federación un requisito? Debido a alguna de las siguientes causas:
 - SSO basado en Kerberos
 - Su empresa tiene una aplicación local (ya sea generada internamente o de terceros) que usa SAML o capacidades de federación similares.
 - MFA mediante tarjetas inteligentes. RSA SecurID, etc.
 - Reglas de acceso de cliente que abordan las preguntas siguientes:
     1. ¿Puedo bloquear todo acceso externo a Office 365 según la dirección IP del cliente?
     1. ¿Puedo bloquear todo acceso externo a Office 365, excepto para Exchange ActiveSync?
     1. ¿Puedo bloquear todo acceso externo a Office 365, excepto para las aplicaciones basadas en explorador (OWA, SPO)?
     1. ¿Puedo bloquear todo acceso externo a Office 365 para los miembros de grupos específicos de AD?
- Consideraciones de seguridad o auditoría
- Inversión ya existente en la autenticación federada
- ¿Qué nombre empleará la organización para nuestro dominio en la nube?
- ¿La organización tiene un dominio personalizado?
    1. ¿Es dicho dominio público y fácilmente comprobable a través de DNS?
    1. Si no es así, ¿tiene un dominio público que puede usarse para registrar un UPN alternativo en AD?
- ¿Son los identificadores de usuario coherentes para la representación en la nube? 
- ¿Tiene la organización aplicaciones que requieren la integración con servicios en la nube?
- ¿Tiene la organización varios dominios y en todos ellos se usará la autenticación estándar o federada?

## Evaluación de las aplicaciones que se ejecutan en el entorno
Ahora que tiene una idea de la infraestructura local y en la nube, tiene que evaluar las aplicaciones que se ejecutan en estos entornos. Esta evaluación es importante para definir los requisitos técnicos para integrar estas aplicaciones en el sistema de administración de identidad en la nube. Asegúrese de responder a las siguientes preguntas:

- ¿Dónde residirán las aplicaciones?
- ¿Los usuarios van a acceder a aplicaciones locales? ¿Y en la nube? ¿O en ambos entornos?
- ¿Existen planes para tomar las cargas de trabajo de aplicación existentes y moverlas a la nube?
- ¿Existen planes para desarrollar nuevas aplicaciones que residan de forma local o en la nube y que vayan a usar autenticación en la nube?

## Evaluación de los requisitos del usuario
También es necesario evaluar los requisitos del usuario. Esta evaluación es importante para definir los pasos necesarios para la incorporación y para ayudar a los usuarios a realizar la transición a la nube. Asegúrese de responder a las siguientes preguntas:

- ¿Los usuarios van a acceder a las aplicaciones de forma local?
- ¿Los usuarios van a acceder a las aplicaciones en la nube?
- ¿Cómo inician sesión normalmente los usuarios en su entorno local?
- ¿Cómo van a iniciar sesión en la nube los usuarios?

>[Nota] Asegúrese de anotar cada respuesta y de que comprende la lógica subyacente a ellas. En la sección [Determinación de los requisitos de respuesta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) se revisarán las opciones disponibles y las ventajas e inconvenientes de cada opción. Las respuestas que obtenga partir de estas preguntas le servirán para seleccionar la opción que mejor se adapte a sus necesidades empresariales.

## Pasos siguientes
[Determinación de los requisitos de sincronización de directorios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## Consulte también
[Información general sobre las consideraciones de diseño]((active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_1210_2015-->