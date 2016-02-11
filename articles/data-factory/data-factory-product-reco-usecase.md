<properties 
	pageTitle="Caso de uso de Factoría de datos: recomendaciones del producto" 
	description="Obtenga información acerca de un caso de uso que se implementan mediante Factoría de datos de Azure junto con otros servicios." 
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

# Caso de uso: recomendaciones de productos 

Factoría de datos de Azure es uno de los muchos servicios que se usan para implementar el conjunto de aplicaciones Cortana Analytics de aceleradores de soluciones. Consulte [Conjunto de aplicaciones de Cortana Analytics](http://www.microsoft.com/cortanaanalytics) para obtener más detalles acerca de este conjunto. En este documento se describe un caso de uso común que los usuarios de Azure ya resolvieron e implementaron mediante Factoría de datos de Azure y otros servicios del componente Cortana Analytics.

## Escenario

Los distribuidores en línea normalmente desean conseguir que sus clientes compren los productos presentando los productos que probablemente más les interesen y, por tanto, es más probable que compren. Para lograrlo, los distribuidores en línea deben personalizar la experiencia en línea de sus usuarios mediante las recomendaciones de productos personalizadas para ese usuario específico que tengan en cuenta sus datos de comportamiento de compra actuales e históricos, información de los productos, marcas recién presentadas y datos de segmentación de productos y clientes para presentar el producto más relevante para el cliente. Además, pueden proporcionar las recomendaciones de productos a usuarios basadas en el análisis del comportamiento de uso general de todos los usuarios combinado.

El objetivo de estos distribuidores es optimizar para conseguir conversiones de clic a venta y obtener mayores ingresos por ventas. Para conseguirlo, proporcionan recomendaciones de productos contextuales, basadas en el comportamiento en función de los intereses y las acciones del cliente. Para este caso de uso, se emplean distribuidores en línea como ejemplo de empresas que desean optimizarse para sus clientes, pero estos principios se aplican a cualquier empresa que desea atraer a sus clientes a sus productos y servicios y mejorar la experiencia de compra de sus clientes con las recomendaciones de productos personalizadas.

## Desafíos

Existen muchos desafíos a los que se enfrentan los distribuidores en línea al intentar implementar este tipo de caso de uso. En primer lugar, deben introducirse datos de diferentes tamaños y formas desde varios orígenes de datos, tanto locales como en la nube para capturar datos de productos, datos históricos del comportamiento de los clientes y datos de usuario cuando el usuario consulta el sitio del distribuidor en línea.

En segundo lugar, las recomendaciones de productos personalizadas deben calcularse y predecirse de forma razonable y precisa. Además del producto, la marca y los datos de comportamiento del cliente y del explorador, los distribuidores en línea también necesitan incluir comentarios proporcionados por otros clientes que compraron los productos anteriormente para conseguir las mejores recomendaciones de productos para un usuario.

En tercer lugar, las recomendaciones deben poder entregarse inmediatamente al usuario para proporcionar una exploración y experiencia de compra perfectas, y proporcionar las recomendaciones más recientes y relevantes. Por último, los distribuidores necesitan medir la eficacia de su enfoque mediante el seguimiento de las ventas totales, las ventas cruzadas y la conversión de clic a ventas, así como realizar ajustes en sus recomendaciones futuras.

## Información general de la solución

Este caso de uso de ejemplo fue resuelto e implementado por usuarios reales de Azure con Factoría de datos de Azure y otros servicios del componente Cortana Analytics, incluidos [HDInsight](https://azure.microsoft.com/services/hdinsight/) y [Power BI](https://powerbi.microsoft.com/) para introducir, preparar, transformar, analizar y publicar los datos finales.

El distribuidor en línea usa un almacén de blobs de Azure, un servidor de SQL Sefver local, la Base de datos SQL de Azure y un data mart relacional como opciones de almacenamiento de datos a lo largo del flujo de trabajo. El almacén de blobs contiene información del cliente, los datos de comportamiento de cliente y los datos de información de los productos. Los datos de información de los productos incluyen información de la marca de producto y un catálogo de los producto almacenado localmente en un almacenamiento de datos SQL.

Como se describe en la siguiente ilustración, todos los datos se combinan e introducen en un sistema de recomendación de productos para ofrecer recomendaciones personalizadas según los intereses y las acciones del cliente, mientras el usuario consulta los productos en el catálogo del sitio web del distribuidor. Los clientes también ven productos similares que puedan estar relacionados con el producto que están viendo de acuerdo con los patrones de uso general de sitio web que no están relacionados con ningún otro usuario

![diagrama del caso de uso](./media/data-factory-product-reco-usecase/diagram-1.png)

Diariamente se generan gigabytes de archivos de registro web sin formato desde el sitio web del distribuidor en línea como archivos semiestructurados. Los archivos de registro de web sin formato y la información del catálogo de clientes y productos se introducen periódicamente en una cuenta de almacenamiento de blobs de Azure mediante el movimiento de datos implementado globalmente de Factoría de datos como un servicio. Los archivos de registro sin procesar del día se dividen (por año y mes) en el almacenamiento de blobs de almacenamiento a largo plazo. [HDInsight de Azure](https://azure.microsoft.com/services/hdinsight/) (Hadoop como servicio) se usa para dividir los archivos de registro sin procesar (para obtener una mejor capacidad de administración, disponibilidad y rendimiento) en el almacén de blobs y procesar los registros introducidos a escala con scripts de Hive y Pig . A continuación, los registros web divididos se procesan para extraer las entradas necesarias para un sistema de recomendaciones de aprendizaje automático para generar las recomendaciones de productos personalizadas.

El sistema de recomendaciones usado para el aprendizaje automático en este ejemplo es una plataforma de recomendaciones de aprendizaje automático de código abierto de [Apache Mahout](http://mahout.apache.org/). Tenga en cuenta que se puede aplicar cualquier modelo de [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/)o personalizado. El modelo Mahout se usa para predecir la similitud entre los elementos en el sitio web del distribuidor de acuerdo con los patrones de uso general y para generar las recomendaciones personalizadas basadas en el usuario específico.

Por último, el conjunto de resultados de las recomendaciones de productos personalizadas se mueve a un data mart relacional para su consumo por el sitio web del distribuidor. Otra aplicación también podría acceder al conjunto de resultados directamente desde el almacenamiento de blobs, o bien el conjunto de resultados podría moverse a almacenes adicionales para otros consumidores y casos de uso.

## Ventajas

Al optimizar su estrategia de recomendación de productos y alinearla con los objetivos empresariales, la solución cumplió los objetivos de marketing y comercialización de los distribuidores en línea. Además, pudieron hacer que fuera operativo y administrar el flujo de recomendaciones de productos de una manera rentable, confiable y eficaz, lo que facilita la actualización del modelo y el ajuste de su eficacia en función de las medidas de la conversión de clic a ventas. Mediante Factoría de datos de Azure, fueron capaces de abandonar su administración de recursos manual en la nube, cara y que consumía mucho tiempo, y moverse a la administración de recursos en la nube a petición, lo que permite ahorrar tiempo y dinero, así como reducir el tiempo para implementar la solución. Las vistas de linaje de datos y el estado de funcionamiento del servicio ahora se pueden visualizar y los problemas pueden solucionarse fácilmente con la supervisión de factoría de datos intuitiva y la administración de interfaz de usuario disponible en el Portal de Azure clásico. Su solución ahora se puede programar y administrar para que los datos terminados se generen y proporcionen a sus usuarios de forma confiable, y los datos y las dependencias de procesamiento se administran automáticamente sin intervención humana.

Gracias a esta experiencia de compra personalizada, el distribuidor en línea creó una experiencia del cliente más competitiva y atractiva y, por tanto, aumentar la satisfacción general del cliente y las ventas.



  

<!---HONumber=AcomDC_0128_2016-->