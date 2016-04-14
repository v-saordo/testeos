<properties 
	pageTitle="Más información: Administración de contraseñas de Azure AD | Microsoft Azure" 
	description="Temas avanzados sobre administración de contraseñas de Azure AD, entre otros, el funcionamiento de la escritura diferida de contraseñas, la seguridad de la escritura diferida de contraseñas, el funcionamiento del portal de restablecimiento de contraseñas y los datos que sirven para el restablecimiento de contraseñas." 
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
	ms.date="02/16/2016" 
	ms.author="asteen"/>

# Más información sobre la administración de contraseñas
Si ya ha implementado la administración de contraseñas, o simplemente desea obtener más información sobre los detalles técnicos del funcionamiento antes de implementarla, en esta sección se le ofrece una introducción apropiada de los conceptos técnicos subyacentes al servicio. Trataremos lo siguiente:

* [**Información general sobre la escritura diferida de contraseñas**](#password-writeback-overview)
  - [Funcionamiento de la escritura diferida de contraseñas](#how-password-writeback-works)
  - [Escenarios admitidos para la escritura diferida de contraseñas](#scenarios-supported-for-password-writeback)
  - [Modelo de seguridad de la escritura diferida de contraseñas](#password-writeback-security-model)
* [**¿Cómo funciona el portal de restablecimiento de contraseñas?**](#how-does-the-password-reset-portal-work)
  - [¿Qué datos sirven para restablecer la contraseña?](#what-data-is-used-by-password-reset)
  - [Cómo obtener acceso a los datos de restablecimiento de la contraseña de los usuarios](#how-to-access-password-reset-data-for-your-users)

## Información general sobre la escritura diferida de contraseñas
La escritura diferida de contraseñas es un componente de [Azure Active Directory Connect](active-directory-aadconnect) que los suscriptores actuales de Azure Active Directory Premium pueden habilitar y utilizar. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

La escritura diferida de contraseñas le permite configurar el inquilino de nube para que escriba contraseñas en diferido en su Active Directory local. Evita tener que configurar y administrar una solución de restablecimiento de contraseñas de autoservicio local y ofrece una manera conveniente basada en la nube para que los usuarios restablezcan sus contraseñas locales dondequiera que estén. Siga leyendo para conocer algunas de las características clave de la escritura diferida de contraseñas:

- **Comentarios sin ningún retraso.** La escritura diferida de contraseñas es una operación sincrónica. Si la contraseña de un usuario no cumple la directiva o no se puede restablecer o modificar por algún motivo, a dicho usuario se le enviará una notificación inmediatamente.
- **Admite el restablecimiento de contraseñas de los usuarios que usan AD FS u otras tecnologías de federación.** Con la escritura diferida de contraseñas, siempre que las cuentas de usuario federadas estén sincronizadas en el inquilino de Azure AD, podrán administrar sus contraseñas de AD locales desde la nube.
- **Admite el restablecimiento de las contraseñas de usuario mediante la sincronización de hash de contraseñas.** Cuando el servicio de restablecimiento de contraseñas detecta que una cuenta de usuario sincronizada está habilitada para la sincronización de hash de contraseñas, restablecemos la contraseña local y de la nube de la cuenta al mismo tiempo.
- **Permite cambiar las contraseñas del panel de acceso y Office 365.** Si los usuarios con sincronización de contraseñas o federados modifican las contraseñas expiradas o no expiradas, escribiremos tales contraseñas en diferido en el entorno local de AD.
- **Admite la escritura diferida de contraseñas cuando un administrador las restablece desde el** [**Portal de administración de Azure**](https://manage.windowsazure.com). Cada vez que un administrador restablece una contraseña de usuario en el [Portal de administración de Azure](https://manage.windowsazure.com), si ese usuario se federa o se sincroniza con contraseña, también estableceremos la contraseña que el administrador seleccione en el entorno local de AD. Esto actualmente no se admite en el Portal de administración de Office.
- **Aplica las directivas de contraseñas del entorno de AD local.** Cuando un usuario restablece su contraseña, nos aseguramos de que cumple la directiva del entorno local de AD antes de enviarla a ese directorio. Esto incluye el historial, la complejidad, la antigüedad, los filtros de contraseñas y otras restricciones de contraseña que ha definido en el entorno local de AD.
- **No requiere ninguna regla de firewall de entrada.** La escritura diferida de contraseñas usa una retransmisión de Bus de servicio de Azure como un canal de comunicación subyacente, lo que significa que no tendrá que abrir puertos de entrada en el firewall para que esta característica funcione.
- **No se admite para las cuentas de usuario que se encuentran en grupos protegidos del entorno local de Active Directory.** Para obtener más información sobre los grupos protegidos, consulte [Cuentas y grupos protegidos en Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### Funcionamiento de la escritura diferida de contraseñas
La escritura diferida de contraseñas tiene tres componentes principales:

- Servicio en la nube de restablecimiento de contraseñas (también está integrado en las páginas de cambio de contraseña de Azure AD)
- Retransmisión de Bus de servicio de Azure específica del inquilino
- Extremo de restablecimiento de contraseña local

Se relacionan entre sí como se describe en el diagrama siguiente:

  ![][001]

Si un usuario federado o con sincronización de hash de contraseña cambia o restablece su contraseña en la nube, ocurre lo siguiente:

1.	Comprobamos qué tipo de contraseña tiene el usuario. Si observamos que la contraseña se administra de forma local, a continuación, garantizamos que el servicio de escritura diferida está en funcionamiento. Si es así, permitimos al usuario continuar; de lo contrario, comunicaremos al usuario que la contraseña no se puede restablecer aquí.
2.	A continuación, el usuario pasa las puertas de autenticación adecuadas y llega a la pantalla de restablecimiento de contraseña.
3.	El usuario selecciona una nueva contraseña y la confirma.
4.	Al hacer clic en enviar, se cifra la contraseña de texto no cifrado con una clave simétrica que se creó durante el proceso de configuración de la escritura diferida.
5.	Después de cifrar la contraseña, la incluimos en una carga que se envía a través de un canal HTTPS a la retransmisión de Bus de servicio específico del inquilino (que también establecemos automáticamente durante el proceso de configuración de la escritura diferida). Esta retransmisión está protegida por una contraseña generada de forma aleatoria que sólo conoce la instalación local.
6.	Una vez que el mensaje llega al bus de servicio, el extremo de restablecimiento de contraseña se activa automáticamente y observa que tiene una solicitud de restablecimiento pendiente.
7.	A continuación, el servicio busca al usuario correspondiente mediante el atributo delimitador de la nube. Para que esta búsqueda se realice correctamente, el objeto de usuario debe existir en el espacio del conector AD, debe estar vinculado al objeto MV correspondiente y se debe vincular al objeto de conector de AAD correspondiente. Por último, para que la sincronización encuentre esta cuenta de usuario, el vínculo del objeto de conector de AD al objeto MV debe tener la regla de sincronización `Microsoft.InfromADUserAccountEnabled.xxx` en el vínculo. Esto es necesario porque cuando se recibe la llamada de la nube, el motor de sincronización utiliza el atributo cloudAnchor para buscar el objeto del espacio del conector de AAD; posteriormente, sigue el vínculo al objeto MV y, a continuación, sigue el vínculo al objeto de AD. Dado que podría haber varios objetos de AD (bosque múltiple) para el mismo usuario, el motor de sincronización se basa en el `Microsoft.InfromADUserAccountEnabled.xxx` vínculo para seleccionar el correcto.
8.	Una vez que se encuentra la cuenta de usuario, se intenta restablecer la contraseña directamente en el bosque de AD correspondiente.
9.	Si la operación de establecimiento de la contraseña se realiza correctamente, notificamos al usuario que la contraseña se ha modificado y que puede continuar con su trabajo.
10.	Si se produce un error en la operación de establecimiento de la contraseña, se devolverá el error al usuario y se le permitirá que vuelva a intentarlo. La operación puede producir un error porque el servicio no estaba disponible, la contraseña seleccionada no cumplía las directivas de la organización, no se encontraba el usuario en el entorno local de AD o por otros motivos. Disponemos de un mensaje específico para muchos de estos casos, a fin de indicar al usuario qué puede hacer para resolver el problema.

### Escenarios admitidos para la escritura diferida de contraseñas
En la tabla siguiente se describe qué escenarios se admiten para las versiones de nuestras capacidades de sincronización. En general, se recomienda que instale la versión más reciente de [Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect) si desea utilizar la escritura diferida de contraseñas.

  ![][002]

### Modelo de seguridad de la escritura diferida de contraseñas
La escritura diferida de contraseñas es un servicio sumamente seguro y sólido. Para asegurarse de que su información está protegida, habilitamos un modelo de seguridad de cuatro niveles que describimos a continuación.

- **Retransmisión de Bus de servicio específica de inquilino**: al configurar el servicio, configuramos una retransmisión de Bus de servicio específica del inquilino que está protegida por una contraseña segura generada aleatoriamente a la que Microsoft nunca tiene acceso.
- **Clave de cifrado de contraseñas bloqueadas y criptográficamente segura**: una vez creada la retransmisión de Bus de servicio, creamos una clave simétrica segura que se utiliza para cifrar la contraseña tal como se transfiere a través de la red. Esta clave reside solo en el almacén secreto de la compañía en la nube, que se bloquea y audita de forma estricta como cualquier contraseña en el directorio.
- **TLS estándar del sector**: cuando se produce una operación de restablecimiento o cambio de contraseña, ciframos la contraseña de texto no cifrado con la clave pública. A continuación, la insertamos en un mensaje HTTPS que se envía a través de un canal cifrado con certificados SSL de Microsoft para la retransmisión de Bus de servicio. Después de que ese mensaje llega al Bus de servicio, el agente local se activa, se autentica en el Bus de servicio con la contraseña segura que se había generado previamente, recoge el mensaje cifrado, lo descifra con la clave privada generada y, a continuación, intenta establecer la contraseña a través de la API SetPassword de AD DS. Este paso nos permite aplicar la directiva de contraseñas local de AD (complejidad, edad, historial, filtros, etc.) en la nube.
- **Directivas de expiración de mensajes**: por último, si por alguna razón el mensaje espera en el Bus de servicio porque el servicio local no funciona, se agotará el tiempo de espera y se eliminará transcurridos unos minutos para aumentar aún más la seguridad.

## ¿Cómo funciona el portal de restablecimiento de contraseñas?
Cuando un usuario navega al portal de restablecimiento de contraseñas, se inicia un flujo de trabajo para determinar si esa cuenta de usuario es válida, a qué organización pertenece, dónde se administra la contraseña del usuario y si el usuario dispone o no de una licencia para usar la característica. Lea los pasos siguientes para obtener información sobre la lógica de la página de restablecimiento de contraseña.

1.	El usuario hace clic en el vínculo ¿No puede tener acceso a su cuenta? o visita directamente [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.	El usuario escribe un identificador de usuario y pasa un captcha.
3.	Azure AD comprueba si el usuario es capaz de utilizar esta característica; para ello, hace lo siguiente:
    - Comprueba que el usuario tiene esta característica habilitada y una licencia de Azure AD asignada.
        - Si el usuario no tiene asignada una licencia o esta característica no está habilitada, se solicita al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
    - Comprueba que el usuario tiene los datos de comprobación definidos en la cuenta según la directiva del administrador.
        - Si la directiva requiere solo una comprobación, se garantiza que el usuario tiene los datos correspondientes definidos para al menos una de las comprobaciones habilitadas por la directiva del administrador.
          - Si el usuario no está configurado, se recomienda al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
        - Si la directiva requiere dos comprobaciones, se garantiza que el usuario tiene los datos correspondientes definidos para al menos dos de las comprobaciones habilitadas por la directiva del administrador.
          - Si el usuario no está configurado, se recomienda al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
    - Comprueba si la contraseña del usuario se administra o no a nivel local (federada o sincronizada con hash de contraseña).
       - Si la escritura diferida de contraseñas está implementada y la contraseña del usuario se administra de forma local, se le permite continuar con la autenticación y restablecer la contraseña.
       - Si la escritura diferida de contraseñas no está implementada y la contraseña del usuario se administra de forma local, se le pide que se ponga en contacto con el administrador para restablecer la contraseña.
4.	Si se determina que el usuario puede restablecer correctamente la contraseña, se le guiará a través del proceso de restablecimiento.

Obtenga más información sobre cómo implementar la escritura diferida de contraseñas en [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md).

### ¿Qué datos sirven para restablecer la contraseña?
En la tabla siguiente se describe dónde y cómo se usan estos datos durante el restablecimiento de la contraseña y está diseñada para ayudarle a decidir qué opciones de autenticación resultan apropiadas para su organización. En esta tabla también se indican los requisitos de formato para los casos donde va a proporcionar datos en nombre de usuarios desde rutas de acceso de entrada que no validan estos datos.

> [AZURE.NOTE] El teléfono del trabajo no aparece en el portal de registro porque los usuarios actualmente no pueden editar esta propiedad en el directorio.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nombre del método de contacto</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de datos de Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Dónde se usa/configura?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisitos de formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Teléfono del trabajo</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>Por ejemplo: Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Configurable en:</p>
              <p>PhoneNumber se puede configurar desde PowerShell, DirSync, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (p. ej.: +1 1234567890)</p>
              <ul>
                <li class="unordered">
										Debe proporcionar un código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Debe proporcionar un código de área (si procede).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Debe proporcionar un signo + antes del código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Debe haber un espacio entre el código de país y el resto del número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										No se admiten extensiones; por tanto, si ha especificado extensiones, las quitaremos antes de enviar la llamada de teléfono.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Teléfono móvil</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>O</p>
              <p>MobilePhone</p>
              <p>(El teléfono de autenticación se utiliza si hay datos presentes; en caso contrario, recurre al campo de teléfono móvil).</p>
              <p>Por ejemplo: Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Portal de registro</p>
              <p>Configurable en: </p>
              <p>AuthenticationPhone se puede configurar desde el portal de registro de restablecimiento de contraseña o desde el portal de registro de MFA.</p>
              <p>MobilePhone se puede configurar desde PowerShell, DirSync, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (p. ej.: +1 1234567890)</p>
              <ul>
                <li class="unordered">
										Debe proporcionar un código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Debe proporcionar un código de área (si procede).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Debe proporcionar un signo + antes del código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Debe haber un espacio entre el código de país y el resto del número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										No se admiten extensiones; por tanto, si ha especificado extensiones, las ignoraremos al realizar la llamada de teléfono.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Correo electrónico alternativo</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>O</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>(El correo electrónico de autenticación se utiliza si hay datos presentes; en caso contrario, recurre al campo de correo electrónico alternativo).</p>
              <p>Nota: El campo de correo electrónico alternativo se especifica como una matriz de cadenas en el directorio. Usamos la primera entrada de esta matriz.</p>
              <p>Por ejemplo: Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Portal de registro</p>
              <p>Configurable en: </p>
              <p>AuthenticationEmail se puede configurar desde el portal de registro de restablecimiento de contraseña o desde el portal de registro de MFA.</p>
              <p>AuthenticationEmail se puede configurar desde PowerShell, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> o 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
										Los mensajes de correo electrónico deben seguir el formato estándar.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Se admiten los correos electrónicos Unicode.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Preguntas y respuestas de seguridad</p>
            </td>
            <td>
              <p>No está disponible para modificarlo directamente en el directorio.</p>
            </td>
            <td>
              <p>Usado en:</p>
              <p>Portal de restablecimiento de contraseñas</p>
              <p>Portal de registro </p>
              <p>Configurable en: </p>
              <p>Las preguntas de seguridad solo pueden realizarse en el Portal de administración de Azure.</p>
              <p>Las respuestas a preguntas de seguridad de un usuario determinado solo se pueden realizar en el Portal de registro.</p>
            </td>
            <td>
              <p>Las preguntas de seguridad deben contener un máximo de 200 caracteres y un mínimo de 3.</p>
              <p>Las preguntas de seguridad deben contener un máximo de 40 caracteres y un mínimo de 3.</p>
            </td>
          </tr>
        </tbody></table>

###Cómo obtener acceso a los datos de restablecimiento de la contraseña de los usuarios
####Datos configurables mediante la sincronización
Desde el entorno local, se pueden sincronizar los campos siguientes:

* Teléfono móvil
* Teléfono del trabajo

####Datos configurables con Azure AD PowerShell
Es posible acceder a los siguientes campos mediante Azure AD PowerShell y la API Graph:

* Correo electrónico alternativo
* Teléfono móvil
* Teléfono del trabajo
* Teléfono de autenticación
* Correo electrónico de autenticación

####Datos configurables solamente con la IU de registro
Solo se puede acceder a los campos siguientes a través del interfaz de usuario de registro SSPR (https://aka.ms/ssprsetup):

* Preguntas y respuestas de seguridad

####¿Qué ocurre cuando se registra un usuario?
Cuando se registre un usuario, la página de registro **siempre** establecerá los campos siguientes:

* Teléfono de autenticación
* Correo electrónico de autenticación
* Preguntas y respuestas de seguridad

Si ha proporcionado un valor para **Teléfono móvil** o **Correo electrónico alternativo**, los usuarios podrán usarlos inmediatamente los restablecer sus contraseñas, incluso si no se han registrado para el servicio. Además, los usuarios verán esos valores cuando se registren por primera vez, y podrán modificarlos si lo desean. Sin embargo, después de que se registren correctamente, estos valores se conservarán en los campos **Teléfono de autenticación** y **Correo electrónico de autenticación**, respectivamente.

Esto puede ser una forma útil de desbloquear grandes cantidades de usuarios para usar SSPR mientras se sigue permitiendo que los usuarios validen esta información a través del proceso de registro.

####Establecimiento de datos de restablecimiento de contraseñas con PowerShell
Puede establecer valores para los siguientes campos con Azure AD PowerShell.

* Correo electrónico alternativo
* Teléfono móvil
* Teléfono del trabajo

Para empezar, primero deberá [descargar e instalar el módulo de Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Una vez instalado, puede seguir los pasos siguientes para configurar cada campo.

#####Correo electrónico alternativo
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####Teléfono móvil
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####Teléfono del trabajo
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####Lectura de datos de restablecimiento de contraseñas con PowerShell
Puede leer valores de los siguientes campos con Azure AD PowerShell.

* Correo electrónico alternativo
* Teléfono móvil
* Teléfono del trabajo
* Teléfono de autenticación
* Correo electrónico de autenticación

Para empezar, primero deberá [descargar e instalar el módulo de Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Una vez instalado, puede seguir los pasos siguientes para configurar cada campo.

#####Correo electrónico alternativo
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####Teléfono móvil
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####Teléfono del trabajo
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####Teléfono de autenticación
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####Correo electrónico de autenticación
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## Vínculos a la documentación de restablecimiento de la contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* [**Restablecimiento de la propia contraseña**](active-directory-passwords-update-your-own-password.md): obtenga información sobre cómo restablecer o cambiar su propia contraseña como usuario del sistema
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis diferentes componentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Prácticas recomendadas**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Obtener perspectivas**](active-directory-passwords-get-insights.md): obtenga información sobre nuestras capacidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

<!---HONumber=AcomDC_0218_2016-->