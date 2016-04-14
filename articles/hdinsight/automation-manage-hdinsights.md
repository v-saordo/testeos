<properties
	pageTitle="Administrar HDInsight de Azure mediante Automatización de Azure"
	description="Obtenga información acerca de cómo puede usarse el servicio de Automatización de Azure para administrar HDInsight de Azure."
	services="HDInsight, automation"
	documentationCenter=""
	authors="elcooper"
	manager="eamono"
	editor=""/>

<tags
	ms.service="HDInsight"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="elcooper"/>



#Administrar HDInsight de Azure mediante Automatización de Azure
Esta guía le ofrece el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de clústeres y automatizar tareas comunes en HDInsight de Azure.

## ¿Qué es Automatización de Azure?
[Automatización de Azure](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio programando las tareas de administración en la nube para que se ejecuten automáticamente en Automatización de Azure.


## ¿Cómo puede ayudar Automatización de Azure a administrar HDInsight de Azure?

HDInsight se puede administrar en Automatización de Azure mediante los [cmdlets de HDInsight de Azure](https://msdn.microsoft.com/library/azure/dn479228.aspx) que están disponibles en las [herramientas de Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatización de Azure tiene estos cmdlets disponibles directamente para que pueda realizar todas las tareas de administración de HDInsight dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Con los cmdlets de HDInsight de Azure puede automatizar tareas como aprovisionar clústeres de HDInsight en Linux o Windows, escalar clústeres, administrar clústeres y enviar trabajos de MapReduce. Estas son solo algunas de las muchas tareas que puede automatizar con PowerShell en Automatización de Azure.


## Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar HDInsight de Azure, siga este vínculo para obtener más información acerca de Automatización de Azure.

* Consulte el [Tutorial de introducción](../automation-create-runbook-from-samples.md) de Automatización de Azure.
* Vea ejemplos en el [Centro de scripts](http://aka.ms/scriptcentergallery).  

 

<!---HONumber=AcomDC_0218_2016-->