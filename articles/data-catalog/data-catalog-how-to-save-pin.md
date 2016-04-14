<properties
   pageTitle="Cómo guardar búsquedas y anclar recursos de datos"
   description="Artículo de procedimientos que destaca las funciones del Catálogo de datos de Azure para guardar orígenes de datos y recursos de datos para su uso posterior."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/26/2016"
   ms.author="maroche"/>

# Cómo guardar búsquedas y anclar recursos de datos

## Introducción

El Catálogo de datos de Microsoft Azure proporciona funciones de detección de orígenes de datos. Los usuarios pueden buscar y filtrar rápidamente el catálogo para buscar orígenes de datos y entender su finalidad prevista, lo que permite encontrar fácilmente los datos correctos para el trabajo en cuestión.

Pero ¿qué ocurre cuando los usuarios necesitan trabajar regularmente con los mismos datos? ¿Qué sucede cuando los usuarios aportan regularmente sus conocimientos a los mismos orígenes de datos en el catálogo? En estas situaciones, tener que emitir repetidamente las mismas búsquedas puede no resultar eficiente; aquí es donde las búsquedas guardadas y los recursos de datos anclados pueden ayudar.

## Búsquedas guardadas

Una búsqueda guardada en el Catálogo de datos de Azure es una definición de búsqueda por usuario reutilizable. Después de que un usuario define una búsqueda, incluidos los términos de búsqueda, etiquetas y otros filtros, puede guardarla para su uso posterior. La definición de la búsqueda guardada puede volver a ejecutarse posteriormente para devolver los recursos de datos que coincidan con los criterios de búsqueda.

### Creación de una búsqueda guardada

Para crear una búsqueda guardada, especifique primero los criterios de búsqueda que se reutilizarán. A continuación, haga clic en el vínculo "Guardar" en el cuadro "Búsqueda actual" del portal Catálogo de datos de Azure.

 ![Seleccione 'Guardar' para guardar la configuración de búsqueda actual](./media/data-catalog-how-to-save-pin/01-save-option.png)

Cuando se le solicite, escriba un nombre para la búsqueda guardada. Elija un nombre que sea significativo y descriptivo de los recursos de datos que la búsqueda devolverá.

 ![Proporcione un nombre para la búsqueda guardada](./media/data-catalog-how-to-save-pin/02-name.png)

### Administración de búsquedas guardadas

Después de que el usuario guarda una o varias búsquedas, aparecerá una opción "Búsquedas guardadas" en el portal Catálogo de datos de Azure, en el cuadro "Búsqueda actual". Una vez expandida la lista, se mostrarán todas las búsquedas guardadas.

 ![Lista de búsquedas guardadas](./media/data-catalog-how-to-save-pin/03-list.png)

Al seleccionar una búsqueda guardada en la lista, esta se ejecutará.

Al seleccionar el menú desplegable aparecerá un conjunto de opciones de administración:

 ![Opción para administrar búsquedas guardadas](./media/data-catalog-how-to-save-pin/04-managing.png)

Al seleccionar "Cambiar nombre", le pedirá al usuario que escriba un nuevo nombre para la búsqueda guardada. No se cambiará la definición de la búsqueda.

Al seleccionar "Eliminar" le pedirá confirmación al usuario y, a continuación, la búsqueda guardada se quitará de la lista del usuario.

Al seleccionar "Guardar como predeterminado", la búsqueda guardada elegida se marcará como la búsqueda predeterminada del usuario. Si el usuario realiza una búsqueda "vacía" desde la página principal del Catálogo de datos de Azure, se ejecutará la búsqueda predeterminada del usuario. Además, la búsqueda marcada como predeterminada aparecerá en la parte superior de la lista de búsquedas guardadas.

## Recursos de datos anclados

Las búsquedas guardadas permiten a los usuarios guardar y reutilizar las definiciones de búsqueda; los recursos de datos devueltos por las búsquedas pueden cambiar con el tiempo a medida que el contenido del catálogo cambia. El anclado de recursos de datos permite a los usuarios identificar explícitamente recursos de datos específicos para facilitar su acceso sin necesidad de usar una búsqueda.

El anclado de un recurso de datos es sencillo: los usuarios solo tienen que hacer clic en el icono del recurso de datos para agregarlo a su lista anclada. Este icono aparece en la esquina del icono del recurso en la vista de iconos, y en la columna situada más a la izquierda en la vista de lista del portal Catálogo de datos de Azure.

![Anclado de un recurso de datos](./media/data-catalog-how-to-save-pin/05-pinning.png)

Desanclar un recurso es igualmente sencillo: los usuarios solo tienen que volver a hacer clic en el icono de "anclar" para cambiar la configuración del recurso seleccionado.

![Desanclado de un recurso de datos](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## “Mis recursos”
La página principal del portal Catálogo de datos de Azure una sección "Mis recursos" que muestra los recursos de interés para el usuario actual. Esta sección incluye tanto recursos anclados como búsquedas guardadas.

!['Mis recursos' en la página principal](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## Resumen
El Catálogo de datos de Azure proporciona funciones que permiten a los usuarios encontrar fácilmente los orígenes de datos que necesitan, por lo que pueden dedicar menos tiempo a buscar datos y más tiempo a trabajar con ellos. Las búsquedas guardadas y los recursos de datos anclados se basan en estas funciones centrales para que los usuarios puedan identificar fácilmente los orígenes de datos con los que trabajarán repetidamente.

<!---HONumber=AcomDC_0302_2016-->