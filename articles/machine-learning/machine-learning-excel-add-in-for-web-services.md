<properties
	pageTitle="Complemento de Excel para los servicios web de Aprendizaje automático | Microsoft Azure"
	description="Uso de los servicios web de Aprendizaje automático de Azure directamente en Excel sin escribir código."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="02/12/2016"
	ms.author="tedway;garye" />

# Complemento de Excel para servicios web de Aprendizaje automático de Azure

Excel facilita la llamada a servicios web directamente sin necesidad de escribir ningún código.

## Pasos para el uso de un servicio web existente en el libro

1. Abra el [archivo de Excel de ejemplo](http://aka.ms/amlexcel-sample-2) que contiene el complemento de Excel y los datos acerca de los pasajeros del Titanic.
2. Elija el servicio web haciendo clic en él, "Predictor de supervivientes del Titanic (complemento de Excel de ejemplo) [puntuación]" en este ejemplo.

    ![Seleccionar un servicio web][01]

3. Esto le llevará a la sección **Predicción**. Este libro ya contiene datos de ejemplo, pero para un libro en blanco también puede seleccionar una celda en Excel y hacer clic en **Usar datos de ejemplo**.
4. Seleccione los datos con encabezados y haga clic en el icono del intervalo de datos de entrada. Asegúrese de que está activada la casilla "Mis datos tienen encabezados".
5. En **Resultado**, escriba el número de la celda donde desea que se muestre el resultado, por ejemplo, "H1" aquí.
6. Haga clic en **Predicción**.

	![Sección Predicción][02]

## Pasos para agregar un nuevo servicio web

1. Publique un servicio web (en [esta página](machine-learning-walkthrough-5-publish-web-service.md) se explica cómo hacerlo) o busque un servicio web existente.
2. En Excel, vaya a la sección **Servicios web** (si se encuentra en la sección **Predicción**, haga clic en la flecha Atrás para ir a la lista de servicios web).

	![Ir a la selección de servicio web][03]

3. Haga clic en **Agregar servicio web**.
4. En el Estudio de aprendizaje automático, haga clic en la sección **SERVICIOS WEB** en el panel izquierdo y, luego, seleccione el servicio web.

	![Seleccionar servicio web de Studio][04]

5. Copie la clave de API del servicio web.

	![Clave de API de Studio][05]

6. Péguela en el cuadro de texto de complemento de Excel denominado **Clave de API**.
7. En la pestaña **PANEL** del servicio web, haga clic en el vínculo **SOLICITUD-RESPUESTA**.
8. Copie la sección **OData Endpoint Address**. Copie la URL y péguela en el cuadro de texto llamado **URL**.
9. Haga clic en **Agregar**.

	![URL y clave de API][06]

10.	Para usar el servicio web, siga las instrucciones anteriores, "Pasos para usar un servicio web existente".

## Compartir el libro

Si guarda el libro, también se guardarán las claves de API para los servicios web que ha agregado. Esto significa que solo debe compartir el libro con personas de confianza.

Haga sus preguntas a continuación o en nuestro [foro](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png

<!---HONumber=AcomDC_0218_2016-->