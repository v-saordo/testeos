<properties 
	pageTitle="Conjunto de distribución binomial | Microsoft Azure" 
	description="Conjunto de distribución binomial" 
	services="machine-learning" 
	documentationCenter="" 
	authors="ireiter" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2016" 
	ms.author="ireiter"/>


#Conjunto de distribución binomial




El conjunto de distribución binomial es una serie de servicios web de ejemplo ([Generador binomial](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Calculadora de probabilidad](https://datamarket.azure.com/dataset/aml_labs/bdp4), [Calculadora de cuantil](https://datamarket.azure.com/dataset/aml_labs/bdq5)) que ayudan a generar y administrar distribuciones binomiales. Los servicios permiten generar una secuencia de distribución binomial de cualquier longitud, calcular los cuantiles de una probabilidad determinada y calcular la probabilidad a partir de un cuantil específico. Cada uno de los servicios genera salidas diferentes en función del servicio seleccionado (consulte la siguiente descripción). El conjunto de distribución binomial se basa en funciones qbinom, rbinom y pbinom de R que se incluyen en el paquete de estadísticas de R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Los servicio web pueden ser consumidos por los usuarios, bien directamente en marketplace, a través de una aplicación móvil, a través de un sitio web o incluso en un equipo local, por ejemplo. Pero el objetivo del servicio web también es actuar como un ejemplo de cómo se puede usar Aprendizaje automático de Azure para crear servicios web encima del código R. Con tan solo unas líneas de código R y algunos clics en un botón en Estudio de aprendizaje automático de Microsoft Azure, puede crear un experimento con código R y publicarlo como servicio web. A continuación, el servicio web se puede publicar en Azure Marketplace para que lo puedan consumir usuarios y dispositivos en todo el mundo sin necesidad de que el autor del servicio web configure la infraestructura.

##Consumo del servicio web
El conjunto de distribución binomial incluye los tres servicios siguientes.

###Calculadora de cuantil para la distribución binomial
Este servicio acepta 4 argumentos de una distribución normal y calcula el cuantil asociado. Los argumentos de entrada son:

- p: una única probabilidad agregada de varias pruebas.  
- tamaño: el número de pruebas.
- probabilidad: la probabilidad de éxito en una prueba.
- Lado: L para la parte inferior de la distribución, U para la parte superior de la distribución. 

La salida del servicio es el cuantil calculado asociado con la probabilidad dada.

###Calculadora de probabilidad para la distribución binomial
Este servicio acepta 4 argumentos de una distribución binomial y calcula la probabilidad asociada. Los argumentos de entrada son:

- q: un único cuantil de un evento con distribución binomial. 
- tamaño: el número de pruebas.
- probabilidad: la probabilidad de éxito en una prueba.
- Lado: L para la parte inferior de la distribución, U para la parte superior de la distribución o E que es igual a un único número de éxitos.

La salida del servicio es la probabilidad calculada asociada con el cuantil dado.

###Generador de distribución binomial
Este servicio acepta 3 argumentos de una distribución binomial y genera una secuencia aleatoria de números que se distribuyen de manera binomial. Se le deben proporcionar los siguientes argumentos en la solicitud:

- n: número de observaciones. 
- tamaño: número de pruebas.
- prob: probabilidad de éxito.

La salida del servicio es una secuencia de longitud n con una distribución binomial basada en los argumentos de tamaño y probabilidad.

>Este servicio cuando está hospedado en Azure Marketplace es un servicio de OData, al que se puede llamar mediante los métodos POST o GET.

Hay varias maneras de consumir el servicio de forma automática (las aplicaciones de ejemplo son: [Generador](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [Calculadora de probabilidad](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [Calculadora de cuantil](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)).

###Inicio del código C# para el uso del servicio web:

###Calculadora de cuantil para la distribución binomial
	public class Input
	{
	        public string p;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
    void main()
	{
	        var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}

###Calculadora de probabilidad para la distribución binomial
	public class Input
	{
	        public string q;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}


###Generador de distribución binomial
	public class Input
	{
	        public string n;
	        public string size;
	        public string p;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}





##Creación del servicio web 

>Este servicio web se ha creado con el Aprendizaje automático de Azure. Para obtener acceso a una evaluación gratuita y a vídeos introductorios sobre la creación de experimentos y la [publicación de servicios web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). A continuación se muestra una captura de pantalla del experimento que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del experimento.

###Calculadora de cuantil para la distribución binomial

![Creación del espacio de trabajo][4]

####Módulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####Módulo 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###Calculadora de probabilidad para la distribución binomial

![Creación del espacio de trabajo][5]

####Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###Generador de distribución binomial

![Creación del espacio de trabajo][6]

####Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##Limitaciones 
Se trata de ejemplos muy sencillos relacionados con la distribución binomial. Como puede observarse en el código de ejemplo anterior, se implementa una detección de errores mínima.

##P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Azure Marketplace, haga clic [aquí](machine-learning-marketplace-faq.md).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 

<!---HONumber=AcomDC_0302_2016-->