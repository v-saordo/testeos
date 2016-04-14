<properties pageTitle="Replicación de máquinas virtuales de VMware y servidores físicos en Azure Site Recovery (heredada) | Microsoft Azure" description="Describe una implementación heredada de configuración de Azure Site Recovery para orquestar la replicación, conmutación por error y recuperación de máquinas virtuales de VMware locales y servidores físicos de Windows/Linux en Azure." " services="site-recovery" documentationCenter="" authors="rayne-wiselman" manager="jwhit" editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="raynew"/>

# Replicación de máquinas virtuales de VMware y servidores físicos en Azure Site Recovery (heredada)

> [AZURE.SELECTOR]
- [Mejorada](site-recovery-vmware-to-azure-classic.md)
- [Heredado](site-recovery-vmware-to-azure-classic-legacy.md)


El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para ver una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

## Información general

En este artículo se describe cómo:

- **Replicar máquinas virtuales de VMware en Azure**: implemente Site Recovery para coordinar la replicación, la conmutación por error y la recuperación de máquinas virtuales locales de VMware en Almacenamiento de Azure.
- **Replicar servidores físicos en Azure**: implemente Azure Site Recovery para coordinar la replicación, la conmutación por error y la recuperación de servidores físicos locales de Windows y Linux en Azure.

>[AZURE.NOTE] El escenario descrito en este artículo incluye **instrucciones heredadas**. No siga este artículo para nuevas implementaciones. En su lugar, utilice las instrucciones de [implementación mejorada](site-recovery-vmware-to-azure-classic.md) para el Portal clásico. Si ya ha realizado la implementación mediante el método descrito en este artículo, le recomendamos que migre a la nueva versión tal y como se describe a continuación.


## Migración a la implementación mejorada

Esta sección solo es relevante si ya ha implementado la replicación de máquinas virtuales de VMware o de servidores físicos de Windows/Linux en Azure siguiendo las instrucciones de este artículo.

Para migrar la implementación existente, necesitará:

1. Implementar nuevos componentes de Site Recovery en el sitio local.
2. Configurar las credenciales para el servidor vCenter y los equipos de origen en el nuevo servidor de configuración.
3. Detectar el servidor vCenter existente con el nuevo servidor de configuración.
3. Crear un nuevo grupo de protección con el nuevo servidor de configuración.


Antes de comenzar, tenga en cuenta que:

- Se recomienda que planee una ventana de mantenimiento para la migración a la implementación mejorada.
- La opción **Migrar máquinas** estará disponible solo si tiene grupos de protección existentes que se crearon durante una implementación heredada.
- Una vez completados los pasos de la migración, puede tardar 15 minutos o más para actualizar las credenciales y para detectar y actualizar las máquinas virtuales de modo que pueda agregarlas a un grupo de protección. Puede actualizar manualmente en lugar de esperar. 

Realice la migración de la siguiente forma:

1. Lea acerca de las [características mejoradas](site-recovery-vmware-to-azure-classic.md#enhanced-deployment), asegúrese de que entiende la nueva [arquitectura](site-recovery-vmware-to-azure-classic.md#scenario-architecture) y compruebe los [requisitos previos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) para la implementación mejorada.
2. Desinstale el servicio de movilidad de las máquinas que está protegiendo actualmente. Cuando agregue las máquinas a un grupo de protección nuevo, se instalará en ellas una nueva versión del servicio de movilidad.
3. Obtenga una [clave de registro del almacén](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) y [ejecute el Asistente para la instalación unificada](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) para instalar el servidor de configuración, el servidor de procesos y los componentes de servidor de destino maestro en el servidor de administración. Más información sobre el [planeamiento de capacidad](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. Si tiene un servidor VMware vCenter, [configure las credenciales](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) para tener acceso a él de modo que Site Recovery pueda detectar automáticamente las VM que administra. Más información sobre los [permisos necesarios](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Agregue [servidores vCenter o hosts ESXi](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). El portal puede tardar hasta 15 minutos en actualizarse para que aparezcan las credenciales.
6. Cree un [nuevo grupo de protección](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). El portal puede tardar hasta 15 minutos en actualizarse y que se detecten y aparezcan las máquinas virtuales. Si no quiere esperar, puede resaltar el nombre del servidor de administración (no haga clic en él) > **Actualizar**.
7. En el nuevo grupo de protección, haga clic en **Migrar máquinas**.

	![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. En **Seleccionar máquinas**, seleccione el grupo de protección desde el que quiere migrar y las máquinas que se migrarán.

	![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)
9. En **Configurar ajustes de destino**, especifique si desea utilizar la misma configuración para todas las máquinas y seleccione el servidor de proceso y la cuenta de almacenamiento de Azure. Si ha configurado un único servidor de administración, el servidor de procesos será la dirección IP de dicho servidor de administración.

	![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

10. En **Especificar cuentas**, seleccione la cuenta que creó para insertar automáticamente la nueva versión de Mobility Service en las máquinas protegidas.

	![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Site Recovery migrará los datos replicados a la cuenta de almacenamiento de Azure que proporcionó. Opcionalmente, puede volver a utilizar la cuenta de almacenamiento que empleó en la implementación heredada.
12. Una vez finalizado el trabajo, se sincronizarán automáticamente las máquinas virtuales. Una vez completada la sincronización, puede eliminar las máquinas virtuales del grupo de protección heredado.
13. Una vez se han migrado todos los equipos, puede eliminar el grupo de protección heredado.
14. Recuerde especificar las propiedades de conmutación por error para las máquinas y la configuración de red de Azure una vez completada la sincronización.
15. Si ya dispone de planes de recuperación, puede migrarlos a la implementación mejorada mediante la opción **Migrar plan de recuperación**. Solo debe hacerlo después de que se hayan migrado todas las máquinas protegidas. 

	![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Una vez completados los pasos de migración debe continuar con el [artículo mejorado](site-recovery-vmware-to-azure-classic.md). Después de la migración, el resto de este artículo heredado ya no es relevante y no necesita seguir los pasos descritos en él**.




## ¿Qué necesito?

En este diagrama se muestran los componentes de implementación.

![Almacén nuevo](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Esto es lo que necesita:

**Componente** | **Implementación** | **Detalles**
--- | --- | ---
**Servidor de configuración** | <p>Se implementa como una máquina virtual A3 estándar de Azure en la misma suscripción de Azure que Site Recovery.</p> <p>Configura en el portal de Site Recovery</p> | Este servidor coordina la comunicación entre máquinas protegidas, el servidor de proceso y los servidores de destino principales en Azure. Configura la replicación y coordina la recuperación en Azure cuando se produce la conmutación por error.
**Servidor de destino principal** | <p>Se implementa como una máquina virtual de Azure, como un servidor de Windows basado en una galería de imágenes de Windows Server 2012 R2 (para proteger los equipos de Windows) o como un servidor Linux basado en una galería de imágenes de OpenLogic CentOS 6.6 (para proteger equipos Linux).</p> <p>Tres opciones de ajuste de tamaño disponibles: A4 estándar, D14 estándar y DS4 estándar.<p><p>El servidor está conectado a la misma red de Azure que el servidor de configuración.</p><p>Configura en el portal de Site Recovery</p> | <p>Recibe y retiene los datos replicados de los equipos protegidos con VHD adjuntos creados en el almacenamiento de blobs de su cuenta de almacenamiento de Azure.</p> <p>Seleccione DS4 estándar específicamente para configurar la protección para cargas de trabajo que requieren alto rendimiento y baja latencia coherentes mediante la cuenta de almacenamiento premium.</p>
**Servidor de proceso** | <p>Se implementa como un servidor virtual o físico local que ejecuta Windows Server 2012 R2</p> <p>Recomendamos colocarlo en la misma red y el segmento de LAN que los equipos que desea proteger, pero se puede ejecutar en una red diferente mientras los equipos protegidos tengan visibilidad de red L3.<p>Lo configura y registra en el servidor de configuración en el portal de Site Recovery.</p> | <p>Los equipos protegidos envían datos de replicación al servidor de proceso local. Dispone de una caché basada en disco para almacenar en caché los datos de replicación que recibe. Realiza una serie de acciones en los datos.</p><p>Optimiza los datos mediante el almacenamiento en caché, la compresión y el cifrado antes de enviarlos al servidor de destino principal.</p><p>Controla la instalación de inserción de Mobility Service.</p><p>Realiza la detección automática de máquinas virtuales de VMware.</p>
**Máquinas locales** | Máquinas virtuales locales que se ejecutan en un hipervisor de VMware o servidores físicos con Windows o Linux. | Establece las opciones de replicación que se aplican a las máquinas virtuales y los servidores. Puede crear una conmutación por error en una máquina individual o, más generalmente, como parte de un plan de recuperación que contenga varias máquinas virtuales que conmutan por error juntas.
**Servicio de movilidad** | <p>Se instala en cada máquina virtual o en un servidor físico que desea proteger</p><p>Puede instalarse manualmente o insertar e instalar automáticamente mediante el servidor de procesos cuando la protección esté habilitada para el servidor. | El servicio de movilidad envía datos al servidor de procesos como parte de la replicación inicial (resincronización). Cuando el servidor llega a un estado protegido (después de completarse la resincronización) el servicio de movilidad realiza una captura en memoria de las escrituras en disco y la envía al servidor de procesos. La coherencia de las aplicaciones para servidores Windows se logra mediante el marco de VSS.
**Almacén de Azure Site Recovery** | Se configura una vez que se haya suscrito al servicio de Site Recovery. | Los servidores se registran en un almacén de Site Recovery. El almacén coordina y organiza la réplica de datos, la conmutación por error y la recuperación entre el sitio local y Azure.
**Mecanismo de replicación** | <p>**A través de Internet**: se comunican y replican datos de servidores protegidos locales y Azure usando un canal de comunicación SSL/TLS seguro a través de una conexión a Internet pública. Se trata de la opción predeterminada.</p><p>**VPN/ExpressRoute**: se comunican y replican datos entre servidores locales y Azure a través de una conexión VPN. Deberá configurar una VPN de sitio a sitio o una conexión ExpressRoute entre el sitio local y la red de Azure.</p><p>Seleccione cómo quiere realizar la replicación durante la implementación de Site Recovery. No se puede cambiar el mecanismo después de configurarlo sin afectar a la protección de los servidores que ya están protegidos.| <p>Ninguna opción requiere que se abra ningún puerto de red entrante en las máquinas protegidas. Todas las comunicaciones de red se inician en el sitio local.</p> 

## Planificación de capacidad

Ámbitos principales que hay que considerar:

- **Entorno de origen**: la infraestructura, la configuración de la máquina de origen y los requisitos de VMware.
- **Servidores de componentes**: el servidor de procesos, el servidor de configuración y el servidor de destino principal. 

### Consideraciones para el entorno de origen

- **Tamaño máximo de disco**: el tamaño máximo actual del disco que se puede adjuntar a una máquina virtual es 1 TB. Por lo tanto, el tamaño máximo de un disco de origen que se puede replicar también se limita a 1 TB.
- **Tamaño máximo por origen**: el tamaño máximo de una máquina de origen único es 31 TB (con 31 discos) y con una instancia de D14 aprovisionada para el servidor de destino principal. 
- **Número de orígenes por servidor de destino principal**: se pueden proteger varias máquinas de origen con un único servidor de destino principal. No obstante, no se puede proteger una única máquina de origen en varios servidores de destino principales porque cuando se replican los discos, se crea un disco duro virtual que refleja el tamaño del disco en el almacenamiento de blobs de Azure y se adjunta como un disco de datos al servidor de destino principal.  
- **Frecuencia de cambio diaria máxima por origen**: hay tres factores que hay que tener en cuenta al considerar la frecuencia de cambio recomendada por origen. Para las consideraciones basadas en destino, se necesitan dos E/S por segundo en el disco de destino para cada operación en el origen. Esto se debe a que se producirá una lectura de datos anteriores y una escritura de datos nuevos en el disco de destino. 
	- **Frecuencia de cambio diaria admitida por el servidor de procesos**: una máquina de origen no puede abarcar varios servidores de procesos. Un servidor de un solo proceso puede admitir hasta 1 TB de frecuencia de cambio diaria. Por lo tanto, 1 TB es la frecuencia de cambio de datos diaria máxima admitida para una máquina de origen. 
	- **Rendimiento máximo compatible con el disco de destino**: la renovación máxima por disco de origen no puede ser superior a 144 GB/día (con tamaño de escritura de 8K). Consulte la tabla en la sección de destino principal para el rendimiento y la E/S por segundo del destino para distintos tamaños de escritura. Este número debe dividirse entre dos porque cada E/S por segundo de origen genera 2 E/S por segundo en el disco de destino. Conozca los [objetivos de escalabilidad y rendimiento de Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) al configurar el destino para las cuentas de almacenamiento premium.
	- **Rendimiento máximo admitido por la cuenta de almacenamiento**: un origen no puede abarcar varias cuentas de almacenamiento. Dado que una cuenta de almacenamiento tiene un máximo de 20.000 solicitudes por segundo y que cada E/S por segundo de origen genera 2 E/S por segundo en el servidor de destino principal, se recomienda mantener el número de E/S por segundo en el origen a 10.000. Conozca los [objetivos de escalabilidad y rendimiento de Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) al configurar el origen para las cuentas de almacenamiento premium.

### Consideraciones para servidores de componentes

En la tabla 1 se resumen los tamaños de máquina virtual para la configuración y los servidores de destino principales.

**Componente** | **Instancias de Azure implementadas** | **Núcleos** | **Memoria** | **Discos máx.** | **Tamaño del disco**
--- | --- | --- | --- | --- | ---
Servidor de configuración | A3 estándar | 4 | 7 GB | 8 | 1023 GB
Servidor de destino principal | A4 estándar | 8 | 14 GB | 16 | 1023 GB
 | D14 estándar | 16 | 112 GB | 32 | 1023 GB
 | DS4 estándar | 8 | 28 GB | 16 | 1023 GB

**Tabla 1**

#### Consideraciones acerca del servidor de procesos

Por lo general, el tamaño del servidor de procesos depende de la frecuencia de cambios diaria a través de todas las cargas de trabajo protegidas. Las consideraciones principales incluyen:

-	Necesita cálculo suficiente para realizar tareas tales como el cifrado y la compresión en línea.
-	El servidor de procesos utiliza la caché basada en disco. Asegúrese de que el espacio de memoria caché recomendado y el rendimiento de disco están disponibles para facilitar los cambios de datos almacenados en el caso de interrupción o cuello de botella de red. 
-	Asegúrese de que hay ancho de banda suficiente para que el servidor de proceso pueda cargar los datos en el servidor de destino principal para proporcionar una protección continua de datos. 

En la tabla 2 se proporciona un resumen de las instrucciones del servidor de proceso.

**Frecuencia de cambio de datos** | **CPU** | **Memoria** | **Tamaño del disco de caché**| **Rendimiento del disco de caché** | **Entrada/salida de ancho de banda**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 vCPU (2 sockets * 2 núcleos @ 2,5 GHz) | 4 GB | 600 GB | 7 a 10 MB por segundo | 30 Mbps/21 Mbps
300 a 600 GB | 8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz) | 6 GB | 600 GB | 11 a 15 MB por segundo | 60 Mbps/42 Mbps
600 GB a 1 TB | 12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz) | 8 GB | 600 GB | 16 a 20 MB por segundo | 100 Mbps/70 Mbps
> 1 TB | Implementar otro servidor de procesos | | | | 

**Tabla 2**

Donde:

- Entrada es el ancho de banda de descarga (intranet entre el servidor de origen y de procesos).
- Salida es el ancho de banda de carga (Internet entre el servidor de procesos y el servidor de destino principal). Los números de salida suponen una compresión promedio de servidor de procesos del 30%.
- Para la caché de disco, se recomienda un disco de sistema operativo independiente de 128 GB mínimo para todos los servidores de procesos.
- Para el rendimiento de disco de memoria caché, el almacenamiento siguiente se usó para pruebas comparativas: 8 unidades SAS de 10 K RPM con configuración RAID 10.

#### Consideraciones del servidor de configuración

Cada servidor de configuración puede admitir hasta 100 máquinas de origen 3 y 4 volúmenes. Si se superan estos números se recomienda implementar otro servidor de configuración. Consulte la tabla 1 para conocer las propiedades de la máquina virtual predeterminadas del servidor de configuración.

#### Consideraciones de la cuenta de almacenamiento y el servidor de destino principal

El almacenamiento para cada servidor de destino principal se compone de un disco del sistema operativo, un volumen de retención y discos de datos. La unidad de retención mantiene el diario de cambios del disco para la duración de la ventana de retención definida en el portal de Site Recovery. Consulte la tabla 1 para conocer las propiedades de la máquina virtual del servidor de destino principal. En la tabla 3 se muestra cómo se utilizan los discos de A4.

**Instancia** | **Disco del sistema operativo** | **Retención** | **Discos de datos**
--- | --- | --- | ---
 | | **Retención** | **Discos de datos**
A4 estándar | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (15 * 1023 GB)
D14 estándar | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 31 discos (15 * 1023 GB)
DS4 estándar | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (15 * 1023 GB)

**Tabla 3**

La planificación de la capacidad del servidor de destino principal depende de:

- Las limitaciones y el rendimiento de almacenamiento de Azure
	- El número máximo de discos muy usados para una máquina virtual de nivel estándar, es de aproximadamente 40 (20.000/500 IOPS por disco) en una única cuenta de almacenamiento. Conozca los [objetivos de escalabilidad de las cuentas de almacenamiento estándar](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) y de las [cuentas de almacenamiento premium](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-	Frecuencia de cambio de datos 
-	Almacenamiento de volúmenes de retención.

Observe lo siguiente:

- Un origen no puede abarcar varias cuentas de almacenamiento. Esto se aplica al disco de datos va a las cuentas de almacenamiento seleccionadas al configurar la protección. El sistema operativo y los discos de retención normalmente van a la cuenta de almacenamiento implementada automáticamente.
- El volumen de almacenamiento de retención necesario depende de la frecuencia de cambios diaria y el número de días de retención. El almacenamiento de retención requerido por el servidor de destino principal = renovación total del origen al día * número de días de retención. 
- Cada servidor de destino principal tiene solo un volumen de retención. El volumen de retención se comparte entre los discos conectados al servidor de destino principal. Por ejemplo:
	- Si hay una máquina de origen con 5 discos y cada disco genera 120 E/S por segundo (tamaño de 8 KB) en el origen, esto se traduce en 240 E/S por segundo por disco (dos operaciones en el disco de destino por cada E/S de origen). 240 E/S por segundo se encuentran dentro de Azure por límite de E/S por segundo de disco de 500.
	- En el volumen de retención, esto se convierte en 120 * 5 = 600 E/S por segundo y esto puede convertirse en un cuello de botella. En este escenario, una buena estrategia sería agregar más discos al volumen de retención y distribuirlos, como una configuración de bandas RAID. Esto mejorará el rendimiento porque las E/S por segundo se distribuyen en varias unidades. El número de unidades que se agregarán al volumen de retención será el siguiente:
		- E/S por segundo total del entorno de origen / 500
		- Renovación total por día desde el entorno de origen (sin comprimir) / 287 GB. 287 GB es el rendimiento máximo compatible con un disco de destino por día. Esta métrica variará según el tamaño de escritura con un factor de 8K, porque en este caso, 8K es el tamaño de escritura asumido. Por ejemplo, si el tamaño de escritura es 4K, el rendimiento será 287/2. Y, si el tamaño de escritura es 16K, el rendimiento será 287*2.
- El número de cuentas de almacenamiento necesarias = E/S por segundo de origen total/10.000.


## Antes de comenzar

**Componente** | **Requisitos** | **Detalles**
--- | --- | --- 
**Cuenta de Azure** | Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](pricing/free-trial/).
**Almacenamiento de Azure** | <p>Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados</p><p>La cuenta debe ser una [cuenta con almacenamiento con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) o bien [una cuenta de almacenamiento premium](../storage/storage-premium-storage.md).</p><p>Debe estar en la misma región que el servicio Azure Site Recovery y asociarse a la misma suscripción.</p><p>Para obtener más información, consulte [Introducción al almacenamiento de Microsoft Azure](../storage/storage-introduction.md)</p>
**Red virtual de Azure** | Necesitará una red virtual en la que se implementarán el servidor de configuración y el servidor de destino principal. Debe estar en la misma región y suscripción que el almacén de Azure Site Recovery. Si desea replicar datos a través de una conexión VPN o ExpressRoute, la red virtual de Azure debe estar conectada a su red local a través de una conexión de ExpressRoute o una VPN de sitio a sitio.
**Recursos de Azure** | Asegúrese de que tiene recursos de Azure suficientes para implementar todos los componentes. Obtenga más información en [Límites de suscripción de Azure](../azure-subscription-service-limits.md).
**Máquinas virtuales de Azure** | <p>Las máquinas virtuales que desea proteger deben cumplir los [requisitos previos de Azure](site-recovery-best-practices.md).</p><p>**Recuento de disco**: se pueden admitir un máximo de 31 discos en un único servidor protegido</p><p>**Tamaños de disco**: la capacidad de disco individual no debe superar 1023 GB</p><p>**Agrupación en clústeres**: no se admiten servidores de clúster</p><p>**Arranque**: el arranque Unified Extensible Firmware Interface(UEFI)/Extensible Firmware Interface(EFI) no es compatible</p><p>**Volúmenes**: los volúmenes cifrados de Bitlocker no se admiten</p><p> **Nombres de servidor**: los nombres deben contener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. Después de proteger un equipo, puede modificar el nombre de Azure.</p>
**Servidor de configuración** | <p>Se crea en su suscripción una máquina virtual A3 estándar basada en una imagen de la galería de Windows Server 2012 R2 de Azure Site Recovery para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio en la nube. Si selecciona Internet pública como el tipo de conectividad para el servidor de configuración, el servicio en la nube se creará con una dirección IP pública reservada.</p><p>La ruta de instalación debe tener solo caracteres ingleses.</p>
**Servidor de destino principal** | <p>Máquina virtual de Azure A4, D14 o DS4 estándar.</p><p>La ruta de instalación solo debe contener caracteres en inglés. Por ejemplo, la ruta debe ser **/usr/local/ASR** para un servidor de destino principal que ejecuta Linux.</p></p>
**Servidor de proceso** | <p>Puede implementar el servidor de proceso en la máquina virtual o física que ejecuta Windows Server 2012 R2 con las actualizaciones más recientes. Instale en C:/.</p><p>Se recomienda que ubique el servidor en la misma red y subred que los equipos que desea proteger.</p><p>Instale VMware vSphere CLI 5.5.0 en el servidor de procesos. El componente de VMware vSphere CLI es necesario en el servidor de procesos para descubrir máquinas virtuales administradas por un servidor vCenter, o máquinas virtuales que se ejecutan en un host ESXi.</p><p>La ruta de instalación solo debe contener caracteres del alfabeto inglés.</p><p>No existe compatibilidad para el sistema de archivos ReFS.</p>
**VMware** | <p>Un servidor VMWare vCenter que administra los hipervisores de VMware vSphere. Se debe ejecutar vCenter versión 5.1 o 5.5 con las actualizaciones más recientes.</p><p>Uno o varios de los hipervisores de vSphere que contienen máquinas virtuales de VMware que desea proteger. El hipervisor debe ejecutar ESX/ESXi versión 5.1 o 5.5 con las actualizaciones más recientes.</p><p>Las máquinas virtuales de VMware deben tener las herramientas de VMware instaladas y en ejecución.</p>  
**Máquinas de Windows** | <p>Los servidores físicos protegidos o las máquinas virtuales de VMware que ejecutan Windows tienen una serie de requisitos.</p><p>Un sistema operativo compatible de 64 bits: **Windows Server 2012 R2**, **Windows Server 2012** o **Windows Server 2008 R2 con al menos SP1**.</p><p>El nombre de host, los puntos de montaje, los nombres de dispositivo, la ruta de acceso de sistema de Windows (p. ej.: C:\\Windows) deben estar en inglés solamente.</p><p>El sistema operativo debe instalarse en la unidad C:\\.</p><p>Solo los discos básicos son compatibles. No se admiten los discos dinámicos.</p><p><Firewall rules on protected machines should allow them to reach the configuration and master target servers in Azure.p><p>Tendrá que proporcionar una cuenta de administrador (debe ser un administrador local en el equipo de Windows) para la instalación de inserción de Mobility Service en los servidores de Windows. Si la cuenta proporcionada no es una cuenta de dominio, deberá deshabilitar el control de acceso de usuarios remotos en el equipo local. Para ello, agregue la entrada del Registro LocalAccountTokenFilterPolicy DWORD con un valor de 1 en HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Para agregar la entrada del Registro de un CLI, abra cmd o powershell y escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Más información](https://msdn.microsoft.com/library/aa826699.aspx) sobre el control de acceso.</p><p>Después de la conmutación por error, si desea conectarse a máquinas virtuales de Microsoft Azure con Escritorio remoto, asegúrese de que Escritorio remoto está habilitado en el equipo local. Si no se conecta a través de las reglas de firewall de VPN, debe permitir las conexiones de escritorio remoto a través de Internet.</p>
**Equipos Linux** | <p> Un sistema operativo de 64 bits admitido: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 ejecutando el kernel de Red Hat compatible o Unbreakable Enterprise Kernel Release 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.</p><p>Las reglas de firewall en equipos protegidos deben permitirles ponerse en contacto con los servidores de configuración y de destino principal en Azure.</p><p>Los archivos /etc/hosts en los equipos protegidos deben contener entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los NIC </p><p>Si desea conectarse a una máquina virtual de Azure con Linux después de la conmutación por error mediante un cliente Secure Shell (ssh), asegúrese de que el servicio de Secure Shell en el equipo protegido está configurado para iniciarse automáticamente al arrancar el sistema y que las reglas de firewall permiten una conexión ssh.</p><p>El nombre del host, los puntos de montaje, los nombres de dispositivo y las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/; /usr) deben estar en inglés solamente.</p><p>Se puede habilitar la protección para máquinas locales con el almacenamiento siguiente:-<br>Sistema de archivos: XT3, ETX4, ReiserFS, XFS<br>Software de múltiples rutas-Asignador de dispositivos (múltiples rutas)<br>Administrador de volúmenes: no se admiten servidores físicos<br>LVM2Physical con almacenamiento de controlador HP CCISS.</p>
**Terceros** | Algunos componentes de implementación de este escenario dependen de software de terceros para poder funcionar correctamente. Para obtener una lista completa, consulte [Avisos e información de software de terceros](#third-party).

## Implementación

El gráfico resume los pasos de implementación.

![Pasos de implementación](./media/site-recovery-vmware-to-azure-classic-legacy/deployment-steps.png)

## Conectividad de red

Tiene dos opciones para configurar la conectividad de red entre el sitio local y la red virtual de Azure en la que se implementan los componentes de infraestructura (servidor de configuración, servidores de destino maestros). Deberá decidir qué opción de conectividad de red usar para poder implementar su servidor de configuración. Debe elegir esta opción en el momento de la implementación. No puede cambiarse más adelante.

**Internet pública:** la comunicación y la replicación de datos entre los servidores locales (servidor de procesos, máquinas protegidas) y los servidores de componentes de infraestructura de Azure (servidor de configuración, servidor de destino maestro) se realiza mediante una conexión SSL/TLS segura entre el entorno local y los puntos de conexión públicos de los servidores de configuración y de destino maestros. (La única excepción es la conexión entre el servidor de procesos y el servidor de destino maestro en el puerto TCP 9080 que es sin cifrar. Solo se intercambia en esta conexión la información de control relacionada con el protocolo de replicación usado para configurar la replicación.)

![Diagrama de implementación Internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN:** la comunicación y la replicación de datos entre los servidores locales (servidor de procesos, máquinas protegidas) y los servidores de componentes de infraestructura de Azure (servidor de configuración, servidor de destino maestro) se realiza mediante una conexión VPN entre su red local y la red virtual de Azure en la que están implementados el servidor de configuración y los servidores de destino maestros. Asegúrese de que su red local esté conectada a la red virtual de Azure mediante una conexión de ExpressRoute o una conexión VPN de sitio a sitio.

![Diagrama de implementación VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## Paso 1: Creación de un almacén

1. Inicie sesión en el [Portal de administración](https://portal.azure.com).


2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Almacén de Site Recovery**.


3. Haga clic en **Crear nuevo** > **Creación rápida**.

4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.

5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](pricing/details/site-recovery/)

6. Haga clic en **Crear almacén**.

	![Almacén nuevo](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de **Servicios de recuperación**.

## Paso 2: Implementación de un servidor de configuración

### Configuración del servidor

1. En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página Inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Icono de inicio rápido](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. En la lista desplegable, seleccione **Entre un sitio local con VMware/servidores físicos y Azure**.
3. En **Preparar los recursos de destino (Azure)**, haga clic en **Implementar el servidor de configuración**.

	![Implementación de un servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. En **Nuevos detalles del servidor de configuración**, especifique:

	- Un nombre del servidor de configuración y las credenciales para conectarse a él.
	- En el desplegable de tipos de conectividad de red seleccione Internet pública o VPN.[AZURE.NOTE] Esta es una opción que se realiza en el tiempo de implementación y que no se podrá cambiar posteriormente.  
	- Seleccione la red en la que se debe encontrar el servidor. Si ha especificado VPN como el tipo de conectividad de red, asegúrese de que esta red virtual de Azure está conectada a su sitio local a través de una conexión de ExpressRoute o una VPN de sitio a sitio.
	- Especifique la dirección IP interna y la subred para asignarlas al servidor. Tenga en cuenta que las cuatro primeras direcciones IP en las subredes están reservadas para uso interno de Azure. Utilice cualquier dirección IP disponible.
	
	![Implementación de un servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Al hacer clic en **Aceptar** se crea en su suscripción una máquina virtual de A3 estándar basada en una imagen de la galería de Windows Server 2012 R2 de Azure Site Recovery para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio en la nube. Si ha especificado el tipo de conectividad de red para que sea Internet pública, se creará el servicio en la nube con una dirección IP pública reservada. Puede supervisar el progreso en la pestaña **Trabajos**.

	![Supervisión de progreso](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  **Este paso sólo es aplicable si el tipo de conectividad es Internet pública.** Después de implementar el servidor de configuración, tome nota de la dirección IP pública asignada a él en la página **Máquinas virtuales** del portal de Azure. Después en la pestaña **Puntos de conexión**, tome nota del puerto HTTPS asignado al puerto privado 443. Necesitará esta información más adelante al registrar el destino principal y los servidores de proceso con el servidor de configuración. El servidor de configuración se implementa con estos extremos:

	- HTTP: puerto público que se usa para coordinar la comunicación entre los servidores de componentes y Azure a través de Internet. El puerto privado 443 se usa para coordinar la comunicación entre los servidores de componentes y Azure a través de la VPN.
	- Personalizado: Puerto público se utiliza para la comunicación de la herramienta de conmutación por recuperación a través de la conexión de
	- Internet. El puerto privado 9443 se usa para la herramienta de conmutación por recuperación a través de una VPN.
	- PowerShell: puerto privado 5986
	- Escritorio remoto: puerto 3389 privado
	
	![Extremos de VM](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] No eliminar o cambiar el número de puerto público o privado de cualquiera de los extremos creados durante la implementación del servidor de configuración.

El servidor de configuración se implementa en un servicio en la nube de Azure creado automáticamente con una dirección IP reservada. Se necesita la dirección reservada para garantizar que la dirección IP del servicio en la nube del servidor de configuración es la misma al arrancar las máquinas virtuales (incluido el servidor de configuración) en el servicio en la nube. Es necesario anular manualmente la reserva de la dirección IP pública reservada cuando se retira el servidor de configuración o permanecerá reservada. Hay un límite predeterminado de 20 direcciones IP públicas reservadas por suscripción. [Más información](../virtual-network/virtual-networks-reserved-private-ip.md) sobre las direcciones IP reservadas.

### Registro del servidor de configuración en el almacén

1. En la página **Inicio rápido**, haga clic en **Preparar los recursos de destino** > **Descargar una clave de registro**. El archivo de clave se genera automáticamente. Es válido durante 5 días a partir del momento en que se genera. Copie el archivo en el servidor de configuración.
2. En **Máquinas virtuales** seleccione el servidor de configuración en la lista de máquinas virtuales. Abra la pestaña **Panel** y haga clic en **Conectar**. **Abra** el archivo RDP descargado para iniciar sesión en el servidor de configuración con Escritorio remoto. Si el servidor de configuración se implementa en una red VPN, use la dirección IP interna (la dirección IP que especificó cuando implementó el servidor de configuración y que también se puede ver en la página de panel de máquinas virtuales para la máquina virtual del servidor de configuración) del servidor de configuración para conectarse a él a través de Escritorio remoto desde la red local. Al iniciar sesión por primera vez, se ejecuta automáticamente el Asistente para la configuración del servidor de configuración de Azure Site Recovery.

	![Registro](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. En **Instalación de Software de terceros** haga clic en **Acepto** para descargar e instalar MySQL.

	![Instalación de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. En **Detalles de MySQL Server**, cree credenciales para iniciar sesión en la instancia del servidor MySQL.

	![Credenciales de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. En **Configuración de Internet**, especifique cómo el servidor de configuración se conectará a Internet. Observe lo siguiente:

	- Si desea utilizar un proxy personalizado, debe configurarlo antes de instalar el proveedor.
	- Si hace clic en **Siguiente**, se ejecutará una prueba para comprobar la conexión del proxy.
	- Si utiliza a un proxy personalizado o el proxy predeterminado requiere autenticación, tendrá que especificar los detalles del proxy, incluida la dirección, el puerto y las credenciales.
	- Las siguientes direcciones URL deben ser accesibles a través del proxy:
		- **.hypervrecoverymanager.windowsazure.com
		- **.accesscontrol.windows.net
		- **.backup.windowsazure.com
		- **.blob.core.windows.net
		- **.store.core.windows.net
	- Si dispone de reglas de firewall basadas en direcciones IP, asegúrese de que están configuradas para permitir la comunicación desde el servidor de configuración hasta las direcciones IP descritas en [Intervalos de direcciones IP de los centros de datos de Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) y el protocolo HTTPS (443). Tendrá que incluir en una lista blanca los intervalos de direcciones IP de la región de Azure que va a usar y los del Oeste de EE. UU.

	![Registro de proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. En **Configuración de localización de mensajes de error de proveedores**, especifique en qué idioma desea que aparezcan los mensajes de error.

	![Registro de mensajes de error](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. En la página **Registro de Azure Site Recovery**, busque el archivo de clave que copió en el servidor.

	![Registro de archivo de clave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. En la página de finalización del asistente, seleccione estas opciones:

	- Seleccione **Iniciar el cuadro de diálogo Administración de cuentas** para especificar que se debe abrir el cuadro de diálogo Administrar cuentas después de finalizar el asistente.
	- Seleccione **Crear un icono de escritorio para Cspsconfigtool** para agregar un acceso directo en el servidor de configuración para que pueda abrir el cuadro de diálogo **Administrar cuentas** en cualquier momento sin necesidad de volver a ejecutar el asistente.

	![Registro completado](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Haga clic en **Finalizar** para completar el asistente. Se genera una frase de contraseña. Cópiela en una ubicación segura. La necesitará para autenticarse y registrar los servidores de destino principales y de proceso con el servidor de configuración. También se utiliza para garantizar la integridad del canal en las comunicaciones del servidor de configuración. Puede volver a generar la frase de contraseña pero, a continuación, deberá volver a registrar el destino principal y los servidores de proceso mediante la nueva frase de contraseña.

	![Frase de contraseña](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Después de registrar el servidor de configuración, se mostrará en la página **Servidores de configuración** del almacén.

### Configuración y administración de cuentas

Durante la implementación , Site Recovery solicita credenciales para las siguientes acciones:

- Al agregar un servidor vCenter para la detección automatizada de las máquinas virtuales administradas por el servidor vCenter. Se requiere una cuenta de vCenter para la detección automática de las máquinas virtuales.
- Al agregar equipos para la protección, para que Site Recovery pueda instalar Mobility Service en ellos.

Después de que se haya registrado el servidor de configuración, puede abrir el cuadro de diálogo **Administrar cuentas** para agregar y administrar las cuentas que deben usarse para estas acciones. Hay un par de formas de hacerlo:

- Abra el acceso directo que se utilizó para crear el cuadro de diálogo en la última página del programa de instalación para el servidor de configuración (cspsconfigtool).
- Abra el cuadro de diálogo al finalizar la instalación del servidor de configuración.

1. En **Administrar cuentas**, haga clic en **Agregar cuenta**. También puede modificar y eliminar las cuentas existentes.

	![Administrar cuentas](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. En **Detalles de la cuenta**, especifique un nombre de cuenta para usarlo en Azure y credenciales (nombre de usuario/dominio).

	![Administrar cuentas](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### Conexión al servidor de configuración 

Hay dos maneras de conectarse al servidor de configuración:

- Mediante una conexión VPN de sitio a sitio o ExpressRoute
- Por Internet 

Observe lo siguiente:

- Una conexión a Internet utiliza los extremos de la máquina virtual junto con la dirección IP virtual pública del servidor.
- Una conexión VPN utiliza la dirección IP interna del servidor junto con los puertos privados del extremo.
- Es una decisión única decidir si desea conectarse (control y replicación de datos) de los servidores locales a los distintos servidores de componente (servidor de configuración, servidor de destino principal) que se ejecutan en Azure a través de una conexión VPN o de Internet. No puede cambiar esta configuración posteriormente. Si lo hace, necesitará volver a implementar el escenario y volver a proteger sus equipos.  


## Paso 3: Implementación del servidor de destino principal

1. En **Preparar recursos de destino (Azure)**, haga clic en **Implementar el servidor de destino maestro**.
2. Especifique las credenciales y los detalles del servidor de destino principal. El servidor se implementará en la misma red de Azure que el servidor de configuración al que se ha registrado. Al hacer clic para completar una máquina virtual de Azure, se creará con una imagen de galería de Windows o Linux.

	![Configuración de servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Tenga en cuenta que las cuatro primeras direcciones IP en las subredes están reservadas para uso interno de Azure. Especifique cualquier dirección IP disponible.

>[AZURE.NOTE] Seleccione DS4 estándar al configurar la protección para cargas de trabajo que requieren un alto rendimiento de E/S y latencia baja coherentes para hospedar cargas de trabajo intensivas de E/S mediante [cuenta de almacenamiento Premium](../storage/storage-premium-storage.md).


3. Se crea una máquina virtual de servidor de destino maestro de Windows con estos extremos (los extremos públicos solo se crean si el tipo de implementación es Internet pública):

	- Custom: el puerto público lo utiliza el servidor de procesos para enviar datos de replicación a través de Internet. El puerto privado 9443 lo utiliza el servidor de procesos para enviar datos de replicación al servidor de destino principal a través de VPN.
	- Custom1: el puerto público lo utiliza el servidor de procesos para enviar metadatos de control a través de Internet. El puerto privado 9080 lo utiliza el servidor de procesos para enviar metadatos de control al servidor de destino principal a través de VPN.
	- PowerShell: puerto privado 5986
	- Escritorio remoto: puerto 3389 privado

4. Se crea una máquina virtual de servidor de destino maestro de Linux con estos extremos (los extremos públicos solo se crean si el tipo de implementación es Internet pública):

	- Custom: el puerto público lo utiliza el servidor de procesos para enviar datos de replicación a través de Internet. El puerto privado 9443 lo utiliza el servidor de procesos para enviar datos de replicación al servidor de destino principal a través de VPN.
	- Custom1: el puerto público lo utiliza el servidor de procesos para enviar metadatos de control a través de Internet. El puerto privado 9080 lo utiliza el servidor de procesos para enviar datos de control al servidor de destino principal a través de VPN.
	- SSH: puerto privado 22

    >[AZURE.WARNING] No elimine ni cambie el número de puerto público o privado de cualquiera de los extremos creados durante la implementación del servidor de destino principal.

5. En **Máquinas virtuales**, espere a que se inicie la máquina virtual.

	- Si ha configurado el servidor con Windows, tome nota de los detalles del escritorio remoto.
	- Si lo ha configurado con Linux y se está conectando a través de VPN, anote la dirección IP interna de la máquina virtual. Si se conecta a través de Internet, anote la dirección IP pública.

6.  Inicie sesión en el servidor para completar la instalación y registrarlo con el servidor de configuración.
7.  Si está ejecutando Windows:

	1. Inicie una conexión de escritorio remoto con la máquina virtual. La primera vez que inicie sesión en un script se ejecutará en una ventana de PowerShell. No la cierre. Cuando haya terminado, se abre automáticamente la herramienta de configuración de agente de host para registrar el servidor.
	2. En **Configuración de agente de host** especifique la dirección IP interna del servidor de configuración y el puerto 443. Puede utilizar la dirección interna y el puerto privado 443, incluso si no se conecta en VPN porque la máquina virtual está conectada a la misma red de Azure que el servidor de configuración. Deje **Usar HTTPS** habilitado. Escriba la frase de contraseña del servidor de configuración que anotó anteriormente. Haga clic en **Aceptar** para registrar el servidor. Tenga en cuenta que puede ignorar las opciones de NAT en la página. No se usan.
	3. Si su requisito de unidad de retención estimada es superior a 1 TB, puede configurar el volumen de retención (R:) mediante un disco virtual y [espacios de almacenamiento](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
	
	![Servidor de destino principal de Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Si ejecuta Linux:
	1. Asegúrese de que ha instalado los Linux Integration Services (LIS) más recientes antes de instalar el software de servidor de destino maestro. Encontrará la versión más reciente de LIS junto con instrucciones sobre cómo instalar [aquí](https://www.microsoft.com/download/details.aspx?id=46842). Reinicie el equipo después de instalar LIS.
	2. En **Preparar los recursos de destino (Azure)**, haga clic en **Descargar e instalar software adicional (solo para el servidor de destino principal de Linux)** para descargar el paquete de servidor de destino principal de Linux. Copie el archivo tar descargado en la máquina virtual mediante un cliente sftp. También puede iniciar sesión en el servidor de destino principal de Linux implementado y usar wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* para descargar el archivo.
	2. Inicie sesión en el servidor mediante un cliente de Secure Shell. Tenga en cuenta que si está conectado a la red de Azure a través de VPN, utilice la dirección IP interna. En caso contrario, utilice la dirección IP externa y el extremo público SSH.
	3. Extraiga los archivos del instalador comprimidos con gzip ejecutando: **tar –xvzf Microsoft-ASR\_UA\_8.4.0.0\_RHEL6-64***  
	![Servidor de destino principal de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
	4. Asegúrese de que se encuentra en el directorio en el que extrajo el contenido del archivo tar.
	5. Copie la frase de contraseña del servidor de configuración en un archivo local con el comando **echo *`<passphrase>`* >passphrase.txt**
	6. Ejecute el comando “**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**”.

	![Registrar servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Espere unos minutos (de 10 a 15) y en la página **Servidores** > **Servidores de configuración** compruebe que el servidor de destino principal se muestra como registrado en la pestaña **Detalles del servidor**. Si ejecuta Linux y no se ha registrado, vuelva a ejecutar la herramienta de configuración de host en /usr/local/ASR/Vx/bin/hostconfigcli. Deberá establecer los permisos de acceso mediante la ejecución de chmod como raíz.

	![Verificar servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Tenga en cuenta que pueden pasar 15 minutos después de completar el registro para que el servidor de destino principal se muestre en el servidor de configuración. Para actualizar inmediatamente, actualice al servidor de configuración haciendo clic en el botón Actualizar en la parte inferior de la página de los servidores de configuración.

## Paso 4: Implementación de un servidor de proceso local

>[AZURE.NOTE] Se recomienda que configure una dirección IP estática en el servidor de proceso para garantizar la persistencia en los reinicios.

1. Haga clic en Inicio rápido > **Instalar el servidor de proceso local** > **Descargar e instalar el servidor de proceso**.

	![Instalar servidor de procesos](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copie el archivo comprimido descargado en el servidor en el que va a instalar al servidor de proceso. El archivo comprimido contiene dos archivos de instalación:

	- Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*
	- Microsoft-ASR\_CX\_8.4.0.0\_Windows*

3. Descomprima el archivo y copie los archivos de instalación en una ubicación en el servidor.
4. Ejecute el archivo de instalación **Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*** y siga las instrucciones. Se instalan los componentes de terceros necesarios para la implementación.
5. A continuación, ejecute **Microsoft-ASR\_CX\_8.4.0.0\_Windows***.
6. En la página **Modo servidor**, seleccione **Servidor de proceso**.
7. En la página **Detalles del entorno**, haga lo siguiente:


	- Si desea proteger las máquinas virtuales de VMware, haga clic en **Sí**
	- Si solo desea proteger servidores físicos y, por lo tanto, no necesita vCLI VMware instalado en el servidor de procesos. Haga clic en **No** y continúe.

8. Cuando se instala VMware vCLI, tenga en cuenta lo siguiente:

	- **Se admite solo VMware vSphere CLI 5.5.0**. El servidor de proceso no funciona con otras versiones o actualizaciones de vSphere CLI.
	- Descargue vSphere CLI 5.5.0 desde [aquí.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
	- Si instaló vSphere CLI justo antes de iniciar la instalación del servidor de proceso y el programa de instalación no lo encuentra, espere hasta cinco minutos antes de volver a intentar la instalación. Esto garantiza que todas las variables de entorno necesarias para la detección de vSphere CLI se han inicializado correctamente.

9.	En **Selección de NIC para el servidor de proceso**, seleccione el adaptador de red que debe usar el servidor de proceso.

	![Seleccionar adaptador](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10.	En **Detalles del servidor de configuración**:

	- Para la dirección IP y el puerto, si se conecta a través de VPN, especifique la dirección IP interna del servidor de configuración y 443 para el puerto. De lo contrario, especifique la dirección IP virtual pública y el extremo HTTP público asignado.
	- Escriba la frase de contraseña del servidor de configuración.
	- Desactive **Comprobar firma de software del servicio de movilidad** si desea deshabilitar la comprobación cuando utiliza la inserción automática para instalar el servicio. La comprobación de la firma necesita conectividad a Internet en el servidor de proceso.
	- Haga clic en **Siguiente**.

	![Registrar servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. En **Seleccionar unidad de instalación**, seleccione una unidad de caché. El servidor de proceso necesita una unidad de memoria caché con al menos 600 GB de espacio libre. Luego haga clic en **Instalar**.

	![Registrar servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Tenga en cuenta que debe reiniciar el equipo para completar la instalación. En **Servidor de configuración** > **Detalles del servidor**, compruebe que el servidor de proceso aparece y que está correctamente registrado en el almacén.

>[AZURE.NOTE]Pueden pasar 15 minutos después de completar el registro para que el servidor de proceso se muestre en el servidor de configuración. Para actualizar inmediatamente, actualice al servidor de configuración haciendo clic en el botón Actualizar en la parte inferior de la página del servidor de configuración.
 
![Validar servidor de procesos](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Si no deshabilita la comprobación de firmas para Mobility Service al registrar el servidor de proceso, puede hacerlo más adelante de la forma siguiente:

1. Inicie sesión en el servidor de proceso como administrador y abra el archivo C:\\pushinstallsvc\\pushinstaller.conf para modificarlo. En la sección **[PushInstaller.transport]** agregue esta línea: **SignatureVerificationChecks=”0”**. Guarde y cierre el archivo.
2. Reinicie el servicio InMage PushInstall.


## Paso 5: Instalar actualizaciones más recientes

Antes de continuar, asegúrese de que tiene instaladas las actualizaciones más recientes. Recuerde que debe instalar las actualizaciones en el orden siguiente:

1. Servidor de configuración
2. Servidor de proceso
3. Servidor de destino principal
4. Herramienta de conmutación por recuperación (vContinuum)

Puede obtener las actualizaciones en el **Panel ** de Site Recovery. Para la instalación de Linux, extraiga los archivos del instalador comprimidos con gzip y ejecute el comando “sudo ./install” para instalar la actualización

Descargue la actualización más reciente para la **herramienta de conmutación por recuperación (vContinuum)** desde [aquí](http://go.microsoft.com/fwlink/?LinkID=533813).

Si está ejecutando las máquinas virtuales o los servidores físicos que ya tienen instalado Mobility Service, puede obtener actualizaciones para el servicio como sigue:

- Descargar las actualizaciones para el servicio como sigue:
	- [Windows Server 64 (solo 64 bits)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
	- [CentOS 6.4, 6.5, 6.6 (solo 64 bits)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
	- [Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
	- [SUSE Linux Enterprise Server SP3 (solo 64 bits)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
- O bien, después de actualizar el servidor de proceso, puede obtener la versión actualizada de Mobility Service de la carpeta C:\\pushinstallsvc\\repository en el servidor de proceso.
- Si tiene una máquina ya protegida con una versión anterior del servicio de movilidad instalado, también podría actualizar automáticamente el servicio de movilidad en las máquinas protegidos desde el portal de administración. Para ello, seleccione el grupo de protección al que pertenece la máquina, resalte la máquina protegida y haga clic en el botón de servicio de movilidad de actualización de la parte inferior. El botón del servicio de movilidad de actualización solo se activará si hay una versión más reciente del servicio de movilidad. Asegúrese de que el servidor de procesos está ejecutando la versión más reciente del software del servidor de procesos antes de actualizar el servicio de movilidad. El servidor protegido tiene que cumplir todos los [requisitos previos de instalación de inserción automática](#install-the-mobility-service-automatically) para que el servicio de movilidad de actualización funcione.

![Seleccionar servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

En Seleccione cuentas especifique la cuenta de administrador que se usará para actualizar el servicio de movilidad en el servidor protegido. Haga clic en Aceptar y espere a que se complete el trabajo desencadenado.


## Paso 6: Agregar servidores vCenter o hosts ESXi

1. En la pestaña **Servidores** > **Servidores de configuración**, seleccione el servidor de configuración y haga clic en **AGREGAR SERVIDOR VCENTER** para agregar un servidor vCenter o un host ESXi.

	![Seleccionar servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Especifique los detalles para el servidor vCenter o el host ESXi y seleccione el servidor de proceso que se usará para detectarlo.

	- Si el servidor vCenter no se está ejecutando en el puerto 443 predeterminado, especifique el número de puerto en el que se ejecuta el servidor vCenter.
	- El servidor de proceso debe estar en la misma red que el servidor vCenter o el host ESXi y debe tener instalado VMware vSphere CLI 5.5.0.

	![Configuración de servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Una vez completada la detección del servidor de vCenter, se mostrará en los detalles de configuración del servidor.

	![Configuración de servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Si utiliza una cuenta sin privilegios de administrador para agregar el servidor vCenter o el host ESXi, asegúrese de que la cuenta tenga los privilegios siguientes:

	- Las cuentas de vCenter deben tener habilitados el centro de datos, el almacén de datos, la carpeta, el host, la red, recursos, vistas de almacenamiento, la máquina virtual y privilegios del conmutador distribuido de vSphere habilitados.
	- Las cuentas de host de ESXi deben tener habilitados el centro de datos, el almacén de datos, la carpeta, el host, la red, recursos, la máquina virtual y privilegios del conmutador distribuido de vSphere habilitados.



## Paso 7: Crear un grupo de protección

1. Abra **Elementos protegidos** > **Grupo de protección** y haga clic para agregar un grupo de protección.

	![Crear un grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. En la página **Especificar la configuración del grupo de protección**, especifique un nombre para el grupo y seleccione el servidor de configuración en el que desea crear el grupo.

	![Configuración del grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. En la página **Especificar valores de replicación**, configure las opciones de replicación que se usarán en todos los equipos del grupo.

	![Replicación del grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Configuración:
	- **Coherencia de múltiples máquinas virtuales**: si lo activa, se crean puntos de recuperación coherentes con las aplicaciones compartidas en los equipos del grupo de protección. Esta configuración es muy importante cuando todos los equipos del grupo de protección ejecutan la misma carga de trabajo. Se recuperarán todos los equipos en el mismo punto de datos. Solo disponible para servidores Windows Server.
	- **Umbral de RPO**: se generan alertas cuando el RPO de replicación de protección de datos continua supera el valor de umbral RPO configurado.
	- **Retención de punto de recuperación**: especifica el período de retención. Los equipos protegidos se pueden recuperar en cualquier punto dentro de este período.
	- **Frecuencia de la instantánea de coherencia de la aplicación**: especifica la frecuencia con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación.

Puede supervisar el grupo de protección, pues se crean en la página **Elementos protegidos**.

## Paso 8: Configurar equipos que desea proteger

Necesitará instalar Mobility Service en máquinas virtuales y servidores físicos que desea proteger. Puede hacerlo de dos maneras:

- Insertar e instalar automáticamente el servicio en cada máquina desde el servidor de proceso.
- Instalar el servicio manualmente. 

### Instalación automática de Mobility Service

Al agregar equipos a un grupo de protección, el servicio de movilidad se inserta automáticamente y el servidor de proceso lo instala en cada equipo.

**Instalación de inserción automática de Mobility Service en servidores Windows:**

1. Instale las actualizaciones más recientes para el servidor de procesos, como se describe en [Paso 5: Instalar actualizaciones más recientes](#step-5-install-latest-updates), y asegúrese de que el servidor de procesos está disponible. 
2. Asegúrese de que no hay conectividad de red entre el equipo de origen y el servidor de procesos, y que se puede tener acceso a la máquina de origen desde el servidor de procesos.  
3. Configure el firewall de Windows para permitir **Compartir archivos e impresoras** y el **Instrumental de administración de Windows**. En Configuración de Firewall de Windows, seleccione la opción "Permitir una aplicación o una característica a través del firewall" y seleccione las aplicaciones tal como se muestra en la figura siguiente. Para los equipos que pertenecen a un dominio puede configurar la directiva de firewall con un GPO.

	![Configuración de firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. La cuenta utilizada para realizar la instalación de inserción debe encontrarse en el grupo de administradores en el equipo que desea proteger. Estas credenciales se utilizan únicamente para la instalación de inserción de Mobility Service y podrá proporcionarlas al agregar un equipo a un grupo de protección.
5. Si la cuenta de administrador no es una cuenta de dominio, deberá deshabilitar el control de acceso de usuarios remotos en el equipo local. Para ello, agregue la entrada del Registro LocalAccountTokenFilterPolicy DWORD con un valor de 1 en HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Para agregar la entrada del Registro de un CLI, abra cmd o powershell y escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Instalación de inserción automática de Mobility Service en servidores Linux:**

1. Instale las actualizaciones más recientes para el servidor de procesos, como se describe en [Paso 5: Instalar actualizaciones más recientes](#step-5-install-latest-updates), y asegúrese de que el servidor de procesos está disponible.
2. Asegúrese de que no hay conectividad de red entre el equipo de origen y el servidor de procesos, y que se puede tener acceso a la máquina de origen desde el servidor de procesos.  
3. Asegúrese de que la cuenta es un usuario raíz en el servidor Linux de origen.
4. Asegúrese de que el archivo /etc/hosts del servidor Linux de origen contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todas las tarjetas NIC.
5. Instale los paquetes openssh, openssh-server, openssl más recientes en el equipo que desea proteger.
6. Asegúrese de que SSH está habilitado y ejecutándose en el puerto 22. 
7. Habilite la autenticación de la contraseña y del subsistema SFTP en el archivo sshd\_config: 

	- a) Inicie sesión como root.
	- b) En el archivo /etc/ssh/sshd\_config, busque la línea que comienza con **PasswordAuthentication**.
	- c) Quite el comentario de la línea y cambie el valor de “no” a “yes”.

		![Movilidad de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

	- d) Busque la línea que comienza con Subsistema y quite el comentario.
	
		![Movilidad de inserción de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)

8. Asegúrese de que se admite la variante de Linux del equipo de origen.
 
### Instalación manual de Mobility Service

Los paquetes de software que se usan para instalar Mobility Service están en el servidor de procesos en C:\\pushinstallsvc\\repository. Inicie sesión en el servidor de procesos y copie el paquete de instalación correspondiente a la máquina de origen basándose en la tabla siguiente:-

| Sistema operativo de origen | Paquete de Mobility Service en el servidor de procesos |
|---------------------------------------------------	|------------------------------------------------------------------------------------------------------	|
| Windows Server 64 (solo 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 (solo 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |


**Para instalar Mobility Service manualmente en un servidor Windows**, haga lo siguiente:

1. Copie el paquete **Microsoft-ASR\_UA\_8.4.0.0\_Windows\_GA\_28Jul2015\_release.exe** de la ruta de acceso al directorio del servidor de procesos indicada en la tabla anterior al equipo de origen.
2. Instale Mobility Service mediante la ejecución del archivo ejecutable en el equipo de origen.
3. Siga las instrucciones del instalador.
4. Seleccione **Mobility Service** como el rol y haga clic en **Siguiente**.
	
	![Instalar Mobility Service](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Deje el directorio de instalación como la ruta de instalación predeterminada y haga clic en **Instalar**.
6. En **Configuración de agente de host**, especifique la dirección IP y el puerto HTTPS del servidor de configuración.

	- Si se conecta a través de Internet, especifique la dirección IP virtual pública y el extremo HTTPS público como el puerto.
	- Si se conecta a través de VPN, especifique la dirección IP interna y 443 para el puerto. Deje la opción **Usar HTTPS** marcada.

	![Instalar Mobility Service](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Especifique la frase de contraseña del servidor de configuración y haga clic en **Aceptar** para registrar Mobility Service con el servidor de configuración.

**Para ejecutar desde la línea de comandos:**

1. Copie la frase de contraseña desde el CX en el archivo "C:\\connection.passphrase" en el servidor y ejecute este comando. En nuestro ejemplo CX i 104.40.75.37 y el puerto HTTPS es 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Instalación de Mobility Service manualmente en un servidor Linux**:

1. Copie el archivo tar adecuado basado en la tabla anterior, desde el servidor de proceso a la máquina de origen.
2. Abra un programa de shell y extraiga el archivo tar comprimido en una ruta de acceso local mediante la ejecución de `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Cree un archivo passphrase.txt en el directorio local en que extrajo el contenido del archivo tar; para ello, escriba *`echo <passphrase> >passphrase.txt`* desde shell.
4. Instale Mobility Service; para ello, escriba *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique la dirección IP y el puerto:

	- Si se conecta al servidor de configuración a través de Internet, especifique la dirección IP pública virtual del servidor de configuración y el extremo HTTPS público en `<IP address>` y `<port>`.
	- Si se conecta a través de VPN, especifique la dirección IP interna y 443.

**Para ejecutar desde la línea de comandos**:

1. Copie la frase de contraseña desde el CX en el archivo "passphrase.txt" en el servidor y ejecute este comando. En nuestro ejemplo CX i 104.40.75.37 y el puerto HTTPS es 62519:

Para instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Para instalar en el servidor de destino:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Al agregar equipos a un grupo de protección que ya están ejecutando una versión adecuada de Mobility Service, la instalación de inserción se omite.


## Paso 9: Habilitar protección

Para habilitar la protección, agregue máquinas virtuales y servidores físicos a un grupo de protección. Antes de comenzar, tenga en cuenta que:

- Las máquinas virtuales se detectan cada 15 minutos y pueden tardar hasta 15 minutos en aparecer en Azure Site Recovery después de la detección.
- Los cambios de entorno en la máquina virtual (como la instalación de herramientas de VMware) también pueden tardar hasta 15 minutos en actualizarse en Site Recovery.
- Puede consultar la última detección en el campo **ÚLTIMO CONTACTO A LAS** para el servidor vCenter/host ESXi en la página **Servidores de configuración**.
- Si tiene un grupo de protección ya creado y agrega un servidor vCenter o un host ESXi después de eso, se tarda 15 minutos en actualizar el portal de Azure Site Recovery y en mostrar las máquinas virtuales en el cuadro de diálogo **Agregar máquinas al grupo de protección**.
- Si desea continuar inmediatamente con la incorporación de equipos al grupo de protección, sin esperar la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar**.
- Al agregar máquinas virtuales o equipos físicos a un grupo de protección, el servidor de procesos inserta automáticamente e instala Mobility Service en el servidor de origen si aún no está instalado.
- Asegúrese de que ha configurado los equipos protegidos tal como se ha descrito en el paso anterior para que el mecanismo de inserción automática funcione.

Agregue las máquinas como sigue:

1. Pestaña **Elementos protegidos** > **Grupo de protección** > **Máquinas**. Haga clic en **AGREGAR MÁQUINAS**. Como procedimiento recomendado, se recomienda que los grupos de protección deben reflejar las cargas de trabajo para que puedan agregar los equipos que ejecutan una aplicación específica al mismo grupo.
2. En **Seleccionar máquinas virtuales**, si protege servidores físicos, en el asistente **Agregar máquinas físicas**, proporcione la dirección IP y el nombre descriptivo. A continuación, seleccione la familia de sistemas operativos.

	![Agregar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. En **Seleccionar máquinas virtuales**, si protege máquinas virtuales de VMware, seleccione un servidor vCenter que administre las máquinas virtuales (o el host EXSi en el que se ejecutan) y después seleccione las máquinas.

	![Agregar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)	
4. En **Especificar recursos de destino**, seleccione los servidores de destino maestros y el almacenamiento que se va a usar para la replicación y seleccione si se debe usar la configuración para todas las cargas de trabajo. Seleccione [cuenta de almacenamiento Premium](../storage/storage-premium-storage.md) al configurar la protección para las cargas de trabajo que requieren un alto rendimiento de E/S y baja latencia uniformes para hospedar cargas de trabajo intensivas de E/S. Si quiere usar una cuenta de almacenamiento premium para los discos de cargas de trabajo, necesitará utilizar el destino principal de la serie DS. No puede usar discos de almacenamiento premium con máquinas virtuales que no sean de serie DS.

	![Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. En **Especificar cuentas**, seleccione la cuenta que desea usar para instalar Mobility Service en equipos protegidos. Las credenciales de cuenta son necesarias para la instalación automática de Mobility Service. Si no puede seleccionar una cuenta, asegúrese configurar una tal y como se describe en el paso 2. Tenga en cuenta que Azure no puede tener acceso a esta cuenta. En Windows Server, la cuenta debe tener privilegios de administrador en el servidor de origen. Para Linux, la cuenta debe ser raíz.

	![Credenciales de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Haga clic en la marca de verificación para terminar de agregar equipos al grupo de protección y para iniciar la replicación inicial en cada equipo. Puede supervisar el estado en la página **Trabajos**.

	![Agregar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. Además, puede supervisar el estado de protección; para ello, haga clic en **Elementos protegidos** > Nombre del grupo de protección > **Máquinas virtuales**. Cuando se completa la replicación inicial y las máquinas están sincronizando datos, mostrarán el estado **Protegido**.

	![Trabajos de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### Establecimiento de propiedades del equipo protegido

1. Cuando los equipos ya tienen el estado **Protegido**, puede configurar sus propiedades de conmutación por error. En los detalles del grupo de protección, seleccione el equipo y abra la pestaña **Configurar**.
2. Puede modificar el nombre que se asignará al equipo de Azure después de la conmutación por error así como el tamaño de la máquina virtual de Azure. También puede seleccionar la red de Azure a la que se conectará el equipo después de la conmutación por error.

	![Establecer propiedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Observe lo siguiente:

- El nombre del equipo de Azure debe cumplir los requisitos de Azure.
- De forma predeterminada, las máquinas virtuales replicadas en Azure no están conectadas a una red de Azure. Si desea que se comuniquen las máquinas virtuales, asegúrese de establecer la misma red de Azure para ellas.
- Si cambia el tamaño de un volumen en un servidor físico o máquina virtual de VMware, entrará en estado crítico. Si necesita modificar el tamaño, haga lo siguiente:

	- a) Cambie la configuración del tamaño.
	- b) En la pestaña **Máquinas virtuales**, seleccione la máquina virtual y haga clic en **Quitar**.
	- c) En **Quitar máquina virtual**, seleccione la opción **Deshabilitar protección (úselo para la obtención de detalles y modificación del tamaño del volumen)**. Esta opción deshabilita la protección, pero conserva los puntos de recuperación en Azure.

		![Establecer propiedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

	- d) Vuelva a habilitar la protección de una máquina virtual. Cuando se vuelve a habilitar la protección, se transferirán los datos para el volumen cuyo tamaño ha cambiado a Azure.

	

## Paso 10: Ejecutar la conmutación por error

Actualmente solo se pueden ejecutar conmutaciones por error no previstas de servidores físicos y máquinas virtuales de VMware protegidos. Tenga en cuenta lo siguiente:



- Antes de iniciar una conmutación por error, asegúrese de que los servidores de configuración y de destino principal funcionan y están en buen estado. De lo contrario, se producirá un error de conmutación por error.
- Las máquinas de origen no se apagan como parte de una conmutación por error no planeada. Realizar una conmutación por error no planeada detiene la replicación de datos para los servidores protegidos. Deberá eliminar las máquinas del grupo de protección y agregarlas de nuevo para comenzar a proteger los equipos de nuevo una vez completada la conmutación por error no planeada.
- Si desea conmutar por error sin perder datos, asegúrese de que están desactivadas las máquinas virtuales del sitio primario antes de iniciar la conmutación por error.

1. En la página **Planes de recuperación**, agregue un plan de recuperación. Especifique los detalles del plan y seleccione **Azure** como destino.

	![Configurar plan de recuperación](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. En **Seleccionar máquina virtual** seleccione un grupo de protección y después seleccione los equipos del grupo que se van a agregar al plan de recuperación. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

	![Agregar máquinas virtuales](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Si es necesario, puede personalizar el plan para crear grupos y organizar el orden en el los equipos del plan de recuperación se van a conmutar por error. También puede agregar avisos para las acciones manuales y los scripts. Los scripts para recuperar en Azure pueden agregarse mediante [Runbooks de automatización de Azure](site-recovery-runbook-automation.md).

4. En la página **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error no planeada**.
5. En **Confirmar conmutación por error**, compruebe la dirección de la conmutación por error (en Azure) y seleccione el punto de recuperación en el que se va a realizar la conmutación.
6. Espere a que el trabajo de conmutación por error se complete y, a continuación, compruebe que la conmutación por error funciona de la forma prevista y que las máquinas virtuales replicadas se inician correctamente en Azure.




## Paso 11: Conmutación por recuperación a través de máquinas de Azure

[Obtenga más información](site-recovery-failback-azure-to-vmware-classic-legacy.md) sobre cómo devolver las máquinas de conmutación por recuperación que se ejecutan en Azure al entorno local.


## Administración de servidores de procesos

El servidor de proceso envía los datos de replicación al servidor de destino principal en Azure y detecta nuevas máquinas virtuales de VMware agregadas a un servidor vCenter. En las siguientes circunstancias, puede cambiar el servidor de proceso en su implementación:

- Si el servidor de proceso actual deja de funcionar
- Si su objetivo de punto de recuperación (RPO) aumenta hasta un nivel inaceptable para su organización.

Si es necesario puede mover la replicación de algunas o todas las máquinas virtuales de VMware locales y los servidores físicos a un servidor de proceso diferente. Por ejemplo:

- **Error**: si en un servidor de procesos se produce un error o no está disponible, puede mover la replicación del equipo protegido a otro servidor de procesos. Los metadatos de la máquina de origen y la máquina de réplica se moverán al nuevo servidor de proceso y los datos se volverán a sincronizar. El nuevo servidor de proceso se conectará automáticamente al servidor vCenter para realizar la detección automática. Puede supervisar el estado de los servidores de procesos en el panel de Site Recovery.
- **Equilibrio de carga para ajustar el RPO**: para conseguir un equilibrio de carga mejorado, puede seleccionar un servidor de procesos diferente en el portal de Site Recovery y mover la replicación de uno o más equipos a este para equilibrar la carga manualmente. En este caso los metadatos de los equipos de origen y réplica seleccionados se mueven al nuevo servidor de procesos. El servidor de procesos original permanece conectado al servidor vCenter. 

### Supervisión del servidor de procesos

Si un servidor de procesos está en un estado crítico, se mostrará una advertencia de estado en el panel de Site Recovery. Puede hacer clic en el estado para abrir la pestaña Eventos y después desglosar trabajos específicos en la pestaña Trabajos.

### Modificación del servidor de procesos utilizado para la replicación

1. Vaya a la página **SERVIDORES DE CONFIGURACIÓN** en **SERVIDORES**.
2. Haga clic en el nombre del servidor de configuración y vaya a **Detalles del servidor**.
3. En la lista **Servidores de procesos**, haga clic en **Cambiar servidor de procesos** junto al servidor que desea modificar. ![Cambiar servidor de proceso 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)
4. En el cuadro de diálogo **Cambiar servidor de procesos**, seleccione el servidor nuevo en **Servidor de proceso de destino** y después seleccione las máquinas virtuales que se van a replicar en el nuevo servidor. Haga clic en el icono de información junto al nombre del servidor para obtener información sobre él, como el espacio libre o la memoria utilizada. El espacio medio que será necesario para replicar cada máquina virtual seleccionada en el nuevo servidor de procesos se muestra para ayudarlo a tomar decisiones de carga. ![Cambiar servidor de proceso 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)
5. Haga clic en la marca de verificación para empezar a replicar en un nuevo servidor de procesos. Si quita todas las máquinas virtuales de un servidor de procesos que era fundamental, ya no debe mostrarse una advertencia crítica en el panel.


## Avisos e información de software de terceros

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0302_2016-->
