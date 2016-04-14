<properties 
	pageTitle="Integración de directorios entre Active Directory y Azure Multi-Factor Authentication" 
	description="Se trata de la página Azure Multi-Factor Authentication que describe cómo integrar Servidor Azure Multi-Factor Authentication con Active Directory para poder sincronizar los directorios." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Integración de directorios entre Servidor Azure MFA y Active Directory

La sección Integración de directorios permite configurar el servidor para integrarlo con Active Directory u otro directorio LDAP. Permite configurar atributos para que coincidan con el esquema de directorios y establecer la sincronización automática de los usuarios.

## Settings
De forma predeterminada, Servidor Azure Multi-Factor Authentication está configurado para importar o sincronizar usuarios desde Active Directory. La pestaña permite invalidar el comportamiento predeterminado y enlazar a un directorio LDAP diferente, a un directorio ADAM o a un controlador de dominio específico de Active Directory. También sirve para el uso de autenticación LDAP en el proxy LDAP o para el enlace LDAP como destino RADIUS, autenticación previa para autenticación de IIS o autenticación principal para el Portal de usuarios. En la tabla siguiente se describen las configuraciones individuales.

![Configuración](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Característica | Descripción |
| ------- | ----------- |
| Usar Active Directory | Seleccione la opción Usar Active Directory para usar Active Directory para la importación y sincronización. Esta es la configuración predeterminada. <br>Nota: el equipo debe estar unido a un dominio y es necesario iniciar sesión con una cuenta de dominio para que la integración con Active Directory funcione correctamente. |
| Incluir dominios de confianza | Active la casilla Incluir dominios de confianza para que el agente intente conectarse a dominios de confianza para el dominio actual, otro dominio del bosque o dominios implicados en una confianza de bosque. Si no está importando o sincronizando usuarios desde los dominios de confianza, desactive la casilla para mejorar el rendimiento. El valor predeterminado es activado. |
| Usar la configuración LDAP específica | Seleccione esta opción para usar la configuración LDAP especificada para la importación y sincronización. Nota: cuando se selecciona Usar la configuración LDAP específica, la interfaz de usuario cambia las referencias de Active Directory a LDAP. |
| Botón Editar | El botón Editar permite modificar los valores actuales de la configuración LDAP. |
| Usar consultas de ámbito de atributo | Indica si se deben usar consultas de ámbito de atributo. Las consultas de ámbito de atributo permiten unas búsquedas eficientes de registros calificados de directorio basándose en las entradas otro atributo de registro. Servidor Azure Multi-Factor Authentication usa las consultas de ámbito de atributo para consultar de forma eficiente a los usuarios que son miembros de un grupo de seguridad. <br>Nota: existen algunos casos donde se admiten las consultas de ámbito de atributo pero no deberían usarse. Por ejemplo, Active Directory puede tener problemas con las consultas de ámbito de atributo cuando un grupo de seguridad contiene miembros de más de un dominio. En este caso, debe desactivarse la casilla. |

En la tabla siguiente se describen las opciones de configuración LDAP.

| Característica | Descripción |
| ------- | ----------- |
| Server | Escriba el nombre de host o dirección IP del servidor que ejecuta el directorio LDAP. También puede especificar un servidor de copia de seguridad separado por un punto y coma. <br>Nota: cuando el tipo de enlace es SSL, se requiere generalmente un nombre de host completo. |
| DN base | Escriba el nombre distintivo del objeto de directorio base desde el que se iniciarán todas las consultas de directorio. Por ejemplo, dc=abc,dc=com. |
| Tipo de enlace - Consultas | Seleccione el tipo de enlace apropiado al enlazar para buscar en el directorio LDAP. Se utiliza para importaciones, sincronización y resolución de nombres de usuario. <br><br>Anónimo: se realizará un enlace anónimo. No se utilizarán DN de enlace ni Contraseña de enlace. Esto solo funcionará si el directorio LDAP permite el enlace anónimo y los permisos autorizan la consulta de los registros y atributos correspondientes. <br><br>Simple: DN de enlace y Contraseña de enlace se pasarán como texto sin formato para enlazar con el directorio LDAP. Esto debería utilizarse solo a modo de prueba para verificar que se puede tener acceso al servidor y que la cuenta de enlace tiene el acceso adecuado. Es su lugar, se recomienda usar SSL una vez instalado el certificado adecuado. <br><br>SSL: DN de enlace y Contraseña de enlace se cifrarán mediante SSL para enlazar con el directorio LDAP. Esto requiere que esté instalado localmente un certificado en el que confíe el directorio LDAP. <br><br>Windows: Nombre de usuario de enlace y Contraseña de enlace se utilizarán para conectarse de forma segura a un controlador de dominio de Active Directory o a un directorio ADAM. Si se deja en blanco Nombre de usuario de enlace, se usará la cuenta del usuario que ha iniciado sesión para enlazar. |
| Tipo de enlace: autenticaciones | Seleccione el tipo de enlace apropiado que se usa al realizar la autenticación de enlace LDAP. Consulte las descripciones de tipos de enlace en Tipo de enlace - Consultas. Por ejemplo, esto permite usar el enlace Anónimo para consultas mientras que el enlace SSL se puede utilizar para proteger autenticaciones de enlace LDAP. |
| DN de enlace o Nombre de usuario de enlace | Escriba el nombre distintivo del registro de usuario para la cuenta que se va a usar al enlazar con el directorio LDAP.<br><br>El nombre distintivo de enlace solo se utiliza cuando el tipo de enlace es Simple o SSL. <br><br>Escriba el nombre de usuario de la cuenta de Windows que va a usar al enlazar con el directorio LDAP cuando el tipo de enlace es Windows. Si se deja en blanco, se usará la cuenta del usuario que ha iniciado sesión para enlazar. |
| Contraseña de enlace | Escriba la contraseña de enlace para DN de enlace o el nombre de usuario que se usa para enlazar con el directorio LDAP. Para configurar la contraseña para el servicio AdSync del Servidor Multi-Factor Auth, debe estar habilitada la sincronización y el servicio debe estar ejecutándose en el equipo local. La contraseña se guardará en usuarios y contraseñas almacenados en Windows en la cuenta en la que se está ejecutando el servicio AdSync de Servidor Multi-Factor Authentication. La contraseña también se guardará en la cuenta en la que se ejecuta la interfaz de usuario de Servidor Multi-Factor Authentication y en la cuenta en la que se ejecuta el servicio Servidor Multi-Factor Authentication. <br><br> Nota: puesto que la contraseña se almacena solo en usuarios y contraseñas almacenados en Windows del servidor local, este paso deberá realizarse en cada Servidor Multi-Factor Authentication que necesite tener acceso a la contraseña. |
| Límite de tamaño de consulta | Especifique el límite de tamaño para el número máximo de usuarios que devolverá una búsqueda de directorio. Este límite debe coincidir con la configuración en el directorio LDAP. Para búsquedas grandes en las que no se admite paginación, la importación y sincronización intentarán recuperar usuarios por lotes. Si el límite de tamaño especificado aquí es mayor que el límite configurado en el directorio LDAP, pueden faltar algunos usuarios. |
| Botón Probar | Haga clic en el botón Probar para probar el enlace con el servidor LDAP. <br><br>Nota: no es necesario que la opción Usar la configuración LDAP específica esté seleccionada para probar el enlace. Esto permite probar el enlace antes de usar la configuración LDAP. |

## Filtros
Los filtros permiten establecer criterios para calificar los registros al realizar una búsqueda de directorio. Al establecer el filtro, puede restringir el ámbito de los objetos que desea sincronizar.

![Filtros](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure Multi-Factor Authentication presenta las tres opciones siguientes.

- **Filtro de contenedor**: especifique los criterios de filtro que se utilizan para calificar los registros de contenedor al realizar una búsqueda de directorio. Para Active Directory y ADAM, se suele usar (|(objectClass=organizationalUnit)(objectClass=container)). Para otros directorios LDAP, se deben usar criterios de filtro que califiquen cada tipo de objeto de contenedor en función del esquema de directorios. <br>Nota: si se deja en blanco, se usará ((objectClass=organizationalUnit)(objectClass=container)) de forma predeterminada.

- **Filtro de grupo de seguridad**: especifique los criterios de filtro que se utilizan para calificar los registros de grupo de seguridad al realizar una búsqueda de directorio. Para Active Directory y ADAM, se suele usar (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)). Para otros directorios LDAP, se deben usar criterios de filtro que califiquen cada tipo de objeto de grupo de seguridad en función del esquema de directorios. <br>Nota: si se deja en blanco, se usará (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) de forma predeterminada.

- **Filtro de usuarios**: especifique los criterios de filtro que se utilizan para calificar los registros de usuario al realizar una búsqueda de directorio. Para Active Directory y ADAM, se suele utilizar (&(objectClass=user)(objectCategory=person)). Para otros directorios LDAP, se debe usar (objectClass=inetOrgPerson) o similar en función del esquema de directorios. <br>Nota: si se deja en blanco, se usará (&(objectCategory=person)(objectClass=user)) de forma predeterminada.

## Atributos
Los atributos se pueden personalizar según sea necesario para un directorio específico. Esto permite agregar atributos personalizados y ajustar la sincronización solo para los atributos que necesite. El valor de cada campo de atributo debe ser el nombre del atributo tal como se define en el esquema de directorios. En la tabla siguiente puede obtener información adicional.

![Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Característica | Descripción |
| ------- | ----------- |
| Identificador único | Escriba el nombre del atributo que actúa como identificador único de los registros de contenedor, de grupo de seguridad y de usuario. En Active Directory, suele ser objectGUID. En otras implementaciones LDAP, puede ser entryUUID o similar. El valor predeterminado es objectGUID. |
| ... Botones ... (Seleccionar atributo) | Cada campo de atributo tiene un botón "..." al lado que mostrará el cuadro de diálogo Seleccionar atributo para poder seleccionar un atributo de una lista. <br><br>Cuadro de diálogo Seleccionar atributo.<br><br>Nota: los atributos pueden especificarse manualmente y no tienen que coincidir con un atributo de la lista de atributos. |
| Tipo de identificador único | Seleccione el tipo de atributo de identificador único. En Active Directory, el atributo objectGUID es de tipo GUID. En otras implementaciones LDAP, puede ser de tipo matriz de bytes ASCII o cadena. El valor predeterminado es GUID. <br><br>Nota: es importante establecer esto correctamente ya que se hace referencia a los elementos de sincronización por su identificador único y el tipo de identificador único se usa para buscar el objeto directamente en el directorio. El establecimiento de este valor en cadena cuando el directorio almacena realmente el valor como matriz de bytes de caracteres ASCII impedirá que la sincronización funcione correctamente. |
| Nombre distintivo | Escriba el nombre del atributo que contiene el nombre distintivo para cada registro. En Active Directory, suele ser distinguishedName. En otras implementaciones LDAP, puede ser entryDN o similar. El valor predeterminado es distinguishedName. <br><br>Nota: si no existe un atributo que contiene únicamente el nombre distintivo, puede utilizar el atributo adspath. La parte "LDAP://<server>/" de la ruta de acceso se quitará automáticamente y dejará solo el nombre distintivo del objeto. |
| Nombre del contenedor | Escriba el nombre del atributo que contiene el nombre en un registro de contenedor. El valor de este atributo se mostrará en la jerarquía de contenedores al importar desde Active Directory o agregar elementos de sincronización. El valor predeterminado es name. <br><br>Nota: si distintos contenedores usan atributos diferentes para sus nombres, se pueden especificar varios atributos de nombres de contenedor separados por punto y coma. Se utilizará el primer atributo de nombre de contenedor que se encuentre en un objeto de contenedor para mostrar su nombre. |
| Nombre de grupo de seguridad | Escriba el nombre del atributo que contiene el nombre en un registro de grupo de seguridad. El valor de este atributo se mostrará en la lista de grupos de seguridad al importar desde Active Directory o agregar elementos de sincronización. El valor predeterminado es name. |
| Usuarios | Los atributos siguientes se usan para buscar, mostrar, importar y sincronizar información de usuario desde el directorio. |
| Nombre de usuario | Escriba el nombre del atributo que contiene el nombre de usuario en un registro de usuario. El valor de este atributo se usará como nombre de usuario de Servidor Multi-Factor Authentication. Puede especificar un segundo atributo como copia de seguridad del primero. El segundo atributo solo se usará si el primer atributo no contiene ningún valor para el usuario. Los valores predeterminados son userPrincipalName y sAMAccountName. |
| Nombre | Escriba el nombre del atributo que contiene el nombre en un registro de usuario. El valor predeterminado es givenName. |
| Apellidos | Escriba el nombre del atributo que contiene los apellidos en un registro de usuario. El valor predeterminado es sn. |
| Dirección de correo electrónico | Escriba el nombre del atributo que contiene la dirección de correo electrónico en un registro de usuario. La dirección de correo electrónico se usará para enviar al usuario mensajes de correo electrónico de bienvenida y de actualización. El valor predeterminado es mail. |
| Grupo de usuarios | Escriba el nombre del atributo que contiene el grupo de usuarios en un registro de usuario. Se pueden usar un grupo de usuarios para filtrar usuarios en el agente y en informes en el Portal de administración de Servidor Multi-Factor Authentication. |
| Descripción | Escriba el nombre del atributo que contiene la descripción en un registro de usuario. La descripción solo se utiliza para realizar búsquedas. El valor predeterminado es description. |
| Idioma de llamadas de voz | Escriba el nombre del atributo que contiene el nombre abreviado del idioma que se utilizará para llamadas de voz para el usuario. |
| Idioma de texto de SMS | Escriba el nombre del atributo que contiene el nombre abreviado del idioma que se utilizará para los mensajes de texto de SMS para el usuario. |
| Idioma de la aplicación de teléfono | Escriba el nombre del atributo que contiene el nombre abreviado del idioma que se utilizará para los mensajes de texto de la aplicación de teléfono para el usuario. |
| Idioma de token OATH | Escriba el nombre del atributo que contiene el nombre abreviado del idioma que se utilizará para los mensajes de texto de token OATH para el usuario. |
| Teléfonos | Los siguientes atributos se utilizan para importar o sincronizar números de teléfono de usuario. Si no se especifica un nombre de atributo para el tipo de teléfono, este no estará disponible al importar desde Active Directory o agregar elementos de sincronización. |
| Business | Escriba el nombre del atributo que contiene el número de teléfono profesional en un registro de usuario. El valor predeterminado es telephoneNumber. |
| Inicio | Escriba el nombre del atributo que contiene el número de teléfono particular en un registro de usuario. El valor predeterminado es homePhone. |
| Buscapersonas | Escriba el nombre del atributo que contiene el número de buscapersonas en un registro de usuario. El valor predeterminado es pager. |
| Móvil | Escriba el nombre del atributo que contiene el número de teléfono móvil en un registro de usuario. El valor predeterminado es mobile. |
| Fax | Escriba el nombre del atributo que contiene el número de fax en un registro de usuario. El valor predeterminado es facsimileTelephoneNumber. |
| Teléfono IP | Escriba el nombre del atributo que contiene el número de teléfono IP en un registro de usuario. El valor predeterminado es ipPhone. |
| Personalizado | Escriba el nombre del atributo que contiene un número de teléfono personalizado en |
| | un registro de usuario. El valor predeterminado es en blanco. |
| Extensión | Escriba el nombre del atributo que contiene la extensión del número de teléfono en un registro de usuario. El valor del campo de extensión se usará como extensión solo para el número de teléfono principal. El valor predeterminado es en blanco. <br><br>Nota: si no se especifica el atributo de extensión, las extensiones se pueden incluir como parte del atributo del teléfono. La extensión debe ir precedida de una 'x' para que se pueda analizar. Por ejemplo, 555-123-4567 x890 sería el número de teléfono 555-123-4567 y la extensión 890. |
| Botón Restaurar valores predeterminados | Haga clic en el botón Restaurar valores predeterminados para que todos los atributos recuperen sus valores predeterminados. Los valores predeterminados deben funcionar correctamente con el esquema normal de Active Directory o de ADAM. |

Para editar los atributos, haga simplemente clic en el botón Editar de la pestaña Atributos. Aparecerá una ventana que permite editar los atributos.

![Editar atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## Sincronización
La sincronización mantiene la base de datos de usuarios de Azure Multi-Factor sincronizada con los usuarios de Active Directory o de otro directorio de protocolo ligero de acceso a directorios (LDAP). El proceso es similar a importar usuarios manualmente desde Active Directory pero realiza un sondeo periódico para que se procesen los cambios de usuarios y de grupos de seguridad de Active Directory. También sirve para deshabilitar o quitar usuarios eliminados de un grupo de seguridad o contenedor y quitar usuarios eliminados de Active Directory.

El servicio ADSync de Multi-Factor Authentication es un servicio de Windows que realiza el sondeo periódico de Active Directory. No se debe confundirse esto con Sincronización de Azure AD o Azure AD Connect. ADSync de Multi-Factor Authentication, aunque se basa en una base de código similar, es específico para Servidor Azure Multi-Factor Authentication. Se instala en un estado detenido y se inicia mediante el servicio Servidor Multi-Factor Authentication cuando se configura para ejecutarse. Si tiene una configuración de Servidor Multi-Factor Authentication de varios servidores, ADSync de Multi-Factor Authentication solo puede ejecutarse en un único servidor.

El servicio AdSync de Multi-Factor Authentication usa la extensión de servidor DirSync LDAP proporcionada por Microsoft para realizar sondeos eficientes de los cambios. Este llamador de control DirSync debe tener el derecho "directory get changes" y el derecho de control de acceso extendido DS-Replication-Get-Changes. De forma predeterminada, estos derechos se asignan a las cuentas de administrador y de sistema local en los controladores de dominio. El servicio AdSync de Multi-Factor Authentication está configurado para ejecutarse como cuenta de sistema local de forma predeterminada. Por lo tanto, es más sencillo ejecutar el servicio en un controlador de dominio. El servicio puede ejecutarse como una cuenta con menos permisos si se configura para realizar siempre una sincronización completa. Esto es menos eficaz pero requiere menos privilegios de cuenta.

Si se ha configurado para usar LDAP y el directorio LDAP admite el control DirSync, la realización de un sondeo para cambios de usuarios y de grupos de seguridad funcionará igual que lo hace con Active Directory. Si el directorio LDAP no admite el control DirSync, se realizará una sincronización completa durante cada ciclo.

![Sincronización](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Utilice la tabla siguiente para obtener información adicional sobre cada una de las opciones de configuración individuales en la pestaña Sincronización.

| Característica | Descripción |
| ------- | ----------- |
| Habilitar sincronización con Active Directory | Cuando está activado, se iniciará el servicio Servidor Multi-Factor Authentication para realizar sondeos periódicos de los cambios para Active Directory. <br><br>Nota: se debe agregar al menos un elemento de sincronización y ejecutar Sincronizar ahora antes de que el servicio Servidor Multi-Factor Authentication empiece a procesar los cambios. |
| Sincronizar cada | Especifique el intervalo de tiempo que el servicio Servidor Multi-Factor Authentication esperará entre el sondeo y el procesamiento de los cambios. <br><br>Nota: el intervalo especificado es el tiempo entre el inicio de cada ciclo. Si el tiempo de procesamiento de los cambios supera el intervalo, el servicio volverá a realizar un sondeo de inmediato. |
| Quitar usuarios que ya no estén en Active Directory | Cuando está activado, el servicio Servidor Multi-Factor Authentication procesará los marcadores de exclusión de usuarios eliminados de Active Directory y quitará el usuario relacionado de Servidor Multi-Factor Authentication. |
| Realizar siempre una sincronización completa | Cuando está activado, el servicio Servidor Multi-Factor Authentication realizará siempre una sincronización completa. Si está desactivado, el servicio Servidor Multi-Factor Authentication realizará una sincronización incremental consultando solo los usuarios que han cambiado. El valor predeterminado es desactivado. <br><br>Nota: si está desactivado, una sincronización incremental solo puede realizarse cuando el directorio admite el control DirSync y la cuenta que se usa para enlazar al directorio tiene los permisos adecuados para realizar consultas incrementales de DirSync. Si la cuenta no tiene los permisos adecuados o hay varios dominios implicados en la sincronización, se recomienda realizar una sincronización completa. |
| Requerir autorización del administrador cuando se quiten o deshabiliten más de X usuarios | Los elementos de sincronización se pueden configurar para deshabilitar o quitar los usuarios que ya no son miembros de un contenedor o de un grupo de seguridad del elemento. Como medida de seguridad, puede ser necesaria la autorización del administrador cuando el número de usuarios que se van a deshabilitar o quitar supera un umbral. Cuando está activado, se requerirá autorización para un umbral especificado. El valor predeterminado es 5 y el intervalo es de 1 a 999. <br><br> La autorización se facilita enviando primero una notificación por correo electrónico a los administradores. La notificación por correo electrónico proporciona instrucciones para revisar y autorizar la deshabilitación y eliminación de usuarios. Cuando se inicia la interfaz de usuario de Servidor Multi-Factor Authentication, solicitará la autorización. |

El botón **Sincronizar ahora** permite ejecutar una sincronización completa de los elementos de sincronización especificados. Es necesaria una sincronización completa cuando se agregan, modifican, quitan o reordenan elementos de sincronización. También es necesaria antes de que el servicio AdSync de Multi-Factor Authentication esté operativo, ya que establece el punto de partida desde el cual el servicio realizará sondeos de los cambios incrementales. Si se han realizado cambios en elementos de sincronización y no se ha ejecutado una sincronización completa, se le solicitará Sincronizar ahora al desplazarse a otra sección o al cerrar la interfaz de usuario.

El botón **Quitar** permite al administrador eliminar uno o más elementos de sincronización de la lista de elementos de sincronización de Servidor Multi-Factor Authentication.

>[AZURE.WARNING]Una vez quitado un registro de elemento de sincronización, no se puede recuperar. Tendrá que volver a agregar el registro del elemento de sincronización si lo eliminó por error.

El elemento o elementos de sincronización se quitaron de Servidor Multi-Factor Authentication. El servicio Servidor Multi-Factor Authentication deja de procesar los elementos de sincronización.

Los botones Subir y Bajar permiten al administrador cambiar el orden de los elementos de sincronización. El orden es importante, ya que el mismo usuario puede ser miembro de más de un elemento de sincronización (por ejemplo, un contenedor y un grupo de seguridad). La configuración aplicada al usuario durante la sincronización procederá del primer elemento de sincronización de la lista a la que está asociado el usuario. Por lo tanto, los elementos de sincronización deben colocarse en orden de prioridad.

>[AZURE.TIP]Se debe realizar una sincronización completa después de quitar elementos de sincronización. Es necesario realizar una sincronización completa después de ordenar elementos de sincronización. Haga clic en el botón Sincronizar ahora para realizar una sincronización completa.

## Servidores Multi-Factor Authentication
Se pueden configurar Servidores Multi-Factor Authentication adicionales para que actúen como proxy RADIUS de copia de seguridad, proxy LDAP o para autenticación de IIS. La configuración de sincronización se compartirá entre todos los agentes. Sin embargo, solo uno de estos agentes puede tener Servidor Multi-Factor Authentication en ejecución. Esta pestaña permite seleccionar Servidor Multi-Factor Authentication que debe habilitarse para la sincronización.

![Servidores Multi-Factor Authentication](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)

<!---HONumber=AcomDC_0224_2016-->