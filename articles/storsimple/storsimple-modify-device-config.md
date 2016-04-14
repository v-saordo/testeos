<properties 
   pageTitle="Modificar la configuración del dispositivo StorSimple | Microsoft Azure" 
   description="Describe cómo usar el servicio StorSimple Manager para volver a configurar un dispositivo StorSimple que ya se ha implementado." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="01/15/2016"
   ms.author="v-sharos"/>

# Uso del servicio StorSimple Manager para modificar la configuración del dispositivo StorSimple

## Información general 

La página **Configurar** del Portal de Azure clásico contiene todos los parámetros de dispositivo que puede volver a configurar en un dispositivo de StorSimple administrado mediante un servicio de Administrador de StorSimple. En este tutorial se explica cómo usar la página **Configurar** para realizar las siguientes tareas de nivel de dispositivo:

- Modificar la configuración del dispositivo 
- Modificar la configuración del tiempo 
- Modificar la configuración de DNS 
- Modificar las interfaces de red
- Intercambiar o volver a asignar direcciones IP

## Modificar la configuración del dispositivo

La configuración de dispositivos incluye el nombre descriptivo del dispositivo y la descripción de este.

A un dispositivo StorSimple que está conectado al servicio StorSimple Manager se le asigna un nombre predeterminado. El nombre predeterminado normalmente refleja el número de serie del dispositivo. Por ejemplo, un nombre de dispositivo predeterminado que tiene 15 caracteres, como 8600-SHX0991003G44HT indica lo siguiente:

- **8600**: indica el modelo del dispositivo.
- **SHX**: indica el centro de producción.
- **0991003**: indica un producto específico.
- **G44HT**: los últimos 5 dígitos se incrementan para crear números de serie únicos. Es posible que esto no sea un conjunto secuencial.

Puede usar el Portal de Azure clásico para cambiar el nombre del dispositivo y asignarle un nombre descriptivo único de su elección. El nombre descriptivo puede contener cualquier carácter y tener un máximo de 64 caracteres.

También puede especificar una descripción del dispositivo. La descripción del dispositivo suele ayudar a identificar el propietario y la ubicación física del dispositivo. El campo de descripción debe contener menos de 256 caracteres.
 
## Modificar la configuración del tiempo

El dispositivo debe sincronizar la hora para autenticarse con su proveedor de servicios de almacenamiento en la nube. Seleccione la zona horaria en la lista desplegable y especifique un máximo de dos servidores de Protocolo de tiempo de redes (NTP). El servidor NTP principal es necesario y se especifica cuando usa Windows PowerShell para StorSimple para configurar el dispositivo. Puede especificar el Windows Server predeterminado **time.windows.com** como el servidor NTP. Puede ver la configuración del servidor NTP principal a través del Portal de Azure clásico, pero debe usar la interfaz de Windows PowerShell para cambiarla.

La configuración del servidor NTP secundario es opcional. Puede usar el Portal clásico para configurar un servidor NTP secundario.

Al configurar el servidor NTP, asegúrese de que su red permite el paso del tráfico NTP del centro de datos a Internet. Al especificar un servidor NTP público, debe asegurarse de que los firewalls de red y otros dispositivos de seguridad estén configurados para permitir que el tráfico NTP viaje hacia y desde la red externa. Si no se permite el tráfico NTP bidireccional, debe usar un servidor NTP interno (esta función la proporciona un controlador de dominio de Windows). Si el dispositivo no puede sincronizar la hora, es posible que no sea capaz de comunicarse con el proveedor de almacenamiento en la nube.

Para ver una lista de servidores NTP públicos, vaya a la [Web de servidores NTP](http://support.ntp.org/bin/view/Servers/WebHome).

### ¿Qué sucede si el dispositivo se implementa en una zona horaria diferente?

Si el dispositivo se implementa en una zona horaria distinta, cambiará la zona horaria del dispositivo. Dado que todas las directivas de copia de seguridad usan la zona horaria del dispositivo, las directivas de copia de seguridad se ajustarán automáticamente según la nueva zona horaria. No se requiere ninguna intervención del usuario.

## Modificar la configuración de DNS

Cuando el dispositivo intenta comunicarse con el proveedor de servicios de almacenamiento en la nube, se usa un servidor DNS. Para lograr alta disponibilidad, se debe configurar el servidor DNS principal y el secundario durante la implementación inicial del dispositivo. Para volver a configurar el servidor DNS principal, deberá usar la interfaz de Windows PowerShell en el dispositivo StorSimple.

Para modificar el servidor DNS secundario, puede usar el Portal de Azure clásico.

<!-- If a secondary DNS server is not configured, you will not be able to create volume containers or provision volumes on the device.-->

## Modificar las interfaces de red

El dispositivo tiene seis interfaces de red de dispositivo, cuatro de las cuales son de 1 GbE y dos de 10 GbE. Estas interfaces están etiquetadas mediante DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 y DATA 5 son de 1 GbE, mientras que DATA 2 y DATA 3 son interfaces de red de 10 GbE.

Establezca la **Configuración de la interfaz de red** para cada una de las interfaces que se usarán. Para garantizar una alta disponibilidad, se recomienda tener al menos dos interfaces de iSCSI y dos interfaces compatibles con la nube en el dispositivo. Es recomendable, pero no obligatorio, deshabilitar las interfaces no usadas.

Al configurar cualquiera de las interfaces de red, debe configurar una dirección IP virtual (VIP).

DATA 0 es compatible con la nube de manera predeterminada. Al configurar DATA 0, también se deben configurar dos direcciones IP fijas, una para cada controlador. Estas direcciones IP fijas pueden usarse para tener acceso directamente a los controladores de dispositivos y son útiles cuando se instalan actualizaciones en el dispositivo o al obtener acceso a los controladores con el fin de solucionar problemas.

En StorSimple 8000 Series Update 1, se establece la métrica de enrutamiento de DATA 0 en el nivel mínimo; por lo tanto, si el dispositivo está ejecutando StorSimple 8000 Series Update 1, todo el tráfico en la nube se enrutará a través de DATA 0. Tome nota de esto si hay más de una interfaz de red compatible con la nube en el dispositivo StorSimple.

>[AZURE.NOTE]Las direcciones IP fijas del controlador se usan para el mantenimiento de las actualizaciones del dispositivo. Por lo tanto, las direcciones IP fijas deben ser enrutables y disponer de capacidad de conexión a Internet.

Para cada interfaz de red, se muestran los parámetros siguientes:

- **Velocidad**: no es un parámetro configurable por el usuario. DATA 0, DATA 1, DATA 4 y DATA 5 son siempre de 1 GbE, mientras que DATA 2 y DATA 3 son interfaces de 10 GbE.

     >[AZURE.NOTE]La velocidad y el dúplex siempre se negocian automáticamente. Las tramas gigantes no son compatibles.
 
- **Estado de interfaz**: una interfaz se puede habilitar o deshabilitar. Si está habilitada, el dispositivo intentará usar la interfaz. Es recomendable habilitar tan solo las interfaces que están conectadas a la red y que se usan. Deshabilite las interfaces que no esté usando.

- **Tipo de interfaz**: este parámetro permite aislar el tráfico iSCSI del tráfico de almacenamiento de la nube. Este parámetro puede ser uno de los siguientes:

    - **Compatible con la nube**: si está habilitado, el dispositivo usará esta interfaz para comunicarse con la nube.
    - **Compatible con iSCSI**: si está habilitado, el dispositivo usará esta interfaz para comunicarse con el host iSCSI.

    Es recomendable aislar el tráfico iSCSI del tráfico de almacenamiento en la nube. Tenga también en cuenta que si el host se encuentra dentro de la misma subred que el dispositivo, no es necesario asignar una puerta de enlace; sin embargo, si el host está en una subred diferente a la de su dispositivo, deberá asignar una puerta de enlace.

- **Dirección IP**: esta puede ser IPv4, IPv6 o ambas. Las familias de direcciones IPv4 e IPv6 son compatibles con las interfaces de red del dispositivo. Si usa IPv4, especifique una dirección IP de 32 bits (*xxx.xxx.xxx.xxx*) en notación punto-decimal. Cuando use IPv6, solo tiene que proporcionar un prefijo de 4 dígitos y se generará automáticamente una dirección de 128 bits para la interfaz de red del dispositivo en función de ese prefijo.

- **Subred**: esto hace referencia a la máscara de subred y se configura mediante la interfaz de Windows PowerShell.

- **Puerta de enlace**: se trata de la puerta de enlace predeterminada que debe usar esta interfaz cuando intenta comunicarse con nodos que no están en el mismo espacio de direcciones IP (subred). La puerta de enlace predeterminada debe estar en el mismo espacio de direcciones (subred) que la dirección IP de la interfaz, según lo determinado por la máscara de subred.

- **Dirección IP fija**: este campo solo está disponible mientras se configura la interfaz DATA 0. Para efectuar operaciones como las actualizaciones o la solución de problemas del dispositivo, es posible que necesite conectarse directamente con el controlador del dispositivo. La dirección IP fija puede usarse para tener acceso al controlador activo y el pasivo en el dispositivo.

Puede volver a configurar el Controlador 0 y el Controlador 1 a través del Portal de Azure clásico.

>[AZURE.NOTE]
>
>- Para garantizar un funcionamiento correcto, compruebe la velocidad de la interfaz y dúplex en el conmutador al que está conectado la interfaz de cada dispositivo. Las interfaces de conmutador deben negociar con o configurarse para Gigabit Ethernet (1000 Mbps) y ser de dúplex completo. Las interfaces que funcionan a velocidades menores o en un semidúplex darán como resultado problemas de rendimiento.
>
>- Para minimizar las interrupciones y el tiempo de inactividad, es recomendable habilitar portfast en cada uno de los puertos de conmutador a los que se va a conectar la interfaz de red iSCSI del dispositivo. Esto garantizará que la conectividad de red se pueda establecer rápidamente si se produce una conmutación por error.
 
## Intercambiar o volver a asignar direcciones IP

Actualmente, si se le asigna una VIP que esté siendo usado (por el mismo dispositivo u otro de la red) a cualquier interfaz de red del controlador, a continuación, el controlador conmutará por error. Por lo tanto, debe seguir el procedimiento correcto si está intercambiando VIP para la interfaz de red del dispositivo, puesto que creará una situación de IP duplicada.

Realice los pasos siguientes para intercambiar o volver a asignar las VIP para cualquiera de las interfaces de red:

#### Para volver a asignar direcciones IP

1. Borre la dirección IP de ambas interfaces.

2. Una vez borradas las direcciones IP, asigne las nuevas direcciones IP a las interfaces correspondientes.

## Pasos siguientes

- Obtenga información sobre cómo [configurar MPIO para el dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

- Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).
     

<!---HONumber=AcomDC_0121_2016-->