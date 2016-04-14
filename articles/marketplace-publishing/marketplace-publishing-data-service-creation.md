<properties
   pageTitle="Guía para crear un servicio de datos en Marketplace | Microsoft Azure"
   description="Se ofrecen instrucciones detalladas sobre cómo crear, certificar e implementar un servicio de datos para la venta en Azure Marketplace."
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
      ms.date="01/28/2016"
      ms.author="hascipio; avikova" />

# Guía de publicación de servicio de datos para Azure Marketplace
Después de completar el paso 1, [Creación y registro de la cuenta][link-acct-creation], le guiamos a través de los requisitos [no técnicos ](marketplace-publishing-pre-requisites.md) y [técnicos](marketplace-publishing-data-service-creation-prerequisites.md) generales de una oferta de servicio de datos en Azure Marketplace. Ahora le guiaremos a través de los pasos para crear una oferta de servicio de datos en el [Portal de publicación][link-pubportal] de Azure Marketplace.

## 1\. Inicie sesión en el Portal de publicación.

Vaya a [https://publish.windowsazure.com](https://publish.windowsazure.com.).

**Para el primer inicio de sesión en el Portal de publicación, use la misma cuenta con la que se registró el perfil de vendedor de la compañía en el Centro para desarrolladores.** (Posteriormente, puede agregar a cualquier empleado de la empresa como coadministrador en el Portal de publicación).

Haga clic en el icono **Publicar un servicio de datos** si se trata del primer inicio de sesión en el Portal de publicación.

## 2\. Elija **Servicios de datos** en el menú de navegación del lado izquierdo.

  ![dibujo](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## 3\. Creación de un nuevo servicio de datos

Complete el título de la nueva oferta de servicio de datos y haga clic en el signo "+" de la derecha.

  ![dibujo](media/marketplace-publishing-data-service-creation/step-3.png)

## 4\. Revise el submenú del servicio de datos recién creado en el menú de navegación.

Haga clic en la pestaña **Tutorial** y revise todos los pasos necesarios para publicar correctamente el servicio de datos en Azure Marketplace.

> [AZURE.TIP] Siempre puede hacer clic en los vínculos de la página "Tutorial" o usar las pestañas del submenú de la oferta del servicio de datos en el lado izquierdo.

## 5\. Creación de un nuevo plan

### Ofertas, Planes, Transacciones

Cada Oferta puede tener varios Planes, pero debe tener al menos un (1) Plan. Cuando los usuarios finales se suscriben a su oferta, también lo hacen en uno de los planes de la oferta. Cada plan define el modo en que los usuarios podrán hacer uso del servicio.

Actualmente, Azure Marketplace admite solo el modelo basado en transacciones de suscripciones mensuales para Servicios de datos; es decir, los usuarios finales pagarán una cuota mensual según el precio del plan específico al que se hayan suscrito y podrán consumir cada mes el número de transacciones definidas por el plan.

Cada Transacción se define normalmente como el número de registros que devolverá el Servicio de datos en función de la consulta enviada al Servicio. El valor predeterminado es 100. El número de transacciones devueltas para cada consulta será el número de registros dividido por 100 y redondeado al entero más cercano.

El nivel del Servicio de Azure Marketplace es quien se ocupa de supervisar (medir) el número de transacciones consumidas por cada consulta.

> [AZURE.IMPORTANT] Los usuarios finales que alcancen el límite de transacciones durante el mes no podrán seguir utilizando el servicio hasta el final de su ciclo de suscripción mensual.

> El plan (o uno de los planes) puede (pero no es obligatorio) incluir un número ilimitado de transacciones.

### Cree un plan.
1. Haga clic en **"+"** junto a "Agregar un nuevo plan".

2. Elija una de las opciones para el uso de este plan: **Ilimitado** o **Limitado**. Si elige Limitado, indique a continuación el número de transacciones que el plan permitirá consumir en un mes.

    ![dibujo](media/marketplace-publishing-data-service-creation/step-5.1.png)

    El Portal de publicación también le sugerirá el "Identificador del Plan", que se utilizará para comunicar a los usuarios finales el nombre del plan en la interfaz de usuario y también se utilizará por el servicio de Marketplace para identificar el Plan. Puede cambiar el "Identificador del Plan" si lo desea.

    > [AZURE.NOTE] El "Identificador del Plan" debe ser único dentro del ámbito de cada oferta. Como muchos otros identificadores utilizados en el Portal de publicación, el Identificador del Plan se bloqueará tras la primera publicación en producción y ya no podrá cambiarlo.

3. Haga clic para aceptar su elección.

4. A continuación, se le harán algunas preguntas adicionales sobre el Plan recién creado.

    ![dibujo](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Pregunta|Significado|
|----|----|
|**¿Este Plan es gratuito y está disponible en todo el mundo?**|Puede crear un plan completamente gratuito. Si es el único plan de esta oferta, eso significa que está publicando una "Oferta gratuita" en Marketplace. Si solo es para un Plan (o para unos pocos), se le dará la opción de ofrecer a los usuarios finales más información acerca del servicio con una cantidad relativamente reducida de transacciones al mes. Si la respuesta es "Sí", no se le harán más preguntas.|

> [AZURE.NOTE] Los usuarios finales siempre pueden actualizar a los planes de pago.

|Pregunta|Significado|
|----|----|
|**¿Hay una versión de evaluación gratuita disponible?**|Puede elegir entre "Sin evaluación" o permitir el uso de su Plan durante "Un mes". A los publicadores le gusta utilizar esta opción para proporcionar a los usuarios finales la posibilidad de comprender las ventajas de la oferta de forma gratuita durante un mes.|

> [AZURE.IMPORTANT] Los usuarios finales solo podrán adquirir una versión de evaluación gratuita si han configurado un instrumento de pago; por ejemplo, una tarjeta de crédito o un contrato Enterprise.

> Transcurrido un mes tras el comienzo del período de la evaluación gratuita, Azure Marketplace empezará cobrar a los clientes el precio a partir de la fecha de la suscripción, a menos que el cliente inicie la cancelación de la suscripción. Los usuarios finales no recibirán ninguna notificación especial.

|Pregunta|Significado|
|----|----|
|**¿Este plan requiere un código de promoción para comprar?**| Los publicadores tienen una opción para limitar el acceso a sus Planes del servicio proporcionando un código especial, llamado "Promocode" a clientes específicos. Solo los usuarios finales que tendrán este Promocode podrán suscribirse al Plan. Si elige "No", acepta que todo el mundo de la región donde está disponible la oferta (vea la [Guía de contenido de marketing de Marketplace](marketplace-publishing-push-to-staging.md) para obtener más detalles) podrá suscribirse a este plan. No se le harán más preguntas.|
|**¿Ocultar también este plan a cualquier persona que no tenga un código de promoción válido?**|Si la respuesta a la pregunta anterior es "Sí", el Publicador tiene la opción de hacer que este plan no aparezca en ninguna parte de la interfaz de usuario de Marketplace. Esto implica que los usuarios no verán este plan en la página de detalles de la Oferta. Los usuarios finales que reciban un Promocode para adquirirlo, podrán suscribirse a dicho plan utilizando ese código.|

## 6\. Creación de contenido de marketing de Marketplace
Para saber cómo proporcionar la información requerida en las pestañas de **marketing, establecimiento de precios, soporte técnico y categorías**, visite la [Guía de contenido de marketing de Marketplace](marketplace-publishing-push-to-staging.md), que es común para todos los artefactos publicados en Azure Marketplace.

## 7\. Conecte la oferta al servicio (basado en SQL Azure o Servicio web).

Haga clic en el submenú **Servicios de datos**.

En la mitad superior de la página, se le pedirá que proporcione el **Espacio de nombres** de la oferta.

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.png)

La siguiente pregunta definirá cómo expondrá el Publicador la oferta recién creada en Azure Marketplace. (Para obtener más detalles, consulte la [Guía de requisitos previos técnicos de Servicios de datos](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Publicación del servicio basado en Base de datos**

Haga clic en **Base de datos**. Aparecerá la siguiente página:

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.3.png)

Para crear una asignación de CSDL para el Conjunto de datos en función de la Base de datos de SQL Azure:

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.4.png)

Y, a continuación, para cada tabla

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.6.png)

Si Servicio web

  ![dibujo](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Lea [Asignación de un servicio web existente a OData mediante CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) para obtener instrucciones detalladas y ejemplos para crear un Servicio web de CSDL.

## Pasos siguientes
Ahora que ha creado la oferta de servicio de datos, asegúrese de completar las instrucciones de la [Guía de contenido de marketing de Marketplace](marketplace-publishing-push-to-staging.md) antes de avanzar a [Prueba del servicio de datos en ensayo](marketplace-publishing-data-service-test-in-staging.md).

## Otras referencias
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- Si le interesa entender el proceso de asignación de OData y el propósito general, lea este artículo sobre [Asignación de OData del servicio de datos](marketplace-publishing-data-service-creation-odata-mapping.md) para revisar las definiciones, las estructuras y las instrucciones.
- Si está interesado en aprender y comprender los nodos específicos y sus parámetros, lea este artículo sobre [Nodos de asignación de OData del servicio de datos](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para ver definiciones y explicaciones, ejemplos y contexto de caso de uso.
- Si está interesado en revisar ejemplos, lea este artículo sobre [Ejemplos de asignación de OData del servicio de datos](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver ejemplos de código y comprender el contexto y la sintaxis del código.


[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=AcomDC_0204_2016-->