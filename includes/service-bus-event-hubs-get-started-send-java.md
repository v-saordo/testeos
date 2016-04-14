## Envío de mensajes a Centros de eventos

En esta sección se escribirá una aplicación de consola Java para enviar eventos al Centro de eventos. Usaremos el proveedor de JMS AMQP del [proyecto Apache Qpid](http://qpid.apache.org/). Esto es parecido a usar temas y colas de Bus de servicio con AMQP a través de Java, como se muestra [aquí](../service-bus/service-bus-java-how-to-use-jms-api-amqp.md). Para obtener más información, consulte la [documentación de Qpid JMS](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) y el [servicio de mensajería de Java](http://www.oracle.com/technetwork/java/jms/index.html).

1. En Eclipse, instale el [Kit de herramientas de Azure para Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx). Incluye las bibliotecas de cliente de Qpid JMS AMQP.

2. En Eclipse, cree un nuevo proyecto de Java denominado **Remitente**.

3. En el Explorador de paquetes de Eclipse, haga clic con el botón derecho en el proyecto **Remitente** y seleccione **Propiedades**. En el panel izquierdo del cuadro de diálogo, haga clic en **Ruta de compilación Java**, elija la pestaña **Bibliotecas** y luego seleccione el botón **Agregar Biblioteca**. Seleccione **Paquete para bibliotecas de cliente de Apache Qpid de JMS (por MS Open Tech)**, haga clic en **Siguiente**, y, a continuación, haga clic en **Finalizar**.

	![][8]

4. Cree un archivo denominado **servicebus.properties** en la raíz del proyecto **Remitente**, con el siguiente contenido. No olvide sustituir los valores de su:
	- Nombre del centro de eventos.
	- Nombre del espacio de nombres (este suele ser `{event hub name}-ns`).
	- Clave **SendRule** codificada mediante URL (anotó esta clave cuando creó el Centro de eventos). Puede codificar con URL [aquí](http://www.w3schools.com/tags/ref_urlencode.asp).

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. Cree una clase nueva denominada **Remitente**. Agregue las instrucciones siguientes `import`:

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;

		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException;

6. Después agregue el siguiente código:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();

			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}

		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=Nov15_HO3-->