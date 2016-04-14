<properties
   pageTitle="Orientación sobre la Red de entrega de contenido (CDN) | Microsoft Azure"
   description="Orientación sobre la Red de entrega de contenido (CDN) para entregar contenido de alto ancho de banda hospedado en Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="masashin"/>

# Orientación sobre Red de entrega de contenido (CDN)

![](media/best-practices-cdn/pnp-logo.png)

## Información general
La Red de entrega de contenido (CDN) de Microsoft Azure ofrece a los desarrolladores una solución global para entregar contenido con alto ancho de banda que se hospeda en Azure. Con la red CDN, puede almacenar en la caché objetos disponibles públicamente cargados desde Almacenamiento de blobs de Azure, una aplicación web, una máquina virtual o una carpeta de la aplicación. La caché de CDN puede mantenerse en ubicaciones estratégicas, con el fin de proporcionar el ancho de banda máximo para entregar contenido a los usuarios. La red CDN se suele usar para entregar el contenido estático, como imágenes, hojas de estilo, documentos, archivos, scripts de cliente y páginas HTML.

La red CDN también se puede usar como una memoria caché para servir contenido dinámico, como un informe en PDF o un gráfico basados en las entradas especificadas; si diferentes usuarios proporcionan los mismos valores de entrada, el resultado debería ser el mismo.

Las principales ventajas de usar la red CDN son una menor latencia y una entrega de contenido más rápida a los usuarios, independientemente de su ubicación geográfica en relación con el centro de datos en que se hospeda la aplicación.

![](media/best-practices-cdn/CDN.png)

El uso de CDN también ayudará a reducir la carga de la aplicación, ya que se alivia el procesamiento requerido para acceder al contenido y entregarlo. Esta reducción puede ayudar a mejorar el rendimiento y la escalabilidad de la aplicación, así como a minimizar los costos de hospedaje, ya que se reducen los recursos de procesamiento requeridos para lograr un nivel concreto de disponibilidad y rendimiento.

Si la red CDN de Azure no satisface sus necesidades, es posible que pueda usar otros sistemas de red de entrega de contenido que no sean implementados por Azure en sus aplicaciones. Como alternativa, puede usar la red CDN de Azure para las aplicaciones hospedadas en otros proveedores mediante la exposición del contenido estático en Almacenamiento de Azure o en las instancias de proceso de Azure.


## Cómo y por qué se usa la red CDN

Entre los usos típicos de la red CDN se incluyen:

+ La entrega de recursos estáticos para aplicaciones cliente, a menudo desde un sitio web. Dichos recursos pueden ser imágenes, hojas de estilo, documentos, archivos, scripts de cliente, páginas HTML, fragmentos de HTML o cualquier otro contenido que el servidor no necesite modificar para cada solicitud. La aplicación puede crear elementos en tiempo de ejecución y ponerlos a disposición de la red CDN (por ejemplo, mediante la creación de una lista de los titulares más recientes), pero no lo hace para cada solicitud.

+ Entrega de contenido compartido y estático público a dispositivos como teléfonos móviles y Tablet PC. La propia aplicación es un servicio web que ofrece una API a los clientes que se ejecutan en los distintos dispositivos. La red CDN también puede ofrecer conjuntos de datos estáticos (mediante el servicio web) para que los clientes los usen, quizás para generar la interfaz de usuario del cliente. Por ejemplo, la red CDN se puede usar para distribuir documentos JSON o XML.

+ La prestación de servicio de sitios web completos que constan únicamente de contenido público estático que se entrega a los clientes, sin necesidad de disponer de recursos de proceso dedicados.

+ El streaming de archivos de vídeo en el cliente a petición. El vídeo aprovecha la latencia baja y una conectividad confiable disponible en los centros de datos distribuidos en todo el mundo que ofrecen conexiones de red CDN.

+ Por lo general, esto mejora la experiencia de los usuarios, sobre todo la de los que están lejos del centro de datos que hospeda la aplicación. De lo contrario, dichos usuarios podrían sufrir una mayor latencia. Una gran parte del tamaño total del contenido de una aplicación web suele ser estática y el uso de la red CDN puede ayudar a mantener el rendimiento y la experiencia general del usuario al tiempo que elimina la necesidad de implementar la aplicación en varios centros de datos.

+ Control de la creciente carga de las aplicaciones que admiten soluciones IoT (Internet de las cosas). La gran cantidad de dichos dispositivos y aparatos implicados podrían sobrepasar fácilmente las capacidades de la aplicación si fuera necesario procesar mensajes de difusión y administrar la distribución de actualizaciones de firmware directamente en cada dispositivo.

+ La capacidad de hacer frente a los picos y aumentos repentinos en la demanda sin que se sea preciso realizar ningún tipo de escalado, lo que evitar el consiguiente aumento de los gastos de mantenimiento. Por ejemplo, cuando se publique una actualización del sistema operativo de un dispositivo de hardware (como un modelo específico de enrutador) o de un dispositivo de consumo (por ejemplo, un televisor inteligente), se producirá un importante pico en la demanda, ya que la descargarán millones de usuarios en millones de dispositivos en un breve período de tiempo.

En la lista siguiente se muestran ejemplos del tiempo medio hasta el primer byte desde distintas ubicaciones geográficas. El rol web de destino se implementa en Azure en la zona oeste de EE. UU. Hay una correlación estrecha entre el mayor aumento debido a la red CDN y su proximidad a un nodo de la red CDN. Encontrará una lista completa de las ubicaciones de los nodos de la red CDN de Azure en [Ubicaciones de nodos de la red de entrega de contenido (CDN) de Azure](cdn/cdn-pop-locations.md/).


| Tiempo (ms) hasta el primer byte (origen) | Tiempo (ms) hasta la primera (CDN) |% de mejora de tiempo de CDN|
|-------------|------------------------|--------------------|------------------|
|*San José, California| 47,5 | 46,5 | 2 % |
|**Dulles, Virginia| 109 | 40,5 | 169 % |
|Buenos Aires, Argentina| 210 | 151 | 39 %|
|*Londres, Reino Unido| 195 | 44 | 343 %|
|Shangai, China| 242 | 206 | 17 % |
|*Singapur | 214 | 74 | 189 % |
|*Tokio, Japón | 163 | 48 | 204 % |
|Seúl, Corea del Sur| 190 | 190 | 0 % |


\* Tiene un nodo de la red CDN de Azure en la misma ciudad.  
\*\* Tiene un nodo de la red CDN de Azure en una ciudad vecina.

## Desafíos  

Hay varios desafíos que se deben tener en cuenta a la hora de usar la red CDN:

+ **Implementación** Debe decidir el origen del que la red CDN obtendrá el contenido y si es preciso implementar el contenido en más de un sistema de almacenamiento (por ejemplo, en la red CDN y en una ubicación alternativa).

  El mecanismo de implementación de la aplicación debe tener en cuenta este proceso para implementar tanto el contenido estático como los recursos, así como para implementar los archivos de la aplicación, como las páginas ASPX. Por ejemplo, puede que necesite implementar un paso independiente para cargar el contenido en Almacenamiento de blobs de Azure.

+ **Control de versiones y control de caché**. Debe considerar cómo va a actualizar el contenido estático y a implementar las nuevas versiones. El contenido de la red CDN se puede purgar en el Administrador de perfiles de la red CDN ubicado en el sitio del Portal de Azure cuando haya nuevas versiones disponibles. Este es un desafío similar a administrar el almacenamiento en caché del lado cliente, como lo que sucede en un explorador web.

+ **Pruebas** Puede resultar difícil realizar pruebas locales de la configuración de la red CDN al desarrollar y probar una aplicación localmente o en un entorno de ensayo.

+ **Optimización del motor de búsqueda (SEO)**. Cierto contenido, como imágenes y documentos, se sirve desde un dominio diferente cuando se usa la red CDN. Esto puede tener un efecto en la SEO de dicho contenido.

+ **Seguridad de contenido**. Muchos servicios de la red CDN, como la red CDN de Azure, no ofrecen ningún tipo de control de acceso al contenido.

+ **Seguridad de los clientes**. Los clientes se pueden conectar desde un entorno que no permita el acceso a los recursos de la red CDN. Podría tratarse de un entorno con restricciones de seguridad que limita el acceso a un conjunto de orígenes conocidos o uno que impide la carga de recursos desde cualquier otra cosa que no sea el origen de la página. Para tratar estos casos se requiere una implementación de reserva.

+ **Resistencia**. La red CDN es un potencial punto único de error de una aplicación. Tiene un SLA de menor disponibilidad que el de almacenamiento de blobs (que se puede usar para entregar contenido directamente). Por tanto, es posible que tenga que considerar la implementación de un mecanismo de reserva para contenido crítico.

  Puede supervisar la disponibilidad del contenido de la red CDN, su ancho de banda, los datos transferidos, los accesos, la frecuencia de aciertos de caché y las métricas de caché desde el Administrador de perfiles de la red CDN que se encuentra en el sitio del Portal de Azure.

Escenarios donde puede resultar útil incluir la red CDN:

+ Si el contenido tiene una tasa de aciertos baja solo se puede acceder a él pocas veces mientras sea válido (esto lo determinada su periodo de vida). La primera vez que se descarga un elemento se generan dos cargos de transacciones, desde el origen a la red CDN y luego desde la red CDN al cliente.

+ Si los datos son privados, como por ejemplo en grandes empresas o en ecosistemas de la cadena de suministros.


## Directrices generales y prácticas recomendadas

El uso de la red CDN es una buena manera de minimizar la carga en la aplicación y maximizar la disponibilidad y el rendimiento. Debería considerar la posibilidad de adoptar esta estrategia para todo el contenido adecuado y los recursos que usa la aplicación. Al diseñar la estrategia de uso de la red CDN, considere los puntos de las siguientes secciones:


### Origen

La implementación de contenido a través de la red CDN requiere que se especifique un punto de conexión HTTP (puerto 80) que usará el servicio de la red CDN para obtener acceso al contenido y almacenarlo en la caché.

El punto de conexión puede especificar un contenedor de Almacenamiento de blobs de Azure que contiene el contenido estático que se va a entregar a través de la red CDN. El contenedor debe marcarse como público. Solo los blobs de un contenedor público con acceso de lectura público estarán disponibles a través de la red CDN.

El punto de conexión puede especificar una carpeta denominada **cdn** en la raíz de una de las capas de proceso de la aplicación (por ejemplo, un rol web o una máquina virtual). Los resultados de las solicitudes de recursos, incluidos los recursos dinámicos, tales como las páginas ASPX, se almacenarán en caché en la red CDN. El período mínimo de almacenamiento en la caché es de 300 segundos. Un periodo más corto impedirá que el contenido se implemente en la red CDN (para más información, consulte la sección [Control de caché](#cache-control)).

Si usa Sitios web de Azure, el extremo se establece en la carpeta raíz del sitio al seleccionar el sitio cuando se crea la instancia de red CDN. Todo el contenido del sitio estará disponible a través de la red CDN.

En la mayoría de los casos, apuntar el extremo de red CDN a una carpeta dentro de una de las capas de proceso de la aplicación ofrece mayor flexibilidad y control. Por ejemplo, resulta más fácil administrar los requisitos de enrutamiento actuales y futuros, así como generar dinámicamente el contenido estático, tales como imágenes en miniatura.

Puede usar cadenas de consulta para diferenciar los objetos en la memoria caché cuando el contenido se entrega desde orígenes dinámicos, tales como páginas ASPX. Sin embargo, este comportamiento se puede deshabilitar con una opción de configuración en el portal de administración cuando se especifica el extremo de la red CDN. Cuando se entrega el contenido desde el almacenamiento de blobs, las cadenas de consulta se tratan como literales de cadena para que dos elementos que tienen el mismo nombre pero cadenas de consulta diferentes se almacenen como elementos separados en la red CDN.

Puede utilizar la reescritura de direcciones URL para que los recursos, como scripts y otro contenido, evite mover los archivos a la carpeta de origen de la red CDN.

Al usar blobs de almacenamiento de Azure para almacenar el contenido de la red CDN, la dirección URL de los recursos de los blobs distingue mayúsculas de minúsculas para el nombre de contenedor y blob.

Al usar Sitios web de Azure, especifique la ruta de acceso a la instancia de la red CDN en los vínculos a los recursos. Por ejemplo, en el siguiente fragmento se especifica un archivo de imagen en la carpeta **Images** del sitio que se entregarán a través de la red CDN:

```XML
<img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
```

### Implementación

Es posible que sea preciso que el contenido estático se aprovisione e implemente de forma independiente de la aplicación si no se incluye en el paquete de implementación o en el proceso de la aplicación. Tenga en cuenta cómo esto afectará al método de control de versiones que se use para administrar tanto los componentes de aplicaciones como el contenido de recursos estáticos.

Tenga en cuenta cómo se tratará la unión (la combinación de varios archivos en un solo archivo) y la minificación (la eliminación de caracteres innecesarios, como espacios en blanco, caracteres de nueva línea, comentarios y otros caracteres) de los archivos de script y CSS. Estas técnicas se usan de manera habitual, pueden reducir los tiempos de carga de los clientes y son compatibles con la entrega de contenido a través de la red CDN. Para más información, consulte el artículo sobre la [unión y minificación](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

Si necesita implementar el contenido en una ubicación adicional, será un paso adicional en el proceso de implementación. Si la aplicación actualiza el contenido para la red CDN, quizás a intervalos regulares o en respuesta a un evento, debe almacenar el contenido actualizado en las ubicaciones adicionales, así como el extremo de la red CDN.

No se puede configurar un extremo de red CDN para una aplicación implementada en el almacenamiento provisional de Azure o en el emulador local de Azure en Visual Studio. Esto afectará a las pruebas unitarias, a las pruebas funcionales y a las pruebas finales previas a la implementación. Debe permitir esto al implementar un mecanismo alternativo. Por ejemplo, podría realizar una implementación previa del contenido en la red CDN mediante una aplicación o utilidad personalizada, y realizar pruebas durante el período en que esté almacenado en la caché. También puede usar directivas de compilación o constantes globales para controlar el lugar desde el que la aplicación carga los recursos. Por ejemplo, durante la ejecución en modo de depuración, podría cargar recursos, como paquetes de script de cliente y otro contenido, de una carpeta local y usar la red CDN durante la ejecución en modo de lanzamiento.

Considere qué enfoque de compresión desea que admita la red CDN:

+ Puede habilitar la compresión en el servidor de origen, en cuyo caso la red CDN admitirá la compresión de forma predeterminada y entregará contenido comprimido a los clientes en los formatos zip o gzip. Si se usa una carpeta de aplicaciones como punto de conexión de la red CDN, el servidor puede comprimir una parte del contenido automáticamente, del mismo modo que cuando lo entrega directamente a un explorador web u otro tipo de cliente. El formato depende del valor del encabezado **Accept-Encoding** de la solicitud enviada por el cliente. En Azure, el mecanismo predeterminado es comprimir automáticamente el contenido cuando el uso de la CPU es inferior al 50 %. Si se usa un servicio en la nube para hospedar la aplicación, el cambio de la configuración puede requerir que se use una tarea de inicio para activar la compresión de la salida dinámica en IIS. Para más información, consulte [Enabling gzip compression with Microsoft Azure CDN through Web Role](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx).

+ Puede habilitar la compresión directamente en los servidores perimetrales de la red CDN, en cuyo caso esta red comprimirá los archivos y los servirá a los usuarios finales. Para más información, consulte [Compresión en la red CDN de Azure](cdn/cdn-improve-performance.md/).

### Enrutamiento y control de versiones

Es posible que necesite distintas instancias de la red CDN en momentos diferentes. Por ejemplo, al implementar una versión nueva de la aplicación puede usar una red CDN nueva y conservar la red CDN anterior (que tiene el contenido en un formato anterior) para las versiones anteriores. Si usa el almacenamiento de blobs de Azure como el origen de contenido, puede simplemente crear una cuenta de almacenamiento independiente o un contenedor independiente y elegir el extremo de la red CDN. Si usa la carpeta raíz *cdn* dentro de la aplicación como el extremo de red CDN, puede usar técnicas de reescritura de direcciones URL para dirigir las solicitudes a otra carpeta.

No use la cadena de consulta para indicar versiones diferentes de la aplicación en los vínculos a los recursos de la red CDN porque, al recuperar el contenido de Almacenamiento de blobs de Azure, la cadena de consulta forma parte del nombre del recurso (el nombre del blob). Este enfoque también puede afectar a la manera en que el cliente almacena los recursos en la caché.

La implementación de nuevas versiones de contenido estático cuando se actualiza una aplicación puede ser un desafío si los recursos anteriores se almacenan en caché en la red CDN. Para más información, consulte la sección [Control de caché](#cache-control").

Considere la posibilidad de restringir el acceso al contenido de la red CDN por país. La red CDN de Azure le permite filtrar las solicitudes en función del país de origen y restringir el contenido que se entrega. Para más información, consulte [Restringir el acceso al contenido por país](cdn/cdn-restrict-access-by-country/).

###Control de caché

Considere cómo administrar el almacenamiento en la caché en el sistema. Por ejemplo, si se usa una carpeta como origen de la red CDN, se puede especificar la capacidad de almacenamiento en caché de las páginas que generan el contenido y el tiempo de caducidad del contenido de todos los recursos de una carpeta específica. También puede especificar las propiedades de caché para la red CDN y para el cliente con encabezados HTTP estándar. Aunque ya debería estar administrando el almacenamiento en caché en el servidor y cliente, el uso de la red CDN le ayudará a hacer más consciente de cómo y dónde el contenido se almacena en caché.

Para impedir que los objetos estén disponibles en la red CDN, puede eliminarlos del origen (contenedor de blobs o carpeta raíz *cdn* de la aplicación), quitar o eliminar el punto de conexión de la red CDN, o, en el caso de Almacenamiento de blobs, hacer que el contenedor o el blob sean privados. Sin embargo, los elementos solo se quitarán de la red CDN cuando expire su período de vida. Si no se especifica período de expiración de la caché (por ejemplo, cuando el contenido se carga desde el Almacenamiento de blobs), se almacenará en la caché de la red CDN un máximo de siete días.

En una aplicación web, se puede establecer el almacenamiento en caché y la fecha de expiración de todo el contenido mediante el elemento *clientCache* de la sección *system.webServer/staticContent* del archivo web.config. Recuerde que si coloca un archivo web.config en una carpeta, los archivos de dicha carpeta y todas las subcarpetas resultan afectados.

Si crea el contenido de la red CDN dinámicamente (por ejemplo, en el código de la aplicación) asegúrese de especificar la propiedad *Cache.SetExpires* en todas las páginas. La red CDN no almacenará en la caché la salida de las páginas que usan la configuración predeterminada de capacidad de almacenamiento en caché *public*. Establezca el período de expiración de caché en un valor adecuado para asegurarse de que el contenido no se descarte y se vuelva a cargar desde la aplicación a intervalos muy cortos.

### Seguridad

La red CDN puede entregar contenido a través de HTTPS (SSL) con el certificado que proporciona la red CDN, pero también estará disponible a través de HTTP. No se puede bloquear el acceso HTTP a los elementos de la red CDN. Es posible que tenga que usar HTTPS para solicitar el contenido estático que se muestra en las páginas que se cargan a través de HTTPS (por ejemplo, un carro de la compra) para evitar las advertencias de explorador sobre contenido mixto.

La red CDN de Azure no proporciona capacidades de control de acceso para proteger el acceso al contenido. No puede usar firmas de acceso compartido (SAS) con la red CDN.

Si entrega scripts de cliente con la red CDN, se pueden producir problemas si estos scripts usan una llamada *XMLHttpRequest* para realizar solicitudes HTTP para otros recursos, como datos, imágenes o fuentes en un dominio diferente. Muchos exploradores web impiden el uso compartido de recursos entre orígenes (CORS), a menos que el servidor web se haya configurado para establecer los encabezados de respuesta adecuados. Puede configurar la red CDN para que admita CORS:

+ Si el origen desde el que se entrega el contenido es Almacenamiento de blobs de Azure, puede agregar un objeto *CorsRule* a las propiedades del servicio. La regla puede especificar los orígenes permitidos para las solicitudes CORS, los métodos permitidos, como GET, y el tiempo máximo en segundos para la regla (es decir, el período dentro del cual el cliente debe solicitar los recursos vinculados después de cargar el contenido original). Para más información, consulte [Compatibilidad con Uso compartido de recursos entre orígenes (CORS) para los Servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

+ Si el origen desde el que se entrega de contenido es una carpeta dentro de la aplicación, como la carpeta raíz *cdn*, puede configurar reglas de salida en el archivo de configuración para establecer un encabezado *Access-Control-Allow-Origin* en todas las respuestas. Para más información sobre el uso de reglas de reescritura, consulte el artículo sobre el [módulo de reescritura de direcciones URL](http://www.iis.net/learn/extensions/url-rewrite-module). Tenga en cuenta que esta técnica no es posible cuando se usa Sitios web de Azure.

### Dominios personalizados

La red CDN de Azure permite especificar un nombre de dominio personalizado y usarlo para acceder a recursos a través de la red CDN. También puede configurar un nombre de subdominio personalizado con un registro *CNAME* en el DNS. El uso de este método puede proporcionar un nivel adicional de abstracción y control.

Si se usa un registro *CNAME*, no se puede usar SSL, ya que la red CDN usa su propio certificado SSL único y este no coincidirá con los nombres de dominio o subdominio personalizados.

### Reserva de red CDN

Debe considerar la forma en que la aplicación hará frente a un error o a la no disponibilidad temporal de la red CDN. Las aplicaciones cliente pueden usar copias de los recursos almacenados en la caché local (en el cliente) durante las solicitudes anteriores, o bien, se puede incluir código que detecte errores y, en su lugar, solicite recursos del origen (la carpeta de aplicaciones o el contenedor de blobs de Azure que contiene los recursos) si la red CDN no está disponible.

### Optimización del motor de búsqueda

Si la SEO es una consideración importante en la aplicación, realice las siguientes tareas:

+ Incluya un encabezado canónico *Rel* en todas las páginas o recursos.

+ Use un registro de subdominio *CNAME* y acceda a los recursos con este nombre.

+ Tenga en cuenta el impacto del hecho de que la dirección IP de la red CDN podría estar en un país o región diferente de la aplicación en sí.

+ Cuando se usa el almacenamiento de blobs de Azure como origen, conserve la misma estructura de archivos para los recursos de la red CDN que la de las carpetas de aplicación.


### Supervisión y registro

Incluya la red CDN como parte de la estrategia de supervisión de la aplicación para la detección y medición de errores o apariciones de latencia extendida. La supervisión se puede realizar desde el Administrador de perfiles de la red CDN ubicado en el sitio del Portal de Azure

Habilite el registro en la red CDN y supervise dicho registro como parte de las operaciones diarias.

Consider la posibilidad de analizar el tráfico de la red CDN para encontrar patrones de uso. El Portal de Azure proporciona herramientas que permiten supervisar:
+ Ancho de banda,
+ Datos transferidos,
+ Accesos (códigos de estado),
+ Estado de la caché,
+ Proporción de aciertos de la caché y
+ Proporción de solicitudes de IPV4 o IPV6.

Para más información, consulte [Análisis de patrones de uso de la red CDN de Azure](cdn/cdn-analyze-usage-patterns.md/).

### Costos asociados

Cuando la red CDN carga datos desde su aplicación, se le cobran tanto las transferencias de datos que salen de la red CDN como las transacciones de almacenamiento. Debe establecer períodos de expiración de caché realistas para el contenido con el fin de garantizar la actualización, pero no tan corto que se produzca la recarga repetida del contenido de la aplicación o el almacenamiento de blobs en la red CDN. Sin embargo, periodos de TTL muy prolongados dificultan la eliminación de elementos de la red CDN, ya que hay que esperar a que expiren.

Los elementos que raramente se descargan incurrirá en los dos cargos de transacción, sin proporcionar ninguna reducción significativa en la carga del servidor.

### Unión y minificación

Use la unión y minificación para reducir el tamaño de los recursos como código JavaScript y páginas HTML almacenados en la red CDN. Esta estrategia puede ayudarle a reducir el tiempo necesario para descargar estos elementos en el cliente.

La unión y minificación los puede controlar ASP.NET. En un proyecto MVC, las uniones se definen en *BundleConfig.cs*. Una referencia a la unión minificada del script se crea al llamar al método *Script.Render*, normalmente en el código de la clase de vista. Esta referencia contiene una cadena de consulta en la que se incluye un valor hash, que se basa en el contenido de la unión. Si cambia el contenido de la unión, también cambiará el código hash generado.

De forma predeterminada, las instancias de la red CDN de Azure tienen la opción *Estado de las cadenas de consultas* deshabilitada. Para que las uniones de script actualizadas las controle correctamente la red CDN, debe habilitar la opción *Estado de las cadenas de consultas* para la instancia de la red CDN. Tenga en cuenta que el valor puede tardar una o dos horas en entrar en vigor.


## Ejemplo de código
En esta sección se incluyen algunos ejemplos de código y técnicas para trabajar con la red CDN.


### Reescritura de direcciones URL
El siguiente extracto de un archivo Web.config de la raíz de una aplicación hospedada en Servicios en la nube muestra cómo realizar la [reescritura de URL](https://technet.microsoft.com/library/ee215194.aspx) al usar la red CDN. Las solicitudes desde la red CDN de contenido almacenado en la caché se redirigen a carpetas concretas de la raíz de la aplicación según el tipo de recurso (por ejemplo, scripts e imágenes).


```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

Estas reglas de reescritura realizan las siguientes redirecciones:

+ La primera regla permite integrar una versión en el nombre de archivo de un recurso, que luego se omite. Por ejemplo, *Filename\_v123.jpg * se reescribe como *Filename.jpg*.

+ Las cuatro reglas siguientes muestran cómo redirigir solicitudes si no se desea almacenar los recursos en una carpeta denominada *cdn** de la raíz del rol web. Las reglas asignan las direcciones URL *cdn/Images*, *cdn/Content*, *cdn/Scripts* y *cdn/bundles* a sus respectivas carpetas raíz en el rol web.

Tenga en cuenta que el uso de la reescritura de URL requiere que se realicen varios cambios en la unión de recursos.

## Más información


+ [Red CDN de Azure](https://azure.microsoft.com/services/cdn/)
+ [CDN documentation](https://azure.microsoft.com/documentation/services/cdn/)
+ [Entrega de contenido desde la red CDN de Azure en su aplicación web](cdn/cdn-serve-content-from-cdn-in-your-web-application/)
+ [Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure](cdn/cdn-cloud-service-with-cdn.md/)
+ [Best Practices for the Microsoft Azure Content Delivery Network](https://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=AcomDC_0128_2016-->