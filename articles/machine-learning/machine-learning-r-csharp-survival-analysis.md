<properties 
	pageTitle="Análisis de supervivencia con Aprendizaje automático de Azure | Microsoft Azure" 
	description="Probabilidad de aparición de eventos de análisis de supervivencia" 
	services="machine-learning" 
	documentationCenter="" 
	authors="zhangya" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2016" 
	ms.author="zhangya"/>


#Análisis de supervivencia 

En muchos escenarios, el resultado principal de la evaluación es el momento en que se producirá un evento de interés. En otras palabras, se plantea la pregunta "¿cuándo se producirá este evento?". Como ejemplos, tenga en cuenta las situaciones donde los datos describen el tiempo transcurrido (días, años, kilometraje, etc.) hasta que el evento de interés (recaída de la enfermedad, grado de doctorado recibido o pastillas de freno estropeadas) se produce. Cada instancia de los datos representa un objeto específico (un paciente, un alumno, un automóvil, etc.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [servicio web](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) responde a la pregunta "¿qué probabilidad hay de que el evento de interés se produzca en el momento n para el objeto x?". Al proporcionar un modelo de análisis de supervivencia, este servicio web permite a los usuarios proporcionar datos para entrenar el modelo y probarlo. El tema principal del experimento es modelar la duración del tiempo que transcurre hasta que se produzca el evento de interés.

>Este servicio web puede ser consumido por los usuarios; posiblemente a través de una aplicación móvil, a través de un sitio web o incluso en un equipo local, por ejemplo. Pero el objetivo del servicio web también es actuar como un ejemplo de cómo se puede usar Aprendizaje automático de Azure para crear servicios web encima del código R. Con tan solo unas líneas de código R y algunos clics en un botón en Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan usar usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.

##Consumo del servicio web

El esquema de datos de entrada del servicio web se muestra en la tabla siguiente. Se necesitan seis elementos de información como entrada: los datos de aprendizaje, los datos de prueba, el tiempo de interés, el índice de la dimensión de "tiempo", el índice de la dimensión de "evento" y los tipos de variables (continua o factor). Los datos de aprendizaje se representan con una cadena, donde las filas están separadas por comas y las columnas están separadas por punto y coma. El número de características de los datos es flexible. Todos los elementos de la cadena de entrada deben ser numéricos. En los datos de aprendizaje, la dimensión de "tiempo" indica que el número de unidades de tiempo (días, años, kilometraje, etc.) transcurrido desde el inicio del estudio (un paciente recibe los programas de tratamiento contra el consumo de drogas, un alumno que empieza el doctorado, un automóvil que empieza a conducirse, etc.) hasta que se produce el evento de interés (el paciente vuelve a consumir droga, el alumno obtiene el título de doctorado, el vehículo presenta desgaste de las pastillas de freno, etc.). La dimensión de "evento" indica si se produce el evento de interés al final del estudio. El valor "evento = 1" significa que se produce el evento de interés en el momento indicado por la dimensión de "tiempo"; mientras que "evento = 0" significa que el evento de interés no se ha producido todavía en el momento indicado por la dimensión de "tiempo".

- trainingdata: una cadena de caracteres Las filas están separadas por coma y las columnas están separadas por punto y coma. Cada fila incluye la dimensión de "tiempo", la dimensión de "evento" y las variables del previsor.
- testingdata: una fila de datos que contiene variables del previsor para un objeto determinado.
- time\_of\_interest: el tiempo transcurrido de interés n.
- index\_time: el índice de columna de la dimensión de "tiempo" (a partir de 1)
- index\_event: el índice de columna de la dimensión de "evento" (a partir de 1)
- variable\_types: una cadena de caracteres con punto y coma como separadores. 0 representa variables continuas y 1 representa variables factor.


El resultado es la probabilidad de que un evento se produzca en un momento determinado.

>Este servicio cuando está hospedado en Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET.

Hay varias maneras de consumir el servicio de forma automática ([aquí](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx) se puede ver una aplicación de ejemplo).

###Inicio del código C# para el uso del servicio web:
	public class Input
	{
	        public string trainingdata;
	        public string testingdata;
	        public string timeofinterest;
	        public string indextime;
	        public string indexevent;
	        public string variabletypes;
	}

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}




Esta prueba se interpreta de la siguiente forma. Supongamos que el objetivo de los datos es modelar el tiempo transcurrido hasta que los pacientes sometidos a algunos de los dos programas de tratamiento contra las drogas vuelven a consumir drogas. El resultado del servicio web es: para pacientes de 35 años, que se han sometido dos veces a un tratamiento anterior contra el consumo de drogas, con un largo programa de tratamiento residencial y que consumían heroína y cocaína, la probabilidad de volver a consumir drogas es del 95,64 % el día 500.

##Creación del servicio web

>Este servicio web se ha creado con el Aprendizaje automático de Azure. Para obtener acceso a una evaluación gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.

En Aprendizaje automático de Azure se creó un nuevo experimento en blanco y se extrajeron dos módulos [Ejecutar scripts R][execute-r-script] en el área de trabajo. El esquema de datos se creó con un módulo [Ejecutar script R][execute-r-script] sencillo, que define el esquema de datos de entrada para el servicio web. Este módulo está vinculado al segundo módulo [Ejecutar script R][execute-r-script], que hace la mayor parte del trabajo. Este módulo realiza el preprocesamiento de datos, la creación de modelos y las previsiones. En el paso del preprocesamiento de los datos, los datos de entrada representados por una cadena larga se transforman y convierten en una trama de datos. En el paso de creación del modelo, un paquete R externo "survival\_2.37 7.zip" se instala en primer lugar para llevar a cabo el análisis de supervivencia. A continuación, se ejecuta la función "coxph" después de una serie de tareas de procesamiento de datos. Se pueden leer los detalles de la función "coxph" para el análisis de supervivencia en la documentación de R. En el paso de previsión, se proporciona una instancia de prueba en el modelo de entrenado con la función "surfit", y la curva de supervivencia para esta instancia de prueba se genera como variable "curva". Por último, se obtiene la probabilidad del tiempo de interés.

###Flujo de experimento:

![flujo de experimento][1]

####Módulo 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Módulo 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Limitaciones

Este servicio web solo puede aceptar valores numéricos como variables de característica (columnas). La columna "evento" solo puede aceptar el valor 0 o 1. La columna "tiempo" debe ser un entero positivo.

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Azure Marketplace, haga clic [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0302_2016-->