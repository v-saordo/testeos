<properties
    pageTitle="Habilitación de Enterprise State Roaming en Azure Active Directory | Microsoft Azure"
    description="Preguntas más frecuentes sobre la configuración Enterprise State Roaming en dispositivos de Windows. Enterprise State Roaming proporciona a los usuarios una experiencia unificada a través de sus dispositivos de Windows y reduce el tiempo necesario para configurar un nuevo dispositivo."
    services="active-directory"
    keywords="enterprise state roaming, nube de windows, cómo habilitar enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
    ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

 

# Habilitación de Enterprise State Roaming en Azure Active Directory

Enterprise State Roaming está disponible para cualquier organización con una suscripción Premium de Azure Active Directory (Azure AD). Para más información acerca de cómo obtener una suscripción de Azure AD, consulte la [página de producto de Azure AD](https://azure.microsoft.com/services/active-directory).

Al habilitar Enterprise State Roaming, a su organización se le otorgará automáticamente licencias para una suscripción gratuita a Azure Rights Management. Esta suscripción gratis se limita a cifrar y descifrar los datos de configuración de la empresa; debe tener una suscripción de pago para utilizar todas las funcionalidades de Azure Rights Management.

Después de obtener una suscripción de Azure AD, siga estos pasos para habilitar Enterprise State Roaming:
 
1. Inicie sesión en el Portal de Azure clásico. 
2. A la izquierda, seleccione **ACTIVE DIRECTORY** y después seleccione el directorio para el que desea habilitar Enterprise State Roaming. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)	 
3. Vaya a la pestaña **Configurar**. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.	Desplácese hacia abajo en la página y seleccione **Los usuarios pueden sincronizar la configuración y los datos de aplicaciones empresariales** y después haga clic en **Guardar**. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Para que un dispositivo Windows 10 movilice la configuración con el servicio Enterprise State Roaming, el dispositivo debe autenticarse mediante una identidad de Azure AD. Para los dispositivos que están unidos a Azure AD, el inicio de sesión del usuario principal es la identidad de Azure AD, por lo que no se requiere ninguna configuración adicional. Para los dispositivos que usan Active Directory local, los administradores de TI deben conectar Active Directory local a Azure AD mediante [Azure AD Connect](active-directory-aadconnect.md) y después configurar la directiva de grupo para obligar a los dispositivos cliente a sincronizar automáticamente los datos de usuario con Azure.

## Almacenamiento de datos de sincronización
Los datos de Enterprise State Roaming se hospedan en una o varias [regiones de Azure](https://azure.microsoft.com/regions/) que mejor se alineen con el valor de país establecido en la instancia de Azure AD. Por ejemplo, los clientes cuyo valor de país está establecido en "Francia" se hospedarán en una o varias de las regiones de Azure de Europa, mientras que los clientes que establecen su valor de país de Azure AD en "US", tendrán sus datos hospedados en una o varias de las regiones de Azure dentro de Estados Unidos. El valor de país se establece como parte del proceso de creación del dominio de AD de Azure y no se puede modificar posteriormente.

Para más detalles sobre la ubicación de almacenamiento de datos, genere una incidencia con el [soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## Administración de Enterprise State Roaming
Los administradores de inquilinos de Azure AD pueden habilitar y deshabilitar Enterprise State Roaming en el Portal de Azure clásico. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Los administradores de inquilinos también pueden ver un informe de estado de sincronización de cada usuario y pueden limitar la sincronización de configuración con grupos de seguridad específicos.

##Retención de datos
Los datos sincronizados con Azure a través de Enterprise State Roaming se conservarán indefinidamente a menos que se realice una operación de eliminación manual o se determine que los datos en cuestión sean obsoletos.
 
- **Eliminación manual de datos**: si el administrador de Azure AD desea eliminar manualmente los datos de configuración, el administrador puede generar una incidencia con el [soporte técnico de Azure](https://azure.microsoft.com/support/options/).
 
 - **Eliminación de usuarios**: cuando se elimina un usuario en Azure AD, la cuenta de usuario se colocará en un estado deshabilitado durante 30 días. Al cabo de 30 días, se eliminará la cuenta y los datos de la configuración asociada están sujetos a su eliminación.
 - **Eliminación de inquilinos (directorio)**: la eliminación de un directorio completo en Azure AD es una operación inmediata. Todos los datos de configuración asociados a ese directorio podrán ser eliminados. 
 - **Eliminación de configuración**: si el administrador de Azure AD desea eliminar inmediatamente los datos de configuración de un usuario específico, el administrador puede generar una incidencia con el soporte técnico de Azure. 
- **Datos obsoletos**: los datos a los que no se ha accedido durante un año ("el período de retención") se considerarán obsoletos y podrán ser eliminado de Azure. Los datos obsoletos pueden ser un conjunto específico de valores de configuración de Windows o de la aplicación o toda la configuración de un usuario. Por ejemplo: 
 - Si ningún dispositivo accede a una colección de configuraciones concreta (por ejemplo, una aplicación se quita del dispositivo o un grupo de configuraciones, como "Tema", está deshabilitado para todos los dispositivos de un usuario), esa colección quedará obsoleta tras el período de retención y se puede eliminar. 
 - Si un usuario ha desactivado la sincronización de la configuración en todos sus dispositivos, no se podrá acceder a ninguno de los datos de configuración y todos los datos de configuración para ese usuario quedarán obsoletos y pueden eliminarse tras el período de retención. 
 - Si el administrador del directorio de Azure AD desactiva Enterprise State Roaming para todo el directorio, todos los usuarios de ese directorio dejarán de sincronizar la configuración y todos los datos de configuración de todos los usuarios quedarán obsoletos y podrán eliminarse tras el período de retención. 

- **Recuperación de los datos eliminados**: la directiva de retención de datos no es configurable. Cuando los datos se hayan eliminado permanentemente, no se pueden recuperar. Sin embargo, es importante tener en cuenta que solo se eliminarán los datos de configuración de Azure, no el dispositivo del usuario final. Si cualquier dispositivo más adelante vuelve a conectarse al servicio Enterprise State Roaming, la configuración se sincronizará de nuevo y almacenará en Azure.

## Temas relacionados
- [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Preguntas más frecuentes sobre itinerancia de datos y configuración](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Configuración de MDM y directivas de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->