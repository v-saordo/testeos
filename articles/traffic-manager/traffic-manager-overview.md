<properties 
   pageTitle="¿Qué es el Administrador de tráfico? | Microsoft Azure"
   description="Este artículo le ayudará a comprender qué es el Administrador de tráfico y cómo funciona"
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
   ms.date="12/07/2015"
   ms.author="joaoma" />

# ¿Qué es el Administrador de tráfico?

El Administrador de tráfico de Microsoft Azure permite controlar la distribución del tráfico de usuario a los extremos especificados, que pueden incluir servicios en la nube de Azure, sitios web y otros extremos. El Administrador de tráfico aplica un motor de directivas inteligente a las consultas del Sistema de nombres de dominio (DNS) para los nombres de dominio de los recursos de Internet. Los servicios en la nube de Azure o los sitios web pueden ejecutarse en distintos centros de datos de todo el mundo.

El Administrador de tráfico puede ayudarle a:

- **Mejorar la disponibilidad de las aplicaciones críticas**: el Administrador de tráfico permite mejorar la disponibilidad de las aplicaciones críticas mediante la supervisión de los extremos de Azure y ofreciendo capacidades de conmutación por error automática cuando un servicio en la nube de Azure, el sitio web de Azure u otra ubicación, dejan de funcionar.
- **Mejorar la capacidad de respuesta para las aplicaciones de alto rendimiento**: Azure permite ejecutar servicios en la nube o sitios web en centros de datos de todo el mundo. El Administrador de tráfico puede mejorar la capacidad de respuesta de las aplicaciones y los tiempos de entrega de contenido dirigiendo a los usuarios finales al extremo con la menor latencia de red del cliente.
- **Actualizar y realizar el mantenimiento de servicio sin tiempo de inactividad**: el Administrador de tráfico es compatible con escenarios ampliados en implementaciones híbridas en la nube y locales, entre los que se incluyen los escenarios "ráfaga a la nube", "migración a la nube" y "conmutación por error a la nube". En el caso del mantenimiento planeado, deshabilite el extremo en el Administrador de tráfico y espere hasta que el extremo finalice el mantenimiento de las conexiones existentes. Cuando no haya más tráfico hacia el extremo, actualice el servicio en ese extremo y pruébelo, y vuelva a habilitarlo en el Administrador de tráfico. Esto ayuda a mantener y actualizar los servicios sin tiempo de inactividad para los clientes.
- **Distribución del tráfico en implementaciones grandes y complejas**: gracias a los perfiles anidados del Administrador de tráfico en los que un perfil de Administrador de tráfico puede tener otro perfil de Administrador de tráfico como extremo, puede crear configuraciones para optimizar el rendimiento y la distribución en implementaciones más grandes y complejas. Para obtener más información, consulte [Perfiles anidados](#nested-profiles).

## Cómo funciona el Administrador de tráfico

Cuando se configura un perfil del Administrador de tráfico, los valores que especifique proporcionan al Administrador de tráfico la información necesaria para determinar qué extremo debe atender la solicitud basada en una consulta DNS. Ningún tráfico de extremo real enruta el tráfico a través del Administrador de tráfico.

La *Ilustración 1* muestra cómo el Administrador de tráfico dirige a los usuarios a uno de los extremos de un conjunto de extremos. Los números de la ilustración 1 corresponden a las siguientes descripciones numeradas:

![Cómo funciona el Administrador de tráfico](./media/traffic-manager-overview/IC740854.jpg)

**Ilustración 1**

1. **Tráfico de usuario al nombre de dominio de la compañía**: el cliente solicita información mediante el nombre de dominio de la compañía. El objetivo es resolver un nombre de DNS en una dirección IP. Los dominios de la compañía deben reservarse a través de registros normales de nombres de dominio de Internet de la compañía que se mantienen fuera del Administrador de tráfico. En la ilustración 1, el nombre de dominio de la compañía es *www.contoso.com*.
2. **Nombre de dominio de la compañía al nombre de dominio del Administrador de tráfico**: el registro de recursos DNS del dominio de la compañía indica el nombre de dominio del Administrador de tráfico que se mantiene en el Administrador de tráfico de Azure. Esto se consigue con un registro de recursos CNAME que asigna el nombre de dominio de la compañía al nombre de dominio del Administrador de tráfico. En el ejemplo, el nombre de dominio del Administrador de tráfico es *contoso.trafficmanager.net*.
3. **Nombre de dominio y perfil del Administrador de tráfico**: el nombre de dominio del Administrador de tráfico forma parte del perfil del Administrador de tráfico. El servidor DNS del usuario envía una nueva consulta de DNS para el nombre de dominio del Administrador de tráfico (en nuestro ejemplo *contoso.trafficmanager.net*), que los servidores de nombre DNS del Administrador de tráfico reciben.
4. **Reglas de perfil del Administrador de tráfico procesadas**: el Administrador de tráfico usa el método de enrutamiento y el estado de supervisión del tráfico para determinar qué extremo de Azure o que otro extremo debe atender la solicitud.
5. **Nombre de dominio del extremo enviado al usuario**: el Administrador de tráfico devuelve un registro CNAME que asigna el nombre de dominio del Administrador de tráfico al nombre de dominio del extremo. El servidor DNS del usuario resuelve el nombre de dominio de extremo en su dirección IP y la envía al usuario.
6. **El usuario llama al extremo**: el usuario llama al extremo devuelto usando su dirección IP directamente .

Dado que el dominio de la compañía y la dirección IP resuelta se almacenan en la caché del equipo cliente, el usuario continuará interactuando con el extremo elegido hasta que su entrada de la caché DNS local expire. Es importante tener en cuenta que el cliente DNS almacena en caché las entradas de host de DNS durante su durante su período de vida (TTL). La recuperación de entradas de host desde la caché del cliente DNS pasa por alto el perfil del Administrador de tráfico y se pueden experimentar retrasos en la conexión si el extremo no está disponible antes de que expire el período de vida. Si el TTL de una entrada de host DNS en la caché expira y el equipo cliente necesita volver a resolver el nombre de dominio de la compañía, envía una nueva consulta DNS. El equipo cliente recibe la dirección IP de otro extremo en función del método de enrutamiento del tráfico aplicado y el estado de los extremos en el momento de la solicitud.

## Cómo implementar el Administrador de tráfico

La *ilustración 2* muestra por orden los pasos que son necesarios para implementar el Administrador de tráfico. Estos pasos pueden realizarse en un orden ligeramente diferente una vez que se tenga un conocimiento sólido de la configuración del Administrador de tráfico y las prácticas recomendadas. Los números de la ilustración 2 corresponden a las siguientes descripciones numeradas:

![Cómo configurar el Administrador de tráfico](./media/traffic-manager-overview/IC740855.jpg)

**Ilustración 2**

1. **Implementar los servicios en la nube de Azure, los sitios web de Azure u otros extremos en el entorno de producción**. Cuando se crea un perfil del Administrador de tráfico, debe asociarse a una suscripción. Después agregue los extremos para servicios en la nube y sitios web de nivel estándar de producción que forman parte de la misma suscripción. Si un extremo se encuentra en almacenamiento provisional y no está en un entorno de producción de Azure o no está en la misma suscripción, se puede agregar como un extremo externo. Para obtener más información sobre servicios en la nube, consulte [Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obtener más información sobre sitios web, consulte [Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Decidir un nombre para el dominio del Administrador de tráfico**. Considerar la posibilidad de usar un nombre para el dominio con un prefijo único. La última parte del dominio, trafficmanager.net, es fija. Para obtener más información, consulte los [Procedimientos recomendados](#best-practices).
3. **Decidir la configuración de supervisión que se desea usar**. El Administrador de tráfico supervisa los extremos para comprobar que están en línea, independientemente del método de enrutamiento del tráfico. Después de configurar las opciones de supervisión, el Administrador de tráfico no dirigirá tráfico a extremos que estén sin conexión según el sistema de supervisión a menos que detecte que todos los extremos están sin conexión o que no puede detectar el estado de ninguno de los extremos contenidos en el perfil. Para obtener más información acerca de la supervisión, consulte [Supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
4. **Decidir el método de enrutamiento del tráfico que se desea usar**. Existen tres métodos distintos de enrutamiento del tráfico. Tómese su tiempo para entender qué método satisface mejor sus necesidades. Si necesita cambiar el método más adelante, puede hacerlo cuando quiera. Tenga en cuenta también que cada método requiere pasos de configuración ligeramente distintos. Para obtener información acerca de los métodos de enrutamiento del tráfico, consulte [Información acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-load-balancing-methods.md).
5. **Crear el perfil y configurar las opciones**. Puede usar las API de REST, Windows PowerShell o el Portal de Azure clásico para crear el perfil del Administrador de tráfico y configurar los valores. Para obtener más información, consulte [Cómo configurar las opciones del Administrador de tráfico](#how-to-configure-traffic-manager-settings). En los siguientes pasos se asume que usará la opción **Creación rápida** en el Portal de Azure clásico. 
   - **Crear el perfil de Administrador de tráfico**: para crear un perfil mediante Creación rápida en el Portal de Azure clásico, vea [Administrar perfiles de Administrador de tráfico](traffic-manager-manage-profiles.md).
   - **Configurar los valores del método de enrutamiento del tráfico**: en Creación rápida, debe seleccionar el método de enrutamiento del tráfico para el perfil. Esta configuración puede cambiarse en cualquier momento después de completar los pasos de Creación rápida. Para los pasos de configuración, consulte el tema que corresponde a su método de enrutamiento del tráfico: [Configuración del método de enrutamiento del tráfico de rendimiento](traffic-manager-configure-performance-load-balancing.md), [Configuración del método de enrutamiento del tráfico de conmutación por error](traffic-manager-configure-failover-load-balancing.md), [Método de enrutamiento del tráfico Round Robin](traffic-manager-configure-round-robin-load-balancing.md).
   
   >[AZURE.NOTE] El método de enrutamiento del tráfico de Round Robin admite una distribución ponderada del tráfico de red. Sin embargo, en este momento debe usar las API de REST o Windows PowerShell para configurar la ponderación. Para obtener más información y un ejemplo de configuración, consulte [Extremos externos del Administrador de tráfico de Azure y Round Robin ponderado mediante PowerShell](https://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) en el blog de Azure.

   - **Configurar extremos**: los extremos no se configuran durante la Creación rápida. Después de crear el perfil y especificar el método de enrutamiento del tráfico, tiene que informar al Administrador de tráfico sobre los extremos. Para obtener los pasos para configurar los extremos, consulte [Administrar extremos en el Administrador de tráfico](traffic-manager-endpoints.md)

   - **Configurar opciones de supervisión**: las opciones de supervisión no se configuran durante la Creación rápida. Después de crear el perfil y especificar el método de enrutamiento del tráfico, tiene que informar al Administrador de tráfico sobre lo que debe supervisar. Para obtener los pasos para configurar la supervisión, consulte [Supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
6. **Probar el perfil de Administrador de tráfico**. Compruebe que el perfil y el dominio funcionan según lo esperado. Para obtener información sobre cómo llevar a cabo esto, consulte [Pruebas de configuración del Administrador de tráfico](traffic-manager-testing-settings.md).
7. **Hacer que el registro de recursos DNS del nombre de dominio de la compañía apunte al perfil para activarlo**. Para obtener más información, consulte [Seleccionar un dominio de Internet de la compañía para un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).

Si toma el ejemplo de la ilustración 1 como referencia, podrá cambiar el registro de recursos DNS de sus servidores de la manera siguiente para que el nombre de dominio de la compañía indique el nombre de dominio del Administrador de tráfico: www.contoso.com IN CNAME contoso.trafficmanager.net

## Cómo configurar opciones del Administrador de tráfico

Puede configurar opciones del Administrador de tráfico mediante el Portal de Azure clásico, con las API de REST y con los cmdlets de Windows PowerShell.

Aunque en el Portal de Azure clásico no se ve cada elemento de la API de REST, hay muchas opciones de configuración disponibles con cualquier método. Para obtener más información sobre el uso de las API de REST, consulte [Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Para obtener más información sobre los cmdlets de Windows PowerShell para el Administrador de tráfico, consulte [Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE] Actualmente no es posible configurar puntos de conexión externos (tipo = 'Any'), ponderaciones para el método de enrutamiento de tráfico Round Robin y perfiles anidados, ya que no son compatibles con el Portal de Azure clásico. Debe usar REST (consulte [Crear definición](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Configuración de opciones en el Portal de Azure clásico

En el Portal de Azure clásico, puede crear su perfil del Administrador de tráfico mediante la opción Creación rápida. Creación rápida permite crear un perfil básico. Después de crear el perfil, puede configurar opciones adicionales o modificar las opciones que configuró previamente. Para obtener más información sobre cómo crear un perfil de Administrador de tráfico mediante Creación rápida, consulte [Administrar los perfiles del Administrador de tráfico](traffic-manager-manage-profiles.md).

Puede configurar las siguientes opciones en el Portal de Azure clásico:

- **Prefijo de DNS**: prefijo único que el usuario crea. Los perfiles se muestran por prefijo en el Portal de Azure clásico.
- **TTL de DNS**: el valor del período de vida (TTL) de DNS controla la frecuencia con la que el servidor de nombres de almacenamiento en caché local del cliente consultará el sistema DNS del Administrador de tráfico de Azure para actualizar las entradas DNS.
- **Suscripción**: seleccione la suscripción a la que corresponde el perfil. Tenga en cuenta que esta opción solo aparece si tiene varias suscripciones.
- **Método de enrutamiento del tráfico**: indica la forma en la que quiere que el Administrador de tráfico controle el enrutamiento del tráfico.
- **Orden de conmutación por error**: es el orden de los extremos cuando se emplea el método de enrutamiento del tráfico de conmutación por error.
- **Supervisión**: las opciones de supervisión incluyen el protocolo (HTTP o HTTPS), el puerto y la ruta de acceso relativa y nombre de archivo.

### Configuración de opciones mediante las API de REST

Puede crear y configurar el perfil del Administrador de tráfico mediante las API de REST. Para obtener más información, consulte [Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Perfil**: un perfil contiene un prefijo de nombre de dominio que el usuario crea. Cada perfil se corresponde con la suscripción. Puede crear varios perfiles por suscripción. El nombre del perfil se ve en el Portal de Azure clásico. El nombre que cree, que se incluye en el perfil, se conoce como el dominio del Administrador de tráfico.
- **Definición**: una definición contiene la configuración de la directiva y la configuración de supervisión. Una definición corresponde a un perfil. Únicamente puede tener una sola definición por perfil. La propia definición no se ve en el Portal de Azure clásico, aunque muchos de los valores contenidos en la definición se ven y pueden configurarse en el Portal de Azure clásico.
- **Opciones de DNS**: en cada definición hay opciones de DNS. Aquí se configura el TTL de DNS.
- **Supervisiones**: dentro de cada definición existen opciones de supervisión. Aquí se configuran el protocolo, el puerto y la ruta de acceso relativa y nombre de archivo. Las opciones de supervisión se ven y se pueden configurar en el Portal de Azure clásico. Para obtener más información, consulte [Supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
- **Directiva**: dentro de cada definición existen opciones de directiva. En la directiva se especifican los métodos de enrutamiento del tráfico y los extremos. La propia directiva no se ve en el Portal de Azure clásico, aunque algunas de las opciones de la misma son visibles y se pueden configurar en el Portal de Azure clásico. Para obtener más información, consulte [Acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-load-balancing-methods.md).

## Configuración de opciones mediante Windows PowerShell

Puede crear y configurar el perfil del Administrador de tráfico mediante Windows PowerShell. Para obtener más información, consulte [Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Prácticas recomendadas

- **Crear prefijos únicos y fáciles de entender**: el nombre DNS del perfil del Administrador de tráfico debe ser único. Solo puede controlar la primera parte del nombre de DNS. El nombre de dominio del Administrador de tráfico se usa únicamente con fines de identificación y para dirigir las solicitudes de cliente. Los equipos cliente nunca mostrarán estos nombres al usuario final. Sin embargo, los perfiles se identifican mediante este nombre de dominio, por lo que es importante que pueda identificarlo rápidamente entre otros nombres de dominio que aparecen en el Portal de Azure clásico.
- **Usar puntos para que los nombres de dominio sean únicos o legibles**: también puede usar puntos para separar las partes del prefijo del nombre de dominio. Si piensa crear varias directivas en el Administrador de tráfico, use una jerarquía coherente para distinguir un servicio de otro. Por ejemplo, Contoso tiene servicios globales para web, facturación y administración de la utilidad. Las tres directivas serían *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net* y *util.contoso.trafficmanager.net*. Al configurar servicios en la nube o sitios web, use nombres que incluyan la ubicación. Por ejemplo, *web-us-contoso.cloudapp.net* y *web-asia-contoso.cloudapp.net*. Las limitaciones son las impuestas por el DNS. Suponga que un nombre de dominio es una secuencia de etiquetas separadas por puntos (etiqueta.etiqueta.etiqueta.etiqueta.etc.). Cuando se redactó este documento, los límites para los nombres de dominio en el Administrador de tráfico eran como sigue:
   - Cada etiqueta puede tener un máximo de 63 caracteres.
   - No puede tener más de 40 etiquetas en total. Puesto que trafficmanager.net, ocupa dos etiquetas, quedan 38 para el prefijo.
   - El nombre de dominio completo puede tener un máximo de 253 caracteres. Tenga en cuenta que trafficmanager.net ocupa 19 de los caracteres.
- **TTL de DNS**: el valor de TTL de DNS controla la frecuencia con la que el servidor de nombres de almacenamiento en caché local del cliente consultará el sistema DNS del Administrador de tráfico de Azure para actualizar las entradas DNS. Los cambios que se producen en el Administrador de tráfico, como cambios del perfil o cambios en la disponibilidad de los extremos, tardarán este período de tiempo en actualizarse en todo el sistema global de servidores DNS. Se recomienda que dejar la opción en el valor predeterminado de 300 segundos (cinco minutos). Un número mayor aumenta el tiempo que las resoluciones de DNS y los clientes almacenan en caché las respuestas DNS del Administrador de tráfico, lo que reduce la latencia total de consulta de DNS. Sin embargo, si se requiere una conmutación por error muy rápida, es preferible configurar un valor inferior.
- **Los extremos deben estar en una sola suscripción**: todos los extremos deben estar en la misma suscripción en la que se crea el perfil. Puede agregar extremos de distintas suscripciones a un perfil como extremos externos, pero Azure no los quitará automáticamente si se deshabilita o elimina el servicio asociado. En consecuencia, los extremos externos permanecen en el perfil del Administrador de tráfico y a usted se le seguirán facturando a menos que los quite manualmente.
- **Solo servicios de producción**: solo están disponibles los extremos presentes en un entorno de producción. No se puede dirigir a extremos que se ejecuten en un entorno de ensayo. Tenga en cuenta que si realiza un intercambio de direcciones IP virtuales (VIP) mientras un perfil dirige tráfico, el tráfico usará el extremo que acaba de incorporarse al entorno de producción.
- **Asignar nombres a los extremos que se puedan identificar fácilmente**: tenga en cuenta el prefijo DNS que quiere usar. Se usa el nombre de DNS porque se tiene la certeza de que es único en una suscripción, mientras que el nombre del servicio en la nube o el sitio web puede que no lo sea. Para evitar confusiones, asigne un nombre y un prefijo de DNS a un servicio en la nube o sitio web que sean iguales o similares. Si tiene más de 20 servicios en la nube y sitios web, una nomenclatura incorrecta puede dificultar la búsqueda del extremo correcto. Además, los extremos con nombres inadecuados complicarán el mantenimiento de perfiles.
- **Todos los extremos de un perfil deben prestar servicio a las mismas operaciones y puertos**: si mezcla los extremos, habrá más posibilidades de que un cliente llame a un extremo que no pueda atender su solicitud.
- **Todos los servicios en la nube de un perfil deben usar la misma configuración de supervisión**: solo puede elegir una ruta de acceso y un archivo para supervisar todos los extremos de una definición determinada. Puede escribir "/" en el cuadro de texto **Ruta de acceso relativa y nombre de archivo** para que la supervisión intente tener acceso a la ruta de acceso y al nombre de archivo predeterminados.
- **Deshabilitar extremos para realizar cambios temporales, en lugar de cambiar la configuración**: es posible que, en muchos casos, desee desconectar un extremo. En lugar de quitar el extremo del perfil, deshabilite simplemente el extremo individual en el perfil. Esta acción deja el extremo como parte del perfil, pero el perfil actúa como si el extremo no estuviera incluido en él. Esto resulta muy útil para quitar temporalmente un extremo que se encuentra en modo de mantenimiento o que se vuelve a implementar. Cuando el extremo vuelva a estar funcionando, puede habilitarlo. Para obtener más información, consulte [Administrar extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
- **Deshabilitar un perfil para realizar cambios temporales, en lugar de eliminarlo**: puede que desee desconectar un perfil completo, no solo extremos individuales especificados dentro de él. Para ello, deshabilite el perfil. Cuando se deshabilita un perfil, todas las opciones de configuración siguen estando disponibles para que se puedan modificar en el Portal de Azure clásico; asimismo, cuando se desee volver a usarlo, podrá restablecer rápida y fácilmente el perfil al estado “En línea”. Para obtener más información, consulte [Administrar extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
- **Almacenamiento**: al usar el Administrador de tráfico, es importante que sepa cómo diseñar la ubicación y distribución de almacenamiento. Al diseñar e implementar las aplicaciones en el Administrador de tráfico, piense en la transacción de un extremo a otro y en cómo fluirán sus datos.
- **SQL Azure**: igual que con el diseño de almacenamiento, analice los requisitos de los datos y el estado de la aplicación al ampliar los extremos a varias regiones geográficas.

## Perfiles anidados

Puede especificar el nombre de otro perfil del Administrador de tráfico como extremo, una práctica conocida como perfiles anidados. El nombre de perfil del Administrador de tráfico es su nombre de DNS como, por ejemplo, contoso-europe.trafficmanager.net.

De este modo puede configurar el Administrador de tráfico para que las consultas de nombres DNS entrantes se analicen en un conjunto de niveles a fin de garantizar que el cliente que realiza la solicitud es dirigido al conjunto de extremos correcto. La *ilustración 3* muestra un ejemplo.

![Ejemplo de perfiles anidados del Administrador de tráfico](./media/traffic-manager-overview/IC751072.png)

**Ilustración 3**

Puede anidar hasta 10 niveles y cada perfil se puede configurar con un método de enrutamiento del tráfico distinto.

Por ejemplo, podría crear una configuración para lo siguiente:

- En el nivel superior (el perfil del Administrador de tráfico que se asigna al nombre de DNS externo), puede configurar el perfil con el método de enrutamiento del tráfico de rendimiento.
- En el nivel intermedio, un conjunto de perfiles del Administrador de tráfico representan distintos centros de datos y usan el método de enrutamiento del tráfico de Round Robin.
- En el nivel inferior, un conjunto de extremos de servicio en la nube de cada centro de datos atienden las solicitudes de tráfico del usuario.

El resultado es que los usuarios se dirigen a un centro de datos regional adecuado en función del rendimiento y a un servicio en la nube dentro de ese centro de datos en función de la distribución de carga equivalente o ponderada. Por ejemplo, podría utilizarse ponderación para distribuir un pequeño porcentaje de tráfico a una implementación nueva o de prueba para comentarios de pruebas o de clientes.

La *ilustración 4* muestra la configuración.

![Ejemplo de perfiles de varios niveles del Administrador de tráfico](./media/traffic-manager-overview/IC751073.png)

**Ilustración 4**

En la *ilustración 4*, el perfil de Administrador de tráfico del nivel superior es un perfil principal y los perfiles de Administrador de tráfico del nivel medio son perfiles secundarios.

Si el Administrador de tráfico dirige usuarios a un perfil secundario que tiene un pequeño número de extremos correctos, es posible dichos extremos se sobrecarguen y causen problemas de rendimiento. Para evitar esta situación, puede configurar el perfil del Administrador de tráfico principal con un umbral de extremos correctos que determine si cualquiera de los extremos en los perfiles secundarios de ese elemento principal puede recibir tráfico. Por ejemplo, si desea asegurarse de que hay al menos tres extremos correctos en los perfiles secundarios, establecería este valor de umbral en tres. En el ejemplo de la figura 4, configuraría el perfil del Administrador de tráfico de nivel superior con este umbral.

Para agregar un perfil del Administrador de tráfico como extremo y configurar el número mínimo de extremos correctos, debe usar REST (consulte [Crear definición](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). No puede usar el Portal de Azure clásico.

## Figuras del Administrador de tráfico

Si desea incluir las ilustraciones de este tema como diapositivas de PowerPoint para su propia presentación sobre el Administrador de tráfico o modificarlas para sus propios fines, consulte [Ilustraciones del Administrador de tráfico en la documentación de MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Pasos siguientes

[Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md)

[Supervisión del Administrador de tráfico](traffic-manager-monitoring.md)

[Creación de un perfil](traffic-manager-manage-profiles.md)

[Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!---HONumber=AcomDC_0128_2016-->