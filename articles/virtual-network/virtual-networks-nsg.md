<properties 
   pageTitle="Qué es un grupo de seguridad de red"
   description="Conozca el firewall distribuido de Azure mediante grupos de seguridad de red (NSG) y cómo utilizarlos para aislar y controlar el flujo de tráfico dentro de las redes virtuales (VNet)."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="telmos" />

# ¿Qué es un grupo de seguridad de red?

El grupo de seguridad de red (NSG) contiene una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a sus instancias de VM en una red virtual. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de ACL se aplican a todas las instancias de VM de esa subred. Además, se puede restringir más el tráfico a una máquina virtual individual asociando un NSG directamente a esa máquina virtual.

## Recurso NSG

Los grupos de seguridad de red contienen las siguientes propiedades:

|Propiedad|Descripción|Restricciones|Consideraciones|
|---|---|---|---|
|Nombre|Nombre del grupo de seguridad de red|Debe ser único dentro de la región.<br/>Puede contener letras, números, caracteres de subrayado, puntos y guiones.<br/>Debe comenzar con una letra o un número.<br/>Debe terminar con una letra, un número o un carácter de subrayado.<br/>Puede tener hasta 80 caracteres.|Dado que es posible que tenga que crear varios grupos de seguridad de red, asegúrese de tener una convención de nomenclatura que facilite la identificación de la función de sus grupos.|
|Region|Región de Azure donde se hospeda el grupo de seguridad de red|Los grupos de seguridad de red solo pueden aplicarse a los recursos dentro de la región en que se crean.|Consulte a continuación los [límites](#Limits) para saber cuántos grupos de seguridad de red puede tener en una región.|
|Grupos de recursos|El grupo de recursos al que pertenece el grupo de seguridad de red.|Aunque un grupo de seguridad de red pertenezca a un grupo de recursos, puede estar asociado a recursos de cualquier grupo de recursos, siempre y cuando el recurso forme parte de la misma región de Azure que el grupo de seguridad de red.|Los grupos de recursos se usan para administrar varios recursos juntos, como una unidad de implementación.<br/>Puede considerar la posibilidad de agrupar los grupos de seguridad de red con los recursos a los que están asociados.|
|Reglas|Las reglas que definen qué tráfico se permite o deniega.||Vea a continuación [Reglas de grupo de seguridad de red](#Nsg-rules).| 

>[AZURE.NOTE] No se admiten ACL basadas en el extremo y grupos de seguridad de red en la misma instancia de máquina virtual. Si desea usar un grupo de seguridad de red y ya tiene un extremo del ACL, quite primero el extremo del ACL. Para obtener información acerca de cómo hacerlo, consulte [Administración de listas de control de acceso (ACL) para extremos mediante PowerShell](virtual-networks-acl-powershell.md).

### Reglas de grupo de seguridad de red

Las reglas de grupo de seguridad de red contienen las siguientes propiedades:

|Propiedad|Descripción|Restricciones|Consideraciones|
|---|---|---|---|
|**Name**|Nombre de la regla|Debe ser único dentro de la región.<br/>Puede contener letras, números, caracteres de subrayado, puntos y guiones.<br/>Debe comenzar con una letra o un número.<br/>Debe terminar con una letra, un número o un carácter de subrayado.<br/>Puede tener hasta 80 caracteres.|Puede tener varias reglas dentro de un grupo de seguridad de red, de modo que asegúrese de seguir una convención de nomenclatura que le permita identificar la función de cada una.|
|**Protocolo**|Protocolo que debe coincidir con la regla|TCP, UDP o *.|El uso de * como protocolo incluye ICMP (solo tráfico este oeste), así como UDP y TCP, y puede reducir el número de reglas necesarias. <br/>Al mismo tiempo, el uso de * podría ser un enfoque demasiado amplio, así que asegúrese de usarlo solamente cuando sea realmente necesario.|
|**Intervalo de puertos de origen**|Intervalo del puerto de origen que debe coincidir con la regla|Número de puerto único entre 1 y 65535, intervalo de puertos (es decir, 100-2.000) o * (para todos los puertos).|Intente usar intervalos de puertos tanto como sea posible para evitar la necesidad de varias reglas.|
|**Intervalo de puertos de destino**|Intervalo del puerto de destino que debe coincidir con la regla|Número de puerto único entre 1 y 65535, intervalo de puertos (es decir, 100-2.000) o * (para todos los puertos).|Intente usar intervalos de puertos tanto como sea posible para evitar la necesidad de varias reglas.|
|**Prefijo de dirección de origen**|Prefijo o etiqueta de la dirección de origen que debe coincidir con la regla.|Dirección IP única (es decir, 10.10.10.10), subred IP (es decir, 192.168.1.0/24), [etiqueta predeterminada](#Default-Tags) o * (para todas las direcciones).|Considere el uso de intervalos, etiquetas y * para reducir el número de reglas.|
|**Prefijo de dirección de destino**|Prefijo o etiqueta de la dirección de destino que debe coincidir con la regla.|Dirección IP única (es decir, 10.10.10.10), subred IP (es decir, 192.168.1.0/24), [etiqueta predeterminada](#Default-Tags) o * (para todas las direcciones).|Considere el uso de intervalos, etiquetas y * para reducir el número de reglas.|
|**Dirección**|Dirección del tráfico que debe coincidir con la regla|entrada o salida|Las reglas de entrada y salida se procesan por separado, en función de la dirección.|
|**Prioridad**|Las reglas se comprueban en orden de prioridad; una vez que se aplica una regla, no se prueba la coincidencia de más reglas.|Número entre 100 y 65535.|Considere la posibilidad de crear prioridades de salto de reglas por 100 para cada regla, para dejar que las nuevas reglas se interpongan entre las existentes.|
|**Access**|Tipo de acceso que se debe aplicar si coincide con la regla|permitir o denegar|Tenga en cuenta que si no se encuentra una regla de permiso para un paquete, el paquete se descarta.|

Contiene dos tipos de reglas: de entrada y de salida. La prioridad de una regla debe ser única dentro de cada conjunto.

![Procesamiento de reglas de NSG](./media/virtual-network-nsg-overview/figure3.png)

La ilustración anterior muestra cómo se procesan las reglas de NSG.

### Etiquetas predeterminadas

Las etiquetas predeterminadas son identificadores proporcionados por el sistema para tratar una categoría de direcciones IP. Puede usar etiquetas predeterminadas en las propiedades de **prefijo de dirección de origen** y **prefijo de dirección de destino** de cualquier regla. Hay tres etiquetas predeterminadas que puede utilizar.

- **VIRTUAL\_NETWORK:** esta etiqueta predeterminada denota todo el espacio de dirección de red. Incluye el espacio de direcciones de red virtual (intervalos CIDR definidos en Azure), así como todos los espacios de direcciones locales conectados y las redes virtuales de Azure conectadas (redes locales).

- **AZURE\_LOADBALANCER:** esta etiqueta predeterminada denota el equilibrador de carga de la infraestructura de Azure. Esto se traducirá en una IP de centro de datos de Azure donde se originarán los sondeos de mantenimiento de Azure.

- **INTERNET:** esta etiqueta predeterminada denota el espacio de dirección IP que se encuentra fuera de la red virtual y es accesible mediante Internet pública. Este intervalo incluye además un [espacio de IP pública propiedad de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Reglas predeterminadas

Todos los grupos de seguridad de red contienen un conjunto de reglas predeterminadas. No se pueden eliminar las reglas predeterminadas, pero dado que tienen asignada la mínima prioridad, pueden reemplazarse por las reglas que cree.

Como se muestra en las siguientes reglas predeterminadas, se permite el tráfico que se origina y termina en una red virtual en las direcciones tanto de entrada como de salida. A pesar de que la conectividad a Internet está permitida para la dirección de salida, está bloqueada para la dirección de entrada de manera predeterminada. Hay una regla predeterminada para permitir que el equilibrador de carga de Azure sondee el estado de las máquinas virtuales y las instancias de rol. Puede invalidar esta regla si no va a usar un conjunto con equilibrio de carga.

**Reglas predeterminadas de entrada**

| Nombre | Prioridad | IP de origen | Puerto de origen | IP de destino | Puerto de destino | Protocolo | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET INBOUND (PERMITIR ENTRANTE DE RED VIRTUAL) | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | PERMITIR |
| ALLOW AZURE LOAD BALANCER INBOUND (PERMITIR ENTRANTE DEL EQUILIBRADOR DE CARGA DE AZURE) | 65001 | AZURE\_LOADBALANCER | * | * | * | * | PERMITIR |
| DENY ALL INBOUND (DENEGAR TODO EL TRÁFICO ENTRANTE) | 65500 | * | * | * | * | * | DENEGAR |

**Reglas predeterminadas de salida**

| Nombre | Prioridad | IP de origen | Puerto de origen | IP de destino | Puerto de destino | Protocolo | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET OUTBOUND (PERMITIR SALIENTE DE RED VIRTUAL) | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | PERMITIR |
| ALLOW INTERNET OUTBOUND (PERMITIR SALIENTE DE INTERNET) | 65001 | * | * | INTERNET | * | * | PERMITIR |
| DENY ALL OUTBOUND (DENEGAR TODO EL TRÁFICO SALIENTE) | 65500 | * | * | * | * | * | DENEGAR |

## Asociación de grupos de seguridad de red

Puede asociar un grupo de seguridad de red a máquinas virtuales, NIC y subredes, según el modelo de implementación que use.

[AZURE.INCLUDE [learn-about-deployment-models-both-include.md](../../includes/learn-about-deployment-models-both-include.md)]
 
- **Asociación de un grupo de seguridad de red a una máquina virtual (solo implementaciones clásicas).** Al asociar un grupo de seguridad de red a una VM, las reglas de acceso de red del grupo de seguridad de red se aplican a todo el tráfico que entra en la VM y sale de esta. 

- **Asociación de un grupo de seguridad de red a una NIC (solo implementaciones del Administrador de recursos).** Al asociar un grupo de seguridad de red a una NIC, las reglas de acceso de red del grupo de seguridad de red se aplican solo a esa NIC. Esto significa que en una máquina virtual con varias NIC, el que un grupo de seguridad de red se aplique a una sola NIC no afecta al tráfico enlazado a otras NIC.

- **Asociación de un grupo de seguridad de red a una subred (todas las implementaciones)** Al asociar un grupo de seguridad de red a una subred, las reglas de acceso de red del grupo de seguridad de red se aplican a todos los recursos IaaS y PaaS de la subred.

Puede asociar grupos de seguridad de red diferentes a una máquina virtual (o NIC, según el modelo de implementación) y a la subred a la que está enlazada una NIC o máquina virtual. Cuando esto sucede, todas las reglas de acceso de red se aplican al tráfico en el orden siguiente:

- **Tráfico de entrada**
	1. El grupo de seguridad de red se aplica a la subred.
	2. El grupo de seguridad de red se aplica a la NIC (Administrador de recursos) o a la máquina virtual (clásica).
- **Tráfico de salida**
	1. El grupo de seguridad de red se aplica a la NIC (Administrador de recursos) o a la máquina virtual (clásica).
	2. El grupo de seguridad de red se aplica a la subred.

![ACL de grupos de seguridad de red](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE] Aunque solamente se puede asociar un solo grupo de seguridad de red a una subred, VM o NIC, puede asociar el mismo grupo de seguridad de red a tantos recursos como desee.

## Implementación
Puede implementar los NSG en el modelo clásico o en los modelos de implementación del Administrador de recursos mediante las distintas herramientas que se enumeran a continuación.

|Herramienta de implementación|Clásico|Resource Manager|
|---|---|---|
|Portal clásico|![No][red]|![No][red]|
|Portal de Azure|![No][red]|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-pportal">![Sí][green]</a>|
|PowerShell|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-ps">![Sí][green]</a>|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-ps">![Sí][green]</a>|
|Azure CLI|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-cli">![Sí][green]</a>|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-cli">![Sí][green]</a>|
|Plantilla ARM|![No][red]|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-template">![Sí][green]</a>|

|**Clave**|Compatible con ![Sí][green]. Haga clic para leer el artículo.|No compatible con ![No][red].|
|---|---|---|

## Planificación

Antes de implementar los grupos de seguridad de red, deberá responder a las siguientes preguntas:

1. ¿Por qué tipos de recursos desea filtrar el tráfico entrante y saliente: NIC en la misma máquina virtual, máquinas virtuales y otros recursos como servicios en la nube o entornos de servicio de aplicaciones conectados a la misma subred, o entre recursos conectados a distintas subredes?

2. ¿Están los recursos por los que desea filtrar el tráfico entrante y saliente conectados a subredes de redes virtuales existentes o se van a conectar a nuevas redes virtuales o subredes?
 
Para más información sobre la planeación de la seguridad de red en Azure, lea los [procedimientos recomendados para los servicios en la nube y la seguridad de red](../best-practices-network-security.md).

## Consideraciones de diseño

Una vez que sepa las respuestas a las preguntas de la sección [Planeación](#Planning), revise los siguientes puntos antes de definir los grupos de seguridad de red.

### Límites

Debe tener en cuenta los límites siguientes al diseñar sus grupos de seguridad de red.

|**Descripción**|**Límite predeterminado**|**Implicaciones**|
|---|---|---|
|Número de grupos de seguridad de red que puede asociar a una subred, VM o NIC|1|Esto significa que no se pueden combinar grupos de seguridad de red. Asegúrese de que todas las reglas necesarias para un determinado conjunto de recursos se incluyen en un único grupo de seguridad de red.|
|Grupos de seguridad de red por región por suscripción|100|De forma predeterminada, se crea un nuevo grupo de seguridad de red para cada máquina virtual que crea en el Portal de Azure. Si permite este comportamiento predeterminado, se quedará rápidamente sin grupos de seguridad de red. Asegúrese de tener en cuenta este límite durante su diseño, y, si es necesario, separe los recursos en varias regiones o suscripciones. |
|Reglas de NSG por NSG|200|Use una amplio intervalo de direcciones IP y puertos para asegurarse de no sobrepasar este límite. |

>[AZURE.IMPORTANT] Asegúrese de ver todos los [límites relacionados con servicios de redes en Azure](../azure-subscription-service-limits.md#networking-limits) antes de diseñar la solución. Algunos límites se pueden aumentar con la apertura de una incidencia de soporte técnico.

### Diseño de red virtual y subred

Puesto que los grupos de seguridad de red se pueden aplicar a subredes, puede minimizar el número de ellos si agrupa los recursos por subred y aplica estos grupos a subredes. Si decide aplicar grupos de seguridad de red a subredes, puede encontrarse con que las redes virtuales y subredes existentes que tenga se hayan definido sin tenerlos en cuenta. Así que es posible que deba definir nuevas subredes y redes virtuales para ajustarse al diseño de grupos de seguridad de red. Además, deberá implementar nuevos recursos en las nuevas subredes. Luego, podría definir una estrategia de migración para mover los recursos existentes a las nuevas subredes.

### Reglas especiales

También debe tener en cuenta las reglas especiales que se enumeran a continuación. Asegúrese de que no bloquea el tráfico permitido por esas reglas ya que, en caso contrario, la infraestructura no podrá comunicarse con servicios esenciales de Azure.

- **IP virtual del nodo de host:** servicios de infraestructura básica, como DHCP, DNS y seguimiento de estado, se proporcionan a través de la dirección IP 168.63.129.16 del host virtualizado. Esta dirección IP pública pertenece a Microsoft y será la única dirección IP virtualizada que se usará en todas las regiones con este fin. Esta dirección IP se asigna a la dirección IP física del equipo del servidor (nodo de host) que hospeda la máquina virtual. El nodo de host actúa como la retransmisión DHCP, la resolución recursiva de DNS y el origen de sonda del sondeo de mantenimiento del equilibrador de carga y el sondeo de mantenimiento del equipo. La comunicación con esta dirección IP no se debe considerar como un ataque.

- **Licencias (servicio de administración de claves):** las imágenes de Windows que se ejecutan en las máquinas virtuales deben contar con licencia. Para ello, se debe enviar una solicitud de licencia a los servidores host del servicio de administración de claves que administran dichas consultas. Esto siempre se hará en el puerto de salida 1688.

### Tráfico ICMP

Las reglas de los grupos de seguridad de red actuales solo permiten los protocolos *TCP* o *UDP*. No hay una etiqueta específica para *ICMP*. Sin embargo, el tráfico ICMP se permite dentro de una red virtual de manera predeterminada gracias a las reglas de red virtual de entrada que permiten el tráfico desde y hacia cualquier puerto y protocolo dentro de la red virtual.

### Subredes

- Tenga en cuenta el número de niveles que requiere la carga de trabajo. Cada nivel se puede aislar mediante el uso de una subred, y a cada subred se le aplica un grupo de seguridad de red. 
- Si necesita implementar una subred para una puerta de enlace de VPN o un circuito ExpressRoute, asegúrese de **NO** aplicar un grupo de seguridad de red a esa subred. Si lo hace, la conectividad entre entornos locales o entre redes virtuales no funcionará.
- Si necesita implementar un aparato virtual, asegúrese de hacerlo en su propia subred, de forma que sus rutas definidas por el usuario (UDR) puedan funcionar correctamente. Puede implementar un grupo de seguridad de red de nivel de subred para filtrar el tráfico dentro y fuera de esta subred. Obtenga más información sobre [cómo controlar el flujo de tráfico y usar aparatos virtuales](virtual-networks-udr-overview.md).

### Equilibradores de carga

- Tenga en cuenta las reglas NAT y de equilibrio de carga para cada equilibrador de carga usado por cada una de las cargas de trabajo. Estas reglas están enlazadas a un grupo back-end que contiene NIC (implementaciones del Administrador de recursos) o máquinas virtuales e instancias de rol (implementaciones clásicas). Considere la posibilidad de crear un grupo de seguridad de red para cada grupo back-end, de forma que solo se permita el tráfico asignado mediante las reglas implementadas en los equilibradores de carga. De esta manera se garantiza que el tráfico que llega directamente al grupo back-end, sin pasar por el equilibrado de carga, también se filtra.
- En implementaciones clásicas, cree puntos de conexión que asignen puertos de un equilibrador de carga a puertos de las máquinas virtuales o instancias de rol. También puede crear su propio equilibrador de carga con acceso público individual en una implementación del Administrador de recursos. Si va a restringir el tráfico a las máquinas virtuales y las instancias de rol que son parte de un grupo back-end de un equilibrador de carga mediante el uso de grupos de seguridad de red, tenga en cuenta que el puerto de destino para el tráfico entrante es el puerto real de la máquina virtual o instancia de rol, y no el puerto que expone el equilibrador de carga. Tenga en cuenta también que la dirección y el puerto de origen para la conexión a la máquina virtual es el puerto y la dirección en el equipo remoto en Internet, y no el puerto y la dirección que expone el equilibrador de carga.
- De forma parecida a los equilibradores de carga con acceso público, cuando crea grupos de seguridad de red para filtrar el tráfico procedente de un equilibrador de carga interno (ILB), debe comprender que el puerto de origen y el intervalo de direcciones aplicado son los del equipo que origina la llamada, y no los del equilibrador de carga. Y el puerto y el intervalo de direcciones de destino están relacionados con el equipo que recibe el tráfico, y no con el equilibrador de carga.

### Otros

- No se admiten listas ACL basadas en puntos de conexión en la misma instancia de máquina virtual. Si desea usar un grupo de seguridad de red y ya tiene un extremo del ACL, quite primero el extremo del ACL. Para información al respecto, vea [Administración de ACL de puntos de conexión](virtual-networks-acl-powershell.md).
- En el modelo de implementación del Administrador de recursos, puede usar un grupo de seguridad de red asociado a una NIC en máquinas virtuales con varias NIC para permitir la administración (acceso remoto) por NIC, de modo que se segregue el tráfico.
- De forma parecida al uso de los equilibradores de carga, al filtrar el tráfico de otras redes virtuales, debe usar el intervalo de direcciones de origen del equipo remoto, y no la puerta de enlace que conecta las redes virtuales.
- Muchos servicios de Azure no se pueden conectar a Redes virtuales de Azure y, por lo tanto, no se puede filtrar el tráfico que entra y sale de ellos con los grupos de seguridad de red. Lea la documentación de los servicios que usa para determinar si se pueden conectar o no a redes virtuales.

## Ejemplo de implementación

Para ilustrar la aplicación de la información de este artículo, definiremos grupos de seguridad de red para filtrar el tráfico de red en una solución de carga de trabajo de dos niveles con los siguientes requisitos:

1. Separación del tráfico entre el front-end (servidores web de Windows) y el back-end (servidores de base de datos SQL).
2. Reglas de equilibrio de carga que reenvían el tráfico que va al equilibrador de carga a todos los servidores web en el puerto 80.
3. Reglas NAT que reenvían el trafico que entra en el puerto 50001 del equilibrador de carga al puerto 3389 de una sola máquina virtual en el front-end.
4. Sin acceso a las máquinas virtuales front-end o back-end desde Internet, a excepción del requisito número 1.
5. Sin acceso a Internet desde el front-end o el back-end.
6. Acceso en el puerto 3389 a cualquier servidor web del front-end, para el tráfico procedente de la propia subred front-end.
7. Acceso en el puerto 3389 a todas las máquinas virtuales de SQL Server del back-end solo desde la subred front-end.
8. Acceso en el puerto 1433 a todas las máquinas virtuales de SQL Server del back-end solo desde la subred front-end.
9. Separación del tráfico de administración (puerto 3389) y el tráfico de base de datos (1433) en diferentes NIC en las máquinas virtuales back-end.

![Grupos de seguridad de red](./media/virtual-network-nsg-overview/figure1.png)

Como se ve en el diagrama anterior, las máquinas virtuales *Web1* y *Web2* están conectadas a la subred *FrontEnd*, y las máquinas virtuales *DB1* y *DB2* están conectadas a la subred *BackEnd*. Ambas subredes forman parte de la red virtual *TestVNet*. Todos los recursos se asignan a la región de Azure *Oeste de EE. UU.*.

Los requisitos del 1 al 6 (a excepción del 3) anteriores se limitan todos a espacios de subred. Para reducir el número de reglas necesarias para cada grupo de seguridad de red, y que sea fácil agregar máquinas virtuales adicionales a las subredes que ejecutan los mismos tipos de cargas de trabajo que las máquinas virtuales existentes, podemos implementar los siguientes grupos de seguridad de red de nivel de subred.

### Grupo de seguridad de red para la subred FrontEnd

**Reglas de entrada**

|Regla|Access|Prioridad|Intervalo de direcciones de origen|Puerto de origen|Intervalo de direcciones de destino|Puerto de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir HTTP|Permitir|100|INTERNET|*|*|80|TCP|
|permitir RDP de front-end|Permitir|200|192\.168.1.0/24|*|*|3389|TCP|
|no denegar nada que proceda de Internet|Denegar|300|INTERNET|*|*|*|TCP|

**Reglas de salida**

|Regla|Access|Prioridad|Intervalo de direcciones de origen|Puerto de origen|Intervalo de direcciones de destino|Puerto de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|denegar Internet|Denegar|100|*|*|INTERNET|*|*|

### Grupo de seguridad de red para la subred BackEnd

**Reglas de entrada**

|Regla|Access|Prioridad|Intervalo de direcciones de origen|Puerto de origen|Intervalo de direcciones de destino|Puerto de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|denegar Internet|Denegar|100|INTERNET|*|*|*|*|

**Reglas de salida**

|Regla|Access|Prioridad|Intervalo de direcciones de origen|Puerto de origen|Intervalo de direcciones de destino|Puerto de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|denegar Internet|Denegar|100|*|*|INTERNET|*|*|

### Grupo de seguridad de red para una única máquina virtual (NIC) en FrontEnd para RDP desde Internet

**Reglas de entrada**

|Regla|Access|Prioridad|Intervalo de direcciones de origen|Puerto de origen|Intervalo de direcciones de destino|Puerto de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir RDP de Internet|Permitir|100|INTERNET|*|\*|3389|TCP|

>[AZURE.NOTE] Observe que el intervalo de direcciones de origen para esta regla es **Internet** y no la dirección IP virtual del equilibrador de carga; el puerto de origen es **\***, no 500001. No confunda reglas NAT, reglas de equilibrio de carga y reglas de grupo de seguridad de red. Las reglas de grupo de seguridad de red siempre se relacionan con el origen y el destino final de tráfico, **NO** con el equilibrador de carga entre los dos.

### Grupo de seguridad de red para las NIC de administración en BackEnd

**Reglas de entrada**

|Regla|Access|Prioridad|Intervalo de direcciones de origen|Puerto de origen|Intervalo de direcciones de destino|Puerto de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir RDP de front-end|Permitir|100|192\.168.1.0/24|*|\*|3389|TCP|

### Grupo de seguridad de red para las NIC de acceso a la base de datos en el back-end

**Reglas de entrada**

|Regla|Access|Prioridad|Intervalo de direcciones de origen|Puerto de origen|Intervalo de direcciones de destino|Puerto de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir SQL del front-end|Permitir|100|192\.168.1.0/24|*|\*|1433|TCP|

Puesto que algunos de los grupos de seguridad de red mencionados anteriormente deben estar asociados a NIC individuales, este escenario se deberá implementar como una implementación del Administrador de recursos. Observe cómo se combinan las reglas para el nivel de subred y NIC, según cómo deban aplicarse.

## Pasos siguientes

- [Implemente grupos de seguridad de red en el modelo de implementación clásica](virtual-networks-create-nsg-classic-ps.md).
- [Implemente grupos de seguridad de red en el Administrador de recursos](virtual-networks-create-nsg-arm-pportal.md).
- [Administración de registros de grupo de seguridad de red](virtual-network-nsg-manage-log.md).

[green]: ./media/virtual-network-nsg-overview/green.png
[yellow]: ./media/virtual-network-nsg-overview/yellow.png
[red]: ./media/virtual-network-nsg-overview/red.png

<!---HONumber=AcomDC_0218_2016-->
