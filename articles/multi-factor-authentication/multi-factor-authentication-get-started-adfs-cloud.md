<properties 
	pageTitle="Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS" 
	description="En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS 2.0 en la nube." 
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

# Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS

Si su organización está federada con Azure Active Directory y dispone de los recursos a los que tiene acceso Azure AD, puede usar Azure Multi-Factor Authentication o Servicios de federación de Active Directory para proteger esos recursos. Utilice los siguientes procedimientos para proteger recursos de Azure Active Directory mediante Azure Multi-Factor Authentication o Servicios de federación de Active Directory.

## Para proteger recursos de Azure AD mediante AD FS, realice lo siguiente: 



1. Utilice los pasos descritos en [Activación de autenticación multifactor para usuarios](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) para habilitar una cuenta.
2. Utilice el siguiente procedimiento para configurar una regla de notificación:

![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

- 	Inicie la consola de administración de AD FS.
- 	Vaya a Relaciones de confianza para usuario autenticado y haga clic con el botón derecho en la relaciones de confianza para usuario autenticado. Seleccione Editar reglas de notificación...
- 	Haga clic en Agregar regla…
- 	En la lista desplegable, seleccione Enviar notificaciones con una regla personalizada y haga clic en Siguiente.
- 	Escriba un nombre para la regla de notificación.
- 	En Regla personalizada: agregue lo siguiente:


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Notificación correspondiente.

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Haga clic en Aceptar. Haga clic en Finish. Cierre la consola de administración de AD FS.

Los usuarios ya pueden completar el inicio de sesión mediante el método local (como una tarjeta inteligente).

## Direcciones IP de confianza para usuarios federados
Las direcciones IP de confianza permiten a los administradores omitir la autenticación multifactor para una dirección IP específica o para usuarios federados que tienen solicitudes que se originan dentro de su propia intranet. En las secciones siguientes se describe cómo configurar IP fiables de Azure Multi-Factor Authentication con usuarios federados y omitir la autenticación multifactor cuando una solicitud se origina en una intranet de usuarios federados. Esto se consigue configurando AD FS para usar un paso a través o filtrar una plantilla de notificación entrante con el tipo de notificación dentro de la red corporativa. En este ejemplo se utiliza Office 365 para las relaciones de confianza para usuario autenticado .

### Configurar las reglas de notificaciones de AD FS

Utilice el procedimiento siguiente para configurar las notificaciones de AD FS. Se crearán dos reglas de notificaciones, una para el tipo de notificación dentro de la red corporativa y otra adicional para mantener a nuestros usuarios con la sesión iniciada.

1. Abra Administración de AD FS.
2. A la izquierda, seleccione Relaciones de confianza para usuario autenticado.
3. En el centro, haga clic con el botón secundario en Plataforma de identidad de Microsoft Office 365 y seleccione **Editar reglas de notificaciones…** ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. En Reglas de transformación de emisión, haga clic en **Agregar regla.** ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. En el Asistente para agregar regla de notificaciones de transformación, seleccione Pasar por una notificación entrante o filtrarla en la lista desplegable y haga clic en Siguiente. ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. En el cuadro situado junto al nombre de la regla de notificación, asigne un nombre a la regla. Por ejemplo: InsideCorpNet.
7. En la lista desplegable, junto a Tipo de notificación entrante, seleccione Dentro de la red corporativa. ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Haga clic en Finish.
9. En Reglas de transformación de emisión, haga clic en **Agregar regla**.
10. En el Asistente para agregar regla de notificaciones de transformación, seleccione Enviar notificaciones mediante regla personalizada en la lista desplegable y haga clic en Siguiente.
11. En el cuadro situado bajo el nombre de la regla de notificación: escriba Mantener a los usuarios con la sesión iniciada.
12. En el cuadro de regla personalizada, escriba:
	    
		c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
			=> issue(claim = c);
![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Haga clic en **Finalizar**
14. Haga clic en **Apply**.
15. Haga clic en **Aceptar**.
16. Cierre Administración de AD FS.



### Configuración de las IP de confianza de Azure Multi-Factor Authentication con usuarios federados
Ahora que las notificaciones están listas, podemos configurar IP de confianza.

1. Inicie sesión en el Portal de administración de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En Directorio, haga clic en el directorio en el que desea configurar IP de confianza.
4. En el directorio que ha seleccionado, haga clic en Configurar.
5. En la sección de la autenticación multifactor, haga clic en Administrar configuración del servicio.
6. En la página Configuración del servicio, en IP de confianza, seleccione **Para solicitudes de usuarios federados cuyo origen esté en mi intranet.** ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Haga clic en Guardar.
8. Una vez que se han aplicado las actualizaciones, haga clic en Cerrar.


¡Ya está! En este punto, los usuarios federados de Office 365 solo deberán usar MFA cuando una notificación se origine fuera de la intranet corporativa.

<!---HONumber=AcomDC_0224_2016-->