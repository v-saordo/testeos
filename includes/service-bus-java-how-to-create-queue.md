## ¿Qué son las colas del Bus de servicio?

Las colas del Bus de servicio son compatibles con el modelo de comunicación de **mensajería asíncrona**. Cuando se usan colas, los componentes de una aplicación distribuida no se comunican directamente entre sí, sino que intercambian mensajes a través de una cola, que actúa como un intermediario (agente). El productor del mensaje (remitente) manda un mensaje a la cola y, a continuación sigue con su procesamiento. De forma asíncrona, el destinatario del mensaje (receptor) extrae el mensaje de la cola y lo procesa. El productor no tiene que esperar una respuesta del destinatario para continuar el proceso y el envío de más mensajes. Las colas ofrecen una entrega de mensajes según el modelo **El primero en entrar es el primero en salir (FIFO)** a uno o más destinatarios de la competencia. Es decir, normalmente los receptores reciben y procesan los mensajes en el orden en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes.

![QueueConcepts](./media/service-bus-java-how-to-create-queue/sb-queues-08.png)

Las colas del Bus de servicio son una tecnología de uso general que puede utilizarse en una variedad de escenarios:

-   Comunicación entre los roles de trabajo y web en una aplicación de Azure de niveles múltiples.
-   Comunicación entre aplicaciones locales y aplicaciones hospedadas de Azure en una solución híbrida.
-   Comunicación entre componentes de una aplicación distribuida que se ejecuta en local en distintas organizaciones o departamentos de una organización.

El uso de las colas le permite escalar sus aplicaciones horizontalmente más fácilmente y dotar de más resiliencia a su arquitectura.

## Creación de un espacio de nombres de servicio

Para comenzar a usar colas del Bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:

1.  Inicie sesión en el [Portal de Azure clásico][].

2.  En el panel de navegación izquierdo del Portal, haga clic en **Bus de servicio**.

3.  En el panel inferior del Portal, haga clic en **Crear**. 
	![](./media/service-bus-java-how-to-create-queue/sb-queues-03.png)

4.  En el cuadro de diálogo **Agregar un nuevo espacio de nombres**, escriba un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible. 
	![](./media/service-bus-java-how-to-create-queue/sb-queues-04.png)

5.  Después de asegurarse de que el nombre de espacio de nombres está disponible, seleccione el país o región en el que debe hospedarse el espacio de nombres (asegúrese de que usa el mismo país o la misma región en los que está realizando la implementación de los recursos de proceso).

	IMPORTANTE: seleccione la **misma región** que vaya a seleccionar para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6. 	Deje los demás campos del cuadro de diálogo con los valores predeterminados (**Mensajería** y **Nivel estándar**) y, a continuación, haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![](./media/service-bus-java-how-to-create-queue/getting-started-multi-tier-27.png)

El espacio de nombres que creó tardará un momento en activarse y, después, aparecerá en el Portal de Azure. Espere hasta que el estado del espacio de nombres sea **Activo** antes de continuar.

## Obtención de credenciales de administración predeterminadas para el espacio de nombres

Para realizar operaciones de administración (como la creación de una cola) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres. Puede obtener estas credenciales en el portal.

1.  En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para ver la lista de espacios de nombres disponibles: 
	![](./media/service-bus-java-how-to-create-queue/sb-queues-13.png)

2.  Haga clic en el espacio de nombres que acaba de crear en la lista mostrada.

3.  Haga clic en **Configurar** para ver las directivas de acceso compartido para el espacio de nombres. 
	![](./media/service-bus-java-how-to-create-queue/sb-queues-14.png)

4.  Anote la clave principal o cópiela en el Portapapeles.

  [Portal de Azure clásico]: http://manage.windowsazure.com

  [34]: ./media/service-bus-java-how-to-create-queue/VSProperties.png

<!---HONumber=AcomDC_0128_2016-->