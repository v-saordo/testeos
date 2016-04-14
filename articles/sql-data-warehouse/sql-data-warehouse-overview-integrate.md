<properties
   pageTitle="Creación de soluciones integradas con Almacenamiento de datos SQL | Microsoft Azure"
   description="Herramientas y asociados con soluciones que se integran con Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#Aprovechamiento de otros servicios con Almacenamiento de datos SQL
Además de su funcionalidad básica, Almacenamiento de datos SQL permite a los usuarios aprovechar muchos de los otros servicios de Azure. En concreto, hemos tomado medidas para que realizar una estrecha integración con lo siguiente:

+ Power BI
+ Factoría de datos de Azure
+ Aprendizaje automático de Azure
+ Análisis de transmisiones de Azure

Estamos trabajando para conectar con más servicios en el ecosistema de Azure.

##Power BI
La integración de Power BI le permite aprovechar en gran medida la capacidad de procesamiento de Almacenamiento de datos SQL con los informes dinámicos y la visualización de Power BI. La integración de Power BI actualmente incluye:

+ **Conexión directa**: una conexión más avanzada con aplicación de lógica en Almacenamiento de datos SQL. Esto proporciona un análisis más rápido a mayor escala.
+ **Open in Power BI**: el botón ‘Open in Power BI’ (Abrir en Power BI) pasa la información de la instancia a Power BI para lograr una conexión más fluida. 

Consulte [Integración con Power BI](./sql-data-warehouse-integrate-power-bi.md) o la [documentación de Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) para obtener más información.

##Factoría de datos de Azure
Factoría de datos de Azure ofrece a los usuarios una plataforma administrada para crear canalizaciones complejas de extracción y carga. La integración de Almacenamiento de datos SQL con Factoría de datos de Azure incluye lo siguiente:

+ **Procedimientos almacenados**: coordinar la ejecución de procedimientos almacenados en Almacenamiento de datos SQL.

Consulte [Integración con Factoría de datos de Azure](./sql-data-warehouse-integrate-azure-data-factory.md) o la [documentación de Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/) para obtener más información.

##Aprendizaje automático de Azure
Aprendizaje automático de Azure es un servicio de análisis totalmente administrado que permite a los usuarios crear modelos complejos aprovechando un amplio conjunto de herramientas de predicción. Almacenamiento de datos SQL se admite como origen y destino para estos modelos con la siguiente funcionalidad:

+ **Lectura de datos:** producir modelos a escala usando T-SQL en Almacenamiento de datos SQL. 
+ **Escritura de datos:** confirmar los cambios de cualquier modelo en Almacenamiento de datos SQL.

Consulte [Integración con Aprendizaje automático de Azure](./sql-data-warehouse-integrate-azure-machine-learning.md) o la [documentación de Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) para obtener más información.

##Análisis de transmisiones de Azure
Análisis de transmisores de Azure es una infraestructura compleja y totalmente administrada para el procesamiento y consumo de datos de eventos generados por el Centro de eventos de Azure. La integración con Almacenamiento de datos SQL permite que los datos de transmisión se procesen y almacenen junto con los datos relacionales para permitir un análisis más profundo y avanzado de los datos.

+ **Resultado del trabajo:** enviar los resultados de los trabajos de Análisis de transmisiones directamente al Almacenamiento de datos SQL.

Consulte [Integración con Análisis de transmisiones de Azure](./sql-data-warehouse-integrate-azure-stream-analytics.md) o la [documentación de Análisis de transmisiones de Azure](https://azure.microsoft.com/documentation/services/stream-analytics/) para obtener más información.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0204_2016-->