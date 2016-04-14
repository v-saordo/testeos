<properties 
	pageTitle="Tutorial: análisis de opiniones mediante Análisis de transmisiones de Azure y Aprendizaje automático de Azure | Microsoft Azure" 
	description="Cómo aprovechar las funciones definidas por el usuario y Aprendizaje automático en los trabajos de Análisis de transmisiones"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"
/>

# Tutorial: realizar análisis de opiniones mediante Análisis de transmisiones y Aprendizaje automático #

Este tutorial está pensado para ayudarle a configurar rápidamente un trabajo sencillo de Análisis de transmisiones con la integración de Aprendizaje automático. Aprovecharemos un modelo de Aprendizaje automático de análisis de opinión de la galería de Cortana Analytics para analizar datos de texto de transmisión y determinar la puntuación de opinión en tiempo real. Es un tutorial excelente para entender escenarios como el análisis de opinión en tiempo real al transmitir datos de Twitter, análisis del registro de chats de clientes con el personal de soporte técnico, comentarios en foros, blogs y vídeos y muchos otros escenarios de puntuación predictiva en tiempo real.
  
En este tutorial se incluye un archivo CSV de ejemplo con texto (como se muestra en la ilustración 1 a continuación) como entrada del almacén de blobs de Azure. El trabajo aplicará el modelo de análisis de opinión como una función definida por el usuario (UDF) en los datos de texto de ejemplo desde el almacén de blobs. El resultado final se colocará en el mismo almacén de blobs de Azure en otro archivo CSV. En la lustración 2 más abajo se muestra un diagrama de esta configuración. Para un escenario más realista, esta entrada del almacén de blobs puede sustituirse por datos de Twitter de transmisión desde una entrada del Centro de eventos de Azure. Además, se puede crear la visualización en tiempo real de [Power BI](https://powerbi.microsoft.com/) de la opinión agregada. Las futuras iteraciones de este artículo incluirán dichas extensiones.

Ilustración 1:

![tutorial de aprendizaje automático de análisis de transmisiones - ilustración 1](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

Ilustración 2:

![tutorial de aprendizaje automático de análisis de transmisiones - ilustración 2](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

## Requisitos previos

Los requisitos previos de este artículo son los siguientes:

1.	Una suscripción de Azure activa
2.	Un archivo CSV con algunos datos. El de la figura 2 se ofrece [en GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) para su descarga, o bien puede crear los suyos propios. Este tutorial se ha escrito asumiendo que se usa el que se ha proporcionado para la descarga.

A nivel general, se llevarán a cabo los pasos siguientes:

1.	Carga del archivo de entrada CSV en Almacenamiento de blobs
2.	Adición de un modelo de análisis de opinión de la galería de Cortana Analytics al área de trabajo de Aprendizaje automático
3.	Implementación de este modelo como un servicio web dentro del área de trabajo de Aprendizaje automático de Azure
4.	Creación de un trabajo de Análisis de transmisiones que llama a este servicio web como una función para determinar la opinión sobre la entrada de texto
5.	Inicio del trabajo de Análisis de transmisiones y observación de la salida 


## Carga del archivo de entrada CSV en Almacenamiento de blobs

En este paso puede usar cualquier archivo CSV, incluso el especificado en la introducción. Para cargar el archivo, se puede usar [Explorador de almacenamiento de Azure](http://storageexplorer.com/) o Visual Studio, así como código personalizado. En este tutorial se proporcionan ejemplos para Visual Studio.

1.	Expanda Azure y haga clic con el botón secundario en el **Almacenamiento**. Elija **Asociar almacenamiento externo** y ofrezca un valor para **Nombre de cuenta** y **Clave de cuenta**.  

    ![tutorial de aprendizaje automático de análisis de transmisiones - explorador de servidores](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Expanda el almacenamiento que acaba de asociar y elija **Crear contenedor de blobs** y proporcione un nombre lógico. Una vez creado, haga doble clic en el contenedor para ver su contenido (que estará vacío en este momento).

    ![tutorial de aprendizaje automático de análisis de transmisiones - crear blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Para cargar el archivo CSV, haga clic en el icono **Cargar blob** y elija el **archivo desde el disco local**.

## Adición del modelo de análisis de opinión desde la galería de Cortana Analytics

1.	Descargue el [modelo de análisis de opinión predictivo](https://gallery.cortanaanalytics.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) desde la galería de Cortana Analytics.  
2.	Haga clic en **Abrir** en Studio:  

    ![tutorial de aprendizaje automático de análisis de transmisiones - abrir studio de aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Inicie sesión para acceder al área de trabajo. Elija la ubicación que mejor se adapte a la suya.
4.	Ahora haga clic en **Ejecutar** en la parte inferior de Studio.  
5.	Cuando se ejecuta correctamente, haga clic en **Implementar servicio web**.
6.	El modelo de análisis de opinión ya está listo para su uso. Para validarlo, haga clic en el botón **Probar** y ofrezca una entrada de texto como "Me encanta Microsoft" y la prueba debe devolver un resultado similar al que se muestra a continuación:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![tutorial de aprendizaje automático de análisis de transmisiones - datos de análisis](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Haga clic en el vínculo del libro **Excel 2010 o anterior** para obtener la clave de API y la dirección URL que necesitará después para configurar el trabajo de Análisis de transmisiones. (Este paso solo es necesario para aprovechar un modelo de aprendizaje automático desde el área de trabajo de otra cuenta de Azure. Este tutorial asume que es el caso que se va a abordar en este escenario)

![tutorial de aprendizaje automático de análisis de transmisiones - experimento de análisis](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

Tome nota de la clave de acceso y de la dirección URL de servicio web desde el Excel descargado, tal como se muestra a continuación:

![tutorial de aprendizaje automático de análisis de transmisiones - vista rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Creación de un trabajo de Análisis de transmisiones que usa el modelo de Aprendizaje automático

1.	Vaya al [Portal de administración de Azure](https://manage.windowsazure.com).  
2.	Haga clic en **Nuevo**, **Servicios de datos**, **Análisis de transmisiones** y **Creación rápida**. Ofrezca un valor en **Nombre del trabajo**, un valor para la **Región** apropiada al trabajo y elija una **Cuenta de almacenamiento de supervisión regional**.    
3.	Una vez creado el trabajo, vaya a la pestaña **Entradas** y haga clic en **Agregar entrada**.  

    ![tutorial de aprendizaje automático de análisis de transmisiones - entrada de aprendizaje automático de adición de datos](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	En la primera página de la ventana del asistente **Agregar entrada**, seleccione **Flujo de datos** y haga clic en Siguiente. En la segunda página, seleccione **Almacenamiento de blobs** como entrada y haga clic en **Siguiente**.
5.	En la página del asistente **Configuración del almacenamiento de blobs** del asistente, proporcione el nombre del contenedor de blobs de la cuenta de almacenamiento definida anteriormente cuando se cargaron los datos. Haga clic en **Siguiente**. Elija **CSV** como **Formato de serialización de eventos**. Acepte los valores predeterminados para el resto de la **Configuración de serialización**. Haga clic en **Aceptar**.  
6.	Desplácese hasta la pestaña **Salidas** y haga clic en **Agregar una salida**.  

    ![tutorial de aprendizaje automático de análisis de transmisiones - agregar salida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Elija **Almacenamiento de blobs** y proporcione los mismos parámetros excepto el contenedor. El valor **Entrada** se configuró para leerse desde el contenedor denominado "test", donde se cargó el archivo **CSV**. Como valor de **Salida**, use "testoutput". Los nombres del contenedor deben diferentes, y compruebe que existe este contenedor.
8.	Haga clic en **Siguiente** para definir la **Configuración de serialización** de la salida. Al igual que en la entrada, elija **CSV** y haga clic en el botón **Aceptar**.
9.	Vaya a la pestaña **Funciones** y haga clic en **Agregar una función de aprendizaje automático**.  

    ![tutorial de aprendizaje automático de análisis de transmisiones - agregar función de aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	En la página **Configuración de servicio web de Aprendizaje automático**, busque el área de trabajo de Aprendizaje automático, el servicio web y el punto de conexión predeterminado. Para este tutorial, aplique la configuración manualmente para familiarizarse con la configuración de un servicio web para cualquier área de trabajo, siempre y cuando conozca la dirección URL y tenga la clave. Proporcione la dirección **URL** y la **clave de API** del punto de conexión. y, a continuación, haga clic en **Aceptar**.

    ![tutorial de aprendizaje automático de análisis de transmisiones - servicio web de aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Vaya a la pestaña **Consulta** y modifique la consulta de la manera siguiente:

```
	WITH subquery AS (  
		SELECT text, sentiment(text) as result from input  
	)  
	  
	Select text, result.[Score]  
	Into output  
	From subquery  
```

12. Haga clic en **Guardar** para guardar la consulta.    

## Inicio del trabajo de Análisis de transmisiones y observación de la salida

1.	Haga clic en **Iniciar** en la parte inferior del trabajo. 
2.	En el **cuadro de diálogo Iniciar consulta**, elija **Hora personalizada** y seleccione una hora antes de cuando se cargó el archivo CSV en Almacenamiento de blobs. Haga clic en **Aceptar**.  

    ![tutorial de aprendizaje automático de análisis de transmisiones - hora personalizada](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Desplácese hasta Almacenamiento de blobs mediante la herramienta usada cuando se cargó el archivo CSV. Este tutorial utilizó Visual Studio.
4.	Pocos minutos después de iniciado el trabajo, se crea el contenedor de salida y se carga en él un archivo CSV.  
5.	Si se hace doble clic en el archivo, se abrirá el editor de CSV predeterminado, que debe mostrar algo como lo siguiente:  

    ![tutorial de aprendizaje automático de análisis de transmisiones - vista de csv](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusión

En este tutorial, se creó un trabajo de Análisis de transmisiones que lee datos de texto de trasmisiones y aplica el análisis de opinión en tiempo real. Puede hacerlo sin tener que preocuparse por las complejidades de la creación de un modelo de análisis de opinión. Esta es una de las ventajas de Cortana Analytics Suite.

También se pueden observar las métricas relacionadas con funciones de Aprendizaje automático de Azure. Haga clic en la pestaña **MONITOR**. Se muestran tres métricas relacionadas con funciones.
  
- SOLICITUDES DE FUNCIÓN indica el número de solicitudes en el servicio web de Aprendizaje automático.  
- EVENTOS DE FUNCIÓN indica el número de eventos de la solicitud. De forma predeterminada, cada solicitud de servicio web de Aprendizaje automático contiene hasta 1000 eventos.  
- SOLICITUDES DE FUNCIÓN CON ERROR indica el número de solicitudes con error en el servicio web de Aprendizaje automático.  

    ![tutorial de aprendizaje automático de análisis de transmisiones - vista de monitor de aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0204_2016-->