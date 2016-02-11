<properties
   pageTitle="Copia de seguridad de los archivos y carpetas de Windows Server o del Cliente de Windows en Azure | Microsoft Azure"
   description="Copia de seguridad de un Windows Server o un Cliente de Windows en Azure con este procedimiento simple. Puede hacer una copia de seguridad de los archivos y las carpetas de Windows en la nube en unos pocos pasos sencillos."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="copia de seguridad de Windows Server;realizar copia de seguridad de Windows Server"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="01/22/2016"
	 ms.author="jimpark;"/>

# Cómo realizar copias de seguridad de archivos y carpetas de Windows Server o del Cliente de Windows en Azure.
Resulta sencillo realizar una copia de carpetas y archivos de Windows en Azure con este procedimiento simple. Si aún no lo ha hecho, antes de continuar complete los [requisitos previos](backup-configure-vault.md#before-you-start) con el objeto de preparar el entorno para la copia de seguridad de su máquina Windows antes de continuar.

## Realizar copias de seguridad de archivos y carpetas
1. Una vez registrada la máquina, abra el complemento MMC de Copia de seguridad de Microsoft Azure.

    ![Resultado de la búsqueda](./media/backup-azure-backup-windows-server/result.png)

2. Haga clic en **Programar una copia de seguridad**

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Seleccione los elementos de los cuales desea tener una copia de seguridad. Copia de seguridad de Azure en un cliente de Windows Server/Windows (es decir, sin System Center Data Protection Manager) permite proteger archivos y carpetas.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-azure-backup-windows-server/items.png)

4. Especifique la programación de la copia de seguridad y la directiva de retención, que se explica en detalle en el siguiente [artículo](backup-azure-backup-cloud-as-tape.md).

5. Elija el método de envío de la copia de seguridad inicial. La elección de completar la propagación inicial depende de la cantidad de datos de los que se desea realizar la copia de seguridad y de la velocidad del vínculo de carga de Internet. Si tiene intención de realizar copias de seguridad de GB/TB de datos mediante una conexión de latencia alta y ancho de banda bajo, se recomienda que complete la copia de seguridad inicial enviando un disco al centro de datos de Azure más cercano. Esto se denomina "Copia de seguridad sin conexión" y se trata en detalle en este [artículo](backup-azure-backup-import-export.md). Si dispone de conexión con ancho de banda suficiente, se recomienda que complete la copia de seguridad inicial en la red.

    ![Copia de seguridad inicial de Windows Server](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Una vez completado el proceso de programación de la copia de seguridad, vuelva al complemento MMC y haga clic en **Hacer copia de seguridad ahora** para completar la propagación inicial a través de la red.

    ![Realizar copia de seguridad de Windows Server ahora](./media/backup-azure-backup-windows-server/backupnow.png)

7. Una vez completada la propagación inicial, la vista **Trabajos** de la consola de Copia de seguridad de Azure indica el estado.

    ![IR completado](./media/backup-azure-backup-windows-server/ircomplete.png)

## Pasos siguientes
- [Administración de Windows Server o el cliente de Windows](backup-azure-manage-windows-server.md)
- [Restauración de Windows Server o el cliente de Windows desde Azure](backup-azure-restore-windows-server.md)
- [Preguntas más frecuentes de Copia de seguridad de Azure](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0128_2016-->