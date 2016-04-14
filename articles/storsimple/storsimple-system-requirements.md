<properties 
   pageTitle="Requisitos del sistema StorSimple | Microsoft Azure" 
   description="Describe los requisitos de software, redes y alta disponibilidad y procedimientos recomendados para una solución Microsoft Azure StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="02/04/2016"
   ms.author="alkohli"/>

# Software de StorSimple, alta disponibilidad y requisitos de red

## Información general

Bienvenido a Microsoft Azure StorSimple. Este artículo describe los requisitos del sistema importantes y las prácticas recomendadas para el dispositivo StorSimple y para los clientes de almacenamiento que acceden al dispositivo. Es recomendable que revise cuidadosamente la siguiente información antes de implementar el sistema StorSimple y que luego la consulte según sea necesario durante la implementación y el funcionamiento posterior.

Los requisitos del sistema incluyen:

- **Requisitos de software para clientes de almacenamiento**: describe los sistemas operativos compatibles y cualquier requisito adicional para esos sistemas operativos.
- **Requisitos de red para el dispositivo StorSimple**: proporciona información acerca de los puertos que deben estar abiertos en el firewall para permitir el tráfico iSCSI, de nube o de administración.
- **Requisitos de alta disponibilidad para StorSimple**: describe los requisitos de alta disponibilidad y las prácticas recomendadas del equipo host y del dispositivo StorSimple. 


## Requisitos de software para los clientes de almacenamiento 

Los siguientes requisitos de software son para los clientes de almacenamiento que tienen acceso a su dispositivo StorSimple.

| Sistemas operativos compatibles | Versión requerida | Requisitos/notas adicionales |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008R2 SP1, 2012, 2012R2 |Se admiten volúmenes iSCSI de StorSimple para utilizarlos exclusivamente en los siguientes tipos de discos de Windows:<ul><li>Volumen simple en disco básico</li><li>Volumen simple y reflejado en disco dinámico</li></ul>Se admiten el aprovisionamiento fino de Windows Server 2012 y las características ODX si usa un volumen iSCSI de StorSimple.<br><br>StorSimple puede crear volúmenes con aprovisionamiento fino y totalmente aprovisionados. No puede crear volúmenes de aprovisionamiento parcial.<br><br>El proceso de volver a formatear un volumen con aprovisionamiento fino puede tardar mucho tiempo en completarse. Se recomienda eliminar el volumen y luego crear uno nuevo en lugar de volverlo a formatear. Sin embargo, si aun así prefiere volver a formatear un volumen:<ul><li>Ejecute el siguiente comando antes de volver a formatearlo para evitar retrasos de recuperación de espacio: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Después de finalizar el formato, use el siguiente comando para volver a habilitar la recuperación de espacio:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique la revisión de Windows Server 2012 al equipo de Windows Server, como se describe en el artículo de KB [2878635](https://support.microsoft.com/kb/2870270).</li></ul></li></ul></ul> Si va a configurar Snapshot Manager de StorSimple o el Adaptador de StorSimple para SharePoint, vaya a los [requisitos de software para ver los componentes opcionales](#software-requirements-for-optional-components).|
| VMWare ESX | 5\.1 y 5.5 | Admitido con VMware vSphere como cliente iSCSI La característica VAAI-block se admite con VMware vSphere en dispositivos de StorSimple. 
| Linux RHEL/CentOS | 5 y 6 | Compatibilidad con clientes iSCSI de Linux con versiones del iniciador Open-iSCSI 5 y 6. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX no es compatible actualmente con StorSimple.

## Requisitos de software de componentes opcionales

Los siguientes requisitos de software son para los componentes de StorSimple opcionales (Snapshot Manager de StorSimple y Adaptador de StorSimple para SharePoint).

| Componente | Plataforma de host | Requisitos/notas adicionales |
| --------------------------- | ---------------- | ------------- |
| StorSimple Snapshot Manager | Windows Server 2008 R2 SP1, 2012, 2012 R2 | El uso de Snapshot Manager de StorSimple en Windows Server es necesario para realizar la copia de seguridad y restauración de los discos dinámicos reflejados y para realizar copias de seguridad coherentes con la aplicación.<br> Snapshot Manager de StorSimple solo se admite en Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 y Windows Server 2012.<ul><li>Si va a usar Window Server 2012, debe instalar .NET 3.5 o 4.5 antes de instalar Snapshot Manager de StorSimple.</li><li>Si va a usar Windows Server 2008 R2 SP1, debe instalar Windows Management Framework 3.0 antes de instalar Snapshot Manager de StorSimple.</li></ul> |
| Adaptador de StorSimple para SharePoint | Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>El Adaptador de StorSimple para SharePoint solo se admite en SharePoint 2010 y SharePoint 2013.</li><li>RBS requiere SQL Server Enterprise Edition, versión 2008 R2 o 2012.</li></ul>|
 
## Requisitos de red para el dispositivo StorSimple

El dispositivo StorSimple es un dispositivo bloqueado. Sin embargo, los puertos deben abrirse en el firewall para permitir el tráfico de administración, de nube o iSCSI. En la tabla siguiente se enumeran los puertos que deben estar abiertos en el firewall. En esta tabla, *dentro* o *entrante* hace referencia a la dirección desde la que el cliente entrante solicita acceso al dispositivo. *Fuera* o *saliente* hace referencia a la dirección en la que el dispositivo StorSimple envía datos externamente, más allá de la implementación: por ejemplo, saliente a Internet.

| Nº de puerto<sup>1,2</sup> | Dentro o fuera | Ámbito de puerto | Obligatorio | Notas |
|------------------------|-----------|------------|----------|-------| 
|TCP 80 (HTTP)<sup>3</sup>| Fuera | WAN | No |<ul><li>El puerto de salida se usa para tener acceso a Internet para recuperar las actualizaciones.</li><li>El proxy web de salida es configurable por el usuario.</li><li>Para permitir las actualizaciones del sistema, este puerto también debe estar abierto para las direcciones IP fijas del controlador.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Fuera | WAN | Sí |<ul><li>El puerto de salida se usa para tener acceso a los datos de la nube.</li><li>El proxy web de salida es configurable por el usuario.</li><li>Para permitir las actualizaciones del sistema, este puerto también debe estar abierto para las direcciones IP fijas del controlador.</li></ul>|
|UDP 53 (DNS) | Fuera | WAN | En algunos casos; consulte las notas. |Este puerto es necesario solo si está utilizando un servidor DNS basado en Internet. |
| UDP 123 (NTP) | Fuera | WAN | En algunos casos; consulte las notas. |Este puerto solo es necesario si está utilizando un servidor DNS basado en Internet. |
| TCP 9354 | Fuera | WAN | Sí |El dispositivo StorSimple usa el puerto de salida para comunicarse con el servicio StorSimple Manager. |
| 3260 (iSCSI) | En el | LAN | No | Este puerto se utiliza para tener acceso a datos a través de iSCSI.|
| 5985 | En el | LAN | No | El puerto de entrada se usa en Snapshot Manager de StorSimple para comunicarse con el dispositivo de StorSimple.<br>También se usa cuando se conecta de forma remota a Windows PowerShell para StorSimple a través de HTTP. |
| 5986 | En el | LAN | No | Este puerto se usa al conectarse de forma remota a Windows PowerShell para StorSimple a través de HTTPS. |

<sup>1</sup> Ningún puerto de entrada debe estar abierto en la red Internet pública.

<sup>2</sup> Si varios puertos llevan una configuración de puerta de enlace, el orden del tráfico enrutado saliente se determinará según el orden de enrutamiento del puerto que se describe a continuación en [Enrutamiento del puerto](#routing-metric).

<sup>3</sup> Las direcciones IP fijas del controlador del dispositivo StorSimple deben ser enrutables y capaces de conectarse a Internet. Las direcciones IP fijas se utilizan para el mantenimiento de las actualizaciones del dispositivo. Si los controladores del dispositivo no pueden conectarse a Internet a través de direcciones IP fijas, no podrá actualizar el dispositivo StorSimple.

> [AZURE.IMPORTANT] Asegúrese de que el firewall no modifica ni descifra ningún tráfico SSL entre el dispositivo de StorSimple y Azure.

### Métrica de enrutamiento

Una métrica de enrutamiento se asocia con las interfaces y con la puerta de enlace que enruta los datos a las redes específicas. La métrica de enrutamiento la usa el protocolo de enrutamiento para calcular la mejor ruta a un destino determinado, si aprende que existen varias rutas al mismo destino. Cuanto más bajo sea el valor de la métrica de enrutamiento, mayor será la preferencia.

En el contexto de StorSimple, si se configuran varias puertas de enlace e interfaces de red para el tráfico del canal, la métrica de enrutamiento entra en juego para determinar el orden relativo en que se usarán las interfaces. El usuario no puede cambiar las métricas de enrutamiento. Sin embargo, puede usar el cmdlet `Get-HcsRoutingTable` para imprimir la tabla de enrutamiento (y las métricas) en el dispositivo de StorSimple. Obtener más información sobre el cmdlet Get-HcsRoutingTable en [Solución de problemas de implementación de StorSimple](storsimple-troubleshoot-deployment.md).

Los algoritmos de la métrica de enrutamiento difieren en función de la versión de software que se ejecuta en el dispositivo de StorSimple.

**Versiones anteriores a Update 1**

Esto incluye las versiones de software anteriores a Update 1, como GA, 0.1, 0.2 o 0.3. El orden basado en la métrica de enrutamiento es el siguiente:

   *Última interfaz de red de 10 GbE configurada > Otra interfaz de red de 10 GbE > Última interfaz de red de 1 GbE configurada > Otra interfaz de red de 1 GbE*


**Versiones a partir de Update 1 y anteriores a Update 2**

Esto incluye las versiones de software, como 1, 1.1 o 1.2. El orden basado en la métrica de enrutamiento se decide en función de lo siguiente:

   *DATA 0 > Última interfaz de red de 10 GbE configurada > Otra interfaz de red de 10 GbE > Última interfaz de red de 1 GbE configurada > Otra interfaz de red de 1 GbE*

   En Update 1, se establece la métrica de enrutamiento de DATA 0 más baja; por lo tanto, todo el tráfico en la nube se enruta a través de DATA 0. Tome nota de esto si hay más de una interfaz de red habilitada para la nube en el dispositivo de StorSimple.


**Versiones a partir de Update 2**

Update 2 tiene varias mejoras relacionadas con las redes y las métricas de enrutamiento han cambiado. El comportamiento puede explicarse como sigue.

- Un conjunto de valores predeterminados se han asignado a interfaces de red. 	
		
- Considere una tabla de ejemplo que se muestra a continuación con valores asignados a las diversas interfaces de red si están habilitadas para la nube o deshabilitadas para la nube pero con una puerta de enlace configurada. Tenga en cuenta que los valores asignados aquí son solo valores de ejemplo.

		
	| Interfaz de red | Habilitada para la nube | Deshabilitada para la nube con puerta de enlace |
	|-----|---------------|---------------------------|
	| Data 0 | 1 | - |
	| Data 1 | 2 | 20 |
	| Data 2 | 3 | 30 |
	| Data 3 | 4 | 40 |
	| Data 4 | 5 | 50 |
	| Data 5 | 6 | 60 |


- El orden en que el tráfico de nube se enrutará a través de las interfaces de red es:
	 
	*Data 0 > Data 1 > Date 2 > Data 3 > Data 4 > Data 5*

	Esto también puede explicarse mediante el ejemplo siguiente.

	Consideremos un dispositivo de StorSimple con dos interfaces de red habilitadas para la nube, Data 0 y Data 5. De Data 1 a Data 4 la interfaz estará deshabilitada para la nube pero con una puerta de enlace configurada. El orden en que el tráfico se enrutará para este dispositivo será:

	*Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
	
	*donde los números entre paréntesis indican las métricas de enrutamientos respectivas.*
	
	Si se produce un error en Data 0, el tráfico de nube se enrutará a través de Data 5. Habida cuenta que se configura una puerta de enlace para todas las demás redes, si se produce algún error en Data 0 y Data 5, el tráfico de nube pasará por Data 1.
 

- Si se produce un error en una interfaz de red habilitada para la nube, entonces habrá 3 reintentos con un retraso de 30 segundos para conectarse a la interfaz. Si se produce un error en todos los reintentos, el tráfico se enruta a la siguiente interfaz habilitada para la nube disponible según se determine en la tabla de enrutamiento. Si se producen errores en todas las interfaces de red habilitadas para la nube, se producirá una conmutación por error del dispositivo al otro controlador (sin reinicio en este caso).
	
- Si se produce un error de la IP virtual de una interfaz de red habilitada para iSCSI, se producirán 3 reintentos con un retraso de 2 segundos. Este comportamiento ha sido el mismo desde las versiones anteriores. Si se produce un error en todas las interfaces de red iSCSI, se producirá una conmutación por error del controlador (acompañada de un reinicio).


- También se genera una alerta en el dispositivo de StorSimple cuando hay un error en la IP virtual. Para más información, vaya a la [referencia rápida de alertas](storsimple-manage-alerts.md).
	
- En términos de reintentos, iSCSI tendrá prioridad sobre la nube.

	Considere el ejemplo siguiente: un dispositivo de StorSimple tiene dos interfaces de red habilitadas, Data 0 y Data 1. Data 0 está habilitada para la nube mientras que Data 1 está habilitada para la nube y para iSCSI. Ninguna otra interfaz de red de este dispositivo está habilitada para la nube ni para iSCSI.
		
	Si se produce un error de Data 1, dado que es la última interfaz de red de iSCSI, esto produce una conmutación por error del controlador a Data 1 en el otro controlador.


### Prácticas recomendadas de redes

Además de los requisitos de redes anteriores, para obtener un rendimiento óptimo de la solución StorSimple, cumpla las siguientes prácticas recomendadas:

- Asegúrese de que el dispositivo StorSimple tenga un ancho de banda de 40 Mbps (o más) disponible en todo momento. Este ancho de banda no debe compartirse con otras aplicaciones.

- Asegúrese de que la conectividad de red a Internet está disponible en todo momento. Las conexiones a Internet esporádicas o poco confiables de los dispositivos, incluida la falta total de conectividad a Internet, dará como resultado una configuración no admitida.

- Aísle el tráfico iSCSI y de la nube mediante interfaces de red específicas en el dispositivo para el acceso a iSCSI y a la nube. Para obtener más información, consulte cómo [modificar interfaces de red](storsimple-modify-device-config.md#modify-network-interfaces) en el dispositivo de StorSimple.

- No utilice una configuración de protocolo de agregación de vínculos (LACP) para las interfaces de red. Se trata de una configuración no admitida.


## Requisitos de alta disponibilidad para StorSimple

La plataforma de hardware que se incluye con la solución de StorSimple tiene características de confiabilidad y disponibilidad que proporcionan la base para una infraestructura de almacenamiento de alta disponibilidad con tolerancia a errores en su centro de datos. Sin embargo, existen requisitos y procedimientos recomendados que debe cumplir para ayudar a garantizar la disponibilidad de la solución StorSimple. Antes de implementar StorSimple, revise cuidadosamente los siguientes requisitos y recomendaciones para el dispositivo StorSimple y los equipos host conectados.

Para obtener más información sobre la supervisión y el mantenimiento de los componentes de hardware del dispositivo de StorSimple, vaya a [Uso del servicio StorSimple Manager para supervisar el estado y los componentes de hardware](storsimple-monitor-hardware-status.md) y [Reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

### Requisitos de alta disponibilidad y procedimientos para el dispositivo StorSimple

Revise la siguiente información con cuidado para garantizar la alta disponibilidad del dispositivo StorSimple.

#### PCM

Los dispositivos de StorSimple incluyen módulos de alimentación y refrigeración (PCM) redundantes intercambiables en caliente. Cada PCM tiene suficiente capacidad para proporcionar un servicio para el chasis completo. Para garantizar una alta disponibilidad, debe instalar ambos PCM.

- Conecte los PCM a diferentes fuentes de alimentación para proporcionar disponibilidad si se produce un error en una fuente de alimentación.
- Si se produce un error en un PCM, solicite su sustitución inmediatamente.
- Retire el PCM que presenta el error unicamente cuando tenga el recambio y esté listo para instalarlo.
- No quite los PCM simultáneamente. El módulo PCM incluye el módulo de batería de reserva. La eliminación de los PCM dará lugar a un apagado sin protección de la batería y no se guardará el estado del dispositivo. Para obtener más información sobre la batería, vaya a [Mantenimiento del módulo de batería de reserva](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### Módulos de controlador

Los dispositivos StorSimple incluyen módulos de controlador intercambiables en caliente. Los módulos de controlador funcionan de manera activa/pasiva. En un momento dado, un módulo de controlador está activo y proporcionando servicio, mientras que el módulo del controlador permanece pasivo. El módulo de controlador pasivo está encendido y pasa a estar operativo si el módulo del controlador activo falla o es retirado. Cada módulo de controlador dispone de suficiente capacidad para proporcionar un servicio para el chasis completo. Los módulos de controlador deben instalarse para garantizar una alta disponibilidad.

- Asegúrese de que ambos módulos de controlador estén instalados en todo momento.

- Si se produce un error en un módulo de controlador, solicite su sustitución inmediatamente.

- Retire el módulo de controlador que presenta el error unicamente cuando tenga el recambio y esté listo para instalarlo. La extracción de un módulo durante períodos prolongados afectará al flujo de aire y, por tanto, al enfriamiento del sistema.

- Asegúrese de que las conexiones de red a los módulos de controlador sean idénticas, y que las interfaces de red conectadas tengan una configuración de red idéntica.

- Si un módulo del controlador falla o necesita ser sustituido, asegúrese de que el otro módulo del controlador esté en estado activo antes de reemplazar el módulo del controlador que presenta el error. Para comprobar que un controlador está activo, vaya a [Identificación del controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- No quite ambos módulos del controlador al mismo tiempo. Si se está efectuando una conmutación por error en un controlador, no apague el módulo del controlador en modo de espera ni lo retire del chasis.

- Después de una conmutación por error del controlador, espere al menos cinco minutos antes de quitar cualquier módulo del controlador.

#### Interfaces de red

Los módulos del controlador del dispositivo StorSimple tienen cada uno cuatro interfaces de red Ethernet de 1 Gigabit y dos de 10 Gigabits.

- Asegúrese de que las conexiones de red a los módulos de controlador sean idénticas y que las interfaces de red interconectadas por el módulo del controlador estén conectadas para disponer de una configuración de red idéntica.

- Cuando sea posible, implemente conexiones de red a través de diferentes conmutadores para garantizar la disponibilidad del servicio si se produce un error del dispositivo de red.

- Antes de desconectar la única o la última interfaz restante compatible con iSCSI (con direcciones IP asignadas), deshabilite la interfaz primero y, a continuación, desconecte los cables. Si la interfaz se desconecta en primer lugar, hará que el controlador activo conmute por error al controlador pasivo. Si el controlador pasivo también tiene sus interfaces correspondientes desconectadas, ambos controladores se reiniciarán varias veces antes de establecerse en un controlador.

- Conecte al menos dos interfaces de datos a la red desde cada módulo del controlador.

- Si ha habilitado las dos interfaces de 10 GbE, impleméntelas en distintos conmutadores.

- Cuando sea posible, use MPIO en servidores para asegurarse de que los servidores pueden tolerar un vínculo, red o errores de interfaz.

Para obtener más información sobre cómo conectar en red su dispositivo para obtener alta disponibilidad y rendimiento, vaya a [Instalación del dispositivo de StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [Instalación del dispositivo de StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### SSD y unidades de disco duro

Los dispositivos StorSimple incluyen discos de estado sólido (SSD) y unidades de disco duro (HDD) que están protegidos con espacios reflejados. El uso de espacios reflejados garantiza que el dispositivo sea capaz de tolerar el error de uno o más SSD o unidades de disco duro.

- Asegúrese de que estén instalados todos los módulos SSD y de disco duro.

- Si se produce un error en un PCM o en un disco duro, solicite su sustitución inmediatamente.

- Si un SSD o una unidad de disco duro falla o requiere su sustitución, asegúrese de retirar solo los SSD o unidades de disco duro que requieran ser reemplazados.

- No retire más de un SSD o unidad de disco duro del sistema en un determinado momento. Es posible que un error de 2 o más discos de cierto tipo (disco duro, SSD) o errores consecutivos dentro de un período de tiempo breve provoquen un fallo de funcionamiento en el sistema y una posible pérdida de datos. Si esto ocurre, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para obtener ayuda.

- Durante la sustitución, supervise el **Estado del hardware** en la página **Mantenimiento** para las unidades de SSD y unidades de disco duro. Un estado de verificación verde indica que los discos están en buen estado o correctos, mientras que un signo de exclamación rojo indica un error de SSD o de unidad de disco duro.

- Se recomienda configurar instantáneas en la nube para todos los volúmenes que deberá proteger en caso de que se produzca un error del sistema.

#### Receptáculo EBOD

El modelo 8600 del dispositivo StorSimple incluye un receptáculo Extended Bunch of Disks (EBOD) además del receptáculo principal. Un EBOD contiene controladores EBOD y unidades de disco duro (HDD) que están protegidas mediante espacios reflejados. El uso de espacios reflejados garantiza que el dispositivo sea capaz de tolerar el error de uno o más unidades de disco duro. El receptáculo EBOD está conectado al receptáculo principal a través de cables SAS redundantes.

- Asegúrese de que los módulos del controlador del receptáculo EBOD, los cables SAS y todas las unidades de disco duro estén instalado en todo momento.

- Si se produce un error en un módulo de controlador de receptáculo EBOD, solicite su sustitución inmediatamente.

- Si se produce un error en un módulo de controlador de receptáculo EBOD, asegúrese de que el módulo del controlador esté activo antes de reemplazar el módulo con error. Para comprobar que un controlador está activo, vaya a [Identificación del controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Durante la sustitución de un módulo del controlador EBOD, supervise de manera continua el estado del componente en el servicio del administrador de StorSimple mediante el acceso a **Mantenimiento** - **Estado del hardware**.

- Si se produce un error en un cable SAS o requiere ser sustituido (el Soporte de Microsoft deberá estar implicado en la toma de dicha determinación), asegúrese de quitar solamente el cable de SAS que deba sustituirse.

- No retire simultáneamente ambos cables SAS del sistema en un determinado momento.

### Recomendaciones de alta disponibilidad para los equipos host

Revise cuidadosamente estos procedimientos recomendados para garantizar la alta disponibilidad de los hosts conectados al dispositivo de StorSimple.

- Configure StorSimple con [configuraciones de clúster de servidor de archivos de dos nodos][1]. Mediante la eliminación de puntos individuales de error y el aumento de la redundancia en el lado del host, la solución completa pasará a presentar una alta disponibilidad.

- Use recursos compatibles de disponibilidad continua (CA) disponibles con Windows Server 2012 (SMB 3.0) para lograr una alta disponibilidad durante la conmutación por error de los controladores de almacenamiento. Para obtener información adicional para configurar los clústeres de servidores de archivos y de recursos compartidos disponibles continuamente con Windows Server 2012, consulte este [vídeo de demostración](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## Pasos siguientes

- [Obtenga más información acerca de los límites de StorSimple](storsimple-limits.md).
- [Obtenga más información sobre cómo implementar la solución StorSimple](storsimple-deployment-walkthrough-u2.md).
 
<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx

<!---HONumber=AcomDC_0211_2016-->