<properties
   pageTitle="Descripción de los microservicios | Microsoft Azure"
   description="Información general sobre por qué la creación de aplicaciones de nube con un enfoque de microservicios es importante para el desarrollo de aplicaciones modernas y cómo Azure Service Fabric proporciona una plataforma para lograrlo"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/18/2015"
   ms.author="mfussell"/>

# ¿Por qué usar un enfoque de microservicios para crear aplicaciones?
La forma en que los desarrolladores de software nos planteamos la factorización de una aplicación en sus distintos componentes no es nueva en absoluto. Es el paradigma central de la orientación a objetos, las abstracciones de software y la componentización. En la actualidad, dicha factorización tiende a adoptar la forma de clases e interfaces entre bibliotecas compartidas y niveles de tecnología, normalmente mediante un enfoque por capas, con un almacén back-end, lógica de negocios en el nivel intermedio y una interfaz de usuario front-end. Lo que *ha* cambiado en los últimos años es que los desarrolladores ahora crean aplicaciones distribuidas para la nube impulsados por las empresas.

Las cambiantes necesidades empresariales son:

- La necesidad de crear y operar un servicio a escala que permita que el cliente tenga mayor cobertura (por ejemplo, que pueda llegar a nuevas regiones geográficas o que no tenga que realizar ninguna implementación en las ubicaciones de los clientes).
- Entrega más rápida de características y funciones para poder responder a las demandas de los clientes de forma ágil.
- Mejora de la utilización de los recursos para reducir costos.

Todas estas necesidades afectan a la *forma* en que se crean las aplicaciones.

## Enfoque de diseño monolítico en comparación con el de microservicios
Todas las aplicaciones evolucionan con el tiempo. Las aplicaciones que triunfan evolucionan por ser útiles a sus usuarios. Las que fracasan no evolucionan y terminan por entrar en desuso. Por consiguiente, la pregunta pertinente es: ¿cuánto sabe acerca de los requisitos actuales y cuáles cree que serán en el futuro? Por ejemplo, si va a crear una aplicación de generación de informes para un departamento y tiene la certeza tanto de que va a permanecer en el ámbito de la empresa como de que los informes van a ser efímeros, el enfoque que se elija será diferente del que se usaría, por ejemplo, para crear un servicio para entregar contenido de vídeo a decenas de millones de clientes. A veces, terminar un servicio como prueba de concepto es el factor determinante, ya que se es consciente de que el diseño de la aplicación se puede modificar más adelante. No tiene mucho sentido volcarse en la ingeniería de algo que no se va a usar nunca. Es el equilibrio habitual en ingeniería. Por otra parte, cuando las empresas hablan sobre la creación de aplicaciones para la nube, lo que esperan es crecimiento y uso. El problema es que el crecimiento y la escala son imprevisibles. Nos gustaría poder crear prototipos rápidamente y, al mismo tiempo, saber que estamos bien encaminados para tratar el futuro éxito. Este es el enfoque Lean Startup: compilar, medir, aprender e iterar.

En la época de cliente-servidor, tendíamos a centrarnos en la creación de aplicaciones en capas mediante el uso de tecnologías específicas en cada nivel. El término aplicación "monolítica" ha surgido de estos enfoques. Las interfaces tendían a estar entre las capas y normalmente se utilizaba un diseño más acoplado entre los componentes de cada nivel. Los desarrolladores diseñaban y generaban con clases compiladas en bibliotecas y las vinculaban conjuntamente en varios archivos EXE y DLL. Dicho enfoque de diseño monolítico tiene varias ventajas. Puede ser más fácil de diseñar y las llamadas entre los componentes son más rápidas, ya que a menudo se realizan a través de IPC. Además, todo el mundo prueba un único producto, lo que tiende a ser más eficaz en la relación entre personas y recursos. Las desventajas son que la aplicación está estrechamente emparejada en los niveles de capas, lo que no permite escalar los componentes individuales. Si necesita realizar actualizaciones o correcciones, tendrá que esperar hasta que otros usuarios finalicen sus pruebas, lo que dificulta la agilidad.

Los microservicios solucionan estas desventajas y se adaptan mejor a los requisitos empresariales descritos, pero también tienen ventajas y desventajas. Las ventajas de los microservicios son que cada uno suele encapsular funcionalidades empresariales más simples y se pueden escalar o reducir verticalmente, probar, implementar y administrar de forma independiente. Un beneficio importante del enfoque de microservicios es que los equipos suelen estar más condicionados por los escenarios empresariales que por la tecnología, que es lo que fomenta el enfoque en capas. En la práctica esto significa que los equipos más pequeños desarrollan un microservicio en función del escenario de un cliente, para lo que usan las tecnologías preferidas. En otras palabras, no es preciso que la organización normalice su tecnología para mantener monolitos. Además, los equipos individuales con servicios propios pueden hacer lo más lógico en función de sus conocimientos o de lo que sea más adecuado para el problema que el servicio intenta resolver. Indudablemente, en la práctica, es preferible tener un conjunto de tecnologías recomendadas, como un almacén NoSQL o un marco de aplicaciones web concretos. La desventaja de los microservicios es tener que administrar un mayor número de entidades independientes, trabajar con implementaciones más complejas y realizar el control de versiones, tener más tráfico de red entre los microservicios y las latencias de red correspondientes. Tener muchos servicios muy granulares fragmentados es una posible solución para los problemas de rendimiento. Sin herramientas que ayuden a ver estas dependencias, es difícil “ver” todo el sistema. En última instancia, los estándares son lo que hacen que el enfoque de microservicios funcione, pues establecen un acuerdo sobre cómo llevar a cabo la comunicación y tolerar únicamente lo que se necesita de un servicio, en lugar de contratos rígidos. Es importante definir estos contactos por adelantado en el diseño, ya que los servicios se van a actualizar de forma independiente entre sí. Otra descripción acuñada para realizar diseños con el enfoque de microservicios es "SOA específica".


***En su forma más simple, el enfoque del diseño de microservicios consiste en una federación desacoplada de servicios, con cambios independientes para cada uno y estándares acordados para la comunicación.***


A medida que se producen más aplicaciones en la nube, más personas detectan que la descomposición de la aplicación global en servicios independientes centrados en escenarios es un mejor enfoque a largo plazo.
## Comparación entre los enfoques de desarrollo de aplicaciones

![Desarrollo de aplicaciones de la plataforma Service Fabric][Image1]

1. Las aplicaciones monolíticas contienen funciones específicas del dominio y normalmente se dividen en capas funcionales, como Web, negocios y datos.

2. Para escalar aplicaciones monolíticas, es preciso clonarlas en varios servidores, máquinas virtuales o contenedores.

3. Las aplicaciones de microservicios separan las funciones en servicios más pequeños independientes.

4. Este enfoque se escala horizontalmente mediante la implementación de cada servicio de manera independiente, con la creación de instancias de estos servicios en servidores, máquinas virtuales y contenedores.


La realización del diseño con un enfoque de microservicios no es la panacea para todos los proyectos, pero se adapta mejor a los objetivos de negocio descritos. Además, comenzar con un enfoque monolítico puede ser aceptable si se sabe que después habrá oportunidad de reprocesar el código para que tenga un diseño con microservicios, en caso de que sea necesario. Es más frecuente comenzar con una aplicación monolítica y, poco a poco, dividirla en fases, empezando por las áreas funcionales que deban ser más escalables o ágiles.

En resumen, el enfoque de microservicios consiste en componer su aplicación con muchos servicios más pequeños que se ejecutan en los contenedores que se implementan en un clúster de máquinas. Cada servicio lo desarrolla un equipo pequeño que se centra en un escenario y cada servicio se prueba, se implementa, se escala y se crean versiones de él de manera independiente, con el fin de que se pueda desarrollar toda la aplicación.

## ¿Qué es un microservicio?

Existen diferentes definiciones de los microservicios y las búsquedas en Internet proporcionan muchos recursos de calidad que explican sus puntos de vista y definiciones. Sin embargo, la mayoría coincide en la mayor parte de las siguientes características de los microservicios:

- Encapsulan un escenario de cliente o negocio. ¿Cuál es el problema que va a solucionar?
- Los desarrolla un pequeño equipo de ingeniería.
- Se pueden escribir en cualquier lenguaje de programación y pueden usar cualquier marco.
- Constan de código y, opcionalmente, de un estado cuyo control de versiones, implementación y escalado se realizan de forma independiente.
- Interactúan con otros microservicios a través de protocolos e interfaces bien definidos.
- Tienen nombres únicos (direcciones URL) que pueden utilizarse para resolver su ubicación.
- Siguen siendo coherentes y estando disponibles en caso de errores.

Todo esto se puede resumir en:

***Las aplicaciones de microservicios se componen de servicios pequeños centrados en el cliente, escalables y con control de versiones independiente que se comunican entre sí mediante protocolos estándar con interfaces bien definidas.***


Ya tratamos los dos primeros puntos en la sección anterior y ahora ampliaremos y aclararemos los restantes.

### Se pueden escribir en cualquier lenguaje de programación y pueden usar cualquier marco.
Como desarrolladores, deberíamos disponer de la libertad de elegir el lenguaje o marco que queramos según nuestras aptitudes o las necesidades del servicio. En algunos servicios, se pueden valorar los beneficios, en términos de rendimiento, de C++ por encima de todo, mientras que en otros la facilidad del desarrollo administrado en C# o Java puede ser más importante. En algunos casos, es posible que se tenga que usar una biblioteca de terceros concreta, una tecnología de almacenamiento de datos o medios para exponer el servicio a los clientes.

Una vez que haya seleccionado la tecnología, pasamos a la administración operativa o del ciclo de vida y al escalado del servicio.

### Permite que el control de versiones, la implementación y el escalado del código y del estado se realicen de forma independiente  

Independientemente de la forma en que elija escribir los microservicios, cada uno de ellos se debe implementar, actualizar y escalar de forma independiente, tanto para el código como para, opcionalmente, el estado. De hecho, este es uno de los problemas más difíciles de resolver, ya que depende exclusivamente de las tecnologías que elija y, en el caso del escalado, de si se sabe cómo crear particiones tanto en el código como en el estado. Si el código y el estado usan tecnologías independientes (algo que hoy es bastante habitual), es preciso que los scripts de implementación del microservicio puedan escalar ambos. También tienen relevancia la agilidad y la flexibilidad, por lo que puede actualizar algunos de los microservicios sin que sea preciso de actualizar todos a la vez. Si volvemos un momento a la comparación entre el enfoque monolítico y el de microservicios, el diagrama siguiente muestra las diferencias en el enfoque del almacenamiento del estado.

#### Almacenamiento de estado en los distintos estilos de aplicación
![Almacenamiento de estados de la plataforma Service Fabric][Image2]

***A la izquierda se encuentra el enfoque monolítico, con una base de datos única y varios niveles de tecnologías específicas.***

***A la derecha se encuentra el enfoque de microservicios, un gráfico de microservicios interconectados donde el estado normalmente tiene como ámbito el microservicio y se usan diversas tecnologías.***

En un enfoque monolítico, normalmente la aplicación usa una base de datos única. La ventaja es que está en una única ubicación, lo que facilita la implementación. Cada componente puede tener una única tabla para almacenar su estado. La dificultad reside en que los equipos deben ser estrictos en la separación del estado y es inevitable que exista la tentación de agregar simplemente una nueva columna a una tabla existente del cliente, realizar una combinación entre tablas y, por lo general, crear dependencias en la capa de almacenamiento. Tras esto, no es posible escalar los componentes individuales. En el enfoque de microservicios, cada servicio administra y almacena su propio estado, lo que significa que es responsable de escalar conjuntamente tanto el código como el estado para que cumplan las demandas del servicio. La desventaja surge cuando es necesario crear vistas, o consultas, de los datos de las aplicaciones, ya que tendrá que consultar estos almacenes de estado dispares. Por lo general, esto se resuelve teniendo un microservicio independiente que genera una vista de una colección de microservicios. Si necesita realizar varias consultas ad hoc en los datos, cada microservicio debería considerar la posibilidad escribir sus datos en un servicio de almacenamiento de datos para poder realizar análisis sin conexión.


El control de versiones es específico de la versión implementada de un microservicio. Se requiere para poder distribuir y ejecutar en paralelo varias versiones diferentes. El control de versiones sirve para los escenarios en que una versión más reciente de un microservicio produce un error durante la actualización y es preciso revertir a una versión anterior. El otro escenario para el control de versiones es la realización de pruebas de estilo A/B en las que distintos usuarios experimentan versiones diferentes del servicio. Por ejemplo, es común actualizar un microservicio para que un conjunto específico de clientes prueben probar nuevas funcionalidades, antes de distribuirlas más ampliamente. Después de la administración del ciclo de vida de los microservicios, pasamos a la comunicación entre ellos.


### Interactúan con otros microservicios por medio de protocolos e interfaces bien definidos.

Sobre este tema, basta fundamentalmente con leer la amplia documentación sobre la arquitectura orientada a servicios publicada en los últimos diez años, ya que en gran parte estaba orientada a los patrones de comunicación. Por lo general, en la actualidad, esto básicamente consiste en usar un enfoque de REST con los protocolos HTTP y TCP, y XML o JSON como formato de serialización. Desde la perspectiva de la interfaz, esto supone adoptar el enfoque de diseño web. Pero no hay nada que le impida usar protocolos binarios o sus propios formatos de datos. No obstante, debe estar preparado para que a los usuarios les resulte más complicado usar sus microservicios si están disponibles públicamente.

### Tienen un nombre único (URL) que puede usarse para resolver su ubicación

¿Recuerda que repetimos varias veces que el enfoque de microservicios es como la Web? Al igual que la Web, el microservicio debe ser direccionable, independientemente de dónde se ejecute. Si piensa en las máquinas y en cuál de ellas ejecuta un microservicio concreto, va las cosas empeorarán rápidamente. De la misma manera que DNS resuelve una dirección URL determinada en un equipo concreto, su microservicio debe tener un nombre único para que se pueda detectar dónde se encuentra actualmente. Los microservicios necesitan nombres direccionables que los hagan independientes de la infraestructura en que se ejecutan. Por supuesto, esto implica que existe una interacción entre la forma en que se implementa el servicio y cómo se detecta, ya que debe haber un Registro del servicio. También es preciso que haya una interacción entre el momento en que se produce un error en la máquina y lo que sucede en el microservicio para que el Registro del servicio pueda indicar dónde se ejecuta en ese momento. Esto nos lleva al siguiente tema: la resistencia y la coherencia.

### Siguen siendo coherentes y estando disponibles en el caso de errores.

Enfrentarse a errores inesperados es uno de los problemas más difíciles de resolver, sobre todo en un sistema distribuido. Una gran parte del código que escribimos los desarrolladores sirve para administrar las excepciones y en las pruebas, dedicamos gran parte de nuestro tiempo a esta tarea. No obstante, el problema requiere algo más que escribir código para controlar los errores. ¿Qué ocurre cuando se produce un error en la máquina en que se ejecuta el microservicio? No solo es preciso detectar el error del microservicio (un problema difícil en sí), sino que se necesita algo para reiniciar el microservicio. Es necesario que el microservicio sea resistente a los errores y posea la capacidad de reiniciarse con frecuencia en otro equipo por motivos de disponibilidad. También es importante qué estado se guardó en nombre del microservicio, desde dónde se puede recuperar dicho estado y si se puede reiniciar correctamente. En otras palabras, es preciso que haya resistencia en el proceso (es decir, el proceso se reinicia), así como en el estado o en los datos (que no se pierdan datos y que estos mantengan su coherencia).

Los problemas de resistencia se agravan en otros escenarios, como cuando se producen errores en la actualización de una aplicación. El microservicio, si funciona junto con el sistema de implementación, no necesita recuperarse. También es preciso decidir si puede pasar a la versión más reciente o es preciso revertirlo a una versión anterior para mantener la coherencia del estado. Se deben tener en cuenta cuestiones tales como si hay máquinas suficientes como para pasar a la versión más reciente y cómo recuperar las versiones anteriores del microservicio. Para esto, se requiere que el microservicio emita información de estado que permita tomar estas decisiones.

### Notifica el estado y diagnósticos

Es esencial que los microservicios informen sobre su mantenimiento y los resultados de los diagnósticos. Esto es algo que, aunque pueda parecer obvio, a menudo se pasa por alto. De lo contrario, hay poca información desde la perspectiva de las operaciones. El desafío es ver la correlación de los eventos de diagnóstico en un conjunto de servicios independientes en el que cada uno registra de forma independiente y controla que el sesgo del reloj de la máquina para entender el orden de eventos. De la misma manera que se interactúa con un microservicio por medio de protocolos y formatos de datos acordados, surge la necesidad de estandarizar la forma en que se registran los eventos de mantenimiento y diagnóstico que, en último término, terminan en un almacén de eventos que se puede consultar y ver. En un enfoque de microservicios, resulta esencial que los distintos equipos se pongan de acuerdo en un formato de registro único, ya que se necesita que haya un enfoque coherente para ver los eventos de diagnóstico en la aplicación en conjunto.

El estado es diferente de los diagnósticos. Estado significa que el microservicio notifica su estado actual, con el fin de que se puedan realizar acciones. La más evidente es colaborar con los mecanismos de actualización e implementación para mantener la disponibilidad. Por ejemplo, un servicio puede tener un estado incorrecto debido a un bloqueo de UN proceso o al reinicio de la máquina, y seguir operativo. En este caso, lo menos recomendables es realizar una actualización. El mejor enfoque es investigar primero, o bien dejar tiempo para que el microservicio se recupere. Por tanto, los eventos de estado de un microservicio permiten tomar decisiones fundamentadas y, de hecho, ayudan a crear servicios de recuperación automática.

## Service Fabric como plataforma de microservicios

Azure Service Fabric nació a partir de la transición que realizó Microsoft de ofrecer productos embalados, cuyo estilo era habitualmente monolítico, a ofrecer servicios. El motor principal de Service Fabric fue la experiencia de crear y operar servicios grandes, como Base de datos SQL de Azure, DocumentDB y otros servicios principales de Azure. Abordamos las necesidades empresariales en cuanto a escalabilidad, agilidad e independencia de los equipos, y dejamos que la plataforma evolucionara con el tiempo a medida que la fueron adoptando cada vez más servicios. Algo importante era que Service Fabric tenía que ejecutarse en cualquier lugar, no solo en Azure, sino también en implementaciones independientes de Windows Server.

***El objetivo de Service Fabric es resolver los problemas que conlleva crear y ejecutar un servicio, como los errores o las actualizaciones, y usar los recursos de infraestructura de forma eficaz, con el fin de que los equipos puedan resolver los problemas empresariales con un enfoque de microservicios.***

Service Fabric proporciona dos amplias áreas que le ayudan a crear aplicaciones con un enfoque de microservicios:

- Una plataforma que consta de un conjunto de servicios del sistema que se ocupan de las implementaciones, las actualizaciones, la detección y el reinicio de servicios con errores, la detección de los lugares en que se ejecutan los servicios, la administración del estado, la supervisión del estado, etc. De hecho, estos servicios del sistema hacen posibles muchas de las características de microservicios descritas antes.

-  API de programación, o marcos, que le ayudan a crear aplicaciones como microservicios. Las API de programación que se proporcionan se denominan [Reliable Actors y Reliable Services](service-fabric-choose-framework.md). Por supuesto, puede utilizar el código que prefiera para generar un microservicio. Sin embargo, estas API facilitan el trabajo y se integran con la plataforma a un nivel más profundo. Por ejemplo, con ellas es posible obtener información de estado y de los diagnósticos, o se puede aprovechar la alta disponibilidad integrada.

***Service Fabric es independiente de la forma en que cree el servicio y permite usar cualquier tecnología. Sin embargo, proporciona API de programación integradas que facilitan enormemente la compilación de microservicios.***

### ¿Son los microservicios adecuados para mi aplicación?

Es posible. En nuestra experiencia, a medida que se encargaba a más y más equipos de Microsoft que crearan para la nube por razones empresariales, muchos de ellos constataron las ventajas de adoptar el enfoque de microservicios. Por ejemplo, Bing lleva años haciendo esto con la búsqueda. Para otros equipos, este enfoque fue toda una novedad. Por consiguiente, se toparon con problemas complicados que necesitaban solución, algo que no estaba entre sus fortalezas principales. Por esta razón, Service Fabric ganó popularidad como tecnología preferida para la creación de servicios.

El objetivo de Service Fabric es reducir las complejidades que conlleva crear aplicaciones con un enfoque de microservicios, con el fin de que no haya que realizar muchos cambios costosos de diseño. Comience poco a poco, escale cuando sea necesario, deje de usar servicios, agregue otros nuevos y evolucione con el uso del cliente, así es este enfoque. También sabemos que en la realidad hay muchos otros problemas que aún deben resolverse para que los microservicios sean más accesibles para la mayoría de los desarrolladores. Los contenedores y el modelo de programación de actor son ejemplos de pasos pequeños tomados en esa dirección y estamos seguros de que surgirán más innovaciones para facilitar esta tarea. <!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

* Para obtener más información:
	* [Información general de Service Fabric](service-fabric-overview.md)
	* [Introducción técnica](service-fabric-technical-overview.md)
* Configuración del [entorno de desarrollo](service-fabric-get-started.md) de Service Fabric
* Selección de un [marco de modelo de programación](service-fabric-choose-framework.md) para el servicio

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png

<!---HONumber=AcomDC_0107_2016-->