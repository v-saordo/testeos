<properties 
	pageTitle="Funcionalidad del sistema operativo en el Servicio de aplicaciones de Azure" 
	description="Conozca la funcionalidad del sistema operativo disponible para las aplicaciones web, back-ends de aplicaciones móviles y aplicaciones de API en el Servicio de aplicaciones de Azure" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="cephalin"/>

# Funcionalidad del sistema operativo en el Servicio de aplicaciones de Azure #

En este artículo se describe la funcionalidad del sistema operativo de línea base común que está disponible para todas las aplicaciones que se ejecutan en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Esta funcionalidad incluye archivo, red, acceso de registro, registros de diagnóstico y eventos.

<a id="tiers"></a>
## Niveles de plan del Servicio de aplicaciones

El Servicio de aplicaciones ejecuta aplicaciones cliente en un entorno de hospedaje multiempresa. Las aplicaciones implementadas en los niveles **Gratis** y **Compartido** se ejecutan en procesos de trabajo en máquinas virtuales compartidas, mientras que las aplicaciones implementadas en los niveles **Estándar** y **Premium** se ejecutan en máquinas virtuales dedicadas específicamente a las aplicaciones asociadas con un único cliente.

Como el Servicio de aplicaciones permite una experiencia de escalado sin fisuras entre distintos niveles, la configuración de seguridad aplicada al Servicio de aplicaciones permanece sin cambios. Esto garantiza que las aplicaciones no se comportan de manera diferente de forma repentina, provocando errores de manera inesperada, cuando un plan del Servicio de aplicaciones cambia entre un nivel y otro.

<a id="developmentframeworks"></a>
## Marcos de desarrollo

Los planes de tarifa del Servicio de aplicaciones controlan la cantidad de recursos de proceso (CPU, almacenamiento en disco, memoria y entrada de red) disponibles para aplicaciones. Sin embargo, la amplitud de la funcionalidad del marco disponible para las aplicaciones sigue siendo la misma independientemente de los planes de escalado.

El Servicio de aplicaciones es compatible con una gran variedad de marcos de desarrollo, incluidos ASP.NET, ASP clásico, node.js, PHP y Python (todos se ejecutan como extensiones en IIS). Para simplificar y normalizar la configuración de seguridad, las aplicaciones del Servicio de aplicaciones ejecutan normalmente los distintos marcos de desarrollo con su configuración predeterminada. Un enfoque para configurar aplicaciones podría ser personalizar la funcionalidad y el área expuesta de la API para cada marco de desarrollo individual. Por el contrario, el Servicio de aplicaciones adopta un enfoque más genérico mediante la habilitación de una línea base común de la funcionalidad del sistema operativo independientemente de un marco de desarrollo de la aplicación.

Las siguientes secciones resumen los tipos generales de funcionalidad del sistema operativo disponibles para aplicaciones del Servicio de aplicaciones.

<a id="FileAccess"></a>
##Acceso a archivos

Existen varias unidades en el Servicio de aplicaciones, incluidas las unidades locales y las unidades de red.

<a id="LocalDrives"></a>
### Unidades locales

Principalmente, el Servicio de aplicaciones es un servicio que se ejecuta en la parte superior de la infraestructura PaaS (plataforma como servicio) de Azure. Como resultado, las unidades locales "conectadas" a una máquina virtual son los mismos tipos de unidades disponibles para cualquier rol de trabajo que se ejecute en Azure. Esto incluye una unidad de sistema operativo (la unidad D:\\), una unidad de aplicación que contiene archivos cspkg del paquete de Azure que usa exclusivamente el Servicio de aplicaciones (y a los que los clientes no pueden obtener acceso) y una unidad de "usuario" (la unidad C:\\), cuyo tamaño varía según el tamaño de la máquina virtual.

<a id="NetworkDrives"></a>
### Unidades de red (también conocidas como recursos compartidos UNC)

Uno de los aspectos exclusivos del Servicio de aplicaciones que hace que el mantenimiento y la implementación de aplicaciones sean sencillos es que todo el contenido del usuario se almacena en un conjunto de recursos compartidos UNC. Este modelo se asigna perfectamente al patrón común de almacenamiento de contenido usado en entornos locales de hospedaje web que disponen de varios servidores con equilibrio de carga.

En el Servicio de aplicaciones existen varios recursos compartidos UNC creados en cada centro de datos. Un porcentaje del contenido de usuario para todos los clientes en cada centro de datos se asigna a cada recurso compartido UNC. Además, todo el contenido del archivo para una suscripción de cliente única se coloca siempre en el mismo recurso compartido UNC.

Tenga en cuenta que según la forma en la que funcionen los servicios en la nube, la máquina virtual específica responsable del hospedaje de un recurso UNC cambiará con el tiempo. Se garantiza que distintas máquinas virtuales realizarán el montaje de los recursos compartidos UNC, ya que estos aumentan y disminuyen durante el curso normal de operaciones en la nube. Por este motivo, las aplicaciones nunca realizan suposiciones de forma rígida sobre que la información de la máquina en una ruta de acceso del UNC permanezca estable con el tiempo. En su lugar, deben usar la ruta de acceso absoluta *faux* adecuada **D:\\home\\site** que proporciona el Servicio de aplicaciones. Esta ruta de acceso absoluta faux proporciona un método portátil independiente del usuario y la aplicación para hacer referencia a la propia aplicación de alguien. Si usa **D:\\home\\site**, puede transferir archivos compartidos de una aplicación a otra sin tener que configurar una nueva ruta de acceso absoluta para cada transferencia.

<a id="TypesOfFileAccess"></a>
### Tipos de acceso a archivo concedidos a una aplicación

Cada suscripción de cliente tiene una estructura de directorio reservada en un recurso compartido UNC específico dentro de un centro de datos. Un cliente puede disponer de varias aplicaciones creadas en un centro de datos determinado, por lo que todos los directorios que pertenecen a una única suscripción de cliente se crean en el mismo recurso compartido UNC. El recurso compartido puede incluir directorios como los de contenido, registros de diagnóstico y error, y versiones anteriores de la aplicación creadas por el control de origen. Lo esperado es que los directorios de la aplicación del cliente dispongan de acceso de lectura y escritura en el tiempo de ejecución del código de aplicación de la aplicación.

En las unidades locales conectadas a una máquina virtual que ejecute una aplicación, el Servicio de aplicaciones reserva un fragmento de espacio en la unidad C:\\ para el almacenamiento local temporal específico de la aplicación. Aunque la aplicación dispone de un acceso de lectura/escritura completo a su propio almacenamiento local temporal, el almacenamiento no se ha creado realmente para que lo use directamente el código de aplicación. El objetivo real es proporcionar un almacenamiento temporal de archivos para marcos de aplicaciones web e IIS. El Servicio de aplicaciones también limita la cantidad de almacenamiento local temporal disponible para cada aplicación a fin de evitar que las aplicaciones consuman cantidades excesivas de almacenamiento de archivos locales.

Los dos ejemplos de cómo usa el Servicio de aplicaciones el almacenamiento local temporal son el directorio para archivos temporales ASP.NET y el directorio para archivos comprimidos IIS. El sistema de compilación ASP.NET usa el directorio de archivos temporales ASP.NET como ubicación temporal de la memoria caché de compilación. IIS usa el directorio de archivos temporales comprimidos IIS para almacenar los resultados comprimidos de la respuesta. Ambos tipos de uso de archivo (al igual que otros) se reasignan en el Servicio de aplicaciones según el almacenamiento local temporal de la aplicación. Esta reasignación garantiza que la funcionalidad continúa según lo esperado.

Cada aplicación del Servicio de aplicaciones se ejecuta como una identidad de proceso de trabajo con privilegios reducidos, exclusiva y aleatoria denominada "identidad de grupo de aplicaciones", que se describe a continuación: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). El código de la aplicación usa esta identidad para el acceso básico de solo lectura para la unidad del sistema operativo (la unidad D:\\). Esto significa que el código de aplicación puede enumerar estructuras de directorio comunes y leer archivos comunes en la unidad del sistema operativo. Aunque esto puede parecer ser un tipo de nivel amplio de acceso, puede obtenerse acceso a los mismos directorios y archivos cuando realiza el aprovisionamiento de un rol de trabajo en un servicio hospedado de Azure y lee el contenido de la unidad.

<a name="multipleinstances"></a>
### Acceso al archivo en varias instancias

El directorio particular incluye el contenido de una aplicación y el código de una aplicación puede escribir en él. Si una aplicación se ejecuta en varias instancias, el directorio particular se comparte entre todas las instancias de modo que todas ellas ven el mismo directorio. Así, por ejemplo, si una aplicación guarda los archivos cargados en el directorio particular, esos archivos se encuentran inmediatamente disponibles para todas las instancias.

<a id="NetworkAccess"></a>
## Acceso de red
El código de aplicación puede usar protocolos basados en TCP/IPyd UDP para convertir las conexiones de red salientes en extremos accesibles a Internet que expongan servicios externos. Las aplicaciones pueden usar los mismos protocolos para conectarse a los servicios en Azure, por ejemplo, mediante el establecimiento de conexiones HTTPS a Base de datos SQL.

Existe también una funcionalidad limitada para las aplicaciones a la hora de establecer una conexión de bucle invertido local y disponer de una escucha de la aplicación en ese socket de bucle invertido local. Esta característica existe principalmente para habilitar aplicaciones que escuchan en sockets de bucle invertido local como parte de su funcionalidad. Tenga en cuenta que cada aplicación ve una conexión de bucle invertido "privada"; la aplicación "A" no puede escuchar en un socket de bucle invertido establecido por la aplicación "B".

Las canalizaciones con nombre también son compatibles como un mecanismo de comunicación entre procesos (IPC) entre los distintos procesos que ejecutan conjuntamente una aplicación. Por ejemplo, el módulo FastCGI de IIS se basa en conductos con nombre para coordinar los procesos individuales que ejecutan páginas PHP.


<a id="Code"></a>
## Memoria, procesos y ejecución de código
Como se indicó anteriormente, las aplicaciones se ejecutan dentro de los procesos de trabajo con privilegios reducidos mediante una identidad aleatoria de grupo de aplicaciones. El código de aplicación dispone de acceso al espacio de memoria asociado al proceso de trabajo, así como a procesos secundarios que pueden dividirse en procesos CGI u otras aplicaciones. Sin embargo, una aplicación no puede obtener acceso a la memoria o los datos de otra aplicación incluso si se encuentra en la misma máquina virtual.

Las aplicaciones pueden ejecutar scripts o páginas escritas con marcos de desarrollo web compatibles. El Servicio de aplicaciones no configura los ajustes del marco web en modos más restringidos. Por ejemplo, las aplicaciones de ASP.NET que se ejecutan en el Servicio de aplicaciones con una confiabilidad "completa" en oposición a un modo de confiabilidad más restringido. Los marcos web, incluidos ASP clásico y ASP.NET, pueden llamar a componentes COM en proceso (pero no componentes COM fuera del proceso) como ADO (objetos de datos ActiveX) que se registran de manera predeterminada en el sistema operativo Windows.

Las aplicaciones pueden dividir y ejecutar código arbitrario. Una aplicación puede realizar acciones como la división de un shell de comandos o ejecutar un script PowerShell. Sin embargo, aunque los procesos y el código arbitrario pueden dividirse desde una aplicación, los programas ejecutables y los scripts siguen restringidos a los privilegios concedidos en el grupo de aplicaciones primario. Por ejemplo, una aplicación puede dividir un ejecutable que realice una llamada HTTP saliente, pero el mismo ejecutable no puede intentar desvincular la dirección IP de una máquina virtual desde la NIC. Se permite la realización de una llamada de red saliente al código de privilegios reducidos, pero el intento de volver a configurar los ajustes de red en una máquina virtual requiere privilegios administrativos.


<a id="Diagnostics"></a>
## Eventos y registros de diagnóstico
La información de registro es otro conjunto de datos a los que las aplicaciones intentan obtener acceso. Los tipos de información de registro disponibles para el código que se ejecuta en el Servicio de aplicaciones incluyen la información de registro y diagnóstico que genera una aplicación, que también es de fácil acceso para la aplicación.

Por ejemplo, los registros HTTP de W3C generados por una aplicación activa se encuentran disponibles en un directorio de registro en la ubicación de recursos compartidos de red para la aplicación o están disponibles en el almacenamiento de blobs si un cliente ha configurado el registro de W3C en el almacenamiento. La última opción permite que se recopile una gran cantidad de registros sin que exista el riesgo de exceder los límites de almacenamiento de archivos asociados a un recurso compartido de red.

De forma similar, la información de diagnóstico en tiempo real de aplicaciones de .NET también puede registrarse mediante la infraestructura de diagnóstico y seguimiento de .NET con opciones para escribir la información de seguimiento en el recurso compartido de red de la aplicación o, de manera alternativa, en una ubicación del almacenamiento de blobs.

Las áreas de seguimiento y registro de diagnóstico que no están disponibles para las aplicaciones son eventos ETW de Windows y registros de eventos comunes de Windows (por ejemplo, registros de eventos del sistema, de la aplicación y de seguridad). Puesto que es posible que la información de seguimiento ETW se visualice en toda la máquina (con ACL correctas), el acceso de lectura y escritura a los eventos ETW se encuentra bloqueado. Los desarrolladores pueden experimentar que las llamadas a la API para leer y escribir eventos ETW y registros de eventos comunes de Windows parezcan funcionar, pero eso se debe a que el Servicio de aplicaciones "emula" las llamadas de manera que parezcan ser correctas. En realidad, el código de la aplicación no dispone de acceso a estos datos de eventos.

<a id="RegistryAccess"></a>
## Acceso al registro
Las aplicaciones disponen de acceso de solo lectura a la mayor parte del registro (no a todo él) de la máquina virtual que están ejecutando. En la práctica, esto significa que las aplicaciones pueden disponer de acceso a las claves de registro que permiten acceso de solo lectura al grupo de usuarios local. Un área de registro no compatible actualmente para el acceso de lectura y escritura es el subárbol HKEY\_CURRENT\_USER.

El acceso de escritura al registro está bloqueado, incluido el acceso a las claves de registro por usuario. Desde una perspectiva de la aplicación, el acceso de escritura al registro no debe basarse nunca en el entorno de Azure, puesto que se pueden migrar aplicaciones entre distintas máquinas virtuales y, de hecho, se hace. El único almacenamiento persistente en el que se puede escribir y que puede depender de la aplicación es la estructura de directorios de contenido por aplicación almacenada en recursos compartidos UNC del Servicio de aplicaciones.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 

<!---HONumber=AcomDC_0128_2016-->