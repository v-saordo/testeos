<properties
   pageTitle="Introducción a la seguridad de Microsoft Azure | Microsoft Azure"
   description="Este artículo proporciona información general sobre las capacidades de seguridad de Microsoft Azure y consideraciones generales para las organizaciones que va a migrar sus activos a un proveedor de nube."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="yuridio"/>

#Introducción a la seguridad de Microsoft Azure

Al crear o migrar los activos de TI a un proveedor en la nube, confía en las capacidades de la organización para proteger las aplicaciones y datos que les confía y los controles de seguridad que proporcionan para controlar la seguridad de sus activos en la nube.

La infraestructura de Azure está diseñada desde la instalación para aplicaciones de hospedaje de millones de clientes simultáneamente y proporciona una base de confianza en la que las empresas pueden satisfacer sus necesidades de seguridad. Además, Azure proporciona una amplia gama de opciones de seguridad configurables, así como la capacidad para controlarlas, por lo que puede personalizar la seguridad para satisfacer los requisitos únicos para sus implementaciones.

En este artículo de información general sobre la seguridad de Azure, veremos:

-   Los servicios y características de Azure que puede usar para ayudar a proteger sus servicios y datos en Azure

-   Cómo Microsoft protege la infraestructura de Azure para ayudar a proteger sus datos y aplicaciones

##Administración de identidades y acceso


Controlar el acceso a la infraestructura de TI, los datos y las aplicaciones es fundamental. En Microsoft Azure, estas capacidades se proporcionan mediante servicios como Azure Active Directory, Almacenamiento Azure y la compatibilidad con numerosos estándares y API.

[Azure Active Directory](active-directory-whatis.md) (Azure AD) es un repositorio de identidades y el motor que proporciona autenticación, autorización y control de acceso para usuarios, grupos y objetos de una organización. Azure AD ofrece a los desarrolladores una forma eficaz de integrar la administración de identidades en sus aplicaciones. Protocolos estándar del sector como [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx) y [OpenID Connect](http://openid.net/connect/) hacen posible iniciar sesión en una gran variedad de plataformas, como .Net, Java, Node.js y PHP.

La API Graph basada en REST permite a los desarrolladores leer y escribir en el directorio desde cualquier plataforma. Gracias a la compatibilidad con [OAuth 2.0](http://oauth.net/2/), los desarrolladores pueden compilar aplicaciones móviles y web que se integran con las API web de Microsoft y de terceros, así como crear API web seguras propias. Hay bibliotecas de cliente de código abierto disponibles para .NET, la Tienda Windows, iOS y Android, así como otras bibliotecas en fase de desarrollo.

### Cómo habilita Azure la administración de identidades y acceso

Azure AD se puede usar como un directorio en la nube independiente para su organización, o bien como una solución integrada con su Active Directory local existente. Algunas características de integración incluyen la sincronización de directorios y el inicio de sesión único (SSO). Estos extienden el alcance de sus identidades locales existentes a la nube y mejoran la experiencia del administrador y del usuario final.

Otras capacidades de administración de identidades y acceso incluyen:

-   Azure AD habilita [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) para las aplicaciones SaaS, independientemente de donde estén hospedadas. Algunas aplicaciones están federadas con Azure AD y otras usan SSO con contraseña. Las aplicaciones federadas también pueden admitir el aprovisionamiento de usuarios y almacén de contraseñas.

-   El acceso a datos en el [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) se controla mediante la autenticación. Cada cuenta de almacenamiento tiene una clave principal ([clave de la cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/ee460785.aspx), o SAK) y una clave secreta secundaria (la [firma de acceso compartido](storage-dotnet-shared-access-signature-part-1.md), o SAS).

-   Azure AD proporciona la identidad como un servicio a través de la federación (mediante [Servicios de federación de Active Directory](fundamentals-identity.md)), la sincronización y la replicación de directorios locales.

-   [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication.md) es el servicio de autenticación mediante varias fases que requiere que el usuario también compruebe los inicios de sesión mediante una aplicación móvil, una llamada de teléfono o un mensaje de texto. Está disponible para usarse con Azure AD, para garantizar los recursos locales con el servidor de Azure Multi-Factor Authentication y con directorios y aplicaciones personalizados mediante el SDK.

-   Los [servicios de dominio de Azure AD](https://azure.microsoft.com/services/active-directory-ds/) le permiten unir máquinas virtuales de Azure a un dominio sin necesidad de implementar controladores de dominio. Los usuarios pueden iniciar sesión en estas máquinas virtuales con sus credenciales corporativas de Active Directory y administrar las máquinas virtuales unidas a un dominio mediante una directiva de grupo para aplicar una base de referencia de seguridad en todas sus máquinas virtuales de Azure.

-   [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) proporciona un servicio de administración de identidades global y de alta disponibilidad para aplicaciones de consumo que se escala a cientos de millones de identidades. Se puede integrar en plataformas móviles y web. Los consumidores pueden iniciar sesión en todas sus aplicaciones con una experiencia totalmente personalizable, usando sus cuentas de las redes sociales o mediante credenciales nuevas.

##Control de acceso de datos y cifrado

Microsoft emplea los principios de separación de funciones y [privilegios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) en las operaciones de Azure. El acceso a los datos por el personal de soporte técnico de Azure requiere su permiso explícito y se concede de forma "just-in-time" que se registra y audita; a continuación, revoca tras la finalización de la contratación.

Además, Azure proporciona varias funciones para proteger datos en tránsito y en reposo, incluido el cifrado de datos, archivos, aplicaciones, servicios, comunicaciones y unidades. Tiene la opción de cifrar la información antes de colocarla en Azure, así como almacenar claves en sus centros de datos locales.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started\sec-azgsfig1.PNG)

### Tecnologías de cifrado de Azure

Puede recopilar detalles sobre el acceso administrativo al entorno de suscripción mediante [Informes de Azure AD](active-directory-reporting-audit-events.md). Tiene la opción para configurar [Cifrado de unidad BitLocker](https://technet.microsoft.com/library/cc732774.aspx) en los discos duros virtuales que contienen información confidencial en Azure.

Otras funciones de Azure que le ayuda a proteger los datos incluyen:

-   Los desarrolladores de aplicaciones pueden crear el cifrado en las aplicaciones que se implementan en Azure mediante Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) y .NET Framework.

- El cifrado en el cliente para el almacenamiento de blobs de Microsoft permite controlar totalmente las claves. El servicio de almacenamiento no ve nunca las claves y no es capaz de descifrar los datos.

-   [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) (con [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) proporciona cifrado de nivel de datos y archivos y prevención de pérdida de datos mediante la administración de acceso basada en directivas.

-   Azure es compatible con el [cifrado de nivel de tabla y columna (TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) en máquinas virtuales de SQL Server y es compatible con servidores de administración de claves locales de terceros en centros de datos de los clientes.

-   Las claves de cuenta de almacenamiento, las firmas de acceso compartido, los certificados de administración y otras claves son únicas para cada inquilino de Azure.

-   El almacenamiento híbrido de Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) cifra los datos mediante un par de claves pública/privada de 128 bits antes de cargarlos en Almacenamiento de Azure.

-   Azure admite y usa numerosos mecanismos de cifrado, como SSL/TLS, IPsec y AES, en función de los tipos de datos, los contenedores y los transportes.

##Virtualización

La plataforma de Azure usa un entorno virtualizado. Las instancias de usuario funcionan como máquinas virtuales independientes que no tienen acceso a un servidor host físico, y este aislamiento se aplica con [niveles de privilegios de procesador (anillo 0/anillo 3)](https://en.wikipedia.org/wiki/Protection_ring) físicos.

El anillo 0 tiene los mayores privilegios y el anillo 3 tiene los menores. El sistema operativo invitado se ejecuta en el anillo 1, con menos privilegios, y las aplicaciones en el anillo 3, el que menos privilegios tiene. Esta virtualización de recursos físicos conduce a una separación clara entre el sistema operativo invitado e hipervisor, lo que resulta en la separación de seguridad adicional entre los dos.

El hipervisor de Azure actúa como un micronúcleo y pasa todas las solicitudes de acceso al hardware desde las máquinas virtuales invitadas al host para el procesamiento mediante una interfaz de memoria compartida denominada VMBus. Esto impide que los usuarios obtengan acceso de lectura/escritura/ejecución sin procesar en el sistema y reduce el riesgo de compartir recursos del sistema.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started\sec-azgsfig2.PNG)

### Cómo implementa Azure la virtualización

Azure usa un firewall de hipervisor (filtro de paquetes), que se implementa en el hipervisor y se configura mediante un agente de controlador de tejido. Esto ayuda a proteger los inquilinos contra accesos no autorizados. De forma predeterminada, cuando se crea una máquina virtual, se bloquea todo el tráfico y, a continuación, el agente de controlador de tejido configura el filtro de paquetes para agregar *reglas y excepciones* para permitir el tráfico autorizado.

Existen dos categorías de reglas que se programan aquí:

-   **Reglas de configuración de la máquina o la infraestructura**: de forma predeterminada, se bloquea toda la comunicación. Hay excepciones para permitir que una máquina virtual envíe y reciba tráfico DHCP y DNS. Las máquinas virtuales también pueden enviar tráfico a la Internet "pública" y enviar tráfico a otras máquinas virtuales en el clúster y el servidor de activación del sistema operativo. La lista de destinos de salida de máquinas virtuales no incluye subredes de enrutador de Azure, administración back-end de Azure y otras propiedades de Microsoft.

-   **Archivo de configuración de funciones**: esto define las ACL de entrada según el modelo de servicio de los inquilinos. Por ejemplo, si un inquilino tiene un front-end web en el puerto 80 de una determinada máquina virtual, Azure abre el puerto TCP 80 para todas las direcciones IP si configura un punto de conexión en el modelo [Administración de servicios de Azure](resource-manager-deployment-model.md). Si la máquina virtual tiene un rol de back-end o de trabajo que se ejecuta, abrimos el rol de trabajo únicamente para la máquina virtual con el mismo inquilino.

##Aislamiento

El mantenimiento de la separación para evitar la transferencia de información no autorizada y no intencionada entre las implementaciones en una arquitectura multiinquilino compartida es otro requisito de seguridad en la nube importante.

Azure implementa el [control de acceso de red](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) y la segregación mediante el aislamiento de VLAN, las ACL, los filtros IP y los equilibradores de carga. El tráfico entrante externo a sus máquinas virtuales está restringido a los puertos y protocolos que defina. El filtrado de red se implementa para evitar tráfico falsificado y restringe el tráfico entrante y saliente a los componentes de plataforma segura. Se implementan directivas de flujo de tráfico en los dispositivos de protección de límites que deniegan el tráfico de forma predeterminada.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started\sec-azgsfig3.PNG)

La traducción de direcciones de red (NAT) se usa para separar el tráfico de red interno de tráfico externo. El tráfico interno no es enrutable externamente. Las [direcciones IP virtuales](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que sean enrutables externamente se traducen en direcciones [IP dinámicas internas](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que solo se pueden enrutar en Azure.

El tráfico externo a máquinas virtuales de Azure atraviesa el firewall mediante listas de control de acceso (ACL) en enrutadores, equilibradores de carga y conmutadores de nivel 3. Solo se permiten determinados protocolos conocidos. Las ACL se usan para limitar el tráfico procedente de las máquinas virtuales invitadas a las otras VLAN usadas para la administración. Además, el tráfico filtrado a través de filtros IP en el sistema operativo host limita aún más el tráfico en vínculos de datos y niveles de red.

### Cómo implementa Azure el aislamiento

El controlador de tejido de Azure es responsable de asignar recursos de infraestructura para cargas de trabajo de inquilinos y administra las comunicaciones unidireccionales desde el host a las máquinas virtuales. El hipervisor de Azure fuerza la separación de la memoria y el proceso entre las máquinas virtuales y enruta de forma segura el tráfico de red a los inquilinos de SO invitado. Azure también implementa el aislamiento de inquilinos, el almacenamiento y las redes virtuales:

-   Cada inquilino de Azure AD está aislado de forma lógica con los límites de seguridad.

-   Las cuentas de almacenamiento de Azure son únicas para cada suscripción, y el acceso debe autenticarse mediante una clave de cuenta de almacenamiento.

-   Las redes virtuales están aisladas de forma lógica mediante una combinación de direcciones IP privadas únicas, firewalls y ACL de IP. Los equilibradores de carga enrutan el tráfico a los inquilinos adecuados en función de las definiciones de puntos de conexión.

##Red virtual y firewall

Las [redes distribuidas y virtuales](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) en la ayuda de Azure, asegúrese de que el tráfico de red privada se aísla lógicamente del tráfico en otras redes virtuales de Azure.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started\sec-azgsfig4.PNG)

Su suscripción puede contener varias redes privadas aisladas (e incluir el firewall, el equilibrio de carga y la traducción de direcciones de red).

Azure proporciona tres niveles principales de segregación de red en cada clúster de Azure para separar el tráfico lógicamente. Las [redes de área local virtuales](https://azure.microsoft.com/services/virtual-network/) (VLAN) se usan para separar el tráfico del cliente del resto de la red de Azure. El acceso a la red de Azure desde fuera del clúster se restringe a través de equilibradores de carga.

El tráfico de red hacia y desde máquinas virtuales debe pasar a través del conmutador virtual del hipervisor. El componente del filtro IP en el SO raíz aísla la máquina virtual raíz de las máquinas virtuales invitadas y estas entre sí. Realiza el filtrado del tráfico para restringir la comunicación entre los nodos del inquilino y la Internet pública (según la configuración del servicio del cliente), y los separa de otros inquilinos.

El filtro IP ayuda a evitar que las máquinas virtuales invitadas:
 
- Generen tráfico falsificado
 
- Reciban tráfico que no está dirigido a ellas

- Dirijan tráfico a puntos de conexión de la infraestructura protegida

- Envíen o reciban tráfico de difusión inadecuado

Puede colocar las máquinas virtuales en [redes virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Estas redes virtuales son similares a las redes que se configuran en entornos locales, que normalmente se asocian a un conmutador virtual. Las máquinas virtuales conectadas a la misma red virtual de Azure pueden comunicarse entre sí sin ninguna configuración adicional. También tiene la opción de configurar subredes diferentes dentro de la red virtual de Azure.

Puede usar las siguientes tecnologías de red virtual de Azure para ayudar a proteger las comunicaciones de la red virtual de Azure:

-   [**Grupos de seguridad de red (NSG)**](virtual-networks-nsg.md). Puede usar un grupo de seguridad de red para controlar el tráfico a una o más instancias de máquina virtual en la red virtual. Un grupo de seguridad de red contiene reglas de control de acceso que permitan o denieguen el tráfico según la dirección del tráfico, el protocolo, la dirección de origen y el puerto, y la dirección de destino y el puerto.

-   [**Enrutamiento definido por el usuario**](virtual-networks-udr-overview.md). Puede controlar el enrutamiento de paquetes a través de un dispositivo virtual mediante la creación de rutas definidas por el usuario que especifican el próximo salto de los paquetes que fluyen a una subred específica para que así vayan a su dispositivo de seguridad de red virtual.

-   [**Reenvío IP**](virtual-networks-udr-overview.md). Un dispositivo de seguridad de red virtual debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual reciba el tráfico dirigido a otros destinos, debe habilitar el reenvío IP de la máquina virtual.

-   [**Tunelización forzada**](vpn-gateway-about-forced-tunneling.md). La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet generado por las máquinas virtuales en una red virtual de Azure de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio para inspección y auditoría.

-   [ACL de **puntos de conexión**](virtual-machines-set-up-endpoints.md). Puede controlar en qué equipos se permiten las conexiones entrantes de Internet a una máquina virtual en la red virtual de Azure mediante la definición de las ACL de puntos de conexión.

-   [**Soluciones de seguridad de red de asociados**](https://azure.microsoft.com/marketplace/). Hay una serie de soluciones de seguridad de la red de asociados a la que puede tener acceso desde Azure Marketplace.

### Cómo implementa Azure las redes virtuales y el firewall

Azure implementa firewalls de filtrado de paquetes en todas las máquinas virtuales host e invitadas de forma predeterminada. Las imágenes del sistema operativo Windows desde la Galería de Azure también tienen el Firewall de Windows habilitado de forma predeterminada. Los equilibradores de carga en el perímetro de redes públicas de Azure controlan las comunicaciones según las ACL de IP administradas por los administradores del cliente.

Si Azure mueve los datos del cliente como parte de las operaciones normales o durante un desastre, lo hace a través de canales de comunicaciones privados y cifrados. Otras capacidades que Azure aprovecha para usar en el firewall y las redes virtuales son:

-   **Firewall de host nativo**: el almacenamiento y el tejido de Azure se ejecutan en un sistema operativo nativo que no tiene hipervisor y, por tanto, el firewall de windows se configura con los dos conjuntos de reglas anteriores. El almacenamiento se ejecuta nativo para optimizar el rendimiento.

-   **Firewall de host**: el firewall del host protege el sistema operativo que ejecuta el hipervisor. Las reglas se programan para permitir que solo el controlador de tejido y los cuadros de salto se comuniquen con el sistema operativo del host en un puerto específico. Las otras excepciones son para permitir la respuesta DHCP y las respuestas DNS. Azure usa un archivo de configuración de la máquina que tiene la plantilla de reglas de firewall para el sistema operativo del host. El propio host está protegido contra ataques externos por un firewall de Windows configurado para permitir únicamente la comunicación de orígenes conocidos y autenticados.

-   **Firewall de invitado**: replica las reglas del filtro de paquetes del conmutador de máquina virtual, pero programados con otro software (es decir, el Firewall de Windows del sistema operativo invitado). El firewall de la máquina virtual invitada puede configurarse para restringir las comunicaciones a o de la máquina virtual invitada, incluso si la configuración permite la comunicación en el filtro IP del host. Por ejemplo, puede usar el firewall de la máquina virtual invitada para restringir la comunicación entre dos de sus redes virtuales que se han configurado para conectarse entre sí.

-   **Firewall de almacenamiento (FW)**: el firewall en el front-end de almacenamiento filtra el tráfico solo en los puertos 80/443 y otros puertos de utilidad necesarios. El firewall en el back-end de almacenamiento restringe las comunicaciones a solo las que proceden de los servidores front-end de almacenamiento.

-   **Puerta de enlace de red virtual**: las [puertas de enlace de red virtual de Azure](virtual-networks-configure-vnet-to-vnet-connection.md) sirven como puertas de enlace entre entornos que conectan las cargas de trabajo en las red virtual de Azure a los sitios locales. Es necesaria para conectarse a los sitios locales a través de [túneles VPN de sitio a sitio IPsec](vpn-gateway-create-site-to-site-rm-powershell.md) o a través de circuitos [ExpressRoute](expressroute-introduction.md). Para los túneles VPN de IPsec/IKE, las puertas de enlace realizan protocolos de enlace de IKE y establecen los túneles VPN de IPsec S2S entre las redes virtuales y los sitios locales. Las puertas de enlace de redes virtuales también terminan las [VPN de punto a sitio](vpn-gateway-point-to-site-create.md).

##Acceso remoto seguro

Los datos almacenados en la nube deben tener suficientes medidas de seguridad habilitadas para evitar vulnerabilidades de seguridad y mantener la confidencialidad e integridad durante el tránsito. Esto incluye controles de red que se enlazan con los mecanismos de administración de acceso e identidades auditables basados en las directivas de una organización.

La tecnología de cifrado integrada permite cifrar las comunicaciones dentro y entre las implementaciones, entre las regiones de Azure y de Azure a centros de datos locales. El acceso de administrador a máquinas virtuales mediante [sesiones de escritorio remoto](virtual-machines-log-on-windows-server.md), [Windows PowerShell remoto](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx) y el [Portal de administración de Azure](https://azure.microsoft.com/overview/preview-portal/) está siempre cifrado.

Para ampliar su centro de datos local a la nube de forma segura, Azure proporciona [VPN de sitio a sitio](vpn-gateway-create-site-to-site-rm-powershell.md) y [VPN de punto a sitio](vpn-gateway-point-to-site-create.md), así como vínculos dedicados con [ExpressRoute](expressroute-introduction.md) (las conexiones a redes virtuales de Azure a través de VPN se cifran).

### Cómo implementa Azure el acceso remoto seguro

Siempre se deben autenticar las conexiones al Portal de Azure y requieren SSL/TLS. Puede configurar certificados de administración para habilitar la administración segura. Los protocolos seguros estándar del sector [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) e [IPsec](https://en.wikipedia.org/wiki/IPsec) son totalmente compatibles.

[Azure ExpressRoute](expressroute-introduction.md) permite crear conexiones privadas entre los centros de datos y la infraestructura de Azure que están en su entorno local o de un entorno de ubicación compartida. Las conexiones ExpressRoute no pasan por la red pública de Internet. Ofrecen más confiabilidad, velocidades más rápidas, latencias más bajas y mayor seguridad que los vínculos típicos basados en Internet. En algunos casos, el uso de conexiones de ExpressRoute para transferir datos entre dispositivos locales y Azure también puede aportar beneficios económicos importantes.

##Registro y supervisión

Azure proporciona un registro autenticado de eventos relevantes para la seguridad que genera una pista de auditoría y está diseñada para resistir manipulaciones. Esto incluye información del sistema, como registros de eventos de seguridad en las máquinas virtuales de infraestructura de Azure y Azure AD. La supervisión de eventos de seguridad incluye la recopilación de eventos, como cambios en direcciones IP del servidor DNS o DHCP, intentos de acceso a los puertos, protocolos o direcciones IP que están bloqueadas por diseño, cambios en la configuración del firewall o la directiva de seguridad, creación de cuentas o grupos, procesos inesperados o instalación de controladores.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started\sec-azgsfig5.PNG)

La grabación de registros de auditoría de accesos de usuario con privilegios y actividades, intentos de acceso autorizados y no autorizados, excepciones del sistema e información de eventos de seguridad se conservan durante un período de tiempo establecido. El mantenimiento de los registros depende de usted, ya que usted configura la recopilación y mantenimiento de los registros de acuerdo con sus propios requisitos.

### Cómo implementa Azure el registro y la supervisión

Azure implementa los agentes de administración (MA) y el agente Monitor de seguridad de Azure (ASM) para cada proceso, almacenamiento o nodo de tejido en la administración, ya sean nativos o virtuales. Cada agente de administración está configurado para autenticarse en una cuenta de almacenamiento del equipo de servicio con un certificado obtenido en el almacén de certificados de Azure y reenvía el diagnóstico preconfigurados y los datos de eventos a la cuenta de almacenamiento. Estos agentes no se implementan en las máquinas virtuales de los clientes.

Los administradores de Azure tienen acceso a los registros a través de un portal web para el acceso autenticado y controlado a los registros. Un administrador puede analizar, filtrar, correlacionar y analizar los registros. Las cuentas de almacenamiento del equipo de servicio de Azure para los registros están protegidas contra el acceso directo del administrador para ayudar a evitar la alteración del registro.

Microsoft recopila registros de dispositivos de red mediante el protocolo Syslog y de servidores de host mediante los servicios de recopilación de auditorías (ACS) de Microsoft. Estos registros se colocan en una base de datos de registro desde la que se generan alertas para los eventos sospechosos directamente a un administrador de Microsoft. El administrador puede acceder a estos registros y analizarlos.

[Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) es una característica de Azure que permite recopilar datos de diagnóstico de una aplicación que se está ejecutando en Azure. Se trata de datos de diagnóstico para depurar y solucionar problemas, medir el rendimiento, supervisar el uso de los recursos, analizar el tráfico y planificar la capacidad y realizar auditorías. Después de recopilar los datos de diagnóstico, se pueden transferir a una cuenta de almacenamiento de Azure para la persistencia. Las transferencias pueden se programadas o a petición. El artículo [Administración de registros de auditoría y seguridad de Microsoft Azure](azure-security-audit-log-management.md) proporciona información acerca de cómo puede recopilar esta información y analizarla.

##Mitigación de amenazas

Además del aislamiento, el cifrado y el filtrado, Azure emplea a una serie de mecanismos y procesos de mitigación de amenazas para proteger la infraestructura y los servicios. Estos incluyen tecnologías y controles internos usados para detectar y corregir amenazas avanzadas como DDoS, aumento de privilegios y [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Los controles de seguridad y la administración de riesgos implantados por Microsoft para proteger su infraestructura en la nube reducen el riesgo de incidentes de seguridad. Sin embargo, en caso de que se produzca un incidente, el equipo de administración de incidentes de seguridad (SIM) del equipo de los servicios en línea de seguridad y cumplimiento de Microsoft (OSSC) está listo para responder 24 horas al día los 7 días de la semana.

### Cómo implementa Azure la mitigación de amenazas

Azure tiene controles de seguridad para implementar la mitigación de amenazas y ayudar a los clientes a mitigar posibles amenazas en sus entornos. La lista siguiente resume las capacidades de mitigación de amenazas ofrecidas por Azure:

-   [Azure Anti-Malware](azure-security-antimalware.md) está habilitado de forma predeterminada en todos los servidores de infraestructura. Opcionalmente, puede habilitarlo en sus propias máquinas virtuales.

-   Microsoft mantiene una supervisión continua a través de los servidores, las redes y las aplicaciones para detectar las amenazas y evitar vulnerabilidades de seguridad. Las alertas automatizadas notifican a los administradores acerca de comportamientos anómalos, lo que les permite tomar medidas correctivas contra amenazas internas y externas.

-   Tiene la opción de implementar soluciones de seguridad de terceros en de sus suscripciones, como los firewalls de aplicaciones web de [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).

-   El enfoque de Microsoft para pruebas de penetración incluye "[formación de equipos de Red](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)", lo que implica que los profesionales de seguridad de Microsoft atacan sistemas de producción activos (no de clientes) en Azure para probar las defensas contra amenazas persistentes reales y avanzadas.

-   Los sistemas de implementación integrados administran la distribución e instalación de revisiones de seguridad en la plataforma Azure.

##Pasos siguientes

[Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/)

[Blog del equipo de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/)

[Centro de respuesta de seguridad de Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

[Blog de Active Directory](http://blogs.technet.com/b/ad/)

<!---HONumber=AcomDC_1217_2015-->