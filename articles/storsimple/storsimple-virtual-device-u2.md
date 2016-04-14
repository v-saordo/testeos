<properties 
   pageTitle="Actualización 2 del dispositivo virtual de StorSimple | Microsoft Azure"
   description="Obtenga información acerca de cómo crear, implementar y administrar un dispositivo virtual StorSimple en una red virtual de Microsoft Azure. (Se aplica a la actualización 2 de StorSimple)."
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
   ms.date="02/12/2016"
   ms.author="alkohli" />

# Implementación y administración de un dispositivo virtual de StorSimple en Azure (actualización 2)

> [AZURE.SELECTOR]
- [Actualización 2](../articles/storsimple/storsimple-virtual-device-u2.md)
- [Actualización 1](../articles/storsimple/storsimple-virtual-device-u1.md)
- [Versión: disponibilidad general](../articles/storsimple/storsimple-virtual-device.md)

##Información general
El dispositivo virtual de StorSimple es una capacidad adicional que se incluye con la solución de Microsoft Azure StorSimple. El dispositivo virtual de StorSimple se ejecuta en una máquina virtual en una red virtual de Microsoft Azure y se puede usar para realizar copias de seguridad y clonar los datos de los hosts.


#### Comparación del modelo de dispositivo virtual

El dispositivo virtual StorSimple está disponible en dos modelos: estándar 8010 (anteriormente conocido como 1100) y premium 8020 (introducido en la actualización 2). A continuación se incluye una tabla comparativa de los dos modelos.


| Modelo de dispositivo | 8010<sup>1</sup> | 8020 |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacidad máxima** | 30 TB | 64 TB |
| **MV de Azure** | Standard\_A3 (4 núcleos, 7 GB de memoria) | Standard\_DS3 (4 núcleos, 14 GB de memoria) |
| **Compatibilidad de versión** | Ejecuta las versiones previas a la actualización 2 o posterior | Ejecuta las versiones de la actualización 2 o posterior |
| **Disponibilidad en regiones** | Todas las regiones de Azure | Regiones de Azure que admiten Almacenamiento premium<br></br>Si desea obtener una lista de las regiones, consulte las [regiones admitidas para 8020](#supported-regions-for-8020). |
| **Tipo de almacenamiento** | Usa el almacenamiento estándar de Azure <br></br> Infórmese de cómo [crear una cuenta de almacenamiento estándar]() | Usa el Almacenamiento premium de Azure <br></br> Infórmese de cómo [crear una cuenta de almacenamiento premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk) |
| **Guía de la carga de trabajo** | Recuperación a nivel de elemento de archivos de copias de seguridad | Escenarios de desarrollo y pruebas en la nube, baja latencia, mayores cargas de trabajo de rendimiento <br></br>Dispositivo secundario para recuperación ante desastres |
 
<sup>1</sup> *Anteriormente conocido como 1100*.

#### Regiones admitidas para 8020

Las regiones de Almacenamiento premium que actualmente admiten 8020 aparecen a continuación. Esta lista se actualizará de manera periódica cada vez que Almacenamiento premium esté disponible en más regiones.

| S. No. | Actualmente se admite en las regiones |
|---------------------------------------------------------|--------------------------------|
| 1 | Central EE. UU.: |
| 2 | Este de EE. UU. |
| 3 | Este de EE. UU. 2 |
| 4 | Oeste de EE. UU. |
| 5 | Europa del Norte |
| 6 | Europa occidental |
| 7 | Sudeste asiático |
| 8 | Este de Japón |
| 9 | Oeste de Japón |
| 10 | Australia Oriental |
| 11 | Sudeste de Australia* |
| 12 | Asia Oriental* |
| 13 | Centro-sur de EE. UU.* |

** Almacenamiento premium se lanzó recientemente en estas geoáreas.

Este artículo describe paso a paso el proceso de implementación de un dispositivo virtual de StorSimple en Azure. Después de leer este artículo, habrá aprendido lo siguiente:

- En qué difiere el dispositivo virtual del dispositivo físico.

- Procedimiento a seguir para crear y configurar el dispositivo virtual.

- Realizar la conexión con la máquina virtual.

- Cómo trabajar con el dispositivo virtual.

Este tutorial se aplica a todos los dispositivos virtuales StorSimple que ejecuten la actualización 2 y posterior.

## ¿Cómo difiere el dispositivo virtual del dispositivo físico

El dispositivo virtual de StorSimple es una versión solo de software de StorSimple que se ejecuta en un único nodo en una máquina virtual de Microsoft Azure. El dispositivo virtual admite escenarios de recuperación ante desastres en los que el dispositivo físico no está disponible, y es adecuado para su uso en la recuperación a nivel de elemento de copias de seguridad, en la recuperación ante desastres local y en escenarios de prueba y desarrollo en la nube.

#### Diferencias del dispositivo físico

La tabla siguiente muestra algunas diferencias clave entre el dispositivo virtual de StorSimple y el dispositivo físico StorSimple:

| | Dispositivo físico | Dispositivo virtual |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Ubicación** | Se encuentra en el centro de datos. | Se ejecuta en Azure. |
| **Interfaces de red** | Tiene seis interfaces de red: de DATA 0 a DATA 5. | Solo tiene una interfaz de red: DATA 0. |
| **Registro** | Registrado durante el paso de configuración. | Registrado a través de una tarea independiente. |
| **Clave de cifrado de datos de servicio** | Vuelve a generar en el dispositivo físico y, a continuación, actualiza el dispositivo virtual con la clave nueva. | No se puede volver a generar desde el dispositivo virtual. |


## Requisitos previos para el dispositivo virtual

Las siguientes secciones explican los requisitos previos de configuración para el dispositivo virtual de StorSimple. Antes de implementar un dispositivo virtual, consulte el artículo [Protección de datos y seguridad de StorSimple](storsimple-security.md#storsimple-virtual-device-security).

#### Requisitos de Azure

Antes de aprovisionar el dispositivo virtual, deberá realizar los siguientes preparativos en el entorno de Azure:

- Para el dispositivo virtual, [configure una red virtual en Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Si usa Almacenamiento premium, tiene que crear una red virtual en una región de Azure que admita dicho almacenamiento. Más información sobre las [regiones que actualmente admiten 8020](#supported-regions-for-8020).
- Puede usar el servidor DNS predeterminado proporcionado por Azure en lugar de especificar su propio nombre del servidor DNS. Si el nombre del servidor DNS no es válido o si el servidor DNS no es capaz de resolver las direcciones IP correctamente, no se podrá crear el dispositivo virtual.
- Punto a sitio y sitio a sitio son opcionales, pero no obligatorios. Si lo desea, puede configurar estas opciones para escenarios más avanzados. 
- Puede crear [Máquinas virtuales de Azure](../virtual-machines/virtual-machines-about.md) (servidores host) en la red virtual que pueden usar los volúmenes expuestos por el dispositivo virtual. Estos servidores deben cumplir los siguientes requisitos: 							
	- Estar en máquinas virtuales de Windows o Linux con el software iSCSI Initiator instalado.
	- Ejecutarse en la misma red virtual como el dispositivo virtual.
	- Ser capaz de conectarse al destino iSCSI del dispositivo virtual a través de la dirección IP interna del dispositivo virtual.

- Asegúrese de que haya habilitado la compatibilidad para el tráfico iSCSI y en la nube en la misma red virtual.


#### Requisitos de StorSimple

Realice las siguientes actualizaciones en su servicio StorSimple de Azure antes de crear un dispositivo virtual:


- Agregue [registros de control de acceso](storsimple-manage-acrs.md) para las máquinas virtuales que vayan a ser servidores de host para el dispositivo virtual.

- Utilice una [cuenta de almacenamiento](storsimple-manage-storage-accounts.md#add-a-storage-account) en la misma región que el dispositivo virtual. Las cuentas de almacenamiento en regiones diferentes pueden causar un bajo rendimiento. Puede usar una cuenta de almacenamiento estándar o premium con el dispositivo virtual. Obtenga más información sobre cómo crear una [cuenta de almacenamiento estándar]() o una [cuenta de almacenamiento premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

- Para crear el dispositivo virtual use una cuenta diferente de la que usa para los datos. Utilizar la misma cuenta de almacenamiento puede causar un bajo rendimiento.

Asegúrese de que tiene la siguiente información antes de empezar:

- Cuenta del Portal de Azure clásico con credenciales de acceso.

- Una copia de la clave de cifrado de datos del servicio del dispositivo físico.


## Creación y configuración del dispositivo virtual

Antes de realizar estos procedimientos, asegúrese de que se han cumplido los [requisitos previos para el dispositivo virtual](#prerequisites-for-the-virtual-device).

Después de crear una red virtual, configurar un servicio del Administrador de StorSimple y registrar el dispositivo físico de StorSimple con el servicio, siga los pasos a continuación para crear un dispositivo virtual de StorSimple.

### Paso 1: Creación de un dispositivo virtual

Realice los pasos siguientes para crear el dispositivo virtual de StorSimple.

[AZURE.INCLUDE [Creación de un dispositivo virtual](../../includes/storsimple-create-virtual-device-u2.md)]


### Paso 2: Configuración y registro del dispositivo virtual

Antes de comenzar este procedimiento, asegúrese de que tiene una copia de la clave de cifrado de datos del servicio. La clave de cifrado de datos del servicio se creó cuando se configuró el primer dispositivo de StorSimple y se le pidió que lo guardara en una ubicación segura. Si no tiene una copia de la clave de cifrado de datos del servicio, debe ponerse en contacto con Microsoft Support para obtener ayuda.

Realice los pasos siguientes para configurar y registrar el dispositivo virtual de StorSimple.
[AZURE.INCLUDE [Configuración y registro de un dispositivo virtual](../../includes/storsimple-configure-register-virtual-device.md)]

### Paso 3: (Opcional) Modificación de la configuración del dispositivo

En la siguiente sección se describen las opciones de configuración de dispositivo necesarias para el dispositivo virtual de StorSimple si desea usar CHAP, Administrador de instantáneas StorSimple, o si quiere cambiar la contraseña del administrador de dispositivos.

#### Configuración del iniciador CHAP

Este parámetro contiene las credenciales que espera el dispositivo virtual (destino) de los iniciadores (servidores) que intentan obtener acceso a los volúmenes. Los iniciadores proporcionarán un nombre de usuario y una contraseña CHAP para identificarse en el dispositivo durante la autenticación. Para obtener los pasos detallados necesarios, vaya a [Configurar CHAP para el dispositivo StorSimple](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### Configuración del destino CHAP

Este parámetro contiene las credenciales que el dispositivo virtual utiliza cuando un iniciador habilitado para CHAP solicita la autenticación mutua o bidireccional. El dispositivo virtual utilizará un nombre de usuario de CHAP inverso y una contraseña de CHAP inversa para identificarse con el iniciador durante este proceso de autenticación. Tenga en cuenta que los valores de destino de CHAP son valores globales. Cuando se aplican, todos los volúmenes conectados al dispositivo virtual de almacenamiento utilizan la autenticación CHAP. Para obtener los pasos detallados necesarios, vaya a [Configurar CHAP para el dispositivo StorSimple](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### Configuración de la contraseña de StorSimple Snapshot Manager

El software StorSimple Snapshot Manager reside en el host de Windows y permite a los administradores administrar copias de seguridad del dispositivo StorSimple en forma de instantáneas locales y en la nube.

>[AZURE.NOTE] Para el dispositivo virtual, el host de Windows es una máquina virtual de Azure.

Al configurar un dispositivo en StorSimple Snapshot Manager, se le pedirá que proporcione la dirección IP del dispositivo StorSimple y la contraseña para autenticar el dispositivo de almacenamiento. Para obtener los pasos detallados necesarios, vaya a [Cambio de la contraseña de StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)

#### Cambio de la contraseña del administrador de dispositivos 

Cuando se utiliza la interfaz de Windows PowerShell para tener acceso al dispositivo virtual, se le pedirá que escriba una contraseña de administrador de dispositivos. Por seguridad de sus datos, es necesario cambiar esta contraseña para poder usar el dispositivo virtual. Para obtener los pasos detallados necesarios, vaya a [Cambio de la contraseña del administrador de dispositivos](storsimple-change-passwords.md#change-the-device-administrator-password).

## Conexión remota con el dispositivo virtual.
El acceso remoto a su dispositivo virtual mediante la interfaz de Windows PowerShell no está habilitado de forma predeterminada. Deberá habilitar primero la administración remota en el dispositivo virtual y, a continuación, habilitarlo en el cliente que se utilizará para acceder al dispositivo virtual.

A continuación se detalla los dos pasos del procedimiento para conectarse de forma remota.

### Paso 1: Configuración de la administración remota

Realice los pasos siguientes para configurar la administración remota para el dispositivo virtual de StorSimple.

[AZURE.INCLUDE [Configurar la administración remota a través de HTTP para el dispositivo virtual](../../includes/storsimple-configure-remote-management-http-device.md)]

### Paso 2: Obtención de acceso remoto al dispositivo virtual

Una vez habilitada la administración remota en la página de configuración del dispositivo StorSimple, puede usar la comunicación remota de Windows PowerShell para conectarse al dispositivo virtual desde otra máquina virtual dentro de la misma red virtual; por ejemplo, puede conectarse desde la máquina virtual del host que ha configurado y utilizado para conectarse a iSCSI. En la mayoría de las implementaciones, ya habrá abierto un punto de conexión público para tener acceso a su máquina virtual host que se puede utilizar para tener acceso al dispositivo virtual.

>[AZURE.WARNING] **Para mayor seguridad, se recomienda utilizar HTTPS al conectarse a los extremos y, a continuación, eliminar los extremos después de haber completado la sesión remota de PowerShell.**

Debe seguir los procedimientos que aparecen en [Conectarse de forma remota al dispositivo StorSimple](storsimple-remote-connect.md) para establecer la comunicación remota para el dispositivo virtual.

## Conexión directa con el dispositivo virtual.

También puede realizar una conexión directa con el dispositivo virtual. Si desea conectarse directamente al dispositivo virtual desde otro equipo fuera de la red virtual o fuera del entorno de Microsoft Azure, tendrá que crear puntos de conexión adicionales, como se describe en el procedimiento siguiente.

Realice los pasos siguientes para crear un punto de conexión público en el dispositivo virtual.

[AZURE.INCLUDE [Crear puntos de conexión públicos en un dispositivo virtual](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Se recomienda conectarse desde otra máquina virtual dentro de la misma red virtual, ya que esta práctica minimiza el número de puntos de conexión públicos de la red virtual. Cuando utiliza este método, simplemente conéctese a la máquina virtual mediante una sesión de escritorio remoto y, a continuación, configure esa máquina virtual para su uso como haría con cualquier otro cliente de Windows en una red local. No es necesario anexar el número de puerto público porque ya se conocerá el puerto.

## Trabajo con el dispositivo virtual de StorSimple

Ahora que ha creado y configurado el dispositivo virtual de StorSimple, está listo para comenzar a trabajar con él. Puede trabajar con contenedores de volúmenes, volúmenes y directivas de copia de seguridad en un dispositivo virtual como lo haría en un dispositivo físico de StorSimple; la única diferencia es que necesita asegurarse de que selecciona el dispositivo virtual en la lista de dispositivos. Consulte [Utilizar el servicio de Administrador de StorSimple para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md) para conocer los procedimientos paso a paso de las diversas tareas de administración para el dispositivo virtual.

Las secciones siguientes describen algunas de las diferencias que surgen al trabajar con el dispositivo virtual.

### Mantenimiento de un dispositivo virtual de StorSimple

Dado que es un dispositivo solo de software, el mantenimiento del dispositivo virtual es mínimo en comparación con el mantenimiento del dispositivo físico. Tiene las siguientes opciones:

- **Actualizaciones de software**: puede ver la fecha en que se actualizó por última vez el software, junto con cualquier actualización de los mensajes de estado. Puede utilizar el botón **Buscar actualizaciones** en la parte inferior de la página para realizar una búsqueda manual si desea comprobar si hay nuevas actualizaciones. Si hay actualizaciones disponibles, haga clic en **Instalar actualizaciones** para instalar. Puesto que solo hay una única interfaz en el dispositivo virtual, significa que habrá una pequeña interrupción del servicio cuando se apliquen las actualizaciones. El dispositivo virtual se apagará automáticamente y se reiniciará (si es necesario) para aplicar las actualizaciones que se han publicado. Para obtener un procedimiento paso a paso, vaya a [Actualización del dispositivo de la serie StorSimple 8000](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Paquete de soporte**: puede crear y cargar un paquete de soporte para ayudar al Soporte de Microsoft a solucionar problemas con el dispositivo virtual. Para obtener un procedimiento paso a paso, vaya a [Crear y administrar paquetes de soporte técnico de StorSimple](storsimple-create-manage-support-package.md).

### Cuentas de almacenamiento para un dispositivo virtual

Las cuentas de almacenamiento se crean para su uso por el servicio StorSimple Manager, el dispositivo virtual y el dispositivo físico. Al crear las cuentas de almacenamiento, se recomienda usar un identificador de región en el nombre descriptivo para ayudar a garantizar que la región sea coherente en todos los componentes del sistema. Para un dispositivo virtual, es importante que todos los componentes estén en la misma región para evitar problemas de rendimiento.

Para conocer el procedimiento paso a paso, vaya a [Agregar una cuenta de almacenamiento](storsimple-manage-storage-accounts.md#add-a-storage-account).

### Desactivación de un dispositivo virtual de StorSimple

Desactivar un dispositivo virtual elimina la máquina virtual y los recursos que se crean cuando se aprovisionaron los servicios. Después de desactivar el dispositivo virtual, no se puede restaurar a su estado anterior. Antes de desactivar el dispositivo virtual, asegúrese de detener o eliminar clientes y hosts que dependen de él.

Desactivar un dispositivo virtual genera las siguientes acciones:

- Se quita el dispositivo virtual.

- Se quitan los discos del sistema operativo y de los datos creados para el dispositivo virtual.

- Se mantienen el servicio hospedado y la red virtual creados durante el aprovisionamiento. Si no los utiliza, debe eliminarlos manualmente.

- Se conservan las instantáneas de nube creadas para el dispositivo virtual.

Para obtener un procedimiento paso a paso, consulte [Desactivación y eliminación de un dispositivo de StorSimple](storsimple-deactivate-and-delete-device.md).

Tan pronto como se muestre el dispositivo como desactivado en la página de servicio del Administrador de StorSimple, puede eliminar el dispositivo virtual de la lista en la página **Dispositivos**.


### Inicio, detención y reinicio de un dispositivo virtual
A diferencia del dispositivo físico de StorSimple, en un dispositivo virtual de StorSimple no hay botón de encendido o apagado. Sin embargo, puede haber ocasiones en las que necesite detener y reiniciar el dispositivo virtual. Por ejemplo, algunas actualizaciones pueden requerir que se reinicie la máquina virtual para finalizar el proceso de actualización. La manera más fácil para iniciar, detener y reiniciar un dispositivo virtual es utilizar la consola de administración de máquinas virtuales.

Cuando observa la consola de administración, el estado del dispositivo virtual es **En ejecución** porque se inicia de forma predeterminada después de crearlo. Puede iniciar, detener y reiniciar una máquina virtual en cualquier momento.

[AZURE.INCLUDE [Inicio, detención y reinicio de un dispositivo virtual](../../includes/storsimple-stop-restart-virtual-device.md)]

### Restablecimiento de los valores predeterminados de fábrica

Si decide que desea empezar de nuevo con su dispositivo virtual, simplemente desactívelo y elimínelo y, a continuación, cree uno nuevo. Al igual que cuando se restablece el dispositivo físico, el dispositivo virtual nuevo no tendrá las actualizaciones instaladas; por lo tanto, asegúrese de comprobar si hay actualizaciones antes de usarlo.


## Conmutación por error en el dispositivo virtual

La recuperación ante desastres (DR) es uno de los escenarios clave para los que se diseñó el dispositivo virtual de StorSimple. En este escenario, es posible que el dispositivo físico de StorSimple o todo el centro de datos no esté disponible. Afortunadamente, puede usar un dispositivo virtual para restaurar las operaciones en una ubicación alternativa. Durante la recuperación ante desastres, los contenedores de volúmenes del dispositivo de origen cambia la propiedad y se transfieren al dispositivo virtual. Los requisitos previos para la recuperación ante desastres son que haya creado y configurado el dispositivo virtual, todos los volúmenes en el contenedor de volúmenes se hayan desconectado y el contenedor de volúmenes tenga asociada una instantánea en la nube.

>[AZURE.NOTE] 
>
> - Al utilizar un dispositivo virtual como el dispositivo secundario para recuperación ante desastres, tenga en cuenta que el 8010 tiene 30 TB de almacenamiento estándar y 8020 tiene 64 TB de almacenamiento premium. La mayor capacidad del 8020 puede resultar más adecuada para un escenario de recuperación ante desastres.
> - No se puede efectuar la conmutación por error ni clonar desde un dispositivo que ejecute la actualización 2 a un dispositivo que ejecute el software anterior a la actualización 1. Sin embargo puede conmutar por error un dispositivo que ejecuta la actualización 2 a un dispositivo que ejecuta la actualización 1 (1.1 o 1.2)

Para obtener un procedimiento paso a paso, vaya a [Conmutación por error y recuperación ante desastres para el dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## Cerrar o eliminar el dispositivo virtual

Si ha configurado y usado previamente un dispositivo virtual de StorSimple, pero ahora desea detener la acumulación de cargos para su uso por el proceso, puede apagar el dispositivo virtual. Apagar el dispositivo virtual no elimina su sistema operativo ni los discos de datos del almacenamiento. Detiene el cargo acumulado en su suscripción, pero seguirán los cargos de almacenamiento del sistema operativo y los discos de datos.

Si elimina o apaga el dispositivo virtual, aparecerá como **Desconectado** en la página de dispositivos del servicio StorSimple Manager. Puede desactivar o eliminar el dispositivo si desea eliminar las copias de seguridad creadas por el dispositivo virtual. Para más información, consulte [Desactivación y eliminación de un dispositivo de StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Apagado de un dispositivo virtual](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Eliminación de un dispositivo virtual](../../includes/storsimple-delete-virtual-device.md)]

   

## Pasos siguientes

- Aprenda a [usar el servicio StorSimple Manager para administrar un dispositivo virtual](storsimple-manager-service-administration.md).
 
- Sepa cómo [restaurar un volumen de StorSimple de un conjunto de copias de seguridad](storsimple-restore-from-backup-set.md).

<!---HONumber=AcomDC_0302_2016-->