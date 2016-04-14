<properties
	pageTitle="Vista previa de Azure Active Directory B2C: personalización de la interfaz de usuario (UI) | Microsoft Azure"
	description="Tema sobre las características de personalización de la interfaz de usuario de Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Versión preliminar de Azure Active Directory B2C: personalización de la interfaz de usuario de Azure AD B2C

La experiencia del usuario es primordial en una aplicación de consumo. Ahí radica la diferencia entre una buena aplicación y otra excelente, y entre los consumidores simplemente activos y los realmente implicados. Azure Active Directory (AD) B2C le permite personalizar las páginas de registro, inicio de sesión (*vea la nota siguiente*) y edición de perfil del consumidor con un control perfecto de píxeles.

> [AZURE.NOTE]
En este momento, las páginas de inicio de sesión de cuentas locales, los mensajes de correo electrónico de verificación y las páginas de autoservicio de restablecimiento de contraseña solo se pueden personalizar mediante la [característica de personalización de marca corporativa](../active-directory/active-directory-add-company-branding.md) y no mediante los mecanismos descritos en este artículo.

En este artículo se describen:

- La característica de personalización de la interfaz de usuario de la página.
- Una herramienta auxiliar que le ayudará a probar la característica de personalización de la interfaz de usuario de la página empleando nuestro contenido de ejemplo.
- Los elementos de interfaz de usuario principales en cada tipo de página.
- Prácticas recomendadas al usar esta característica.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## La característica de personalización de la interfaz de usuario de la página

La característica de personalización de la interfaz de usuario de la página le permite personalizar la apariencia de las páginas de registro, inicio de sesión y edición de perfil del consumidor (mediante la configuración de [directivas](active-directory-b2c-reference-policies.md)). Los consumidores tendrán experiencias uniformes al navegar entre su aplicación y las páginas a las que presta servicio Azure AD B2C.

A diferencia de otros servicios, donde las opciones de IU son limitadas o solo están disponibles a través de API, Azure AD B2C usa un enfoque moderno (y más sencillo) para la personalización de la interfaz de usuario de la página. Funciona de la siguiente manera: Azure AD B2C ejecuta código en el explorador del consumidor y usa un enfoque moderno denominado [Uso compartido de recursos entre orígenes (CORS)](http://www.w3.org/TR/cors/) para cargar el contenido de una dirección URL que especifique en una directiva. Puede especificar diferentes direcciones URL para distintas páginas. El código combina elementos de IU de Azure AD B2C con el contenido cargado desde la dirección URL y muestra la página al consumidor. Basta con que cree contenido HTML5 bien formado con un elemento `<div id="api"></div>` ubicado en algún lugar de `<body>`. Ese elemento marca el lugar en el que se inserta el contenido de Azure AD B2C. Este contenido debe hospedarse en un punto de conexión HTTPS (con CORS permitido). También puede aplicar estilo completo a elementos de la interfaz de usuario de Azure AD B2C.

## Prueba de la característica de personalización de la interfaz de usuario

Si quiere probar la característica de personalización de la interfaz de usuario mediante nuestro contenido HTML y CSS de ejemplo hospedado en el Almacenamiento de blobs de Azure, ponemos a su disposición [una sencilla herramienta auxiliar](active-directory-b2c-reference-ui-customization-helper-tool.md) para cargar y configurar el contenido estático.

## Principales elementos de la interfaz de usuario en cada tipo de página

En las secciones siguientes encontrará ejemplos de fragmentos HTML5 (para cada tipo de página) que Azure AD B2C combina en el elemento <div id="api"></div> que se encuentra en el contenido. Puede usar sus propias hojas de estilos para personalizar estos elementos de interfaz de usuario. Estas hojas de estilos tendrán que invalidar las hojas de estilos predeterminadas que agregamos en estas páginas en los fragmentos <head>.

> [AZURE.IMPORTANT]
	Durante la vista previa, tenga en cuenta que cambiarán los elementos exactos de la interfaz de usuario a medida que recibimos sus comentarios y los adaptamos. Consulte siempre el código fuente de páginas predeterminadas para las actualizaciones más recientes. De hecho, el primer cambio que se va a considerar es la eliminación de nuestras hojas de estilos predeterminadas; esto significa que siempre debe suministrar sus propias hojas de estilos para estos elementos de interfaz de usuario en su contenido.

### Página de selección de proveedores de identidades

Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el registro o el inicio de sesión. Estos son los proveedores de identidades sociales como Facebook y Google+ o las cuentas locales (basadas en una dirección de correo electrónico o un nombre de usuario).

```HTML

<div id="api" data-name="IdpSelections">
	<div class="intro">
         <p>Sign up</p>
	</div>

	<div>
		<ul>
			<li>
				<button class="accountButton" id="FacebookExchange">Facebook</button>
			</li>
			<li>
				<button class="accountButton" id="GoogleExchange">Google+</button>
			</li>
			<li>
				<button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
			</li>
		</ul>
	</div>
</div>

```

### Página de registro de cuenta local

Esta página contiene un formulario de registro que el usuario tiene que rellenar al registrarse con una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener diferentes controles de entrada, como un cuadro de entrada de texto, un cuadro de entrada de contraseña, un botón de radio, cuadros desplegables de selección única y casillas de verificación de selección múltiple.

```HTML

<div id="api" data-name="SelfAsserted">
	<div class="intro">
		<p>Create your account by providing the following details</p>
	</div>

	<div id="attributeVerification">
		<div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
		<div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
		<div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
		<div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
		<div class="attr" id="attributeList">
			<ul>
				<li>
					<div class="attrEntry validate">
						<div>
							<div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
							<div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
							<div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
							<div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
							<div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
							<div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
							<div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
						</div>

					<div class="helpText show">This information is required</div>
						<label>Email</label>
						<input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

					<div class="buttons verify" claim_id="email">
						<div id="email_ver_wait" class="working" style="display: none;"></div>
							<label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
							<input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
							<button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
							<button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
							<button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
							<button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
							<button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
						</div>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
						<label>Enter password</label>
						<input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"> This information is required</div>
						<label>Reenter password</label>
						<input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Name</label>
						<input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Gender</label>
						<input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
						<label for="extension_Gender_F">Female</label>
						<input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
						<label for="extension_Gender_M">Male</label>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Loyalty number</label>
						<input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>State</label>
						<select class="dropdown_single" id="state">
							<option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
							<option value="WA">Washington</option>
							<option value="NY">New York</option>
							<option value="CA">California</option>
						</select>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Zip code</label>
						<input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
					</div>
				</li>
			</ul>
		</div>
		<div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
	</div>
	<div class="verifying-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="verifying_blurb"></div>
	</div>
</div>

```

### Página de registro de cuenta social

Esta página contiene un formulario de registro que el consumidor tiene que rellenar al registrarse con una cuenta existente de un proveedor de identidades social como Facebook o Google+. Esta página es similar a la página de registro de cuenta local (que se muestra en la sección anterior) con la excepción de los campos de entrada de contraseña.

### Página de autenticación multifactor

Esta página permite a los usuarios verificar sus números de teléfono (mediante texto o voz) durante el registro o el inicio de sesión.

```HTML

<div id="api" data-name="Phonefactor">
	<div id="phonefactor_initial">
		<div class="intro">
			<p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
		</div>
		<div class="errorText" id="errorMessage" style="display:none"></div>
		<div class="phoneEntry" id="phoneEntry">
			<div class="phoneNumber">
				<select id="countryCode" style="display:inline-block">
					<option value="+93">Afghanistan (+93)</option>
					<!-- Not all country codes listed -->
					<option value="+44">United Kingdom (+44)</option>
					<option value="+1" selected="">United States (+1)</option>
					<!-- Not all country codes listed -->
				</select>
			</div>
			<div class="phoneNumber">
				<input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
			</div>
		</div>
		<div id="codeVerification" style="display:none">
			<div>
				<p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
				<input type="text" id="verificationCode" placeholder="Verification code">
			</div>
		</div>
		<div class="buttons">
			<button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
			<button id="verifyPhone" style="display:inline-block">Call Me</button>
			<button id="cancel" style="display:inline-block">Cancel</button>
		</div>
	</div>
	<div class="dialing-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="dialing_blurb"></div><div id="dialing_number"></div>
	</div>
</div>

```

### Página de error

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
	<h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
	<div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
	<div class="error-page-messagedetails">
		<div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
		<div>Timestamp:2015-09-14 23:22:35Z</div>
		<div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
	</div>
</div>

```

## Elementos que debe recordar al crear su propio contenido

Si piensa usar la característica de personalización de la interfaz de usuario de la página, revise las siguientes prácticas recomendadas:

- No copie la plantilla predeterminada de Azure AD B2C y luego intente modificarla. Es mejor generar su propio contenido HTML5 desde cero y usar la plantilla predeterminada como referencia.
- Por motivos de seguridad, no le permitimos incluir cualquier código JavaScript en su contenido. La mayor parte de lo que necesita debería estar disponible de fábrica. De no ser así, use la [voz del usuario](http://feedback.azure.com/forums/169401-azure-active-directory) para solicitar la nueva funcionalidad.
- Versiones de explorador admitidas:
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (limitado)
	- Internet Explorer 8 (limitado)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=AcomDC_0224_2016-->