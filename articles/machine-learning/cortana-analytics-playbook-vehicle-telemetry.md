<properties 
	pageTitle="Cuaderno de estrategias de soluciones de análisis de telemetría de vehículo | Microsoft Azure" 
	description="Use las capacidades de Análisis de Cortana para obtener información en tiempo real y predictiva del estado del vehículo y hábitos de conducción." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Cuaderno de estrategias de soluciones de análisis de telemetría de vehículo

Este **menú** vincula a los capítulos de este cuaderno de estrategias.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## Información general
Se han incorporado superequipos a coches vanguardistas, con una infinidad de sensores, lo que ofrece la posibilidad de realizar un seguimiento y de supervisar millones de eventos por segundo. Esperamos que para el 2020, la mayoría de estos vehículos se conectará a Internet. Imagínese explotar esta riqueza de datos para ofrece la mejor seguridad, confiabilidad y experiencia de conducción de su clase. Microsoft ha hecho que esto sea posible mediante el Análisis de Cortana.

El Análisis de Cortana de Microsoft es un conjunto de aplicaciones de análisis avanzado y macrodatos completamente administrados que le permiten transformar sus datos en acciones inteligentes. Queremos presentarle la plantilla de la solución de análisis de telemetría de vehículos de Análisis de Cortana. Esta solución muestra cómo los fabricantes y los concesionarios de automóviles, y las compañías de seguros pueden usar las capacidades de Análisis de Cortana para obtener información predictiva y en tiempo real sobre el estado del vehículo y los hábitos de conducción.

La solución se implementa como un [patrón de arquitectura lambda](https://en.wikipedia.org/wiki/Lambda_architecture) en el que se muestra el potencial completo de la plataforma de Análisis de Cortana para tiempo real y el procesamiento por lotes. Incluye un simulador de telemetría de vehículos, aprovecha los centros de eventos para introducir millones de eventos de telemetría de vehículos simulados en Azure y luego usa Análisis de transmisiones para obtener información en tiempo real sobre el estado del vehículo y mantiene esos datos en un almacenamiento a largo plazo para análisis de lotes más enriquecidos. Aprovecha las ventajas del aprendizaje automático para la detección de anomalías en tiempo real y el procesamiento por lotes para obtener información predictiva. HDInsight se aprovecha para transformar los datos a escala y la factoría de datos controla la orquestación, la programación, la administración de recursos y la supervisión de la canalización del procesamiento por lotes. Por último, Power BI ofrece a esta solución un panel completo para datos en tiempo real y visualizaciones de análisis predictivo.

## Arquitectura

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *Ilustración 1 – Arquitectura de la solución de análisis de telemetría de vehículo*

Esta solución incluye los siguientes **componentes del Análisis de Cortana** y presenta su integración completa.


- **Centros de eventos** para la introducción de millones de eventos de telemetría de vehículo en Azure.
- **Análisis de transmisiones** para obtener información en tiempo real sobre el estado del vehículo y conserva los datos en el almacenamiento a largo plazo para análisis de lotes más completo.
- **Aprendizaje automático** para la detección de anomalías en tiempo real y el procesamiento por lotes para obtener información predictiva.
- **HDInsight** se aprovecha para transformar datos a escala.
- La **Factoría de datos** controla la orquestación, la programación, la administración de recursos y la supervisión de la canalización del procesamiento por lotes.
- **Power BI** ofrece a esta solución un panel completo para datos en tiempo real y visualizaciones de análisis predictivo.

Esta solución tiene acceso a dos **orígenes de datos** diferentes:

- **Diagnósticos y señales de vehículos simuladas**: un simulador telemático de vehículo emite información de diagnóstico y señales que se corresponden con el estado del vehículo y el patrón de conducción en un momento determinado. 
- **Catálogo de vehículo**: un conjunto de datos de referencia que contiene una asignación de número de identificación del vehículo a modelo.

<!---HONumber=AcomDC_1203_2015-->