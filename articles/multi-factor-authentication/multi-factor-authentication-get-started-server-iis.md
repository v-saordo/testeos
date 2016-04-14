<properties 
	pageTitle="Servidor de autenticación IIS y Azure Multi-Factor Authentication" 
	description="Se trata de la página Azure Multi-Factor Authentication que ayudará a implementar la autenticación IIS y el servidor Azure Multi-Factor Authentication." 
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

# Autenticación IIS

La sección de autenticación de IIS del servidor Azure Multi-Factor Authentication permite habilitar y configurar la autenticación IIS para la integración con aplicaciones web Microsoft IIS. El servidor Azure Multi-Factor Authentication instala un complemento que puede filtrar las solicitudes realizadas al servidor web IIS para agregar la Azure Multi-Factor Authentication. El complemento IIS proporciona compatibilidad para la autenticación basada en formularios y la autenticación HTTP de Windows integrada. También se pueden configurar IP de confianza para eximir direcciones IP internas de la autenticación en dos fases.


![Autenticación IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Mediante la autenticación IIS basada en formularios con el servidor Azure Multi-Factor Authentication

Para proteger una aplicación web IIS que usa la autenticación basada en formularios, instale al servidor Azure Multi-Factor Authentication en el servidor web IIS y configure el servidor de acuerdo con el procedimiento siguiente.

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.
2. Haga clic en la pestaña Basados en formularios.
3. Haga clic en el botón Agregar...
4. Para detectar el nombre de usuario, la contraseña y las variables de dominio automáticamente, escriba la dirección URL de inicio de sesión (por ejemplo, https://localhost/contoso/auth/login.aspx) en el cuadro de diálogo Configuración automática de sitio web basado en formularios y haga clic en Aceptar.
5. Active la casilla Requerir coincidencia de usuario de Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada.
6. Si no se pueden detectar automáticamente las variables de página, haga clic en el botón Especificar manualmente... en el cuadro de diálogo Configuración automática de sitio web basado en formularios.
7. En el cuadro de diálogo Agregar sitio web basado en formularios, escriba la dirección URL en la página de inicio de sesión en el campo URL de envío y escriba un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil. Consulte el archivo de ayuda para obtener más información sobre la dirección URL de envío. 
8. Seleccione el formato de solicitud correcto. Esto se establece en "POST o GET" para la mayoría de las aplicaciones web.
9. Especifique las variables de nombre de usuario, contraseña y dominio (si aparece en la página de inicio de sesión). Es posible que deba ir a la página de inicio de sesión en un explorador web, hacer doble clic en la página y seleccionar "Ver código fuente " para buscar los nombres de los cuadros de entrada de la página.
10. Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica.
11.  Haga clic en el botón Avanzadas... para revisar la configuración avanzada, incluida la capacidad de seleccionar un archivo de página de denegación personalizada, a fin de almacenar en caché las autenticaciones correctas en el sitio web durante un período de tiempo mediante cookies y seleccionar si desea autenticar las credenciales principales en el dominio de Windows, un directorio LDAP o un servidor RADIUS. Cuando haya terminado, haga clic en el botón Aceptar para volver al cuadro de diálogo Agregar sitio web basado en formularios. Consulte el archivo de ayuda para obtener más información sobre las configuraciones avanzadas.
12. Haga clic en el botón Aceptar.
13. Una vez detectadas o introducidas las variables de página y dirección URL, los datos del sitio web se mostrarán en el panel Basados en formularios.
14. Consulte la sección Habilitar complementos IIS para el servidor Azure Multi-Factor Authentication directamente debajo para completar la configuración de la autenticación de IIS. 

## Uso de la autenticación integrada de Windows con el servidor Azure Multi-Factor Authentication

Para proteger una aplicación web IIS que usa la autenticación integrada HTTP de Windows, instale el servidor Azure Multi-Factor Authentication en el servidor web IIS y configure el servidor mediante el procedimiento siguiente.

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.
2. Haga clic en la pestaña HTTP. Haga clic en la pestaña Basados en formularios.
3. Haga clic en el botón Agregar...
4. En el cuadro de diálogo Agregar dirección URL Base, escriba la dirección URL del sitio web donde se realiza la autenticación HTTP (por ejemplo, http://localhost/owa) en el campo Dirección URL base y escriba un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.
5. Ajuste el tiempo de espera de inactividad y los tiempos de sesión máximos si el valor predeterminado no es suficiente.
6. Active la casilla Requerir coincidencia de usuario de Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica. 
7. Active la casilla de la caché de cookies si lo desea.
8. Haga clic en el botón Aceptar.
9. Consulte la sección [Habilitar complementos IIS para el servidor Azure Multi-Factor Authentication](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) directamente debajo para completar la configuración de la autenticación de IIS. 


## Habilitar complementos IIS para el servidor Azure Multi-Factor Authentication

Una vez haya configurado las direcciones URL basadas en formularios o de autenticación HTTP y la configuración, deberá seleccionar las ubicaciones donde se deben cargar y habilitar los complementos de IIS de Azure Multi-Factor Authentication en IIS. Utilice el siguiente procedimiento:

1. Si se ejecuta en IIS 6, haga clic en la ficha ISAPI y seleccione el sitio web en el que se está ejecutando la aplicación web (por ejemplo, el sitio web predeterminado) para habilitar el complemento del filtro ISAPI de Azure Multi-Factor Authentication para ese sitio.
2. Si se ejecuta en IIS 7 o versiones posteriores, haga clic en la ficha Módulo nativo y seleccione el servidor, sitios web o aplicaciones para habilitar el complemento de IIS en el nivel o niveles deseados.
3. Haga clic en el cuadro Habilitar autenticación IIS en la parte superior de la pantalla. Azure Multi-Factor Authentication está ahora protegiendo la aplicación IIS seleccionada. Asegúrese de que los usuarios se han importado en el servidor. Consulte la siguiente sección IP de confianza si desea agregar a la lista blanca direcciones IP internas, con lo que ya no sería necesaria la autenticación de dos factores al iniciar sesión en el sitio web desde esas ubicaciones. 


## IP de confianza

Las IP de confianza permiten a los usuarios omitir Azure Multi-Factor Authentication para las solicitudes de sitio web procedentes de determinadas direcciones IP o subredes. Por ejemplo, puede que desee excluir a determinados usuarios de Azure Multi-Factor Authentication mientras inicia sesión desde la oficina. Para ello, debe especificar la subred de la oficina como entrada de IP de confianza. Para configurar direcciones IP de confianza, use el procedimiento siguiente:

1. En la sección Autenticación de IIS, haga clic en la pestaña IP de confianza. 
2. Haga clic en el botón Agregar...
3. Cuando aparezca el cuadro de diálogo Agregar IP de confianza, seleccione el botón de radio IP única, Intervalo IP o Subred.
4. Escriba la dirección IP, el intervalo IP o la subred que debe estar en la lista blanca. Si introduce una subred, seleccione la máscara de red adecuada y haga clic en el botón Aceptar. Se ha agregado a la lista blanca.

<!---HONumber=AcomDC_0224_2016-->