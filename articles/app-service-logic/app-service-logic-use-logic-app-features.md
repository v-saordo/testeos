<properties 
	pageTitle="Uso de las características de aplicaciones lógicas | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar las características avanzadas de las aplicaciones lógicas." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="stepsic"/>
	
# Uso de las características de aplicaciones lógicas

En el [tema anterior][Create a new logic app], creó su primera aplicación lógica. Ahora le mostraremos cómo crear un proceso más completo mediante las aplicaciones lógicas de Servicios de aplicaciones. En este tema se presentan los siguientes conceptos de las nuevas aplicaciones lógicas:

- La lógica condicional, que ejecuta una acción solo cuando se cumple una determinada condición.
- Acciones repetidas.
- Vista de código para editar una aplicación lógica existente.
- Opciones para iniciar un flujo de trabajo.

Antes de completar este tema, debe completar los pasos de [Creación de una nueva aplicación lógica]. En el [Portal de Azure], vaya a su aplicación lógica y haga clic en **Acciones y desencadenadores** en el resumen para editar la definición de la aplicación lógica.

## Material de referencia

Estos documentos pueden serle útiles:

- [API de REST de administración y tiempo de ejecución](https://msdn.microsoft.com/library/azure/dn948513.aspx): incluyen los métodos para invocar las aplicaciones lógicas directamente.
- [Referencia del lenguaje](https://msdn.microsoft.com/library/azure/dn948512.aspx): una lista completa de todas las funciones y expresiones compatibles.
- [Tipos de desencadenadores y acciones](https://msdn.microsoft.com/library/azure/dn948511.aspx): los diferentes tipos de acciones y las entradas que toman.
- [Información general sobre el Servicio de aplicaciones](app-service-value-prop-what-is.md): descripción de los componentes que se deben elegir al crear una solución.

## Incorporación de lógica condicional y una repetición

Aunque el flujo original funciona, hay algunas áreas que se podrían mejorar. En primer lugar, la acción solo envía el primer tweet devuelto. Lógicamente, desearía recibir todos los tweets con la palabra clave. Para repetir una acción para obtener una lista de elementos, como los tweets devueltos, debe utilizar la propiedad `repeat`.

### Repetición
La repetición toma una lista de elementos y ejecuta la acción para cada elemento de esa lista. Los siguientes pasos actualizan la acción existente para que utilice repeticiones, que tiene más sentido para una lista de tweets.

1. Vuelva al flujo de trabajo que creó y haga clic en el vínculo **Definición** en **Essentials**. 

2. Para editar la acción **conector de Dropbox** , haga clic en el icono de lápiz.

3. Haga clic en el icono de engranaje y seleccione **Repetir en una lista**.
 
2. Junto al cuadro **Repetir**, haga clic en `...` y seleccione **Cuerpo**. Esto proporcionará la siguiente entrada:

    	@body('twitterconnector')

	En el cuadro de texto. Esta función genera una lista de tweets.

3. Seleccione todo el texto del cuadro de texto **Contenido** y elimínelo. A continuación, haga clic en `...` y seleccione **Texto de los tweets**. Esto inserta la función **repeatItem()**, que devuelve cada elemento de la lista.

Por último, tenga en cuenta que las salidas de las acciones de repetición son especiales. Si desea hacer referencia a los resultados de la operación de Dropbox, por ejemplo, *no* podría hacer lo normal `@actions('dropboxconnector').outputs.body`. En lugar de ello, haría: `@actions('dropboxconnector').outputs.repeatItems`. Esto devuelve una lista de todas las veces que se ejecutó la operación, junto con las salidas de cada una. Por ejemplo, `@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath` devuelve la ruta de acceso del primer archivo cargado.

### Condicional
Esta aplicación lógica sigue dando como resultado una gran cantidad de archivos que se cargan en Dropbox. Los siguientes pasos agregan lógica adicional para asegurarse de que solo reciba un archivo cuando el tweet tenga un número determinado de retweets.

1. Haga clic en el icono de engranaje en la parte superior de la acción y seleccione **Agregar una condición a cumplir**.

2. En el cuadro de texto, escriba lo siguiente:

    	@greater(repeatItem().Retweet_Count , 5)
    
	La función **mayor** compara dos valores y solo permite ejecutar la acción cuando el primer valor es mayor que el segundo valor. Obtenga acceso a una propiedad determinada como un punto (.) seguido del nombre de propiedad, como `.Retweet_Count` anteriormente.

3. Haga clic en la marca de verificación para guardar la acción de Dropbox.

## Uso de la vista código para modificar una aplicación lógica

Además del diseñador, puede editar directamente el código que define una aplicación lógica, como sigue.

1. Haga clic en el botón **vista Código** en la barra de comandos. 

	Se abrirá un editor completo que muestra la definición que acaba de modificar.

	![Vista de código](./media/app-service-logic-use-logic-app-features/codeview.png)

    Mediante el editor de texto, puede copiar y pegar cualquier cantidad de acciones dentro de la misma aplicación lógica o entre aplicaciones lógicas. También puede agregar o quitar secciones completas de la definición, así como compartir definiciones con otros.

2. Después de realizar los cambios en la vista Código, simplemente haga clic en **Guardar**.

### Parámetros
Hay algunas capacidades de las aplicaciones lógicas que solo puede utilizarse en la vista Código. Un ejemplo son los parámetros. Los parámetros facilitan volver a usar los valores a lo largo de la aplicación lógica. Por ejemplo, si tiene una dirección de correo electrónico que desea utilizar en varias acciones, debe definirla como un parámetro.

Lo siguiente actualiza la aplicación lógica existente para que use parámetros para el término de la consulta.

1. En la vista Código, busque el objeto `parameters : {}` e inserte el siguiente objeto de tema:

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. Desplácese a la acción `twitterconnector`, busque el valor de consulta y reemplácelo por `#@{parameters('topic')}`. También podría usar la función **concat** para unir dos o más cadenas, por ejemplo: `@concat('#',parameters('topic'))` es idéntica a la anterior.
 
3. Por último, vaya a la acción `dropboxconnector` y agregue el parámetro de tema, como sigue:

    	/tweets/@{parameters('topic')}/@{repeatItem().TweetID}.txt

Los parámetros son una buena forma de extraer valores que probablemente cambie mucho. Son especialmente útiles cuando necesite reemplazar parámetros en entornos diferentes. Para obtener más información sobre cómo invalidar los parámetros basados en el entorno, consulte nuestra [documentación de API de REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

Ahora, al hacer clic en **Guardar**, a cada hora recibirá los nuevos tweets que tengan más de 5 retweets en una carpeta llamada **tweets** en su Dropbox.

Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](app-service-logic-author-definitions.md).

## Inicio de un flujo de trabajo de aplicación lógica
Hay varias opciones para iniciar el flujo de trabajo definido en la aplicación lógica. Un flujo de trabajo siempre se puede iniciar a petición en el [Portal de Azure].

### Desencadenadores periódicos
Un desencadenador periódico se ejecuta en un intervalo que especifique. Cuando el desencadenador tiene lógica condicional, el desencadenador determina si necesita ejecutar el flujo de trabajo. Un desencadenador indica si se debe ejecutar; para ello, devuelve un código de estado de `200`. Cuando no es necesario ejecutarlo, devuelve un código de estado `202`.

### Devolución de llamada mediante las API de REST
Los servicios pueden llamar a un extremo de aplicación lógica para iniciar un flujo de trabajo. Para buscar el extremo al que desea tener acceso, desplácese hasta la hoja **Propiedades** desde el botón de la barra de comandos **Configuración** en la aplicación lógica.

Puede usar esta devolución de llamada para invocar una aplicación lógica desde dentro de la aplicación personalizada. Deberá usar la autenticación **básica**. El nombre de usuario de `default` se crea automáticamente, y la contraseña es el campo **Clave de acceso primaria** en la hoja **Propiedades**. Por ejemplo:

        POST https://<< your endpoint >>/run?api-version=2015-02-01-preview
        Content-type: application/json
        Authorization: Basic << base-64 encoded string of default:<access key> >>
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

Puede pasar salidas al flujo de trabajo y hacer referencia a ellas en el flujo de trabajo. Por ejemplo, con el desencadenador anterior, si incluye `@triggers().outputs.property`, obtiene `value`.

Para obtener más información, consulte la [documentación de REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

### Ejecución manual
Puede definir una aplicación lógica que no tenga ningún desencadenador. En este caso, se debe iniciar el flujo de trabajo a petición. Este tipo de aplicación lógica es más adecuado para un proceso que solo debe ejecutarse de forma intermitente. Para crear una aplicación lógica sin ningún desencadenador, seleccione **Ejecutar esta lógica manualmente** en el cuadro **Iniciar lógica** en el diseñador.

Para iniciar la aplicación lógica a petición, haga clic en el botón **Ejecutar ahora** en la barra de comandos.

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[Creación de una nueva aplicación lógica]: app-service-logic-create-a-logic-app.md
[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0107_2016-->