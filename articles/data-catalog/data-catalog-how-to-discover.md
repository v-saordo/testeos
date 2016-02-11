<properties
   pageTitle="Detección de orígenes de datos"
   description="Artículo de procedimientos que indica cómo detectar recursos de datos registrados con el Catálogo de datos de Azure, incluidos la búsqueda y el filtrado, y mediante las capacidades de resaltado de referencias del portal del Catálogo de datos de Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/10/2015"
   ms.author="maroche"/>


# Detección de orígenes de datos

## Introducción
El **Catálogo de datos de Microsoft Azure** es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección de orígenes de datos empresariales. En otras palabras, el **Catálogo de datos de Azure** consiste en ayudar a las personas a detectar, comprender y usar orígenes de datos, y en ayudar a las organizaciones a obtener más valor de sus datos. Cuando se ha registrado un origen de datos con el **Catálogo de datos de Azure**, sus metadatos se indexan según el servicio, de forma que los usuarios pueden realizar búsquedas con facilidad de los datos que necesitan.

## Búsqueda y filtrado

La detección en el **Catálogo de datos de Azure** usa dos mecanismos principales: la búsqueda y el filtrado.

La búsqueda está diseñada para ser potente e intuitiva: de forma predeterminada, se comparan los términos de la búsqueda con cualquier propiedad del catálogo, incluidas las anotaciones proporcionadas por el usuario.

El filtrado está diseñado para complementar la búsqueda. Los usuarios pueden seleccionar características específicas como expertos, tipos de origen de datos, tipos de objeto y etiquetas, para ver solo los recursos de datos que coincidan, así como para restringir los resultados de la búsqueda a los recursos correspondientes.

Con una combinación de la búsqueda y el filtrado, los usuarios pueden navegar rápidamente a través de los orígenes de datos registrados con el **Catálogo de datos de Azure** para detectar los orígenes de datos que necesitan.

## Sintaxis de búsqueda

Aunque la búsqueda de texto libre predeterminada es sencilla e intuitiva, los usuarios también pueden usar la sintaxis de búsqueda del **Catálogo de datos de Azure** para tener un mayor control sobre los resultados de la búsqueda. La búsqueda del **Catálogo de datos de Azure** admite las siguientes técnicas:

| Técnica | Uso | Ejemplo |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Búsqueda básica | Búsqueda básica con uno o varios términos de búsqueda. Los resultados son los recursos que tienen alguna propiedad que coincide con uno o más de los términos especificados. | sales data |
| Ámbito de propiedad | Devuelve solamente los orígenes de datos para los que el término de búsqueda coincide con la propiedad especificada | name:finance |
| Operadores booleanos | Se puede ampliar o reducir una búsqueda mediante operaciones booleanas | finance NOT corporate |
| Agrupación con paréntesis | Use paréntesis para agrupar partes de la consulta y así conseguir aislamiento lógico, especialmente en combinación con los operadores booleanos | name:finance AND (tags:Q1 OR tags:Q2)Comparison Operators |
| Operadores de comparación | Use comparaciones distintas de la igualdad de propiedades que tengan tipos de datos numéricos y de fechas | creationTime>"11/05/2014" |

Para obtener más información sobre la búsqueda del **Catálogo de datos de Azure**, consulte [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## Resaltado de referencias
Al visualizar los resultados de la búsqueda, se resaltarán las propiedades mostradas que coinciden con los términos de búsqueda especificados (como el nombre del recurso de datos, la descripción y las etiquetas), para que sea más fácil identificar por qué se devuelve un recurso de datos concreto a partir de una búsqueda determinada.

> [AZURE.NOTE]Los usuarios pueden desactivar el resaltado de referencias si lo desean, mediante el conmutador "Resaltado" del portal del **Catálogo de datos de Azure**.

Al visualizar los resultados de la búsqueda, puede que no siempre sea evidente por qué se incluye un recurso de datos, incluso con el resaltado habilitado. Dado que se busca en todas las propiedades de forma predeterminada, es posible que se devuelva un recurso de datos debido a una coincidencia en una propiedad de nivel de columna. Y puesto que distintos usuarios pueden anotar los recursos de datos registrados con sus propias descripciones y etiquetas, es posible que no se muestren todos los metadatos en la lista de resultados de la búsqueda.

En la vista de iconos predeterminada, cada icono que se muestre en los resultados de la búsqueda incluirá un icono "Ver coincidencias de los términos de búsqueda", que permite al usuario ver rápidamente el número de coincidencias y su ubicación, y obtener acceso a ellas si lo desea.

 ![Resaltado de referencias y coincidencias de búsqueda en el portal del Catálogo de datos de Azure](./media/data-catalog-how-to-discover/search-matches.png)

## Resumen
Al registrar un origen de datos con el **Catálogo de datos de Azure** se facilita la detección y comprensión de ese origen de datos, copiando los metadatos descriptivos y estructurales del origen de datos en el servicio Catálogo. Cuando se registra un origen de datos, los usuarios pueden detectarlo mediante el filtrado y la búsqueda desde el portal del **Catálogo de datos de Azure**.

<!---HONumber=Nov15_HO3-->