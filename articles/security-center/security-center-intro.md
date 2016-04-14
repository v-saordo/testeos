<properties
   pageTitle="Introducción al Centro de seguridad de Azure | Microsoft Azure"
   description="Obtenga información sobre el Centro de seguridad de Azure, sus capacidades clave y cómo funciona."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# Introducción al Centro de seguridad de Azure

Obtenga información sobre el Centro de seguridad de Azure, sus capacidades clave y cómo funciona.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo.

## ¿Qué es el Centro de seguridad de Azure?
 El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

##	Principales capacidades
 El Centro de seguridad ofrece las funcionalidades sencillas y eficaces de prevención, detección y respuesta ante amenazas integradas en Azure. Principales capacidades:

| | |
|----- |-----|
| Prevención | Supervisa el estado de seguridad de los recursos de Azure |
| | Define las directivas de sus suscripciones y grupos de recursos de Azure en función de los requisitos de seguridad de su compañía, los tipos de aplicaciones que use y la confidencialidad de los datos |
| | Usa recomendaciones de seguridad controladas por directivas para guiar a los propietarios de los servicios a través del proceso de implementación de los controles necesarios |
| | Implementa rápidamente servicios y dispositivos de seguridad de Microsoft y asociados |
| Detección |Recopila y analiza automáticamente los datos de seguridad de los recursos de Azure, la red y las soluciones de asociados como firewalls y programas antimalware |
| | Aprovecha la inteligencia de amenazas global de los productos y servicio de Microsoft, la Unidad de crímenes digitales de Microsoft, Microsoft Security Response Center (MSRC) y fuentes externas. |
| | Aplica análisis avanzados, incluido el aprendizaje automático y el análisis de comportamientos |
| Respuesta | Proporciona seguridad prioritaria ante incidentes y alertas |
| | Proporciona información sobre el origen del ataque y los recursos afectados |
| | Sugiere formas de detener el ataque actual y de ayudar a impedir ataques futuros |

## Tutorial de introducción
 Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). [Inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar** y desplácese hasta la opción **Centro de seguridad** o seleccione el icono **Centro de seguridad** que ancló antes al panel del portal.

![Icono Seguridad en el Portal de Azure][1]

En el Centro de seguridad, puede establecer directivas de seguridad, supervisar configuraciones de seguridad y ver alertas de seguridad.

### Directivas de seguridad

Puede definir directivas para las suscripciones y los grupos de recursos de Azure según los requisitos de seguridad de su empresa. También puede personalizarlas según los tipos de aplicaciones que use o la confidencialidad de los datos en cada suscripción. Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan requisitos de seguridad distintos a los empleados para las aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como PII requieran un mayor nivel de seguridad.

> [AZURE.NOTE] Para modificar una directiva de seguridad en el nivel de suscripción o el nivel de grupo de recursos, debe ser el propietario de la suscripción o un colaborador de esta.

En la hoja **Centro de seguridad**, seleccione el icono **Directiva de seguridad** para obtener una lista de las suscripciones y los grupos de recursos.

![Hoja de centro de seguridad][2]

En la hoja **Directiva de seguridad**, seleccione una suscripción para ver los detalles de la directiva.

![Suscripción de hoja de directiva de seguridad][3]

**Recopilación de datos** (consultar más arriba) habilita la recopilación de datos para una directiva de seguridad. Con la habilitación se consigue lo siguiente:

- Examen diario de todas las máquinas virtuales admitidas para las recomendaciones y la supervisión de seguridad.
- Colección de eventos de seguridad para el análisis y la detección de amenazas.

**Elegir una cuenta de almacenamiento por región** (véase más arriba) le permite elegir, para cada región en la que disponga de máquinas virtuales en funcionamiento, la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Si no elige una cuenta de almacenamiento para cada región, se creará automáticamente. Los datos recopilados se aíslan lógicamente de los datos de otros clientes por seguridad.

> [AZURE.NOTE] La recopilación de datos y la elección de una cuenta de almacenamiento por región se configura en el nivel de suscripción.

**Mostrar recomendaciones para:** (véase más arriba) le permite elegir los controles de seguridad que desea supervisar y recomendar según las necesidades de seguridad de los recursos de la suscripción.

A continuación, seleccione un grupo de recursos para ver los detalles de la directiva.

![Grupo de recursos de hoja de directiva de seguridad][4]

**Herencia** (véase más arriba) le permite definir el grupo de recursos como:

- Heredado (valor predeterminado), lo que significa que todas las directivas de seguridad para este grupo de recursos se heredan a partir del nivel de suscripción.
- Único, lo que significa que el grupo de recursos tendrá una directiva de seguridad personalizada. Será necesario realizar cambios en **Mostrar recomendaciones para**.

> [AZURE.NOTE] En caso de un conflicto entre la directiva del nivel de suscripción y la directiva del nivel de grupo de recursos, la del nivel del grupo de recursos tiene prioridad.

### Recomendaciones de seguridad

 El Centro de seguridad analiza el estado de seguridad de los recursos de Azure para identificar posibles vulnerabilidades de seguridad. Una lista de recomendaciones le guiará por el proceso de configuración de los controles necesarios. Algunos ejemplos son:

- Aprovisionamiento de antimalware para ayudar a identificar y eliminar el software malintencionado.
- Configuración de reglas y grupos de seguridad de red para controlar el tráfico a las máquinas virtuales.
- Aprovisionamiento de firewalls de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web.
- Implementación de actualizaciones del sistema que faltan.
- Resolución de las configuraciones de sistema operativo que no coinciden con las líneas de base recomendadas

Haga clic en el icono **Recomendaciones** para obtener una lista de recomendaciones. Haga clic en cada recomendación para ver más información o para llevar a cabo acciones para resolver el problema.

![Recomendaciones de seguridad en el Centro de seguridad de Azure][5]

### Estado de los recursos

El icono **Estado de los recursos** muestra el estado general de la seguridad del entorno por tipo de recurso, lo que incluye máquinas virtuales, aplicaciones web y otros recursos.

Seleccione un tipo de recurso en el icono **Estado de los recursos** para ver más información, incluida una lista de las posibles vulnerabilidades de seguridad que se hayan identificado. (En el ejemplo de abajo, se ha seleccionado **Máquinas virtuales**).

![Icono Estado de los recursos][6]

### Alertas de seguridad

 El Centro de seguridad recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y soluciones de asociados como firewalls y programas antimalware. Cuando se detecten amenazas, se creará una alerta de seguridad. Como ejemplos se incluye la detección de:

- Máquinas virtuales en peligro que se comunican con direcciones IP malintencionadas conocidas.
- Malware avanzado detectado mediante la generación de informes de errores de Windows.
- Ataques por fuerza bruta contra máquinas virtuales.
- Alertas de seguridad de programas antimalware y firewalls integrados

Al hacer clic en el icono **Alertas de seguridad** se muestra una lista de alertas por prioridad.

![Alertas de seguridad][7]

Al seleccionar una alerta, se muestra más información sobre el ataque y sugerencias para resolverlo.

![Detalles de alertas de seguridad][8]

## Primeros pasos
Para empezar a trabajar con el Centro de seguridad, necesita una suscripción a Microsoft Azure. El Centro de seguridad se habilita con su suscripción de Azure. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). Consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/) para obtener más información.

[Introducción al Centro de seguridad de Azure](security-center-get-started.md) le guía rápidamente por los componentes de administración de directivas y supervisión de seguridad del Centro de seguridad.

## Pasos siguientes
En este documento, se ha presentado el Centro de seguridad, sus funcionalidades clave y cómo empezar a usarlo. Para obtener más información, consulte:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información acerca de cómo configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): obtenga información sobre cómo las recomendaciones lo ayudan a proteger sus recursos de Azure.
- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información de seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_0309_2016-->