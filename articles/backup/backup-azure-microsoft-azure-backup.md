<properties
  pageTitle="Preparación del entorno para realizar copias de seguridad de cargas de trabajo con el servidor de Copia de seguridad de Azure | Microsoft Azure"
  description="Asegúrese de que el entorno está preparado correctamente para hacer copias de seguridad de las cargas de trabajo con el servidor de Copia de seguridad de Azure"
  services="backup"
  documentationCenter=""
  authors="Jim-Parker"
  manager="jwhit"
  editor=""
  keywords="servidor de copia de seguridad de Azure; almacén de copia de seguridad"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="02/04/2016"
  ms.author="jimpark; trinadhk;"/>

# Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure

> [AZURE.SELECTOR]
- [Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)
- [System Center DPM](backup-azure-dpm-introduction.md)

En este artículo, se trata cómo preparar el entorno para hacer copias de seguridad de las cargas de trabajo con el servidor de Copia de seguridad de Azure. Con el servidor de Copia de seguridad de Azure, puede proteger cargas de trabajo de aplicaciones como máquinas virtuales de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange y clientes Windows desde una única consola:

>[AZURE.WARNING] El servidor de Copia de seguridad de Azure hereda la funcionalidad de Data Protection Manager (DPM) para copias de seguridad de cargas de trabajo. Encontrará punteros para la documentación de DPM para algunas de estas capacidades. Sin embargo, el servidor de Copia de seguridad de Azure no ofrece protección en cinta o integración con System Center.

## 1\. Máquina de Windows Server

![step1](./media/backup-azure-microsoft-azure-backup/step1.png)

El primer paso para que funcione el servidor de Copia de seguridad de Azure es tener una máquina Windows Server.

| Ubicación | Requisitos mínimos | Instrucciones adicionales |
| -------- | -------------------- | ----------------------- |
| Azure | Máquina virtual de Azure IaaS<br><br>A2 Standard: 2 núcleos, 3,5 GB de RAM | Puede comenzar por una galería de imágenes sencilla de Windows Server 2012 R2 Datacenter. [La protección de cargas de trabajo de IaaS con el servidor de Copia de seguridad de Azure (DPM)](https://technet.microsoft.com/library/jj852163.aspx) presenta numerosos matices. Asegúrese de leer el artículo completo antes de implementar la máquina. |
| Local | Máquina virtual de Hyper-V,<br> Máquina virtual de VMWare<br> o un host físico<br><br>2 núcleos y 4 GB de RAM | Puede desduplicar el almacenamiento de DPM con la desduplicación de Windows Server. Obtenga más información sobre cómo funcionan [DPM y la desduplicación](https://technet.microsoft.com/library/dn891438.aspx) juntos al implementarlos en las máquinas virtuales de Hyper-V. |

> [AZURE.NOTE] Se recomienda instalar el servidor de Copia de seguridad de Azure en una máquina con Windows Server 2012 R2 Datacenter. Muchos de los requisitos previos quedan resueltos automáticamente con la versión más reciente del sistema operativo Windows.

Si planea unir este servidor a un dominio en algún momento, se recomienda que la actividad de unión a dominio se realice antes de la instalación del servidor de Copia de seguridad de Azure. *No se permite* mover una máquina del servidor de Copia de seguridad de Azure existente a un dominio nuevo después de la implementación.

## 2\. Almacén de copia de seguridad

![step2](./media/backup-azure-microsoft-azure-backup/step2.png)

Tanto si envía datos de copia de seguridad a Azure como si los mantiene localmente, el software debe estar conectado a Azure. Más concretamente, la máquina del servidor de Copia de seguridad de Azure debe estar registrada en un almacén de copia de seguridad.

Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com/).

2. Haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida**. Si tiene varias suscripciones asociadas a su cuenta profesional, elija la suscripción adecuada que se asociará al almacén de copia de seguridad.

3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. Esto debe ser único para cada suscripción.

4. En **Región**, seleccione la región geográfica del almacén. Normalmente, la región del almacén se selecciona basándose en las restricciones de latencia de red o de soberanía de los datos.

    ![Crear un almacén de copia de seguridad](./media/backup-azure-microsoft-azure-backup/backup_vaultcreate.png)

5. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-microsoft-azure-backup/creating-vault.png)

6. Un mensaje confirma que el almacén se ha creado correctamente y se mostrará en la página de servicios de recuperación como activo. ![Lista de copias de seguridad](./media/backup-azure-microsoft-azure-backup/backup_vaultslist.png)

  > [AZURE.IMPORTANT] Asegúrese de que se ha elegido la opción de redundancia de almacenamiento apropiada justo después de que se ha creado el almacén. Obtenga más información sobre las opciones [con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y [con redundancia local](../storage/storage-redundancy.md#locally-redundant-storage) en esta [Introducción](../storage/storage-redundancy.md).


## 3\. Paquete de software

![step3](./media/backup-azure-microsoft-azure-backup/step3.png)

### Descarga del paquete de software

De forma similar a las credenciales del almacén, puede descargar Copia de seguridad de Microsoft Azure para cargas de trabajo de aplicaciones desde la **página de inicio rápido** del almacén de copia de seguridad.

1. Haga clic en **For Application Workloads (Disk to Disk to Cloud)** (Para cargas de trabajo de aplicaciones (de disco a disco y a nube)). Esto le llevará a la página del Centro de descargas donde podrá descargar el paquete de software.

    ![Pantalla de bienvenida de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Haga clic en **Descargar**.

    ![Centro de descarga 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Seleccione todos los archivos y haga clic en **Siguiente**. Descargue todos los archivos procedentes de la página de descarga de Copia de seguridad de Microsoft Azure y colóquelos en la misma carpeta. ![Centro de descarga 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Puesto que el tamaño de descarga de todos los archivos juntos es de más de 3 GB, con un vínculo de descarga a 10 Mbps, se puede tardar hasta 60 minutos en completarla.


### Extracción del paquete de software

Después de descargar todos los archivos, haga clic en **MicrosoftAzureBackupInstaller.exe**. Esto iniciará el **Asistente para instalación de Copia de seguridad de Microsoft Azure** y extraerá los archivos de instalación en una ubicación especificada por el usuario. Siga con el asistente y haga clic en el botón **Extraer** para comenzar el proceso de extracción.

> [AZURE.WARNING] Se requieren al menos 4 GB de espacio libre para extraer los archivos de instalación.


![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Una vez completado el proceso de extracción, marque la casilla para iniciar el archivo *setup.exe* recién extraído y empezar a instalar el servidor de Copia de seguridad de Microsoft Azure y, a continuación, haga clic en el botón **Finalizar**.

### Instalación del paquete de software

1. Haga clic en **Copia de seguridad de Microsoft Azure** para iniciar el asistente para la instalación.

    ![Asistente para instalación de Copia de seguridad de Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. En la pantalla de bienvenida, haga clic en **Siguiente**. Esto le lleva a la sección *Comprobaciones de requisitos previos*. En esta pantalla, haga clic en el botón **Comprobar** para determinar si se cumplieron los requisitos previos de hardware y software para el servidor de Copia de seguridad de Azure. Si se cumplieron todos los requisitos previos, verá un mensaje que indica que la máquina los cumple. Haga clic en el botón **Siguiente**.

    ![Servidor de Copia de seguridad Azure - Bienvenida y requisitos previos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. El servidor de Copia de seguridad de Microsoft Azure requiere SQL Server Standard y en el paquete de instalación del servidor de Copia de seguridad de Azure se incluyen los correspondientes archivos binarios de SQL Server necesarios. Cuando comience una nueva instalación del servidor de Copia de seguridad de Azure, debe elegir la opción **Instalar nueva instancia de SQL Server con esta configuración** y hacer clic en el botón **Comprobar e instalar**. Una vez que los requisitos previos se instalen correctamente, haga clic en **Next** (Siguiente).

    ![Servidor de Copia de seguridad de Azure - Comprobación de SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Si se produce un error con una recomendación para reiniciar el equipo, proceda a reiniciar y haga clic en **Check Again** (Comprobar de nuevo).

    > [AZURE.NOTE] El servidor de Copia de seguridad de Azure no funcionará con una instancia remota de SQL Server. La instancia que se utiliza en el servidor de Copia de seguridad de Azure debe ser local.

4. Proporcione una ubicación donde instalar los archivos del servidor de Copia de seguridad de Microsoft Azure y haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    La ubicación temporal es un requisito para hacer copias de seguridad en Azure. Asegúrese de que la ubicación temporal sea al menos el 5% de los datos cuya copia de seguridad se planea hacer en la nube. Para la protección de disco, deben configurarse discos independientes una vez completada la instalación. Para obtener más información acerca de los grupos de almacenamiento, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).

5. Proporcione una contraseña segura para las cuentas de usuario locales con permisos restringidos y haga clic en **Next** (Siguiente).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. Seleccione si desea usar *Microsoft Update* para comprobar si hay actualizaciones y haga clic en **Next** (Siguiente).

    >[AZURE.NOTE] Se recomienda que Windows Update se redirija a Microsoft Update, que ofrece actualizaciones importantes y de seguridad para Windows y otros productos como el servidor de Copia de seguridad de Microsoft Azure.

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. Revise *Summary of Settings* (Resumen de la configuración) y haga clic en **Install** (Instalar).

    ![Requisitos previos de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. La instalación se realiza en fases. En la primera fase, se instala el agente de Servicios de recuperación de Microsoft Azure en el servidor. El asistente comprueba igualmente la conectividad a Internet. Si la conectividad a Internet está disponible, puede continuar con la instalación; de lo contrario, debe proporcionar los detalles del proxy para conectarse a Internet.

    El siguiente paso es configurar el agente de Servicios de recuperación de Microsoft Azure. Como parte de la configuración, tendrá que proporcionar las credenciales del almacén para registrar la máquina en el almacén de copia de seguridad. También proporcionará una frase de contraseña para cifrar y descifrar los datos enviados entre Azure y sus instalaciones. Puede generar una frase de contraseña automáticamente o proporcionar la suya propia, con un mínimo de 16 caracteres. Continúe con el asistente hasta que se haya configurado el agente.

    ![Requisitos previos del servidor de Copia de seguridad de Microsoft Azure2](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Una vez que se complete correctamente el registro del servidor de Copia de seguridad de Microsoft Azure, el asistente para instalación global prosigue con la instalación y configuración de los componentes de SQL Server y de Copia de seguridad de Microsoft Azure. Tras completarse la instalación de los componentes de SQL Server, se instalan los componentes del servidor de Copia de seguridad de Azure.

    ![Servidor de Copia de seguridad de Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


Cuando el paso de instalación haya finalizado, se habrán creado también los iconos de escritorio del producto. Haga doble clic en el icono para iniciar el producto.

### Incorporación de almacenamiento de copia de seguridad

La primera copia de seguridad se mantiene en el almacenamiento conectado a la máquina del servidor de Copia de seguridad de Azure. Para obtener más información acerca de los discos, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).

> [AZURE.NOTE] Debe agregar el almacenamiento de copia de seguridad incluso si tiene pensado enviar los datos a Azure. En la arquitectura del servidor de Copia de seguridad de Azure actual, el almacén de Copia de seguridad de Azure contiene la *segunda* copia de los datos mientras que el almacenamiento local contiene la primera (y obligatoria) copia de seguridad.

## 4\. Conectividad de red

![step4](./media/backup-azure-microsoft-azure-backup/step4.png)

El servidor de Copia de seguridad de Azure requiere conectividad al servicio de Copia de seguridad de Azure para que el producto funcione correctamente. Para validar si la máquina tiene conectividad a Azure, use el commandlet ```Get-DPMCloudConnection``` en la consola del servidor de Copia de seguridad de Azure PowerShell. Si la salida del commandlet es TRUE, entonces existe conectividad, de lo contrario, no hay conectividad.

Además, la suscripción de Azure debe encontrarse en un estado correcto. Para averiguar el estado de la suscripción y administrarla, inicie sesión en el [portal de suscripción](https://account.windowsazure.com/Subscriptions).

Una vez que conozca el estado de la conectividad y suscripción de Azure, puede usar la tabla siguiente para saber el impacto en la funcionalidad de copia de seguridad y restauración que se ofrece.

| Estado de conectividad | Suscripción de Azure | Copia de seguridad en Azure| Copia de seguridad en disco | Restauración desde Azure | Restauración desde disco |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Conectado | Active | Permitida | Permitida | Permitida | Permitida |
| Conectado | Expirada | Stopped | Stopped | Permitida | Permitida |
| Conectado | Desaprovisionada | Stopped | Stopped | Detenida y puntos de recuperación de Azure eliminados | Stopped |
| Pérdida de conectividad > 15 días | Active | Stopped | Stopped | Permitida | Permitida |
| Pérdida de conectividad > 15 días | Expirada | Stopped | Stopped | Permitida | Permitida |
| Pérdida de conectividad > 15 días | Desaprovisionada | Stopped | Stopped | Detenida y puntos de recuperación de Azure eliminados | Detenido |

### Recuperación de una pérdida de conectividad
Si tiene un firewall o un proxy que impide el acceso a Azure, deberá permitir primero las siguientes direcciones de dominio en el perfil del firewall/proxy:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

Una vez restaurada la conectividad a Azure en la máquina del servidor de Copia de seguridad de Azure, las operaciones que pueden realizarse dependen del estado de la suscripción de Azure. La tabla anterior incluye detalles acerca de las operaciones permitidas una vez que la máquina esté "Conectada".

### Control de los estados de la suscripción

Es posible tener una suscripción de Azure desde un estado *Expirado* o *Desaprovisionado* hasta un estado *Activo*. Sin embargo, esto tiene algunas implicaciones en el comportamiento del producto mientras el estado no sea *Activo*:

- Una suscripción *Desaprovisionada* pierde la funcionalidad durante el período en el que está desaprovisionada. Al pasar a *Activa*, se reactivará la funcionalidad del producto de copia de seguridad y restauración. Los datos de copia de seguridad del disco local también pueden recuperarse en caso de que se haya mantenido con un período de retención lo suficientemente amplio. No obstante, los datos de copia de seguridad de Azure se pierden irremediablemente una vez que la suscripción pasa al estado *Desaprovisionado*.
- Una suscripción *Expirada* solo pierde la funcionalidad hasta que pase de nuevo a estar *Activa*. Las copias de seguridad programadas para el período en el que la suscripción estaba *Expirada* no se ejecutarán.


## Solución de problemas

Si el servidor de Copia de seguridad de Microsoft Azure produce un error durante la fase de instalación (o copia de seguridad o restauración), consulte este [documento de códigos de error](https://support.microsoft.com/kb/3041338) para obtener más información. También puede consultar [Copia de seguridad de Azure - Preguntas más frecuentes](backup-azure-backup-faq.md).


## Pasos siguientes

Puede obtener información detallada sobre la [preparación del entorno para DPM](https://technet.microsoft.com/library/hh758176.aspx) en el sitio de Microsoft TechNet. También contiene información sobre las configuraciones admitidas en las que se puede implementar y usar el servidor de Copia de seguridad de Azure.

Puede usar estos artículos para mejorar la comprensión sobre la protección de cargas de trabajo mediante el servidor de Copia de seguridad de Microsoft Azure.

- [Copia de seguridad de SQL Server](backup-azure-backup-sql.md)
- [Copia de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint.md)
- [Copia de seguridad de otro servidor](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0302_2016-->