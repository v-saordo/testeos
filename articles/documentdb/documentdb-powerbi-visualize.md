<properties 
	pageTitle="Tutorial de Power BI para el conector de DocumentDB | Microsoft Azure" 
	description="Use este tutorial de Power BI para importar JSON, crear informes muy precisos y visualizar datos mediante el conector de DocumentDB y Power BI." 
	keywords="tutorial de power bi, visualizar datos, conector de power bi"
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="hawong"/>

# Tutorial de Power BI para DocumentDB: visualizar datos mediante el conector de Power BI
[PowerBI.com](https://powerbi.microsoft.com/) es un servicio en línea, en el que se pueden crear paneles e informes, y compartirlos, con los datos que son importantes para el usuario y su organización. Power BI Desktop es una herramienta dedicada de creación de informes que permite recuperar datos de varios orígenes de datos, combinar y transformar los datos, crear visualizaciones e informes eficaces y publicar los informes en Power BI. Con la versión más reciente de Power BI Desktop, ahora puede conectarse a su cuenta de DocumentDB a través del conector DocumentDB para Power BI.

En este tutorial de Power BI, se le guiará por los pasos para conectarse a una cuenta de DocumentDB en Power BI Desktop, desplazarse a una colección donde queremos extraer los datos mediante el navegador, transformar datos JSON en formato tabular mediante el Editor de consultas de Power BI Desktop y generar y publicar un informe en PowerBI.com.

Después de completar este tutorial de Power BI, podrá responder a las preguntas siguientes:

-	¿Cómo genero informes con datos de DocumentDB con Power BI Desktop? 
-	¿Cómo puedo conectar a una cuenta de DocumentDB en Power BI Desktop?
-	¿Cómo puedo recuperar datos de una colección en Power BI Desktop?
-	¿Cómo puedo transformar datos JSON anidados en Power BI Desktop?
-	¿Cómo puedo publicar y compartir mis informes en PowerBI.com? 

## Requisitos previos

Antes de seguir las instrucciones de este tutorial de Power BI, asegúrese de contar con lo siguiente:

- [La versión más reciente de Power BI Desktop](https://powerbi.microsoft.com/desktop).
- Acceda a nuestros datos o cuenta de demostración de la cuenta de Azure DocumentDB. 
	- La cuenta de demostración se rellena con los datos de los volcanes mostrados en este tutorial. Esta cuenta de demostración no está vinculada a ningún SLA y está pensada únicamente con fines de demostración. Nos reservamos el derecho de realizar modificaciones en esta cuenta de demostración, incluido sin limitación, la cancelación de la cuenta, el cambio de clave, la restricción del acceso, el cambio y la eliminación de los datos, todo ello en cualquier momento y sin previo aviso. 
		- URL: https://analytics.documents.azure.com
		- Clave de solo lectura: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
	- O bien, para crear su propia cuenta, vea [Creación de una cuenta de base de datos de DocumentDB mediante el Portal de Azure](https://azure.microsoft.com/documentation/articles/documentdb-create-account/). Después, para obtener los datos de los volcanes que son similares a los usados en este tutorial (pero que no contienen los bloques de GeoJSON), vea el [sitio de NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) y luego importe los datos mediante la [herramienta de migración de datos de DocumentDB](https://azure.microsoft.com/documentation/articles/documentdb-import-data/).


Para compartir los informes en PowerBI.com, debe tener una cuenta en PowerBI.com. Para más información sobre la versión gratuita de Power BI y de Power BI Pro, visite [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## Comencemos.
En este tutorial, imaginemos que usted es un geólogo que estudia los volcanes de todo el mundo. Los datos de los volcanes se almacenan en una cuenta de DocumentDB y los documentos JSON tiene el aspecto que se muestra a continuación.

	{
    	"Volcano Name": "Rainier",
   		"Country": "United States",
  		"Region": "US-Washington",
  		"Location": {
    		"type": "Point",
    		"coordinates": [
      		-121.758,
      		46.87
    		]
  		},
  		"Elevation": 4392,
  		"Type": "Stratovolcano",
  		"Status": "Dendrochronology",
  		"Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
	}	

Quiere recuperar los datos de los volcanes de la cuenta DocumentDB y visualizar datos en un informe de Power BI interactivo como el siguiente.

![Después de completar este tutorial de Power BI con el conector de Power BI, podrá visualizar los datos con el informe de volcanes de Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

¿Listo para probarlo? Comencemos.


1. Ejecute Power BI Desktop en su estación de trabajo.
2. Una vez iniciado Power BI Desktop, se muestra la pantalla de *inicio de sesión*.

	![Pantalla de inicio de sesión de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_welcome.png)

3. También puede **obtener datos**, consultar **orígenes recientes** o **abrir otros informes** directamente de la pantalla de *inicio de sesión*. Haga clic en la X de la esquina superior derecha para cerrar la pantalla. Se muestra la vista **Informe** de Power BI Desktop.

	![Vista de informes de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview.png)

4. Seleccione la cinta de opciones **Inicio** y haga clic en **Obtener datos**. Se muestra la pantalla **Obtener datos**.

5. Haga clic en **Azure**, seleccione **Microsoft Azure DocumentDB (Beta)** y, a continuación, haga clic en **Conectar**. Se muestra la pantalla de **conexión de Microsoft Azure DocumentDB**.

	![Obtención de datos de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbigetdata.png)

6. Especifique la URL del punto de conexión de la cuenta de DocumentDB desde la que desea recuperar los datos, de tal como se muestra a continuación, y haga clic en **Aceptar**. Puede recuperar la dirección URL en el cuadro URI de la hoja **Claves** del Portal de Azure o bien puede usar la información de la cuenta de demostración proporcionada anteriormente. Para obtener más información, consulte [Claves](documentdb-manage-account.md#keys).


	*Nota. En este tutorial, no especificaremos el nombre de la base de datos, el nombre de la colección o una instrucción SQL, ya que estos campos son opcionales. En su lugar, se usará el navegador para seleccionar la base de datos y la colección para identificar la procedencia de los datos.*

    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Ventana de conexión de Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiconnectwindow.png)

7. Si se conecta a este punto de conexión por primera vez, se le pedirá la clave de cuenta. Escriba la clave de cuenta y haga clic en **Conectar**.
	
	*Nota. Se recomienda usar la clave de solo lectura al generar informes. De esta forma, evitará una exposición innecesaria de la clave maestra a posibles riesgos de seguridad. La clave de solo lectura está disponible en la hoja de claves de solo lectura del Portal de Azure o puede usar la información de la cuenta de demostración proporcionada anteriormente.*

    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Clave de cuenta](./media/documentdb-powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)

8. Cuando la cuenta está conectada correctamente, se mostrará el **navegador**. El **navegador** mostrará una lista de bases de datos en la cuenta.
9. Haga clic y expanda la base de datos de donde procederán los datos del informe. Se mostrará una lista de colecciones en la base de datos.  

10. Ahora, seleccione una colección desde la que va a recuperar los datos, por ejemplo, volcano1.

	*Nota. El panel de vista previa muestra una lista de elementos **Registro**. Un documento se representa como un tipo **Registro** en Power BI. De forma similar, un bloque JSON anidado dentro de un documento es también un **registro**.*

    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Ventana del navegador](./media/documentdb-powerbi-visualize/power_bi_connector_pbinavigator.png)

11. Haga clic en **Editar** para iniciar el Editor de consultas, para que podamos transformar los datos.

## Eliminación de formato y transformación de documentos JSON
1. En el Editor de consultas de Power BI, debería ver la columna **Documento** en el panel central. ![Editor de consultas de Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditor.png)

2. Haga clic en el botón de expansión en el lado derecho del encabezado de columna **Documento**. Se muestra el menú contextual con una lista de campos. Seleccione los campos necesarios para el informe, por ejemplo, nombre de volcán, país, región, ubicación, elevación, tipo, estado y última erupción conocida y, después, haga clic en **Aceptar**.
    
	![Tutorial de Power BI para conector de Power BI de DocumentDB - Expandir Documentos](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)

3. El panel central mostrará una vista previa del resultado con los campos seleccionados.

	![Tutorial de Power BI para conector de Power BI de DocumentDB - Resultados sin formato](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflatten.png)

4. En nuestro ejemplo, la propiedad Ubicación es un bloque de GeoJSON en un documento. Como puede ver, la ubicación se representa como un tipo **Registro** en Power BI Desktop.
5. Haga clic en el botón de expansión en el lado derecho del encabezado de columna Ubicación. Aparecerá el menú contextual con los campos de tipo y coordenadas. Ahora seleccione el campo de coordenadas y haga clic en **Aceptar**.

    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Registro de ubicación](./media/documentdb-powerbi-visualize/power_bi_connector_pbilocationrecord.png)

6. El panel central muestra ahora una columna de coordenadas de tipo **lista**. Como se ha indicado al comienzo del tutorial, los datos de GeoJSON son de tipo Point con los valores de latitud y longitud registrados en la matriz de coordenadas.

	*Nota: el elemento coordinates[0] representa la longitud mientras que coordinates[1] representa la latitud.* ![Tutorial de Power BI para conector de Power BI de DocumentDB - Lista de coordenadas](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)

7. Para eliminar el formato de la matriz de coordenadas, crearemos una **columna personalizada** llamada LatLong. Seleccione la cinta de opciones **Agregar columna** y haga clic en **Agregar columna personalizada**. La ventana **Agregar columna personalizada** debería aparecer .

8. Dele un nombre a la nueva columna, por ejemplo, LatLong.

9. Después, especifique la fórmula personalizada para la nueva columna. En nuestro ejemplo, concatenaremos los valores de latitud y longitud separados por comas, como se muestra a continuación mediante la siguiente fórmula: Text.From([coordinates]{1})&","&Text.From([coordinates]{0}). Haga clic en **Aceptar**.
	
    *Nota. Para obtener más información sobre el lenguaje de expresiones de análisis de datos (DAX) incluidas las funciones DAX, consulte el artículo [DAX Basic in Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop) (Aspectos básicos de DAX en Power BI Desktop).*

    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Agregar columna personalizada](./media/documentdb-powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Ahora, el panel central mostrará la nueva columna LatLong rellena con los valores de latitud y longitud separados por una coma.

	![Tutorial de Power BI para conector de Power BI de DocumentDB - Columna LatLong personalizada](./media/documentdb-powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)

11. Hemos finalizado la eliminación del formato de los datos en formato tabular. Puede aprovechar todas las características disponibles en el Editor de consultas para dar forma y transformar los datos de DocumentDB. Por ejemplo, puede cambiar el tipo de datos para elevación a **número decimal** mediante el cambio del **tipo de datos** en la cinta de opciones **Inicio**.

    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Cambiar tipo de columna](./media/documentdb-powerbi-visualize/power_bi_connector_pbichangetype.png)

12. Haga clic en **Cerrar y aplicar** para guardar el modelo de datos.
    
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Cerrar y aplicar](./media/documentdb-powerbi-visualize/power_bi_connector_pbicloseapply.png)

## Generación de informes
La vista de informe de Power BI Desktop es donde puede empezar a crear informes para visualizar datos. Puede crear informes arrastrando y soltando campos en el lienzo del **informe**.

![Vista de informes de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview2.png)
 
En la vista de informe, debe buscar:

 1. El panel **Campos**, que es donde verá una lista de los modelos de datos con campos que puede usar para los informes.

 2. El panel **Visualizaciones**. Un informe puede contener una o varias visualizaciones. Seleccione los tipos visuales que se ajusten a sus necesidades en el panel **Visualizaciones**.

 3. El lienzo **Informe**, donde creará los objetos visuales para el informe.

 4. La página **Informe**. Puede agregar varias páginas de informes en Power BI Desktop.

A continuación se muestran los pasos básicos para crear un sencillo informe de la vista de mapa interactivo.

1. En nuestro ejemplo, crearemos una vista del mapa que muestra la ubicación de cada volcán. En el panel de **visualizaciones**, haga clic en el tipo de asignación visual como marcadas en la captura de pantalla anterior. Debería ver el tipo de mapa visual pintado en el lienzo **Informe**. El panel de **visualizaciones** también debe mostrar un conjunto de propiedades relacionadas con el tipo de mapa visual.

2. Ahora, arrastre y coloque el campo LatLong desde el panel de **campos** a la **Ubicación** del panel de **visualizaciones**.
3. A continuación, arrastre y coloque el campo del nombre del volcán en la propiedad **Leyenda**.  

4. A continuación, arrastre y coloque el campo de elevación en la propiedad **Valores**.

5. Ahora debería ver el mapa visual que muestra un conjunto de burbujas que indican la ubicación de cada volcán, con el tamaño de la burbuja correlacionado con la elevación del volcán.

6. Ahora ha creado un informe básico. Puede personalizar aún más el informe agregando más visualizaciones. En nuestro caso, hemos agregado una segmentación del tipo de volcán para que el informe sea interactivo.

    ![Captura de pantalla del informe final de Power BI Desktop tras la finalización del tutorial de Power BI para DocumentDB](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

## Publicación y uso compartido del informe
Para compartir el informe, debe tener una cuenta en PowerBI.com.

1. En Power BI Desktop, haga clic en la cinta de opciones de **inicio**.
2. Haga clic en **Publicar**. Se le pedirá que escriba el nombre de usuario y la contraseña de su cuenta de PowerBI.com.
3. Una vez autenticada la credencial, el informe se publica en su cuenta de PowerBI.com.
4. A continuación, puede compartir el informe en PowerBI.com.

## Pasos siguientes
- Haga clic [aquí](https://support.powerbi.com/knowledgebase) para obtener más información sobre Power BI.
- Para obtener más información sobre DocumentDB, haga clic [aquí](https://azure.microsoft.com/documentation/services/documentdb/).

<!---HONumber=AcomDC_0302_2016-->