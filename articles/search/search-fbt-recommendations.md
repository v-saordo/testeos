<properties pageTitle="Recomendaciones para Búsqueda de Azure: "Artículos que con frecuencia se compran juntos" | Microsoft Azure | Apache Mahout | Aprendizaje automático de Azure" description="Código de ejemplo para agregarlo a las recomendaciones de Artículos que con frecuencia se compran juntos, Novedades o También compraron en una aplicación de Búsqueda de Azure usando Apache Mahout o Aprendizaje automático de Azure" services="buscar" documentationCenter="" authors="liamca" manager="pablocas" editor="" tags="aprendizaje automático"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Recomendaciones para Búsqueda de Azure: "Artículos que con frecuencia se compran juntos"

Si ha efectuado compras en línea, probablemente habrá observado algunas listas del tipo "Artículos que con frecuencia se compran juntos", "Los clientes que compraron este producto también compraron" o "Novedades" que muestran otros productos o servicios para que vea si le interesan. Por lo general, estos datos los compilan minoristas en línea en función del historial de compras de muchos clientes o, simplemente, a partir de los datos de preferencias exclusivos del usuario. La capacidad para ofrecer recomendaciones pertinentes, dirigidas directamente al consumidor a través de la búsqueda es una herramienta eficaz que se agrega no solo a las aplicaciones comerciales, sino también a los sitios que ofrecen información o contenido multimedia.

En las aplicaciones de Búsqueda de Azure, se pueden implementar recomendaciones escribiendo código adicional que aporta datos y servicios usados para identificar patrones de datos y las relaciones entre ellos. A continuación, se transforma todo en recomendaciones pertinentes para los usuarios.

[En este ejemplo, se muestra cómo hacerlo](https://github.com/liamca/azure-search-recommendations) con [Apache Mahout]() para el motor de recomendaciones.

En el resto de este artículo, usaré un código de ejemplo para agregar recomendaciones sobre "Películas que con frecuencia se alquilan juntas" usando datos de muestra sobre alquileres de películas.

![1](./media/search-fbt-recommendations/product_recommendations.png)

## ¿Qué es una recomendación?

Una *recomendación* es una técnica para mostrar más elementos de un catálogo en función de la búsqueda concreta que ha efectuado un usuario (como registros web). Se usa para recomendar artículos y mejorar la conversión.

Los motores de recomendaciones se suelen entrenar con datos de actividades previas del cliente o recopilando datos directamente desde un almacén digital. En este ejemplo, se usa Apache Mahout para compilar los datos de recomendaciones.

Las recomendaciones habituales suelen incluir productos que con frecuencia se compran juntos (es decir, un cliente que compró esto también adquirió aquello otro) o recomendaciones de productos para un cliente (es decir, un cliente como usted también compró este producto).

## Creación de un índice de Búsqueda de Azure

- Abra la solución AzureSearchMovieRecommendations.sln y configure ImportAzureSearchIndexData como proyecto predeterminado.  
- Abra Program.cs en ImportAzureSearchIndexData y modifique SearchServiceName y SearchApiKey para que haga referencia al servicio Búsqueda de Azure.
- Descargue hetrec2011-movielens-2k.zip desde http://grouplens.org/datasets/hetrec-2011/ y copie los archivos Movies.dat y user\_ratedmovies.dat en \\ImportAzureSearchIndexData\\data.
- Ejecute el proyecto para crear un índice y cargar los datos de las películas. 
- Al final, la aplicación ejecutará una búsqueda de prueba.

## Cree una aplicación HTML sencilla para buscar películas.

En \\WebSite\\starter-template-complete, podrá encontrar una aplicación web JavaScript terminada que permite hacer consultas en el índice de Búsqueda de Azure.

Si desea realizar una demostración desde cero, puede encontrar el CSS original aquí: \\WebSite\\starter-template.

Abra el archivo search.js en \\WebSite\\starter-template-complete y actualice apiKey y azureSearchService con los detalles del servicio de Búsqueda de Azure.

Debería poder abrir este archivo en un explorador como Chrome para ver ahora las películas escribiendo en el cuadro de búsqueda.

## Comando para ejecutar la creación de recomendaciones con Mahout

- Cargue el archivo data\\movie\_usage.txt en Almacenamiento de blobs de Azure. 
- Cree una instancia de HDInsight (para habilitar el Escritorio remoto) y conéctese a la máquina a través de Escritorio remoto (disponible en el Portal de Azure clásico).
- En la máquina HDInsight, inicie la "línea de comandos de Hadoop".
- Acceda al directorio bin Mahout, en c:\\apps\\dist. El aspecto de la minería es este, pero se puede obtener una versión más reciente de Mahout C:\\apps\\dist\\mahout-1.0.0.2.3.3.0-2992\\bin.
- Ejecute la siguiente línea de comandos, donde sustituirá [CONTAINER] y [STORAGEACT] por los datos del Almacenamiento de Azure (ubicación donde colocó el archivo movie\_usage.txt):

    mahout itemsimilarity -s SIMILARITY\_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie\_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

Esto puede tardar algunos minutos en completarse, pero cuando termine, el contenedor de almacenamiento debe contener el siguiente archivo que incluirá las recomendaciones de películas: /movies/output/part-r-00000

El archivo tiene 3 columnas que muestran el identificador de elemento de la película, el identificador de elemento de las recomendaciones relacionadas con esta película y el porcentaje de similitud.

## Importación de datos desde Mahout hasta Búsqueda de Azure

El programa que creó el índice de Búsqueda de Azure también crea un campo denominado `Recommendations`, que es de tipo Collection (similar a un conjunto de cadenas separadas por comas). Combinaremos los datos creados en el paso anterior con este índice de Búsqueda de Azure.

- En la solución de Visual Studio AzureSearchMovieRecommendations.sln, abra Program.cs en MahoutOutputLoader.
- Actualice SearchServiceName y SearchApiKey con los detalles del servicio de Búsqueda de Azure.
- Actualice StorageApiKey y StorageAccountName con los detalles de la cuenta de Almacenamiento de Azure para la que se almacenó el archivo de recomendaciones de productos de Mahout.
- Ejecute la aplicación para combinar los datos.
 
## Visualización de las recomendaciones
En este punto, debe poder volver a la aplicación web y hacer clic en cualquiera de las películas para ver las recomendaciones.

Si desea ver cómo se han devuelto las recomendaciones al hacer clic en esta imagen, abra Search.js y busque en la función openMovieDetails().

## Créditos

Los datos los ha proporcionado GroupLens (http://grouplens.org/datasets/hetrec-2011/).

Consulte esta página para obtener más información sobre la licencia de estos datos: http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt

<!---HONumber=AcomDC_1203_2015-->