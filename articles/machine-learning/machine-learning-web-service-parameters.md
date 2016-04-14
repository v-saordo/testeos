<properties 
	pageTitle="Uso de parámetros de servicio web de Aprendizaje automático de Azure | Microsoft Azure" 
	description="Cómo utilizar parámetros de servicio web de Aprendizaje automático de Azure para modificar el comportamiento de su modelo cuando se tiene acceso al servicio web." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="raymondl;garye"/>

#Usar parámetros de servicio web de Aprendizaje automático de Azure

Se crea un servicio web de Aprendizaje automático de Azure mediante la publicación de un experimento que contiene módulos con parámetros configurables. En algunos casos, puede que desee cambiar el comportamiento del módulo mientras se está ejecutando el servicio web. Los *parámetros del servicio web* le permiten hacer esto.

Un ejemplo común es la configuración del módulo [Lector][reader] para que el usuario del servicio web publicado pueda especificar un origen de datos diferente al acceder al servicio web. También puede configurar el módulo [Escritor][writer] para que se pueda especificar un destino diferente. Algunos otros ejemplos incluyen cambiar el número de bits del [hash de características][feature-hashing] o el número de características deseadas para el módulo [Selección de características basada en filtros][filter-based-feature-selection] módulo.

Puede definir parámetros de servicio web y asociarlos con uno o más parámetros de módulo en el experimento, y puede especificar si son obligatorios u opcionales. El usuario del servicio web puede entonces proporcionar valores para estos parámetros cuando llama el servicio web.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Cómo establecer y utilizar los parámetros de servicio web

Para definir un parámetro de servicio web, haga clic en el icono situado junto al parámetro de un módulo y seleccione "Establecer como parámetro del servicio web". Esto crea un nuevo parámetro de servicio web y se conecta a ese parámetro de módulo. A continuación, cuando se obtiene acceso al servicio web, el usuario puede especificar un valor para el parámetro del servicio web y se aplicará al parámetro del módulo.

Una vez que defina un parámetro de servicio web, está disponible para cualquier otro parámetro de módulo en el experimento. Si define un parámetro del servicio web asociado a un parámetro para un módulo, puede usar ese mismo parámetro del servicio web para cualquier otro módulo, siempre que el parámetro espere el mismo tipo de valor. Por ejemplo, si el parámetro del servicio web es un valor numérico, entonces solo se puede usar para parámetros de módulo que esperan un valor numérico. Cuando el usuario establece un valor para el parámetro del servicio web, se aplicará a todos los parámetros de módulo asociado.

Puede decidir si se debe proporcionar un valor predeterminado para el parámetro del servicio web. Si lo hace, el parámetro es opcional para el usuario del servicio web. Si no proporciona un valor predeterminado, el usuario tiene que especificar un valor al que se tiene acceso al servicio web.

La documentación para el servicio web (mediante el vínculo **Página de Ayuda de API** en el servicio web **PANEL** en el Estudio de aprendizaje automático) incluirá información para el usuario del servicio web acerca de cómo especificar el parámetro del servicio web mediante programación al obtener acceso al servicio web.


##Ejemplo

Por ejemplo, supongamos que tenemos un experimento con un módulo de [Escritor][writer] que envía información al almacenamiento de blobs de Azure. Definiremos un parámetro del servicio web denominado "Ruta de acceso de Blob" que permite al usuario del servicio web cambiar la ruta de acceso al almacenamiento de blobs cuando se tenga acceso al servicio.

1.	En el Estudio de aprendizaje automático, haga clic en el módulo [Escritor][writer] para seleccionarlo. Sus propiedades se muestran en el panel Propiedades a la derecha del lienzo del experimento.

2.	Especifique el tipo de almacenamiento:

    - En **Especifique el destino de los datos**, seleccione "Almacenamiento de blobs de Azure".
    - En **Especifique el tipo de autenticación**, seleccione "Cuenta".
    - Escriba la información de cuenta para el almacenamiento de blobs de Azure. 
    <p />

3.	Haga clic en el icono situado a la derecha de la **ruta de acceso que comienza con el parámetro de contenedor de blobs**. Su aspecto es similar a este:

	![Icono de parámetro del servicio web][icon]

    Seleccione "Establecer como parámetro del servicio web".

    Se agregará una entrada en **Parámetros del servicio web** en la parte inferior del panel Propiedades con el nombre "Ruta de acceso que comienza con el contenedor de blobs". Este es el parámetro de servicio web que está ahora asociado con este parámetro de módulo [Escritor][writer].

4.	Para cambiar el nombre del parámetro del servicio web, haga clic en el nombre, escriba "Ruta de acceso de blobs" y presione la tecla **Intro**.
 
5.	Para proporcionar un valor predeterminado para el parámetro del servicio web, haga clic en el icono a la derecha del nombre, seleccione "Proporcionar valor predeterminado", escriba un valor (por ejemplo, "container1/output1.csv") y presione la tecla **Intro**.

	![Parámetro del servicio web][parameter]

6.	Haga clic en **Ejecutar**.

7.	Haga clic en **PUBLICAR SERVICIO WEB** para publicar el servicio web.

El usuario del servicio web ahora puede especificar un nuevo destino para el módulo [Escritor][writer] al obtener acceso al servicio web.

##Más información

Para obtener un ejemplo más detallado, vea la entrada [Parámetros del servicio web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) en el [Blog de Aprendizaje automático](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para más información sobre el acceso a un servicio web de Aprendizaje automático, vea [Cómo consumir un servicio web de Aprendizaje automático publicado](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=AcomDC_0211_2016-->