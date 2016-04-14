<properties 
	pageTitle="Servidor de autenticación LDAP y Azure Multi-Factor Authentication" 
	description="Se trata de la página Azure Multi-Factor Authentication que ayudará a implementar la autenticación LDAP y el servidor Azure Multi-Factor Authentication." 
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

# Servidor de autenticación LDAP y Azure Multi-Factor Authentication 


De forma predeterminada, Servidor Azure Multi-Factor Authentication está configurado para importar o sincronizar usuarios desde Active Directory. Sin embargo, se puede configurar para enlazar a directorios LDAP diferentes, por ejemplo, un directorio ADAM o un controlador de dominio de Active Directory específico. Cuando se configura para conectarse a un directorio mediante LDAP, el servidor Azure Multi-Factor Authentication puede configurarse para que actúe como proxy LDAP para realizar autenticaciones. También permite el uso del enlace LDAP como destino RADIUS para la autenticación previa de usuarios al usar la autenticación de IIS o la autenticación principal en el portal de usuario de Azure Multi-Factor Authentication.

Cuando se usa la Azure Multi-Factor Authentication como un proxy LDAP, el servidor de Azure Multi-Factor Authentication se inserta entre el cliente LDAP (por ejemplo, el dispositivo VPN o la aplicación) y el servidor del directorio LDAP para agregar la autenticación multifactor. Para que funcione la Azure Multi-Factor Authentication, deberá configurarse el servidor de Azure Multi-Factor Authentication para comunicarse con los servidores de cliente y el directorio LDAP. En esta configuración, el servidor de Azure Multi-Factor Authentication acepta las solicitudes LDAP de servidores y aplicaciones cliente y las reenvía al servidor de directorio LDAP de destino para validar las credenciales principales. Si la respuesta del directorio LDAP muestra que las credenciales principales son válidas, la Azure Multi-Factor Authentication realiza la autenticación de segundo factor y envía una respuesta al cliente LDAP. La autenticación completa se realizará correctamente solamente si la autenticación en el servidor LDAP y la autenticación multifactor se realizan correctamente.





## Configuración de autenticación LDAP


Para configurar la autenticación LDAP, instale al servidor Azure Multi-Factor Authentication en un servidor Windows. Utilice el siguiente procedimiento:

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de LDAP en el menú izquierdo.
2. Active la casilla de verificación Habilitar autenticación LDAP.![Autenticación LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png) 
3. En la pestaña Clientes, cambie el puerto TCP y SSL si el servicio LDAP de Azure Multi-Factor Authentication debe enlazarse con puertos no estándar para escuchar solicitudes LDAP de los clientes que se van a configurar.
4. Si tiene previsto usar LDAPS desde el cliente en el servidor de Azure Multi-Factor Authentication, deberá instalarse un certificado SSL en el servidor en el que se está ejecutando en el servidor. Haga clic en el botón Examinar... que se encuentra situado junto al cuadro Certificado SSL y seleccione el certificado instalado que se usará para la conexión segura. 
5. Haga clic en el botón Agregar...
6. En el cuadro de diálogo Agregar cliente LDAP, escriba la dirección IP del dispositivo, servidor o aplicación que se autenticará en el servidor y un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.
7. Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica. 
8. Puede repetir los pasos del 5 al 7 para agregar clientes LDAP adicionales.
9. Cuando se configura la Azure Multi-Factor Authentication para recibir autenticaciones LDAP, debe entregar dichas autenticaciones en el directorio LDAP. Por lo tanto, en la pestaña Destino solo muestra una única opción atenuada para usar un destino LDAP. Para configurar la conexión del directorio LDAP, haga clic en el icono de Integración de directorios. 
10. En la pestaña Configuración, seleccione el botón de radio Usar la configuración LDAP específica.
11. Haga clic en el botón Editar…
12. En el cuadro de diálogo Editar configuración de LDAP, rellene los campos con la información necesaria para conectarse al directorio LDAP. Se incluyen descripciones de los campos en la tabla siguiente. Nota: Esta información también se incluye en el archivo de ayuda de ayuda del servidor Azure Multi-Factor Authentication.![Integración de directorios](./media/multi-factor-authentication-get-started-server-ldap/ldap.png) 
13. Pruebe la conexión LDAP haciendo clic en el botón Probar.
14. Si la prueba de conexión LDAP se realizó correctamente, haga clic en el botón Aceptar. 
15. Haga clic en la pestaña Filtros. El servidor está preconfigurado para cargar contenedores, grupos de seguridad y usuarios de Active Directory. Si desea enlazar a un directorio LDAP diferente, probablemente necesitará editar los filtros mostrados. Haga clic en el vínculo Ayuda para obtener más información acerca de los filtros.
16. Haga clic en la ficha Atributos. El servidor está preconfigurado para asignar atributos de Active Directory.
17. Si desea enlazar a un directorio LDAP diferente o cambiar las asignaciones de atributos configuradas previamente, haga clic en el botón Editar...
18. En el cuadro de diálogo Editar atributos, modifique las asignaciones de atributos LDAP para el directorio. Los nombres de atributos pueden escribirse o seleccionarse haciendo clic en el botón ... que se encuentra situado junto a cada campo.
19. Haga clic en el vínculo Ayuda para obtener más información sobre los atributos.
20. Haga clic en el botón Aceptar.
21. Haga clic en el icono Configuración de la empresa y seleccione la pestaña Resolución de nombre de usuario. 22. Si desea conectarse a Active Directory desde un servidor unido a un dominio, podrá dejar el botón de opción Usar los identificadores de seguridad de Windows (SID) para que coincidan con los nombres de usuario seleccionado. De lo contrario, seleccione el botón de radio Usar el atributo del identificador único LDAP para los nombres de usuario coincidentes. Cuando se selecciona, el servidor Azure Multi-Factor Authentication tratará de resolver cada nombre de usuario en un identificador único del directorio LDAP. Se realizará una búsqueda LDAP en los atributos de Nombre de usuario definidos en la pestaña Integración de directorios -> Atributos. Cuando un usuario se autentica, el nombre de usuario se resolverá en el identificador único en el directorio LDAP y se usará el identificador único para que coincida con el usuario del archivo de datos de Azure Multi-Factor Authentication. Esto permite efectuar comparaciones que no distingan entre mayúsculas y minúsculas, además de formatos de nombre de usuario cortos y largos Esto completa la configuración del servidor Azure Multi-Factor Authentication. El servidor está escuchando ahora en los puertos configurados para las solicitudes de acceso LDAP de los clientes configurados y establecido para entregar esas solicitudes en el directorio LDAP para la autenticación.


## Configuración de cliente LDAP

Para configurar el cliente LDAP, use las siguientes directrices:

- Configure el dispositivo, el servidor o la aplicación para autenticarse mediante LDAP en el servidor Azure Multi-Factor Authentication como si fuese el directorio LDAP. Debe usar la misma configuración que usaría normalmente para conectarse directamente a su directorio LDAP, excepto el nombre del servidor o la dirección IP que será la del servidor Azure Multi-Factor Authentication. 
- Configure el tiempo de espera LDAP en entre 30 y 60 segundos para que haya tiempo para validar las credenciales del usuario con el directorio LDAP, realizar la autenticación de segundo factor, recibir su respuesta y, a continuación, responder a la solicitud de acceso LDAP. 
- Si usa LDAPS, el dispositivo o el servidor que realiza las consultas LDAP debe confiar en el certificado SSL instalado en el servidor Azure Multi-Factor Authentication.

<!---HONumber=AcomDC_0224_2016-->