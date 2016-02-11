<properties
	pageTitle="Configuración del acceso condicional local mediante el Registro de dispositivos de Azure Active Directory | Microsoft Azure"
	description="Guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante Servicios de federación de Active Directory (AD FS) en Windows Server 2012 R2."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>


# Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory

Los dispositivos de propiedad personal de los usuarios pueden marcarse como conocidos para la organización exigiendo a los usuarios la unión de sus dispositivos al área de trabajo para el servicio Registro de dispositivos de Azure Active Directory. A continuación, se ofrece una guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante Servicios de federación de Active Directory (AD FS) en Windows Server 2012 R2.

> [AZURE.NOTE]
Se requiere licencia de Azure AD Premium o de Office 365 al usar dispositivos registrados en directivas de acceso condicional del servicio Registro de dispositivos de Azure Active Directory. Esto incluye las directivas que exigen los Servicios de federación de Active Directory (AD FS) con recursos locales.

Para obtener más información sobre los escenarios de acceso condicional en local, vea [Unión al área de trabajo desde cualquier dispositivo para SSO y segundo factor de autenticación sin interrupciones a través de aplicaciones de la compañía](https://technet.microsoft.com/library/dn280945.aspx).

Estas capacidades están disponibles para los clientes que compren una licencia de Azure Active Directory Premium.

Dispositivos compatibles
-------------------------------------------------------------------------
* Dispositivos Windows 7 unidos a un dominio.
* Dispositivos Windows 8.1 personales y unidos a un dominio.
* iOS 6 y versiones posteriores, para el explorador Safari
* Teléfonos Android 4.0 o versiones posteriores, Samsung GS3 o modelos posteriores, tabletas Samsung Note2 o versiones posteriores.


Requisitos previos de escenario
------------------------------------------------------------------------
* Suscripción a Office 365 o Azure Active Directory Premium
* Inquilino de Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 o versiones posteriores)
* Esquema actualizado en Windows Server 2012 R2
* Suscripción a Azure Active Directory Premium
* Servicios de federación con Windows Server 2012 R2, configurados para SSO en Azure AD
* Windows Server 2012 R2, Web Application Proxy, Microsoft Azure Active Directory Connect (Azure AD Connect). [Descargue Azure AD Connect aquí](http://www.microsoft.com/es-ES/download/details.aspx?id=47594).
* Dominio comprobado. 

Problemas conocidos en esta versión
-------------------------------------------------------------------------------
* Las directivas de acceso condicional basado en dispositivos requieren reescritura de objetos de dispositivo en Active Directory de Azure Active Directory. Los objetos de dispositivo pueden tardar hasta tres horas en reescribirse en Active Directory.
* Los dispositivos iOS7 siempre solicitarán al usuario que seleccione un certificado durante la autenticación de certificados de cliente. 
* Algunas versiones de iOS8 no funcionan antes de iOS 8.3. 

## Escenarios hipotéticos
En este escenario se asume que tiene un entorno híbrido que consta de un inquilino de Azure AD y un Active Directory local. Estos inquilinos deben conectarse mediante Azure AD Connect y con un dominio comprobado y AD FS para SSO. La lista de comprobación siguiente le ayudará a configurar su entorno para la fase descrita anteriormente.

Lista de comprobación: requisitos previos para un escenario de acceso condicional
--------------------------------------------------------------
Conecte un inquilino de Azure AD al Active Directory local.

## Configuración del servicio de registro de dispositivos de Azure Active Directory
Use esta guía para implementar y configurar el servicio Registro de dispositivos de Azure Active Directory para su organización.

En esta guía se asume que ya configuró Windows Server Active Directory y se suscribió a Microsoft Azure Active Directory. Vea los requisitos previos anteriores.

Para implementar el servicio Registro de dispositivos de Azure Active Directory con su inquilino de Azure Active Directory, complete por orden las tareas de la lista de comprobación siguiente. Cuando un vínculo de referencia le lleve a un tema conceptual, vuelva a esta lista de comprobación después de revisar el tema conceptual para continuar con las demás tareas de la lista. Algunas tareas incluirán un paso de validación del escenario que puede ayudarle a confirmar que el paso se completó correctamente.

## Paso 1: habilitación del registro de dispositivos de Azure Active Directory

Siga la lista de comprobación mostrada a continuación para habilitar y configurar el servicio Registro de dispositivos de Azure Active Directory.

| Tarea | Referencia |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Habilite el Registro de dispositivos en el inquilino de Azure Active Directory para permitir que los dispositivos se unan al área de trabajo. De forma predeterminada, Multi-Factor Authentication no está habilitada para el servicio. Aunque se recomienda usar Multi-Factor Authentication al registrar un dispositivo. Antes de habilitar Multi-Factor Authentication en ADRS, asegúrese de que AD FS está configurado para un proveedor de Multi-Factor Authentication. | [Habilitación del Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| Los dispositivos detectarán el servicio Registro de dispositivos de Azure Active Directory buscando registros DNS conocidos. Debe configurar el DNS de su compañía para que los dispositivos puedan detectar el servicio Registro de dispositivos de Azure Active Directory. | [Configuración de la detección del Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |

##Parte 2: implementación de Servicios de federación de Active Directory Windows Server 2012 R2 y configuración de una relación de federación con Azure AD.

| Tarea | Referencia |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Implemente el dominio de Servicios de dominio de Active Directory con las extensiones de esquema de Windows Server 2012 R2. No es preciso actualizar los controladores de dominio a Windows Server 2012 R2. El único requisito es la actualización del esquema. | [Actualizar el esquema de Servicios de dominio de Active Directory] (#Upgrade your Active Directory Domain Services Schema) |
| Los dispositivos detectarán el servicio Registro de dispositivos de Azure Active Directory buscando registros DNS conocidos. Debe configurar el DNS de su compañía para que los dispositivos puedan detectar el servicio Registro de dispositivos de Azure Active Directory. | [Preparación de dispositivos de soporte de Active Directory] (#Prepare your Active Directory to support devices) |


##Parte 3: habilitación de reescritura de dispositivos en Azure AD

| Tarea | Referencia |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Complete la parte 2 de Habilitación de reescritura de dispositivos en Azure AD Connect. Tras la finalización, vuelva a esta guía. | [Habilitación de reescritura de dispositivos en Azure AD] (#Upgrade your Active Directory Domain Services Schema) |
	 

##[Opcional] Parte 4: Habilitar Multi-factor Authentication

Se recomienda encarecidamente configurar una de las distintas opciones de Multi-Factor Authentication. Si desea requerir MFA, consulte [Selección de la solución de seguridad multifactor más adecuada](multi-factor-authentication-get-started.md). Incluye una descripción de cada solución y vínculos para ayudarle a configurar la solución que haya elegido.

## Parte 5: verificación

La implementación ha finalizado. Ya puede probar algunos escenarios. Siga los vínculos siguientes para probar el servicio y familiarizarse con las características


| Tarea | Referencia |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Conecte varios dispositivos al área de trabajo con el Registro de dispositivos de Azure Active Directory Puede conectar dispositivos iOS, Windows y Android | [Unir dispositivos a su área de trabajo con el registro de dispositivos de Azure Active Directory](#Join devices to your workplace using Azure Active Directory Device Registration) |
| Los dispositivos registrados se pueden ver y habilitar o deshabilitar desde el Portal de administrador. En esta tarea verá algunos dispositivos registrados con el Portal de administrador. | [Descripción general sobre el registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| Compruebe que los objetos de dispositivo se reescriben desde Azure Active Directory a Windows Server Active Directory. | [Comprobar que los dispositivos registrados están reescritos en Active Directory] (#Verify registered devices are written-back to Active Director) |
| Ahora que los usuarios pueden registrar sus dispositivos, puede crear en AD FS directivas de acceso a la aplicación que solo permitan dispositivos registrados. En esta tarea creará una regla de acceso a la aplicación y un mensaje de denegación de acceso personalizado | [Crear una directiva de acceso a una aplicación y un mensaje de acceso denegado personalizado] (#Create an application access policy and custom access denied message) |



## Integración de Azure Active Directory con Active Directory local
Esto le ayudará a integrar un inquilino de Azure AD con Active Directory local mediante Azure AD Connect. Aunque los pasos están disponibles en el Portal de Azure, anote las instrucciones especiales que se enumeran en esta sección.

1.	Inicie sesión en el Portal de Azure como administrador.
2.	En el panel izquierdo, seleccione **Active Directory**.
3.	En la pestaña **Directorio**, seleccione su directorio.
4.	Seleccione la pestaña **Integración de directorios**.
5.	En la sección **implementar y administrar**, siga los pasos del 1 al 3 para integrar Azure Active Directory con su directorio local.
  1.	Adición de dominios.
  2.	Instalar y ejecutar Azure AD Connect: para instalar Azure AD Connect, siga las instrucciones que se indican a continuación, [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).
  3. Comprobación y administración de la sincronización de directorios. Las instrucciones de inicio de sesión único están disponibles en este paso. >[AZURE.NOTE] Configurar la federación con AD FS como se describe en el documento vinculado anterior. >[AZURE.NOTE] No es preciso configurar las características de vista previa.
  
   


## Actualizar el esquema de Servicios de dominio de Active Directory
> [AZURE.NOTE]
La actualización del esquema de Active Directory no se puede revertir. Se recomienda realizar la operación primero en un entorno de prueba.

1. Inicie sesión en el controlador de dominio con una cuenta que tenga derechos de administrador de organización y de administrador de esquema.
2. Copie el directorio **[media]\\support\\adprep** y los subdirectorios en uno de los controladores de dominio de Active Directory. 
3. Donde [media] es la ruta de acceso a los medios de instalación de Windows Server 2012 R2.
4. En un símbolo del sistema, navegue hasta el directorio adprep y ejecute: **adprep.exe /forestprep**. Siga las instrucciones en pantalla para completar la actualización del esquema.

## Preparación de Active Directory para que admita los dispositivos
>[AZURE.NOTE] Se trata de una operación que se realiza una sola vez que debe ejecutar para preparar el bosque de Active Directory para admitir los dispositivos. Para completar este procedimiento, debió iniciar sesión con permisos de administrador de organización y el bosque de Active Directory debe tener el esquema de Windows Server 2012 R2.


##Preparación del bosque de Active Directory para que admita los dispositivos

> [AZURE.NOTE]
Se trata de una operación que se realiza una sola vez que debe ejecutar para preparar el bosque de Active Directory para admitir los dispositivos. Para completar este procedimiento, debió iniciar sesión con permisos de administrador de organización y el bosque de Active Directory debe tener el esquema de Windows Server 2012 R2.

### Preparación del bosque de Active Directory

1.	En el servidor de federación, abra una ventana Comandos de Windows PowerShell y escriba: Initialize-ADDeviceRegistration
2.	Cuando le pidan el valor de **ServiceAccountName**, escriba el nombre de la cuenta de servicio que seleccionó como cuenta de servicio de AD FS. Si es una cuenta de gMSA, escríbala con el formato **dominio\\nombreDeCuenta$**. En el caso de una cuenta de dominio, use el formato **dominio\\nombreDeCuenta**.



### Habilitación de la autenticación del dispositivo en AD FS

1. En el servidor de federación, abra la consola de administración de AD FS y navegue hasta **AD FS** > **Directivas de autenticación**.
2. Seleccione **Editar autenticación principal global…** en el panel **Acciones**.
3. Active **Habilitar autenticación de dispositivo** y seleccione **Aceptar**.
4. De forma predeterminada, AD FS quitará periódicamente los dispositivos no usados de Active Directory. Debe deshabilitar esta tarea cuando use el Registro de dispositivos de Azure Active Directory para que los dispositivos se pueden administrar en Azure.


### Deshabilitación de la limpieza de dispositivos no usados
1. En el servidor de federación, abra una ventana Comandos de Windows PowerShell y escriba: Set-AdfsDeviceRegistration -MaximumInactiveDays 0

### Preparación de Azure AD Connect para la reescritura de dispositivos

1.	Complete la parte 1: preparación de AAD Connect. 


## Unión de dispositivos al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

### Unión de un dispositivo iOS al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

El Registro de dispositivos de Azure Active Directory usa el proceso de inscripción de perfil móvil para dispositivos iOS. El proceso comienza con la conexión del usuario a la dirección URL de inscripción del perfil mediante el explorador web Safari. El formato de la dirección URL es el siguiente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Donde `yourdomainname` es el nombre de dominio que se configuró con Azure Active Directory. Por ejemplo, si el nombre de dominio es contoso.com, la dirección URL sería:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Existen varias formas de comunicar la URL a los usuarios. Una forma que se recomienda consiste en publicar esta dirección URL en un mensaje personalizado de acceso denegado a la aplicación en AD FS. Esto se trata en la próxima sección: [Crear una directiva de acceso a una aplicación y un mensaje de acceso denegado personalizado] (#Create an application access policy and custom access denied message)

###Unión de un dispositivo Windows 8.1 al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

1. En el dispositivo Windows 8.1, navegue hasta **Configuración de PC** > **Red** > **Área de trabajo**.
2. Escriba su nombre de usuario en formato UPN. Por ejemplo: dan@contoso.com..
3. Seleccione **Combinar**.
4. Cuando se lo pidan, inicie sesión con sus credenciales. El dispositivo ahora está unido.

###Unión de un dispositivo Windows 7 al área de trabajo mediante el Registro de dispositivos de Azure Active Directory
Para registrar los dispositivos Windows 7 unidos a un dominio debe implementar el paquete de software de registro del dispositivo. El paquete de software se llama Unión al lugar de trabajo para Windows 7 y está disponible para su descarga en el [sitio web de Microsoft Connect](https://connect.microsoft.com/site1164). Las instrucciones para utilizar el paquete están disponibles en [Configuración del registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md).

### Unión de un dispositivo Android al área de trabajo mediante el Registro de dispositivos de Azure Active Directory

El [tema Azure Authenticator para Android](active-directory-conditional-access-azure-authenticator-app.md) incluye instrucciones sobre cómo instalar la aplicación Azure Authenticator en el dispositivo Android y agregar una cuenta profesional. Cuando una cuenta profesional se crea correctamente en un dispositivo Android, el dispositivo queda unido al área de trabajo en la organización.

## Comprobación de que los dispositivos registrados se reescriben en Active Directory
Puede ver y comprobar que los objetos de dispositivo se reescribieron en Active Directory con LDP.exe o con el Editor ADSI. Ambos están disponibles con las herramientas del administrador de Active Directory.

De forma predeterminada, los objetos de dispositivo que se reescriben desde Azure Active Directory se colocan en el mismo dominio que la granja de AD FS.

    CN=RegisteredDevices,defaultNamingContext

## Creación de una directiva de acceso a aplicaciones y un mensaje personalizado de acceso denegado
Considere el siguiente escenario: se crea una aplicación de confianza para usuario autenticado en AD FS y se configura una regla de autorización de emisión que solo permite dispositivos registrados. Ahora solo los dispositivos que están registrados pueden tener acceso a la aplicación. Para que a los usuarios les resulte más fácil obtener acceso a la aplicación, configure un mensaje personalizado de acceso denegado que incluya instrucciones sobre cómo deben unir el dispositivo. Ahora los usuarios disponen de una manera directa de registrar sus dispositivos para tener acceso a una aplicación.

Los pasos siguientes le indicarán cómo implementar este escenario:
>[AZURE.NOTE]
En esta sección se supone que ya configuró una relación de confianza para usuario autenticado para la aplicación en AD FS.

1. Abra la herramienta MMC de AD FS y navegue hasta AD FS > Relaciones de confianza > Relaciones de confianza para usuario autenticado.
2. Busque la aplicación a la que se aplicará la nueva regla de acceso. Haga clic con el botón derecho en la aplicación y seleccione Editar reglas de notificación...
3. Seleccione la pestaña **Reglas de autorización de emisión** y luego **Agregar regla…**
4. En la lista desplegable de plantillas **Regla de notificaciones**, seleccione **Permitir o denegar usuarios según notificación entrante**. Seleccione **Siguiente**.
5. En el campo Nombre de regla de notificaciones:, especifique: **Permitir acceso desde dispositivos registrados**.
6. En la lista desplegable Tipo de notificación entrante:, seleccione **Es usuario registrado**.
7. En el campo Valor de notificación entrante:, especifique: **true**.
8. Seleccione el botón de radio **Permitir acceso a usuarios con esta notificación entrante**.
9. Seleccione **Finalizar** y luego **Aplicar**.
10. Quite todas las reglas que son más permisivas que la regla que acaba de crear. Por ejemplo, quite la regla **Permitir acceso a todos los usuarios** predeterminada.

Ahora, la aplicación está configurada para permitir el acceso solo cuando el usuario procede de un dispositivo registrado y unido al área de trabajo. Para ver directivas de acceso más avanzadas, vea [Administración de riesgos con el control de acceso multifactor](https://technet.microsoft.com/library/dn280949.aspx).

A continuación, configurará un mensaje de error personalizado para la aplicación. El mensaje de error notificará a los usuarios que han de unir el dispositivo al área de trabajo para tener acceso a la aplicación. Puede usar HTML personalizado y Windows PowerShell para crear un mensaje personalizado de acceso denegado a la aplicación.

En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escriba el comando siguiente. Reemplazar partes del comando con elementos específicos de su sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Para poder acceder a esta aplicación, debe registrar el dispositivo.

**Si usa un dispositivo iOS, seleccione este vínculo para conectarlo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Una el dispositivo iOS al área de trabajo.


**Si usa un dispositivo Windows 8.1**, puede conectarlo desde **Configuración de PC ** > **Red** > **Área de trabajo**.


Donde "**nombre de confianza de usuario de confianza**" es el nombre del objeto Confianza de usuario de confianza en AD FS. Donde **yourdomain.com** es el nombre de dominio que configuró en Azure Active Directory. Por ejemplo, contoso.com. Asegúrese de quitar los saltos de línea (si existen) del contenido HTML que se pasa al cmdlet **Set-AdfsRelyingPartyWebContent**.


Ahora, cuando los usuarios accedan a la aplicación desde un dispositivo que no esté registrado con en el servicio Registro de dispositivos de Azure Active Directory, recibirán una página similar a la captura de pantalla siguiente.

![Captura de pantalla de un error cuando los usuarios no han registrado su dispositivo en Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

<!---HONumber=AcomDC_0128_2016-->