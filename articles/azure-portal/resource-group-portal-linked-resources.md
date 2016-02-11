<properties 
	pageTitle="Recursos relacionados y vinculados en la galería de iconos" 
	description="Obtenga información acerca de los recursos vinculados y relacionados que se muestran en la galería de iconos del Portal de vista previa de Azure." 
	services="azure-portal" 
	documentationCenter="" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="adamab"/>

# Recursos relacionados y vinculados en la galería de iconos

La galería de iconos permite encontrar iconos para un recurso determinado y arrastrarlos a la hoja actual. Con la galería de iconos puede crear vistas de administración que abarcan los recursos. Para cualquier recurso especificado, los recursos relacionados incluyen todos los recursos que comparten el mismo grupo de recursos que el recurso, además de los recursos que se vinculan a o desde el recurso.

## Recursos vinculados en el Administrador de recursos de Azure

La vinculación es una característica del Administrador de recursos de Azure que permite declarar relaciones entre los recursos, incluso si no residen en el mismo grupo de recursos. La vinculación no influye en el tiempo de ejecución de los recursos ni tampoco en la facturación ni en el acceso basado en rol. Es simplemente un mecanismo que puede usar para representar relaciones para que herramientas como la galería de iconos puedan proporcionar una experiencia de administración más rica. Las herramientas pueden inspeccionar los vínculos mediante la API de vínculos y proporcionar también experiencias de administración de relaciones personalizadas.

## ¿Cómo puedo vincular mis recursos?

Al crear recursos a través del portal o mediante la implementación de una plantilla a través de Azure PowerShell o CLI de Azure, se crean automáticamente vínculos para algunos recursos dependientes. También puede vincular recursos mediante programación usando la [API de REST de recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx) o mediante la declaración de las relaciones en la plantilla. Para obtener una descripción completa de cómo trabajar con recursos vinculados, vea [Vinculación de recursos en el Administrador de recursos de Azure](../resource-group-link-resources.md).

## Pasos siguientes

- Si necesita una introducción a la escritura de plantillas del Administrador de recursos de Azure, vea [Creación de plantillas](../resource-group-authoring-templates.md).
- Para profundizar en la creación de vínculos entre los recursos, vea [Vinculación de recursos en el Administrador de recursos de Azure](../resource-group-link-resources.md).
- Para conocer más sobre cómo trabajar con grupos de recursos a través del Portal de vista previa, vea [Uso del Portal de vista previa de Azure para administrar los recursos de Azure](resource-group-portal.md).

<!---HONumber=Oct15_HO3-->