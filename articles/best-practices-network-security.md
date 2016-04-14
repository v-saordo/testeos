<properties
   pageTitle="Servicios en la nube de Microsoft y seguridad de red | Microsoft Azure"
   description="Obtenga información acerca de las características clave disponibles en Azure para crear entornos de red seguros"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# Servicios en la nube de Microsoft y seguridad de red

Servicios en la nube de Microsoft ofrece servicios e infraestructura a gran escala, funcionalidades de calidad empresarial y numerosas opciones de conectividad híbrida. Los clientes pueden elegir tener acceso a estos servicios a través de Internet o con ExpressRoute, que proporciona conectividad de red privada. La plataforma Microsoft Azure permite a los clientes ampliar su infraestructura en la nube sin problemas y crear arquitecturas de varios niveles. Para complementar los servicios de Microsoft, otros fabricantes pueden habilitar capacidades mejoradas que ofrecen servicios de seguridad y dispositivos virtuales. Este documento proporciona una visión general de los problemas de seguridad y de arquitectura que los clientes deben tener en cuenta al usar Servicios en la nube de Microsoft con acceso a través de ExpressRoute así como la creación de servicios seguros en la red virtual de Microsoft Azure.

## Inicio rápido
El gráfico de lógica siguiente puede dirigirle a un ejemplo específico de las numerosas técnicas de seguridad disponibles con la plataforma Microsoft Azure. Úselo para encontrar exactamente lo que necesita de forma rápida o lea el documento desde el principio para obtener una explicación de muchas de las opciones disponibles. ![Diagrama de flujo de opciones de seguridad][0]

[Ejemplo 1: Creación de una red perimetral para proteger las aplicaciones con grupos de seguridad de red](#example-1-build-a-simple-dmz-with-nsgs)</br> [Ejemplo 2: Creación de una red perimetral para proteger las aplicaciones con un firewall y grupos de seguridad de red](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br> [Ejemplo 3: Creación de una red perimetral para proteger las redes con un firewall, enrutamiento definido por el usuario y grupo de seguridad de red](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br> [Ejemplo 4: Incorporación de una conexión híbrida con una VPN de dispositivo virtual de sitio a sitio](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br> [Ejemplo 5: Incorporación de una conexión híbrida con una VPN de puerta de enlace de Azure de sitio a sitio](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br> [Ejemplo 6: Incorporación de una conexión híbrida con ExpressRoute](#example-6-adding-a-hybrid-connection-with-expressroute)</br> En los próximos meses se agregarán a este documento ejemplos para agregar conexiones de red virtual a red virtual, alta disponibilidad y encadenamiento de servicios.

## Protección de infraestructura y cumplimiento normativo de Microsoft
Microsoft ha adoptado una posición de liderazgo al apoyar las iniciativas de cumplimiento normativo requeridas por los clientes de empresa. Éstas son algunas de las certificaciones de cumplimiento normativo para Azure: ![Notificaciones de cumplimiento de Azure][1]

Encontrará más detalles en: [http://azure.microsoft.com/support/trust-center/compliance/](https://azure.microsoft.com/support/trust-center/compliance/)

Microsoft tiene un enfoque integral para proteger la infraestructura en la nube necesaria para ejecutar servicios globales a gran escala. La infraestructura en la nube de Microsoft incluye hardware, software, redes, personal administrativo y operativo, así como centros de datos físicos.

![Características de seguridad de Azure][2]

El enfoque anterior proporciona una base segura para que los clientes implementen los servicios en la nube de Microsoft. El siguiente paso es que los clientes diseñen y creen una arquitectura de seguridad para proteger estos servicios.

## Arquitecturas de seguridad tradicionales y redes perimetrales
Aunque Microsoft realiza importantes inversiones en la protección de la infraestructura en la nube, los clientes también deben proteger los servicios en la nube y los grupos de recursos. Un enfoque de varios niveles de seguridad proporciona la mejor defensa. Una red perimetral de seguridad de red protege los recursos de red internos de una red que no sea de confianza. La red perimetral es un concepto conocido en arquitectura de seguridad de redes de TI de empresa, que hace referencia a los bordes o partes de la red que se encuentran entre Internet y la infraestructura de TI de empresa protegida.

En las redes de empresa típicas, la infraestructura principal está considerablemente fortalecida en los perímetros, con varios niveles de dispositivos de seguridad. El límite de cada nivel constará de dispositivos y puntos de aplicación de directivas. Los dispositivos pueden incluir: firewalls, prevención de DDoS (denegación de servicio distribuido), identificadores y direcciones IP (detección de intrusiones o sistemas de protección), dispositivos de VPN (redes privadas virtuales), etc. La aplicación de directivas puede adoptar la forma de directivas de firewall, listas de control de acceso (ACL) o enrutamiento específico. La primera línea de defensa de la red, que acepta directamente el tráfico entrante desde Internet, constará de una combinación de estos mecanismos para bloquear los ataques y el tráfico perjudicial mientras pasa por solicitudes legítimas aún más dentro de la red. Este tráfico se enruta directamente a los recursos de la red perimetral. Ese recurso puede, a continuación, "hablar" con recursos más profundos de la red, pasando el siguiente límite para la validación antes de enrutarse de forma más profunda en la red. El nivel exterior se denomina red perimetral porque esta parte de la red está expuesta a Internet, normalmente con algún tipo de protección a ambos lados de la red perimetral. La siguiente ilustración muestra un ejemplo de una sola red perimetral de subred en una red corporativa con dos límites de seguridad (red perimetral-Internet y red perimetral-VLAN back-end).

![Red perimetral en una red corporativa][3]

Existen numerosas arquitecturas usadas para implementar una red perimetral, desde un simple equilibrador de carga ante una granja de servidores web hasta varias redes perimetrales de subred con distintos mecanismos en cada límite para bloquear el tráfico y proteger los niveles más profundos de la red corporativa. El modo de crear la red perimetral depende de las necesidades específicas de la organización y de la tolerancia al riesgo asociada.

Al desplazar los clientes las cargas de trabajo a nubes públicas, es fundamental admitir capacidades similares para la arquitectura de la red perimetral en Azure a fin de cumplir los requisitos de seguridad y cumplimiento normativo. Este documento proporciona instrucciones sobre cómo pueden crear los clientes un entorno de red seguro en Azure; se centra en la red perimetral pero abarca una discusión completa sobre numerosos aspectos de la seguridad de red, comenzando por las siguientes preguntas pero sin limitarse a ellas:

1.	¿Cómo se puede crear una red perimetral en Azure?
2.	¿Cuáles son algunas de las características de Azure disponibles para crear la red perimetral?
3.	¿Cómo se pueden proteger las cargas de trabajo back-end?
4.	¿Cómo se controlan las comunicaciones de Internet con las cargas de trabajo en Azure?
5.	¿Cómo se pueden proteger las redes locales de las implementaciones en Azure?
6.	¿Cuándo se deben utilizar las características de seguridad de Azure nativas frente a dispositivos o servicios de terceros?

El siguiente diagrama muestra los distintos niveles de seguridad que Azure ofrece a los clientes tanto nativos en la misma plataforma Azure como a través de las características definidas por el cliente:

![Arquitectura de seguridad de Azure][4]

Está la protección DDoS de Azure entrante desde Internet que vigila ataques a gran escala contra Azure. Una vez superado esto, se llegaría a los extremos públicos definidos por el cliente que se usan para determinar el tráfico que puede pasar a través del servicio en la nube a la red virtual. El aislamiento de la red virtual de Azure nativa garantiza un aislamiento completo de todas las demás redes y que el tráfico solo fluya a través de los métodos y las rutas de acceso configurados por el usuario. Estas rutas de acceso y métodos son el siguiente nivel en el que los grupos de seguridad de red (NSG), el enrutamiento definido por el usuario (UDR) y los dispositivos virtuales de red se pueden usar para crear límites de seguridad, incluidas las redes perimetrales, para proteger las implementaciones de aplicaciones en la red protegida.

La siguiente sección proporciona una visión general de las redes virtuales de Azure. Los clientes crean redes virtuales de Azure, que son a las que están conectadas las cargas de trabajo implementadas. Las redes virtuales constituyen la base de todas las características de seguridad de red necesarias para establecer una red perimetral con el fin de proteger las implementaciones de clientes en Azure.

## Información general sobre las redes virtuales de Azure
Antes de que el tráfico de Internet pueda llegar a las redes virtuales de Azure, hay dos niveles de seguridad inherentes a la plataforma Azure:

1.	**Protección de DDoS**: la protección de denegación de servicio distribuido (DDoS) es un nivel de la red física de Azure que protege la misma plataforma Azure de ataques basados en Internet a gran escala, en los que los atacantes usan varios nodos "bot" en un intento de sobrecargar un servicio de Internet. Azure ofrece una sólida malla de protección de DDoS en toda la conectividad de Internet entrante. Este nivel de protección de DDoS no tiene ningún atributo configurable por el usuario y no es accesible para el cliente. Esto protege a Azure como plataforma de los ataques a gran escala pero no protegerá directamente las aplicaciones de cliente individuales. El cliente puede configurar niveles adicionales de resistencia contra un ataque localizado. Por ejemplo: si se ataca al cliente A con un ataque de DDoS a gran escala en un extremo público, Azure bloqueará las conexiones a ese servicio. El cliente A puede conmutar a otra red virtual o a un extremo de servicio no implicado en el ataque para restaurar el servicio. Se debe tener en cuenta que mientras el cliente A puede verse afectado en ese extremo, no se vería afectado ningún otro servicio fuera de ese extremo. Además, otros clientes y servicios no verían impacto alguno de este ataque.
2.	**Extremos de servicio**: los extremos permiten que los servicios en la nube o grupos de recursos tengan direcciones IP públicas (en Internet) y puertos expuestos, el extremo traducirá el tráfico a la dirección interna y al puerto en la red virtual de Azure. Se trata de la ruta de acceso principal para que el tráfico externo pase a la red virtual de Azure. Los extremos de servicio son configurables por el usuario para determinar qué tráfico se pasa, y cómo y dónde es traducido en la red virtual. 

Una vez que el tráfico llega a la red virtual, hay muchas características que entran en juego ya que las redes virtuales de Azure son la base para que los clientes conecten sus cargas de trabajo y en las que se aplica una seguridad de nivel de red básica. Es una red privada (una superposición de red virtual) en Azure para clientes con las funciones y características siguientes:
 
1.	**Aislamiento del tráfico**: una red virtual es el límite de aislamiento del tráfico en la plataforma Azure. Las máquinas virtuales de una red virtual no se pueden comunicar directamente con las máquinas virtuales de otra red virtual diferente, incluso si las dos redes virtuales las creó el mismo cliente. Se trata de una propiedad fundamental que garantiza que las máquinas virtuales del cliente y la comunicación sigan siendo privadas en una red virtual. 
2.	**Topología de múltiples niveles**: las redes virtuales permiten a los clientes definir una topología de múltiples niveles al asignar subredes y designar espacios de direcciones independientes para distintos elementos o "niveles" de las cargas de trabajo. Estas agrupaciones lógicas y topologías permiten a los clientes definir una directiva de acceso diferente en función de los tipos de cargas de trabajo y controlar igualmente los flujos de tráfico entre los niveles. 
3.	**Conectividad entre instalaciones locales**: los clientes pueden establecer conectividad entre instalaciones locales entre una red virtual y varios sitios locales u otras redes virtuales en Azure mediante puertas de enlace de VPN de Azure o dispositivos virtuales de red de terceros. Azure es compatible con VPN de sitio a sitio (S2S) mediante los protocolos estándar IPsec/IKE y conectividad privada de ExpressRoute. 
4.	**Grupo de seguridad de red** (NSG) permite a los clientes crear reglas (ACL) con el nivel de granularidad deseado: interfaces de red, máquinas virtuales individuales o subredes virtuales. Los clientes pueden controlar el acceso al permitir o denegar la comunicación entre las cargas de trabajo dentro de una red virtual, desde sistemas de redes del cliente a través de conectividad entre locales o comunicación de Internet directa. 
5.	**Rutas definidas por el usuario** (UDR) y **Reenvío IP** permiten a los clientes definir las rutas de comunicación entre distintos niveles dentro de una red virtual. Los clientes pueden implementar un firewall, identificadores y direcciones IP y otros dispositivos virtuales y enrutar el tráfico de red a través de estos dispositivos de seguridad para la aplicación de directivas de límites de seguridad, auditoría e inspección.
6.	**Dispositivos virtuales de red** en Azure Marketplace: hay dispositivos de seguridad, como firewalls, equilibradores de carga e identificadores/direcciones IP (servicios de detección y prevención de intrusiones) disponibles en Azure Marketplace y la Galería de imágenes de máquina virtual. Los clientes pueden implementar estos dispositivos en sus redes virtuales y, en concreto, en los límites de seguridad (incluidas las subredes de la red perimetral) para completar un entorno de red segura en múltiples niveles.

Con estas características y funcionalidades, a continuación se muestra un ejemplo de cómo se puede construir una arquitectura de red perimetral en Azure:

![Red perimetral en una red virtual de Azure][5]

## Requisitos y características de una red perimetral
Esta sección describe las características y los requisitos de una red perimetral en Azure. Tal como se describió anteriormente, la red perimetral está diseñada para ser el front-end de la red y crear una interfaz directamente con la comunicación desde Internet. Los paquetes entrantes deben fluir a través de los dispositivos de seguridad: firewall, identificadores, direcciones IP, etc. de la red perimetral antes de llegar a los servidores back-end. Los paquetes enlazados a Internet de las cargas de trabajo también pueden fluir a través de los dispositivos de seguridad de la red perimetral para fines de auditoría, inspección y aplicación de directivas antes de salir de la red. Además, también se puede usar la red perimetral para hospedar las puertas de enlace de VPN entre locales entre redes virtuales del cliente y redes locales.

### Características de una red perimetral
En referencia a la ilustración anterior, una red perimetral de una red virtual de Azure, algunas de las características de una buena red perimetral son las siguientes:

- Red perimetral accesible desde Internet (front-end):
    - La propia subred de red perimetral es accesible desde Internet y se comunica directamente con Internet
    - Las direcciones IP públicas, direcciones IP virtuales o extremos de servicio pasan tráfico de Internet a la red front-end y los dispositivos
    - El tráfico entrante de Internet pasa a través de dispositivos de seguridad antes de otros recursos de la red front-end
    - Si está habilitada la seguridad saliente, el tráfico pasa a través de dispositivos de seguridad, como último paso, antes de pasar a Internet
- "Red protegida" (back-end): hacia la infraestructura central
    - No hay una ruta directa desde Internet a la infraestructura central
    - Los canales hacia la infraestructura central DEBEN atravesar dispositivos de seguridad, como NSG, firewalls o dispositivos VPN en la red perimetral
    - Otros dispositivos de la red perimetral NO DEBEN tender puentes entre Internet y la infraestructura central
    - Los dispositivos de seguridad accesibles tanto de Internet y como los límites accesibles a la red protegida de la red perimetral (por ejemplo, los dos iconos que se muestran en la ilustración anterior del firewall) en realidad pueden ser un solo dispositivo virtual con reglas diferenciadas o interfaces para el límite de Internet y el límite de back-end (es decir, un dispositivo, separado lógicamente, que administra la carga de los dos límites de la red perimetral)
- Otras prácticas y restricciones comunes
    - Las cargas de trabajo en la red perimetral NO DEBEN almacenar información crítica de la empresa
    - El acceso y las actualizaciones de las configuraciones e implementaciones de red perimetral están limitadas solo a los administradores autorizados

### Requisitos de una red perimetral
Para habilitar estas características, en la lista siguiente se proporciona información sobre los requisitos de red virtual para implementar una red perimetral correcta:

- Arquitectura de subred: especifique la red virtual de modo que se dedica una subred completa como red perimetral, separada de otras subredes en la misma red virtual. Esto garantizará el tráfico entre la red perimetral y otro flujo de niveles de subred interna o privada a través de un firewall o dispositivo virtual de identificadores y direcciones IP en los límites de la subred con rutas definidas por el usuario.
- Grupo de seguridad de red (NSG) de la red perimetral: la subred de la red perimetral debe estar abierta para permitir la comunicación entre la red perimetral e Internet pero esto no significa que los clientes puedan omitir los grupos de seguridad de red. Siga las recomendaciones de seguridad habituales para minimizar las superficies de red expuestas a Internet al bloquear intervalos de direcciones remotas que pueden tener acceso a implementaciones y/o protocolos de aplicación específicos y puertos que están abiertos. Tenga en cuenta que estos no siempre son posibles. Por ejemplo, si los clientes tienen un sitio web externo en Azure, la red perimetral debe permitir las solicitudes web entrantes desde cualquier dirección IP pública pero solo debe abrir los puertos de aplicación web: TCP:80 y TCP:443.
- Tabla de enrutamiento de red perimetral: la subred de red perimetral en sí debe poder comunicarse directamente con Internet pero no debe permitir la comunicación directa a y desde el back-end o en redes locales sin pasar por un firewall o dispositivo de seguridad.
- Configuración del dispositivo de seguridad de red perimetral: para enrutar e inspeccionar los paquetes entre la red perimetral y el resto de las redes protegidas, los dispositivos de seguridad como dispositivos de firewall, identificadores y direcciones IP de la red perimetral pueden tener hosts múltiples, con una NIC independiente para la red perimetral y las subredes back-end. Las NIC de la red perimetral se comunicarán directamente desde y hacia Internet, con los correspondientes grupos de seguridad de red y tabla de enrutamiento de red perimetral. Las NIC que se conectan a las subredes back-end tendrán grupos de seguridad de red y tablas de enrutamiento de las correspondientes subredes back-end más restringidos.
- Funciones del dispositivo de seguridad de red perimetral: los dispositivos de seguridad implementados en la red perimetral suelen realizar las siguientes funcionalidades:
    - Firewall: aplicación de reglas de firewall o directivas de control de acceso para las solicitudes entrantes
    - Detección y prevención de amenazas: detecta y mitiga los ataques malintencionados desde Internet
    - Auditoría y registro: mantener registros detallados para auditoría y análisis
    - Proxy inverso: redirige las solicitudes entrantes a los servidores back-end correspondientes al asignar y traducir las direcciones de destino de los dispositivos front-end de la red perimetral, normalmente firewalls, a las direcciones de servidores back-end reales.
    - Proxy de reenvío: proporciona NAT y también realiza auditorías para comunicaciones iniciadas desde la red virtual hacia Internet
    - Enrutador: actúa como enrutador para reenviar tráfico entrante y entre subredes dentro de la red virtual
    - Dispositivo VPN: actúa como puertas de enlace de VPN entre locales para conectividad VPN entre locales entre clientes de redes locales y redes virtuales de Azure
    - Servidor VPN: actúa como servidores de VPN para aceptar clientes de VPN que se conectan a redes virtuales de Azure

>[AZURE.TIP] Hacer que las personas autorizadas a tener acceso al engranaje de seguridad de la red perimetral sean totalmente distintas de las personas autorizadas como administradores de desarrollo/implementación/operaciones de aplicaciones. Mantener estos grupos separados permite una separación de funciones y evita que una sola persona omita la seguridad de las aplicaciones y los controles de seguridad de red.

### Preguntas que se deben plantear al crear límites de red
Cuando se habla de "Redes de Azure" en esta sección, a menos que especifique de otro modo, todas las redes (redes, redes virtuales o subredes) hacen referencia a redes virtuales de Azure privadas creadas por un administrador de suscripción y no se habla de redes físicas subyacentes dentro de Azure.

Además, las redes virtuales de Azure a menudo se usan para extender las redes locales tradicionales. Es posible incorporar soluciones de redes híbridas de sitio a sitio o de ExpressRoute con arquitecturas de redes perimetrales. Esta es una consideración importante a la hora de crear límites de seguridad de red y se describe en las preguntas siguientes.

Por lo tanto, al crear una red con una red perimetral y varios límites de seguridad, se debe responder a tres preguntas fundamentales.

#### 1) ¿Cuántos límites se necesitan?
La primera decisión consiste en decidir cuántos límites de seguridad son necesarios en un escenario determinado:

- Un solo límite: uno en la red perimetral front-end entre la red virtual e Internet.
- Dos límites: uno en el lado de Internet de la red perimetral y otro entre la subred de la red perimetral y las subredes back-end de las redes virtuales de Azure
- Tres límites: en el lado de Internet de la red perimetral, entre la red perimetral y las subredes back-end y entre las subredes back-end y la red local
- N límites: en función de los requisitos de seguridad, no existe un tope para el número de límites de seguridad que se pueden aplicar a una red determinada.

El número y el tipo de límites necesarios variarán en función de la tolerancia al riesgo de la empresa y el escenario concreto que se implementa. Por lo general, suele ser una decisión tomada conjuntamente por varios grupos de una organización, a menudo, un equipo de riesgos y cumplimientos, un equipo de redes/plataformas y un equipo de desarrollo de aplicaciones. Las personas con conocimientos sobre seguridad, los datos implicados y las tecnologías que se van a usar deben participar en esta toma de decisiones a fin de garantizar la posición de seguridad adecuada para cada implementación.

>[AZURE.TIP] Use el menor número posible de límites que cumplan los requisitos de seguridad para una situación determinada. Cuanto mayor sea el número de límites, más difíciles pueden ser las operaciones y la solución de problemas, así como la sobrecarga de administración implicada en la administración de varias directivas de límites con el tiempo. Sin embargo, unos límites insuficientes aumentan el riesgo. Encontrar el equilibrio es fundamental.

![Red híbrida con tres límites de seguridad][6]

La ilustración anterior muestra una visión general de alto nivel de una red con tres límites de seguridad, con un límite entre la red perimetral e Internet, entre las subredes privadas front-end y back-end de Azure y entre la subred back-end de Azure y la red corporativa local.

#### (2) ¿Dónde se encuentran los límites?
Una vez decidido el número de límites, la siguiente decisión es saber dónde se implementan. Por lo general, hay tres opciones; 1) usar un servicio intermedio basado en Internet (por ejemplo, nube basada en WAFS, que no se trata en este documento); 2) usar las características nativas o dispositivos virtuales de red en Azure;y 3) usar dispositivos físicos en la red local.

En redes únicamente de Azure, las opciones son características de Azure nativas (por ejemplo, equilibradores de carga de Azure) o dispositivos virtuales de red del ecosistema de socios enriquecido de Azure (por ejemplo, firewalls de punto de comprobación).

Si se necesita un límite entre Azure y una red local, los dispositivos de seguridad pueden residir en cualquier lado de la conexión (o en ambos). Por lo tanto, se debe tomar una decisión sobre la ubicación en la que se va a colocar un engranaje de seguridad.

En la ilustración anterior, los límites entre Internet y red perimetral y entre front-end y back-end están totalmente incluidos en Azure y deben ser características nativas de Azure o dispositivos virtuales de red. Los dispositivos de seguridad en el límite entre Azure (subred back-end) y la red corporativa pueden estar tanto en el lado de Azure como en el local o incluso una combinación de dispositivos en ambos lados. Pueden existir importantes ventajas y desventajas en cualquiera de las opciones, que se deben muy en cuenta.

Por ejemplo, mediante el uso de engranajes de seguridad físicos existentes en el lado de la red local. Ventaja: no se necesita ningún engranaje, solo un nueva configuración. Desventaja: todo el tráfico debe ir desde Azure a la red local para que lo pueda ver el engranaje de seguridad. Por lo tanto, el tráfico de Azure a Azure puede sufrir una latencia importante y afectar a la experiencia de usuario y al rendimiento de las aplicaciones si se obligó a dirigirlo a la red local para la aplicación de las directivas de seguridad.

#### (3) ¿Cómo se implementan los límites?
Cada límite de seguridad probablemente tendrá requisitos de capacidad diferentes (por ejemplo, identificadores y reglas de firewall en Internet de la red perimetral pero solo listas de control de acceso entre la red perimetral y la subred back-end). La decisión sobre los dispositivos que se van a usar dependerá de los requisitos de escenario y seguridad. En los ejemplos 1, 2 y 3 de redes perimetrales se describen algunas opciones que se pueden usar. La revisión de las características de red nativa de Azure y los dispositivos disponibles en Azure en el ecosistema de socios expondrá las innumerables opciones disponibles para resolver prácticamente cualquier escenario.

Otro punto de decisión de implementación clave es cómo conectar la red local con Azure. A través de la puerta de enlace virtual de Azure o un dispositivo virtual de red. Estas opciones se describen y se tratan con más detalle en los ejemplos 4, 5 y 6 descritos a continuación.

Además, puede ser necesaria la red virtual para el tráfico de red virtual dentro de Azure, estos escenarios se describen con más detalle en los ejemplos 7 y 8 siguientes.

Una vez conocidas las respuestas a las preguntas anteriores, la sección [Inicio rápido](#fast-start) anterior puede ayudar a identificar qué ejemplos son más adecuados para un escenario determinado.

## Creación de límites de seguridad con redes virtuales de Azure
### Ejemplo 1: Creación de una red perimetral simple con grupos de seguridad de red
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example1]

![Red perimetral de entrada con grupo de seguridad de red][7]

#### Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Dos servicios en la nube: "FrontEnd001" y "BackEnd001"
- Una red virtual, “CorpNetwork”, con dos subredes, “FrontEnd” y “BackEnd”
- Un grupo de seguridad de red que se aplica a ambas subredes
- Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores Windows que representan servidores back-end de aplicaciones (“AppVM01”, “AppVM02”)
- Un servidor Windows que representa un servidor DNS ("DNS01")

Hay más detalles para crear este ejemplo (proporciona ambos scripts y una plantilla ARM) que se encuentran en la página de [instrucciones de compilación detalladas][Example1].

#### Descripción de un grupo de seguridad de red (NSG)
En este ejemplo, se crea un grupo de seguridad de red y se cargan después seis reglas.

>[AZURE.TIP] Por lo general, debe crear primero las reglas específicas de "Permitir" y, por último, las reglas de "Denegar" más genéricas. La prioridad asignada indica qué reglas se evalúan primero. Una vez que se encuentra el tráfico para aplicar a una regla específica, no se evalúa ninguna otra regla. Se pueden aplicar reglas de grupo de seguridad de red en cualquier dirección, entrante o saliente (desde la perspectiva de la subred).

De forma declarativa, se compilan las reglas siguientes para el tráfico entrante:

1.	Se permite el tráfico DNS interno (puerto 53)
2.	Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina virtual
3.	Se permite el tráfico HTTP (puerto 80) desde Internet al servidor web (IIS01)
4.	Se permite cualquier tráfico (todos los puertos) desde IIS01 a AppVM1.
5.	Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (ambas subredes).
6.	Se deniega todo el tráfico (todos los puertos) desde la subred front-end a la subred back-end.

Con estas reglas enlazadas a cada subred, si una solicitud HTTP era entrante desde Internet al servidor web, se aplicarían ambas reglas, la 3 (permitir) y la 5 (denegar), pero como la regla 3 tiene mayor prioridad, solo se aplicaría esa y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al servidor web. Si ese mismo tráfico estaba intentando ponerse en contacto con el servidor DNS01, se aplicaría primero la regla 5 (denegar) y no se permitiría que el tráfico pase al servidor. La regla 6 (denegar) impide que la subred front-end se comunique con la subred back-end (excepto el tráfico permitido en las reglas 1 y 4) y esto protege la red back-end en el caso de que un atacante ponga en peligro la aplicación web en el front-end, y el atacante tendría acceso limitado a la red back-end "protegida" (solo a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario; esto se verá en el "Ejemplo 3" que aparece a continuación.

#### Conclusión
Se trata de una manera relativamente sencilla y directa de aislar la subred back-end del tráfico entrante. Más información sobre este ejemplo a continuación:

- Creación de esta red perimetral con scripts de PowerShell
- Creación de esta red perimetral con una plantilla ARM
- Descripciones detalladas de cada comando NSG
- Escenarios de flujo de tráfico detallados, que muestran cómo se permite o deniega el tráfico en cada nivel

Se puede encontrar en la página de [instrucciones detalladas de compilación][Example1].

### Ejemplo 2: Creación de una red perimetral para proteger las aplicaciones con un firewall y grupos de seguridad de red
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example2]

![Red perimetral de entrada con dispositivo virtual de red y grupo de seguridad de red][8]

#### Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Dos servicios en la nube: "FrontEnd001" y "BackEnd001"
- Una red virtual, “CorpNetwork”, con dos subredes, “FrontEnd” y “BackEnd”
- Un único grupo de seguridad de red que se aplica a ambas subredes
- Un dispositivo virtual de red, en este ejemplo, un firewall, conectado a la subred front-end
- Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores Windows que representan servidores back-end de aplicaciones (“AppVM01”, “AppVM02”)
- Un servidor Windows que representa un servidor DNS ("DNS01")

Hay más detalles para crear este ejemplo (proporciona ambos scripts y una plantilla ARM) que se encuentran en la página de [instrucciones de compilación detalladas][Example2].

#### Descripción de un grupo de seguridad de red (NSG)
En este ejemplo, se crea un grupo de seguridad de red y se cargan después seis reglas.

>[AZURE.TIP] Por lo general, debe crear primero las reglas específicas de "Permitir" y, por último, las reglas de "Denegar" más genéricas. La prioridad asignada indica qué reglas se evalúan primero. Una vez que se encuentra el tráfico para aplicar a una regla específica, no se evalúa ninguna otra regla. Se pueden aplicar reglas de grupo de seguridad de red en cualquier dirección, entrante o saliente (desde la perspectiva de la subred).

De forma declarativa, se compilan las reglas siguientes para el tráfico entrante:

1.	Se permite el tráfico DNS interno (puerto 53)
2.	Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina virtual
3.	Se permite cualquier tráfico de Internet (todos los puertos) al dispositivo de red virtual (firewall)
4.	Se permite cualquier tráfico (todos los puertos) desde IIS01 a AppVM1.
5.	Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (ambas subredes).
6.	Se deniega todo el tráfico (todos los puertos) desde la subred front-end a la subred back-end.

Con estas reglas enlazadas a cada subred, si una solicitud HTTP era entrante desde Internet al firewall, se aplicarían ambas reglas, la 3 (permitir) y la 5 (denegar), pero como la regla 3 tiene mayor prioridad, solo se aplicaría esa y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al firewall. Si ese mismo tráfico estaba intentando ponerse en contacto con el servidor IIS01, incluso si está en la subred front-end, se aplicaría la regla 5 (denegar) y no se permitiría que el tráfico pasara al servidor. La regla 6 (denegar) impide que la subred front-end se comunique con la subred back-end (excepto el tráfico permitido en las reglas 1 y 4) y esto protege la red back-end en el caso de que un atacante ponga en peligro la aplicación web en el front-end, y el atacante tendría acceso limitado a la red back-end "protegida" (solo a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario; esto se verá en el "Ejemplo 3" que aparece a continuación.

#### Descripción de reglas de firewall
En el firewall, deberán crearse reglas de reenvío. Como en este ejemplo solo se enruta el tráfico entrante de Internet al firewall y después al servidor web, únicamente se necesita un regla NAT de reenvío.

La regla de reenvío aceptará cualquier dirección de origen entrante que alcance el firewall al intentar llegar a HTTP (puerto 80 o 443 para HTTPS), se enviará fuera de la interfaz local del firewall y se redirigirá al servidor web con la dirección IP de 10.0.1.5.

#### Conclusión
Se trata de una manera relativamente sencilla de proteger la aplicación con un firewall y de aislar la subred back-end del tráfico entrante. Más información sobre este ejemplo a continuación:

- Creación de este ejemplo de red perimetral con scripts de PowerShell
- Creación de este ejemplo con una plantilla ARM
- Descripciones detalladas de cada comando de grupo de seguridad de red y regla de firewall
- Escenarios de flujo de tráfico detallados, que muestran cómo se permite o deniega el tráfico en cada nivel

Se puede encontrar en la página de [instrucciones detalladas de compilación][Example2].

### Ejemplo 3: Creación de una red perimetral para proteger las redes con un firewall, enrutamiento definido por el usuario y grupo de seguridad de red
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example3]

![Red perimetral bidireccional con un dispositivo de red virtual, grupos de seguridad de red y enrutamiento definido por el usuario][9]

#### Configuración del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Tres servicios en la nube: “SecSvc001”, “FrontEnd001” y “BackEnd001”
- Una red virtual, “CorpNetwork”, con tres subredes, “SecNet”, “FrontEnd” y “BackEnd”
- Un dispositivo virtual de red, en este ejemplo, un firewall, conectado a la subred SecNet
- Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores Windows que representan servidores back-end de aplicaciones (“AppVM01”, “AppVM02”)
- Un servidor Windows que representa un servidor DNS ("DNS01")

Hay más detalles para crear este ejemplo (proporciona ambos scripts y una plantilla ARM) que se encuentran en la página de [instrucciones de compilación detalladas][Example3].

#### Descripción del enrutamiento definido por el usuario (UDR)
De forma predeterminada, las siguientes rutas de sistema se definen como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal es siempre el prefijo de dirección definido de la red virtual para esa red específica (es decir, cambiará de una red virtual a otra según cómo se defina cada red virtual específica). Las rutas de sistema restantes son estáticas y tienen el valor predeterminado indicado anteriormente.

En este ejemplo, se crean dos tablas de enrutamiento para las subredes front-end y back-end. Cada tabla se carga con rutas estáticas adecuadas para la subred indicada. Para este ejemplo, cada tabla tiene tres rutas que dirigen todo el tráfico (0.0.0.0/0) a través del firewall (salto siguiente = dirección IP del dispositivo virtual):

1. Tráfico de subred local sin próximo salto definido para que el tráfico de la subred local pase por alto el firewall.
2. Tráfico de red virtual con un próximo salto definido como firewall; esto invalida la regla predeterminada que permite que el tráfico de la red virtual local se enrute directamente.
3. Todo el tráfico restante (0/0) con un próximo salto definido como firewall.

Una vez creadas las tablas de enrutamiento se enlazan a sus subredes. Una vez creada la tabla de enrutamiento de la subred front-end y enlazada a la subred, debe tener el siguiente aspecto:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] Hay determinadas restricciones en el uso del enrutamiento definido por el usuario (UDR) con ExpressRoute debido a la complejidad del enrutamiento dinámico que se usa en la puerta de enlace virtual de Azure. Son las siguientes:
>
> 1. El enrutamiento definido por el usuario no se debe aplicar a la subred de la puerta de enlace en la que está conectada la puerta de enlace virtual de Azure vinculada a ExpressRoute.
> 2. La puerta de enlace virtual de Azure vinculada a ExpressRoute no puede ser el dispositivo NextHop para otras subredes enlazadas con enrutamiento definido por el usuario.
>
>La capacidad para integrar completamente el enrutamiento definido por el usuario y ExpressRoute se habilitará en una futura versión de Azure; a continuación, se describen ejemplos de cómo habilitar la red perimetral con ExpressRoute o redes de sitio a sitio en los ejemplos 3 y 4.


#### Descripción de reenvío IP
Una característica complementaria del enrutamiento definido por el usuario es el reenvío IP. Es una opción de configuración de los dispositivos virtuales que les permite recibir tráfico no dirigido específicamente al dispositivo y, después, reenviar ese tráfico a su destino final.

Por ejemplo, si el tráfico de AppVM01 realiza una solicitud al servidor DNS01, el enrutamiento definido por el usuario los enrutará al firewall. Con el reenvío IP habilitado, el dispositivo (10.0.0.4) aceptará el tráfico dirigido a DNS01 (10.0.2.4) y, a continuación, se reenviará a su destino final (10.0.2.4). Sin el reenvío IP habilitado en el firewall, el dispositivo no aceptará el tráfico aunque la tabla de enrutamiento tenga el firewall como próximo salto. Para usar un dispositivo virtual, es fundamental acordarse de habilitar el reenvío IP junto con el enrutamiento definido por el usuario.

#### Descripción de un grupo de seguridad de red (NSG)
En este ejemplo, se crea un grupo de seguridad de red y se carga después una única regla. Este grupo se enlaza solo a las subredes front-end y back-end (no el SecNet). Mediante declaración se genera la siguiente regla:

1.	Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (todas las subredes)

Aunque en este ejemplo se usan grupos de seguridad de red, su principal objetivo es constituir un segundo nivel de defensa frente a errores de configuración manual. Deseamos bloquear todo el tráfico entrante de Internet a las subredes front-end o back-end, el tráfico solo debe fluir a través de la subred SecNet al firewall (y, si es necesario, a las subredes front-end o back-end). Además, con las reglas de enrutamiento definido por el usuario vigentes, el tráfico que llegue a las subredes front-end o back-end se redirigirá al firewall (gracias al enrutamiento definido por el usuario). El firewall lo consideraría un flujo asimétrico y descartaría el tráfico saliente. Por lo tanto, hay tres niveles de seguridad que protegen las subredes front-end y back-end: (1) ningún extremo abierto en los servicios en la nube FrontEnd001 y BackEnd001; (2) los grupos de seguridad de red deniegan el tráfico de Internet; (3) el firewall descarta el tráfico asimétrico.

Un punto interesante sobre el grupo de seguridad de red de este ejemplo es que contiene solo una regla, que consiste en denegar el tráfico de Internet a toda la red virtual, lo que incluiría la subred de seguridad. Sin embargo, como el grupo de seguridad de red solo está enlazado a las subredes front-end y back-end, la regla no se procesa en el tráfico entrante a la subred de seguridad. Como resultado, aunque la regla de grupo de seguridad de red deniegue el tráfico de Internet a cualquier dirección de la red virtual, como el grupo de seguridad de red nunca estuvo enlazado a la subred de seguridad, el tráfico se dirigirá a la subred de seguridad.

#### Reglas de firewall
En el firewall, deberán crearse reglas de reenvío. Dado que el firewall bloquea o reenvía todo el tráfico entrante, saliente y entre redes virtuales, se necesitan muchas reglas de firewall. Además, todo el tráfico entrante llegará a la dirección IP pública del servicio de seguridad (en puertos diferentes) para ser procesado por el firewall. El procedimiento recomendado es crear un diagrama de los flujos lógicos antes de configurar las subredes y las reglas de firewall para evitar tener que modificar más adelante. La siguiente ilustración es una vista lógica de las reglas de firewall para este ejemplo:
 
![Vista lógica de las reglas de firewall][10]

>[AZURE.NOTE] Según el dispositivo virtual de red usado, los puertos de administración varían. En este ejemplo se hace referencia a un Barracuda NextGen Firewall que utiliza los puertos 22, 801 y 807. Consulte la documentación del proveedor del dispositivo para buscar los puertos exactos usados para la administración del dispositivo que se va a usar.

#### Descripción de reglas de firewall
En el diagrama lógico anterior, no se muestra la subred de seguridad debido a que el firewall es el único recurso de la subred y este diagrama muestra las reglas de firewall y cómo permiten o deniegan lógicamente los flujos de tráfico, no la ruta enrutada real. Además, los puertos externos seleccionados para el tráfico RDP están en un intervalo más alto (8014 – 8026) y se han seleccionado para que se correspondan en cierto modo con los dos últimos octetos de la dirección IP local y facilitar así su legibilidad (por ejemplo, la dirección de servidor local 10.0.1.4 está asociada al puerto externo 8014); sin embargo, podría usarse cualquier puerto superior que no planteara conflictos.

En este ejemplo, necesitamos siete tipos de reglas que se describen a continuación:

- Reglas externas (para el tráfico entrante):
  1.	Regla de administración de firewall: esta regla de redirección de aplicación permite que el tráfico pase a los puertos de administración del dispositivo virtual de red.
  2.	Reglas de RDP (para cada servidor Windows): estas cuatro reglas (una para cada servidor) permitirán la administración de los servidores individuales a través de RDP. También se puede agrupar en una regla según las capacidades del dispositivo virtual de red que se use.
  3.	Reglas de tráfico de aplicación: hay dos reglas de tráfico de aplicación, la primera para el tráfico web front-end y la segunda para el tráfico back-end (p. ej. servidor web a capa de datos). La configuración de estas reglas dependerá de la arquitectura de red (donde están situados los servidores) y los flujos de tráfico (en qué dirección fluye el tráfico y qué puertos se usan).
      - La primera regla permitirá que el tráfico de aplicación real llegue al servidor de aplicaciones. Mientras que las demás reglas permiten seguridad, administración, etc., las reglas de aplicación son las que permiten a los usuarios o servicios externos obtener acceso a las aplicaciones. En este ejemplo, hay un único servidor web en el puerto 80, por lo tanto, una única regla de aplicación de firewall redirigirá el tráfico entrante a la dirección IP externa y a la dirección IP interna de los servidores web. La dirección de la sesión de tráfico redirigido se traduciría al servidor interno.
      - La segunda regla de tráfico de aplicación es la regla back-end que permite que el servidor web se comunique con el servidor AppVM01 (pero no AppVM02) a través de cualquier puerto.
- Reglas internas (para el tráfico entre redes virtuales)
  4.	Regla de saliente a Internet: esta regla permitirá que el tráfico de cualquier red pase a las redes seleccionadas. Normalmente, esta es una regla predeterminada que ya existe en el firewall, pero en estado deshabilitado. Esta regla debe habilitarse para este ejemplo.
  5.	Regla DNS: esta regla permite que pase solo el tráfico DNS (puerto 53) al servidor DNS. En este entorno, se bloquea la mayor parte del tráfico desde la subred front-end a la subred back-end. Esta regla permite específicamente DNS desde cualquier subred local.
  6.	Regla de subred a subred: esta regla permite que cualquier servidor en la subred back-end conecte con cualquier servidor en la subred front-end (pero no a la inversa).
- Regla para notificaciones de error (para el tráfico que no cumple ninguna de las anteriores):
  7.	Denegar todas las reglas de tráfico: esta debe ser siempre la última regla (en términos de prioridad) y, como tal, si un flujo de tráfico no coincide con ninguna de las reglas anteriores, esta regla lo descartará. Esta es una regla predeterminada y normalmente está activada; normalmente no se necesitan modificaciones.

>[AZURE.TIP] En la segunda regla de tráfico de aplicación, se permite cualquier puerto para facilitar el ejemplo; en un escenario real, se usarían el puerto más específico e intervalos de direcciones para reducir la superficie de ataque de esta regla.

Una vez creadas todas las reglas anteriores, es importante revisar la prioridad de cada una para asegurarse de que el tráfico se permitirá o se denegará según se desee. En este ejemplo, las reglas están en orden de prioridad.

#### Conclusión
Se trata de una manera más compleja pero más completa de protección y aislamiento de la red (en el ejemplo 2 se protegía solamente la aplicación y en el ejemplo 1 solo se aislaban subredes). Este diseño permite supervisar el tráfico en ambas direcciones y protege no solo el servidor de aplicaciones de entrada sino que aplica la directiva de seguridad de red a todos los servidores de esta red. Además, según el dispositivo que se usa, se pueden lograr el reconocimiento y la auditoría de todo el tráfico. Más información sobre este ejemplo a continuación:

- Creación de este ejemplo de red perimetral con scripts de PowerShell
- Creación de este ejemplo con una plantilla ARM
- Descripciones detalladas de cada enrutamiento definido por el usuario, comando de grupo de seguridad de red y regla de firewall
- Escenarios de flujo de tráfico detallados, que muestran cómo se permite o deniega el tráfico en cada nivel

Se puede encontrar en la página de [instrucciones detalladas de compilación][Example3].

### Ejemplo 4: Incorporación de una conexión híbrida con una VPN de dispositivo virtual de sitio a sitio
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

![Red perimetral con red híbrida conectada a un dispositivo virtual de red][11]

#### Configuración del entorno
Las redes híbridas que usan un dispositivo virtual de red (NVA) pueden agregarse a cualquiera de los tipos de red perimetral descritos en el ejemplo 1, 2 o 3.

Como se muestra en la ilustración anterior, se usa una conexión VPN a través de Internet (sitio a sitio) para conectar una red local a una red virtual de Azure a través de un dispositivo virtual de red.

>[AZURE.NOTE] Si usa ExpressRoute con la opción de configuración entre pares públicos de Azure habilitada, se debe crear una ruta estática para enrutar la dirección IP de VPN del dispositivo virtual de red con el borde de Internet corporativo y no con el borde WAN de ExpressRoute. Esto es debido a la NAT requerida en la opción de configuración entre pares públicos de ExpressRoute de Azure que probablemente interrumpirá la sesión de VPN (al IPSec normalmente no le gustan las NAT).

Una vez implementada la conexión VPN, el dispositivo virtual de red se convierte en el "concentrador" central para todas las redes y subredes. Las reglas de reenvío de firewall determinan qué flujos de tráfico se permiten, se traducen, redirigen o descartan (incluso para los flujos de tráfico entre la red local y Azure si los flujos están diseñados de este modo).

Los flujos de tráfico deben tenerse en cuenta cuidadosamente ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Mediante el entorno creado en el ejemplo 3, "Creación de una red perimetral para proteger las redes con un firewall, enrutamiento definido por el usuario y grupo de seguridad de red" y la incorporación posterior de una conexión de red híbrida VPN de sitio a sitio, se produciría el siguiente diseño:

![Red perimetral con un dispositivo virtual de red conectada mediante una VPN de sitio a sitio][12]

En el diseño anterior, el enrutador local, o cualquier otro dispositivo de red que sea compatible con el dispositivo virtual de red para VPN, sería el cliente VPN. Este dispositivo físico sería responsable de iniciar y mantener la conexión VPN con el dispositivo virtual de red.

Según la lógica del dispositivo virtual de red, la red parece estar constituida por cuatro "zonas de seguridad" independientes con las reglas del dispositivo virtual de red como director principal del tráfico entre estas zonas. Esto aparecería lógicamente como:

![Red lógica desde la perspectiva del dispositivo virtual de red][13]

#### Conclusión
La incorporación de una conexión de red híbrida VPN de sitio a sitio a una red virtual de Azure puede ampliar la red local a Azure de forma segura. Al usar una conexión VPN, el tráfico se cifra y se enruta a través de Internet. Mediante el dispositivo virtual de red, tal como se hizo en este ejemplo, se proporciona una ubicación central para aplicar y administrar la directiva de seguridad. Más información sobre este ejemplo a continuación:

- Creación de este ejemplo de red perimetral con scripts de PowerShell
- Creación de este ejemplo con una plantilla ARM
- Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño

Pronto estarán disponible y enlazados desde esta página.

### Ejemplo 5: Incorporación de una conexión híbrida con una VPN de puerta de enlace de Azure de sitio a sitio
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

![Red perimetral con red híbrida conectada a puerta de enlace][14]

#### Configuración del entorno
Las redes híbridas que usan una puerta de enlace de VPN de Azure se pueden agregar a cualquier tipo de red perimetral descrito en los ejemplos 1 y 2.

Como se muestra en la ilustración anterior, se usa una conexión VPN a través de Internet (sitio a sitio) para conectar una red local a una red virtual de Azure a través de una puerta de enlace de VPN de Azure.

>[AZURE.NOTE] Si usa ExpressRoute con la opción de configuración entre pares públicos de Azure habilitada, se debe crear una ruta estática para enrutar a la dirección IP de puerta de enlace de VPN de Azure con el borde de Internet corporativo y no con el borde WAN de ExpressRoute. Esto es debido a la NAT requerida en la opción de configuración entre pares públicos de ExpressRoute de Azure que probablemente interrumpirá la sesión de VPN (al IPSec normalmente no le gustan las NAT).

Como se muestra a continuación, con esta opción el entorno tiene ahora dos extremos de red. En el primer borde, el dispositivo virtual de red y el grupo de seguridad de red controlan los flujos de tráfico para redes internas de Azure y entre Azure e Internet, mientras que el segundo borde es la puerta de enlace de VPN de Azure, que es un borde de red totalmente independiente y aislado entre local y Azure.

Los flujos de tráfico deben tenerse en cuenta cuidadosamente ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Mediante el entorno creado en el ejemplo 1, "Creación de una red perimetral para proteger las aplicaciones con grupos de seguridad de red" y la incorporación posterior de una conexión de red híbrida de VPN de sitio a sitio, se produciría el siguiente diseño:

![Red perimetral con puerta de enlace conectada mediante una conexión de ExpressRoute][15]

#### Conclusión
La incorporación de una conexión de red híbrida VPN de sitio a sitio a una red virtual de Azure puede ampliar la red local a Azure de forma segura. Mediante una puerta de enlace nativa de VPN de Azure, el tráfico está cifrado con IPSec y se enruta a través de Internet. Asimismo, mediante la puerta de enlace de VPN de Azure, se proporciona una opción menos costosa (sin costes de licencias adicionales como ocurre con dispositivos virtuales de red de terceros). Donde resulta más económico es en el ejemplo 1, en el que no se usa ningún dispositivo virtual de red. Más información sobre este ejemplo a continuación:

- Creación de este ejemplo de red perimetral con scripts de PowerShell
- Creación de este ejemplo con una plantilla ARM
- Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño

Pronto estarán disponible y enlazados desde esta página.

### Ejemplo 6: Incorporación de una conexión híbrida con ExpressRoute
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

![Red perimetral con red híbrida conectada a puerta de enlace][16]

#### Configuración del entorno
Las redes híbridas que usan una conexión de configuración entre pares privados de ExpressRoute las redes híbridas se pueden agregar a cualquier tipo de red perimetral descrito en el ejemplo 1 o 2.

Como se muestra en la ilustración anterior, la configuración entre pares privados de ExpressRoute proporciona una conexión directa entre la red local y la red virtual de Azure. El tráfico transita solo por la red del proveedor de servicio y la red de Microsoft Azure y no toca nunca Internet.

>[AZURE.NOTE] Hay determinadas restricciones en el uso del enrutamiento definido por el usuario (UDR) con ExpressRoute debido a la complejidad del enrutamiento dinámico que se usa en la puerta de enlace virtual de Azure. Son las siguientes:
>
> 1. El enrutamiento definido por el usuario no se debe aplicar a la subred de la puerta de enlace en la que está conectada la puerta de enlace virtual de Azure vinculada a ExpressRoute.
> 2. La puerta de enlace virtual de Azure vinculada a ExpressRoute no puede ser el dispositivo NextHop para otras subredes enlazadas con enrutamiento definido por el usuario.
>
>La capacidad de integrar totalmente el enrutamiento definido por el usuario y ExpressRoute se habilitará en una futura versión de Azure.

<br />

>[AZURE.TIP] Mediante ExpressRoute, se mantiene el tráfico de la red corporativa fuera de Internet para una mayor seguridad, se aumenta significativamente el rendimiento y se permiten los contratos de nivel de servicio del proveedor de ExpressRoute. En cuanto al rendimiento de ExpressRoute, la puerta de enlace de Azure puede pasar a 2 Gbps con ExpressRoute, mientras que con la puerta de enlace de Azure de VPN de sitio a sitio, el rendimiento máximo es de 200 Mbps.

Tal como se ve en el diagrama siguiente, con esta opción el entorno tiene ahora dos bordes de red, el dispositivo virtual de red y el grupo de seguridad de red controlan los flujos de tráfico para redes internas de Azure y entre Azure e Internet, mientras que la puerta de enlace de VPN de Azure es un borde de red totalmente independiente y aislado entre local y Azure.

Los flujos de tráfico deben tenerse en cuenta cuidadosamente ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Mediante el entorno creado en el ejemplo 1, "Creación de una red perimetral simple con grupos de seguridad de red" y la incorporación posterior de una conexión de red híbrida de ExpressRoute, se produciría el siguiente diseño:

![Red perimetral con puerta de enlace conectada mediante una conexión de ExpressRoute][17]

#### Conclusión
La incorporación de una conexión de red de configuración entre pares privados de ExpressRoute puede ampliar la red local a Azure de un modo seguro, con una latencia inferior y con un rendimiento mayor. Asimismo, mediante la puerta de enlace nativa de Azure, tal como se hizo en este ejemplo, se proporciona una opción menos costosa (sin necesidad de licencias adicionales como ocurre con dispositivos virtuales de red de terceros). Más información sobre este ejemplo a continuación:

- Creación de este ejemplo de red perimetral con scripts de PowerShell
- Creación de este ejemplo con una plantilla ARM
- Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño

Pronto estarán disponible y enlazados desde esta página.

## Referencias
### Documentación y sitios web útiles
- Acceso a Azure con ARM: 
- Acceso a Azure con PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentación de red virtual: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentación de grupo de seguridad de red: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentación de enrutamiento definido por el usuario: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Puertas de enlace virtuales de Azure: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- VPN de sitio a sitio: [https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/](./virtual-network/vpn-gateway-site-to-site-create.md)
- Documentación de ExpressRoute (asegúrese de consultar las secciones "Introducción" y "Procedimientos"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Diagrama de flujo de opciones de seguridad"
[1]: ./media/best-practices-network-security/compliancebadges.png "Notificaciones de cumplimiento de Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Características de seguridad de Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Red perimetral en una red corporativa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitectura de seguridad de Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Red perimetral en una red virtual de Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Red híbrida con tres límites de seguridad"
[7]: ./media/best-practices-network-security/example1design.png "Red perimetral de entrada con grupo de seguridad de red"
[8]: ./media/best-practices-network-security/example2design.png "Red perimetral de entrada con dispositivo virtual de red y grupo de seguridad de red"
[9]: ./media/best-practices-network-security/example3design.png "Red perimetral bidireccional con un dispositivo de red virtual, grupos de seguridad de red y enrutamiento definido por el usuario"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Vista lógica de las reglas de firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "Red perimetral con red híbrida conectada a un dispositivo virtual de red"
[12]: ./media/best-practices-network-security/example4designs2s.png "Red perimetral con un dispositivo virtual de red conectada mediante una VPN de sitio a sitio"
[13]: ./media/best-practices-network-security/example4networklogical.png "Red lógica desde la perspectiva del dispositivo virtual de red"
[14]: ./media/best-practices-network-security/example5designoptions.png "Red perimetral con puerta de enlace de Azure conectada a red híbrida de sitio a sitio"
[15]: ./media/best-practices-network-security/example5designs2s.png "Red perimetral con puerta de enlace de Azure mediante VPN de sitio a sitio"
[16]: ./media/best-practices-network-security/example6designoptions.png "Red perimetral con puerta de enlace de Azure conectada a red híbrida de ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Red perimetral con puerta de enlace de Azure mediante una conexión de ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

<!---HONumber=AcomDC_0204_2016-->