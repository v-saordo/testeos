## Envío de mensajes a Centros de eventos

En esta sección se escribirá una aplicación C para enviar eventos al Centro de eventos. Usaremos la biblioteca Proton AMQP del [proyecto Apache Qpid](http://qpid.apache.org/). Esto es parecido a usar temas y colas de Bus de servicio con AMQP a través de C como se muestra [aquí](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para más información, vea la [documentación de Qpid Proton](http://qpid.apache.org/proton/index.html).

1. En la página [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), haga clic en el vínculo **Instalación de Qpid Proton** y siga las instrucciones dependiendo de su entorno. Consideraremos un entorno de Linux, por ejemplo, una [máquina virtual Linux de Azure](../virtual-machines/virtual-machines-linux-tutorial.md) con Ubuntu 14.04.

2. Para compilar la biblioteca Proton, instale los paquetes siguientes:

	```
	sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
	```

3. Descargue la [biblioteca de Qpid Proton](http://qpid.apache.org/proton/index.html) y extráigala, por ejemplo:

	```
	wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
	tar xvfz qpid-proton-0.7.tar.gz
	```

4. Cree un directorio de compilación, compile y realice la instalación:

	```
	cd qpid-proton-0.7
	mkdir build
	cd build
	cmake -DCMAKE_INSTALL_PREFIX=/usr ..
	sudo make install
	```

5. En su directorio de trabajo, cree un nuevo archivo denominado **sender.c** con el siguiente contenido. No olvide sustituir el valor para el nombre del Centro de eventos y el espacio de nombres (este último suele ser `{event hub name}-ns`). También debe sustituir una versión con codificación URL de la clave para la regla **SendRule** creada anteriormente. Puede codificar con URL [aquí](http://www.w3schools.com/tags/ref_urlencode.asp).

	```
	#include "proton/message.h"
	#include "proton/messenger.h"
	
	#include <getopt.h>
	#include <proton/util.h>
	#include <sys/time.h>
	#include <stddef.h>
	#include <stdio.h>
	#include <string.h>
	#include <unistd.h>
	#include <stdlib.h>
	
	#define check(messenger)                                                     \
	  {                                                                          \
	    if(pn_messenger_errno(messenger))                                        \
	    {                                                                        \
	      printf("check\n");													 \
	      die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
	    }                                                                        \
	  }  
	
	pn_timestamp_t time_now(void)
	{
	  struct timeval now;
	  if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
	  return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
	}  
	
	void die(const char *file, int line, const char *message)
	{
	  printf("Dead\n");
	  fprintf(stderr, "%s:%i: %s\n", file, line, message);
	  exit(1);
	}
	
	int sendMessage(pn_messenger_t * messenger) {
		char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
		char * msgtext = (char *) "Hello from C!";
	
		pn_message_t * message;
		pn_data_t * body;
		message = pn_message();
	
		pn_message_set_address(message, address);
		pn_message_set_content_type(message, (char*) "application/octect-stream");
		pn_message_set_inferred(message, true);
	
		body = pn_message_body(message);
		pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
	
		pn_messenger_put(messenger, message);
		check(messenger);
		pn_messenger_send(messenger, 1);
		check(messenger);
	
		pn_message_free(message);
	}
	
	int main(int argc, char** argv) {
		printf("Press Ctrl-C to stop the sender process\n");
	
		pn_messenger_t *messenger = pn_messenger(NULL);
		pn_messenger_set_outgoing_window(messenger, 1);
		pn_messenger_start(messenger);
	
		while(true) {
			sendMessage(messenger);
			printf("Sent message\n");
			sleep(1);
		}
	
		// release messenger resources
		pn_messenger_stop(messenger);
		pn_messenger_free(messenger);
	
		return 0;
	}
	```

6. Compile el archivo, suponiendo que **gcc**:

	```
	gcc sender.c -o sender -lqpid-proton
	```

> [AZURE.NOTE] En este código, usamos una ventana de salida de 1 para forzar que los mensajes salgan tan pronto como sea posible. En general, la aplicación debe probar con los mensajes por lotes para aumentar el rendimiento. Vea la [página Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) para más información sobre cómo usar la biblioteca de Qpid Proton en este y otros entornos y desde las plataformas para las que se proporcionan enlaces (actualmente, Perl, PHP, Python y Ruby).

<!---HONumber=AcomDC_0204_2016-->