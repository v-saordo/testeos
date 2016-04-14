<properties 
	pageTitle="P+F de Azure RemoteApp| Microsoft Azure" 
	description="Conozca las respuestas para las preguntas más frecuentes acerca de Azure RemoteApp." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/25/2016" 
	ms.author="elizapo"/>

# Preguntas más frecuentes sobre Azure RemoteApp
Hemos recibido las siguientes preguntas acerca de Azure RemoteApp. ¿Tiene alguna otra? Visite los [Foros de RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) y pregúntenos lo que quiera saber o deje un comentario abajo.

## ¿Qué es Azure RemoteApp? ##


- **¿Qué es RemoteApp de Azure?** RemoteApp es un servicio de Azure que ayuda a proporcionar acceso remoto seguro a las aplicaciones desde muchos dispositivos de usuario diferentes. Obtenga más información sobre [RemoteApp de Azure](remoteapp-whatis.md).
- **¿Cuáles son las opciones de implementación?** Hay dos tipos de colecciones de RemoteApp: híbridas y nube. La que necesite dependerá de varios factores, como si es necesaria la unión de dominios. Hablamos acerca de todas esas decisiones [aquí](remoteapp-collections.md).

## Sugerencias rápidas para usar Azure RemoteApp ##
- **¿Cuánto tiempo pasa hasta que me desconecto? ¿Cuánto tiempo puedo estar inactivo antes de arrancar?** 4 horas. Si usted o uno de los usuarios está inactivo durante 4 horas, se cerrará automáticamente la sesión de Azure RemoteApp. Compruebe los demás valores predeterminados en [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).
- **¿Puedo probar este servicio de forma gratuita?** Sí. Hay una versión de prueba gratuita durante 30 días. Tras la finalización de la prueba, puede realizar la transición a una cuenta de pago (que puede usar en producción) o dejar de usar el servicio. Para comenzar una evaluación gratuita, vaya a [portal.azure.com](http://portal.azure.com) y cree una instancia de RemoteApp. Con la versión de prueba gratuita, puede crear dos instancias de RemoteApp con 10 usuarios por instancia. Recuerde que esta versión de prueba solo tiene una duración de 30 días.
## Detalles de la suscripción de Azure RemoteApp ##

- **¿Cuáles son los límites de servicio?** Puede obtener más información sobre la configuración predeterminada y los límites de servicios de Azure RemoteApp en [Límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md). Indíquenos si tiene otras preguntas.
- **¿Cuántos usuarios es necesario tener?** Hay un mínimo de 20 usuarios. Permítame que me repita para que quede más claro: el valor mínimo es 20. Se le facturarán 20. 
- **¿Cuánto cuesta RemoteApp?** Consulte [Detalles de precios de RemoteApp de Azure](../../../pricing/details/remoteapp/).
- **¿Un tipo de colección cuesta más que otro?** Sí, puede que sí, según los requisitos de la colección. Una colección híbrida requiere una conexión desde Azure RemoteApp hasta la red local. Si utiliza una ruta de red virtual (VNET) o una ExpressRoute existente, no se genera ningún costo adicional. Pero si usa una red virtual de Azure nueva y una puerta de enlace o una ExpressRoute, se le cobrará por la [puerta de enlace de VPN](../../../pricing/details/vpn-gateway) o por la [ExpressRoute](../../../pricing/details/expressroute/). Este costo (detallado en los vínculos) se suma a los costos mensuales de Azure RemoteApp.

## Colecciones: cuáles son compatibles, cuáles debe usar, etc.
- **¿Son compatibles las aplicaciones personalizadas de línea de negocio (LOB)?** Sí. Para usar una aplicación personalizada de RemoteApp de Azure, cree una [imagen de plantilla personalizada](remoteapp-create-custom-image.md), y cárguela en la colección de RemoteApp.
- **¿Funcionará mi aplicación personalizada de LOB en RemoteApp de Azure?** La mejor manera de averiguarlo es probándola. Revise los [requisitos de compatibilidad de aplicaciones](http://www.microsoft.com/download/details.aspx?id=18704) y consulte el [Centro de compatibilidad de Escritorio remoto](http://www.rdcompatibility.com/compatibility/default.aspx).
- **¿Qué método de implementación (nube o híbrida) es mejor para mi organización?** Las colecciones híbridas ofrecen una mejor experiencia si desea una integración completa y segura con el inicio de sesión único (SSO) en entornos locales de conectividad de red. Las colecciones de nube ofrecen una manera fácil y ágil para aislar la implementación usando varios métodos de autenticación. Obtenga más información sobre las [Opciones de implementación](remoteapp-whatis.md).
- **Tenemos SQL u otra base de datos en implementación local o en Azure. ¿Qué tipo de implementación debemos usar?** Depende de dónde esté la base de datos SQL o back-end. Si la base de datos está en una red privada, use la colección híbrida. Si la base de datos está expuesta en Internet y permite conexiones cliente para conectarse a ella, puede usar la colección en la nube.
- **¿Qué sucede con las características de asignación de unidad, puerto serie y USB, uso compartido del Portapapeles y redirección de impresora?** Todas estas características se admiten en RemoteApp de Azure. El uso compartido del Portapapeles y la redirección de impresora se habilitan de forma predeterminada. Puede obtener más información sobre la redirección [aquí](remoteapp-redirection.md). 


## Imágenes de plantillas
- **¿Puedo usar una nube o una máquina virtual existente como plantilla para mi colección de RemoteApp?** Sí. Puede crear una imagen basada en una máquina virtual de Azure, usar una de las imágenes que se incluyen con su suscripción o crear una imagen personalizada. Consulte las [Opciones de imagen de RemoteApp](remoteapp-imageoptions.md).


## Opciones de red
- **La colección híbrida requiere una red virtual. ¿Podemos usar nuestra red virtual existente?** Puede hacerlo si la red virtual existente es una red virtual de Azure. Para obtener más información, consulte el "Paso 1: Configuración de la red virtual" en las [Instrucciones de la colección híbrida](remoteapp-create-hybrid-deployment.md).
- **¿Puedo usar una red virtual con una colección de nube?** Sí, claro. Consulte [Creación de una colección en la nube](remoteapp-create-cloud-deployment.md), especialmente el paso 1, para obtener más información.

## Opciones de autenticación



- **¿Qué ocurre con la autenticación? ¿Qué métodos son compatibles?** La colección en la nube admite cuentas de Microsoft y cuentas de Azure Active Directory, que son también cuentas de Office 365. La colección híbrida solo admite cuentas de Azure Active Directory que se hayan sincronizado (con una herramienta como [Sincronización de Azure Active Directory](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) desde una implementación de Windows Server Active Directory; en concreto, sincronizada con la opción de sincronización de contraseña o bien con los Servicios de federación de Active Directory (AD FS) configurados. También puede configurar la [Autenticación multifactor (MFA)](../../services/multi-factor-authentication/).

>[AZURE.NOTE]Los usuarios de Azure Active Directory deben proceder del inquilino asociado a la suscripción. (Puede ver y modificar su suscripción en la pestaña **Configuración** del portal. Consulte [Cambio del inquilino de Azure Active Directory que usa RemoteApp](remoteapp-changetenant.md) para obtener más información).

- **¿Por qué no puedo ofrecer mi acceso a la cuenta de Azure Active Directory?** Los usuarios de Azure Active Directory deben proceder del directorio asociado a la suscripción. Puede ver o modificar ese directorio en la pestaña Configuración del portal. Consulte [Cambio del inquilino de Azure Active Directory que usa RemoteApp](remoteapp-changetenant.md) para obtener más información.

## Clientes: ¿qué dispositivos puedo utilizar para acceder a Azure RemoteApp?
Puede encontrar toda la información del cliente, incluidos los pasos para instalar los distintos clientes en [Acceso a las aplicaciones de Azure RemoteApp](remoteapp-clients.md).

- **¿Qué dispositivos y sistemas operativos admiten las aplicaciones cliente?** Primero, los equipos y las tabletas: 
	- Windows 10 (vista previa del cliente)
	- Windows 8.1 y Windows 8
	- Windows 7 Service Pack 1
	- Mac OS X
	- Windows RT
	- Tabletas Android
	- iPad y teléfonos:
	- iPhone
	- Teléfono Android
	- Windows Phone
 
	[Descargar](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) ahora un cliente de RemoteApp.
- **¿Admite RemoteApp de Azure clientes ligeros?** Sí, se admiten los siguientes clientes ligeros de Windows Embedded:
	- Windows Embedded Standard 7
	- Windows Embedded 8 Standard
	- Windows Embedded 8.1 Industry Pro
	- Windows 10 IoT Enterprise

- **¿Qué versión de Windows Server es compatible con el host de sesión de Escritorio remoto (RDSH)?** Windows Server 2012 R2.

##Soporte y comentarios


- **¿Cuál es el plan de soporte técnico para RemoteApp?** Se ofrecen de forma gratuita los servicios de asistencia para facturación y administración de suscripciones. El soporte técnico está disponible a través de los [planes de servicio de Azure](../../../support/plans/). También puede obtener soporte técnico gratuito de la comunidad a través de nuestro [foro de discusión de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **¿Cómo puedo enviar comentarios?** Visite el [foro de comentarios](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **¿A quién puedo dirigirme para obtener más información sobre RemoteApp de Azure?** Además de nuestro [foro de discusión](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), que es un buen sitio para publicar preguntas, puede unirse al [seminario web Preguntar a los expertos](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html) que se realiza semanalmente, donde se habla de todo lo relacionado con RemoteApp.
- **¿Existe documentación sobre RemoteApp?** Nos encanta que lo haya preguntado. Además del contenido de ayuda en el correspondiente espacio del portal (haga clic en **?** en cualquier página del portal), están disponibles los artículos siguientes para mostrarle todo sobre RemoteApp:
	- **Introducción:**
		- [¿Qué es RemoteApp?](remoteapp-whatis.md)
		- [¿Cuáles son las imágenes de plantilla de RemoteApp?](remoteapp-images.md)
		- [¿Cómo funciona la concesión de licencias?](remoteapp-licensing.md)
		- [¿Cómo funcionan conjuntamente RemoteApp y Office?](remoteapp-o365.md)
		- [¿Cómo funciona la redirección en RemoteApp](remoteapp-redirection.md)?
	- **Implementación:**
		- [Creación de una imagen de plantilla personalizada](remoteapp-create-custom-image.md)
		- [Creación de una colección híbrida](remoteapp-create-hybrid-deployment.md)
		- [Creación de una colección en la nube](remoteapp-create-cloud-deployment.md)
		- [Configuración de Active Directory para RemoteApp](remoteapp-ad.md)
		- [Publicación de una aplicación en RemoteApp](remoteapp-publish.md)
	- **Administración:**
		- [Incorporación de usuarios](remoteapp-user.md)
		- [Prácticas recomendadas para configurar y usar RemoteApp](remoteapp-bestpractices.md)	

	Vídeos También tenemos una serie de vídeos acerca de RemoteApp. Algunos ofrecen una introducción ([Introducción a RemoteApp de Azure](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be)) y otros le guiarán a través de la implementación ([Implementación en la nube](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/) e [Implementación híbrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Écheles un vistazo.

 
### Permítanos ayudarle 
¿Sabía que, además de clasificar este artículo y realizar comentarios abajo, puede realizar cambios en el artículo? ¿Falta algo? ¿Algo no es correcto? ¿Algo de lo que he escrito es simplemente confuso? Desplácese hacia arriba y haga clic en **Editar en GitHub** para realizar cambios que nos llegarán para su revisión y, a continuación, una vez que los aprobemos, verá los cambios y mejoras aquí.

<!---HONumber=AcomDC_0302_2016-->