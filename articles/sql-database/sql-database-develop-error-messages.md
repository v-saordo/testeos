<properties
	pageTitle="Códigos de error SQL: error de conexión de base de datos | Microsoft Azure"
	description="Obtenga información acerca de los códigos de error de SQL de las aplicaciones cliente de la Base de datos SQL, como los errores de conexión de base de datos más comunes, los problemas de copia de la base de datos y los errores generales."
	keywords="código de error de SQL, acceder a SQL, error de conexión de base de datos, códigos de error de SQL"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/06/2015"
	ms.author="genemi"/>


# Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas.


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


En este artículo se enumeran los códigos de error de SQL de la aplicación cliente de la Base de datos SQL, incluidos los errores de conexión de base de datos, los errores transitorios, los errores de regulación de recursos, los problemas de copia de la base de datos y otros errores. La mayoría de las categorías son específicas de la Base de datos SQL de Azure y no se aplican a Microsoft SQL Server.

En la aplicación cliente, tiene la opción de ofrecer a su usuario un mensaje personalizado cuando se produzca cualquier error.

<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Errores de conexión de base de datos, errores transitorios y otros errores temporales

En la siguiente tabla se abordan los códigos de error de SQL para errores de pérdida de conexión y otros errores transitorios que pueden surgir cuando la aplicación intenta obtener acceso a la Base de datos SQL.


### Errores de conexión de base de datos y errores transitorios más comunes


Los errores transitorios suelen manifestarse como uno de los siguientes mensajes de error de los programas cliente:

- La base de datos <db_name> en el servidor <Azure_instance> no está disponible actualmente. Vuelva a intentar la conexión más tarde. Si el problema continúa, póngase en contacto con el servicio de soporte al cliente y proporcióneles el id. de seguimiento de sesión de <session_id>.

- La base de datos <db_name> en el servidor <Azure_instance> no está disponible actualmente. Vuelva a intentar la conexión más tarde. Si el problema continúa, póngase en contacto con el servicio de soporte al cliente y proporcióneles el id. de seguimiento de sesión de <session_id>. (Microsoft SQL Server, error: 40613)

- El host remoto cerró a la fuerza una conexión ya existente.

- System.Data.Entity.Core.EntityCommandExecutionException: Se produjo un error al ejecutar la definición del comando. Vea la excepción interna para obtener detalles. ---> System.Data.SqlClient.SqlException: Error en el nivel del transporte al recibir los resultados del servidor. (proveedor: proveedor de sesión, error: 19: La conexión física es inservible)

Los errores transitorios deberían dar lugar a que el programa cliente ejecute *lógica de reintento* que se diseña para reintentar la operación. Para obtener ejemplos de lógica de reintento, vea:


- [Ejemplos de código de desarrollo de cliente y de inicio rápido para la base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)

- [Acciones para corregir errores de conexión y errores transitorios en Base de datos SQL](sql-database-connectivity-issues.md)


### Códigos de errores transitorios


| Código de error | Gravedad | Descripción |
| ---: | ---: | :--- |
| 4060 | 16 | No se puede abrir la base de datos "%.&#x2a;ls" solicitada por el inicio de sesión. Error de inicio de sesión. |
|40197|17|Error en el servicio al procesar la solicitud. Vuelva a intentarlo. Código de error %d.<br/><br/>Recibirá este error cuando el servicio esté inactivo debido a actualizaciones de software o hardware, errores de hardware u otros problemas de conmutación por error. El código de error (%d) incrustado en el mensaje de error 40197 proporciona información adicional sobre el tipo de error o conmutación por error que se ha producido. Algunos ejemplos de los códigos de error que se incrustan dentro del mensaje de error 40197 son 40020, 40143, 40166 y 40540.<br/><br/>Al volver a conectarse al servidor de Base de datos SQL se conectará automáticamente a una copia correcta de su base de datos. La aplicación debe detectar el error 40197, registrar el código de error incrustado (%d) dentro del mensaje para solucionar problemas y volver a conectarse a la base de datos SQL hasta que los recursos estén disponibles; entonces, la conexión se establecerá de nuevo.|
|40501|20|El servicio está ocupado actualmente. Vuelva a intentar la solicitud después de 10 segundos. Identificador de incidente: %ls. Código: %d.<br/><br/>*Nota:* para obtener más información, consulte <br/>• [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md).
|40613|17|La base de datos '%.&#x2a;ls' en el servidor '%.&#x2a;ls' no está disponible actualmente. Vuelva a intentar la conexión más tarde. Si el problema continúa, póngase en contacto con el servicio de soporte al cliente y proporcióneles el id. de seguimiento de sesión de '%. & #x2a; ls'.|
|49918|16|No se puede procesar la solicitud. No hay suficientes recursos para procesar la solicitud.<br/><br/>El servicio está ocupado actualmente. Vuelva a intentar realizar la solicitud más tarde. |
|49919|16|No se procesar, crear ni actualizar la solicitud. Hay demasiadas operaciones de creación o actualización en curso para la suscripción "%ld".<br/><br/>El servicio está ocupado procesando varias solicitudes de creación o actualización para su suscripción o servidor. Actualmente las solicitudes están bloqueadas para la optimización de recursos. Consulta [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para las operaciones pendientes. Espere a que se completen solicitudes de creación o actualización pendientes o elimine una de las solicitudes pendientes y vuelva a intentar la solicitud más tarde. |
|49920|16|No se puede procesar la solicitud. Hay demasiadas operaciones en curso para la suscripción "%ld".<br/><br/>El servicio está ocupado procesando varias solicitudes para esta suscripción. Actualmente las solicitudes están bloqueadas para la optimización de recursos. Consulta [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para el estado de la operación. Espere a que las solicitudes pendientes se hayan completado o elimine una de las solicitudes pendientes y vuelva a intentar la solicitud más tarde. |


<a id="bkmk_b_database_copy_errors" name="bkmk_b_database_copy_errors">&nbsp;</a>

## Errores de copia de base de datos


En la tabla siguiente se describen los diversos errores que puede encontrarse al copiar una base de datos en la Base de datos SQL de Azure. Para más información, vea [Copiar una base de datos SQL de Azure](sql-database-copy.md).


|Código de error|Gravedad|Descripción|
|---:|---:|:---|
|40635|16|El cliente con la dirección IP '%.&#x2a;ls' está deshabilitado temporalmente.|
|40637|16|Crear copia de base de datos está deshabilitado actualmente.|
|40561|16|Error al copiar la base de datos. No existe la base de datos de origen o de destino.|
|40562|16|Error al copiar la base de datos. La base de datos de origen se ha quitado.|
|40563|16|Error al copiar la base de datos. La base de datos de destino se ha quitado.|
|40564|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo.|
|40565|16|Error al copiar la base de datos. No se permite más de una copia de base de datos simultánea para el mismo origen. Quite la base de datos de destino y vuelva a intentarlo más adelante.|
|40566|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo.|
|40567|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo.|
|40568|16|Error al copiar la base de datos. La base de datos de origen ha dejado de estar disponible. Quite la base de datos de destino e inténtelo de nuevo.|
|40569|16|Error al copiar la base de datos. La base de datos de destino ha dejado de estar disponible. Quite la base de datos de destino e inténtelo de nuevo.|
|40570|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino y vuelva a intentarlo más adelante.|
|40571|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino y vuelva a intentarlo más adelante.|


<a id="bkmk_c_resource_gov_errors" name="bkmk_c_resource_gov_errors">&nbsp;</a>

## Errores de regulación de recursos


En la tabla siguiente se muestran los errores causados por un uso excesivo de recursos mientras se trabaja con la base de datos SQL de Azure. Por ejemplo:


- Es posible que su transacción haya estado abierta demasiado tiempo.
- Puede que su transacción contenga demasiados bloqueos.
- Es posible que el programa está consumiendo demasiada memoria.
- Puede que el programa está consumiendo demasiado espacio en `TempDb`.


**Sugerencia:** el vínculo siguiente ofrece más información que se aplica a la mayoría de los errores de esta sección o a todos ellos:


- [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md)


|Código de error|Gravedad|Descripción|
|---:|---:|:---|
|10928|20|Id. de recurso: %d. El límite %s para la base de datos es %d y se ha alcanzado. Para más información, vea [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>El id. de recurso indica el recurso que alcanzó el límite. Para subprocesos de trabajo, el id. de recurso = 1. Para las sesiones, el identificador de recurso = 2.<br/><br/>*Nota:* para obtener más información sobre este error y cómo resolverlo, consulte:<br/>• [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md). |
|10929|20|Id. de recurso: %d. La garantía mínima de %s es de %d, el límite máximo es %d y el uso actual de la base de datos es %d. Sin embargo, el servidor está demasiado ocupado en estos momentos para admitir solicitudes mayores que %d para esta base de datos. Para obtener más información, vea [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). De lo contrario, vuelva a intentarlo más tarde.<br/><br/>El id. de recurso indica el recurso que alcanzó el límite. Para subprocesos de trabajo, el id. de recurso = 1. Para las sesiones, el id. de recurso = 2.<br/><br/>*Nota:* para más información sobre este error y cómo resolverlo, vea:<br/>• [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md).|
|40544|20|La base de datos ha alcanzado su cuota de tamaño. Cree particiones o elimine datos, quite índices o consulte la documentación para obtener soluciones posibles.|
|40549|16|La sesión terminó porque tiene una transacción de larga duración. Intente reducir la transacción.|
|40550|16|La sesión ha terminado porque ha adquirido demasiados bloqueos. Intente leer o modificar menos filas en una sola transacción.|
|40551|16|La sesión ha terminado debido al uso excesivo de `TEMPDB`. Intente modificar la consulta para reducir el uso de espacio de la tabla temporal.<br/><br/>*Sugerencia: * si usa objetos temporales, puede ahorrar espacio en la base de datos `TEMPDB` si quita los objetos temporales cuando la sesión ya no los necesite.|
|40552|16|La sesión ha terminado debido al excesivo uso de espacio del registro de transacciones. Intente modificar menos filas en una sola transacción.<br/><br/>*Sugerencia:* si realiza inserciones masivas con la utilidad `bcp.exe` o la clase `System.Data.SqlClient.SqlBulkCopy`, intente usar las opciones `-b batchsize`o `BatchSize` para limitar el número de filas copiadas al servidor en cada transacción. Si está volviendo a crear un índice con la instrucción `ALTER INDEX`, intente usar la opción `REBUILD WITH ONLINE = ON`.|
|40553|16|La sesión ha terminado debido al uso excesivo de la memoria. Intente modificar su consulta para procesar menos filas.<br/><br/>*Sugerencia:* la reducción del número de operaciones `ORDER BY` y `GROUP BY` en el código de Transact-SQL reduce los requisitos de memoria de la consulta.|


Para obtener más información sobre la regulación de recursos y los errores asociados, vea:


- [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md)



<a id="bkmk_e_general_errors" name="bkmk_e_general_errors">&nbsp;</a>

## Errores generales


En la tabla siguiente se muestran todos los errores generales que no pertenecen a ninguna categoría anterior.


|Código de error|Gravedad|Descripción|
|---:|---:|:---|
|15006|16|<AdministratorLogin> no es un nombre válido porque contiene caracteres no válidos.|
|18452|14|Error de inicio de sesión. El inicio de sesión se realiza desde un dominio que no es de confianza y no se puede utilizar con autenticación de Windows.%.&#x2a;ls (Los inicios de sesión de Windows no se admiten en esta versión de SQL Server.).|
|18456|14|Error de inicio de sesión del usuario '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls (Error de inicio de sesión del usuario "%.&#x2a;ls". Error de cambio de contraseña. El cambio de contraseña durante el inicio de sesión no se admite en esta versión de SQL Server.)|
|18470|14|Error de inicio de sesión del usuario '%.&#x2a;ls'. Motivo: la cuenta está deshabilitada.%.&#x2a;ls|
|40014|16|No se pueden utilizar varias bases de datos en la misma transacción.|
|40054|16|No se admiten las tablas sin un índice agrupado en esta versión de SQL Server. Crear un índice agrupado e inténtelo de nuevo.|
|40133|15|No se admite esta operación en esta versión de SQL Server.|
|40506|16|El SID especificado no es válido para esta versión de SQL Server.|
|40507|16|No se puede invocar '%.&#x2a;ls' con parámetros en esta versión de SQL Server.|
|40508|16|No se admite la instrucción de USE para cambiar entre bases de datos. Use una nueva conexión para conectarse a una base de datos diferente.|
|40510|16|No se admite la instrucción '%.&#x2a;ls' en esta versión de SQL Server.|
|40511|16|No se admite la instrucción integrada '%.&#x2a;ls' en esta versión de SQL Server.|
|40512|16|No se admite la característica desusada '%ls' en esta versión de SQL Server.|
|40513|16|No se admite la variable del servidor '%.&#x2a;ls' en esta versión de SQL Server.|
|40514|16|No se admite '%ls' en esta versión de SQL Server.|
|40515|16|No se admite referencia al nombre de la base de datos o del servidor en '%. & #x2a; ls' en esta versión de SQL Server.|
|40516|16|Los objetos temporales globales no se admiten en esta versión de SQL Server.|
|40517|16|No se admite la opción de palabra clave o instrucción '%.&#x2a;ls' en esta versión de SQL Server.|
|40518|16|No se admite el comando DBCC '%.&#x2a;ls' en esta versión de SQL Server.|
|40520|16|La clase protegible '%S\_MSG' no se admite en esta versión de SQL Server.|
|40521|16|La clase protegible '%S\_MSG' no se admite en el ámbito de servidor en esta versión de SQL Server.|
|40522|16|El tipo de entidad de seguridad de base de datos '%.&#x2a;ls' no se admite en esta versión de SQL Server.|
|40523|16|La creación de usuario implícita '%.&#x2a;ls' no se admite en esta versión de SQL Server. Cree el usuario de forma explícita antes de usarlo.|
|40524|16|El tipo de datos '%.&#x2a;ls' no se admite en esta versión de SQL Server.|
|40525|16|WITH '%ls' no se admite en esta versión de SQL Server.|
|40526|16|El proveedor de conjuntos de filas '%.&#x2a;ls' no se admite en esta versión de SQL Server.|
|40527|16|Los servidores vinculados no se admiten en esta versión de SQL Server.|
|40528|16|Los usuarios no se pueden asignar a certificados, claves asimétricas o inicios de sesión de Windows en esta versión de SQL Server.|
|40529|16|La función integrada '%.&#x2a;ls' en el contexto de suplantación no se admite en esta versión de SQL Server.|
|40532|11|No se puede abrir el servidor "%.&#x2a;ls" solicitado por el inicio de sesión. Error de inicio de sesión.|
|40553|16|La sesión ha terminado debido al uso excesivo de la memoria. Intente modificar la consulta para procesar menos filas.<br/><br/>*Nota:* la reducción del número de operaciones `ORDER BY` y `GROUP BY` en el código Transact-SQL ayuda a reducir los requisitos de memoria de su consulta.|
|40604|16|No se pudo CREATE/ALTER DATABASE porque superaría la cuota del servidor.|
|40606|16|Adjuntar base de datos no se admite en esta versión de SQL Server.|
|40607|16|Los inicios de sesión de Windows no se admiten en esta versión de SQL Server.|
|40611|16|Los servidores pueden tener como máximo 128 reglas de firewall definidas.|
|40614|16|La dirección IP de inicio de la regla de firewall no puede superar la dirección IP final.|
|40615|16|No se puede abrir el servidor '{0}' solicitado por el inicio de sesión. No está permitido que el cliente con la dirección IP '{1}' acceda al servidor. Para habilitar el acceso, use el Portal de bases de datos SQL o ejecute sp\_set\_firewall\_rule en la base de datos maestra para crear una regla de firewall para esta dirección IP o intervalo de direcciones. Puede tardar hasta cinco minutos que este cambio surta efecto.|
|40617|16|El nombre de regla de firewall que comienza por <rule name> es demasiado largo. La longitud máxima es 128.|
|40618|16|El nombre de la regla de firewall no puede estar vacío.|
|40620|16|Error de inicio de sesión del usuario "%.&#x2a;ls". Error de cambio de contraseña. El cambio de contraseña durante el inicio de sesión no se admite en esta versión de SQL Server.|
|40627|20|La operación en el servidor '{0}' y la base de datos '{1}' está en curso. Espere algunos minutos antes de volver a intentarlo.|
|40630|16|Error de validación de contraseña. La contraseña no cumple los requisitos de directiva porque es demasiado corta.|
|40631|16|La contraseña especificada es demasiado larga. No debe tener más de 128 caracteres.|
|40632|16|Error de validación de contraseña. La contraseña no cumple los requisitos de directiva porque no es lo suficientemente compleja.|
|40636|16|No se puede usar un nombre de base de datos reservado '%.&#x2a;ls' en esta operación.|
|40638|16|Id. de suscripción no válido <subscription-id>. La suscripción no existe.|
|40639|16|La solicitud no se ajusta al esquema: <schema error>.|
|40640|20|El servidor detectó una excepción inesperada.|
|40641|16|La ubicación especificada no es válida.|
|40642|17|El servidor está demasiado ocupado. Inténtelo de nuevo más tarde.|
|40643|16|El valor del encabezado x-ms-version especificado no es válido.|
|40644|14|No se pudo autorizar el acceso a la suscripción especificada.|
|40645|16|El nombre de servidor <servername> no puede estar vacío ni ser NULL. Solo puede contener letras minúsculas de la 'a' a la 'z', los números del 0 al 9 y guiones. El guión no puede estar al principio ni al final del nombre.|
|40646|16|El id. de suscripción no puede estar vacío.|
|40647|16|La suscripción <subscription-id no tiene nombre de servidor.|
|40648|17|Se han realizado demasiadas solicitudes. Inténtelo de nuevo más tarde.|
|40649|16|Se ha especificado un content-type no válido. Solo se admite application/xml.|
|40650|16|La suscripción <subscription-id> no existe o no está lista para la operación.|
|40651|16|No se pudo crear el servidor porque la suscripción <subscription-id> está deshabilitada.|
|40652|16|No se puede mover ni crear un servidor. La suscripción <subscription-id> excederá la cuota|
|40671|17|Error de comunicación entre la puerta de enlace y el servicio de administración. Inténtelo de nuevo más tarde.|
|40852|16|No se puede abrir la base de datos '%.*ls' del servidor '%.*ls' solicitada por el inicio de sesión. Solo se permite el acceso a la base de datos mediante una cadena de conexión habilitada para seguridad. Para obtener acceso a esta base de datos, modifique sus cadenas de conexión que contienen 'secure' en el FQDN del servidor. -'server name'.database.windows.net debe modificarse para 'server name'.database.`secure`.windows.net.| 
|45168|16|El sistema de SQL Azure está bajo carga y está colocando un límite superior en operaciones DB CRUD simultáneas para un único servidor (por ejemplo, crear base de datos). El servidor especificado en el mensaje de error ha superado el número máximo de conexiones simultáneas. Inténtelo de nuevo más tarde.| 
|45169|16|El sistema de SQL Azure está bajo carga y está colocando un límite superior en operaciones CRUD de servidor simultáneas para una única suscripción (por ejemplo, crear servidor). La suscripción especificada en el mensaje de error ha superado el número máximo de conexiones simultáneas y se denegó la solicitud. Inténtelo de nuevo más tarde.|


## Vínculos relacionados

- [Instrucciones y limitaciones generales de Base de datos SQL de Azure](sql-database-general-limitations.md)
- [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md)

<!---HONumber=AcomDC_0204_2016-->