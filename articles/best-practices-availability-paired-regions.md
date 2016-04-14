<properties
	pageTitle="Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure | Microsoft Azure"
	description="Las parejas regionales de Azure garantizan que las aplicaciones serán resistentes durante los errores en el centro de datos."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="raynew"/>

# Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure

## ¿Qué son las regiones emparejadas?

Azure funciona en varias ubicaciones geográficas del mundo. Una ubicación geográfica de Azure es un área definida del mundo que contiene al menos una región de Azure. Una región de Azure es un área dentro de una ubicación geográfica que contiene uno o varios centros de datos.

Cada región de Azure se empareja con otra región dentro de la misma ubicación geográfica (a excepción del Sur de Brasil, que se corresponde con un área fuera de su ubicación geográfica) y ambas forman una pareja regional.


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Ilustración 1: Diagrama de pareja regional de Azure



| Geography | Regiones emparejadas | |
| :-------------| :-------------   | :-------------   |
| Norteamérica | Centro-Norte de EE. UU | Centro-Sur de EE. UU |
| Norteamérica | Este de EE. UU. | Oeste de EE. UU. |
| Norteamérica | Este de EE. UU. - 2 | Centro de EE. UU. |
| Europa | Europa del Norte | Europa occidental |
| Asia | Sudeste de Asia | Asia oriental |
| China | Este de China | Norte de China |
| Japón | Este de Japón | Oeste de Japón |
| Brasil | Sur de Brasil (1) | Centro-Sur de EE. UU |
| Australia | Australia Oriental | Sudeste de Australia|
| Gobierno de Estados Unidos | Gobierno de EE. UU. - Iowa | Gobierno de EE. UU. - Virginia |
| India | India Central | Sur de la India |

Tabla 1: Asignación de las parejas regionales de Azure

> Sur de Brasil (1) es un caso único porque se empareja con una región fuera de su propia ubicación geográfica. Tenga en cuenta que la región secundaria del Sur de Brasil es Centro-Sur de EE. UU, pero la región secundaria de esta última no es Sur de Brasil.

Se recomienda que replique las cargas de trabajo entre las parejas regionales para beneficiarse de las directivas de aislamiento y disponibilidad de Azure. Por ejemplo, las actualizaciones planeadas del sistema de Azure se implementan de forma secuencial (no al mismo tiempo) entre regiones emparejadas. Esto significa que, incluso en el caso excepcional de una actualización defectuosa, ambas regiones no se verán afectadas al mismo tiempo. Además, en el improbable caso de una interrupción amplia, se da prioridad a la recuperación de al menos una región de cada pareja.

## Un ejemplo de regiones emparejadas
La figura 2 muestra una aplicación hipotética que utiliza el par regional para la recuperación ante desastres. Los números verdes resaltan las actividades entre regiones de tres servicios de Azure (Proceso, Almacenamiento y Base de datos de Azure) y cómo están configurados para replicar entre regiones. Con los números de color naranja se resaltan los beneficios exclusivos de la implementación en regiones emparejadas.


![Información general sobre las ventajas de las regiones emparejadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Ilustración 2: Pareja regional de Azure hipotética

## Actividades entre regiones
Como se indica en la ilustración 2.

![1Green](./media/best-practices-availability-paired-regions/1Green.png) **Proceso de Azure (Paas)**: debe aprovisionar recursos de procesos adicionales de antemano para asegurarse de que haya recursos disponibles en otra región durante un desastre. Para obtener más información, consulte [Guía técnica sobre continuidad empresarial de Azure](https://msdn.microsoft.com/library/azure/hh873027.aspx).

![2Green](./media/best-practices-availability-paired-regions/2Green.png) **Almacenamiento de Azure**: el almacenamiento con redundancia geográfica (GRS) se configura de manera predeterminada cuando se crea una cuenta de Almacenamiento de Azure. Con GRS, los datos se replican automáticamente tres veces dentro de la región primaria y tres veces en la región emparejada. Para obtener más información, consulte [Opciones de redundancia de Almacenamiento de Azure](../storage/storage-redundancy.md).


![3Green](./media/best-practices-availability-paired-regions/3Green.png) **Bases de datos SQL de Azure**: con la replicación geográfica estándar de SQL de Azure puede configurar la replicación asincrónica de transacciones en una región emparejada. Con la replicación geográfica Premium, puede configurar la replicación en cualquier región del mundo; sin embargo, se recomienda implementar estos recursos en una región emparejada para la mayoría de los escenarios de recuperación ante desastres. Para obtener más información, consulte [Replicación geográfica en Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn783447.aspx).

![4Green](./media/best-practices-availability-paired-regions/4Green.png) **Administrador de recursos de Azure (ARM)**: el Administrador de recursos de Azure (ARM) proporciona de forma inherente aislamiento lógico para los componentes de administración de servicios entre regiones. Esto significa que es menos probable que los errores lógicos de una región afecten a otra.

## Ventajas de las regiones emparejadas
Como se indica en la ilustración 2.

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png) **Aislamiento físico**: cuando sea posible, Azure prefiere por lo menos 300 millas de separación entre los centros de datos de una pareja regional, aunque esto no será práctico o posible en todo el mundo. La separación del centro de datos físico reduce la probabilidad de que los desastres naturales, los disturbios civiles, los cortes del suministro eléctrico o las interrupciones de la red física afecten simultáneamente a ambas regiones. El aislamiento está sujeto a las restricciones geográficas (tamaño de la ubicación geográfica, disponibilidad de la infraestructura de red/energía, normativas, etc.).

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)**Replicación proporcionada por la plataforma**: algunos servicios, como el almacenamiento con redundancia geográfica ofrecen replicación automática a la región emparejada.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png) **Orden de recuperación de región**: en el caso de una interrupción amplia, tiene prioridad la recuperación de una región por cada pareja. Se garantiza que, si las aplicaciones se implementan en regiones emparejadas, se dará prioridad a la recuperación de una de las regiones. Si una aplicación se implementa en regiones que no están emparejadas, podría retrasarse la recuperación; en el peor de los casos, es posible que las regiones elegidas sean las dos últimas en recuperarse.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png) **Actualizaciones secuenciales**: las actualizaciones planeadas del sistema de Azure se implementan en regiones emparejadas de forma secuencial (no al mismo tiempo) para minimizar el tiempo de inactividad, el efecto de los errores y la aparición de errores lógicos en el infrecuente caso de una actualización incorrecta.


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png) **Residencia de datos**: una región reside dentro de la misma ubicación geográfica que su pareja (a excepción del Sur de Brasil) con el objeto de cumplir los requisitos de residencia de datos para fines de jurisdicción de impuestos y aplicación de la ley.

<!---HONumber=AcomDC_0121_2016-->