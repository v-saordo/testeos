<properties 
	pageTitle="Servidor de autenticación RADIUS y Azure Multi-Factor Authentication" 
	description="Se trata de la página Azure Multi-Factor Authentication que ayudará a implementar la autenticación RADIUS y el servidor Azure Multi-Factor Authentication." 
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



# Servidor de autenticación RADIUS y Azure Multi-Factor Authentication

La sección de autenticación RADIUS le permite habilitar y configurar la autenticación RADIUS para el servidor Azure Multi-Factor Authentication. RADIUS es un protocolo estándar para aceptar las solicitudes de autenticación y procesar estas solicitudes. El servidor Azure Multi-Factor Authentication actúa como un servidor RADIUS y se inserta entre el cliente RADIUS (por ejemplo, el dispositivo VPN) y el destino de autenticación, que puede ser Active Directory (AD), un directorio LDAP u otro servidor RADIUS, para agregar la Azure Multi-Factor Authentication. Para que la Azure Multi-Factor Authentication funcione, debe configurar el servidor Azure Multi-Factor Authentication para que pueda comunicarse con los servidores de cliente y el destino de autenticación. El servidor Azure Multi-Factor Authentication acepta solicitudes de un cliente RADIUS, valida las credenciales en el destino de autenticación, agrega la Azure Multi-Factor Authentication y envía una respuesta al cliente RADIUS. La autenticación completa se realizará correctamente solo si la autenticación principal y la Azure Multi-Factor Authentication se realizan correctamente.

>[AZURE.NOTE]
El servidor de MFA solo admite los protocolos RADIUS de PAP (protocolo de autenticación de contraseña) y MSCHAPv2 (protocolo de autenticación por desafío mutuo de Microsoft) cuando actúa como servidor RADIUS. Otros protocolos, como EAP (protocolo de autenticación extensible), se pueden usar cuando el servidor MFA actúa como un proxy RADIUS para otro servidor RADIUS que admite dicho protocolo, como Microsoft NPS. </br> Si usa otros protocolos en esta configuración, los tokens SMS y OATH unidireccionales no funcionarán, ya que el servidor de MFA no puede iniciar una respuesta de desafío de RADIUS correcta con ese protocolo.


![Autenticación Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Configuración de autenticación RADIUS

Para configurar la autenticación RADIUS, instale el servidor Azure Multi-Factor Authentication en un servidor Windows. Si tiene un entorno de Active Directory, el servidor debe estar unido al dominio de dentro de la red. Use el procedimiento siguiente para configurar el servidor Azure Multi-Factor Authentication.

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de RADIUS en el menú izquierdo.En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.
2. Marque la casilla de verificación Habilitar autenticación RADIUS.
3. En la pestaña Clientes, cambie los puertos de autenticación y de cuentas si el servicio RADIUS de Azure Multi-Factor Authentication debe enlazarse con puertos no estándar para escuchar las solicitudes RADIUS de los clientes que se van a configurar.
4. Haga clic en el botón Agregar...
5. En el cuadro de diálogo Agregar cliente RADIUS, escriba la dirección IP del dispositivo o el servidor que se autenticará en el servidor de Azure Multi-Factor Authentication, un nombre de aplicación (opcional) y un secreto compartido. El secreto compartido deberá ser el mismo en el servidor de Azure Multi-Factor Authentication y en el dispositivo/servidor. El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.
6. Active la casilla Requerir coincidencia de usuario de Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica.
7. Marque la casilla Habilitar token OATH de reserva si los usuarios van a usar la autenticación de la aplicación móvil de Azure Multi-Factor Authentication y desea usar códigos de acceso OATH como autenticación de reserva para la llamada de teléfono fuera de banda, el SMS o la notificación push.
8. Haga clic en el botón Aceptar.
9. Puede repetir los pasos del 4 al 8 para agregar más clientes RADIUS.
10. Haga clic en la pestaña Destino.
11. Si el servidor Azure Multi-Factor Authentication está instalado en un servidor unido a un dominio de un entorno de Active Directory, seleccione el dominio de Windows.
12. Si los usuarios deben autenticarse en un directorio LDAP, seleccione un enlace LDAP. Cuando use un enlace LDAP, deberá hacer clic en el icono de integración de directorios y editar la configuración de LDAP en la pestaña Configuración para que el servidor pueda enlazar con su directorio. Puede encontrar instrucciones para configurar LDAP en la Guía de configuración de proxy LDAP. 
13. Si los usuarios deben autenticarse en otro servidor RADIUS, seleccione servidores RADIUS.
14. Configure el servidor al que el servidor entregará las solicitudes RADIUS haciendo clic en el botón Agregar...
15. En el cuadro de diálogo Agregar servidor RADIUS, escriba la dirección IP del servidor RADIUS y un secreto compartido. El secreto compartido deberá ser el mismo en el servidor Azure Multi-Factor Authentication y en el servidor RADIUS. Cambie el puerto de autenticación y el de cuentas si el servidor RADIUS usa puertos diferentes.
16. Haga clic en el botón Aceptar. 
17. Debe agregar el servidor Azure Multi-Factor Authentication como cliente RADIUS en el otro servidor RADIUS para que procese las solicitudes de acceso que se le envían desde el servidor Azure Multi-Factor Authentication. Debe usar el mismo secreto compartido configurado en el servidor Azure Multi-Factor Authentication.
18. Puede repetir este paso para agregar más servidores RADIUS y configurar el orden en el que el servidor debe llamarles mediante los botones Subir y Bajar. Esto completa la configuración del servidor Azure Multi-Factor Authentication. Ahora, el servidor está escuchando en los puertos configurados las solicitudes de acceso RADIUS de los clientes configurados.   


## Configuración del cliente RADIUS

Para configurar el cliente RADIUS, siga las siguientes instrucciones:

- Configure el dispositivo/servidor para autenticarse mediante RADIUS en la dirección IP del servidor Azure Multi-Factor Authentication, que actuará como servidor RADIUS. 
- Use el mismo secreto compartido que se configuró anteriormente. 
- Configure el tiempo de espera RADIUS en entre 30 y 60 segundos para que haya tiempo para validar las credenciales del usuario, realizar la autenticación multifactor, recibir su respuesta y, a continuación, responder a la solicitud de acceso RADIUS.

<!---HONumber=AcomDC_0224_2016-->