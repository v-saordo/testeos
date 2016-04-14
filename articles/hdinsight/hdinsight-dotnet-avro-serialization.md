<properties
	pageTitle="Serialización de datos con Microsoft Avro Library | Microsoft Azure"
	description="Obtenga información sobre cómo HDInsight de Azure usa Avro para serializar grandes volúmenes de datos."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian" 
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="jgao"/>


# Serialización de datos en Hadoop con Microsoft Avro Library

En este tema, se explica el uso de <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> para serializar objetos y otras estructuras de datos en secuencias con el fin de guardarlos en memoria, en una base de datos o en un archivo, y cómo deserializarlos para recuperar los objetos originales.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementa el sistema de serialización de datos de Apache Avro para el entorno de Microsoft.NET. Apache Avro proporciona un formato compacto de intercambio de datos binarios para la serialización. Utiliza <a href="http://www.json.org" target="_blank">JSON</a> para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes. Los datos serializados en un lenguaje se pueden leer en otro. Actualmente, se admiten C, C++, C#, Java, PHP, Python y Ruby. Puede encontrar información detallada sobre el formato en la <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">especificación de Apache Avro</a>. Tenga en cuenta que la versión actual de Microsoft Avro Library no admite la parte de llamadas a procedimientos remotos (RPC) de esta especificación.

La representación serializada de un objeto en el sistema Avro consta de dos partes: el esquema y el valor real. El esquema de Avro describe el modelo de datos independiente del lenguaje de los datos serializados con JSON. Está presente en paralelo con una representación binaria de los datos. Tener el esquema aparte de la representación binaria permite escribir cada objeto sin sobrecargas por valor, lo que agiliza la serialización y reduce la representación.

##Escenario de Hadoop
El formato de serialización de Apache Avro se utiliza de forma generalizada en HDIngisht de Azure y otros entornos de Apache Hadoop. Avro proporciona una forma práctica de representar estructuras de datos complejas dentro de un trabajo MapReduce de Hadoop. El formato de los archivos de Avro (archivo contenedor de objetos Avro) está diseñado para admitir el modelo de programación distribuida de MapReduce. La característica clave que permite la distribución es que los archivos son "divisibles" en el sentido de que se puede buscar cualquier punto en un archivo y comenzar a leer desde un bloque concreto.

##Serialización de Avro Library
La biblioteca de .NET para Avro admite dos formas de serialización de objetos:

- **reflexión**: el esquema JSON para los tipos se compila automáticamente a partir de los atributos del contrato de datos de los tipos .NET que se van a serializar.
- **registro genérico**: el esquema JSON se especifica de forma explícita en un registro representado por la clase [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) cuando no hay tipos .NET para describir el esquema de los datos que se van a serializar.

Cuando tanto el escritor como el lector de la secuencia conocen el esquema de los datos, se pueden enviar los datos sin el esquema. En aquellos casos en los que se utiliza el archivo contenedor de objetos Avro, el esquema se almacena en el archivo. Se pueden especificar otros parámetros, como el códec utilizado para la compresión de datos. Estos escenarios se explican e ilustran con más detalle en los ejemplos de código siguientes.


## Instalación de Avro Library

Antes de instalar la biblioteca necesitará lo siguiente:

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (v5.6.0.4 o posterior)

Tenga en cuenta que la dependencia de Newtonsoft.Json.dll se descarga automáticamente con la instalación de Microsoft Avro Library. En la sección siguiente se detalla el procedimiento para ello.


Microsoft Avro Library se distribuye como un paquete de NuGet que se puede instalar desde Visual Studio mediante el siguiente procedimiento:

1. Elija la pestaña **Proyecto** -> **Administrar paquetes de NuGet...**
2. Busque "Microsoft.Hadoop.Avro" en el cuadro **Búsqueda en línea**.
3. Haga clic en el botón **Instalar** junto a **Microsoft Azure HDInsight Avro Library**.

Tenga en cuenta que la dependencia de Newtonsoft.Json.dll (>= .6.0.4) se descarga también automáticamente con Microsoft Avro Library.

Puede que desee visitar la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">página principal de Microsoft Avro Library</a> para leer las notas de la versión actual.


El código fuente de Microsoft Avro Library está disponible en la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">página principal de Microsoft Avro Library</a>.

##Compilación de esquemas con Avro Library

Microsoft Avro Library contiene una utilidad de generación de código que permite la creación de tipos de C# automáticamente en función del esquema JSON previamente definido. La utilidad de generación de código no se distribuye como un ejecutable binario, pero se puede compilar fácilmente mediante el siguiente procedimiento:

1. Descargue el archivo ZIP con la versión más reciente del código fuente del SDK de HDInsight desde <a href="http://hadoopsdk.codeplex.com/SourceControl/latest" target="_blank">Microsoft .NET SDK para Hadoop</a>. (Haga clic en el icono **Descargar**).

2. Extraiga el SDK de HDInsight en un directorio que se encuentre en el equipo que tiene .NET Framework 4 instalado y conectado a Internet para descargar los paquetes de NuGet de dependencias necesarios. Supongamos que el código fuente se ha extraído en C:\\SDK.

3. Vaya a la carpeta C:\\SDK\\src\\Microsoft.Hadoop.Avro.Tools y ejecute build.bat. (El archivo llamará a MS Build desde la distribución de 32 bits de .NET Framework. Si desea utilizar una versión de 64 bits, edite build.bat siguiendo los comentarios que se encuentran dentro del archivo). Asegúrese de que la compilación se haya realizado correctamente. (En algunos sistemas, MSBuild puede producir advertencias. Estas advertencias no afectan a la utilidad siempre que no haya ningún error de compilación).

4. La utilidad compilada se encuentra en C:\\SDK\\Bin\\Unsigned\\Release\\Microsoft.Hadoop.Avro.Tools.


Para familiarizarse con la sintaxis de la línea de comandos, ejecute el comando siguiente desde la carpeta en donde se encuentra la utilidad de generación de código: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Para probar la utilidad, puede generar clases de C# a partir del archivo de esquema JSON de muestra proporcionado con el código fuente. Ejecute el comando siguiente:

	Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Se supone que va a producir dos archivos C# en el directorio actual: SensorData.cs y Location.cs.

Para comprender la lógica que está utilizando la utilidad de generación de código mientras convierte el esquema JSON en tipos de C#, consulte el archivo GenerationVerification.feature que se encuentra en C:\\SDK\\src\\Microsoft.Hadoop.Avro.Tools\\Doc.

Tenga en cuenta que los espacios de nombres se extraen del esquema JSON mediante la lógica descrita en el archivo mencionado en el párrafo anterior. Los espacios de nombres extraídos del esquema tienen prioridad sobre lo que se proporcione con el parámetro /n en la línea de comandos de la utilidad. Si desea reemplazar los espacios de nombres contenidos en el esquema, utilice el parámetro /nf. Por ejemplo, para cambiar todos los espacios de nombres de SampleJSONSchema.avsc a my.own.nspace, ejecute el comando siguiente:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## Muestras
En este tema se proporcionan seis ejemplos que ilustran escenarios diferentes admitidos por Microsoft Avro Library. Microsoft Avro Library está diseñada para funcionar con cualquier secuencia. En estos ejemplos, los datos se manipulan usando secuencias de memoria en lugar de secuencias de archivos o bases de datos por simplicidad y coherencia. El método que se elija en un entorno de producción dependerá de los requisitos exactos del escenario, el origen de datos y el volumen, restricciones de rendimiento y otros factores.

Los dos primeros ejemplos muestran cómo serializar y deserializar datos en búferes de secuencias de memoria usando reflexión y registros genéricos. Se supone que el esquema en estos dos casos se va a compartir entre los lectores y escritores no conectados.

Los ejemplos tercero y cuarto muestran cómo serializar y deserializar datos mediante los archivos contenedores de objetos de Avro. Cuando los datos se almacenan en un archivo contenedor de Avro, el esquema se almacena siempre con ellos porque debe compartirse para la deserialización.

La muestra que contiene los cuatro ejemplos primeros se puede descargar del sitio de <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">muestras de código de Azure</a>.

El quinto ejemplo muestra cómo usar un códec de compresión personalizado para archivos contenedores de objetos de Avro. Puede descargar una muestra que contiene el código para este ejemplo en el sitio de <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">muestras de código de Azure</a>.

El sexto ejemplo muestra cómo utilizar la serialización de Avro para cargar datos en el almacenamiento de blobs de Azure y después analizarlos mediante Hive con un clúster HDInsight (Hadoop). Se puede descargar desde el sitio de <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">muestras de código de Azure</a>.

A continuación se muestran los vínjculos a los seis ejemplos tratados en el tema:

 * <a href="#Scenario1">**Serialización mediante el uso de reflexión**</a>: el esquema JSON para los tipos que se van a serializar se compila automáticamente a partir de los atributos del contrato de datos.
 * <a href="#Scenario2">**Serialización mediante el uso de un registro genérico**</a>: el esquema JSON se especifica de forma explícita en un registro cuando no hay ningún tipo .NET disponible para reflexión.
 * <a href="#Scenario3">**Serialización mediante el uso de archivos contenedores de objetos con reflexión**</a>: el esquema JSON se compila de manera automática y se comparte junto con los datos serializados mediante el uso de un archivo contenedor de objetos de Avro.
 * <a href="#Scenario4">**Serialización mediante el uso de archivos contenedores de objetos con un registro genérico**</a>: el esquema JSON se especifica explícitamente antes de la serialización y se comparte junto con los datos mediante el uso de un archivo contenedor de objetos de Avro.
 * <a href="#Scenario5">**Serialización mediante el uso de archivos contenedores de objetos con un códec de compresión personalizado**</a>: este ejemplo muestra cómo crear un archivo contenedor de objetos de Avro con una implementación .NET personalizada del códec de compresión de datos Deflate.
 * <a href="#Scenario6">**Uso de Avro para cargar datos para el servicio HDInsight de Microsoft Azure**</a>: muestra cómo la serialización de Avro interactúa con el servicio HDInsight. Para ejecutar este ejemplo, se requiere una suscripción activa a Azure y acceso al clúster HDInsight.

###<a name="Scenario1"></a>Muestra 1: serialización mediante el uso de reflexión

El esquema JSON para los tipos se puede compilar automáticamente con Microsoft Avro Library usando reflexión a partir de los atributos del contrato de datos de los objetos de C# que se van a serializar. Microsoft Avro Library crea un [**IAvroSeralizer<T>**](http://msdn.microsoft.com/library/dn627341.aspx) para identificar los campos que se van a serializar.

En este ejemplo, los objetos (una clase **SensorData** con un miembro struct **Location**) se serializan en una secuencia de memoria que, a su vez, se deserializa. Después, se compara el resultado con la instancia inicial para confirmar que el objeto **SensorData** recuperado es idéntico al original.

En este ejemplo, se supone que el esquema está compartido entre los lectores y escritores, por lo que no es necesario el formato de contenedor de objetos de Avro. Para ver un ejemplo de cómo serializar y deserializar datos en búferes de memoria usando reflexión con el formato de contenedor de objetos cuando debe compartirse el esquema con los datos, consulte <a href="#Scenario3">Serialización mediante el uso de archivos contenedores de objetos con reflexión</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
		using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###Ejemplo 2: Serialización con un registro genérico

Cuando no se puede usar reflexión porque los datos no se pueden representar mediante clases .NET con un contrato de datos, se puede especificar el esquema JSON de forma explícita en un registro genérico. Este método suele ser más lento que cuando se utiliza la reflexión. En estos casos, el esquema de los datos puede ser dinámico, es decir, no se conoce hasta el momento de la compilación. Los datos representados como archivos de valores separados por comas (CSV) cuyo esquema se desconoce hasta que se transforman con el formato de Avro en tiempo de ejecución son un ejemplo de este tipo de escenario dinámico.

En este ejemplo se muestra cómo crear y usar un objeto [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) para especificar un esquema JSON de forma explícita, cómo rellenarlo con los datos y cómo serializarlo y deserializarlo. Después, se compara el resultado con la instancia inicial para confirmar que el registro recuperado es idéntico al original.

En este ejemplo, se supone que el esquema está compartido entre los lectores y escritores, por lo que no es necesario el formato de contenedor de objetos de Avro. Para ver un ejemplo de cómo serializar y deserializar datos en búferes de memoria usando un registro genérico con el formato de contenedor de objetos cuando debe incluirse el esquema con los datos serializados, consulte <a href="#Scenario4">Serialización mediante el uso de archivos contenedores de objetos con un registro genérico</a>.


	namespace Microsoft.Hadoop.Avro.Sample
	{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
	using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
	}
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###Ejemplo 3: Serialización mediante el uso de archivos contenedores de objetos y serialización con reflexión

Este ejemplo es similar al escenario del <a href="#Scenario1">primer ejemplo</a>, donde el esquema se especifica de forma implícita con reflexión. La diferencia es que aquí no se presupone su conocimiento por parte del lector que lo deserializa. Los objetos **SensorData** que se van a serializar y el esquema especificado de forma implícita se almacenan en un archivo contenedor de objetos representado por la clase [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx).

En este ejemplo, los datos se serializan con [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx) y se deserializan con [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Después, se compara el resultado con las instancias iniciales para asegurarse de la identidad.

Los datos del archivo contenedor de objetos se comprimen usando el códec de compresión predeterminado [**Deflate**][deflate-100] de .NET Framework 4. Consulte el <a href="#Scenario5">quinto ejemplo</a> de este tema para ver cómo se utiliza una versión más reciente y superior del códec de compresión [**Deflate**][deflate-110] disponible en .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
		using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file "{0}"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file "{0}". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file "{0}"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file "{0}"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file "{0}". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###Ejemplo 4: Serialización mediante el uso de archivos contenedores de objetos y serialización con registro genérico

Este ejemplo es similar al escenario del <a href="#Scenario2">segundo ejemplo</a>, donde el esquema se especifica de forma implícita con JSON. La diferencia es que aquí no se presupone su conocimiento por parte del lector que lo deserializa.

El conjunto de datos de prueba se recopila en una lista de objetos [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) usando un esquema JSON definido de forma explícita y se almacenan en un archivo contenedor de objetos representado por la clase [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx). Este archivo contenedor crea un escritor que se usa para serializar los datos, descomprimidos, en una secuencia de memoria que se guarda después en un archivo. El parámetro [**Codex.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) que se usa para crear el lector especifica que estos datos no se van a comprimir.

Los datos se leen después en el archivo y se deserializan en una colección de objetos. Esta colección se compara con la lista inicial de registros de Avro para confirmar que son idénticos.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
		using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file "{0}"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file "{0}". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file "{0}"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file "{0}"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file "{0}". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###Ejemplo 5: Serialización mediante el uso de archivos contenedores de objetos con un códec de compresión personalizado

El quinto ejemplo muestra cómo usar un códec de compresión personalizado para archivos contenedores de objetos de Avro. Puede descargar una muestra que contiene el código para este ejemplo desde el sitio de [muestras de código de Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111).

La [especificación de Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permite el uso de un códec de compresión opcional (además de los predeterminados **Null** y **Deflate**). En este ejemplo, no se implementa el códec totalmente nuevo Snappy (mencionado como códec opcional admitido en la [especificación de Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Se muestra cómo usar la implementación de .NET Framework 4.5 del códec [**Deflate**][deflate-110], que proporciona un algoritmo de compresión basado en la biblioteca de compresión [zlib](http://zlib.net/) mejor que el predeterminado de la versión .NET Framework 4.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
		using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file "{0}"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file "{0}". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file "{0}"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file "{0}"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file "{0}". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###Ejemplo 6: Uso de Avro para cargar datos para el servicio HDInsight de Microsoft Azure

El sexto ejemplo muestra algunas técnicas de programación relacionadas para interactuar con el servicio HDInsight de Azure. Puede descargar una muestra con el código para este ejemplo desde el sitio de [muestras de código de Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3).

La muestra hace lo siguiente:

* Se conecta a un clúster del servicio HDInsight existente.
* Serializa varios archivos CSV y carga el resultado en el almacenamiento de blobs de Azure. (Los archivos CSV se distribuyen junto con la muestra y representan un extracto de los datos históricos del mercado de valores AMEX distribuidos por [Infochimps](http://www.infochimps.com/) para el período 1970-2010. La muestra lee los datos de los archivos CSV, convierte los registros en instancias de la clase **Stock** y, después, los serializa mediante el uso de la reflexión. La definición del tipo de valores se crea en un esquema JSON utilizando la utilidad de generación de código de Microsoft Avro Library.
* Crea una nueva tabla externa denominada **Stocks** en Hive y la vincula a los datos cargados en el paso anterior.
* Ejecuta una consulta mediante Hive en la tabla **Stocks**.

Además, la muestra realiza un procedimiento de limpieza antes y después de realizar las principales operaciones. Durante la limpieza, todos los datos y carpetas de blobs de Azure se quitan, y se elimina la tabla de Hive. También puede llamar al procedimiento de limpieza desde la línea de comandos de la muestra.

La muestra cuenta con los siguientes requisitos previos:

* Una suscripción activa a Microsoft Azure y su identificador de suscripción.
* Un certificado de administración para la suscripción con la clave privada correspondiente. El certificado debe estar instalado en el almacenamiento privado del usuario actual en el equipo utilizado para ejecutar la muestra.
* Un clúster de HDInsight activo.
* Una cuenta de almacenamiento de Azure vinculada a un clúster de HDInsight desde el requisito previo anterior junto con la clave de acceso secundaria o primaria correspondiente.

Debe introducirse toda la información de los requisitos previos en el archivo de configuración de muestra antes de ejecutar la muestra. Existen dos formas posibles de hacerlo:

* Editar el archivo app.config en el directorio raíz de la muestra y después compilar la muestra.
* Compilar primero la muestra y después editar AvroHDISample.exe.config en el directorio de compilación.

En ambos casos, todas las modificaciones deben realizarse en la sección de configuración **<appSettings>**. Siga los comentarios del archivo. La muestra se ejecuta en la línea de comandos ejecutando el comando siguiente (donde se supone que el archivo .zip con la muestra se va a extraer en C:\\AvroHDISample; en caso contrario, utilice la ruta de acceso al archivo pertinente):

    AvroHDISample run C:\AvroHDISample\Data

Para limpiar el clúster, ejecute el comando siguiente:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx

<!---HONumber=AcomDC_0218_2016-->