<properties
	pageTitle="Uso de dominios personalizados en el proxy de aplicación de Azure AD | Microsoft Azure"
	description="Explica cómo trabajar con dominios personalizados en el proxy de la aplicación de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="kgremban"/>

# Uso de dominios personalizados en el proxy de la aplicación de Azure AD
> [AZURE.NOTE]Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

El uso de un dominio predeterminado permite establecer la misma dirección URL como URL interna y externa de acceso a la aplicación a fin de que los usuarios solo tengan que recordar una dirección URL para tener acceso a la aplicación, sin importar desde dónde lo hagan. Esto también le permite crear un acceso directo único en el Panel de acceso para la aplicación. Si usa el dominio predeterminado que proporciona el proxy de la aplicación de Azure AD, no tendrá que realizar ninguna configuración adicional para habilitar el dominio. En caso de que use un dominio personalizado, hay algunas cosas que debe hacer para asegurarse de que el proxy de la aplicación reconoce su dominio y valida sus certificados.

## Selección de un dominio personalizado

1. Publique la aplicación según las instrucciones de [publicación de aplicaciones con el proxy de la aplicación](active-directory-application-proxy-publish.md).
2. Cuando la aplicación aparezca en la lista de aplicaciones, selecciónela y haga clic en **Configurar**.
3. En **Dirección URL externa**, escriba el dominio personalizado.
4. Si la dirección URL externa es https, se le pedirá que cargue un certificado para que Azure pueda validar la dirección URL de la aplicación. También puede cargar un certificado comodín que coincida con la dirección URL externa de la aplicación. Este dominio debe estar en la lista de los [dominios comprobados de Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure debe tener un certificado para la dirección URL del dominio de la aplicación o un certificado comodín que coincida con la dirección URL externa de la aplicación.
5. Asegúrese de agregar un registro DNS que enrute la dirección URL interna a la aplicación que le permita tener la misma dirección URL para el acceso interno y externo y un solo acceso directo a la aplicación en la lista de aplicaciones de usuario.

## Preguntas más frecuentes sobre cómo trabajar con dominios personalizados

P: ¿Puedo seleccionar un certificado ya cargado sin cargarlo de nuevo? R: Los certificados cargados previamente se enlazan automáticamente a una aplicación y existe exactamente un certificado que coincida con el nombre de host de la aplicación. P: ¿Cómo agrego un certificado y en qué formato debe cargarse el certificado exportado? R: El certificado se debe cargar desde la página de configuración de la aplicación. El certificado debe ser un archivo PFX. … P: ¿Se pueden usar certificados ECC? R: No hay ninguna limitación explícita en cuanto a métodos de firma. … P: ¿Se pueden usar certificados SAN? R: Sí. ... P: ¿Se pueden usar certificados comodín? R: Sí. ... P: ¿Se puede usar un certificado diferente en cada aplicación? R: Sí, a menos que las dos aplicaciones compartan el mismo host externo. ... P: ¿Si registro un nuevo dominio, puedo usar ese dominio? R: Sí, la lista de dominios se basa en la lista de dominios comprobados del inquilino. ... P: ¿Qué sucede cuando expira un certificado? R: Recibirá una advertencia en la sección de certificados de la página de configuración de la aplicación. Cuando un usuario intente tener acceso a la aplicación, aparecerá una advertencia de seguridad. ... P: ¿Qué debo hacer si quiero reemplazar un certificado para una aplicación determinada? R: Cargar un nuevo certificado desde la página de configuración de la aplicación. ... P: ¿Puedo eliminar un certificado y reemplazarlo? R: Cuando se carga un nuevo certificado, si el certificado antiguo no lo está usando otra aplicación, se eliminará automáticamente. ... P: ¿Qué sucede cuando se revoca un certificado? R: No se realizan comprobaciones de revocación para certificados. Cuando un usuario intenta obtener acceso a la aplicación, dependiendo del explorador, es posible que aparezca una advertencia de seguridad. ... P: ¿Puedo usar un certificado autofirmado? R: Sí, se permiten los certificados autofirmados. Tenga en cuenta que si usa una entidad de certificación privada, el CDP (punto de distribución de lista de revocación de certificados) del certificado debe ser público. ... P: ¿Existe un lugar en donde ver todos los certificados de mi inquilino? R: Esta posibilidad no se admite en la versión actual.


## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:

- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)- [Solucionar los problemas que tiene con el Proxy de la aplicación](active-directory-application-proxy-troubleshoot.md)

## Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionales

* [Registro en Azure como una organización](sign-up-organization.md)
* [Identidad de Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0114_2016-->