<properties
	pageTitle="Azure AD Connect: Solución de problemas de conectividad | Microsoft Azure"
	description="Explica cómo solucionar problemas de conectividad con Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="andkjell"/>

# Solución de problemas de conectividad con Azure AD Connect
Este artículo explica cómo funciona la conectividad entre Azure AD Connect y Azure AD y cómo solucionar los problemas de conectividad. Estos problemas suelen aparecer en un entorno con un servidor proxy.

## Solución de problemas de conectividad en el asistente para la instalación
Azure AD Connect depende de dos métodos de configuración diferentes para conectarse a Azure AD. El asistente para instalación y el motor de sincronización correspondiente requieren machine.config para configurarse correctamente ya que se trata de aplicaciones. NET. También hay una dependencia del asistente de inicio de sesión y requiere que se configure winhttp para funcionar correctamente.

En este artículo mostraremos cómo Fabrikam se conecta a Azure AD a través de su servidor proxy. El servidor proxy se llama fabrikamproxy y está usando el puerto 8080.

En primer lugar, debe asegurarse de que [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) está configurado correctamente. ![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [AZURE.NOTE] En algunos blogs se indica que se deben realizar cambios en miiserver.exe.config. Sin embargo, este archivo se sobrescribe en cada actualización por lo que aunque funcione durante la instalación inicial, el sistema dejará de funcionar en la primera actualización. Por ese motivo, se recomienda actualizar el archivo machine.config en su lugar.

En segundo lugar, debemos asegurarnos de que winhttp está configurado. Esto puede hacerse con [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity). ![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

El servidor proxy también debe tener abiertas las direcciones URL necesarias. La lista oficial está documentada en [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

De ellas, la tabla siguiente es el mínimo necesario para poder conectarse a Azure AD. Esta lista no incluye características opcionales, como la escritura diferida de contraseñas ni Azure AD Connect Health. Se documentan aquí para ayudar a solucionar problemas de la configuración inicial.

| URL | Port | Descripción |
| ---- | ---- | ---- |
| mscrl.microsoft.com | HTTP/80 | Se usa para descargar listas CRL. |
| **.verisign.com | HTTP/80 | Se usa para descargar listas CRL. | | *.windows.net | HTTPS/443 | Se usa para iniciar sesión en Azure AD. | | *.microsoftonline.com | HTTPS/443 | Se usa para configurar el directorio de Azure AD y los datos de importación y exportación. |

## Errores en el asistente
El asistente para la instalación usa dos contextos de seguridad diferentes. En la página **Conectarse a Azure AD** que utiliza el usuario que ha iniciado sesión actualmente. En la página **Configurar** que está cambiando a la [cuenta de que ejecuta el servicio para el motor de sincronización](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Las configuraciones de proxy que realizamos son globales para el equipo por lo que si hay un problema, probablemente ya aparecerá en la página **Conectarse a Azure AD** del asistente.

Estos son los errores más comunes que verá en el asistente para la instalación.

### El asistente para la instalación no se ha configurado correctamente
Este error aparecerá cuando el propio asistente no pueda ponerse en contacto con el proxy. ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Si ve esto, compruebe si la configuración de [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) es correcta.
- Si es correcta, siga los pasos de [Comprobar la conectividad de proxy](#verify-proxy-connectivity) para ver si el problema está presente también fuera del asistente.

### El Asistente para el inicio de sesión no se ha configurado correctamente
Este error aparece cuando el Ayudante para el inicio de sesión no puede conectar con el servidor proxy o este no permite la solicitud. ![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Si ve esto, examine la configuración de proxy en [netsh](active-directory-aadconnect-prerequisites.md#connectivity) y compruebe si es correcta. ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Si es correcta, siga los pasos de [Comprobar la conectividad de proxy](#verify-proxy-connectivity) para ver si el problema está presente también fuera del asistente.

### No se puede comprobar la contraseña
Si el asistente para la instalación conecta correctamente con Azure AD pero la contraseña no se puede comprobar, verá lo siguiente: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- ¿La contraseña es temporal y se debe cambiar? ¿Es realmente la contraseña correcta? Intente iniciar sesión en https://login.microsoftonline.com (en un servidor distinto del servidor de Azure AD Connect) y compruebe que la cuenta se puede usar.
- ¿Multi-Factor Authentication (MFA) está habilitado en el usuario? Si lo está, deshabilítelo.

### Comprobar la conectividad de proxy
Para comprobar si el servidor de Azure AD Connect tiene conectividad real con el servidor proxy e Internet, usaremos PowerShell para ver si el servidor proxy permite solicitudes web o no. En un símbolo del sistema de PowerShell, ejecute `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Técnicamente, la primera llamada es a https://login.microsoftonline.com y esto funcionará también, pero el otro URI es más rápido en responder).

PowerShell usará la configuración de machine.config para ponerse en contacto con el servidor proxy. La configuración de winhttp/netsh no debería afectar a estos cmdlets.

Si el servidor proxy está configurado correctamente, debería obtener un estado correcto: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Si recibe **No es posible conectar con el servidor remoto**, PowerShell está intentando realizar una llamada directa sin usar el servidor proxy o DNS no está configurado correctamente. Asegúrese de que **machine.config** está configurado correctamente. ![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Si el proxy no está configurado correctamente, se producirá un error: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png) ![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Error | Texto del error | Comentario |
| ---- | ---- | ---- |
| 403 | Prohibido | No se abrió el servidor proxy para la dirección URL solicitada. Revise la configuración del servidor proxy y asegúrese de que las direcciones [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) estén abiertas. |
| 407 | Se requiere autenticación del proxy | El servidor proxy requiere inicio de sesión y no se ha proporcionado. Si el servidor proxy requiere autenticación, asegúrese de que esto esté configurado en el archivo machine.config. Compruebe también que está usando cuentas de dominio para el usuario que ejecuta el asistente, así como para la cuenta de servicio. |

## Patrón de comunicación entre Azure AD Connect y Azure AD
Si siguió todos los pasos anteriores y aún no se puede conectar, en este momento puede comenzar a examinar los registros de red. En esta sección se documenta un patrón de conectividad normal y correcta. También muestra una lista de pistas falsas habituales que se pueden omitir si se están leyendo los registros de red.

- Habrá llamadas a https://dc.services.visualstudio.com. No es necesario tenerla abierto en el proxy para que la instalación sea correcta y estas pueden omitirse.
- Verá que la resolución dns enumerará los hosts reales que estarán en el espacio de nombres DNS nsatc.net y otros espacios de nombres que no están bajo microsoftonline.com. Sin embargo, no habrá solicitudes de servicio web en los nombres de servidor reales y no es necesario agregarlos al proxy.
- Los puntos de conexión adminwebservice y provisioningapi (consulte más adelante en los registros) son los puntos de conexión de detección y se usan para buscar el punto de conexión real que se usará y serán diferentes según su región.

### Registros de proxy de referencia
Este es un volcado de un registro de proxy real y la página del asistente para instalación desde la que se tomó (se quitaron las entradas duplicadas al mismo punto de conexión). Puede usarse como referencia para sus propios registros de proxy y de red. Los puntos de conexión reales pueden variar en su entorno (en particular los que están en *cursiva*).

**Conectarse a Azure AD**

Hora | URL
--- | ---
11/1/2016 8:31 | connect://login.microsoftonline.com:443
11/1/2016 8:31 | connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:32 | connect://*bba800-anchor*.microsoftonline.com:443
11/1/2016 8:32 | connect://login.microsoftonline.com:443
11/1/2016 8:33 | connect://provisioningapi.microsoftonline.com:443
11/1/2016 8:33 | connect://*bwsc02-relay*.microsoftonline.com:443

**Configuración**

Hora | URL
--- | ---
11/1/2016 8:43 | connect://login.microsoftonline.com:443
11/1/2016 8:43 | connect://*bba800-anchor*.microsoftonline.com:443
11/1/2016 8:43 | connect://login.microsoftonline.com:443
11/1/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:44 | connect://*bba900-anchor*.microsoftonline.com:443
11/1/2016 8:44 | connect://login.microsoftonline.com:443
11/1/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:44 | connect://*bba800-anchor*.microsoftonline.com:443
11/1/2016 8:44 | connect://login.microsoftonline.com:443
11/1/2016 8:46 | connect://provisioningapi.microsoftonline.com:443
11/1/2016 8:46 | connect://*bwsc02-relay*.microsoftonline.com:443

**Sincronización inicial**

Hora | URL
--- | ---
11/1/2016 8:48 | connect://login.windows.net:443
11/1/2016 8:49 | connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:49 | connect://*bba900-anchor*.microsoftonline.com:443
11/1/2016 8:49 | connect://*bba800-anchor*.microsoftonline.com:443

<!---HONumber=AcomDC_0128_2016-->