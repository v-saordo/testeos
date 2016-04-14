<properties
	pageTitle="Administración de almacenes y servidores de la Copia de seguridad de Azure | Microsoft Azure"
	description="Use este tutorial para aprender a administrar almacenes y servidores de Copia de seguridad de Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="jimpark; markgal"/>


# Administración de almacenes y servidores de Copia de seguridad
En este artículo encontrará información general sobre las tareas de administración de copias de seguridad que tiene disponibles en el portal de administración.

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).
2. Haga clic en **Servicios de recuperación** y luego en el nombre del almacén de copia de seguridad para ver la página de inicio rápido.

    Si selecciona las opciones que se encuentran en la parte superior de la página de Inicio rápido, podrá ver las tareas de administración disponibles.

    ![Elementos protegidos](./media/backup-azure-manage-windows-server/RS_tabs.png)

## Panel
Seleccione **Panel** para ver información general sobre el uso del servidor. En la parte inferior del panel puede llevar a cabo las tareas siguientes:

- **Administrar certificado**. Si un certificado se usó para registrar un servidor, utilícelo para actualizar el certificado. Si usa credenciales de almacén, no utilice **Administrar certificado**.
- **Eliminar**. Elimina el almacén de credenciales de copia de seguridad actual. En caso de que ya no se use un almacén de credenciales de copia de seguridad, puede eliminarlo para liberar espacio de almacenamiento. **Eliminar** solo está habilitado después de que todos los servidores registrados se hayan eliminado del almacén.
- **Almacén de credenciales**. Use este elemento del menú de vista rápida para configurar las credenciales de almacén.

## Elementos protegidos
Haga clic en **Elementos protegidos** para ver los elementos de los que se han hecho copias de seguridad desde los servidores. Esta lista solo tiene propósitos informativos.

![Elementos protegidos](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## Elementos registrados
Seleccione la opción **Elementos registrados** para ver los nombres de los servidores que se registraron en este almacén de claves.

![Servidor eliminado](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

Aquí puede realizar las siguientes tareas:

- **Permitir nuevo registro**: cuando se selecciona esta opción para un servidor, puede usar el **Asistente para registro** en el agente para registrar el servidor en el almacén de copia de seguridad por segunda vez. Es posible que necesite volver a registrarse debido a un error en el certificado o si un servidor tuvo que reconstruirse.
- **Eliminar**: elimina un servidor del almacén de copias seguridad. Todos los datos almacenados asociados con el servidor se eliminan inmediatamente.

## Pasos siguientes
- [Restauración de Windows Server o el cliente de Windows desde Azure](backup-azure-restore-windows-server.md)
- Para obtener más información sobre Copia de seguridad de Azure, consulte [Información general de Copia de seguridad de Azure](backup-introduction-to-azure-backup.md).
- Visite el [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0302_2016-->