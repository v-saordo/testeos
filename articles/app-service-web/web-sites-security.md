<properties
	pageTitle="Protección de una aplicación en el Servicio de aplicaciones de Azure"
	description="Aprenda a proteger una aplicación web, un back-end de aplicación móvil o una aplicación de API en el Servicio de aplicaciones de Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>


#Protección de una aplicación en el Servicio de aplicaciones de Azure

Este artículo sirve de introducción a la protección de su aplicación web, back-end de aplicación móvil o aplicación de API en el Servicio de aplicaciones de Azure.

La seguridad en el Servicio de aplicaciones de Azure tiene dos niveles:

- **Seguridad de la infraestructura y la plataforma**: confía en que Azure dispone de los servicios que necesita para ejecutar aplicaciones de forma segura en la nube.
- **Seguridad de las aplicaciones**: necesita diseñar la aplicación en sí de forma segura. Esto incluye cómo integrarla con Azure Active Directory, cómo administrar los certificados y cómo asegurarse de poder comunicarse de forma segura con distintos servicios. 

#### Seguridad de la infraestructura y la plataforma
Dado que el Servicio de aplicaciones mantiene las máquinas virtuales de Azure, el almacenamiento, las conexiones de red, los marcos web, la administración y las características de integración, entre muchas otras cosas, se protege y refuerza de forma activa, además de someterla a controles y comprobaciones de cumplimiento exhaustivos de forma continua para garantizar que:

- Sus aplicaciones del Servicio de aplicaciones están aisladas de Internet y de los recursos de Azure de otros clientes.
- La comunicación de secretos (por ejemplo, cadenas de conexión) entre su aplicación del Servicio de aplicaciones y otros recursos de Azure (por ejemplo, Base de datos SQL) en un grupo de recursos permanezca dentro de Azure y no cruce ningún límite de la red. Los secretos siempre están cifrados.
- Toda la comunicación entre su aplicación del Servicio de aplicaciones y los recursos externos, como la administración de PowerShell, la interfaz de la línea de comandos, los SDK de Azure, las API de REST y las conexiones híbridas, se hayan cifrado correctamente.
- La administración ininterrumpida de amenazas proteja los recursos del Servicio de aplicaciones frente a malware, ataques por denegación de servicio distribuido (DDoS), ataques de tipo "Man in the middle" (MITM) y otras amenazas. 

Para más información sobre la seguridad de la infraestructura y la plataforma en Azure, consulte [Centro de confianza de Microsoft Azure](/support/trust-center/security/).

#### Seguridad de las aplicaciones

Aunque Azure se encarga de proteger la infraestructura y la plataforma en las que se ejecuta su aplicación, es responsabilidad suya proteger su propia aplicación. Es decir, debe desarrollar, implementar y administrar el código y el contenido de la aplicación de forma segura. De lo contrario, el código o el contenido de la aplicación pueden seguir siendo vulnerables frente a amenazas como:

- Inyección de código SQL
- Secuestro de sesión
- Ataque de scripts de sitios
- MITM en el nivel de aplicación
- DDoS en el nivel de aplicación

No es el objetivo de este documento abarcar una completa exposición de las consideraciones de seguridad para las aplicaciones basadas en web. Como punto de inicio para conocer más en profundidad la cuestión de la protección de su aplicación, consulte [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) [Proyecto de código abierto sobre seguridad en aplicaciones web (OWASP)], concretamente el proyecto de los [10 principales](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que enumera los 10 principales errores de seguridad en aplicaciones web según los miembros de OWASP.

## Realización de pruebas de penetración en la aplicación

Una de las maneras más fáciles de empezar a probar las vulnerabilidades en su aplicación del Servicio de aplicaciones consiste en usar la [integración con Tinfoil Security](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para realizar un análisis con un solo clic del nivel de vulnerabilidad de su aplicación. Puede ver los resultados de la prueba en un informe fácil de entender, y aprender a corregir cada vulnerabilidad con instrucciones detalladas.

Si prefiere realizar sus propias pruebas de penetración o desea usar otro conjunto de aplicaciones de análisis u otro proveedor, debe seguir el [proceso de aprobación de pruebas de penetración de Azure](https://security-forms.azure.com/penetration-testing/terms) y obtener aprobación previa para realizar las pruebas de penetración deseadas.

##<a name="https"></a> Protección de la comunicación con clientes

Si usa el nombre de dominio **\*.azurewebsites.net** que se crea para su aplicación del Servicio de aplicaciones, puede usar de inmediato HTTPS, ya que se proporciona un certificado SSL para todos los nombres de dominio **\*.azurewebsites.net**. Si el sitio utiliza un [nombre de dominio personalizado](web-sites-custom-domain-name.md), puede cargar un certificado SSL para [habilitar HTTPS](web-sites-configure-ssl-certificate.md) para el dominio personalizado.

Si habilita [HTTPS](https://en.wikipedia.org/wiki/HTTPS), puede contribuir a la protección frente a ataques MITM dirigidos a la comunicación entre la aplicación y sus usuarios.

## Protección de la capa de datos

El Servicio de aplicaciones se integra estrechamente con Base de datos SQL, de forma que todas las cadenas de conexión se cifran de modo generalizado y solo se descifran en la máquina virtual donde se ejecuta la aplicación *y* solamente cuando se ejecuta la aplicación. Además, Base de datos SQL de Azure incluye muchas características de seguridad para ayudar a proteger los datos de la aplicación frente a las amenazas de Internet, entre ellas [cifrado en reposo](https://msdn.microsoft.com/library/dn948096.aspx), [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx), [Enmascaramiento dinámico de datos](../sql-database/sql-database-dynamic-data-masking-get-started.md) y [Detección de amenazas](sql-database-threat-detection-get-started). Si tiene datos confidenciales o requisitos de cumplimiento, consulte [Protección de Bases de datos SQL](../sql-database/sql-database-security.md) para más información sobre cómo proteger los datos.

Si usa un proveedor de bases de datos de terceros, como ClearDB, debe consultar los procedimientos recomendados de seguridad directamente en la documentación del proveedor.

##<a name="develop"></a> Desarrollo e implementación seguros

### Perfiles de publicación y configuración de publicación

Al desarrollar aplicaciones, realizar tareas de mantenimiento o automatizar tareas mediante herramientas como **Visual Studio**, **Web Matrix**, **Azure PowerShell** o la **Interfaz de la línea de comandos de Azure (CLI de Azure)**, puede utilizar un archivo de *configuración de publicación* o un *perfil de publicación*. Ambos tipos de archivo lo autentican con Azure, y deberían estar protegidos para evitar el acceso no autorizado.

* Un archivo de **configuración de publicación** contiene

	* El identificador de suscripción a Azure

	* Un certificado de administración que le permite realizar tareas de administración para su suscripción *sin tener que proporcionar un nombre de cuenta o contraseña*.

* Un archivo de **perfil de publicación** contiene

	* Información para la publicación en su aplicación

Si usa una utilidad que emplea un archivo de configuración de publicación o un archivo de perfil de publicación, importe el archivo que contiene la configuración o el perfil de publicación en dicha utilidad y después **elimínelo**. Si debe conservar el archivo, por ejemplo para compartirlo con otros que trabajan en el proyecto, almacénelo en una ubicación segura, como un directorio *cifrado* con permisos restringidos.

Además, debería asegurarse de que se protegen las credenciales importadas. Por ejemplo, tanto **Azure PowerShell** como la **Interfaz de la línea de comandos de Azure (CLI de Azure)** almacenan la información importada en su **directorio particular** (*~* en Linux o sistemas OS X y */usuarios/sunombredeusuario* en sistemas Windows). Para conseguir una seguridad adicional, **cifre** estas ubicaciones mediante herramientas de cifrado disponibles para su sistema operativo.

### Valores de configuración y cadenas de conexión
Es habitual almacenar cadenas de conexión, credenciales de autenticación y otra información de tipo confidencial en archivos de configuración. Lamentablemente, estos archivos pueden resultar expuestos en su sitio web o podrían aparecer en búsquedas de un repositorio público, con lo que la información podría quedar al descubierto. Por ejemplo, una simple búsqueda en [GitHub](https://github.com) descubre innumerables archivos de configuración con secretos expuestos en los repositorios públicos.

Lo mejor es mantener esta información fuera de los archivos de configuración de la aplicación. El Servicio de aplicaciones le permite almacenar la información de configuración como parte del entorno en tiempo de ejecución en forma de **configuración de aplicación** y **cadenas de conexión**. Estos valores se exponen a su aplicación en el tiempo de ejecución a través de *variables de entorno* para la mayoría de lenguajes de programación. En el caso de las aplicaciones .NET, estos valores se insertan en la configuración de .NET en el tiempo de ejecución. Aparte de estos casos, estas opciones de configuración permanecerán cifradas a menos que las vea o configure mediante el [Portal de Azure](https://portal.azure.com) o utilidades tales como PowerShell o la CLI de Azure.

Si se almacena información de configuración en el Servicio de aplicaciones, el administrador de la aplicación puede bloquear la información confidencial para las aplicaciones de producción. Los desarrolladores pueden usar un conjunto independiente de valores de configuración para el desarrollo de aplicaciones y la configuración se puede sustituir automáticamente por la configuración en el Servicio de aplicaciones. Ni siquiera los desarrolladores necesitan saber los secretos configurados para la aplicación de producción. Para más información sobre cómo establecer la configuración de la aplicación y las cadenas de conexión en el Servicio de aplicaciones, consulte [Configuración de aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-configure.md).

### FTPS

El Servicio de aplicaciones de Azure proporciona a la aplicación acceso FTP seguro al sistema de archivos a través de **FTPS**. Esto le permite tener acceso seguro al código de la aplicación en la aplicación web, así como a los registros de diagnóstico. Se recomienda que use siempre FTPS en lugar de FTP.

Puede encontrar el vínculo FTPS para su aplicación con los siguientes pasos:

1. Abra el [Portal de Azure](https://portal.azure.com).
2. Seleccione **Examinar todo**.
3. En la hoja **Examinar**, seleccione **Servicios de aplicaciones**.
4. En la hoja **Servicios de aplicaciones**, seleccione la aplicación deseada.
5. En la hoja de la aplicación, seleccione **Toda la configuración**.
6. Desde la hoja **Configuración**, seleccione **Propiedades**.
7. Se proporcionan los vínculos FTP y FTPS en la hoja **Configuración**. 

Para obtener más información acerca de FTPS, consulte [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## Pasos siguientes

Para obtener más información sobre la seguridad en la plataforma de Azure, aprender cómo notificar un **abuso o incidente de seguridad** o informar a Microsoft de que va a realizar una **prueba de penetración** en su sitio, consulte la sección de seguridad del [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Para obtener información sobre los archivos **web.config** o **applicationhost.config** en aplicaciones del Servicio de aplicaciones, consulte [ Opciones de configuración desbloqueadas en aplicaciones web del Servicio de aplicaciones de Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para más datos sobre la información de registro para aplicaciones del Servicio de aplicaciones, que puede resultar útil para detectar de ataques, consulte [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado

* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!----HONumber=AcomDC_0211_2016-->