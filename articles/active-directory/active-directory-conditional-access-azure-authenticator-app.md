
<properties
	pageTitle="Azure Authenticator para Android | Microsoft Azure"
	description="La aplicación Microsoft Azure Authenticator se puede utilizar para iniciar sesión y tener acceso a los recursos de trabajo. La aplicación Azure Authenticator le notifica una solicitud de verificación de dos factores pendiente mediante una alerta en el dispositivo móvil."
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

# Azure Authenticator para Android

Es posible que el administrador de TI le haya recomendado utilizar Microsoft Azure Authenticator para iniciar sesión y tener acceso a los recursos de trabajo. Esta aplicación proporciona dos opciones de inicio de sesión:

* Multi-Factor Authentication permite proteger las cuentas profesionales o educativas con una verificación en dos pasos. Inicie sesión con algo que conoce (por ejemplo, la contraseña) y proteja la cuenta aún más con algo que tiene (una clave de seguridad de esta aplicación). La aplicación Azure Authenticator le notifica una solicitud de verificación de dos factores pendiente mediante una alerta en el dispositivo móvil. Solo tiene que leer la solicitud en la aplicación y pulsar Comprobar o Cancelar. Como alternativa, puede que se le pida que escriba el código de acceso que se muestra en la aplicación.

* La cuenta profesional le permite convertir su teléfono o tableta Android en un dispositivo de confianza y proporcionar el inicio de sesión único (SSO) para las aplicaciones de la empresa. El administrador de TI puede solicitarle que agregue una cuenta profesional para tener acceso a los recursos de la empresa. SSO permite iniciar sesión una vez y aprovechar automáticamente el inicio de sesión en todas las aplicaciones que la empresa ha puesto a su disposición.

## Instalación de la aplicación Azure Authenticator

Puede instalar la aplicación Azure Authenticator desde Google Play Store. Las instrucciones para agregar una cuenta profesional desde un dispositivo Android de Samsung y desde un dispositivo Android que no es de Samsung son ligeramente diferentes. A continuación se muestran las instrucciones para ambos.

Agregar la cuenta profesional desde un dispositivo Android de Samsung
----------------------------------------------------------------------------------------------------------------
###Agregar la cuenta profesional desde la pantalla principal de la aplicación

Las instrucciones siguientes son aplicables a teléfonos Samsung GS3 y versiones posteriores, y tabletas Note2 y versiones posteriores.

1. En la pantalla principal de la aplicación, acepte el contrato de licencia de usuario final (CLUF).
2. En la pantalla Activar cuenta, haga clic en el menú contextual de la derecha y seleccione **Cuenta profesional**.
3. En la pantalla Agregar cuenta, seleccione **Cuenta profesional**.
4. En la pantalla Activar administrador del dispositivo, haga clic en **Activar**.
5. En la pantalla de la directiva de privacidad, marque la casilla y haga clic en **Confirmar**.
6. En la pantalla Unión al área de trabajo, escriba el identificador de usuario proporcionado por su organización y haga clic en **Unir**.
7. Para iniciar sesión en la aplicación Azure Authenticator, escriba su cuenta profesional y contraseña y haga clic en **Iniciar sesión**.
8. La siguiente pantalla que muestra información acerca de Multi-Factor Authentication (MFA) se incluye para mayor seguridad y es opcional. Verá esta pantalla si su trabajo o escuela requiere autenticación de segundo factor para crear la cuenta profesional. Proporciona instrucciones para comprobar su cuenta.
9. En la pantalla Unión al área de trabajo aparece el mensaje "**Unirse al área de trabajo**". La aplicación Azure Authenticator está intentando unir el dispositivo al área de trabajo.
10. En la pantalla siguiente debería aparecer el mensaje "Unido a área de trabajo".

>[AZURE.NOTE]Se permite una sola cuenta profesional en el dispositivo.

### Agregar la cuenta profesional en el menú Configuración
Después de instalar la aplicación Azure Authenticator, también puede crear una cuenta profesional desde el Administrador de cuentas de Android.

1. Desde el menú Configuración, vaya a**Cuentas** y haga clic en **Agregar cuenta**.
2. Siga los pasos del 3 al 10 del procedimiento "Agregar la cuenta profesional desde la pantalla principal de la aplicación" para agregar una cuenta profesional.

Agregar la cuenta profesional desde un dispositivo Android que no es de Samsung
------------------------------------------------------------------------------------------------------------------
### Agregar la cuenta profesional desde la pantalla principal de la aplicación

1. En la pantalla principal de la aplicación, acepte el contrato de licencia de usuario final (CLUF).
2. En la pantalla Activar cuenta, haga clic en el menú contextual de la derecha y seleccione **Cuenta profesional**.
3. En la pantalla Cuentas, haga clic en **Agregar cuenta**.
4. Si ve la pantalla Cuentas, haga clic en **Agregar cuenta**. Si ya se creó previamente una cuenta profesional, verá la pantalla Sincronización, que muestra la cuenta profesional existente. Puede conservar la cuenta profesional simplemente pulsando la flecha Atrás para regresar a la pantalla principal. Como alternativa, puede seleccionar una cuenta para quitarla y volver a crear una nueva cuenta profesional. En la pantalla Unión al área de trabajo, escriba el identificador de usuario proporcionado por su organización y haga clic en Unir.
5. Para iniciar sesión en la aplicación Azure Authenticator, escriba su cuenta de organización y contraseña y haga clic en **Iniciar sesión**.
7. La siguiente pantalla que muestra información acerca de Multi-Factor Authentication (MFA) se incluye para mayor seguridad y es opcional. Verá esta pantalla si su trabajo o escuela requiere autenticación de segundo factor para crear la cuenta profesional. Proporciona instrucciones para comprobar su cuenta.
8. Haga clic en **Aceptar** en la pantalla siguiente. No cambie el nombre del certificado. En la pantalla Unión al área de trabajo aparece el mensaje "**Unirse al área de trabajo**". La aplicación Azure Authenticator está intentando unir el dispositivo al área de trabajo. En la pantalla siguiente debería aparecer el mensaje "Unido a área de trabajo".

>[AZURE.NOTE]Se permite una sola cuenta profesional en el dispositivo.

Después de instalar la aplicación Azure Authenticator, también puede crear una cuenta profesional desde el Administrador de cuentas de Android.

1. Desde el menú **Configuración**, vaya a Cuentas y haga clic en **Agregar cuenta**.
2. Siga los pasos del 2 al 7 del procedimiento "Agregar la cuenta profesional desde la pantalla principal de la aplicación" para agregar una cuenta profesional.

### Información sobre qué versión está instalada

1. Puede averiguar qué versión de la aplicación Azure Authenticator y qué versiones de servicios asociados están instaladas en el dispositivo.
2. En el menú emergente, haga clic en **Acerca de**.
3. En la pantalla "Acerca de" se muestran los servicios de la aplicación y las versiones instalados en el dispositivo.
 
### Envío de archivos de registro para informar de problemas

1. Siga las instrucciones del Soporte técnico de Microsoft para informar sobre un incidente con la aplicación Azure Authenticator, obtener un número de incidente y enviar archivos de registro con el número de incidente asignado de la manera siguiente:
2. En el menú emergente, haga clic en **Registro**.
3. Si dispone de un incidente abierto en el Soporte técnico de Microsoft, anote el número de incidente (ya que lo necesitará en un paso posterior). Si aún no ha creado un incidente de soporte técnico y necesita ayuda, siga las instrucciones del [Soporte técnico de Microsoft](https://support.microsoft.com/es-ES/contactus) para abrir un incidente nuevo.
4. En la pantalla de registro, haga clic en **Enviar ahora**.
5. Seleccione el proveedor de correo electrónico que desea utilizar.
7. Si ya dispone de un incidente abierto en el Soporte técnico de Microsoft, póngase en contacto con el ingeniero de soporte técnico asignado a su problema para averiguar cómo enviar los datos del registro y asociarlos al incidente. El ingeniero de soporte técnico le proporcionará información relativa a la dirección de correo electrónico y a la línea de asunto del correo. Si todavía no dispone de un incidente, siga las instrucciones indicadas en el Soporte técnico de Microsoft para abrir un incidente nuevo.
9. Edite la línea **Para** y **Asunto** con la información que ha recibido del Soporte técnico de Microsoft.
10. La aplicación Azure Authenticator adjunta el archivo de registro al correo electrónico que va a enviar. Describa el problema que está experimentando, actualice la lista de destinatarios (opcional) y envíe el correo electrónico.

### Eliminación de una cuenta profesional y salida del área de trabajo

Puede quitar la cuenta profesional creada en cualquier momento; para ello, proceda como sigue:

**Para eliminar la cuenta profesional desde el menú Configuración**

1. En el Administrador de cuentas, seleccione **Cuenta profesional**.
2. En la pantalla Cuenta profesional, en **Configuración general**, seleccione **Configuración de la cuenta - Abandonar la red de área de trabajo**.
3. Seleccione **Abandonar** en la pantalla **Unión al área de trabajo**.
4. Haga clic en **Aceptar** cuando se muestre el mensaje "¿Está seguro de que desea abandonar el área de trabajo?".
5. Esto garantiza que ha eliminado la cuenta profesional de un área de trabajo.

>[AZURE.NOTE> Se recomienda no utilizar la opción Quitar cuenta para eliminar una cuenta profesional, ya que esta opción no funciona en algunas versiones anteriores de Android.

##Desinstalación de la aplicación

En un dispositivo Android de Samsung, es necesario quitar los privilegios de administrador del dispositivo de la manera siguiente antes de desinstalar la aplicación Azure Authenticator. 1 Desde **Configuración**, en **Sistema**, seleccione **Seguridad**. 2. En **Administración del dispositivo**, haga clic en **Administradores del dispositivo**. Asegúrese de que la casilla junto a**Azure Authenticator** está desactivada.

##Solución de problemas

Si ve un error que indica **Error de KeyStore**, podría deberse a que no ha configurado la pantalla de bloqueo con un PIN. Para evitar este problema, desinstale la aplicación Azure Authenticator, configure un PIN para la pantalla de bloqueo y reinstale la aplicación.

<!---HONumber=AcomDC_1203_2015-->