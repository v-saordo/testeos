<properties
	pageTitle="Administración de iteraciones de experimentos en Estudio de aprendizaje automático | Microsoft Azure"
	description="Cómo administrar iteraciones de experimentos en Estudio de aprendizaje automático de Azure"
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

# Administrar iteraciones de experimentos en Estudio de aprendizaje automático de Azure

El desarrollo de un modelo de análisis predictivo es un proceso iterativo: a medida que se modifican las diversas funciones y los parámetros de su experimento, sus resultados convergen hasta que esté satisfecho con un modelo entrenado y efectivo. La clave de este proceso es realizar un seguimiento de las iteraciones de los parámetros de su experimento y sus configuraciones.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Puede revisar las ejecuciones anteriores de sus experimentos en cualquier momento con el fin de cuestionar, volver a plantear y, en última instancia, confirmar o refinar suposiciones anteriores. Cuando se ejecuta un experimento, el Estudio de aprendizaje automático conserva un historial de la ejecución, incluidos el conjunto de datos, el módulo y las conexiones y los parámetros de puertos. Este historial también captura los resultados, información de tiempo de ejecución (como el inicio y las detenciones), los mensajes de registro y el estado de ejecución. Puede volver atrás en cualquiera de estas ejecuciones en cualquier momento para revisar la cronología de su experimento y los resultados intermedios. Incluso puede usar una ejecución anterior de su experimento para iniciar en una nueva fase de consulta y detección en su ruta de acceso para la creación de soluciones simples, complejas o incluso de modelado de conjuntos.

> [AZURE.NOTE] Al ver una ejecución anterior de un experimento, esa versión del experimento está bloqueada y no se puede editar. Sin embargo, puede guardar una copia haciendo clic en **GUARDAR COMO** y proporcionar un nombre nuevo para la copia. Estudio de aprendizaje automático abrirá la nueva copia, que podrá editar y ejecutar. Esta copia del experimento está disponible en la lista **EXPERIMENTOS** junto con los demás experimentos.

## Ver la ejecución previa

Al abrir un experimento que ha ejecutado al menos una vez, puede ver la ejecución anterior del experimento haciendo clic en **Ejecución anterior** en el panel Propiedades.

Por ejemplo, suponga que crea un experimento y ejecuta versiones de este a las 11:23, 11:42 y 11:55. Si abre la última ejecución del experimento (11:55) y hace clic en **Ejecución anterior**, se abrirá la versión que se ejecutó a las 11:42.

## Ver el historial de ejecuciones

Puede ver todas las ejecuciones anteriores de un experimento haciendo clic en **Ver historial de ejecución** en un experimento abierto.

Por ejemplo, suponga que crea un experimento con el [regresión lineal][linear-regression] módulo y desea observar el efecto de cambiar el valor de **velocidad de aprendizaje** en los resultados del experimento. Ejecute el experimento varias veces con distintos valores para este parámetro, de la siguiente forma:

| Valor de velocidad de aprendizaje | Hora de inicio de la ejecución |
| ------------------- | -------------- |
| 0,1 | 9/11/2014 4:18:58 PM
| 0,2 | 9/11/2014 4:24:33 PM
| 0,4 | 9/11/2014 4:28:36 PM
| 0,5 | 9/11/2014 4:33:31 PM

Si hace clic en **VER HISTORIAL DE EJECUCIONES**, verá una lista de todas estas ejecuciones:

![Historial de ejecución de ejemplo][runhistory]

Haga clic en cualquiera de estas ejecuciones para ver una instantánea del experimento en el momento en que se ejecutó. La configuración, los valores de parámetro, los comentarios y los resultados se conservan para darle un registro completo de esa ejecución del experimento.

> [AZURE.TIP] Para documentar las iteraciones del experimento, puede modificar el título cada vez que lo ejecuta, puede actualizar el **Resumen** del experimento en el panel de propiedades y puede agregar o actualizar comentarios en módulos individuales para registrar los cambios. El título, el resumen y los comentarios del módulo se guardan con cada ejecución del experimento.

La lista de experimentos de la pestaña **EXPERIMENTOS** de Estudio de aprendizaje automático muestra siempre la versión más reciente de un experimento. Si abre una ejecución anterior del experimento (mediante **Ejecución anterior** o **VER HISTORIAL DE EJECUCIÓN**), puede volver a la versión de borrador haciendo clic en **VER HISTORIAL DE EJECUCIÓN** y seleccionando la iteración que con un **ESTADO** **Modificable**.

## Iterar en una ejecución anterior

Al hacer clic en **Ejecución anterior** o en **VER HISTORIAL DE EJECUCIÓN**, puede ver un experimento terminado en modo de solo lectura.

Si desea iniciar una iteración del experimento a partir de la configuración de una ejecución anterior, puede hacerlo abriendo la ejecución y haciendo clic en **GUARDAR COMO**. Esto crea un nuevo experimento, con un título nuevo, un historial de ejecución vacío y todos los componentes y valores de parámetros de la ejecución anterior. Este nuevo experimento aparece en la pestaña **EXPERIMENTOS** en la página principal de Estudio de aprendizaje automático, y puede modificarlo y ejecutarlo iniciando un nuevo historial de ejecución para esta iteración del experimento.

Por ejemplo, suponga que el historial de ejecución del experimento se muestra en la sección anterior. Desea observar lo que sucede cuando establece el parámetro de **Velocidad de aprendizaje** en 0,4 y probar distintos valores para el parámetro **Número de tiempos de formación**.


1. Haga clic en **VER HISTORIAL DE EJECUCIÓN** y abra la iteración del experimento que ejecutó a las 4:28:36 pm (en la que estableció el valor del parámetro en 0,4).

2. Haga clic en **GUARDAR COMO**.

3. Escriba un título nuevo y active en la casilla **Aceptar**. Se creará una nueva copia del experimento.

4. Modifique el parámetro **Número de tiempos de formación**.

5. Haga clic en **EJECUTAR**.

Ahora puede continuar para modificar y ejecutar esta versión del experimento, creando un historial de ejecución nuevo para registrar su trabajo.


<!-- Images -->
[runhistory]: ./media/machine-learning-manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/

<!---HONumber=AcomDC_0204_2016-->