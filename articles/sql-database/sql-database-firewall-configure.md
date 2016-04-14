<properties
   pageTitle="Configurar un firewall de base de datos SQL | Microsoft Azure"
   description="Obtenga información acerca de cómo configurar un firewall de base de datos SQL mediante las reglas de firewall de nivel de servidor y de nivel de base de datos, para administrar el acceso."
   keywords="firewall de base de datos"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/18/2016"
   ms.author="rickbyh"/>

# Cómo configurar un firewall de base de datos SQL de Azure

Base de datos SQL de Microsoft Azure ofrece un servicio de base de datos relacional para Azure y otras aplicaciones basadas en Internet. Para mantener sus datos protegidos, el firewall de la Base de datos SQL impide todo acceso al servidor de Base de datos SQL, excepto a aquellos equipos a los cuales haya concedido permiso. El firewall otorga acceso a la base de datos según la dirección IP de origen de cada solicitud.

Para configurar el firewall de la base de datos, debe crear reglas de firewall que especifiquen intervalos de direcciones IP aceptables. Puede crear reglas de firewall en los niveles de servidor y base de datos.

- **Reglas de firewall de nivel de servidor:** estas reglas permiten a los clientes tener acceso a todo el servidor de la Base de datos SQL de Azure; es decir, a todas las bases de datos que se encuentren en el mismo servidor lógico. Estas reglas se almacenan en la base de datos **maestra**.
- **Reglas de firewall de nivel de base de datos:** estas reglas permiten a los clientes tener acceso a las bases de datos individuales que se encuentran dentro del servidor de la Base de datos SQL de Azure. Estas reglas se crean por cada base de datos y se almacenan en las bases de datos individuales (incluidas las **maestras**). Estas reglas pueden resultar de utilidad al restringir el acceso a determinadas bases de datos (seguras) dentro del mismo servidor lógico.

**Recomendación:** Microsoft le recomienda que use reglas de firewall de nivel de base de datos siempre que le sea posible, para así hacer que la base de datos sea más portátil. Use reglas de firewall de nivel de servidor cuando tenga muchas bases de datos con los mismos requisitos de acceso y no quiera dedicar tiempo a configurar individualmente cada base de datos.

**Sobre las federaciones**: la implementación actual de las federaciones finalizará con los niveles de servicio Web y Business. Considere la posibilidad de implementar soluciones de particionamiento personalizadas para maximizar la escalabilidad, la flexibilidad y el rendimiento. Para obtener más información sobre el particionamiento personalizado, consulte [Escalado horizontal de Bases de datos SQL de Azure](https://msdn.microsoft.com/library/dn495641.aspx).

> [AZURE.NOTE] Si crea una federación de base de datos en la Base de datos SQL de Azure donde la base de datos raíz contiene reglas de firewall de nivel de base de datos, las reglas no se copian en las bases de datos de miembros de federación. Si necesita reglas de firewall de nivel de base de datos para los miembros de federación, tendrá que volver a crear las reglas para los miembros de federación. Sin embargo, si divide un miembro de federación con una regla de firewall de nivel de base de datos en nuevos miembros de federación mediante la instrucción ALTER FEDERATION … SPLIT, los nuevos miembros de destino tendrán las mismas reglas de firewall de nivel de base de datos como el miembro de federación de origen. Para obtener más información sobre las federaciones, consulte [Federaciones en la Base de datos SQL de Azure](https://msdn.microsoft.com/library/hh597452.aspx).

## Introducción al firewall de la Base de datos SQL

Inicialmente, todo el acceso a su servidor de Base de datos SQL de Azure está bloqueado por el firewall. Para comenzar a usar el servidor de Base de datos SQL de Azure, debe ir al Portal de Azure y especificar una o más reglas de firewall de nivel de servidor que permitan el acceso al servidor de Base de datos SQL de Azure. Use las reglas de firewall para especificar qué intervalos de direcciones IP de Internet se permiten y si las aplicaciones de Azure pueden intentar conectarse o no al servidor de Base de datos SQL de Azure.

Sin embargo, si desea conceder acceso de manera selectiva solo a una de las bases de datos en su servidor de Base de datos SQL de Azure, debe crear una regla de nivel de base de datos para la base de datos necesaria con un intervalo de direcciones IP que está más allá del intervalo de direcciones IP especificado en la regla de firewall de nivel de servidor y asegurarse de que la dirección IP del cliente se encuentra en el intervalo especificado en la regla de nivel de base de datos.

Los intentos de conexión desde Internet y Azure deben atravesar primero el firewall antes de poder alcanzar su base de datos o servidor de Base de datos SQL de Azure, como se muestra en el siguiente diagrama.

   ![Diagrama donde se describe la configuración del firewall.][1]

## Conexión desde Internet

Cuando un equipo intenta conectarse al servidor de bases de datos desde Internet, el firewall comprueba la dirección IP de origen de la solicitud con el conjunto completo de nivel de servidor y (en caso necesario) las reglas de firewall de nivel de base de datos:

- Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión se concede al servidor de Base de datos SQL de Azure.

- Si la dirección IP de la solicitud no está comprendida en uno de los intervalos especificados en las regla de firewall de nivel de servidor, se comprueban las reglas de firewall de nivel de base de datos. Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión solo se concede a la base de datos que tiene una regla de nivel de base de datos coincidente.

- Si la dirección IP de la solicitud no se encuentra dentro de los intervalos especificados en cualquiera de las reglas de firewall de nivel de servidor o de base de datos, la solicitud de la conexión genera error.

> [AZURE.NOTE] Para obtener acceso a la Base de datos SQL de Azure desde el equipo local, asegúrese de que el firewall de su red y el equipo local permite la comunicación saliente en el puerto TCP 1433.


## Conexión desde Azure

Cuando una aplicación desde Azure intenta conectarse a su servidor de s de datos, el firewall comprueba que se permiten las conexiones de Azure. Una configuración del firewall con dirección inicial y final igual a 0.0.0.0 indica que se permiten estas conexiones. Si no se permite el intento de conexión, la solicitud no alcanza el servidor de Base de datos SQL de Azure.

Puede habilitar conexiones desde Azure de dos maneras:

- En el [Portal de Microsoft Azure](https://portal.azure.com/), active la casilla de verificación **Permitir que los servicios de Microsoft Azure accedan al servidor** al crear un nuevo servidor.

- En el [Portal clásico](http://go.microsoft.com/fwlink/p/?LinkID=161793), en la pestaña **Configurar** de un servidor, en la sección **Servicios permitidos**, haga clic en **Sí** para **Servicios de Microsoft Azure**.

## Crear de la primera regla de firewall de nivel de servidor

La primera configuración del firewall de nivel de servidor puede crearse mediante el [Portal de Azure](https://portal.azure.com/), mediante programación con la API de REST o con Azure PowerShell. Las reglas de firewall de nivel de servidor posteriores se pueden crear y administrar mediante estos métodos, así como a través de Transact-SQL. Para obtener más información sobre reglas de firewall de nivel de servidor, vea [Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md).

## Reglas de firewall de nivel de base de datos

Después de haber configurado el primer firewall de nivel de servidor, puede que desee restringir el acceso a determinadas bases de datos. Si especifica un intervalo de direcciones IP en la regla de firewall de nivel de base de datos que se encuentra fuera del intervalo especificado en la regla de firewall de nivel de servidor, solo los clientes que tengan direcciones IP en el intervalo de nivel de base de datos pueden tener acceso a la base de datos. Puede tener un máximo de 128 reglas de firewall de nivel de base de datos para una base de datos. Se pueden crear reglas de firewall de nivel de base de datos para las bases de datos de usuario y maestras y administrarse a través de Transact-SQL. Para obtener más información, vea [Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md).

## Administración mediante programación de reglas de firewall

Además del Portal de Azure, las reglas de firewall se pueden administrar mediante programación con Transact-SQL, la API de REST y Azure PowerShell. En las tablas siguientes se describe el conjunto de comandos disponibles para cada método.


### Transact-SQL

| Vista de catálogo o un procedimiento almacenado | Level | Descripción |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | Server | Muestra las reglas de firewall de nivel de servidor actuales |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | Server | Crea o actualiza las reglas de firewall de nivel de servidor |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | Server | Elimina las reglas de firewall de nivel de servidor |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | Base de datos | Muestra las reglas de firewall de nivel de base de datos actuales |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | Base de datos | Crea o actualiza las reglas de firewall de nivel de base de datos |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bases de datos | Elimina las reglas de firewall de nivel de base de datos |

### API de REST

| API | Level | Descripción |
|----------------------|--------|------------------------------------------------------------------|
| [Enumerar reglas de firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx) | Server | Muestra las reglas de firewall de nivel de servidor actuales |
| [Crear regla de firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Server | Crea o actualiza las reglas de firewall de nivel de servidor |
| [Establecer regla de firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx) | Server | Actualiza las propiedades de una regla de firewall de nivel de servidor existente |
| [Eliminar regla de firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Server | Elimina las reglas de firewall de nivel de servidor |


### Azure PowerShell

| Cmdlet | Level | Descripción |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | Server | Devuelve las reglas de firewall de nivel de servidor actuales |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | Server | Crear una regla de firewall de nivel de servidor |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | Server | Actualiza las propiedades de una regla de firewall de nivel de servidor existente |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Server | Elimina las reglas de firewall de nivel de servidor |

> [AZURE.NOTE] los cambios de la configuración del firewall pueden tardar hasta cinco minutos en aplicarse.

## Solución de problemas del firewall de la base de datos

Tenga en cuenta los siguientes puntos cuando el acceso al servicio de Base de datos SQL de Microsoft Azure no se comporte de la manera prevista:

- **Configuración del firewall local:** antes de que el equipo pueda tener acceso a la Base de datos SQL de Azure, puede que necesite crear una excepción de firewall en el equipo para el puerto TCP 1433. Es posible que tenga que abrir puertos adicionales si está realizando conexiones dentro del límite de la nube de Azure. Para obtener más información, consulte la sección **Versión V12 de la Base de datos SQL: fuera frente a dentro** del artículo [Puertos más allá de 1433 para ADO.NET 4.5 y para la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Traducción de direcciones de red (NAT):** debido a la NAT, la dirección IP usada por su equipo para conectarse a la Base de datos SQL de Azure puede diferir de la dirección IP que se muestra en los valores de la configuración de IP del equipo. Para ver la dirección IP que su equipo está usando para conectarse a Azure, inicie sesión en el Portal y vaya a la pestaña **Configurar** del servidor que hospeda su base de datos. En la sección **Direcciones IP permitidas**, verá la sección **Dirección IP del cliente actual**. Haga clic en **Agregar** en la opción **Direcciones IP permitidas**, para permitir que este equipo tenga acceso al servidor.

- **Los cambios realizados en la lista de permitidos no han surtido efecto todavía:** puede haber un retraso de hasta cinco minutos para que cambios en la configuración del firewall de Base de datos SQL de Azure surtan efecto.

- **El inicio de sesión no está autorizado o se ha usado una contraseña incorrecta:** si un inicio de sesión no tiene permisos en el servidor de la Base de datos SQL de Azure o la contraseña usada es incorrecta, se denegará la conexión al servidor de la Base de datos SQL de Azure. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad de intentar conectarse al servidor; cada cliente debe ofrecer las credenciales de seguridad necesarias. Para obtener más información sobre la preparación de inicios de sesión, vea Administración de bases de datos, inicios de sesión y usuarios en la Base de datos SQL de Azure.

- **Dirección IP dinámica:** si tiene una conexión a Internet con una direccionamiento IP dinámica y tiene problemas al acceder al firewall, podría intentar una de las siguientes soluciones:

 - Pida a su proveedor de servicios de Internet (ISP) el intervalo de direcciones IP asignado a sus equipos cliente que tendrán acceso al servidor de Base de datos SQL de Azure y luego agregue el intervalo de direcciones IP como una regla de firewall.

 - Obtenga el direccionamiento IP estático en su lugar para los equipos cliente y luego agregue las direcciones IP como reglas de firewall.

## Consulte también

[Configuración del firewall de la base de datos (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md)

[Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_0224_2016-->