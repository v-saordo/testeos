<properties
   pageTitle="Primer vistazo: cómo hacer una copia de seguridad de un equipo con Windows Server o un equipo cliente en Azure | Microsoft Azure"
   description="Obtenga información acerca de cómo hacer una copia de seguridad de un equipo con Windows Server mediante Copia de seguridad de Azure mediante estos sencillos pasos"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="cómo realizar la copia de seguridad; cómo realizar la copia de seguridad"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="hero-article"
	 ms.date="02/21/2016"
	 ms.author="jimpark;"/>

# Realización de copias de seguridad de archivos y carpetas de Windows Server o del Cliente de Windows en Azure

En unos pocos pasos puede realizar una copia de seguridad de su equipo Windows (cliente Windows o Windows Server) en Azure. Cuando haya completado los 4 pasos que se indican a continuación, habrá:

- Configurado una suscripción de Azure (si es necesario).
- Creado un almacén de copia de seguridad y descargado los componentes necesarios.
- Preparado el servidor de Windows o el cliente mediante la instalación y registro de dichos componentes.
- Realizado una copia de seguridad de los datos.

## Paso 1: obtener una suscripción de Azure

Si no tiene una suscripción de Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) que permite el acceso a cualquier servicio de Azure.

>[AZURE.NOTE] Puede omitir este paso si ya tiene una suscripción de Azure.

## Paso 2: Creación de un almacén de copia de seguridad

Para hacer una copia de seguridad de los archivos y los datos de un equipo Windows en Azure, debe crear un almacén de copia de seguridad en la región geográfica donde desea almacenar los datos.

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/) mediante su suscripción.

2. Haga clic en **Nuevo > Integración híbrida > Copia de seguridad**.

    ![Crear almacén](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. En la pantalla que aparece, en el campo **Nombre**, escriba un nombre descriptivo para identificar el almacén de copia de seguridad. Escriba un nombre que tenga entre 2 y 50 caracteres. El campo debe comenzar por una letra y solo puede contener letras, números y guiones.

    b. En **Región**, elija la región geográfica para el almacén de copia de seguridad. Si elige una región geográfica cerca de su ubicación, puede reducir la latencia de red al hacer una copia de seguridad en Azure.

    c. Haga clic en **Crear almacén**.

    ![Crear almacén](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal.

    ![Creación de almacén](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Una vez creado el almacén de copia de seguridad, verá que aparece en los recursos de Servicios de recuperación como **Active** (Activo).

    ![Estado de creación de almacén](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Seleccione las opciones de **redundancia de almacenamiento**.

    El mejor momento para seleccionar la opción de redundancia de almacenamiento es justo después de la creación del almacén y antes de que las máquinas se registren en este. Una vez que un elemento se ha registrado en el almacén, la opción de redundancia de almacenamiento está bloqueada y no se puede modificar.

    Si usa Azure como punto de conexión principal de almacenamiento de copias de seguridad (por ejemplo, realiza una copia de seguridad en Azure desde Windows Server), debería considerar seleccionar la opción [Almacenamiento con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) (predeterminada).

    Si utiliza Azure como punto de conexión terciario de almacenamiento de copias de seguridad (por ejemplo, usa SCDPM para tener una copia de seguridad local y utiliza Azure para cubrir sus necesidades de retención a largo plazo), debería considerar elegir [Almacenamiento con redundancia local](../storage/storage-redundancy.md#locally-redundant-storage). Esto reduce el costo de almacenamiento de datos en Azure, a la vez que ofrece un menor nivel de durabilidad de los datos, el cual podría ser aceptable para copias terciarias.

    a. Haga clic en el almacén que acaba de crear.

    b. En la página Inicio rápido, seleccione **Configurar**.

    ![Configurar el estado de almacén](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Elija la opción de redundancia de almacenamiento adecuada.

    Si ha seleccionado **Localmente redundante**, será preciso que haga clic en **Guardar**, ya que la opción predeterminada es **Redundancia geográfica**.

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d. Haga clic en **Servicios de recuperación**, en el panel de navegación izquierdo, para volver a la lista de recursos de **Servicios de recuperación**.

    ![Seleccionar almacén de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Ahora debe autenticarse el equipo Windows con el almacén de copia de seguridad que acaba de crear. La autenticación se realiza mediante las credenciales de almacén.

4.  Haga clic en el almacén que acaba de crear.

    ![Estado de creación de almacén](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. En la página **Inicio rápido**, haga clic en **Descargar credenciales de almacén**.

    ![Descargar](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    El portal generará una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual.

    >[AZURE.NOTE] El archivo de credenciales de almacén se utiliza solo durante el flujo de trabajo de registro y expira tras 48 horas.

6. Haga clic en **Guardar** para descargar las credenciales del almacén en la carpeta de **descargas** de la cuenta local, o bien elija **Guardar como** en el menú **Guardar** para especificar una ubicación para las credenciales del almacén.

    ![Seleccionar almacén de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/save.png)

    Asegúrese de que las credenciales de almacén se guardan en una ubicación a la que se puede acceder desde la máquina. Si el archivo se almacena en un recurso compartido/SMB de archivo, compruebe los permisos de acceso.

    >[AZURE.NOTE] No es necesario abrir las credenciales de almacén en este momento.

    A continuación, debe descargar el agente de copia de seguridad

7. Haga clic en **Servicios de recuperación**, en el panel de navegación izquierdo, y luego haga clic en el almacén de copia de seguridad que desea registrar en un servidor.

    ![Seleccionar almacén de copia de seguridad](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. En la página Inicio rápido, haga clic en **Agente para Windows Server, System Center Data Protection Manager o cliente de Windows > Guardar** (o seleccione **Guardar como** en el menú **Guardar** para especificar la ubicación del agente).

    ![Guardar agente](./media/backup-try-azure-backup-in-10-mins/agent.png)

Llegados a este punto, habrá terminado de crear un almacén de copia de seguridad y de descargar los componentes necesarios. Ahora podrá instalar el agente de copia de seguridad.

## Paso 3: Instalación del agente de Copia de seguridad de Azure en su servidor o cliente de Windows

1. Una vez finalizada la descarga, haga doble clic en el archivo **MARSagentinstaller.exe** desde la ubicación guardada (o puede hacer clic en **Ejecutar**, en lugar de guardar el archivo en el paso anterior).

2. Elija la **carpeta de instalación** y la **carpeta de caché** requeridas para el agente.

    La ubicación de caché especificada debe tener un espacio libre de al menos el 5 % de los datos de copia de seguridad.

    Haga clic en **Siguiente**.

    ![Directorio temporal y caché](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. Aún puede conectarse a Internet a través de la configuración predeterminada del proxy o, si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy**, active la casilla **Utilice una configuración de proxy personalizada** y especifique los detalles del servidor proxy.

    Si utiliza un servidor proxy autenticado, escriba los detalles de nombre y contraseña del usuario.

    Haga clic en **Siguiente**.

    ![Configuración de proxy](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Haga clic en **Instalar**.

    ![Configuración de proxy](./media/backup-try-azure-backup-in-10-mins/agent-installation.png)

    El agente de Copia de seguridad de Azure instala .NET Framework 4.5 y Windows PowerShell (si aún no están instalados) para completar la instalación.

5. Una vez que el agente esté instalado, haga clic en **Proceder al registro** para seguir con el flujo de trabajo.

    ![Registro](./media/backup-try-azure-backup-in-10-mins/register.png)

6. En la pantalla **Identificación del almacén**, busque y seleccione el **archivo de credenciales del almacén** que descargó anteriormente. Asegúrese de que el archivo de almacén de credenciales está disponible en una ubicación a la que puede tener acceso la aplicación de instalación.

    Haga clic en **Siguiente**.

    ![Credenciales de almacén](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. En la pantalla **Configuración de cifrado**, puede generar una frase de contraseña o proporcionarla (mínimo de 16 caracteres). Recuerde guardar la frase de contraseña en una ubicación segura.

    > [AZURE.WARNING] Si la frase de contraseña se pierde u olvida, Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no puede ver la frase de contraseña que usa el usuario final. Guarde el archivo en una ubicación segura, ya que puede ser necesario durante una operación de recuperación.

    Haga clic en **Finalizar**

    ![Cifrado](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    El **Asistente para registrar servidor** registra el servidor en Copia de seguridad de Microsoft Azure.

    ![Cifrado](./media/backup-try-azure-backup-in-10-mins/registering-server.png)

8. Una vez que la **clave de cifrado** esté establecida, deje la casilla **Inicio del agente de Servicios de recuperación de Microsoft Azure** activada y haga clic en **Cerrar**.

    ![Cifrado](./media/backup-try-azure-backup-in-10-mins/close-server-registration.png)

    La máquina se registra correctamente en el almacén y ahora está listo para configurar y programar las opciones de copia de seguridad.

## Paso 4: Realización de copias de seguridad de archivos y carpetas

1. En el complemento MMC (que se abre automáticamente si ha dejado la casilla **Inicio del agente de Servicios de recuperación de Microsoft Azure** activada) haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. En la pantalla **Introducción**, haga clic en **Siguiente**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/getting-started.png)

3. En la pantalla **Seleccionar elementos de los que realizar copia de seguridad**, haga clic en **Agregar elementos**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Seleccione los elementos de los cuales desea realizar una copia de seguridad y haga clic en **Aceptar**. Copia de seguridad de Azure en Windows Server o en un cliente Windows le permite proteger archivos y carpetas.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/added-items.png)

    Haga clic en **Siguiente**.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/selected-items.png)

4. Especifique la **programación de copia de seguridad** y haga clic en **Siguiente**.

    Puede programar copias de seguridad diarias (un máximo de 3 veces al día) o semanales.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Seleccione la **directiva de retención** de la copia de seguridad. Puede modificar la directiva de retención diaria, semanal, mensual y anual según sus necesidades.

    >[AZURE.NOTE] La programación de copia de seguridad se explica con detalle en este [artículo](backup-azure-backup-cloud-as-tape.md).

     Haga clic en **Siguiente**.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Elija el tipo de copia de seguridad inicial.

    Puede hacer una copia de seguridad automáticamente en la red, o puede realizar una copia sin conexión. El resto de este artículo sigue el proceso de copia de seguridad automática. Si prefiere hacer una copia de seguridad sin conexión, consulte este artículo para obtener información adicional sobre el [flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](backup-azure-backup-import-export.md).

    Haga clic en **Siguiente**.

    ![Copia de seguridad inicial de Windows Server](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. En la pantalla **Confirmación** revise la información y haga clic en **Finalizar**.

    ![Copia de seguridad inicial de Windows Server](./media/backup-try-azure-backup-in-10-mins/confirmation.png)

8. Una vez que el asistente finalice la creación de la **programación de copia de seguridad**, haga clic en **Cerrar**.

    ![Copia de seguridad inicial de Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-schedule-created.png)

9. En el complemento MMC, haga clic en **Hacer ahora una copia de seguridad** para completar la propagación inicial a través de la red.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. En la pantalla **Confirmación**, revise la configuración que utilizará el asistente para realizar la copia de seguridad del equipo y haga clic en **Hacer una copia de seguridad **.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-try-azure-backup-in-10-mins/backup-now-confirmation.png)

11. Haga clic en **Cerrar** para cerrar el asistente. Puede hacerlo antes de que se complete el **proceso de copia de seguridad** y se seguirá ejecutando en segundo plano.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-try-azure-backup-in-10-mins/backup-progress.png)

12. Después de que se completa la copia de seguridad inicial, la vista **Trabajos** de la consola de Copia de seguridad de Azure indica el estado "Trabajo completado".

    ![IR completado](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Enhorabuena, ha realizado correctamente la copia de seguridad de los archivos y carpetas mediante Copia de seguridad de Azure.

## Pasos siguientes
- Para obtener más información sobre Copia de seguridad de Azure, consulte [Información general de Copia de seguridad de Azure](backup-introduction-to-azure-backup.md).
- Más información acerca de la [preparación del entorno para la copia de seguridad de máquinas de Windows](backup-configure-vault.md)
- Más información acerca de la [copia de seguridad de Windows Server](backup-azure-backup-windows-server.md)
- Visite el [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0302_2016-->