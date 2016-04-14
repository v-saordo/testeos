<properties
   pageTitle="Información general de los diversos portales necesarios para crear una oferta en Marketplace | Microsoft Azure"
   description="Información general de los diversos portales necesarios para crear una oferta en Marketplace"
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
   ms.date="12/06/2015"
   ms.author="hascipio" />


# Portales que necesitará
Antes de que empiece el proceso de la publicación de una oferta, vamos a presentarle los diversos portales que necesitará. A continuación se muestra un resumen breve de los portales: Centro de desarrolladores, Portal de publicación de Azure y Portal de Azure, en el orden en que se interactúa con ellos.
## Centro de desarrolladores
[http://dev.windows.com](http://dev.windows.com/registration?accountprogram=azure)
### Descripción
La creación de la cuenta del Centro de desarrolladores de Microsoft es una tarea que solo se realiza una vez. Asegúrese de que la compañía no tenga ya una cuenta del Centro de desarrolladores antes de intentar crear una nueva. Durante el proceso, recopilamos los datos de su cuenta bancaria, su información fiscal y la dirección de la compañía.

> [AZURE.NOTE]Si solo va a publicar ofertas gratuitas (u ofertas de tipo traiga su propia licencia), no necesitamos información bancaria ni fiscal.

### Identidad y cuenta usadas
Lo ideal es una lista de distribución o un grupo de seguridad (por ejemplo, azurepublishing@*partnercompany*.com). La lista de distribución o el grupo de seguridad **debe** registrarse como cuenta de Microsoft.

> [AZURE.TIP]Se recomienda usar una lista de distribución o un grupo de seguridad porque que elimina la dependencia de cualquier persona aunque también se puede usar una cuenta individual.

## Portal de publicación
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Descripción
Este es el portal para que usa para trabajar en la ofertas y publicarla (marketing, precios, publicación, certificación si es aplicable, etc.).

### Identidad y cuenta usadas
Se debe usar la lista de distribución o el grupo de seguridad por primera vez para iniciar sesión en el portal de publicación. Más adelante, se pueden agregar otros usuarios como coadministradores. Así es cómo se asignan a los datos de registro del Centro para desarrolladores.

## Portal de Azure
[https://portal.azure.com](https://portal.azure.com)
### Descripción
Este es el portal donde puede ver sus ofertas publicadas y de ensayo en Azure Marketplace (aplicable a máquinas virtuales, plantillas de solución y servicios de desarrolladores basados en el Administrador de recursos de Azure).
### Identidad y cuenta usadas
Mientras pone en ensayo una oferta del portal de publicación, es preciso que se incluya un identificador de suscripción en la lista blanca. Para probar la oferta de ensayo, hay que usar la misma suscripción (no hay nombre de usuario ni contraseña asociados) al iniciar sesión en este portal.

## Consulte también
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_1210_2015-->