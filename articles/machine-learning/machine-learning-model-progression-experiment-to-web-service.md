<properties
	pageTitle="Progreso de un modelo de Aprendizaje automático de un experimento a un servicio web aplicado | Microsoft Azure"
	description="Información general de cómo progresa el modelo de Aprendizaje automático desde un experimento de desarrollo a un servicio web aplicado."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="garye"/>


# Progreso de un modelo de Aprendizaje automático de un experimento a un servicio web aplicado

Un ***experimento*** es un lienzo de Estudio de aprendizaje automático de Azure que permite desarrollar, ejecutar, probar e iterar cuando se crea un modelo de análisis predictivo. Están disponibles una amplia variedad de módulos que puede usar para insertar datos en el experimento, manipular los datos, entrenar un modelo con algoritmos de aprendizaje automático, puntuar el modelo, evaluar los resultados y producir los valores finales.

Una vez que esté satisfecho con el experimento, puede implementarlo como un ***servicio web de Azure*** para que los usuarios puedan enviar datos nuevos y recibir los resultados de vuelta.

En este artículo le ofreceremos una información general de cómo progresa el modelo de Aprendizaje automático desde un experimento de desarrollo a un servicio web aplicado.

>[AZURE.NOTE] Hay otras maneras de desarrollar e implementar modelos de Aprendizaje automático, pero este artículo se centra en cómo usar Estudio de aprendizaje automático. Para obtener una explicación de cómo crear un servicio web predictivo con R, consulte la entrada del blog [Build & Deploy Predictive Web Apps Using RStudio and Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx) (Creación e implementación de aplicaciones web predictivas con RStudio y Aprendizaje automático de Azure).

Aunque Estudio de aprendizaje automático de Azure está diseñado principalmente para ayudarle a desarrollar e implementar un *modelo de análisis predictivo*, es posible usar Estudio para desarrollar un experimento que no incluya un modelo de este tipo. Por ejemplo, un experimento podría simplemente insertar datos, manipularlos y, después, producir los resultados. Al igual que un experimento de análisis predictivo, puede implementar este experimento no predictivo como un servicio web, pero es un proceso más sencillo porque el experimento no está entrenando ni puntuando un modelo de Aprendizaje automático. Aunque no es el uso típico de Estudio, se deberá incluir en la explicación siguiente para que podemos dar una explicación completa de cómo funciona Estudio.

## Desarrollo e implementación de un servicio web predictivo

Estas son las fases que sigue una solución típica cuando la desarrolla e implementa mediante Estudio de aprendizaje automático:

![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Ilustración 1 - Fases de un modelo de análisis predictivo típico*

### El experimento de entrenamiento

El ***experimento de entrenamiento*** es el lienzo inicial del experimento en Estudio de Aprendizaje automático. El propósito del experimento de entrenamiento es brindarle un lugar para desarrollar, probar, iterar y finalmente entrenar un modelo de aprendizaje automático. Incluso puede entrenar varios modelos simultáneamente mientras busca la mejor solución, pero una vez que haya terminado de experimentar, puede seleccionar un único modelo entrenado y eliminar el resto del experimento. Para ver un ejemplo de desarrollo de un experimento de análisis predictivo, consulte [Desarrollo de una solución de análisis predictivo para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### El experimento predictivo

Cuando ya tenga un modelo entrenado en el experimento de entrenamiento, haga clic en **Configurar servicio web** en Estudio de aprendizaje automático y Estudio pasará por el proceso de convertir el experimento de entrenamiento en un ***experimento predictivo***. El propósito del experimento predictivo es usar el modelo entrenado para puntuar nuevos datos, con el fin de ponerlo en marcha con el tiempo como un servicio web de Azure.

Esta conversión se realiza a través de los pasos siguientes:

-   Conversión del conjunto de módulos usados para el entrenamiento en un único módulo y su almacenamiento como un modelo entrenado

-   Eliminación de los módulos extraños no relacionados con la puntuación

-   Adición de puertos de entrada y salida que van a usar el servicio web final

Puede haber más cambios que desee realizar para que el experimento predictivo esté listo para implementarse como un servicio web. Por ejemplo, si desea que el servicio web genere solo un subconjunto de resultados, puede agregar un módulo de filtrado antes el puerto de salida.

En este proceso de conversión no se descarta el experimento de entrenamiento. Cuando se complete el proceso, tendrá dos pestañas en Estudio: uno para el experimento de entrenamiento y otro para el experimento predictivo. De este modo, antes de implementar el servicio web, puede realizar cambios en el experimento de entrenamiento y volver a generar el experimento predictivo. O bien, puede guardar una copia del experimento de entrenamiento para iniciar otra línea de experimentación.

>[AZURE.NOTE] Al hacer clic en **Configurar servicio web**, se inicia un proceso automático para convertir el experimento de entrenamiento en un experimento predictivo y esto funciona bien en la mayoría de los casos. Pero si el experimento de entrenamiento es complejo (por ejemplo, tiene varias rutas de acceso para el entrenamiento que se unen), es posible que prefiera realizar esta conversión manualmente. Para obtener más detalles sobre el funcionamiento de este proceso de conversión, consulte [Convertir un experimento de entrenamiento en Aprendizaje automático en un experimento predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### El servicio web

Cuando el experimento predictivo esté listo, haga clic en **Implementar servicio web** para aplicar el modelo mediante la implementación como un ***servicio web de Azure***. Los usuarios ahora pueden enviar datos al modelo mediante la API de REST del servicio web y recibir los resultados de vuelta. Para obtener más información sobre cómo hacerlo, consulte [Cómo consumir un servicio web de Aprendizaje automático de Azure implementado en un experimento de Aprendizaje automático](machine-learning-consume-web-services.md).

Una vez implementado el servicio web, el experimento predictivo y el servicio web permanecen conectados y puede alternar entre ellos:

|***Desde esta página...***|***haga clic en esto...***|***para abrir esta página...***|
| ------------------- | --------------- | ---------------------- |
|lienzo del experimento en Studio|**Ir al servicio web**|configuración del servicio web en Studio|
|configuración del servicio web en Studio|**Ver más recientes**|lienzo del experimento en Studio|
|configuración del servicio web en Studio|**Administrar puntos de conexión...**|administración de puntos de conexión en el Portal de Azure clásico|
|administración de puntos de conexión en el Portal de Azure clásico|**Editar en estudio**|lienzo del experimento en Studio|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*Ilustración 2 - Conexiones entre un experimento y el servicio web*

## El caso no típico: creación de un servicio web no predictivo

Si el experimento no entrena un modelo de análisis predictivo, en este caso no es necesario crear el experimento de entrenamiento y el experimento de puntuación: solo hay un experimento y se puede implementar como un servicio web (Estudio de aprendizaje automático detecta si el experimento contiene un modelo predictivo mediante el análisis de los módulos que se usaron).

Después de que haya iterado en el experimento y esté satisfecho con él:

1.  Haga clic en **Configurar servicio web**: los nodos de entrada y salida se agregan automáticamente

2.  Haga clic en **Ejecutar**.

3.  Haga clic en **Implementar servicio web**

El servicio web ahora se implementó y puede tener acceso a él y a administrarlo como un servicio web de predicción.

## Botones del servicio web

En Estudio de aprendizaje automático, los botones de servicio web, **Configurar servicio web** e **Implementar servicio web**, cambian de nombre y función dependiendo de dónde se encuentre el usuario en el proceso de desarrollo.

**El experimento contiene un modelo de predicción**

Si el experimento entrena y puntúa un modelo de predicción, los botones del servicio web realizan estas funciones:

|**Tipo de experimento**|**Botón**|**Qué hace**|
| -------------------- | -------- | -------------- |
|Experimento en desarrollo|**Configurar servicio web**|Proporciona dos opciones|
|&nbsp;|- **Servicio web predictivo**|Convierte el experimento en un experimento de entrenamiento y en un experimento de puntuación.|
|&nbsp;|- **Reciclaje del servicio web**|Convierte el experimento en una experimento de reciclaje (consulte la sección "Actualizar" a continuación).|
|Experimento de entrenamiento|**Configurar servicio web**|Proporciona dos opciones|
|&nbsp;|- **Actualizar experimento predictivo**|Actualiza el experimento predictivo asociado con los cambios realizados en el experimento de entrenamiento.|
|&nbsp;|- **Reciclaje del servicio web**|Convierte el experimento de entrenamiento en un experimento de reciclaje (consulte la sección "Actualizar" a continuación)|
|&nbsp;|-*o*- **Implementar servicio web**|Si configuró el experimento de reciclaje para la implementación, se implementa como un servicio web.|
|Experimento predictivo|**Implementar servicio web**|Implementa el experimento predictivo como servicio web.|

**El experimento *no* contiene un modelo predictivo**

Si el experimento no entrena ni puntúa un modelo predictivo, los botones del servicio web son mucho más sencillos:

|**Tipo de experimento**|**Botón**|**Qué hace**|
| -------------------- | -------- | -------------- |
|Experimento en desarrollo|**Configurar servicio web**|Prepara el experimento para su implementación como un servicio web.|
|Experimento preparado para la implementación|**Implementar servicio web**|Implementa el experimento como un servicio web, abre la página de configuración del servicio web.|

## Actualización del servicio web

Ahora que implementó el experimento como un servicio web, ¿qué ocurre si necesita actualizarlo?

Depende de lo que necesite actualizar:

**Quiere cambiar la entrada o salida, o quiere modificar cómo el servicio web manipula los datos**

Si no cambia el modelo, pero está cambiando cómo el servicio web administra los datos, puede modificar el experimento predictivo y después volver a hacer clic en **Implementar servicio web**. El servicio web se detendrá, el experimento predictivo actualizado se implementará y el servicio web se volverá a iniciar.

Por ejemplo: imagine que el experimento predictivo devuelve toda la fila de datos de entrada con el resultado de la predicción. Puede decidir que quiere que el servicio web solo devuelva el resultado. Por tanto agrega un módulo de **columnas del proyecto** en el experimento predictivo, justo antes del puerto de salida, para excluir otras columnas que no sean el resultado. Al volver a hacer clic en **Implementar servicio web**, se actualiza el servicio web.

**Quiere reciclar el modelo con nuevos datos**

Si quiere mantener su modelo de aprendizaje automático, pero le gustaría reciclarlo con nuevos datos, tiene dos opciones:

1.  **Reciclar el modelo mientras se está ejecutando el servicio web** -Si desea reciclar el modelo mientras se está ejecutando el servicio web predictivo, puede hacerlo realizando algunas modificaciones en el experimento de entrenamiento para convertirlo en un ***experimento de reciclaje*** y después puede implementarlo como un servicio ***web de reciclaje***. Para obtener más información sobre cómo hacerlo, consulte [Reciclar modelos de Aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md).

2.  **Vuelva al experimento de entrenamiento original y use distintos datos de entrenamiento para desarrollar el modelo** - El experimento predictivo está vinculado al servicio web, pero el experimento de entrenamiento no está vinculado directamente de esta manera. Si se modifica el experimento de entrenamiento original y se hace clic en **Configurar servicio Web**, se creará un *nuevo* experimento predictivo que, cuando se implementa, creará un *nuevo* servicio web. No solo actualiza el servicio web original.

    Así que si necesita modificar el experimento de entrenamiento, ábralo y haga clic en **Guardar como** para realizar una copia. Esto dejará intacto el experimento de entrenamiento original, el experimento predictivo y el servicio web. Ahora puede crear un nuevo servicio web con los cambios. Después de implementar el nuevo servicio web, podrá decidir si quiere detener el servicio web anterior o que se siga ejecutándose junto con el nuevo.

**Quiere entrenar un modelo diferente**

Si desea realizar cambios en el experimento predictivo original, como seleccionar un algoritmo de Aprendizaje automático diferente o probar un método de entrenamiento distinto, debe seguir el segundo procedimiento descrito anteriormente para reciclar el modelo: abra el experimento de entrenamiento, haga clic en **Guardar como** para hacer una copia e inicie la nueva ruta de acceso del desarrollo del modelo, de la creación del experimento predictivo y de la implementación del servicio web. Esto creará un nuevo servicio web no relacionado con el original, por lo que podrá decidir cuál de ellos seguirá funcionando.

## Lecturas adicionales

Para obtener información más detallada sobre este proceso, consulte los siguientes artículos:

-   conversión del experimento - [Convertir un experimento de entrenamiento de Aprendizaje automático en un experimento predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   implementación del servicio web - [Implementar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   reciclaje del modelo - [Reciclar modelos de Aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md)

Para ver ejemplos de todo el proceso, consulte:

-   [Tutorial de Aprendizaje automático: Creación del primer experimento en Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md)

-   [Tutorial: Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)

<!---HONumber=AcomDC_0204_2016-->