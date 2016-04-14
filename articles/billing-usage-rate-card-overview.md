<properties
   pageTitle="Obtención de información sobre el consumo de recursos de Microsoft Azure | Microsoft Azure"
   description="Proporciona información general conceptual de las API de uso de facturación de Azure y de RateCard, que se utilizan para proporcionar información sobre el consumo de recursos y tendencias de Azure."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="02/19/2016"
   ms.author="mobandyo;bryanla"/>

# Obtención de información sobre el consumo de recursos de Microsoft Azure 

Los clientes y socios requieren la capacidad de predecir y administrar los costos de Azure con precisión. Con la transición de un modelo Capex a uno de Opex, también necesitan la capacidad de realizar análisis de showback (visibilidad completa de los gastos) frente a análisis de chargeback (contracargos), así como proporcionar fidelidad de modo en estimación y facturación, especialmente para implementaciones en la nube de gran tamaño.

Las API de uso de recursos de Azure y de RateCard que se tratan en este artículo resuelven estas necesidades al habilitar nuevas perspectivas en el consumo de recursos de Azure.

## Introducción a las API de uso de recursos de Azure y de RateCard 

Las API de uso de recursos de Azure y de RateCard se implementan como proveedor de recursos, como parte de la familia de las API expuestas por Administrador de recursos de Azure.

### API de uso de recursos de Azure (vista previa)
Los clientes y socios pueden utilizar la API de uso de recursos de Azure para obtener los datos de consumo de Azure estimados. Las características incluyen:
	
- **Control de acceso basado en roles de Azure**: los clientes y socios pueden configurar sus directivas de acceso en el [Portal de vista previa de Azure](https://portal.azure.com) o en [Cmdlets de Azure PowerShell](powershell-install-configure.md) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de uso de la suscripción. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. El autor de llamada también debe agregarse al rol Lector, Propietario o Colaborador para obtener acceso a los datos de uso para una suscripción de Azure.

- **Agregaciones cada hora o diarias**: los autores de llamadas pueden especificar si desean sus datos de uso de Azure en depósitos cada hora o diarios. El valor predeterminado es diario.

- **Metadatos de instancia proporcionados (incluye etiquetas de recursos)**: en la respuesta se proporcionan detalles de nivel de instancia como el URI de recurso completo (/subscriptions/{subscription-id}/..), junto con la información del grupo de recursos y las etiquetas de recursos. Esto ayudará a los clientes de forma determinista y mediante programación a asignar el uso mediante las etiquetas, para casos de uso como cargos cruzados.

- **Metadatos de recursos proporcionados**: detalles de recursos, como nombre de medidor, categoría de medidor, subcategoría de medidor, unidad y región, también se transmitirán en la respuesta para que los autores de llamadas conozcan mejor lo que han consumido. También estamos trabajando para alinear la terminología de metadatos de recursos en todo el portal de Azure, CSV de uso de Azure, CSV de facturación de EA y otras experiencias orientadas al público, para que los clientes puedan poner en correlación los datos en todas las experiencias.

- **Uso para todos los tipos de ofertas**: los datos de uso serán accesibles para todos los tipos de ofertas incluidos pago por uso, MSDN, compromiso monetario, crédito monetario y EA, entre otros.

### API de RateCard de recursos de Azure (vista previa)
Los clientes y socios pueden utilizar la API de RateCard de recursos de Azure para obtener la lista de recursos disponibles de Azure, junto con una información de precios estimada para cada uno. Las características incluyen:

- **Control de acceso basado en roles de Azure**: los clientes y socios pueden configurar sus directivas de acceso en el [Portal de vista previa de Azure](https://portal.azure.com) o en [Cmdlets de Azure PowerShell](powershell-install-configure.md) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de RateCard. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. El autor de llamada también debe agregarse al rol Lector, Propietario o Colaborador para obtener acceso a los datos de uso para una suscripción de Azure.
	
- **Soporte para ofertas de pago por uso, MSDN, compromiso monetario y crédito monetario (no compatible con EA)**: esta API proporciona información de tarifas de nivel de oferta de Azure, frente a nivel de suscripción. El autor de llamada de esta API debe pasar la información de oferta para obtener detalles y tarifas de recursos Como las ofertas de EA tienen tarifas personalizadas por inscripción, no es posible proporcionar tarifas de EA en este momento.

## Escenarios

Éstos son algunos de los escenarios posibles con la combinación de las API de uso y de RateCard:

- **Gastos de Azure durante el mes**: los clientes pueden usar las API de uso y de RateCard en combinación para obtener una mejor perspectiva de sus gastos en la nube durante el mes, mediante el análisis de los depósitos por hora y diarios de las estimaciones de usos y cargos. 

- **Configuración de alertas**: los clientes y socios pueden configurar alertas basadas en recursos o basadas en importes monetarios sobre su consumo en la nube al obtener las estimaciones de consumo y de cargo mediante las API de uso y de RateCard.

- **Predicción de facturas**: los clientes y socios pueden obtener el consumo y los gastos en la nube estimados, y aplicar algoritmos de aprendizaje automático para predecir cuál sería la factura al final del ciclo de facturación.

- **Análisis de costos previos al consumo**: los clientes también pueden utilizar la API de RateCard para predecir el importe de su factura si desplazaran sus cargas de trabajo a Azure, proporcionando los números de uso que deseen. Si los clientes tienen cargas de trabajo existentes en otras nubes o nubes privadas, también pueden asignar su uso con las tarifas de Azure para obtener una estimación más precisa del gasto previsto de Azure. Esto proporciona una mejor vista de lo que se puede obtener a través de la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) ya que, por ejemplo, nuestros socios de facturación proporcionan la capacidad de moverse sobre la oferta y comparar/contrastar entre distintos tipos de oferta más allá de pago por uso, incluidos el compromiso monetario y el crédito monetario. Las API también proporcionan la capacidad de realizar cambios de estimación de costos por región, activando el tipo de análisis de hipótesis necesario para tomar decisiones de implementación, ya que la implementación de recursos en distintos controladores de dominio de todo el mundo pueden tener un impacto directo en el costo total.

- **Análisis de hipótesis**:

	- Los clientes y socios pueden determinar si es más rentable ejecutar las cargas de trabajo en otra región o en otra configuración del recurso de Azure. Los costos de recursos de Azure pueden diferir en función de la región de Azure en la que se están ejecutando y esto permite que clientes y socios obtengan optimizaciones de costos.

	- Los clientes y socios también pueden determinar si otro tipo de oferta de Azure ofrece una mejor tarifa en un recurso de Azure.

## Soluciones de socios

[API de uso de Microsoft Azure y de RateCard que permiten a Cloudyn proporcionar ITFM a clientes](billing-usage-rate-card-partner-solution-cloudyn.md) describe la experiencia de integración ofrecida por el socio de API de facturación de Azure, [Cloudyn](https://www.cloudyn.com/microsoft-azure/). Este artículo proporciona una cobertura detallada sobre sus experiencias, incluido un breve vídeo que muestra cómo un cliente de Azure puede utilizar Cloudyn y las API de facturación de Azure para tener unas perspectivas de sus datos de consumo de Azure.

[Integración de Cloud Cruiser y de las API de facturación de Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) describe cómo funciona [Cloud Cruiser Express para el paquete de Azure](http://www.cloudcruiser.com/partners/microsoft/) directamente desde el portal WAP, lo que permite a los clientes administrar perfectamente los aspectos operativos y financieros de su nube privada o pública hospedada de Microsoft Azure desde una única interfaz de usuario.

## Pasos siguientes
+ Consulte [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (Referencia de API de REST de facturación de Azure) para obtener más detalles sobre ambas API, que forman parte del conjunto de API proporcionadas por Administrador de recursos de Azure.
+ Si quiere profundizar en el código de ejemplo, consulte nuestros ejemplos de código de la API de facturación de Microsoft Azure en [Ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## Más información
+ Consulte el artículo [Información general de Administrador de recursos de Azure](resource-group-overview.md) para obtener más información sobre Administrador de recursos de Azure.
+ Para obtener información adicional acerca del conjunto de herramientas necesarias para ayudarle a comprender el gasto en nube, consulte el artículo de Gartner [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb) (Guía de mercado para las herramientas de administración financiera de TI (ITFM)).

<!---HONumber=AcomDC_0302_2016-->