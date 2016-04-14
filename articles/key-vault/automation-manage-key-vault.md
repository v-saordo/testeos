<properties
	pageTitle="Administración del Almacén de claves de Azure mediante Automatización de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo puede usarse el servicio de Automatización de Azure para administrar el Almacén de claves de Azure."
	services="Key-Vault, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="csand"/>



#Administración del Almacén de claves de Azure mediante Automatización de Azure

Esta guía le ofrece el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de claves y secretos en el Almacén de claves de Azure.

## ¿Qué es Automatización de Azure?

[Automatización de Azure](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.


## ¿Cómo puede ayudar Automatización de Azure a administrar el Almacén de claves de Azure?

El Almacén de claves se puede administrar en Automatización de Azure mediante los [cmdlets del Almacén de claves de Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx) que están disponibles en la [Galería de PowerShell](https://azure.microsoft.com/blog/azps-1-0/). Puede importar este módulo en Automatización de Azure, a fin de que pueda realizar muchas de las tareas de administración del Almacén de claves dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Con los cmdlets del Almacén de claves de Azure puede realizar estas tareas, entre otras: crear o importar una clave, crear o actualizar un secreto, actualizar los atributos de una clave, obtener una clave o un secreto y eliminar una clave o un secreto.


## Pasos siguientes

Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar el Almacén de claves de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

* Consulte el [Tutorial de introducción](../automation-create-runbook-from-samples.md) de Automatización de Azure.
* Consulte los [scripts de PowerShell del Almacén de claves de Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-Key-Vault-Powershell-1349b091).

<!---HONumber=AcomDC_0128_2016-->