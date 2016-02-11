<properties 
	pageTitle="Introducción a la administración de contraseñas en Azure AD | Microsoft Azure" 
	description="La administración de contraseñas permite que los usuarios restablezcan sus propias contraseñas, descubre los requisitos previos para el restablecimiento de contraseñas y permite que la Escritura diferida de contraseñas administre las contraseñas locales en Active Directory." 
	services="active-directory"
	keywords="Administración de contraseñas de Active Directory, administración de contraseñas, restablecimiento de contraseñas de Azure AD" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/25/2016" 
	ms.author="asteen"/>

# Introducción a la administración de contraseñas
Permitir que los usuarios administren sus propias contraseñas de Active Directory local o de Azure Active Directory en la nube solo requiere de unos pocos pasos simples. Una vez que se asegure de haber cumplido algunos requisitos previos simples, tendrá habilitado el cambio y el restablecimiento de contraseñas para toda su organización de manera inmediata. Este artículo le guiará a través de los conceptos siguientes:

* [**Cómo permitir que los usuarios restablezcan sus contraseñas de Azure Active Directory en la nube**](#enable-users-to-reset-their-azure-ad-passwords)
 - [Requisitos previos para el restablecimiento de contraseña de autoservicio](#prerequisites)
 - [Paso 1: configurar la directiva de restablecimiento de contraseña](#step-1-configure-password-reset-policy)
 - [Paso 2: agregar datos de contacto del usuario de prueba](#step-2-add-contact-data-for-your-test-user)
 - [Paso 3: restablecer la contraseña como usuario](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Cómo permitir que los usuarios restablezcan o cambien sus contraseñas de Active Directory local**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [Requisitos previos de escritura diferida de contraseñas](#writeback-prerequisites)
 - [Paso 1: descargar la versión más reciente de Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect a través de la interfaz de usuario o PowerShell y comprobarla](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [Paso 3: configurar el firewall](#step-3-configure-your-firewall)
 - [Paso 4: configurar los permisos adecuados](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [Paso 5: restablecer la contraseña como usuario y comprobarla](#step-5-reset-your-ad-password-as-a-user)

## Cómo permitir que los usuarios restablezcan sus contraseñas de Azure AD
En esta sección se explica cómo habilitar el restablecimiento de contraseña para el directorio en la nube de AAD, cómo registrar a los usuarios para el restablecimiento de contraseña de autoservicio y, finalmente, cómo realizar un restablecimiento de contraseña de autoservicio de prueba como usuario.

- [Requisitos previos para el restablecimiento de contraseña de autoservicio](#prerequisites)
- [Paso 1: configurar la directiva de restablecimiento de contraseña](#step-1-configure-password-reset-policy)
- [Paso 2: agregar datos de contacto del usuario de prueba](#step-2-add-contact-data-for-your-test-user)
- [Paso 3: restablecer la contraseña como usuario](#step-3-reset-your-azure-ad-password-as-a-user)


###  Requisitos previos
Antes de poder habilitar y utilizar el restablecimiento de contraseña de autoservicio, debe completar los siguientes requisitos previos:

- Crear un inquilino de AAD. Para obtener más información, consulte [Introducción a Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).
- Obtener una suscripción de Azure. Para obtener más información, consulte [¿Qué es un inquilino de Azure AD?](active-directory-administer.md#what-is-an-azure-ad-tenant)
- Asociar su inquilino de AAD con su suscripción de Azure. Para obtener más información, consulte [Cómo se asocian las suscripciones a Azure con Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
- Actualizar a Azure AD Premium o Basic. Para obtener más información, consulte [Ediciones de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

  >[AZURE.NOTE] Para habilitar el restablecimiento de contraseña de autoservicio, debe actualizar a Azure AD Premium o a Azure AD Basic. Para obtener más información, consulte Ediciones de Azure Active Directory. Esta información incluye instrucciones detalladas sobre cómo suscribirse a Azure AD Premium o Basic, cómo activar su plan de licencias y el acceso de Azure AD y cómo asignar acceso a las cuentas de administrador y de usuario.
  
- Crear, al menos, una cuenta de administrador y una cuenta de usuario en el directorio de AAD.
- Asignar una licencia de AAD Premium o Basic a la cuenta de administrador y a la cuenta de usuario que creó.

### Paso 1: configurar la directiva de restablecimiento de contraseña
Para configurar la directiva de restablecimiento de contraseña del usuario, complete los pasos siguientes:
 
1.	Abra el explorador de su preferencia y vaya al [Portal de administración de Azure](https://manage.windowsazure.com).
2.	En el [Portal de administración de Azure](https://manage.windowsazure.com), encuentre la **extensión de Active Directory** en la barra de navegación izquierda.

    ![Administración de contraseñas en Azure AD][001]

3. En la pestaña **Directorio**, haga clic en el directorio en el que desea configurar la directiva de restablecimiento de contraseña del usuario, por ejemplo, Wingtip Toys.

    ![][002]

4.	Haga clic en la pestaña **Configurar**.

    ![][003]

5.	En la pestaña **Configurar**, desplácese hacia abajo hasta la sección **Directiva de restablecimiento de contraseña del usuario**. Ahí puede configurar todos los aspectos de la directiva de restablecimiento de contraseña del usuario de un directorio determinado.

    >[AZURE.NOTE] Esta **directiva se aplica solo a los usuarios finales de su organización, no a los administradores**. Por motivos de seguridad, Microsoft controla la directiva de restablecimiento de contraseña de los administradores. Si no ve esta sección, asegúrese de haberse suscrito a Azure Active Directory Premium o Basic y de haber **asignado una licencia** a la cuenta de administrador que configura esta característica.

    ![][004]

6.	Para configurar la directiva de restablecimiento de contraseña del usuario, deslice el botón de alternancia **Usuarios habilitados para restablecer la contraseña** a la opción **Sí**. Con esto se revelan muchos controles más que le permitirán configurar cómo funciona esta característica en el directorio. Puede personalizar el restablecimiento de contraseña como desee. Si desea obtener más información sobre qué hace cada uno de los controles de la directiva de restablecimiento de contraseña, consulte [Personalización de la administración de contraseñas de Azure AD](active-directory-passwords-customize).

    ![][005]

7.	Después de configurar la directiva de restablecimiento de contraseña del usuario como desea para su inquilino, haga clic en el botón **Guardar** en la parte inferior de la pantalla.

  >[AZURE.NOTE] Se recomienda una directiva de restablecimiento de contraseña del usuario de dos desafíos, para que pueda ver cómo funciona esta funcionalidad en el caso más complejo.

  ![][006]

### Paso 2: agregar datos de contacto del usuario de prueba
Tiene varias opciones para especificar los datos de los usuarios de su organización que se usarán para restablecer la contraseña.

-	Editar usuarios en el [Portal de administración de Azure](https://manage.windowsazure.com) o en el [Portal de administración de Office 365](https://portal.microsoftonline.com)
-	Usar AAD Connect para sincronizar las propiedades de usuario en Azure AD desde un dominio de Active Directory local
-	Usar Windows PowerShell para editar las propiedades de usuario
-	Permitir a los usuarios que registren sus propios datos guiándolos al portal de registro en [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
-	Requerir a los usuarios que se registren para el restablecimiento de contraseña al iniciar sesión en el panel de acceso [http://myapps.microsoft.com](http://myapps.microsoft.com) mediante el establecimiento de la opción de configuración del restablecimiento de contraseña de autoservicio **Requerir que los usuarios se registren cuando inicien sesión en el panel de acceso** en **Sí**

Si desea obtener más información sobre qué datos se usan para el restablecimiento de contraseña, además de cualquier requisito de formato de esos datos, consulte [¿Qué datos se utilizan para el restablecimiento de contraseña?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)

#### Para agregar datos de contacto del usuario a través del Portal de registro del usuario
1.	Para usar el Portal de registro para el restablecimiento de contraseña, debe proporcionar a los usuarios de su organización un vínculo a esta página ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) o activar la opción para requerir que los usuarios se registren automáticamente. Una vez que hagan clic en este vínculo, se les pedirá iniciar sesión con su cuenta profesional. Después de hacerlo, verán la página siguiente:

    ![][007]

2.	Aquí los usuarios pueden proporcionar y comprobar su teléfono móvil, su dirección de correo electrónico alternativa o las preguntas de seguridad. La comprobación de un teléfono móvil tiene el aspecto siguiente.

    ![][008]

3.	Una vez que un usuario especifica esta información, se actualizará la página para indicar que la información es válida (aparece encubierta abajo). Si hace clic en el botón **Finalizar** o en el botón **Cancelar**, el usuario se desplazará hasta el panel de acceso.

    ![][009]

4.	Una vez que un usuario compruebe ambos datos, su perfil se actualizará con la información proporcionada. En este ejemplo, el número de **teléfono de la oficina** se especificó manualmente, por lo que el usuario también puede usarlo como método de contacto para restablecer su contraseña.

    ![][010]

### Paso 3: restablecer la contraseña de Azure AD como usuario
Ahora que configuró una directiva de restablecimiento de usuario y especificó los detalles de contacto del usuario, este puede realizar un restablecimiento de contraseña de autoservicio.

#### Para realizar un restablecimiento de contraseña de autoservicio
1.	Si va a un sitio como [**portal.microsoftonline.com**](http://portal.microsoftonline.com), verá una pantalla de inicio de sesión similar a la siguiente. Haga clic en el vínculo **¿No puede tener acceso a su cuenta?** para probar la interfaz de usuario del restablecimiento de contraseña.

    ![][011]

2.	Una vez que hace clic en **¿No puede tener acceso a su cuenta?**, verá una página nueva que le pedirá un **Id. de usuario** para el cual desea restablecer una contraseña. Escriba aquí el **Id. de usuario** de prueba, escriba el código CAPTCHA y, luego, haga clic en **Siguiente**.

    ![][012]

3.	Debido a que el usuario especificó un **teléfono de la oficina**, un **teléfono móvil** y un **correo electrónico alternativo** en este caso, puede ver que tiene todas esas opciones para pasar el primer desafío.

    ![][013]

4.	En este caso, elija **llamar** primero al **teléfono de la oficina**. Tenga en cuenta que, cuando se selecciona un método basado en teléfono, los usuarios deberán **comprobar su número de teléfono** antes de poder restablecer sus contraseñas. Esto es para evitar que usuarios malintencionados realicen llamadas no deseadas a los números de teléfono de los usuarios de la organización.

    ![][014]

5.	Una vez que el usuario confirme el número de teléfono, un clic en el muro de llamadas hará que aparezca un control de número y sonará su teléfono. Cuando conteste el teléfono, se reproducirá un mensaje que indicará que el **usuario debe presionar "#"** para comprobar la cuenta. Al presionar esta tecla, se comprobará automáticamente que el usuario cumple el primer desafío y la interfaz de usuario avanza al segundo paso de comprobación.

    ![][015]

6.	Una vez que se cumple el primer desafío, la interfaz de usuario se actualiza automáticamente para quitarlo de la lista de opciones que tiene el usuario. En este caso, como usó primero el **teléfono de la oficina**, solo quedan **Teléfono móvil** y **Correo electrónico alternativo** como opciones válidas para utilizarlas como el desafío para el segundo paso de comprobación. Haga clic en la opción **Enviar un mensaje de correo electrónico a mi dirección alternativa**. Una vez hecho eso, al presionar el correo electrónico se enviará un mensaje de correo electrónico a la dirección alternativa existente en el archivo.

    ![][016]

7.	El siguiente es un ejemplo de un correo electrónico que verán los usuarios; observe la personalización de marca del inquilino:

    ![][017]

8.	Cuando llegue el correo electrónico, se actualizará la página y podrá escribir el código de comprobación que aparece en el correo electrónico en el cuadro de entrada que se muestra a continuación. Una vez que se escribe un código adecuado, se ilumina el botón Siguiente y puede pasar al segundo paso de comprobación.

    ![][018]

9.	Una vez cumplidos los requisitos de la directiva organizativa, podrá elegir una contraseña nueva. La contraseña se valida cuando cumple los requisitos de contraseña "segura" de AAD (consulte [Directiva de contraseñas de Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)) y aparece un validador de seguridad que indica al usuario si la contraseña escrita satisface la directiva.

    ![][019]

10.	Una vez que proporciona contraseñas coincidentes que satisfacen la directiva organizativa, se restablece la contraseña y puede iniciar inmediatamente sesión con la contraseña nueva.

    ![][020]


## Permitir que los usuarios restablezcan o cambien sus contraseñas de AD

En esta sección se explica cómo configurar el restablecimiento de contraseña para escribir contraseñas en diferido en Active Directory local.

- [Requisitos previos de escritura diferida de contraseñas](#writeback-prerequisites)
- [Paso 1: descargar la versión más reciente de Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
- [Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect a través de la interfaz de usuario o PowerShell y comprobarla](#step-2-enable-password-writeback-in-azure-ad-connect)
- [Paso 3: configurar el firewall](#step-3-configure-your-firewall)
- [Paso 4: configurar los permisos adecuados](#step-4-set-up-the-appropriate-active-directory-permissions)
- [Paso 5: restablecer la contraseña como usuario y comprobarla](#step-5-reset-your-ad-password-as-a-user)


### Requisitos previos de escritura diferida
Antes de poder habilitar y utilizar la escritura diferida, debe asegurarse de completar los siguientes requisitos previos:

- Tener un inquilino de Azure AD con Azure AD Premium habilitado. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
- Se configuró y habilitó el restablecimiento de contraseña en el inquilino. Para obtener más información, consulte [Permitir que los usuarios restablezcan o cambien sus contraseñas de AD](#enable-users-to-reset-their-azure-ad-passwords)
- Tiene, al menos una cuenta de administración y una cuenta de usuario de prueba con una licencia de Azure AD Premium que puede utilizar para probar esta característica. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

  >[AZURE.NOTE] Asegúrese de que la cuenta de administrador que usa para habilitar la escritura diferida de contraseña sea una cuenta de administrador de la nube (creada en Azure AD) y no una cuenta federada (creada en AD local y sincronizada en Azure AD).
  
- Tiene una implementación local de AD de uno o de varios bosques que ejecuta Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 o Windows Server 2012 R2 con los Service Packs más recientes instalados.

  >[AZURE.NOTE] Si ejecuta una versión anterior de Windows Server 2008 o 2008 R2, puede seguir utilizando esta característica, pero necesita [descargar e instalar KB 2386717](https://support.microsoft.com/kb/2386717) antes de poder aplicar la directiva de contraseña de AD local en la nube.
  
- Tiene instalada la herramienta Azure AD Connect y preparó el entorno de AD para que se sincronice con la nube. Para obtener más instrucciones, consulte [Uso de la infraestructura de identidad local en la nube](active-directory-aadconnect.md).

  >[AZURE.NOTE] Antes de probar la escritura diferida de contraseñas, asegúrese de finalizar primero una importación y sincronización completas de AD y Azure AD en Azure AD Connect.

- Si utiliza Sincronización de Azure AD o Azure AD Connect, se deberá abrir el puerto **TCP 443** de salida (y, en algunos casos, el puerto **TCP 9350-9354**). Vea el [Paso 3: configurar el firewall](#step-3-configure-your-firewall) para obtener más información. Ya no se admite el uso de DirSync para este escenario. Si todavía usa DirSync, actualice a la versión más reciente de Azure AD Connect antes de implementar la escritura diferida de contraseñas.

  >[AZURE.NOTE] Recomendamos encarecidamente que quienes utilicen las herramientas Sincronización de Azure AD o DirSync actualicen a la versión más reciente de Azure AD Connect para asegurarse de contar con la mejor experiencia posible y las características nuevas tan pronto como se lanzan.
  

### Paso 1: descargar la versión más reciente de Azure AD Connect
La escritura diferida de contraseñas está disponible en las versiones de Azure AD Connect o en la herramienta Sincronización de Azure AD con el número de versión **1.0.0419.0911** o posterior. La escritura diferida de contraseñas con desbloqueo automático de cuenta está disponible en las versiones de Azure AD Connect o en la herramienta Sincronización de Azure AD con el número de versión **1.0.0485.0222** o posterior. Si ejecuta una versión anterior, al menos actualice a esta versión antes de continuar. [Haga clic aquí para descargar la versión más reciente de Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect).

#### Para comprobar la versión de Sincronización de Azure AD
1.	Vaya a **%ProgramFiles%\Azure Active Directory Sync**. 
2.	Busque el archivo ejecutable **ConfigWizard.exe**.
3.	Haga clic con el botón derecho en el archivo ejecutable y seleccione la opción **Propiedades** del menú contextual.
4.	Haga clic en la pestaña **Detalles**.
5.	Busque el campo **Versión del archivo**.

    ![][021]

Si el número de la versión es mayor o igual que **1.0.0419.0911** o si instala Azure AD Connect, puede ir directamente al [Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect a través de la interfaz de usuario o PowerShell y comprobarla](#step-2-enable-password-writeback-in-azure-ad-connect).

 >[AZURE.NOTE] Si es la primera vez que instala la herramienta Azure AD Connect, se recomienda que siga algunas prácticas recomendadas para preparar el entorno para la sincronización de directorios. Antes de instalar la herramienta Azure AD Connect, debe activar la sincronización de directorios en el [Portal de administración de Office 365](https://portal.microsoftonline.com) o en el [Portal de administración de Azure](https://manage.windowsazure.com). Para obtener más información, consulte [Administración de Azure AD Connect](active-directory-aadconnect-whats-next.md).


### Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect
Ahora que descargó la herramienta Azure AD Connect, está listo para habilitar la escritura diferida de contraseñas. Puede hacerlo de dos maneras. Puede habilitar la escritura diferida de contraseñas en la pantalla de características opcionales del asistente para la configuración de Azure AD Connect, o bien puede habilitar a través de Windows PowerShell.

#### Para habilitar la escritura diferida de contraseñas en el asistente de configuración
1.	En el **equipo con Sincronización de directorios**, abra el asistente para la configuración de **Azure AD Connect**.
2.	Haga clic en los pasos hasta llegar a la pantalla de configuración de **características opcionales**.
3.	Active la opción **Escritura diferida de contraseñas**.

    ![][022]

4.	Complete el asistente. En la última página aparecerá un resumen de los cambios, el que incluirá el cambio de la configuración de la escritura diferida de contraseñas.

> [AZURE.NOTE] Puede deshabilitar la escritura diferida de contraseñas en cualquier momento si ejecuta nuevamente este asistente y desactive la característica, o bien establezca la opción **Escribir contraseñas en diferido en el directorio local** en **No** en la sección **Directiva de restablecimiento de contraseña del usuario** de la pestaña **Configurar** del directorio en el [Portal de administración de Azure](https://manage.windowsazure.com). Para obtener más información sobre cómo personalizar la experiencia de restablecimiento de contraseña, consulte [Personalización de la administración de contraseñas de Azure AD](active-directory-passwords-customize.md).

#### Para habilitar la escritura diferida de contraseñas con Windows PowerShell
1.	En el **equipo con Sincronización de directorios**, abra una nueva **ventana de Windows PowerShell con privilegios elevados**.
2.	Si el módulo todavía no está cargado, escriba el comando `Import-Module ADSync` para cargar los cmdlets de Azure AD Connect en la sesión actual.
3.	Para obtener la lista de los conectores de AAD del sistema, ejecute el cmdlet `Get-ADSyncConnector` y almacene los resultados en `$aadConnectorName`
4.	Para obtener el estado actual de la escritura diferida del conector actual, ejecute el siguiente cmdlet: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.	Habilite la escritura diferida de contraseñas mediante la ejecución del cmdlet: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE] Si se le solicita una credencial, asegúrese de que la cuenta de administrador que especifica para AzureADCredential sea una**cuenta de administrador de la nube (creada en Azure AD)** y no una cuenta federada (creada en AD local y sincronizada en Azure AD)
> [AZURE.NOTE] Puede deshabilitar la escritura diferida de contraseñas mediante la repetición de las mismas instrucciones anteriores, pero pasando `$false` en el paso, o bien al establecer la opción **Escribir contraseñas en diferido en el directorio local** en **No** en la **sección Directiva de restablecimiento de contraseña del usuario** de la pestaña **Configurar** en el [Portal de administración de Azure](https://manage.windowsazure.com).

#### Comprobar que la configuración se realizó correctamente
Una vez que la configuración se realice correctamente, verá el mensaje La escritura diferida de restablecimiento de contraseña está habilitada en la ventana de Windows PowerShell o un mensaje de éxito en la interfaz de usuario de la configuración.

Para comprobar que el servicio se instaló correctamente, también puede abrir el Visor de eventos, ir al registro de eventos de la aplicación y buscar el evento **31005 - OnboardingEventSuccess** en el **PasswordResetService** de origen.

  ![][023]

### Paso 3: configurar el firewall
Una vez que haya habilitado la escritura diferida de contraseñas en la herramienta Azure AD Connect, deberá asegurarse de que el servicio se puede conectar a la nube.

1.	Una vez que finalice la instalación, si bloquea las conexiones salientes desconocidas en el entorno, también deberá agregar las reglas siguientes al firewall. Asegúrese de reiniciar su máquina con AAD Connect después de realizar estos cambios:
   - Permita conexiones salientes en el puerto TCP 443
   - Permita conexiones salientes a https://ssprsbprodncu-sb.accesscontrol.windows.net/ 
   - Cuando use un servidor proxy o tenga problemas generales de conectividad, permita conexiones salientes en el puerto TCP 9350-9354

### Paso 4: configurar los permisos adecuados de Active Directory
Para cada bosque que contiene usuarios con contraseñas que se restablecerán, si X es la cuenta que se especificó para ese bosque en el asistente de configuración (durante la configuración inicial), X debe contar con los derechos extendidos **Restablecer contraseña**, **Cambiar contraseña**, **Escribir permisos** en `lockoutTime` y **Escribir permisos** en `pwdLastSet` sobre el objeto raíz de cada dominio del bosque en cuestión. El derecho debe estar marcado como heredado por todos los objetos de usuario.

Si no está seguro de cuál es la cuenta a la que se hace referencia en el párrafo anterior, abra la UI de configuración de Azure Active Directory Connect y haga clic en la opción **Revisar su solución**. La cuenta a la que debe agregar permisos se muestra subrayada en rojo en la captura de pantalla siguiente.

**<font color="red">Asegúrese de establecer este permiso para cada dominio de cada bosque del sistema, si no, la escritura diferida de contraseñas no funcionará correctamente.</font>**

  ![][032]

  Al establecer estos permisos se permitirá que la cuenta de servicio de agente de administración de cada bosque administre las contraseñas en nombre de las cuentas de usuario dentro de ese bosque. Si olvida asignar estos permisos, y aunque la escritura diferida aparecerá como correctamente configurada, los usuarios encontrarán errores cuando intenten administrar sus contraseñas locales desde la nube. Los siguientes son los pasos detallados sobre cómo puede hacer esto con el complemento de administración de **Usuarios y equipos de Active Directory**:

>[AZURE.NOTE] Estos permisos podrían demorar hasta una hora en replicarse a todos los objetos del directorio.

#### Para configurar los permisos adecuados para que se realice la escritura diferida

1.	Abra **Usuarios y equipos de Active Directory** con una cuenta con los permisos de administración de dominios adecuados.
2.	En la opción del **menú Ver**, asegúrese de que la opción **Características avanzadas** esté activada.
3.	En el panel izquierdo, haga clic con el botón derecho en el objeto que representa la raíz del dominio.
4.	Haga clic en la pestaña **Seguridad**.
5.	haga clic en **Opciones avanzadas**.

    ![][024]

6.	En la pestaña **Permisos**, haga clic en **Agregar**.

    ![][025]

7.	Seleccione la cuenta a la que desea otorgar permisos. Se trata de la misma cuenta que se especificó al configurar la sincronización de ese bosque.
8.	En la lista desplegable que aparece en la parte superior, seleccione **Objetos de usuario descendientes**.
9.	En el cuadro de diálogo **Entrada de permiso** que aparece, active la casilla correspondiente a **Restablecer contraseña**, **Cambiar contraseña**, **Escribir permisos** en `lockoutTime` y **Escribir permisos** en `pwdLastSet`.

    ![][026]
    ![][027]
    ![][028]

10.	Luego haga clic en **Aplicar o Aceptar** en todos los cuadros de diálogo abiertos.

### Paso 5: restablecer la contraseña de AD como usuario
Ahora que la escritura diferida de contraseñas está habilitada, para saber si funciona, restablezca la contraseña de un usuario cuya cuenta se haya sincronizado en el inquilino de la nube.
 
#### Para comprobar que la escritura diferida de contraseñas funciona correctamente
1.	Vaya a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) o a cualquier pantalla de inicio de sesión de identificación de la organización y haga clic en el vínculo **¿No puede tener acceso a su cuenta?**

    ![][029]

2.	Debiera ver una página nueva que pide un Id. de usuario para el que desea restablecer una contraseña. Escriba su Id. de usuario de prueba y siga el flujo del restablecimiento de contraseñas.
3.	Después de restablecer su contraseña, verá una pantalla similar a esta. Esto significa que restableció correctamente la contraseña en los directorios locales y de la nube.

    ![][030]

4.	Para comprobar si la operación finalizó correctamente o diagnosticar algún error, vaya al **equipo con Sincronización de directorios**, abra el **Visor de eventos**, vaya al **registro de eventos de la aplicación** y busque el evento **31002 - PasswordResetSuccess** en el **PasswordResetService** de origen del usuario de prueba.

    ![][031]


<br/> <br/> <br/>

## Vínculos a la documentación de restablecimiento de la contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* [**Restablecimiento de la propia contraseña**](active-directory-passwords-update-your-own-password.md): obtenga información sobre cómo restablecer o cambiar su propia contraseña como usuario del sistema
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis diferentes componentes del servicio y lo que hace cada uno.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Prácticas recomendadas**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Obtener perspectivas**](active-directory-passwords-get-insights.md): obtenga información sobre nuestras capacidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

<!---HONumber=AcomDC_0128_2016-->