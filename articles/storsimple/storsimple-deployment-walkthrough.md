<properties
   pageTitle="Implementación de un dispositivo StorSimple local | Microsoft Azure"
   description="Describe los pasos y procedimientos recomendados para implementar el servicio y el dispositivo StorSimple. (Se aplica a la versión 3 y anteriores de Microsoft Azure StorSimple)."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/22/2016"
   ms.author="alkohli" />

# Implementar el dispositivo StorSimple local

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Update 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA Release](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Información general

Bienvenido a la implementación del dispositivo StorSimple de Microsoft Azure. Estos tutoriales de implementación se aplican a la versión de lanzamiento de la serie StorSimple 8000, actualización 0.1 de la serie StorSimple 8000, actualización 0.2 de la serie StorSimple 8000 y actualización 0.3 de la serie StorSimple 8000. En esta serie de tutoriales se describe cómo configurar el dispositivo StorSimple, y se incluye una lista de comprobación de configuración, los requisitos previos de configuración y los pasos de configuración detallados.


La información de estos tutoriales da por supuesto que revisó las precauciones de seguridad, y que desempaquetó, montó y cableó el dispositivo StorSimple. Si todavía necesita realizar dichas tareas, empiece por revisar las [precauciones de seguridad](storsimple-safety.md). Según el modelo de dispositivo, puede desempaquetarlo, montarlo en bastidor y cablearlo siguiendo las instrucciones que se describen en:

- [Desempaquetado, montaje en bastidor y cableado del 8100](storsimple-8100-hardware-installation.md)
- [Desempaquetado, montaje en bastidor y cableado del 8600](storsimple-8600-hardware-installation.md)

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Se recomienda que revise la lista de comprobación de configuración antes de comenzar. El proceso de implementación y configuración puede tardar algún tiempo en completarse.

> [AZURE.NOTE] La información de implementación de StorSimple publicada en el sitio web de Microsoft Azure se aplica solo a los dispositivos StorSimple de la serie 8000. Para obtener información completa sobre los dispositivos de la serie 7000, vaya a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obtener información sobre la implementación de la serie 7000, vea la [Guía de inicio rápido del sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

## Pasos de implementación

Siga estos pasos obligatorios para configurar el dispositivo StorSimple y conectarlo al servicio StorSimple Manager: Además de los pasos obligatorios, hay pasos y procedimientos opcionales que puede que necesite llevar a cabo durante la implementación. Las instrucciones detalladas de implementación indican cuándo debe realizar cada uno de estos pasos opcionales.


| Paso | Descripción |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **REQUISITOS PREVIOS** | Se deben llevar a cabo como preparación para la próxima implementación. |
| Lista de comprobación de la configuración de implementación. | Use esta lista de comprobación para recopilar y registrar información antes y durante la implementación. |
| Requisitos previos de implementación. | Validan que el entorno está listo para la implementación. |
| | |
| **IMPLEMENTACIÓN PASO A PASO** | Estos pasos son obligatorios para implementar el dispositivo StorSimple en producción. |
| Paso 1: Crear un nuevo servicio. | Configure la administración y el almacenamiento en la nube para el dispositivo StorSimple. Omita este paso si tiene un servicio existente para otros dispositivos StorSimple. |
| Paso 2: Obtener la clave de registro del servicio. | Esta clave se usa para registrar y conectar el dispositivo StorSimple con el servicio de administración. |
| Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple. | Conecte el dispositivo a la red y regístrelo con Azure para completar la instalación mediante el servicio de administración. |
| Paso 4: Completar el programa de instalación mínima del dispositivo </br>Opcional: actualización del dispositivo StorSimple. | Use el servicio de administración para realizar la instalación del dispositivo y habilitarlo para proporcionar almacenamiento. |
| Paso 5: Crear un contenedor de volúmenes. | Cree un contenedor para aprovisionar los volúmenes. Un contenedor de volúmenes tiene la configuración de la cuenta de almacenamiento, el ancho de banda y el cifrado de todos los volúmenes que contiene. |
| Paso 6: Crear un volumen. | Aprovisione volúmenes de almacenamiento en el dispositivo StorSimple para los servidores. |
| Paso 7: Montar, inicializar y formatear un volumen.</br>Opcional: configurar MPIO. | Conecte los servidores al almacenamiento iSCSI proporcionado por el dispositivo. De forma opcional, puede configurar MPIO para asegurarse de que los servidores pueden tolerar errores de vínculo, red e interfaz. |
| Paso 8: Realizar una copia de seguridad. | Configure la directiva de copia de seguridad para proteger los datos. |
| | |
| **OTROS PROCEDIMIENTOS** | Puede que necesite hacer referencia a estos procedimientos mientras implementa la solución. |
| Configurar una nueva cuenta de almacenamiento para el servicio. | |
| Usar PuTTY para conectarse a la consola serie del dispositivo. | |
| Obtener el IQN de un host de Windows Server. | |
| Crear una copia de seguridad manual. |


## Lista de comprobación de la configuración de implementación

La siguiente lista de comprobación de la configuración de implementación describe la información que necesita recopilar antes de configurar el software en el dispositivo StorSimple. Si prepara alguna de esta información con antelación, simplificará el proceso de implementar el dispositivo StorSimple en el entorno. Use esta lista de comprobación para anotar también los detalles de configuración cuando implemente el dispositivo.

| Fase | Parámetro | Detalles | Valores |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Cablear el dispositivo** | Acceso serie | Configuración inicial del dispositivo | Sí/No |
| | | | |
| **Configurar y registrar el dispositivo** | Configuración de red de Data 0 | Dirección IP de Data 0:</br>Máscara de subred:</br>Puerta de enlace:</br>Servidor DNS principal:</br>Servidor NTP principal:</br>IP/FQDN de servidor proxy web (opcional):</br>Puerto de proxy web:| |
| | Contraseña de administrador de dispositivo | La contraseña debe contener entre 8 y 15 caracteres en minúscula, mayúscula, numéricos y especiales. | |
| | Contraseña para StorSimple Snapshot Manager | La contraseña debe contener entre 14 o 15 caracteres en minúscula, mayúscula, numéricos y especiales.| |
| | Clave de registro del servicio | Esta clave se genera desde el Portal de Azure clásico. | |
| | Clave de cifrado de datos de servicio | Esta clave se crea cuando el dispositivo se registra con el servicio de administración a través de Windows PowerShell para StorSimple. Copie esta clave y guárdela en un lugar seguro.| |
| | | | |
| **Completar la instalación mínima del dispositivo** | Nombre descriptivo para el dispositivo | Nombre que ayuda a identificar el dispositivo. | |
| | Zona horaria | El dispositivo usará esta zona horaria para todas las operaciones programadas. | |
| | Servidor DNS secundario | Es una configuración obligatoria. | |
| | Interfaz de red: IP fijas del controlador Data 0. | Estas IP deben ser enrutables a Internet.</br>Dirección IP fija del controlador 0:</br>Dirección IP fija del controlador 1:|
| | | | |
| **Configuración adicional de la interfaz de red** | Interfaz de red: Data 1</br>Si iSCSI está habilitado, no configure la puerta de enlace. | Propósito: nube/iSCSI/no se usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
| | Interfaz de red: Data 2</br>Si iSCSI está habilitado, no configure la puerta de enlace. | Propósito: nube/iSCSI/no se usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
| | Interfaz de red: Data 3</br>Si iSCSI está habilitado, no configure la puerta de enlace. | Propósito: nube/iSCSI/no se usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
| | Interfaz de red: Data 4</br>Si iSCSI está habilitado, no configure la puerta de enlace. | Propósito: nube/iSCSI/no se usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
| | Interfaz de red: Data 5</br>Si iSCSI está habilitado, no configure la puerta de enlace. | Propósito: nube/iSCSI/no se usa</br>Dirección IP:</br>Máscara de subred:</br>Puerta de enlace:|
| | | | |
| **Crear un contenedor de volúmenes** | Nombre del contenedor de volúmenes: | Nombre del contenedor. | |
| | Cuenta de almacenamiento de Azure: | Nombre de la cuenta de almacenamiento y clave de acceso que se asociarán con este contenedor de volúmenes. | |
| | Clave de cifrado de almacenamiento en la nube: | Clave de cifrado para el almacenamiento en cada contenedor. | |
| | | | |
| **Crear un volumen** | Detalles de cada volumen | Nombre del volumen: | |
| | | Tamaño | |
| | | Tipo de uso: | |
| | | Nombre ACR: | |
| | | Directiva de copia de seguridad predeterminada: | |
| | | | |
| **Montar, inicializar y formatear un volumen** | Detalles de cada servidor host que se conecta al almacenamiento | Nombre de Windows Server: | |
| | | IQN de Windows Server: | |
| | | Nombre de volumen de Windows Server: | |
| | | Letra de unidad o punto de montaje de NTFS: | |

## Requisitos previos de implementación

En las siguientes secciones se explican los requisitos previos de configuración del servicio StorSimple Manager, el dispositivo StorSimple y la red del centro de datos.

### Para el servicio de Administrador de StorSimple

Antes de comenzar, asegúrese de que:

- Tiene una cuenta Microsoft con credenciales de acceso.

- Tiene una cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

- Su suscripción de Microsoft Azure está habilitada para el servicio de Administrador de StorSimple. Debe adquirir la suscripción a través del [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Tiene acceso a software de emulación de terminales, como PuTTY.

### Para el dispositivo en el centro de datos

Antes de configurar el dispositivo, asegúrese de que:

- El dispositivo viene completamente desempaquetado, montado en un bastidor y totalmente cableado para alimentación, red y acceso serie, tal como se describe en:

	-  [Desempaquetado, montaje en bastidor y cableado del dispositivo 8100](storsimple-8100-hardware-installation.md)
	-  [Desempaquetado, montaje en bastidor y cableado del dispositivo 8600](storsimple-8600-hardware-installation.md)


### Para la red del centro de datos

Antes de comenzar, asegúrese de que:

- Se abren los puertos del firewall del centro de datos para permitir el tráfico de iSCSI y de la nube, como se describe en [Requisitos de red para el dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- El dispositivo en su centro de datos puede conectarse a la red externa. Ejecute los siguientes cmdlets de [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) (que se muestran a continuación en una tabla) para validar la conectividad a la red externa. Realice esta validación en un equipo (en la red del centro de datos) que tenga conectividad a Azure y donde vaya a implementar el dispositivo StorSimple.  

| Para este parámetro... | Para comprobar la validez... | Ejecute estos comandos o cmdlets. |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP**</br>**Subred**</br>**Puerta de enlace** | ¿Es una dirección IPv4 o IPv6 válida?</br>¿Es una subred válida?</br>¿Es una puerta de enlace válida?</br>¿Es una dirección IP duplicada en la red? | `ping ip`</br>`arp -a`</br>Los comandos `ping` y `arp` no deberían dar error al indicar que no hay ningún dispositivo en la red del centro de datos que este usando esta dirección IP.
| | | |
| **DNS** | ¿Es una DNS válida y puede resolver las direcciones URL de Azure? | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>`</br>Un comando alternativo que se puede usar es:</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com` |
| | Compruebe si el puerto 53 está abierto. Esto solo es aplicable si está usando una DNS externa para el dispositivo. El DNS interno debe resolver automáticamente las direcciones URL externas. | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000` </br>[Más información sobre este cmdlet](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/)|
| | | |
| **NTP** | Desencadenamos una sincronización de tiempo tan pronto como se introduce el servidor NTP. Compruebe que el puerto UDP 123 está abierto cuando introduce `time.windows.com` o servidores de hora públicos). | [Descargue y use este script](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca). |
| | | |
| **Proxy (opcional)** | ¿Es un URI y un puerto válidos para el proxy? </br>¿Es correcto el modo de autenticación? | <code>wget http://bing.com | % {$\_.StatusCode}</code></br>Este comando debe ejecutarse inmediatamente después de configurar el proxy web. Si se devuelve el código e estado 200, indica que la conexión es correcta. |
| | ¿Se puede enrutar el tráfico a través del proxy? | Ejecute las validación DNS y la comprobación NTP o HTTP después de configurar el proxy en el dispositivo. Esto le dará una idea clara de si el tráfico se está bloqueando en el proxy o en otro lugar. |
| | | |
| **Registro** | Compruebe si los puertos TCP de salida 443, 80, 9354 están abiertos. | `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Más información sobre el cmdlet Test-NetConnection](https://technet.microsoft.com/library/dn372891.aspx). |

## Implementación paso a paso

Use las siguientes instrucciones paso a paso para implementar el dispositivo StorSimple en el centro de datos.

## Paso 1: Crear un nuevo servicio

El servicio de Administrador de StorSimple puede administrar varios dispositivos de StorSimple. Para la implementación de su primer dispositivo de StorSimple, deberá crear un nuevo servicio StorSimple Manager.

> [AZURE.IMPORTANT] Omita este paso si tiene un servicio StorSimple Manager existente y desea implementar su dispositivo StorSimple con ese servicio.

Siga estos pasos para crear una nueva instancia del servicio de Administrador de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de crear correctamente un servicio. Dicha cuenta de almacenamiento se usará al crear un contenedor de volúmenes.
>
> Si no creó automáticamente una cuenta de almacenamiento, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas.
> Si habilitó la creación automática de una cuenta de almacenamiento, vaya al [Paso 2: Obtener la clave de registro del servicio](#step-2:-get-the-service-registration-key).

## Paso 2: Obtener la clave de registro del servicio

Una vez esté en funcionamiento el servicio de Administrador de StorSimple, necesitará la clave de registro del servicio. Esta clave se usa para registrar y conectar el dispositivo StorSimple con el servicio.

Siga estos pasos en el Portal de Azure clásico.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple

> [AZURE.IMPORTANT] Antes de realizar esta configuración, desconecte todas las interfaces de red que no sean de DATA 0 en ambos controladores (activo y pasivo).

Use Windows PowerShell para StorSimple para completar la configuración inicial del dispositivo StorSimple, tal como se explica en el procedimiento siguiente. Deberá usar software de emulación de terminales para completar este paso. Para obtener más información, consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## Paso 4: Completar la instalación mínima del dispositivo

Para proceder a la configuración mínima del dispositivo StorSimple, debe hacer lo siguiente:

- Configurar el servidor DNS secundario.
- Habilitar iSCSI en al menos una interfaz de red.
- Asignar direcciones IP fijas a ambos controladores.

Siga estos pasos en el Portal de Azure clásico para completar la configuración mínima del dispositivo.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Una vez completada la configuración del dispositivo, debe buscar actualizaciones e instalarlas en caso de que estén disponibles. Las actualizaciones pueden tardar varias horas en completarse. Siga las instrucciones que se indican en [Búsqueda y aplicación de actualizaciones](#scan-for-and-apply-updates).


## Paso 5: Crear un contenedor de volúmenes

Un contenedor de volúmenes tiene la configuración de la cuenta de almacenamiento, el ancho de banda y el cifrado de todos los volúmenes que contiene. Deberá crear un contenedor de volúmenes para poder empezar a aprovisionar volúmenes en el dispositivo StorSimple.

Siga estos pasos en el Portal de Azure clásico para crear un contenedor de volúmenes.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Paso 6: Crear un volumen

Después de crear un contenedor de volúmenes, puede aprovisionar un volumen de almacenamiento en el dispositivo StorSimple para los servidores. Siga estos pasos en el Portal de Azure clásico para crear un volumen.

> [AZURE.IMPORTANT] StorSimple Manager solo puede crear volúmenes con aprovisionamiento fino. No se pueden crear volúmenes aprovisionados total o parcialmente.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Paso 7: Montar, inicializar y formatear un volumen

> [AZURE.IMPORTANT]

> - Para obtener la alta disponibilidad de la solución de StorSimple, se recomienda que configure MPIO en el host de Windows Server (opcional) antes de configurar iSCSI en el host de Windows Server. La configuración MPIO en los servidores host garantizará que los servidores pueden tolerar errores de vínculos, redes o interfaces.

> - Para obtener instrucciones de instalación y configuración de MPIO y iSCSI, vaya a [Configuración de MPIO para el dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). También se incluyen los pasos para montar, inicializar y formatear volúmenes StorSimple.

Si decide no configurar MPIO, realice los pasos siguientes para montar, inicializar y formatear los volúmenes StorSimple.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Paso 8: Realizar una copia de seguridad

Las copias de seguridad proporcionan seguridad para los volúmenes a partir de un momento específico y mejoran la capacidad de recuperación al mismo tiempo que reducen los tiempos de restauración. Puede realizar dos tipos de copia de seguridad en el dispositivo StorSimple: instantáneas locales e instantáneas en la nube. Cada uno de estos tipos de copia de seguridad puede ser **Programada** o **Manual**.

Siga estos pasos en el Portal de Azure clásico para crear una copia de seguridad programada.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Puede realizar una copia de seguridad manual en cualquier momento. Para conocer los procedimientos, vaya a [Crear una copia de seguridad manual](#Create-a-manual-backup).

## Configurar una nueva cuenta de almacenamiento para el servicio

Se trata de un paso opcional que debe llevar a cabo únicamente si no habilitó la creación automática de una cuenta de almacenamiento con su servicio. Se requiere una cuenta de almacenamiento de Microsoft Azure para crear un contenedor de volúmenes de StorSimple.

Si necesita crear una cuenta de almacenamiento de Azure en una región distinta, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md) para obtener instrucciones paso a paso.

Siga estos pasos en el Portal de Azure clásico, en la página **Servicio Administrador de StorSimple**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## Usar PuTTY para conectarse a la consola serie del dispositivo

Para conectarse a Windows PowerShell para StorSimple, deberá usar software de emulación de terminales, como PuTTY. Puede usar PuTTY cuando acceda al dispositivo directamente a través de la consola serie o abriendo una sesión de Telnet desde un equipo remoto.

[AZURE.INCLUDE [Usar PuTTY para conectarse a la consola serie del dispositivo](../../includes/storsimple-use-putty.md)]

## Búsqueda y aplicación de actualizaciones

La actualización del dispositivo puede tardar entre 1 y 4 horas. Realice los pasos siguientes para detectar y aplicar las actualizaciones en el dispositivo.

> [AZURE.NOTE] Si tiene una puerta de enlace configurada en una interfaz de red que no sea Data 0, deberá deshabilitar las interfaces de red Data 2 y Data 3 antes de instalar la actualización. Vaya a **Dispositivos > Configurar** y deshabilite las interfaces Data 2 y Data 3. Deberá volver a habilitar estas interfaces después de actualiza el dispositivo.

#### Para actualizar su dispositivo
1.	En la página **Inicio rápido** del dispositivo, haga clic en **Dispositivos**. Seleccione el dispositivo físico, haga clic en **Mantenimiento** y luego en **Buscar actualizaciones**.  
2.	Se crea un trabajo para buscar las actualizaciones disponibles. Si hay actualizaciones disponibles, la opción **Buscar actualizaciones** cambia a **Instalar actualizaciones**. Haga clic en **Instalar actualizaciones**. Puede que se le pida que deshabilite Data 2 y Data 3 antes de instalar las actualizaciones. Debe deshabilitar estas interfaces de red o las actualizaciones podrían dar error.
3.	Se creará un trabajo de actualización. Vaya a **Trabajos** para supervisar el estado de la actualización.

	> [AZURE.NOTE] Cuando se inicia el trabajo de actualización, se muestra inmediatamente el estado como 50 por ciento. Luego, el estado cambia al 100 por cien, una vez completado el trabajo de actualización. No hay ningún estado en tiempo real para el proceso de actualizaciones.

4.	Después de que el dispositivo se actualiza correctamente, habilite las interfaces de red Data 2 y Data 3 si estaban deshabilitadas.



## Obtener el IQN de un host de Windows Server

Siga estos pasos para obtener el nombre completo del iSCSI (IQN) de un host de Windows que ejecute Windows Server 2012.

[AZURE.INCLUDE [Crear una copia de seguridad manual](../../includes/storsimple-get-iqn.md)]


## Crear una copia de seguridad manual

Siga estos pasos en el Portal de Azure clásico para crear una copia de seguridad manual a petición para un único volumen en el dispositivo StorSimple.

[AZURE.INCLUDE [Crear una copia de seguridad manual](../../includes/storsimple-create-manual-backup.md)]


## Pasos siguientes

- Configure un [dispositivo virtual](storsimple-virtual-device.md).

- Use el [servicio de Administrador de StorSimple](https://msdn.microsoft.com/library/azure/dn772396.aspx) para administrar el dispositivo StorSimple.

<!---HONumber=AcomDC_0224_2016-->