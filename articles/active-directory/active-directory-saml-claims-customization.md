<properties
	pageTitle="Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory | Microsoft Azure"
	description="Aprenda a personalizar las notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/18/2015"
	ms.author="asmalser"/>

#Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory

Hoy en día Azure Active Directory admite miles de aplicaciones previamente integradas en la galería de aplicaciones de Azure AD, de las cuales más de 150 admiten el inicio de sesión único mediante el protocolo SAML 2.0. Cuando un usuario se autentica en una aplicación mediante Azure AD con SAML, Azure AD envía un token a la aplicación (mediante un redireccionamiento HTTP 302). Dicha aplicación lo valida y lo usa para iniciar la sesión del usuario en lugar de solicitar el nombre de usuario y la contraseña. Estos tokens SAML contienen trozos de información sobre el usuario conocidos como "notificaciones".

En términos de identidad, una "notificación" es información que declara un proveedor de identidades sobre un usuario dentro del token que emite para dicho usuario. En un [token SAML](http://en.wikipedia.org/wiki/SAML_2.0), estos datos están contenidos normalmente en la instrucción de atributo de SAML, y el id. único del usuario se representa habitualmente en el asunto de SAML.

De forma predeterminada, Azure AD emite un token SAML a la aplicación que contiene una reclamación NameIdentifier, con un valor de nombre de usuario del usuario en Azure AD (este valor identifica al usuario). El token SAML también contiene notificaciones adicionales con la dirección de correo electrónico, el nombre y el apellido del usuario.

Para ver o modificar las notificaciones emitidas en el token SAML a la aplicación, abra el registro de aplicación en el Portal de administración de Azure y seleccione la pestaña **Atributos** debajo de la aplicación.

![][1]

Existen dos razones posibles por las que podría tener la necesidad de editar las notificaciones emitidas en el token SAML: •La aplicación se ha escrito para requerir un conjunto diferente de URI de notificaciones o valores de notificación •La aplicación se ha implementado de forma que requiere que la notificación NameIdentifier sea algo distinto al nombre de usuario (conocido también como nombre principal de usuario) almacenado en Azure Active Directory.

Puede editar cualquiera de los valores de notificación predeterminados; para ello, seleccione el icono en forma de lápiz que aparece cuando sitúa el mouse sobre una de las filas de la tabla de atributos del token SAML. También puede quitar notificaciones (excepto NameIdentifier) mediante el icono **X** y agregar nuevas notificaciones mediante el botón **Agregar atributo de usuario**.

##Edición de la notificación NameIdentifier

Para solucionar el problema en el que la aplicación se ha implementado con un nombre de usuario diferente, haga clic en el icono de lápiz junto a la notificación NameIdentifier. Se mostrará un cuadro de diálogo con varias opciones:

![][2]

En el menú **Valor de atributo**, seleccione **user.mail** para que la notificación NameIdentifier sea la dirección de correo electrónico del usuario en el directorio, o seleccione **user.onpremisessamaccountname** para establecerla en el nombre de cuenta SAM del usuario que se ha sincronizado desde Azure AD local.

También puede usar la función especial ExtractMailPrefix() para quitar el sufijo de dominio de la dirección de correo electrónico o el nombre principal de usuario, lo que da lugar a que solo se pase la primera parte del nombre de usuario (por ejemplo, "joesmith" en lugar de joesmith@contoso.com).

![][3]

##Incorporación de notificaciones

Cuando se agrega una nueva notificación, puede especificar el nombre del atributo (que no tiene que seguir estrictamente un patrón de URI según la especificación SAML), o bien establecer el valor en cualquier atributo de usuario que se almacene en el directorio.

![][4]

Por ejemplo, si necesita enviar el departamento al que pertenece el usuario en su organización como notificación (por ejemplo, Ventas), puede insertar el valor de notificación que espera la aplicación y luego seleccionar **user.department** como valor.

Si no hay ningún valor almacenado para el atributo seleccionado para un usuario determinado, esa notificación no se emitirá en el token.

**Nota:** **user.onpremisesecurityidentifier** y **user.onpremisesamaccountname** solo se admiten al sincronizar datos de usuario desde Active Directory local mediante la versión preliminar más reciente de la herramienta AAD Connect. Puede descargar la versión preliminar de la herramienta Connect en el siguiente vínculo:

http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=53949
	
<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png

<!---HONumber=Nov15_HO4-->