<properties 
	pageTitle="Operaciones comunes en la API de recomendaciones de Aprendizaje automático | Microsoft Azure" 
	description="Aplicación de ejemplo de recomendación de aprendizaje automático de Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="luisca"/>


# Operaciones comunes en la API de recomendaciones de aprendizaje automático

##Propósito

Este documento muestra el uso de algunas de las API de recomendaciones de Aprendizaje automático de Azure a través de una [aplicación de ejemplo](http://1drv.ms/1xeO2F3).

Esta aplicación no está pensada para incluir todas las funcionalidades ni usa todas las API. Muestra algunas operaciones que se suelen realizar las primeras veces que se usa el servicio de recomendación de Aprendizaje automático.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Introducción al servicio de recomendación de Aprendizaje automático

Las recomendaciones a través del servicio de recomendación de Aprendizaje automático se habilitan al compilar un modelo de recomendaciones basado en los datos siguientes:

* Un repositorio de los elementos que desea recomendar, también conocido como "catálogo".
* Datos que representan el uso de los elementos por cada usuario o sesión (se pueden adquirir con el tiempo a través de la adquisición de datos, no forman parte de la aplicación de muestra).

Una vez compilado un modelo de recomendación, puede utilizarlo para predecir elementos que puedan resultar de interés para algún usuario, según un conjunto de elementos (o un elemento único) que seleccione el usuario.

Para habilitar el escenario anterior, haga lo siguiente en el servicio de recomendación de Aprendizaje automático:

* Crear un modelo: se trata de un contenedor lógico que incluye los datos (catálogo y uso) y los modelos de predicción. Cada contenedor de modelo se identifica mediante un identificador único que se asigna en el momento de su creación. Este identificador se denomina "identificador de modelo" y lo utilizan la mayoría de las API. 
* Cargar en el catálogo: una vez que se crea un contenedor de modelo, es posible asociarlo a un catálogo.

**Nota**: la creación de un modelo y la carga a un catálogo normalmente se realizan solo una vez durante el ciclo de vida del modelo.

* Cargar datos de uso: cargue los datos de uso en el contenedor del modelo.
* Compilar un modelo de recomendación: cuando tenga suficientes datos, podrá crear el modelo de recomendación. Esta operación usa algoritmos de Aprendizaje automático de última generación para crear un modelo de recomendación. Cada compilación está asociada a un identificador único. Hay que mantener un registro de este identificador porque es necesario para la funcionalidad de algunas API.
* Supervisar el proceso de compilación: una compilación de un modelo de recomendación es una operación asincrónica y puede tardar desde varios minutos a varias horas, según la cantidad de datos (catálogo y uso) y los parámetros de compilación. Por lo tanto, es necesario supervisar la compilación. Un modelo de recomendación solo se crea si su compilación asociada finaliza correctamente.
* (Opcional) Elegir una compilación de modelo de recomendación activa: este paso solo es necesario si tiene más de un modelo de recomendación compilado en el contenedor de modelos. El sistema redirigirá automáticamente a la compilación activa predeterminada cualquier solicitud para obtener recomendaciones en la que no se indique el modelo de recomendación activa. 

**Nota**: los modelos de recomendación activa están listos para su uso en producción y se diseñan para cargas de trabajo de producción. Esto difiere de un modelo de recomendación no activo, que permanece en un entorno similar al de prueba (a veces denominado "de ensayo").

* Obtener recomendación: cuando ya tenga un modelo de recomendación, puede desencadenar recomendaciones para un solo elemento o para una lista de elementos que elija. 

Normalmente, se ejecuta la recomendación Get para un cierto período de tiempo. Durante ese período de tiempo, puede redirigir los datos de uso al sistema de recomendación de Aprendizaje automático, que agrega estos datos al contenedor del modelo especificado. Cuando tenga suficientes datos de uso, puede crear un nuevo modelo de recomendación que incorpore los datos de uso adicionales.

##Requisitos previos

* Visual Studio 2013
* Acceso a Internet 
* Suscripción a la API de recomendaciones (https://datamarket.azure.com/dataset/amla/recommendations).

##Solución de aplicación de ejemplo de Aprendizaje automático de Azure

Esta solución contiene el código fuente, el uso de la muestra, el archivo de catálogo y las directivas para descargar los paquetes necesarios para la compilación.

##API utilizadas

La aplicación utiliza la funcionalidad de la recomendación de Aprendizaje automático a través de un subconjunto de API disponibles. Las siguientes API se muestran en la aplicación:

* Crear modelo: cree el contenedor lógico para mantener los modelos de datos y recomendación. Un modelo se identifica mediante un nombre. El usuario no puede crear más de un modelo con el mismo nombre.
* Cargar archivo de catálogo: se usa para cargar los datos del catálogo.
* Cargar archivo de uso: se usa para cargar los datos de uso.
* Desencadenar la compilación: se usa para crear un modelo de recomendación.
* Supervisar la ejecución de la compilación: se usa para supervisar el estado de una compilación de modelo de recomendación.
* Elegir un modelo compilado para recomendación: se usa para indicar qué modelo de recomendación se debe usar de forma predeterminada para un contenedor de modelos concreto. Este paso solamente es necesario si tiene más de un modelo de recomendación y desea activar una compilación no activa como modelo de recomendación activa.
* Obtener recomendación: se usa para recuperar elementos recomendados según un único elemento o un conjunto de elementos. 

Para obtener una descripción completa de la API, consulte la documentación de Microsoft Azure Marketplace.

**Nota**: un modelo puede tener varias compilaciones con el tiempo (no de forma simultánea). Cada compilación se crea con el mismo catálogo o con uno actualizado y los datos de uso adicionales.

## Dificultades habituales

* Debe proporcionar su nombre de usuario y la clave de la cuenta principal de Microsoft Azure Marketplace para ejecutar la aplicación de ejemplo.
* Si ejecuta la aplicación de ejemplo de forma consecutiva, se producirá un error. El flujo de la aplicación incluye el monitor de creación, carga y compilación, y obtiene la recomendación de un nombre de modelo predefinido; por lo tanto, se producirán errores cuando se ejecute de forma consecutiva si no cambia el nombre del modelo entre ejecuciones.
* Puede que las recomendaciones no devuelvan datos. La aplicación de ejemplo usa un archivo de uso y de catálogo muy pequeño. Por lo tanto, algunos elementos del catálogo no tendrán ningún elemento recomendado.

## Renuncia de responsabilidades
La aplicación de ejemplo no está diseñada para ejecutarse en un entorno de producción. Los datos proporcionados en el catálogo son muy pequeños y no constituyen un modelo de recomendación significativo. Los datos se proporcionan como una demostración.
 

<!---HONumber=AcomDC_1210_2015-->