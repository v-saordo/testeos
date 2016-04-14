<properties
   pageTitle="Guía para crear una plantilla de solución en Marketplace | Microsoft Azure"
   description="Se ofrecen instrucciones detalladas sobre cómo crear, certificar e implementar una plantilla de solución de imagen de varias máquinas virtuales para la venta en Azure Marketplace."
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
      ms.date="02/06/2016"
      ms.author="hascipio; v-divte" />

# Guía para crear una plantilla de solución en Azure Marketplace
Después de completar el paso 1, [Creación y registro de cuentas][link-acct-creation], le guiamos en la creación de una plantilla de solución compatible con Azure en [Requisitos previos técnicos para crear una plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md). Ahora le guiaremos a través de los pasos para crear una plantilla de solución de varias máquinas virtuales en el [Portal de publicación][link-pubportal] de Azure Marketplace.

## Creación de la oferta de plantilla de solución en el Portal de publicación
Vaya a [https://publish.windowsazure.com](http://publish.windowsazure.com). Cuando inicie sesión por primera vez en el [Portal de publicación](https://publish.windowsazure.com/), use la misma cuenta con la que se registró el perfil de vendedor de la compañía. Posteriormente, puede agregar a cualquier empleado de la empresa como coadministrador en el Portal de publicación.

### 1\. Selección de "Plantillas de solución"

  ![dibujo][img-pubportal-menu-sol-templ]

### 2\. Creación de una plantilla de solución

  ![dibujo][img-pubportal-sol-templ-new]

### 3\. Comienzo con topologías
Una plantilla de solución es una "matriz" para todas sus topologías. Puede definir varias topologías en una oferta o plantilla de solución. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus topologías. Siga estos pasos para definir la oferta:
- Creación de una topología: el "identificador de la topología" suele ser el nombre de la topología en la plantilla de solución. El identificador de la topología se usará en la dirección URL, tal como se muestra a continuación:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portal de vista previa de Azure: https://ms.portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- Adición de una nueva versión

### 4\. Certificación de las versiones de topología
Cargue un archivo zip que contiene todos los archivos necesarios para realizar el aprovisionamiento de esa versión específica de la topología. Este archivo zip debe contener lo siguiente:
- Los archivos *mainTemplate.json* y *createUiDefinition.json* en su directorio raíz.
- Las plantillas vinculadas y todos los scripts requeridos.

Después de cargar el archivo zip, haga clic en **Solicitar certificación**. El equipo de certificación de Microsoft revisará los archivos y certificará la topología.

También puede validar la experiencia de creación sin la implementación real para el cliente mediante los pasos indicados a continuación.

1. Guarde el archivo *createUiDefinition.json* y genere la dirección URL absoluta. La dirección URL debe ser accesible públicamente.
2. Codifique la dirección URL mediante la herramienta en [[http://www.url-encode-decode.com/](http://www.url-encode-decode.com/)].
3. Reemplace el texto en negrita con la ubicación (dirección URL codificada) del archivo *createUiDefinition.json* que requiere validación.

  > https://portal.azure.com/?clientOptimizations=false#blade/Microsoft_Azure_Compute/CreateMultiVmWizardBlade/internal_bladeCallId/anything/internal_bladeCallerParams/ **{"initialData":{},"providerConfig":{"createUiDefinition":"http://yoururltocreateuidefinition.jsonURLencoded"}}**

4. Copie y pegue la dirección URL en cualquier explorador y vea la experiencia del cliente del archivo createUiDefinition.json.

  > [AZURE.TIP] Mientras los desarrolladores trabajan en la creación de las topologías de la plantilla de solución y en conseguir que se certifiquen, el departamento comercial, de marketing o legal de su empresa puede trabajar en el contenido de marketing y legal.

## Pasos siguientes
Ahora que ya creó la plantilla de solución y envió el archivo zip con los archivos necesarios para la certificación, puede continuar y seguir las instrucciones de la [Guía de contenido de marketing de Marketplace](marketplace-publishing-push-to-staging.md) para preparar la oferta para pruebas en ensayo. O bien, para ver el conjunto completo de artículos de la publicación de Marketplace, consulte [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md).

Es posible que también le interesen los siguientes artículos relacionados:

- Imágenes de máquinas virtuales: [Acerca de las imágenes de máquina virtual en Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

- Extensiones de VM: [Información general del agente de máquina virtual y las extensiones de VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) y [Características y extensiones de máquina virtual de Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)

- Administrador de recursos de Azure: [Creación de plantillas de Azure ARM](../resource-group-authoring-templates/) y [Ejemplos sencillos de plantillas de ARM](https://github.com/rjmax/ArmExamples)

- Limitaciones de cuentas de almacenamiento: [Supervisión de limitaciones de cuentas de almacenamiento](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) y [Almacenamiento Premium](../storage/storage-premium-storage.md#scalability-and-performance-targets-whes-ESing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=AcomDC_0302_2016-->