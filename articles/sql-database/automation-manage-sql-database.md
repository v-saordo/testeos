<properties
	pageTitle="Administración de bases de datos SQL de Azure mediante Automatización de Azure"
	description="Obtenga información acerca de cómo puede usarse el servicio Automatización de Azure para administrar bases de datos SQL de Azure a escala."
	services="sql-database, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="jolevy"/>



#Administración de bases de datos SQL de Azure mediante Automatización de Azure

Esta guía le ofrece el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de las bases de datos SQL de Azure.


## ¿Qué es Automatización de Azure?

[Automatización de Azure](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades a medida que crece la organización. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.


## ¿Cómo puede Automatización de Azure ayudar a administrar bases de datos SQL de Azure?

La base de datos SQL de Azure puede administrarse en Automatización de Azure mediante los [cmdlets de PowerShell de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn546726.aspx) que están disponibles en las [herramientas de PowerShell de Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatización de Azure tiene estos cmdlets de PowerShell de base de datos SQL de Azure disponibles directamente para que pueda realizar todas las tareas de administración de base de datos SQL dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Automatización de Azure también tiene la capacidad de comunicarse con servidores SQL Server directamente, mediante la emisión de comandos SQL con PowerShell.

La [Galería de runbooks de Automatización de Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene una gran variedad de runbooks de comunidad y equipo de producto para empezar a automatizar la administración de Base de datos SQL de Azure, otros servicios de Azure y sistemas de terceros. Los runbooks de la Galería incluyen:

 * [Ejecución de consultas SQL en una Base de datos de SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Escalación vertical (arriba o abajo) de una Base de datos SQL de Azure en una programación](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Truncamiento de una tabla SQL si su base de datos se aproxima al tamaño máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Indexación de tablas en una Base de datos SQL de Azure si están muy fragmentadas](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## Pasos siguientes

Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar bases de datos SQL de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

- [Información general sobre Automatización de Azure](../automation/automation-intro.md)
- [Mi primer runbook](../automation/automation-first-runbook-graphical.md)
- [Mapa de aprendizaje de Automatización de Azure ](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Automatización de Azure: el agente SQL en la nube](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 

<!---HONumber=AcomDC_0128_2016-->