<properties
   pageTitle="Probar su oferta de VM para Marketplace | Microsoft Azure"
   description="Entienda cómo probar su imagen de VM para Azure Marketplace."
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
   ms.date="02/01/2016"
   ms.author="hascipio" />

# Probar su oferta de VM para Azure Marketplace en un entorno de ensayo

En la etapa de ensayo, se implementa la SKU en un “espacio aislado” privado, donde puede probar y validar su funcionalidad antes de implementarla en Marketplace. La SKU aparece en un entorno de ensayo tal y como lo haría para un cliente que la ha implementado. Su imagen de VM debe estar certificada para trasladarse a un entorno de ensayo.

## Paso 1: trasladar su oferta a un entorno de ensayo

1. Haga clic en **Publicar** haga clic en **Enviar a ensayo**.

  ![dibujo](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Si el Portal de publicación le informa errores, corríjalos.
3.	En el cuadro de diálogo **¿Quién puede obtener acceso a su oferta de ensayo?**, escriba la lista de suscripciones de Azure que usará para obtener una vista previa de su oferta en el [portal de vista previa de Azure](https://portal.azure.com).
4. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com) mediante una de las suscripciones de Azure anteriores que se enumeran en el paso anterior.
5. Busque su oferta y valide los puntos de su imagen de VM:
  - Asegúrese de que el contenido de marketing se muestra correctamente en el Marketplace.
  - Implementación completa de la imagen de VM.
  
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)




> [AZURE.IMPORTANT] Su oferta permanecerá en un entorno de ensayo hasta que notifique a Microsoft a través del Portal de publicación [**Publicar** > **"Solicitar aprobación para enviar a producción"**] que está listo para enviar a producción. Este es un momento ideal para que todos los miembros de su equipo revisen todo antes de hacer efectiva la oferta.

> La replicación por los centros de datos tarda hasta 48 horas. Cuando se complete la replicación, la oferta se mostrará en [Azure Marketplace](https://azure.microsoft.com/marketplace/).

## Pasos siguientes
Ahora que el estado de su oferta es "de ensayo" y que ha probado su funcionalidad y contenido de marketing, puede continuar con la fase de publicación final, el **Paso 4**: [Implementación de su oferta en Marketplace](marketplace-publishing-push-to-production.md).

## Consulte también
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0204_2016-->