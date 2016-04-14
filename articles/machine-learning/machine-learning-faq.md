<properties
	pageTitle="Preguntas frecuentes de Aprendizaje automático de Azure | Microsoft Azure"
	description="Introducción a Aprendizaje automático Azure: preguntas más frecuentes sobre facturación, capacidades y limitaciones de un servicio de nube para un modelado de predicción optimizado."
	keywords="introducción de aprendizaje automático, modelo predictivo, qué es el aprendizaje automático"
	services="machine-learning"
	documentationCenter=""
	authors="pablissima"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/25/2016"
	ms.author="paulettm"/>

# Preguntas más frecuentes de Aprendizaje automático de Azure: facturación, capacidades, limitaciones y compatibilidad

Las preguntas más frecuentes son preguntas y respuestas sobre Aprendizaje automático de Azure, un servicio en la nube para soluciones de funcionamiento y modelado de predicción a través de servicios web. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad.

## Preguntas generales

**¿Qué es el Aprendizaje automático de Azure?**

El Aprendizaje automático de Azure es un servicio completamente administrado que sirve para crear, probar, utilizar y administrar soluciones de análisis predictivo en la nube. Con solo un explorador, puede registrarse, cargar datos e realizar inmediatamente experimentos de aprendizaje automático. Arrastre y colocación del modelado de predicción, una gran paleta de módulos y una biblioteca de plantillas de inicio convierten las tareas de Aprendizaje automático comunes en algo sencillo y rápido. Para obtener más información, consulte [Descripción general del servicio de Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/). Para obtener una introducción de aprendizaje automático que cubre los conceptos y terminología clave, consulte [Introducción al aprendizaje automático de Azure](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**¿Qué es Estudio de aprendizaje automático?**

Estudio de aprendizaje automático es un entorno de área de trabajo a la que se accede mediante un explorador web. Estudio de aprendizaje automático dispone de una paleta de módulos con una interfaz de composición visual que permite crear un flujo de trabajo de datos científicos completo en forma de un experimento.

Para obtener más información sobre Estudio de aprendizaje automático, consulte [¿Qué es Estudio de aprendizaje automático?](machine-learning-what-is-ml-studio.md)

**¿Qué es el servicio API de Aprendizaje automático?**

El servicio API de Aprendizaje automático permite implementar modelos predictivos creados en Estudio de aprendizaje automático como servicios web escalables con tolerancia a errores. Los servicios web creados con el servicio API de Aprendizaje automático son varias API de REST que proporcionan una interfaz para la comunicación entre las aplicaciones externas y los modelos de análisis predictivo.

Consulte [Conexión a un servicio web de Aprendizaje automático](machine-learning-connect-to-azure-machine-learning-web-service.md) para obtener más información.


## Preguntas sobre facturación

**¿Cómo funciona la facturación de Aprendizaje automático?**

Para obtener información sobre precios y facturación, consulte [Precios de Aprendizaje automático](https://azure.microsoft.com/pricing/details/machine-learning/).

**¿Dispone Aprendizaje automático de una evaluación gratuita?**

 Cuando se registra para una evaluación gratuita de Azure, puede probar cualquier servicio de Azure durante un mes. Para obtener más información acerca de la evaluación gratuita de Azure, visite [Preguntas más frecuentes sobre la evaluación gratuita de Azure](/pricing/free-trial-faq/).

## Preguntas sobre Estudio de aprendizaje automático

### Creación de un experimento
**¿Hay control de versiones o integración Git para experimentar con gráficos?**

No. Sin embargo, cada vez que se ejecuta un experimento, esa versión del gráfico se conserva y no la pueden modificar otros usuarios.

### Importación y exportación de datos para Aprendizaje automático
**¿Qué orígenes de datos admite Aprendizaje automático?**

Los datos se pueden cargar en Estudio de aprendizaje automático de dos formas: se pueden cargar los archivos locales como un conjunto de datos o se puede usar el módulo de lectura para importar los datos. Los archivos locales se pueden cargar mediante la incorporación de nuevos conjuntos de datos a Estudio de aprendizaje automático. Consulte [Importar datos de entrenamiento a Estudio de aprendizaje automático](machine-learning-data-science-import-data.md) para obtener más información acerca de los formatos de archivo compatibles.


#### <a id="ModuleLimit"></a>¿Cómo de grande puede ser el conjunto de datos para mis módulos?

Los módulos en Estudio de aprendizaje automático admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, los 10 GB serán el total de todos los tamaños de entrada. También puede realizar un muestreo de conjuntos de datos más grandes mediante consultas de Base de datos SQL de Azure o Hive o usando el procesamiento previo de Aprendizaje por recuentos, antes de la ingesta.

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características. Están limitados a menos de 10 GB:

- Dispersos
- Categorías
- Cadenas
- Datos binarios

Los siguientes módulos solo admiten conjuntos de datos que tengan menos de 10 GB:

- Módulos de recomendación.
- Módulo SMOTE.
- Módulos de script: R, Python y SQL
- Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
- Validación cruzada, parámetros de barrido, regresión ordinal y "uno frente a todo multiclase", cuando el número de iteraciones sea muy grande.

En el caso de conjuntos de datos que tengan varios gigas, hay que cargar los datos en el almacenamiento de Azure o en la Base de datos SQL de Azure o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.


####<a id="UploadLimit"></a>¿Cuáles son los límites de carga de datos?
En el caso de conjuntos de datos que tengan más de dos gigas, hay que cargar los datos en el almacenamiento de Azure o en la Base de datos SQL de Azure o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.

**¿Se pueden leer datos de Amazon S3?**

Si tiene una pequeña cantidad de datos y desea exponerlos a través de una dirección URL http, puede usar el módulo de [lectura][reader]. Para transferir grandes cantidades de datos al Almacenamiento de Azure, en primer lugar, hay que realizar la transferencia y, luego, utilizar el módulo [Lector][reader] para incluirlos en el experimento.
<!--
<SEE CLOUD DS PROCESS>
-->

**¿Hay una capacidad integrada para usar una entrada de imagen?**

Puede obtener información sobre la capacidad para usar entradas de imágenes en [Lector de imágenes][image-reader].

### Módulos

**El algoritmo, el origen de datos, el formato de datos o la operación de transformación de datos que busco no están en Estudio de aprendizaje automático de Azure. ¿Qué opciones tengo?**

Puede visitar el [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231) para ver las solicitudes de características cuyo seguimiento estamos realizando. Agregue su voto a esta solicitud si la funcionalidad que busca ya se ha solicitado. Si la funcionalidad que busca no existe, cree una nueva solicitud. El estado de su solicitud puede verlo también en este foro. Seguimos muy de cerca esta lista y actualizamos el estado de la disponibilidad de las características con frecuencia. Además de la compatibilidad integrada para R y Python, las transformaciones personalizadas pueden crearse según sea necesario.


**¿Puedo importar mi código existente a Estudio de aprendizaje automático?**

Sí, puede usar su código R existente en Estudio de aprendizaje automático y ejecutarlo en el mismo experimento con Aprendizaje automático de Azure que han proporcionado los aprendices. Luego puede implementarlo como un servicio web a través de Aprendizaje automático de Azure. Consulte [Ampliación de experimentos con R](machine-learning-extend-your-experiment-with-r.md).

**¿Se puede usar algo como [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir un modelo?**

No, no es compatible. Sin embargo, sí se puede utilizar código Phyton y R para definir un módulo.


### Procesamiento de datos

**¿Se pueden visualizar los datos (más allá de visualizaciones R) interactivamente con el experimento?**

Si hace clic en el resultado de un módulo, puede visualizar los datos y obtener las estadísticas.

**Al obtener una vista previa de los resultados o los datos en el explorador, el número de filas y columnas es limitado, ¿por qué?**

Como los datos se transmiten al explorador y pueden ser grandes, su tamaño está limitado para evitar que Estudio de aprendizaje automático se ralentice. Es mejor descargar los resultados o los datos y utilizar Excel u otra herramienta para verlos en su totalidad.

### Algoritmos

**¿Qué algoritmos existentes se admiten en Estudio de aprendizaje automático?**

Estudio de aprendizaje automático ofrece modernos algoritmos de Aprendizaje automático, como árboles de decisiones incrementados escalables, sistemas de recomendaciones bayesianas, redes neuronales profundas y junglas de decisiones desarrollados en Microsoft Research. También se incluyen paquetes de Aprendizaje automático escalables de código abierto como Vowpal Wabbit. Estudio de aprendizaje automático admite algoritmos de aprendizaje automático para clasificación, regresión y agrupación en clústeres y binarias y multiclase. Consulte la lista completa de [Módulos de aprendizaje automático][machine-learning-modules].

**¿Se sugiere automáticamente el algoritmo de Aprendizaje automático adecuado para utilizarlo con mis datos?**

No. Sin embargo, Estudio de aprendizaje automático ofrece varias maneras de comparar los resultados de cada algoritmo para determinar la opción correcta para su problema.

**¿Hay instrucciones sobre la elección de un algoritmo en lugar de otro de los proporcionados?** Consulte [Elección de un algoritmo](machine-learning-algorithm-choice.md).

**¿Se proporcionan algoritmos escritos en R o Python?**

No. Estos algoritmos principalmente se escriben en lenguajes compilados para que ofrezcan un mayor rendimiento.

**¿Se proporcionan algunos detalles de los algoritmos?**

La documentación proporciona alguna información acerca de los algoritmos, y se describen los parámetros proporcionados para optimizar el algoritmo para su uso.

**¿Se admite el aprendizaje en línea?**

No. Actualmente solo se admite el reentrenamiento mediante programación.

**¿Puedo visualizar las capas de un modelo de red neuronal con el módulo integrado?**

Nº

**¿Puedo crear mis propios módulos en C# o algún otro lenguaje?**

Actualmente, solo se pueden crear nuevos módulos personalizados en R.

### Módulo R

**¿Qué paquetes de R están disponibles en Estudio de aprendizaje automático?**

Estudio de aprendizaje automático admite en la actualidad más de 400 paquetes de CRAN R, y la lista sigue creciendo. Consulte [Ampliación de experimentos con R](machine-learning-extend-your-experiment-with-r.md) para conocer la forma de obtener la lista de paquetes R admitidos. Si el paquete que desea no está en la lista, especifique el nombre del paquete en el [foro de comentarios de los usuarios](http://go.microsoft.com/fwlink/?LinkId=404231).

**¿Es posible crear un módulo personalizado de R?**

Sí. Consulte [Creación de módulos R personalizados en Aprendizaje automático de Azure](machine-learning-custom-r-modules.md) para obtener más información.

**¿Hay un entorno de REPL para R?**

No. No hay ningún entorno de REPL para R en el estudio.

### Módulo de Python

**¿Es posible crear un módulo personalizado de Python?**

Actualmente no, pero con el módulo de Python estándar, o con un conjunto de ellos, se puede lograr el mismo resultado.

**¿Hay un entorno de REPL para Python?**

Puede usar los Jupyter Notebooks en el Estudio de aprendizaje automático de Azure. Para obtener más información, consulte [Presentación de Jupyter Notebooks en Estudio de aprendizaje automático de Azure](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)

## Servicio web

###Reentrenamiento de modelos mediante programación

**¿Cómo puedo volver a entrenar los modelos de Aprendizaje automático de Azure mediante programación?** Use las API de reentrenamiento. Hay código de ejemplo disponible [aquí](https://azuremlretrain.codeplex.com/).

### Crear

**¿Puedo implementar el modelo de forma local o en una aplicación sin conexión a internet?** Nº


**¿Cabe esperar una latencia de línea de base para todos los servicios web?**

Consulte [Límites de la suscripción de Azure](../azure-subscription-service-limits.md).

### Uso

**¿Cuándo podría ejecutar mi modelo predictivo como servicio de ejecución de lotes en lugar de como servicio web de solicitud/respuesta?**

El servicio de solicitud-respuesta (RRS) es un servicio web de alta escala y baja latencia que se usa para proporcionar una interfaz con los modelos sin estado creados e implementados desde el entorno de experimentación. El servicio de ejecución de lotes (BES) es un servicio para la puntuación asincrónica de lotes de registros de datos. La entrada para BES es similar a la entrada de datos que se utiliza en RRS. La diferencia principal radica en que BES lee un bloque de registros de varios orígenes, como servicios de blobs y tablas de Azure, Base de datos SQL de Azure, HDInsight (consultas de Hive) y orígenes HTTP. Para obtener más información, consulte [Consumo de servicios web de Aprendizaje automático](machine-learning-consume-web-services.md).

**¿Cómo se actualiza el modelo para la producción del servicio implementado?**

Actualizar un modelo predictivo para un servicio ya implementado es tan fácil como modificar y volver a ejecutar el experimento usado para crear y guardar el modelo entrenado. Una vez que puede disponer de la nueva versión del modelo entrenado, el Estudio de aprendizaje automático le pregunta si quiere actualizar el servicio web provisional. Después de aplicar la actualización al servicio web provisional, la misma actualización estará disponible para que la aplique también al servicio web de producción. Vea [Implementación de un servicio web de Aprendizaje automático](machine-learning-publish-a-machine-learning-web-service.md) para obtener detalles sobre cómo actualizar un servicio web implementado.

También puede usar las API de reciclaje. El código de ejemplo está disponible [aquí](https://azuremlretrain.codeplex.com/).

**¿Cómo se supervisa el servicio web implementado en producción?**

Cuando el modelo predictivo se ha puesto en producción, lo puede supervisar desde el Portal de Azure clásico. Cada servicio implementado cuenta con su propio panel, donde se puede ver la información de supervisión de ese servicio.

**¿Hay algún lugar donde pueda ver la salida de mi RRS/BES?**

Para RRS, en la respuesta del servicio web normalmente es donde se verá el resultado. También puede escribirlo en un blob. Para BES, el resultado se escribe en un blob de manera predeterminada. También puede escribir el resultado en una base de datos o una tabla con el módulo de escritor.

 **¿**Solamente puedo crear servicios web a partir de los modelos creados en el Estudio? No. También puede crear servicios web directamente desde los Jupyter Notebooks y RStudio.

 **¿Dónde puedo encontrar información sobre los códigos de error? Los códigos de error se describen [aquí.](https://msdn.microsoft.com/library/azure/dn905910.aspx)

## Escalabilidad

**¿Qué es la escalabilidad del servicio web?**

En la actualidad, el punto de conexión predeterminado se ha aprovisionado con 20 solicitudes simultáneas de RRS por punto de conexión. Puede escalar la solicitud simultánea a 200 solicitudes por punto de conexión y puede escalar cada servicio web a 10 000 puntos de conexión por servicio web como se describe en el artículo sobre [escalar puntos de conexión de la API](machine-learning-scaling-endpoints.md). Para BES, cada punto de conexión permite el procesamiento de 40 solicitudes a la vez y se ponen en cola las solicitudes adicionales que superan este número. Estas solicitudes en cola se ejecutarán automáticamente a medida que avanza la cola.


**¿Los trabajos de R se reparten entre nodos?**

Nº


**¿Con qué cantidad de datos puedo hacer entrenamientos?**

Los módulos en Estudio de aprendizaje automático admiten conjuntos de datos de hasta 10 GB de datos numéricos densos para casos de uso comunes. Si un módulo ocupa más de una entrada, los 10 GB serán el total de todos los tamaños de entrada. También puede realizar un muestreo de conjuntos de datos más grandes mediante consultas de Base de datos SQL de Azure o Hive o usando el procesamiento previo de Aprendizaje por recuentos, antes de la ingesta.

Los siguientes tipos de datos se pueden expandir en conjuntos de datos grandes durante la normalización de características. Están limitados a menos de 10 GB:

- Dispersos
- Categorías
- Cadenas
- Datos binarios

Los siguientes módulos solo admiten conjuntos de datos que tengan menos de 10 GB:

- Módulos de recomendación.
- Módulo SMOTE.
- Módulos de script: R, Python y SQL
- Módulos donde el tamaño de los datos de salida puede ser mayor que el tamaño de los datos de entrada, como en la aplicación de hash de característica o unión.
- Validación cruzada, parámetros de barrido, regresión ordinal y "uno frente a todo multiclase", cuando el número de iteraciones sea muy grande.

En el caso de conjuntos de datos que tengan varios gigas, hay que cargar los datos en el almacenamiento de Azure o en la Base de datos SQL de Azure o usar HDInsight, en lugar de cargarlos directamente desde el archivo local.


**¿Hay alguna limitación en el tamaño de los vectores?**

Las filas y las columnas están limitadas a la limitación .NET de Máx. int.: 2,147,483,647.

**¿Se puede ajustar el tamaño de la máquina virtual en la que se está ejecutando?**

Nº

## Seguridad y disponibilidad

**De forma predeterminada, ¿quién tiene acceso al punto final http del servicio web implementado en producción? ¿Cómo se restringe el acceso al punto final?**

Cuando se implementa un servicio web, creamos un extremo predeterminado para ese servicio. Ese extremo predeterminado se implementa en producción y se puede llamar mediante su clave de API. Los puntos de conexión adicionales se pueden agregar con sus propias claves desde el Portal de Azure clásico o mediante programación con las API de administración de servicios web. Las claves de acceso son necesarias para realizar llamadas al servicio web en producción y ensayo. Para obtener más información, consulte [Conexión a un servicio web de Aprendizaje automático](machine-learning-connect-to-azure-machine-learning-web-service.md).


**¿Qué ocurre si no encuentro mi cuenta de almacenamiento?**

Estudio de aprendizaje automático depende de la cuenta de almacenamiento de Azure que proporciona el usuario para guardar datos intermediarios al ejecutar el flujo de trabajo. Esta cuenta de almacenamiento se transmite a Estudio de aprendizaje automático en el momento de crear un área de trabajo. Una vez que se crea el área de trabajo, si se elimina la cuenta de almacenamiento y ya no puede encontrarse, el área de trabajo dejará de funcionar y todos experimentación que haya en ella fallarán.

Si elimina accidentalmente la cuenta de almacenamiento, la única manera de recuperarla es volver a crear esa cuenta de almacenamiento con el mismo nombre en la misma región que la eliminada. Después, vuelva a sincronizar la clave de acceso.


**¿Qué sucede si la clave de acceso de mi cuenta de almacenamiento no está sincronizada?** Estudio de aprendizaje automático depende de la cuenta de almacenamiento de Azure que proporciona el usuario para guardar datos intermediarios al ejecutar el flujo de trabajo. Esta cuenta de almacenamiento se transmite a Estudio de aprendizaje automático en el momento de crear un área de trabajo. Las claves de acceso se asocian a dicha área de trabajo. Una vez que se crea el área de trabajo, si se cambian las claves de acceso, el área de trabajo no podrá acceder a la cuenta de almacenamiento, por lo que dejará de funcionar y todos experimentación que haya en ella fallarán.

Si han cambiado las claves de acceso de la cuenta de almacenamiento, asegúrese de resincronizar las claves de acceso en la configuración del área de trabajo en el Portal de Azure clásico


## Azure Marketplace

Consulte [Preguntas más frecuentes sobre la publicación y utilización de aplicaciones de Aprendizaje automático en Marketplace](machine-learning-marketplace-faq.md)

## Soporte técnico y entrenamiento

**¿Dónde puedo recibir entrenamiento para Aprendizaje automático de Azure?**

El [Centro de Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) contiene tutoriales de vídeo y guías de procedimientos. Estas guías paso a paso proporcionan una introducción a los servicios y un recorrido por el ciclo de vida científico de los datos consistente en importar los datos, limpiarlos, construir modelos predictivos e implementarlos en producción con el Aprendizaje automático de Azure.

Iremos agregando continuamente nuevo material al Centro de Aprendizaje automático. Puede enviarnos una solicitud de material de aprendizaje adicional en el Centro de Aprendizaje automático a través del [foro de comentarios de los usuarios](https://windowsazure.uservoice.com/forums/257792-machine-learning).

También puede encontrar cursos en [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**¿Dónde puedo recibir soporte técnico para Aprendizaje automático de Azure?**

Para obtener soporte técnico para Aprendizaje automático de Azure, vaya al [Soporte técnico de Azure](/support/options/) y elija **Aprendizaje automático**.

El Aprendizaje automático de Azure cuenta también con un foro de la comunidad en MSDN, donde puede plantear preguntas sobre el tema. El equipo de Aprendizaje automático de Azure es el encargado de supervisar el foro. Visite el [foro de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0309_2016-->