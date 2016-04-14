<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: determinación de los requisitos de administración de contenido| Microsoft Azure"
	description="Se proporciona información detallada sobre cómo determinar los requisitos de administración de contenido de su empresa. Normalmente, cuando un usuario tiene su propio dispositivo lo habitual es que tenga más varias credenciales que se irán alternando según la aplicación que use. Es importante distinguir qué contenido se creó con las credenciales personales frente al que se creó con las credenciales corporativas. La solución de identidad debe ser capaz de interactuar con los servicios en la nube a fin de proporcionar al usuario final una experiencia sin fisuras, y al mismo tiempo asegurar su privacidad y aumentar la protección frente a la pérdida de datos."
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

# Determinación de los requisitos de administración de contenido para la solución de identidad híbrida

Comprender los requisitos de administración de contenido de su empresa puede afectar directamente a su decisión sobre la solución de identidad híbrida que es mejor usar. Con la proliferación de tantos dispositivos y la posibilidad de que los usuarios traigan los suyos propios ([BYOD](http://aka.ms/byodcg)), la empresa debe proteger sus propios datos, pero también mantener intacta la privacidad del usuario. Normalmente, cuando un usuario tiene su propio dispositivo lo habitual es que tenga más varias credenciales que se irán alternando según la aplicación que use. Es importante distinguir qué contenido se creó con las credenciales personales frente al que se creó con las credenciales corporativas. La solución de identidad debe ser capaz de interactuar con los servicios en la nube a fin de proporcionar al usuario final una experiencia sin fisuras, y al mismo tiempo asegurar su privacidad y aumentar la protección frente a la pérdida de datos.

Diferentes controles técnicos aprovecharán la solución de identidad para proporcionar administración de contenido, como se muestra en la siguiente ilustración:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controles de seguridad que aprovecharán su sistema de administración de identidad**

En general, los requisitos de administración de contenido aprovecharán su sistema de administración de identidad en las áreas siguientes:

- Privacidad: identificarán al usuario propietario de un recurso y aplicarán los controles adecuados para mantener la integridad.
- Clasificación de datos: identificarán al usuario o grupo y el nivel de acceso a un objeto en función de su clasificación. 
- Protección contra pérdida de datos: los controles de seguridad responsables de la protección de los datos deberán interactuar con el sistema de identidad para validar la identidad del usuario a fin de evitar la pérdida de estos datos. Esto también es importante para los fines de seguimiento de auditoría.

>[AZURE.NOTE]
Lea [Clasificación de los datos para prepararlos para la nube](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obtener más información sobre los procedimientos recomendados e instrucciones para la clasificación de los datos.

Al planear la solución de identidad híbrida, asegúrese de que puede responder a las siguientes preguntas según los requisitos de su organización:

- ¿Tiene su empresa controles de seguridad para aplicar la privacidad de los datos?
 - En caso afirmativo, ¿podrán integrarse esos controles de seguridad con la solución de identidad híbrida que va a adoptar?
- ¿Usa su empresa clasificación de los datos?
 - En caso afirmativo, ¿se puede integrar la solución actual con la solución de identidad híbrida que va a adoptar?
- ¿Tiene su empresa actualmente una solución para hacer frente a la pérdida de datos? 
 - En caso afirmativo, ¿se puede integrar la solución actual con la solución de identidad híbrida que va a adoptar?
- ¿Necesita su empresa auditar el acceso a los recursos?
 - En caso afirmativo, ¿para qué tipo de recursos?
 - En caso afirmativo, ¿qué nivel de información es necesario?
 - En caso afirmativo, ¿dónde debe residir el registro de auditoría? ¿En el entorno local o en la nube?
- ¿Necesita su empresa cifrar los mensajes de correo electrónico que contienen datos confidenciales (números de seguridad social, números de tarjeta de crédito, etc.)?
- ¿Necesita su empresa cifrar todos los documentos o contenidos compartidos con socios comerciales externos?
- ¿Necesita su empresa aplicar directivas corporativas en determinadas clases de correos electrónicos (no responder a todos, no reenvíar)?
 
>[AZURE.NOTE]
Asegúrese de anotar cada respuesta y de que comprende las razones que se esconden detrás. En la sección [Definición de la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) se recorren las opciones disponibles y las ventajas y desventajas de cada una. Las respuestas que obtenga partir de estas preguntas le servirán para seleccionar la opción que mejor se adapte a sus necesidades empresariales.


## Pasos siguientes
[Determinación de los requisitos de control de acceso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## Otras referencias
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0218_2016-->