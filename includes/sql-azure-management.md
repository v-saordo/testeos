
# Administración de Base de datos SQL de Azure con el uso de SQL Server Management Studio 

Puede usar SQL Server Management Studio (SSMS) para administrar las bases de datos y los servidores lógicos de Base de datos SQL de Azure. Este tema le guía a través de las tareas comunes con SSMS. Ya debería tener una base de datos y un servidor lógico creados en la Base de datos SQL de Azure antes de comenzar. Para empezar, lea [Crear la primera base de datos SQL de Azure](sql-database-get-started.md) y vuelva luego.

Se recomienda usar la versión más reciente de SSMS siempre que trabaje con la Base de datos SQL de Azure. Visite [Descargar SQL Server Management Studio](https://msdn.microsoft.com/es-ES/library/mt238290.aspx) para obtenerla.


## Conectarse a un servidor lógico de Base de datos SQL

Para conectarse a Base de datos SQL, debe saber el nombre del servidor de Azure. Es necesario que inicie sesión en el portal para tener acceso a esta información.

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  En el panel izquierda, haga clic en **Base de datos SQL**.

3.  En la página principal Bases de datos SQL, haga clic en **SERVIDORES**, en la parte superior de la página, para ver todos los servidores asociados con la suscripción. Encuentre el nombre del servidor con el que desea establecer la conexión y cópielo en el Portapapeles.

	A continuación, configure el firewall de Base de datos SQL para que permita todas las conexiones desde la máquina local. Para ello, agregue la dirección IP de las máquinas locales a la lista de excepciones del firewall.

1.  En la página principal Bases de datos SQL, haga clic en **SERVIDORES** y, a continuación, haga clic en el servidor al que desea conectarse.

2.  Haga clic en **Configurar** en la parte superior de la página.

3.  Copie la dirección IP en DIRECCIÓN IP DEL CLIENTE ACTUAL.

4.  En la página Configurar, la sección **Direcciones IP permitidas** incluye tres cuadros donde puede especificar un nombre de regla y un intervalo de direcciones IP como valores iniciales y finales. Para un nombre de regla, puede escribir el nombre de su PC. Para el intervalo inicial y final, pegue la dirección IP de su PC en ambos cuadros y, a continuación, haga clic en la casilla que aparece.

	El nombre de regla debe ser exclusivo. Si se trata de un equipo de desarrollo, puede escribir la dirección IP en el cuadro de inicio de intervalo de IP y en el cuadro de final de intervalo de IP. De lo contrario, puede que tenga que escribir un intervalo más amplio de direcciones IP para tener en cuenta conexiones de equipos adicionales de la organización.
 
5. Haga clic en **GUARDAR** en la parte inferior de la página.

    **Nota:** los cambios de la configuración del firewall pueden tardar hasta cinco minutos en aplicarse.

	Ya puede conectarse a Base de datos SQL con Management Studio.

1.  En la barra de tareas, haga clic en **Inicio**, vaya a **Todos los programas** y a **Microsoft SQL Server 2014** y, a continuación, haga clic en **SQL Server Management Studio**.

2.  En **Conectar con el servidor**, especifique el nombre completo del servidor como *nombreDeServidor*.database.windows.net. En Azure, el nombre del servidor es una cadena generada automáticamente compuesta de caracteres alfanuméricos.

3.  Seleccione **Autenticación de SQL Server**.

4.  En el cuadro **Inicio de sesión**, escriba el inicio de sesión de administrador de SQL Server especificado en el portal cuando creó el servidor.

5.  En el cuadro **Contraseña**, escriba la contraseña especificada en el portal cuando creó el servidor.

8.  Haga clic en **Conectar** para establecer la conexión.

SQL Server 2014 SSMS con las actualizaciones más recientes ofrece soporte ampliado para tareas como la creación y la modificación de bases de datos SQL de Azure. Además, puede usar instrucciones Transact-SQL para realizar estas tareas. En los pasos descritos a continuación se facilitan ejemplos de tales instrucciones. Para obtener más información acerca del uso de Transact-SQL con Base de datos SQL, incluidos los detalles sobre los comandos compatibles, consulte [Referencia de Transact-SQL (Base de datos SQL)](http://msdn.microsoft.com/library/bb510741.aspx).

## Crear y administrar bases de datos SQL de Azure

Mientras está conectado a la base de datos **maestra**, puede crear bases de datos nuevas en el servidor y modificar o anular las existentes. En los pasos siguientes se describe cómo realizar varias tareas comunes de administración de bases de datos con Management Studio. Para realizar estas tareas, asegúrese de que está conectado a la base de datos **maestra** con el inicio de sesión principal a nivel de servidor que creó cuando creó el servidor.

Para abrir una ventana de consulta en Management Studio, abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, en **Nueva consulta**.

-   Use la instrucción **CREATE DATABASE** para crear una base de datos nueva. Para obtener más información, consulte [CREATE DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/dn268335.aspx). La instrucción siguiente crea una base de datos con el nombre **myTestDB** y especifica que se trata de una base de datos Standard S0 Edition con un tamaño máximo de 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Haga clic en **Ejecutar** para ejecutar la consulta.

-   Use la instrucción **ALTER DATABASE** para modificar una base de datos existente, por ejemplo, si desea cambiar el nombre y la edición de la base de datos. Para obtener más información, consulte [ALTER DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/ms174269.aspx). La instrucción siguiente modifica la base de datos creada en el paso anterior para cambiar a la edición Standard S1.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Use la instrucción **DROP DATABASE** para eliminar una base de datos existente. Para obtener más información, consulte [DROP DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/ms178613.aspx). La instrucción siguiente elimina la base de datos **myTestDB**, pero no la anula ahora porque la usará para crear inicios de sesión en el paso siguiente.

        DROP DATABASE myTestBase;

-   La base de datos maestra tiene la vista **sys.databases**, que puede usar para ver los detalles acerca de todas las bases de datos. Para ver todas las bases de datos existentes, ejecute la siguiente instrucción:

        SELECT * FROM sys.databases;

-   En Base de datos SQL, no se admite la instrucción **USE** para cambiar entre bases de datos. En su lugar, necesita establecer una conexión directa con la base de datos de destino.

>[AZURE.NOTE]Muchas de las instrucciones de Transact-SQL que crean o modifican una base de datos deben ejecutarse en su propio lote y no se pueden agrupar con otras instrucciones de Transact-SQL. Para obtener más información, consulte la información específica de las instrucciones disponible en los vínculos mencionados anteriormente.

## Crear y administrar los inicios de sesión

La base de datos **maestra** realiza un seguimiento de los inicios de sesión y de cuáles tienen permiso para crear bases de datos u otros inicios de sesión. Para administrar inicios de sesión, conéctese a la base de datos **maestra** con el inicio de sesión principal del nivel del servidor creado cuando configuró el servidor. Puede usar las instrucciones **CREATE LOGIN**, **ALTER LOGIN** o **DROP LOGIN** para ejecutar consultas en la base de datos maestra que administrará los inicios de sesión en todo el servidor. Para obtener más información, consulte [Administración de bases de datos e inicios de sesión en Base de datos SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx).


-   Use la instrucción **CREATE LOGIN** para crear un inicio de sesión nuevo a nivel de servidor. Para obtener más información, consulte [CREATE LOGIN (Base de datos SQL)](https://msdn.microsoft.com/library/ms189751.aspx). La instrucción siguiente crea un inicio de sesión nuevo denominado **login1**. Reemplace **password1** por la contraseña que desee.

        CREATE LOGIN login1 WITH password='password1';

-   Use la instrucción **CREATE USER** para conceder permisos a nivel de base de datos. Todos los inicios de sesión deben crearse en la base de datos **maestra**, pero para que un inicio de sesión se conecte a otra base de datos, debe concederle permisos a nivel de base de datos con el uso de la instrucción **CREATE USER** en dicha base de datos. Para obtener más información, consulte [CREATE USER (Base de datos SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

-   Para conceder permisos a login1 para una base de datos llamada **myTestDB**, siga estos pasos:

 1.  Para actualizar el Explorador de objetos y ver la base de datos **myTestDB** que acaba de crear, haga clic con el botón derecho en el nombre del servidor en el Explorador de objetos y, a continuación, haga clic en **Actualizar**.  

     Si cerró la conexión, puede seleccionar **Conectar Explorador de objetos** para volver a establecerla en el menú Archivo.

 2. Haga clic con el botón derecho en la base de datos **myTestDB** y seleccione **Nueva consulta**.

    3.  Ejecute la instrucción siguiente en la base de datos myTestDB para crear un usuario de base de datos con el nombre **login1User** que se corresponda con el inicio de sesión de nivel de servidor **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Use el procedimiento almacenado **sp\_addrolemember** para conceder a la cuenta de usuario el nivel apropiado de permisos para la base de datos. Para obtener más información, consulte[ sp\_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). La instrucción siguiente concede a **login1User** permisos de solo lectura a la base de datos mediante la adición de **login1User** al rol **db\\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Use la instrucción **ALTER LOGIN** para modificar un inicio de sesión existente, por ejemplo, si desea cambiar la contraseña de inicio de sesión. Para obtener más información, consulte [ALTER LOGIN (Base de datos SQL)](https://msdn.microsoft.com/library/ms189828.aspx). La instrucción **ALTER LOGIN** debe ejecutarse en la base de datos **maestra**. Vuelva a la ventana de consulta conectada a la base de datos.

    La instrucción siguiente modifica el inicio de sesión **login1** para restablecer la contraseña. Reemplace **newPassword** por la contraseña que desee y **oldPassword** por la contraseña actual de inicio de sesión.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Use la instrucción **DROP LOGIN** para eliminar un inicio de sesión existente. Al eliminar un inicio de sesión a nivel de servidor también se eliminan todas las cuentas de usuario de la base de datos asociadas. Para obtener más información, consulte [DROP DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/ms178613.aspx). La instrucción **DROP LOGIN** debe ejecutarse en la base de datos **maestra**. La instrucción siguiente elimina el inicio de sesión **login1**.

        DROP LOGIN login1;

-   La base de datos maestra tiene la vista **sys.sql\\_logins**, que puede usar para ver los inicios de sesión. Para ver todos los inicios de sesión existentes, ejecute la siguiente instrucción:

        SELECT * FROM sys.sql_logins;

## Supervisar Base de datos SQL mediante las vistas de administración dinámica</h2>

Base de datos SQL admite varias vistas de administración dinámica que puede usar para supervisar una base de datos individual. A continuación se facilitan algunos ejemplos del tipo de datos de supervisión que puede recuperar en estas vistas. Para obtener todos los detalles y más ejemplos de uso, consulte [Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Para consultar una vista de administración dinámica, es necesario disponer de los permisos **VER ESTADO DE BASE DE DATOS**. Para conceder el permiso **VER ESTADO DE BASE DE DATOS** a un usuario específico de la base de datos, conéctese a la base de datos que desea administrar con el inicio de sesión principal a nivel de servidor y ejecute la siguiente instrucción en la base de datos:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcule el tamaño de la Calcule el tamaño de la base de datos; para ello, use la vista **sys.dm\_db\_partition\_stats**. La vista **sys.dm\_db\_partition\_stats** devuelve información sobre la página y el recuento de filas para cada partición de la base de datos, que puede usar para calcular el tamaño de la base de datos. La siguiente consulta devuelve el tamaño de la base de datos en megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use las vistas **sys.dm\_exec\_connections** y **sys.dm\_exec\_sessions** para recuperar información sobre las conexiones de usuario actuales y las tareas internas asociadas con la base de datos. La consulta siguiente devuelve información sobre la conexión actual:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Use la vista **sys.dm\_exec\_query\_stats** para recuperar estadísticas de agregado acerca del rendimiento para los planes de consulta en caché. La siguiente consulta devuelve información acerca de las cinco consultas principales clasificadas en función del tiempo promedio de CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<!---HONumber=Oct15_HO3-->