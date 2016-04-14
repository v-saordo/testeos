<properties 
   pageTitle="Información de Análisis de Data Lake y U-SQL mediante los tutoriales interactivos del Portal de Azure | Azure" 
   description="Inicio rápido en el aprendizaje de Análisis de Data Lake y U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="jgao"/>


# Uso de tutoriales interactivos de Análisis de Azure Data Lake

El Portal de Azure proporciona un tutorial interactivo para que pueda empezar a trabajar con Análisis de Data Lake. En este artículo se muestra cómo consultar el tutorial para analizar los registros de sitios web.


>[AZURE.NOTE] Si desea realizar el mismo tutorial con Visual Studio, consulte [Análisis de registros de sitios web mediante Análisis de Data Lake](data-lake-analytics-analyze-weblogs.md). Se agregarán al portal más tutoriales interactivos.


Para otros tutoriales, consulte:

- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Introducción a Análisis de Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introducción a Análisis de Data Lake mediante .NET SDK](data-lake-analytics-get-started-net-sdk.md)
- [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una cuenta de Análisis de Data Lake**. Consulte [Introducción a Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md).

##Creación de una cuenta de Análisis de Data Lake 

Debe tener una cuenta de Análisis de Data Lake para poder ejecutar trabajos.

Cada cuenta de Análisis de Data Lake depende de una cuenta del [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md). Esta cuenta se conoce como la cuenta predeterminada de Almacén de Data Lake. Puede crear la cuenta del Almacén de Data Lake previamente o cuando se crea la cuenta de Análisis de Data Lake. En este tutorial, creará la cuenta del Almacén de Data Lake con la cuenta de Análisis.

**Para crear una cuenta de Análisis de Data Lake**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Haga clic en **Microsoft Azure** en la esquina superior izquierda para abrir el Panel de inicio.
3. Haga clic en el icono **Marketplace**.  
3. Escriba **Análisis de Azure Data Lake** en el cuadro de búsqueda en la hoja **Todo** y presione **ENTRAR**. Verá **Análisis de Azure Data Lake** en la lista.
4. Haga clic en **Análisis de Azure Data Lake** en la lista.
5. Haga clic en **Crear** en la parte inferior de la hoja.
6. Escriba o seleccione los valores siguientes:

    ![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Nombre**: el nombre de la cuenta de Análisis.
	- **Almacén de Data Lake**: cada cuenta de Análisis de Data Lake depende de una cuenta del Almacén de Data Lake. La cuenta de Análisis de Data Lake y la cuenta de Almacén de Data Lake dependiente deben ubicarse en el mismo centro de datos de Azure. Siga las instrucciones para crear una nueva cuenta de Almacén de Data Lake o seleccione una existente.
	- **Suscripción**: seleccione la suscripción de Azure usada para la cuenta de Análisis.
	- **Grupo de recursos**: seleccione un grupo de recursos de Azure existente o cree uno nuevo. Las aplicaciones normalmente se componen de muchos componentes,por ejemplo una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como Grupo de recursos de Azures Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md). 
	- **Ubicación**: seleccione un centro de datos de Azure para la cuenta de Análisis de Data Lake. 
7. Seleccione **Anclar a Panel de inicio**. Esto es necesario para seguir este tutorial.
8. Haga clic en **Crear**. Se abre el Panel de inicio del portal. Se agrega un nuevo icono a la página de inicio con la etiqueta "Implementación de Análisis de Azure Data Lake". Se tarda unos momentos en crear una cuenta de Análisis de Data Lake. Cuando la cuenta está creada, se abre la cuenta en una hoja nueva.

	![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##Ejecución del tutorial interactivo de Análisis del registro del sitio web

**Para abrir el tutorial interactivo de Análisis del registro del sitio web**

1. En el Portal, haga clic en **Microsoft Azure** en el menú de la izquierda para abrir el Panel de inicio.
2. Haga clic en el icono que está vinculado en la cuenta de Análisis de Data Lake.
3. Haga clic en **Explorar tutoriales interactivos** desde la barra **Essentials**.

	![Tutoriales interactivos de Análisis de Data Lake](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. Si aparece una advertencia naranja diciendo "Ejemplos no configurados, haga clic en...", haga clic en **Copiar datos de ejemplo** para copiar los datos de ejemplo en la cuenta del Almacén de Data Lake predeterminada. El tutorial interactivo necesita datos para ejecutarse.
5. Desde la hoja **Tutoriales interactivos**, haga clic en **Análisis del registro del sitio web**. El portal abre el tutorial en una nueva hoja del Portal.
5. Haga clic en **1. Introducción** y siga las instrucciones.

##Consulte también

- [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Introducción a Análisis de Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=AcomDC_0302_2016-->