<properties 
 pageTitle="Planes y facturación en Programador de Azure" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>
 
# Planes y facturación en Programador de Azure

## Planes de colección de trabajos

Las colecciones de trabajos son la entidad facturable en Programador de Azure. Las colecciones de trabajos contienen varios trabajos y vienen en tres planes que se describen a continuación: Gratis, Standard y Premium.

|**Plan de colección de trabajos**|**Nº máximo de trabajos por colección de trabajos**|**Periodicidad máxima**|**Nº máximo de colecciones de trabajo por suscripción**|**Límites**|
|:---|:---|:---|:---|:---|
|**Gratis**|5 trabajos por colección de trabajos|Una vez por hora. No se pueden ejecutar trabajos con una frecuencia superior a una vez por hora|Se permite una suscripción de 1 colección de trabajos gratuita como máximo|No se puede usar un [objeto de autorización saliente HTTP](scheduler-outbound-authentication.md)
|**Standard**|50 trabajos por colección de trabajos|Una vez por minuto. No se pueden ejecutar trabajos con una frecuencia superior a una vez por minuto|Se permite una suscripción de 100 colecciones de trabajos estándar como máximo|Acceso al conjunto completo de características de Programador|
|**Premium**|50 trabajos por colección de trabajos|Una vez por minuto. No se pueden ejecutar trabajos con una frecuencia superior a una vez por minuto|Se permite una suscripción de 10 000 colecciones de trabajos premium como máximo. <a href="mailto:wapteams@microsoft.com">Póngase en contacto con nosotros</a> para obtener más información.|Acceso al conjunto completo de características de Programador|

## Actualizaciones y degradaciones de planes de colección de trabajos

Puede actualizar o degradar un plan de colección de trabajos siempre que quiera entre los planes Gratis, Standard y Premium. Sin embargo, al degradar a una colección de trabajos gratuita, puede que la degradación genere un error por uno de los siguientes motivos:

- Ya existe una colección de trabajos gratuita en la suscripción
- Un trabajo de la colección de trabajos tiene una periodicidad superior a la permitida para los trabajos de colecciones de trabajos gratuitas. La periodicidad máxima permitida en una colección de trabajos gratuita es una vez por hora
- Hay más de 5 trabajos en la colección de trabajos
- Un trabajo de la colección de trabajos tiene una acción HTTP o HTTPS que usa un [objeto de autorización saliente HTTP](scheduler-outbound-authentication.md)

## Planes de Azure y facturación

Las suscripciones no se cobran en colecciones de trabajos gratuitas. Si tiene más de 100 colecciones de trabajos estándar (10 unidades de facturación estándar), resulta más conveniente tener todas las colecciones de trabajo en el plan Premium.

Si tiene una colección de trabajos estándar y una colección de trabajos premium, se le facturará una unidad de facturación estándar _y_ una unidad de facturación premium. Las facturas del servicio Programador se basan en el número de colecciones de trabajos activas que se establecen en Standard o Premium; esto se explica más detalladamente en las dos secciones siguientes.

## Unidades facturables estándar

Una unidad facturable estándar puede incluir como máximo 10 colecciones de trabajos estándar. Puesto que una colección de trabajos estándar puede tener hasta 50 trabajos por colección de trabajos, una unidad de facturación estándar permite que una suscripción tenga hasta 500 trabajos: casi 22 millones de ejecuciones de trabajos al mes.

Si tiene entre 1 y 10 colecciones de trabajos estándar, se le facturará 1 unidad de facturación estándar. Si tiene entre 11 y 20 colecciones de trabajos estándar, se le facturarán 2 unidades de facturación estándar. Si tiene entre 21 y 30 colecciones de trabajos estándar, se le facturarán 3 unidades de facturación estándar.

## Unidades facturables premium

Una unidad facturable premium puede incluir como máximo 10.000 colecciones de trabajos premium. Puesto que una colección de trabajos premium puede tener hasta 50 trabajos por colección de trabajos, una unidad de facturación premium permite que una suscripción tenga hasta 500.000 trabajos: casi 22 mil millones de ejecuciones de trabajos al mes.

Si tiene entre 1 y 10.000 colecciones de trabajos premium, se le facturará 1 unidad de facturación premium. Si tiene entre 10.001 y 20.000 colecciones de trabajos premium, se le facturarán 2 unidades de facturación premium.

Por lo tanto, las colecciones de trabajos premium tienen la misma funcionalidad que las colecciones de trabajos estándar, pero ofrecen un descuento en caso de que la aplicación requiera gran cantidad de colecciones de trabajos.

## Estado de facturación y activo

Las colecciones de trabajos están siempre activas, salvo que la suscripción completa pase a un estado deshabilitado temporal debido a problemas de facturación. La única manera de garantizar que una colección de trabajos no se facture consiste en establecerla en el plan _Gratis_ o en eliminar la colección de trabajos.

Aunque puede deshabilitar todos los trabajos de una colección de trabajos en una sola operación, esto no cambia el estado de facturación de la colección de trabajos: la colección de trabajos se _seguirá_ facturando. De forma similar, las colecciones de trabajos vacías se consideran activas y se facturarán.

## Precios

Para obtener información detallada sobre los precios, vea [Precios de Programador](https://azure.microsoft.com/pricing/details/scheduler/).

## Otras referencias
 

 [¿Qué es Programador?](scheduler-intro.md)
 
 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)
 
  

  

<!---HONumber=AcomDC_0128_2016-->