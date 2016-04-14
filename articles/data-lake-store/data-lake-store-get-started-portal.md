<properties 
   pageTitle="Introducción al Almacén de Data Lake | Azure" 
   description="Use el portal para crear una cuenta de Almacén de Data Lake y realizar operaciones básicas en Almacén de Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Introducción al Almacén de Azure Data Lake mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a usar el Portal de Azure para crear una cuenta de Almacén de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para obtener más información acerca del Almacén de Data Lake, consulte la página con [información general del Almacén de Azure Data Lake](data-lake-store-overview.md).

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="signup"></a>Habilitación de la suscripción de Azure para la vista previa pública del Almacén de Data Lake

En primer lugar, debe solicitar que se habilite la suscripción de Azure para la vista previa pública del Almacén de Data Lake. Siga los pasos que se indican a continuación.

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Datos y almacenamiento** y después en **Almacén de Azure Data Lake**.
3. En la nueva hoja **Nuevo almacén de Data Lake**, haga clic en **Suscribirse a la vista previa**. Lea la información y haga clic en **Aceptar**. Recibirá un correo electrónico una vez que su suscripción se habilite para la vista previa pública.

	![Suscribirse para la vista previa pública](./media/data-lake-store-get-started-portal/preview-signup.png "Crear una nueva cuenta de Azure Data Lake")

## Creación de una cuenta de Almacén de Azure Data Lake

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **NUEVO**, en **Datos y almacenamiento** y después en **Almacén de Azure Data Lake**. Lea la información de la hoja **Almacén de Azure Data Lake** y después haga clic en **Crear** en la esquina inferior izquierda de la hoja.

3. En la hoja **Nuevo almacén de Data Lake**, proporcione los valores como se muestra en la captura de pantalla siguiente:

	![Crear una nueva cuenta de Almacén de Azure Data Lake](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Crear una nueva cuenta de Azure Data Lake")

	- **Suscripción**. Seleccione la suscripción con la que desea crear una nueva cuenta de Almacén de Data Lake.
	- **Grupo de recursos**. Seleccione un grupo de recursos existente o haga clic en **Crear nuevo grupo de recursos** para crear uno. Un grupo de recursos es un contenedor que incluye los recursos relacionados de una aplicación. Para obtener más información, consulte [Grupos de recursos en Azure](resource-group-overview.md#resource-groups).
	- **Ubicación**. Seleccione la ubicación donde desea crear la cuenta de Almacén de Data Lake.

4. Seleccione **Anclar a Panel de inicio** si desea que la cuenta de Almacén de Data Lake sea accesible desde el Panel de inicio.

5. Haga clic en **Crear**. Si elige anclar la cuenta al Panel de inicio, se regresa al Panel de inicio, donde puede ver el progreso del aprovisionamiento de su cuenta de Almacén de Data Lake. Una vez aprovisionada la cuenta de Almacén de Data Lake, aparece la hoja de la cuenta.

6. Expanda el menú desplegable **Essentials** para ver la información sobre su cuenta de Almacén de Data Lake, como el recurso de grupo del que forma parte, la ubicación, etc. Haga clic en el icono **Inicio rápido** para ver vínculos a otros recursos relacionados con el Almacén de Data Lake.

	![Su cuenta de Almacén de Azure Data Lake](./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Su cuenta de Azure Data Lake")

## <a name="createfolder"></a>Creación de carpetas en una cuenta de Almacén de Azure Data Lake

Puede crear carpetas en su cuenta de Almacén de Data Lake para administrar y almacenar datos.

1. Abra la cuenta de Almacén de Data Lake que acaba de crear. En el panel izquierdo, haga clic en **Examinar** y en **Almacén de Data Lake**; después, en la hoja Almacén de Data Lake, haga clic en el nombre de la cuenta en la que desee crear carpetas. Si ancló la cuenta al Panel de inicio, haga clic en ese icono de cuenta.

2. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Explorador de datos**.

	![Crear carpetas en una cuenta de Almacén de Data Lake](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Crear carpetas en una cuenta de Almacén de Data Lake")

3. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Nueva carpeta**, escriba un nombre para la nueva carpeta y después haga clic en **Aceptar**.
	
	![Crear carpetas en una cuenta de Almacén de Data Lake](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Crear carpetas en una cuenta de Almacén de Data Lake")
	
	La carpeta recién creada se mostrará en la hoja **Explorador de datos**. Puede crear carpetas anidadas hasta cualquier nivel.

	![Crear carpetas en una cuenta de Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Crear carpetas en una cuenta de Data Lake")


## <a name="uploaddata"></a>Carga de datos en la cuenta de Almacén de Azure Data Lake

Puede cargar los datos en una cuenta de Almacén de Azure Data Lake directamente en el nivel raíz o en una carpeta que creó en la cuenta. En la siguiente captura de pantalla, siga los pasos para cargar un archivo en una subcarpeta desde la hoja **Explorador de datos**. En esta captura de pantalla, el archivo se carga en una subcarpeta que se muestra en las rutas de navegación (marcada en un cuadro rojo).

Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Carga de datos](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carga de datos")


## <a name="properties"></a>Propiedades y acciones disponibles en los datos almacenados

Haga clic en el archivo recién agregado para abrir la hoja **Propiedades**. Las propiedades asociadas con el archivo y las acciones que puede realizar en él están disponibles en esta hoja. También puede copiar la ruta de acceso completa del archivo en su cuenta de Almacén de Azure Data Lake, resaltada en el cuadro rojo en la captura de pantalla siguiente.

![Propiedades de los datos](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propiedades de los datos")

* Haga clic en **Vista previa** para obtener una vista previa del archivo, directamente desde el explorador. Además, puede especificar el formato de la vista previa. Haga clic en **Vista previa**, haga clic en **Formato** en la hoja **Vista previa de archivo** y, en la hoja **Formato de vista previa de archivo**, especifique las opciones, como el número de filas que se muestran, la codificación o el delimitador que se van a usar, etc.

  ![Formato de vista previa de archivo](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato de vista previa de archivo")

* Haga clic en **Descargar** para descargar el archivo en el equipo.

* Haga clic en **Cambiar nombre de archivo** para cambiar el nombre del archivo.

* Haga clic en **Eliminar archivo** para eliminar el archivo.


## Protección de los datos

Puede proteger los datos almacenados en su cuenta de Almacén de Azure Data Lake con Azure Active Directory y el control de acceso (ACL). Para obtener instrucciones sobre cómo hacerlo, consulte [Protección de los datos almacenados en el Almacén de Azure Data Lake](data-lake-store-secure-data.md).


## Eliminación de la cuenta de Almacén de Azure Data Lake

Para eliminar una cuenta de Almacén de Azure Data Lake, en la hoja de su Almacén de Data Lake, haga clic en **Eliminar**. Para confirmar la acción, se le pedirá que escriba el nombre de la cuenta que desea eliminar. Escriba el nombre de la cuenta y después haga clic en **Eliminar**.

![Eliminar cuenta de Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminar cuenta de Data Lake")

## Otras formas de crear una cuenta de Almacén de Data Lake

- [Introducción al Almacén de Azure Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
- [Introducción al Almacén de Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
- [Introducción al Almacén de Data Lake mediante la CLI de Azure](data-lake-store-get-started-cli.md)


## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0211_2016-->