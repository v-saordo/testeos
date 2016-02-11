<properties 
   pageTitle="Creación gráfica en Automatización de Azure"
   description="Creación gráfica le permite crear runbooks para Automatización de Azure sin trabajar con el código. Este artículo brinda una introducción a la creación gráfica y todos los detalles necesarios para comenzar a crear un runbook gráfico."
   services="automation"   
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="bwren" />

# Creación gráfica en Automatización de Azure

## Introducción

Creación gráfica permite crear runbooks para Automatización de Azure sin las complejidades del código subyacente de flujo de trabajo de Windows PowerShell. Puede agregar actividades de una biblioteca de cmdlets y otras actividades a lienzo y vincularlas para conformar un flujo de trabajo.

Este artículo proporciona una introducción a la creación gráfica y los conceptos que necesita para comenzar a crear un runbook gráfico.

## Runbooks gráficos

Todos los runbooks de Automatización de Azure son flujos de trabajo de Windows PowerShell. Los runbooks gráficos generan código de PowerShell que ejecutan los trabajos de Automatización, pero no es posible verlo ni modificarlo directamente. Los runbooks gráficos no se pueden convertir en runbooks de texto ni tampoco un runbook de texto se puede importar al editor de gráficos.


## Información general del editor de gráficos

Puede abrir el editor de gráficos en el Portal de vista previa de Azure a través de la creación o la edición de un runbook gráfico.

![Área de trabajo gráfica](media/automation-graphical-authoring-intro/graphical-editor.png)


Las secciones siguientes describen los controles del editor de gráficos.


### Lienzo
El lienzo es donde se diseña su runbook. Puede agregar actividades desde los nodos del control de Biblioteca al runbook y conectarlas con vínculos para definir la lógica del runbook.

Puede utilizar los controles en la parte inferior del lienzo para acercar y alejar.

![Área de trabajo gráfica](media/automation-graphical-authoring-intro/canvas-zoom.png)

### Control de Biblioteca

El control de Biblioteca es donde selecciona las [actividades](#activities) para agregar a su runbook. Puede agregarlas al lienzo donde las conecta a otras actividades. Incluye cuatro secciones que se describen en la tabla siguiente.

| Sección | Descripción |
|:---|:---|
| Cmdlets | Incluye todos los cmdlets que se pueden usar en su runbook. Los cmdlets se organizan por módulo. Estarán disponibles todos los módulos que ha instalado en su cuenta de Automatización. |
| Runbooks | Incluye los runbooks en la cuenta de Automatizado organizados por etiqueta. Debido a que un runbook puede tener más de una etiqueta, puede aparecer bajo varias etiquetas. Estos runbooks se pueden agregar al lienzo para usarlos como runbooks secundarios. Aparece el runbook que se está editando, pero no se puede agregar al lienzo, porque un runbook no se puede llamar a sí mismo.
| Recursos | Incluye los [recursos de Automatización](http://msdn.microsoft.com/library/dn939988.aspx) de su cuenta de Automatización que se pueden usar en el runbook. Cuando un recurso se carga en un runbook, agregará una actividad de flujo de trabajo que obtiene el recurso seleccionado. En el caso de los recursos de variables, puede seleccionar si agregar una actividad para obtener o seleccionar la variable.
| Control de Runbook | Incluye las actividades de control de runbook que se pueden usar en el runbook actual. Una *unión* toma varias entradas y espera hasta que todas se completen antes de continuar con el flujo de trabajo. Un *script de flujo de trabajo* ejecuta una o más líneas de código del flujo de trabajo de PowerShell. Puede usar esta actividad para código personalizado o para funcionalidad que no puede lograr con otras actividades.|

### Control de Configuración

El control de Configuración es donde se proporcionan los detalles de un objeto seleccionado en el lienzo. Las propiedades disponibles en este control dependerán del tipo de objeto seleccionado. Cuando selecciona una opción en el control de Configuración, se abrirán hojas adicionales para proporcionar más información.

### Control de Prueba

El control de Prueba no aparece cuando se inicia por primera vez el editor de gráficos. Se abre cuando [prueba un runbook gráfico](#graphical-runbook-procedures) de manera interactiva.

## Procedimientos de runbook gráficos 

### Exportación e importación de un runbook gráfico

Solo puede exportar la versión publicada de un runbook gráfico. Si el runbook no se ha publicado todavía, se deshabilitará el botón **Exportar publicados**. Al hacer clic en el **Exportar publicados**, el runbook se descargará en el equipo local. El nombre del archivo coincide con el nombre del runbook con una extensión *graphrunbook*.

![Exportar publicados](media/automation-graphical-authoring-intro/runbook-export.png)

Par importar un archivo de runbook gráfico, seleccione la opción **Importar** al agregar un runbook. Al seleccionar el archivo para importar, puede mantener el mismo **Nombre** o proporcionar otro nuevo.

![Importar runbook](media/automation-graphical-authoring-intro/runbook-import.png)


### Prueba de un runbook gráfico

Puede probar la versión de borrador de un runbook en el Portal de vista previa de Azure mientras deja sin modificación la versión publicada del runbook, o bien puede probar un runbook nuevo antes de su publicación. Esto permite comprobar que el runbook se ejecute correctamente antes de reemplazar la versión publicada. Cuando se prueba un runbook, se ejecuta el runbook de borrador y se completan todas las acciones que realiza. No se crea ningún historial de trabajos, pero sí se muestran los resultados en el Panel de resultados de prueba.

Para abrir el control de Prueba de un runbook, abra el runbook para editarlo y, a continuación, haga clic en el botón **Panel de prueba**.

![Botón Panel de prueba](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

El control de Prueba solicitará cualquier parámetro de entrada y podrá iniciar el runbook si hace clic en el botón **Comenzar**.

![Botones Control de Prueba](media/automation-graphical-authoring-intro/runbook-test-start.png)

### Publicación de un runbook gráfico

Cada runbook de Automatización de Azure tiene una versión de borrador y una versión publicada. Solo es posible ejecutar la versión publicada y solo es posible editar la versión de borrador. Los cambios realizados en la versión de borrador no afectan la versión publicada. Cuando la versión de borrador está lista para su disponibilidad, puede publicarla, lo que sobrescribirá la versión publicada con la versión de borrador.

Para publicar un runbook gráfico, abra el runbook para editarlo y, a continuación, haga clic en el botón **Publicar**.

![Botón Publicar](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

Cuando todavía no se ha publicado un runbook, tiene un estado de **Nuevo**. Cuando se publica, su estado es **Publicado**. Si edita el runbook después de haberlo publicado y la versión de borrador y la versión publicada son distintas, el runbook tiene un estado de **En edición**.

![Estados de runbooks](media/automation-graphical-authoring-intro/runbook-statuses.png)

También tiene la opción de revertir a la versión publicada de un runbook. Esto descarta los cambios realizados desde la última publicación del runbook y reemplaza la versión de borrador del runbook por la versión publicada.

![Revertir al botón de publicación](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)


## Actividades

Las actividades son los bloques de creación de un runbook. Una actividad puede ser un cmdlet de PowerShell, un runbook secundario o una actividad de flujo de trabajo. Para agregar una actividad al runbook, haga clic con el botón derecho en él en el control de Biblioteca y seleccione **Agregar a lienzo**. Luego puede hacer clic en la actividad y arrastrarla para colocarla en cualquier lugar del lienzo que desee. La ubicación de la actividad en el lienzo no afecta de ningún modo la operación del runbook. Puede distribuir el runbook de la manera que considere más adecuada para visualizar su operación.

![Agregar a lienzo](media/automation-graphical-authoring-intro/add-to-canvas.png)

Seleccione la actividad en el lienzo para configurar sus propiedades y parámetros en la hoja Configuración. Puede cambiar la **Etiqueta** de la actividad a algo que sea descriptivo. El cmdlet original sigue en ejecución; simplemente se cambia el nombre para mostrar que se usará en el editor gráfico. La etiqueta debe ser única dentro del runbook.

### Conjuntos de parámetros

Un conjunto de parámetros define los parámetros obligatorios y opcionales que aceptarán valores para un cmdlet determinado. Todos los cmdlets tienen, al menos, un conjunto de parámetros y algunos tienen varios. Si un cmdlet tiene varios conjuntos de parámetros, debe seleccionar el que usará antes de poder configurar los parámetros. Los parámetros que puede configurar dependerán del conjunto de parámetros que elija. Puede cambiar el conjunto de parámetros que una actividad usa mediante la selección de **Conjunto de parámetros** y luego de otro conjunto. En este caso, se pierden todos los valores de parámetro que configuró.

En el ejemplo siguiente, el cmdlet de Get-AzureVM tiene dos conjuntos de parámetros. No es posible configurar valores de parámetro hasta que seleccione uno de los conjuntos de parámetros. El conjunto de parámetros ListAllVMs se utiliza para devolver todas las máquinas virtuales y solo tiene un parámetro opcional. El parámetro GetVMByServiceand VMName se utiliza para especificar la máquina virtual que desea devolver y tiene un parámetro obligatorio y dos parámetros opcionales.

![Conjunto de parámetros](media/automation-graphical-authoring-intro/parameter-set.png)

#### Valores de parámetro

Cuando especifica un valor para un parámetro, selecciona un origen de datos para determinar cómo se especificará el valor. Los orígenes de datos disponibles para un parámetro determinado dependerán de los valores válidos para ese parámetro. Por ejemplo, Null no será una opción disponible para un parámetro que no permite valores nulos.

| Origen de datos | Descripción |
|:---|:---|
|Valor constante|Escriba un valor para el parámetro. Este solo se encuentra disponible para los siguientes tipos de datos: Int32, Int64, String, Boolean, DateTime, Switch. |
|Salida de la actividad|Salida de una actividad que precede la actividad actual en el flujo de trabajo. Se mostrarán todas las actividades válidas. Seleccione solo la actividad para usar su salida en el valor de parámetro. Si la actividad genera un objeto con varias propiedades, puede escribir el nombre de la propiedad después de seleccionar la actividad.|
|Parámetro de entrada de runbook|Seleccione un parámetro de entrada runbook como entrada para el parámetro de actividad.|  
|Recurso de variable de automatización|Seleccione una variable de Automatización como entrada.|  
|Recurso de credencial de automatización|Seleccione una credencial de Automatización como entrada.|  
|Recurso de certificado de automatización|Seleccione un certificado de Automatización como entrada.|  
|Recurso de conexión de automatización|Seleccione una conexión de Automatización como entrada.| 
|Expresión de PowerShell|Especifique una [expresión de PowerShell](#powershell-expressions) simple. La expresión se evaluará antes de la actividad y el resultado se usará en el valor del parámetro. Puede usar variables para consultar la salida de una actividad o un parámetro de entrada de runbook.|
|Cadena vacía|Un valor de cadena vacío.|
|Null|Un valor Null.|
|Anular selección|Borrar cualquier valor configurado anteriormente.|


#### Parámetros adicionales opcionales

Todos los cmdlets tendrán la opción de proporcionar parámetros adicionales. Se trata de parámetros comunes de PowerShell u otros parámetros personalizados. Aparecerá un cuadro de texto en el que podrá proporcionar parámetros con la sintaxis de PowerShell. Por ejemplo, para usar el parámetro común **Verbose**, debiera especificar **"-Verbose:$True"**.

### Vuelva a intentar la actividad

**Comportamiento de reintento** permite que una actividad se ejecute varias veces hasta que se cumpla una condición determinada. Puede utilizar esta característica para las actividades que deben ejecutarse varias veces o que son propensas a errores y pueden requerir más de un intento para realizarlas correctamente.

Cuando se habilita el reintento de una actividad, puede establecer un retraso y una condición. El retraso es el tiempo (medido en segundos o minutos) que el runbook esperará antes de que se ejecute la actividad de nuevo. Si no se especifica ningún retraso, la actividad se ejecutará de nuevo inmediatamente después de que se complete.

![Retraso de reintento de actividades](media/automation-graphical-authoring-intro/retry-delay.png)

La condición de reintento es una expresión de PowerShell que se evalúa cada vez que se ejecuta la actividad. Si la expresión se resuelve como True, la actividad se vuelve a ejecutar. Si la expresión se resuelve en False, la actividad no se vuelve a ejecutar y el runbook se mueve a la actividad siguiente.

![Retraso de reintento de actividades](media/automation-graphical-authoring-intro/retry-condition.png)

La condición de reintento puede utilizar una variable denominada $RetryData que proporciona acceso a información sobre los reintentos de actividad. Esta variable tiene las propiedades de la tabla siguiente.

| Propiedad | Descripción |
|:--|:--|
| NumberOfAttempts | Número de veces que se ha ejecutado la actividad. |
| Salida | Salida de la última ejecución de la actividad. |
| TotalDuration | Tiempo transcurrido desde la primera vez que se inició la actividad. |
| StartedAt | Hora en formato UTC a la que se inició la actividad por primera vez. |

A continuación se muestran ejemplos de las condiciones de reintento de actividades.

	# Run the activity exactly 10 times.
	$RetryData.NumberOfAttempts -ge 10 

	# Run the activity repeatedly until it produces any output.
	$RetryData.Output.Count -ge 1 

	# Run the activity repeatedly until 2 minutes has elapsed. 
	$RetryData.TotalDuration.TotalMinutes -ge 2

### Control de Script de flujo de trabajo

Un control de Script de flujo de trabajo es una actividad especial que acepta código de flujo de trabajo de PowerShell para proporcionar funcionalidad que, de otro modo, podría no estar disponible. Este no es un flujo de trabajo completo, pero debe contener líneas válidas de código de flujo de trabajo de PowerShell. No puede aceptar parámetros, pero puede usar variables para los parámetros de entrada de runbook y de salida de actividad. Cualquier salida de la actividad se agrega al bus de datos, a menos que no tenga un vínculo de salida, en cuyo caso se agrega a la salida del runbook.

Por ejemplo, el código siguiente realiza cálculos de fecha con una variable de entrada de runbook llamada $NumberOfDays. Luego envía una fecha y hora calculadas como salida para su uso por parte de actividades subsiguientes en el runbook.

    $DateTimeNow = InlineScript{(Get-Date).ToUniversalTime()}
    $DateTimeStart = InlineScript{($using:DateTimeNow).AddDays(-$using:NumberOfDays)}
	$DateTimeStart


## Vínculos y flujo de trabajo

Un **vínculo** en un runbook gráfico conecta dos actividades. Se muestra en el lienzo como una flecha que apunta desde la actividad de origen hasta la actividad de destino. Las actividades se ejecutan en la dirección de la flecha y la actividad de destino se inicia una vez que se completa la actividad de origen.

### Creación de un vínculo

Para crear un vínculo entre dos actividades, seleccione la actividad de origen y haga clic en el círculo que aparece en la parte inferior de la forma. Arrastre la flecha a la actividad de destino y suéltela.

![Creación de un vínculo](media/automation-graphical-authoring-intro/create-link.png)

Seleccione el vínculo para configurar sus propiedades en la hoja Configuración. Esto incluirá el tipo de vínculo que se describe en la tabla siguiente.

| Tipo de vínculo | Descripción |
|:---|:---|
| Canalización | La actividad de destino se ejecuta una vez para cada salida de objeto desde la actividad de origen. La actividad de destino no se ejecuta si la actividad de origen no genera salida. La salida de la actividad de origen está disponible como objeto. |
| Secuencia | La actividad de destino se ejecuta solo una vez. Recibe una matriz de objetos desde la actividad de origen. La salida de la actividad de origen está disponible como una matriz de objetos. |

### Actividad de inicio

Un runbook gráfico se iniciará con cualquier actividad que no tenga un vínculo entrante. A menudo, esta será solo una actividad que podría servir como la actividad de inicio del runbook. Si son varias las actividades que no tienen un vínculo entrante, el runbook se iniciará al ejecutarlas en paralelo. A continuación, el runbook seguirá los vínculos para ejecutar otras actividades a medida que se finaliza cada una.

### Condiciones

Cuando especifica una condición en un vínculo, la actividad de destino solo se ejecuta si la condición se resuelve como verdadera. Normalmente usará una variable $ActivityOutput en una condición para recuperar la salida desde la actividad de origen.

Para un vínculo de canalización, especifique una condición para un solo objeto y la actividad de origen evaluará la condición para cada salida de objeto. A continuación, se ejecuta la actividad de destino para cada objeto que cumple con la condición. Por ejemplo, con una actividad de Get-AzureVM, podría usarse la siguiente sintaxis para que un vínculo de canalización condicional recupere solo las máquinas virtuales que actualmente se encuentran en ejecución.

	$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'

En el caso de un vínculo de secuencia, la condición solo se evalúa una vez, debido que se devuelve una sola matriz que contiene la salida de todos los objetos desde la actividad de origen. Debido a esto, no es posible usar un vínculo de secuencia para filtrar como un vínculo de canalización, sino que simplemente se determinará si la actividad siguiente se ejecuta o no. El código siguiente muestra el mismo ejemplo de evaluación de salida desde Get-AzureVM para determinar las máquinas virtuales que están en ejecución. En este caso, el código pasa por cada objeto de la matriz y se resuelve en verdadero si al menos una máquina virtual está en ejecución. La actividad de destino sería responsable de analizar estos datos.

	$test = $false
	$VMs = $ActivityOutput['Get-AzureVm']
	Foreach ($VM in VMs)
	{
		If ($VM.PowerState –eq 'Started')
			{
				$test = $true
			}
	}
	$test

Cuando usa un vínculo condicional, los datos disponibles desde la actividad de origen a otras actividades en esa rama se filtrarán según la condición. Si una actividad es el origen de varios vínculos, los datos disponibles para las actividades en cada rama dependerán de la condición del vínculo que se conecta a esa rama.

Por ejemplo, la actividad de origen del runbook que aparece a continuación obtiene todas las máquinas virtuales. Tiene dos vínculos condicionales y un vínculo sin una condición. El primer vínculo condicional usa la expresión *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'* para filtrar solo las máquinas virtuales que están en ejecución. El segundo usa la expresión *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Stopped'* para filtrar solo las máquinas virtual que están detenidas.

![Ejemplo de vínculo condicional](media/automation-graphical-authoring-intro/conditional-links.png)

Una actividad que sigue el primer vínculo y usa la salida de la actividad desde Get-AzureVM solo obtendrá las máquinas virtuales iniciadas en el momento en que se ejecutó Get-AzureVM. Toda actividad que siga el segundo vínculo solo obtendrá las máquinas virtuales detenidas en el momento en que se ejecutó Get-AzureVM. Toda actividad que siga el tercer vínculo obtendrá todas las máquinas virtuales, sin importar su estado de ejecución.

### Uniones

Una unión es una actividad especial que esperará hasta que se hayan completado todas las ramas entrantes. Esto le permite ejecutar varias actividades en paralelo y garantizar que todas se hayan completado antes de continuar.

A pesar de que una unión puede tener una cantidad ilimitada de vínculos entrantes, solo uno de esos vínculos puede ser una canalización. No se limita la cantidad de vínculos entrantes de secuencia. Podrá crear la unión con varios vínculos entrantes de canalización y guardar el runbook, pero se producirá un error cuando esté en ejecución.

El ejemplo siguiente forma parte de un runbook que inicia un conjunto de máquinas virtuales, a la vez que descarga revisiones para aplicarlas a esas máquinas. Una unión se usa para garantizar que ambos procesos se completen antes de que el runbook continúe.

![Unión](media/automation-graphical-authoring-intro/junction.png)

### Ciclos

Un ciclo es cuando una actividad de destino se vincula de vuelta a su actividad de origen o a otra actividad que, al final, se vincule a su origen. La creación gráfica no permite actualmente los ciclos. Si el runbook tiene un ciclo, lo guardará como corresponde, pero recibirá un error cuando se ejecute.

![Ciclo](media/automation-graphical-authoring-intro/cycle.png)

### Bucles

Un bucle es cuando se repite una actividad una cantidad de veces especificada o se sigue repitiendo hasta que se cumple una condición determinada. Los runbooks gráficos no admiten actualmente los bucles.

### Uso compartido de datos entre actividades

Todo dato generado por una actividad con un vínculo saliente se escribe en el *bus de datos* del runbook. Toda actividad del runbook puede usar datos del bus de datos para rellenar los valores de parámetros o para incluirlos en el código de script. Una actividad puede tener acceso a la salida de cualquier actividad anterior en el flujo de trabajo.

La manera en que los datos se escriben en el bus de datos depende del tipo de vínculo en la actividad. En el caso de una **canalización**, los datos se generan como varios objetos. En el caso de un vínculo de **secuencia**, los datos se generan como una matriz. Si solo existe un valor, se generará como una matriz de un solo elemento.

Puede tener acceso al bus de datos si usa uno de estos dos métodos. El primer método es usar un origen de datos **Salida de actividad** para rellenar un parámetro de otra actividad. Si la salida es un objeto, puede especificar una propiedad.

![Salida de la actividad](media/automation-graphical-authoring-intro/activity-output-datasource.png)

También puede recuperar la salida de una actividad en un origen de datos **Expresión de PowerShell** o desde una actividad de **script de flujo de trabajo** con una variable ActivityOutput. Si la salida es un objeto, puede especificar una propiedad. Las variables ActivityOutput usan la siguiente sintaxis.

	$ActivityOutput['Activity Label']
	$ActivityOutput['Activity Label'].PropertyName 

### Puntos de control

Puede establecer [puntos de control](automation-powershell-workflow/#checkpoints) en un runbook gráfico; para ello, seleccione *Establecer punto de control en runbook* en cualquier actividad. Esto permite establecer un punto de control después de que se ejecuta la actividad.

![Punto de control](media/automation-graphical-authoring-intro/set-checkpoint.png)

Las mismas instrucciones para configurar los puntos de control en el runbook se aplican a los runbooks gráficos. Si el runbook usa cmdlets de Azure, debe seguir cualquier actividad establecida con puntos de control con un AzureRMAccount en caso de suspender el runbook y de reiniciarlo a partir de dicho punto de control en un trabajo diferente.


## Autenticación a los recursos de Azure

La mayoría de los runbooks en Automatización de Azure requerirán autenticación a los recursos de Azure. El método típico usado para esta autenticación es el cmdlet Add-AzureAccount con un [recurso de credencial](http://msdn.microsoft.com/library/dn940015.aspx) que representa un usuario de Active Directory con acceso a la cuenta de Azure. Esto se explica en [Configuración de Automatización de Azure](automation-configuring.md).

Puede agregar esta funcionalidad a un runbook gráfico si agrega un recurso de credencial al lienzo, seguido de una actividad Add-AzureAccount. Add-AzureAccount usa la actividad de credencial para su entrada. Esto se muestra en el ejemplo siguiente.

![Actividades de autenticación](media/automation-graphical-authoring-intro/authentication-activities.png)

Tiene que realizar la autenticación al comienzo del runbook y después de cada punto de control. Esto significa agregar una actividad Add-AzureAccount adicional después de toda actividad Checkpoint-Workflow. No necesita una actividad de credencial adicional, porque puede usar la misma.

![Salida de la actividad](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## Entrada y salida de runbook

### Entrada de runbook

Un runbook puede requerir una entrada, ya sea de un usuario cuando el runbook se inicia mediante el Portal de vista previa de Azure o desde otro runbook, si el actual se usa como secundario. Por ejemplo, si tiene un runbook que crea una máquina virtual, es posible que deba proporcionar información, como el nombre de la máquina virtual y otras propiedades cada vez que inicie el runbook.

Para aceptar entradas para un runbook, defina uno o más parámetros de entrada. Debe proporcionar valores para estos parámetros cada vez que se inicie el runbook. Cuando se inicia un runbook con el Portal de vista previa de Azure, se le solicitará que proporcione valores para cada uno de los parámetros de entrada del runbook.

Para tener acceso a los parámetros de entrada correspondientes a un runbook, haga clic en el botón **Entrada y salida** en la barra de herramientas del runbook.

![Entrada y salida de runbooks](media/automation-graphical-authoring-intro/runbook-edit-input-output.png)

Con esto se abre el control **Entrada y salida**, donde puede editar un parámetro de entrada existente o crear uno nuevo, mediante un clic en **Agregar entrada**.

![Agregar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Las propiedades de la siguiente tabla definen cada parámetro de entrada.

|Propiedad|Descripción|
|:---|:---|
| Nombre | El nombre único del parámetro. Solo puede contener caracteres alfanuméricos y no puede contener un espacio. |
| Descripción | Una descripción opcional del parámetro de entrada. |
| Tipo | El tipo de datos que se espera para el valor del parámetro. El Portal de vista previa de Azure proporcionará un control adecuado para el tipo de datos de cada parámetro cuando se solicite una entrada. |
| Obligatorio | Especifica si se debe proporcionar un valor para el parámetro. No es posible iniciar el runbook si no se proporciona un valor para cada parámetro obligatorio que no tiene definido un valor predeterminado. |
| Valor predeterminado | Especifica el valor que se usa para el parámetro si no se brinda alguno. Puede ser Null o un valor específico. |


### Salida de runbook

Los datos que crea alguna actividad que no tienen un vínculo saliente se agregarán a la [salida del runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx). La salida se guarda con el trabajo del runbook y queda a disposición de un runbook principal cuando el runbook se usa como secundario.


## Expresiones de PowerShell

Una de las ventajas de la creación gráfica es que proporciona la capacidad de crear un runbook teniendo mínimos conocimientos de PowerShell. No obstante, actualmente es necesario conocer un poco PowerShell para rellenar determinados [valores de parámetro](#activities) y para configurar [condiciones de vínculo](#links-and-workflow). En esta sección se proporciona una introducción rápida a las expresiones de PowerShell para aquellos usuarios que quizás no estén familiarizados con esta interfaz. En [Scripting con Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx) están disponibles todos los detalles sobre PowerShell.


### Origen de datos de expresiones de PowerShell

Puede usar una expresión de PowerShell como origen de datos para rellenar el valor de un [parámetro de actividad](#activities) con los resultados de algún código de PowerShell. Podría tratarse de una sola línea de código que realizara alguna función simple o varias líneas que desarrollaran cierta lógica compleja. La salida de un comando que no está asignado a una variable se envía al valor del parámetro.

Por ejemplo, el siguiente comando generaría la fecha actual.

	Get-Date

Los siguientes comandos crean una cadena a partir de la fecha actual y la asignan a una variable. Después, el contenido de la variable se envía a la salida.

	$string = "The current date is " + (Get-Date)
	$string

Los siguientes comandos evalúan la fecha actual y devuelven una cadena que indica si el día actual cae en fin de semana o es un día entre semana.

	$date = Get-Date
	if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
	else { "Weekday" }
	
 

### Salida de la actividad

Para usar la salida de una actividad anterior en el runbook, use la variable $ActivityOutput con la siguiente sintaxis.

	$ActivityOutput['Activity Label'].PropertyName

Por ejemplo, puede tener una actividad con una propiedad que requiere el nombre de una máquina virtual, en cuyo caso podría usar la siguiente expresión.

	$ActivityOutput['Get-AzureVm'].Name

Si la propiedad necesitaba el objeto de máquina virtual en lugar de solo una propiedad, se devolvería el objeto completo mediante la siguiente sintaxis.

	$ActivityOutput['Get-AzureVm']

También puede usar la salida de una actividad en una expresión más compleja, como la siguiente, que concatena texto al nombre de la máquina virtual.

	"The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### Condiciones

Utilice [operadores de comparación](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores o determinar si un valor coincide con un patrón especificado. Una comparación devuelve un valor de $true o $false.

Por ejemplo, la siguiente condición determina si la máquina virtual de una actividad denominada *Get-AzureVM* está actualmente *detenida*.

	$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

La siguiente condición comprueba si la misma máquina virtual está en cualquier estado distinto de *detenida*.

	$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Puede combinar varias condiciones, mediante un [operador lógico](https://technet.microsoft.com/library/hh847789.aspx) como **-and** u **-or**. Por ejemplo, la siguiente condición comprueba si la misma máquina virtual del ejemplo anterior se encuentra en un estado de *detenida* o *deteniéndose*.

	($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### Tablas hash

Las [tablas hash](http://technet.microsoft.com/library/hh847780.aspx) son pares de nombre-valor que son útiles para devolver un conjunto de valores. Las propiedades de ciertas actividades pueden esperar una tabla hash en lugar de un valor simple. También puede ver cómo se hace referencia a una tabla hash como un diccionario.

Una tabla hash se crea con la siguiente sintaxis. Una tabla hash puede contener cualquier número de entradas, pero cada una se define mediante un nombre y valor.

	@{ <name> = <value>; [<name> = <value> ] ...}

Por ejemplo, la siguiente expresión crea una tabla hash que se usará en el origen de datos para un parámetro de actividad que espera una tabla hash con valores para una búsqueda en Internet.

	$query = "Azure Automation"
	$count = 10
	$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
	$h

En el ejemplo siguiente se usa la salida de una actividad llamada *Get Twitter Connection* para rellenar una tabla hash.

	@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
	  'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
	  'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
	  'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## Artículos relacionados

- [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md)
- [Recursos de automatización](http://msdn.microsoft.com/library/azure/dn939988.aspx)
- [Operadores](https://technet.microsoft.com/library/hh847732.aspx)
 

<!---HONumber=AcomDC_0128_2016-->