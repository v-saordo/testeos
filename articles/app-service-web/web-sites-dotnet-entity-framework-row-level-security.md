<properties
	pageTitle="Tutorial: Creación de una aplicación web con una base de datos multiempresa mediante Entity Framework y la seguridad de nivel de fila"
	description="Aprenda a desarrollar una aplicación web de ASP.NET MVC 5 con un back-end de Base de datos SQL multiempresa, mediante Entity Framework y la seguridad de nivel de fila."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
	services="app-service\web"
	documentationCenter=".net"
	manager="jeffreyg"
  authors="tmullaney"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="thmullan"/>

# Tutorial: Creación de una aplicación web con una base de datos multiempresa mediante Entity Framework y la seguridad de nivel de fila

En este tutorial se muestra cómo crear una aplicación web multiempresa con un modelo de arrendamiento "[base de datos compartida, esquema compartido](https://msdn.microsoft.com/library/aa479086.aspx)", con Entity Framework y la [seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131.aspx). En este modelo, una sola base de datos contiene datos de muchos inquilinos, y cada fila de cada tabla está asociada a un "identificador de inquilino". La seguridad de nivel de fila (RLS), una nueva característica de Base de datos SQL de Azure, se usa para impedir que los inquilinos obtengan acceso a los datos de los demás inquilinos. Para ello solo es necesario realizar un pequeño cambio en la aplicación. Al centralizar la lógica de acceso del inquilino dentro de la propia base de datos, RLS simplifica el código de la aplicación y reduce el riesgo de pérdidas accidentales de los datos entre los inquilinos.

Vamos a comenzar con la aplicación sencilla de Contact Manager de la sección [Creación de una aplicación de ASP.NET MVP con autenticación y Base de datos SQL e implementación en el Servicio de aplicaciones de Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Ahora mismo, la aplicación permite a todos los usuarios (inquilinos) ver todos los contactos:

![Aplicación de Contact Manager antes de habilitar RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Con solo algunos pequeños cambios, agregaremos compatibilidad con multiempresa, de forma que los usuarios solo podrán ver los contactos que les pertenecen.

## Paso 1: Incorporación de una clase de Interceptor en la aplicación para establecer el valor de SESSION\_CONTEXT

Solo tenemos que realizar un cambio en la aplicación. Puesto que todos los usuarios de la aplicación se conectan a la base de datos con la misma cadena de conexión (es decir, el mismo inicio de sesión SQL), no hay actualmente ninguna forma de que una directiva RSL sepa por qué usuario se debería filtrar. Este enfoque es muy común en las aplicaciones web porque permite la agrupación eficaz de conexiones, pero significa que necesitamos otra manera de identificar al usuario actual de la aplicación dentro de la base de datos. La solución consiste en que la aplicación establezca un par de clave-valor para el UserId actual en [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806) inmediatamente después de abrir una conexión y antes de ejecutar una consulta. SESSION\_CONTEXT es un almacén de pares de clave-valor con ámbito de sesión, y nuestra política RLS usará el UserId almacenado en él para identificar al usuario actual.

Agregaremos un [interceptor](https://msdn.microsoft.com/data/dn469464.aspx) (en concreto [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)), una nueva característica de Entity Framework (EF) 6, para establecer automáticamente el UserId actual en SESSION\_CONTEXT ejecutando una instrucción T-SQL cuando EF abra una conexión.

1.	Abra el proyecto ContactManager en Visual Studio.
2.	Haga clic con el botón derecho en la carpeta Modelos en el Explorador de soluciones y seleccione Agregar > Clase.
3.	Asigne a la nueva clase el nombre "SessionContextInterceptor.cs" y haga clic en Agregar.
4.	Reemplace el contenido de SessionContextInterceptor.cs por el código siguiente.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        	// Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        
        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Este es el único cambio que precisa la aplicación. Siga adelante y compile y publique la aplicación.

## Paso 2: Incorporación de una columna UserId al esquema de base de datos

A continuación, debemos agregar una columna UserId a la tabla Contactos para asociar cada fila a un usuario (inquilino). Modificaremos el esquema directamente en la base de datos, por lo que no tenemos que incluir este campo en nuestro modelo de datos de EF.

Conéctese a la base de datos directamente, mediante SQL Server Management Studio o Visual Studio, y luego ejecute la siguiente instrucción T-SQL:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Se agrega una columna UserId a la tabla Contactos. Usamos el tipo de datos nvarchar (128) para que coincidan los UserId almacenados en la tabla AspNetUsers y creamos una restricción DEFAULT que establece automáticamente que el UserId de las filas recién insertadas sea el UserId almacenado actualmente en SESSION\_CONTEXT.

Ahora la tabla tiene este aspecto:

![Tabla Contactos de SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Cuando se creen nuevos contactos, se les asignará automáticamente el UserId correcto. No obstante, con fines de demostración, vamos a asignar algunos de estos contactos existentes a un usuario existente.

Si ya ha creado unos cuantos usuarios en la aplicación (por ejemplo, mediante cuentas locales, de Google o de Facebook), los verá en la tabla AspNetUsers. En la siguiente captura de pantalla, hasta el momento solo hay un usuario.

![Tabla AspNetUsers de SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Copie el identificador de user1@contoso.com y péguelo en la siguiente instrucción T-SQL. Ejecute esta instrucción para asociar tres de los contactos con este UserId.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## Paso 3: Creación de una directiva de seguridad de nivel de fila en la base de datos

El paso final consiste en crear una directiva de seguridad que use el UserId de SESSION\_CONTEXT para filtrar automáticamente los resultados devueltos por las consultas.

Mientras sigue conectado a la base de datos, ejecute la siguiente instrucción T-SQL:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
	RETURNS TABLE
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS accessResult
	WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
	ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
	ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Este código hace tres cosas. En primer lugar, crea un nuevo esquema como procedimiento recomendado para centralizar y limitar el acceso a los objetos RLS. A continuación, crea una función de predicado que devolverá '1' cuando el UserId de una fila coincide con el UserId de SESSION\_CONTEXT. Finalmente, crea una directiva de seguridad que agrega esta función como un predicado de filtro y bloqueo en la tabla Contactos. El predicado de filtro hace que las consultas solo devuelvan las filas que pertenecen al usuario actual, y el predicado de bloqueo actúa como salvaguarda para impedir que la aplicación inserte por accidente una fila para el usuario incorrecto.

Ejecute ahora la aplicación e inicie sesión como user1@contoso.com. Ahora este usuario solo ve los contactos que se asignaron anteriormente a este UserId:

![Aplicación de Contact Manager antes de habilitar RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Para validar esto aún más, intente registrar un nuevo usuario. Este usuario no verá ningún contacto, ya que no se le ha asignado ninguno. Si dicho usuario crea un nuevo contacto, se le asignará a él, y solo él podrá verlo.

## Pasos siguientes

Eso es todo. La aplicación web sencilla de Contact Manager se ha convertido en una aplicación multiempresa donde cada usuario tiene su propia lista de contactos. Mediante la seguridad de nivel de fila, hemos evitado la complejidad de aplicar lógica de acceso de inquilino a nuestro código de aplicación. Esta transparencia permite a la aplicación centrarse en el problema empresarial real entre manos, y también reduce el riesgo de pérdida accidental de datos cuando el código base de la aplicación crece.

En este tutorial solo se ha mostrado una mínima parte de lo que se puede hacer con RLS. Por ejemplo, se puede tener una lógica de acceso más sofisticada o granular, y es posible almacenar más valores que únicamente el UserId de SESSION\_CONTEXT. También es posible [integrar RLS con las bibliotecas de cliente de herramientas de base de datos elástica](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) para permitir particiones multiempresa en una capa de datos de escalado horizontal.

Al margen de estas posibilidades, también estamos trabajando para mejorar RLS más incluso. Si tiene alguna pregunta, idea o cosas que le gustaría ver, háganos llegar sus comentarios. Agradecemos su participación.

<!---HONumber=AcomDC_0128_2016-->