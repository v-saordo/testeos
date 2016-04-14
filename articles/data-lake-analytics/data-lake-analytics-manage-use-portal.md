<properties 
   pageTitle="Administración de Análisis de Azure Data Lake mediante el Portal de Azure | Azure" 
   description="Aprenda a administrar cuentas, orígenes de datos, usuarios y trabajos de Análisis de Data Lake." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="jgao"/>

# Administración de Análisis de Azure Data Lake mediante el Portal de Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Aprenda a administrar cuentas, orígenes de datos, usuarios y trabajos de Análisis con Azure Data Lake mediante el Portal de Azure. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).




<!-- ################################ -->
<!-- ################################ -->
## Administrar cuentas

Antes de ejecutar un trabajo de Análisis de Data Lake, debe tener una cuenta de Análisis de Data Lake. A diferencia de HDInsight de Azure, no se paga por una cuenta de Análisis cuando no está ejecutando un trabajo. Solo se paga por el tiempo en que se ejecuta un trabajo. Para obtener más información, consulte la página con [información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

**Para crear una cuenta de Análisis de Data Lake**

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, en **Datos y análisis** y después en **Análisis de Data Lake**.
6. Escriba o seleccione los valores siguientes:

	![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Nombre**: el nombre de la cuenta de Análisis.
	- **Almacén de Data Lake**: cada cuenta de Análisis de Data Lake tiene una cuenta de Almacén de Azure Data Lake dependiente. La cuenta de Análisis de Data Lake y la cuenta de Almacén de Data Lake dependiente deben ubicarse en el mismo centro de datos de Azure. Siga las instrucciones para crear una nueva cuenta de Almacén de Data Lake o seleccione una existente.
	- **Suscripción**: seleccione la suscripción de Azure usada para la cuenta de Análisis.
	- **Grupo de recursos**: seleccione un grupo de recursos de Azure existente o cree uno nuevo. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md). 
	- **Ubicación**: seleccione un centro de datos de Azure para la cuenta de Análisis de Data Lake. 

8. Haga clic en **Crear**. Se abre la pantalla de inicio del portal. Se agrega un nuevo icono al Panel de inicio con la etiqueta "Implementación de Análisis de Azure Data Lake". Se tarda unos momentos en crear una cuenta de Análisis de Data Lake. Cuando la cuenta está creada, se abre la cuenta en una hoja nueva.

	![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

<a name="access-adla-account"></a> **Para acceder a una cuenta de Análisis de Data Lake o abrirla**

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar** en el menú de la izquierda y después en **Análisis de Data Lake**.
3. Haga clic en la cuenta de Análisis de Data Lake a la que desea acceder. La cuenta se abrirá en una nueva hoja.

**Para eliminar una cuenta de Análisis de Data Lake**

1. Abra la cuenta de Análisis de Data Lake que desee eliminar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account).
2. Haga clic en **Eliminar** en el menú de botones de la parte superior de la hoja.
3. Escriba el nombre de la cuenta y después haga clic en **Eliminar**.

Si elimina una cuenta de Análisis, no se eliminará la cuenta de Almacén de Data Lake dependiente. Para obtener instrucciones para eliminar cuentas de Almacén de Data Lake, consulte [Eliminación de una cuenta de Almacén de Data Lake](data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account).




























<!-- ################################ -->
<!-- ################################ -->
## Administración de orígenes de datos de cuenta

Actualmente, Análisis de Data Lake admite los siguientes orígenes de datos:

- [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
- [Almacenamiento de Azure](../storage/storage-introduction.md)

Cuando se crea una cuenta de Análisis de Data Lake, debe designar una cuenta de Almacén de Azure Data Lake para que sea la cuenta de almacenamiento predeterminada. La cuenta predeterminada de Almacén de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Análisis de Data Lake, puede agregar más cuentas de Almacén de Data Lake o cuentas de Almacenamiento de Azure.

<a name="default-adl-account"></a>**Para buscar la cuenta predeterminada de Almacén de Data Lake**

- Abra la cuenta de Análisis de Data Lake que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account). El almacén predeterminado de Data Lake se muestra en **Essentials**:

	![Agregar origen de datos en Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**Para agregar más orígenes de datos**

1. Abra la cuenta de Análisis de Data Lake que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account).
2. Haga clic en **Configuración** y después en **Orígenes de datos**. Verá la cuenta predeterminada de Almacén de Data Lake en la lista. 
3. Haga clic en **Agregar origen de datos**.

	![Agregar origen de datos en Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)

	Para agregar una cuenta de Almacén de Azure Data Lake, necesita el nombre de cuenta y acceso a la cuenta para poder realizar consultas en ella. Para agregar un almacenamiento de blobs de Azure, necesita la cuenta de almacenamiento y la clave de la cuenta, que se pueden encontrar yendo a la cuenta de almacenamiento en el portal.

<a name="explore-data-sources"></a>**Para explorar los orígenes de datos**

1. Abra la cuenta de Análisis que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account).
2. Haga clic en **Configuración** y después en **Explorador de datos**. 
 
	![Explorador de datos en Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
	
3. Haga clic en una cuenta de Almacén de Data Lake para abrirla.

	![Cuenta de almacenamiento de Explorador de datos en Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
	
	Para cada cuenta de Almacén de Data Lake, puede hacer lo siguiente:
	
	- **Nueva carpeta**: agregue una nueva carpeta.
	- **Cargar**: cargue archivos a la cuenta de Almacén desde su estación de trabajo.
	- **Acceso**: configure los permisos de acceso.
	- **Cambiar nombre de carpeta**: cambie el nombre de una carpeta.
	- **Propiedades de la carpeta**: muestre las propiedades del archivo o la carpeta, como la ruta de acceso de WASB, la ruta de acceso de WEBHDFS, la hora de la última modificación, etcétera.
	- **Eliminar carpeta**: elimine una carpeta.

<a name="upload-data-to-adls"></a> **Para cargar archivos a la cuenta de Almacén de Data Lake**

1. En el Portal, haga clic en **Examinar ** en el menú de la izquierda y después haga clic en **Almacén de Data Lake**.
2. Haga clic en la cuenta de Almacén de Data Lake a la que desea cargar datos. Para buscar la cuenta predeterminada de Almacén de Data Lake, consulte [aquí](#default-adl-account).
3. Haga clic en **Explorador de datos** en el menú superior.
4. Haga clic en **Nuevo directorio** para crear una carpeta o haga clic en el nombre de una carpeta para cambiar de carpeta.
6. Haga clic en **Cargar** en el menú superior para cargar el archivo.


<a name="upload-data-to-wasb"></a> **Para cargar archivos a la cuenta de almacenamiento de blobs de Azure**

Consulte [Carga de datos para trabajos de Hadoop en HDInsight](../hdinsight/hdinsight-upload-data.md). La información se aplica a Análisis de Data Lake.


## Administrar usuarios

Análisis de Data Lake usa el control de acceso basado en roles con Azure Active Directory. Cuando se crea una cuenta de Análisis de Data Lake, se agrega el rol "Administradores de suscripciones" a la cuenta. Puede agregar más usuarios y grupos de seguridad con los siguientes roles:

|Rol|Descripción|
|----|-----------|
|Propietario|Permite administrar todo, incluido el acceso a los recursos.|
|Colaborador|Tiene acceso al portal; envía y supervisa trabajos. Para poder enviar trabajos, un colaborador también necesita el permiso de lectura o escritura para las cuentas de Almacén de Data Lake.|
|Desarrollador de Análisis de Data Lake | Este usuario puede enviar trabajos y supervisar todos los trabajos, pero solo puede cancelar sus propios trabajos. No puede administrar su propia cuenta, por ejemplo, agregar usuarios, cambiar permisos ni eliminar la cuenta. Para poder ejecutar trabajos, necesita acceso de lectura o escritura a las cuentas de Almacén de Data Lake. | 
|Lector|Permite ver todo el contenido, pero no realizar cambios.|  
|Usuario del laboratorio de desarrollo y pruebas|Permite ver todo el contenido y conectar, iniciar, reiniciar y apagar las máquinas virtuales.|  
|Administrador de acceso de usuario|Permite administrar el acceso de usuario a los recursos de Azure.|  

Para obtener información sobre la creación de grupos de seguridad y usuarios de Azure Active Directory, consulte [¿Qué es Azure Active Directory?](../active-directory/active-directory-whatis.md)

**Para agregar usuarios o grupos de seguridad a una cuenta de Análisis**

1. Abra la cuenta de Análisis que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account).
2. Haga clic en **Configuración** y después en **Usuarios**. También puede hacer clic **Acceso** en la barra de título **Essentials**, como se muestra en la siguiente captura de pantalla:

	![Agregar usuarios en cuenta de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. En la hoja **Usuario**, haga clic en **Agregar**.
4. Seleccione un rol, agregue un usuario y después haga clic en **Aceptar**.

**Nota: si este usuario o grupo de seguridad necesita enviar trabajos, se le deberá conceder también permiso para el Almacén de Data Lake. Para obtener más detalles, consulte [Protección de datos almacenados en el Almacén de Azure Data Lake](../data-lake-store/data-lake-store-secure-data.md).**





<!-- ################################ -->
<!-- ################################ -->
## Trabajos de administración

Debe tener una cuenta de Análisis de Data Lake para poder ejecutar trabajos. Para obtener más información, consulte [Administración de cuentas de Análisis de Data Lake](#manage-data-lake-analytics-accounts).

<a name="create-job"></a>**Para crear un trabajo**

1. Abra la cuenta de Análisis que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account).
2. Haga clic en **Nuevo trabajo**.

	![Crear trabajos de U-SQL de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)

	Verá una nueva hoja similar a:

	![Crear trabajos de U-SQL de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)

	Para cada trabajo, puede configurar:


	|Nombre|Descripción|
	|----|-----------|
	|Nombre del trabajo|Escriba el nombre del trabajo.|
	|Prioridad|Cuanto menor es el número, mayor es la prioridad. Si hay dos trabajos en cola, se ejecutará en primer lugar el que tenga un número más bajo.|
	|Paralelismo |Número máximo de procesos de cálculo que pueden ocurrir al mismo tiempo. Al aumentar este número, se puede mejorar el rendimiento, pero también aumentar el costo.|
	|Script|Escriba el script U-SQL para el trabajo.|

	Con la misma interfaz, se pueden explorar los orígenes de datos de vínculo y agregar más archivos a los orígenes de datos vinculados. 
3. Haga clic en **Enviar trabajo** si desea enviar el trabajo.

**Para enviar un trabajo**

Consulte [Creación de trabajos de Análisis de Data Lake](#create-job).

<a name="monitor-jobs"></a>**Para supervisar trabajos**

1. Abra la cuenta de Análisis que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account). El panel Administración de trabajos muestra la información básica sobre los trabajos:

	![Administrar trabajos de U-SQL de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)

3. Haga clic en **Administración de trabajos** como se muestra en la captura de pantalla anterior.

	![Administrar trabajos de U-SQL de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)

4. Haga clic en un trabajo de las listas. O haga clic en **Filtro** para facilitar la búsqueda de trabajos:

	![Filtrar trabajos de U-SQL de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)

	Puede filtrar trabajos por **Intervalo de tiempo**, **Nombre del trabajo** y **Autor**.
5. Haga clic en **Volver a enviar** si desea volver a enviar el trabajo.

**Para volver a enviar un trabajo**

Consulte [Supervisión de trabajos de Análisis de Data Lake](#monitor-jobs).

##Supervisión del uso de la cuenta

**Para supervisar el uso de la cuenta**

1. Abra la cuenta de Análisis que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account). En el panel Uso muestra el uso:

	![Supervisar el uso de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)

2. Haga doble clic en el panel para ver más detalles.

##Ver el catálogo de U-SQL

El [catálogo de U-SQL](data-lake-analytics-use-u-sql-catalog.md) se usa para estructurar datos y código, de forma que puedan compartirse mediante scripts U-SQL. El catálogo permite el mayor rendimiento posible con los datos en Azure Data Lake. En el Portal de Azure, se puede ver el catálogo de U-SQL.

**Para examinar el catálogo de U-SQL**

1. Abra la cuenta de Análisis que desee administrar. Para obtener instrucciones, consulte [Acceso a las cuentas de Análisis de Data Lake](#access-adla-account).
2. Haga clic en **Explorador de datos** en el menú superior.
3. Expanda **Catálogo**, **maestro** y después Tablas, **Funciones con valores de tabla** o **Ensamblados**. En la siguiente captura de pantalla, se muestra una función con valores de tabla.

	![Cuenta de almacenamiento de Explorador de datos en Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)


<!-- ################################ -->
<!-- ################################ -->
## Uso de grupos del Administrador de recursos de Azure

Las aplicaciones normalmente se componen de muchos componentes,por ejemplo una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como Grupo de recursos de Azures Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

Un servicio Análisis de Data Lake puede incluir los siguientes componentes:

- Cuenta de Análisis de Azure Data Lake
- Cuenta predeterminada de Almacén de Azure Data Lake necesaria
- Más cuentas de Almacén de Azure Data Lake
- Más cuentas de Almacenamiento de Azure

Puede crear todos estos componentes en un grupo de ARM para que sean más fáciles de administrar.

![Cuenta y almacenamiento de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

La cuenta de Análisis de Data Lake y las cuentas de almacenamiento dependientes deben ubicarse en el mismo centro de datos de Azure. Sin embargo, el grupo de ARM puede encontrarse en otro centro de datos.



##Consulte también 

- [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Administración de Análisis de Azure Data Lake mediante Azure Powershell](data-lake-analytics-manage-use-powershell.md)
- [Supervisión y solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0218_2016-->