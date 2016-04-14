<properties
	pageTitle="Tutorial de la herramienta de división y combinación de Base de datos elástica | Microsoft Azure"
	description="División y combinación con las herramientas de Base de datos elástica"
	services="sql-database"  
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="sidneyh" />

# Tutorial de la herramienta de división y combinación de Base de datos elástica

## Descarga de los paquetes de División y combinación
1. Descargue la última versión de NuGet desde [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Abra un símbolo del sistema y vaya al directorio al que descargó nuget.exe.
3. Descargue el paquete de división y combinación más reciente en el directorio actual con el comando que aparece a continuación:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

Los pasos anteriores descargan los archivos de División y combinación al directorio actual. Los archivos están ubicados en un directorio llamado **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**, donde *x.x.xxx.x* refleja el número de la versión. Busque los archivos del servicio División y combinación en el subdirectorio **content\\splitmerge\\service** y los scripts de Powershell de División y combinación (y las .dll de cliente necesarias) en el subdirectorio **content\\splitmerge\\powershell**.

## Requisitos previos

1. Cree una base de datos de Base de datos SQL de Azure que se usará como la base de datos de estado de División y combinación. Vaya al [Portal de Azure](https://ms.portal.azure.com). Cree una nueva **Base de datos SQL**. Rellene el nombre de la base de datos y cree un nuevo usuario y contraseña. Asegúrese de anotar el nombre y la contraseña para usarlos más adelante.

2. Asegúrese de que el servidor de Base de datos SQL de Azure permite que los servicios de Azure se conecten a él. En el portal, en la **Configuración de firewall**, asegúrese de que la opción **Permitir acceso a Servicios de Azure** está establecida en **Activado**. Haga clic en el icono de "guardar".

    ![Servicios permitidos][1]

3. Cree una cuenta de almacenamiento de Azure que se usará para la salida de diagnóstico. Vaya al Portal de Azure. En la barra de la izquierda, haga clic en **Nuevo**, en**Datos + almacenamiento** y en **Almacenamiento**.

4. Cree un servicio en la nube de Azure que contendrá el servicio de División y combinación. Vaya al Portal de Azure. En la barra de la izquierda, haga clic en **Nuevo**, en **Proceso**, en **Servicio en la nube** y en **Crear**.


## Configuración del servicio División y combinación

### Configuración del servicio División y combinación

1. En la carpeta donde ha descargado los ensamblados de división y combinación, cree una copia del archivo **ServiceConfiguration.Template.cscfg** que se suministra junto a **SplitMergeService.cspkg** y cámbiele el nombre por **ServiceConfiguration.cscfg**.

2. Abra **ServiceConfiguration.cscfg** en un editor de texto como Visual Studio que permite validar las entradas como el formato de las huellas digitales de certificados.

3. Cree una nueva base de datos o elija una base de datos existente que sirva como la base de datos de estado para las operaciones de división y combinación y recupere la cadena de conexión de esa base de datos.

	**Importante** En este momento, la base de datos de estado debe usar la intercalación latina (SQL\_Latin1\_General\_CP1\_CI\_AS). Para obtener más información, vea [Nombre de intercalación de Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).

	Con Base de datos SQL de Azure, la cadena de conexión suele tener el formato:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Escriba esta cadena de conexión en el archivo cscfg en las secciones de roles **SplitMergeWeb** y **SplitMergeWorker** en la configuración de ElasticScaleMetadata.

5.    Para el rol **SplitMergeWorker**, escriba una cadena de conexión válida en el Almacenamiento de Azure para la configuración **WorkerRoleSynchronizationStorageAccountConnectionString**.
        
### Configuración de seguridad
Para obtener instrucciones detalladas para configurar la seguridad del servicio, consulte [Configuración de seguridad de división y combinación](sql-database-elastic-scale-split-merge-security-configuration.md).

Para fines de una simple implementación de prueba para este tutorial, se realizará un conjunto mínimo de pasos de configuración para configurar y ejecutar el servicio. Estos pasos solo habilitan la máquina/cuenta que los ejecuta para comunicarse con el servicio.

### Creación de un certificado autofirmado

Cree un directorio nuevo y desde este directorio ejecute el siguiente comando usando una ventana de [Símbolo del sistema para desarrolladores para Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Se le pedirá que escriba una contraseña para proteger la clave privada. Escriba una contraseña segura y confírmela. Luego se le pedirá que escriba nuevamente la contraseña. Haga clic en **Sí** al final para importarla al almacén Raíz de Entidades de certificación de confianza.

### Creación de un archivo PFX

Ejecute el siguiente comando desde la misma ventana donde se ejecutó makecert; use la misma contraseña que usó para crear el certificado:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importación del certificado de cliente al almacén Personal
1. En el Explorador de Windows, haga doble clic en **MyCert.pfx**.
2. En el **Asistente para importar certificados**, seleccione **Usuarios actual** y haga clic en **Siguiente**.
3. Confirme la ruta del archivo y haga clic en **Siguiente**.
4. Escriba la contraseña, deje activada la opción **Incluir todas las propiedades extendidas** y haga clic en **Siguiente**.
5. Deje activada la opción **Seleccionar automáticamente el almacén de certificados…** y haga clic en **Siguiente**.
6. Haga clic en **Finalizar** y en **Aceptar**.

### Carga del archivo PFX al servicio en la nube

Vaya al [Portal de Azure](https://portal.azure.com).

1. Seleccione **Servicios en la nube**.
2. Seleccione el servicio en la nube que creó anteriormente para el servicio División y combinación.
3. Haga clic en **Certificados** en el menú superior.
4. Haga clic en **Cargar** en la barra inferior.
5. Seleccione el archivo PFX y escriba la misma contraseña anterior.
6. Una vez realizado, copie la huella digital del certificado desde la entrada nueva en la lista.

### Actualizar el archivo de configuración del servicio

Pegue la huella digital del certificado copiada anteriormente en el atributo de huella digital/valor de esta configuración. Para el rol de trabajo:

    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

Para el rol web:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />


Tenga en cuenta que para las implementaciones de producción se deben usar certificados independientes para la CA, para cifrado, el certificado de servidor y los certificados de cliente. Para obtener instrucciones detalladas al respecto, consulte [Configuración de seguridad](sql-database-elastic-scale-split-merge-security-configuration.md).

### Implementación del servicio División y combinación

1. Vaya al [portal de Azure](https://manage.windowsazure.com).
2. Haga clic en la pestaña **Servicios en la nube** que aparece a la izquierda y seleccione el servicio en la nube que creó anteriormente.
3. Haga clic en **Panel**.
4. Elija el entorno de ensayo y, a continuación, haga clic en **Cargar una nueva implementación de ensayo**.

    ![Ensayo][3]

5. En el cuadro de diálogo, escriba una etiqueta de implementación. Para "Paquete" y "Configuración", haga clic en "Desde local" y elija el archivo **SplitMergeService.cspkg** y el archivo .cscfg que configuró anteriormente.
6. Asegúrese de que la casilla de verificación con la etiqueta **Implementar aunque uno o varios roles contengan una sola instancia esté activada**.
7. Presione el botón de verificación que aparece en la esquina inferior derecha para comenzar la implementación. Tenga en cuenta que puede tardar unos minutos en completarse.

![Cargar][4]


## Solución de problemas de implementación

Si el rol web no puede ponerse en línea, probablemente haya un problema con la configuración de seguridad. Compruebe que SSL esté configurado como se describió anteriormente.

Si el rol de trabajo no puede ponerse en línea, pero el rol web sí, probablemente se trate de un problema al conectarse a la base de datos de estado que creó anteriormente.

* Asegúrese de que la cadena de conexión en el archivo .cscfg esté correcta.
* Compruebe que existan el servidor y la base de datos y de que el identificador de usuario y la contraseña estén correctos.
* En el caso de Base de datos SQL de Azure, la cadena de conexión debe tener el formato:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Asegúrese de que el nombre del servidor no comience por ****https://**.
* Asegúrese de que el servidor de Base de datos SQL de Azure permite que los servicios de Azure se conecten a él. Para ello, abra https://manage.windowsazure.com, haga clic en "Bases de datos SQL" a la izquierda, haga clic en "Servidores" en la parte superior y, a continuación, seleccione su servidor. Haga clic en **Configurar** en la parte superior para asegurarse de que el valor **Servicios de Microsoft Azure** esté establecido en "Sí". (Consulte la sección Requisitos previos al principio de este artículo).

## Prueba de la implementación del servicio División y combinación

### Conexión con un explorador web

Determine el extremo web de su servicio División y combinación. Para averiguar esto, vaya al Portal de Azure clásico, seleccione el **Panel** de su servicio en la nube y busque en **Dirección URL del sitio** en el lado derecho. Sustituya ****http://** por ****https://** dado que la configuración de seguridad predeterminada deshabilita el extremo HTTP. Cargue la página de esta dirección URL en el explorador.

### Pruebas con scripts de PowerShell

Puede probar la implementación y su entorno si ejecuta los scripts de PowerShell de ejemplo incluidos.

Los archivos de script incluidos son:

1. **SetupSampleSplitMergeEnvironment.ps1**: configura una capa de datos de prueba para División y combinación (consulte la tabla que aparece a continuación para ver una descripción detallada).
2. **ExecuteSampleSplitMerge.ps1**: ejecuta operaciones de prueba en la capa de datos de prueba (consulte la tabla que aparece a continuación para ver una descripción detallada).
3. **GetMappings.ps1**: script de muestra de nivel superior que imprime el estado actual de las asignaciones de particiones.
4. **ShardManagement.psm1**: script auxiliar que encapsula la API ShardManagement
5. **SqlDatabaseHelpers.psm1**: script auxiliar para crear y administrar bases de datos SQL.

<table style="width:100%">
  <tr>
    <th>Archivo de PowerShell</th>
    <th>Pasos</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1. Crea una base de datos del administrador de mapa de particiones</td>
  </tr>
  <tr>
    <td>2. Crea dos bases de datos de particiones.
  </tr>
  <tr>
    <td>3. Crea un mapa de particiones para esas bases de datos (elimina todo mapa de particiones existente en esas bases de datos). </td>
  </tr>
  <tr>
    <td>4. Crea una pequeña tabla de ejemplo en las particiones y rellena la tabla en una de las particiones.</td>
  </tr>
  <tr>
    <td>5. Declara la SchemaInfo para la tabla particionada.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>Archivo de PowerShell</th>
    <th>Pasos</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1. Envía una solicitud de división al front-end web del servicio división-combinación, que divide la mitad de los datos entre la primera partición y la segunda.</td>
  </tr>
  <tr>
    <td>2. Sondea el front-end web para ver el estado de la solicitud de división y espera hasta que se complete la solicitud.</td>
  </tr>
  <tr>
    <td>3. Envía una solicitud de combinación al front-end del servicio División y combinación, que mueve los datos desde la segunda partición de vuelta a la primera partición.</td>
  </tr>
  <tr>
    <td>4. Sondea el front-end web para ver el estado de la solicitud de combinación y espera hasta que se complete la solicitud.</td>
  </tr>
</table>

##Uso de PowerShell para comprobar la implementación

1.    Abra una nueva ventana de PowerShell y vaya al directorio al que descargó el paquete División y combinación y, a continuación, vaya al directorio "powershell".
2.    Cree un servidor de base de datos SQL de Azure (o elija un servidor existente) en el que se crearán las particiones y el administrador de mapa de particiones.

    Nota: el script SetupSampleSplitMergeEnvironment.ps1 crea todas estas bases de datos en el mismo servidor de manera predeterminada para que el script sea simple. Esta no es una restricción del servicio División y combinación mismo.

    Se requerirá un inicio de sesión de autenticación de SQL con acceso de lectura/escritura a las bases de datos para que el servicio División y combinación mueva los datos y actualice el mapa de particiones. Debido a que el servicio División y combinación se ejecuta en la nube, actualmente no es compatible con Autenticación integrada.

    Asegúrese de que el servidor SQL de Azure esté configurado para permitir el acceso desde la dirección IP dela máquina que ejecuta estos scripts. Puede encontrar esta configuración en Servidor SQL de Azure / Configuración / Direcciones IP permitidas.

3.    Ejecute el script SetupSampleSplitMergeEnvironment.ps1 para crear el entorno de ejemplo.

    Ejecutar este script eliminará toda estructura de datos de administrador del mapa de particiones existente en la base de datos del administrador de mapa de particiones y las particiones. Puede ser útil volver a ejecutar el script si desea volver a inicializar las particiones o el mapa de particiones.

    Línea de comandos de ejemplo:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

4.    Ejecute el script Getmappings.ps1 para ver las asignaciones que existen actualmente en el entorno de ejemplo.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Ejecute el script ExecuteSampleSplitMerge.ps1 para ejecutar una operación de división (moviendo la mitad de los datos de la primera partición a la segunda partición) y luego una operación de combinación (moviendo los datos de vuelta a la primera partición). Si configuró SSL y dejó deshabilitado el extremo http, asegúrese de usar el extremo https://.

    Línea de comandos de ejemplo:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Si recibe el siguiente error, es muy probable que se trate de un problema con el certificado de su extremo web. Intente conectarse al extremo web con el explorador web de su preferencia y revise si hay un error en el certificado.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Si está correcto, el resultado debiera verse de la siguiente manera:

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' –CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    Experimente con otros tipos de datos. Todos estos scripts tienen un parámetro -ShardKeyType opcional que le permite especificar el tipo de clave. El valor predeterminado es Int32, pero también puede especificar Int64, Guid o Binary.

## Creación de sus propias solicitudes

El servicio se puede utilizar mediante la interfaz de usuario web o importando y utilizando el módulo de PowerShell SplitMerge.psm1 que enviará sus solicitudes a través del rol web.

El servicio División y combinación puede mover los datos de las tablas particionadas y de las tablas de referencia. Una tabla particionada tiene una columna de clave de particionamiento y distintos datos de fila en cada partición. Una tabla de referencia no está particionada, por lo que contiene los mismos datos de fila en cada partición. Las tablas de referencia son útiles para los datos que no cambian con frecuencia y que se usan para UNIRSE con tablas particionadas en consultas.

Para realizar una operación de división y combinación, debe declarar las tablas particionadas y las tablas de referencia que desea mover. Esto se realiza con la API **SchemaInfo**. Esta API está en el espacio de nombres **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Para cada tabla particionada, cree un objeto **ShardedTableInfo** que describa el nombre del esquema principal de la tabla (opcional, se define de manera predeterminada en "dbo"), el nombre de la tabla y el nombre de la columna de esa tabla que contiene la clave de particionamiento.
2.    Para cada tabla de referencia, cree un objeto **ReferenceTableInfo** que describa el nombre del esquema principal de la tabla (opcional, se define de manera predeterminada en "dbo") y el nombre de la tabla.
3.    Agregue los objetos TableInfo anteriores a un nuevo objeto **SchemaInfo**.
4.    Obtenga una referencia a un objeto **ShardMapManager** y llame a **GetSchemaInfoCollection**.
5.    Agregue **SchemaInfo** a **SchemaInfoCollection** y proporcione el nombre del mapa de particiones.

Es posible ver un ejemplo de esto en el script SetupSampleSplitMergeEnvironment.ps1.

Tenga en cuenta que el servicio División y combinación no crea la base de datos de destino (o esquema para cualquier tabla de la base de datos) por usted. Deben crearse previamente antes de enviar una solicitud al servicio.


## Solución de problemas
Es posible que cuando ejecute los scripts de ejemplo de PowerShell vea el siguiente mensaje:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Este error significa que el certificado SSL no está configurado correctamente. Siga las instrucciones que aparecen en la sección "Conexión con un explorador web".

Si no se pueden enviar solicitudes, verá lo siguiente:

 [Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.

En este caso, compruebe el archivo de configuración, en particular la configuración de **WorkerRoleSynchronizationStorageAccountConnectionString**. Este error normalmente indica que el rol de trabajo no pudo inicializar correctamente la base de datos de metadatos la primera vez.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
 

<!---HONumber=AcomDC_0224_2016-->