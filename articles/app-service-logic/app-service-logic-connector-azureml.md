<properties
   pageTitle="Uso del conector de Aprendizaje automático de Azure en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de Aprendizaje automático de Azure y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="jehollan"/>
   
# Información general
El conector de Aprendizaje automático de Azure para Aplicaciones lógicas permite llamar a las API de Aprendizaje automático de Azure de puntuación de lotes (servicio de ejecución por lotes) y reciclaje. Estas características junto con desencadenadores de aplicación lógica permiten programar trabajos por lotes y configurar el reciclaje programado de modelos.

 ![][1]
 
## Introducción al conector de Aprendizaje automático de Azure y su incorporación a la aplicación lógica
Para empezar, cree un experimento en Estudio de aprendizaje automático y luego configure e implemente un servicio web. Después, puede usar la dirección URL de API y la clave de la URL del mensaje de BES que se encuentra en la página de Ayuda de Exacción de lotes ([más información](https://github.com/Azure/azure-content/blob/master/articles/machine-learning/machine-learning-walkthrough-5-publish-web-service.md))

Para ejecutar un trabajo de BES mediante el conector, agregue el conector de Aprendizaje automático de Azure a su aplicación lógica. Después, escriba la información necesaria (vea a continuación para más información al respecto). Para configurar Reciclaje, agregue un segundo conector de Aprendizaje automático de Azure y especifique los parámetros de entrada (vea [aquí](machine-learning-retrain-models-programmatically.md) para más información sobre cómo configurar un modelo para reciclaje).

## Ejecución de un trabajo de ejecución de lotes de Aprendizaje automático de Azure
El conector de Aprendizaje automático de Azure ofrece las cuatro opciones siguientes para ejecutar trabajos de ejecución de lotes (BES): 1. Trabajo por lotes con entrada y salida: el experimento tiene módulos de entrada y salida de servicio web 2. Trabajo por lotes sin entrada y salida: el experimento no tiene módulo de entrada o salida de web servicio (por ejemplo, usa módulos Lector y Escritor) 3. Trabajo por lotes con solo entrada: el experimento tiene módulo de entrada de servicio web, pero no módulo de salida de servicio web (por ejemplo, usa un módulo Escritor) 4. Trabajo por lotes con solo salida: el experimento no tiene ningún módulo de entrada de servicio web, pero tiene un módulo de salida de servicio web (por ejemplo, se usa un módulo Lector). Tenga en cuenta que BES es una solicitud asincrónica y puede tardar tiempo en completarse en función del tamaño de los datos y la complejidad del modelo. Al finalizar el trabajo, el conector devolverá el resultado de salida.

### Ejecución de la ejecución por lotes: con entrada y salida
Si el experimento de estudio tiene módulos de entrada y salida de servicio web, tendrá que proporcionar información sobre la cuenta del blob de almacenamiento y la ubicación ([más aquí](machine-learning-consume-web-services.md)). Además, puede incluir parámetros globales (servicio web) si se configuran en el experimento ([más aquí](machine-learning-web-service-parameters.md)).

![][2]

Haga clic en el botón de puntos suspensivos para mostrar y ocultar los campos de parámetros globales. Esto le permite proporcionar uno o más parámetros globales (servicio web) en una lista de campos y valores separados por coma.

![][3]

Otras variaciones en los trabajos de BES, como un trabajo con ninguna entrada o salida de servicio web, también están disponibles a través del conector.

## Configuración de reciclaje

Use la acción Configurar reciclaje para configurar un reciclaje de un solo uso o programado del modelo de aprendizaje automático. Conjuntamente con un trabajo de ejecución por lotes creado desde el conector, puede completar los pasos del entrenamiento y actualizar un modelo del servicio web. En este flujo de trabajo, se usaría dos veces el conector. 1. El primer conector se utiliza para ejecutar el trabajo de BES a fin de reciclar el modelo y devolver la salida. La salida de esta ejecución tendrá la dirección URL del nuevo modelo (.ilearner). Puede también, opcionalmente, si lo ha configurado en el experimento, devolver la dirección URL de la salida del módulo Evaluar, que es un archivo csv. En el paso siguiente, puede usar los datos de la salida del módulo Evaluar para tomar una decisión sobre si se debe o no reemplazar el modelo en el servicio web (por ejemplo, si Precisión > 0,85). 1. El segundo conector se utiliza para configurar el reciclaje. Usa parámetros de la salida del primer conector para comprobar, opcionalmente, la condición de modelo de actualización y actualizar el servicio web con el modelo recién entrenado. * A modo de ejemplo, puede acceder a la salida del trabajo de BES con la dirección URL para el modelo recién entrenado escribiendo `@{body('besconnector').Results.output2.FullURL}` en el campo de dirección URL del modelo reciclado. Esto presupone que la salida de servicio web en el experimento de entrenamiento se llama output2. * Como nombre del recurso, use el nombre completo del modelo reciclado que guardó en el experimento predictivo, p. ej., MyTrainedModel [modelo reciclado] * En el campo Clave de resultado de evaluación, puede introducir cualquiera de los parámetros que se devuelven en la salida del módulo Evaluar del experimento de entrenamiento (si se ha incluido). Puede ver la lista de parámetros disponibles visualizando los resultados del módulo Evaluar en el experimento de entrenamiento de Estudio de aprendizaje automático de Azure. En un experimento de clasificación, se incluirían Exactitud, Precisión, Recuperación, Puntuación F, AUC, Pérdida media de registro y Pérdida de registro de formación.

![][4]
 
### Reciclaje programado
 
Mediante desencadenadores de aplicación lógica, puede configurar el conector para ejecutarlo en una programación. Esto puede habilitar el reciclaje de un modelo periódicamente a medida que llegan nuevos datos. El trabajo de BES podría reciclar el modelo y la acción Reciclaje actualizaría el modelo una vez completado el reciclaje.
 
![][5]
 
## Salida del conector 
 
**BES**: una vez finalizado correctamente el trabajo por lotes, la salida del conector tendrá la siguiente información para cada salida de servicio web.
 
 ![][6]
 
Tenga en cuenta que esta opción no estará disponible si no se incluyó una salida de servicio web (por ejemplo, se usa un módulo Escritor para escribir en una base de datos desde el experimento del Estudio).

**Reciclaje**: una vez finalizado correctamente el reciclaje, la salida tendrá la siguiente información.

![][7]

## Resumen

Con el conector de Aprendizaje automático de Azure para aplicaciones lógicas, puede ejecutar trabajos de puntuación y reciclaje de lotes que se ejecuten a petición o según una programación periódica. La combinación de las dos acciones puede automáticamente puntuar los datos, y reciclar, evaluar y actualizar el modelo de servicio web sin necesidad de escribir ningún código.

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_1217_2015-->