<properties
	pageTitle="Uso de conectores del proxy de aplicación de Azure AD | Microsoft Azure"
	description="Explica cómo crear y administrar grupos de conectores en el Proxy de aplicación de Azure AD."
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
	ms.date="02/09/2016"
	ms.author="kgremban"/>


# Publicación de aplicaciones en redes independientes y ubicaciones mediante grupos de conectores

> [AZURE.NOTE] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Los grupos de conectores son útiles para diversos escenarios, por ejemplo:


- Sitios con varios centros de datos conectadas entre sí en los que quiere mantener tanto tráfico dentro del centro de datos como sea posible, porque los vínculos entre centros de datos suelen ser caros y tener una latencia elevada. Puede implementar conectores en cada centro de datos para servir solo las aplicaciones que residen en el centro de datos a fin de guardar vínculos valiosos entre centros de datos y ofrecer una experiencia completamente transparente para los usuarios.
- Administración de aplicaciones instaladas en redes aisladas que no forman parte de la red corporativa principal. Puede usar grupos de conectores para instalar conectores dedicados para redes aisladas para permitir también el aislamiento de aplicaciones en la red.
- Para las aplicaciones instaladas en IaaS para el acceso en la nube, los grupos de conectores proporcionan un servicio común para proteger el acceso a todos ellos sin crear dependencia adicional en la red corporativa o fragmentar la experiencia. Los conectores se pueden instalarse en cada centro de datos en la nube y servir solo las aplicaciones que residen en esta red. Puede instalar varios conectores para lograr alta disponibilidad.
- Soporte para entornos de varios bosques en los que se pueden implementar conectores específicos por bosque y establecerse para servir aplicaciones específicas.
- Los grupos de conectores se pueden utilizar en sitios de recuperación ante desastres para detectar la conmutación por error o como copia de seguridad para el sitio principal.
- Los grupos de conectores también se pueden utilizar para dar servicio a varias compañías desde un solo inquilino.

## Trabajo con grupos de conectores
Para agrupar los conectores, debe asegurarse de que [instaló varios conectores](active-directory-application-proxy-enable.md), que le asigna un nombre. Después puede agruparlos. Finalmente deberá asignarlos a aplicaciones específicas.

## Paso 1: Crear grupos de conectores
Puede crear tantos grupos de conectores como desee. La creación de grupos de conectores se lleva a cabo en el Portal de Azure clásico. Seleccione el directorio y haga clic en **Configurar**.

  ![Captura de la configuración del proxy de la aplicación: clic en administrar grupos de conectores](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

Después, en el Proxy de aplicación, haga clic en **Administrar grupos de conectores** y cree un nuevo grupo de conectores proporcionando un nombre al grupo.

  ![Captura de pantalla de grupos de conectores de proxy de la aplicación - dar nombre a nuevo grupo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## Paso 2: Asignar conectores a los grupos
Después de crear los grupos de conector, mueva los conectores al grupo adecuado. En **Proxy de la aplicación**, haga clic en **Administrar conectores**. En **Grupo**, seleccione el grupo que quiera para cada conector. Tenga en cuenta que los conectores pueden tardar en activarse hasta 10 minutos en el nuevo grupo.

  ![Captura de pantalla de conectores de proxy de la aplicación: seleccionar grupo en el menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## Paso 3: Asignar aplicaciones a los grupos de conectores
El último paso es establecer cada aplicación en el grupo de conectores que la servirá. En el Portal de Azure clásico, en el directorio, seleccione la aplicación que quiere asignar al grupo y haga clic en **Configurar**. En **Grupo conectores**, seleccione el grupo que desee que utilice la aplicación. Este cambio se aplica inmediatamente.

  ![Captura de pantalla de grupo de conectores de proxy de la aplicación: seleccionar grupo en el menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

Para más información sobre la publicación de aplicaciones, vea [Publicación de aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md).

## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:

- [Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

## Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionales
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Sobre la delegación limitada de Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=AcomDC_0211_2016-->