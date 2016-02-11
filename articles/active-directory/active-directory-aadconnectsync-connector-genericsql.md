<properties
   pageTitle="Azure AD Connect Sync: Conector de SQL genérico | Microsoft Azure"
   description="En este artículo se describe cómo configurar el conector de SQL genérico de Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Referencia técnica del conector de SQL genérico

En este artículo se describe el conector de SQL genérico. El artículo se aplica a los siguientes productos:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Debe usar la revisión 4.1.3461.0 o posterior ([KB2870703](https://support.microsoft.com/kb/2870703)).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## Información general sobre el conector de SQL genérico

El conector de SQL genérico permite integrar el servicio de sincronización con un sistema de base de datos que ofrece conectividad ODBC.

Desde una perspectiva de alto nivel, las siguientes características son compatibles con la versión actual del conector:

| Característica | Soporte técnico |
| --- | --- |
| Origen de datos conectado | El conector es compatible con todos los controladores ODBC de 64 bits. Se ha probado con los siguientes elementos: <li>Microsoft SQL Server y SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 y 11g</li><li>MySQL 5.x</li>
| Escenarios | <li>Administración del ciclo de vida de objetos</li><li>Administración de contraseñas</li> |
| Operaciones | <li>Importación completa e Importación diferencial, Exportación</li><li>Para Exportación: Agregar, Eliminar, Actualizar y Reemplazar</li><li>Establecer contraseña, Cambiar contraseña</li>
| Esquema | <li>Detección dinámica de objetos y atributos</li>

### Requisitos previos

Antes de usar el conector, asegúrese de que tiene lo siguiente en el servidor de sincronización, además de cualquier revisión mencionada antes:

- Microsoft .NET 4.5.2 Framework o posterior
- Controladores cliente ODBC de 64 bits

### Permisos en origen de datos conectado

Para crear o realizar alguna de las tareas admitidas en el conector de SQL genérico, debe tener:

- db\_datareader
- db\_datawriter

### Puertos y protocolos

En lo relativo a los puertos necesarios para el funcionamiento del controlador ODBC, consulte la documentación del proveedor de la base de datos.

## Creación de un nuevo conector

Para crear un conector de SQL genérico, en **Servicio de sincronización**, seleccione **Agente de administración** y **Crear**. Seleccione el conector **SQL genérico (Microsoft)**.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### Conectividad

El conector utiliza un archivo DSN de ODBC para la conectividad. Cree el archivo DSN mediante **Orígenes de datos ODBC** que se encuentra en el menú Inicio, en **Herramientas administrativas**. En la herramienta administrativa, cree un **DSN de archivo** que se pueda proporcionar al conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

La pantalla Conectividad es la primera que aparece cuando se crea un nuevo conector de SQL genérico. Primero tiene que proporcionar la siguiente información:

- Ruta de acceso de archivo DSN
- Autenticación
    - User Name
    - Password

La base de datos debe admitir uno de los métodos de autenticación que se mencionan a continuación.

- **Autenticación de Windows**: la base de datos de autenticación utilizará las credenciales de Windows para comprobar el usuario. En este caso, se usará la cuenta de servicio utilizada por el servicio de sincronización. Esta cuenta necesita permisos para la base de datos.
- **Autenticación de SQL**: la base de datos de autenticación utilizará el nombre de usuario y la contraseña definidos en la pantalla Conectividad para conectarse a la base de datos. Si almacena el nombre de usuario y la contraseña en el archivo DSN, las credenciales proporcionadas en la pantalla de Conectividad tendrán prioridad.
- **Autenticación de Base de datos SQL de Azure**: para obtener más información, consulte [Conexión a Base de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md).

**DN es el delimitador**: si selecciona esta opción, el DN también se utilizará como atributo de delimitador. Se puede usar para una implementación simple, aunque tiene las siguientes limitaciones:

-	El conector solo admite el tipo de objeto 1. Por lo tanto, los atributos de referencia únicamente pueden hacer referencia al mismo tipo de objeto.

**Tipo de exportación: reemplazar objeto**: durante la exportación, cuando solo se han cambiado algunos atributos, el objeto completo con todos los atributos se exportará y reemplazará el objeto existente.

### Esquema 1 (Detectar tipos de objeto)

En esta página va a configurar cómo encuentra el conector los distintos tipos de objeto en la base de datos.

Cada tipo de objeto se presentará como una partición y se configurará más adelante en **Configurar particiones y jerarquías**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Método de detección de tipos de objeto**: el conector admite estos métodos de detección de tipos de objeto.

- **Valor fijo**: proporcione la lista de tipos de objeto con una lista separada por comas. Por ejemplo, User,Group,Department. ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Tabla/Vista/Procedimiento almacenado**: indique el nombre de la tabla/vista/procedimiento almacenado y, a continuación, el nombre de columna que proporcionará la lista de tipos de objeto. Si utiliza un procedimiento almacenado, indique también los parámetros para él en el formato **[Nombre]:[Dirección]:[Valor]**. Proporcione cada parámetro en una línea independiente (use Ctrl + Entrar para obtener una nueva línea). ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Consulta SQL**: esta opción permite proporcionar una consulta SQL que devolverá una sola columna con tipos de objeto, por ejemplo, `SELECT [Column Name] FROM TABLENAME`. La columna devuelta debe ser de tipo cadena (varchar).

### Esquema 2 (Detectar tipos de atributo)

En esta página va a configurar cómo se van a detectar los nombres y tipos de atributo. Se muestran las opciones de configuración para cada tipo de objeto detectado en la página anterior.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Método de detección de tipos de atributo**: el conector admite estos métodos de detección de tipos de atributo con cada tipo de objeto detectado en la pantalla Esquema 1.

- **Tabla/Vista/Procedimiento almacenado**: proporcione el nombre de la tabla/vista/procedimiento almacenado que debe usarse para buscar los nombres de atributo. Si utiliza un procedimiento almacenado, indique también los parámetros para él en el formato **[Nombre]:[Dirección]:[Valor]**. Proporcione cada parámetro en una línea independiente (use Ctrl + Entrar para obtener una nueva línea). Para detectar los nombres de atributo en un atributo multivalor, proporcione una lista separada por comas de tablas o vistas. No se admiten escenarios multivalor si la tabla principal y secundaria tienen los mismos nombres de columna.
- **Consulta SQL**: esta opción permite proporcionar una consulta SQL que devolverá una sola columna con nombres de atributo, por ejemplo, `SELECT [Column Name] FROM TABLENAME`. La columna devuelta debe ser de tipo cadena (varchar).

### Esquema 3 (Definir delimitador y DN)

Esta página permite configurar el atributo de delimitador y de DN para cada tipo de objeto detectado. Puede seleccionar varios atributos para que el delimitador sea único.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- No se enumeran los atributos multivalor y booleanos.
- No se puede usar el mismo atributo para el DN y el delimitador, a menos que **DN es el delimitador** esté seleccionado en la página Conectividad.
- Si **DN es el delimitador** está seleccionado en la página Conectividad, esta página requiere solo el atributo de DN. Este atributo se utilizará también como el atributo de delimitador. ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### Esquema 4 (Definir tipo de atributo, referencia y dirección)

Esta página permite configurar el tipo de atributo como entero, referencia, cadena, binario o un valor booleano y dirección para cada atributo. Se muestran todos los atributos de la página **Esquema 2**, incluidos los atributos multivalor.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **DataType**: se utiliza para asignar el tipo de atributo a aquellos conocidos por el motor de sincronización. El valor predeterminado es usar el mismo tipo que el detectado en el esquema SQL, pero DateTime y Reference no son fácilmente detectables. Para estos, necesita especificar **DateTime** o **Reference**.
- **Dirección**: puede establecer la dirección del atributo en Import, Export o ImportExport. ImportExport es el valor predeterminado. ![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Notas:

- Si un tipo de atributo no es detectable por el conector, utilizará el tipo de datos String.
- **Tablas anidadas** se pueden considerar tablas de base de datos con una única columna. Oracle almacena las filas de una tabla anidada sin un orden determinado. Sin embargo, al recuperar la tabla anidada en una variable de PL/SQL, se asigna a las filas subíndices consecutivos a partir de 1. Esto le proporciona un acceso de tipo matriz a las filas individuales.
- No se admiten **VARRYS** en el conector.

### Esquema 5 (Definir partición para atributos de referencia)

En esta página podrá configurar para todos los atributos de referencia a qué partición, es decir, tipo de objeto, hace referencia un atributo.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Si utiliza **DN es el delimitador**, debe usar el mismo tipo de objeto que el que desde el que hace referencia. No se puede hacer referencia a otro tipo de objeto.

### Parámetros globales

La página Parámetros globales se utiliza para configurar la importación diferencial, el formato de fecha y hora, y el método de contraseña.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

El conector de SQL genérico admite los siguientes métodos para la importación diferencial:

- **Trigger**: consulte [Generating Delta Views Using Triggers](https://technet.microsoft.com/library/cc708665.aspx).
- **WaterMark**: se trata de un enfoque genérico y se puede utilizar con cualquier base de datos. La consulta de marca de agua se rellena previamente basándose en el proveedor de base de datos. En cada tabla/vista que se utiliza, debe estar presente una columna de marca de agua. Esta debe realizar el seguimiento de las inserciones y actualizaciones en las tablas, así como sus tablas dependientes (multivalor o secundarias). Se deben sincronizar los relojes entre el servicio de sincronización y el servidor de base de datos. Si no es así, se pueden omitir algunas entradas en la importación diferencial. Limitación:
    - La estrategia de marca de agua no admite objetos eliminados.
- **Snapshot**: (solo funciona con Microsoft SQL Server) [Generating Delta Views Using Snapshots](https://technet.microsoft.com/library/cc720640.aspx)
- **ChangeTracking** (solo funciona con Microsoft SQL Server) [Acerca del seguimiento de cambios (SQL Server)](https://msdn.microsoft.com/library/bb933875.aspx) Limitaciones:
    - Los atributos de delimitador y de DN deben ser parte de la clave principal para el objeto seleccionado en la tabla.
    - No se admite la consulta SQL durante la importación y exportación con seguimiento de cambios.

**Parámetros adicionales**: especifique la zona horaria del servidor de base de datos que indica dónde se encuentra el servidor de base de datos. Este valor se utiliza para admitir los diversos formatos de los atributos de fecha y hora.

El conector siempre almacenará fecha y fecha y hora en formato UTC. Para poder convertir correctamente la fecha y hora, se debe especificar la zona horaria del servidor de base de datos y el formato utilizado. El formato debe expresarse en formato .NET.

Durante la exportación, se debe proporcionar cada atributo de fecha y hora al conector en formato UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuración de contraseña**: el conector proporciona funcionalidades de sincronización de contraseña, y admite el establecimiento y el cambio de contraseña.

El conector proporciona dos métodos que admiten la sincronización de contraseña:

- **Procedimiento almacenado**: este método requiere dos procedimientos almacenados para admitir Establecer y Cambiar contraseña. Escriba todos los parámetros para la operación de agregación y cambio de contraseña en Establecer nombre de PA de contraseña y Cambiar parámetros de PA de contraseña respectivamente como en el ejemplo siguiente. ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Extensión de contraseña**: este método requiere el archivo DLL de extensión de contraseña (debe proporcionar el nombre del archivo DLL de extensión que está implementando la interfaz [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx)). El ensamblado de la extensión de contraseña debe colocarse en la carpeta de extensión para que el conector pueda cargar el archivo DLL en tiempo de ejecución. ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

También debe habilitar la administración de contraseñas en la página **Configurar extensiones**. ![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### Configurar particiones y jerarquías

En la página de particiones y jerarquías, seleccione todos los tipos de objeto. Cada tipo de objeto está en su propia partición.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

También puede invalidar los valores definidos en la página **Conectividad** o **Parámetros globales**.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### Configuración de delimitadores

Esta página es de solo lectura puesto que ya se ha definido el delimitador. El atributo de delimitador seleccionado se anexa siempre con el tipo de objeto para asegurarse de que seguirá siendo único en todos los tipos de objeto.

![anchors](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## Configuración del parámetro de paso de ejecución

Estos pasos se configuran en los perfiles de ejecución del conector. Estas configuraciones realizan el trabajo real de importación y exportación de datos.

### Importación completa y diferencial

El conector de SQL genérico admite la importación completa y diferencial mediante estos métodos:

- Tabla
- Ver
- Procedimiento almacenado
- Consulta SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabla/Vista**

Para importar los atributos multivalor de un objeto, debe proporcionar el nombre de tabla/vista separado por comas en **Nombre de tabla/vistas multivalor** y las condiciones de combinación correspondientes en **Condición de combinación** con la tabla principal.

Ejemplo: Desea importar el objeto Employee y todos sus atributos multivalor. Hay dos tablas con el nombre Employee (tabla principal) y Department (multivalor). Haga lo siguiente:

- Escriba **Employee** en **Tabla/Vista/PA**.
- Indique Department en **Nombre de tabla/vistas multivalor**.
- Escriba la condición de combinación entre Employee y Department en **Condición de combinación**, por ejemplo, `Employee.DEPTID=Department.DepartmentID`. ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procedimientos almacenados**

![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Si tiene una gran cantidad de datos, se recomienda implementar la paginación con los procedimientos almacenados.
- Para que el procedimiento almacenado admita la paginación, debe proporcionar el índice inicial y el índice final. Consulte: [Tutorial 25: Efficiently Paging Through Large Amounts of Data](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndex y @EndIndex se reemplazarán en tiempo de ejecución por el valor del tamaño de página correspondiente establecido en la página **Configurar paso**. Ejemplo: si el conector recupera la primera página y el tamaño de página está establecido en 500, en estas circunstancias, @StartIndex sería 1 y @EndIndex se consideraría como 500, y este valor aumentaría al ir el conector recuperando las páginas siguientes y cambiando el valor de @StartIndex y @EndIndex.
- Para ejecutar el procedimiento almacenado con parámetros, proporcione estos en el formato `[Name]:[Direction]:[Value]`. Escriba cada parámetro en una línea independiente (use Ctrl + Entrar para obtener una nueva línea).
- El conector SQL genérico también admite la operación de importación desde el entorno distribuido, por ejemplo, Servidores vinculados de Microsoft SQL Server. En caso de que se tenga que recuperar información de una tabla en un servidor vinculado, es necesario proporcionar la tabla en el formato: `[ServerName].[Database].[Schema].[TableName]`
    - En entornos distribuidos, el conector solo admite servidores vinculados de Microsoft.
- El conector de SQL genérico admite únicamente los objetos que tienen una estructura similar (tanto nombre de alias como tipo de datos) entre la información de pasos de ejecución y la detección del esquema. Si el objeto seleccionado en el esquema y la información proporcionada en el paso de ejecución es diferente, el conector de SQL no podrá admitir este tipo de escenarios.

**Consulta SQL**

![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- No se admiten consultas de conjuntos de resultados múltiples.
- La consulta de SQL admite la paginación y proporciona índice inicial e índice final como una variable para admitir la paginación.

### Importación delta

![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

La configuración de la importación diferencial requiere una mayor configuración junto con otro método compatible como la importación completa.

- Si el usuario elige el enfoque Trigger o Snapshot para realizar el seguimiento de los cambios diferenciales, puede indicar la base de datos de tablas o de instantáneas de historial en el cuadro **Nombre de base de datos de tablas o instantáneas de historial** cuadro.
- El usuario también deberá proporcionar la condición de combinación entre la tabla de historial y la tabla principal. Ejemplo: `Employee.ID=History.EmployeeID`
- El seguimiento de la transacción en la tabla principal del usuario de la tabla de historial debe proporcionar el nombre de la columna que contiene la información de la operación (como Agregar/Actualizar/Eliminar).
- Si el usuario elige WaterMark para realizar el seguimiento de los cambios diferenciales, debe proporcionar el nombre de la columna que contiene la información de la operación en **Nombre de columna de marca de agua**. Por lo tanto, el conector puede tener en cuenta esta columna al ejecutar una importación diferencial.
- La columna **Atributo de tipo de modificación** es necesaria para el tipo de cambio. Esta columna asigna un cambio que se produce en la tabla principal o una tabla multivalor a un tipo de cambio en la vista diferencial. Puede contener el tipo de cambio Modify\_Attribute para un cambio de nivel de atributo o un tipo de cambio Agregar, Modificar o Eliminar para un tipo de cambio de nivel de objeto. Si se trata de algo distinto al valor predeterminado Agregar, Modificar o Eliminar, el usuario puede definir estos valores mediante esta opción.

### Exportación

![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

El conector de SQL genérico admite la exportación mediante cuatro métodos compatibles como:

- Tabla
- Ver
- Procedimiento almacenado
- Consulta SQL

**Tabla/Vista**

Si el usuario elige la opción Tabla/Vista, el conector generará las consultas correspondientes y ejecutará la exportación.

**Procedimientos almacenados**

![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Si el usuario elige la opción Procedimiento almacenado, la exportación requiere tres procedimientos almacenados diferentes para realizar las distintas operaciones Insertar/Actualizar/Eliminar.

- **Agregar nombre de PA**: este procedimiento almacenado se ejecuta si se trata de cualquier objeto que llega al conector para su inserción en la tabla correspondiente.
- **Actualizar nombre de PA**: este procedimiento almacenado se ejecuta si se trata de cualquier objeto que llega al conector para su actualización en la tabla correspondiente.
- **Eliminar nombre de PA**: este procedimiento almacenado se ejecuta si se trata de cualquier objeto que llega al conector para su eliminación en la tabla correspondiente.
- Atributo seleccionado en el esquema usado como valor de parámetro para el procedimiento almacenado. Ejemplo: EmployeeName: INPUT: @EmployeeName (EmployeeName está seleccionado en el esquema del conector y el conector reemplaza el valor correspondiente al realizar la exportación)
- Para ejecutar el procedimiento almacenado con parámetros, escriba todos los parámetros en el formato `[Name]:[Direction]:[Value]`. Escriba cada parámetro en una línea independiente (use Ctrl + Entrar para obtener una nueva línea).

**Consulta SQL**

![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Si el usuario elige la opción Consulta SQL, la exportación requiere tres consultas diferentes para realizar las distintas operaciones Insertar/Actualizar/Eliminar.

- **Consulta Insert**: esta consulta se ejecuta si se trata de cualquier objeto que llega al conector para su inserción en la tabla correspondiente.
- **Consulta Update**: esta consulta se ejecuta si se trata de cualquier objeto que llega al conector para su actualización en la tabla correspondiente.
- **Consulta Delete**: esta consulta se ejecuta si se trata de cualquier objeto que llega al conector para su eliminación en la tabla correspondiente.
- Atributo seleccionado en el esquema usado como valor de parámetro para la consulta. Ejemplo: `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## Solución de problemas

-	Para más información acerca de cómo habilitar el registro para solucionar problemas del conector, consulte [How to Enable ETW Tracing for FIM 2010 R2 Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0128_2016-->