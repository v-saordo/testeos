<properties 
	pageTitle="Caso de uso - Generación de perfiles de clientes" 
	description="Obtenga información sobre cómo se usa Factoría de datos de Azure para crear un flujo de trabajo orientado a datos (canalización) para generar perfiles de clientes de juegos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Caso de uso - Generación de perfiles de clientes

Factoría de datos de Azure es uno de los muchos servicios que se usan para implementar el conjunto de aplicaciones Cortana Analytics de aceleradores de soluciones. Para obtener más información acerca de Cortana Analytics, visite[Conjunto de aplicaciones de Cortana Analytics](http://www.microsoft.com/cortanaanalytics). En este documento se describe un caso de uso sencillo para que pueda comprender cómo Factoría de datos de Azure puede resolver los problemas comunes de análisis.

Todo lo que necesita para acceder y probar este caso de uso sencillo es una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/). Puede implementar un ejemplo que ponga en marcha este caso de uso siguiendo los pasos descritos en el artículo [Ejemplos](data-factory-samples.md).

## Escenario

Contoso es una empresa de juegos que crea juegos para varias plataformas: consolas de juegos, dispositivos portátiles y PC. Como los usuarios juegan a estos juegos, se generan grandes volúmenes de datos de registro que realizan el seguimiento de los patrones de uso, estilo de juegos y preferencias del usuario. Cuando se combinan con datos demográficos, regionales y de productos, Contoso realiza análisis para guiar a los usuarios sobre cómo mejorar su experiencia y orientarles sobre actualizaciones y compras en el juego.

El objetivo de Contoso es identificar oportunidades de aumento de ventas potenciales y las ventas cruzadas basadas en el perfil del historial de juegos de sus usuarios y desarrollar nuevas características atractivas para impulsar el crecimiento del negocio y ofrecer una mejor experiencia a los clientes. Para este caso de uso, usamos una empresa de juegos como ejemplo de una empresa que desea optimizar el comportamiento de los usuarios, pero estos principios se aplican a cualquier empresa que desea comprometer a sus clientes en torno a sus productos y servicios y mejorar la experiencia de sus clientes.

## Desafíos

Existen muchos desafíos a los que se enfrentan las empresas de juegos al intentar implementar este tipo de caso de uso. En primer lugar, deben introducirse datos de diferentes tamaños y formas desde varios orígenes de datos, tanto locales como en la nube para capturar datos de productos, datos históricos del comportamiento de los usuarios y datos de usuario cuando el usuario juega en varias plataformas. En segundo lugar, los patrones de uso del juego deben calcularse de manera razonable y precisa. En tercer lugar, las empresas de juegos tiene que medir la eficacia de su enfoque mediante el seguimiento de los éxitos generales de del aumento de las ventas potenciales y de las ventas cruzadas de las compras dentro del juego y realizar ajustes en sus campañas de marketing futuras.

## Información general de la solución

Este caso de uso sencillo puede usarse como un ejemplo de cómo puede usar Factoría de datos de Azure para recopilar, preparar, transformar, analizar y publicar datos.

![Flujo de trabajo de un extremo a otro](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png) La ilustración anterior describe cómo las canalizaciones de datos aparecen en la interfaz de usuario del Portal de Azure clásico una vez implementadas.

1.	**PartitionGameLogsPipeline** lee los eventos de juegos sin procesar de un almacenamiento de blobs y crea particiones basadas en el año, el mes y el día.
2.	**EnrichGameLogsPipeline** combina eventos de juegos con particiones con datos de referencia de código geográfico y enriquece los datos mediante la asignación de direcciones IP a las ubicaciones geográficas correspondientes.
3.	La canalización **AnalyzeMarketingCampaignPipeline** aprovecha los datos enriquecidos y los procesa con los datos de publicidad para crear el resultado final que contiene la eficacia de la campaña de marketing.

En este caso de uso de ejemplo, se usa Factoría de datos de Azure para coordinar las actividades que copian los datos de entrada, transforman y procesan los datos con actividades de HDInsight (transformaciones Hive y Pig) y envía los datos finales a una Base de datos SQL de Azure. También puede visualizar la red de canalizaciones de datos, administrarla y supervisar su estado desde la interfaz de usuario.

## Ventajas

Al optimizar el análisis de su perfil de usuario y alinearlo con los objetivos empresariales, la empresa de juegos puede recopilar rápidamente los patrones de uso y analizar la eficacia de sus campañas de marketing para todos sus productos de juegos diferentes.

<!---HONumber=AcomDC_0128_2016-->