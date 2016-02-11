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
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/20/2015"
   ms.author="derrickv"/>

# Introducción al Catálogo de datos de Azure

Este artículo es un tutorial integral de los escenarios y las capacidades de la versión preliminar pública de Catálogo de datos de Azure. Cuando se haya registrado para la vista previa, puede seguir estos pasos para crear un catálogo de datos y registrar, anotar y detectar los orígenes de datos.

## Requisitos previos de tutoriales

Antes de empezar este tutorial, debe contar con lo siguiente:

-	**Una suscripción de Azure**: si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Consulte el artículo [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) para obtener información.
-	**Azure Active Directory**: Catálogo de datos de Azure usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para la administración de identidades y acceso.
-	**Orígenes de datos**: Catálogo de datos de Azure proporciona capacidades de detección de orígenes de datos, y para continuar con el tutorial debe tener acceso a uno o más orígenes de datos. El tutorial se escribe con las bases de datos de ejemplo de Adventure Works, pero puede usar cualquier origen de datos admitido si prefiere trabajar con datos que sean familiares y relevantes para su rol.

## Ejercicio 1: Instalación de una base de datos de ejemplo Adventure Works

En este ejercicio, instale el ejemplo de Adventure Works para el Motor de base de datos de SQL Server y SQL Server Analysis Services multidimensionales. Estos ejemplos se usan en los ejercicios siguientes.

> [AZURE.NOTE] Este ejercicio es opcional. Los ejercicios restantes del tutorial se escriben para hacer referencia a las bases de datos de ejemplo de Adventure Works, pero también puede omitir este ejercicio y trabajar con sus propios orígenes de datos en su lugar. A continuación se facilitan los para instalar Adventure Works.

### Instalar las bases de datos de Adventure Works 2014 OLTP y de Almacenamiento de datos

La base de datos OLTP de Adventure Works admite escenarios de procesamiento de transacciones en línea estándar para un fabricante de bicicletas ficticio (Adventure Works Cycles) que incluye fabricación, ventas y compras. La base de datos de Adventure Works DW muestra cómo crear un almacenamiento de datos.

Las bases de datos se encuentran en [CodePlex.com](http://msftdbprodsamples.codeplex.com/) y pueden instalarse siguiendo los pasos descritos en la sección [Archivo Léame de bases de datos de ejemplo de Adventure Works 2014](https://msftdbprodsamples.codeplex.com/downloads/get/880669).

En este ejercicio se instalan las bases de datos de ejemplo de Adventure Works que se usan en los ejercicios restantes. Si decide omitir este ejercicio y usar sus propios orígenes de datos empresariales, esté preparado para recordar los nombres, etiquetas y otros metadatos.

## Ejercicio 2: Registro de orígenes de datos

En este ejercicio usará la herramienta de registro de Catálogo de datos de Azure para registrar los orígenes de datos con el catálogo. Registro es el proceso de extraer metadatos estructurales clave (como nombres, tipos y ubicaciones) del origen de datos y los recursos que contiene y copiar esos metadatos en el catálogo. Los orígenes de datos y sus datos permanecen donde están, pero el catálogo usa los metadatos para que se puedan detectar y comprender más fácilmente.

### A continuación se muestra cómo registrar un origen de datos

1.	Inicie sesión en el portal del Catálogo de datos de Azure.

    ![register1][1]

2.	Desplácese hacia abajo y haga clic en **Publicar datos**.

    ![register2][2]
3.	Haga clic en **Iniciar aplicación**.
4.	En la página **Bienvenida**, haga clic en **Iniciar sesión** y escriba sus credenciales.
5.	En la página **Catálogo de datos de Microsoft Azure**, haga clic en **SQL Server**.

    ![register3][3]
6.	Escriba su **Nombre del servidor** y haga clic en **CONECTAR**.
7.	En la página siguiente es donde se registran los metadatos de su origen de datos. En este ejemplo, registrará objetos de **producto** del espacio de nombres de producción de AdventureWorks. Aquí se muestra cómo hacerlo:

    a. En el árbol de jerarquía, haga clic en **Producción**.

    b. Ctrl+haga clic en Producto, ProductCategory, ProductDescription y ProductPhoto.

    ![register4][4]

    c. Haga clic en la flecha de selección de movimiento (**>**). De este modo se moverán todos los objetos de producto a la lista **Pendiente de registrar**.

    ![register5][5]

    d. **Opcional**: puede **Incluir una vista previa** y **Agregar un experto de origen de datos**.

    e. En **Agregar etiquetas**, introduzca una descripción, foto. De este modo se agregarán etiquetas de búsqueda para estos recursos de datos. Las etiquetas son una excelente manera de ayudar a los usuarios buscar un origen de datos registrados.

    f. Haga clic en **REGISTRAR**. Catálogo de datos de Azure registra los objetos seleccionados. En este ejercicio, se registran los objetos seleccionados de Adventure Works.

    ![register6][6]

    g. Para ver los objetos de origen de datos registrados, haga clic en **Ver portal**. En el portal de Catálogo de datos de Azure, puede ver los objetos de origen de datos en **Iconos** o una **Lista**.

    ![register7][7]

En este ejercicio se registran los objetos de la base de datos de ejemplo de Adventure Works para que puedan ser detectados fácilmente por los usuarios de su organización. En el siguiente ejercicio aprenderá a detectar los activos de datos registrados.

## Ejercicio 3: Detección de recursos de datos registrados

En este ejercicio usará el portal del Catálogo de datos de Azure para detectar recursos de datos registrados y ver sus metadatos. Catálogo de datos de Azure proporciona varias herramientas para detectar recursos de datos, incluida la búsqueda de palabras clave simple, filtros interactivos y una sintaxis de búsqueda avanzada para los usuarios "power".

### A continuación se indica cómo descubrir recursos de datos registrados

**Catálogo de datos de Azure** tiene una sencilla pero eficaz sintaxis de búsqueda que le permite crear fácilmente consultas que devuelven los datos que los usuarios necesitan. Para obtener más información acerca de **Catálogo de datos de Azure**, consulte la referencia de la sintaxis de búsqueda.

**Catálogo de datos de Azure** tiene las siguientes opciones de búsqueda:

- Búsqueda de palabra clave
- Filtrar
- Búsqueda avanzada

También puede refinar los activos de datos que desea ver. **Catálogo de datos de Azure** tiene las siguientes opciones de vista:

- Ver propiedades
- Ver columnas
- Ver vista previa

En este ejemplo, usará una búsqueda de palabras clave. La búsqueda de **Catálogo de datos de Azure** tiene varias técnicas de consulta. Este ejemplo usará una consulta de búsqueda de **agrupación**.

**Técnicas de consulta**<table><tr><td><b>Técnica</b></td><td><b>Uso</b></td><td><b>Ejemplo</b></td></tr><tr><td>Ámbito de propiedad</td><td>Solo devuelven orígenes de datos en los que el término de búsqueda coincide con la propiedad especificada</td><td>name:product</td></tr><tr><td>Operadores lógicos</td><td>Amplíe o acote una búsqueda mediante operaciones booleanas, como se describe en la sección de operadores booleanos de esta página</td><td>finanzas NO corporativas</td></tr><tr><td>Agrupación con paréntesis</td><td>Use paréntesis para agrupar partes de la consulta para conseguir el aislamiento lógico, especialmente en combinación con los operadores booleanos</td><td>name:product AND (tags:illustration OR tags:photo)</td></tr><tr><td>Operadores de comparación</td><td>Use comparaciones que no sean de igualdad para las propiedades que tienen tipos de datos numéricos y de fecha</td><td>creationTime:&gt;11/05/14</td></tr></table>

En este ejemplo, se hace una búsqueda de **agrupación** de recursos de datos en las que name equivale a product y tags equivale a illustration o tags equivale a photo.

1.	Inicie sesión en el portal **Catálogo de datos de Azure**.
2.	Haga clic en **Detectar**.
3.	En el cuadro **Búsqueda**, especifique una consulta de **Agrupación**: (tags:description OR tags:photo).
4.	Haga clic en el icono de búsqueda o presione ENTRAR. **Catálogo de datos de Azure** mostrará los recursos de datos de esta consulta de búsqueda.

    ![búsqueda][8]

En este ejercicio ha usado el portal **Catálogo de datos de Azure** para detectar y ver los recursos de datos registrados con el catálogo.

## Ejercicio 4: Anotación de orígenes de datos registrados

En este ejercicio usará el portal del **Catálogo de datos de Azure** para anotar los recursos de datos que se han registrado anteriormente en el catálogo. Las anotaciones que proporcione complementarán y mejorarán los metadatos estructurales extraídos del origen de datos durante el registro y hará que los recursos de datos resulten todavía más fáciles de detectar y entender. Dado que cada usuario del **Catálogo de datos** puede proporcionar sus propias anotaciones, es fácil para todos los usuarios que tengan una perspectiva de los datos compartirlos.

### A continuación se indica cómo agregar anotaciones a los activos de datos

1.	Inicie sesión en el portal **Catálogo de datos de Azure**.
2.	Haga clic en **Detectar**.
3.	Elija uno o varios **recursos de datos**. En este ejemplo, elija **ProductPhoto** y escriba "Fotos de productos para materiales de marketing".
4.	Escriba una **descripción** que le ayude a otros usuarios a descubrir y entender por qué y cómo usar el recurso de datos seleccionado. Por ejemplo, escriba "Imágenes de producto". También puede agregar más etiquetas y ver columnas.
5.	Ahora puede intentar buscar y filtrar para detectar recursos de datos mediante los metadatos descriptivos que haya agregado al catálogo.

    ![anotar][9]

En este ejercicio ha agregado información descriptiva a los recursos de datos registrados para que los usuarios del catálogo puedan detectar el origen de datos usando términos que puedan entender.

## Ejercicio 5: Metadatos de micromecenazgo

En este ejercicio trabajará con otro usuario para agregar metadatos a los recursos de datos del catálogo. El enfoque de micromecenazgo de las anotaciones del Catálogo de datos de Azure permite a cualquier usuario agregar etiquetas, descripciones y otros metadatos para que cualquier usuario con una perspectiva de un recurso de datos y su uso pueda tener esa perspectiva capturada y disponible para otros usuarios.

> [AZURE.NOTE] Si no dispone de otro usuario con el que trabajar en este tutorial, no se preocupe. Los usuarios que acceden al catálogo de datos pueden agregar su propio punto de vista cuando lo deseen. Este enfoque de micromecenazgo de los metadatos permite que el contenido del catálogo y la riqueza de los metadatos del catálogo crezcan con el tiempo.

### A continuación se indica cómo puede aplicar micromecenazgo a los metadatos sobre recursos de datos

Solicite a un compañero que repita el ejercicio **Anotación de los orígenes de datos registrados** anterior. Una vez que su colega agregue una descripción a un recurso de datos, como ProductPhoto, verá varias anotaciones.


![crowdsourcing][13]

En este ejercicio se han explorado las capacidades del Catálogo de datos de Azure para los metadatos de micromecenazgo, donde cualquier usuario del catálogo puede anotar los recursos de datos que detecta.


## Ejercicio 6: Conectarse a orígenes de datos

En este ejercicio usará el portal **Catálogo de datos de Azure** para conectarse a orígenes de datos con Microsoft Excel.


> [AZURE.NOTE] Es importante recordar que **Catálogo de datos de Azure** no da a los usuarios acceso al origen de datos en sí: simplemente facilita a los usuarios detectarlos y comprenderlos. Cuando los usuarios se conectan a un origen de datos, la aplicación cliente que eligen usará sus credenciales de Windows o les pedirán las credenciales según sea necesario. Si no se ha concedido acceso al origen de datos previamente al usuario, deberá concederse acceso a este para que pueda conectarse.

### A continuación se indica cómo conectarse a un origen de datos desde Excel

1.	Inicie sesión en el portal **Catálogo de datos de Azure**.
2.	Haga clic en **Detectar**.
3.	Elija un recurso de datos. En este ejemplo, elija ProductCategory.
4.	Elija **Abrir en** > **Excel**.

    ![connect1][10]
5.	En la ventana **Aviso de seguridad de Microsoft Excel**, haga clic en **Habilitar**.
6.	Abra el archivo **ProductCategory.odc**.
7.	El origen de datos se abre en Excel.

    ![connect2][11]

En este ejercicio, se ha conectado a orígenes de datos detectados mediante el Catálogo de datos de Azure. El portal **Catálogo de datos de Azure** permite a los usuarios conectarse directamente con las aplicaciones de cliente integradas en su menú **Abrir en...**, y permite a los usuarios conectarse con cualquier aplicación que elijan usando la información de ubicación de conexión incluida en los metadatos de los recursos.

## Ejercicio 7: Quitar los metadatos del origen de datos

En este ejercicio usará el portal del **Catálogo de datos de Azure** para quitar los datos de vista previa de los recursos de datos registrados y para eliminar los recursos de datos del catálogo.

> [AZURE.NOTE] El comportamiento predeterminado del catálogo es permitir que cualquier usuario registre cualquier origen de datos y permitir que cualquier usuario elimine cualquier recurso de datos que se haya registrado. Las capacidades de administración incluidas en la **edición Standard del Catálogo de datos de Azure** proporcionan opciones adicionales para la toma de posesión de los recursos y la restricción de quién puede detectar y eliminar los recursos.

En el **Catálogo de datos de Azure** puede quitar la vista previa de la eliminación del recurso individual o la eliminación de varios recursos.

### A continuación se muestra cómo eliminar varios recursos de datos

1.	Inicie sesión en el portal **Catálogo de datos de Azure**.
2.	Haga clic en **Detectar**.
3.	Elija uno o varios recursos de datos.
4.	Hacer clic en **Eliminar**.

En este ejercicio ha eliminado recursos de datos registrados del catálogo.

## Ejercicio 8: Administración de orígenes de datos registrados

En este ejercicio usará las capacidades de administración del **Catálogo de datos de Azure** para tomar posesión de los recursos de datos y para controlar qué usuarios pueden detectar y administrar esos recursos.

Nota: Las capacidades de administración que se describe en este ejercicio están disponibles solo en la Standard Edition del Catálogo de datos de Azure y no en la edición gratuita. En **Catálogo de datos de Azure**, puede tomar posesión de los recursos de datos, agregar copropietarios a los recursos de datos y establecer la visibilidad de los recursos de datos.

### A continuación se muestra cómo tomar posesión de los recursos de datos y restringir la visibilidad

1.	Inicie sesión en el portal **Catálogo de datos de Azure**.
2.	Haga clic en **Detectar**.
3.	Elija uno o varios recursos de datos.
4.	En el panel **Propiedades**, sección **Administración**, haga clic en **Tomar posesión**.
5.	Para restringir la visibilidad, haga clic en **Propietarios y estos usuarios**.

    ![propiedad][12]

En este ejercicio exploró las capacidades de administración del catálogo y restringido la visibilidad en recursos de datos seleccionados.

## Resumen

En este tutorial exploró capacidades esenciales de la vista previa del **Catálogo de datos de Azure**, incluido el registro, la anotación, la detección y la administración de orígenes de datos empresariales. Ahora que ha completado el tutorial, ha llegado el momento de comenzar. Puede empezar hoy mismo por registrar los orígenes de datos en los que confían usted y su equipo, e invitando a compañeros a usar el catálogo.


<!--Image references-->
[1]: ./media/data-catalog-get-started/register1.png
[2]: ./media/data-catalog-get-started/register2.png
[3]: ./media/data-catalog-get-started/register3.png
[4]: ./media/data-catalog-get-started/register4.png
[5]: ./media/data-catalog-get-started/register5.png
[6]: ./media/data-catalog-get-started/register6.png
[7]: ./media/data-catalog-get-started/register7.png
[8]: ./media/data-catalog-get-started/search.png
[9]: ./media/data-catalog-get-started/annotate.png
[10]: ./media/data-catalog-get-started/connect1.png
[11]: ./media/data-catalog-get-started/connect2.png
[12]: ./media/data-catalog-get-started/ownership.png
[13]: ./media/data-catalog-get-started/crowdsource.png

<!---HONumber=AcomDC_0128_2016-->