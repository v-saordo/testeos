<properties
	pageTitle="Explorador de consultas de DocumentDB: un editor de consultas SQL | Microsoft Azure"
	description="Obtenga información sobre el Explorador de consultas de DocumentDB, un Editor de consultas de SQL del Portal de Azure para escribir consultas SQL y ejecutarlas en una colección de DocumentDB NoSQL."
	keywords="escribir consultas sql, editor de consultas de sql"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/23/2016"
	ms.author="anhoh"/>

# Escritura, edición y ejecución de consultas SQL para DocumentDB mediante Explorador de consultas 

En este artículo se proporciona información general sobre el Explorador de consultas de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), que es una herramienta del Portal de Azure que permite escribir, editar y ejecutar consultas SQL en una [colección de DocumentDB](documentdb-create-collection).

1. En la barra de salto del Portal de Azure, haga clic en **Cuentas de DocumentDB**. Si **Cuentas de DocumentDB** no está visible, haga clic en **Examinar** y después en **Cuentas de DocumentDB**.

2. En la parte superior de la hoja **Cuenta de DocumentDB**, haga clic en **Explorador de consultas**.

	![Captura de pantalla del Portal de Azure con el Explorador de consultas resaltado](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)

    >[AZURE.NOTE] El Explorador de consultas también aparece en las hojas de bases de datos y colecciones.

3. En la hoja **Explorador de consultas**, seleccione las **bases de datos** y **colecciones** en que desee realizar consultas en las listas desplegables y especifique la consulta que desee ejecutar.

    Las listas desplegables **Bases de datos** y **Colecciones** se rellenan previamente según el contexto en el que se inicie el Explorador de consultas.

    Se proporciona la consulta predeterminada `SELECT TOP 100 * FROM c`. Puede aceptar la consulta predeterminada o crear su propia consulta con el lenguaje de consulta SQL que se describe en el [hoja de referencia rápida de consulta SQL](documentdb-sql-query-cheat-sheet.md) o en el artículo sobre [consulta SQL y sintaxis SQL](documentdb-sql-query.md).

    Haga clic en **Ejecutar consulta** para ver los resultados.

	![Captura de pantalla de escritura de consultas SQL en el Explorador de consultas, un Editor de consultas de SQL](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

4. La hoja **Resultados** hoja muestra el resultado de la consulta.

	![Captura de pantalla de resultados de la escritura de consultas SQL en el Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## Trabajo con resultados

De forma predeterminada, el Explorador de consultas devuelve resultados en grupos de 100. Si la consulta genera más de 100 resultados, utilice los comandos **Página siguiente** y **Página anterior** para desplazarse por el conjunto de resultados.

![Captura de pantalla de la compatibilidad con la paginación del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

En las consultas correctas, el panel **Información** contiene métricas como el gasto de la solicitud, el número de recorridos de ida y vuelta que realizó la consulta, el conjunto de resultados que se muestra actualmente y si hay más resultados, a los que se puede acceder mediante el comando **Página siguiente**, tal como se indicó anteriormente.

![Captura de pantalla de información de consultas del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## Uso de múltiples consultas

Si usa varias consultas y desea conmutar cambiar rápidamente de unas a otras, puede especificarlas todas en el cuadro de texto de consulta de la hoja **Explorador de consultas**, luego resaltar la que desee ejecutar y, finalmente, haga clic en **Ejecutar consulta** para ver los resultados.

![Captura de pantalla de la escritura de varias consultas SQL en el Explorador de consultas (un Editor de consultas de SQL) y el resaltado y ejecución de consultas individuales](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## Adición de consultas desde un archivo en el Editor de consultas de SQL

Puede cargar el contenido de un archivo existente con el comando **Cargar archivo** .

![Captura de pantalla que muestra cómo cargar consultas SQL desde un archivo en el Explorador de consultas mediante Cargar archivo](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## Solución de problemas

Si una consulta se completa con errores, el Explorador de consultas muestra una lista de errores que puede ser útil a la hora de solucionar los problemas.

![Captura de pantalla de errores de consulta del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryerror.png)

## Ejecución de consultas SQL de DocumentDB fuera del portal

El Explorador de consultas del Portal de Azure es simplemente una forma de ejecutar consultas SQL en DocumentDB. Las consultas SQL también se pueden ejecutar mediante la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o los [SDK de cliente](documentdb-sdk-dotnet.md). Para más información sobre el uso de estos otros métodos, consulte [Executing SQL queries](documentdb-sql-query.md#executing-sql-queries) (Ejecución de consultas SQL)

## Pasos siguientes

Para más información acerca de la gramática de SQL de DocumentDB compatible con el Explorador de consultas, consulte el artículo sobre [consulta SQL y sintaxis SQL](documentdb-sql-query.md) o imprima la [hoja de referencia rápida de consultas SQL](documentdb-sql-query-cheat-sheet.md). También puede disfrutar de la experimentación con [Query Playground](https://www.documentdb.com/sql/demo), donde puede probar las consultas en línea con un conjunto de datos de ejemplo.

<!---HONumber=AcomDC_0224_2016-->