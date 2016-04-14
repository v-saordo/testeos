<properties 
	pageTitle="Usar el emulador de almacenamiento de Azure para desarrollo y pruebas | Microsoft Azure" 
	description="El emulador de almacenamiento de Azure ofrece un entorno de desarrollo local gratuito para desarrollo y pruebas frente al almacenamiento de Azure. Obtenga información sobre el emulador de almacenamiento, incluido cómo se autentican las solicitudes, cómo conectarse al emulador de la aplicación y cómo usar la herramienta de la línea de comandos." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/21/2016" 
	ms.author="tamram"/>

# Uso del emulador de almacenamiento de Azure para desarrollo y pruebas

## Información general

El emulador de almacenamiento de Microsoft Azure proporciona un entorno local que emula los servicios de Azure de blob, cola y tabla para fines de desarrollo. Mediante el emulador de almacenamiento, puede probar la aplicación en los servicios de almacenamiento local, sin crear una suscripción a Azure ni incurrir en ningún gasto. Cuando esté satisfecho con el funcionamiento de la aplicación en el emulador, puede cambiar al uso de una cuenta de almacenamiento de Azure en la nube.

> [AZURE.NOTE] El emulador de almacenamiento se encuentra disponible con el [SDK de Microsoft Azure](https://azure.microsoft.com/downloads/). También puede instalar el emulador de almacenamiento como un paquete independiente. Para configurar el emulador de almacenamiento, debe tener privilegios de administrador en el equipo.
>  
> Tenga en cuenta que no se garantiza que los datos que se crean en una versión del emulador de almacenamiento estén disponibles cuando se utilice una versión diferente. Si necesita conservar los datos a largo plazo, es recomendable que almacene esos datos en una cuenta de almacenamiento de Azure y no en el mismo emulador de almacenamiento.

## Cómo funciona el emulador de almacenamiento.
 
El emulador de almacenamiento usa una instancia de Microsoft SQL Server local, así como el sistema de archivos local para emular los servicios de almacenamiento de Azure. De manera predeterminada, el emulador de almacenamiento usa una base de datos en Microsoft SQL Server 2012 Express LocalDB. Puede configurar el emulador de almacenamiento para acceder a una instancia local de SQL Server en lugar de una de LocalDB. Vea [Iniciar e inicializar el emulador de almacenamiento](#start-and-initialize-the-storage-emulator) a continuación para obtener más información.

Puede instalar SQL Server Management Studio Express para administrar la instalación de LocalDB. El emulador de almacenamiento se conecta a SQL Server o LocalDB mediante la autenticación de Windows.

Existen algunas diferencias de funcionalidad entre el emulador de almacenamiento y los servicios de almacenamiento de Azure. Para obtener más información sobre estas diferencias, vea [Diferencias entre el emulador de almacenamiento y el almacenamiento de Azure](#differences-between-the-storage-emulator-and-azure-storage).

## Autenticar solicitudes frente al emulador de almacenamiento

De la misma manera que con el almacenamiento de Azure en la nube, se deben autenticar todas las solicitudes que realice contra el emulador de almacenamiento, a menos que sea una solicitud anónima. Puede autenticar solicitudes contra el emulador de almacenamiento mediante la autenticación de clave compartida o con una firma de acceso compartido (SAS).

### Autenticación con credenciales de clave compartida

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Para obtener más detalles sobre las cadenas de conexión, vea [Configuración de las cadenas de conexión de Almacenamiento de Azure](storage-configure-connection-string.md).

### Autenticación con una firma de acceso compartido 

Algunas bibliotecas de cliente de almacenamiento de Azure, como la biblioteca Xamarin, solo admiten la autenticación con un token de firma de acceso compartido (SAS). Necesitará crear este token SAS mediante una herramienta o aplicación que admite la autenticación de clave compartida. Una forma sencilla de generar el token SAS es mediante Azure PowerShell:

1. Instale Azure PowerShell si todavía no lo ha hecho. Se recomienda usar la versión más reciente de los cmdlets de Azure PowerShell. Vea [Instalación y configuración de Azure PowerShell](../articles/powershell-install-configure.md#Install) para obtener instrucciones de instalación.

2. Abra Azure PowerShell y ejecute los comandos siguientes. No olvide reemplazar *ACCOUNT\_NAME* y *ACCOUNT\_KEY==* con sus propias credenciales. Reemplace *CONTAINER\_NAME* por el nombre que desee.

		$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
		
		New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
		
		$now = Get-Date 
		
		New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

El URI de la firma de acceso compartido resultante para el nuevo contenedor debe ser similar al siguiente:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La firma de acceso compartido creada con este ejemplo es válida durante un día. La firma concede acceso completo (por ejemplo, lectura, escritura, eliminación, enumeración) para blobs situados dentro del contenedor.

Para obtener más información sobre firmas de acceso compartido, vea [Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).


## Iniciar e inicializar el emulador de almacenamiento

Para iniciar el emulador de almacenamiento de Azure, seleccione el botón Inicio o pulse la tecla Windows. Comience a escribir **Emulador de almacenamiento de Azure** y seleccione el emulador de almacenamiento en la lista de aplicaciones.

Cuando se ejecuta el emulador, verá un icono en el área de notificación de la barra de tareas de Windows.

Cuando se inicie el emulador de almacenamiento, aparecerá una ventana de línea de comandos. Puede usar esta ventana de la línea de comandos para iniciar y detener el emulador de almacenamiento, así como para borrar datos, obtener el estado actual e inicializar el emulador. Para obtener más información, vea [Referencia de la herramienta de la línea de comandos del emulador de almacenamiento](#storage-emulator-command-line-tool-reference).

Cuando se cierra la ventana de la línea de comandos, el emulador de almacenamiento continuará ejecutándose. Para abrir de nuevo la línea de comandos, siga los pasos anteriores como si fuera a iniciar el emulador de almacenamiento.

Al ejecutar por primera vez el emulador de almacenamiento, el entorno de almacenamiento local se inicia automáticamente. El proceso de inicialización crea una base de datos en LocalDB y se reserva puertos HTTP para cada servicio de almacenamiento local.

El emulador de almacenamiento se instala de manera predeterminada en el directorio C:\\Archivos de programa (x86)\\Microsoft SDKs\\Azure\\Storage Emulator.

### Inicializar el emulador de almacenamiento para usar otra base de datos SQL

La herramienta de la línea de comandos del emulador de almacenamiento se puede usar para inicializar el emulador de almacenamiento para que señale a una instancia de base de datos SQL distinta a la instancia LocalDB predeterminada. Tenga en cuenta que debe estar ejecutando la herramienta de la línea de comandos con privilegios administrativos para inicializar la base de datos back-end para el emulador de almacenamiento:

1. Haga clic en el botón **Inicio** o pulse la tecla **Windows**. Comience a escribir `Azure Storage Emulator` y selecciónelo cuando aparezca abrir la herramienta de la línea de comandos del emulador de almacenamiento.
2. En la ventana del símbolo del sistema, escriba el siguiente comando, donde `<SQLServerInstance>` es el nombre de la instancia de SQL Server. Para utilizar LocalDb, especifique `(localdb)\v11.0` como instancia de SQL Server.

		AzureStorageEmulator init /server <SQLServerInstance> 
    
	También puede usar el siguiente comando, el cual indicará al emulador que utilice la instancia predeterminada de SQL Server:

    	AzureStorageEmulator init /server .\\ 

	O bien, puede usar el comando siguiente, que reinicializa la base de datos a la instancia de LocalDB predeterminada:

    	AzureStorageEmulator init /forceCreate 

Para obtener más información acerca de estos comandos, vea la [Referencia de la herramienta de línea de comandos del emulador de almacenamiento](#storage-emulator-command-line-tool-reference).

## Direccionar los recursos en el emulador de almacenamiento

Los extremos de servicio para el emulador de almacenamiento son diferentes de los de una cuenta de almacenamiento de Azure. La diferencia se debe al hecho de que el equipo local no realiza la resolución de nombres de dominio, por lo que los extremos del emulador de almacenamiento requieren una dirección local en lugar de un nombre de dominio.

Cuando direccione un recurso en una cuenta de almacenamiento de Azure, usa el siguiente esquema, donde el nombre de la cuenta forma parte del nombre de host del URI y el recurso que se está tratando forma parte de la ruta de acceso del URI:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Por ejemplo, el siguiente URI es una dirección válida para un blob en una cuenta de almacenamiento de Azure:

	https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

En el emulador de almacenamiento, dado que el equipo local no lleva a cabo la resolución de nombres de dominio, el nombre de la cuenta forma parte de la ruta de acceso del URI en lugar del nombre de host. Use el siguiente esquema para un recurso que se ejecuta en el emulador de almacenamiento:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Por ejemplo, la siguiente dirección se puede usar para obtener acceso a un blob en el emulador de almacenamiento:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Los extremos de servicio para el emulador de almacenamiento son:

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### Direccionar la cuenta secundaria con RA-GRS

A partir de la versión 3.1, la cuenta del emulador de almacenamiento admite la replicación con redundancia geográfica con acceso de lectura (RA-GRS). Para los recursos de almacenamiento en la nube y en el emulador local, puedes obtener acceso a la ubicación de la cuenta secundaria si anexa -secondary al nombre de la cuenta. Por ejemplo, la siguiente dirección se puede usar para obtener acceso a un blob usando la cuenta secundaria de solo lectura en el emulador de almacenamiento:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Para el acceso mediante programación a la cuenta secundaria con el emulador de almacenamiento, usa la biblioteca de cliente de almacenamiento para la versión 3.2 de .NET o una versión posterior. Consulte la [Biblioteca del cliente de Almacenamiento de Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obtener más información.

## Referencia de la herramienta de línea de comandos del emulador de almacenamiento

A partir de la versión 3.0, al iniciar el emulador de almacenamiento, se abrirá una ventana emergente con la ventana de la línea de comandos. Use la ventana de la línea de comandos para iniciar y detener el emulador, así como para consultar el estado y realizar otras operaciones.

> [AZURE.NOTE] Si tiene instalado el emulador de proceso de Microsoft Azure, aparecerá un icono de la bandeja del sistema al iniciar el emulador de almacenamiento. Haga clic con el botón secundario en el icono para abrir un menú que ofrece en forma de gráficos las opciones de iniciar y detener el emulador de almacenamiento.

### Sintaxis de la línea de comandos

	AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### Opciones

Para ver la lista de opciones, escriba `/help` en el símbolo del sistema.

| Opción | Descripción | Comando | Argumentos |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Iniciar** | Inicia el emulador de almacenamiento. | `AzureStorageEmulator start [-inprocess]` | *-inprocess*: inicia el emulador en el proceso actual en lugar de crear un nuevo proceso. |
| **Detención** | Detiene el emulador de almacenamiento. | `AzureStorageEmulator stop` | |
| **Estado** | Imprime el estado del emulador de almacenamiento. | `AzureStorageEmulator status` | |
| **Borrar** | Borra los datos de todos los servicios especificados en la línea de comandos. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*: borra datos de blobs. <br/>*cola*: borra los datos de la cola. <br/>*tabl*: borra los datos de la tabla. <br/>*todos*: borra todos los datos de todos los servicios. |
| **Init** | Realiza una inicialización única para configurar el emulador. | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server nombre del servidor\\nombre de la instancia*: especifica el servidor que hospeda la instancia de SQL. <br/>*-sqlinstance instanceName*: especifica el nombre de la instancia SQL que se va a usar en la instancia del servidor predeterminada. <br/>*-forcecreate*: fuerza la creación de la base de datos SQL, incluso si ya existe. <br/>*-inprocess*: realiza la inicialización en el proceso actual en lugar de generar un nuevo proceso. Tiene que iniciar el proceso actual con permisos elevados para realizar la inicialización. |
                                                                                                                  
## Diferencias entre el emulador de almacenamiento y el almacenamiento de Azure

Dado que el emulador de almacenamiento es un entorno emulado que se ejecuta en una instancia de SQL local, hay algunas diferencias de funcionalidad entre el emulador y una cuenta de almacenamiento de Azure en la nube:

- El emulador de almacenamiento es compatible con una sola cuenta fija y una clave de autenticación ya conocida.

- El emulador de almacenamiento no es un servicio de almacenamiento escalable y no será compatible con un gran número de clientes simultáneos.

- Como se describe en [Direccionar los recursos en el emulador de almacenamiento](#addressing-resources-in-the-storage-emulator), los recursos se tratan de forma diferente en el emulador de almacenamiento frente a una cuenta de almacenamiento de Azure. Esta diferencia se debe al hecho de que la resolución de nombres de dominio está disponible en la nube, pero no en el equipo local.

- A partir de la versión 3.1, la cuenta del emulador de almacenamiento admite la replicación con redundancia geográfica con acceso de lectura (RA-GRS). En el emulador, todas las cuentas tienen RA-GRS habilitado y no hay ningún retraso entre las réplicas principal y secundaria. Las operaciones Get Blob Service Stats, Get Queue Service Stats y Get Table Service Stats son compatibles con la cuenta secundaria y siempre devolverán el valor del elemento de respuesta `LastSyncTime` como la hora actual según la base de datos SQL subyacente.

	Para el acceso mediante programación a la cuenta secundaria con el emulador de almacenamiento, usa la biblioteca de cliente de almacenamiento para la versión 3.2 de .NET o una versión posterior. Consulte la [Biblioteca del cliente de Almacenamiento de Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obtener más información.

- El servicio de archivo y los extremos de servicio de protocolo SMB no se admiten actualmente en el emulador de almacenamiento.

- El emulador de almacenamiento devuelve un error VersionNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta) si usa una versión de los servicios de almacenamiento que no es compatible aún con la versión del emulador que está usando.

### Diferencias en el almacenamiento de blobs 

Las siguientes diferencias se aplican al almacenamiento de blobs en el emulador:

- El emulador de almacenamiento solo admite tamaños de blobs de hasta 2 GB.

- Una operación Put Blob puede ser correcta con un blob que existe en el emulador de almacenamiento y tiene una concesión activa, incluso si el identificador de concesión no se ha especificado como parte de la solicitud.

- El emulador no admite operaciones de blob en anexos. Intentando realizar una operación en un blob en anexos, devuelve un error FeatureNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta).

### Diferencias en el almacenamiento de tabla 

Las siguientes diferencias se aplican al almacenamiento de tablas en el emulador:

- Las propiedades de fecha en el servicio Tabla del emulador de almacenamiento solo admiten el intervalo admitido por SQL Server 2005 (*es decir*, tienen que ser posteriores al 1 de enero de 1753). Todas las fechas anteriores al 1 de enero de 1753 se cambiarán a este valor. La precisión de las fechas se limita a la precisión de SQL Server 2005, lo que significa que las fechas son precisas hasta 1/300 de un segundo.

- El emulador de almacenamiento admite valores de propiedad de clave de fila y de clave de partición de menos de 512 bytes. Además, el tamaño total del nombre de la cuenta, el nombre de la tabla y los nombres de las propiedades de clave juntos no pueden superar los 900 bytes.

- El tamaño total de una fila de una tabla en el emulador de almacenamiento se limita a menos de 1 MB.

- En el emulador de almacenamiento, las propiedades de tipo de datos `Edm.Guid` o `Edm.Binary` solo admiten los operadores de comparación `Equal (eq)` y `NotEqual (ne)` en las cadenas de filtro de consultas.

### Diferencias en el almacenamiento de cola

No hay ninguna diferencia específica del almacenamiento en cola en el emulador.

## Notas de la versión del emulador de almacenamiento

### Versión 4.2

- El emulador de almacenamiento admite ahora la versión 2015-04-05 de los servicios de almacenamiento en los extremos de servicio Blob, Cola y Tabla.

### Versión 4.1

- El emulador de almacenamiento admite ahora la versión 2015-02-21 de los servicios de almacenamiento en los extremos de servicio Blob, Cola y Tabla, con la excepción de las nuevas características de blob en anexos. 

- El emulador de almacenamiento ahora devuelve un mensaje de error descriptivo si usa una versión de los servicios de almacenamiento que no es compatible aún con esa versión del emulador. Se recomienda usar la versión más reciente del emulador. Si se produce un error VersionNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta), descargue la última versión del emulador de almacenamiento.

- Se ha corregido un error en el que una condición de carrera hacía que los datos de entidad de tabla fuesen incorrectos durante las operaciones de mezcla simultáneas.

### Versión 4.0

- Se ha cambiado el nombre de ejecutable del emulador de almacenamiento a *AzureStorageEmulator.exe*.

### Versión 3.2
- El emulador de almacenamiento admite ahora la versión 2014-02-14 de los servicios de almacenamiento en los extremos de servicio de blob, cola y tabla. Tenga en cuenta que los extremos de servicio de archivos no se admiten actualmente en el emulador de almacenamiento. Consulte [Versiones de los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) para obtener información acerca de la versión 2014-02-14.

### Versión 3.1
- Ahora se admite el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) en el emulador de almacenamiento. Las API de las operaciones Get Blob Service Stats, Get Queue Service Stats y Get Table Service Stats son compatibles con la cuenta secundaria y siempre devolverán el valor del elemento de respuesta LastSyncTime como la hora actual según la base de datos SQL subyacente. Para el acceso mediante programación a la cuenta secundaria con el emulador de almacenamiento, usa la biblioteca de cliente de almacenamiento para la versión 3.2 de .NET o una versión posterior. Consulte la Documentación de referencia de la biblioteca cliente de Almacenamiento de Microsoft Azure para .NET para obtener más información.

### Versión 3.0
- El emulador de almacenamiento de Azure ya no se incluye en el mismo paquete que el emulador de proceso.

- La interfaz de usuario gráfica del emulador de almacenamiento se ha sustituido por una interfaz de línea de comandos mediante scripts. Para obtener más información sobre la interfaz de la línea de comandos, vea la Referencia de la herramienta de línea de comandos del emulador de almacenamiento. La interfaz gráfica seguirá estando presente en la versión 3.0, pero solo se puede acceder a ella cuando se instala el emulador de proceso con el botón secundario en el icono de la bandeja del sistema y se selecciona la opción de mostrar IU del emulador de almacenamiento.

- La versión 2013-08-15 de los servicios de almacenamiento de Azure ahora es totalmente compatible. (Anteriormente esta versión solo era compatible con versión la versión 2.2.1 Preview del emulador de almacenamiento.)

<!---HONumber=AcomDC_0224_2016-->
