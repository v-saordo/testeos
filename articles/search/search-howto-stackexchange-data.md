<properties
	pageTitle="Búsqueda de datos de StackExchange mediante Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Obtenga información acerca de cómo realizar búsquedas REST con Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure."
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="liamca"/>

# Búsqueda de datos de StackExchange mediante Búsqueda de Azure

Este artículo es un tutorial que resalta algunas de las capacidades de búsqueda de texto completo básicas que puede realizar con [Búsqueda de Azure](https://azure.microsoft.com/services/search/). Aprovecha los datos que Exchange puso a [disposición](https://archive.org/details/stackexchange) para el uso de Creative Commons con la siguiente [atribución](http://blog.stackoverflow.com/2009/06/attribution-required/).

## Introducción

Para resaltar algunas de estas capacidades, he creado un índice de Búsqueda de Azure con el que jugar que contiene datos de Programmers StackExchange a partir del 14 de octubre de 2015. Nota: también mostraré cómo crear su propio índice de Búsqueda de Azure con estos datos más adelante.

Comencemos con una consulta de búsqueda de texto completo realmente simple en la que los usuarios pueden escribir la palabra "azure" para buscar las entradas de StackExchange relativas a Azure. Pruébelo haciendo clic en este vínculo para verlo en acción:

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

En este ejemplo, simplemente pasamos la palabra "azure" como un parámetro de búsqueda y mostramos los resultados con el formato JSON que se devuelven. A continuación se muestran algunos otros ejemplos de consultas que puede probar.

-	`Faceting`: Una vez que el usuario busca en el conjunto de datos, la posibilidad de filtrar los datos es una excelente manera de ayudarles a navegar por los resultados. Para implementarlo, normalmente empezará con un conjunto de categorías (facetas) que se muestran al usuario. A continuación se proporcionan algunos ejemplos de las facetas que podemos desear aprovechar:
  -	**Etiquetas**: muchas de las preguntas tienen etiquetas asociadas para permitir a los usuarios explorar categorías específicas
  -	**Fechas**: un usuario puede querer ver solo las preguntas preguntadas o respondidas durante un período específico
  -	**Usuario**: puede que desee ver o limitar los resultados de usuarios específicos. En este ejemplo, buscamos "azure", pero devuelven los recuentos de facetas para los nombres de usuario tagsCollection y acceptedAnswerDisplayName.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`: Una vez que un usuario elige una faceta, querrá realizar otra búsqueda, pero aprovechará un filtro para limitar los resultados a ese valor de faceta. Por ejemplo, para buscar "Azure" y limitar los resultados a la ubicación en la que hay una etiqueta "architecture", ordenados por viewCount en orden descendente:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Fuzzy Search`: Nuestra nueva compatibilidad con [expresiones de consulta Lucene](https://msdn.microsoft.com/library/mt589323.aspx) también permite hacer algunas consultas elegantes, como una coincidencia aproximada de los resultados y limitar la búsqueda a campos específicos. Este ejemplo busca en el campo de título la palabra “visualize”, pero ~ indica una coincidencia aproximada, lo que significa que los resultados como visualise y visualizar también se devolverán.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: Los [perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx) son muy útiles para ayudarle a afinar los resultados devueltos por el servicio de búsqueda. De hecho, ahora también podemos usar las [ expresiones de consulta Lucene](https://msdn.microsoft.com/library/mt589323.aspx) para aplicar la puntuación a términos y campos individuales sobre la marcha. Por ejemplo, si deseamos buscar las palabras “visualize” o “chart” en el campo de título, pero dar más peso a los elementos que tienen la palabra “chart” en ellos, podríamos hacer lo siguiente:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  Hay muchos otros campos en este conjunto de datos que se puede usar para mejorar los resultados relevantes para los usuarios. Por ejemplo, puedo usar:

  -	La puntuación **Magnitude** para campos numéricos, como answerCount, commentCount, favoriteCount y viewCount que proporciona un aumento de los resultados de búsqueda si tienen recuentos de altos.
  -	La puntuación **Freshness** para campos de fecha y hora, como creationDate y lastActivityDate para aumentar los elementos que se crearon o activaron recientemente
  -	**Field weights** para indicar que si el texto de búsqueda se encuentra en el cuerpo de la pregunta, es más relevante que si se encuentra en la respuesta.

Otras cosas con las que puede jugar, incluyen:

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx): A medida que los usuarios escriben en el cuadro de búsqueda, es conveniente usar el autocompletado en campos como Title, Tags y UserName.  

-	`Recommendations`: A menudo, necesitará herramientas como Apache Mahout o Aprendizaje automático de Azure para ayudarle a crear recomendaciones que le permitan mostrar preguntas similares a los usuarios que puedan estar interesado en la verlas, pero afortunadamente, este conjunto de datos ya tiene algunas recomendaciones.

No dude en jugar con esta página de JSFiddle para probar diferentes tipos de consultas. Si desea obtener más información acerca de cómo se creó este índice, siga leyendo. No dude también ponerse en contacto conmigo directamente a través de mi cuenta de twitter [@liamca](https://twitter.com/liamca).

## ¿Cómo se creó este índice de búsqueda de Azure?

Brent hizo un gran parte del trabajo duro al mostrar cómo realizar una copia intermedia de los datos en una base de datos SQL. Si desea ver este proceso de almacenamiento provisional de los datos, consulte su [tutorial aquí](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/). De lo contrario, puede aprovechar simplemente la Base de datos SQL de Azure rellenada previamente con datos desde el 15 de octubre de 2015, o bien puede probar el índice de Búsqueda de Azure que he creado. Sus detalles se describen a continuación en la sección sobre importación de datos de Base de datos SQL de Azure con copia intermedia. Lo único que hice más allá de lo descrito por Brent, fue crear una vista en mi Base de datos SQL de Azure que usará el [Indexador de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx) para rastrear e introducir datos de un grupo de tablas que estoy interesado en usar. A continuación se muestra cómo se define esta vista.

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

Una vez hecho esto, puede usar el [Portal de Azure clásico](https://portal.azure.com) para “Importar datos” de la vista de SQL Azure anterior que, a continuación, creará un índice de Búsqueda de Azure basado en el esquema de los campos en la vista. Si desea usar la Base de datos SQL de Azure cuya copia intermedia he realizado, esta es la cadena de conexión de solo lectura que puede usar:

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=AcomDC_0211_2016-->