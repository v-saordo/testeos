<properties
   pageTitle="Información general de creación e implementación de una oferta en Marketplace | Microsoft Azure"
   description="Conozca los pasos necesarios para convertirse en un desarrollador de Microsoft aprobado y crear e implementar una imagen de máquina virtual, una plantilla, un servicio de datos o un servicio de desarrolladores en Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="hascipio" />

# Publicación de una oferta en Azure Marketplace
Este artículo sirve de ayuda para desarrollar e implementar una solución en Azure Marketplace para que otros socios y clientes de Azure la compren y la usen.

Lo primero que le convendría hacer como publicador es definir el tipo de solución que ofrece su compañía. Azure Marketplace admite varias soluciones y cada una de ellas requiere que se establezca un conjunto de trabajo ligeramente diferente para publicar correctamente en Marketplace.

Tipos de soluciones:

- Imagen de máquina virtual
- Servicio de desarrolladores
- Servicio de datos
- Plantilla de solución

Algunos pasos se comparten entre los distintos tipos de soluciones. En este artículo se ofrece una breve introducción a los pasos que debe completar para cualquier tipo de solución.

> [AZURE.NOTE] Antes de empezar cualquier trabajo en Azure Marketplace, debe obtener la probación previa. Esto no se aplica a los publicadores de servicios de datos.

||Imagen de máquina virtual |Servicio de desarrolladores | Servicio de datos | Plantilla de solución |
|----|----|----|----|----|
| **Obtención de la aprobación previa** | [Microsoft Azure Certified][link-certification] | [Microsoft Azure Certified][link-certification] | N/D | [Microsoft Azure Certified][link-certification] |
| **Paso 1: Registro de la cuenta de desarrollador** | [Cuenta de Microsoft Developer: creación y registro][link-accts] | [Cuenta de Microsoft Developer: creación y registro][link-accts] | [Cuenta de Microsoft Developer: creación y registro][link-accts] | [Cuenta de Microsoft Developer: creación y registro][link-accts] |
|**Paso 2: Creación de la oferta**| [Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)| [Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)| [Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)| [Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)|
|| [Requisitos previos técnicos de máquina virtual][link-single-vm-prereq] | Requisitos previos técnicos del servicio de desarrolladores | [Requisitos previos técnicos del servicio de datos](marketplace-publishing-data-service-creation-prerequisites.md) | [Requisitos previos técnicos de la plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md) |
||[Guía de publicación de imágenes de máquina virtual][link-single-vm] | Guía de publicación de servicios de desarrollador | [Guía de publicación de servicios de datos](marketplace-publishing-data-service-creation.md) | [Guía de publicación de plantillas de solución](marketplace-publishing-solution-template-creation.md) |
|| [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] |
| **Paso 3: Traslado de la oferta al entorno de ensayo** | [Prueba de la oferta de máquina virtual en el entorno de ensayo](marketplace-publishing-vm-image-test-in-staging.md) | Prueba de la oferta de servicio de desarrollador en el entorno de ensayo | [Prueba de la oferta de servicio de datos en el entorno de ensayo](marketplace-publishing-data-service-test-in-staging.md) | [Prueba de la plantilla de solución en el entorno de ensayo](marketplace-publishing-solution-template-test-in-staging.md) |
| **Paso 4: Implementación de la oferta en Marketplace** | [Implementación de la oferta en Marketplace][link-pushprod] | [Implementación de la oferta en Marketplace][link-pushprod] | [Implementación de la oferta en Marketplace][link-pushprod] | [Implementación de la oferta en Marketplace][link-pushprod] |

## Soporte técnico
- [Obtención de soporte técnico como publicador][suppt-general]
- [Descripción de informes de perspectivas de vendedor][suppt-rpt-insights]
- [Descripción de informes de pago][suppt-rpt-payouts]
- [Cómo cambiar el incentivo de revendedores para proveedores de soluciones en la nube](marketplace-publishing-csp-incentive.md)
- [Solución de problemas comunes de publicación en Marketplace][suppt-common]

## Recursos adicionales
- Para más información sobre los portales que se usan, vea [Portales que necesitará](marketplace-publishing-portals.md).

**Máquinas virtuales**

- [Configuración de Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](../virtual-machines-windows-tutorial/)

**Servicios de datos**

- [Asignación de OData del servicio de datos](marketplace-publishing-data-service-creation-odata-mapping.md)
- [Nodos de asignación de OData del servicio de datos](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)
- [Ejemplos de asignación de OData del servicio de datos](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

[suppt-general]: marketplace-publishing-get-publisher-support.md
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md
[suppt-common]: marketplace-publishing-support-common-issues.md
[link-certification]: marketplace-publishing-azure-certification.md
[link-accts]: marketplace-publishing-accounts-creation-registration.md
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-pushprod]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0224_2016-->