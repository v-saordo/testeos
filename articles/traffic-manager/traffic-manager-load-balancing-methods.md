<properties 
   pageTitle="Administrador de tráfico: métodos de enrutamiento del tráfico | Microsoft Azure"
   description="Este artículo le ayudará a entender los distintos métodos de enrutamiento de tráfico usados por el Administrador de tráfico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="joaoma" />

# Métodos de enrutamiento del Administrador de tráfico

Existen tres métodos de enrutamiento disponibles en el Administrador de tráfico. Cada perfil del Administrador de tráfico solo puede usar un método de enrutamiento del tráfico cada vez, si bien puede seleccionar otro método de enrutamiento del tráfico para su perfil en cualquier momento.

Es importante tener en cuenta que todos los métodos de enrutamiento del tráfico incluyen la supervisión del extremo. Después de configurar su perfil en el Administrador de tráfico para especificar el método de enrutamiento del tráfico que mejor satisfaga sus necesidades, configure los valores de supervisión. Cuando la supervisión esté configurada correctamente, el Administrador de tráfico supervisará el estado de los extremos, que consisten en los servicios en la nube y sitios web, y no enviará tráfico a los extremos que considera que no están disponibles. Para obtener más información acerca de la supervisión del Administrador de tráfico, consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md).

Los tres métodos de enrutamiento del tráfico del Administrador de tráfico son los siguientes:

- **Conmutación por error**: seleccione la conmutación por error cuando tenga puntos de conexión en el mismo centro de datos o en distintos centros de datos de Azure (más conocidos como regiones en el Portal de Azure) y desee usar un punto de conexión principal para todo el tráfico, pero proporcione copias de seguridad en caso de que los puntos de conexión principales o de copia de seguridad no estén disponibles. Para obtener más información, consulte [Método de enrutamiento de tráfico de conmutación por error](#failover-traffic-routing-method).

- **Round Robin**: seleccione Round Robin cuando desee distribuir la carga entre un conjunto de extremos en el mismo centro de datos o entre diferentes centros de datos. Para obtener más información, consulte [Método de enrutamiento de tráfico round robin](#round-robin-traffic-routing-method).

- **Rendimiento**: seleccione Rendimiento cuando tenga extremos en diferentes ubicaciones geográficas y desee solicitar a los clientes que usen el extremo "más cercano" según la latencia más baja. Para obtener más información, consulte [Método de enrutamiento de tráfico de rendimiento](#performance-traffic-routing-method).

Tenga en cuenta que Sitios web de Azure ya proporciona la funcionalidad del método de enrutamiento de tráfico round robin de conmutación por error para sitios web de un centro de datos, independientemente del modo del sitio web. El Administrador de tráfico permite especificar el enrutamiento de tráfico de conmutación por error y de round robin para sitios web en distintos centros de datos.

>[AZURE.NOTE] El período de vida (TTL) de DNS informa a los clientes y a las resoluciones DNS en los servidores DNS sobre el período de tiempo durante el que se deben almacenar los nombres resueltos en la memoria caché. Los clientes seguirán usando un extremo determinado cuando resuelvan su nombre de dominio hasta que expire la entrada de la memoria caché de DNS local para el nombre.

## Método de enrutamiento de tráfico de conmutación por error

En ocasiones, una organización desea proporcionar confiabilidad en sus servicios. Puede hacerlo mediante servicios de copia de seguridad en caso de que el servicio principal esté desactivado. Un patrón habitual de conmutación por error del servicio es proporcionar un conjunto de extremos idénticos y enviar tráfico a un servicio principal con una lista de una o más copias de seguridad. Si el servicio principal no está disponible, los clientes que realizan la solicitud se envían al siguiente por orden. Si los servicios del primer y segundo puesto de la lista no están disponibles, el tráfico irá al que esté en tercer puesto y así sucesivamente.

Al configurar el método de enrutamiento de tráfico de conmutación por error, es importante el orden de los extremos seleccionados. Con el Portal de Azure, puede configurar el orden de la conmutación por error en la página Configuración del perfil.

La Figura 1 muestra un ejemplo del método de enrutamiento del tráfico de conmutación por error para un conjunto de extremos.

![Equilibrio de carga de conmutación por error del Administrador de tráfico](./media/traffic-manager-load-balancing-methods/IC750592.jpg)

**Ilustración 1**

Los siguientes pasos numerados se corresponden con los números de la ilustración 1.

1. El Administrador de tráfico recibe una solicitud entrante de un cliente de un cliente a través de DNS y localiza el perfil.
2. El perfil contiene una lista ordenada de extremos. El Administrador de tráfico comprueba qué extremo es el primero de la lista. Si el extremo está en línea (según la supervisión continuada del extremo), especificará ese nombre de DNS de ese extremo en la respuesta DNS al cliente. Si el extremo está sin conexión, el Administrador de tráfico determina el siguiente extremo en línea de la lista. En este ejemplo CS-A está sin conexión (no disponible), pero CS-B está en línea (disponible).
3. El Administrador de tráfico devuelve el nombre de dominio de CS-B al servidor DNS del cliente, que resuelve este nombre de dominio en una dirección IP y lo envía al cliente.
4. El cliente inicia el tráfico hacia CS-B.

## Método de enrutamiento de tráfico de Round robin

Un patrón de enrutamiento de tráfico habitual es proporcionar un conjunto de extremos idénticos y enviar tráfico a cada uno por el método round robin. El método round robin divide el tráfico en varios extremos. Selecciona un extremo en buen estado de forma aleatoria y no enviará tráfico a los servicios que se detecten que no están en funcionamiento. Para obtener más información, consulte [Supervisión del Administrador de tráfico](../traffic-manager-onitoring.md).

La Figura 2 muestra un ejemplo del método de enrutamiento de tráfico de round robin para un conjunto de extremos.

![Equilibrio de carga de Round Robin del Administrador de tráfico](./media/traffic-manager-load-balancing-methods/IC750593.jpg)

**Ilustración 2**

Los siguientes pasos numerados se corresponden con los números de la ilustración 2.

1. El Administrador de tráfico recibe una solicitud entrante de un cliente y localiza el perfil.
2. El perfil contiene una lista de extremos. El Administrador de tráfico selecciona un extremo de esta lista de forma aleatoria, salvo extremos sin conexión (no disponibles) determinados por la supervisión de extremos del Administrador de tráfico. En este ejemplo, es el extremo CS-B.
3. El Administrador de tráfico devuelve el nombre de dominio de CS-B al servidor DNS del cliente. El servidor DNS del cliente resuelve este nombre de dominio en una dirección IP y lo envía al cliente.
4. El cliente inicia el tráfico hacia CS-B.

El enrutamiento de tráfico de round robin actualmente admite una distribución ponderada del tráfico de red. La Figura 3 muestra un ejemplo del método de enrutamiento de tráfico de round robin ponderado para un conjunto de extremos.

![Equilibrio de carga de Round Robin ponderado](./media/traffic-manager-load-balancing-methods/IC750594.png)

**Ilustración 3**

El enrutamiento de tráfico ponderado round robin le permite distribuir la carga en varios extremos basados en un valor asignado de ponderación de cada extremo. Cuanto mayor sea el peso, mayor frecuencia de retornos tendrá el extremo. Entre los escenarios en los que se puede considerar útil este método se incluyen:

- Actualización gradual de aplicaciones: asigne un porcentaje de tráfico para redirigirlo a un nuevo extremo y aumentar gradualmente el tráfico hasta el 100 %.
- Migración de aplicaciones a Azure: cree un perfil con Azure y los extremos externos, y especifique el peso del tráfico que se redirigirá a cada extremo.
- Expansión de la nube para conseguir capacidad adicional: expanda rápidamente una implementación local en la nube colocándola detrás de un perfil de Administrador de tráfico. Cuando necesite capacidad adicional en la nube, puede agregar o habilitar más extremos y especificar la porción de tráfico que va a cada extremo.

En este momento, no puede usar el Portal de Azure para configurar el enrutamiento de tráfico ponderado. Azure proporciona acceso mediante programación a este método con la API de REST de administración de servicios y los cmdlets de Azure PowerShell asociados.

Para obtener información acerca del uso de las API de REST, consulte [Operaciones en el Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Para obtener información acerca del uso de los cmdlets de Azure PowerShell, consulte [Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769). Para obtener una configuración de ejemplo, consulte [Extremos externos del Administrador de tráfico de Azure y Round Robin ponderado a través de PowerShell](https://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) en el blog de Azure.

Para probar el perfil desde un solo cliente y observar el comportamiento de round robin igual o ponderado, compruebe que el nombre de DNS se resuelve en direcciones IP distintas de los extremos según los valores iguales o ponderados en el perfil. Cuando realice comprobaciones, debe deshabilitar el almacenamiento en memoria caché de DNS del cliente o limpiar la memoria caché de DNS entre cada intento para asegurarse de que se envía una solicitud de nombre de DNS nueva.

## Método de enrutamiento de tráfico de rendimiento

Al objeto de enrutar el tráfico de los extremos que se encuentran en distintos centros de datos en todo el mundo, puede dirigir el tráfico de entrada al extremo más cercano en términos de la latencia más baja entre el cliente que realiza la solicitud y el extremo. Normalmente, el extremo "más cercano" se corresponde directamente con la distancia geográfica más corta. El método de enrutamiento de tráfico de rendimiento le permitirá realizar la distribución según la ubicación y la latencia, pero no puede tener en cuenta los cambios en tiempo real de la cuenta en la carga o configuración de red.

El método de enrutamiento de tráfico de rendimiento identifica el cliente que realiza la solicitud y lo envía al extremo más cercano. La "proximidad" se determina mediante una tabla de latencia de Internet que muestra el tiempo que se invierte en la ida y la vuelta entre varias direcciones IP y cada centro de datos de Azure. Esta tabla se actualiza a intervalos periódicos y no pretende mostrar el rendimiento en tiempo real en Internet. Esto no tiene en cuenta la carga de un servicio determinado, aunque el Administrador de tráfico supervisa los extremos en función del método elegido y no los incluye en las respuestas de las consultas DNS si no están disponibles. En otras palabras, el enrutamiento de tráfico del rendimiento también incorpora el método de enrutamiento de tráfico de conmutación por error.

La Figura 4 muestra un ejemplo del método de enrutamiento de tráfico de rendimiento para un conjunto de extremos.

![Equilibrio de carga de rendimiento del Administrador de tráfico](./media/traffic-manager-load-balancing-methods/IC753237.jpg)

**Ilustración 4**

Los siguientes pasos numerados se corresponden con los números de la ilustración 4.

1. El Administrador de tráfico crea la tabla de latencia de Internet periódicamente. La infraestructura del Administrador de tráfico ejecuta pruebas para determinar el tiempo invertido en viajes de ida y vuelta entre los distintos puntos del mundo y los centros de datos de Azure que hospedan extremos.
2. El Administrador de tráfico recibe una solicitud entrante de un cliente de un cliente a través de DNS y localiza el perfil.
3. El Administrador de tráfico localiza la fila de la tabla de latencia de Internet para la dirección IP de la solicitud de DNS entrante. Debido a que el servidor DNS local del usuario está realizando una solicitud de DNS iterativa para encontrar el servidor DNS autoritativo para el nombre del perfil del Administrador de tráfico, la solicitud de DNS se envía desde la dirección IP del servidor DNS local del cliente.
4. El Administrador de tráfico localiza el centro de datos con el menor tiempo para los centros de datos que hospedan los extremos definidos en el perfil. En este ejemplo, es CS-B.
5. El Administrador de tráfico devuelve el nombre de dominio de CS-B al servidor DNS local del cliente, que resuelve este nombre de dominio en una dirección IP y lo envía al cliente.
6. El cliente inicia el tráfico hacia CS-B.

**Puntos a tener en cuenta:**

- Si el perfil contiene varios extremos del mismo centro de datos, el tráfico dirigido a ese centro de datos se distribuye por igual entre los extremos que estén disponibles y en buen estado según la supervisión del extremo.
- Si todos los extremos de un centro de datos dado no están disponibles (según la supervisión del extremo), el tráfico para los extremos se distribuye por el resto de los extremos disponibles que se especifican en el perfil, no a los extremos más cercanos. Esto ayuda a evitar un error en cascada que podría producirse si se sobrecarga el extremo más cercano.
- Cuando se actualiza la tabla de latencia de Internet, es posible que identifique una diferencia en los patrones de tráfico y carga en los extremos. Estas diferencias deben ser mínimas.
- Al usar el Método de enrutamiento de tráfico de rendimiento con extremos externos, deberá especificar la ubicación de estos extremos. Elija la región de Azure más cercana a su implementación. Para obtener más información, consulte [Administrar extremos en el Administrador de tráfico](traffic-manager-endpoints.md).

## Figuras del Administrador de tráfico

Si desea incluir las ilustraciones de este tema como diapositivas de PowerPoint para su propia presentación sobre el Administrador de tráfico o modificarlas para sus propios fines, consulte [Ilustraciones del Administrador de tráfico en la documentación de MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Pasos siguientes

[¿Qué es el Administrador de tráfico?](traffic-manager-overview.md)

[Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

 

<!---HONumber=AcomDC_0128_2016-->