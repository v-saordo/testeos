# Proceso

Azure permite implementar y supervisar cualquier código de aplicación que se ejecute en un centro de datos de Microsoft. Al crear una aplicación y ejecutarla en Azure, el código y la configuración se denominan conjuntamente un servicio hospedado de Azure. Los servicios hospedados son fáciles de administrar, escalar y reducir verticalmente, volver a configurar y actualizar con versiones nuevas del código de la aplicación. Este artículo se centra en el modelo de aplicación de los servicios hospedados de Azure.<a id="compare" name="compare"></a>

## Tabla de contenido<a id="_GoBack" name="_GoBack"></a>

-   [Beneficios del modelo de aplicación de Azure][]
-   [Conceptos básicos de los servicios hospedados][]
-   [Consideraciones acerca del diseño de servicios hospedados][]
-   [Diseño de aplicaciones para escala][]
-   [Definición y configuración de servicios hospedados][]
-   [El archivo de definición de servicio][]
-   [El archivo de configuración de servicio][]
-   [Creación e implementación de servicios hospedados][]
-   [Referencias][]

## <a id="benefits"> </a>Beneficios del modelo de aplicación de Azure

Al implementar una aplicación como un servicio hospedado, Azure crea una o varias máquinas virtuales (VM) que contienen el código de la aplicación y arranca las VM en las máquinas físicas que residen en cualquiera de los centros de datos de Azure. A medida que las solicitudes de los clientes a la aplicación hospedada entran en el centro de datos, un equilibrador de carga las distribuye equitativamente entre las VM. Si la aplicación se hospeda en Azure, obtiene tres beneficios principales:

-   **Alta disponibilidad.** Alta disponibilidad significa que Azure garantiza que la aplicación se ejecuta durante tanto tiempo como sea posible y que puede responder a las solicitudes de cliente. Si la aplicación termina (por ejemplo, debido a una excepción no controlada), Azure detectará este evento y la reiniciará automáticamente. Si la máquina en que se ejecuta la aplicación sufre cualquier tipo de error de hardware, Azure también lo detectará y creará automáticamente una VM en otra máquina física que funcione correctamente y ejecutará el código desde ella. NOTA: para que una aplicación obtenga un contrato de nivel de servicio de Microsoft con una disponibilidad del 99,95%, es preciso que el código de la aplicación se ejecute al menos en dos VM, ya que esto permite que una máquina virtual procese las solicitudes los clientes mientras que Azure mueve el código de una máquina virtual con errores a otra máquina virtual nueva que funcione correctamente.

-   **Escalabilidad.** Azure permite cambiar fácil y dinámicamente el número de VM que ejecutan un código de aplicación para controlar la carga real que la aplicación admite. Esto permite ajustar la aplicación a la carga de trabajo que los clientes le aplican, con lo que se paga solo por las VM que se necesitan y cuando se necesitan. Cuando se desee cambiar el número de VM, Azure responderá en cuestión de minutos, lo que hace posible cambiar dinámicamente el número de VM que se ejecutan con tanta frecuencia como se desee.

-   **Manejabilidad.** Al ser Azure una oferta de plataforma como servicio (PaaS), administra la infraestructura (el propio hardware, la electricidad y las redes) necesaria para que las máquinas funcionen. Azure también administra la plataforma, lo que asegura un sistema operativo actualizado con todas las revisiones y actualizaciones de seguridad correctas, así como las actualizaciones de componentes como .NET Framework e Internet Information Services. Como todas las VM ejecutan Windows Server 2008, Azure proporciona características adicionales, como la supervisión de diagnósticos, la compatibilidad de Escritorio remoto, firewalls y la configuración de almacén de certificados. Todas estas características se proporcionan sin coste adicional. De hecho, cuando se ejecuta una aplicación en Azure, se incluye la licencia del sistema operativo (SO) Windows Server 2008. Dado que todas las máquinas virtuales ejecutan Windows Server 2008, todo el código que se ejecute en Windows Server 2008 funcionará correctamente en Azure.

## <a id="concepts"> </a>Conceptos básicos de los servicios hospedados

Si una aplicación se implementa como un servicio hospedado en Azure, se ejecuta como uno o varios *roles.* Un *rol* sencillamente hace referencia a los archivos y la configuración de una aplicación. Puede definir uno o varios roles para una aplicación, cada uno de ellos con su propio conjunto y configuración de archivos de aplicación. Para cada rol de la aplicación puede especificar el número de VM, o *instancias de rol*, que se ejecutan. En la siguiente ilustración se muestran dos ejemplos simples de una aplicación modelada como servicio hospedado con roles e instancias de rol.

##### Figura 1: un único rol con tres instancias (VM) que se ejecutan en un centro de datos de Azure

![imagen][0]

##### Figura 2: dos roles, cada uno de ellos con dos instancias (máquinas virtuales), que se ejecutan en un centro de datos de Azure

![imagen][1]

Las instancias de rol suelen procesar las solicitudes de cliente de Internet que entran en el centro de datos a través de lo que se denomina un *extremo de entrada*. Un único rol puede tener 0, o más, extremos de entrada, y cada uno de estos extremos indica un protocolo (HTTP, HTTPS o TCP) y un puerto. Es habitual configurar un rol para tener dos extremos de entrada: HTTP que escucha en el puerto 80 y HTTPS que escucha en el puerto 443. En la siguiente ilustración se muestra un ejemplo de dos roles diferentes con distintos extremos de entrada que les dirigen las solicitudes de los clientes.

![imagen][2]

Al crear un servicio hospedado en Azure, se le asigna una dirección IP direccionable públicamente que los clientes pueden utilizar para poder tener acceso a él. Tras crear el servicio hospedado también es preciso seleccionar un prefijo de URL que se asigna a dicha dirección IP. Dicho prefijo debe ser único, ya que básicamente se reserva la dirección URL *prefijo*.cloudapp.net URL para que nadie más pueda tenerla. Los clientes se comunican con las instancias de rol a través de la dirección URL. Normalmente, la dirección URL *prefijo*.cloudapp.net de Azure no se distribuye ni se publica. En su lugar, se compra un nombre DNS al registrador de DNS que desee y se configura para que redireccione las solicitudes de cliente a la URL de Azure. Para obtener más información, consulte [Configuración de un nombre de dominio personalizado en Azure][].

## <a id="considerations"> </a>Consideraciones acerca del diseño de servicios hospedados

Al diseñar una aplicación para que se ejecute en un entorno de nube, se deben tener en cuenta varias consideraciones, como la latencia, la alta disponibilidad y la escalabilidad.

Decidir dónde ubicar el código de aplicación es una consideración importante al ejecutar un servicio hospedado en Azure. Es habitual implementar la aplicación en los centros de datos más próximos a los clientes, con el fin de reducir la latencia y obtener el mejor rendimiento posible. Sin embargo, también se puede elegir un centro de datos más cercano a la empresa o a los datos si hay problemas jurisdiccionales o legales con los datos y con el lugar en que residen. Hay seis centros de datos por todo el mundo que sean capaces de hospedar el código de aplicación. En la siguiente tabla se indican las ubicaciones disponibles:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**País/región**

</td>
<td style="width: 200px;">
**Subregiones**

</td>
</tr>
<tr>
<td>
Estados Unidos

</td>
<td>
Centromeridional y centroseptentrional

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
Noroccidental

</td>
</tr>
<tr>
<td>
Asia

</td>
<td>
Sudoriental

</td>
</tr>
</tbody>
</table>
Al crear un servicio hospedado, se selecciona una subregión que indica la ubicación en la que se desea que se ejecute el código.

Para lograr una alta disponibilidad y escalabilidad, es muy importante que los datos de la aplicación se guarden en un repositorio central al que pueden tener acceso varias instancias de rol. Para ayudarle, Azure ofrece varias opciones de almacenamiento como blobs, tablas y Base de datos SQL. Para obtener más información acerca de estas tecnologías de almacenamiento, consulte el artículo [Ofertas para el almacenamiento de datos en Azure][]. En la ilustración siguiente se muestra la forma en que el equilibrador de carga del centro de datos de Azure distribuye las solicitudes de cliente a diferentes instancias de rol que tienen acceso al mismo almacén de datos.

![imagen][3]

Normalmente, el código de aplicación y los datos se ubican en el mismo centro de datos, ya que así se reduce la latencia (se mejora el rendimiento) cuando el código de aplicación obtiene acceso a los datos. Además, cuando los datos se mueven por el mismo centro de datos no se cobra el ancho de banda.

## <a id="scale"> </a>Diseño de aplicaciones para escala

A veces puede tomar una aplicación individual (como un sitio web simple) y hospedarla en Azure. Pero con frecuencia, una aplicación puede constar de varios roles que funcionan conjuntamente. Por ejemplo, en la ilustración siguiente hay dos instancias de rol de sitio web, tres instancias de rol de procesamiento de pedidos y una instancia del rol de generador de informes. Todos estos roles funcionan conjuntamente y el código de todos ellos se puede empaquetar conjuntamente e implementarlo como una única unidad en Azure.

![imagen][4]

La razón principal para dividir una aplicación en distintos roles, cada uno de ellos ejecutándose en su propio conjunto de instancias de rol (es decir, VM), es escalar los roles de forma independiente. Por ejemplo, durante el periodo vacacional, es posible que muchos clientes compren productos en su empresa, por lo que quizás desee aumentar el número de instancias de rol que ejecutan su rol de sitio web, así como el número de instancias de rol que ejecutan su rol de procesamiento de pedidos. Después del periodo vacacional es posible que devuelvan muchos productos, por lo que es posible que siga necesitando muchas instancias de sitio web, pero menos de procesamiento de pedidos. Durante el resto del año, es posible que necesite unas pocas instancias de sitio web y de procesamiento de pedidos. Durante todo el tiempo, es posible que solo necesite una instancia de generador de informes. La flexibilidad de las implementaciones basadas en roles en Azure le permite adaptar fácilmente su aplicación a sus necesidades empresariales.

Es común que las instancias de rol del servicio hospedado se comuniquen entre sí. Por ejemplo, el rol de sitio web acepta un pedido de un cliente, pero posteriormente descarga el procesamiento de dicho pedido a las instancias del rol de procesamiento de pedidos. La mejor forma de pasar trabajo de un conjunto de instancias de rol a otro es utilizar la tecnología de puesta en cola que proporciona Azure, el servicio de la cola o las colas del Bus de servicio. Aquí es fundamental utilizar una cola, ya que la cola permite al servicio hospedado escalar sus roles de forma independiente, lo que le permite lograr el equilibrio entre carga de trabajo y coste. Si el número de mensajes de la cola aumenta con el tiempo, puede escalar verticalmente el número de instancias de rol de procesamiento de pedidos. Si el número de mensajes de la cola disminuye con el tiempo, puede reducir verticalmente el número de instancias de rol de procesamiento de pedidos. De esta forma, solo va a pagar por las instancias necesarias para controlar la carga de trabajo real.

La cola también proporciona confiabilidad. Al reducir verticalmente el número de instancias de rol de procesamiento de pedidos, Azure decide las instancias que va a terminar. Puede decidir terminar una instancia que esté en medio del procesamiento del mensaje de una cola. Sin embargo, como el procesamiento del mensaje no ha finalizado correctamente, otra instancia del rol de procesamiento de pedidos vuelve a ver el mensaje, por lo que lo selecciona y lo procesa. Dada la visibilidad de los mensajes de la cola, se garantiza que finalmente todos ellos se procesan. La cola también actúa como equilibrador de carga, ya que distribuye eficazmente sus mensajes a todas las instancias de rol que se los solicitan.

En el caso de las instancias de rol de sitio web, es posible tanto supervisar el tráfico que llega a ellas como decidir escalar verticalmente varias de ellas. La cola permite escalar el número de instancias de rol de sitio web independientemente de las instancias del rol de procesamiento de pedidos. Esta posibilidad es muy eficaz y le proporciona gran flexibilidad. Indudablemente, si la aplicación contiene roles adicionales, se pueden agregar colas adicionales como conducto para comunicarse entre ellos, a fin de sacar provecho de los mismos beneficios de ajuste de escala y coste.

## <a id="defandcfg"> </a>Definición y configuración de servicios hospedados

La implementación de un servicio hospedado en Azure requiere tener también un archivo de definición de servicio y un archivo de configuración de servicio. Ambos son archivos XML y permiten especificar mediante declaración las opciones de implementación de un servicio hospedado. El archivo de definición de servicio describe todos los roles que conforman un servicio hospedado y la forma en que se comunican. El archivo de configuración de servicio describe el número de instancias de cada rol y la configuración de cada una de estas instancias.

## <a id="def"> </a>El archivo de definición de servicio

Como ya se ha mencionado, el archivo de definición de servicio (CSDEF) es un archivo XML que describe los distintos roles que conforman toda la aplicación. Puede encontrar el esquema completo para el archivo XML aquí: [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx][]. El archivo CSDEF contiene un elemento WebRole o WorkerRole para cada rol que desee en su aplicación. La implementación de un rol como rol web (mediante el elemento WebRole) significa que el código se ejecutará en una instancia de rol que contenga Windows Server 2008 e Internet Information Services (IIS). La implementación de un rol como rol de trabajo (mediante el elemento WorkerRole) significa que la instancia de rol tendrá Windows Server 2008 (IIS no estará instalado).

Puede crear e implementar un rol de trabajo que use otro mecanismo para escuchar solicitudes web de entrada (por ejemplo, el código podría crear y usar un HttpListener .NET). Dado que todas las instancias de rol ejecutan Windows Server 2008, el código puede realizar todas las operaciones que normalmente están a disposición de cualquier aplicación que se ejecute en Windows Server 2008.

En cada rol se indica el tamaño de VM deseado que las instancias de dicho rol deben usar. En la siguiente tabla se muestran los distintos tamaños de VM disponibles en la actualidad y los atributos de cada uno de ellos:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Tamaño de VM**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco**

</td>
<td>
**E/S de red pico**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra pequeño**

</td>
<td>
1 x 1,0 GHz

</td>
<td>
768 MB

</td>
<td>
20&#160;GB

</td>
<td>
~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Pequeño**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225&#160;GB

</td>
<td>
~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Mediano**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490&#160;GB

</td>
<td>
~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1&#160;TB

</td>
<td>
~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra grande**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2&#160;TB

</td>
<td>
~800 Mbps

</td>
</tr>
</tbody>
</table>
Se cobra por horas cada VM que se usa como instancia de rol y también se cobran todos los datos que las instancias de rol envían fuera del centro de datos. No se cobra por los datos que entran en el centro de datos. Para obtener más información, consulte [Precios de Azure][]. En general, se aconseja utilizar muchas instancias de rol pequeñas, en lugar de pocas instancias grandes, con lo que la aplicación será más resistente ante errores. Después de todo, cuantas menos instancias de rol tenga, más perjudicial resulta un error en cualquiera de ellas para la aplicación general. Además, como se ha indicado anteriormente, debe implementar al menos dos instancias en cada rol para obtener el contrato del nivel de servicio del 99,95% que Microsoft proporciona.

El archivo de definición de servicio (CSDEF) es también el lugar en el que se especifican muchos atributos acerca de cada rol de la aplicación. Estos son algunos de los elementos más útiles que tiene a su disposición:

-   **Certificados**. Los certificados se usan para cifrar datos o si un servicio web admite SSL. Todos los certificados tienen que cargarse en Azure. Para obtener más información, consulte [Administrar certificados en Azure][]. Esta configuración de XML instala certificados cargados previamente en el almacén de certificados de la instancia de rol para que el código de aplicación pueda usarlos.

-   **Nombres de opciones de configuración**. Para los valores que desea que sus aplicaciones lean cuando se ejecutan en una instancia de rol. El valor real de las opciones de configuración se establece en el archivo de configuración de servicio (CSCFG), que se puede actualizar en cualquier momento sin que sea preciso volver a implementar el código. De hecho, puede codificar las aplicaciones de tal manera que pueda detectar los valores de configuración cambiados sin que se produzca tiempo de inactividad.

-   **Extremos de entrada**. Aquí especifica los extremos HTTP, HTTPS o TCP (con puertos) que desea exponer al mundo exterior a través de la dirección URL *prefijo*.cloadapp.net. Cuando Azure implementa el rol, configurará automáticamente el firewall en la instancia de rol.

-   **Extremos internos**. Aquí especifica los extremos HTTP o TCP que desea exponer a otras instancias de rol que se implementan como parte de la aplicación. Los extremos internos permiten que todas las instancias de rol de la aplicación hablen entre ellas, pero no están accesibles a las instancias de rol que estén fuera de la aplicación.

-   **Módulos de importación**. Instalan opcionalmente componentes útiles en las instancias de rol. Existen componentes para la supervisión de diagnósticos, el Escritorio remoto y Azure Connect (que permite que su instancia de rol tenga acceso a los recursos locales a través de un canal seguro).

-   **Almacenamiento local**. Asigna un subdirectorio de la instancia de rol para que lo use su aplicación. Se describe con mayor detalle en el artículo [Ofertas para el almacenamiento de datos en Azure][].

-   **Tareas de inicio**. Las tareas de inicio le proporcionan una forma de instalar los componentes necesarios en una instancia de rol cuando se arranca. Si se requiere, las tareas se pueden ejecutar con permisos elevados como administrador.

## <a id="cfg"> </a>El archivo de configuración de servicio

El archivo de configuración de servicio (CSCFG) es un archivo XML que describe las opciones que se pueden cambiar sin tener que volver a implementar la aplicación. Puede encontrar el esquema completo para el archivo XML aquí: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][]. El archivo CSCFG contiene un elemento Role para cada rol de la aplicación. Estos son algunos de los elementos que se pueden especificar en el archivo CSCFG:

-   **Versión de SO**. Este atributo permite seleccionar la versión del sistema operativo que se desea que se use en todas las instancias de rol que ejecutan un código de aplicación. Este SO se conoce como *SO invitado* y cada versión nueva incluye las revisiones de seguridad y actualizaciones más recientes disponibles en el momento en que publica el SO invitado. Si establece el valor del atributo osVersion en "*", Azure actualiza automáticamente el SO invitado en todas las instancias de rol cuando hay nuevas versiones del SO invitado disponibles. Sin embargo, puede cancelar las actualizaciones automáticas, para lo que debe seleccionar una versión concreta del SO invitado. Por ejemplo, si se establece el valor "WA-GUEST-OS-2.8\_201109-01" en el atributo osVersion, todas las instancias de rol obtienen lo que se describe en esta página web: [http://msdn.microsoft.com/library/hh560567.aspx][]. Para obtener más información sobre las versiones del SOL invitado, consulte [Administrar las actualizaciones del SO invitado de Azure].

-   **Instancias**. El valor de este elemento indica el número de instancias de rol que desea que se aprovisionen ejecutando el código para un rol concreto. Dado que puede cargar un archivo CSCFG nuevo en Azure (sin tener que volver a implementar su aplicación), es muy sencillo cambiar el valor de este elemento y cargar un archivo CSCFG nuevo para aumentar o reducir dinámicamente el número de instancias de rol que ejecutan su código de aplicación. Esto le permite escalar verticalmente su aplicación para satisfacer las demandas reales de carga de trabajo, al mismo tiempo que controla cuánto le cuesta ejecutar las instancias de rol.

-   **Valores de opciones de configuración**. Este elemento indica los valores de las opciones (que se definen en el archivo CSDEF). Su rol puede leer estos valores mientras se ejecuta. Estos valores de opciones de configuración se suelen usar para las cadenas de conexión a Base de datos SQL o a Almacenamiento de Azure, pero se pueden usar para cualquier fin que se desee.

## <a id="hostedservices"> </a>Creación e implementación de servicios hospedados

Crear un servicio hospedado requiere que primero vaya al [Portal de administración de Azure] y aprovisione un servicio hospedado mediante la especificación de un prefijo de DNS y el centro de datos en el que desea que se ejecute el código. A continuación, en el entorno de desarrollo, cree el archivo de definición de servicio (CSDEF), compile el código de la aplicación y empaquete (zip) todos estos archivos en un archivo de paquete de servicio (CSPKG). También debe preparar su archivo de configuración de servicio (CSCFG). Para implementar su rol, cargue los archivos CSPKG y CSCFG con la API de administración de servicios de Azure. Una vez implementado, Azure aprovisionará instancias de rol en el centro de datos (basándose en los datos de configuración), extraerá del paquete el código de aplicación, lo copiará en las instancias de rol y arrancará las instancias. Ahora el código ya está en funcionamiento.

En la siguiente ilustración se muestran los archivos CSPKG y CSCFG que crea en el equipo de desarrollo. El archivo CSPKG contiene el archivo CSDEF y el código de dos roles. Después de cargar los archivos CSPKG y CSCFG con la API de administración de servicios de Azure, Azure crea las instancias de rol en el centro de datos. En este ejemplo, el archivo CSCFG indicaba que Azure debería crear tres instancias del rol 1 y dos instancias del rol 2.

![imagen][5]

Para obtener más información acerca de cómo implementar, actualizar y volver a configurar roles, consulte el artículo [Implementación y actualización de aplicaciones de Azure][].<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referencias

-   [Creación de un servicio hospedado para Azure][]

-   [Administración de servicios hospedados en Azure][]

-   [Migración de aplicaciones a Azure][]

-   [Configurar un proyecto de Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Escrito por Jeffrey Richter (Wintellect)</p>

</div>

  [Beneficios del modelo de aplicación de Azure]: #benefits
  [Conceptos básicos de los servicios hospedados]: #concepts
  [Consideraciones acerca del diseño de servicios hospedados]: #considerations
  [Diseño de aplicaciones para escala]: #scale
  [Definición y configuración de servicios hospedados]: #defandcfg
  [El archivo de definición de servicio]: #def
  [El archivo de configuración de servicio]: #cfg
  [Creación e implementación de servicios hospedados]: #hostedservices
  [Referencias]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuración de un nombre de dominio personalizado en Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Ofertas para el almacenamiento de datos en Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Precios de Azure]: http://www.windowsazure.com/pricing/calculator/
  [Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Managing Upgrades to the Azure Guests OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Implementación y actualización de aplicaciones de Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Creación de un servicio hospedado para Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Administración de servicios hospedados en Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migración de aplicaciones a Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configurar un proyecto de Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx

<!---HONumber=Oct15_HO3-->