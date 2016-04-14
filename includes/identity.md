La administración de la identidad es igual de importante en la nube pública que en las instalaciones locales. Para ayudar en esto, Azure es compatible con diferentes tecnologías de identidad en la nube. Entre ellas se incluyen las siguientes:

- Puede ejecutar Windows Server Active Directory (conocido normalmente como AD) en la nube con máquinas virtuales creadas con máquinas virtuales de Azure. Este enfoque es recomendable cuando usa Azure para ampliar el centro de datos local en la nube.

- Puede usar Azure Active Directory para proporcionar a los usuarios un inicio de sesión único en aplicaciones de Software como servicio (SaaS). Por ejemplo, Microsoft Office 365 usa esta tecnología y las aplicaciones que se ejecutan en Azure u otras plataformas en la nube también pueden usarla.

- Las aplicaciones que se ejecutan en la nube o en local pueden usar el control de acceso de Azure Active Directory para permitir que los usuarios inicien sesión mediante identidades de Facebook, Google, Microsoft y otros proveedores de identidades.


En este artículo se describen las tres opciones.

## Tabla de contenido

- [Ejecución de Windows Server Active Directory en máquinas virtuales](#adinvm)

- [Uso de Azure Active Directory](#ad)

- [Uso del control de acceso de Azure Active Directory](#ac)


## <a name="adinvm"></a>Ejecución de Windows Server Active Directory en máquinas virtuales

Ejecutar Windows Server AD en máquinas virtuales de Azure es muy parecido a hacerlo de manera local. La [ilustración 1](#fig1) muestra un ejemplo típico de su apariencia.

![Azure Active Directory en máquina virtual](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Ilustración 1: Windows Server Active Directory puede ejecutarse en máquinas virtuales de Azure conectadas al centro de datos local de una organización mediante la red virtual de Azure.

En el ejemplo que se muestra a continuación, Windows Server AD se ejecuta en máquinas virtuales creadas con máquinas virtuales de Azure, la tecnología IaaS de la plataforma. Esas máquinas virtuales, y algunas otras, se agrupan en una red virtual conectada a un centro de datos local mediante la red virtual de Azure. La red virtual modela un grupo de máquinas virtuales en la nube que interactúan con la red local a través de una conexión de red privada virtual (VPN). Esto permite que las máquinas virtuales de Azure parezcan otra subred en el centro de datos local. Como muestra la ilustración, dos de estas máquinas virtuales ejecutan controladores de dominio de Windows Server AD. Las otras máquinas virtuales de la red virtual pueden ejecutar aplicaciones, como SharePoint, o utilizarse para otro fin, como el desarrollo y la realización de pruebas. El centro de datos local también ejecuta dos controladores de dominio de Windows Server AD.

Existen varias opciones para conectar los controladores de dominio en la nube con los que se ejecutan localmente:

- Hacer que todos formen parte de un dominio de Active Directory.

- Crear dominios de AD independientes de manera local y en la nube que formen parte del mismo bosque.

- Crear bosques de AD en la nube y de forma local y, a continuación, conectar los bosques mediante confianzas entre bosques o Servicios de federación de Active Directory (AD FS) de Windows Server, que también pueden ejecutarse en máquinas virtuales en Azure.

Sea cual sea la elección, el administrador debe asegurarse de que las solicitudes de autenticación de los usuarios locales se dirigen a los controladores de dominio en la nube solo cuando sea necesario, ya que es posible que el vínculo a la nube sea más lento que las redes locales. Otro factor a tener en cuenta en la conexión de controladores de dominio locales y en la nube es el tráfico que genera la replicación. Los controladores de dominio en la nube se encuentran normalmente en su propio sitio de AD, lo que permite al administrador programar la frecuencia con la que se realiza la replicación. Azure cobra el tráfico de envío saliente de un centro de datos de Azure, aunque no los bytes de envío entrante, lo que puede afectar a las opciones de replicación del administrador. También cabe destacar que mientras Azure proporciona su propio soporte para el Sistema de nombres de dominio (DNS), al servicio le faltan las características necesarias para Active Directory (como soporte para registros de SRV y Dynamic DNS). Debido a esto, la ejecución de Windows Server AD en Azure requiere que se configuren sus propios servidores DNS en la nube.

Ejecutar Windows Server AD en máquinas virtuales de Azure es recomendable en distintas situaciones determinadas. A continuación se muestran algunos ejemplos:

- Si usa máquinas virtuales de Azure como una extensión de su propio centro de datos, debe ejecutar aplicaciones en la nube que necesiten controladores de dominio locales para controlar aspectos como las solicitudes de autenticación integrada en Windows o consultas LDAP. Por ejemplo, SharePoint interactúa normalmente con Active Directory, y cuando sea posible ejecutar una granja de SharePoint en Azure con un directorio local, la configuración de controladores de dominio en la nube mejorará notablemente el rendimiento. Es importante tener en cuenta que esto no es obligatorio. Sin embargo, muchas aplicaciones pueden ejecutarse correctamente en la nube solo mediante controladores de dominio locales.

- Supongamos que una sucursal lejana no dispone de los recursos para ejecutar sus propios controladores de dominio. Actualmente, los usuarios deben autenticar en los controles de dominio en el otro lado del mundo, por lo que el inicio de sesión es lento. La ejecución de Active Directory en Azure en un centro de datos de Microsoft cercano puede acelerar el inicio de sesión sin que sean necesarios más servidores en la sucursal.

- Una organización que usa Azure para la recuperación ante desastres puede conservar un conjunto reducido de máquinas virtuales activas en la nube, incluido un controlador de dominio. Por lo tanto, puede estar preparada para ampliar este sitio según corresponda y hacerse cargo de los errores en cualquier otro lugar.

Existen también otras posibilidades. Por ejemplo, en un centro de datos local no es obligatorio conectarse a Windows Server AD en la nube. Debe crear un bosque independiente en Azure si su intención es ejecutar una granja de SharePoint que ofrezca servicio, por ejemplo, a un determinado grupo de usuarios que iniciarían sesión de manera independiente con identidades basadas en la nube. La forma en la que use esta tecnología depende de cuáles sean sus objetivos. (Para obtener información más detallada sobre cómo usar Windows Server AD con Azure, [consulte aquí](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)).

## <a name="ad"></a>Uso de Azure Active Directory

A medida que las aplicaciones SaaS se vuelven más frecuentes, se plantea una pregunta obvia: ¿qué tipo de servicio de directorio deben usar estas aplicaciones basadas en la nube? La respuesta de Microsoft a esa pregunta es Azure Active Directory.

Existen dos opciones principales para usar este servicio de directorio en la nube:

- Las personas y las organizaciones que usan solo aplicaciones SaaS pueden confiar en Azure Active Directory como su único servicio de directorio.

- Las organizaciones que ejecutan Windows Server Active Directory pueden conectar su directorio local a Azure Active Directory y, a continuación, usarlo para ofrecer a sus usuarios un inicio de sesión único en aplicaciones SaaS.


En la [ilustración 2](#fig2) se muestra la primera de las dos opciones, en la que Azure Active Directory es lo único necesario.

![Azure Active Directory en máquina virtual](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Ilustración 2: Azure Active Directory ofrece a los usuarios de la organización un inicio de sesión único en las aplicaciones SaaS, incluido Office 365.

Como se muestra en la ilustración, Azure AD es un servicio multiinquilino. Esto significa que puede ofrecer asistencia simultáneamente a distintas organizaciones y almacenar la información de directorio sobre los usuarios en cada una de ellas. En este ejemplo, un usuario de la organización A está intentando obtener acceso a una aplicación SaaS. Esta aplicación puede ser parte de Office 365, como SharePoint Online, o de otra solución; las aplicaciones que no son de Microsoft también pueden usar esta tecnología. Puesto que Azure AD es compatible con el protocolo SAML 2.0, todo lo que necesita una aplicación es capacidad para interactuar con ese estándar industrial. De hecho, las aplicaciones que usan Azure AD pueden ejecutarse en cualquier centro de datos, no solo en un centro de datos de Azure.

El proceso comienza cuando el usuario obtiene acceso a una aplicación SaaS (paso 1). Para usar esta aplicación, el usuario debe presentar un token emitido por Azure AD.

Ese token contiene información que identifica al usuario y presenta una firma digital de Azure AD. Para obtener el token, el usuario realiza su propia autenticación en Azure AD mediante un nombre de usuario y una contraseña (paso 2). A continuación, Azure AD devuelve el token necesario (paso 3).

El token se envía después a la aplicación SaaS (paso 4), que valida la firma del token y usa su contenido (paso 5). Normalmente, la aplicación usará la información de identidad que contiene el token para decidir a qué información puede obtener acceso el usuario y si lo puede hacer de alguna otra manera.

Si la aplicación precisa más información sobre el usuario contenido en el token, puede solicitarla directamente en la API Graph de Azure AD (paso 6). En la versión inicial de Azure AD, el esquema de directorio es bastante simple: contiene solo los usuarios y los grupos y las relaciones entre ellos. Las aplicaciones pueden usar esa información para conocer las conexiones entre usuarios. Por ejemplo, supongamos que una aplicación quiere saber cuál es la decisión del administrador de usuarios para permitirle el acceso a algunos fragmentos de datos. Puede saberlo realizando una consulta a Azure AD a través de API Graph.

API Graph usa un protocolo RESTful ordinario, que facilita el uso desde la mayor parte de los clientes, incluidos los dispositivos móviles. La API también es compatible con las extensiones que define OData, ya que se agregan elementos como un lenguaje de consulta para permitir a los clientes obtener acceso a los datos de una forma más útil. (Para obtener más información sobre OData, consulte [Introducción a OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf)). Puesto que API Graph puede usarse para conocer las relaciones entre usuarios, permite a las aplicaciones conocer el gráfico social incrustado en el esquema de Azure AD de una organización determinada (por eso se llama API Graph, gráfico de la API). Para realizar la autenticación en Azure AD para las solicitudes de API Graph, la aplicación usa OAuth 2.0.

Si una organización no usa Windows Server Active Directory (no dispone de dominios o servidores locales) y confía solo en aplicaciones en la nube que usan Azure AD, el uso de solo este directorio en la nube proporcionará a un inicio de sesión único a todos los usuarios de la empresa. A pesar de que este escenario es más común cada día, la mayor parte de las organizaciones siguen usando dominios locales creados con Windows Server Active Directory. Azure AD también tiene un rol importante aquí, como se muestra en la [ilustración 3](#fig3).

![Azure Active Directory en máquina virtual](./media/identity/identity_03_AD.png) <a id="fig3"></a>Ilustración 3: una organización puede federar Windows Server Active Directory con Azure Active Directory para ofrecer a los usuarios un inicio de sesión único en aplicaciones SaaS.

En este escenario, un usuario de la organización B desea obtener acceso a una aplicación SaaS. Antes de conseguirlo, los administradores del directorio de la organización deben establecer una relación de federación con Azure AD con AD FS, como se muestra en la ilustración. Los administradores también deben configurar la sincronización de datos entre las soluciones Windows Server AD y Azure AD locales de la organización. De esta forma, se copia automáticamente la información de usuario y del grupo del directorio local a Azure AD. Observe lo que se permite: de hecho, la organización está ampliando el directorio local en la nube. La combinación de Windows Server AD y Azure AD de esta forma ofrece a la organización un servicio de directorio que puede administrarse como una única entidad a la vez que se mantiene un rastro en la nube y localmente.

Para usar Azure AD, el usuario primero inicia sesión en el dominio de Active Directory local de manera normal (paso 1). Cuando intenta obtener acceso a la aplicación SaaS (paso 2), el proceso de federación provoca que Azure AD le emita un token para esa aplicación (paso 3). (Para obtener más información sobre cómo funciona la federación, consulte [Identidad basada en notificaciones para Windows: escenarios y tecnologías](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx)). Como ocurría anteriormente, este token contiene información que identifica al usuario y se encuentra firmado digitalmente por Azure AD. El token se envía después a la aplicación SaaS (paso 4), que valida la firma del token y usa su contenido (paso 5). Como ocurre en el escenario anterior, la aplicación SaaS puede usar API Graph para obtener más información sobre el usuario si fuese necesario (paso 6).

Actualmente, Azure AD no es una sustitución completa para Windows Server AD local. Como ya hemos mencionado, el directorio en la nube cuenta con un esquema mucho más simple y al que también le faltan elementos como una directiva de grupo, la capacidad de almacenar información sobre máquinas y compatibilidad con LDAP. De hecho, no puede configurarse una máquina de Windows para permitir a los usuarios iniciar sesión en ella con otra solución que no sea Azure AD; este escenario no se admite. En este caso, entre los objetivos iniciales de Azure AD se encuentran dejar que los usuarios empresariales obtengan acceso a aplicaciones en la nube sin conservar un inicio de sesión independiente y liberar a los administradores de directorio de la sincronización manual del directorio local con cada aplicación SaaS que usa su organización. Sin embargo, con el paso del tiempo, se espera que este servicio de directorio en la nube se aplique a un mayor número de escenarios.

## <a name="ac"></a>Uso del control de acceso de Azure Active Directory

Las tecnologías de identidad basada en la nube pueden usarse para resolver una serie de problemas. Azure Active Directory puede ofrecer a los usuarios de la organización un inicio de sesión único, por ejemplo, en varias aplicaciones SaaS. Sin embargo, las tecnologías de identidad en la nube también pueden usarse de otras formas.

Supongamos, por ejemplo, que una aplicación desea permitir a sus usuarios iniciar sesión con tokens emitidos por varios *proveedores de identidades*. Hoy en día existe un gran número de proveedores de identidad, como Facebook, Google, Microsoft y otros, y las aplicaciones permiten normalmente a los usuarios iniciar sesión con una de estas identidades. ¿Por qué una aplicación se preocupa por conservar su propia lista de usuarios y contraseñas cuando puede confiar en identidades que ya existen? La aceptación de identidades existentes facilita la vida a los usuarios, que cuentan con un nombre de usuario y contraseña menos que recordar, y a los creadores de la aplicación, que no tienen que mantener sus propias listas de nombres de usuario y contraseñas.

Sin embargo, si el proveedor de identidad emite algún tipo de token, estos no son estándar; cada IdP tiene su propio formato. Además, la información en esos tokens tampoco es estándar. Una aplicación que desee aceptar tokens emitidos por ejemplo por Facebook, Google, y Microsoft, se enfrenta al reto de escribir código exclusivo para administrar cada uno de esos formatos diferentes.

¿Por qué hacer esto? ¿Por qué no crear un intermediario que pueda generar un formato de token único con una representación común de información de identidad? Este enfoque le haría la vida más fácil a los desarrolladores que crean aplicaciones, ya que ahora necesitarían administrar solo un tipo de token. El control de acceso de Azure Active Directory hace precisamente eso, proporciona un intermediario en la nube para trabajar con distintos tokens. La [ilustración 4](#fig4) muestra su funcionamiento.

![Azure Active Directory en máquina virtual](./media/identity/identity_04_IdentityProviders.png) <a id="fig4"></a>Ilustración 4: el control de acceso de Azure Active Directory facilita que las aplicaciones acepten tokens de identidad emitidos por distintos proveedores de identidades.

El proceso comienza cuando un usuario intenta obtener acceso a la aplicación desde un explorador. La aplicación lo redirige a un IdP que elija (y en el que la aplicación también confíe). Se autentica en este IdP, especificando un nombre de usuario y una contraseña (paso 1), y el IdP devuelve un token con información sobre él (paso 2).

Como se muestra en la ilustración, el control de acceso es compatible con una amplia variedad de IdP basados en la nube, incluidas las cuentas creadas por Google, Yahoo, Facebook, Microsoft (anteriormente Windows Live ID) y cualquier proveedor OpenID. También es compatible con identificaciones creadas con Azure Directory y, a través de la federación con AD FS, con Windows Server Active Directory. El objetivo es abarcar las identidades que se usan con más frecuencia hoy en día, ya que los IdP las emiten en la nube o localmente.

Una vez que el explorador del usuario disponga de un token del IdP seleccionado, envía ese token al control de acceso (paso 3). El control de acceso valida el token y se asegura de que efectivamente el IdP lo ha emitido. A continuación, crea un nuevo token de acuerdo con las reglas definidas para esa aplicación. Como ocurre con Azure Active Directory, el control de acceso es un servicio multiinquilino, pero los inquilinos son aplicaciones en lugar de organizaciones de clientes. Cada aplicación puede obtener su propio espacio de nombre, como se muestra en la ilustración, y puede definir varias reglas sobre autorización, etc.

Estas reglas permiten a cada administrador de la aplicación definir cómo deben transformarse los tokens de varios IdP en un token de control de acceso. Por ejemplo, si IdP distintos utilizan tipos diferentes para la representación de nombres de usuario, las reglas del control de acceso pueden transformar todo esto en un tipo de nombre de usuario común. A continuación, el control de acceso envía este nuevo token de nuevo al explorador (paso 4), que lo envía a la aplicación (paso 5). Una vez que dispone del token de control de acceso, la aplicación comprueba que ese token lo emitió realmente el control de acceso y, a continuación, usa la información que contiene (paso 6).

Aunque el proceso puede parecer un poco complicado, realmente facilita bastante la vida al creador de la aplicación. En lugar de administrar distintos tokens que contienen información diferente, la aplicación puede aceptar identidades emitidas por varios proveedores de identidad mientras recibe solo un token con información conocida. Además, en lugar de requerir que cada aplicación se configure para confiar en distintos IdP, el acceso de control mantiene esas relaciones de confianza; la aplicación solo tiene que confiar en él.

Cabe destacar que ninguno de estos aspectos del control de acceso es exclusivo de Windows; también puede usarlo una aplicación de Linux que haya aceptado solo identidades de Google y Facebook. A pesar de que el control de acceso forma parte de la familia de Azure Active Directory, puede considerarlo un servicio completamente distinto del que se describió en la sección anterior. Aunque ambas tecnologías trabajan con identidades, solucionan problemas totalmente diferentes (aunque Microsoft ha afirmado que espera integrar las dos en algún momento).

Trabajar con identidades es importante en prácticamente cualquier aplicación. El objetivo del control de acceso es hacer que los desarrolladores creen aplicaciones que acepten identidades de distintos proveedores de identidad de una manera más sencilla. Con la colocación de este servicio en la nube, Microsoft ha conseguido que esté disponible para cualquier aplicación que se ejecute en cualquier plataforma.

##Acerca del autor

David Chappell es el director de Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) en San Francisco (California).

<!---HONumber=Oct15_HO3-->