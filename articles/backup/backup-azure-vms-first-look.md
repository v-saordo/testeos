<properties
	pageTitle="Protección de máquinas virtuales en Azure con Copia de seguridad de Azure | Microsoft Azure"
	description="Protección de máquinas virtuales de Azure con el servicio Copia de seguridad de Azure. El tutorial explica la creación de un almacén, el registro de máquinas virtuales, la creación de directivas y la protección de máquinas virtuales en Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/07/2016"
	ms.author="markgal; jimpark"/>


# Primer contacto: copia de seguridad de máquinas virtuales de Azure

Este artículo es un tutorial que le guiará por el conjunto de pasos que es preciso seguir para preparar un entorno de Azure para hacer copia de seguridad de una máquina virtual (VM) de Azure. En este tutorial se asume que tiene una máquina virtual en su suscripción de Azure y que ha tomado las medidas necesarias para que el servicio de copia de seguridad pueda acceder a dicha máquina virtual. A un alto nivel, estos son los pasos que debe completar.

1. Cree un almacén de copia de seguridad o identifique un almacén de copia de seguridad existente que se encuentre *en la misma región que la máquina virtual*.
2. Utilice el portal de Azure para detectar y registrar las máquinas virtuales en la suscripción.
3. Instale el agente de máquina virtual en la máquina virtual.
4. Proteja las máquinas virtuales: cree una directiva para realizar copias de seguridad de máquinas virtuales.
5. Ejecute la copia de seguridad.

>[AZURE.NOTE] Azure tiene dos modelos de implementación para crear y trabajar con recursos: [Administrador de recursos e implementación clásica](../resource-manager-deployment-model.md). Actualmente, el servicio Copia de seguridad de Azure no admite máquinas virtuales basada en el Administrador de recursos de Azure (ARM), también se las conoce como máquinas virtuales de IaaS V2. Dado que las máquinas virtuales de IaaS V2 surgieron con el lanzamiento del nuevo Portal de Azure, este tutorial se ha diseñado para que se use con el tipo de máquinas virtuales que se pueden crear en el Portal de Azure clásico.


## Paso 1: creación de un almacén de copia seguridad para una máquina virtual

Un almacén de copia de seguridad es una entidad que almacena todas las copias de seguridad y los puntos de recuperación creados con el tiempo. El almacén de copia de seguridad contiene también las directivas de copia de seguridad que se aplicarán a las máquinas virtuales cuya copia de seguridad se está realizando.

La siguiente imagen muestra las relaciones entre las diversas entidades de Copia de seguridad de Azure: ![Relaciones y entidades de Copia de seguridad de Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).

2. En el Portal de Azure, haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida** (vea la imagen siguiente).

    ![Crear un almacén de copia de seguridad](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

3. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

4. En **Región**, seleccione la región geográfica del almacén. El almacén **debe** estar en la misma región que las máquinas virtuales que desea proteger.

    Si no está seguro de en qué región existe la máquina virtual, cierre el cuadro de diálogo de creación del almacén y vaya a la lista de máquinas virtuales del portal. Si tiene máquinas virtuales en varias regiones, será preciso que cree un almacén de copia de seguridad en cada región (pero debe completar la creación del almacén de la primera región antes de pasar a la siguiente). No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de copia de seguridad y el servicio Copia de seguridad de Azure lo controlarán automáticamente.

5. En **Suscripción**, seleccione la suscripción que desea asociar con el almacén de copia de seguridad. Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.

6. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Un mensaje confirmará que el almacén ha creado correctamente. Se enumerará en la página **Servicios de recuperación** como **Activo**.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

7. En la lista de almacenes de la página **Servicios de recuperación** haga clic en el almacén que ha creado para iniciar la página **Inicio rápido**.

    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look/active-vault-demo.png)

8. En la página **Inicio rápido**, haga clic en **Configurar** para abrir la opción de replicación del almacenamiento. ![Lista de copias de seguridad](./media/backup-azure-vms-first-look/configure-storage.png)

9. En la opción de **replicación del almacenamiento**. elija la opción de replicación del almacén.

    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, se recomienda seguir utilizando el almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión del almacenamiento de copia de seguridad no principal, se puede considerar la posibilidad de elegir un almacenamiento con redundancia local, ya que ello reducirá el costo de almacenamiento de datos en Azure. En esta página de [información general](../storage/storage-redundancy.md), puede encontrar más información acerca de las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y con [redundancia local](../storage/storage-redundancy.md#locally-redundant-storage) .

Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## Paso 2: detección y registro de máquinas virtuales de Azure
Antes de registrar una máquina virtual en un almacén, ejecute el proceso de detección para asegurarse de que se identifican todas las nuevas máquinas virtuales que se hayan agregado a la suscripción. El proceso consulta a Azure la lista de máquinas virtuales incluidas en la suscripción, junto con información adicional, por ejemplo, el nombre del servicio en la nube y la región.

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).

2. En el Portal de Azure clásico, haga clic en **Servicios de recuperación** para abrir la lista de almacenes de Servicios de recuperación. ![Seleccionar carga de trabajo](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. En la lista de almacenes de **Servicios de recuperación**, seleccione el almacén que desea utilizar para realizar la copia de seguridad de una máquina virtual.

    Cuando seleccione el almacén, se abrirá en la página **Inicio rápido**.

4. En el menú del almacén (en la parte superior de la página), haga clic en **Elementos registrados**.

5. En el menú **Tipo**, seleccione **Máquina virtual de Azure**.

    ![Seleccionar carga de trabajo](./media/backup-azure-vms/discovery-select-workload.png)

6. Haga clic en **DETECTAR** en la parte inferior de la página. ![Botón Detectar](./media/backup-azure-vms/discover-button-only.png)

    El proceso de detección puede tardar unos minutos mientras se tabulan las máquinas virtuales. Hay una notificación en la parte inferior de la pantalla que informa de que el proceso se está ejecutando.

    ![Detectar máquinas virtuales](./media/backup-azure-vms/discovering-vms.png)

    La notificación cambia cuando el proceso se completa.

    ![Detección realizada](./media/backup-azure-vms-first-look/discovery-complete.png)

7. Haga clic en **REGISTRAR** en la parte inferior de la página. ![Botón Registrar](./media/backup-azure-vms-first-look/register-icon.png)

8. En el menú contextual **Elementos registrados**, seleccione las máquinas virtuales que desea registrar. Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguirlas.

    >[AZURE.TIP] Se pueden registrar varias máquinas virtuales al mismo tiempo.

    Se crea un trabajo para cada máquina virtual que ha seleccionado.

9. Haga clic en **Ver trabajo** en la notificación para ir a la página **Trabajos**.

    ![Registrar trabajo](./media/backup-azure-vms/register-create-job.png)

    La máquina virtual también aparece en la lista de elementos registrados junto con el estado de la operación de registro.

    ![Registrando estado 1](./media/backup-azure-vms/register-status01.png)

    Una vez completada la operación, el estado cambiará para reflejar el estado *registrado*.

    ![Registrando estado 2](./media/backup-azure-vms/register-status02.png)

## Paso 3: instalación del agente de máquina virtual en la máquina virtual

El agente de máquina virtual de Azure se debe instalar en la máquina virtual de Azure para que funcione la extensión de copia de seguridad. Si la máquina virtual se creó desde la galería de Azure, el agente de máquina virtual ya está presente en la máquina virtual. Sin embargo, las máquinas virtuales que se migran desde centros de datos locales no tienen instalado el agente de máquina virtual. En ese caso, el agente de máquina virtual debe instalarse explícitamente. Antes de intentar realiza una copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure está instalado correctamente en la máquina virtual (consulte la tabla siguiente). Si va a crear una máquina virtual personalizada, [asegúrese de que la casilla **Instalar el agente de máquina virtual** está activada](../virtual-machines/virtual-machines-extensions-agent-about.md) antes de que se aprovisione la máquina virtual.

Obtenga información acerca del [Agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y de [cómo instalarlo](../virtual-machines/virtual-machines-extensions-install.md).

La tabla siguiente proporciona información adicional acerca del agente de máquina virtual para las máquinas virtuales de Windows y Linux.

| **Operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual | <li>Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación. <li>[Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. | <li> Instale el [agente de Linux](https://github.com/Azure/WALinuxAgent) más reciente desde GitHub. Necesitará privilegios de administrador para efectuar la instalación. <li> [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |
| Actualización del agente de la máquina virtual | Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Asegúrese de que no se ejecuta ninguna operación de copia de seguridad durante la actualización del agente de máquina virtual. | Siga las instrucciones de [actualización del agente de máquina virtual con Linux](../virtual-machines-linux-update-agent.md). <br>Asegúrese de que no se ejecuta ninguna operación de copia de seguridad durante la actualización del agente de máquina virtual. |
| Validación de la instalación del agente de máquina virtual | <li>Navegue a la carpeta *C:\\WindowsAzure\\Packages* de la máquina virtual de Azure. <li>El archivo WaAppAgent.exe debe estar ahí.<li> Haga clic con el botón derecho en el archivo, vaya a **Propiedades** y, luego, seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior. | N/D |


### Extensión de copia de seguridad

Una vez que el agente de máquina virtual está instalado en la máquina virtual, el servicio Copia de seguridad de Azure instala la extensión de copia de seguridad en el agente de máquina virtual. El servicio Copia de seguridad de Azure actualiza y aplica revisiones perfectamente a la extensión de copia de seguridad sin la intervención del usuario.

El servicio Copia de seguridad instala la extensión de copia de seguridad tanto si la máquina virtual está en ejecución como si no lo está. Una máquina virtual en ejecución ofrece más probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Copia de seguridad de Azure realizará la copia de seguridad aunque esté apagada y no haya sido posible instalar la extensión. Esto se conoce como máquina virtual sin conexión. En este caso, el punto de recuperación será *coherente con los bloqueos*.


## Paso 4: protección de máquinas virtuales de Azure
Ahora puede configurar una directiva de retención y copia de seguridad para la máquina virtual. Se pueden proteger varias máquinas virtuales en una sola acción de protección. Los almacenes de Copia de seguridad de Azure creados después de mayo de 2015 incluyen una directiva predeterminada integrada en el almacén. Esta directiva predeterminada viene con un período de retención predeterminado de 30 días y una programación de copia de seguridad diaria.

1. Vaya al almacén de copias de seguridad en **Servicios de recuperación**, en el Portal de Azure, y haga clic en **Elementos registrados**.
2. Seleccione **Máquina virtual de Azure** en el menú desplegable.

    ![Seleccionar carga de trabajo en el portal](./media/backup-azure-vms/select-workload.png)

3. Haga clic en **PROTEGER** en la parte inferior de la página. ![Haga clic en Proteger](./media/backup-azure-vms-first-look/protect-icon.png)

    Aparece el **Asistente para proteger elementos**, que enumera *solo* las máquinas virtuales que están registradas y no protegidas.

    ![Configuración de protección a escala](./media/backup-azure-vms/protect-at-scale.png)

4. Seleccione las máquinas virtuales que desee proteger.

    Si hay dos o más máquinas virtuales con el mismo nombre, use el servicio en la nube para distinguirlas.

5. En **Configurar la protección**, seleccione una directiva existente o cree una directiva nueva para proteger las máquinas virtuales que ha identificado.

    Cada directiva de copia de seguridad puede tener asociadas varias máquinas virtuales. Sin embargo, la máquina virtual solo se puede asociar con una directiva en un momento determinado.

    ![Protección mediante nueva directiva](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] Una directiva de copia de seguridad incluye un esquema de retención de las copias de seguridad programadas. Si selecciona una directiva de copia de seguridad existente, no podrá modificar las opciones de retención en el paso siguiente.

6. En **Duración de retención** defina el ámbito diario, semanal, mensual y anual de los puntos de copia de seguridad específicos.

    ![Protección con retención flexible](./media/backup-azure-vms/policy-retention.png)

    La directiva de retención especifica el período de tiempo que se almacena una copia de seguridad. Puede especificar directivas de retención diferentes en función de cuándo se realizó la copia de seguridad. Por ejemplo, puede que el punto de copia de seguridad que se realiza al final de cada trimestre se tenga que conservar durante más tiempo (con fines de auditoría), mientras que el punto de copia de seguridad diario (que sirve como punto de recuperación operativo) solo debe mantenerse durante 90 días.

    ![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/long-term-retention.png)

    En esta imagen de ejemplo:

    - **Directiva de retención diaria**: las copias de seguridad realizadas diariamente se almacenan durante 30 días.
    - **Directiva de retención semanal**: las copias de seguridad realizadas cada domingo se conservarán durante 104 semanas.
    - **Directiva de retención mensual**: las copias de seguridad realizadas el último domingo de cada mes se conservarán durante 120 meses.
    - **Directiva de retención anual**: las copias de seguridad realizadas el primer domingo de cada mes de enero se conservarán durante 99 años.

    Se crea un trabajo para configurar la directiva de protección y asociar a esa directiva cada máquina virtual que ha seleccionado.

6. Haga clic en **Trabajo** y elija el filtro adecuado para ver la lista de trabajos **Configurar protección**.

    ![Configurar trabajo de protección](./media/backup-azure-vms/protect-configureprotection.png)


## Paso 5: copia de seguridad inicial

Una vez que una máquina virtual se ha protegido con una directiva, puede ver esa relación en la pestaña **Elementos protegidos**. Hasta que se realice la copia de seguridad inicial de una máquina virtual, **Estado de protección** muestra el valor **Protegido (pendiente de copia de seguridad inicial)**. De forma predeterminada, la primera copia de seguridad programada es la *copia de seguridad inicial*.

![Copia de seguridad pendiente](./media/backup-azure-vms-first-look/protection-pending-border.png)

Para desencadenar la copia de seguridad inicial inmediatamente después de configurar la protección:

1. En la parte inferior de la página **Elementos protegidos**, haga clic en el botón **Realizar copia de seguridad ahora**. ![Icono Realizar copia de seguridad ahora](./media/backup-azure-vms-first-look/backup-now-icon.png)

    El servicio Copia de seguridad de Azure crea un trabajo de copia de seguridad para la operación de copia de seguridad inicial.

2. Haga clic en la pestaña **Trabajos** para ver la lista de los trabajos.

    ![Copia de seguridad en curso](./media/backup-azure-vms-first-look/protect-inprogress.png)

    >[AZURE.NOTE] Como parte de la operación de copia de seguridad, el servicio Copia de seguridad de Azure emite un comando a la extensión de copia de seguridad en cada máquina virtual para vaciar toda la escritura y tomar una instantánea coherente.

    Una vez completada la copia de seguridad inicial, el estado de la máquina virtual en la pestaña **Elementos protegidos** se mostrará como *Protegido*.

    ![Se realiza una copia de seguridad de la máquina virtual con punto de recuperación](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] La copia de seguridad de máquinas virtuales es un proceso local. No puede realizar copias de seguridad de máquinas virtuales desde una región hasta un almacén de copia de seguridad de otra región. Por lo tanto, para cada región de Azure que tiene máquinas virtuales que necesiten una copia de seguridad, debe crearse al menos un almacén de copia de seguridad en esa región.

## Pasos siguientes
Una vez que se ha copiado correctamente una máquina virtual, hay varios pasos que pueden ser de interés. El paso más lógico es familiarizarse con la restauración de datos a una máquina virtual; sin embargo, hay tareas de administración que le ayudarán a saber cómo mantener los datos seguros y, al mismo tiempo, contener los costos.

- [Administración y supervisión de las máquinas virtuales](backup-azure-manage-vms.md)
- [Restauración de máquinas virtuales](backup-azure-restore-vms.md)
- [Guía de solución de problemas](backup-azure-vms-troubleshoot.md)


## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0309_2016-->