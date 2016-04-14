<properties 
	pageTitle="¿Qué son las aplicaciones lógicas?" 
	description="Obtenga más información acerca de las Aplicaciones lógicas del Servicio de aplicaciones" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="03/01/2016"
	ms.author="klam"/>

#¿Qué son las aplicaciones lógicas?

| Referencia rápida |
| --------------- |
| [Lenguaje de definición de aplicaciones lógicas](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Documentación de API administrada de aplicaciones lógicas](https://azure.microsoft.com/documentation/articles/apis-list) |
| [Foro de aplicaciones lógicas](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps) |

Servicio de aplicaciones de Azure cuenta con PaaS (plataforma como servicio) totalmente administrada para desarrolladores que facilita la creación de aplicaciones web, móviles y de integración. Aplicaciones lógicas forma parte de este conjunto y permite a cualquier usuario técnico o desarrollador automatizar el flujo de trabajo y la ejecución de procesos de negocio mediante un diseñador visual fácil de usar.

Y lo mejor de todo: Aplicaciones lógicas se puede combinar con las [API administradas][managedapis] integradas para ayudar a resolver incluso escenarios de integración complicados de forma sencilla:

![Diseñador de aplicación de flujo](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Como se ha mencionado, con las aplicaciones lógicas puede automatizar procesos empresariales. Estos son algunos ejemplos:
 
* Puede replicar automáticamente nuevos registros en la Base de datos SQL y enviar correos a la primera línea de atención al cliente.   
* Puede encontrar automáticamente tweets negativos y enviarlos a un canal de demora.

Todos estos tipos de escenarios se pueden configurar desde el diseñador visual y sin escribir ni una sola línea de código. Comience a [crear su aplicación lógica ahora][create].

## ¿Por qué aplicaciones lógicas?

Aplicaciones lógicas permite a los desarrolladores diseñar flujos de trabajo que se inician a partir de un desencadenador y después ejecutan una serie de pasos. Cada paso invoca una API mientras se ocupa de forma segura de la autenticación y los procedimientos recomendados, como punto de comprobación y ejecución duradera.

Si desea automatizar cualquier proceso empresarial (por ejemplo, encontrar tweets negativos y publicar en el canal de inactividad interno, o replicar nuevos registros de clientes de SQL, según llegan, en el sistema CRM), las aplicaciones lógicas realizan la integración de orígenes de datos dispares desde la nube al entorno local de forma sencilla. Consulte nuestras [API administradas][managedapis] para obtener más ideas y [comenzar][create] ahora a ver qué puede hacer.

Además, con nuestras [API administradas de BizTalk][biztalk], puede realizar el escalado a escenarios de integración consolidados con la eficacia de un [motor de reglas][rules], la [administración de socios comerciales][tpm] y mucho más.

- **Herramientas de diseño fáciles de usar**: las aplicaciones lógicas pueden diseñarse de principio a fin en el explorador. Inicio con un desencadenador: desde una simple programación hasta siempre vez que aparezcas un tweet acerca de su compañía. A continuación, se coordina cualquier número de acciones mediante la sofisticada galería de conectores.

- **Crear fácilmente SaaS**: incluso las tareas de composición que son fáciles de describir son difíciles de implementar en el código. Las aplicaciones lógicas hacen muy fácil de conectar sistemas dispares. ¿Desea crear una tarea en el software CRM que se basa en la actividad de las cuentas de Facebook o Twitter? ¿Desea conectar su solución de marketing en la nube a su sistema de facturación local? Las aplicaciones lógicas son la forma más rápida y más confiable para ofrecer soluciones a estos problemas.

- **Introducción rápida desde plantillas**: para ayudarle a empezar, hemos dispuesto una [galería de plantillas][templates] que permiten crear rápidamente algunas soluciones comunes. Desde soluciones avanzadas de BizTalk a la conectividad de SaaS simple e incluso algunos que sean solo por diversión: la galería es la forma más rápida de comprender la eficacia de las aplicaciones lógicas.

- **Extensibilidad incorporada**: ¿no ve la API que necesita? Las Aplicaciones lógicas están diseñadas para trabajar con Aplicaciones de API; puede crear fácilmente su propia aplicación de API para utilizarla como API personalizada. Cree una nueva aplicación solo para usted, o bien compártala y rentabilícela en el marketplace.

- **Eficacia de integración real**: empiece por algo sencillo y crezca a medida que sea necesario. Las aplicaciones lógicas pueden aprovechar con facilidad la eficacia de BizTalk, la solución de integración líder del sector de Microsoft para permitir a los profesionales de integración crear las soluciones que necesitan. Obtenga más información sobre las [funcionalidades de BizTalk proporcionadas con Aplicaciones lógicas][biztalk].

## Conceptos de aplicaciones lógicas

Las siguientes son algunas de las principales partes que componen la experiencia de aplicaciones lógicas.

- **Flujo de trabajo**: las aplicaciones lógicas ofrecen una forma gráfica para modelar los procesos de negocio como una serie de pasos o un flujo de trabajo.
- **API administradas**: las aplicaciones lógicas necesitan tener acceso a datos y servicios. Las API administradas se crean específicamente para ayudarle cuando se conecta y trabaja con datos. Consulte la lista de las API disponibles ahora en [API administradas][managedapis].
- **Desencadenadores**: algunas API administradas también pueden actuar como desencadenadores. Un desencadenador inicia una nueva instancia de un flujo de trabajo de acuerdo con un evento específico, como la llegada de un correo electrónico o un cambio en la cuenta de almacenamiento de Azure.
-  **Acciones**: cada paso después de la llamada a la acción de un desencadenador en un flujo de trabajo. Cada acción normalmente se asigna a una operación en sus aplicaciones de API administradas o personalizadas.
- **BizTalk**: para escenarios más avanzados de integración, Aplicaciones lógicas incluye funcionalidades de BizTalk. BizTalk es la plataforma de integración líder del sector de Microsoft. Las aplicaciones de API de BizTalk permiten incluir fácilmente la validación, transformación, reglas y mucho más en sus flujos de trabajo de Aplicaciones lógicas. Obtenga más información en [Qué son las Aplicaciones de API de BizTalk][biztalk].

## Introducción

Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica][create].

Para obtener más información sobre la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md

<!---HONumber=AcomDC_0309_2016-->