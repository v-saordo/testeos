<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: determinación de los requisitos de protección de datos| Microsoft Azure"
	description="Al planear la solución de identidad híbrida, identifique los requisitos de protección de datos de su empresa y las opciones disponibles que mejor satisfacen esos requisitos."
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

#Plan para mejorar la seguridad de los datos mediante una solución de identidad sólida

El primer paso para proteger los datos es identificar quién puede tener acceso a ellos. Como parte de este proceso, necesita contar con una solución de identidad que se pueda integrar con su sistema con el fin de proporcionar funcionalidades de autenticación y autorización. Con frecuencia la autenticación y la autorización se confunden y no se comprende bien su función. En realidad, son bastante diferentes, tal como se muestra en la ilustración siguiente:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Fases del ciclo de vida de administración de dispositivos móviles**

Al planear la solución de identidad híbrida, debe comprender los requisitos de protección de datos de su negocio y las opciones de las que dispone que mejor pueden satisfacer esos requisitos.
 
>[AZURE.NOTE]
Después de terminar de planear la seguridad de los datos, revise [Determinación de los requisitos de autenticación multifactor](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para asegurarse de que las selecciones respecto a los requisitos de la autenticación multifactor no resultaron afectadas por las decisiones tomadas en esta sección.

## Determinación de los requisitos de protección de datos
En la era de la movilidad, el objetivo de la mayoría de las empresas es permitir a los usuarios ser productivos con sus dispositivos móviles, tanto de forma local como remota y, desde cualquier parte, con el fin de aumentar la productividad. Si bien este podría ser un objetivo común, las empresas que tienen tales requisitos también se sienten preocupadas respecto a la cantidad de amenazas que se deben mitigar a fin de proteger los datos de la empresa y mantener la privacidad de los usuarios. En este sentido, cada empresa puede tener requisitos diferentes; diferentes reglas de cumplimiento que variarán según la industria para la que trabaje la empresa y que conducirán a diferentes decisiones de diseño.

Sin embargo, hay algunos aspectos de seguridad que se deben explorar y validar, con independencia de la industria, que se explican en la sección siguiente.

## Rutas de protección de datos

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Rutas de protección de datos**

En el diagrama anterior, el componente de identidad será el primero en comprobarse antes de acceder a los datos. Sin embargo, estos datos pueden estar en distintos estados según el momento en el que se tuvo acceso a ellos. Cada número de este diagrama representa una ruta en la que se pueden encontrar los datos en algún momento en el tiempo. Estos números se explican a continuación:

1. Protección de datos en el nivel de dispositivo.
2. Protección de datos en tránsito.
3. Protección de datos en reposo en el entorno local.
4. Protección de datos mientras están en reposo en la nube.

Aunque la solución de identidad híbrida no ofrece directamente los controles técnicos que permiten al departamento de TI proteger los datos en cada una de esas fases, es necesario que la solución de identidad híbrida sea capaz de aprovechar los recursos de administración de identidades locales y en la nube con el fin de identificar al usuario antes de que se le conceda acceso a los datos. Al planear la solución de identidad híbrida, asegúrese de que puede responder a las siguientes preguntas según los requisitos de su organización:

## Protección de datos en reposo
Con independencia de dónde se encuentren los datos en reposo (dispositivo, nube o local), es importante realizar una evaluación para comprender las necesidades de la organización al respecto. En este área, asegúrese de plantearse las siguientes cuestiones:

- ¿Necesita su empresa proteger los datos en reposo?
 - En caso afirmativo, ¿podrá integrarse la solución híbrida con la infraestructura local actual?
 - En caso afirmativo, ¿podrá integrarse la solución híbrida con las cargas de trabajo ubicadas en la nube?
- ¿Podrá proteger la administración de identidades en la nube las credenciales del usuario y otros datos almacenados en la nube?

## Protección de datos en tránsito
Los datos en tránsito entre el dispositivo y el centro de datos o entre el dispositivo y la nube deben estar protegidos. Sin embargo, estar en tránsito no significa necesariamente que el proceso de comunicaciones con un componente se produzca fuera del servicio en la nube; también se mueve internamente, por ejemplo, entre dos redes virtuales. En este área, asegúrese de plantearse las siguientes cuestiones:

- ¿Necesita su empresa proteger los datos en tránsito?
 - En caso afirmativo, ¿podrá integrarse la solución híbrida con controles seguros como SSL/TLS?
- ¿Mantiene la administración de identidades en la nube el tráfico al almacén de directorios firmado y dentro de él (dentro y entre centros de datos)?


## Cumplimiento normativo
Las normas, las leyes y los requisitos de cumplimiento normativo variarán según la industria a la que pertenezca su empresa. Las empresas de industrias muy reguladas deben solucionar los problemas de administración de identidades relacionados con los aspectos de cumplimiento. Normativas como Sarbanes-Oxley (SOX), la Ley de transferencia y responsabilidad de seguros de salud (HIPAA), la Ley de Gramm-Leach-Bliley (GLBA) y el Estándar de Seguridad de Datos para la Industria de Tarjeta de Pago (PCI DSS) son muy estrictos respecto a la identidad y el acceso. La solución de identidad híbrida que su empresa adopte debe tener las funcionalidades básicas que satisfagan los requisitos de una o varias de estas normativas. En este área, asegúrese de plantearse las siguientes cuestiones:

- ¿Cumple la solución de identidad híbrida los requisitos normativos de su empresa?
- ¿Integra la solución de identidad híbrida funcionalidades que permiten a su empresa satisfacer los requisitos de cumplimiento normativo? 
 
>[AZURE.NOTE]
Asegúrese de anotar cada respuesta y de que comprende las razones que se esconden detrás. En la sección [Definición de la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) se recorren las opciones disponibles y las ventajas y desventajas de cada una. Las respuestas que obtenga partir de estas preguntas le servirán para seleccionar la opción que mejor se adapte a sus necesidades empresariales.

## Pasos siguientes
 [Determinación de los requisitos de administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## Otras referencias
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0218_2016-->