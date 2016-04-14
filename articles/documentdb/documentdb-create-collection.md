<properties 
	pageTitle="Crear una colección de base de datos de DocumentDB | Microsoft Azure" 
	description="Aprenda a crear colecciones de documentos JSON mediante el portal de servicios en línea para Azure DocumentDB, una base de datos de documentos NoSQL basada en la nube. Obtenga una versión de evaluación gratuita hoy mismo." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/22/2016" 
	ms.author="mimig"/>

# Creación de una colección de DocumentDB mediante el Portal de Azure

Para usar Microsoft Azure DocumentDB, debe tener una [cuenta de DocumentDB](documentdb-create-account.md), una [base de datos](documentdb-create-database.md), una colección y documentos. En este tema se describe cómo crear una colección de DocumentDB en el Portal de Azure.

¿No está seguro de lo que es una colección? Vea [¿Qué es una colección de DocumentDB?](#what-is-a-documentdb-collection)

1.  En la barra de accesos directos del [Portal de Azure](https://portal.azure.com/), haga clic en **Cuentas de DocumentDB**. Si **Cuentas de DocumentDB** no está visible, haga clic en **Examinar** y después en **Cuentas de DocumentDB**.

    ![Captura de pantalla con la opción Cuentas de DocumentDB de la barra de accesos directos, la cuenta en la hoja Cuentas de DocumentDB y la base de datos en la hoja de la cuenta de DocumentDB, en la lente Bases de datos, resaltados](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

2.  En la hoja **Cuentas de DocumentDB**, seleccione la cuenta en la que desea agregar una colección. Si no se muestra ninguna cuenta, deberá [crear una cuenta de DocumentDB](documentdb-create-account.md).

3. En la hoja **Cuenta de DocumentDB** de la cuenta seleccionada, desplácese hacia abajo hasta la lente **Bases de datos** y seleccione la base de datos en la que se va a agregar una colección.

    ![Captura de pantalla con la opción Cuentas de DocumentDB de la barra de accesos directos, la cuenta en la hoja Cuentas de DocumentDB y la base de datos en la hoja de la cuenta de DocumentDB, en la lente Bases de datos, resaltados](./media/documentdb-create-collection/docdb-database-creation-3.png)

4. En la hoja **Base de datos**, haga clic en **Agregar colecciones**.

	![Captura de pantalla con el botón Agregar colección de la hoja Base de datos, la configuración de la hoja Agregar colección y el botón Aceptar resaltados - Portal de Azure para DocumentDB - Creador de bases de datos basadas en la nube para bases de datos JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-4.png)

5. En la hoja **Agregar colección**, en el cuadro **Identificador**, escriba el identificador de la nueva colección. Los nombres de colección deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final. Cuando se valida el nombre, aparece una marca de verificación verde en el cuadro Id.

	![Captura de pantalla con el botón Agregar colección de la hoja Base de datos, la configuración de la hoja Agregar colección y el botón Aceptar resaltados - Portal de Azure para DocumentDB - Creador de bases de datos basadas en la nube para bases de datos JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)

6. Seleccione un nivel de precios para la nueva colección. Cada colección creada es una entidad facturable. Para obtener más información sobre los niveles de rendimiento disponibles, consulte [Niveles de rendimiento en DocumentDB](documentdb-performance-levels.md).

7. Seleccione una de las siguientes **Directivas de indexación**.

	- **Predeterminada**. Esta directiva usa indexación hash para cadenas e indexación de intervalo para números. Es mejor para consultas de igualdad en cadenas, ORDER BY e intervalo, y consultas de igualdad en números. Esta directiva tiene una menor sobrecarga de almacenamiento de índice e incluye indexación geoespacial.
	- **Hash**. Esta directiva es mejor si está usando consultas de ORDER BY, intervalo e igualdad en números y cadenas. Esta directiva tiene una mayor sobrecarga de almacenamiento de índice que **Predeterminada** e incluye indexación geoespacial.

	Para obtener más información sobre las directivas de indexación, consulte [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md).

8. Haga clic en **Aceptar** en la parte inferior de la pantalla para crear la nueva colección.


9. La nueva colección aparece ahora en el modo **Colecciones** en la hoja **Base de datos**.
 
	![Captura de pantalla de la nueva colección en la hoja Base de datos - Portal de Azure para DocumentDB - Creador de bases de datos basadas en la nube para bases de datos JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-9.png)

## ¿Qué es una colección de DocumentDB? 

Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Una colección es una entidad facturable, donde el [costo](documentdb-performance-levels.md) está determinado por el nivel de rendimiento asociado a la colección.

Las colecciones son el límite de la transacción para los desencadenadores y procedimientos almacenados, además de ser el punto de entrada para las consultas y las operaciones CRUD. Cada colección tiene una cantidad reservada de capacidad de proceso específica para esa colección, que no se comparte con otras colecciones de la misma cuenta. Por lo tanto, puede escalar horizontalmente la aplicación, tanto en términos de almacenamiento como de capacidad de proceso, mediante la adición de más colecciones y la posterior distribución de documentos en ellas.

Las colecciones son distintas de las tablas incluidas en las bases de datos relacionales. Las colecciones no imponen un esquema, de hecho, DocumentDB no impone ningún esquema, sino que es una base de datos sin esquemas. Por lo tanto, se pueden almacenar distintos tipos de documentos con esquemas diferentes en la misma colección. Puede optar por utilizar colecciones para almacenar objetos de un solo tipo, como se haría con las tablas. El mejor modelo depende solo de la forma en que aparezcan los datos juntos en las consultas y las transacciones.

## Otras formas de crear una colección de DocumentDB

No es necesario que las colecciones se creen en el portal, también se pueden crear mediante los [SDK de DocumentDB](documentdb-sdk-dotnet.md). Para los ejemplos de código C# que muestran cómo trabajar con colecciones mediante el SDK de .NET de DocumentDB, consulte la sección [Ejemplos de colección de C#](documentdb-dotnet-samples.md#collection-examples). Para los ejemplos de código Node.js que muestran cómo trabajar con colecciones mediante el SDK de Node.js de DocumentDB, consulte la sección [Ejemplos de colección de C#](documentdb-nodejs-samples.md#collection-examples).

## Solución de problemas

Si la opción **Agregar colección** está deshabilitada en el portal de Azure, significa que la cuenta está deshabilitada, lo que normalmente se produce cuando se utilizan todos los créditos de beneficios del mes.

## Pasos siguientes

Ahora que tiene una colección, el paso siguiente es agregar o importar documentos a la colección. Cuando se trata de agregar documentos a una colección, tiene varias posibilidades:

- Puede [agregar documentos](documentdb-view-json-document-explorer.md) mediante el Explorador de documentos en el Portal.
- También puede [importar documentos y datos](documentdb-import-data.md) mediante la Herramienta de migración de datos de DocumentDB, que le permite importar archivos CSV y JSON, así como datos de SQL Server, MongoDB, almacenamiento de tablas de Azure y otras colecciones de DocumentDB. 
- También puede agregar documentos con uno de los [SDK de DocumentDB](documentdb-sdk-dotnet.md). DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript. Para los ejemplos de código C# que muestran cómo trabajar con documentos mediante el SDK .NET de DocumentDB, consulte la sección [Ejemplos de colección de C#](documentdb-dotnet-samples.md#document-examples). Para los ejemplos de código Node.js que muestran cómo trabajar con documentos mediante el SDK de Node.js de DocumentDB, consulte la sección [Ejemplos de documentos de Node.js](documentdb-nodejs-samples.md#document-examples).

Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#executing-queries) en sus documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) del portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](documentdb-sdk-dotnet.md).

<!---HONumber=AcomDC_0224_2016-->