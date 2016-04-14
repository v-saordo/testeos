<properties
    pageTitle="Uso del redireccionamiento en Azure RemoteApp | Microsoft Azure"
    description="Obtenga información acerca de cómo configurar y usar el redireccionamiento en RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/21/2016"
    ms.author="elizapo" />

# Uso del redireccionamiento de RemoteApp de Azure

Redireccionamiento de dispositivos permite a los usuarios interactuar con aplicaciones remotas mediante los dispositivos conectados a su equipo local, un teléfono o una tableta. Por ejemplo, si proporciona Skype a través de Azure RemoteApp, el usuario tendrá que disponer de la cámara instalada en su equipo para que funcione con Skype. Esto también se aplica a las impresoras, altavoces, monitores y a una amplia gama de periféricos conectados mediante USB.

RemoteApp aprovecha el Protocolo de escritorio remoto (RDP) y RemoteFX para proporcionar la redireccionamiento.

## ¿Qué redireccionamiento está habilitado de manera predeterminada?
Al usar RemoteApp, los redireccionamientos siguientes se habilitan de forma predeterminada. La información entre paréntesis muestra la configuración de RDP.

- Reproducir sonidos en el equipo local (**reproducir en este equipo**). (audiomode:i:0)
- Capturar audio desde el equipo local y enviar al equipo remoto (**Registro desde este equipo**). (audiocapturemode:i:1)
- Imprimir en impresoras locales (redirectprinters:i:1)
- Puertos COM (redirectcomports:i:1)
- Dispositivo de tarjeta inteligente (redirectsmartcards:i:1)
- Portapapeles (capacidad de copiar y pegar) (redirectclipboard:i:1)
- Desactivar el suavizado de fuentes (permitir suavizado de fuentes: i:1)
- Redirigir todos los dispositivos Plug and Play compatibles. (devicestoredirect:s: *)

## ¿Qué otro redireccionamiento está disponible?
Existen dos opciones de redireccionamiento deshabilitadas de forma predeterminada:

- Redireccionamiento de unidad (asignación de unidades): las unidades del equipo local se convierten en las unidades asignadas en la sesión remota. Esto permite guardar o abrir los archivos de las unidades locales mientras trabaja en la sesión remota.
- Redireccionamiento de USB: puede usar los dispositivos USB conectados al equipo local en la sesión remota.

## Cambiar la configuración de redireccionamiento de RemoteApp
Puede cambiar la configuración de redireccionamiento de dispositivos para una colección mediante el uso de Microsoft Azure PowerShell con el SDK. Después de instalar el nuevo SDK y PowerShell, primero configúrelo para administrar la suscripción como se describe en [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

A continuación, utilice un comando similar al siguiente para establecer las propiedades personalizadas de RDP:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Tenga en cuenta que *' n* se utiliza como delimitador entre las propiedades individuales).

Para obtener una lista de las propiedades personalizadas de RDP que se configuran, ejecute el siguiente cmdlet. Tenga en cuenta que solo se muestran las propiedades personalizadas como resultados de salida y no las propiedades predeterminadas:

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Al establecer las propiedades personalizadas debe especificar todas las propiedades personalizadas cada vez; de lo contrario, la configuración se volverá a deshabilitar.

### Ejemplos comunes
Use el siguiente cmdlet para habilitar el redireccionamiento de la unidad:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Use este cmdlet para habilitar el redireccionamiento USB y de la unidad:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Use este cmdlet para deshabilitar el uso compartido del portapapeles:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] Asegúrese de cerrar completamente la sesión de todos los usuarios de la colección (y no solo de desconectarlos) antes de probar el cambio. Para asegurarse de que los usuarios han cerrado sesión completamente, vaya a la pestaña **Sesiones** de la colección del portal de Azure y cierre la sesión de todos los usuarios que estén desconectados o que hayan iniciado sesión. A veces es posible que las unidades locales tarden varios segundos en mostrarse en el explorador dentro de la sesión.

## Cambie la configuración de redireccionamiento de USB en el cliente Windows

Si desea usar el redireccionamiento de USB en un equipo que se conecta a RemoteApp, hay 2 acciones que se deben realizar. 1.- El administrador debe habilitar el redireccionamiento USB en el nivel de colección mediante Azure PowerShell. 2 - En cada dispositivo en el que desee usar el redireccionamiento USB, deberá habilitar una directiva de grupo que lo permita. Este paso debe realizarse para cada usuario que desee usar el redireccionamiento USB.

> [AZURE.NOTE] El redireccionamiento USB con RemoteApp de Azure solo se admite en equipos Windows.

### Habilitar el redireccionamiento USB para la colección RemoteApp
Utilice el siguiente cmdlet para habilitar la redirección de USB en el nivel de colección:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### Habilitar el redireccionamiento USB para el equipo cliente

Para configurar el redireccionamiento USB en su equipo:

1. Abra el Editor de directivas de grupo local (GPEDIT. MSC). (Ejecute gpedit.msc desde un símbolo del sistema de comando).
2. Abra **Configuración del equipo\\Directivas\\Plantillas administrativas\\Componentes Windows\\Servicios de escritorio remoto\\Cliente de conexión de escritorio remoto\\Redireccionamiento de dispositivo USB RemoteFX**.
3. Haga doble clic en **Permitir el redireccionamiento RDP de otros dispositivos USB RemoteFX compatibles desde este equipo**.
4. Seleccione **Habilitado**, y, a continuación, seleccione **Administradores y usuarios en los derechos de acceso de redireccionamiento de RemoteFX USB**.
5. Abra un símbolo del sistema de comandos con permisos administrativos y ejecute el siguiente comando:

		gpupdate /force
6. Reinicie el equipo.

También puede utilizar la herramienta de administración de directivas de grupo para crear y aplicar la directiva de redireccionamiento USB para todos los equipos del dominio:

1. Inicie sesión en el controlador de dominio como administrador de dominio.
2. Abra la consola de administración de directivas de grupo. (Haga clic en **Inicio > Herramientas administrativas > Administración de directivas de grupo**).
3. Navegue hasta el dominio o unidad organizativa para la que desea crear la directiva.
4. Haga clic con el botón derecho en **Directiva de dominio predeterminada** y, a continuación, haga clic en **Editar**.
5. Abra **Configuración del equipo\\Directivas\\Plantillas administrativas\\Componentes Windows\\Servicios de escritorio remoto\\Cliente de conexión de escritorio remoto\\Redireccionamiento de dispositivo USB RemoteFX**.
6. Haga doble clic en **Permitir el redireccionamiento RDP de otros dispositivos USB RemoteFX compatibles desde este equipo**.
7. Seleccione **Habilitado** y, a continuación, seleccione **Administradores y usuarios en los derechos de acceso de redireccionamiento USB RemoteFX**.
8. Haga clic en **Aceptar**.  

<!---HONumber=AcomDC_0128_2016-->