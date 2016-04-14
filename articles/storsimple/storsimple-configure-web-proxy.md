<properties 
   pageTitle="Configuración de un proxy web de un dispositivo StorSimple | Microsoft Azure"
   description="Obtenga información acerca de cómo usar Windows PowerShell para StorSimple para configurar opciones de proxy web para el dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Configurar el proxy web para el dispositivo StorSimple

## Información general

Este tutorial describe cómo utilizar Windows PowerShell para StorSimple para configurar y ver la configuración de proxy web de su dispositivo StorSimple. El dispositivo StorSimple utiliza la configuración de proxy web cuando se comunica con la nube. Un servidor proxy web se utiliza para agregar otra capa de seguridad, filtrar contenido, como memoria caché para facilitar el cumplimiento de los requisitos de ancho de banda o incluso para ayudar en los análisis.

Proxy Web es una configuración opcional para el dispositivo StorSimple. Puede configurar el proxy web solo a través de Windows PowerShell para StorSimple. La configuración es un proceso de dos pasos, como sigue:

1. En primer lugar, configure los valores de proxy web mediante el asistente de instalación o los cmdlets de Windows PowerShell para StorSimple.

2. A continuación, habilite los valores de proxy web configurados a través de los cmdlets de Windows PowerShell para StorSimple.

Una vez completada la configuración de proxy web, puede ver los valores configurados en el servicio de Administrador de Microsoft Azure StorSimple y en Windows PowerShell para StorSimple.

Después de leer este tutorial, podrá:

- Configurar el proxy web con el asistente de instalación y los cmdlets
- Habilitar el proxy web mediante cmdlets
- Ver la configuración de proxy web en el Portal de Azure clásico
- Solucionar errores durante la configuración del proxy web


## Configurar el proxy web a través de Windows PowerShell para StorSimple

Puede utilizar cualquiera de las siguientes opciones para configurar el proxy web:

- El asistente de instalación que le guiará a través de los pasos de configuración.

- Los cmdlets de Windows PowerShell para StorSimple

En las siguientes secciones se detallan cada uno de estos métodos.

## Configuración del proxy web mediante el asistente de instalación

Puede utilizar al asistente de configuración que le guiará a través de los pasos de configuración del proxy web. Lleve a cabo los siguientes pasos para configurar el proxy web en su dispositivo.

#### Para configurar el proxy web mediante el asistente de instalación

1. En el menú de la consola serie, elija la opción 1, **Iniciar sesión con acceso total** y proporcione la **contraseña de administrador de dispositivo**. Escriba el siguiente comando para iniciar la sesión del asistente de configuración:

    `Invoke-HcsSetupWizard`

2. Si esta es la primera vez que utiliza el asistente de configuración para el registro de dispositivos, deberá configurar todos los valores de red requeridos hasta llegar a la configuración del proxy web. Si el dispositivo ya está registrado, puede aceptar todos los valores de red configurados hasta llegar a la configuración del proxy web. En el asistente de configuración, cuando se le pregunte si desea configurar los valores del proxy web, escriba **Sí**.

3. Para la **URL del Proxy Web** especifique la dirección IP o el nombre de dominio completo (FQDN) del servidor proxy web y el número de puerto TCP que desea que el dispositivo utilice cuando se comunique con la nube. Utilice el siguiente formato:

	`http://<IP address or FQDN of the web proxy server>:<TCP port number>`

	De manera predeterminada, se especifica el número de puerto TCP 8080.

4. Elija el tipo de autenticación entre **NTLM**, **Básica** o **Ninguna**. La autenticación Básica es la menos segura para la configuración del servidor proxy. NT LAN Manager (NTLM) es un protocolo de autenticación complejo y muy seguro que utiliza un sistema de mensajería de tres vías (a veces cuatro si se requiere integridad adicional) para autenticar a un usuario. La autenticación predeterminada es NTLM. Para obtener más información, consulte [autenticación Básica](http://hc.apache.org/httpclient-3.x/authentication.html) y [NTLM](http://hc.apache.org/httpclient-3.x/authentication.html).

	> [AZURE.IMPORTANT] **En el servicio de Administrador de StorSimple, los gráficos de supervisión de dispositivos no funcionan cuando está habilitada la autenticación básica o NTLM en la configuración del servidor proxy para el dispositivo. Para que los gráficos de supervisión funcionen, debe asegurarse de que la autenticación esté establecida en NINGUNA.**

5. Si utiliza la autenticación, proporcione un **nombre de usuario de Proxy Web** y una **contraseña de Proxy Web**. También deberá confirmar la contraseña.

	![Configurar el proxy web en el dispositivo StorSimple 1](./media/storsimple-configure-web-proxy/IC751830.png)

Si va a registrar su dispositivo por primera vez, continúe con el registro. Si su dispositivo ya está registrado, el asistente se cerrará. Se guardarán los valores configurados.

Asimismo, se habilitará el proxy web. Puede omitir el paso [Habilitar proxy web](#enable-web-proxy) e ir directamente a [Ver la configuración de proxy web en el Portal de Azure clásico](#view-web-proxy-settings-in-the-azure-classic-portal).


## Configurar el proxy web mediante Windows PowerShell para StorSimple

Una forma alternativa de configurar los valores de proxy web es mediante los cmdlets de Windows PowerShell para StorSimple. Lleve a cabo los siguientes pasos para configurar el proxy web.

#### Para configurar el proxy web mediante los cmdlets

1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador del dispositivo**. La contraseña predeterminada es `Password1`.

2. En el símbolo del sistema, escriba:

	`Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

	Proporcione y confirme la contraseña cuando se le pida, tal como se muestra a continuación.

	![Configurar el proxy web en el dispositivo StorSimple 3](./media/storsimple-configure-web-proxy/IC751831.png)

El proxy web ya está configurado y debe habilitarse.

## Habilitar el proxy web

El proxy web está deshabilitado de manera predeterminada. Después de configurar los valores del proxy web en el dispositivo StorSimple, deberá utilizar Windows PowerShell para StorSimple para habilitar la configuración de proxy web.

> [AZURE.NOTE] **Este paso no será necesario si usa el asistente de configuración para configurar el proxy web. El proxy web se habilita automáticamente de manera predeterminada después de una sesión del asistente de configuración.**

Lleve a cabo los siguientes pasos en Windows PowerShell para StorSimple para habilitar el proxy web en el dispositivo:

#### Para habilitar el proxy web

1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador del dispositivo**. La contraseña predeterminada es `Password1`.

2. En el símbolo del sistema, escriba:

	`Enable-HcsWebProxy`

	La configuración de proxy web ya está habilitada en su dispositivo StorSimple.

	![Configurar el proxy web en el dispositivo StorSimple 4](./media/storsimple-configure-web-proxy/IC751832.png)

## Ver la configuración de proxy web en el Portal de Azure clásico

Los valores de proxy web se configuran mediante la interfaz de Windows PowerShell y no pueden modificarse desde el Portal clásico. Sin embargo, puede ver los valores configurados en el Portal clásico. Lleve a cabo los siguientes pasos para ver el proxy web:

#### Para ver la configuración de proxy web:
1. Vaya a **Servicio StorSimple Manager > Dispositivos**. Seleccione un dispositivo y haga clic en él y, luego, vaya a **Configurar**.
1. Desplácese hacia abajo en la página **Configurar** hasta la sección **Configuración de proxy web**. Puede ver los valores de proxy web configurados en el dispositivo StorSimple tal como se muestra a continuación.

	![Ver el proxy web en el Portal de administración](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## Errores durante la configuración del proxy web

Si el proxy web se configuró de manera incorrecta, se mostrarán mensajes de error al usuario en el Windows PowerShell para StorSimple. En la siguiente tabla se detallan algunos mensajes de error, sus causas probables y las acciones recomendadas.

|Nº de serie|Código de error HRESULT|Posible causa principal|Acción recomendada|
|:---|:---|:---|:---|
|1\.|0x80070001|El comando se ejecuta desde el controlador pasivo y no puede comunicarse con el controlador activo.|Ejecute el comando en el controlador activo. Para ejecutar el comando desde el controlador pasivo, deberá arreglar la conectividad del controlador pasivo al activo. Si la conectividad está rota, deberá ponerse en contacto con el soporte técnico de Microsoft.|
|2\.|0x800710dd - El identificador de la operación no es válido|El dispositivo virtual StorSimple no admite la configuración de proxy.|El dispositivo virtual StorSimple no admite la configuración de proxy. La misma solo puede establecerse en un dispositivo StorSimple físico.|
|3\.|0x80070057 - Parámetro no válido|Uno de los parámetros proporcionados para la configuración del proxy no es válido.|El URI no se proporciona con el formato correcto. Utilice el siguiente formato: `http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4\.|0x800706ba - Servidor RPC no disponible|La causa principal es una de las siguientes:</br></br>El clúster está inactivo.</br></br>El servicio de ruta de datos no está en ejecución.</br></br>El comando se ejecuta desde el controlador pasivo y no puede comunicarse con el controlador activo.|Póngase en contacto con el soporte técnico de Microsoft para asegurarse de que el clúster está activo y el servicio de ruta de datos está ejecutándose.</br></br>Ejecute el comando desde el controlador activo. Si desea ejecutar el comando desde el controlador pasivo, deberá asegurarse de que el controlador pasivo puede comunicarse con el controlador activo. Si la conectividad está rota, deberá ponerse en contacto con el soporte técnico de Microsoft.|
|5\.|0X800706be - Llamada RPC con errores|El clúster está inactivo.|Póngase en contacto con el soporte técnico de Microsoft para asegurarse de que el clúster está activo.|
|6\.|0x8007138f - Recurso de clúster no encontrado|El recurso de clúster del servicio de plataforma no se encuentra. Esto puede ocurrir cuando la instalación no se ha realizado correctamente.|Puede que deba restablecer su dispositivo a los valores de fábrica. Es posible que deba crear un recurso de plataforma. Póngase en contacto con el soporte técnico de Microsoft para conocer los siguientes pasos.|
|7\.|0x8007138c - El recurso de clúster no está en línea|Los recursos de clúster de ruta de datos o plataforma no están en línea.|Póngase en contacto con el soporte técnico de Microsoft para asegurarse de que la ruta de datos y el recurso del servicio de plataforma están en línea.|

> [AZURE.NOTE] 
> 
> -  La lista anterior de mensajes de error no es exhaustiva. 
> - Los errores relacionados con la configuración de proxy web no se muestran en el Portal de Azure clásico del servicio del Administrador de StorSimple. Si hay un problema con el proxy web una vez completada la configuración, el estado del dispositivo cambiará a **Sin conexión** en el Portal clásico.

## Pasos siguientes

- Si experimenta problemas durante la implementación del dispositivo o la configuración de los valores de proxy web, vea [Solución de problemas de implementación del dispositivo StorSimple](storsimple-troubleshoot-deployment.md).

- Para obtener información sobre cómo usar el servicio StorSimple Manager, vaya a [Uso del servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0224_2016-->