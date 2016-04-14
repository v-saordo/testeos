<properties
	pageTitle="Preguntas más frecuentes sobre itinerancia de datos y configuración | Microsoft Azure"
	description="Responde a algunas preguntas que los administradores de TI podrían tener sobre la sincronización de datos de aplicación y la configuración."
	services="active-directory"
    keywords="configuración de enterprise state roaming, nube de windows, preguntas más frecuentes sobre enterprise state roaming"
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
	ms.date="02/03/2016"
	ms.author="femila"/>

# Preguntas más frecuentes sobre itinerancia de datos y configuración

Este tema responde a algunas preguntas que los administradores de TI podrían tener sobre la sincronización de datos de aplicación y la configuración.

## ¿Qué datos se movilizan?
**Configuración de Windows**: la configuración de PC que está integrada en el sistema operativo Windows. Por lo general, estas son opciones que personalizan el equipo del usuario, e incluyen las categorías generales siguientes:

- **Tema**: tema del escritorio, configuración de la barra de tareas, etc. 
- **Configuración de Internet Explorer**: pestañas abiertas recientemente, favoritos, etc.
- **Configuración del explorador Edge**: favoritos, lista de lectura
- **Contraseñas**: contraseñas de Internet, perfiles de Wi-Fi, etc. 
- **Preferencias de idioma**: diseños del teclado, idioma del sistema, fecha y hora, etc. 
- **Facilidad de acceso**: tema de contraste alto, narrador, lupa, etc. 
- **Otra configuración de Windows**: configuración de símbolo del sistema, lista de aplicaciones, etc. 

**Datos de la aplicación**: las aplicaciones universales de Windows pueden escribir los datos de configuración en una carpeta de "movilidad" y todos los datos escritos en esta carpeta se sincronizarán automáticamente. Depende del desarrollador de las aplicaciones individuales el diseñar una aplicación para sacar partido a esta funcionalidad. Para más información sobre cómo desarrollar una aplicación universal de Windows que usa la movilidad, consulte [Almacenar y recuperar la configuración y otros datos de aplicación](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) y el [blog para desarrolladores de itinerancia de datos de aplicación de Windows 8](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## ¿Qué cuenta se usa para la sincronización de configuración?
En Windows 8 y Windows 8.1, la sincronización de configuración siempre utiliza cuentas de Microsoft de consumidor. Los usuarios empresariales tenían la capacidad de conectar una cuenta de Microsoft a su cuenta de dominio de Active Directory para obtener acceso a la sincronización de configuración. En Windows 10, esta funcionalidad "de cuenta de Microsoft conectada" se ha sustituido por un marco de cuenta principal o secundario.
 
La cuenta principal se define como la cuenta usada para iniciar sesión en Windows: puede ser una cuenta de Microsoft, una cuenta de Azure Active Directory (Azure AD), una cuenta de Active Directory local o una cuenta local. Además de la cuenta principal, los usuarios de Windows 10 pueden agregar una o más cuentas de nube secundarias a su dispositivo. Estas cuentas secundarias suelen ser una cuenta de Microsoft, una cuenta de Azure Active Directory o alguna otra cuenta como Gmail o Facebook. Estas cuentas secundarias proporcionan acceso a servicios adicionales, como el inicio de sesión único o a la Tienda Windows, pero no pueden activar la sincronización de configuración.

En Windows 10, solo puede usarse la cuenta principal del dispositivo para la sincronización de configuración (consulte ¿Cómo actualizo desde la sincronización de configuración de cuenta de Microsoft en Windows 8 a la sincronización de configuración de Azure AD en Windows 10? para obtener información sobre excepciones).

Los datos nunca se mezclan entre las distintas cuentas de usuario en el dispositivo. Hay dos reglas para la sincronización de configuración: - La configuración de Windows se movilizará siempre con la cuenta principal. -Los datos de la aplicación se etiquetarán con la cuenta utilizada para adquirir la aplicación. Solo se sincronizarán las aplicaciones etiquetadas con la cuenta principal. El etiquetado de la propiedad de la aplicación se determina cuando se instala una aplicación a través de la Tienda Windows o cuando la aplicación se cargan a través de la administración de dispositivos móviles (MDM).

Si no se puede identificar al propietario de una aplicación, se movilizará con la cuenta principal. Si un dispositivo se actualiza desde Windows 8 o Windows 8.1 a Windows 10, todas las aplicaciones se etiquetarán como adquiridas por la cuenta de Microsoft, ya que, en general, la mayoría de las aplicaciones se adquirieron a través de la Tienda Windows y no había compatibilidad de la Tienda Windows con cuentas de Azure AD antes de Windows 10. Si se instala una aplicación a través de una licencia sin conexión, la aplicación se etiquetará mediante la cuenta principal en el dispositivo.

>[AZURE.NOTE]  
La capacidad de conectar una cuenta de Microsoft con una cuenta de dominio y de tener toda la sincronización de datos del usuario en la cuenta de Microsoft (es decir, movilidad de la cuenta de Microsoft a través de la funcionalidad "cuenta de Microsoft y Active Directory conectados") se quita de los dispositivos Windows 10 que se unen a un entorno Active Directory o Azure AD conectado.
 
## ¿Cómo actualizo desde la sincronización de configuración de cuenta de Microsoft en Windows 8 a la sincronización de configuración de Azure AD en Windows 10?
Un usuario unido al dominio de Active Directory que ejecuta Windows 8 o Windows 8.1 con una cuenta de Microsoft conectada sincronizará la configuración a través de la cuenta de usuario de Microsoft. Después de actualizar a Windows 10, los usuarios unidos a dominio sincronizarán la configuración de usuario a través de la cuenta de Microsoft, siempre que el dominio de Active Directory no se conecte con Azure AD. Si el dominio de Active Directory local se conecta con Azure AD, el dispositivo del usuario comenzará a intentar sincronizar la configuración mediante la cuenta de Azure AD conectada. Si el administrador de Azure AD no permite Enterprise State Roaming, los usuarios con una cuenta de Azure AD conectada detendrán la sincronización de la configuración. Después de habilitar la sincronización de configuración a través de Azure AD, los usuarios de Windows 10 iniciarán inmediatamente la sincronización de configuración de Windows con Azure AD si el usuario inicia sesión con una identidad de Azure AD.

Los usuarios que almacenan sus datos personales en los dispositivos corporativos deben ser conscientes de que el sistema operativo Windows y los datos de la aplicación comenzarán a sincronizarse en Azure AD. Esto tiene las implicaciones siguientes: - Los usuarios de cuentas personales de Microsoft verán que su configuración se "separa" lentamente de la configuración en las cuentas de Azure AD organizativas, ya que la sincronización de la configuración de la cuenta de Microsoft y de Azure AD utiliza ahora cuentas distintas. -Los datos personales, como contraseñas Wi-Fi, credenciales de web y favoritos y pestañas de Internet Explorer previamente sincronizados a través de una cuenta de Microsoft conectada se sincronizarán a través de Azure AD.


## ¿Cómo funciona la interoperabilidad de la cuenta de Microsoft y Enterprise State Roaming de Azure AD? 
En las versiones de 2015 de Windows 10, Enterprise State Roaming solo se admite para una única cuenta a la vez. Si el usuario inicia sesión en Windows mediante una cuenta de organización de Azure AD, todos los datos se sincronizarán a través de Azure AD. Si el usuario inicia sesión en Windows mediante una cuenta personal de Microsoft, todos los datos se sincronizarán a través de la cuenta de Microsoft. Los datos de aplicación se movilizarán con la cuenta de inicio de sesión principal en el dispositivo si la licencia de la aplicación es propiedad de la cuenta principal. Las aplicaciones de las cuentas secundarias no sincronizarán los datos de la aplicación en Windows 10.

## ¿Se sincroniza la configuración para las cuentas de Azure AD desde varios inquilinos?
Cuando varias cuentas de AD Azure de distintos inquilinos de Azure AD se encuentran en el mismo dispositivo, debe actualizar el registro del dispositivo para comunicarse con el servicio Azure Rights Management Service (RMS) para cada inquilino de Azure AD.

1. En primer lugar, necesita el GUID para cada inquilino de Azure AD. Abra el Portal de Azure clásico y seleccione un inquilino de Azure AD. El GUID del inquilino es la dirección URL en la barra de direcciones del explorador, como sigue: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Cuando ya disponga del GUID, tendrá que agregar la siguiente clave del Registro: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<tenant ID GUID>**. Desde la clave <**tenant ID GUID**>, cree un valor de cadena múltiple (REG-MULTI-SZ) denominado **AllowedRMSServerUrls** y para sus datos, especifique las direcciones URL de los puntos de distribución de licencias de los demás inquilinos de Azure a los que accede el dispositivo. 
3. Puede encontrar las direcciones URL de los puntos de distribución de licencias mediante la ejecución del cmdlet **Get-AadrmConfiguration**. Si los valores para **LicensingIntranetDistributionPointUrl** y **LicenseingExtranetDistributionPointUrl** son diferentes, especifique ambos valores. Si los valores son iguales, especifique solo un valor.


## ¿Cuáles son las opciones de configuración de movilidad para aplicaciones Windows clásicas?
La movilidad solo funciona para las aplicaciones universales de Windows. Hay dos opciones disponibles para habilitar la movilidad en una aplicación de Windows clásica:

- Con Project Centennial, puede convertir fácilmente una aplicación de Windows clásica en una aplicación universal de Windows. A partir de aquí, el desarrollador de la aplicación requerirá cambios mínimos en el código para poder aprovechar las ventajas de la itinerancia de datos de la aplicación de Azure AD. Esta es la ruta de acceso recomendada para habilitar la itinerancia de datos de aplicación de Win32. 
- Mediante el generador de plantillas [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx), se puede crear una plantilla de configuración personalizada y aplicarla a la aplicación cada vez que se inicie. Esta opción no requiere que el desarrollador de la aplicación cambie el código de la aplicación, pero en las versiones de 2015 de Windows 10, UE-V se limita a la movilidad de Active Directory local para aquellos clientes que han comprado Microsoft Desktop Optimization Package. En el futuro, Microsoft podría investigar formas de integrar UE-V profundamente en Windows y ampliar UE-V para movilizar la configuración a través de la nube de Azure AD. 

## ¿Se pueden almacenar configuraciones sincronizados y datos locales?
Enterprise State Roaming está diseñado para almacenar datos de configuración en la nube de Azure. UE-V ofrece una solución de movilidad local para Windows 10.

## ¿Quién administra los datos que se están movilizando?
El administrador de inquilinos administrará los datos y se almacenarán en un centro de datos de Azure. Todos los datos de usuario se cifran en tránsito y en reposo en la nube mediante Azure Rights Management (Azure RMS). Esto es una mejora comparado con la sincronización de configuración basada en la cuenta de Microsoft, donde solo determinados datos confidenciales, como las credenciales de usuario, se cifran antes de dejar el dispositivo.

Microsoft se compromete a proteger los datos de los clientes. Los datos de configuración de los usuarios empresariales se cifran automáticamente por Azure RMS cada vez que salen de un dispositivo de Windows 10, de tal forma que ningún usuario pueda leer estos datos. Si la organización tiene una suscripción de pago para Azure RMS, puede utilizar otras características de Azure RMS, como el seguimiento y revocación de documentos, proteger automáticamente correos electrónicos que contienen información confidencial y administrar sus propias claves (la solución "bring your own key" solución, también conocida como BYOK). Para más información acerca de estas características y cómo funciona Azure RMS, consulte [¿Qué es Azure Rights Management](https://technet.microsoft.com/jj585026.aspx)?

## ¿Puedo administrar la sincronización para una aplicación o configuración específica?
En Windows 10, no hay ninguna configuración de MDM o de directiva de grupo para deshabilitar la movilidad en una aplicación individual. Los administradores de inquilinos pueden deshabilitar la sincronización de los datos de la aplicación en todas las aplicaciones en un dispositivo administrado, pero no hay ningún control más preciso de nivel de aplicación o dentro de la aplicación.

## ¿Qué puede hacer un usuario individual para habilitar o deshabilitar la movilidad?
En la aplicación de configuración, vaya a Cuentas -> Sincronizar la configuración. En esta página, puede ver qué cuenta se usa para movilizar la configuración y puede habilitar o deshabilitar los grupos de configuración individuales que se van a movilizar.
 
##¿Cuál es la recomendación actual de Microsoft para habilitar la movilidad en Windows 10? 
Microsoft tiene algunas soluciones de movilidad de configuración disponibles, incluidos los perfiles de usuario móviles, UE-V y la movilidad de Azure AD. Si su organización no está preparada o no está cómoda con el desplazamiento de datos a la nube, Microsoft recomienda utilizar UE-V como principal tecnología móvil. Si su organización requiere compatibilidad de movilidad para las aplicaciones de Windows clásicas, pero está dispuesta a desplazarse a la nube, Microsoft recomienda que utilice tanto sincronización de configuración empresarial como UE-V. Aunque la sincronización de configuración empresarial y UE-V son tecnologías muy similares, no son mutuamente excluyentes y hoy en día se complementan entre sí para garantizar que su organización proporciona los servicios móviles que necesitan los usuarios. Cuando se utiliza la sincronización de configuración empresarial y UE-V, se aplican las siguientes reglas:

- Enterprise State Roaming es el agente de movilidad principal en el dispositivo. UE-V se utiliza para complementar el "intervalo de Win32". 
- La movilidad de UE-V para la configuración de Windows y los datos de aplicación UWP modernos deben deshabilitarse porque ya están cubiertos por Enterprise State Roaming. 

## ¿Qué ocurre cuando mi organización adquiere Azure RMS después de usar la movilidad durante un tiempo? 
Si su organización ya está usando la movilidad en Windows 10 con la suscripción gratis de Azure RMS, si compra una suscripción de Azure RMS de pago, no afectará a la funcionalidad de la característica de movilidad y no será necesario ningún cambio de configuración por el administrador de TI.

## Problemas conocidos

- El inicio de sesión de la tarjeta inteligente o de la tarjeta inteligente virtual en Windows hace que la sincronización de configuración deje de funcionar. Si intenta iniciar sesión en el dispositivo con una tarjeta inteligente o tarjeta inteligente virtual, la sincronización dejará de funcionar. Las futuras actualizaciones de Windows 10 pueden resolver este problema.
- Hay un problema con la sincronización de **favoritos** en Internet Explorer. La sincronización de favoritos de Internet Explorer está deshabilitada temporalmente para evitar que surjan otros problemas. Las futuras actualizaciones de Windows 10 resolverán este problema.


## Temas relacionados
- [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitación de Enterprise State Roaming en Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Configuración de MDM y directivas de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->