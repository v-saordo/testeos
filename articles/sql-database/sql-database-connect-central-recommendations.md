<properties 
	pageTitle="Conexión a Base de datos SQL: prácticas recomendadas | Microsoft Azure" 
	description="Tema de punto de partida que reúne vínculos y recomendaciones de prácticas recomendadas para programas cliente que se conectan a Base de datos SQL de Azure a partir de tecnologías como ADO.NET y PHP." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2016" 
	ms.author="genemi"/>


# Conexión a Base de datos SQL: prácticas recomendadas y directrices de diseño


Este tema es un buen lugar para empezar a trabajar con conectividad de cliente a Base de datos SQL de Azure. Proporciona vínculos a ejemplos de código para diversas tecnologías que puede usar para conectarse e interactuar con la Base de datos SQL. Las tecnologías incluyen, entre otras, Enterprise Library, JDBC y PHP. La información proporcionada se aplica independientemente de la tecnología específica que se use para conectarse a la Base de datos SQL.


<a id="a-tech-independent-recommend" name="a-tech-independent-recommend"></a>

## Recomendaciones independientes de la tecnología


- [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](http://msdn.microsoft.com/library/azure/ee336282.aspx) - Se incluye lo siguiente:
 - [Puertos y firewalls](sql-database-configure-firewall-settings.md)
 - Cadenas de conexión
- [Administración de recursos de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338083.aspx) - Se incluye lo siguiente:
 - Regulador de recursos
 - Cumplimiento de límites
 - Limitaciones


<a id="b-authentication-recommend" name="b-authentication-recommend"></a>

## Recomendaciones para la autenticación


- Use la autenticación de Base de datos SQL de Azure, no la autenticación de Windows que no está disponible en la Base de datos de SQL de Azure.
- Especifique una base de datos determinada, en lugar de utilizar la base de datos *maestra* predeterminada.
 - No puede usar la instrucción **USE NombreDeMiBaseDeDatos;** de Transact-SQL en Base de datos SQL para cambiar a otra base de datos.


### Usuarios contenidos


Dispone de opciones al agregar una persona como usuario a Base de datos SQL:

- Agregue un *inicio de sesión* con una contraseña a la base de datos **maestra** y, a continuación, agregue un *usuario* correspondiente a otra o más bases de datos del mismo servidor.

- Agregue un *usuario contenido* con una contraseña a una o más bases de datos y ningún vínculo a cualquier *inicio de sesión* en la base de datos **maestra**.


El enfoque del usuario contenido tiene ventajas e inconvenientes:

- Una de las ventajas es que una base de datos puede moverse con facilidad desde un servidor de Base de datos SQL de Azure a otro, cuando todos los usuarios de la base de datos son usuarios contenidos.

- Uno de los inconvenientes es la mayor dificultad en la administración periódica. Por ejemplo:
 - Es más complicado eliminar varios usuarios contenidos que un inicio de sesión.
 - Quien sea un usuario contenido en varias bases de datos podría tener más contraseñas para recordar o actualizar.


Para obtener más información, consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](http://msdn.microsoft.com/library/ff929188.aspx).


<a id="c-connection-recommend" name="c-connection-recommend"></a>

## Recomendaciones para la conexión


- En la lógica de conexión de cliente, reemplace el tiempo de espera predeterminado para que sea de 30 segundos.
 - El valor predeterminado de 15 segundos es demasiado corto para conexiones que dependen de Internet.


- En el equipo que hospeda el programa cliente, asegúrese de que el firewall permita la comunicación TCP saliente en el puerto 1433.


- Si el programa cliente se conecta a Base de datos SQL V12, mientras el cliente se ejecuta en una máquina virtual (VM) de Azure, debe abrir los intervalos de puerto 11000 a 11999 y 14000 a 14999 en la VM. Haga clic [aquí](sql-database-develop-direct-route-ports-adonet-v12.md) para obtener información detallada.


- Para controlar los *errores transitorios*, agregue [lógica de *reintento*](#TransientFaultsAndRetryLogicGm) a los programas cliente que interactúan con Base de datos SQL de Azure.


### Grupo de conexiones


Si usa un [grupo de conexiones](http://msdn.microsoft.com/library/8xx3tyca.aspx), cierre la conexión en el momento en que el programa no la esté usando activamente y no esté preparándose para volver a usarla.

A menos que el programa vuelva a usar la conexión para otra operación inmediatamente y sin pausas, se recomienda el siguiente patrón:

- Abrir una conexión.
- Hacer una operación a través de la conexión.
- Cerrar la conexión.


### Puertos que no sea simplemente 1433 en V12


En ocasiones, las conexiones de cliente a la base de datos de SQL Azure V12 omiten al proxy e interactúan directamente con la base de datos. Los puertos que no sean 1433 se convierten en puertos importantes. Para obtener información detallada, vea:<br/> [Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


La siguiente sección tiene más que decir sobre el control de errores transitorios y la lógica de reintento.



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

&nbsp;

## Errores transitorios y lógica de reintento


El sistema de Azure tiene la capacidad de volver a configurar dinámicamente servidores cuando surgen pesadas cargas de trabajo en el servicio de Base de datos SQL.

Sin embargo, una reconfiguración podría hacer que su programa cliente perdiera su conexión a Base de datos SQL. Este error se denomina *error transitorio*.

Si el programa cliente tiene lógica de reintento, puede intentar restablecer una conexión después de dar tiempo a que se corrijan los errores transitorios.

Se recomienda un retraso de 5 segundos antes del primer reintento. Si se vuelve a intentar después de un retraso menor de 5 segundos, se correrá el riesgo de sobrecargar el servicio en la nube. Para cada intento siguiente el retraso debe aumentar exponencialmente, hasta un máximo de 60 segundos.

Una explicación del *período de bloqueo* para clientes que usan ADO.NET está disponible en [Grupos de conexión de SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).


Para obtener ejemplos de código que ilustran la lógica de reintento, consulte:

- [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)


### Números de error para errores transitorios


Cuando se produce cualquier error en Base de datos SQL, se inicia una [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx). La **SqlException** contiene un código de error numérico en su propiedad **Number**. Si el código de error identifica un error transitorio, el programa debería reintentar la llamada.


- [Mensajes de error para los programas de cliente de base de datos SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Su sección **Errores transitorios, los errores de pérdida de conexión** es una lista de los errores transitorios que garantizan un reintento automático.
 - Por ejemplo, inténtelo de nuevo si se produce el número de error 40613, que indica algo similar a<br/>*La base de datos "mydatabase" del servidor "theserver" no está actualmente disponible.*


Para obtener más información, consulte:

- [Desarrollo de base de datos SQL de Azure: temas de procedimientos](http://msdn.microsoft.com/library/azure/ee621787.aspx)

<!--  (per Penny Lee, 2016/01/07.  MightyPen==GeneMi)
- [Troubleshoot connection problems to Azure SQL Database](http://support.microsoft.com/kb/2980233/)
-->


<a id="e-technologies" name="e-technologies"></a>

## Tecnologías


Los siguientes temas contienen vínculos a ejemplos de código para varios lenguajes y tecnologías de controlador que puede usar para conectarse a Base de datos SQL Azure desde su programa cliente.


Se proporcionan varios ejemplos de código para clientes que se ejecutan en Windows, Linux y Mac OS X.


**Ejemplos generales:** hay [ejemplos de código](sql-database-develop-quick-start-client-code-samples.md) para diversos lenguajes de programación, por ejemplo, PHP, Python, Node.js y .NET CSharp. Además, se proporcionan ejemplos para clientes que se ejecutan en Windows, Linux y Mac OS X.


**Escalado elástico:** para obtener información sobre la conectividad a bases de datos de Escalado elástico, consulte:

- [Introducción a la vista previa de Escalado elástico de Base de datos SQL de Azure](sql-database-elastic-scale-get-started.md)
- [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)


**Bibliotecas de controlador:** para obtener información acerca de las bibliotecas de controlador de conexión, incluidas las versiones recomendadas, consulte:

- [Bibliotecas de conexiones para la base de datos SQL y SQL Server](sql-database-libraries.md)

<!---HONumber=AcomDC_0114_2016-->