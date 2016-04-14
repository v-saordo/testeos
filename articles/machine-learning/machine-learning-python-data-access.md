<properties 
	pageTitle="Acceso a conjuntos de datos con la biblioteca de cliente de Python de Aprendizaje automático | Microsoft Azure" 
	description="Instale y use la biblioteca de cliente de Python para tener acceso y administrar datos de Aprendizaje automático de Azure de forma segura desde un entorno local de Python." 
	services="machine-learning" 
	documentationCenter="python" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2016" 
	ms.author="huvalo;bradsev" />


#Acceso a conjuntos de datos con Python mediante la biblioteca de cliente de Python de Aprendizaje automático de Azure 

La vista previa de la biblioteca de cliente de Python de Aprendizaje automático de Microsoft Azure puede permitir un acceso seguro a los conjuntos de datos de Aprendizaje automático de Azure desde un entorno local de Python, así como la creación y administración de conjuntos de datos en el área de trabajo.

Este tema proporciona instrucciones sobre cómo:

* instalar la biblioteca de cliente de Python de Aprendizaje automático; 
* obtener acceso y cargar conjuntos de datos, con instrucciones sobre cómo obtener autorización para el acceso a conjuntos de datos de Aprendizaje automático de Azure desde el entorno local de Python;
*  obtener acceso a los conjuntos de datos intermedios de experimentos;
*  usar la biblioteca de cliente de Python para enumerar conjuntos de datos, obtener acceso a los metadatos, leer el contenido de un conjunto de datos, crear nuevos conjuntos de datos y actualizar conjuntos de datos existentes.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Requisitos previos

La biblioteca de cliente de Python se ha probado en los siguientes entornos:

 - Windows, Mac y Linux
 - Python 2.7, 3.3 y 3.4

Tiene una dependencia en los siguientes paquetes:

 - Solicitudes
 - Python-dateutil
 - Pandas

Se recomienda utilizar una distribución de Python como [Anaconda](http://continuum.io/downloads#all) o [Canopy](https://store.enthought.com/downloads/), incluidas con Python, IPython y los tres paquetes enumerados anteriormente. Aunque IPython no es estrictamente necesario, es un excelente entorno para manipular y visualizar datos de forma interactiva.


###<a name="installation"></a>Cómo instalar la biblioteca de cliente de Python de Aprendizaje automático de Azure

La biblioteca de cliente de Python de Aprendizaje automático de Azure también debe instalarse para completar las tareas descritas en este tema. Está disponible desde el [Índice de paquetes de Python](https://pypi.python.org/pypi/azureml). Para instalarlo en su entorno de Python, ejecute el siguiente comando desde el entorno de Python local:

    pip install azureml

Como alternativa, puede descargar e instalar desde los orígenes de [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Si tiene git instalado en su equipo, puede usar pip para instalar directamente desde el repositorio de git:

	pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Utilizar fragmentos de código del Estudio para tener acceso a los conjuntos de datos

La biblioteca de cliente de Python proporciona acceso mediante programación a los conjuntos de datos existentes de los experimentos que se han ejecutado.

Desde la interfaz de web del Estudio, puede generar fragmentos de código que incluyen toda la información necesaria para descargar y deserializar los conjuntos de datos como objetos Pandas DataFrame en el equipo de la ubicación.

### <a name="security"></a>Seguridad de acceso a datos

Los fragmentos de código proporcionados por el Estudio para su uso con la biblioteca de cliente de Python incluyen el identificador de área de trabajo y el token de autorización. Estos proporcionan acceso completo a su área de trabajo y se deben proteger, como una contraseña.

Por motivos de seguridad, la funcionalidad de fragmento de código solo está disponible para los usuarios que tengan su rol definido como **Propietario** para el área de trabajo. Su rol se muestra en el Estudio de aprendizaje automático de Azure en la página **USUARIOS**, bajo **Configuración**.

![Seguridad][security]

Si su rol no está establecido como **Propietario**, puede solicitar que se le vuelva a invitar como propietario o pedir al propietario del área de trabajo que le proporcione el fragmento de código.

Para obtener el token de autorización, puede realizar una de las acciones siguientes:

1. Solicite un token a un propietario. Los propietarios pueden tener acceso a sus tokens de autorización en la página Configuración de su área de trabajo en el Estudio. Seleccione **Configuración** en el panel izquierdo y haga clic en **TOKENS DE AUTORIZACIÓN** para ver los tokens principales y secundarios. Aunque se pueden utilizar los tokens de autorización principales y secundarios, se recomienda que los propietarios solo compartan los tokens de autorización secundarios.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

2. Pida que le amplíen al rol de propietario. Para ello, un propietario actual del área de trabajo debe quitarle primero del área de trabajo y, a continuación, volver a invitarle como propietario.

Una vez que los desarrolladores han obtenido el identificador de área de trabajo y el token de autorización, podrán tener acceso al área de trabajo usando el fragmento de código independientemente de su rol.

Los tokens de autorización se administran en la página **TOKENS DE AUTORIZACIÓN**, en **CONFIGURACIÓN**. Puede volver a generarlos, pero este procedimiento revoca el acceso al token anterior.

### <a name="accessingDatasets"></a>Obtener acceso a los conjuntos de datos desde una aplicación local de Python

1. En el Estudio de aprendizaje automático, haga clic en **CONJUNTOS DE DATOS** en la barra de navegación de la izquierda.

2. Seleccione el conjunto de datos al que le gustaría tener acceso. Puede seleccionar cualquiera de los conjuntos de datos de la lista **MIS CONJUNTOS DE DATOS** o desde la lista **EJEMPLOS**.

3. En la barra de herramientas de la parte inferior, haga clic en **Generar código de acceso a datos**. Tenga en cuenta que este botón se deshabilitará si los datos están en un formato no compatible con la biblioteca de cliente de Python.

	![Conjuntos de datos][datasets]

4. Seleccione el fragmento de código de la ventana que aparece y cópielo al Portapapeles.

	![Código de acceso][dataset-access-code]

5. Pegue el código en el Bloc de notas de su aplicación local de Python.

	![Bloc de notas][ipython-dataset]

### <a name="accessingIntermediateDatasets"></a>Obtener acceso a los conjuntos de datos intermedios de experimentos de Aprendizaje automático

Después de ejecutar un experimento en el Estudio de aprendizaje automático, es posible tener acceso a los conjuntos de datos intermedios desde los nodos de salida de los módulos. Los conjuntos de datos intermedios son datos que se han creado y utilizado para pasos intermedios cuando se ha ejecutado una herramienta de modelo.

El acceso a los conjuntos de datos intermedios es posible siempre que el formato de los datos sea compatible con la biblioteca de cliente de Python.

Se admiten los siguientes formatos (sus constantes están en la clase `azureml.DataTypeIds`):

 - PlainText
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

Puede determinar el formato desplazando el mouse sobre un nodo de salida del módulo. Aparecerá junto al nombre del nodo, en la información sobre herramientas.

Algunos de los módulos, como el de [División][split], dan como resultado un formato denominado `Dataset`, que no es compatible con la biblioteca de cliente de Python.

![Formato de conjunto de datos][dataset-format]

Necesitará usar un módulo de conversión, como [Convertir a CSV][convert-to-csv], para obtener un resultado en un formato compatible.

![Formato GenericCSV][csv-format]

Los pasos siguientes muestran un ejemplo que crea un experimento, lo ejecuta y tiene acceso al conjunto de datos intermedio.

1. Cree un experimento nuevo.

2. Inserte un módulo **Conjunto de datos de clasificación binaria de ingresos en el censo de adultos**.

3. Inserte un módulo [División][split] y conecte su entrada a la salida del módulo del conjunto de datos.

4. Inserte un módulo [Convertir a CSV][convert-to-csv] y conecte su entrada a una de las salidas del módulo [División][split].

5. Guarde el experimento, ejecútelo y espere a que finalice su ejecución.

6. Haga clic en el nodo de salida en el módulo [Convertir a CSV][convert-to-csv].

7. Aparecerá un menú contextual; seleccione **Generar código de acceso a datos**.

	![Menú contextual][experiment]

8. Aparecerá una ventana. Seleccione el fragmento de código y cópielo al Portapapeles.

	![Código de acceso][intermediate-dataset-access-code]

9. Pegue el código en el Bloc de notas.

	![Bloc de notas][ipython-intermediate-dataset]

10. Puede visualizar los datos con matplotlib. Esto se muestra en un histograma de la columna de tiempo:

	![Histograma][ipython-histogram]


##<a name="clientApis"></a>Use la biblioteca de cliente de Python de Aprendizaje automático para obtener acceso, leer, crear y administrar conjuntos de datos.

### Área de trabajo

El área de trabajo es el punto de entrada para la biblioteca de cliente de Python. Proporcione la clase `Workspace` con su id. de área de trabajo y token de autorización para crear una instancia:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### Enumerar los conjuntos de datos

Para enumerar todos los conjuntos de datos en un área determinado:

    for ds in ws.datasets:
        print(ds.name)

Para enumerar solo los conjuntos de datos creados por el usuario:

    for ds in ws.user_datasets:
        print(ds.name)

Para enumerar solo los conjuntos de datos de ejemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Puede tener acceso a un conjunto de datos por nombre (que distingue mayúsculas de minúsculas):

    ds = ws.datasets['my dataset name']

O bien, puede tener acceso a él por su índice:

    ds = ws.datasets[0]


### Metadatos

Los conjuntos de datos tienen metadatos, además de contenido. (Los conjuntos de datos intermedios son una excepción a esta regla y no tienen metadatos).

Algunos valores de metadatos son asignados por el usuario en tiempo de creación:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Los demás son valores asignados por el Aprendizaje automático de Azure:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Vea la clase `SourceDataset` para obtener más información sobre los metadatos disponibles.


### Leer contenido

Los fragmentos de código que proporciona el Estudio de aprendizaje automático descargan y deserializan automáticamente el conjunto de datos a un objeto Pandas DataFrame. Esto se hace en el método `to_dataframe`:

    frame = ds.to_dataframe()

Si prefiere descargar los datos sin procesar y realizar la deserialización usted mismo, tiene esa opción. En este momento, esta es la única opción para formatos como 'ARFF', que la biblioteca de cliente de Python no puede deserializar.

Para leer el contenido como texto:

    text_data = ds.read_as_text()

Para leer el contenido como binario:

    binary_data = ds.read_as_binary()

También puede abrir una secuencia para el contenido:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### Crear un conjunto de datos nuevo

La biblioteca de cliente de Python le permite cargar conjuntos de datos desde el programa de Python. Estos conjuntos de datos estarán disponibles para su uso en el área de trabajo.

Si tiene sus datos en un Pandas DataFrame, utilice el siguiente código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Si sus datos ya están serializados, puede utilizar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La biblioteca de cliente de Python puede serializar un Pandas DataFrame a los siguientes formatos (sus constantes están en la clase `azureml.DataTypeIds`):

 - PlainText
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### Actualizar un registro existente

Si intenta cargar un nuevo conjunto de datos con un nombre que coincida con un conjunto de datos existente, obtendrá un error de conflicto.

Para actualizar un conjunto de datos existente, primero debe obtener una referencia al conjunto de datos existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

A continuación, utilice `update_from_dataframe` para serializar y reemplazar el contenido del conjunto de datos en Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Si desea serializar los datos a un formato diferente, especifique un valor para el parámetro opcional `data_type_id`.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, puede definir una nueva descripción especificando un valor para el parámetro `description`.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, puede definir un nuevo nombre especificando un valor para el parámetro `name`. De ahora en adelante, solo podrá recuperar el conjunto de datos con el nuevo nombre. El siguiente código actualiza los datos, el nombre y la descripción.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Los parámetros `data_type_id`, `name` y `description` son opcionales y tienen el valor anterior como el predeterminado. El parámetro `dataframe` siempre es obligatorio.

Si sus datos ya están serializados, puede utilizar`update_from_raw_data` en lugar de `update_from_dataframe`. Funciona de forma similar, se pasa `raw_data` en lugar `dataframe`.



<!-- Images -->
[security]: ./media/machine-learning-python-data-access/security.png
[dataset-format]: ./media/machine-learning-python-data-access/dataset-format.png
[csv-format]: ./media/machine-learning-python-data-access/csv-format.png
[datasets]: ./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]: ./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]: ./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]: ./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]: ./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]: ./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]: ./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!---HONumber=AcomDC_0302_2016-->