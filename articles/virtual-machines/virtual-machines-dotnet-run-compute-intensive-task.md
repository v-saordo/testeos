<properties
	pageTitle="Ejecutar una aplicación .NET de proceso intensivo en una máquina virtual | Microsoft Azure"
	description="Aprenda a implementar y ejecutar una aplicación .NET de proceso intensivo en una máquina virtual de Azure y a usar colas del Bus de servicio de Azure para supervisar el progreso remotamente."
	services="virtual-machines"
	documentationCenter=".net"
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags=“azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/25/2015"
	ms.author="davidmu"/>

# Ejecución de una tarea de proceso intensivo en .NET en una máquina virtual de Azure

Con Azure puede utilizar una máquina virtual para controlar tareas de proceso intensivo. Por ejemplo, una máquina virtual puede controlar tareas y ofrecer los resultados a equipos cliente o a aplicaciones móviles. Después de completar el tutorial, habrá aprendido a crear una máquina virtual que ejecuta una aplicación .NET de proceso intensivo que otra aplicación .NET puede supervisar.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este tutorial se da por hecho que sabe cómo crear aplicaciones de consola .NET. Se presupone que no tiene conocimiento sobre Azure.

Aprenderá a:

* Crear una máquina virtual.
* Iniciar sesión de manera remota en la máquina virtual.
* Crear un espacio de nombres del Bus de servicio de Azure.
* Crear una aplicación .NET que realiza una tarea de proceso intensivo.
* Crear una aplicación .NET que supervisa el progreso de la tarea de proceso intensivo.
* Ejecutar las aplicaciones .NET.
* Detener las aplicaciones .NET.

En este tutorial se utilizará el problema del viajante para explicar la tarea de proceso intensivo. A continuación se mostrará un ejemplo de la aplicación .NET que ejecuta la tarea de proceso intensivo.

![Solucionador del problema del viajante][solver_output]

A continuación se muestra un ejemplo de la aplicación .NET que supervisa la tarea de proceso intensivo:

![Cliente del problema del viajante][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Para crear una máquina virtual

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Nuevo**.
3. Haga clic en **Máquina virtual**.
4. Haga clic en **Creación rápida**.
5. En la pantalla** Crear una máquina virtual**, especifique un valor en **Nombre de DNS**.
6. En la lista desplegable **Imagen**, seleccione una imagen, como **Windows Server 2012 R2**.
7. Escriba un nombre para el administrador en el campo **Nombre de usuario**. Recuerde este nombre y la contraseña que va a escribir a continuación ya que los utilizará cuando inicie sesión de forma remota en la máquina virtual.
8. Escriba una contraseña en el campo **Contraseña nueva** y vuelva a especificarla en el campo **Confirmar**.
9. En la lista desplegable **Ubicación**, seleccione la ubicación del centro de datos para la máquina virtual.
10. Haga clic en **Crear máquina virtual**. Puede supervisar el estado en la sección **Máquinas virtuales** del Portal de Azure clásico. Cuando el estado se muestre como **Activo**, podrá iniciar sesión en la máquina virtual.

## Para iniciar sesión de manera remota en la máquina virtual

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Máquinas virtuales**.
3. Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.
4. Haga clic en **Conectar**.
5. Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Cuando se le pida el nombre y la contraseña del administrador, utilice los valores que proporcionó cuando creó la máquina virtual.

## Creación de un espacio de nombres del Bus de servicio

Para comenzar a usar colas del Bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor con un ámbito para el desvío de recursos del Bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:

1.  Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Bus de servicio**.
3.  En el panel inferior del Portal de Azure clásico, haga clic en **Crear**.

    ![Creación de un nuevo bus de servicio][create_service_bus]
4.  En el cuadro de diálogo **Crear un espacio de nombres**, especifique un nombre para el espacio de nombres. El sistema realiza la comprobación de inmediato para ver si el nombre está disponible, ya que debe ser un nombre exclusivo.

    ![Creación de un cuadro de diálogo de espacio de nombres][create_namespace_dialog]
5.  Después de comprobar que el nombre de espacio de nombres esté disponible, seleccione la región en la que debe hospedarse el espacio de nombres (asegúrese de que usa la misma región en la que está hospedada la máquina virtual).

    > [AZURE.IMPORTANT] Seleccione la **misma región** que use o vaya a usar para la máquina virtual. Con esto conseguirá el máximo rendimiento.

6. Si dispone de más de una suscripción a Azure para la cuenta con la que ha iniciado sesión, seleccione la suscripción que desea usar para el espacio de nombres. Si cuenta solo con una suscripción para la cuenta con la que inicia sesión, no verá una lista desplegable que contenga las suscripciones.
7. Haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![Captura de pantalla de hacer clic para crear][click_create]

El espacio de nombres que creó aparecerá a continuación en el Portal de Azure clásico y tardará un poco en activarse. Espere hasta que el estado sea **Activo** antes de continuar con el siguiente paso.

## Obtención de credenciales de administración predeterminadas para el espacio de nombres

Para realizar operaciones de administración (como la creación de una cola) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

1.  En el panel de navegación izquierdo, haga clic en **Bus de servicio** para ver la lista de espacios de nombres disponibles. ![Captura de pantalla de espacios de nombres disponibles][available_namespaces]
2.  Seleccione el espacio de nombres que acaba de crear de la lista. ![Captura de pantalla de lista de espacio de nombres][namespace_list]
3. Haga clic en **Información de conexión**. ![Botón de clave de acceso][access_key_button]
4.  En el cuadro de diálogo, busque la entrada **Cadena de conexión**. Anote este valor, ya que usará la información que aparece a continuación en el tutorial para realizar operaciones con el espacio de nombres.

## Creación de una aplicación .NET que realiza una tarea de proceso intensivo

1. En la máquina de desarrollo (que no tiene que ser la máquina virtual que ha creado), descargue el [SDK de Azure para .NET](https://azure.microsoft.com/develop/net/).
2. Cree una aplicación de la consola .NET con el proyecto con el nombre TSPSolver. Asegúrese de que el marco de trabajo de destino esté establecido para .**NET Framework 4** o posterior (no para **.NET Framework 4 Client Profile**). El marco de trabajo de destino puede establecerse después de crear un proyecto mediante lo siguiente: en el menú de Visual Studio, haga clic en **Proyectos**, en **Propiedades**, en la pestaña **Aplicación** y, a continuación, establezca el valor para **Marco de trabajo de destino**.
3. Agregue en la biblioteca de Microsoft ServiceBus. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en **TSPSolver**, en **Agregar referencia**, en la pestaña **Examinar**, vaya a la ubicación del SDK de Azure para .NET (por ejemplo, C:\\Archivos de programa\\Microsoft SDKs\\Azure\\.NET SDK\\v2.5\\ToolsRef) y seleccione **Microsoft.ServiceBus.dll** como referencia.
4. Agregue la biblioteca System Runtime Serialization. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en **TSPSolver**, haga clic en** Agregar referencia**, haga clic en la pestaña** .NET** y, a continuación, seleccione **System.Runtime.Serialization** como referencia.
5. Use el código de ejemplo al final de esta sección para el contenido del archivo Program.cs.
6. Modifique el marcador de posición **your\_connection\_string** para usar la **cadena de conexión** del Bus de servicio.
7. Realice la compilación de la aplicación. De esta forma, se creará TSPSolver.exe en la carpeta bin del proyecto (bin\\release o bin\\debug, según si se dirige a una versión o a una compilación de depuración). Copiará este ejecutable y Microsoft.ServiceBus.dll en la máquina virtual más tarde.

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;

	namespace TSPSolver
	{
	    class Program
	    {
	        // Value specifying how often to provide an update to the console.
	        private static long loopCheck = 100000000;
	        private static long nTimes = 0, nLoops = 0;

	        private static double[,] distances;
	        private static String[] cityNames;
	        private static int[] bestOrder;
	        private static double minDistance;

	        private static NamespaceManager namespaceManager;
	        private static QueueClient queueClient;
	        private static String queueName = "TSPQueue";

	        private static void BuildDistances(String fileLocation, int numCities)
	        {

	            try
	            {
	                StreamReader sr = new StreamReader(fileLocation);
	                String[] sep1 = { ", " };

	                double[,] cityLocs = new double[numCities, 2];

	                for (int i = 0; i < numCities; i++)
	                {
	                    String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
	                    cityNames[i] = line[0];
	                    cityLocs[i, 0] = Convert.ToDouble(line[1]);
	                    cityLocs[i, 1] = Convert.ToDouble(line[2]);
	                }
	                sr.Close();

	                for (int i = 0; i < numCities; i++)
	                {
	                    for (int j = i; j < numCities; j++)
	                    {
	                        distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
	                        distances[j, i] = distances[i, j];
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static double hypot(double x, double y)
	        {
	            return Math.Sqrt(x * x + y * y);
	        }

	        private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
	        {
	            try
	            {

	                nTimes++;
	                if (nTimes == loopCheck)
	                {
	                    nLoops++;
	                    nTimes = 0;
	                    DateTime dateTime = DateTime.Now;
	                    Console.Write("Current time is {0}.", dateTime);
	                    Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
	                }

	                if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
	                {
	                    startCities.Add(restCities[0]);
	                    newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
	                    startCities.Remove(startCities[startCities.Count - 1]);
	                }
	                else
	                {
	                    for (int i = 0; i < restCities.Count; i++)
	                    {
	                        startCities.Add(restCities[0]);
	                        restCities.Remove(restCities[0]);
	                        permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
	                        restCities.Add(startCities[startCities.Count - 1]);
	                        startCities.Remove(startCities[startCities.Count - 1]);
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static void newBestDistance(List<int> cities, double distance)
	        {
	            try
	            {
	                minDistance = distance;
	                String cityList = "Shortest distance is " + minDistance + ", with route: ";

	                for (int i = 0; i < bestOrder.Length; i++)
	                {
	                    bestOrder[i] = cities[i];
	                    cityList += cityNames[bestOrder[i]];
	                    if (i != bestOrder.Length - 1)
	                        cityList += ", ";
	                }
	                Console.WriteLine(cityList);
	                queueClient.Send(new BrokeredMessage(cityList));
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        static void Main(string[] args)
	        {
	            try
	            {

                  String connectionString = @"your_connection_string";

	                int numCities = 10; // Use as the default, if no value is specified
	                // at the command line.
	                if (args.Count() != 0)
	                {

	                    if (args[0].ToLower().CompareTo("createqueue") == 0)
	                    {
	                        // No processing to occur other than creating the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.CreateQueue(queueName);
	                        Console.WriteLine("Queue named {0} was created.", queueName);
	                        Environment.Exit(0);
	                    }

	                    if (args[0].ToLower().CompareTo("deletequeue") == 0)
	                    {
	                        // No processing to occur other than deleting the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.DeleteQueue("TSPQueue");
	                        Console.WriteLine("Queue named {0} was deleted.", queueName);
	                        Environment.Exit(0);
	                    }

	                    // Neither creating or deleting a queue.
	                    // Assume the value passed in is the number of cities to solve.
	                    numCities = Convert.ToInt32(args[0]);
	                }

	                Console.WriteLine("Running for {0} cities.", numCities);

	                queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                List<int> startCities = new List<int>();
	                List<int> restCities = new List<int>();

	                startCities.Add(0);
	                for (int i = 1; i < numCities; i++)
	                {
	                    restCities.Add(i);
	                }
	                distances = new double[numCities, numCities];
	                cityNames = new String[numCities];
	                BuildDistances(@"c:\tsp\cities.txt", numCities);
	                minDistance = -1;
	                bestOrder = new int[numCities];
	                permutation(startCities, 0, restCities);
	                Console.WriteLine("Final solution found!");
	                queueClient.Send(new BrokeredMessage("Complete"));

	                queueClient.Close();
	                Environment.Exit(0);

	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}



## Crear una aplicación .NET que supervisa el progreso de la tarea de proceso intensivo

1. En la máquina de desarrollo, cree una aplicación de consola .NET con TSPClient como nombre de proyecto. Asegúrese de que el marco de trabajo de destino esté establecido para .**NET Framework 4** o posterior (no para **.NET Framework 4 Client Profile**). El marco de trabajo de destino puede establecerse después de crear un proyecto mediante lo siguiente: en el menú de Visual Studio, haga clic en **Proyectos**, en **Propiedades**, en la pestaña **Aplicación** y, a continuación, establezca el valor para **Marco de trabajo de destino**.
2. Agregue en la biblioteca de Microsoft ServiceBus. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en **TSPClient**, en **Agregar referencia**, en la pestaña **Examinar**, vaya a la ubicación del SDK de Azure para .NET (por ejemplo, C:\\Archivos de programa\\Microsoft SDKs\\Azure\\.NET SDK\\v2.5\\ToolsRef) y seleccione **Microsoft.ServiceBus.dll** como referencia.
3. Agregue la biblioteca System Runtime Serialization. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en **TSPClient**, haga clic en **Agregar referencia**, en la pestaña **.NET** y, a continuación, seleccione **System.Runtime.Serialization** como referencia.
4. Use el código de ejemplo al final de esta sección para el contenido del archivo Program.cs.
5. Modifique el marcador de posición **your\_connection\_string** para usar la **cadena de conexión** del Bus de servicio.
6. Realice la compilación de la aplicación. De esta forma, se creará TSPClient.exe en la carpeta bin del proyecto (bin\\release o bin\\debug, según si se dirige a una versión o a una versión de depuración). Puede ejecutar este código de la máquina de desarrollo o copiar este ejecutable y Microsoft.ServiceBus.dll en una máquina que ejecute la aplicación cliente (no es necesario estar en la máquina virtual).

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	using System.Threading; // For Thread.Sleep

	namespace TSPClient
	{
	    class Program
	    {

	        static void Main(string[] args)
	        {

	            try
	            {

	                Console.WriteLine("Starting at {0}", DateTime.Now);

									String connectionString = @"your_connection_string";

	                QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                BrokeredMessage message;

	                int waitMinutes = 3;  // Use as the default, if no value
	                // is specified at command line.

	                if (0 != args.Length)
	                {
	                    waitMinutes = Convert.ToInt16(args[0]);
	                }

	                String waitString;
	                waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";

	                while (true)
	                {
	                    message = queueClient.Receive();

	                    if (message != null)
	                    {
	                        try
	                        {
	                            string str = message.GetBody<string>();
	                            Console.WriteLine(str);

	                            // Remove message from queue.
	                            message.Complete();

	                            if ("Complete" == str)
	                            {
	                                Console.WriteLine("Finished at {0}.", DateTime.Now);
	                                break;
	                            }
	                        }
	                        catch (Exception e)
	                        {
	                            // Indicates a problem. Unlock the message in the queue.
	                            message.Abandon();
	                            throw e;
	                        }
	                    }
	                    else
	                    {
	                        // The queue is empty.
	                        Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
	                        System.Threading.Thread.Sleep(60000 * waitMinutes);
	                    }
	                }
	                queueClient.Close();
	                Environment.Exit(0);
	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}

## Ejecución de aplicaciones .NET

Ejecute la aplicación de proceso intensivo, cree primero la cola y después resuelva el problema del viajante, que agregará la mejor ruta actual a la cola del Bus de servicio. Mientras se está ejecutando esta aplicación (o después), ejecute el cliente para mostrar los resultados de la cola del Bus de servicio.

### Ejecución de la aplicación de proceso intensivo

1. Inicie sesión en la máquina virtual.
2. Cree una carpeta con el nombre c:\\TSP. Este es lugar en donde se ejecutará la aplicación.
3. Copie TSPSolver.exe y Microsoft.ServiceBus.dll. Ambos estarán disponibles en la carpeta bin del proyecto TSPSolver en c:\\TSP.
4. Cree un archivo con el nombre c:\\TSP\\cities.txt con el siguiente contenido.

		City_1, 1002.81, -1841.35
		City_2, -953.55, -229.6
		City_3, -1363.11, -1027.72
		City_4, -1884.47, -1616.16
		City_5, 1603.08, -1030.03
		City_6, -1555.58, 218.58
		City_7, 578.8, -12.87
		City_8, 1350.76, 77.79
		City_9, 293.36, -1820.01
		City_10, 1883.14, 1637.28
		City_11, -1271.41, -1670.5
		City_12, 1475.99, 225.35
		City_13, 1250.78, 379.98
		City_14, 1305.77, 569.75
		City_15, 230.77, 231.58
		City_16, -822.63, -544.68
		City_17, -817.54, -81.92
		City_18, 303.99, -1823.43
		City_19, 239.95, 1007.91
		City_20, -1302.92, 150.39
		City_21, -116.11, 1933.01
		City_22, 382.64, 835.09
		City_23, -580.28, 1040.04
		City_24, 205.55, -264.23
		City_25, -238.81, -576.48
		City_26, -1722.9, -909.65
		City_27, 445.22, 1427.28
		City_28, 513.17, 1828.72
		City_29, 1750.68, -1668.1
		City_30, 1705.09, -309.35
		City_31, -167.34, 1003.76
		City_32, -1162.85, -1674.33
		City_33, 1490.32, 821.04
		City_34, 1208.32, 1523.3
		City_35, 18.04, 1857.11
		City_36, 1852.46, 1647.75
		City_37, -167.44, -336.39
		City_38, 115.4, 0.2
		City_39, -66.96, 917.73
		City_40, 915.96, 474.1
		City_41, 140.03, 725.22
		City_42, -1582.68, 1608.88
		City_43, -567.51, 1253.83
		City_44, 1956.36, 830.92
		City_45, -233.38, 909.93
		City_46, -1750.45, 1940.76
		City_47, 405.81, 421.84
		City_48, 363.68, 768.21
		City_49, -120.3, -463.13
		City_50, 588.51, 679.33

5. En el símbolo del sistema, cambie los directorios a c:\\TSP.
6. Tendrá que crear la cola del Bus de servicio antes de ejecutar las permutaciones del solucionador del TSP. Ejecute el comando siguiente para crear la cola del Bus de servicio:

        TSPSolver createqueue

7. Ahora que se ha creado la cola, puede ejecutar las permutaciones del solucionador del TSP. Por ejemplo, ejecute el comando siguiente para ejecutar el solucionador para ocho ciudades.

        TSPSolver 8

 Si no especifica un número, el solucionador se ejecutará en 10 ciudades. Cuando el solucionador encuentra las rutas más cortas actuales, las agregará a la cola.

El solucionador se ejecutará hasta que acabe de examinar todas las rutas.

> [AZURE.NOTE]
Cuanto mayor sea el número que especifique, más tiempo se ejecutará el solucionador. Por ejemplo, si la ejecución en 14 ciudades puede tardar varios minutos, la ejecución en 15 ciudades podría tardar varias horas. Si se aumenta a 16 o más ciudades, la ejecución podría tardar varios días (posiblemente semanas, meses y años). Esto se debe al rápido aumento del número de permutaciones evaluadas por el solucionador a medida que aumenta el número de ciudades.

### Ejecución de la supervisión de la aplicación cliente
1. Inicie sesión en el equipo donde se va a ejecutar la aplicación cliente. No tiene que ser el mismo que ejecuta la aplicación TSPSolver, aunque podría serlo.
2. Cree una carpeta en la que ejecutará la aplicación. Por ejemplo, c:\\TSP.
3. Copie TSPClient.exe y Microsoft.ServiceBus.dll. Ambos están en la carpeta bin del proyecto TSPClient en c:\\TSP.
4. En el símbolo del sistema, cambie los directorios a c:\\TSP.
5. Ejecute el siguiente comando.

        TSPClient

    Opcionalmente, especifique el número de minutos de suspensión entre la comprobación de la cola, pasando un argumento de línea de comandos. El período de suspensión predeterminado para comprobar la cola es de tres minutos, que se utiliza cuando no se pasa ningún argumento de línea de comandos a TSPClient. Si desea utilizar un valor diferente para el intervalo de suspensión, por ejemplo, un minuto, ejecute el siguiente comando.

	    TSPClient 1

    El cliente se ejecutará hasta que vea un mensaje de cola de "Complete". Tenga en cuenta que si ejecuta varias ocurrencias del solucionador sin ejecutar el cliente, es posible que tenga que ejecutar el cliente varias veces para vaciar la cola por completo. Además, puede eliminar la cola y después volver a crearla. Para eliminar la cola, ejecute el siguiente comando TSPSolver (no TSPClient).

        TSPSolver deletequeue

## Detención de aplicaciones .NET

En ambas aplicaciones, el solucionador y el cliente, presione Ctrl+C para salir si desea acabar antes de la finalización normal.

## Alternativa a la creación y eliminación de la cola con TSPSolver
En lugar de usar TSPSolver para crear o eliminar la cola, puede crear o eliminar la cola en el [Portal de Azure clásico](https://manage.windowsazure.com). Consulte la sección del Bus de servicio del Portal de Azure clásico para obtener acceso a las interfaces de usuario para la creación o la eliminación de una cola, así como para la recuperación de la cadena de conexión, el emisor y la clave de acceso. También puede ver un panel de las colas del Bus de servicio, que le permitirá ver la métrica para los mensajes entrantes y salientes.

[solver_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
[client_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
[create_service_bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
[create_namespace_dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
[available_namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
[click_create]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
[namespace_list]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
[access_key_button]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png

<!---HONumber=AcomDC_0218_2016-->