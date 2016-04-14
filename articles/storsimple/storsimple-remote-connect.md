<properties 
   pageTitle="Conectarse de forma remota al dispositivo StorSimple | Microsoft Azure"
   description="Explica cómo configurar el dispositivo para la administración remota y, a continuación, cómo conectarse a Windows PowerShell para StorSimple a través de HTTP o HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/08/2016"
   ms.author="alkohli" />

# Conectarse de forma remota al dispositivo StorSimple

## Información general

Puede usar la conexión remota de Windows PowerShell para conectarse al dispositivo StorSimple. Cuando se conecta de este modo, no verá un menú. (Verá un menú solo si utiliza la consola en serie del dispositivo para conectarse). Con la conexión remota de Windows PowerShell, se conecta a un espacio de ejecución específico. También puede especificar el idioma de visualización.

Para obtener más información acerca del uso de la conexión remota de Windows PowerShell para administrar el dispositivo, vaya a [Uso de Windows PowerShell para StorSimple para administrar su dispositivo StorSimple](storsimple-windows-powershell-administration.md).

Este tutorial explica cómo configurar el dispositivo para la administración remota y, a continuación, cómo conectarse a Windows PowerShell para StorSimple. Puede utilizar HTTP o HTTPS para conectarse a través de la conexión remota de Windows PowerShell. Sin embargo, cuando decide cómo conectarse a Windows PowerShell para StorSimple, tenga en cuenta lo siguiente:

- Es seguro conectarse directamente a la consola en serie del dispositivo, pero la conexión a la consola en serie a través conmutadores de red no lo es. Tenga presente los riesgos de seguridad al conectarse a la consola en serie del dispositivo a través de los conmutadores de red. 

- Conectarse a través de una sesión HTTP puede ofrecer más seguridad que la conexión a través de la consola en serie a través de la red. Aunque este no es el método más seguro, es aceptable en redes de confianza.

- La conexión a través de una sesión HTTPS con un certificado autofirmado es la opción más segura y la recomendada.

Puede conectarse de forma remota a la interfaz de Windows PowerShell. No obstante, el acceso remoto a su dispositivo StorSimple mediante la interfaz de Windows PowerShell no está habilitado de forma predeterminada. Deberá habilitar primero la administración remota en el dispositivo y, a continuación, habilitarlo en el cliente que se utilizará para acceder al dispositivo.

Los pasos descritos en este artículo se realizaron en un sistema host con Windows Server 2012 R2.

## Conectarse a través de HTTP

Conectarse a Windows PowerShell para StorSimple a través de una sesión HTTP proporciona una mayor seguridad que la conexión a través de la consola en serie del dispositivo StorSimple. Aunque este no es el método más seguro, es aceptable en redes de confianza.

Puede usar el Portal de Azure clásico o la consola en serie para configurar la administración remota. Seleccione entre los siguientes procedimientos:

- [Uso del Portal de Azure clásico para habilitar la administración remota a través de HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Utilice la consola en serie para habilitar la administración remota a través de HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Después de habilitar la administración remota, utilice el procedimiento siguiente para preparar al cliente para una conexión remota.

- [Preparar al cliente para la conexión remota](#prepare-the-client-for-remote-connection)

### Uso del Portal de Azure clásico para habilitar la administración remota a través de HTTP 

Siga estos pasos en el Portal de Azure clásico para habilitar la administración remota a través de HTTP.

#### Para habilitar la administración remota a través del Portal de Azure clásico

1. En el dispositivo, acceda a **Dispositivos** > **Configurar**.

2. Desplácese hacia abajo a la sección **Administración remota**.

3. Establezca **Habilitar administración remota** en **Sí**.

4. Ahora puede elegir conectarse con HTTP. (La configuración predeterminada es conectarse a través de HTTPS). Asegúrese de que se selecciona HTTP.

    >[AZURE.NOTE] La conexión a través de HTTP solo es aceptable en redes de confianza.

6. Haga clic en **Guardar** en la parte inferior de la página.

### Utilice la consola en serie para habilitar la administración remota a través de HTTP

Realice los pasos siguientes en la consola en serie del dispositivo para habilitar la administración remota.

#### Para habilitar la administración remota a través de la consola en serie del dispositivo

1. En el menú de la consola serie, seleccione la opción 1. Para obtener más información acerca del uso de la consola en serie en el dispositivo, vaya a [Conectarse a Windows PowerShell para StorSimple a través de la consola en serie del dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. En el símbolo del sistema, escriba: `Enable-HcsRemoteManagement –AllowHttp`

3. Se le notificará acerca de las vulnerabilidades de seguridad del uso de HTTP para conectarse al dispositivo. Cuando se le solicite, confirme escribiendo **S**.

4. Compruebe que está habilitado HTTP escribiendo: `Get-HcsSystem`

5. Compruebe que el campo **RemoteManagementMode** muestre **HttpsAndHttpEnabled**. La ilustración siguiente muestra esta configuración en PuTTY.

     ![Serie HTTPS y HTTP habilitada](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### Preparar al cliente para la conexión remota

Realice los pasos siguientes en el cliente para habilitar la administración remota.

#### Para preparar al cliente para la conexión remota

1. Iniciar una sesión de Windows PowerShell como administrador.

2. Escriba el siguiente comando para agregar la dirección IP del dispositivo StorSimple a la lista de hosts de confianza del cliente:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Reemplace <*device\_ip*> por la dirección IP del dispositivo; por ejemplo:

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Escriba el siguiente comando para guardar las credenciales del dispositivo en una variable:

     *$cred = Get-Credential*

4. En el cuadro de diálogo que aparece:

    1. Escriba el nombre de usuario en este formato: *device\_ip\\SSAdmin*.
    2. Escriba la contraseña del administrador de dispositivos que se estableció cuando configuró el dispositivo con el asistente para la instalación. La contraseña predeterminada es *Password1*.

7. Inicie una sesión de Windows PowerShell en el dispositivo mediante este comando:

     `Enter-pssession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Para crear una sesión de Windows PowerShell para su uso con el dispositivo virtual StorSimple, anexe el parámetro `–port` y especifique el puerto público que configuró en Conexión remota para dispositivo virtual StorSimple.

     En este momento, debe tener una sesión remota activa de Windows PowerShell en el dispositivo.

    ![Conexión remota de PowerShell mediante HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## Conectarse a través de HTTPS

Conectarse a Windows PowerShell para StorSimple a través de una sesión HTTPS es el método más seguro y recomendado de conexión remota a su dispositivo StorSimple de Microsoft Azure. Los procedimientos siguientes explican cómo configurar los equipos cliente y consola en serie de modo que pueda usar HTTPS para conectarse a Windows PowerShell para StorSimple.

Puede usar el Portal de Azure clásico o la consola en serie para configurar la administración remota. Seleccione entre los siguientes procedimientos:

- [Uso del Portal de Azure clásico para habilitar la administración remota a través de HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Utilice la consola en serie para habilitar la administración remota a través de HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Después de habilitar la administración remota, utilice los procedimientos siguientes para preparar el host para la administración remota y conectarse con el dispositivo desde el host remoto.

- [Preparar el host para la administración remota](#prepare-the-host-for-remote-management)

- [Conectarse al dispositivo desde el host remoto](#connect-to-the-device-from-the-remote-host)

### Uso del Portal de Azure clásico para habilitar la administración remota a través de HTTPS

Siga estos pasos en el Portal de Azure clásico para habilitar la administración remota a través de HTTPS.

#### Para habilitar la administración remota a través de HTTPS desde el Portal de Azure clásico

1. Acceda a **Dispositivos** > **Configurar** para el dispositivo.

2. Desplácese hacia abajo a la sección **Administración remota**.

3. Establezca **Habilitar administración remota** en **Sí**.

4. Ahora puede elegir conectarse mediante HTTPS. (La configuración predeterminada es conectarse a través de HTTPS). Asegúrese de que se ha seleccionado HTTPS.

5. Haga clic en **Descargar certificado de administración remota**. Especifique una ubicación para guardar este archivo. Deberá instalar este certificado en el equipo cliente o host que usará para conectarse al dispositivo.

6. Haga clic en **Guardar** en la parte inferior de la página.

### Utilice la consola en serie para habilitar la administración remota a través de HTTPS

Realice los pasos siguientes en la consola en serie del dispositivo para habilitar la administración remota.

#### Para habilitar la administración remota a través de la consola en serie del dispositivo

1. En el menú de la consola serie, seleccione la opción 1. Para obtener más información acerca del uso de la consola en serie del dispositivo, vaya a [Conectarse a Windows PowerShell para StorSimple a través de la consola en serie del dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. En el símbolo del sistema, escriba:

     `Enable-HcsRemoteManagement`

    Esto debe habilitar HTTPS en el dispositivo.

3. Compruebe que se ha habilitado HTTPS escribiendo:

     `Get-HcsSystem`

    Asegúrese de que el campo **RemoteManagementMode** muestra**Https habilitado**. La ilustración siguiente muestra esta configuración en PuTTY.

     ![Serie HTTPS habilitada](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. Desde la salida de `Get-HcsSystem`, copie el número de serie del dispositivo y guárdelo para usarlo más adelante.

    >[AZURE.NOTE] El número de serie se asigna al nombre CN en el certificado.

5. Obtenga un certificado de administración remota, escriba:
 
     `Get-HcsRemoteManagementCert`

    Aparecerá un certificado similar al siguiente.

    ![Obtener certificado de administración remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copie la información en el certificado de **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** en un editor de texto como Bloc de notas y guárdelo como un archivo .cer. (Copiará este archivo en el host remoto al preparar el host.)

    >[AZURE.NOTE] Para generar un nuevo certificado, utilice cmdlet `Set-HcsRemoteManagementCert`.

### Preparar el host para la administración remota

Para preparar el equipo host para una conexión remota que utiliza una sesión HTTPS, realice los siguientes procedimientos:

- [Importación del archivo .cer en el almacén raíz del cliente o un host remoto](#to-import-the-certificate-on-the-remote-host).

- [Incorporación de los números de serie del dispositivo al archivo de hosts en el host remoto](#to-add-device-serial-numbers-to-the-remote-host).

A continuación se describe cada uno de estos procedimientos.

#### Para importar el certificado en el host remoto

1. Haga clic con el botón secundario en el archivo .cer y seleccione **Instalar certificado**. Esto iniciará al Asistente para importación de certificados.

    ![Asistente para importación de certificados 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. Para **Ubicación de almacén**, seleccione **equipo local** y, a continuación, haga clic en **Siguiente**.

3. Seleccione **Colocar todos los certificados en el siguiente almacén** y, a continuación, haga clic en **Examinar**. Navegue al almacén raíz del host remoto y, a continuación, haga clic en **Siguiente**.

    ![Asistente para importación de certificados 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Haga clic en **Finalizar** Aparece un mensaje que indica que la importación se ha realizado correctamente.

    ![Asistente para importación de certificados 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### Para agregar números de serie del dispositivo con el host remoto

1. Inicie el Bloc de notas como administrador y, a continuación, abra el archivo de hosts que se encuentra en \\Windows\\System32\\Drivers\\etc.

2. Agregue las siguientes tres entradas al archivo de hosts: **Dirección IP de DATA 0**, **Dirección IP fija del Controlador 0** y **Dirección IP fija del Controlador 1**.

3. Escriba el número de serie del dispositivo que guardó anteriormente. Asigne esto a la dirección IP como se muestra en la siguiente imagen. Para Controlador 0 y 1, anexe **Controller0** y **Controller1**al final del número de serie (nombre CN).

    ![Adición de nombre CN a archivo de hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Guarde el archivo de hosts.

### Conectarse al dispositivo desde el host remoto

Use Windows PowerShell y SSL para iniciar una sesión de SSAdmin en el dispositivo desde un host remoto o cliente. La sesión de SSAdmin se asigna a la opción 1 en el menú de la [consola en serie](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) del dispositivo.

Realice el procedimiento siguiente en el equipo desde el que desea realizar la conexión remota de Windows PowerShell.

#### Para iniciar una sesión de SSAdmin en el dispositivo mediante el uso de Windows PowerShell y SSL

1. Iniciar una sesión de Windows PowerShell como administrador.

2. Agregue la dirección IP del dispositivo a los hosts de confianza del cliente, escriba:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Donde <*device\_ip*> es la dirección IP del dispositivo; por ejemplo:

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Cree una nueva credencial, escriba:

     `$cred = new-object pscredential @("<IP of target device>\SSAdmin", (convertto-securestring -force -asplaintext "<Device Administrator Password>"))`

    Donde <*dirección IP del dispositivo de destino*> es la dirección IP de DATA 0 para el dispositivo; por ejemplo, **10.126.173.90** tal como se muestra en la imagen anterior del archivo de hosts. Además, proporcione la contraseña de administrador para el dispositivo.

4. Cree una sesión, escriba:

     `$session = new-pssession -usessl -CN <Serial number of target device> -credential $cred -configurationname "SSAdminConsole"`

    Proporcione el <*número de serie del dispositivo de destino*> para el nombre CN del cmdlet. Este número de serie se asigna a la dirección IP de DATA 0 en el archivo de hosts del host remoto; Por ejemplo, **SHX0991003G44MT** tal como se muestra en la siguiente imagen.

5. Escriba:

     `Enter-PSSession $session`

6. Deberá esperar unos minutos y, a continuación, se conectará al dispositivo a través de HTTPS con SSL. Verá un mensaje que indica que está conectado al dispositivo.

    ![Conexión remota de PowerShell mediante HTTP y SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## Pasos siguientes

- Obtenga más información acerca del [uso de Windows PowerShell para administrar el dispositivo StorSimple](storsimple-windows-powershell-administration.md).

- Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0211_2016-->