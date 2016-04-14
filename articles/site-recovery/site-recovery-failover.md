<properties
	pageTitle="Conmutación por error en Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Obtenga información acerca de la conmutación por error en Azure o en un centro de datos secundario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

# Conmutación por error en Site Recovery

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

## Información general

Este artículo ofrece información e instrucciones para la recuperación (conmutación por error y conmutación por recuperación) de máquinas virtuales y servidores físicos protegidos con Site Recovery.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Tipos de conmutación por error

Después de habilitar la protección para las máquinas virtuales y los servidores físicos y que estos comiencen a replicar, puede ejecutar conmutaciones por error si es necesario. Site Recovery admite varios tipos de conmutación por error.

**Conmutación por error** | **Cuándo se debe ejecutar** | **Detalles** | **Proceso**
---|---|---|---
**Conmutación por error de prueba** | Se ejecuta para validar la estrategia de replicación o para realizar una exploración de recuperación ante desastres. | Sin pérdida de datos ni tiempo de inactividad.<br/><br/>Sin impacto en replicación.<br/><br/>Sin impacto en el entorno de producción. | Iniciar la conmutación por error.<br/><br/>Especificar cómo se conectarán las máquinas de prueba a las redes después de la conmutación por error.<br/><br/>Realizar el seguimiento del progreso en la pestaña **Trabajos**. Se crean e inician máquinas de prueba en la ubicación secundaria.<br/><br/>Azure - Conectar con el equipo en el portal de Azure.<br/><br/>Sitio secundario - Acceder al equipo en el mismo host y nube.<br/><br/>Realizar las pruebas y limpiar automáticamente la configuración de la conmutación por error de prueba.
**Conmutación por error planeada** | Se ejecuta para satisfacer los requisitos de cumplimiento.<br/><br/>Se ejecuta para el mantenimiento planeado.<br/><br/>Se ejecuta para conmutar los datos por error y mantener las cargas de trabajo en ejecución durante interrupciones conocidas, como un error de alimentación esperado o informes meteorológicos graves.<br/><br/>Se ejecuta para realizar una conmutación por recuperación después de una conmutación por error de principal a secundario. | Sin pérdida de datos.<br/><br/>Se produce un tiempo de inactividad durante el tiempo necesario para apagar la máquina virtual en la ubicación principal y activarla en la ubicación secundaria.<br/>Las máquinas virtuales se ven afectadas, ya que las máquinas de destino se convierten en máquinas de origen después de la conmutación por error. | Iniciar la conmutación por error.<br/><br/>Realizar el seguimiento del progreso en la pestaña **Trabajos**. Las máquinas de origen se apagan.<br/><br/>En la ubicación secundaria, se inician las máquinas de réplica.<br/><br/>Azure - Conectar con la máquina de réplica en el portal de Azure.<br/><br/>Sitio secundario - Acceder a la máquina en el mismo host y en la misma nube.<br/><br/>Confirmar la conmutación por error.
**Conmutación por error no planeada** | Este tipo de conmutación por error se ejecuta de manera reactiva cuando no se puede acceder a un sitio principal debido a un incidente inesperado, como un corte de alimentación o un ataque de virus.<br/><br/>Una conmutación por error no planeada se puede ejecutar incluso si el sitio principal no está disponible. | Pérdida de datos en función de la configuración de la frecuencia de replicación.<br/><br/>Los datos estarán actualizados de acuerdo con la última vez que se realizó la sincronización. | Iniciar la conmutación por error.<br/><br/>Realizar el seguimiento del progreso en la pestaña **Trabajos**. También puede apagar las máquinas virtuales y sincronizar los últimos datos.<br/><br/>En la ubicación secundaria se inician las máquinas de réplica.<br/><br/>Azure - Conectar con la máquina de réplica en el portal de Azure.<br/><br/>Sitio secundario - Acceder a la máquina en el mismo host y en la misma nube.<br/><br/>Confirmar la conmutación por error.


Los tipos de conmutaciones por error que se admiten dependen del escenario de implementación.

**Dirección de conmutación por error** | **Conmutación por error de prueba** | **Conmutación por error planeada** | **Conmutación por error no planeada**
---|---|---|---
Sitio principal de VMM a sitio secundario de VMM | Compatible | Compatible | Compatible 
Sitio secundario de VMM a sitio principal de VMM | Compatible | Compatible | Compatible
Nube a nube (servidor VMM único) | Compatible | Compatible | Compatible
Sitio de VMM a Azure | Compatible | Compatible | Compatible 
Azure a sitio de VMM | No compatible | Compatible | No compatible 
Sitio de Hyper-V a Azure | Compatible | Compatible | Compatible
Azure a sitio de Hyper-V | No compatible | Compatible | No compatible
Sitio de VMware a Azure | Compatible (escenario mejorado)<br/><br/>No compatible (escenario heredado) |En este escenario se utiliza la replicación continua, por lo que no hay ninguna distinción entre la conmutación por error planeada y no planeada. Selecciona **Conmutación por error**. | N/A
Servidor físico a Azure | No compatible | En este escenario se utiliza la replicación continua, por lo que no hay ninguna distinción entre la conmutación por error planeada y no planeada. Selecciona **Conmutación por error**. | N/A

## Conmutación por error y conmutación por recuperación

La conmutación por error de las máquinas virtuales se realiza a un sitio local secundario o a Azure, en función de la implementación. La máquina que conmuta por error a Azure se crea como máquina virtual de Azure. Puede realizar la conmutación por error a una única máquina virtual o servidor físico, o bien a un plan de recuperación. Los planes de recuperación se componen de uno o varios grupos ordenados que contienen máquinas virtuales o servidores físicos protegidos. Se utilizan para orquestar la conmutación por error de varias máquinas que conmutan por error en función del grupo donde se encuentren. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

Cuando se haya completado la conmutación por error y las máquinas estén en ejecución en una ubicación secundaria, tenga en cuenta lo siguiente:

- Si la conmutación por error se ha realizado a Azure, tras ella las máquinas no están protegidas ni se replican en la ubicación principal o secundaria. En Azure las máquinas virtuales se almacenan en almacenamiento de replicación geográfica que ofrece resistencia, pero no replicación.
- Si realizó una conmutación por error no planeada a un sitio secundario, tras ella las máquinas de la ubicación secundaria no están protegidas ni se replican.
- Si realizó una conmutación por error planeada a un sitio secundario, tras ella las máquinas de la ubicación secundaria están protegidas.
 

### Conmutación por recuperación desde un sitio secundario

La conmutación por recuperación desde un sitio secundario a uno principal utiliza el mismo proceso que la conmutación por error desde el sitio principal al secundario. Una vez confirmada y finalizada la conmutación por error desde el sitio principal al centro de datos secundario, puede iniciar la replicación inversa cuando el sitio principal vuelva a estar disponible. La replicación inversa inicia la replicación entre el sitio secundario y el principal mediante la sincronización de las diferencias de datos. La replicación inversa pone las máquinas virtuales en un estado protegido pero el centro de datos secundario sigue siendo la ubicación activa. Para que el sitio principal se convierta en la ubicación activa, debe iniciar una conmutación por error planeada desde el secundario al principal, seguida de otra replicación inversa.

### Conmutación por recuperación desde Azure

Si ha realizado la conmutación por error a Azure, las máquinas virtuales están protegidas por las características de resistencia de Azure para máquinas virtuales. Para convertir el sitio principal original en la ubicación active, debe ejecutar una conmutación por error planeada. Puede realizar una conmutación por recuperación a la ubicación original o a una ubicación alternativa si el sitio original no está disponible. Para iniciar la replicación de nuevo tras la conmutación por recuperación a la ubicación principal, debe iniciar una replicación inversa.

### Consideraciones sobre la conmutación por error

- **Dirección IP tras la conmutación por error**: de forma predeterminada, una máquina con conmutación por error tendrá una dirección IP diferente que la máquina de origen. Si desea conservar la misma dirección IP, consulte: 
	- **Sitio secundario**: si la conmutación por error se realiza a un sitio secundario y desea conservar la dirección IP, [lea](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) este artículo. Tenga en cuenta que puede conservar una dirección IP pública si el ISP lo admite.
	- **Azure**: si realiza la conmutación por error a Azure, puede especificar la dirección IP que desea asignar en la pestaña **Configurar** de las propiedades de la máquina virtual. No puede conservar una dirección IP pública después de la conmutación por error en Azure. Puede conservar espacios de direcciones que no son RFC 1918 que se usan como direcciones internas.
- **Conmutación por error parcial**: si desea realizar la conmutación por error de una parte de un sitio y no del sitio completo, tenga en cuenta lo siguiente: 
	- **Sitio secundario**: si realiza la conmutación por error de un sitio principal a un sitio secundario y desea volver a conectarse al sitio principal, debe usar una conexión VPN de sitio a sitio para conectar las aplicaciones con conmutación por error del sitio secundario a componentes de infraestructura que se ejecutan en el sitio principal. Si se realiza la conmutación por error de la subred completa, se puede conservar la dirección IP de la máquina virtual. Si se realiza la conmutación por error de una subred parcial, no se puede conservar la dirección IP de la máquina virtual porque las subredes no pueden dividirse entre sitios.
	- **Azure**: si realiza la conmutación por error de un sitio parcial a Azure y desea volver a conectarse al sitio principal, puede usar la VPN de sitio a sitio para conectar una aplicación conmutada por error de Azure a componentes de infraestructura que se ejecutan en el sitio principal. Tenga en cuenta que si la subred completa conmuta por error, se puede conservar la dirección IP de la máquina virtual. Si se realiza la conmutación por error de una subred parcial, no se puede conservar la dirección IP de la máquina virtual porque las subredes no pueden dividirse entre sitios.
 
- **Letra de la unidad**: si desea conservar la letra de unidad en las máquinas virtuales después de la conmutación por error, puede establecer la directiva SAN para la máquina virtual en **Activado**. Los discos de las máquinas virtuales se activan automáticamente. [Más información](https://technet.microsoft.com/library/gg252636.aspx).
- **Enrutamiento de solicitudes de cliente**: Site Recovery funciona con el Administrador de tráfico de Azure para enrutar las solicitudes de cliente a la aplicación después de la conmutación por error.




## Ejecución de una conmutación por error de prueba

Al ejecutar una prueba de conmutación por error se le pedirá que seleccione la configuración de red de las máquinas de réplica de prueba. Tiene varias opciones.

**Opción de conmutación por error de prueba** | **Descripción** | **Verificación de la conmutación por error** | **Detalles**
---|---|---|---
**Conmutación por error a Azure, sin red** | No seleccione una red de Azure de destino. | La conmutación por error comprueba que la máquina virtual de prueba se inicia como se esperaba en Azure. | Todas las máquinas virtuales de prueba de un plan de recuperación se agregan en un único servicio en la nube y pueden conectarse entre sí.<br/><br/>Las máquinas no se conectan a una red de Azure después de la conmutación por error.<br/><br/>Los usuarios pueden conectarse a las máquinas de prueba con una dirección IP pública.
**Conmutación por error a Azure, con red** | Seleccione una red de Azure de destino. | La conmutación por error comprueba que las máquinas de prueba están conectadas a la red. | Cree una red de Azure aislada de la red de producción de Azure y configure la infraestructura de la máquina virtual replicada para que funcione según lo previsto.<br/><br/>La subred de la máquina virtual de prueba se basa en la subred con la que se espera que se conecte la máquina virtual conmutada por error en caso de que se produzca una conmutación por error planeada o no planeada.
**Conmutación por error a un sitio secundario de VMM, sin red** | No seleccione una red de máquina virtual. | La conmutación por error comprueba que se crean las máquinas de prueba. <br/><br/>La máquina virtual de prueba se creará en el mismo host en el que se encuentra la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica. | <p>La máquina conmutada por error no se conectará a ninguna red.<br/><br/>La máquina puede conectarse a una red de máquina virtual una vez creada.
**Conmutación por error a un sitio secundario de VMM, con red** | Seleccione una red de máquina virtual existente. | La conmutación por error comprueba que se crean las máquinas virtuales. | La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.<br/><br/>Cree una red de máquina virtual aislada de la red de producción.<br/><br/>Si utiliza una red basada en VLAN, se recomienda crear una red lógica independiente (no se utiliza en producción) en VMM para este propósito. Esta red lógica se utiliza para crear redes de máquinas virtuales con el fin de probar la conmutación por error.<br/><br/>La red lógica debe asociarse al menos con uno de los adaptadores de red de todos los servidores Hyper-V que hospedan máquinas virtuales.<br/><br/>En las redes lógicas de VLAN, los sitios de red que agregue a la red lógica deberán estar aislados.<br/><br/>Si utiliza una red lógica basada en virtualización de red de Windows, Azure Site Recovery crea automáticamente redes de máquinas virtuales aisladas.
**Conmutación por error a un sitio secundario de VMM, crear red** | Se creará automáticamente una red de prueba temporal en función de la configuración especificada en **Red lógica** y de sus sitios de red relacionados. | La conmutación por error comprueba que se crean las máquinas virtuales. | Utilice esta opción si el plan de recuperación usa más de una red de VM. Si utiliza redes de virtualización de red de Windows, esta opción permite crear automáticamente redes de máquinas virtuales con la misma configuración (subredes y grupos de direcciones IP) en la red de la máquina virtual de réplica. Estas redes de máquinas virtuales se limpian automáticamente una vez completada la conmutación por error de prueba.</p><p>La máquina virtual de prueba se creará en el mismo host que el host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.

>[AZURE.NOTE] La dirección IP que se asigna a una máquina virtual durante la conmutación por error de prueba es la misma dirección IP que obtendría al realizar una conmutación por error planeada o no planeada (siempre que la dirección IP esté disponible en la red de conmutación por error de prueba). Si la misma dirección IP no está disponible en la red de conmutación por error de prueba, la máquina virtual obtendrá otra dirección IP disponible en la red de conmutación por error de prueba.



### Ejecución de una conmutación por error de prueba de local a Azure

En este procedimiento se describe cómo ejecutar una conmutación por error de prueba para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina en la pestaña **Máquinas virtuales**.

1. Seleccione **Planes de recuperación** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Conmutación por error de prueba**.
2. En la página **Confirmar conmutación por error de prueba**, especifique cómo se conectarán las máquinas de réplica a una red de Azure tras la conmutación por error.
3. Si realiza la conmutación por error a Azure y en la nube está habilitado el cifrado de datos, en **Clave de cifrado** seleccione el certificado que se emitió cuando habilitó el cifrado de datos durante la instalación del proveedor. 
4. Realice el seguimiento del progreso de la conmutación por error en la pestaña **Trabajos**. Debe poder ver la máquina de réplica de prueba en el Portal de Azure.
5. Puede tener acceso a las máquinas de réplica en Azure desde el sitio local. Para ello, inicie una conexión RDP a la máquina virtual. El puerto 3389 deberá estar abierto en el extremo de la máquina virtual.
5. Al terminar, cuando la conmutación por error alcance la fase **Pruebas completadas**, haga clic en **Prueba completada** para finalizar.
5. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba.
8. Haga clic en **La conmutación por error de prueba se ha completado** para limpiar automáticamente el entorno de prueba. Una vez completada esta acción, la conmutación por error de prueba mostrará el estado **Completa**.

> [AZURE.NOTE] Si una conmutación por error de prueba continúa durante más de dos semanas, se forzará su finalización. Se eliminarán todos los elementos o las máquinas virtuales creados automáticamente durante la conmutación por error de prueba.
  

### Ejecute una conmutación por error de prueba de un sitio local principal a un sitio local secundario

Deberá realizar varias tareas para ejecutar una conmutación por error de prueba, como realizar una copia del controlador de dominio e incluir servidores DHCP y DNS de prueba en el entorno de prueba. Puede hacerlo de dos maneras:

- Si desea ejecutar una conmutación por error de prueba con una red existente, prepare Active Directory, DHCP y DNS en esa red.
- Si desea ejecutar una conmutación por error de prueba con la opción de creación automática de redes de máquina virtual, agregue un paso manual antes de Grupo-1 en el plan de recuperación que va a usar para la conmutación por error de prueba y, a continuación, agregue los recursos de infraestructura a la red creada automáticamente antes de ejecutar la conmutación por error de prueba.

#### Puntos a tener en cuenta

- Cuando se replica a un sitio secundario, no es necesario que el tipo de red que utiliza la máquina de réplica coincida con el tipo de red lógica que se usa para la conmutación por error de prueba; sin embargo, es posible que algunas combinaciones no funcionen. Si la réplica utiliza DHCP y aislamiento basado en VLAN, la red de máquina virtual para la réplica no necesita un grupo de direcciones IP estáticas. Por lo tanto, el uso de virtualización de red de Windows para la conmutación por error de prueba no funcionaría porque no hay grupos de direcciones disponibles. Además, la prueba de conmutación por error no funcionará si la red de réplica está configurada en Sin aislamiento y la red de prueba es de virtualización de red de Windows. Esto se debe a que una red sin aislamiento no tiene las subredes necesarias para crear una red de virtualización de red de Windows.
- La manera en que las máquinas virtuales de réplica se conectan a las redes de máquinas virtuales asignadas después de la conmutación por error depende de cómo se configura la red de máquina virtual en la consola VMM:
	- **Red de máquina virtual configurada sin aislamiento o con aislamiento de VLAN**: si se ha definido DHCP para la red de máquina virtual, la máquina virtual de réplica se conectará al identificador de VLAN utilizando la configuración especificada para el sitio de red en la red lógica asociada. La máquina virtual recibirá su dirección IP del servidor DHCP disponible. No es necesario definir un grupo de direcciones IP estáticas para la red de máquina virtual de destino. Si se utiliza un grupo de direcciones IP estáticas para la red de máquina virtual, la máquina virtual de réplica se conectará al identificador de VLAN mediante la configuración especificada para el sitio de red en la red lógica asociada. La máquina virtual recibirá su dirección IP del grupo definido para la red de máquina virtual. Si no se ha definido un grupo de direcciones IP estáticas en la red de máquina virtual de destino, se producirá un error en la asignación de direcciones IP. El grupo de direcciones IP debe crearse en los servidores VMM de origen y de destino que se van a usar para la protección y la recuperación.
	- **Red de máquina virtual con virtualización de red de Windows**: si se establece una red de máquina virtual con esta configuración, debe definirse un grupo estático para la red de máquina virtual de destino, independientemente de si la red de máquina virtual de origen se ha configurado para utilizar DHCP o un grupo de direcciones IP estáticas. Si define DHCP, el servidor VMM de destino actuará como servidor DHCP e indicará una dirección IP del grupo definido para la red de máquina virtual de destino. Si se define el uso de un grupo de direcciones IP estáticas para el servidor de origen, el servidor VMM de destino asignará una dirección IP del grupo. En ambos casos, se producirá un error en la asignación de direcciones IP si no se define un grupo de direcciones IP estáticas.

#### Ejecución de la prueba

En este procedimiento se describe cómo ejecutar una conmutación por error de prueba para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual o un único servidor físico en la pestaña **Máquinas virtuales**.

1. Seleccione **Planes de recuperación** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Conmutación por error de prueba**.
2. En la página **Confirmar conmutación por error de prueba**, especifique cómo se deben conectar las máquinas virtuales a las redes después de la conmutación por error de prueba.
3. Realice el seguimiento del progreso de la conmutación por error en la pestaña **Trabajos**. Cuando la conmutación por error alcance la fase **Pruebas completadas**, haga clic en **Prueba completada** para terminar la conmutación por error de prueba.
4. Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.
4. Cuando haya terminado, compruebe que las máquinas virtuales se inician correctamente.
5. Una vez que compruebe que las máquinas virtuales se inician correctamente, complete la conmutación por error de prueba para limpiar el entorno aislado. Si seleccionó la creación automática de redes de máquina virtual, la limpieza elimina todas las máquinas virtuales de prueba y las redes de prueba.

> [AZURE.NOTE] Si una conmutación por error de prueba continúa durante más de dos semanas, se forzará su finalización. Se eliminarán todos los elementos o las máquinas virtuales creados automáticamente durante la conmutación por error de prueba.


#### Preparación de DHCP

Si las máquinas virtuales implicadas en la conmutación por error de prueba usan DHCP, se debe crear un servidor DHCP de prueba dentro de la red aislada que se crea para la conmutación por error de prueba.


### Preparación de Active Directory
Para ejecutar una conmutación por error de prueba con el fin de probar la aplicación, necesitará una copia del entorno de Active Directory de producción en el entorno de prueba. Examine la sección [Consideraciones sobre la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#considerations-for-test-failover) para obtener más información.


### Preparación de DNS

Prepare un servidor DNS para la conmutación por error de prueba de la forma siguiente:

- **DHCP**: si las máquinas virtuales usan DHCP, debe actualizarse la dirección IP del DNS de prueba en el servidor DHCP de prueba. Si utiliza un tipo de red de virtualización de red de Windows, el servidor VMM actúa como servidor DHCP. Por lo tanto, la dirección IP de DNS debe actualizarse en la red de conmutación por error de prueba. En este caso, las máquinas virtuales se registrarán a sí mismas en el servidor DNS correspondiente.
- **Dirección estática**: si las máquinas virtuales utilizan una dirección IP estática, la dirección IP del servidor DNS de prueba debe actualizarse en la red de conmutación por error de prueba. Es posible que deba actualizar el DNS con la dirección IP de las máquinas virtuales de prueba. Puede usar el siguiente script de ejemplo para este propósito: 

	    Param(
	    [string]$Zone,
	    [string]$name,
	    [string]$IP
	    )
	    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	    $newrecord = $record.clone()
	    $newrecord.RecordData[0].IPv4Address  =  $IP
	    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## Ejecución de una conmutación por error planeada (de principal a secundario)

 En este procedimiento se describe cómo ejecutar una conmutación por error planeada para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual en la pestaña **Máquinas virtuales**.

1. Antes de comenzar, asegúrese de que en todas las máquinas virtuales en las que desea realizar la conmutación por error se ha completado la replicación inicial.
2. Seleccione **Planes de recuperación** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Conmutación por error planeada**. 
3. En la página **Confirmar conmutación por error planeada**, elija las ubicaciones de origen y de destino. Tenga en cuenta la dirección de la conmutación por error.

	- Si las conmutaciones por error anteriores funcionaron como se esperaba y todos los servidores de máquina virtual se encuentran en la ubicación de origen o bien en la ubicación de destino, los detalles de dirección de la conmutación por error son meramente informativos. 
	- Si las máquinas virtuales están activas tanto en las ubicaciones de origen como de destino, aparece el botón **Cambiar dirección**. Utilice este botón para cambiar y especificar la dirección en la que debe realizarse la conmutación por error.

5. Si realiza la conmutación por error a Azure y en la nube está habilitado el cifrado de datos, en **Clave de cifrado** seleccione el certificado que se emitió cuando habilitó el cifrado de datos durante la instalación del proveedor en el servidor VMM.
6. Cuando se inicia una conmutación por error planeada, el primer paso es apagar las máquinas virtuales para garantizar que se no se produce ninguna pérdida de datos. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos**. Si se produce un error en la conmutación por error (ya sea en una máquina virtual o en un script incluido en el plan de recuperación), la conmutación por error planeada de un plan de recuperación se detiene. Puede iniciar la conmutación por error nuevo.
8. Una vez creadas las máquinas virtuales de réplica, pasan a estar en estado pendiente de confirmación. Haga clic en **Confirmar** para confirmar la conmutación por error. 
9. Cuando se ha completado la replicación, las máquinas virtuales se inician en la ubicación secundaria. 

## Ejecución de una conmutación por error no planeada

En este procedimiento se describe cómo ejecutar una conmutación por error no planeada para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual o un único servidor físico en la pestaña **Máquinas virtuales**.

1. Seleccione **Planes de recuperación** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Conmutación por error no planeada**. 
3. En la página **Confirmar conmutación por error no planeada**, elija las ubicaciones de origen y de destino. Tenga en cuenta la dirección de la conmutación por error.

	- Si las conmutaciones por error anteriores funcionaron como se esperaba y todos los servidores de máquina virtual se encuentran en la ubicación de origen o bien en la ubicación de destino, los detalles de dirección de la conmutación por error son meramente informativos. 
	- Si las máquinas virtuales están activas tanto en las ubicaciones de origen como de destino, aparece el botón **Cambiar dirección**. Utilice este botón para cambiar y especificar la dirección en la que debe realizarse la conmutación por error.

4. Si realiza la conmutación por error a Azure y en la nube está habilitado el cifrado de datos, en **Clave de cifrado** seleccione el certificado que se emitió cuando habilitó el cifrado de datos durante la instalación del proveedor en el servidor VMM.
5. Seleccione **Apagar máquinas virtuales y sincronizar los últimos datos** para especificar que Site Recovery debe intentar apagar las máquinas virtuales protegidas y sincronizar los datos para que se realice la conmutación por error de la versión más reciente de los datos. Si no selecciona esta opción o el intento no se realiza correctamente, la conmutación por error se realizará a partir del último punto de recuperación disponible para la máquina virtual.
6. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos**. Tenga en cuenta que incluso si se producen errores durante una conmutación por error no planeada, el plan de recuperación se ejecuta hasta que se completa.
7. Después de la conmutación por error, las máquinas virtuales están en un estado de **confirmación pendiente**. Haga clic en **Confirmar** para confirmar la conmutación por error.
8. Si configura la replicación para usar varios puntos de recuperación, en Cambiar punto de recuperación puede seleccionar el uso de un punto de recuperación que no sea el último (de forma predeterminada se utiliza el último). Después de la confirmación, los puntos de recuperación adicionales se quitan.
9. Cuando se ha completado la replicación, las máquinas virtuales se inician y ejecutan en la ubicación secundaria. Sin embargo, no están protegidas ni se replican. Cuando el sitio principal esté disponible de nuevo con la misma infraestructura subyacente, haga clic en **Replicación inversa** para iniciar la replicación inversa. Esto garantiza que todos los datos se replican otra vez al sitio principal y que la máquina virtual está lista de nuevo para la conmutación por error. La replicación inversa después de una conmutación por error no planeada produce una sobrecarga de transferencia de datos. La transferencia usará el mismo método que se configura para la replicación inicial en la nube.

## Conmutación por recuperación de ubicación secundaria a principal

 Después de la conmutación por error de la ubicación principal a la secundaria, las máquinas virtuales replicadas no están protegidas por Site Recovery y la ubicación secundaria actúa como principal. Siga estos procedimientos para realizar la conmutación por recuperación al sitio principal original. En este procedimiento se describe cómo ejecutar una conmutación por error planeada para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual en la pestaña **Máquinas virtuales**.

1. Seleccione **Planes de recuperación** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Conmutación por error planeada**.
2. En la página **Confirmar conmutación por error planeada**, elija las ubicaciones de origen y de destino. Tenga en cuenta la dirección de la conmutación por error. Si la conmutación por error desde la ubicación principal ha funcionado como se esperaba y todas las máquinas virtuales están en la ubicación secundaria, este dato es solo informativo.
3. Si realiza la conmutación por recuperación desde Azure, seleccione la configuración en **Sincronización de datos**:

	- **Sincronizar los datos antes de la conmutación por error**: esta opción minimiza el tiempo de inactividad de las máquinas virtuales ya que realiza la sincronización sin apagarlas. Hace lo siguiente:
		- Fase 1: realiza una instantánea de la máquina virtual en Azure y la copia en el host de Hyper-V local. El equipo continúa ejecutándose en Azure.
		- Fase 2: apaga la máquina virtual en Azure para que no se realice ningún nuevo cambio allí. El último conjunto de cambios se transfiere al servidor local y se inicia la máquina virtual local.
	
	> [AZURE.NOTE] Se recomienda utilizar esta opción si se ha ejecutado Azure durante un tiempo (un mes o más). Esta opción realiza la sincronización sin apagar las máquinas virtuales. No realiza una resincronización, pero sí una conmutación por recuperación completa. Esta opción no realiza cálculos de suma de comprobación.

	- **Sincronizar los datos solo durante la conmutación por error**: utilice esta opción si ha ejecutado Azure durante un período breve de tiempo. Esta opción es más rápida porque realiza una resincronización en lugar de una conmutación por recuperación completa. Realiza un cálculo rápido de suma de comprobación y solo descarga los bloques modificados.

5. Si realiza la conmutación por error a Azure y en la nube está habilitado el cifrado de datos, en **Clave de cifrado** seleccione el certificado que se emitió cuando habilitó el cifrado de datos durante la instalación del proveedor en el servidor VMM.
5. De forma predeterminada se utiliza el último punto de recuperación, pero en **Cambiar punto de recuperación** puede especificar un punto de recuperación diferente. 
6. Haga clic en la marca de verificación para iniciar la conmutación por recuperación. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos**. 
7. Si seleccionó la opción para sincronizar los datos antes de la conmutación por error, una vez que la sincronización de datos inicial esté completa y esté listo para apagar las máquinas virtuales en Azure, haga clic en **Trabajos** > <planned failover job name> **Completar conmutación por error**. Esta opción apaga la máquina de Azure, transfiere los últimos cambios a la máquina virtual local y la inicia.
8. Ahora puede iniciar sesión en la máquina virtual para confirmar que está disponible como se esperaba. 
9. La máquina virtual está en un estado pendiente de confirmación. Haga clic en **Confirmar** para confirmar la conmutación por error.
10. Para completar la conmutación por recuperación, haga clic en **Replicación inversa** con el fin de comenzar a proteger la máquina virtual en el sitio principal.



## Conmutación por recuperación a una ubicación alternativa

Si ha implementado la protección entre un [sitio de Hyper-V y Azure](site-recovery-hyper-v-site-to-azure.md), tiene la capacidad de realizar la conmutación por recuperación de Azure a una ubicación local alternativa. Esto es útil si necesita configurar nuevo hardware local. Así es cómo debe hacerlo.

1. Si configura nuevo hardware, instale Windows Server 2012 R2 y el rol de Hyper-V en el servidor.
2. Cree un conmutador de red virtual con el mismo nombre que tenía en el servidor original.
3. Seleccione **Elementos protegidos** -> **Grupo de protección** -> <ProtectionGroupName> -> <VirtualMachineName> en el que desea realizar la conmutación por recuperación y seleccione **Conmutación por error planeada**.
4. En **Confirmar conmutación por error planeada** seleccione **Crear máquina virtual local si no existe**. 
5. En **Nombre de host**, seleccione el nuevo servidor host de Hyper-V en el que desea incluir la máquina virtual.
6. En Sincronización de datos, se recomienda seleccionar la opción **Sincronizar los datos antes de la conmutación por error**. Así se reduce el tiempo de inactividad de las máquinas virtuales, ya que la sincronización se realiza sin apagarlas. Hace lo siguiente:

	- Fase 1: realiza una instantánea de la máquina virtual en Azure y la copia en el host de Hyper-V local. El equipo continúa ejecutándose en Azure.
	- Fase 2: apaga la máquina virtual en Azure para que no se realice ningún nuevo cambio allí. El último conjunto de cambios se transfiere al servidor local y se inicia la máquina virtual local.
	
7. Haga clic en la marca de verificación para iniciar la conmutación por error (conmutación por recuperación).
8. Después de que finalice la sincronización inicial y esté listo para apagar la máquina virtual en Azure, haga clic en **Trabajos** > <planned failover job> > **Completar conmutación por error**. Esta opción apaga la máquina de Azure, transfiere los últimos cambios a la máquina virtual local y la inicia.
9. Puede iniciar sesión en la máquina virtual local para comprobar que todo funciona según lo esperado. A continuación, haga clic en **Confirmar** para finalizar la conmutación por error.
10. Haga clic en **Replicación inversa** para comenzar a proteger la máquina virtual local.

 

<!---HONumber=AcomDC_0224_2016-->