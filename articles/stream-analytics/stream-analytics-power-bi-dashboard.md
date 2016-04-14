<properties 
	pageTitle="Panel de Power BI en Análisis de transmisiones | Microsoft Azure" 
	description="Utilice un panel de Power BI de streaming en tiempo real para reunir información de inteligencia empresarial y analizar grandes volúmenes de datos procedentes de un trabajo de Análisis de transmisiones." 
	keywords="panel de análisis, panel en tiempo real"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>
	
#  Análisis de transmisiones y Power BI: panel de análisis en tiempo real de flujo de datos

Análisis de transmisiones de Azure permite aprovechar una de las principales herramientas de inteligencia empresarial, Microsoft Power BI. Aprenda a usar Análisis de transmisiones de Azure para analizar grandes volúmenes de datos de streaming y obtener información detallada en un panel de análisis Power BI en tiempo real.

Utilice [Microsoft Power BI](https://powerbi.com/) para crear rápidamente un panel dinámico. [Vea un vídeo que ilustra el escenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

En este artículo, aprenderá a crear sus propias herramientas de inteligencia empresarial personalizadas mediante Power BI como salida para los trabajos de Análisis de transmisiones de Azure y a utilizar un panel en tiempo real.

> [AZURE.NOTE] La salida a Power BI es una característica de vista previa de Análisis de transmisiones de Azure. En este momento, la creación y configuración de salidas de Power BI no se admite en el Portal de vista previa de Azure.

## Requisitos previos

* Cuenta de Microsoft Azure
* Una entrada de la que el trabajo de Análisis de transmisiones pueda consumir datos de streaming. Análisis de transmisiones acepta entradas de Centros de eventos de Azure o de Almacenamiento de blobs de Azure.  
* Cuenta profesional o educativa para Power BI

## Creación de un trabajo de Análisis de transmisiones de Azure

En el [Portal de Azure](https://manage.windowsazure.com), haga clic en **Nuevo, Servicios de datos, Análisis de transmisiones, Creación rápida**.

Especifique los valores siguientes y haga clic en **Creación de un Análisis de transmisiones**:

* **Nombre del trabajo**: escriba un nombre del trabajo. Por ejemplo, **DeviceTemperatures**.
* **Región**: seleccione la región donde desea que esté el trabajo. Considere la posibilidad de colocar el trabajo y el centro de eventos en la misma región para garantizar un rendimiento óptimo y evitar incurrir en costos de transferencia de datos entre regiones.
* **Cuenta de almacenamiento**: elija la cuenta de almacenamiento que desea usar para almacenar los datos de supervisión de todos los trabajos de Análisis de transmisiones que se ejecutan en esta región. Tiene la opción de elegir una cuenta de almacenamiento existente o crear una nueva.

Haga clic en **Análisis de transmisiones** en el panel izquierdo para ver una lista de los trabajos de Análisis de transmisiones.

![graphic1][graphic1]

> [AZURE.TIP] El nuevo trabajo aparecerá con el estado **No iniciado**. Tenga en cuenta que el botón **Iniciar** en la parte inferior de la página está deshabilitado. Este es el comportamiento esperado, ya que debe configurar la entrada, salida y consulta del trabajo, etc. antes de poder iniciar el trabajo.

## Especificación de la entrada del trabajo

Para este tutorial, se supone que está usando el centro de eventos como una entrada con codificación UTF-8 y serialización JSON.

* Haga clic en el nombre del trabajo.
* Haga clic en **Entradas** en la parte superior de la página y luego haga clic en **Agregar entrada**. El cuadro de diálogo que se abre le guiará a través de una serie de pasos para configurar la entrada.
*	Seleccione **Flujo de datos** y, a continuación, haga clic con el botón secundario.
*	Seleccione **Centro de eventos** y, a continuación, haga clic con el botón secundario.
*	Escriba o seleccione los valores siguientes en la tercera página:
  *	**Alias de entrada**: escriba un nombre descriptivo para esta entrada de trabajo. Tenga en cuenta que va a utilizar este nombre en la consulta más adelante.
  * **Centro de eventos**: si el centro de eventos que ha creado está en la misma suscripción que el trabajo de Análisis de transmisiones, seleccione el espacio de nombres en el que está el centro de eventos.
*	Si el centro de eventos está en otra suscripción, seleccione **Usar centro de eventos de otra suscripción** y escriba manualmente la información de **Espacio de nombres de servicio de Bus**, **Nombre de centro de eventos**, **Nombre de directiva de centro de eventos**, **Clave de directiva de centro de eventos** y **Recuento de particiones de centro de eventos**.

> [AZURE.NOTE]	En este ejemplo se utiliza el número predeterminado de particiones, que es 16.

* **Nombre del centro de eventos**: seleccione el nombre del centro de eventos de Azure que tiene.
* **Nombre de directiva de centro de eventos**: seleccione la directiva del centro de eventos para el centro de eventos que está usando. Asegúrese de que esta directiva tiene permisos de administración.
*	**Grupo de consumidores del Centro de eventos** : puede especificar un grupo de consumidores que tiene en el Centro de eventos o dejarlo en blanco. Tenga en cuenta que cada grupo de consumidores de un centro de eventos solo puede tener 5 lectores a la vez. Por tanto, decida el grupo de consumidores adecuado para su trabajo según corresponda. Si deja el campo en blanco, usará el grupo de consumidores predeterminado.

*	Haga clic con el botón secundario.
*	Especifique los siguientes valores:
  *	**Formato del serializador de eventos**: JSON
  *	**Codificación**: UTF8
*	Haga clic en el botón de comprobación para agregar este origen y comprobar que Stream Analytics puede conectarse correctamente al centro de eventos.

## Incorporación del resultado de Power BI

1.  Haga clic en **Salida** en la parte superior de la página y luego haga clic en **Agregar salida**. Verá que Power BI aparece como opción de salida.

    ![graphic2][graphic2]

2.  Seleccione **Power BI** y, a continuación, haga clic con el botón secundario.
3.  Verá una pantalla similar a la siguiente:

    ![graphic3][graphic3]

4.  En este paso, proporcione una cuenta profesional o educativa para la salida del trabajo de Análisis de transmisiones. Si ya tiene una cuenta de Power BI, seleccione **Autorizar ahora**. Si no, elija **Registrarse ahora**. [Aquí hay un buen blog que recorre los detalles de registro de Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]

5.  A continuación verá una pantalla similar a la siguiente:

    ![graphic4][graphic4]

Proporcione valores como sigue:

* **Alias de salida**: puede colocar cualquier alias de salida a la que es fácil que haga referencia. Este alias de salida es especialmente útil si decide tener varias salidas para su trabajo. En ese caso, debe hacer referencia a esta salida de la consulta. Por ejemplo, vamos a usar el valor de alias de salida = "OutPbi".
* **Nombre del conjunto de datos**: proporcione un nombre del conjunto de datos que desea que tenga la salida de Power BI. Por ejemplo, vamos a usar "pbidemo".
*	**Nombre de tabla**: proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Supongamos que lo llamamos "pbidemo". Actualmente, la salida de Power BI de trabajos de Análisis de transmisiones solo puede tener una tabla en un conjunto de datos.

>	[AZURE.NOTE] No debe crear explícitamente este conjunto de datos y esta tabla en su cuenta de Power BI. Se crearán automáticamente cuando empiece su trabajo de Análisis de transmisiones y el trabajo comience a producir salidas en Power BI. Si el trabajo no devuelve resultados, no se creará el conjunto de datos ni la tabla.

*	Haga clic en **Aceptar**, **Probar conexión**; ahora la configuración de la salida ha finalizado.

>	[AZURE.WARNING] Tenga en cuenta asimismo que si Power BI ya cuenta con un conjunto de datos y una tabla con el mismo nombre que el proporcionado en este trabajo de Análisis de transmisiones, se sobrescribirán los datos existentes.


## Escritura de una consulta

Vaya a la pestaña **Consulta** de su trabajo. Escriba la consulta cuya salida desea tener en su Power BI. Por ejemplo, podría ser algo como la siguiente consulta SQL:

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl

    
    
Inicie su trabajo. Valide que el centro de eventos recibe eventos y la consulta genera los resultados esperados. Si la consulta da como resultado 0 filas, las tablas y conjunto de datos de Power BI no se creará automáticamente.

## Creación del panel en Power BI

Vaya a [Powerbi.com](https://powerbi.com) e inicie sesión con su cuenta profesional o educativa. Si la consulta del trabajo de Análisis de transmisiones genera resultados, verá que ya se ha creado el conjunto de datos:

![graphic5][graphic5]

Para crear el panel, vaya a la opción Paneles y cree un nuevo panel.

![graphic6][graphic6]

En este ejemplo vamos a llamarlo "Panel de demostración".

Ahora haga clic en el conjunto de datos creado por el trabajo de Análisis de transmisiones (pbidemo en nuestro ejemplo actual). Se le llevará a una página para crear un gráfico encima de este conjunto de datos. El siguiente solo es un ejemplo de los informes que puede crear:

Seleccione los campos Σ temp y time. Pasarán automáticamente al valor y el eje del gráfico:

![graphic7][graphic7]

De esta forma, obtendrá automáticamente un gráfico como el siguiente:

![graphic8][graphic8]

En la sección de valores, haga clic en la lista desplegable para temp y elija **Media** para la temperatura y, en el gráfico, haga clic en **Visualización** y elija **Gráfico de líneas**:

![graphic9][graphic9]

Ahora obtendrá un gráfico de líneas de la media a lo largo del tiempo. Con la siguiente opción de anclaje, puede anclarlo al panel que creó anteriormente:

![graphic10][graphic10]

Ahora cuando vea el panel con este informe anclado, verá la actualización de informes en tiempo real. Pruebe a cambiar los datos de sus eventos: temperatura máxima o algo similar y verá su efecto en tiempo real reflejado en el panel.

Tenga en cuenta que este tutorial muestra cómo crear un tipo de gráfico para un conjunto de datos. Power BI puede ayudarle a crear otras herramientas de inteligencia empresarial de cliente para su organización. Para obtener otro ejemplo de un panel de Power BI, vea el vídeo [Getting Started with Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s).

Para obtener más información acerca de cómo configurar una salida de Power BI y para utilizar grupos de Power BI, revise la [sección Power BI](stream-analytics-define-outputs.md#power-bi) de [Descripción de salidas de Análisis de transmisiones](stream-analytics-define-outputs.md "Descripción de salidas de Análisis de transmisiones"). Otro recurso útil para obtener más información acerca de la creación de paneles con Power BI es [Vista previa de los paneles de Power BI](http://support.powerbi.com/knowledgebase/articles/424868-dashboards-in-power-bi-preview).

## Limitaciones y prácticas recomendadas

Power BI emplea restricciones tanto de simultaneidad como de rendimiento, tal como se describe aquí: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Precios de Power BI")

Gracias a ello Power BI se hace de forma natural con los casos en los que Análisis de transmisiones de Azure realiza una reducción considerable de la carga de datos. Se recomienda usar TumblingWindow o HoppingWindow para asegurarse de que la inserción de datos sea como máximo de 1 inserción/segundo, y de que la consulta entre dentro de los requisitos de rendimiento. Puede usar la siguiente ecuación para calcular el valor que se debe asignar a la ventana en segundos:
  
![ecuación 1](./media/stream-analytics-power-bi-dashboard/equation1.png)
  
Por ejemplo: si tiene 1.000 dispositivos enviando datos cada segundo, está en Power BI Pro SKU que admite 1.000.000 de filas/hora, y desea obtener la media de datos por dispositivo en Power BI, puede usar como mucho una inserción cada 4 segundos por dispositivo (como se muestra a continuación):
  
![ecuación 2](./media/stream-analytics-power-bi-dashboard/equation2.png)
  
Lo que significa que se cambiaría la consulta original a:

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
    	OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,4),
    	dspl

### Actualización de la vista de Power BI

Una pregunta común es "¿por qué el panel no se actualiza automáticamente en Power BI?".

Para lograr esto, en Power BI use las preguntas y respuestas y formule una pregunta como "valor máximo por valor temporal donde la marca de tiempo es hoy" y ancle ese icono al panel.

### Renovar la autorización

Hay una limitación temporal en la que el token de autenticación debe actualizarse manualmente cada 90 días para todos los trabajos con salida de Power BI. También necesitará volver a autenticar la cuenta de Power BI si su contraseña ha cambiado desde que se creó o autenticó por última vez su trabajo. Un síntoma de este problema es la ausencia de salida de trabajos y un "error de autenticación de usuario" en los registros de operaciones:

![graphic12][graphic12]

Para resolver este problema, detenga su trabajo en ejecución y vaya a la salida de Power BI. Haga clic en el vínculo "Renovar autorización" y reinicie el trabajo desde la hora en que se detuvo por última vez para evitar la pérdida de datos.

![graphic13][graphic13]

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png

<!---HONumber=AcomDC_0204_2016-->