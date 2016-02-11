<properties 
	pageTitle="Solución de problemas: Administración de contraseñas de Azure AD | Microsoft Azure" 
	description="Pasos comunes de solución de problemas para la administración de contraseñas de Azure AD, entre otros, restablecimiento, cambio, escritura diferida, registro y la información que hay que proporcionar al solicitar ayuda." 
	services="active-directory" 
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
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# Solución de problemas de administración de contraseñas
Si tiene problemas con la administración de contraseñas, estamos aquí para ayudarlo. Se pueden resolver la mayoría de los problemas que pueden surgir con unos sencillos pasos que puede leer a continuación para solucionar problemas de implementación:

* [**Información para incluir si necesita ayuda**](#information-to-include-when-you-need-help)
* [**Problemas con la configuración de administración de contraseñas en el Portal de administración de Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemas con los informes de administración de contraseñas en el Portal de administración de Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemas con el portal de registro de restablecimiento de contraseña**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemas con el portal de restablecimiento de contraseña**](#troubleshoot-the-password-reset-portal)
* [**Problemas con la escritura diferida de contraseñas**](#troubleshoot-password-writeback)
  - [Códigos de error de registro de eventos de escritura diferida de contraseñas](#password-writeback-event-log-error-codes)
  - [Problemas con la conectividad de la escritura diferida de contraseñas](#troubleshoot-password-writeback-connectivity)

Si ha probado los pasos de solución de problemas siguientes y continúa teniendo problemas, puede publicar una pregunta en los [foros de Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o ponerse en contacto con el servicio de soporte técnico y estudiaremos el problema en cuanto nos sea posible.

## Información para incluir si necesita ayuda

Si no puede resolver el problema con las instrucciones siguientes, póngase en contacto con nuestros ingenieros de soporte técnico. Cuando se comunique con ellos, se recomienda que incluya la información siguiente:

 - **Descripción general del error**: qué mensaje de error exacto ha obtenido el usuario. Si no hay ningún mensaje de error, describe detalladamente el comportamiento inesperado observado.
 - **Página**: en qué página estaba cuando se generó el error (incluir la dirección URL).
 - **Fecha/hora/marca de tiempo**: en qué fecha y a qué hora exactamente se ha generado el error (incluir la marca de tiempo).
 - **Código de soporte** : qué código de soporte se generó cuando el usuario obtuvo el error (para encontrarlo, reproducir el error, hacer clic en el vínculo Código de soporte en la parte inferior de la pantalla y enviar el GUID resultante al ingeniero de soporte). 
   - Si se encuentra en una página sin código de soporte en la parte inferior, presione F12 y busque el SID y CID y envíe estos dos resultados al ingeniero de soporte.

    ![][001]

 - **Id. de usuario**: cuál fue el identificador del usuario que ha obtenido el error (por ejemplo, user@contoso.com)?
 - **Información sobre el usuario**: si se trataba de un usuario federado, con sincronización de hash de contraseña o solo de la nube. ¿El usuario tenía asignada una licencia AAD Premium o AAD Basic?
 - **Registro de eventos de la aplicación**: si utiliza la escritura diferida de contraseñas y el error se produce en la infraestructura local, comprima una copia del registro de eventos de la aplicación desde el servidor de Azure AD Connect y envíela junto con su solicitud.

Incluir esta información nos ayudará a solucionar el problema lo antes posible.


## Solución de problemas con la configuración de restablecimiento de contraseñas en el Portal de administración de Azure
Si se produce un error al configurar el restablecimiento de la contraseña, puede resolverlo con los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué error obtiene un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No aparece la sección <strong>Directiva de restablecimiento de contraseña del usuario </strong> en la pestaña <strong>Configurar</strong> en el portal de administración de Azure</p>
            </td>
            <td>
              <p>No aparece la sección <strong>Directiva de restablecimiento de contraseña del usuario </strong> en la pestaña <strong>Configurar</strong> en el portal de administración de Azure.</p>
            </td>
            <td>
              <p>Esto puede ocurrir si no tiene una licencia AAD Premium o AAD Basic asignada al administrador que realiza la operación. </p>
              <p>Para corregir esta situación, asigne una licencia AAD Premium o AAD Basic a la cuenta de administrador en cuestión; para ello, desplácese hasta la pestaña <strong>Licencias</strong> y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No veo ninguna de las opciones de configuración en la sección <strong>Directiva de restablecimiento de contraseña del usuario</strong> que se describen en la documentación.</p>
            </td>
            <td>
              <p>La sección <strong>Directiva de restablecimiento de contraseña de usuario</strong> está visible, pero la única marca que aparece debajo es <strong>Usuarios habilitados para restablecer la contraseña</strong>.</p>
            </td>
            <td>
              <p>El resto de la interfaz de usuario aparecerá cuando cambie la marca <strong>Usuarios habilitados para restablecer la contraseña</strong> a <strong>Sí</strong>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No veo una opción de configuración determinada.</p>
            </td>
            <td>
              <p>Por ejemplo, no veo la opción <strong>Número de días antes de que el usuario deba confirmar sus datos de contacto</strong> al desplazarme por la sección <strong>Directiva de restablecimiento de contraseña de usuario</strong> (u otros ejemplos del mismo problema).</p>
            </td>
            <td>
              <p>Muchos elementos de la IU están ocultos hasta que se necesitan. Intente habilitar todas las opciones en la página si desea verlas.</p>
              <p>Consulte <a href="../active-directory-passwords-customize#password-management-behavior">Personalización de la directiva de restablecimiento de contraseña</a> para obtener más información acerca de todos los controles disponibles.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No veo la opción de configuración <strong>Escritura diferida de contraseñas al Active Directory local</strong></p>
            </td>
            <td>
              <p>La opción <strong>Escritura diferida de contraseñas al Active Directory local</strong> no está visible en la pestaña <strong>Configurar</strong> en el Portal de administración de Azure</p>
            </td>
            <td>
              <p>Esta opción solo está visible si ha descargado Azure AD Connect y ha configurado la escritura diferida de contraseñas. Cuando haya hecho esto, esta opción aparece y le permite habilitar o deshabilitar la escritura diferida de contraseñas desde la nube.</p>
              <p>Consulte <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Cómo habilitar o deshabilitar la escritura diferida de contraseñas</a> para obtener más información acerca de cómo hacerlo.</p>
            </td>
          </tr>
        </tbody></table>

## Solución de problemas con los informes de administración de contraseñas en el Portal de administración de Azure
Si se produce un error al utilizar los informes de administración de contraseñas, puede resolverlo con los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué error obtiene un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No veo ningún informe de administración de contraseñas</p>
            </td>
            <td>
              <p>Los informes <strong>Actividad de restablecimiento de contraseña</strong> y <strong>Actividad de registro de restablecimiento de contraseñas</strong> no aparecen en los informes de <strong>Registro de actividades</strong> en la pestaña <strong>Informes</strong>.</p>
            </td>
            <td>
              <p>Esto puede ocurrir si no tiene una licencia AAD Premium o AAD Basic asignada al administrador que realiza la operación. </p>
              <p>Para corregir esta situación, asigne una licencia AAD Premium o AAD Basic a la cuenta de administrador en cuestión; para ello, desplácese hasta la pestaña <strong>Licencias</strong> y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Los registros de usuario se muestran varias veces</p>
            </td>
            <td>
              <p>Cuando un usuario registra el correo electrónico alternativo, el teléfono móvil y preguntas de seguridad, cada uno de estos elementos se muestra en una línea independiente, en lugar de mostrarse en una única línea.</p>
            </td>
            <td>
              <p>Actualmente, cuando un usuario se registra, no podemos asumir que registrará todo lo que se encuentra en la página de registro. Como resultado, actualmente registramos cada pieza de datos individual registrada como un evento independiente.</p>
              <p>Si desea agregar estos datos, puede descargar el informe y abrir los datos como una tabla dinámica de Excel para disfrutar de mayor flexibilidad.</p>
            </td>
          </tr>
        </tbody></table>

## Solución de problemas con el portal de registro de restablecimiento de contraseña
Si se produce un error al registrar un usuario para el restablecimiento de contraseña, puede resolverlo con los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué error obtiene un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El directorio no está habilitado para restablecer la contraseña</p>
            </td>
            <td>
              <p>El administrador no lo ha habilitado para usar esta característica.</p>
            </td>
            <td>
              <p>Modifique la marca <strong>Usuarios habilitados para restablecer la contraseña</strong> a <strong>Sí</strong> y presione <strong>Guardar</strong> en la ficha de configuración del directorio del Portal de administración de Azure. Debe tener una licencia Azure AD Premium o Basic asignada al administrador que realiza la operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El usuario no tenía asignada una licencia AAD Premium o AAD Basic</p>
            </td>
            <td>
              <p>El administrador no lo ha habilitado para usar esta característica.</p>
            </td>
            <td>
              <p>Asigne una licencia Azure AD Premium o Azure AD Basic al usuario en la pestaña <strong>Licencias</strong> en el Portal de administración de Azure. Debe tener una licencia Azure AD Premium o Basic asignada al administrador que realiza la operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error al procesar la solicitud</p>
            </td>
            <td>
              <p>El usuario ve un error que indica:</p>
              <p>
                
              </p>
              <p>Error al procesar la solicitud </p>
              <p>Al tratar de restablecer la contraseña.</p>
            </td>
            <td>
              <p>Esto puede deberse a muchos problemas, pero por lo general, este error se produce por una interrupción del servicio o un problema de configuración que no puede resolverse. </p>
              <p>Si ve este error y afecta a su empresa, póngase en contacto con soporte técnico y lo ayudaremos lo antes posible. Consulte <a href="#information-to-include-when-you-need-help">Información para incluir si necesita ayuda</a> para ver lo que debe proporcionar al ingeniero de soporte técnico para ayudarle a dar una solución rápida.</p>
            </td>
          </tr>
        </tbody></table>

## Solución de problemas con el portal de restablecimiento de contraseña
Si se produce un error al restablecer la contraseña de un usuario, puede resolverlo con los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué error obtiene un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El directorio no está habilitado para restablecer la contraseña</p>
            </td>
            <td>
              <p>La cuenta no está habilitada para restablecer la contraseña</p>
              <p>El administrador no ha configurado la cuenta para utilizarla con este servicio. </p>
              <p>
                
              </p>
              <p>Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña.</p>
            </td>
            <td>
              <p>Modifique la marca <strong>Usuarios habilitados para restablecer la contraseña</strong> a <strong>Sí</strong> y presione <strong>Guardar</strong> en la ficha de configuración del directorio del Portal de administración de Azure. Debe tener una licencia Azure AD Premium o Basic asignada al administrador que realiza la operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El usuario no tenía asignada una licencia AAD Premium o AAD Basic</p>
            </td>
            <td>
              <p>No podemos restablecer contraseñas de cuentas no administrativas automáticamente, pero podemos ponernos en contacto con el administrador de su organización para que lo haga en su lugar.</p>
            </td>
            <td>
              <p>Asigne una licencia Azure AD Premium o Azure AD Basic al usuario en la pestaña <strong>Licencias</strong> en el Portal de administración de Azure. Debe tener una licencia Azure AD Premium o Basic asignada al administrador que realiza la operación.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El directorio está habilitado para restablecer la contraseña, pero falta la información de autenticación del usuario o es incorrecta</p>
            </td>
            <td>
              <p>La cuenta no está habilitada para restablecer la contraseña</p>
              <p>El administrador no ha configurado la cuenta para utilizarla con este servicio. </p>
              <p>
                
              </p>
              <p>Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña.</p>
            </td>
            <td>
              <p>Asegúrese de que el usuario ha formado correctamente los datos de contacto en el archivo del directorio antes de continuar. Consulte <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">¿Qué datos sirven para restablecer la contraseña?</a> para obtener información sobre cómo configurar la información de autenticación en el directorio para que los usuarios no obtengan este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El directorio está habilitado para restablecer la contraseña, pero un usuario tiene sólo una parte de los datos de contacto en el archivo cuando la directiva está configurada para requerir dos pasos de comprobación</p>
            </td>
            <td>
              <p>La cuenta no está habilitada para restablecer la contraseña</p>
              <p>El administrador no ha configurado la cuenta para utilizarla con este servicio. </p>
              <p>
                
              </p>
              <p>Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña.</p>
            </td>
            <td>
              <p>Asegúrese de que el usuario tiene al menos dos métodos de contacto configurados correctamente (por ejemplo, teléfono móvil y teléfono del trabajo) antes de continuar. Consulte <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">¿Qué datos sirven para restablecer la contraseña?</a> para obtener información sobre cómo configurar la información de autenticación en el directorio para que los usuarios no obtengan este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El directorio está habilitado para restablecer la contraseña y el usuario está correctamente configurado, pero no es posible ponerse en contacto con él </p>
            </td>
            <td>
              <p>¡Huy! Se encontró un error inesperado al contactar con usted.</p>
            </td>
            <td>
              <p>Puede deberse a un error de servicio temporal o a datos de contacto configurados incorrectamente que no hemos podido detectar como es debido. Si el usuario espera 10 segundos, vuelve a intentarlo y aparece el vínculo "Póngase en contacto con el administrador". Al hacer clic en Intentar de nuevo, la llamada se enviará, mientras que, al hacer clic en "Póngase en contacto con el administrador", enviará un correo electrónico de formulario al usuario, el administrador de contraseñas y el administrador global (en ese orden de prioridad) solicita una restablecimiento de que solicite un restablecimiento de contraseña para dicha cuenta de usuario.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El usuario nunca recibe la llamada de teléfono o el SMS de restablecimiento de contraseña</p>
            </td>
            <td>
              <p>El usuario hace clic en "Enviar mensaje" o "Llamarme”, pero no recibe nada.</p>
            </td>
            <td>
              <p>Esto puede ser el resultado de un número de teléfono incorrecto en el directorio. Asegúrese de que el número de teléfono tiene el formato "+ccc xxxyyyzzzzXeeee". Para obtener más información acerca del formato de los números de teléfono para utilizarlos con el restablecimiento de contraseñas, consulte <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">¿Qué datos sirven para restablecer la contraseña?</a></p>
              <p>Si se requiere una extensión para enrutar al usuario en cuestión, tenga en cuenta que restablecer la contraseña no admite extensiones, incluso si se especifica una en el directorio (se quitan antes de enviar la llamada). Pruebe a utilizar un número sin una extensión o a integrar la extensión en el número de teléfono en su PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>El usuario nunca recibe correo electrónico de restablecimiento de contraseña</p>
            </td>
            <td>
              <p>El usuario hace clic en "Enviarme correo electrónico" o "Llamarme”, pero no recibe nada.</p>
            </td>
            <td>
              <p>La causa más común para este problema es que un filtro de correo no deseado rechaza el mensaje. Compruebe la carpeta de elementos eliminados o correo no deseado para comprobar si ha recibido el correo.</p>
              <p>También asegúrese de que está comprobando el correo electrónico adecuado para el mensaje... muchas personas tienen direcciones de correo electrónico muy similares y terminan por comprobar si el mensaje ha llegado en la bandeja de entrada incorrecta. Si ninguna de estas opciones funciona, también es posible que la dirección de correo electrónico del directorio sea incorrecta; compruébelo para asegurarse de que la dirección de correo electrónico es la adecuada e inténtelo de nuevo. Para obtener más información acerca del formato de las direcciones de correo electrónico para utilizarlas con el restablecimiento de contraseñas, consulte <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">¿Qué datos sirven para restablecer la contraseña?</a></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>He configurado una directiva para restablecer la contraseña, pero cuando una cuenta de administrador usa el restablecimiento de contraseña, no se aplica esa directiva</p>
            </td>
            <td>
              <p>Las cuentas de administrador que restablecen las contraseñas ven las mismas opciones habilitadas para restablecer la contraseña, correo electrónico y teléfono móvil, sin importar qué directiva esté establecida en la sección <strong>Directiva de restablecimiento de contraseña de usuario</strong> de la pestaña <strong>Configurar</strong>.</p>
            </td>
            <td>
              <p>Las opciones configuradas en la sección <strong>Directiva de restablecimiento de contraseña de usuario</strong> de la pestaña <strong>Configurar</strong> sólo se aplican a los usuarios finales de su organización.</p>
              <p>Microsoft administra y controla la directiva de restablecimiento de contraseñas de administrador para garantizar el mayor nivel de seguridad</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Se impide que el usuario trate de restablecer la contraseña demasiadas veces al día</p>
            </td>
            <td>
              <p>El usuario obtiene un error que indica:</p>
              <p>
                
              </p>
              <p>Use otra opción.</p>
              <p>Ha intentado comprobar su cuenta demasiadas veces en la última hora. Por motivos de seguridad, tendrá que esperar 24 horas antes de volver a intentarlo. </p>
              <p>Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña.</p>
            </td>
            <td>
              <p>Implementamos un mecanismo de limitación automático para evitar que los usuarios intenten restablecer sus contraseñas demasiadas veces en un breve período de tiempo. Esto ocurre cuando:</p>
              <ol class="ordered">
                <li>
										Un usuario intenta validar un número de teléfono 5 veces en una hora.<br\><br\></li>
                <li>
										Un usuario intenta utilizar la puerta de preguntas de seguridad 5 veces en una hora.<br\><br\></li>
                <li>
										Un usuario intenta restablecer la contraseña de la misma cuenta de usuario 5 veces en una hora.<br\><br\></li>
              </ol>
              <p>Para solucionar este problema, indique al usuario que espere 24 horas después del último intento, y el usuario podrá restablecer su contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Un usuario obtiene un error al validar su número de teléfono</p>
            </td>
            <td>
              <p>Al intentar comprobar un teléfono para usarlo como método de autenticación, el usuario obtiene un error que indica:</p>
              <p>
                
              </p>
              <p>Número de teléfono incorrecto especificado.</p>
            </td>
            <td>
              <p>Este error se produce cuando el número de teléfono especificado no coincide con el número de teléfono archivado.</p>
              <p>Asegúrese de que el usuario escribe el número de teléfono completo, incluido el código de área y país, al intentar utilizar un método basado en teléfono para restablecer la contraseña.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error al procesar la solicitud</p>
            </td>
            <td>
              <p>El usuario ve un error que indica:</p>
              <p>
                
              </p>
              <p>Error al procesar la solicitud </p>
              <p>Al tratar de restablecer la contraseña.</p>
            </td>
            <td>
              <p>Esto puede deberse a muchos problemas, pero por lo general, este error se produce por una interrupción del servicio o un problema de configuración que no puede resolverse. </p>
              <p>Si ve este error y afecta a su empresa, póngase en contacto con soporte técnico y lo ayudaremos lo antes posible. Consulte <a href="#information-to-include-when-you-need-help">Información para incluir si necesita ayuda</a> para ver lo que debe proporcionar al ingeniero de soporte técnico para ayudarle a dar una solución rápida.</p>
            </td>
          </tr>
        </tbody></table>

## Solución de problemas con la escritura diferida de contraseñas
Si se produce un error al habilitar, deshabilitar o usar la escritura diferida de contraseñas, puede resolverlo con los pasos de solución de problemas siguientes:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de error</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Qué error obtiene un usuario?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solución</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errores de inicio e incorporados generales</p>
            </td>
            <td>
              <p>El servicio de restablecimiento de contraseña no se inicia de forma local con el error 6800 en el registro de eventos de la aplicación de la máquina de Azure AD Connect.</p>
              <p>
                
              </p>
              <p>Después de la incorporación, los usuarios federados o con sincronización de hash de contraseña no pueden restablecer sus contraseñas.</p>
            </td>
            <td>
              <p>Cuando está habilitada la escritura diferida de contraseñas, el motor de sincronización llamará a la biblioteca de escritura diferida para realizar la configuración (incorporación) hablando con el servicio de incorporación de la nube. Los errores detectados durante la incorporación o al iniciar el extremo de WCF para la escritura diferida de contraseñas producirán errores en el registro de eventos del equipo de Azure AD Connect.</p>
              <p>Durante el reinicio del servicio ADSync, si se configuró la escritura diferida, se iniciará el extremo de WCF. Sin embargo, si se produce un error en el inicio del extremo, simplemente, se registra el evento 6800 y se deja que se inicie el servicio de sincronización. La presencia de este evento significa que el extremo de la escritura diferida de contraseñas no se ha iniciado. Los detalles de registro de eventos para este evento (6800) junto con las entradas de registro de eventos generadas por el componente PasswordResetService indicarán por qué el extremo no se ha podido iniciar. Revise estos errores de registro de eventos e intente volver a iniciar Azure AD Connect si la escritura diferida de contraseñas sigue sin funcionar. Si el problema persiste, intente deshabilitar y volver a habilitar la escritura diferida de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error al configurar la escritura diferida durante la instalación de Azure AD Connect.</p>
            </td>
            <td>
              <p>En el último paso del proceso de instalación de Azure AD Connect, se generará un error que indica que no se ha podido configurar la escritura diferida de contraseñas.</p>
              <p>
                
              </p>
              <p>El registro de eventos de la aplicación para Azure AD Connect contiene el error 32009 con el texto "Error al obtener el token de autorización".</p>
            </td>
            <td>
              <p>Este error se produce en los dos casos siguientes:</p>
              <ul>
                <li class="unordered">
										Ha especificado una contraseña incorrecta para la cuenta de administrador global especificada al principio del proceso de instalación de Azure AD Connect.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Ha tratado de usar un usuario federado para la cuenta de administrador global especificada al principio del proceso de instalación de Azure AD Connect.<br\><br\></li>
              </ul>
              <p>Para corregir este error, asegúrese de que no está usando una cuenta federada para el administrador global que especificó al principio del proceso de instalación de Azure AD Connect y que la contraseña especificada es correcta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error al configurar la escritura diferida durante la instalación de Azure AD Connect.</p>
            </td>
            <td>
              <p>El registro de eventos del equipo de Azure AD Connect contiene el error 32002 producido por PasswordResetService.</p>
              <p>
                
              </p>
              <p>El error dice: "Error al conectarse al Bus de servicio, el proveedor de tokens no pudo proporcionar un token de seguridad..."</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>La causa raíz de este error es que el servicio de restablecimiento de contraseña ejecutado en su entorno local no es capaz de conectarse al extremo del Bus de servicio en la nube. Este error normalmente se debe a una regla de firewall que bloquea una conexión saliente a una dirección de puerto o web determinada.</p>
              <p>
                
              </p>
              <p>Asegúrese de que el firewall permite las conexiones salientes para lo siguiente:</p>
              <ul>
                <li class="unordered">
										Todo el tráfico a través de TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Conexiones salientes a <br\><br\></li>
              </ul>
              <p>
                
              </p>
              <p>Una vez que haya actualizado estas reglas, reinicie el equipo de Azure AD Connect y la escritura diferida de contraseñas debería empezar a funcionar de nuevo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No se puede llegar al extremo local de la escritura diferida de contraseñas</p>
            </td>
            <td>
              <p>Después trabajar durante algún tiempo, los usuarios federados o con sincronización de hash de contraseña no pueden restablecer las contraseñas.</p>
            </td>
            <td>
              <p>En algunos casos excepcionales, el servicio de escritura diferida de contraseñas puede no volver a iniciarse cuando se reinicia Azure AD Connect. En estos casos, en primer lugar, compruebe si la escritura diferida de contraseñas está habilitada en el entorno local. Esto puede hacerse mediante el Asistente de Azure AD Connect o PowerShell (consulte la sección anterior sobre los procedimientos). Si la característica parece estar habilitada, pruebe a habilitar o deshabilitar la característica de nuevo a través de la interfaz de usuario o de PowerShell. Consulte "Paso 2: Habilitar la escritura diferida de contraseñas en su equipo de sincronización de directorios y configurar reglas de firewall" en <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Cómo habilitar o deshabilitar la escritura diferida de contraseñas</a> para obtener más información acerca de cómo hacerlo.</p>
              <p>
                
              </p>
              <p>Si esto no funciona, pruebe a desinstalar por completo y volver a instalar Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errores con permisos</p>
            </td>
            <td>
              <p>Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseñas obtienen un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
              <p>
                
              </p>
              <p>Además, durante las operaciones de restablecimiento de contraseña, verá un error relacionado con que al agente de administración se le denegó el acceso a los registros de eventos locales.</p>
            </td>
            <td>
              <p>Si ve estos errores en el registro de eventos, confirme que la cuenta de AD MA (que se especificó en el asistente en el momento de la configuración) tiene los permisos necesarios para la escritura diferida de contraseñas.</p>
              <p>
                
              </p>
              <p>TENGA EN CUENTA que una vez que se concede este permiso, puede tardar hasta una hora en poder utilizarse a través de la tarea en segundo plano sdprop en el controlador de dominio. </p>
              <p>Para que el restablecimiento de contraseña funcione, el permiso debe quedar marcado en el descriptor de seguridad del objeto de usuario cuya contraseña se está restableciendo. Hasta que este permiso se muestra en el objeto de usuario, el restablecimiento de contraseña seguirá generando un error con acceso denegado.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error al configurar la escritura diferida de contraseñas desde el Asistente para configuración de Azure AD Connect </p>
            </td>
            <td>
              <p>Error “No se ha podido localizar el agente de administración (AM)” en el asistente al habilitar o deshabilitar la escritura diferida de contraseñas</p>
            </td>
            <td>
              <p>Hay un problema conocido en la versión publicada de Azure AD Connect que se manifiesta en la siguiente situación:</p>
              <ol class="ordered">
                <li>
										Configura Azure AD Connect para abc.com inquilino (dominio comprobado) mediante credenciales. Esto crea el conector de AAD con el nombre "abc.com – AAD".<br\><br\></li>
                <li>
										A continuación, cambie las credenciales de AAD para el conector (mediante la UI de sincronización anterior) (tenga en cuenta que se trata del mismo inquilino, pero con un nombre de dominio distinto). <br\><br\></li>
                <li>
										Ahora intente habilitar/deshabilitar la escritura diferida de contraseñas. El asistente creará el nombre del conector mediante las credenciales, como "abc.onmicrosoft.com – AAD", y pasará al cmdlet de escritura diferida de contraseñas. Esto generará un error porque no hay ningún conector creado con este nombre.<br\><br\></li>
              </ol>
              <p>Esto se ha solucionado en nuestras compilaciones más recientes. Si tiene una compilación anterior, una solución alternativa es usar el cmdlet de PowerShell para habilitar o deshabilitar la característica. Consulte "Paso 2: Habilitar la escritura diferida de contraseñas en su equipo de sincronización de directorios y configurar reglas de firewall" en <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Cómo habilitar o deshabilitar la escritura diferida de contraseñas</a> para obtener más información acerca de cómo hacerlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>No se puede restablecer la contraseña para los usuarios en grupos especiales tales como administradores de dominio, administradores de empresa, etc.</p>
            </td>
            <td>
              <p>Los usuarios federados o con sincronización de hash de contraseña que forman parte de grupos protegidos que intenten restablecer su contraseñas obtienen un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
            </td>
            <td>
              <p>Los usuarios con privilegios en Active Directory están protegidos mediante AdminSDHolder. Consulte <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> para obtener más detalles. </p>
              <p>
                
              </p>
              <p>Esto significa que los descriptores de seguridad en estos objetos se comprueban periódicamente para que coincidan con el especificado en AdminSDHolder y se restablecen si son diferentes. Por lo tanto, los permisos adicionales necesarios para la escritura diferida de contraseñas no filtran a dichos usuarios. Esto puede ocasionar que la escritura diferida de contraseñas no funcione para dichos usuarios. Como resultado, no se admite la administración de contraseñas para los usuarios de estos grupos porque rompe el modelo de seguridad de AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Las operaciones de restablecimiento generan error porque no se ha podido encontrar el objeto</p>
            </td>
            <td>
              <p>Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseñas obtienen un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
              <p>
                
              </p>
              <p>Además, durante las operaciones de restablecimiento de contraseña, puede obtener un error en los registros de eventos del servicio Azure AD Connect que indique un error “No se encontró el objeto”.</p>
            </td>
            <td>
              <p>Este error suele indicar que el motor de sincronización no puede encontrar el objeto de usuario en el espacio del conector de AAD o en el objeto de espacio del conector MV o AD vinculado. </p>
              <p>
                
              </p>
              <p>Para solucionar este problema, asegúrese de que el usuario realmente está sincronizado desde el entorno local a AAD a través de la instancia actual de Azure AD Connect e inspeccione el estado de los objetos de los espacios de conector y MV. Confirme que el objeto de AD CS es conector al objeto MV a través de la regla "Microsoft.InfromADUserAccountEnabled.xxx".</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error en las operaciones de restablecimiento con varias coincidencias encontradas</p>
            </td>
            <td>
              <p>Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseñas obtienen un error después de enviar la contraseña que indica que se ha producido un problema de servicio.</p>
              <p>
                
              </p>
              <p>Además, durante las operaciones de restablecimiento de contraseña, puede obtener un error en los registros de eventos del servicio Azure AD Connect que indique un error “Varias coincidencias detectadas”.</p>
            </td>
            <td>
              <p>Esto indica que el motor de sincronización ha detectado que el objeto de MV está conectado a más de un objeto de AD CS a través de "Microsoft.InfromADUserAccountEnabled.xxx". Esto significa que el usuario tiene una cuenta habilitada en más de un bosque. </p>
              <p>
                
              </p>
              <p>Actualmente, este escenario no se admite para la escritura diferida de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error de configuración en las operaciones con contraseñas.</p>
            </td>
            <td>
              <p>Error de configuración en las operaciones con contraseñas. El registro de eventos de la aplicación contiene el error 6329 de Azure AD Connect con el texto: 0x8023061f (error en la operación porque no está habilitada la sincronización de contraseñas en este agente de administración).</p>
            </td>
            <td>
              <p>Esto ocurre si se cambia la configuración de Azure AD Connect para agregar un nuevo bosque de AD (o para quitar y volver a agregar un bosque existente) <strong>después</strong> de que ya se haya habilitado la escritura diferida de contraseñas. Se producirá un error en las operaciones de contraseñas para los usuarios de estos bosques recién agregados. Para corregir el problema, deshabilite y vuelva a habilitar la característica de escritura diferida de contraseñas después de que se hayan completado los cambios de configuración del bosque.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Volver a escribir las contraseñas restablecidas por los usuarios funciona bien, pero se producen errores en la escritura diferida de las contraseñas modificadas por un usuario o restablecidas por un administrador para un usuario.</p>
            </td>
            <td>
              <p>Al intentar restablecer la contraseña en nombre del usuario del Portal de administración de Azure, obtendrá el siguiente mensaje: "El servicio de restablecimiento de contraseña ejecutado en su entorno local no admite que los administradores restablezcan las contraseñas de usuario". Actualice a la versión más reciente de Azure AD Connect para resolver el problema”.</p>
            </td>
            <td>
              <p>Esto sucede cuando la versión del motor de sincronización no admite la operación particular utilizada de la escritura diferida de contraseñas. Las versiones de Azure AD Connect posteriores a la 1.0.0419.0911 admiten todas las operaciones de administración de contraseñas, entre otras, la escritura diferida de restablecimiento de contraseñas, la escritura diferida de modificación de contraseñas y la escritura diferida de restablecimiento de contraseñas iniciada por el administrador desde el Portal de administración de Azure.&#160; Las versiones de DirSync posteriores a la 1.0.6862 solo admiten la escritura diferida de restablecimiento de contraseñas. Para resolver este problema, recomendamos encarecidamente que instale la versión más reciente de Azure AD Connect o Azure Active Directory Connect (para obtener más información, consulte <a href="../active-directory-aadconnect#download-azure-ad-connect">Herramientas de integración de directorio</a>) para resolver este problema y para sacar el máximo partido de la escritura diferida de contraseñas en su organización.</p>
            </td>
          </tr>
        </tbody></table>


## Códigos de error de registro de eventos de escritura diferida de contraseñas
Una práctica recomendada para solucionar problemas con la escritura diferida de contraseñas consiste en inspeccionar ese registro de eventos de la aplicación en el equipo de Azure AD Connect. Este registro de eventos contiene eventos de dos orígenes de interés para la escritura diferida de contraseñas. El origen PasswordResetService describirá las operaciones y los problemas relacionados con el funcionamiento de la escritura diferida de contraseñas. El origen de ADSync describe las operaciones y los problemas relacionados con la configuración de contraseñas en el entorno de AD.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Código</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nombre/mensaje</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Origen</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Descripción</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619 –: "Una restricción impide que la contraseña se modifique por la que ha especificado actualmente".</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Este evento se produce cuando el servicio de escritura diferida de contraseñas intenta establecer una contraseña en su directorio local que no cumple la duración de la contraseña, el historial, la complejidad o los requisitos de filtrado del dominio.</p>
              <ul>
                <li class="unordered">
										Si tiene una vigencia mínima de contraseña y ha cambiado recientemente la contraseña desde la ventana de tiempo, no podrá volver a cambiarla hasta que alcance la duración especificada en el dominio. Con fines de prueba, la duración mínima debe establecerse en 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si tiene los requisitos del historial de contraseña habilitados, debe seleccionar una contraseña que no se haya utilizado en las últimas N veces, donde N es la configuración de la duración de la contraseña. Si selecciona una contraseña que se ha utilizado en las últimas N veces, a continuación, verá un error en este caso. Con fines de prueba, el historial mínimo debe establecerse en 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si tiene requisitos de complejidad de contraseña, todos ellos se aplicarán cuando el usuario intenta cambiar o restablecer una contraseña.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si tiene habilitados filtros de contraseña y un usuario selecciona una contraseña que no cumple los criterios de filtrado, se producirá un error en la operación de restablecimiento o modificación.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>El motor de sincronización devolvió un error hr=80230402, mensaje=Error al intentar obtener un objeto debido a que hay entradas duplicadas con el mismo delimitador</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Este evento se produce cuando se habilita el mismo identificador de usuario en varios dominios. Por ejemplo, si se están sincronizando los bosques de cuentas y recursos y tienen el mismo identificador de usuario presente y habilitado en cada uno, es posible que se produzca este error.  </p>
              <p>Este error también puede producirse si usa un atributo delimitador que no sea único (como un alias o UPN) y dos usuarios comparten el mismo atributo delimitador.</p>
              <p>Para resolver este problema, asegúrese de no tener ningún usuario duplicado dentro de los dominios y de estar utilizando un atributo de delimitador único para cada usuario.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local ha detectado una solicitud de restablecimiento de contraseña para un usuario federado o con sincronización de hash de contraseña originada desde la nube. Este evento es que el primer evento en cada operación de escritura diferida de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una nueva contraseña durante una operación de restablecimiento de contraseña, determinamos que esta contraseña cumple los requisitos de contraseñas corporativos y que esa contraseña se ha escrito en diferido correctamente en el entorno de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una contraseña y que la contraseña ha llegado correctamente al entorno local, pero al intentar establecer la contraseña en el entorno de AD local, se produjo un error. Esto puede ocurrir por varios motivos:</p>
              <ul>
                <li class="unordered">
										La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Pruebe una contraseña completamente nueva para solucionar el problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										La cuenta de servicio del agente de administración no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas.<br\><br\></li>
              </ul>
              <p>Consulte <a href="#troubleshoot-password-writeback">Solución de problemas con la escritura diferida de contraseñas</a> para obtener más información acerca de qué otras situaciones pueden producir este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento se produce si habilita la escritura diferida de contraseñas con Azure AD Connect e indica que se inició la incorporación en su organización del servicio web de escritura diferida de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el proceso de incorporación se realizó correctamente y que la capacidad de escritura diferida de contraseñas está lista para usarse.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local ha detectado una solicitud de cambio de contraseña para un usuario federado o con sincronización de hash de contraseña originada desde la nube. Este evento es el primer evento de cada operación de escritura diferida de cambio de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una nueva contraseña durante una operación de cambio de contraseña, determinamos que esta contraseña cumple los requisitos de contraseñas corporativos y que esa contraseña se ha escrito en diferido correctamente en el entorno de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que un usuario selecciona una contraseña y que la contraseña ha llegado correctamente al entorno local, pero al intentar establecer la contraseña en el entorno de AD local, se produjo un error. Esto puede ocurrir por varios motivos:</p>
              <ul>
                <li class="unordered">
										La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Pruebe una contraseña completamente nueva para solucionar el problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										La cuenta de servicio del agente de administración no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas.<br\><br\></li>
              </ul>
              <p>Consulte <a href="#troubleshoot-password-writeback">Solución de problemas con la escritura diferida de contraseñas</a> para obtener más información acerca de qué otras situaciones pueden producir este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>El servicio local ha detectado una solicitud de restablecimiento de contraseña para un usuario federado o con sincronización de hash de contraseña originada por el administrador local en nombre de un usuario. Este evento es el primero en una operación de escritura diferida de restablecimiento de contraseña iniciada por el administrador.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>El administrador ha seleccionado una nueva contraseña durante una operación de restablecimiento de contraseña iniciada por el administrador, determinamos que esta contraseña cumple los requisitos de contraseñas corporativos y que esa contraseña se ha escrito en diferido correctamente en el entorno de AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>El administrador ha seleccionado una contraseña en nombre de un usuario y que la contraseña ha llegado correctamente al entorno local, pero al intentar establecer la contraseña en el entorno de AD local, se produjo un error. Esto puede ocurrir por varios motivos:</p>
              <ul>
                <li class="unordered">
										La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Pruebe una contraseña completamente nueva para solucionar el problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										La cuenta de servicio del agente de administración no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas.<br\><br\></li>
              </ul>
              <p>Consulte <a href="#troubleshoot-password-writeback">Solución de problemas con la escritura diferida de contraseñas</a> para obtener más información acerca de qué otras situaciones pueden producir este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento se produce si habilita la escritura diferida de contraseñas con Azure AD Connect e indica que se inició la externalización en su organización del servicio web de escritura diferida de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el proceso de externalización se realizó correctamente y que la capacidad de escritura diferida de contraseñas se ha deshabilitado correctamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el proceso de externalización no se realizó correctamente. Esto podría deberse a un error de permisos en la cuenta de administrador de la nube o local especificada durante la configuración, o porque está intentando utilizar un administrador global de nube federado al deshabilitar la escritura diferida de contraseñas. Para solucionar este problema, compruebe los permisos administrativos y no se utilice ninguna cuenta federada al configurar la capacidad de escritura federada de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio de escritura diferida de contraseñas se inició correctamente y está listo para aceptar las solicitudes de administración de contraseñas desde la nube.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio de escritura diferida de contraseñas se ha detenido y que las solicitudes de administración de contraseñas desde la nube no se realizarán correctamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha recuperado correctamente un token de autorización para el administrador global especificado durante la instalación de Azure AD Connect para iniciar el proceso de incorporación o externalización.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha creado correctamente la clave de cifrado de contraseña que se utilizará para cifrar las contraseñas de la nube para enviarlas al entorno local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica un error desconocido durante una operación de administración de contraseñas. Observe el texto de excepción en el evento para obtener más detalles. Si tiene problemas, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas. Si esto no soluciona el problema, incluya una copia del registro de eventos junto con el identificador de seguimiento especificado privilegiado a su ingeniero de soporte técnico.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error de conexión al servicio de restablecimiento de contraseña en la nube y suele producirse cuando el servicio local no se ha podido conectar con el servicio web de restablecimiento de contraseñas. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error al conectarse a la instancia de Bus de servicio del inquilino. Esto puede deberse a que está bloqueando las conexiones salientes en el entorno local. Compruebe el firewall para garantizar que permite conexiones a través de TCP 443 y a <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, e inténtelo de nuevo. Si tiene problemas, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que la entrada transferida a la API del servicio web no era válida. Vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error al descifrar la contraseña que ha llegado de la nube. Esto puede deberse a una falta de coincidencia de la clave de descifrado entre el servicio en la nube y su entorno local. Para resolver este problema, deshabilite y vuelva a habilitar la escritura diferida de contraseñas en su entorno local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante la incorporación, guardamos la información específica del inquilino en un archivo de configuración en su entorno local. Este evento indica que se ha producido un error al guardar este archivo o que cuando se inició el servicio había un error en la lectura del archivo. Para corregir este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas para forzar la escritura diferida de contraseñas de este archivo de configuración. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>EN DESUSO: este evento no está presente en Azure AD Connect, solo en versiones muy anteriores de DirSync que admitían la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante la incorporación, enviamos datos de la nube al servicio de restablecimiento de contraseñas local. Esos datos, a continuación, se escriben en un archivo en memoria antes de enviarse al servicio de sincronización para almacenar esta información de forma segura en el disco. Este evento indica un problema con escribir o actualizar datos en la memoria. Para corregir este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas para forzar la escritura diferida de contraseñas de esta configuración.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que hemos recibido una respuesta no válida desde el servicio web de restablecimiento de contraseña. Para solucionar este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que no se ha podido obtener un token de autorización para la cuenta de administrador global especificada durante la instalación de Azure AD Connect. Este error puede deberse a un nombre de usuario o contraseña especificados para la cuenta de administrador global o porque se federa la cuenta de administrador global especificada. Para corregir este problema, vuelva a ejecutar la configuración con el nombre de usuario correcto y la contraseña y asegúrese de que el administrador es una cuenta administrada (solo en la nube o con la sincronización de contraseñas).</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se produjo un error al generar la clave de cifrado de contraseña o de descifrado de una contraseña que llega desde el servicio en la nube. Este error probablemente indica un problema con su entorno. Consulte los detalles de su registro de eventos para obtener más información y resolver este problema. También puede intentar deshabilitar y volver a habilitar el servicio de escritura diferida de contraseñas para resolver este problema.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio web de restablecimiento de contraseña para iniciar el proceso de incorporación. Esto puede deberse a una regla de firewall o un problema al obtener un token de autenticación para el inquilino. Para solucionar este problema, asegúrese de que no está bloqueando las conexiones salientes sobre TCP 443 y TCP 9350 a 9354 o a <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, y que la cuenta de administrador de AAD utilizada para la incorporación no está federada. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>EN DESUSO: este evento no está presente en Azure AD Connect, solo en versiones muy anteriores de DirSync que admitían la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio web de restablecimiento de contraseña para iniciar el proceso de externalización. Esto puede deberse a una regla de firewall o un problema al obtener un token de autenticación para el inquilino. Para solucionar este problema, asegúrese de que no está bloqueando las conexiones salientes sobre TCP 443 o a <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, y que la cuenta de administrador de AAD utilizada para la externalización no está federada. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que tenemos que tratar de volver a establecer la conexión a la instancia de Bus de servicio del inquilino. En condiciones normales, esto no debe ser un problema, pero si este evento se produce muchas veces, considere la posibilidad de comprobar la conexión de red al bus de servicio, especialmente si se trata de una conexión de ancho de banda bajo o una latencia alta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Para supervisar el estado de su servicio de escritura diferida de contraseñas, enviamos datos de latido a nuestro servicio web de restablecimiento de contraseñas cada cinco minutos. Este evento indica que se ha producido un error al enviar esta información de latido al servicio web en la nube. Esta información de estado no incluye datos OII o PII y se trata únicamente de un latido y estadísticas de servicio básicas para que podamos ofrecer información de estado de servicio en la nube.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un error desconocido devuelto por AD; compruebe el registro de eventos de servidor de Azure AD Connect para eventos del origen de ADSync para obtener más información acerca de este error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el usuario que está intentando restablecer o cambiar una contraseña no se encontró en el directorio local. Esto puede ocurrir cuando el usuario se ha eliminado en el entorno local pero no en la nube, o si hay un problema con la sincronización. Compruebe los registros de sincronización, así como los últimos detalles de ejecución de la sincronización para obtener más información.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Si una solicitud de restablecimiento o cambio de contraseña se origina desde la nube, usamos el delimitador de la nube especificado durante el proceso de configuración de Azure AD Connect para determinar cómo vincular tal solicitud de nuevo a un usuario en el entorno local. Este evento indica que hemos encontrado dos usuarios en el directorio local con el mismo atributo delimitador de la nube. Compruebe los registros de sincronización, así como los últimos detalles de ejecución de la sincronización para obtener más información.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que la cuenta de servicio del agente de administración no tiene los permisos adecuados en la cuenta en cuestión para establecer una nueva contraseña. Asegúrese de que la cuenta del agente de administración en el bosque del usuario tiene los permisos de restablecimiento y cambio de contraseña en todos los objetos del bosque. Para obtener más información sobre cómo hacerlo, consulte <a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">Paso 4: configurar los permisos adecuados de Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que hemos intentado restablecer o cambiar una contraseña de una cuenta deshabilitada en el entorno local. Habilite la cuenta y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que hemos intentado restablecer o cambiar una contraseña de una cuenta bloqueada en el entorno local. Los bloqueos se aplican cuando un usuario intenta modificar o restablecer la contraseña demasiadas veces en poco tiempo. Desbloquee la cuenta y vuelva a intentarlo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que el usuario ha especificado una contraseña incorrecta actual al realizar una operación de cambio de contraseña. Especifique la contraseña actual correcta e inténtelo de nuevo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento se produce cuando el servicio de escritura diferida de contraseñas intenta establecer una contraseña en su directorio local que no cumple la duración de la contraseña, el historial, la complejidad o los requisitos de filtrado del dominio.</p>
              <ul>
                <li class="unordered">
										Si tiene una vigencia mínima de contraseña y ha cambiado recientemente la contraseña desde la ventana de tiempo, no podrá volver a cambiarla hasta que alcance la duración especificada en el dominio. Con fines de prueba, la duración mínima debe establecerse en 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si tiene los requisitos del historial de contraseña habilitados, debe seleccionar una contraseña que no se haya utilizado en las últimas N veces, donde N es la configuración de la duración de la contraseña. Si selecciona una contraseña que se ha utilizado en las últimas N veces, a continuación, verá un error en este caso. Con fines de prueba, el historial mínimo debe establecerse en 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si tiene requisitos de complejidad de contraseña, todos ellos se aplicarán cuando el usuario intenta cambiar o restablecer una contraseña.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si tiene habilitados filtros de contraseña y un usuario selecciona una contraseña que no cumple los criterios de filtrado, se producirá un error en la operación de restablecimiento o modificación.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que se ha producido un problema al escribir la contraseña en diferido en el directorio local por un problema de configuración con Active Directory. Compruebe el registro de eventos de la aplicación del equipo de Azure AD Connect para ver los mensajes del servicio ADSync, a fin de obtener más información sobre el error que se ha producido. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>EN DESUSO: este evento no está presente en Azure AD Connect, solo en versiones muy anteriores de DirSync que admitían la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>EN DESUSO: este evento no está presente en Azure AD Connect, solo en versiones muy anteriores de DirSync que admitían la escritura diferida.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>EN DESUSO: este evento no está presente en Azure AD Connect, solo en versiones muy anteriores de DirSync que admitían la escritura diferida.</p>
            </td>
          </tr>
        </tbody></table>
## Solución de problemas con la conectividad de la escritura diferida de contraseñas

Si se producen interrupciones del servicio con el componente de escritura diferida de contraseñas de Azure AD Connect, aquí tiene algunos pasos que puede seguir para resolver este problema:

 - [Reinicio del servicio de sincronización de Azure AD Connect](#restart-the-azure-AD-sync-service)
 - [Deshabilitar y volver a habilitar la característica de escritura diferida de contraseña](#disable-and-re-enable-the-password-writeback-feature)
 - [Instalación de la última versión de Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Solución de problemas con la escritura diferida de contraseñas](#troubleshoot-password-writeback)

En general, se recomienda que ejecute estos pasos en el orden anterior con el fin de recuperar el servicio de la manera más rápida.

### Reinicio del servicio de sincronización de Azure AD Connect
Reiniciar el servicio de sincronización de Azure AD Connect puede ayudar a solucionar problemas de conectividad u otros problemas transitorios con el servicio.

 1.	Como administrador, haga clic en **Iniciar** en el servidor que ejecuta **Azure AD Connect**.
 2.	Escriba **"services.msc"** en el cuadro de búsqueda y presione **ENTRAR**.
 3.	Busque la entrada **Microsoft Azure AD Connect**.
 4.	Haga clic con el botón secundario en la entrada del servicio, haga clic en **Reiniciar** y espere a que se complete la operación.

    ![][002]

Estos pasos restablecerán la conexión con el servicio en la nube y resolverán las interrupciones que pueda experimentar. Si reiniciar el servicio de sincronización no resuelve el problema, se recomienda que intente deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas en el siguiente paso.

### Deshabilitar y volver a habilitar la característica de escritura diferida de contraseña
Deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas puede ayudar a resolver problemas de conectividad.

 1.	Como administrador, abra el **Asistente de configuración de Azure AD Connect**.
 2.	En el cuadro de diálogo **Conectarse a Azure AD**, escriba las **credenciales de administrador global de Azure AD**.
 3.	En el cuadro de diálogo **Conectarse a Azure AD**, escriba las **credenciales de administrador de Servicios de dominio de AD**.
 4.	En el cuadro de diálogo **Identificación de forma exclusiva de usuarios**, haga clic en el botón **Siguiente**.
 5.	En el cuadro de diálogo **Características opcionales**, desactive la casilla **Escritura diferida de contraseñas**.

    ![][003]

 6.	Haga clic en **Siguiente** en las páginas de diálogo restantes sin cambiar nada hasta llegar a la página **Listo para configurar**.
 7.	Asegúrese de que en la página de configuración aparece **Opción de escritura diferida de contraseñas deshabilitada** y, a continuación, haga clic en el botón verde **Configurar** para confirmar los cambios.
 8.	En el cuadro de diálogo **Finalizado**, anule la selección de la opción **Sincronizar ahora** y, a continuación, haga clic en **Finalizar** para cerrar el asistente.
 9.	Vuelva a abrir el **Asistente de configuración de Azure AD Connect**.
 10.	**Repita los pasos 2 a 8**, salvo que debe asegurarse de **volver a marcar la opción de escritura diferida de contraseñas** en la pantalla **Características opcionales** para volver a habilitar el servicio.

    ![][004]

Estos pasos restablecerán la conexión con el servicio en la nube y resolverán las interrupciones que pueda experimentar.

Si el problema no se soluciona después de deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas, le recomendamos que vuelva a instalar Azure AD Connect en el siguiente paso.

### Instalación de la última versión de Azure AD Connect
Volver a instalar el paquete de Azure AD Connect resolverá cualquier problema de configuración que pueda afectar a su capacidad de conectarse a nuestros servicios en la nube o administrar contraseñas en su entorno de AD local. Se recomienda realizar este paso sólo después de probar los dos primeros pasos descritos anteriormente.

 1.	Descargue la última versión de Azure AD Connect [aquí](active-directory-aadconnect.md#download-azure-ad-connect).
 2.	Puesto que ya ha instalado Azure AD Connect, sólo necesitará realizar una actualización in situ para actualizar la instalación de Azure AD Connect a la versión más reciente.
 3.	Ejecute el paquete descargado y siga las instrucciones en pantalla para actualizar el equipo de Azure AD Connect. No se necesitan pasos manuales adicionales a menos que haya personalizado reglas de sincronización rápida, en cuyo caso debe **realizar una copia de seguridad antes de continuar con la actualización y volver a implementarla de forma manual cuando haya terminado**.

Estos pasos restablecerán la conexión con el servicio en la nube y resolverán las interrupciones que pueda experimentar.

Si instalar la última versión del servidor de Azure AD Connect no resuelve el problema, le recomendamos que intente deshabilitar y volver a habilitar la escritura diferida de contraseñas como último paso después de instalar la QFE de sincronización más reciente.

Si esto no resuelve el problema, recomendamos que eche un vistazo a [Solución de problemas con la escritura diferida de contraseñas](#troubleshoot-password-writeback) y a las [preguntas más frecuentes sobre la administración de contraseñas de Azure AD](active-directory-passwords-faq.md) para ver si ahí se puede tratar el problema.


<br/> <br/> <br/>

## Vínculos a la documentación de restablecimiento de la contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* [**Restablecimiento de la propia contraseña**](active-directory-passwords-update-your-own-password.md): obtenga información sobre cómo restablecer o cambiar su propia contraseña como usuario del sistema.
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis diferentes componentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Prácticas recomendadas**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Obtener perspectivas**](active-directory-passwords-get-insights.md): obtenga información sobre nuestras capacidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"

<!---HONumber=AcomDC_1125_2015-->