<properties 
	pageTitle="Creación de extremos de servicio web en Aprendizaje automático de Microsoft Azure" 
	description="Creación de extremos de servicio web en Aprendizaje automático de Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/10/2016"
	ms.author="himad"/>


# Creación de extremos

Aprendizaje automático de Azure le permite crear varios extremos para un servicio web publicado. Cada extremo se trata, limita y administra individualmente, independientemente de los otros extremos de ese servicio web. Hay una clave de autorización y una dirección URL única para cada extremo.

Esto permite a los usuarios de Aprendizaje automático de Azure crear servicios web que, a continuación, pueden vender a sus clientes. Cada extremo puede personalizarse individualmente con sus propios modelos entrenados mientras continúa vinculado al experimento que creó este servicio web. Además, las actualizaciones para el experimento pueden aplicarse de manera selectiva a un extremo sin sobrescribir las personalizaciones.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Pasos de creación de extremos
- Abra [http://manage.windowsazure.com](http://manage.windowsazure.com) y haga clic en **Aprendizaje automático** en la columna izquierda. Haga clic en el área de trabajo que tenga el servicio web en el que está interesado.![Navegar a área de trabajo](./media/machine-learning-create-endpoint/figure-1.png)


- Haga clic en la pestaña **Servicios web**.![Navegar a servicios web](./media/machine-learning-create-endpoint/figure-2.png)


- Haga clic en el servicio web en el que esté interesado para ver la lista de extremos disponibles.![Navegar a extremo](./media/machine-learning-create-endpoint/figure-3.png)


- Haga clic en el botón de **Agregar extremo** en la parte inferior. Introduzca un nombre y una descripción, asegúrese de que no hay ningún otro extremo con el mismo nombre eneste servicio web. Deje el nivel de limitación con su valor predeterminado, a menos que tenga requisitos especiales. Para obtener más información acerca de las limitaciones, consulte [Escalado de extremos de API](machine-learning-scaling-endpoints.md).![Crear extremo](./media/machine-learning-create-endpoint/figure-4.png)


Una vez creado el extremo, puede consumirlo a través de API sincrónicas, API de lotes y hojas de cálculo de Excel. Además de agregar extremos a través de esta interfaz de usuario, también puede usar las API de administración de extremos para agregar extremos mediante programación. Para obtener más información sobre el uso de servicios web de Aprendizaje automático, consulte [Cómo consumir un servicio web de Aprendizaje automático de Azure publicado](machine-learning-consume-web-services.md).
 

<!---HONumber=AcomDC_0211_2016-->