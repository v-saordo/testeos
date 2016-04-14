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
	ms.date="02/23/2016"
	ms.author="stepsic"/>
	
# Uso de las características de aplicaciones lógicas

En el [tema anterior](app-service-logic-create-a-logic-app.md), creó su primera aplicación lógica. Ahora le mostraremos cómo crear un proceso más completo mediante las aplicaciones lógicas de Servicios de aplicaciones. En este tema se presentan los siguientes conceptos de las nuevas aplicaciones lógicas:

- La lógica condicional, que ejecuta una acción solo cuando se cumple una determinada condición.
- Vista de código para editar una aplicación lógica existente.
- Opciones para iniciar un flujo de trabajo.

Antes de completar este tema, debe completar los pasos de [Creación de una nueva aplicación lógica](app-service-logic-create-a-logic-app.md). En el [Portal de Azure], vaya a su aplicación lógica y haga clic en **Acciones y desencadenadores** en el resumen para editar la definición de la aplicación lógica.

## Material de referencia

Estos documentos pueden serle útiles:

- [API de REST de administración y tiempo de ejecución](https://msdn.microsoft.com/library/azure/dn948513.aspx): incluyen los métodos para invocar las aplicaciones lógicas directamente.
- [Referencia del lenguaje](https://msdn.microsoft.com/library/azure/dn948512.aspx): una lista completa de todas las funciones y expresiones compatibles.
- [Tipos de desencadenadores y acciones](https://msdn.microsoft.com/library/azure/dn948511.aspx): los diferentes tipos de acciones y las entradas que toman.
- [Información general sobre el Servicio de aplicaciones](../app-service/app-service-value-prop-what-is.md): descripción de los componentes que se deben elegir al crear una solución.

## Agregar lógica condicional

Aunque el flujo original funciona, hay algunas áreas que se podrían mejorar.


### Condicional
Esta aplicación lógica puede provocar que reciba muchos correos electrónicos. Los siguientes pasos agregan lógica adicional para asegurarse de que solo reciba un correo electrónico cuando el tweet proceda de una persona con un determinado número de seguidores.

1. Haga clic en el signo más y busque la acción *Obtener usuario* de Twitter.

2. Pase al campo **Twitteado por** desde el desencadenador para obtener la información sobre el usuario de Twitter.

	![Obtener usuario](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Haga clic en el signo más de nuevo, pero esta vez seleccione **Agregar condición**.

4. En el primer cuadro, haga clic en **...** debajo de **Obtener usuario** para encontrar el campo **Número de seguidores**.

5. En la lista desplegable, seleccione **Superior a**.

6. En el segundo cuadro, escriba el número de seguidores que desea que tengan los usuarios.

	![Condicional](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Por último, arrastre y coloque el cuadro de correo electrónico en el cuadro **Si procede**. Esto significa que solo obtendrá correos electrónicos cuando se alcance el número de seguidores.

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
 
Los parámetros son una buena forma de extraer valores que probablemente cambie mucho. Son especialmente útiles cuando necesite reemplazar parámetros en entornos diferentes. Para obtener más información sobre cómo invalidar los parámetros basados en el entorno, consulte nuestra [documentación de la API de REST](http://msdn.microsoft.com/library/mt643788(Azure.100).aspx).

Ahora, al hacer clic en **Guardar**, a cada hora recibirá los nuevos tweets que tengan más de 5 retweets en una carpeta llamada **tweets** en su Dropbox.

Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](app-service-logic-author-definitions.md).

## Inicio de un flujo de trabajo de aplicación lógica
Hay varias opciones para iniciar el flujo de trabajo definido en la aplicación lógica. Un flujo de trabajo siempre se puede iniciar a petición en el [Portal de Azure].

### Desencadenadores periódicos
Un desencadenador periódico se ejecuta en un intervalo que especifique. Cuando el desencadenador tiene lógica condicional, el desencadenador determina si necesita ejecutar el flujo de trabajo. Un desencadenador indica si se debe ejecutar; para ello, devuelve un código de estado de `200`. Cuando no es necesario ejecutarlo, devuelve un código de estado `202`.

### Devolución de llamada mediante las API de REST
Los servicios pueden llamar a un extremo de aplicación lógica para iniciar un flujo de trabajo. Consulte [Logic apps as callable endpoints](app-service-logic-connector-http.md) (Aplicaciones lógicas como puntos de conexión invocables) para obtener más información. Para iniciar ese tipo de aplicación lógica a petición, haga clic en el botón **Ejecutar ahora** en la barra de comandos.

<!-- Shared links -->
[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0224_2016-->