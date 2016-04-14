<properties
   pageTitle="Preparación y prueba de la oferta para su implementación en Azure Marketplace | Microsoft Azure"
   description="Se ofrecen instrucciones detalladas sobre cómo proporcionar contenido de marketing, configurar planes de precios y probar la oferta antes de implementarla en Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="01/07/2016"
   ms.author="hascipio"/>

# Finalización de la creación de ofertas con contenido de marketing
En este paso del proceso de publicación, deberá proporcionar determinados contenidos de marketing y los detalles acerca de la oferta o SKU en Azure Marketplace. Por ejemplo, proporcionará una descripción de su producto, logotipos de empresa, planes de precios, detalles de los planes y otra información necesaria para insertar su oferta o SKU en el entorno de ensayo. Esta información se usa como contenido de marketing en nuestro Portal de Azure. Comenzará este proceso en el [portal de publicación][link-pubportal].

## Paso 1: Especificación de contenido de marketing de Marketplace
**El inglés es el idioma establecido de manera predeterminada y el único que se admite.** Asegúrese de que toda la información proporcionada en los campos esté en inglés. Toda la información se puede editar en cualquier momento hasta que pase a la etapa de ensayo.

  1. Vaya al portal de publicación, [https://publish.windowsazure.com](https://publish.windowsazure.com).
  2. En el menú de la izquierda, haga clic en la pestaña **Marketing**.
  3. En el panel principal, haga clic en el botón **Inglés (Estados Unidos)**.

### Detalles
1. Escriba un resumen de la oferta, un resumen amplio y la descripción de la oferta.
2.	Cargue imágenes de las especificaciones necesarias (mencionadas en el portal de publicación) en formato PNG, una por tamaño.

  ![dibujo](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *Título de la oferta, descripción y logotipos*

### Vínculos
En la pestaña **Vínculos** de la barra izquierda, incluya vínculos con información que puedan resultar útiles para los clientes. Escriba un nombre y una dirección URL para cada vínculo.

![dibujo](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Imágenes de ejemplo (opcional)
> [AZURE.NOTE] La inclusión de una imagen de ejemplo es un paso opcional. Puede completar el resto del contenido de marketing para cumplir los requisitos para la inserción en ensayo.

En la pestaña **Imágenes de ejemplo** del menú izquierdo, cargue una nueva imagen haciendo clic en **Cargar una nueva imagen**. Si tiene una imagen existente y desea reemplazarla, haga clic en **Reemplazar imagen**.

![dibujo](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### Planes
![dibujo](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Información legal
En la pestaña **Legal**, proporcione un vínculo a sus directivas o términos de uso. Escriba o pegue los términos en el cuadro grande **Términos de uso**.

> [AZURE.IMPORTANT] Todos los campos deben tener entradas, incluidas las imágenes, para poder pasar a la etapa de ensayo.


## Paso 2: Establecimiento de precios
### Modelos de precios
|Modelo de precios |Descripción |
|---------------|------------------------------------------|
|Base| Tarifa plana mensual que se paga en el momento de la compra; por ejemplo, 10 $/mes|
|Consumo (también conocido como uso, medidor) | Pago por uso, que define el publicador de la oferta. No se puede definir el superávit por puesto, por usuario, etc., ya que no existe un concepto de fracción de usuario ni la posibilidad de prorratear. El asociado informa del uso cada hora. El cliente paga al final del ciclo de facturación mensual en lugar de hacerlo por adelantado como en los planes mensuales. |
|Evaluación gratuita | El cliente puede usarla de forma gratuita durante un tiempo limitado y pagará tarifas normales a partir de entonces. |
|Nivel Gratis | El plan es siempre gratuito. |
| Migración (también conocido como conversión o actualización a una versión posterior o anterior) del plan | Concepto de un usuario que traslada su plan actual a otro plan aceptable; definido por el asociado. |

**Modelos de precios disponibles por tipo de oferta**

> [AZURE.IMPORTANT] La disponibilidad de ciertos modelos de precios varía según el tipo de la oferta. Consulte la siguiente tabla.

| | Solo base | Solo consumo | Base y consumo |
|---|---|---|---|
| Imagen de máquina virtual | No | Sí | No|
| Servicio de desarrolladores | Sí | Sí | Sí |
| Servicio de datos | Sí | No | No |

### 2\.1. Establecimiento de los precios de máquina virtual
> [AZURE.NOTE] BYOL solo es compatible con máquinas virtuales.

1.	En la pestaña **Precios**, verá todos los mercados admitidos. Seleccione el que corresponda para mostrar los campos de precios.
2.	El vínculo proporcionado en el portal de publicación mostrará información de precios para ayudarle a determinar los precios de las SKU.
3.	Si la SKU es BYOL, active la casilla para disponibilidad de SKU con licencia externa (BYOL).
4.	Si la SKU es cada hora, escriba los precios del software. Las SKU sin precios no estarán disponibles para su compra o uso.

  >[AZURE.NOTE] Si tiene SKU tanto con licencia BYOL como con base horaria, asegúrese de que ambos requisitos se incluyen: casilla BYOL y valores de precio para cada hora.

5.	Se abrirá un asistente para precios. Sígalo hasta completar sus precios, incluidos los precios en otros países si opta por permitir compras desde fuera del mercado especificado.
6.	Algunos países son países de envío ISV. Para vender en un país de envío ISV, debe poder cobrar y recaudar impuestos por sus SKU, así como calcular y pagar impuestos al gobierno del país. Microsoft no puede proporcionar asesoramiento legal ni fiscal. Vea la sección "Países a los que se vende de la oferta" en la Introducción de este documento para obtener más información sobre los países a los que se vende.

### 2.2. Establecer precios de servicio para desarrolladores
Los planes pueden ser cualquier combinación de base y consumo, donde base es precio mensual y superávit es el precio de pago por uso. (Vea a continuación para obtener más información).

**Ejemplo:** oferta de servicio de desarrolladores de Contoso

| Plan | Precio | Incluye | Ruta de migración |
|-------|------|-------|-------|
|Gratuito|0 $/mes|Funcionalidad básica|Puede migrar a cualquier otro plan.|
|Bronze|10 $/mes|La funcionalidad básica y una cuota de 1.000 de la característica X.|Puede migrar a los planes Bronze Plus, Silver y Gold.|
|Bronze Plus|Período de evaluación gratuita: 0 $/mes + 0 $/meter01 |La funcionalidad básica y una cuota de 10 000 de la característica X. Cuando se consume la cuota de la característica X, el cliente puede pagar por uso a través de meter01.|Puede migrar a los planes Silver Plus y Gold.|
|Bronze Plus| Período de pago (también conocido como caducidad de la evaluación gratuita): 10 $/mes + 0,05 $/meter01.|La funcionalidad básica y una cuota de 10 000 de la característica X. Cuando se consume la cuota de la característica X, el cliente puede pagar por uso a través de meter01.|Puede migrar a los planes Silver Plus y Gold.|
|Silver|$ 0,15/meter01|El cliente puede pagar por uso a través de meter01, que es para la característica X.|Puede migrar a los planes Bronze y Gold.|
|Silver Plus|20 $/mes + 0,15 $/meter01 + 0,01 dólares/meter02|La funcionalidad básica y una cuota de 10.000 de la característica X y de 100 de la característica Y. Cuando se consume la cuota de la característica X, el cliente puede pagar por uso a través de meter01. Cuando se consume la cuota de la característica Y, el cliente puede pagar por uso a través de meter02.|Puede migrar a los planes Bronze Plus y Gold.|
|Gold|1\.000 $/mes|Cuota de 10.000 de la característica X, 1.000 de la característica Y e ilimitada de la característica Z.|Puede migrar a todos los planes excepto el Gratis.|

## Paso 3: Especificación de información de soporte técnico
Parte de esta información se habrá cumplimentado durante el paso de certificación. Puede agregar o editar información mediante los pasos siguientes. Los detalles de contacto solo se usan para comunicaciones internas entre asociados y Microsoft. La dirección URL de soporte técnico estará disponible para los usuarios finales.

1.	Vaya al encabezado **Soporte técnico** de la izquierda del portal de publicación.
2.	Escriba la información de **Contacto de ingeniería**.
3.	Escriba la información de **Asistencia al cliente**. Si solo proporciona soporte técnico por correo electrónico, escriba un número de teléfono ficticio, así se utilizará su dirección de correo electrónico.
4.	Escriba la dirección URL de soporte técnico.

## Paso 4: Elección de categorías de Azure Marketplace
La pestaña **Categorías** proporciona una matriz de selecciones. Su oferta puede estar dentro de estas y es posible seleccionar hasta cinco categorías.

## Apariencia del marketing
A continuación se muestra una vista detallada de cómo se utiliza la información de marketing de la oferta en el [sitio web de Azure Marketplace](https://azure.microsoft.com/marketplace/) y en el [Portal de vista previa de Azure](https://ms.portal.azure.com).

### Sitio web de Azure Marketplace
![dibujo](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![dibujo](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Lista de las ofertas en el sitio web de Azure Marketplace*

![dibujo](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Detalles de descripción de la oferta en el sitio web de Azure Marketplace*

![dibujo](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Detalles de precios de la descripción de la oferta en el sitio web de Azure Marketplace*

### Portal de vista previa de Azure
![dibujo](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Lista de ofertas en el Portal de vista previa de Azure*

![dibujo](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Detalles de descripción de la oferta en el Portal de vista previa de Azure*

## Pasos siguientes
Ahora que el contenido de Marketplace está cargado, avanzamos a la prueba de la oferta en ensayo. Sin embargo, debe seleccionar el tipo de la oferta adecuado en la lista siguiente, ya que los pasos varían según el tipo de oferta.

|Imagen de máquina virtual |Servicio de desarrolladores | Servicio de datos | Plantilla de solución |
|----|----|----|----|----|
| **Paso 3. Traslado de la oferta al entorno de ensayo** | [Prueba de la oferta de máquina virtual en el entorno de ensayo](marketplace-publishing-vm-image-test-in-staging.md) | Prueba de la oferta de servicio de desarrollador en el entorno de ensayo | [Prueba de la oferta de servicio de datos en el entorno de ensayo](marketplace-publishing-data-service-test-in-staging.md) | [Prueba de la plantilla de solución en el entorno de ensayo](marketplace-publishing-solution-template-test-in-staging.md) |

## Consulte también
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0128_2016-->