<properties 
	pageTitle="Aplicación de ejemplo de Aprendizaje automático: artículos que con frecuencia se compran juntos | Microsoft Azure" 
	description="Un servicio web de aprendizaje automático que realiza análisis en línea del carro de la compra para producir recomendaciones de artículos que con frecuencia se compran juntos a partir de las transacciones históricas proporcionadas por el usuario." 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="luisca"/>

# Aplicación de ejemplo de Aprendizaje automático: artículos que con frecuencia se compran juntos


## IMPORTANTE: ESTE SERVICIO ESTÁ DESUSADO

La característica de Artículos que se suelen comprar juntos que ofrece este servicio ahora se ha integrado en nuestra [API Recomendaciones](http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c) más amplia. Le recomendamos que utilice las Recomendaciones en lugar de este servicio.

##Información general

El [servicio web Frequently Bought Together](https://datamarket.azure.com/dataset/amla/mba) de Aprendizaje automático realiza análisis de carros de la compra en línea para generar recomendaciones de productos de artículos que se suelen comprar juntos a partir de las transacciones históricas. Las recomendaciones Frequently Bought Together ayudan a los compradores a identificar los productos de un catálogo que son más relevantes cuando se compra un artículo concreto. La exposición de estas recomendaciones de forma destacada ha demostrado ser eficaz para mejorar las ventas de los distribuidores en línea.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
  
##Primeros pasos 

Una vez se ha suscrito al servicio web Frequently Bought Together, puede utilizar la [aplicación web de ejemplo Market Basket Analysis Service](https://marketbasket.cloudapp.net/) para cargar fácilmente sus datos en un modelo y detectar conjuntos de productos comprados frecuentemente. Para usar la aplicación o la API, primero necesita su clave de API, que puede obtener en la [página de la cuenta de Azure Data Market](https://datamarket.azure.com/account).

##Consumo del servicio web 

Este servicio contiene API para crear modelos de la aplicación Frequently Bought Together, cargar transacciones históricas y recuperar los conjuntos de productos mejor clasificados, que se suelen comprar juntos correspondientes a un producto determinado. En el repositorio [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) de GitHub puede encontrar ejemplos que muestran cómo utilizar estas API.

 

<!---HONumber=AcomDC_1210_2015-->