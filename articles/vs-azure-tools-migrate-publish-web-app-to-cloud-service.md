<properties
   pageTitle="Procedimiento para migrar y publicar una aplicación web en un servicio en la nube de Azure desde Visual Studio | Microsoft Azure"
   description="Procedimiento para migrar y publicar una aplicación web en un servicio en la nube de Azure desde Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Procedimiento: para migrar y publicar una aplicación web en un servicio en la nube de Azure desde Visual Studio

Para aprovechar los servicios de hospedaje y la escalabilidad de Azure, puede migrar y publicar la aplicación web en un servicio en la nube de Azure. Puede ejecutar una aplicación web en Azure realizando unos cambios mínimos en la aplicación existente.

>[AZURE.NOTE] Este tema explica la implementación en servicios en la nube, no en sitios web. Para obtener más información sobre la implementación en sitios web, vea [Implementación de una aplicación web en el Servicio de aplicaciones de Azure](/app-service-web/web-sites-deploy.md).

Para obtener una lista de plantillas específicas que son compatibles con Visual C# y Visual Basic, consulte la sección **Plantillas de proyecto compatibles** más adelante en este tema.

Primero debe habilitar la aplicación web para Azure desde Visual Studio. En la siguiente ilustración se muestran los pasos principales para publicar la aplicación web existente agregando un proyecto de Azure que se va a usar para la implementación. Este proceso agrega a la solución un proyecto de Azure con el rol web necesario. Según el tipo de proyecto web de que se trate, las propiedades del proyecto para los ensamblados también se actualizarán si el paquete de servicio necesita ensamblados adicionales para la implementación.

![Publicar una aplicación web en Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] El comando **Convertir**, **Convertir en proyecto de servicio en la nube de Azure** solo se muestra para el proyecto web de la solución. Por ejemplo, el comando no está disponible para un proyecto de Silverlight de la solución. Al crear un paquete de servicio o publicar su aplicación en Azure, se podrían producir advertencias o errores. Estas advertencias y errores pueden ayudarle a solucionar problemas antes de la implementación en Azure. Por ejemplo, podría recibir una advertencia sobre un ensamblado que falta. Para obtener más información sobre cómo tratar las advertencias como errores, consulte [Configuración de un proyecto de servicio en la nube de Azure con Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Si compila la aplicación, la ejecuta localmente mediante el emulador de proceso o la publica en Azure, es posible que vea el siguiente error en la ventana **Lista de errores:** **Se especificó una ruta de acceso, nombre de archivo o ambos demasiado largos**. Este error se produce porque el nombre completo de proyecto de Azure es demasiado largo. La longitud del nombre de proyecto, incluida la ruta de acceso completa, no puede tener más de 146 caracteres. Por ejemplo, este es el nombre de proyecto completo incluida la ruta de acceso de archivo para un proyecto de Azure que se crea para una aplicación de Silverlight: `c:\users<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Puede que necesite mover la solución a un directorio diferente que tenga una ruta de acceso más corta para reducir la longitud del nombre completo del proyecto.

Para migrar y publicar una aplicación web en Azure desde Visual Studio, siga estos pasos.

## Habilitar la implementación de una aplicación web en Azure

### Para habilitar la implementación de una aplicación web en Azure

1. Para habilitar la implementación de la aplicación web en Azure, abra el menú contextual de un proyecto web de la solución y elija Agregar proyecto de implementación de Azure.

    Se producirán las acciones siguientes:

    - Se agrega a la solución un proyecto de Azure denominado `<name of the web project>.Azure` para la aplicación.

    - Se agrega un rol web para el proyecto web a este proyecto de Azure.

    - La propiedad **Copia local** se establece en true para todos los ensamblados necesarios para MVC 2, MVC 3, MVC 4 y las aplicaciones de negocios de Silverlight. Esto agrega estos ensamblados al paquete de servicio empleado para la implementación.

  >[AZURE.IMPORTANT] Si tiene otros ensamblados o archivos que son necesarios para esta aplicación web, debe establecer manualmente las propiedades para estos archivos. Para obtener más información sobre cómo establecer estas propiedades, consulte la sección **Incluir archivos en el paquete de servicio** más adelante en este artículo.

  >[AZURE.NOTE] Si ya existe un rol web para un proyecto web específico en un proyecto de Azure de la solución, no aparecerá **Convertir**, **Convertir en proyecto de servicio en la nube de Azure** en el menú contextual para este proyecto web.

  Si tiene varios proyectos web en la aplicación web y desea crear roles web para cada proyecto web, debe realizar los pasos de este procedimiento para cada proyecto web. Esto crea proyectos de Azure diferentes para cada rol web. Cada proyecto web puede publicarse por separado. O bien, puede agregar manualmente otro rol web a un proyecto de Azure existente en la aplicación web. Para ello, abra el menú contextual de la carpeta **Roles** del proyecto de Azure, elija **Agregar**, a continuación, **Proyecto de rol web en la solución**, elija el proyecto que desee agregar como rol web y, continuación, elija el botón **Aceptar**.

## Usar una Base de datos SQL Azure para su aplicación

Si tiene una cadena de conexión para la aplicación web que usa una Base de datos de SQL Server local, debe cambiar esta cadena de conexión para usar una instancia de Base de datos SQL que hospeda Azure en su lugar.

>[AZURE.IMPORTANT] Su suscripción debe permitir usar la Base de datos SQL. Si tiene acceso a su suscripción desde el Portal de administración de Azure, puede determinar qué servicios proporciona su suscripción. Las siguientes instrucciones se aplican en el Portal de administración liberado. Si usa el Portal de administración de vista previa, vaya al siguiente procedimiento. |

### Para usar una instancia de Base de datos SQL en el rol web para la cadena de conexión

1. Para crear una instancia de base de datos SQL en el Portal de administración de Azure, siga los pasos del artículo siguiente: [Crear un servidor de Base de datos SQL](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Al configurar las reglas de firewall de su instancia de Base de datos SQL, debe activar la casilla **Permitir que otros servicios de Azure tengan acceso a este servidor**.

1. Para crear una instancia de Base de datos de SQL para usarla para su cadena de conexión, siga los pasos de la siguiente sección del siguiente artículo: [Crear una Base de datos SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Para copiar la cadena de conexión de ADO.NET para usarla para su cadena de conexión, en el Portal de administración de Azure siga estos pasos:

  1. Elija el botón **Base de datos** y, a continuación, vuelva a abrir el nodo de suscripción que usó para crear la instancia de Base de datos SQL.

  1. Para mostrar las instancias de Base de datos SQL disponibles, elija el nodo **Bases de datos SQL**.

  1. Para mostrar las propiedades de la base de datos, elija la base de datos. Se muestra la vista **Propiedades**.

      >[AZURE.NOTE] Si no aparece la vista **Propiedades**, puede que tenga que abrirla con el divisor.

  1. Para mostrar las cadenas de conexión, elija el botón de puntos suspensivos (...) al lado de Ver.

    Aparece el cuadro de diálogo **Cadenas de conexión**.

  1. Para copiar la cadena de conexión de ADO.NET, resalte el texto y elija las teclas Ctrl+C.

  1. Para cerrar el cuadro de diálogo, elija el botón **Cerrar**.

1. Para reemplazar la cadena de conexión en el archivo web.config para usar esta instancia de Base de datos SQL, abra el archivo web.config, resalte la entrada existente de la cadena de conexión y, a continuación, elija las teclas de Ctrl+V. La cadena de conexión ADO.NET para la instancia de Base de datos SQL reemplaza la cadena de conexión existente.

1. También debe agregar el parámetro `MultipleActiveResultSets=True` a la cadena de conexión. Las cadenas de conexión deben tener el siguiente formato:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Opcional) Un método alternativo a cambiar la cadena de conexión directamente en el archivo web.config es agregar una sección en uno de los archivos de transformación web.config, dependiendo de la configuración de compilación que use para crear su paquete del servicio. Abra el archivo Web.Debug.Config o el archivo Web.Release.Config. Agregue la siguiente sección en este archivo:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Guarde el archivo modificado y vuelva a publicar la aplicación.

### Para usar una instancia de Base de datos SQL mediante el Portal de administración de Azure

1. En el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885), elija el nodo Base de datos SQL.

  - Si aparece la instancia de Base de datos de SQL que desea usar, elija abrirla.

  - Si no ha creado ninguna instancia, elija el vínculo apropiado y después cree una instancia.

1. Después de abrir o crear una instancia de base de datos, elija el vínculo **Cadenas de conexión**.

1. En la parte inferior de la página, elija el vínculo para configurar los valores de firewall y aceptar los valores predeterminados o configurar los valores que necesita.

1. Copie la cadena de conexión ADO.NET, péguela en su archivo web.config sobre la cadena de conexión antigua para la base de datos existente y asegúrese de agregar `MultipleActiveResultSets=True`.

## Publicar una aplicación web en Azure

### Para publicar una aplicación web en Azure

1. Para probar la aplicación en el entorno de desarrollo local usando el emulador de proceso de Azure, abra el menú contextual del proyecto de Azure para el rol web y elija **Establecer como proyecto de inicio**. A continuación, elija **Depurar**, **Iniciar depuración** (teclado: **F5**).

    Aparecerá el cuadro de diálogo **Iniciar el entorno de depuración de Azure** y se iniciará la aplicación en el explorador. Para conocer cualquier detalle específico acerca de cómo iniciar cada tipo de aplicación web en el emulador de proceso, vea la tabla de esta sección.

1. Para configurar los servicios de la aplicación que se va a publicar en Azure, debe tener una cuenta Microsoft y una suscripción de Azure. Use los pasos del tema siguiente para configurar los servicios: [Preparación para publicar o implementar una aplicación de Azure desde Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para publicar la aplicación web en Azure, abra el menú contextual del proyecto web y elija **Publicar en Azure**.

    Se muestra el cuadro de diálogo **Publicar aplicación de Azure** y Visual Studio inicia el proceso de implementación. Para obtener más información acerca de cómo publicar la aplicación, consulte la sección **Publicar o empaquetar una aplicación de Azure desde Visual Studio** en [Publicar un servicio en la nube mediante Azure Tools](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] También puede publicar la aplicación web desde el proyecto de Azure. Para ello, abra el menú contextual del proyecto de Azure y elija **Publicar**.

1. Para ver el progreso de la implementación, puede examinar la ventana **Registro de actividad de Azure**. Este registro se muestra automáticamente cuando se inicia el proceso de implementación. Puede expandir la línea del registro de actividad para mostrar información detallada, como se muestra en la siguiente ilustración:

    ![VST\_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Opcional) Para cancelar el proceso de implementación, abra el menú contextual de la línea del registro de actividad y elija **Cancelar y quitar**. Esto detiene el proceso de implementación y elimina el entorno de implementación de Azure.

    >[AZURE.NOTE] Para quitar este entorno de implementación una vez implementado, debe usar el Portal de administración de Azure.

1. (Opcional) Después de que se hayan iniciado las instancias del rol, Visual Studio muestra automáticamente el entorno de implementación en el nodo **Cálculo de Azure** de **Cloud Explorer** o el **Explorador de servidores**. Desde aquí puede ver el estado de las instancias de rol individuales.

    La siguiente ilustración muestra las instancias del rol en el **Explorador de servidores** mientras todavía están en el estado de inicialización:

    ![VST\_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Para tener acceso a la aplicación después de la implementación, elija la flecha situada junto a la implementación cuando aparezca un estado **Completado** en el **registro de actividad de Azure**. Se muestra la dirección URL de la aplicación web en Azure. Vea la tabla siguiente para obtener detalles acerca de cómo iniciar un tipo específico de aplicación web desde Azure.

    En la tabla siguiente se muestran los detalles acerca de cómo iniciar determinadas aplicaciones web desde Azure o cómo ejecutar o depurar una aplicación web localmente mediante el emulador de proceso de Azure:

    |Tipo de aplicación web|Ejecutar o depurar localmente mediante el emulador de proceso|Ejecución en Azure|
    |---|---|---|
    |Aplicación web ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Elija el hipervínculo de dirección URL que aparece en la pestaña **Implementación** del **registro de actividad de Azure** para cargar la página de inicio en el explorador.|
    |Aplicación web MVC 2 de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Elija el hipervínculo de dirección URL que aparece en la pestaña **Implementación** del **registro de actividad de Azure** para cargar la página de inicio en el explorador.|
    |Aplicación web MVC 3 de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Elija el hipervínculo de dirección URL que aparece en la pestaña **Implementación** del **registro de actividad de Azure** para cargar la página de inicio en el explorador.|
    |Aplicación web MVC 4 de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Elija el hipervínculo de dirección URL que aparece en la pestaña **Implementación** del **registro de actividad de Azure** para cargar la página de inicio en el explorador.|
    |Aplicación web ASP.NET vacía|Debe agregar una página .aspx a la aplicación que configure como página de inicio para el proyecto web. Después, en la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Si tiene una página .aspx predeterminada en la aplicación, elija el hipervínculo de dirección URL mostrado en la pestaña **Implementación** del **registro de actividad de Azure**; esta página se cargará en el explorador. Si tiene una página .aspx diferente, necesita navegar hasta esta página específica usando el siguiente formato para la dirección URL `<url for deployment>/<name of page>.aspx`:|
    |Aplicación de Silverlight|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Necesita navegar hasta la página específica de la aplicación usando el siguiente formato de dirección URL: `<url for deployment>/<name of page>.aspx`|
    |Aplicación de negocios de Silverlight|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Necesita navegar hasta la página específica de la aplicación usando el siguiente formato de dirección URL: `<url for deployment>/<name of page>.aspx`|
    |Aplicación de navegación de Silverlight|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Necesita navegar hasta la página específica de la aplicación usando el siguiente formato de dirección URL: `<url for deployment>/<name of page>.aspx`|
    |Aplicación de servicio de WCF|Debe establecer el archivo .svc como página de inicio para el proyecto de servicio WCF. Después, en la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Necesita navegar hasta el archivo svc de la aplicación usando el siguiente formato de dirección URL: `<url for deployment>/<name of service file>.svc`|
    |Aplicación de servicio de flujo de trabajo WCF|Debe establecer el archivo .svc como página de inicio para el proyecto de servicio WCF. Después, en la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Necesita navegar hasta el archivo svc de la aplicación usando el siguiente formato de dirección URL: `<url for deployment>/<name of service file>.svc`|
    |Entidades dinámicas de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Debe actualizar la cadena de conexión (consulte la sección siguiente). También necesita navegar hasta la página específica de la aplicación usando el siguiente formato de dirección URL: `<url for deployment>/<name of page>.aspx`|
    |Linq to SQL de datos dinámicos de ASP.NET|En la barra de menús, elija **Depurar**, **Iniciar depuración** (teclado: elija la tecla **F5**).|Debe seguir los pasos de este procedimiento: Usar una Base de datos SQL de Azure para su aplicación (vea anteriormente en este tema). También necesita navegar hasta la página específica de la aplicación usando el siguiente formato de dirección URL: `<url for deployment>/<name of page>.aspx`|

## Actualizar una cadena de conexión para Entidades dinámicas de ASP.NET

### Para actualizar una cadena de conexión para Entidades dinámicas de ASP.NET

1. Para crear una Base de datos SQL de Azure que pueda usarse con la aplicación web Entidades dinámicas de ASP.NET, debe seguir los pasos de este procedimiento: **Usar una Base de datos SQL de Azure para su aplicación**.

1. Agregue las tablas y los campos que necesite para esta base de datos desde el Portal de administración de la plataforma Azure.

1. La cadena de conexión para este tipo de aplicación tiene el formato siguiente en el archivo web.config:

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

    Actualice el valor *connectionString* con la cadena de conexión de ADO.NET para la Base de datos SQL Azure de la manera siguiente:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

1. Para guardar el archivo web.config con los cambios que ha realizado en la cadena de conexión, en la barra de menús, elija **Archivo**, **Guardar web.config**.

## Plantillas de proyecto compatibles

Para publicar una aplicación web en Azure, la aplicación debe usar una de las plantillas de proyecto de C# o Visual Basic que se muestran en la tabla siguiente.

|Grupo de plantillas de proyecto|Plantilla de proyecto|
|---|---|
|Web|Aplicación web ASP.NET|
|Web|Aplicación web MVC 2 de ASP.NET|
|Web|Aplicación web MVC 3 de ASP.NET|
|Web|Aplicación web MVC 4 de ASP.NET|
|Web|Aplicación web ASP.NET vacía|
|Web|Aplicación web MVC 2 de ASP.NET vacía|
|Web|Aplicación web de Entidades de datos dinámicos de ASP.NET|
|Web|Aplicación web Linq to SQL de datos dinámicos de ASP.NET|
|Silverlight|Aplicación de Silverlight|
|Silverlight|Aplicación de negocios de Silverlight|
|Silverlight|Aplicación de navegación de Silverlight|
|WCF|Aplicación de servicio de WCF|
|WCF|Aplicación de servicio de flujo de trabajo WCF|
|Flujo de trabajo|Aplicación de servicio de flujo de trabajo WCF|

## Pasos siguientes
Para obtener más información sobre la publicación, vea [Preparación para publicar o implementar una aplicación de Azure desde Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Consulte también [Configuración de credenciales de autenticación con nombre](vs-azure-tools-setting-up-named-authentication-credentials.md).

<!----HONumber=AcomDC_0204_2016-->