<properties 
	pageTitle="Crear módulos R personalizados en Aprendizaje automático | Microsoft Azure" 
	description="Inicio rápido para la creación de módulos R personalizados en Aprendizaje automático de Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/08/2016" 
	ms.author="bradsev;ankarloff" />


# Creación de módulos R personalizados en Aprendizaje automático de Azure

En este tema se describe cómo crear e implementar un módulo R personalizado en Aprendizaje automático de Azure. Se explica qué son los módulos R personalizados y qué archivos se usan para definirlos. Muestra cómo construir estos archivos y cómo registrar el módulo para implementarlo en un área de trabajo de Aprendizaje automático. Los elementos y atributos que se utilizan en la definición del módulo personalizado se describen a continuación con más detalle. También se describe cómo utilizar la funcionalidad y los archivos auxiliares, y varias salidas.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## ¿Qué es un módulo R personalizado?
Un **módulo personalizado** es un módulo definido por el usuario que se puede cargar en el área de trabajo de un usuario y ejecutarlo como parte de un experimento de Aprendizaje automático de Azure. Un **módulo de R personalizado** es un módulo personalizado que ejecuta una función de R definida por el usuario. R es un lenguaje de programación de computación estadística y gráficos utilizado ampliamente por científicos estadísticos y de datos para implementar algoritmos. Actualmente, R es el único lenguaje que se admite en los módulos personalizados, pero en las próximas versiones se agregará compatibilidad con idiomas adicionales.

Los módulos personalizados tienen un **estado de primera clase** en Aprendizaje automático de Azure, en el sentido de que se pueden usar como cualquier otro módulo. Pueden ejecutarse con otros módulos e incluirse en experimentos publicados o visualizaciones. Los usuarios tienen control sobre el algoritmo implementado por el módulo, los puertos de entrada y de salida a utilizar, los parámetros de modelado y otros distintos comportamientos en tiempo de ejecución. Un módulo personalizado solo está disponible en el área de trabajo en que se creó y no se puede publicar en experimentos de comunidad.

## Archivos de un módulo R personalizado
Un módulo R personalizado se define mediante un archivo .zip que contiene, como mínimo, dos archivos:

* Un **archivo de origen** que implementa la función R expuesta por el módulo.
* Un **archivo de definición XML** que describe la interfaz del módulo personalizado.

También se pueden incluir archivos auxiliares adicionales en el archivo .zip que proporcionan una funcionalidad a la que se puede tener acceso desde el módulo personalizado. Esta opción se describe a continuación.

## Ejemplo de inicio rápido: definir, empaquetar y registrar un módulo R personalizado
En este ejemplo se muestra cómo construir los archivos que requiere un módulo R personalizado, empaquetarlos en un archivo zip y, a continuación, registrar el módulo en un área de trabajo de Aprendizaje automático. Tanto el paquete zip de ejemplo como los archivos de ejemplo se pueden descargar en [Descargar el archivo CustomAddRows.zip](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

Considere el ejemplo de un módulo **Agregar filas** personalizado que modifica la implementación estándar del módulo de Agregar filas que se usa para concatenar las filas (observaciones) de dos conjuntos de datos (tramas de datos). El módulo Agregar filas estándar anexa las filas del segundo conjunto de datos de entrada al final del primer conjunto de datos de entrada mediante el algoritmo rbind. La función `CustomAddRows` personalizada acepta dos conjuntos de datos de forma similar, pero también acepta un parámetro booleano de intercambio adicional como entrada. Si el parámetro de intercambio es **FALSE**, devuelve el mismo conjunto de datos que la implementación estándar. Pero si el parámetro de intercambio es **TRUE**, anexa filas del primer conjunto de datos de entrada al final del segundo conjunto de datos. El archivo que implementa la función R `CustomAddRows` expuesta por el módulo **Agregar filas personalizado** contiene el siguiente código R.

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
	{
		if (swap)
		{
			return (rbind(dataset2, dataset1));
		}
		else
		{
			return (rbind(dataset1, dataset2));
		} 
	} 

Para exponer esta función `CustomAddRows` como módulo de Aprendizaje automático de Azure, se debe crear un archivo de definición XML para especificar la apariencia y comportamiento que debe tener el módulo **Agregar filas personalizado**.

	<!-- Defined a module using an R Script -->
	<Module name="Custom Add Rows">
	    <Owner>Microsoft Corporation</Owner>
	    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is false, and vice versa when Swap is true.</Description>
	
	<!-- Specify the base language, script file and R function to use for this module. -->		
	    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />  
		
	<!-- Define module input and output ports -->
	<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
	    <Ports>
			<Input id="dataset1" name="Dataset 1" type="DataTable">
				<Description>First input dataset</Description>
			</Input>
			<Input id="dataset2" name="Dataset 2" type="DataTable">
				<Description>Second input dataset</Description>
			</Input>
			<Output id="dataset" name="Dataset" type="DataTable">
				<Description>Combined dataset</Description>
			</Output>
	    </Ports>
		
	<!-- Define module parameters -->
	    <Arguments>
			<Arg id="swap" name="Swap" type="bool" >
				<Description>Swap input datasets.</Description>
			</Arg>
	    </Arguments>
	</Module>

 
Tenga en cuenta que el valor de los atributos **id** de los elementos **Input** y **Arg** del archivo XML deben coincidir exactamente con los nombres de parámetro de función del código de R (*dataset1*, *dataset2* y *swap* en el ejemplo). De forma similar, el valor el atributo **entryPoint** del elemento **Language** debe coincidir exactamente con el nombre de la función del script de R (*CustomAddRows* en el ejemplo). En cambio, el atributo **id** de los elementos **Output** no se corresponde con las variables del script de R. Cuando se requiere más de una salida, simplemente devuelva una lista de la función de R con los resultados colocados en el mismo orden en que las salidas se declaran en el archivo XML.

Guarde estos dos archivos como *CustomAddRows.R* y *CustomAddRows.xml* y comprímalos juntos en un el archivo *CustomAddRows.zip*.

Para registrarlos en su área de trabajo de Aprendizaje automático, vaya al área de trabajo de Estudio de aprendizaje automático, haga clic en el botón **+NUEVO** de la parte inferior y elija **MÓDULO -> DE PAQUETE ZIP** para cargar el nuevo módulo Agregar filas personalizado.

![Cargar archivo zip](./media/machine-learning-custom-r-modules/upload-from-zip-package.png)

El módulo **Agregar filas personalizado** ya está preparado para que los experimentos de Aprendizaje automático tengan acceso a él.

## Elementos del archivo de definición XML

### Elementos Module
El elemento **Module** se usa para definir un módulo personalizado en el archivo XML. Se pueden definir varios módulos en un archivo XML mediante el uso de varios elementos **Module**. Cada uno de los módulos del área de trabajo debe tener un nombre único. Registre un módulo personalizado con el mismo nombre que un módulo personalizado existente y reemplazará el módulo existente por otro nuevo. Sin embargo, los módulos personalizados se pueden registrar con el mismo nombre que un módulo de Aprendizaje automático de Azure existente y aparecerán en la categoría Custom de la paleta del módulo.

	<Module name="Custom Add Rows" isDeterministic="false"> 
		<Owner>Microsoft Corporation</Owner>
		<Description>Appends one dataset to another...</Description>/> 


En el elemento **Module**, puede especificar un elemento **Owner** que se incrusta en el módulo, así como un elemento **Description**, que es el texto que se muestra en la ayuda rápida del módulo y cuando se mantiene el mouse sobre el módulo en la interfaz de usuario de Aprendizaje automático.

**Reglas para los límites de caracteres de los elementos Module**:

* El valor del atributo **name** del elemento **Module** no debe superar los 64 caracteres. 
* El contenido del elemento **Description** no debe superar los 128 caracteres.
* El contenido del elemento **Owner** no debe superar los 32 caracteres.


**Lo que indica si los resultados de un módulo son deterministas o no deterministas**

De forma predeterminada, todos los módulos se consideran deterministas. Es decir, dado un conjunto de parámetros que no cambian, el módulo debe devolver los mismos resultados cada vez que se ejecute. Dado este comportamiento, Estudio de aprendizaje automático de Azure no vuelve a ejecutar módulos marcados como deterministas, a menos que un parámetro o los datos de entrada hayan cambiado. Se devuelven los resultados almacenados en la memoria caché, lo que resulta en una ejecución más rápida de los experimentos.

Sin embargo, si el módulo usa una función que devuelve resultados diferentes cada que vez que se ejecuta (por ejemplo, RAND o una función que devuelve la fecha u hora actuales), puede especificar que el módulo no sea determinista estableciendo el atributo optional **isDeterministic** en **false**. El módulo se volverá a ejecutar cada vez que se ejecute el experimento, aunque la entrada y los parámetros del módulo no hayan cambiado.

### Definición de lenguaje
El elemento **Lenguage** del archivo de definición XML se usa para especificar el idioma del módulo personalizado. Actualmente, R es el único lenguaje que se admite. El valor del atributo **sourceFile** debe ser el nombre del archivo R que contiene la función a la que se va a llamar cuando se ejecute el módulo. Este archivo debe formar parte del paquete zip. El valor del atributo **entryPoint** es el nombre de la función a la que se llama y debe coincidir con una función válida que se define en el archivo de origen.

	<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### Puertos
Los puertos de entrada y salida de un módulo personalizado se especifican en los elementos secundarios de la sección **Ports** del archivo de definición XML. El orden de estos elementos determina el diseño que ven (UX) los usuarios. La primera **entrada** o **salida** secundaria que aparezca en el elemento **Ports** del archivo XML será el puerto de entrada del extremo situado más a la izquierda en la experiencia de usuario de Aprendizaje automático. Cada puerto de entrada y salida puede tener un elemento secundario **Description** que especifica el texto que se muestra cuando un usuario desplaza el cursor del mouse sobre el puerto en la interfaz de usuario de Aprendizaje automático.

**Reglas de puertos**:

* El número máximo de **puertos de entrada y salida** es 8 para cada uno.

### Elementos de entrada
Los puertos de entrada permiten a los usuarios pasar datos a una función y un área de trabajo de R. Los **tipos de datos** que se admiten para los puertos de entrada son los siguientes:

**DataTable:** este tipo se pasa a la función de R como data.frame. De hecho, todos los tipos (por ejemplo, archivos CSV o archivos ARFF) que admite Aprendizaje automático y que son compatibles con **DataTable** se convierten automáticamente en data.frame.

		<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
        	<Description>Input Dataset 1</Description>
       	</Input>

El atributo **id** asociado a cada puerto de entrada de **DataTable** debe tener un valor único y dicho valor debe coincidir con el parámetro con nombre correspondiente de la función de R. El valor **NULL** de los puertos de **DataTable** opcionales que no se pasan como entrada en un experimento se pasa a la función de R y los puertos zip opcionales se ignorarán si la entrada no está conectada. El atributo **isOptional** es opcional para los tipos **DataTable** y **Zip**, y es *false* de forma predeterminada.
	   
**Zip:** los módulos personalizados pueden aceptar un archivo .zip como entrada. Esta entrada se desempaqueta en el directorio de trabajo de R de la función

		<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
        	<Description>Zip files will be extracted to the R working directory.</Description>
       	</Input>

En el caso de los módulos de R personalizados, el identificador de un puerto Zip no tiene que coincidir con los parámetros de la función de R, ya que el archivo ZIP se extrae automáticamente en el directorio de trabajo de R.

**Reglas de entrada:**

* El valor del atributo **id** del elemento **Input** debe ser un nombre de variable de R válido.
* El valor del atributo **id** del elemento **Input** no debe superar los 64 caracteres.
* El valor del atributo **name** del elemento **Input** no debe superar los 64 caracteres.
* El contenido del elemento **Description** no debe superar los 128 caracteres.
* El valor del atributo **type** del elemento **Input** debe ser *Zip* o *DataTable*.
* El valor del atributo **isOptional** del elemento **Input** no es necesario (y es *false* de forma predeterminada cuando no se especifica); pero si se especifica, debe ser *true* o *false*.

### Elementos de salida

**Puertos de salida estándar:** los puertos de salida se asignan a los valores devueltos desde la función de R, y luego pueden usarlos los módulos posteriores. *DataTable* es el único tipo de puerto de salida estándar que se admite actualmente. (Próximamente se incluirá compatibilidad con *Learners* y *Transforms*). Una salida de *DataTable* se define como:

	<Output id="dataset" name="Dataset" type="DataTable">
		<Description>Combined dataset</Description>
	</Output>

En el caso de las salidas de los módulos de R personalizados, el valor del atributo **id** no tiene que corresponder con nada del script de R, debe ser único. En el caso de la salida de un módulo individual, el valor devuelto de la función de R debe ser *data.frame*. Para emitir más de un objeto de un tipo de datos admitidos, es necesario especificar los puertos de salida correspondientes en el archivo de definición de XML y los objetos deben devolverse como una lista. Los objetos emitidos se asignarán a los puertos de salida de izquierda a derecha, reflejando el orden en que los objetos se colocan en la lista devuelta.

Por ejemplo, si quiere modificar el módulo **Agregar filas personalizado** para que genere los dos conjuntos de datos originales, *dataset1* y *dataset2*, además del conjunto de datos recién incorporado *dataset* (de izquierda a derecha, así: *dataset*, *dataset1*, *dataset2*), defina los puertos de salida en el archivo CustomAddRows.xml, tal como se indica a continuación:

	<Ports> 
		<Output id="dataset" name="Dataset Out" type="DataTable"> 
			<Description>New Dataset</Description> 
		</Output> 
		<Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
			<Description>First Dataset</Description> 
		</Output> 
		<Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
			<Description>Second Dataset</Description> 
		</Output> 
		<Input id="dataset1" name="Dataset 1" type="DataTable"> 
			<Description>First Input Table</Description>
		</Input> 
		<Input id="dataset2" name="Dataset 2" type="DataTable"> 
			<Description>Second Input Table</Description> 
		</Input> 
	</Ports> 


Y devuelva la lista de objetos de una lista en el orden correcto en "CustomAddRows.R":

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
		if (swap) { dataset <- rbind(dataset2, dataset1)) } 
		else { dataset <- rbind(dataset1, dataset2)) 
		} 
	return (list(dataset, dataset1, dataset2)) 
	} 
	
**Salida de visualización:** también puede especificar un puerto de salida del tipo *Visualization* que muestra la salida del dispositivo gráfico de R y la salida de la consola. Este puerto no forma parte de la salida de la función de R y no interfiere en el orden de los restantes tipos de puerto de salida. Para agregar un puerto de visualización a los módulos personalizados, agregue un elemento **Output** con un valor de *Visualization* para su atributo **type**:

	<Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>
	
**Reglas de salida:**

* El valor del atributo **id** del elemento **Output** debe ser un nombre de variable de R válido.
* El valor del atributo **id** del elemento **Output** no debe superar los 32 caracteres.
* El valor del atributo **name** del elemento **Output** no debe superar los 64 caracteres.
* El valor del atributo **type** del elemento **Output** debe ser *Visualization*.

### Argumentos
Se pueden pasar datos adicionales a la función de R a través de los parámetros del módulo que se definen en el elemento **Arguments**. Estos parámetros aparecen en el panel de propiedades de la derecha de la interfaz de usuario de Aprendizaje automático cuando está seleccionado el módulo. Los argumentos pueden ser cualquiera de los tipos admitidos, o bien puede crear una enumeración personalizada cuando sea necesario. Al igual que los elementos **Ports**, los elementos **Arguments** pueden tener un elemento **Description** opcional que especifica el texto que aparece al mantener el mouse sobre el nombre del parámetro. Las propiedades opcionales de un módulo, como defaultValue, minValue y maxValue, se pueden agregar a cualquier argumento como atributos de un elemento **Properties**. Las propiedades válidas para el elemento **Properties** dependen del tipo de argumento y se describen con los siguientes tipos de argumento admitidos. Al igual que con las entradas y salidas, es fundamental que cada uno de los parámetros tenga valores de identificadores únicos asociados a ellos. Además, los valores de identificador deben corresponderse a los parámetros con nombre de la función R. En nuestro ejemplo de inicio rápido, el parámetro/id asociado era *swap*.

### Elemento Arg
Los parámetros del módulo se definen mediante el elemento secundario **Arg** de la sección **Arguments** del archivo de definición XML. Al igual que con los elementos secundarios de la sección **Ports**, el orden de los parámetros de la sección **Arguments** define el diseño que se encuentra en la experiencia de usuario. Los parámetros aparecen de arriba abajo en la interfaz de usuario en el mismo orden en que se definen en el archivo XML. A continuación se enumeran los tipos admitidos por el aprendizaje automático para los parámetros.

**int**: parámetro de tipo entero (32 bits).

		<Arg id="intValue1" name="Int Param" type="int">
			<Properties min="0" max="100" default="0" />
			<Description>Integer Parameter</Description>
       </Arg>



* *Propiedades opcionales*: **min**, **max** y **default**.

**double**: parámetro de tipo doble.

       <Arg id="doubleValue1" name="Double Param" type="double">
           <Properties min="0.000" max="0.999" default="0.3" />
		   <Description>Double Parameter</Description>
       </Arg>


* *Propiedades opcionales*: **min**, **max** y **default**.

**bool**: parámetro booleano que se representa mediante una casilla en la experiencia de usuario.

		<Arg id="boolValue1" name="Boolean Param" type="bool">
			<Properties default="true" />
			<Description>Boolean Parameter</Description>
		</Arg>



* *Propiedades opcionales*: **default**: false si no se ha establecido

**string**: una cadena estándar.

        <Arg id="stringValue1" name="My string Param" type="string">
		   <Properties default="Default string value." isOptional="true" />
           <Description>String Parameter 1</Description>
        </Arg>


* *Propiedades opcionales*: **default** e **isOptional**; si el usuario no especifica un valor, se pasará una cadena opcional sin un valor predeterminado como null a la función de R.

**ColumnPicker**: parámetro de selección de columnas. Este tipo se representa en la experiencia de usuario como selector de columnas. El elemento **Property** se usa aquí para especificar el identificador del puerto desde el que se seleccionarán columnas, donde el tipo de puerto de destino debe ser *DataTable*. El resultado de la selección de columnas se pasará a la función de R como una lista de cadenas que contiene los nombres de columna seleccionados.

		<Arg id="colset" name="Column set" type="ColumnPicker">	  
		  <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
		  <Description>Column set</Description>
		</Arg>


* *Propiedades requeridas*: **portId**; coincide con el identificador de un elemento Input con tipo *DataTable*.
* *Propiedades opcionales*:
	* **allowedTypes**: filtra los tipos de columnas entre los que el usuario puede elegir. Los valores válidos son: 
		* 	Numeric
		* 	Booleano
		* 	Categorías
		* 	String
		* 	Etiqueta
		* 	Característica
		* 	Score
		* 	Todo

	* **default**: entre las selecciones predeterminadas válidas para el selector de columna se incluyen:
		* None
		* NumericFeature
		* NumericLabel
		* NumericScore
		* NumericAll
		* BooleanFeature
		* BooleanLabel
		* BooleanScore
		* BooleanAll
		* CategoricalFeature
		* CategoricalLabel
		* CategoricalScore
		* CategoricalAll
		* StringFeature
		* StringLabel
		* StringScore
		* StringAll
		* AllLabel
		* AllFeature
		* AllScore
		* Todo

                            							
**Desplegable**: lista (desplegable) especificada por el usuario que se muestra. Los elementos de la lista desplegable se especifican en el elemento **Properties** mediante un **Item**. El **id** de cada **Item** debe ser único y una variable de R válida, además, el nombre del elemento es tanto el texto que se muestra a los usuarios como el valor que se pasa a la función de R.

	<Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>	

* *Propiedades opcionales*:
	* **default**: el valor de la propiedad predeterminada debe corresponder a un valor de identificador de uno de los elementos **Item**.


### Archivos auxiliares

Cualquier archivo que se coloque en el archivo ZIP de módulo personalizado estará disponible para su uso durante el tiempo de ejecución. Si hay presente una estructura de directorios, se conservará. Esto significa que el abastecimiento de archivos funcionará igual localmente y en la ejecución de Aprendizaje automático de Azure. Observe que todos los archivos se extraen en el directorio 'src', por lo que todas las rutas de acceso deberían tener el prefijo 'src /'.

Por ejemplo, supongamos que desea quitar tanto todas las filas con ND como todas las filas duplicadas del conjunto de datos antes de generarlo en CustomAddRows y que ya ha escrito una función de R que lo hace en el archivo RemoveDupNARows.R:

	RemoveDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
Puede originar el archivo auxiliar RemoveDupNARows.R en la función CustomAddRows:

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
		source("src/RemoveDupNARows.R")
			if (swap) { 
				dataset <- rbind(dataset2, dataset1))
	 		} else { 
	  			dataset <- rbind(dataset1, dataset2)) 
	 		} 
		dataset <- removeDupNARows(dataset)
		return (dataset)
	}

A continuación, cargue un archivo zip que contenga "myAddRows.R", "myAddRows.xml" y "removeDupNARows.R" como módulo de R personalizado.

## Entorno de ejecución ##
El entorno de ejecución del script de R usa la misma versión de R que el módulo **Ejecutar script de R** y puede usar los mismos paquetes predeterminados. Puede agregar paquetes adicionales de R para el módulo personalizado incluyéndolos en el paquete zip del módulo personalizado y cargándolos en el script de R como lo haría en su propio entorno de R.

Entre las **limitaciones del entorno de ejecución** se incluyen:

* Sistema de archivos no persistentes: los archivos escritos cuando se ejecuta el módulo personalizado no persistirán en varias ejecuciones del mismo módulo.
* Sin acceso de red



 

<!---HONumber=AcomDC_0211_2016-->