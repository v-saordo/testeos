<properties
   pageTitle="Información y creación de una aplicación de API de reglas de BizTalk en la aplicación lógica | Microsoft Azure"
   description="En este tema se describen las características del conector de reglas de BizTalk y se proporcionan instrucciones sobre su uso"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/18/2016"
   ms.author="andalmia"/>

#Reglas de BizTalk

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

Las reglas de negocios encapsulan las directivas y decisiones que controlan los procesos de negocio. Estas directivas se pueden definir formalmente en manuales de procedimientos, contratos o acuerdos, o pueden existir como conocimiento o experiencia incorporados en los empleados. Su naturaleza es dinámica y están sujetas a cambios con el paso del tiempo debido a cambios en los planes de negocio, las normativas u otros motivos.

La implementación de estas directivas en lenguajes de programación tradicionales requiere bastante tiempo y coordinación y no permite que los que no son programadores participen en la creación y el mantenimiento de directivas de negocios. Las reglas de negocios de BizTalk ofrecen una manera de implementar rápidamente estas directivas y desacoplar el resto del proceso de negocio. Esto permite realizar los cambios necesarios en las directivas de negocios sin afectar al resto del proceso de negocio.

##Porqué reglas

Existen 3 motivos principales para usar las reglas de negocios de BizTalk en el proceso de negocio:

* Desacoplar la lógica de negocios del código de aplicación
- Permitir a los analistas de negocios tener un mayor control sobre la administración de la lógica de negocios
+ Los cambios en la lógica de negocios pasan a producción de manera más rápida

##Conceptos sobre reglas

##Vocabulario

Un _vocabulario_ es una colección de definiciones que constan de nombres descriptivos de los objetos de cálculo usados en las condiciones y acciones de reglas. Las definiciones de vocabulario hacen que las reglas sean más fáciles de leer, entender y compartir por la gente de un dominio de negocio en particular.

Se designan vocabularios para salvar las distancias entre la semántica de negocio y la implementación. Por ejemplo, un enlace de datos para un estado de aprobación podría señalar a una determinada columna de una determinada fila de una determinada base de datos, representada como una consulta SQL. En lugar de insertar este tipo de representación compleja en una regla, podría crear una definición de vocabulario asociada con ese enlace de datos, con el nombre descriptivo de "Estado". Posteriormente, puede incluir "Estado" en cualquier número de reglas y el motor de reglas puede recuperar los datos correspondientes de la tabla.

##Regla

Las reglas de negocios son afirmaciones declarativas que rigen la conducta de los procesos de negocio. Una regla está formada por condiciones y acciones. La condición se evalúa y, si el resultado es verdadero, el motor de reglas inicia una o más acciones. Las reglas del marco de reglas de negocios se definen mediante el siguiente formato:

_IF_ _condition_ _THEN_ _action_

Considere el siguiente ejemplo:

*IF el importe es menor o igual que los fondos disponibles*  
*THEN realizar transacción e imprimir recibo*

Esta regla determina si una transacción se realizará aplicando la lógica de negocios, como una comparación de dos valores monetarios, en forma de un importe de transacción y los fondos disponibles. 
Puede usar la regla de negocios para crear, modificar e implementar reglas de negocios. Como alternativa, puede realizar las tareas anteriores mediante programación.

###Condiciones

Una condición es una expresión verdadera o falsa (Booleana) que consta de uno o varios predicados.
En nuestro ejemplo, el predicado menos o igual que se aplica al importe y los fondos disponibles. 
Esta condición siempre se evaluará como verdadera o falsa. Los predicados se pueden combinar con los operadores lógicos AND, OR y NOT para formar una expresión lógica que puede ser bastante larga, pero que siempre se evaluará como verdadera o falsa.

###Acciones

Las acciones son las consecuencias funcionales de la evaluación de las condiciones. Si se cumple una condición de regla, se inician las acciones correspondientes. 
En nuestro ejemplo, "realizar transacción" e "imprimir recibo" son acciones que se llevan a cabo cuando, y solo cuando, la condición (en este caso, "IF el importe es menor o igual que los fondos disponibles") es verdadera. 
Las acciones se representan en el marco de las reglas de negocios mediante la realización de operaciones Set en documentos XML.

##Directiva

Una directiva es una agrupación lógica de reglas. La directiva se redacta, se guarda, se prueba y, cuando se está satisfecho con los resultados, se usa en el entorno de producción.

###Redacción de una directiva

Puede redactar directivas en el portal de reglas. Una directiva puede contener un conjunto de reglas arbitrariamente alto, pero normalmente una regla se redacta a partir de reglas que tienen que ver con un dominio de negocio específico dentro del contexto de la aplicación en la que se usará la directiva.

###Prueba de directivas
Puede realizar una ejecución de prueba de su directiva antes de usarla en el entorno de producción. El portal de reglas le permite proporcionar entradas a una directiva, ejecutar la directiva y ver su resultado. El resultado incluye registros, ejecución de reglas, evaluación de condiciones y salidas resultantes.

##Escenario de ejemplo: reclamaciones de seguros
Vamos a examinar un escenario de ejemplo y recorrerlo mientras redactamos la lógica de negocios para el mismo.

![Texto alternativo][1]

En un escenario de reclamaciones de seguros realmente sencillo, el demandante envía su reclamación (mediante cualquier cliente como un sitio web, una aplicación de teléfono, etc.). Esta solicitud de reclamación se envía a la unidad de procesamiento de reclamaciones de la compañía y, en función del resultado del procesamiento, la reclamación se puede aprobar, rechazar o enviar para seguir procesándola manualmente. 
La unidad de procesamiento de reclamaciones de nuestro escenario sería la que incluiría la lógica de negocios del sistema. Si examinamos más detenidamente esta unidad, podemos ver lo siguiente:

![Texto alternativo][2]

Vamos a usar ahora reglas de negocios para implementar esta lógica de negocios.


##Creación de la aplicación de API de reglas


1. Inicie sesión en el Portal de Azure.
2. Seleccione Nuevo -> Marketplace y busque *Reglas de BizTalk*.
3. Seleccione las reglas de BizTalk de la lista de resultados. Se abre la hoja Reglas de BizTalk.
4. Seleccione el botón *Crear*. ![Texto alternativo][3]
1. En la nueva hoja que se abre, escriba la siguiente información:  
	1. Nombre: proporcione un nombre para su aplicación de API de reglas
	1. Plan del Servicio de aplicaciones: seleccione o cree un nuevo plan del Servicio de aplicaciones
	1. Nivel de precios: elija el nivel de precios en el que desea que resida esta aplicación
	1. Grupo de recursos: seleccione o cree el grupo de recursos donde debe residir la aplicación
	2. Suscripción: seleccione la suscripción que desea usar
	1. Ubicación: elija la ubicación geográfica en la que desearía implementar la aplicación.
4.	Seleccione *Crear*. En unos minutos su aplicación de API de reglas de BizTalk se habrá creado.

##Creación de vocabulario
Tras crear una aplicación de API de reglas de BizTalk, el siguiente paso sería crear vocabularios. Lo normal es que sea el desarrollador el que se encargue de esta tarea. Aquí se muestra cómo hacer esto:


1. Inicie la aplicación de API de Reglas de BizTalk en el portal; para ello, vaya a Examinar->Aplicaciones de API-><Your Rules API App>. Se dirigirá al panel de la aplicación de API de reglas, parecido a este:

   ![Texto alternativo][4]

2.Seleccione "Definiciones de vocabulario". Se mostrará la pantalla de creación de vocabulario.  
3.Seleccione "Agregar" para comenzar a agregar nuevas definiciones de vocabulario.
Actualmente se admiten dos tipos de definiciones de vocabulario: literal y XML.

##Definición literal
1.	Después de hacer clic en “Agregar”, se abre una nueva hoja para “Agregar definición”. Escriba los siguientes valores:
  1.	Nombre: solo se esperan caracteres alfanuméricos sin caracteres especiales. Aparte, debe ser único en su lista de definiciones de vocabulario existente.
  2.	Descripción: es un campo opcional.
  3.	Tipo de definición: se admiten dos tipos. En este ejemplo, elija Literal
  4.	Tipo de entrada: aquí los usuarios pueden seleccionar el tipo de datos de la definición. Actualmente se admiten cuatro tipos: 
    i. Cadena: estos valores deben escribirse entre comillas dobles ("cadena de ejemplo")  
    ii. Booleano: puede tener el valor verdadero o falso  
    iii. Número: puede ser cualquier número decimal  
    iv. DateTime: esto significa que la definición es de tipo fecha. Los datos se deben escribir con el formato: mm/dd/aaaa hh:mm:ss AM\PM  
  5. Entrada: aquí se escribe el valor de su definición. Los valores aquí especificados deben ajustarse al tipo de dato elegido. Puede escribir un solo valor, un conjunto de valores separados por coma o un intervalo de valores mediante la palabra clave *to* (a). Por ejemplo, puede escribir un valor único 1; un conjunto 1, 2, 3; o un intervalo de 1 a 5. Tenga en cuenta que el intervalo solo se permite para números.
  6. Seleccione *Aceptar*.

![Texto alternativo][5]
##Definición de XML
Si el tipo de vocabulario elegido es XML, será necesario especificar las siguientes entradas  
  a.	Esquema: al hacer clic aquí se abrirá una nueva hoja que permite al usuario elegir en una lista de esquemas ya cargados o bien cargar uno nuevo.  
  b.	XPATH: esta entrada solo se desbloquea después de elegir un esquema en el paso anterior. Al hacer clic aquí se mostrará el esquema que se ha seleccionado y permite al usuario seleccionar el nodo para el que es necesario crear una definición de vocabulario.  
  c.	FACT: esta entrada identifica el objeto de datos que se pasaría al motor de reglas para su procesamiento. Se trata de una propiedad avanzada y, de forma predeterminada, se establece en el elemento primario de la XPATH seleccionada. FACT se vuelve especialmente importante en escenario de encadenamiento y recopilación. 

![Texto alternativo][6]

### Agregar en masa
Los pasos anteriores han capturado la experiencia para crear definiciones de vocabulario. Una vez creadas, aparecen en forma de lista. No existen requisitos para poder generar varias definiciones a partir del mismo esquema en lugar de repetir los pasos anteriores cada vez. Aquí es donde la funcionalidad para agregar en masa se vuelve muy útil. 
Al seleccionar "Agregar en masa" irá a parar a una nueva hoja. El primer paso consiste en seleccionar el esquema para el que se van a crear varias definiciones. Al hacer clic en este, se muestra una nueva hoja que le permite elegir de una lista de esquemas ya cargados o cargar uno nuevo. 
Ahora se desbloquea la propiedad XPATHS. Al hacer clic en ella, se abre el Visor de esquemas donde se pueden seleccionar varios nodos. 
Los nombres de las múltiples definiciones creadas adoptarán de forma predeterminada el nombre del nodo seleccionado. Estas se pueden modificar siempre tras la creación.

![Texto alternativo][7]

##Creación de directivas
Una vez que el desarrollador ha creado los vocabularios necesarios, lo normal es que el analista de negocios sea el que cree las directivas de negocios a través del portal de Azure.  
	1.	En la aplicación de reglas creada, hay un modo Directiva en el que al hacer clic el usuario va a la página de creación de directivas.  
 	2. Esta página mostrará la lista de directivas que tiene esta aplicación de reglas en particular. El analista puede agregar una nueva directiva con solo escribir un nombre y pulsar la tecla de tabulación dos veces. Pueden residir varias directivas en una sola aplicación de API de reglas.  
 	3. Al seleccionar la directiva creada, el usuario va a la página de detalles de la directiva donde puede ver las reglas que contiene.  
	![Texto alternativo][8]  
	4. Seleccione "Agregar" para agregar una nueva regla. Esta acción le llevará a una nueva hoja.

##Creación de reglas
Una regla es una colección de declaraciones de condición y acción. Las acciones se ejecutan si la condición se evalúa como verdadera. En la hoja Crear regla, proporcione un nombre único a la regla (para esa directiva) y una descripción (opcional). El cuadro Condición (IF) se puede usar para crear declaraciones condicionales complejas. A continuación se indican las palabras clave admitidas:  
1. 	And: operador condicional  
2. 	Or: operador condicional  
3. 	does_not_exist  
4. 	exists  
5. 	false  
6. 	is_equal_to  
7. 	is_greater_than  
8. 	is_greater_than_equal_to  
9. 	is_in  
10. is_less_than  
11. is_less_than_equal_to  
12. is_not_in  
13. is_not_equal_to  
14. mod  
15. true 

El cuadro Acción (Then) puede contener varias declaraciones, una por línea, para crear acciones que se van a ejecutar. Las palabras clave admitidas son las siguientes:  
1.	equals  
2.	false  
3.	true  
4.	halt  
5.	mod  
6.	null  
7.	update  

Los cuadros de condición y acción proporcionan Intellisense para ayudar al autor a crear una regla rápidamente. Esta función se puede activar bien pulsando Ctrl + espacio o con solo comenzar a escribir. Las palabras clave que coinciden con los caracteres escritos se filtrarán y mostrarán automáticamente. La ventana de Intellisense mostrará todas las palabras clave y definiciones de vocabulario. 
![Texto alternativo][9]

##Encadenamiento progresivo explícito
Las reglas de BizTalk admiten encadenamiento progresivo explícito, de modo que si los usuarios desean volver a evaluar reglas en respuesta a determinadas acciones, pueden hacerlo mediante el uso de determinadas palabras clave. Las palabras clave admitidas son las siguientes:  
   1.	update <vocabulary definition>: esta palabra clave vuelve a evaluar todas las reglas que usan la definición de vocabulario especificada en su condición.  
   2.	Halt: esta palabra clave detiene todas las ejecuciones de reglas

##Habilitación/deshabilitación de reglas
Cada regla de la directiva se puede habilitar o deshabilitar. De forma predeterminada, todas las reglas están habilitadas. Las reglas deshabilitadas no se ejecutarán durante la ejecución de la directiva. Se pueden habilitar o deshabilitar reglas directamente desde la hoja de reglas, los comandos están disponibles en la barra de comandos de la parte superior de la hoja; o desde la directiva, el menú contextual (clic con el botón derecho en una regla) tiene la opción para habilitar o deshabilitar.

##Prioridad de las reglas
Todas las reglas de una directiva se ejecutan en orden. La prioridad de la ejecución viene determinada por el orden en el que tienen lugar en la directiva. Esta prioridad se puede cambiar con solo arrastrar y colocar la regla.

##Prueba de directivas
Puede probar las directivas mediante el comando "Probar directiva" de la hoja Probar directiva. En esta hoja, puede ver una lista de definiciones de vocabulario que se usan en la directiva que requieren que el usuario proporcione datos. Los usuarios pueden agregar valores manualmente para estas entradas de su escenario de prueba. Como alternativa, los usuarios pueden elegir también importar XML de prueba para las entradas. Después de haberse realizado todas las entradas, se puede ejecutar la prueba y las salidas de cada definición de vocabulario se mostrarán en la columna de salida para que pueda compararlas fácilmente. Para ver los registros de análisis de negocios, haga clic en “Ver registros” para ver los registros de ejecución. Para guardar los registros, hay una opción “Guardar salida” que permite almacenar todos lo datos relacionados con la prueba para luego someterlos a un análisis independiente.

## Uso de reglas en las aplicaciones lógicas
Después de que la directiva se ha creado y probado, está lista para su consumo. Puede crear una nueva aplicación lógica si selecciona Aplicaciones lógicas en el lado izquierdo de la página principal del portal. Después de crear la aplicación lógica, iníciela y luego seleccione *Desencadenadores y acciones*. A continuación, puede seleccionar la plantilla *Crear desde cero*. Siga los pasos para agregar la aplicación de API de reglas de BizTalk a la aplicación lógica. Una vez realizado esto, habrá una opción para elegir la aplicación de API de reglas (Acción) a la que dirigirse. Las acciones incluyen la lista de directivas que se van a ejecutar. Elija una directiva específica tras la cual es necesario insertar las entradas necesarias para la directiva. Los usuarios pueden usar la salida de la aplicación de API de reglas de bajada para profundizar en la toma de decisiones.

## Uso de reglas mediante API
La aplicación de API de reglas también se puede invocar mediante el uso de un completo conjunto de API. De esta manera los usuarios no están limitados únicamente al uso de aplicaciones lógicas y pueden emplear reglas en cualquier aplicación mediante la realización de llamadas a REST. Las API de REST exactas disponibles se pueden ver haciendo clic en el modo "Definición de API" en el panel Reglas.

![Texto alternativo][10]

A continuación se muestra un ejemplo de cómo se puede usar esta API en C#

   			// Constructing the JSON message to use in API call to Rules API App

			// xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0="http://tempuri.org/XMLSchema.xsd">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

			// The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
			// In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
			// This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

			// The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Tenga en cuenta que la aplicación de API de reglas anterior tiene su configuración de seguridad establecida en "Anon pública". Esto se puede establecer desde dentro de la aplicación de API con Toda la configuración -> Configuración de la aplicación -> Nivel de acceso.

![Texto alternativo][11]

## Edición de vocabulario y directivas
Una de las principales ventajas del uso de reglas de negocios es que los cambios en la lógica de negocios se pueden trasladar a producción de forma mucho más rápida. Todo cambio realizado en el vocabulario y las directivas se aplica automáticamente en producción. El usuario solo tiene que ir a la directiva o definición de vocabulario respectiva y realizar el cambio para que se haga efectivo.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG

<!---HONumber=AcomDC_0224_2016-->
