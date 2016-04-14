<properties
	pageTitle="Convertir un experimento de entrenamiento en Aprendizaje automático en un experimento predictivo | Microsoft Azure"
	description="Cómo convertir un experimento de entrenamiento en Aprendizaje automático, usado para entrenar el modelo de análisis predictivo, en un experimento predictivo que se puede implementar como servicio web."
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

# Convertir un experimento de entrenamiento en Aprendizaje automático en un experimento predictivo

Aprendizaje automático de Azure permite compilar, probar e implementar soluciones de análisis predictivo.

Una vez que haya creado e iterado en un *experimento de entrenamiento* para entrenar el modelo de análisis predictivo y esté listo para usarlo para puntuar nuevos datos, deberá preparar y simplificar el experimento para puntuar. A continuación, puede implementar este *experimento predictivo* como servicio web de Azure para que los usuarios puedan enviar datos al modelo y recibir las predicciones del modelo.

Al efectuar la conversión a un experimento predictivo, estará preparando el modelo entrenado para implementarlo como servicio web. Los usuarios del servicio web enviarán datos de entrada a su modelo y el modelo devolverá los resultados de predicción. Por lo tanto, cuando efectúe la conversión a un experimento predictivo, deberá tener en cuenta cómo espera que usen el modelo los demás.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

El proceso de convertir un experimento de entrenamiento en un experimento predictivo implica tres pasos:

1.	Guardar el modelo de aprendizaje automático que ha formado y, a continuación, sustituir el algoritmo de aprendizaje automático y los módulos de [Entrenar modelo][train-model] con el modelo entrenado guardado.
2.	Recortar el experimento solo a los módulos que se necesitan para efectuar la puntuación. Un experimento de formación incluye una serie de módulos que son necesarios para la formación, pero que no son necesarios una vez que el modelo está formado y listo para usar para la puntuación.
3.	Defina en qué parte del aceptará datos del usuario del servicio web y qué datos se devolverán.

## Botón Configurar servicio web

Después de ejecutar el experimento (botón **EJECUTAR** de la parte inferior del lienzo del experimento), el botón **Configurar servicio web** (seleccione la opción **Servicio web predictivo**) realizará automáticamente los tres pasos para convertir el experimento de entrenamiento en un experimento predictivo:

1.	Guarda el modelo entrenado como módulo en la sección **Modelos entrenados** de la paleta del módulo (a la izquierda del lienzo del experimento) y, a continuación, reemplaza el algoritmo de aprendizaje automático y los módulos de [Entrenar modelo][train-model] con el modelo entrenado guardado.
2.	Elimina los módulos que claramente no se necesitan. En nuestro ejemplo, esto incluye los módulos [Dividir][split], 2º de [Puntuar modelo][evaluate-model] y [Evaluar modelo][score-model].
3.	Crea módulos de y salida del servicio web y los agrega en ubicaciones predeterminadas en el experimento.

Por ejemplo, en el siguiente experimento se entrena un modelo de árbol de decisión aumentada de dos clases con los datos del censo de muestra:

![Experimento de entrenamiento][figure1]

Los módulos de este experimento realizan básicamente cuatro funciones diferentes:

![Funciones del módulo][figure2]

Al convertir este experimento de entrenamiento en un experimento predictivo, algunos de estos módulos ya no se necesitan o tienen un objetivo diferente:

- **Datos**: los datos de este conjunto de datos de ejemplo no se usan durante la puntuación (el usuario del servicio web proporcionará los datos que se van a puntuar). Sin embargo, los metadatos de este conjunto de datos, como los tipos de datos, son utilizados por el modelo formado. Por ello, necesita mantener el conjunto de datos en el experimento predictivo para que pueda ofrecer estos metadatos.

- **Preparación**: en función de los datos que se vayan a enviar para puntuar, estos módulos pueden ser necesarios, o no, para procesar los datos entrantes.

	Por ejemplo, en este ejemplo, el conjunto de datos de ejemplo puede tener valores que faltan e incluye columnas que no son necesarias para formar el modelo. Por lo tanto, se ha incluido un módulo [Limpiar datos que faltan][clean-missing-data] para tratar los valores ausentes y un módulo [Proyectar columnas][project-columns] para excluir las columnas adicionales del flujo de datos. Si sabe que a los datos que se van a enviar para puntuar a través del servicio web no les van a faltar valores, puede quitar el módulo [Limpiar datos que faltan][clean-missing-data]. Sin embargo, dado que el módulo [Proyectar columnas][project-columns] ayuda a definir el conjunto de funciones que se puntúan, no se puede quitar.

- **Entrenamiento**: una vez que el modelo se ha entrenado correctamente, guárdelo como un módulo de modelo entrenado individual. A continuación, reemplace estos módulos individuales por los del modelo formado guardado.

- **Puntuación**: en este ejemplo, el módulo Dividir se usa para dividir el flujo de datos en un conjunto de datos de prueba y datos de entrenamiento. En el experimento predictivo esto no es necesario y puede quitarse. De forma similar, el segundo módulo [Puntuar modelo][score-model] y el módulo [Evaluar modelo][evaluate-model] se usan para comparar los resultados de los datos de prueba, por lo que estos módulos tampoco son necesarios en el experimento predictivo. No obstante, el módulo [Puntuar modelo][score-model] restante es necesario para devolver un resultado de puntuación a través del servicio web.

Este es el aspecto de nuestro ejemplo después de hacer clic en **Configurar servicio web**:

![Experimento predictivo convertido][figure3]

Esto puede ser suficiente para preparar el experimento a fin de implementarlo como servicio web. Sin embargo, es aconsejable realizar algún trabajo adicional específico en su experimento.

### Ajustar los módulos de entrada y salida

En el experimento de formación, utilizó un conjunto de datos de entrenamiento y, a continuación, realizó algún procesamiento para obtener los datos en un formato necesitado por el algoritmo de aprendizaje automático. Si los datos que espera recibir a través del servicio web no necesitan este procesamiento, puede mover el módulo **Entrada de servicio web** a otro nodo del experimento.

Por ejemplo, de forma predeterminada **Configurar servicio web** coloca el módulo **Entrada de servicio web** en la parte superior del flujo de datos, como se muestra en la ilustración anterior. Sin embargo, si los datos de entrada no necesitan este procesamiento, puede colocar manualmente el módulo **Entrada de servicio web** después de los módulos de procesamiento de datos:

![Mover la entrada del servicio web][figure4]

Los datos de entrada que se proporciona a través del servicio web pasarán ahora directamente al módulo del modelo de puntuación sin ningún procesamiento previo.

De igual forma, **Configurar servicio web** coloca de manera predeterminada el módulo de salida de servicio web en la parte inferior del flujo de datos. En este ejemplo, el servicio web devolverá al usuario la salida del módulo [Puntuar modelo][score-model], que incluye el vector de los datos de entrada completo, además de los resultados de puntuación.

Sin embargo, si prefiere que devuelva otra cosa (por ejemplo, solo los resultados de puntuación y no el vector completo de los datos de entrada), puede insertar un módulo [Proyectar columnas][project-columns] para excluir todas las columnas, excepto los resultados de puntuación. A continuación, mueva el módulo **Salida de servicio web** a la salida del módulo [Proyectar columnas][project-columns]\:

![Mover la salida del servicio web][figure5]

### Agregar o quitar módulos de procesamiento de datos adicionales

Si hay más módulos en el experimento de los que sabe que se necesitarán durante la puntuación, se pueden eliminar. Por ejemplo, al haber movido el módulo **Entrada de servicio web** a un punto posterior a los módulos de procesamiento de datos, se puede eliminar el módulo [Limpiar datos que faltan][clean-missing-data] del experimento predictivo.

Nuestro experimento predictivo tiene ahora el siguiente aspecto:

![Eliminación de módulos adicionales][figure6]

### Agregar parámetros de servicio web opcionales

En algunos casos, puede permitir al usuario del servicio web cambiar el comportamiento de los módulos cuando se accede al servicio. Los *parámetros del servicio web* le permiten hacer esto.

Un ejemplo común es la configuración del módulo [Lector][reader] para que el usuario del servicio web implementado pueda especificar un origen de datos diferente al acceder al servicio web. También puede configurar el módulo [Escritor][writer] para que se pueda especificar un destino diferente.

Puede definir parámetros de servicio web y asociarlos con uno o más parámetros de módulo, y puede especificar si son obligatorios u opcionales. El usuario del servicio web puede proporcionar a continuación valores para estos parámetros cuando se tiene acceso al servicio y las acciones del módulo se modificarán en consecuencia.

Para obtener más información acerca de los parámetros del servicio web, consulte [Usar parámetros de servicio web de Aprendizaje automático de Azure][webserviceparameters]

[webserviceparameters]: machine-learning-web-service-parameters.md


## Implementar el experimento predictivo como servicio web

Ahora que ha preparado el experimento predictivo suficientemente, puede implementarlo como servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar datos a su modelo y el modelo devolverá las predicciones.

Para obtener más información sobre el proceso de implementación completo, consulte [Implementar un servicio web de Aprendizaje automático de Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

<!---HONumber=AcomDC_0204_2016-->