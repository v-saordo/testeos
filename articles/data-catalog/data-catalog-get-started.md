<properties
   pageTitle="Catálogo de datos de Azure Introducción al catálogo de datos de Azure"
   description="Tutorial integral de los escenarios y las capacidades del Catálogo de datos de Azure."
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/03/2016"
   ms.author="derrickv"/>

# Introducción al Catálogo de datos de Azure

Este artículo es un tutorial integral de los escenarios y las funcionalidades de la versión preliminar pública del **Catálogo de datos de Azure**. Cuando se haya registrado para la versión preliminar, siga estos pasos para crear un Catálogo de datos y registrar, anotar y detectar los orígenes de datos.

## Requisitos previos de tutoriales

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**: si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Consulte [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) para más detalles.
- **Azure Active Directory**: Catálogo de datos de Azure usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para la administración de identidades y acceso.
- **Orígenes de datos**: el Catálogo de datos de Azure tiene funciones para detectar los orígenes de datos. Este tutorial usa la base de datos de ejemplo Adventure Works, pero puede usar cualquier origen de datos admitido si prefiere trabajar con datos que conozca y sean y pertinentes para su rol. Consulte [Orígenes de datos compatibles con el Catálogo de datos de Azure](data-catalog-dsr.md) para ver una lista de los orígenes de datos admitidos.

Para empezar, se instalará la base de datos de ejemplo Adventure Works.

## Ejercicio 1: Instalación de una base de datos de ejemplo Adventure Works

En este ejercicio instalará el ejemplo Adventure Works para el motor de base de datos de SQL Server que se usará en los siguientes ejercicios.

### Instalación de la base de datos Adventure Works 2014 OLTP

La base de datos Adventure Works admite escenarios de procesamiento de transacciones en línea estándar para un fabricante de bicicletas ficticio (Adventure Works Cycles), que incluye productos, ventas y compras. En este tutorial registrará información acerca de los productos en el **Catálogo de datos de Azure**.

A continuación se muestra cómo instalar la base de datos de ejemplo Adventure Works.

Para instalar la base de datos de ejemplo Adventure Works, puede restaurar una copia de seguridad de AdventureWorks2014, que se encuentra en [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) en CodePlex. Una forma de restaurar la base de datos es ejecutar un script de T-SQL en SQL Server Management Studio.

**Instalación de una base de datos de ejemplo Adventure Works con un script de T-SQL**

1.	Cree una carpeta denominada C:\\DataCatalog\_GetStarted. Si usa otro nombre de carpeta, asegúrese de cambiar la ruta de acceso en el siguiente script de T-SQL.
2.	Descargue [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661).
3.	Extraiga Adventure Works 2014 Full Database Backup.zip en C:\\DataCatalog\_GetStarted. En el script siguiente se considera que el archivo de copia de seguridad se encuentra en C:\\DataCatalog\_GetStarted\\Adventure Works 2014 Full Database Backup\\AdventureWorks2014.bak.
4.	Desde **SQL Server Management Studio**, en la barra de herramientas **Estándar**, haga clic en **Nueva consulta**.
5.	Ejecute el siguiente código de T-SQL en la ventana de consulta.

**Ejecución de este script para instalar la base de datos Adventure Works 2014**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
    	-- AdventureWorks2014.bak file location
    	FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

    	-- AdventureWorks2014.mdf database location
    	WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

    	-- AdventureWorks2014.ldf log location
    	MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

Como alternativa a ejecutar un script de T-SQL, puede restaurar la base de datos con SQL Server Management Studio. Consulte [Restaurar una copia de seguridad de base de datos (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx).

En este ejercicio ha instalado la base de datos de ejemplo Adventure Works que se usará en los demás ejercicios. En el siguiente ejercicio, aprenderá cómo registrar orígenes de datos del **Catálogo de datos de Azure** procedentes de tablas en la base de datos de ejemplo Adventure Works.

## Ejercicio 2: Registro de orígenes de datos

En este ejercicio usará la herramienta de registro del **Catálogo de datos de Azure** para registrar los orígenes de datos con el catálogo. Registro es el proceso de extraer metadatos estructurales clave (como nombres, tipos y ubicaciones) del origen de datos y los recursos que contiene y copiar esos metadatos en el catálogo. Los orígenes de datos y sus datos permanecen donde están, pero el catálogo usa los metadatos para que se puedan detectar y comprender más fácilmente.

### A continuación se muestra cómo registrar un origen de datos

1.	Vaya a https://azure.microsoft.com/services/data-catalog y haga clic en **Iniciado**.
2.	Inicie sesión en el portal del **Catálogo de datos de Azure** y haga clic en **Publicar datos**.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Haga clic en **Iniciar aplicación**.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. En la página **Bienvenida**, haga clic en **Iniciar sesión** y escriba sus credenciales.
5. En la página **Catálogo de datos de Microsoft Azure**, haga doble clic en **SQL Server**, o bien haga clic en **SQL Server** y **Siguiente**.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Especifique las propiedades de conexión de SQL Server para AdventureWorks2014 (consulte el ejemplo siguiente) y haga clic en **CONECTAR**.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	En la página siguiente es donde se registran los metadatos de su origen de datos. En este ejemplo, registrará los objetos de **Production/Product** desde el espacio de nombres de producción de AdventureWorks. Aquí se muestra cómo hacerlo:

    a. En el árbol **Jerarquía de servidor**, haga clic en **Production**.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Ctrl+haga clic en Producto, ProductCategory, ProductDescription y ProductPhoto.

    c. Haga clic en la flecha de selección de movimiento (**>**). Esto moverá todos los objetos de Product seleccionados a la lista **Objetos que se registrarán**.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. En **Agregar etiquetas**, introduzca una descripción, foto. De este modo se agregarán etiquetas de búsqueda para estos recursos de datos. Las etiquetas son una excelente manera de ayudar a los usuarios buscar un origen de datos registrados.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e. **Opcional**: puede **Incluir una vista previa** y **Agregar un experto de origen de datos**.

    f. Haga clic en **REGISTRAR**. El **Catálogo de datos de Azure** registra los objetos seleccionados. En este ejercicio, se registran los objetos seleccionados de Adventure Works.

    g. Para ver los objetos de origen de datos registrados, haga clic en **Ver portal**. En el portal del **Catálogo de datos de Azure**, puede ver los objetos del origen de datos en **Iconos** o en una **Lista**.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

En este ejercicio se han registrado objetos de la base de datos de ejemplo Adventure Works para que puedan ser detectados fácilmente por los usuarios de su organización. En el siguiente ejercicio aprenderá a detectar los activos de datos registrados.

## Ejercicio 3: Detección de recursos de datos registrados

En este ejercicio usará el portal del **Catálogo de datos de Azure** para detectar recursos de datos registrados y ver sus metadatos. El **Catálogo de datos de Azure** proporciona varias herramientas para detectar recursos de datos, incluida la búsqueda simple de palabras clave, filtros interactivos y una sintaxis de búsqueda avanzada para usuarios avanzados.

### A continuación se indica cómo descubrir recursos de datos registrados

El **Catálogo de datos de Azure** tiene una sintaxis de búsqueda sencilla pero eficaz que le permite crear fácilmente consultas que devuelven los datos que los usuarios necesitan. Para ver más detalles acerca de la búsqueda del **Catálogo de datos de Azure**, consulte [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (Referencia de la sintaxis de búsqueda).

**Catálogo de datos de Azure** tiene las siguientes opciones de búsqueda:

- Búsqueda de palabra clave
- Filtrar
- Búsqueda avanzada

También puede refinar los activos de datos que desea ver. **Catálogo de datos de Azure** tiene las siguientes opciones de vista:

- Ver propiedades
- Ver columnas
- Ver vista previa

En este ejemplo, usará una búsqueda de palabras clave. La búsqueda de **Catálogo de datos de Azure** tiene varias técnicas de consulta. Este ejemplo usará una consulta de búsqueda de **agrupación**.

**Técnicas de consulta**

|Técnica|Uso|Ejemplo
|---|---|---
|Ámbito de propiedad|Devuelve solamente los orígenes de datos en los que el término de búsqueda coincide con la propiedad especificada|name:product
|Operadores lógicos|Amplia o reduce una búsqueda mediante operaciones booleanas, como se describe en la sección de operadores booleanos de esta página|finance NOT corporate
|Agrupación con paréntesis|Use paréntesis para agrupar partes de la consulta y así conseguir aislamiento lógico, especialmente en combinación con los operadores booleanos|name:product AND (tags:illustration OR tags:photo)
|Operadores de comparación|Use comparaciones distintas de la igualdad de propiedades que tengan tipos de datos numéricos y de fechas|creationTime:>11/05/14

En este ejemplo, se hace una búsqueda de **agrupación** de recursos de datos en las que name equivale a product y tags equivale a illustration o tags equivale a photo.

1. Vaya a https://azure.microsoft.com/services/data-catalog, haga clic en **Iniciado** e inicie sesión en el portal del **Catálogo de datos de Azure**.
2. En el cuadro **Buscar Catálogo de datos**, escriba una **Agrupación** como query: (tags:description OR tags:photo).
3. Haga clic en el icono de búsqueda o presione ENTRAR. **Catálogo de datos de Azure** mostrará los recursos de datos de esta consulta de búsqueda.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

En este ejercicio ha usado el portal del **Catálogo de datos de Azure** para detectar y ver los recursos de datos de Adventure Works registrados con el catálogo.

<a name="annotating"/>
## Ejercicio 4: Anotación de orígenes de datos registrados

En este ejercicio usará el portal del **Catálogo de datos de Azure** para anotar los recursos de datos que se han registrado anteriormente en el catálogo. Las anotaciones que proporcione complementarán y mejorarán los metadatos estructurales extraídos del origen de datos durante el registro y hará que los recursos de datos sean mucho más fáciles de detectar y entender. Dado que cada usuario del **Catálogo de datos** puede proporcionar sus propias anotaciones, es fácil para todos los usuarios que tengan una perspectiva de los datos compartirlos.

### A continuación se indica cómo agregar anotaciones a los activos de datos

1. Vaya a https://azure.microsoft.com/services/data-catalog, haga clic en **Iniciado** e inicie sesión en el portal del **Catálogo de datos de Azure**.
2. Haga clic en **Detectar**.
3. Elija uno o varios **recursos de datos**. En este ejemplo, elija **ProductPhoto** y escriba "Fotos de productos para materiales de marketing".
4. Escriba una **Descripción** que ayude a otros usuarios a descubrir y entender por qué y cómo usar el recurso de datos seleccionado. Por ejemplo, escriba "Imágenes de producto". También puede agregar más etiquetas y ver columnas.
5. Ahora puede intentar buscar y filtrar para detectar recursos de datos mediante los metadatos descriptivos que haya agregado al catálogo.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

En este ejercicio ha agregado información descriptiva a los recursos de datos registrados para que los usuarios del catálogo puedan detectar el origen de datos con términos que puedan entender.

## Ejercicio 5: Metadatos de micromecenazgo

En este ejercicio trabajará con otro usuario para agregar metadatos a los recursos de datos del catálogo. El enfoque de micromecenazgo del **Catálogo de datos de Azure** para las anotaciones permite a cualquier usuario agregar etiquetas, descripciones y otros metadatos para que cualquier usuario con una perspectiva de un recurso de datos y su uso pueda hacer que esa perspectiva se capture y esté disponible para otros usuarios.

> [AZURE.NOTE] Si no dispone de otro usuario con el que trabajar en este tutorial, no se preocupe. Los usuarios que acceden al catálogo de datos pueden agregar su propio punto de vista cuando lo deseen. Este enfoque de micromecenazgo de los metadatos permite que el contenido del catálogo y la riqueza de los metadatos del catálogo crezcan con el tiempo.

### A continuación se indica cómo puede aplicar micromecenazgo a los metadatos sobre recursos de datos

Solicite a un compañero que repita el ejercicio [Anotación de los orígenes de datos registrados](#annotating) anterior. Una vez que su colega agregue descripciones a un recurso de datos, como ProductPhoto, verá varias anotaciones.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

En este ejercicio se han explorado las funcionalidades del **Catálogo de datos de Azure** para los metadatos de micromecenazgo, donde cualquier usuario del catálogo puede anotar los recursos de datos que detecta.

## Ejercicio 6: Conectarse a orígenes de datos

En este ejercicio usará el portal del **Catálogo de datos de Azure** para conectarse a orígenes de datos con Microsoft Excel.

> [AZURE.NOTE] Es importante recordar que **Catálogo de datos de Azure** no da a los usuarios acceso al origen de datos en sí: simplemente facilita a los usuarios detectarlos y comprenderlos. Cuando los usuarios se conectan a un origen de datos, la aplicación cliente que eligen usará sus credenciales de Windows o les pedirán las credenciales según sea necesario. Si no se ha concedido acceso al origen de datos previamente al usuario, deberá concederse acceso a este para que pueda conectarse.

### A continuación se indica cómo conectarse a un origen de datos desde Excel

1. Vaya a https://azure.microsoft.com/services/data-catalog, haga clic en **Iniciado** e inicie sesión en el portal del **Catálogo de datos de Azure**.
2. Haga clic en **Detectar**.
3. Elija un recurso de datos. En este ejemplo, elija ProductCategory.
4. Elija **Abrir en** > **Excel**.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. En la ventana **Aviso de seguridad de Microsoft Excel**, haga clic en **Habilitar**.
6. Abra el archivo **ProductCategory.odc**.
7. El origen de datos se abre en Excel.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

En este ejercicio se ha conectado a orígenes de datos detectados mediante el **Catálogo de datos de Azure**. El portal **Catálogo de datos de Azure** permite a los usuarios conectarse directamente con las aplicaciones de cliente integradas en su menú **Abrir en...**, y permite a los usuarios conectarse con cualquier aplicación que elijan usando la información de ubicación de conexión incluida en los metadatos de los recursos.

## Ejercicio 7: Quitar los metadatos del origen de datos

En este ejercicio usará el portal del **Catálogo de datos de Azure** para quitar los datos de vista previa de los recursos de datos registrados y para eliminar los recursos de datos del catálogo.

> [AZURE.NOTE] El comportamiento predeterminado del catálogo es permitir que cualquier usuario registre cualquier origen de datos y permitir que cualquier usuario elimine cualquier recurso de datos que se haya registrado. Las capacidades de administración incluidas en la **edición Standard del Catálogo de datos de Azure** proporcionan opciones adicionales para la toma de posesión de los recursos y la restricción de quién puede detectar y eliminar los recursos.

En el **Catálogo de datos de Azure** puede eliminar un solo recurso o varios.

### A continuación se muestra cómo eliminar varios recursos de datos

1. Vaya a https://azure.microsoft.com/services/data-catalog, haga clic en **Iniciado** e inicie sesión en el portal del **Catálogo de datos de Azure**.
2. Haga clic en **Detectar**.
3. Elija uno o varios recursos de datos.
4. Hacer clic en **Eliminar**.

En este ejercicio ha eliminado recursos de datos registrados del catálogo.

## Ejercicio 8: Administración de orígenes de datos registrados

En este ejercicio usará las funcionalidades de administración del **Catálogo de datos de Azure** para tomar posesión de los recursos de datos y controlar qué usuarios pueden detectar y administrar esos recursos.

> [AZURE.NOTE] Las funcionalidades de administración descritas en este ejercicio solo están disponibles en la **Edición estándar del Catálogo de datos de Azure**, no en la **Edición gratuita**. En **Catálogo de datos de Azure**, puede tomar posesión de los recursos de datos, agregar copropietarios a los recursos de datos y establecer la visibilidad de los recursos de datos.

### A continuación se muestra cómo tomar posesión de los recursos de datos y restringir la visibilidad

1. Vaya a https://azure.microsoft.com/services/data-catalog, haga clic en **Iniciado** e inicie sesión en el portal del **Catálogo de datos de Azure**.
2. Haga clic en **Detectar**.
3. Elija uno o varios recursos de datos.
4. En el panel **Propiedades**, sección **Administración**, haga clic en **Tomar posesión**.
5. Para restringir la visibilidad, haga clic en **Propietarios y estos usuarios**.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

En este ejercicio ha explorado las funcionalidades de administración del **Catálogo de datos de Azure** y restringido la visibilidad en recursos de datos seleccionados.

## Resumen

En este tutorial ha explorado las funcionalidades esenciales de la versión preliminar del **Catálogo de datos de Azure**, incluido el registro, la anotación, la detección y la administración de orígenes de datos empresariales. Ahora que ha completado el tutorial, ha llegado el momento de comenzar. Puede empezar hoy mismo por registrar los orígenes de datos en los que confían usted y su equipo, e invitando a compañeros a usar el catálogo.

<!---HONumber=AcomDC_0309_2016-->