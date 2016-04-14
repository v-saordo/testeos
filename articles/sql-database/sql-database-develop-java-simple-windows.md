<properties
	pageTitle="Conexión a la base de datos SQL mediante Java con JDBC en Windows"
	description="Este tema muestra un ejemplo de código Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa JDBC y se ejecuta en un equipo cliente de Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="lbosq"/>


# Conexión a la base de datos SQL mediante Java con JDBC en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código de Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo de Java se basa en la versión 1.8 del kit de desarrollo de Java (JDK). El ejemplo se conecta a una base de datos SQL de Azure mediante el controlador JDBC.


## Requisitos previos

### Controladores y bibliotecas

- [Controlador JDBC de Microsoft para SQL Server: SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- Cualquier plataforma de sistema operativo que ejecute [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

### Base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos.

### Tabla SQL

El ejemplo de código de Java de este tema presupone que ya existe en la tabla de prueba siguiente en la base de datos SQL de Azure.

<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## Paso 1: Obtención de la cadena de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE]Si usa el controlador JDBC de JTDS, tiene que agregar "ssl=require" a la dirección URL de la cadena de conexión y establecer la siguiente opción para JVM "-Djsse.enableCBCProtection=false". Esta opción de JVM deshabilita una revisión para una vulnerabilidad de seguridad; por tanto, asegúrese de entender qué riesgo implica antes de establecer esta opción.


## Paso 2: Compilación del ejemplo de código Java


La sección contiene la mayor parte del código Java de ejemplo. Incluye comentarios que indican dónde debe copiar y pegar los segmentos Java más pequeños que se presentan en las secciones siguientes. El ejemplo de esta sección puede compilarse y ejecutarse incluso sin las operaciones de copiado y pegado cerca de los comentarios, pero solo se realizaría la conexión y finalizaría el proceso. Los comentarios que encontrará son los siguientes:


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


A continuación se muestra la mayor parte del código Java de ejemplo. El ejemplo incluye la función `main` de la clase `SQLDatabaseTest`.


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;

	public class SQLDatabaseTest {

		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;"
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password=your_password;"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;";

			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;

			try {
				connection = DriverManager.getConnection(connectionString);

				// INSERT two rows into the table.
				// ...

				// TRANSACTION and commit for an UPDATE.
				// ...

				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


Por supuesto, para ejecutar el ejemplo de código Java anterior, tendría que especificar los valores reales en la cadena de conexión para reemplazar los marcadores de posición siguientes:


- your\_server
- your\_database
- your\_user
- your\_password


## Paso 3: Inserción de filas


Este segmento de Java emite una instrucción Transact-SQL INSERT para insertar dos filas en la tabla Person. La secuencia general es la siguiente:


1. Genere un objeto `PreparedStatement` mediante el uso del objeto `Connection`.
 - El parámetro `Statement.RETURN_GENERATED_KEYS` se incluye para que más adelante podemos obtener el valor generado automáticamente para el valor de clave **id**.
2. Llame al método `execute` del objeto `PreparedStatement`.
3. Obtenga el valor numérico que se generó automáticamente para la clave principal usando el objeto `PreparedStatement`.
 - Esto está relacionado con la especificación AUTO\_INCREMENT de la columna **id** de la tabla Person.


Copie y pegue este segmento Java breve en el ejemplo de código principal donde verá el comentario `// INSERT two rows into the table.`.


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";

	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## Paso 4: Confirmación de una transacción

El siguiente segmento de código Java genera una instrucción Transact-SQL UPDATE para aumentar el valor `age` de cada fila de la tabla Person. La secuencia general es la siguiente:


1. El método `setAutoCommit` se llama para evitar que las actualizaciones se confirmen automáticamente en la base de datos.
2. El método `executeUpdate` se llama para ejecutar UPDATE en el contexto de la transacción.
3. El método `commit` se llama para confirmar la transacción de forma explícita.


Copie y pegue este segmento Java breve en el ejemplo de código principal donde verá el comentario `// TRANSACTION and commit for an UPDATE.`.


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);

	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);

	// Execute the statement.
	prepsUpdateAge.executeUpdate();

	//Commit the transaction.
	connection.commit();

	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## Paso 4: Ejecución de una consulta


Este segmento de Java ejecuta una instrucción Transact-SQL SELECT para ver todas las filas actualizadas de la tabla Person. La secuencia general es la siguiente:


1. Genere un objeto `Statement` mediante el uso del objeto `Connection`.
2. Genere un objeto `ResultSet` mediante el uso del objeto `Statement`.
3. Realice un bucle alrededor de una llamada a `resultSet.next` para establecer iteraciones en todas las filas devueltas.


Copie y pegue este segmento Java breve en el ejemplo de código principal donde verá el comentario `// SELECT rows from a table.`.


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);

	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Java](/develop/java/).

<!---HONumber=AcomDC_0107_2016-->