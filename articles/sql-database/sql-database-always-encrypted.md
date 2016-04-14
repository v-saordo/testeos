<properties
	pageTitle="Protección de los datos confidenciales en Base de datos SQL con el cifrado de base de datos | Microsoft Azure"
	description="Proteger datos confidenciales en la base de datos SQL en cuestión de minutos."
	keywords="base de datos SQL, cifrado de sql, cifrado de base de datos, clave de cifrado, datos confidenciales, Always Encrypted"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="sstein"/>

# Protección de los datos confidenciales en Base de datos SQL con cifrado de base de datos y almacenamiento de las claves de cifrado en el almacén de certificados de Windows

> [AZURE.SELECTOR]
- [Almacén de claves de Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Almacén de certificados de Windows](sql-database-always-encrypted.md)

En este artículo se muestra cómo proteger datos confidenciales en una base de datos SQL con cifrado de base de datos mediante el asistente de [Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) en [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) y cómo almacenar las claves de cifrado en el almacén de certificados de Windows.

Always Encrypted es una nueva tecnología de cifrado de Base de datos SQL de Azure y SQL Server que protege los datos confidenciales en reposo en el servidor durante el movimiento entre cliente y servidor, así como mientras los datos estén en uso, asegurando que los datos confidenciales nunca aparezcan como texto no cifrado dentro del sistema de base de datos. Solo las aplicaciones cliente o servidores de aplicaciones, que tienen acceso a las claves, pueden acceder a datos de texto no cifrado. Para más información, consulte [Always Encrypted (motor de base de datos)](https://msdn.microsoft.com/library/mt163865.aspx).


Después de configurar la base de datos para usar Always Encrypted, crearemos una aplicación cliente en C# con Visual Studio para trabajar con los datos cifrados.

Siga los pasos de este artículo y aprenda a configurar Always Encrypted para una base de datos SQL de Azure. En este artículo aprenderá a realizar las siguientes tareas:

- Usar el asistente de Always Encrypted en SSMS para crear [claves de Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
    - Crear una [clave maestra de columna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crear una [clave de cifrado de columna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Crear una tabla de base de datos y cifrar algunas columnas.
- Crear una aplicación que inserta, selecciona y muestra los datos de las columnas cifradas.

> [AZURE.NOTE] Always Encrypted para Base de datos SQL de Azure está actualmente en vista previa.


## Requisitos previos

Para este tutorial, necesitará:

- una cuenta de Azure y una suscripción antes de empezar. Si no tiene una, suscríbase para [una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) versión 13.0.700.242 o posterior.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) o posterior (en el equipo cliente).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## Crear una base de datos SQL en blanco
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Nuevo** > **Datos + almacenamiento** > **Base de datos SQL**.
3. Cree una base de datos **en blanco** denominada **Clinic** en un servidor nuevo o existente. Para tener instrucciones detalladas para crear una base de datos en el Portal de Azure, consulte [Creación de una Base de datos SQL en cuestión de minutos](sql-database-get-started.md).

	![crear una base de datos en blanco](./media/sql-database-always-encrypted/create-database.png)

Necesitará la cadena de conexión más adelante en el tutorial de modo que después de crear la base de datos, examine la nueva base de datos Clinic y copie la cadena de conexión (puede obtener la cadena de conexión en cualquier momento, pero ya estamos en el portal, por lo que es fácil copiarla ahora).

1. Haga clic en **Bases de datos SQL** > **Clinic** > **Mostrar las cadenas de conexión de la base de datos**.
2. Copie la cadena de conexión para **ADO.NET**:

	![copiar la cadena de conexión](./media/sql-database-always-encrypted/connection-strings.png)


## Conéctese a la base de datos con SSMS

Abra SSMS y conéctese al servidor con la base de datos Clinic.


1. Abra SSMS (haga clic en **Conectar** > **Motor de base de datos...** para abrir la ventana **Conectar al servidor** si no está abierta).
2. Escriba su nombre del servidor y las credenciales. El nombre del servidor puede encontrarse en la hoja de la base de datos SQL y en la cadena de conexión que copió anteriormente. Escriba el nombre de servidor completo (incluido *database.windows.net*).

	![copiar la cadena de conexión](./media/sql-database-always-encrypted/ssms-connect.png)

3. Si se abre la ventana **Nueva regla de firewall**, inicie sesión en Azure y permita que SSMS cree una nueva regla de firewall.


## Creación de una tabla

En primer lugar crearemos una tabla para almacenar datos de los pacientes (al principio, simplemente una tabla normal; configuraremos el cifrado en la siguiente sección).

1. Expanda **Bases de datos**.
1. Haga clic con el botón derecho en la base de datos **Clinic** y haga clic en **Nueva consulta**.
2. Pegue el siguiente código Transact-SQL (T-SQL) en la nueva ventana de consulta y haga clic en **Ejecutar**:


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1), 
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL, 
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## Cifre algunas columnas (configurar Always Encrypted)

SSMS proporciona un asistente para configurar Always Encrypted de forma fácil mediante la configuración de la clave maestra de columna (CMK), la clave de cifrado de columna (CEK) y la columnas cifradas para usted.

1. Expanda **Bases de datos** > **Clinic** > **Tablas**.
2. Haga clic con el botón derecho en la tabla **Pacientes** y seleccione **Cifrar columnas...** para abrir el asistente de Always Encrypted:

    ![Cifrar columnas](./media/sql-database-always-encrypted/encrypt-columns.png)

3. **Selección de columnas**

    Haga clic en **Siguiente** en la página **Introducción** para abrir la página **Selección de columnas**, donde puede seleccionar qué columnas quiere cifrar, [el tipo de cifrado y qué clave de cifrado de columna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) usar.

    Queremos cifrar la información **SSN** y **BirthDate** de cada paciente. La columna SSN usará cifrado determinista, que admite búsquedas de igualdad, combinaciones y agrupaciones. La columna BirthDate usará cifrado aleatorio, que no admite operaciones.

    Seleccione y configure el **tipo de cifrado** de la columna SSN como **determinista** y la columna BirthDate como **aleatoria** y, luego, haga clic en **Siguiente**.

    ![Cifrar columnas](./media/sql-database-always-encrypted/column-selection.png)

4. **Configuración de la clave maestra** (CMK)

    La página **Configuración de la clave maestra** es donde se configura la clave maestra de columna (CMK) y se selecciona el proveedor del almacén de claves donde se almacenará la CMK. Actualmente, puede almacenar una CMK en el almacén de certificados de Windows, en el almacén de claves de Azure o en un módulo de seguridad de hardware (HSM). En este tutorial se muestra cómo almacenar las claves en el almacén de certificados de Windows.

    Compruebe que el **almacén de certificados de Windows** esté seleccionado y haga clic en **Siguiente**.

    ![configuración de la clave maestra](./media/sql-database-always-encrypted/master-key-configuration.png)


5. **Validación**

    Puede cifrar las columnas ahora o guardar un script de PowerShell para ejecutarlo más tarde. Para este tutorial, seleccione **Continuar para finalizar ahora** y haga clic en **Siguiente**.

6. **Resumen**

    Compruebe que la configuración sea correcta y haga clic en **Finalizar** para completar la configuración de Always Encrypted.


    ![resumen](./media/sql-database-always-encrypted/summary.png)


### ¿Qué hizo exactamente el asistente?

Una vez finalizado el asistente, la base de datos estará configurada para Always Encrypted y se habrá completado lo siguiente:

- Creación de una clave maestra de columna (CMK).
- Creación de una clave de cifrado de columna (CEK).
- Configuración de las columnas seleccionadas para el cifrado. (Nuestra tabla Pacientes actualmente no tiene datos aún, pero ahora se pueden cifrar los datos existentes en las columnas seleccionadas).

Puede comprobar la creación de las claves en SSMS expandiendo **Clinic** > **Seguridad** > **Claves de Always Encrypted**. Ahora puede ver las nuevas claves que el asistente generó para usted.


## Crear una aplicación cliente que funcione con los datos cifrados

Ahora que Always Encrypted está configurado, vamos a compilar una aplicación que realice algunas INSERCIONES y SELECCIONES en las columnas cifradas. Para ejecutar correctamente la aplicación de ejemplo debe ejecutarla en el mismo equipo en el que ejecutó el asistente de Always Encrypted. Para ejecutarla en otro equipo debe implementar los certificados de Always Encrypted en el equipo que ejecuta la aplicación cliente.

> [AZURE.IMPORTANT] La aplicación debe usar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) al pasar datos de texto no cifrado al servidor con columnas de Always Encrypted. Se generará una excepción al pasar valores literales sin usar objetos SqlParameter.


1. Abra Visual Studio y cree una nueva aplicación de consola C#. Asegúrese de que el proyecto esté establecido en **.NET Framework 4.6** o posterior.
2. Asigne al proyecto el nombre **AlwaysEncryptedConsoleApp** y haga clic en **Aceptar**.


	![nueva aplicación de consola](./media/sql-database-always-encrypted/console-app.png)



## Modificar la cadena de conexión para habilitar Always Encrypted

En esta sección se explica simplemente cómo habilitar Always Encrypted en la cadena de conexión de su base de datos. Se encuentra en la siguiente sección, **Aplicación de la consola de ejemplo de Always Encrypted**, donde realmente modificará la aplicación de consola que acaba de crear.


Para habilitar Always Encrypted, debe agregar la palabra clave **Configuración de cifrado de columnas** a la cadena de conexión y establecerla como **Habilitada**.

Puede realizar esta configuración directamente en la cadena de conexión o mediante [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). La aplicación de ejemplo de la siguiente sección muestra cómo usar **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Este es el único cambio necesario en una aplicación cliente específica de Always Encrypted. Si tiene una aplicación existente que almacena su cadena de conexión de forma externa (es decir, en un archivo de configuración) puede habilitar Always Encrypted sin cambiar ningún código.


### Modificar Always Encrypted en la cadena de conexión

Agregue la siguiente palabra clave en la cadena de conexión:

    Column Encryption Setting=Enabled


### Habilitar Always Encrypted con un SqlConnectionStringBuilder

El siguiente código muestra cómo habilitar Always Encrypted estableciendo [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) como [Habilitado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder = 
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting = 
       SqlConnectionColumnEncryptionSetting.Enabled;



## Aplicación de consola de ejemplo de Always Encrypted

En este ejemplo se muestra cómo:

- Modificar la cadena de conexión para habilitar Always Encrypted.
- Insertar datos en las columnas cifradas.
- Seleccionar un registro mediante el filtrado de un valor específico de una columna cifrada.

Reemplace el contenido de **Program.cs** por el código siguiente. Reemplace la cadena de conexión para la variable global connectionString en la línea directamente encima del método Main por una cadena de conexión válida desde el Portal de Azure. Este es el único cambio que necesita realizar en este código.

Ahora, ejecute la aplicación para ver Always Encrypted en acción.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    
    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure Portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted 
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## Compruebe que los datos están cifrados

Para una comprobación rápida de que los datos reales del servidor están cifrados, podemos consultar fácilmente los datos de los pacientes con SSMS (mediante la conexión actual en la que Configuración de cifrado de columnas aún no está habilitado).

Ejecute la siguiente consulta en la base de datos Clinic:

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Puede ver que las columnas cifradas no contienen datos de texto no cifrado.

   ![nueva aplicación de consola](./media/sql-database-always-encrypted/ssms-encrypted.png)


Para usar SSMS para acceder a los datos de texto no cifrado, podemos agregar el parámetro **Column Encryption Setting=enabled** a la conexión.

1. En SSMS, haga clic con el botón derecho en el servidor en **Explorador de objetos** y en **Desconectar**.
2. Haga clic en **Conectar** > **Motor de base de datos** para abrir la ventana **Conectar al servidor** y haga clic en **Opciones**.
3. Haga clic en **Parámetros de conexión adicionales** y escriba **Column Encryption Setting=enabled**.

	![nueva aplicación de consola](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Ejecute la siguiente consulta en la base de datos Clinic:

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Ahora puede ver los datos de texto no cifrado en las columnas cifradas.


	![nueva aplicación de consola](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Si se conecta con SSMS (o cualquier cliente) desde un equipo diferente no tendrá acceso a las claves de cifrado, por lo que no podrá descifrar los datos.



## Pasos siguientes
Después de crear una base de datos que usa Always Encrypted es posible que quiera hacer lo siguiente:

- Ejecutar este ejemplo desde un equipo diferente. No tendrá acceso a las claves de cifrado, por lo que no tendrá acceso a los datos de texto no cifrado y no se ejecutará correctamente. 
- [Rotación y limpieza de claves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar los datos que ya están cifrados con Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx)
- Implementar certificados de Always Encrypted en otros equipos cliente.


## Información relacionada

- [Always Encrypted (desarrollo de clientes)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Cifrado de datos transparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Cifrado de SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Asistente de Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog de Always Encrypted](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always%20encrypted/)

<!---HONumber=AcomDC_0302_2016-->