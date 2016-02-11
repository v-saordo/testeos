<properties
	pageTitle="¿Qué es Automatización de Azure? | Microsoft Azure"
	description="Conozca el valor que aporta Automatización de Azure y obtenga respuestas a preguntas habituales para comenzar a crear, usar runbooks y DSC de Automatización de Azure."
	services="automation"
	documentationCenter=""
	authors="SnehaGunda"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="11/05/2015"
	ms.author="bwren;sngun"/>

# Información general sobre Automatización de Azure


Automatización de Microsoft Azure ofrece a los usuarios una manera de automatizar las tareas manuales, propensas a errores, con una ejecución prolongada y repetidas con frecuencia que se realizan normalmente en un entorno empresarial y en la nube. Ahorra tiempo y aumenta la confiabilidad de las tareas administrativas periódicas e incluso las programa para que se realicen automáticamente a intervalos regulares. Puede automatizar procesos mediante runbooks o automatizar la administración de configuración mediante la configuración de estado deseado. En este artículo se ofrece una breve información general sobre Automatización de Azure y se responden algunas preguntas habituales. Puede consultar otros artículos de esta biblioteca para obtener información más detallada sobre los diferentes temas.


## Procesos de automatización con runbooks

Un runbook es un conjunto de tareas que realizan algún proceso automatizado en Automatización de Azure. Puede ser un proceso sencillo como iniciar una máquina virtual y crear una entrada de registro, o puede ser un runbook complejo que combina otros runbooks más pequeños para llevar a cabo un proceso complejo en varios recursos o incluso en varias nubes y varios entornos locales.

Por ejemplo, se podría tener un proceso manual para truncar una Base de datos SQL si se aproxima al tamaño máximo que incluye varios pasos como la conexión al servidor, la conexión a la base de datos, la obtención del tamaño actual de la base de datos, la comprobación de si se ha superado el umbral y, finalmente, truncarla y notificarlo al usuario. En lugar de realizar manualmente cada uno de estos pasos, podría crear un runbook que realizara todas estas tareas como un solo proceso. Debería iniciar el runbook, especificar la información necesaria como el nombre del servidor SQL, el nombre de la base de datos y el correo electrónico del destinatario, y esperar mientras el proceso se completa.


## ¿Qué pueden automatizar los runbooks?

Los runbooks de Automatización de Azure se basan en Windows PowerShell o en el flujo de trabajo de PowerShell, por lo que hacen todo lo que puede hacer PowerShell. Si una aplicación o servicio tiene una API, un runbook puede trabajar con ella. Si tiene un módulo de PowerShell para la aplicación, puede cargarlo en Automatización de Azure e incluir estos cmdlets en el runbook. Los runbooks de Automatización de Azure se ejecutan en la nube de Azure y pueden acceder a los recursos en la nube o los recursos externos a los que se puede acceder desde la nube. Con [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), los runbooks pueden ejecutarse en su centro de datos local para administrar recursos locales.


## Obtención de runbooks de la Comunidad

La [Galería de runbooks](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contiene runbooks de Microsoft y de la comunidad que puede usar tal como están en su entorno o personalizarlos para sus propios fines. También son útiles como referencias para aprender a crear sus propios runbooks. Incluso puede aportar a la galería runbooks propios que crea que pueden resultar útiles para otros usuarios.


## Creación de runbooks con Automatización de Azure 

Puede [crear sus propios runbooks](automation-creating-importing-runbook.md) desde cero o modificarlos desde la [Galería de runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) para que satisfagan sus propias necesidades. Hay tres [tipos de runbooks](automation-runbook-types.md) diferentes que puede elegir en función de sus requisitos y de la experiencia de PowerShell. Si prefiere trabajar directamente con el código de PowerShell, puede usar un [runbook de PowerShell ](automation-runbook-types.md#powershell-runbooks) o un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) que edite sin conexión, o bien mediante el [editor de texto](http://msdn.microsoft.com/library/azure/dn879137.aspx) en el Portal de Azure. Si prefiere editar un runbook sin estar expuesto al código subyacente, puede crear un [runbook gráfico](automation-runbook-types.md#graphical-runbooks) con el [editor gráfico](automation-graphical-authoring-intro.md) en el Portal de vista previa de Azure.

¿Prefiere ver a leer? Eche un vistazo al vídeo siguiente de la sesión de Microsoft Ignite en mayo de 2015. Nota: Aunque los conceptos y las características tratadas en este vídeo son correctas, Automatización de Azure ha progresado mucho desde que se registró este vídeo, ahora tiene una interfaz de usuario más amplia en el Portal de Azure y admite otras capacidades.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## Automatización de la administración de configuración con la configuración de estado deseado 

[DSC de PowerShell](https://technet.microsoft.com/library/dn249912.aspx) es una plataforma de administración que permite administrar, implementar y exigir la configuración de hosts físicos y máquinas virtuales mediante el uso de una sintaxis declarativa de PowerShell. Puede definir configuraciones en un servidor de extracción DSC que las máquinas de destino pueden recuperar y aplicar. DSC proporciona un conjunto de cmdlets de PowerShell que puede usar para administrar las configuraciones y los recursos.

[DSC de Automatización de Azure](automation-dsc-overview.md) es una solución basada en la nube para DSC de PowerShell que proporciona los servicios necesarios para los entornos empresariales. Puede administrar los recursos de DSC en Automatización de Azure y aplicar las configuraciones a las máquinas virtuales o físicas que los recuperan desde un servidor de extracción DSC en la nube de Azure. También proporciona servicios de informes que le informan de eventos importantes, como cuando se desvían los nodos de su configuración asignada y cuando se ha aplicado una nueva configuración.


## Creación de sus propias configuraciones DSC con Automatización de Azure

[Configuraciones DSC](automation-dsc-overview.md#azure-automation-dsc-terms) especifican el estado deseado de un nodo. Varios nodos pueden aplicar la misma configuración para garantizar que todos mantienen el mismo estado. Puede crear una configuración con cualquier texto editor en el equipo local y, después, importarla en Automatización de Azure donde la puede compilar y aplicar los nodos.


## Obtención de módulos y configuraciones 

Puede obtener [módulos de PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) con cmdlets que puede usar en sus runbooks y configuraciones DSC en la [Galería de PowerShell](http://www.powershellgallery.com/). Puede iniciar esta galería desde el Portal de vista previa de Azure e importar los módulos directamente en Automatización de Azure o puede descargarlos e importarlos de forma manual. No puede instalar los módulos directamente desde el Portal de Azure, pero puede descargarlos e instalarlos como cualquier otro módulo.


## Aplicaciones prácticas de ejemplo de Automatización de Azure 

A continuación se muestran algunos ejemplos de los tipos de situaciones que se pueden automatizar con Automatización de Azure.

* Cree y copie máquinas virtuales en diferentes suscripciones de Azure. 
* Programe copias de archivos de un equipo local a un contenedor de Almacenamiento de blobs de Azure. 
* Automatice las funciones de seguridad como denegar las solicitudes de un cliente cuando se detecta un ataque de denegación de servicio. 
* Asegúrese de que las máquinas se ajustan continuamente a la directiva de seguridad configuradas.
* Administre la implementación continua del código de aplicación por la infraestructura local y en la nube. 
* Cree un bosque de Active Directory en Azure para su entorno de laboratorio. 
* Trunque una tabla en una Base de datos SQL si la base de datos se acerca al tamaño máximo. 
* Actualice de forma remota la configuración del entorno para un sitio web de Azure. 


## ¿Cómo se relaciona Automatización de Azure con otras herramientas de automatización?

[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) está diseñado para automatizar las tareas de administración en la nube privada. Se instala localmente en el centro de datos como componente del [Paquete de Microsoft Azure](https://www.microsoft.com/es-ES/server-cloud/). SMA y Automatización de Azure usan el mismo formato de runbook basado en Windows PowerShell y en el flujo de trabajo de Windows PowerShell, pero SMA no admite los [runbooks gráficos](automation-graphical-authoring-intro.md).

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) está pensado para la automatización de recursos locales. Usa un formato de runbook distinto al de Automatización de Azure y Service Management Automation, y tiene una interfaz gráfica para crear runbooks sin necesidad de usar scripting. Sus runbooks se componen de actividades de paquetes de integración escritas específicamente para Orchestrator.


## ¿Dónde puedo obtener más información? 

Hay una amplia gama de recursos disponibles para obtener más información sobre Automatización de Azure y crear sus propios runbooks.

* En este momento se encuentra en la **biblioteca de Automatización de Azure**. Los artículos de esta biblioteca proporcionan documentación completa sobre la configuración y administración de Automatización de Azure y para crear sus propios runbooks. 
* Los [cmdlets de Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) proporcionan información para automatizar operaciones de Azure mediante Windows PowerShell. Los runbooks usan estos cmdlets para trabajar con recursos de Azure. 
* El [blog de administración](https://azure.microsoft.com/blog/tag/azure-automation/) ofrece la información más reciente sobre Automatización de Azure y otras tecnologías de administración de Microsoft. Suscríbase a este blog para mantenerse al día con las novedades del equipo de Automatización de Azure. 
* El [foro de automatización](http://go.microsoft.com/fwlink/p/?LinkId=390561) le permite publicar preguntas acerca de Automatización de Azure que serán atendidas por Microsoft y la comunidad de automatización. 
* Los [cmdlets de Automatización de Azure](https://msdn.microsoft.com/library/mt244122.aspx) proporcionan información para automatizar las tareas de administración. Contiene cmdlets para administrar las cuentas de Automatización, activos, runbooks, DSC.


## ¿Puedo proporcionar comentarios? 

**Envíenos sus comentarios** Si está buscando una solución de runbook o un módulo de integración de Automatización de Azure, publique una solicitud de script en el centro de scripts. Si tiene comentarios o solicitudes de características para Automatización de Azure, publíquelos en [Voz del usuario](http://feedback.windowsazure.com/forums/34192--general-feedback). Gracias.

<!---HONumber=AcomDC_0128_2016-->