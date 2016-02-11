<properties
	pageTitle="Solución de problemas de copia de seguridad de máquinas virtuales de Azure | Microsoft Azure"
	description="Solución de problemas de copia de seguridad y restauración de máquinas virtuales de Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="trinadhk;jimpark;aashishr"/>


# Solución de problemas de copia de seguridad de máquinas virtuales de Azure
Puede solucionar los errores detectados al usar Copia de seguridad de Azure con la información incluida en la tabla siguiente.

## Detección

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Detección | Se ha producido un error al detectar elementos nuevos: Copia de seguridad de Microsoft Azure ha detectado un error interno. Espere unos minutos y vuelva a intentar la operación. | Después de 15 minutos, vuelva a intentar el proceso de detección.
| Detección | Se ha producido un error al detectar elementos nuevos: ya hay otra operación de detección en curso. Espere hasta que se haya completado la operación de detección. | None |

## Registro
| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Registro | El número de discos de datos asociados a la máquina virtual sobrepasa el límite admitido. Desconecte algunos discos de datos de esta máquina virtual y vuelva a intentarlo. Copia de seguridad de Azure admite hasta 16 discos de datos conectados a una máquina virtual de Azure para realizar una copia de seguridad. | None |
| Registro | Copia de seguridad de Microsoft Azure encontró un error interno. Espere unos minutos y vuelva a intentarlo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. | Este error puede deberse a una de las siguientes configuraciones no admitidas: <ul><li>LRS Premium</ul>. |
| Registro | Se ha producido un error en el registro porque se ha agotado el tiempo de espera de la operación de instalación del agente. | Compruebe si se admite la versión del sistema operativo de la máquina virtual. |
| Registro | Error en la ejecución de comando: hay otra operación en curso en este elemento. Espere hasta que se complete la operación anterior. | None |
| Registro | No se pueden usar máquinas virtuales con discos duros virtuales almacenados en Almacenamiento Premium para realizar copias de seguridad | None |
| Registro | El agente de máquina virtual no está presente en la máquina virtual: instale los requisitos previos necesarios, el agente de máquina virtual y reinicie la operación. | [Obtenga más información](#vm-agent) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |

## Copia de seguridad

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Copia de seguridad | Al copiar discos duros virtuales desde el almacén de copia de seguridad se agotó el tiempo de espera. Vuelva a intentar la operación en unos minutos. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. | Esto ocurre cuando hay demasiados datos que copiar. Compruebe si tiene menos de 16 discos de datos. |
| Copia de seguridad | No se pudo comunicar con el agente de VM para el estado de la instantánea. Tiempo de espera agotado para esta subtarea de instantánea de máquina virtual: consulte la Guía de solución de problemas acerca de cómo resolver este problema. | Este error se produce si hay un problema con el agente de la máquina virtual o cuando el acceso de red a la infraestructura de Azure está bloqueado por algún motivo. <ul> <li>Obtenga información sobre la [depuración de problemas del agente de máquina virtual](#vm-agent) <li>Obtener información sobre la [depuración de problemas de redes](#networking) <li>Si el agente de máquina virtual se ejecuta bien, obtenga información sobre la [solución de problemas de la instantánea de máquina virtual](#Troubleshoot-VM-Snapshot-Issues)</ul><br>Si el agente de máquina virtual no está causando problemas, reinicie la máquina virtual. A veces, un estado incorrecto de la máquina virtual puede causar problemas, y reiniciar la máquina virtual restablece este "mal estado" |
| Copia de seguridad | Error interno en la copia de seguridad. Vuelva a intentar la operación en unos minutos. Si el problema persiste, póngase en contacto con el Servicio técnico de Microsoft. | Este error puede aparecer por 2 razones: <ol><li> Hay demasiados datos que copiar. <li>Se ha eliminado la máquina virtual original y, por tanto, no se puede realizar la copia de seguridad. Para mantener los datos de copia de seguridad de una máquina virtual eliminada y, a la vez, detener los errores de copia de seguridad, desproteja la máquina virtual y elija la opción para conservar los datos. Este comando detiene la programación de la copia de seguridad y también los mensajes de error recurrentes. |
| Copia de seguridad | No se pudo instalar la extensión de los Servicios de recuperación de Azure en el elemento seleccionado. El agente de la máquina virtual es un requisito previo para la extensión de los servicios de recuperación de Azure. Instale el agente de la máquina virtual de Azure y reinicie el funcionamiento del registro. | <ol> <li>Compruebe si el agente de la máquina virtual se ha instalado correctamente. <li>Asegúrese de que la marca de configuración de la máquina virtual se haya establecido correctamente.</ol> [Obtenga más información](#validating-vm-agent-installation) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |
| Copia de seguridad | Error en la ejecución del comando: otra operación está actualmente en curso en este elemento. Espere hasta que se complete la operación anterior y vuelva a intentarlo. | Se está ejecutando una copia de seguridad existente o un trabajo de restauración para la máquina virtual. No se puede iniciar un nuevo trabajo mientras se está ejecutando otro. |
| Copia de seguridad | Error de instalación de la extensión "COM+ no pudo realizar la conexión con MS DTC (Microsoft Distributed Transaction Coordinator)" | Normalmente, esto significa que el servicio COM+ no se ejecuta. Para obtener ayuda acerca de cómo solucionar este problema, póngase en contacto con el servicio de soporte técnico de Microsoft. |
| Copia de seguridad | Error en la operación de instantánea con el error de operación de VSS "El Cifrado de unidad BitLocker está bloqueando esta unidad" Debe desbloquear esta unidad en el Panel de Control. | Desactive BitLocker para todas las unidades de la máquina virtual y observe si se resuelve el problema VSS |
| Copia de seguridad | No se pueden usar máquinas virtuales con discos duros virtuales almacenados en Almacenamiento Premium para realizar copias de seguridad | None |
| Copia de seguridad | Máquina virtual de Azure no encontrada. | Esto sucede cuando se elimina la máquina virtual principal, pero la directiva de copia de seguridad continúa buscando una máquina virtual para realizar la copia de seguridad. Para solucionar este error: <ol><li>Vuelva a crear la máquina virtual con el mismo nombre e igual nombre de grupo de recursos [nombre del servicio en la nube], <br>(O BIEN) <li> Desactive la protección para esta máquina virtual para que no se creen los trabajos de copia de seguridad </ol> |
| Copia de seguridad | El agente de máquina virtual no está presente en la máquina virtual: instale los requisitos previos necesarios, el agente de máquina virtual y reinicie la operación. | [Obtenga más información](#vm-agent) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |

## Trabajos
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Cancelar trabajo | No se admite la cancelación para este tipo de trabajo; espere hasta que finalice el trabajo. | None |
| Cancelar trabajo | El trabajo no está en un estado que se pueda cancelar; espere hasta que finalice el trabajo. <br>O BIEN<br> El trabajo seleccionado no está en un estado en que se pueda cancelar; espere hasta que finalice el trabajo.| Con toda probabilidad, el trabajo ya está casi completado; espere hasta que finalice el trabajo. |
| Cancelar trabajo | No se puede cancelar el trabajo porque no está en curso; solo se admite la cancelación de trabajos que están en curso. Intente cancelar un trabajo que esté en curso. | Esto sucede debido a un estado transitorio. Espere un momento y vuelva a intentar la operación de cancelación |
| Cancelar trabajo | No se pudo cancelar el trabajo; espere hasta que finalice el trabajo. | None |


## Restauración
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Restauración | Error en la restauración con error interno de nube | <ol><li>El servicio de nube que está intentando restaurar está configurado con la configuración de DNS. Puede consultar <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si hay una dirección configurada, significa que los ajustes de DNS están configurados.<br> <li>El servicio en la nube al que está intentando restaurar se configura con ReservedIP y las máquinas virtuales existentes del servicio en la nube se encuentran en estado detenido.<br>Puede comprobar que un servicio en la nube tiene IP reservada usando los siguientes cmdlets de powershell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Está intentando restaurar una máquina virtual con las siguientes configuraciones de red especiales en el mismo servicio en la nube. <br>- Máquinas virtuales en la configuración del equilibrador de carga (internas y externas)<br>- Máquinas virtuales con varias direcciones IP reservadas<br>- Máquinas virtuales con varias NIC<br>Seleccione un nuevo servicio en la nube en la interfaz de usuario o vea [Consideraciones de restauración](backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) para las máquinas virtuales con configuraciones de red especiales</ol> |
| Restauración | El nombre DNS seleccionado ya existe: especifique otro nombre DNS y vuelva a intentarlo. | El nombre DNS aquí hace referencia al nombre del servicio en la nube (normalmente terminados con .cloudapp.net). Debe ser único. Si se produce este error, deberá elegir otro nombre para la máquina virtual durante la restauración. <br><br> Tenga en cuenta que este error solo se muestra a los usuarios del portal de Azure. La operación de restauración a través de PowerShell se realizará correctamente porque solo restaura los discos y no crea la máquina virtual. El error aparecerá cuando el usuario crea explícitamente la máquina virtual después de la operación de restauración del disco. |
| Restauración | La configuración de red virtual especificada no es correcta: especifique otra configuración de red virtual y vuelva a intentarlo. | None |
| Restauración | El servicio en la nube especificado usa una dirección IP reservada, que no coincide con la configuración de la máquina virtual que se está restaurando: especifique otro servicio en la nube que no use la IP reservada o elija otro punto de recuperación desde el que restaurar. | None |
| Restauración | El Servicio en la nube alcanzó el límite del número de puntos de entrada finales, vuelva a intentar la operación mediante la especificación de otro servicio en la nube o mediante un punto de conexión existente. | None |
| Restauración | La cuenta de almacenamiento de destino y el almacén de copia de seguridad están en dos regiones diferentes: compruebe que la cuenta de almacenamiento especificada en la operación de restauración se encuentra en la misma región de Azure que el almacén de copia de seguridad. | None |
| Restauración | La cuenta de almacenamiento especificada para la operación de restauración no se admite: solo se admiten las cuentas de almacenamiento básicas o estándar con una configuración de réplica con redundancia local o redundancia geográfica. Seleccione una cuenta de almacenamiento compatible | None |
| Restauración | El tipo de cuenta de almacenamiento especificado para la operación de restauración no está en línea: asegúrese de que la cuenta de almacenamiento especificada en la operación de restauración está en línea | Esto puede suceder debido a un error transitorio en el almacenamiento de Azure o debido a una interrupción. Elija otra cuenta de almacenamiento. |
| Restauración | Se alcanzó la cuota del grupo de recursos: elimine algunos grupos de recursos desde el portal de vista previa o póngase en contacto con el servicio de soporte técnico de Azure para aumentar los límites. | None |
| Restauración | La subred seleccionada no existe: seleccione una subred que exista | None |


## Directiva
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Creación de directiva | No se pudo crear la directiva: reduzca las opciones de retención para continuar con la configuración de directiva. | None |


## Agente de máquina virtual de Azure

### Configuración del agente de la máquina virtual
Normalmente, el agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure. Sin embargo, las máquinas virtuales que se migran desde los centros de datos locales no tienen instalado el agente de la máquina virtual. Para dichas máquinas virtuales, el agente de la máquina virtual debe instalarse explícitamente. Lea más sobre cómo [instalar el agente de la máquina virtual en una máquina virtual existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para las máquinas virtuales de Windows:

- Descargue e instale el [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación.
- [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado.

Máquinas virtuales de Linux:

- Instale el [Agente de Linux](https://github.com/Azure/WALinuxAgent) desde Github.
- [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado.


### Actualización del agente de la máquina virtual
Para las máquinas virtuales de Windows:

- Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sin embargo, deberá asegurarse de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual.

Máquinas virtuales de Linux:

- Siga las instrucciones proporcionadas en [Actualización del agente de VM de Linux](../virtual-machines-linux-update-agent.md).


### Validación de la instalación del agente de la máquina virtual
Cómo comprobar la versión del agente de la máquina virtual en máquinas virtuales de Windows:

1. Inicie sesión en la máquina virtual de Azure y vaya a la carpeta *C:\\WindowsAzure\\Packages*. El archivo WaAppAgent.exe debe estar ahí.
2. Haga clic con el botón derecho en el archivo, vaya a **Propiedades** y seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior.

## Solucionar problemas de instantáneas de máquina virtual
La copia de seguridad de máquina virtual se basa en la emisión de comandos de instantánea para el almacenamiento subyacente. No tener acceso al almacenamiento o el retraso en la ejecución de la tarea de instantáneas puede generar un error en la copia de seguridad. Lo siguiente puede producir un error en la tarea de instantáneas.

1. Se bloquea el acceso a la red al almacenamiento mediante NSG<br> Más información sobre cómo [habilitar el acceso a la red](backup-azure-vms-prepare.md#2-network-connectivity) para el almacenamiento mediante cualquier lista blanca de direcciones IP o a través del servidor proxy.
2.  Las máquinas virtuales con la copia de seguridad de SQL Server configurada pueden provocar el retraso de la tarea de instantáneas <br> De manera predeterminada la copia de seguridad de la máquina virtual emite la copia de seguridad completa en máquinas virtuales de Windows. En las máquinas virtuales que ejecutan servidores SQL Server y la copia de seguridad de SQL Server está configurada, esto podría provocar el retraso en la ejecución de la instantánea. Establezca la siguiente clave del Registro si se producen errores de la copia de seguridad debido a problemas de instantáneas.

	```
	[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
	"USEVSSCOPYBACKUP"="TRUE"
	```
3.  Estado de la máquina virtual notificado incorrectamente porque la máquina virtual está apagada en RDP. <br> Si ha apagado la máquina virtual en RDP, vuelva a comprobar en el portal que el estado de la máquina virtual se refleja correctamente. Si no es así, apague la máquina virtual en el portal mediante la opción 'Apagado' en el panel de la máquina virtual.
4.  Muchas máquinas virtuales del mismo servicio en la nube están configuradas para copia de seguridad al mismo tiempo.<br> Se recomienda propagar las máquinas virtuales del mismo servicio en la nube para tener diferentes programaciones de copia de seguridad.
5.  La máquina virtual se está ejecutando en alta CPU/memoria.<br> Si está ejecutando la máquina virtual en el uso o memoria altos de la CPU (más del 90 %), la tarea de instantáneas se pone en cola, se retrasa y el tiempo de espera se agotará finalmente. Pruebe la copia de seguridad a petición en esas situaciones.

<br>

## Redes
Al igual que todas las extensiones, la de copia de seguridad necesita tener acceso a Internet para funcionar. Si no tiene acceso a Internet, se pueden producir estos problemas:

- Se puede producir un error en la instalación de la extensión.
- Se puede producir un error en las operaciones de copia de seguridad (como al realizar la instantánea de disco).
- Se puede producir un error en la operación para mostrar el estado de la operación de copia de seguridad.

La necesidad de resolver direcciones públicas de Internet se describe [aquí](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Deberá comprobar las configuraciones de DNS para la red virtual y asegurarse de que se puedan resolver los URI de Azure.

Una vez que la resolución de nombres se haya realizado correctamente, también hay que proporcionar acceso a las direcciones IP de Azure. Para desbloquear el acceso a la infraestructura de Azure, siga uno de estos pasos:

1. Preparar una lista blanca con los intervalos IP del centro de datos de Azure.
    - Obtenga la lista de [IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) que van a formar parte de la lista de direcciones IP aprobadas.
    - Desbloquee las direcciones IP usando el cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx). Ejecute este cmdlet en la máquina virtual de Azure, en una ventana de PowerShell con privilegios elevados (realice la ejecución como administrador).
    - Agregar reglas al NSG (si dispone de uno implementado) para permitir el acceso a las direcciones IP.
2. Crear una ruta de acceso para el flujo del tráfico HTTP
    - Si tiene alguna restricción de red implementada (por ejemplo, un grupo de seguridad de red), implemente un servidor proxy HTTP para enrutar el tráfico. Encontrará [aquí](backup-azure-vms-prepare.md#2-network-connectivity) pasos para implementar un servidor proxy HTTP.
    - Agregue reglas al NSG (si dispone de uno implementado) para permitir el acceso a INTERNET desde el proxy HTTP.

>[AZURE.NOTE]DHCP debe estar habilitado dentro del invitado para que Copia de seguridad de VM de IaaS funcione. Si necesita una dirección IP privada estática, debe configurarla a través de la plataforma. La opción DHCP dentro de la máquina virtual debe continuar habilitada. Para más información acerca de cómo establecer una dirección IP estática privada interna, vaya [aquí](virtual-networks-reserved-private-ip.md).

<!---HONumber=AcomDC_0121_2016-->