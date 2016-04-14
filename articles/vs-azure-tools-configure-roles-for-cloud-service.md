<properties
   pageTitle="Configuración de los roles para un servicio en la nube de Azure con Visual Studio | Microsoft Azure"
   description="Aprenda a instalar y a configurar roles para servicios en la nube de Azure mediante Visual Studio."
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
   ms.date="02/24/2016"
   ms.author="tarcher" />

# Configuración de los roles para un servicio en la nube de Azure con Visual Studio

Un servicio en la nube de Azure puede tener uno o más roles web o de trabajo. Para cada rol debe definir cómo se configura ese rol y también configurar cómo se ejecuta. Para obtener más información sobre los roles en servicios en la nube, vea el vídeo [Introducción a los servicios en la nube de Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). La información para su servicio en la nube se almacena en los siguientes archivos:

- **ServiceDefinition.csdef**

    El archivo de definición de servicio define la configuración del tiempo de ejecución de su servicio en la nube, incluidos los roles que se requieren, los extremos y el tamaño de la máquina virtual. Ninguno de los datos almacenados en este archivo se puede cambiar cuando se ejecuta el rol.

- **ServiceConfiguration.cscfg**

    El archivo de configuración de servicio configura el número de instancias de un rol que se ejecutan y los valores de la configuración definida para un rol. Los datos almacenados en este archivo se pueden cambiar mientras se ejecuta el rol.

Para poder almacenar diferentes valores para esta configuración para el funcionamiento de su rol, puede tener varias configuraciones del servicio. Puede usar una configuración de servicio diferente para cada entorno de implementación. Por ejemplo, puede establecer su cadena de conexión de la cuenta de almacenamiento para usar el emulador de almacenamiento de Azure local en una configuración de servicio local y crear otra configuración del servicio para usar el almacenamiento de Azure en la nube.

Cuando cree un nuevo servicio en la nube de Azure en Visual Studio, se crearán dos configuraciones de servicio de forma predeterminada. Estas configuraciones se agregan a su proyecto de Azure. Las configuraciones se denominan:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## Configurar un servicio en la nube de Azure

Puede configurar un servicio en la nube de Azure desde el Explorador de soluciones en Visual Studio, como se muestra en la siguiente ilustración.

![Configuración de un servicio en la nube](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### Para configurar un servicio en la nube de Azure

1. Para configurar cada rol en un proyecto de Azure desde el **Explorador de soluciones**, abra el menú contextual para el rol en el proyecto de Azure y luego elija **Propiedades**.

    Se mostrará una página con el nombre del rol en el editor de Visual Studio. La página muestra los campos de la pestaña **Configuración**.

1. En la lista **Configuración del servicio**, elija el nombre de la configuración del servicio que quiere editar.

    Si desea realizar cambios en todas las configuraciones del servicio para este rol, puede elegir **Todas las configuraciones**.

    >[AZURE.IMPORTANT] Si elige una configuración de servicio específica, algunas propiedades están deshabilitadas porque solo se pueden establecer para todas las configuraciones. Para editar estas propiedades, debe elegir Todas las configuraciones.

    Ahora puede elegir una pestaña para actualizar cualquier propiedad habilitada en esa vista.

## Cambiar el número de instancias de rol

Para mejorar el rendimiento de su servicio en la nube, puede cambiar el número de instancias de un rol que se están ejecutando, en función del número de usuarios o de la carga esperada para un rol concreto. Se crea una máquina virtual independiente para cada instancia de un rol cuando se ejecuta el servicio en la nube en Azure. Esto afectará a la facturación de la implementación de este servicio en la nube. Para obtener más información sobre la facturación, vea [Comprender la factura de Microsoft Azure](billing-understand-your-bill.md).

### Para cambiar el número de instancias para un rol

1. Elija la pestaña **Configuración**.

1. En la lista **Configuración del servicio**, elija la configuración del servicio que quiera actualizar.

    >[AZURE.NOTE] Puede establecer el recuento de instancias para una configuración de servicio específico o para todas las configuraciones de servicio.

1. En el cuadro de texto **Recuento de instancias**, escriba el número de instancias que quiere iniciar para este rol.

    >[AZURE.NOTE] Cada instancia se ejecuta en una máquina virtual independiente al publicar su servicio en la nube en Azure.

1. Elija el botón **Guardar** en la barra de herramientas para guardar estos cambios en el archivo de configuración de servicio.

## Administrar cadenas de conexión para cuentas de almacenamiento

Puede agregar, quitar o modificar cadenas de conexión para sus configuraciones de servicio. Es posible que quiera diferentes cadenas de conexión para distintas configuraciones del servicio. Por ejemplo, es posible que quiera una cadena de conexión local para una configuración de servicio local que tiene un valor de `UseDevelopmentStorage=true`. También puede que desee configurar una configuración de servicio en la nube que use una cuenta de almacenamiento de Azure.

>[AZURE.WARNING] Al especificar la información de la clave de la cuenta de almacenamiento de Azure para una cadena de conexión de la cuenta de almacenamiento, esta información se almacena localmente en el archivo de configuración de servicio. Sin embargo, esta información no se almacena actualmente como texto cifrado.

Si usa un valor diferente para cada configuración de servicio, no tendrá que usar cadenas de conexión diferentes en su servicio en la nube ni modificar su código al publicar su servicio en la nube en Azure. Puede usar el mismo nombre para la cadena de conexión en el código y el valor será diferente, en función de la configuración del servicio que selecciona al crear su servicio en la nube o al publicarlo.

### Para administrar cadenas de conexión para cuentas de almacenamiento

1. Elija la pestaña **Configuración**.

1. En la lista **Configuración del servicio**, elija la configuración del servicio que quiera actualizar.

    >[AZURE.NOTE] Puede actualizar las cadenas de conexión para una configuración de servicio específica, pero si necesita agregar o eliminar una cadena de conexión, debe seleccionar Todas las configuraciones.

1. Para agregar una cadena de conexión, elija el botón **Agregar configuración**. Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **Nombre**, escriba el nombre que quiere usar para la cadena de conexión.

1. En la lista desplegable **Tipo**, elija **Cadena de conexión**.

1. Para cambiar el valor de la cadena de conexión, elija el botón de puntos suspensivos (...). Aparecerá el cuadro de diálogo **Crear cadena de conexión de almacenamiento**.

1. Para usar el emulador de la cuenta de almacenamiento local, elija el botón de opción **Emulador de almacenamiento de Microsoft Azure** y luego elija el botón **Aceptar**.

1. Para usar una cuenta de almacenamiento en Azure, elija el botón de opción **Su suscripción** y seleccione la cuenta de almacenamiento que quiera.

1. Para usar las credenciales personalizadas, elija el botón de opciones **Credenciales especificadas manualmente**. Escriba el nombre de la cuenta de almacenamiento y la clave principal o secundaria. Para obtener información sobre cómo crear una cuenta de almacenamiento y sobre cómo escribir los detalles de la cuenta de almacenamiento en el cuadro de diálogo **Crear cadena de conexión de almacenamiento**, vea [Preparación para publicar o implementar una aplicación de Azure desde Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para eliminar una cadena de conexión, selecciónela y luego elija el botón **Quitar configuración**.

1. Elija el icono **Guardar** en la barra de herramientas para guardar estos cambios en el archivo de configuración de servicio.

1. Para obtener acceso a la cadena de conexión en el archivo de configuración de servicio, debe obtener el valor de la opción de configuración. El código siguiente muestra un ejemplo de donde se crea el almacenamiento de blobs y se cargan los datos mediante una cadena de conexión `MyConnectionString` desde el archivo de configuración de servicio cuando un usuario elige **Button1** en la página Default.aspx del rol web para un servicio en la nube de Azure. Agregue las siguientes instrucciones using a Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Abra Default.aspx.cs en la vista de diseño y agregue un botón del cuadro de herramientas. Agregue el siguiente código al método `Button1_Click`. Este código usa `GetConfigurationSettingValue` para obtener el valor del archivo de configuración de servicio para la cadena de conexión. A continuación, se crea un blob en la cuenta de almacenamiento a la que se hace referencia en la cadena de conexión `MyConnectionString` y finalmente el programa agrega texto al blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## Agregar valores personalizados para usarlos en su servicio en la nube de Azure

La configuración personalizada del archivo de configuración de servicio le permite agregar un nombre y un valor para una cadena de una configuración de servicio específico. Puede elegir usar esta configuración para definir una característica en su servicio en la nube leyendo el valor de la configuración y usando este valor para controlar la lógica de su código. Puede cambiar estos valores de configuración de servicio sin tener que volver a generar el paquete de servicio o cuando se ejecuta el servicio en la nube. Su código puede buscar las notificaciones de cuando cambia un valor. Vea [RoleEnvironment.Changing Event](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Puede agregar, quitar o modificar valores personalizados para sus configuraciones de servicio. Es posible que quiera valores diferentes para estas cadenas para distintas configuraciones del servicio.

Al usar un valor diferente para cada configuración de servicio, no tendrá que usar distintas cadenas en su servicio en la nube ni modificar su código al publicar su servicio en la nube en Azure. Puede usar el mismo nombre para la cadena en el código y el valor será diferente, en función de la configuración del servicio que selecciona al crear su servicio en la nube o al publicarlo.

### Para agregar valores personalizados para usarlos en su servicio en la nube de Azure

1. Elija la pestaña **Configuración**.

1. En la lista **Configuración del servicio**, elija la configuración del servicio que quiera actualizar.

    >[AZURE.NOTE] Puede actualizar las cadenas para una configuración de servicio específico, pero si necesita agregar o eliminar una cadena, debe seleccionar **Todas las configuraciones**.

1. Para agregar una cadena, elija el botón **Agregar configuración**. Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **Nombre**, escriba el nombre que quiere usar para la cadena.

1. En la lista desplegable **Tipo**, elija **Cadena**.

1. Para agregar o cambiar el valor de la cadena, en el cuadro de texto **Valor** escriba el nuevo valor.

1. Para eliminar una cadena, seleccione la cadena y luego elija el botón **Quitar configuración**.

1. Elija el botón **Guardar** en la barra de herramientas para guardar estos cambios en el archivo de configuración de servicio.

1. Para obtener acceso a la cadena en el archivo de configuración de servicio, debe obtener el valor de la opción de configuración.

    Debe asegurarse de que las siguientes instrucciones using se han agregado a Default.aspx.cs igual que lo hizo en el procedimiento anterior.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Agregue el código siguiente al método `Button1_Click` para acceder a esta cadena de la misma manera que lo haría para una cadena de conexión. El código puede realizar algún código concreto en función del valor de la cadena de configuración para el archivo de configuración de servicio que se usa.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## Administrar el almacenamiento local para cada instancia de rol

Puede agregar almacenamiento del sistema de archivos local para cada instancia de un rol. Puede almacenar los datos locales aquí a los que no necesitan obtener acceso otros roles. Los datos que no tenga que guardar en tabla, blob o almacenamiento de base de datos SQL pueden almacenarse aquí. Por ejemplo, podría usar este almacenamiento local para almacenar datos en caché que deben volver a usarse. No se puede tener acceso a estos datos almacenados por otras instancias de un rol. Para obtener más información sobre los recursos de almacenamiento local, vea [Configurar los recursos de almacenamiento local](cloud-services/cloud-services-configure-local-storage-resources.md).

Los valores de almacenamiento local se aplican a todas las configuraciones de servicio. Solo puede agregar, quitar o modificar el almacenamiento local para todas las configuraciones de servicio.

### Para administrar el almacenamiento local para cada instancia de rol

1. Elija la pestaña **Almacenamiento local**.

1. En la lista **Configuración de servicio**, elija **Todas las configuraciones**.

1. Para agregar una entrada de almacenamiento local, elija el botón **Agregar almacenamiento local**. Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **Nombre**, escriba el nombre que quiere usar para este almacenamiento local.

1. En el cuadro de texto **Tamaño**, escriba el tamaño en MB que necesita para este almacenamiento local.

1. Para quitar los datos en este almacenamiento local cuando se recicla la máquina virtual para este rol, active la casilla **Limpiar al reciclar rol**.

1. Para editar una entrada de almacenamiento local existente, elija la fila que necesita actualizar. Luego podrá editar los campos, como se describe en los pasos anteriores.

1. Para eliminar una entrada de almacenamiento local, elija la entrada de almacenamiento en la lista y luego elija el botón **Quitar el almacenamiento local**.

1. Para guardar estos cambios en el archivo de configuración de servicio, elija el icono **Guardar** en la barra de herramientas.

1. Para tener acceso al almacenamiento local que ha agregado en el archivo de configuración de servicio, debe obtener el valor de la opción de configuración del recurso local. Use las siguientes líneas de código para tener acceso a este valor y cree un archivo llamado **MyStorageTest.txt** y escriba una línea de datos de prueba en ese archivo. Puede agregar este código al método `Button_Click` que usó en los procedimientos anteriores:

1. Debe asegurarse de que se agregan las siguientes instrucciones using a Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Agregue el siguiente código al método `Button1_Click`. Esto crea el archivo en el almacenamiento local y escribe los datos de prueba en ese archivo.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Opcional) Para ver este archivo que ha creado al ejecuta su servicio en la nube localmente, lleve a cabo los pasos siguientes:

  1. Ejecute el rol web y haga clic en **Button1** para asegurarse de que el código que se encuentra dentro de `Button1_Click` recibe la llamada.

  1. En el área de notificación, abra el menú contextual del icono de Azure y elija **Mostrar la interfaz de usuario del emulador de proceso**. Aparece el cuadro de diálogo **Emulador de proceso de Azure** .

  1. Seleccione el rol web.

  1. En la barra de menús, elija **Herramientas**, **Abrir almacén local**. Aparecerá una ventana del explorador de Windows.

  1. En la barra de menús, escriba **MyStorageTest.txt** en el cuadro de texto **Búsqueda** texto y luego elija **Entrar** para iniciar la búsqueda.

    El archivo se muestra en los resultados de la búsqueda.

  1. Para ver el contenido del archivo, abra el menú contextual para el archivo y elija **Abrir**.

## Recopilar diagnóstico del servicio en la nube

Puede recopilar datos de diagnóstico para el servicio en la nube de Azure. Estos datos se agregan a una cuenta de almacenamiento. Es posible que quiera diferentes cadenas de conexión para distintas configuraciones del servicio. Por ejemplo, es posible que quiera una cuenta de almacenamiento local para una configuración de servicio local que tiene un valor de UseDevelopmentStorage=true. También puede que desee configurar una configuración de servicio en la nube que use una cuenta de almacenamiento de Azure. Para obtener más información sobre los diagnósticos de Azure, consulte Recopilar datos de registro mediante Diagnósticos de Azure.

>[AZURE.NOTE] La configuración del servicio local ya está configurada para usar los recursos locales. Si usa la configuración del servicio en la nube para publicar su servicio en la nube de Azure, la cadena de conexión que especifique al publicar también se utiliza para la cadena de conexión de diagnóstico a menos que haya especificado una cadena de conexión. Si empaqueta su servicio en la nube con Visual Studio, no se cambia la cadena de conexión en la configuración del servicio.

### Para recopilar diagnóstico del servicio en la nube

1. Elija la pestaña **Configuración**.

1. En la lista **Configuración del servicio**, elija la configuración del servicio que quiere actualizar o elija **Todas las configuraciones**.

    >[AZURE.NOTE] Puede actualizar la cuenta de almacenamiento para una configuración de servicio específica, pero si quiere habilitar o deshabilitar los diagnósticos, debe elegir Todas las configuraciones.

1. Para habilitar los diagnósticos, active la casilla **Habilitar diagnósticos**.

1. Para cambiar el valor de la cuenta de almacenamiento, elija el botón de puntos suspensivos (...).

    Aparecerá el cuadro de diálogo **Crear cadena de conexión de almacenamiento**.

1. Para usar una cadena de conexión local, elija la opción del emulador de almacenamiento de Azure y luego elija el botón **Aceptar**.

1. Para usar una cuenta de almacenamiento asociada a su suscripción de Azure, elija la opción **Su suscripción**.

1. Para usar una cuenta de almacenamiento para la cadena de conexión local, elija la opción **Credenciales especificadas manualmente**.

    Para obtener más información sobre cómo crear una cuenta de almacenamiento y sobre cómo escribir los detalles de la cuenta de almacenamiento en el cuadro de diálogo **Crear cadena de conexión de almacenamiento**, vea [Preparación para publicar o implementar una aplicación de Azure desde Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Elija la cuenta de almacenamiento que quiera usar en **Nombre de cuenta**.

    Si va a especificar manualmente sus credenciales de la cuenta de almacenamiento, copie o escriba su clave principal en **Clave de cuenta**. Esta clave se puede copiar desde el Portal de administración de Azure. Para copiar esta clave, siga estos pasos desde la vista **Cuentas de almacenamiento** en el Portal de administración de Azure:

  1. Seleccione la cuenta de almacenamiento que quiera usar para su servicio en la nube.

  1. Elija el botón **Administrar claves de acceso** que se encuentra en la parte inferior de la pantalla. Aparecerá el cuadro de diálogo **Administrar claves de acceso**.

  1. Para copiar la clave de acceso, elija el botón **Copiar al Portapapeles**. Ahora puede pegar esta clave en el campo **Clave de cuenta**.

1. Para usar la cuenta de almacenamiento proporcionada como la cadena de conexión para diagnósticos (y almacenamiento en caché) cuando publica su servicio en la nube en Azure, active la casilla **Actualizar cadenas de conexión de almacenamiento de desarrollo para diagnóstico y almacenamiento en caché con las credenciales de la cuenta de almacenamiento de Azure al publicar en Azure**.

1. Elija el botón **Guardar** en la barra de herramientas para guardar estos cambios en el archivo de configuración de servicio.

## Cambiar el tamaño de la máquina virtual que se usa para cada rol

Puede establecer el tamaño de la máquina virtual para cada rol. Solo puede establecer este tamaño para todas las configuraciones de servicio. Si selecciona un tamaño de máquina menor, se asignarán menos núcleos de CPU, memoria y almacenamiento en disco local. El ancho de banda asignado también es menor. Para obtener más información sobre estos tamaños y los recursos asignados, vea [Tamaños para los Servicios en la nube](cloud-services/cloud-services-sizes-specs.md).

Los recursos necesarios para cada máquina virtual en Azure afectan al costo de ejecutar su servicio en la nube en Azure. Para obtener más información sobre la facturación de Azure, vea [Comprender la factura de Microsoft Azure](billing-understand-your-bill.md).

### Para cambiar el tamaño de la máquina virtual

1. Elija la pestaña **Configuración**.

1. En la lista **Configuración de servicio**, elija **Todas las configuraciones**.

1. Para seleccionar el tamaño de la máquina virtual para este rol, elija el tamaño adecuado en la lista **Tamaño de VM**.

1. Elija el botón **Guardar** en la barra de herramientas para guardar estos cambios en el archivo de configuración de servicio.

## Administrar extremos y certificados para sus roles

Para configurar los extremos de redes, especifique el protocolo, el número de puerto y, para HTTPS, la información del certificado SSL. Las versiones anteriores a junio de 2012 admiten HTTP, HTTPS y TCP. La versión de junio de 2012 admite esos protocolos y UDP. No puede usar UDP para los extremos de entrada en el emulador de proceso. Puede usar ese protocolo solo para los extremos internos.

Para mejorar la seguridad de su servicio en la nube de Azure, puede crear extremos que usen el protocolo HTTPS. Por ejemplo, si tiene un servicio en la nube que se usa por los clientes para pedidos de compra, querrá asegurarse de que su información es segura usando SSL.

También puede agregar extremos que se pueden usar interna o externamente. Los extremos externos se denominan extremos de entrada. Un extremo de entrada permite otro punto de acceso a los usuarios para su servicio en la nube. Si tiene un servicio WCF, es posible que quiera exponer un extremo interno para que un rol web tenga acceso a este servicio.

>[AZURE.IMPORTANT] Solo puede actualizar extremos para todas las configuraciones de servicio.

Si agrega extremos HTTPS, debe usar un certificado SSL. Para ello, puede asociar certificados a su rol para todas las configuraciones de servicio y usarlos para sus extremos.

>[AZURE.IMPORTANT] Estos certificados no se empaquetan con su servicio. Debe cargar sus certificados en Azure a través del portal de administración de la plataforma Azure.

Cualquier certificado de administración que asocie a sus configuraciones de servicio solo se aplicarán cuando se ejecute su servicio en la nube en Azure. Cuando su servicio en la nube se ejecute en el entorno de desarrollo local, se usará un certificado estándar administrado por el emulador de proceso de Azure.

### Para agregar un certificado a un rol

1. Elija la pestaña **Certificados**.

1. En la lista **Configuración de servicio**, elija **Todas las configuraciones**.

    >[AZURE.NOTE] Para agregar o quitar certificados, debe seleccionar Todas las configuraciones. Puede actualizar el nombre y la huella digital de una configuración de servicio concreta si es necesario.

1. Para agregar un certificado para este rol, elija el botón **Agregar certificado**. Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **Nombre**, escriba el nombre del certificado.

1. En la lista **Ubicación del almacén**, elija la ubicación del certificado que quiere agregar.

1. En la lista **Nombre de almacén**, elija el almacén que quiere usar para seleccionar el certificado.

1. Para agregar el certificado, elija el botón de puntos suspensivos (...). Aparecerá el cuadro de diálogo **Seguridad de Windows**.

1. Elija el certificado que quiera usar en la lista y luego elija el botón **Aceptar**.

    >[AZURE.NOTE] Al agregar un certificado del almacén de certificados, cualquier certificado intermedio se agrega automáticamente a la configuración. Estos certificados intermedios también se deben cargar en Azure para configurar el servicio correctamente para SSL.

1. Para eliminar un certificado, seleccione el certificado y luego presione el botón **Quitar certificado**.

1. Elija el icono **Guardar** en la barra de herramientas para guardar estos cambios en los archivos de configuración de servicio.

### Para administrar extremos para un rol

1. Elija la pestaña **Extremos**.

1. En la lista **Configuración de servicio**, elija **Todas las configuraciones**.

1. Para agregar un extremo, elija el botón **Agregar extremo**. Se agrega una nueva entrada a la lista.

1. En el cuadro de texto **Nombre**, escriba el nombre que quiera usar para este extremo.

1. Elija el tipo de extremo que necesita en la lista **Tipo**.

1. Elija el protocolo para el extremo que necesita en la lista **Protocolo**.

1. Si es un extremo de entrada, en el cuadro de texto **Puerto público**, escriba el puerto público que quiere usar.

1. En el cuadro de texto **Puerto privado** escriba el puerto privado que quiere usar.

1. Si el extremo requiere el protocolo https, en la lista **Nombre del certificado SSL**, elija un certificado para usarlo.

    >[AZURE.NOTE] En esta lista se muestran los certificados que se han agregado para este rol en la pestaña **Certificados**.

1. Elija el botón **Guardar** en la barra de herramientas para guardar estos cambios en los archivos de configuración de servicio.

## Pasos siguientes
Para obtener más información sobre los proyectos de Azure en Visual Studio, consulte [Configurar un proyecto de Azure](vs-azure-tools-configuring-an-azure-project.md). Para obtener más información sobre el esquema del servicio en la nube, consulte [Referencia de esquema](https://msdn.microsoft.com/library/azure/dd179398).

<!---HONumber=AcomDC_0224_2016-->