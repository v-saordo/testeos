<properties
	pageTitle="Preparación del entorno para realizar copias de seguridad de máquinas virtuales de Azure | Microsoft Azure"
	description="Asegúrese de que el entorno está preparado para la copia de seguridad de máquinas virtuales en Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="copias de seguridad; realizar copia de seguridad"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/01/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Preparación del entorno de copia de seguridad de máquinas virtuales de Azure

Para hacer copias de seguridad de una máquina virtual (VM) de Azure, deben darse tres condiciones.

- Tiene que crear un almacén de copia de seguridad o identificar un almacén de copia de seguridad existente *en la misma región que la máquina virtual*.
- Establecer la conectividad de red entre las direcciones de Internet públicas Azure y los puntos de conexión de almacenamiento de Azure.
- Instalar el agente de máquina virtual en la VM.

Si sabe que estas condiciones ya existen en su entorno, vaya al artículo [Copias de seguridad de la máquinas virtuales](backup-azure-vms.md). De lo contrario, continúe leyendo, este artículo le guiará por los pasos para preparar el entorno para realizar la copia de una VM de Azure.


## Limitaciones al realizar copias de seguridad y restaurar una máquina virtual

>[AZURE.NOTE] Azure tiene dos modelos de implementación para crear y trabajar con recursos: [Clásico y Administrador de recursos](../resource-manager-deployment-model.md). La siguiente lista proporciona las limitaciones cuando se implementa en el modelo clásico.

- Actualmente no se admite la copia de seguridad de máquinas virtuales (también llamadas IaaS V2) basadas en Azure Resource Manager (ARM).
- No se admite la copia de seguridad de máquinas virtuales con más de 16 discos de datos.
- No se admite la copia de seguridad de máquinas virtuales con el almacenamiento Premium.
- No se admite la copia de seguridad de máquinas virtuales con una dirección IP reservada y sin puntos de conexión definidos.
- No se admite el reemplazo de una máquina virtual existente durante la restauración. Primero, elimine la máquina virtual existente y los discos asociados y, a continuación, restaure los datos de copia de seguridad.
- No se admite la restauración y copia de seguridad entre regiones.
- Se admite la copia de seguridad de máquinas virtuales mediante el uso del servicio Copia de seguridad de Azure en todas las regiones públicas de Azure (consulte la [lista de comprobación](https://azure.microsoft.com/regions/#services) de las regiones compatibles). Si la región que está buscando no es compatible actualmente, no aparecerá en la lista desplegable durante la creación del almacén.
- La copia de seguridad de máquinas virtuales con el servicio Copia de seguridad de Azure solo se admite en determinadas versiones de sistemas operativos:
  - **Linux**: Consulte [la lista de distribuciones aprobadas por Azure](../virtual-machines/virtual-machines-linux-endorsed-distributions.md). Otras distribuciones con la iniciativa "traiga su propio Linux" también deberían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual.
  - **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.
	- La restauración de una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio solo se admite a través de PowerShell. Más información sobre cómo [restaurar un controlador de dominio de varios controladores de dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms)
	- Solo se admite la restauración de las máquinas virtuales que tienen las siguientes configuraciones especiales de red a través de PowerShell. Las máquinas virtuales que se crean con el flujo de trabajo de restauración en la interfaz de usuario no tendrán estas configuraciones de red cuando se complete la operación de restauración. Si desea obtener más información, consulte [Restauración de máquinas virtuales con configuraciones de red especiales](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
		- Máquinas virtuales con la configuración del equilibrador de carga (interna y externa).
		- Máquinas virtuales con varias direcciones IP reservadas.
		- Máquinas virtuales con varios adaptadores de red.

## Creación de un almacén de copia de seguridad para una máquina virtual

Un almacén de copia de seguridad es una entidad que almacena todas las copias de seguridad y los puntos de recuperación que se han creado con el tiempo. El almacén de copia de seguridad contiene también las directivas de copia de seguridad que se aplicarán a las máquinas virtuales de una copia de seguridad.

La siguiente imagen muestra las relaciones entre las diversas entidades de Copia de seguridad de Azure: ![Relaciones y entidades de Copia de seguridad de Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).

2. En el Portal de Azure, haga clic en **Nuevo** > **Integración híbrida** > **Copia de seguridad**. Al hacer clic en **Copia de seguridad**, cambiará automáticamente al portal clásico (que aparece después de la nota).

    ![Portal de Ibiza](./media/backup-azure-vms-prepare/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] Si la suscripción se usó por última vez en el portal clásico, es posible que la suscripción se abra en el portal clásico. En este caso, para crear un almacén de copia de seguridad, haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida** (vea la imagen siguiente).

    ![Crear un almacén de copia de seguridad](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

4. En **Región**, seleccione la región geográfica del almacén. El almacén debe estar en la misma región que las máquinas virtuales que desea proteger. Si tiene máquinas virtuales en varias regiones, debe crear un almacén de copia de seguridad en cada región. No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de copia de seguridad y el servicio Copia de seguridad de Azure lo controlarán automáticamente.

5. En **Suscripción** seleccione la suscripción que desea asociar al almacén de copia de seguridad. Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.

6. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-prepare/creating-vault.png)

7. Un mensaje confirmará que el almacén se creó correctamente. Aparecerá en la página **Servicios de recuperación** como **Activo**. Asegúrese de elegir la opción de redundancia de almacenamiento apropiada justo después de crear el almacén. Lea más sobre cómo [establecer la opción de redundancia de almacenamiento en el almacén de copia de seguridad](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Lista de copias de seguridad](./media/backup-azure-vms-prepare/backup_vaultslist.png)

8. Haga clic en el almacén de copia de seguridad para ir a la página **Inicio rápido**, donde se muestran las instrucciones para realizar la copia de seguridad de las máquinas virtuales de Azure.

    ![Instrucciones de copia de seguridad de máquina virtual en la página del Panel](./media/backup-azure-vms-prepare/vmbackup-instructions.png)


## Conectividad de red

La extensión de copia de seguridad necesita conectividad a las direcciones IP públicas de Azure para funcionar correctamente, puesto que envía comandos a un punto de conexión de Almacenamiento de Azure (dirección URL HTTP) para administrar las instantáneas de la máquina virtual. Sin la conectividad correcta a Internet, estas solicitudes HTTP de la máquina virtual agotarán el tiempo de espera y la operación de copia de seguridad dará error.

### Restricciones de red con grupos de seguridad de red

Si la implementación tiene restricciones de acceso (a través de un grupo de seguridad de red, por ejemplo), deberá realizar pasos adicionales para asegurarse de que el tráfico de copia de seguridad en el almacén de copia de seguridad no se vea afectado.

Hay dos formas de proporcionar una ruta de acceso al tráfico de copia de seguridad:

1. Preparar una lista blanca con los [intervalos IP del centro de datos de Azure](http://www.microsoft.com/es-ES/download/details.aspx?id=41653).
2. Implementar un proxy HTTP para enrutar el tráfico.

Equilibrio entre la facilidad de uso, un control detallado y el costo.

|Opción|Ventajas|Desventajas|
|------|----------|-------------|
|OPCIÓN 1: Preparar una lista blanca con intervalos IP| Sin costos adicionales.<br><br>Para abrir el acceso en un grupo de seguridad de red, use el cmdlet <i>Set-AzureNetworkSecurityRule</i>. | Es complejo de administrar, ya que los intervalos IP afectados cambian con el tiempo.<br>Proporciona acceso a la totalidad de Azure y no solo al almacenamiento.|
|OPCIÓN 2: Proxy HTTP| Se permite un control detallado en el proxy sobre las direcciones URL de almacenamiento.<br>Un único punto de acceso a Internet a las máquinas virtuales.<br>No están sujetas a cambios de direcciones IP de Azure.| Costes adicionales de ejecutar una máquina virtual con el software de proxy.|

### Uso de un proxy HTTP para las copias de seguridad de máquinas virtuales
Cuando se realiza una copia de seguridad de una máquina virtual, los comandos de administración de instantáneas se envían desde la extensión de copia de seguridad al Almacenamiento de Azure mediante una API de HTTPS. Este tráfico debe enrutarse a la extensión a través del proxy, ya que solo el proxy se configurará para que tenga acceso a la red pública de Internet.

>[AZURE.NOTE] No hay ninguna recomendación que deba usarse para el software de proxy. Asegúrese de que selecciona un proxy que sea compatible con los pasos de configuración que se mencionan a continuación.

En el ejemplo siguiente, la máquina virtual de la aplicación debe configurarse para usar la máquina virtual de proxy para todo el tráfico HTTP enlazado a la red pública de Internet. La máquina virtual de proxy debe configurarse para permitir el tráfico entrante desde máquinas virtuales en la red virtual. Y por último, el grupo de seguridad de red (denominado *NSG-lockdown*) necesita una nueva regla de seguridad que permite el tráfico saliente de Internet desde la máquina virtual de proxy.

![Grupo de seguridad de red con el diagrama de implementación de proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) Permitir conexiones de red salientes:**

1. En las máquinas Windows, ejecute el comando siguiente en un símbolo del sistema con privilegios elevados:

    ```
    netsh winhttp set proxy http://<proxy IP>:<proxy port>
    ```
    Se configurará el proxy en todo el equipo y se usará para el tráfico saliente de HTTP/HTTPS.

2. Para las máquinas de Linux, agregue la siguiente línea al archivo ```/etc/environment```:

    ```
    http_proxy=http://<proxy IP>:<proxy port>
    ```

  Agregue las líneas siguientes al archivo ```/etc/waagent.conf```:

    ```
    HttpProxy.Host=<proxy IP>
    HttpProxy.Port=<proxy port>
    ```

**B) Permitir las conexiones entrantes en el servidor proxy:**

1. En el servidor proxy, abra Firewall de Windows. La manera más fácil de acceder al firewall es buscar Firewall de Windows con seguridad avanzada.

    ![Abrir el firewall](./media/backup-azure-vms-prepare/firewall-01.png)

2. En el cuadro de diálogo Firewall de Windows, haga clic con el botón derecho en **Reglas de entrada** y haga clic en **Nueva regla...**.

    ![Crear una nueva regla](./media/backup-azure-vms-prepare/firewall-02.png)

3. En **Asistente para nueva regla de entrada**, elija la opción **Personalizada** para el **Tipo de regla** y haga clic en **Siguiente**.
4. En la pantalla para seleccionar el **programa**, elija **Todos los programas** y haga clic en **Siguiente**.

5. En la página **Protocolo y puertos**, escriba la información siguiente y haga clic en **Siguiente**:

    ![Crear una nueva regla](./media/backup-azure-vms-prepare/firewall-03.png)

    - en *Tipo de protocolo* elija *TCP*
    - en *Puerto Local* elija *Puertos específicos* y, en el campo siguiente, especifique el ```<Proxy Port>``` configurado.
    - en *Puerto remoto* seleccione *Todos los puertos*

    Durante el resto del asistente, haga clic en todas las pantallas hasta el final y asigne un nombre a esta regla.

**C) Agregar una regla de excepción al grupo de seguridad de red:**

En un símbolo del sistema de Azure PowerShell, ejecute el siguiente comando:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

Este comando agrega una excepción al grupo de seguridad de red, lo que permite el tráfico TCP desde cualquier puerto en 10.0.0.5 a cualquier dirección de Internet en el puerto 80 (HTTP) o 443 (HTTPS). Si necesita acceder a un puerto específico en la red pública de Internet, asegúrese de que lo agrega también a ```-DestinationPortRange```.

*Asegúrese de sustituir los nombres en el ejemplo con los detalles correspondientes a la implementación.*


## Agente de máquina virtual

Antes de empezar a realizar la copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure esté instalado correctamente en la máquina virtual. Como el agente de máquina virtual es un componente opcional en el momento de crearse la máquina virtual, asegúrese de que la casilla correspondiente al agente de máquina virtual esté activada antes de aprovisionar la máquina virtual.

### Instalación manual y actualización

El agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure. Sin embargo, las máquinas virtuales que se migran desde los centros de datos locales no tienen instalado el agente de la máquina virtual. Para dichas máquinas virtuales, el agente de la máquina virtual debe instalarse explícitamente. Lea más sobre cómo [instalar el agente de la máquina virtual en una máquina virtual existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual | <li>Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación. <li>[Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. | <li> Instale el [agente de Linux](https://github.com/Azure/WALinuxAgent) más reciente desde GitHub. Necesitará privilegios de administrador para efectuar la instalación. <li> [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |
| Actualización del agente de la máquina virtual | Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. | Siga las instrucciones que aparecen en [Actualización del agente de VM de Linux](../virtual-machines-linux-update-agent.md). <br><br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |
| Validación de la instalación del agente de máquina virtual | <li>Navegue a la carpeta *C:\\WindowsAzure\\Packages* en la VM de Azure. <li>El archivo WaAppAgent.exe debe estar ahí.<li> Haga clic con el botón derecho en el archivo, vaya a **Propiedades** y, luego, seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior. | N/D |


Obtenga información acerca del [Agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y de [cómo instalarlo](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extensión de copia de seguridad

Para crear la copia de seguridad de la máquina virtual, el servicio Copia de seguridad de Azure instala una extensión al agente de máquina virtual. El servicio Copia de seguridad de Azure actualiza y aplica revisiones perfectamente a la extensión de copia de seguridad sin la intervención del usuario.

Si se ejecuta la máquina virtual, se instala la extensión de copia de seguridad. Una máquina virtual en ejecución también ofrece la mayor probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Copia de seguridad de Azure continuará realizando una copia de seguridad de la máquina virtual, incluso si está desactivada y la extensión no se ha podido instalar (lo que se conoce también como máquina virtual sin conexión). En este caso, el punto de recuperación será *coherente frente a bloqueos*, como se explicó anteriormente.


## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## Pasos siguientes
Ahora que ha preparado el entorno para realizar la copia de seguridad de la máquina virtual, el siguiente paso lógico es crear una copia de seguridad. El artículo de planeamiento ofrece información más detallada sobre la realización de copias de seguridad de máquinas virtuales.

- [Copia de seguridad de máquinas virtuales](backup-azure-vms.md)
- [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
- [Administración de copias de seguridad de máquinas virtuales](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0302_2016-->