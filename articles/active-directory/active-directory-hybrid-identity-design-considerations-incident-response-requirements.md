
<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: determinación de los requisitos de respuesta a incidentes| Microsoft Azure"
	description="Determine las funcionalidades de supervisión e informes de la solución de identidad híbrida que puede aprovechar el responsable de TI para realizar acciones de cara a identificar y mitigar las posibles amenazas."
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
	ms.date="02/12/2016"
	ms.author="yuridio"/>

# Determinación de los requisitos de respuesta a incidentes para su solución de identidad híbrida

La mayoría de las organizaciones grandes y medianas contarán probablemente con una [respuesta a los incidentes de seguridad](https://technet.microsoft.com/library/cc700825.aspx) para ayudar al responsable de TI a emprender acciones en función del nivel de incidente. El sistema de administración de identidades es un componente importante en el proceso de respuesta a incidentes, ya que se puede usar para ayudar a identificar quién ha realizado una acción específica en el destino. La solución de identidad híbrida debe ser capaz de proporcionar funcionalidades de supervisión e informes que TI puede aprovechar para realizar acciones con el fin de identificar y mitigar las posibles amenazas. En un plan de respuesta a incidentes típico tendrá las siguientes fases:

1.	Evaluación inicial.
2.	Comunicación de incidentes
3.	Control de daños y reducción de riesgos
4.	Identificación de lo que se ha puesto en peligro y la gravedad
5.	Conservación de evidencias
6.	Notificación a las partes interesadas
7.	Recuperación del sistema
8.	Documentación.
9.	Evaluación de daños y costos
10.	Revisión del proceso y el plan.

Durante la fase de identificación de lo que se pone en peligro y la gravedad, será necesario identificar los sistemas que se han puesto en peligro, los archivos a los que se ha obtenido acceso y determinar la confidencialidad de esos archivos. El sistema de identidad híbrida debe ser capaz de satisfacer esos requisitos para ayudarle a identificar el usuario que realizó dichos cambios.

## Supervisión e informes
En muchas ocasiones, el sistema de identidad híbrida puede ayudar en la fase de evaluación inicial, en especial si lleva integradas funcionalidades de auditoría e informes. Durante la evaluación inicial, el administrador de TI debe poder identificar una actividad sospechosa o el sistema debe ser capaz de desencadenarla automáticamente según una tarea configurada previamente. Muchas actividades podrían indicar un posible ataque; sin embargo, en otros casos, un sistema mal configurado puede conducir a varios falsos positivos en un sistema de detección de intrusiones.

El sistema de administración de identidades debe ayudar a los administradores de TI a identificar y notificar esas actividades sospechosas. Por lo habitual, estos requisitos técnicos se pueden satisfacer con la supervisión de todos los sistemas y una funcionalidad de informes que pueda resaltar las posibles amenazas. Las siguientes preguntas le ayudarán a diseñar la solución de identidad híbrida y a tener en cuenta los requisitos de respuesta a incidentes:

- ¿Tiene su empresa una respuesta a incidentes de seguridad?
 - En caso afirmativo, ¿se usa el sistema de administración de identidades actual como parte del proceso?
- ¿Necesita su empresa identificar los intentos de inicio de sesión sospechosos de los usuarios en distintos dispositivos?
- ¿Necesita su empresa detectar las credenciales del usuario que han podido verse comprometidas?
- ¿Necesita su empresa auditar el acceso y la acción del usuario?
- ¿Necesita su empresa saber cuándo un usuario restablece su contraseña?

## Aplicación de directivas

Durante la fase de control de daños y reducción de riesgos, es importante reducir rápidamente los efectos reales y posibles de un ataque. Esa acción que realice en este punto puede marcar la diferencia entre un daño menor o uno mayor. La respuesta exacta dependerá de su organización y de la naturaleza del ataque al que se enfrenta. Si la evaluación inicial concluye que se ha puesto en peligro una cuenta, deberá aplicar la directiva para bloquear esta cuenta. Este es solo un ejemplo de dónde se puede aprovechar el sistema de administración de identidades. Las siguientes preguntas le ayudarán a diseñar la solución de identidad híbrida y a tener en cuenta el modo en que se aplicarán las directivas para reaccionar a un incidente en curso:

- ¿Dispone su empresa de directivas que impidan a los usuarios el acceso a la red si es necesario?
 - En caso afirmativo, ¿se integra la solución actual con el sistema de administración de identidad híbrida que va a adoptar?
- ¿Necesita aplicar su empresa acceso condicional a los usuarios que están en cuarentena? 
 
>[AZURE.NOTE]
Asegúrese de anotar cada respuesta y de que comprende las razones que se esconden detrás. En la sección [Definición de la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) se recorren las opciones disponibles y las ventajas y desventajas de cada una. Las respuestas que obtenga partir de estas preguntas le servirán para seleccionar la opción que mejor se adapte a sus necesidades empresariales.

## Pasos siguientes
[Definición de la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## Otras referencias
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0218_2016-->