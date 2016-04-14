<properties 
	pageTitle="Uso de AMQP 1.0 con la API del Bus de servicio de Java | Microsoft Azure" 
	description="Cómo usar Java Message Service (JMS) con el Bus de servicio de Azure y Advanced Message Queuing Protodol (AMQP) 1.0." 
	services="service-bus" 
	documentationCenter="java" 
	authors="sethmanheim" 
	writer="sethm" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/06/2015" 
	ms.author="sethm"/>

# Uso de la API de Java Message Service (JMS) con el Bus de servicio y AMQP 1.0

Advanced Message Queuing Protocol (AMQP) 1.0 es un protocolo de mensajes a nivel de red, confiable y eficaz que se puede utilizar para crear aplicaciones de mensajería robustas y compatibles con varias plataformas.

La compatibilidad con AMQP 1.0 del Bus de servicio implica que puede utilizar las funciones de colas y publicación/suscripción de mensajería asíncrona desde una amplia variedad de plataformas mediante un eficaz protocolo binario. Además, puede desarrollar aplicaciones formadas por componentes creados con una mezcla de lenguajes, marcos y sistemas operativos.

En este artículo se explica cómo utilizar las funciones de mensajería asincrónica del Bus de servicio (colas y publicación/suscripción a temas) desde aplicaciones Java utilizando la popular API estándar Java Message Service (JMS). Existe otra guía de instrucciones complementaria en la que se explica cómo hacer lo mismo utilizando la API de .NET del Bus de servicio. Puede utilizar estas dos guías conjuntamente para obtener información acerca de la mensajería entre diferentes plataformas mediante AMQP 1.0.

## Introducción al Bus de servicio

En esta guía se asume que ya dispone de un espacio de nombres del Bus de servicio con una cola denominada “queue1”. Si no es así, puede crear el espacio de nombres y la cola con ayuda del [Portal de Azure clásico](http://manage.windowsazure.com). Para obtener más información acerca de cómo crear espacios de nombres y colas del bus de servicio, consulte la guía de instrucciones titulada [Utilización de las colas del bus de servicio](service-bus-dotnet-how-to-use-queues.md).

> [AZURE.NOTE]Los temas y colas con particiones también admiten AMQP. Para obtener más información, consulte [Entidades de mensajería con particiones](service-bus-partitioning.md) y [Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio](service-bus-partitioned-queues-and-topics-amqp-overview.md).

## Descarga de la biblioteca de cliente AMQP 1.0 JMS

Para obtener información acerca de dónde descargar la versión más reciente de la biblioteca de cliente Apache Qpid JMS AMQP 1.0, visite [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

Debe agregar los cuatro archivos JAR siguientes del archivo de distribución de Apache Qpid JMS AMQP 1.0 al CLASSPATH de Java cuando vaya a crear y ejecutar aplicaciones JMS con el Bus de servicio:

*    geronimo-jms\_1.1\_spec-1.0.jar
*    qpid-amqp-1-0-client-[version].jar
*    qpid-amqp-1-0-client-jms-[version].jar
*    qpid-amqp-1-0-common-[version].jar

## Codificación de las aplicaciones Java

### Interfaz de denominación y directorio Java (JNDI)

JMS usa la interfaz de denominación y directorio Java (JNDI) para crear una separación entre los nombres lógicos y los físicos. Se resuelven dos tipos de objetos JMS usando JNDI: ConnectionFactory y Destination. JNDI usa un modelo de proveedor al que se pueden acoplar diferentes servicios de directorio para controlar labores de resolución de nombres. La biblioteca Apache Qpid JMS AMQP 1.0 se presenta con un proveedor JNDI basado en archivo de propiedades sencillas que se configura usando un archivo de propiedades cuyo formato es el siguiente:

```
# servicebus.properties - sample JNDI configuration
	
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net
	
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### Configuración de ConnectionFactory

La entrada usada para definir **ConnectionFactory** en el proveedor JNDI de archivo de propiedades Qpid tiene el formato siguiente:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Donde **[jndi\_name]** y **[ConnectionURL]** tienen los significados siguientes:

- **[jndi\_name]**: el nombre lógico de ConnectionFactory. Este es el nombre que se resolverá en la aplicación Java usando el método JNDI IntialContext.lookup().
- **[ConnectionURL]**: una URL que proporciona la biblioteca JMS con la información necesaria para el agente AMQP.

El formato de **ConnectionURL** es el siguiente:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```
Donde **[namespace]**, **[username]** y **[password]** tienen los significados siguientes:

- **[namespace]**: espacio de nombres de Bus de servicio.
- **[username]**: nombre del emisor del Bus de servicio.
- **[password]**: formato de codificación de dirección URL de la clave de emisor del Bus de servicio.

> [AZURE.NOTE]debe codificar la contraseña manualmente como dirección URL. Podrá encontrar una práctica utilidad de codificación de la URL en [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### Configuración de destinos

La entrada usada para definir un destino en el proveedor JNDI de archivo de propiedades Qpid tiene el formato siguiente:

```
queue.[jndi_name] = [physical_name]
```

o

```
topic.[jndi_name] = [physical_name]
```

Donde **[jndi\_name]** y **[physical\_name]** tienen los significados siguientes:

- **[jndi\_name]**: el nombre lógico del destino. Este es el nombre que se resolverá en la aplicación Java usando el método JNDI IntialContext.lookup().
- **[physical\_name]**: el nombre de la entidad del Bus de servicio a la que la aplicación envía mensajes o de la que los recibe.

> [AZURE.NOTE]al recibir de una suscripción al tema del bus de servicio, el nombre físico especificado en JNDI debe ser el nombre del tema. El nombre de la suscripción se proporciona cuando la suscripción duradera se crea en el código de aplicación JMS. La [Guía para desarrolladores sobre AMQP 1.0 del bus de servicio](service-bus-amqp-dotnet.md) proporciona más información acerca de cómo trabajar con las suscripciones a temas del bus de servicio desde JMS.

### Escritura de la aplicación JMS

No existen API especiales ni opciones obligatorias al usar JMS con el Bus de servicio. Sin embargo, hay algunas restricciones que se tratarán más adelante. Como con cualquier otra aplicación JMS, el primer requisito necesario es configurar el entorno JNDI para poder resolver **ConnectionFactory** y los destinos.

#### Configuración de JNDI InitialContext

El entorno JNDI se configura pasando una tabla hash con información de configuración al constructor de la clase javax.naming.InitialContext. Los dos elementos necesarios de la tabla hash son el nombre de la clase de Initial Context Factory y la URL del proveedor. El código siguiente indica cómo configurar el entorno JNDI para usar el proveedor JNDI basado en archivo de propiedades Qpid con un archivo de propiedades llamado **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### Aplicación JMS sencilla que usa una cola del Bus de servicio

El programa de ejemplo siguiente envía JMS TextMessages a una cola de Bus de servicio con el nombre lógico JNDI de QUEUE y recibe los mensajes de vuelta.

	// SimpleSenderReceiver.java
	
	import javax.jms.*;
	import javax.naming.Context;
	import javax.naming.InitialContext;
	import java.io.BufferedReader;
	import java.io.InputStreamReader;
	import java.util.Hashtable;
	import java.util.Random;
	
	public class SimpleSenderReceiver implements MessageListener {
	    private static boolean runReceiver = true;
	    private Connection connection;
	    private Session sendSession;
	    private Session receiveSession;
	    private MessageProducer sender;
	    private MessageConsumer receiver;
	    private static Random randomGenerator = new Random();
	
	    public SimpleSenderReceiver() throws Exception {
	        // Configure JNDI environment
	        Hashtable<String, String> env = new Hashtable<String, String>();
	        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
	        env.put(Context.PROVIDER_URL, "servicebus.properties");
	        Context context = new InitialContext(env);
	
	        // Lookup ConnectionFactory and Queue
	        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	        Destination queue = (Destination) context.lookup("QUEUE");
	
	        // Create Connection
	        connection = cf.createConnection();
	
	        // Create sender-side Session and MessageProducer
	        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
	        sender = sendSession.createProducer(queue);
	
	        if (runReceiver) {
	            // Create receiver-side Session, MessageConsumer,and MessageListener
	            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
	            receiver = receiveSession.createConsumer(queue);
	            receiver.setMessageListener(this);
	            connection.start();
	        }
	    }
	
	    public static void main(String[] args) {
	        try {
	
	            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
	                runReceiver = false;
	            }
	
	            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
	            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
	
	            while (true) {
	                String s = commandLine.readLine();
	                if (s.equalsIgnoreCase("exit")) {
	                    simpleSenderReceiver.close();
	                    System.exit(0);
	                } else {
	                    simpleSenderReceiver.sendMessage();
	                }
	            }
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	
	    private void sendMessage() throws JMSException {
	        TextMessage message = sendSession.createTextMessage();
	        message.setText("Test AMQP message from JMS");
	        long randomMessageID = randomGenerator.nextLong() >>>1;
	        message.setJMSMessageID("ID:" + randomMessageID);
	        sender.send(message);
	        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
	    }
	
	    public void close() throws JMSException {
	        connection.close();
	    }
	
	    public void onMessage(Message message) {
	        try {
	            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
	            message.acknowledge();
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	}	

### Ejecución de la aplicación

Al ejecutar la aplicación se obtienen resultados del tipo:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
	
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
	
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## Mensajería de diferentes plataformas entre JMS y .NET

Esta guía le ha mostrado cómo enviar y recibir mensajes con el Bus de servicio como origen y destino usando JMS. Sin embargo, una de las ventajas principales de AMQP 1.0 es que permite que las aplicaciones estén formadas por componentes escritos en diferentes lenguajes, a la vez que garantiza que los mensajes se intercambien con total seguridad y fidelidad.

Utilizando la aplicación JMS de ejemplo descrita anteriormente y una aplicación .NET similar tomada de la guía complementaria [Uso de la API de .NET con el Bus de servicio .NET y AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md), es posible intercambiar mensajes entre .NET y Java.

Para obtener más información acerca de la mensajería entre distintas plataformas mediante el Bus de servicio y AMQP 1.0, consulte la [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](service-bus-amqp-dotnet.md).

### De JMS a .NET

Para comprobar cómo funciona la mensajería de JMS a .NET:

* Inicie la aplicación de ejemplo .NET sin ningún argumento de línea de comandos.
* Inicie la aplicación de ejemplo Java con el argumento de línea de comandos “sendonly”. De esta manera, la aplicación no recibirá mensajes de la cola, sino que solo los enviará.
* Presione **Entrar** unas cuantas veces en la consola de la aplicación Java para enviar los mensajes.
* Estos mensajes los recibe la aplicación .NET.

#### Resultados de la aplicación JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### Resultados de la aplicación .NET

```
> SimpleSenderReceiver.exe	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### De .NET a JMS

Para comprobar cómo funciona la mensajería de .NET a JMS:

* Inicie la aplicación de ejemplo .NET con el argumento de línea de comandos "sendonly". De esta manera, la aplicación no recibirá mensajes de la cola, sino que solo los enviará.
* Inicie la aplicación de ejemplo Java sin argumentos de línea de comandos.
* Presione **Entrar** unas cuantas veces en la consola de la aplicación .NET para enviar los mensajes.
* Estos mensajes los recibe la aplicación Java.

#### Resultados de la aplicación .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### Resultados de la aplicación JMS

```
> java SimpleSenderReceiver	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## Características no admitidas y restricciones

Existen las restricciones siguientes al usar JMS sobre AMQP 1.0 con el Bus de servicio, a saber:

* Solo se permite un elemento **MessageProducer** o **MessageConsumer** por cada **sesión**. Si tiene que crear varios elementos **MessageProducers** o **MessageConsumers** en una aplicación, cree una **sesión** dedicada para cada uno de ellos.
* Actualmente no se admiten las suscripciones a temas volátiles.
* Por el momento no se admiten **MessageSelectors**.
* Actualmente no se admiten destinos temporales, como **TemporaryQueue** y **TemporaryTopic**, junto con las API **QueueRequestor** y **TopicRequestor** que los usan.
* No se admiten las sesiones de transacción ni las transacciones distribuidas.

## Resumen

En esta guía de instrucciones se indica cómo usar las características de mensajería asíncrona del Bus de servicio (colas y publicación/suscripción a temas) desde Java utilizando las populares JMS API y AMQP 1.0.

También puede utilizar AMQP 1.0 del Bus de servicio desde otros lenguajes, como .NET, C, Python y PHP. Los componentes creados utilizando estos lenguajes pueden intercambiar mensajes con seguridad y fidelidad gracias a la compatibilidad de AMQP 1.0 en el bus de servicio. Para obtener más información, consulte la [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](service-bus-amqp-dotnet.md).

## Pasos siguientes

* [Compatibilidad de AMQP 1.0 en el Bus de servicio de Azure](service-bus-amqp-overview.md)
* [Uso de AMQP 1.0 con la API .NET del bus de servicio](service-bus-dotnet-advanced-message-queuing.md)
* [Guía para desarrolladores sobre AMQP 1.0 del Bus de servicio](service-bus-amqp-dotnet.md)
* [Utilización de las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md)
* [Centro de desarrolladores de Java](/develop/java/).



 

<!---HONumber=AcomDC_1203_2015-->