<properties
   pageTitle="Información general sobre el Equilibrador de carga de Azure | Microsoft Azure"
   description="Información general sobre las características, la arquitectura y la implementación del Equilibrador de carga de Azure Ayuda a comprender cómo funciona el equilibrador de carga y a sacarle provecho en la nube."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/09/2015"
   ms.author="joaoma" />


# ¿Qué es Equilibrador de carga de Azure?

Equilibrador de carga de Azure proporciona una alta disponibilidad y un elevado rendimiento de red para sus aplicaciones. Se trata de un equilibrador de carga de nivel 4 (TCP, UDP) que distribuye el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales que se definen en un conjunto de carga equilibrada.

Se puede configurar para:

- Equilibrar la carga del tráfico entrante de Internet entre las máquinas virtuales. A esto se le conoce como [equilibrio de carga accesible desde Internet](load-balancer-internet-overview.md).
- Equilibrar la carga del tráfico entre máquinas virtuales de una red virtual, entre máquinas virtuales de servicios en la nube o entre equipos locales y máquinas virtuales de una red virtual entre entornos locales. Se conoce como [equilibrio de carga interno](load-balancer-internal-overview.md)
- Reenviar el tráfico externo a una máquina virtual determinada.

## Equilibrador de carga en los dos modelos de implementación

Todos los recursos en la nube necesitan una dirección IP pública para ser accesibles desde Internet. La infraestructura de nube de Microsoft Azure usa direcciones IP no enrutables en de sus recursos. Usa traducción de direcciones de red (NAT) con direcciones IP públicas para comunicarse con Internet.

### Azure clásico

En el modelo de implementación clásico de Azure, una dirección IP pública y un FQDN se asignan a un servicio en la nube. Las máquinas virtuales que se implementan dentro de los límites de un servicio en la nube pueden agruparse para usar un equilibrador de carga. Equilibrador de carga de Azure se ocupará de la traducción de puertos y de equilibrar la carga del tráfico de red, aprovechando la dirección IP pública para el servicio en la nube.

En un modelo de implementación clásica, la traducción se realiza mediante puntos de conexión que establecen una relación uno a uno entre el puerto público asignado de la dirección IP pública y el puerto local asignado para enviar tráfico a una máquina virtual específica.

El equilibrio de carga se realiza mediante los puntos de conexión establecidos del equilibrador de carga. Dichos puntos de conexión establecen una relación uno a varios entre la dirección IP pública y los puertos locales asignados a todas las máquinas virtuales del conjunto que responden al tráfico de red con carga equilibrada.

La etiqueta de dominio de la dirección IP pública que usaría un equilibrador de carga en este modelo de implementación es `<cloud service name>.cloudapp.net`.

Se trata de una representación gráfica de un equilibrador de carga en el modelo de implementación clásica:

![Equilibrador de carga en el modelo de implementación clásica](./media/load-balancer-overview/asm-lb.png)

### Administrador de recursos de Azure

El concepto de equilibrador de carga cambia en el Administrador de recursos de Azure (ARM) porque no es necesario un servicio en la nube para crear un equilibrador de carga.

En el Administrador de recursos, una dirección IP pública es su propio recurso y se puede asociar a una etiqueta de dominio o nombre DNS. En este caso, la dirección IP pública se está asociada al recurso de equilibrador de carga. Así, las reglas del equilibrador de carga y las reglas NAT de entrada usarán la dirección IP pública como punto de conexión de Internet para los recursos que reciban tráfico de red con carga equilibrada.

Un recurso de Interfaz de red (NIC) contiene la configuración de dirección IP (IP privada o pública) para una máquina virtual. Una vez que se agrega un NIC a un grupo de direcciones IP de back-end de equilibrador de carga, el equilibrador de carga empezará a enviar tráfico de red con carga equilibrada en función de las reglas de carga equilibrada creadas.

A continuación se muestra una representación gráfica de un equilibrador de carga en el Administrador de recursos de Azure (ARM):

![Equilibrador de carga en el Administrador de recursos](./media/load-balancer-overview/arm-lb.png)

## Características del equilibrador de carga

### Distribución basada en hash

El Equilibrador de carga de Azure usa un algoritmo de distribución basado en hash. De forma predeterminada, usa un hash de 5-tupla (IP de origen, puerto de origen, IP de destino, puerto de destino, tipo de protocolo) para asignar el tráfico a los servidores disponibles. Dicho algoritmo solo proporciona adherencia dentro de una sesión de transporte. Los paquetes de la misma sesión TCP o UDP se dirigirán a la misma instancia de IP del centro de datos (DIP) tras el punto de conexión con equilibrio de carga. Cuando el cliente cierra y vuelve a abrir la conexión o inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia. Esto puede provocar que el tráfico vaya a un extremo DIP diferente.


Para más detalles, vea [Modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md).

Este gráfico muestra una distribución basada en hash:

![Distribución basada en hash](./media/load-balancer-overview/load-balancer-distribution.png)

### Enrutamiento de puerto

El Equilibrador de carga de Azure le permite controlar cómo se administra la comunicación entrante. Estas comunicación puede incluir tráfico iniciado desde hosts de Internet o máquinas virtuales de otros servicios en la nube o redes virtuales. Este control se representa mediante un punto de conexión (también conocido como punto de conexión de entrada).

Un extremo escucha en un puerto público y enruta el tráfico a un puerto interno. Puede asignar los mismos puertos para un extremo interno o externo o usar un puerto diferente para ellos. Por ejemplo, puede tener un servidor web configurado para escuchar en el puerto 81, mientras que la asignación del punto de conexión público es el puerto 80. La creación de un punto de conexión público desencadena la creación de una instancia del Equilibrador de carga de Azure.

El uso y la configuración predeterminados de puntos de conexión en una máquina virtual que cree con el Portal de Azure van al Protocolo de Escritorio remoto y el tráfico de sesión remota de Windows PowerShell. Estos puntos de conexión le permiten administrar la máquina virtual de manera remota a través de Internet.


### Reconfiguración automática

El Equilibrador de carga de Azure se reconfigura inmediatamente al escalar instancias horizontalmente o reducirlas verticalmente. Por ejemplo, esta reconfiguración puede ocurrir cuando se aumenta el recuento de instancias para el rol web o de trabajo, o al colocar máquinas virtuales adicionales en el mismo conjunto de equilibrio de carga.


### Supervisión de servicios
El Equilibrador de carga de Azure ofrece la capacidad de sondear las diversas instancias de servidor para comprobar su estado. Cuando un sondeo no responde, el Equilibrador de carga deja de enviar nuevas conexiones a las instancias incorrectas. Las conexiones existentes no se ven afectadas.

Se admiten tres tipos de sondeos:

- Sondeo de agente invitado (solo en máquinas virtuales PaaS). El Equilibrador de carga de Azure utiliza el agente invitado de la máquina virtual. Solo escucha y responde con una respuesta HTTP 200 OK cuando la instancia se encuentra en estado Listo (es decir, la instancia no está en un estado como Ocupado, Reciclando o Deteniendo). Si el agente invitado no responde con HTTP 200 OK, el Equilibrador de carga de Azure marca la instancia como sin respuesta y deja de enviar tráfico a esa instancia. El Equilibrador de carga continuará para hacer ping a la instancia. Si el agente invitado responde con un HTTP 200, el Equilibrador de carga enviará de nuevo tráfico a esa instancia. Cuando se usa un rol web, el código de sitio web normalmente se ejecuta en w3wp.exe, que no está supervisado por el agente invitado o el tejido de Azure. Esto significa que los errores en w3wp.exe (por ejemplo, respuestas HTTP 500) no se notificarán al agente invitado, y Equilibrador de carga no sabrá sacar esa instancia de la rotación.

- Sondeos HTTP personalizados El sondeo personalizado de Equilibrador de carga invalida el sondeo predeterminado (agente invitado). Puede usarlo para crear su propia lógica personalizada para determinar el estado de la instancia de rol. El Equilibrador de carga sondeará periódicamente el punto de conexión (de forma predeterminada, cada 15 segundos). La instancia se considerará en rotación si responde con un TCP ACK o HTTP 200 dentro del período de tiempo de espera (valor predeterminado, 31 segundos). Esto puede resultar útil para implementar su propia lógica para quitar instancias de rotación del equilibrador de carga. Por ejemplo, puede configurar la instancia para devolver un estado no 200 si la instancia está por encima del 90% de la CPU. Para los roles web que usan w3wp.exe, esto también significa que dispone de supervisión automática de su sitio web, puesto que los errores en el código de su sitio web devolverán un estado no 200 al sondeo del equilibrador de carga.

- Sondeos TCP personalizados Los sondeos TCP se basan en el establecimiento correcto de sesiones TCP para un puerto de sondeo definido.

Para más información, vea [Sondeo de estado del equilibrador de carga](https://msdn.microsoft.com/library/azure/jj151530.aspx).

### NAT de origen


Todo el tráfico saliente a Internet procedente de su servicio pasa por NAT de origen (SNAT) usando la misma dirección VIP que para el tráfico entrante. SNAT proporciona ventajas importantes:

- Permite la actualización y la recuperación ante desastres de servicios de forma fácil, puesto que la dirección VIP puede asignarse dinámicamente a otra instancia del servicio.

- Facilita la administración de ACL, puesto que la ACL se puede expresar en términos de direcciones VIP y, por lo tanto, no cambia a medida que los servicios se escalan o reducen verticalmente o se implementan de nuevo.

La configuración del Equilibrador de carga de Azure admite NAT de cono completo para UDP. NAT de cono completo es un tipo de NAT en el que el puerto permite conexiones entrantes desde cualquier host externo (en respuesta a una solicitud saliente).


>[AZURE.NOTE] Tenga en cuenta que para cada nueva conexión saliente iniciada por una máquina virtual, el Equilibrador de carga de Azure también asigna un puerto saliente. El host externo verá el tráfico entrante como un puerto IP virtual (VIP) asignado. Si los escenarios requieren un gran número de conexiones salientes, se recomienda que las máquinas virtuales usen direcciones IP públicas a nivel de instancia para que tengan una dirección IP saliente dedicada para SNAT. Esto reducirá el riesgo de agotamiento de puertos.
>
>El número máximo de puertos que pueden usar las direcciones VIP o ILPIP (dirección IP pública a nivel de instancia) es 64.000. Se trata de una limitación estándar de TCP.


**Compatibilidad con varias direcciones IP con equilibrio de carga para máquinas virtuales**

Puede tener más de una dirección IP pública con equilibrio de carga asignada a un conjunto de máquinas virtuales. Gracias a esta posibilidad, puede hospedar varios sitios web SSL o varios agentes de escucha de Grupo de disponibilidad AlwaysOn de SQL Server en el mismo conjunto de máquinas virtuales. Encontrará más información en [Varias direcciones VIP por servicio en la nube](load-balancer-multivip.md)

**Implementaciones basadas en plantilla a través del Administrador de recursos de Azure**

El Administrador de recursos de Azure es el nuevo marco de administración de servicios en Azure. El Equilibrador de carga de Azure puede administrarse ahora mediante las herramientas y las API basadas en el Administrador de recursos de Azure. Para obtener más información sobre el Administrador de recursos de Azure, vea [IaaS just got easier with Azure Resource Manager (IaaS, simplemente más fácil con el Administrador de recursos de Azure)](https://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/).


## Pasos siguientes

[Información general sobre el equilibrador de carga accesible desde Internet](load-balancer-internet-overview.md)

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la creación de un equilibrador de carga accesible desde Internet](load-balancer-internet-getstarted.md)

<!---HONumber=AcomDC_0128_2016-->