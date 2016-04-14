<properties
	pageTitle="Consumo de un servicio web de Aprendizaje automático de Excel |Microsoft Azure"
	description="Consumir un servicio web de Aprendizaje automático de Azure de Excel"
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="tedway"/>


# Consumo de un servicio web de Aprendizaje automático de Azure de Excel

 Estudio de aprendizaje automático de Azure facilita la llamada a servicios web directamente desde Excel sin necesidad de escribir ningún código.

 Si está ejecutando Excel 2013 (o posterior), o puede guardar un archivo de OneDrive o de SharePoint para su uso con Excel en línea, se recomienda el [complemento Excel](machine-learning-excel-add-in-for-web-services.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Pasos

1. Publicar un servicio web. En [esta página](machine-learning-walkthrough-5-publish-web-service.md) se explica cómo hacerlo. Actualmente, la función de libro de Excel solo se admite para los servicios de solicitud/respuesta que tienen una salida única (es decir, una etiqueta de puntuación única).

2. Una vez que tenga un servicio web, haga clic en la sección **SERVICIOS WEB** que se encuentra a la izquierda del estudio y, luego, seleccione el servicio web que se va a usar desde Excel.

3. En la pestaña **PANEL** del servicio web se encuentra una fila para el servicio **SOLICITUD/RESPUESTA**. Si este servicio tenía una salida única, deberá consultar el vínculo **Descargar el libro de Excel** de esa fila.

	![][1]

4. Haga clic en **Descargar el libro de Excel** y ábralo en Excel.

5. Aparecerá una advertencia de seguridad; haga clic en el botón **Habilitar edición**.

	![][2]

6. Aparecerá una advertencia de seguridad. Haga clic en el botón **Habilitar contenido** para ejecutar macros en la hoja de cálculo.

	![][3]

7. Una vez que las macros están habilitadas, se generará una tabla. Las columnas en azul son necesarias como entrada al servicio web RRS, o como **PARÁMETROS**. Tenga en cuenta la salida del servicio RRS, los **VALORES PREDICHOS** en verde. Cuando se llenan todas las columnas de una fila determinada, el libro llama a la API de puntuación automáticamente y muestra los resultados con puntuación.

	![][4]

7. Para puntuar más de una fila, rellene la segunda fila con datos y se generarán los valores predichos. Incluso puede pegar varias filas a la vez.

8. Ahora utilice cualquiera de las funciones de Excel (gráficos, asignación de energía, formato condicional, etc.) con los valores de predicción.


## Compartir el libro

Para que las macros funcionen, la CLAVE DE ACCESO debe formar parte de la hoja de cálculo. Esto significa que debe compartir el libro con las entidades e individuos de su confianza.

## Actualizaciones automáticas

En las siguientes dos situaciones se realiza una llamada a RRS:

1. La primera vez que una fila tiene contenido en todos sus **PARÁMETROS**

2. Cada vez que cualquiera de los **PARÁMETROS** cambia en una fila en la que se habían especificado todos sus **PARÁMETROS**.

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png

<!---HONumber=AcomDC_0218_2016-->