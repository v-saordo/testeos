<properties
   pageTitle="El uso de Microsoft Azure y las API de RateCard permiten a Cloudyn proporcionar ITFM a los clientes | Microsoft Azure"
   description="Proporciona una perspectiva exclusiva del socio de facturación de Microsoft Azure Cloudyn sobre sus experiencias de integración de las API de facturación de Azure en su producto. Esto es especialmente útil para los clientes de Azure y de Cloudyn que están interesados en usar o probar Cloudyn para servicios de Azure."
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

# API de uso de Microsoft Azure y de RateCard que permiten a Cloudyn proporcionar ITFM a clientes 

Se ha elegido a Cloudyn, un socio de desarrollo de Microsoft y un proveedor líder en capacidades de administración en la nube, para una vista previa privada de las nuevas API de uso de recursos de Microsoft Azure y de RateCard. La API de uso proporciona acceso a datos de consumo de Azure estimados para una suscripción. La API de RateCard proporciona una información completa de precios de todos los servicios de Azure para los clientes que no son de contrato Enterprise (EA). Integradas juntas, estas API ofrecen una base de información completa para introducirla en herramientas de administración financiera de TI (ITFM) como las proporcionadas por Cloudyn.

## Introducción 

La llamada "multiplicación" de datos de la API de uso con datos de la API de RateCard (uso [unidades] precio [$unit] = uso y costo detallados) crea la información de facturación más pormenorizada, precisa y fiable disponible actualmente para Azure.

![Información general de ITFM][1]

El consumo de estas API ofrece información clave sobre el uso y los costos de los clientes, lo que permite a Cloudyn analizar las cuentas de clientes de manera simple mediante programación y realizar diversas tareas de ITFM para sus clientes.

## Integración de Cloudyn con las API de uso y de RateCard
La API de RateCard requiere varios parámetros de entrada, como información de región, moneda y configuración regional, pero el más importante es OfferDurableID, que especifica el tipo de oferta de Azure que utiliza el cliente (pago por uso, planes de compromisos heredados de 6 y 12 meses, ofertas MSDN, ofertas MPN, ofertas promocionales y otros). OfferDurableID puede encontrarse en el [Portal de uso y facturación de Azure](https://account.windowsazure.com/Subscriptions), en "Id. de oferta" para la suscripción proporcionada.

Al registrarse para los servicios de [Cloudyn para Azure](https://www.cloudyn.com/microsoft-azure/), los clientes pueden agregar su código OfferDurableID, lo que permite a Cloudyn extraer información de precios importante a través de la API de RateCard. Puede encontrar información sobre los distintos tipos de ofertas en la página [Detalles de las ofertas de Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/).

![Información general del motor ITFM de Cloudyn][2]

Cloudyn utiliza las API de uso y de RateCard, además de la API de rendimiento de Azure, para crear capas adicionales de visualización, análisis, alertas, creación de informes, administración de costos y recomendaciones viables, y proporciona una herramienta de ITFM en la nube empresarial de confianza para los clientes de Azure.

## Casos de uso de ITFM de Cloudyn habilitados por la integración de las API de uso y de RateCard 
Entre los casos de uso de ITFM de Cloudyn comunes habilitados por la integración de las API de uso y de RateCard se incluyen:

+ **Análisis de costos**: permite que los costos en la nube sean desglosados hasta cualquier dimensión de identificación nativa (proveedor, servicio, cuenta, región, etc.). Las API de uso de Azure y de RateCard facilitan esta tarea al proporcionar el desglose más pormenorizado de los datos de uso y costos por cuenta, agrupados y filtrados luego por Cloudyn y presentados al usuario en formato gráfico o tabular.

![Gráfico circular de análisis de costos][3]

+ **Asignación de costos 360**: permite a los administradores de finanzas y TI descubrir el desglose de costos reales, factores y tendencias de su implementación en la nube. Además, permite a los administradores asociar fácilmente gastos de implementación a unidades de negocio, departamentos, regiones, etc., lo que proporciona unos conocimientos sin precedentes de los costos en la nube y facilita chargebacks (contracargos) y showbacks (visibilidad completa de los gastos) de la empresa. Las API de uso de Azure y de RateCard sirven de entrada al motor de asignación de costos de Cloudyn, lo que complementa las API mediante la definición de métodos y lógica de negocios para la asignación de recursos sin etiquetar o que no se pueden etiquetar.

![Gráfico de asignación de costos 360][4]

+ **Ajuste de tamaño rentable**: proporciona recomendaciones de ajuste de tamaño para máquinas virtuales infrautilizadas, lo que reduce los gastos del cliente en máquinas sobredimensionadas o provisionadas en exceso. Se hace examinando la CPU de la máquina virtual y las métricas de RAM (con la API de rendimiento), las horas de tiempo de ejecución (con la API de uso) y el costo (con la API de RateCard). Cloudyn proporciona, a continuación, recomendaciones de ajuste de tamaño en función de los recursos de CPU o memoria RAM (rendimiento) infrautilizados y calcula el ahorro estimado derivado multiplicando el diferencial de precios (RateCard) entre las máquinas virtuales por el uso de tiempo real (Uso) de la máquina infrautilizada. 

![Ajuste de tamaño rentable][5]

+ **Recomendaciones sobre portabilidad en nube**: proporciona asesoramiento financiero sobre portabilidad en nube. Examina los costos actuales de los recursos en la nube de un usuario, que se implementan en proveedores en nube importantes y los compara con el costo de una implementación equivalente en Azure. A continuación, proporciona recomendaciones de portabilidad pormenorizadas, por recurso y basadas en finanzas para Azure. Después de evaluar la implementación equivalente requerida en Azure (según las preferencias de usuario y las métricas de rendimiento), Cloudyn usa la API de RateCard para evaluar el costo de la implementación equivalente en Azure.

+ **Informes de rendimiento**: habilitados por la API de rendimiento de Azure, estos informes proporcionan una serie de características desde uso de CPU y RAM hasta recomendaciones de optimización. A continuación se muestra un ejemplo de informe de utilización de instancia, que presenta un desglose de instancia por uso medio de CPU.

![Informes de rendimiento][6]

+ **Administrador de categoría**: una potente característica de Cloudyn que pone orden en los recursos en nube desorganizados. Proporciona a los usuarios la libertad de crear sus propias categorías exclusivas (etiquetas) para unas mediciones y creación de informes eficaces que se adaptan a sus prácticas empresariales. Además, los usuarios pueden fácilmente regular y clasificar etiquetas incoherentes (por ejemplo, errores tipográficos y otras discrepancias) y detectar automáticamente recursos sin etiquetas para una atribución precisa de los costos.

![Administrador de categorías][7]

## Vídeo 

Este es un breve vídeo que muestra cómo un cliente de Azure puede utilizar Cloudyn para Azure y las API de facturación de Azure para tener una mejor perspectiva de sus datos de consumo de Azure.
 
> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## Pasos siguientes

+ Inicie una prueba gratuita de [Cloudyn para Azure](https://www.cloudyn.com/microsoft-azure/) para ver cómo puede obtener transparencia en los costos gracias a la creación de informes y análisis personalizados para la implementación en nube de Microsoft Azure.
+ Consulte [Obtención de información sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md) para obtener información general sobre las API de uso de recursos de Azure y de RateCard. 
+ Consulte [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (Referencia de API de REST de facturación de Azure) para obtener más información sobre ambas API, que forman parte del conjunto de API proporcionadas por Administrador de recursos de Azure.
+ Si quiere profundizar en el código de ejemplo, consulte nuestros ejemplos de código de la API de facturación de Microsoft Azure en [Ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## Más información
+ Para obtener más información acerca de las ofertas del contrato Enterprise (EA) de Microsoft Azure, visite [Licencias de Azure para la empresa](https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Consulte el artículo [Información general de Administrador de recursos de Azure](resource-group-overview.md) para obtener más información sobre Administrador de recursos de Azure.
+ Para obtener información adicional acerca del conjunto de herramientas necesarias para ayudarle a comprender el gasto en nube, consulte el artículo de Gartner [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb) (Guía de mercado para las herramientas de administración financiera de TI (ITFM)).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png

<!---HONumber=AcomDC_0302_2016-->