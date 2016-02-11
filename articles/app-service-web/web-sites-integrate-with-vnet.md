<properties 
	pageTitle="Integración de una aplicación con una red virtual de Azure" 
	description="Muestra cómo conectar una aplicación del Servicio de aplicaciones de Azure a una red virtual de Azure nueva o existente" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="ccompy"/>

# Integración de su aplicación con una red virtual de Azure #

En este documento, se describe la característica Integración con redes virtuales del Servicio de aplicaciones de Azure y se muestra cómo configurarla con aplicaciones en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Si no conoce las redes virtuales de Azure, se trata de una funcionalidad que le permite colocar muchos de sus recursos de Azure en una red no enrutable sin conexión a Internet cuyo acceso controla. Después, estas redes se pueden conectar a sus redes locales mediante diversas tecnologías de VPN. Para aprender más acerca de las redes virtuales de Azure, empiece aquí: [Información general sobre redes virtuales][VNETOverview].

El Servicio de aplicaciones de Azure adopta dos formas. La primera, y la más habitual, es la de aquellos sistemas de varios inquilinos que admiten la gama completa de planes de precios. La segunda es la característica premium Entorno del Servicio de aplicaciones (ASE), que se implementa en la red virtual de un cliente. Con un entorno del Servicio de aplicaciones, normalmente no necesitará usar Integración con redes virtuales, porque el sistema ya está en la red virtual y tiene acceso a todos los recursos de esa red virtual. La única razón por la que aún usaría la característica Integración con redes virtuales con un ASE es si desea acceder a recursos de otra red virtual que no esté conectada a la red virtual que hospeda su ASE.

Integración con redes virtuales ofrece a su aplicación web acceso a los recursos de su red virtual, pero no concede acceso privado a su aplicación web desde la red virtual. Un escenario habitual en el que usaría esta característica es para permitir que su aplicación web acceda a una base de datos o a servicios web que se ejecutan en una máquina virtual de la red virtual de Azure. Con Integración con redes virtuales, no es necesario exponer un punto de conexión público para las aplicaciones en su máquina virtual, sino que puede usar las direcciones privadas no enrutables sin conexión a Internet en su lugar.

La característica Integración con redes virtuales:

- requiere un plan de precios Premium o Estándar; 
- actualmente solo funciona con redes virtuales V1 o clásicas; 
- es compatible con TCP y UDP;
- funciona con aplicaciones web, móviles y de API;
- permite que una aplicación se conecte solo a una red virtual a la vez;
- permite la integración con hasta 5 redes virtuales dentro de un plan de servicio de aplicaciones; 
- permite que varias aplicaciones de un plan de servicio de aplicaciones usen la misma red virtual;
- admite un SLA del 99,9% debido a la dependencia de la puerta de enlace de red virtual.

Hay algunos aspectos que Integración con redes virtuales no admite, como:

- montar una unidad;
- la integración de AD; 
- NetBios;
- el acceso privado a sitios.

### Introducción ###
Le recordamos algunos aspectos que debe tener en cuenta antes de conectar su aplicación web a una red virtual:

- Integración con redes virtuales solo funciona con aplicaciones en un plan de precios **Estándar** o **Premium**. Si habilita la característica y después escala su plan de servicio de aplicaciones a un plan de precios no admitido, las aplicaciones perderán la conexión a las redes virtuales que estén usando.  
- Si su red virtual de destino ya existe, debe tener habilitada la VPN de punto a sitio con una puerta de enlace de enrutamiento dinámico para que se pueda conectar a una aplicación. No puede habilitar una red privada virtual (VPN) de punto a sitio si la puerta de enlace está configurada con enrutamiento estático.
- La red virtual debe compartir la misma suscripción que su plan de servicio de aplicaciones (ASP).  
- Las aplicaciones que se integran con una red virtual usarán el DNS que se especifique para esa red virtual.
- De forma predeterminada, las aplicaciones que se integran solo enrutarán el tráfico hacia la red virtual según las rutas definidas en la red virtual.  


## Habilitación de Integración con redes virtuales ##

Puede conectarse a una red virtual nueva o existente. Si crea una nueva red, además de crearse la red virtual, se preconfigurará de forma automática una puerta de enlace de enrutamiento dinámico y se habilitará la VPN de punto a sitio.

>[AZURE.NOTE]Se puede tardar unos minutos en configurar una nueva integración con redes virtuales.

Para habilitar Integración con redes virtuales, abra la configuración de la aplicación y después seleccione Redes. La interfaz de usuario que se abre ofrece tres opciones de redes. En esta guía, solo se va a tratar Integración con redes virtuales, aunque más adelante en este documento se explicarán Conexiones híbridas y Entornos del Servicio de aplicaciones.

Si la aplicación no pertenece al plan de precios correcto, la interfaz de usuario le permitirá escalarlo a otro superior de su elección.


![][1]
 
###Habilitación de Integración con redes virtuales con una red virtual existente###
La interfaz de usuario de Integración con redes virtuales permite seleccionar en una lista de redes virtuales V1. En la imagen siguiente, se ve que solo se puede seleccionar una red virtual. Existen varias razones por las que una red virtual estará atenuada, entre otras:

- la red virtual pertenece a otra suscripción a la que su cuenta tiene acceso;
- la red virtual no tiene la conectividad de punto a sitio habilitada;
- la red virtual carece de puerta de enlace de enrutamiento dinámico.

También merece la pena mencionar que, puesto que aún no se admite la integración con redes virtuales V2, estas no aparecen en la lista.

![][2]

Para habilitar la integración, basta con hacer clic en la red virtual con la que se desee integrar la aplicación. Después de seleccionar la red virtual, la aplicación se reiniciará automáticamente para que los cambios surtan efecto.

Si la red virtual carece de puerta de enlace o de conectividad de punto a sitio, primero debe configurarlas. Para hacerlo, vaya al Portal de Azure y muestre la lista de redes virtuales (clásicas). Desde aquí, haga clic en la red con la que desee integrar la aplicación y haga clic en el cuadro grande bajo Essentials llamado Conexiones VPN. Aquí puede crear la VPN de punto a sitio e incluso hacer que se cree una puerta de enlace. Después de completar la experiencia de creación de la conexión de punto a sitio y la puerta de enlace, pasarán unos 30 minutos hasta que todo esté listo.

![][8]

### Creación de una red virtual e integración con ella ###
Si desea crear una nueva red virtual, tenga en cuenta que actualmente solo podrá crear una red virtual V1 o clásica. Para crear una red virtual V1 mediante la experiencia de interfaz de usuario de Integración con redes virtuales, basta con que seleccione Crear una nueva red virtual y proporcione después el nombre que desee para la red virtual y su espacio de direcciones.

Tenga en cuenta que, si desea que esta red virtual se conecte a cualquiera de sus otras redes, no debe elegir un espacio de direcciones IP que se solape con esas redes.

>[AZURE.NOTE]La nueva red virtual puede tardar hasta 30 minutos en estar lista, incluidas las puertas de enlace operativas. La interfaz de usuario se actualizará cuando se complete.

![][3]

Las redes virtuales de Azure normalmente se crean dentro de direcciones de red privadas. De forma predeterminada, la característica Integración con redes virtuales enrutará el tráfico destinado a esos intervalos de direcciones IP hacia su red virtual. Los intervalos de direcciones IP privadas son:

- 10\.0.0.0/8: es igual que 10.0.0.0 - 10.255.255.255.
- 172\.16.0.0/12: es igual que 172.16.0.0 - 172.31.255.255. 
- 192\.168.0.0/16: es igual que 192.168.0.0 - 192.168.255.255.
 
El espacio de direcciones de red virtual debe especificarse con la notación CIDR. Si desconoce la notación CIDR, es un método para especificar bloques de direcciones con una dirección IP y un entero que representa la máscara de red. Como referencia rápida, tenga en cuenta que 10.1.0.0/24 corresponde a 256 direcciones y 10.1.0.0/25, a 128. Una dirección IPv4 con un /32 sería simplemente una dirección.

Si establece aquí la información del servidor DNS, quedará establecida para la red virtual. Después de crearse la red virtual, puede editar esta información desde las experiencias de usuario de red virtual.

Cuando se crea una red virtual V1 mediante la interfaz de usuario de Integración con redes virtuales, se creará una red virtual en el mismo grupo de recursos que la aplicación.

## Funcionamiento del sistema ##
Esta característica se basa en la tecnología de VPN de punto a sitio para conectar la aplicación a la red virtual. Las aplicaciones del Servicio de aplicaciones de Azure tienen una arquitectura de sistema de varios inquilinos que impide el aprovisionamiento de una aplicación directamente en una red virtual como se hace con las máquinas virtuales. Al basarse en tecnología de punto a sitio, se limita el acceso a la red a únicamente la máquina virtual que hospeda la aplicación. El acceso a la red se restringe aún más en esos hosts de aplicaciones para que sus aplicaciones solo puedan acceder a las redes que se configuren para ese fin.

![][4]
 
Si no ha configurado un servidor DNS con su red virtual, necesitará usar direcciones IP. Cuando use direcciones IP, recuerde que la principal ventaja de esta característica es que le permite usar las direcciones privadas dentro de la red privada. Si ha configurado la aplicación para que use direcciones IP públicas para una de las máquinas virtuales, no está usando la característica Integración con redes virtuales, sino que se está comunicando a través de Internet.



##Administración de las integraciones con redes virtuales##

La capacidad de conectarse a una red virtual y desconectarse de ella se encuentra en un nivel de aplicación. Las operaciones que pueden afectar a la integración con redes virtuales en varias aplicaciones se encuentran en un nivel de plan de servicio de aplicaciones. Desde la interfaz de usuario que se muestra en el nivel de aplicación, puede obtener detalles sobre la red virtual. Gran parte de esta misma información también se muestra en el nivel de plan de servicio de aplicaciones.

![][5]

En la página Estado de característica de red, puede ver si la aplicación está conectada a la red virtual. Si la puerta de enlace de red virtual no está disponible por cualquier razón, aparecerá como no conectada.

La información que tiene ahora disponible en la interfaz de usuario de Integración con redes virtuales en el nivel de aplicación es igual que la información detallada que se obtiene desde el plan de servicio de aplicaciones. Esto es lo que cubre:

- Nombre de red virtual: este vínculo abre la interfaz de usuario de red.
- Ubicación: esto refleja la ubicación de la red virtual. Es posible realizar la integración con una red virtual en otra ubicación.
- Estado de certificado: hay certificados que se usan para proteger la conexión VPN entre la aplicación y la red virtual. Esto refleja una prueba para asegurarse de que estén sincronizados.
- Estado de la puerta de enlace: si las puertas de enlace estuvieran inactivas por algún motivo, la aplicación no podrá acceder a los recursos de la red virtual.  
- Espacio de direcciones de red virtual: se trata del espacio de direcciones IP para la red virtual.  
- Espacio de direcciones de punto a sitio: es el espacio de direcciones IP de punto a sitio para la red virtual. La aplicación mostrará la comunicación como procedente de una de las direcciones IP de este espacio de direcciones.  
- Espacio de direcciones de sitio a sitio: puede usar VPN de sitio a sitio para conectar la red virtual a sus recursos locales o a otras redes virtuales. Si lo tiene configurado, aquí se mostrarán los intervalos de direcciones IP definidos para esa conexión VPN.
- Servidores DNS: si tiene servidores DNS configurados con la red virtual, aparecen aquí.
- Direcciones IP enrutadas a la red virtual: una lista de direcciones IP para las que la red virtual tiene definido el enrutamiento. Esas direcciones se mostrarán aquí.  

La única operación que puede realizar en la vista de aplicación de su integración con redes virtuales es desconectar la aplicación de la red virtual si está conectada a ella. Para hacerlo, basta con hacer clic en Desconectar arriba. Esta acción no cambia la red virtual. La red virtual y su configuración, incluidas las puertas de enlace, permanecen intactas. Si después desea eliminar la red virtual, debe eliminar primero los recursos en ella, incluidas las puertas de enlace.

La vista del plan de servicio de aplicaciones ofrece varias operaciones adicionales. Además, también se accede a ella de forma diferente desde la aplicación. Para llegar a la interfaz de usuario de Redes del plan de servicio de aplicaciones, abra la interfaz de usuario del plan de servicio de aplicaciones y desplácese hacia abajo. Hay un elemento de interfaz de usuario llamado Estado de característica de red. Ofrece varios detalles menores sobre su integración con redes virtuales. Si hace clic en este elemento, se abre la interfaz de usuario de Estado de característica de red. Si después hace clic en "Haga clic aquí para administrar", se abrirá la interfaz de usuario que enumera las integraciones con redes virtuales en este plan de servicio de aplicaciones.

![][6]

Está bien recordar la ubicación del plan de servicio de aplicaciones cuando se observan las ubicaciones de las redes virtuales con las que va a realizar la integración. Cuando la red virtual está en otra ubicación, es mucho más probable que se experimenten problemas de latencia.

El campo Redes virtuales con las que están integradas es un recordatorio de con cuántas redes virtuales están integradas sus aplicaciones en este plan de servicio de aplicaciones y cuántas puede tener.

Para ver más detalles sobre cada red virtual, haga clic en la red virtual que le interese. Además de los detalles que se indicaron antes, también verá una lista de las aplicaciones de este plan de servicio de aplicaciones que usan esa red virtual.

Con respecto a las acciones, existen dos principales. La primera es la capacidad de agregar rutas que controlan el tráfico que sale de la aplicación hacia la red virtual. La segunda acción es la capacidad de sincronizar los certificados y la información de la red.

![][7]

**Enrutamiento** Como se indicó antes, las rutas definidas en la red virtual son lo que se usa para dirigir el tráfico desde la aplicación hacia la red virtual. Sin embargo, en algunos casos, los clientes desean enviar más tráfico saliente desde una aplicación hacia la red virtual y para ellos se proporciona esta capacidad. Lo que ocurra con el tráfico después de eso depende de cómo el cliente configure su red virtual.

**Certificados** El campo Estado de certificado refleja que el Servicio de aplicaciones está llevando a cabo una comprobación para confirmar que los certificados que se estén usando para la conexión VPN sigan siendo válidos. Cuando se habilita Integración con redes virtuales, si es la primera integración con dicha red virtual desde las aplicaciones de este plan de servicio de aplicaciones, se produce un intercambio obligatorio de certificados para garantizar la seguridad de la conexión. Junto con los certificados, se obtienen la configuración de DNS, las rutas y otros elementos similares que describen la red. Si cambian los certificados o la información de red, será necesario hacer clic en "Sincronizar red". **NOTA**: al hacer clic en "Sincronizar red", se producirá una breve interrupción en la conectividad entre la aplicación y la red virtual. Aunque no se reiniciará la aplicación, la pérdida de conectividad podría hacer que su sitio no funcione correctamente.

##Obtener acceso a recursos locales##

Una de las ventajas de la característica Integración con redes virtuales es que, si la red virtual está conectada a la red local con una VPN de sitio a sitio, las aplicaciones pueden acceder a los recursos locales desde su aplicación. Pero, para que esto funcione, puede que necesite actualizar la puerta de enlace de VPN local con las rutas de su intervalo de direcciones IP de punto a sitio. En la configuración inicial de la VPN de sitio a sitio, los scripts que se usan para configurarla deberían también configurar las rutas, incluida la VPN de punto a sitio. Si agrega la VPN de punto a sitio después de crear la VPN de sitio a sitio, deberá actualizar las rutas manualmente. El procedimiento variará según la puerta de enlace y no se describe aquí.

>[AZURE.NOTE]Aunque la característica Integración con redes virtuales funcionará con una VPN de sitio a sitio para acceder a los recursos locales, actualmente no funcionará con una VPN ExpressRoute para hacer lo mismo.

##Detalles de precios##
Hay algunos matices sobre los precios que se deben tener en cuenta al usar la característica Integración con redes virtuales. El uso de esta característica comporta tres cargos:

- Requisitos del plan de tarifa para el plan de servicio de aplicaciones
- Costos de la transferencia de datos
- Costos de la puerta de enlace de VPN

Para que las aplicaciones puedan usar esta característica, deben pertenecer a un plan de servicio de aplicaciones Premium o Estándar. Puede ver más detalles sobre esos costos aquí: [Precios del Servicio de aplicaciones][ASPricing].

Debido a la forma en que se controlan las VPN de punto a sitio, siempre tiene un cargo por los datos salientes a través de la conexión de Integración con redes virtuales, incluso si la red virtual está en el mismo centro de datos. Para ver cuáles son estos cargos, consulte lo siguiente: [Detalles de precios de Transferencias de datos][DataPricing].

El último elemento es el costo de las puertas de enlace de red virtual. Si no necesita las puertas de enlace para algo diferente, como las VPN de sitio a sitio, va a pagar para que las puertas de enlace admitan la característica Integración con redes virtuales. Los detalles sobre esos costos están aquí: [Precios de Puerta de enlace de VPN][VNETPricing].

##Solución de problemas##

El que una característica sea fácil de configurar no quiere decir que no presente problemas con el uso. Si encuentra problemas para acceder al punto de conexión que desee, existen varias utilidades que sirven para probar la conectividad desde la consola de la aplicación. Puede usar dos experiencias de consola. Una es la consola Kudu y la otra es la consola a la que se accede en el Portal de Azure. Para llegar a la consola Kudu, desde la aplicación, vaya a Herramientas -> Kudu. Esto equivale a ir a [nombreDeSitio].scm.azurewebsites.net. Cuando se abra, simplemente vaya a la pestaña de consola Debug (Depurar). Para llegar a la consola hospedada en el Portal de Azure, desde su aplicación, vaya a Herramientas -> Consola.


####Herramientas####

Las herramientas ping, nslookup y tracert no funcionarán a través de la consola por restricciones de seguridad. Para suplir esta falta, se han agregado dos herramientas independientes. Para probar la funcionalidad de DNS, agregamos una herramienta denominada nameresolver.exe. La sintaxis es:

    nameresolver.exe hostname [optional: DNS Server]

Puede usar nameresolver para comprobar los nombres de host de los que depende la aplicación. De este modo, puede probar si hay algo mal configurado en el DNS o si no tiene acceso al servidor DNS.

La siguiente herramienta permite probar la conectividad de TCP para una combinación de host y puerto. Esta herramienta se llama tcpping.exe y la sintaxis es:

    tcpping.exe hostname [optional: port]

Esta herramienta le indicará si puede establecer contacto con un host y un puerto específicos, pero no llevará a cabo la misma tarea que ofrece la utilidad ping basada en ICMP. La utilidad ping basada ICMP le indicará si el host está activo. Con tcpping, averigua si puede acceder a un puerto específico en un host.


####Depuración del acceso a recursos hospedados en la red virtual####

Hay varias situaciones que pueden impedir que la aplicación llegue a un host y un puerto específicos. Para desglosar las causas del problema, empiece con algunas preguntas fáciles como:

- ¿Aparece la puerta de enlace como activa en el Portal?
- ¿Indican los certificados que están sincronizados?
- ¿Cambió alguien la configuración de la red sin hacer clic en "Sincronizar red" en los planes de servicio de aplicaciones afectados?

Si la puerta de enlace está inactiva, vuelva a activarla. Si los certificados no están sincronizados, vaya a la vista del plan de servicio de aplicaciones en su integración con redes virtuales y haga clic en "Sincronizar red". Si sospecha que se realizó un cambio en la configuración de la red virtual y no se sincronizó con los planes de servicio de aplicaciones, vaya a la vista del plan de servicio de aplicaciones de su integración con redes virtuales y haga clic en "Sincronizar red". Recuerde que esto causará una breve interrupción en la conexión entre la red virtual y sus aplicaciones.

Si todo eso está bien, debe profundizar un poco más:

- ¿Hay otras aplicaciones que estén usando esta red virtual satisfactoriamente para acceder a un recurso remoto? 
- ¿Puede ir a la consola de la aplicación y usar tcpping para llegar a algún recurso de la red virtual?  

Si la respuesta a cualquiera de las anteriores preguntas es afirmativa, la integración con redes virtuales es correcta y el problema reside en otro lugar. También es posible que no tenga respuesta para los dos elementos anteriores porque no haya nada más que alcanzar en la red virtual. Aquí la situación se complica porque no existe una forma sencilla de ver por qué no se llega a un host:puerto. Algunas de las causas son:

- el host de destino está inactivo;
- la aplicación está inactiva;
- la dirección IP o el nombre de host son incorrectos;
- la aplicación escucha en un puerto diferente del que se esperaba. Para comprobarlo, vaya a ese host y use "netstat -aon" en el símbolo del sistema. Esto le mostrará qué identificador de proceso está escuchando en qué puerto;  
- tiene un firewall en el host que impide el acceso al puerto de la aplicación desde su intervalo de direcciones IP de punto a sitio;
- los grupos de seguridad de la red están configurados de tal manera que impiden el acceso al host y al puerto de la aplicación desde su intervalo de direcciones IP de punto a sitio.

Recuerde que no sabe qué dirección IP del intervalo de direcciones IP de punto a sitio usará la aplicación, así que necesita permitir el acceso a todo el intervalo.

Otros pasos de depuración son:

- iniciar sesión en otra máquina virtual de la red virtual e intentar acceder al host:puerto de recursos desde ella. Hay algunas utilidades ping basadas en TCP que sirven para este propósito o incluso puede usar telnet si es necesario. El objetivo es solamente determinar si existe conectividad desde esta otra máquina virtual; 
- abrir una aplicación en otra máquina virtual y probar el acceso a ese host y ese puerto desde la consola de la aplicación.  

####Recursos locales####
Si no puede acceder a los recursos locales, lo primero que debe comprobar es si puede llegar a un recurso de la red virtual. Si eso funciona, los siguientes pasos son bastante sencillos. Debe intentar conectarse a la aplicación local desde una máquina virtual de la red virtual. Puede usar telnet o una utilidad ping basada en TCP. Si la máquina virtual no puede acceder al recurso local, primero asegúrese de que funciona la conexión VPN de sitio a sitio. Si funciona, compruebe lo mismo que se indicó antes, así como el estado y la configuración de la puerta de enlace local.

Ahora bien, si la máquina virtual hospedada en la red virtual puede acceder a su sistema local pero la aplicación no puede, es probable que el motivo sea uno de los siguientes: – Las rutas no están configuradas con los intervalos de direcciones IP de punto a sitio en la puerta de enlace local. – Los grupos de seguridad de la red están bloqueando el acceso del intervalo de direcciones IP de punto a sitio. – Los firewalls locales están bloqueando el tráfico procedente del intervalo de direcciones IP de punto a sitio. – Tiene una ruta definida por el usuario (UDR) en la red virtual que impide que el tráfico basado en la conectividad de punto a sitio llegue a su red local.

## Conexiones híbridas y entornos del Servicio de aplicaciones##
Existen tres características que permiten el acceso a los recursos hospedados en la red virtual. Son las siguientes:

- Integración con redes virtuales
- conexiones híbridas
- Entornos del Servicio de aplicaciones

Conexiones híbridas requiere que se instale un agente de retransmisión llamado administrador de conexiones híbridas (HCM) en la red. El HCM debe ser capaz de conectarse a Azure y también a la aplicación. Esta solución es especialmente atractiva desde una red remota como su red local o incluso otra red hospedada en la nube porque no requiere un punto de conexión accesible por Internet. El HCM solamente se ejecuta en Windows y puede tener hasta 5 instancias en ejecución para proporcionar alta disponibilidad. Sin embargo, Conexiones híbridas solo admite TCP y cada uno de sus puntos de conexión tiene que coincidir con una combinación de host:puerto específica.

La característica Entorno del Servicio de aplicaciones permite ejecutar una instancia del Servicio de aplicaciones de Azure en la red virtual. Esto permite que sus aplicaciones accedan a recursos de la red virtual sin ningún paso adicional. Algunas de las otras ventajas de un entorno del Servicio de aplicaciones es que se pueden usar trabajos dedicados con 8 núcleos y 14 GB de RAM. Otra ventaja es que se puede escalar el sistema para que satisfaga sus necesidades. A diferencia de los entornos con varios inquilinos en los que el plan de servicio de aplicaciones tiene un tamaño limitado, en un entorno del Servicio de aplicaciones puede controlar la cantidad de recursos que desea proporcionar al sistema. Sin embargo, en relación con el enfoque de red de este documento, una de las cosas que ofrece un entorno del Servicio de aplicaciones que no encontrará en Integración con redes virtuales es que funciona con una VPN ExpressRoute.

Aunque algunos casos de uso se solapan, ninguna de estas características puede reemplazar a las otras. Saber qué característica emplear depende de sus necesidades y de cómo desea usarla. Por ejemplo:

- Si es un desarrollador y simplemente desea ejecutar un sitio en Azure y acceder a la base de datos en la estación de trabajo que suele usar, lo más fácil de usar es Conexiones híbridas.  
- Si es una gran organización que desea colocar un gran número de propiedades web en la nube pública y administrarlas en su propia red, entonces le interesará Entorno del Servicio de aplicaciones.  
- Si tiene varias aplicaciones hospedadas con el Servicio de aplicaciones y simplemente desea acceder a recursos de la red virtual, Integración con redes virtuales es la mejor opción.  

Más allá de los casos de uso, hay diversos aspectos relacionados con la simplicidad. Si la red virtual ya está conectada a la red local, usar Integración con redes virtuales o un entorno del Servicio de aplicaciones es una manera fácil de consumir recursos locales. Por otro lado, si la red virtual no está conectada a la red local, resulta mucho más trabajoso configurar una VPN de sitio a sitio con la red virtual en comparación con instalar el HCM.

Además de las diferencias funcionales, existen también diferencias de precio. La característica Entorno del Servicio de aplicaciones es un servicio premium, pero ofrece la mayor variedad de configuraciones de red, además de otras características interesantes. Integración con redes virtuales se puede usar con planes de servicio de aplicaciones Estándar o Premium y es ideal para consumir de forma segura recursos de la red virtual desde el Servicio de aplicaciones con varios inquilinos. Actualmente, Conexiones híbridas depende de un cuenta de BizTalk con niveles de precios que comienzan de forma gratuita y progresivamente van aumentando según la cantidad que necesite. Sin embargo, cuando se necesita trabajar en muchas redes, no hay ninguna característica como Conexiones híbridas, que permite acceder a recursos de más de 100 redes distintas.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/

<!---HONumber=AcomDC_1203_2015-->