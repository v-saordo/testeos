<properties 
	pageTitle="Ejecución de scripts de aprendizaje automático de Python | Microsoft Azure" 
	description="Describe los principios de diseño subyacentes a la compatibilidad con scripts de Python en Aprendizaje automático de Azure y los escenarios de uso básico, las funcionalidades y las limitaciones." 
	keywords="aprendizaje automático de Python, pandas, pandas de python, scripts de python, ejecutar scripts de python"
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2015" 
	ms.author="bradsev" />


# Ejecución de scripts de Python en Aprendizaje automático de Azure | Azure

En este tema se describen los principios de diseño subyacentes a la compatibilidad actual con scripts de Python en Aprendizaje automático de Azure. También se describen las funcionalidades principales, incluida la compatibilidad para importar código existente, exportar visualizaciones y, finalmente, se analizan algunas de las limitaciones y el trabajo en curso.

[Python](https://www.python.org/) es una herramienta indispensable de la caja de herramientas de muchos científicos de datos. Tiene una sintaxis elegante y concisa, asistencia multiplataforma, una amplia colección de potentes bibliotecas y herramientas de desarrollo perfeccionadas. Python se usa en todas las fases del flujo de trabajo que se usa normalmente en el modelado de aprendizaje automático, comenzando desde el procesamiento y la ingesta de datos hasta la construcción de características y al entrenamiento, la validación y la implementación de modelos.

Estudio de aprendizaje automático de Azure admite la incrustación de scripts de Python en varias partes de un experimento del aprendizaje automático y, además, su publicación sin ningún problema como servicios web escalables y de operaciones en Microsoft Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Principios de diseño de los scripts de Python en Aprendizaje automático
La interfaz principal para Python en Estudio de aprendizaje automático de Azure es mediante el módulo [Ejecutar script de Python][execute-python-script] que aparece en la figura 1.

![imagen1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![imagen2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. El módulo **Ejecutar script de Python**.

El módulo [Ejecutar script de Python][execute-python-script] acepta hasta tres entradas y genera hasta dos salidas (las que se analizan más adelante), al igual que su análogo R, el módulo [Ejecutar script de R][execute-r-script]. El código Python que se ejecutará se escribe en el cuadro de parámetro como una función de punto de entrada especialmente denominada llamada `azureml_main`. Los siguientes son los principios de diseño clave que se usan para implementar este módulo:

1.	*Debe ser idiomático para los usuarios de Python.* La mayoría de los usuarios de Python incluyen su código como funciones dentro de los módulos, por lo tanto, es poco frecuente poner gran cantidad de instrucciones ejecutables en un módulo de nivel superior. Como resultado, el cuadro del script también asume una función de Python especialmente denominada, en contraposición a solo una secuencia de instrucciones. Los objetos expuestos en la función son tipos de bibliotecas de Python estándar, como tramas de datos [Pandas](http://pandas.pydata.org/) y matrices [NumPy](http://www.numpy.org/).
2.	*Debe contar con alta fidelidad entre las ejecuciones locales y las ejecuciones en la nube.* El backend que se utiliza para ejecutar el código de Python se basa en [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1, una distribución científica de Python multiplataforma ampliamente utilizada. Incluye cerca de 200 de los paquetes más comunes de Python. Por lo tanto, un científico de datos podría depurar y calificar su código en su entorno Anaconda compatible con Aprendizaje automático de Azure mediante el uso de entornos de desarrollo existentes, como [IPython](http://ipython.org/) Notebook o [Herramientas de Python para Visual Studio](http://aka.ms/ptvs) y ejecutarlo con toda confianza como parte de un experimento de Aprendizaje automático de Azure. Además, el `azureml_main` punto de entrada es una función de básica de Python y se puede crear sin el código específico de Aprendizaje automático de Azure o el SDK instalado.
3.	*Debe admitir composición sin problemas con otros módulos de Aprendizaje automático de Azure.* El módulo [Ejecutar script de Python][execute-python-script] acepta, como entradas y salidas, conjuntos de datos estándar de Aprendizaje automático de Azure. El marco subyacente une de manera transparente y eficiente los tiempos de ejecución de Aprendizaje automático de Azure y de Python (y admite características como valores omitidos). Por tanto, Python se puede usar en conjunto con flujos de trabajo existentes de Aprendizaje automático de Azure, incluidos los que llaman a R y SQLite. De este modo, es posible prever flujos de trabajo que:
  * utilizan Python y Pandas para el procesamiento previo y la limpieza de los datos, 
  * transmitan los datos a una transformación de SQL, uniendo varios conjuntos de datos para formar características, 
  * entrenen modelos usando la amplia colección de algoritmos en Aprendizaje automático de Azure y 
  * evalúen y procesen posteriormente los resultados con R.


## Escenarios de uso básico en Aprendizaje automático para los scripts de Python
En esta sección, analizaremos algunos de los usos básicos del módulo [Ejecutar script de Python][execute-python-script]. Como se mencionó anteriormente, toda entrada que se realice en el módulo Python se expone como trama de datos de Pandas. Puede encontrar más información sobre Python Pandas y sobre cómo se puede usar para manipular datos de manera eficaz y eficiente en *Python for Data Analysis* (Sebastopol, CA.: O'Reilly, 2012) de W. McKinney. La función debe devolver una sola trama de datos de Pandas empaquetada dentro de una [secuencia](https://docs.python.org/2/c-api/sequence.html) de Python, como una tupla, una lista o una matriz de NumPy. Luego, el primer elemento de esta secuencia se devuelve en el primer puerto de salida del módulo. Este esquema aparece en la figura 2.

![imagen3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

Ilustración 2. Asignación de puertos de entrada a parámetros y valor de devolución a puerto de salida.

Puede ver una semántica más detallada de cómo se asignan los puertos de entrada a los parámetros de la `azureml_main` función en la tabla 1:

![imagen1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabla 1. Asignación de puertos de entrada a parámetros de función.

Tenga en cuenta que la asignación entre puertos de entrada y parámetros de función es posicional, es decir, el primer puerto de entrada conectado se asigna al primer parámetro de la función y la segunda entrada (si está conectada) se asigna al segundo parámetro de la función.

## Traslación de tipos de entrada y salida
Como se explicó anteriormente, los conjuntos de datos de entrada en Aprendizaje automático de Azure se convierten en tramas de datos en Pandas y las tramas de datos de salida se convierten nuevamente en conjuntos de datos de Aprendizaje automático de Azure. Se realizan las siguientes conversiones:

1.	Las columnas numéricas y de cadena se convierten tal cual y los valores que faltan en un conjunto de datos se convierten en valores "NA" en Pandas. Se produce la misma conversión de vuelta (los valores NA en Pandas se convierten en valores que faltan en Aprendizaje automático de Azure).
2.	Los vectores de índice en Pandas no son compatibles con Aprendizaje automático de Azure; además, todas las tramas de datos de entrada en la función Python siempre tendrán un índice numérico de 64 bits de 0 hasta el número de filas, menos 1. 
3.	Los conjuntos de datos de Aprendizaje automático de Azure no pueden tener nombres de columnas duplicados ni nombres de columnas que no sean cadenas. Si una trama de datos de salida contiene columnas no numéricas, el marco llama a `str` en los nombres de columna. Del mismo modo, los nombres de columnas duplicados se alternan automáticamente para asegurarse de que los nombres sean únicos. El sufijo (2) se agrega al primer duplicado, (3) al segundo duplicado, etc.

## Funcionamiento de los scripts de Python
Cuando un experimento de puntuación se publica como servicio web, se llama a todos los módulos [Ejecutar script de Python][execute-python-script] usados en él. Por ejemplo, la figura 3 muestra un experimento de puntuación que contiene el código para evaluar una expresión de Python.

![imagen4](./media/machine-learning-execute-python-scripts/figure3a.png)

![imagen5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Servicio web para evaluar una expresión de Python.

Un servicio web creado a partir de este experimento toma como entrada una expresión de Python (como cadena), la envía al intérprete de Python y devuelve una tabla que contiene tanto la expresión como el resultado evaluado.

## Importación de módulos existentes de scripts de Python
Un caso de uso común para muchos científicos de datos es incorporar scripts existentes de Python en experimentos de Aprendizaje automático de Azure. En lugar de concatenar y pegar todo el código en un solo cuadro de script, el módulo [Ejecutar script de Python][execute-python-script] acepta un tercer puerto de entrada al que se puede conectar un archivo ZIP que contiene los módulos de Python. A continuación, el marco de ejecución descomprime el archivo en tiempo de ejecución y los contenidos se agregan a la ruta de acceso de la biblioteca del intérprete de Python. La función de punto de entrada `azureml_main` luego puede importar directamente estos módulos.

Por ejemplo, considere el archivo Hello.py que contiene una función "Hello, World".

![imagen6](./media/machine-learning-execute-python-scripts/figure4.png)

Figura 4. Función definida por el usuario.

A continuación, podemos crear un archivo Hello.zip que contiene Hello.py:

![imagen7](./media/machine-learning-execute-python-scripts/figure5.png)

Figura 5. Archivo ZIP que contiene código Python definido por el usuario.

A continuación, cargue esto como un conjunto de datos en Estudio de aprendizaje automático de Azure. Luego creamos y ejecutamos un experimento simple que usa el módulo:

![imagen8](./media/machine-learning-execute-python-scripts/figure6a.png)

![imagen9](./media/machine-learning-execute-python-scripts/figure6b.png)

Figura 6. Experimento de ejemplo con código Python definido por el usuario cargado como archivo ZIP.

La salida del módulo muestra que el archivo ZIP se desempaquetó y que, efectivamente, la función `print_hello` se ha estado ejecutando. ![imagen10](./media/machine-learning-execute-python-scripts/figure7.png)
 
Figura 7. Función definida por el usuario en uso dentro del módulo [Ejecutar script de Python][execute-python-script].

## Trabajo con visualizaciones
El módulo [Ejecutar script de Python][execute-python-script] puede devolver los gráficos creados con MatplotLib que se pueden visualizar en el explorador. Sin embargo, los gráficos no se redirigen automáticamente a imágenes como sucede cuando se usa R. Por lo tanto, el usuario debe guardar explícitamente todo gráfico como archivo PNG si se va a devolver a Aprendizaje automático de Azure.

Con la finalidad de generar imágenes desde MatplotLib, debe completar el siguiente procedimiento:

* cambie el backend a "AGG" desde el representador basado en Qt predeterminado, 
* cree un nuevo objeto de figura, 
* obtenga el eje y genere todos los gráficos en él, 
* guarde la figura en un archivo PNG. 

Este proceso se muestra en la figura 8 que aparece a continuación, donde se crea una matriz de gráfico de dispersión con la función scatter\_matrix de Pandas.
 
![imagen1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

Figura 8. Figuras de MatplotLib guardadas como imágenes.



La figura 9 muestra un experimento que usa el script mostrado anteriormente para devolver gráficos a través del segundo puerto de salida.

![imagen2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png)
	 
![imagen2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png)

Figura 9. Visualización de los gráficos generados a partir del código Python.

Observe que es posible devolver varias figuras si se guardan en distintas imágenes: el tiempo de ejecución de Aprendizaje automático de Azure selecciona todas las imágenes y las concatena para visualización.


## Ejemplos avanzados
El entorno Anaconda instalado en Aprendizaje automático de Azure contiene paquetes comunes, como NumPy, SciPy y Scikits-Learn, los que se pueden usar de manera eficaz para varias tareas de procesamiento de datos en una canalización típica de aprendizaje automático. Por ejemplo, el siguiente experimento y script muestra el uso de sistemas aprendices de conjunto en Scikits-Learn para calcular las puntuaciones de importancia de características de un conjunto de datos. Las puntuaciones entonces se pueden usar para realizar la selección supervisada de las características antes de ingresarlas en otro modelo de aprendizaje automático.

A continuación, se muestra la función Python para calcular las puntuaciones de importancia y ordenar las características según esto:

![imagen11](./media/machine-learning-execute-python-scripts/figure8.png)

Figura 10. Función para clasificar características por puntuaciones. El siguiente experimento calcula y devuelve las puntuaciones de importancia de características en el conjunto de datos "Diabetes en los indios Pima" en Aprendizaje automático de Azure:

![imagen12](./media/machine-learning-execute-python-scripts/figure9a.png) ![imagen13](./media/machine-learning-execute-python-scripts/figure9b.png)
	
Figura 11. Experimento para clasificar las características del conjunto de datos Diabetes en los indios Pima.

## Limitaciones 
[Ejecutar script de Python][execute-python-script] actualmente tiene las siguientes limitaciones:

1.	*Ejecución en espacio aislado.* El tiempo de ejecución de Python actualmente se realiza en espacio aislado y, como resultado, no permite el acceso a la red o al sistema de archivos local de manera persistente. Todos los archivos guardados localmente son aislados y eliminados una vez que se finaliza el módulo. El código Python no puede tener acceso a la mayoría de los directorios de la máquina en que se ejecuta, con la excepción del directorio actual y sus subdirectorios.
2.	*Falta de compatibilidad con la depuración y desarrollo sofisticado.* El módulo Python actualmente no es compatible con características de IDE, como IntelliSense y depuración. Además, si se produce un error con el módulo en tiempo de ejecución, está disponible el seguimiento de la pila de Python completo, pero se debe ver en el registro de salida del módulo. Actualmente se recomienda que los usuarios desarrollen y depuren sus scripts de Python en un entorno como IPython y, a continuación, importen el código al módulo.
3.	*Salida de una trama de datos.* El punto de entrada de Python solo tiene permitido devolver una trama de datos como salida. Actualmente no es posible devolver objetos arbitrarios de Python, como modelos entrenados, directamente de vuelta al tiempo de ejecución de Aprendizaje automático de Azure. Si embargo, al igual que con [Ejecutar script de R][execute-r-script], que tiene la misma limitación, en muchos casos es posible incluir objetos en una matriz de bytes y, luego, devolverla dentro de una trama de datos.
4.	*Incapacidad para personalizar la instalación de Python*. Actualmente, la única manera de agregar módulos personalizados de Python es a través del mecanismo de archivo ZIP descrito anteriormente. A pesar de que esto es viable cuando se trata de módulos pequeños, es complicado para módulos de gran tamaño (especialmente para los módulos con DLL nativas) o para un gran número de módulos. 


##Conclusiones
El módulo [Ejecutar script de Python][execute-python-script] permite que un científico de datos incorpore código Python existente en flujos de trabajo de aprendizaje automático hospedados en la nube en Aprendizaje automático de Azure y hacerlos operativos como parte de un servicio web. El módulo de script de Python interopera de manera natural con otros módulos en Aprendizaje automático de Azure y se puede usar para una variedad de tareas, que van desde la exploración de datos al procesamiento previo, a la extracción de características, a la evaluación y al procesamiento posterior de los resultados. El tiempo de ejecución de backend que se usa para la ejecución se basa en Anaconda, una distribución de Python comprobada y ampliamente usada. Esto facilita que los usuarios incorporen recursos de código existentes a la nube.

Durante los próximos meses, esperamos proporcionar una funcionalidad adicional al módulo [Ejecutar script de Python][execute-python-script], como la capacidad de entrenar y hacer operativos los modelos en Python y de agregar una mejor compatibilidad para el desarrollo y código de depuración en Estudio de aprendizaje automático de Azure.

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

<!---HONumber=AcomDC_1217_2015-->