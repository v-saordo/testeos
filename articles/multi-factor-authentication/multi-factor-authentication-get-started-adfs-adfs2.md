<properties 
	pageTitle="Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0" 
	description="En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS 2.0." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>
# Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0

Si su organización está federada con Azure Active Directory y tiene recursos locales o en la nube que desea proteger, puede hacerlo mediante el Servidor Azure Multi-Factor Authentication y configurarlo para que funcione con AD FS de modo que la autenticación multifactor se desencadene para extremos de alto valor.

Esta documentación trata del uso de Servidor Azure Multi-Factor Authentication con AD FS 2.0. Para obtener información sobre el uso de Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS, consulte [Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## Proxy AD FS 2.0
Para proteger AD FS 2.0 con un proxy, instale Servidor Azure Multi-Factor Authentication en el servidor proxy ADFS y configure el Servidor según los siguientes pasos.

### Protección de AD FS 2.0 con un proxy

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.
2. Haga clic en la pestaña Basados en formularios.
3. Haga clic en el botón Agregar...
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Para detectar el nombre de usuario, la contraseña y las variables de dominio automáticamente, escriba la dirección URL de inicio de sesión (por ejemplo, https://sso.contoso.com/adfs/ls) en el cuadro de diálogo Configuración automática de sitio web basado en formularios y haga clic en Aceptar.
5. Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica.
6. Si no se pueden detectar automáticamente las variables de página, haga clic en el botón Especificar manualmente... en el cuadro de diálogo Configuración automática de sitio web basado en formularios.
7. En el cuadro de diálogo Agregar sitio web basado en formularios, escriba la dirección URL de la página de inicio de sesión de ADFS en el campo URL de envío (p. ej. https://sso.contoso.com/adfs/ls) y escriba un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil. Consulte el archivo de ayuda para obtener más información sobre la dirección URL de envío.
8. Establezca el formato de solicitud en "POST or GET".
9. Especifique Variable de nombre de usuario (ctl00$ContentPlaceHolder1$UsernameTextBox) y Variable de contraseña (ctl00$ContentPlaceHolder1$PasswordTextBox). Si la página de inicio de sesión basada en formularios muestra un cuadro de texto de dominio, especifique también Variable de dominio. Es posible que deba ir a la página de inicio de sesión en un explorador web, hacer doble clic en la página y seleccionar "Ver código fuente " para buscar los nombres de los cuadros de entrada de la página de inicio de sesión.
10. Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Haga clic en el botón Avanzadas... para revisar la configuración avanzada, incluida la capacidad de seleccionar un archivo de página de denegación personalizada, a fin de almacenar en caché las autenticaciones correctas en el sitio web durante un período de tiempo mediante cookies y seleccionar cómo autenticar las credenciales principales.
12. Puesto que no es probable que el servidor proxy ADFS se una al dominio, es posible utilizar LDAP para conectarse al controlador de dominio para la importación de usuario y la autenticación previa. En el cuadro de diálogo Sitio web basado en formularios avanzado, haga clic en la pestaña Autenticación principal y seleccione "Enlace LDAP" para el tipo de autenticación de autenticación previa.
13. Cuando haya terminado, haga clic en el botón Aceptar para volver al cuadro de diálogo Agregar sitio web basado en formularios. Consulte el archivo de ayuda para obtener más información sobre las configuraciones avanzadas.
14. Haga clic en el botón Aceptar para cerrar el cuadro de diálogo.
15. Una vez detectadas o introducidas las variables de página y dirección URL, los datos del sitio web se mostrarán en el panel Basados en formularios.
16. Haga clic en la pestaña Módulo nativo y seleccione el servidor, el sitio web en el que se ejecuta el proxy ADFS (por ejemplo, "Sitio web predeterminado") o la aplicación de proxy ADFS (por ejemplo, "ls" en "adfs") para habilitar el complemento de IIS en el nivel deseado.
17. Haga clic en el cuadro Habilitar autenticación IIS en la parte superior de la pantalla.
18. Ahora la autenticación de IIS está habilitada. Sin embargo, debe configurar la conexión LDAP con el controlador de dominio para poder realizar la autenticación previa a su Active Directory (AD) a través de LDAP. Para ello, haga clic en el icono Integración de directorios.
19. En la pestaña Configuración, seleccione el botón de radio Usar la configuración LDAP específica.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. Haga clic en el botón Editar…
21. En el cuadro de diálogo Editar configuración de LDAP, rellene los campos con la información necesaria para conectarse al controlador de dominio de AD. Se incluyen descripciones de los campos en la tabla siguiente. Nota: esta información también se incluye en el archivo de ayuda de Servidor Azure Multi-Factor Authentication.
22. Pruebe la conexión LDAP haciendo clic en el botón Probar.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. Si la prueba de conexión LDAP se realizó correctamente, haga clic en el botón Aceptar.
24. A continuación, haga clic en el icono Configuración de la empresa y seleccione la pestaña Resolución de nombre de usuario.
25. Seleccione el botón de radio Usar el atributo del identificador único LDAP para los nombres de usuario coincidentes.
26. Si los usuarios escriben su nombre de usuario en el formulario de inicio de sesión del proxy ADFS en formato "dominio\\nombreDeUsuario", el Servidor debe ser capaz de eliminar el dominio del nombre de usuario cuando crea la consulta LDAP. Esto puede hacerse mediante un valor del Registro.
27. Abra el editor del Registro y vaya a HKEY\_LOCAL\_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor en un servidor de 64 bits. Si está en un servidor de 32 bits, saque "Wow6432Node" fuera de la ruta de acceso. Cree una nueva clave de Registro DWORD denominada "UsernameCxz\_stripPrefixDomain" y establezca el valor en 1. Azure Multi-Factor Authentication protege ahora el proxy ADFS. Asegúrese de que se han importado los usuarios de Active Directory al Servidor. Consulte la siguiente sección IP de confianza si desea agregar a la lista blanca direcciones IP internas, con lo que ya no sería necesaria la autenticación de dos factores al iniciar sesión en el sitio web desde esas ubicaciones.

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 Direct sin proxy

Para proteger AD FS cuando no se usa el proxy AD FS, instale Servidor Azure Multi-Factor Authentication en el servidor AD FS y configure el Servidor según los siguientes pasos.

### Protección de AD FS 2.0 sin proxy
1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.
2. Haga clic en la pestaña HTTP.
3. Haga clic en el botón Agregar...
4. En el cuadro de diálogo Agregar dirección URL Base, escriba la dirección URL del sitio web ADFS donde se realiza la autenticación HTTP (por ejemplo, https://sso.domain.com/adfs/ls/auth/integrated) en el campo Dirección URL base y escriba un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.
5. Si lo desea, ajuste los tiempos Tiempo de espera de inactividad y Sesión máxima.
6. Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica.
7. Active la casilla de la caché de cookies si lo desea.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Haga clic en el botón Aceptar.
9. Haga clic en la pestaña Módulo nativo y seleccione el servidor, el sitio web en el que se ejecuta ADFS (por ejemplo, "Sitio web predeterminado") o la aplicación ADFS (por ejemplo, "ls" en "adfs") para habilitar el complemento de IIS en el nivel deseado.
10. Haga clic en el cuadro Habilitar autenticación IIS en la parte superior de la pantalla. Azure Multi-Factor Authentication protege ahora ADFS. Asegúrese de que se han importado los usuarios de Active Directory al Servidor. Consulte la siguiente sección IP de confianza si desea agregar a la lista blanca direcciones IP internas, con lo que ya no sería necesaria la autenticación de dos factores al iniciar sesión en el sitio web desde esas ubicaciones.


## IP de confianza
Las IP de confianza permiten a los usuarios omitir Azure Multi-Factor Authentication para las solicitudes de sitio web procedentes de determinadas direcciones IP o subredes. Por ejemplo, puede que desee excluir a determinados usuarios de Azure Multi-Factor Authentication mientras inicia sesión desde la oficina. Para ello, debe especificar la subred de la oficina como entrada de IP de confianza.

### Configuración de IP de confianza


1. En la sección Autenticación de IIS, haga clic en la pestaña IP de confianza.
1. Haga clic en el botón Agregar...
1. Cuando aparezca el cuadro de diálogo Agregar IP de confianza, seleccione el botón de radio IP única, Intervalo IP o Subred.
1. Escriba la dirección IP, el intervalo IP o la subred que debe estar en la lista blanca. Si introduce una subred, seleccione la máscara de red adecuada y haga clic en el botón Aceptar. Se ha agregado la dirección IP de confianza.


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=AcomDC_0224_2016-->