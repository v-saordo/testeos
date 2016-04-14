<properties
	pageTitle="Preparación del entorno para la copia de seguridad de una máquina cliente o servidor de Windows | Microsoft Azure"
	description="Para preparar el entorno para realizar la copia de seguridad de Windows, cree un almacén de copias de seguridad, descargue las credenciales e instale el agente de copia de seguridad."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="almacén de copia de seguridad; agente de copia de seguridad; copia de seguridad de Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Preparación del entorno para la copia de seguridad de máquinas de Windows en Azure
En este artículo se enumera todo lo que se debe hacer para preparar el entorno para la copia de seguridad de una máquina Windows en Azure.

| Paso | Nombre | Detalles |
| :------: | ---- | ------- |
| 1 | [Creación de un almacén](#create-a-backup-vault) | Creación de un almacén en el [Portal de administración de Copia de seguridad de Azure](http://manage.windowsazure.com) |
| 2 | [Descarga de las credenciales de almacén](#download-the-vault-credential-file) | Descarga de las credenciales de almacén que se usarán para registrar la máquina de Windows con el almacén de copia de seguridad |
| 3 | [Instalación del agente de Copia de seguridad de Azure](#download-install-and-register-the-azure-backup-agent) | Instalación del agente y registro del servidor en el almacén de copia de seguridad con las credenciales de almacén |

Si ya ha realizado todos los pasos de nivel superior anteriores, puede comenzar a [realizar la copia de seguridad de las máquinas virtuales](backup-azure-backup-windows-server.md). Si no es así, siga estos pasos detallados para asegurarse de que el entorno esté listo.

## Antes de comenzar
Para preparar el entorno para la copia de seguridad de máquinas de Windows, necesita una cuenta de Azure. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) en tan solo unos minutos.

## Creación de un almacén de copia de seguridad
Para hacer una copia de seguridad de archivos y datos desde una máquina de Windows o desde Data Protection Manager (DPM) en Azure o al realizar copias de seguridad de máquinas virtuales de IaaS en Azure, debe crear un almacén de copia de seguridad en la región geográfica donde quiera almacenar los datos.

**Para crear un almacén de copia de seguridad:**

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).

2. Haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** y elija **Creación rápida**.

    ![Crear almacén](./media/backup-configure-vault/createvault1.png)

3. Para el parámetro **Nombre**, escriba un nombre descriptivo para identificar el almacén de copia de seguridad. Esto debe ser único para cada suscripción.

    Para el parámetro **Región**, seleccione la región geográfica para el almacén de credenciales de copia de seguridad. La elección determina la región geográfica a la que se envían los datos de copia de seguridad. Si elige una región geográfica cerca de su ubicación, puede reducir la latencia de red al hacer una copia de seguridad en Azure.

    Haga clic en **Crear almacén** para completar el flujo de trabajo.

    La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal.

    ![Creación de almacén](./media/backup-configure-vault/creatingvault1.png)

    Después de crear el almacén de copia de seguridad, verá un mensaje que indica que el almacén se ha creado correctamente. El almacén aparece también en los recursos de Servicios de recuperación como**Activo**.

    ![Estado de creación de almacén](./media/backup-configure-vault/backupvaultstatus1.png)

    >[AZURE.IMPORTANT] El mejor momento para identificar la opción de redundancia de almacenamiento es justo después de la creación del almacén y antes de que las máquinas se registren en este. Una vez que un elemento se ha registrado en el almacén, la opción de redundancia de almacenamiento está bloqueada y no se puede modificar.

4. Seleccione las opciones de **redundancia de almacenamiento**.

    Si está usando Azure como punto de conexión de almacenamiento de copia de seguridad principal (por ejemplo, si está realizando una copia de seguridad en Azure desde un servidor de Windows), debe considerar la posibilidad de seleccionar la opción de [almacenamiento con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) (valor predeterminado).

    Si está usando Azure como punto de conexión de almacenamiento de copia de seguridad terciario (por ejemplo, si emplea SCDPM para tener una copia de seguridad local y usa Azure para sus necesidades de retención a largo plazo), debería considerar la posibilidad de elegir el [almacenamiento con redundancia local](../storage/storage-redundancy.md#locally-redundant-storage). Esto reduce el costo de almacenamiento de datos en Azure, a la vez que ofrece un menor nivel de durabilidad de los datos, el cual podría ser aceptable para copias terciarias.

    Puede leer más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y con [redundancia local](../storage/storage-redundancy.md#locally-redundant-storage) en esta página de [información general](../storage/storage-redundancy.md).

    a. Haga clic en el almacén que acaba de crear.

    b. En la página Inicio rápido, seleccione **Configurar**.

    ![Configurar el estado de almacén](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Elija la opción de redundancia de almacenamiento adecuada.

    Debe hacer clic en **Guardar** si ha seleccionado el **almacenamiento con redundancia local**, puesto que el **almacenamiento con redundancia geográfica** es la opción predeterminada.

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d. Haga clic en **Servicios de recuperación**, en el panel de navegación izquierdo, para volver a la lista de recursos de **Servicios de recuperación**.

    ![Seleccionar almacén de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

## Descargar el archivo de credenciales de almacén
El servidor local (cliente Windows, servidor de Windows Server o Data Protection Manager) debe autenticarse ante un almacén de copia de seguridad antes de realizar una copia de seguridad de datos en Azure. La autenticación se realiza mediante las "credenciales de almacén". El archivo de credenciales de almacén se descarga a través de un canal seguro desde el Portal de Azure y el servicio Copia de seguridad de Azure es consciente de la clave privada del certificado, que no se conserva en el portal o servicio.

Obtenga más información sobre el [uso de las credenciales de almacén para autenticarse con el servicio Copia de seguridad de Azure](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

**Para descargar el archivo de credenciales de almacén a una máquina local:**

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).

2. Haga clic en **Servicios de recuperación** en el panel de navegación izquierdo y seleccione el almacén de copia de seguridad que ha creado.

3.  Haga clic en el icono de nube para acceder a la vista *Inicio rápido* del almacén de copia de seguridad.

    ![Vista rápida](./media/backup-configure-vault/quickview.png)

4. En la página **Inicio rápido**, haga clic en **Descargar credenciales de almacén**.

    ![Descargar](./media/backup-configure-vault/downloadvc.png)

    El portal generará una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual. El archivo de credenciales de almacén se utiliza solo durante el flujo de trabajo de registro y expira tras 48 horas.

    El archivo de credenciales de almacén puede descargarse desde el portal.

5. Haga clic en **Guardar** para descargar las credenciales del almacén en la carpeta de descargas de la cuenta local, o elija **Guardar como** en el menú *Guardar* para especificar una ubicación para las credenciales de almacén.

    No es necesario abrir las credenciales de almacén en este momento.

    Asegúrese de que las credenciales de almacén se guardan en una ubicación a la que se puede acceder desde la máquina. Si se almacenan en un recurso compartido/SMB de archivo, compruebe los permisos de acceso.

## Descarga, instalación y registro del agente de Copia de seguridad de Azure
Después de crear el almacén de Copia de seguridad de Azure, se debe instalar un agente en cada una de las máquinas de Windows (servidor de Windows Server, cliente de Windows, servidor de System Center Data Protection Manager o la máquina del Servidor de copia de seguridad de Azure) que habilite la copia de seguridad de los datos y las aplicaciones en Azure.

**Para descargar, instalar y registrar el agente:**

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).

2. Haga clic en **Servicios de recuperación** y seleccione el almacén de copia de seguridad que desee registrar en un servidor.

3. En la página Inicio rápido, haga clic en **Agente para Windows Server, System Center Data Protection Manager o cliente de Windows > Guardar**.

    ![Guardar agente](./media/backup-configure-vault/agent.png)

4. Una vez completada la descarga de *MARSagentinstaller.exe*, haga clic en **Ejecutar** (o haga doble clic en **MARSAgentInstaller.exe** en la ubicación guardada). Elija la *carpeta de instalación* y la *carpeta de caché* requerida para el agente y haga clic en **Siguiente**.

    La ubicación de caché especificada debe tener un espacio libre de al menos el 5 % de los datos de copia de seguridad.

    ![Directorio temporal y caché](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. Si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy**, especifique los detalles del servidor proxy. Si usa un servidor proxy autenticado, escriba los detalles de nombre y contraseña del usuario y haga clic en **Siguiente**.

    El agente de Copia de seguridad de Azure instala .NET Framework 4.5 y Windows PowerShell (si aún no está instalado) para completar la instalación.

6. Una vez que el agente esté instalado, haga clic en **Continuar con el registro** para seguir con el flujo de trabajo.

    ![Registro](./media/backup-configure-vault/register.png)

7. En la pantalla **Identificación del almacén**, busque y seleccione el *archivo de credenciales del almacén* que se descargó anteriormente.

    ![Credenciales de almacén](./media/backup-configure-vault/vc.png)

    El archivo de almacén de credenciales solo es válido durante 48 horas (después de descargarlo desde el portal). Si encuentra algún error en esta pantalla (por ejemplo, "El archivo de credenciales de almacén proporcionado ha caducado"), inicie sesión en el Portal de Azure y vuelva a descargar el archivo de almacén de credenciales.

    Asegúrese de que el archivo de almacén de credenciales está disponible en una ubicación a la que puede tener acceso la aplicación de instalación. Si encuentra errores relacionados con el acceso, copie el archivo de almacén de credenciales en una ubicación temporal en esta máquina y vuelva a intentar la operación.

    Si se produce un error de credenciales de almacén no válidas (por ejemplo, "Las credenciales del almacén no son válidas"), el archivo está dañado o no tiene asociadas las credenciales más recientes para el Servicio de recuperación. Vuelva a intentar la operación después de descargar un nuevo archivo de credenciales de almacén desde el portal. Este error suele aparecer si el usuario hace clic en la opción *Descargar credenciales de almacén* en sucesión rápida. En este caso, solo es válido el último archivo de credenciales de almacén.

8. En la pantalla **Configuración de cifrado**, puede *generar* una frase de contraseña o *proporcionarla* (mínimo de 16 caracteres). Recuerde guardar la frase de contraseña en una ubicación segura.

    ![Cifrado](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] Si la frase de contraseña se pierde u olvida, Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no puede ver la frase de contraseña que usa el usuario final. Guarde el archivo en una ubicación segura, ya que puede ser necesario durante una operación de recuperación.

9. Haga clic en **Finalizar**

    La máquina se registra correctamente en el almacén y ahora está listo para iniciar la copia de seguridad en Microsoft Azure.

## Pasos siguientes
- Regístrese para obtener una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).
- [Realice copias de seguridad de un servidor de Windows o una máquina cliente](backup-azure-backup-windows-server.md).
- Si todavía tiene preguntas sin responder, eche un vistazo a [P+F de servicio de Copia de seguridad de Azure](backup-azure-backup-faq.md).
- Visite el [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0302_2016-->