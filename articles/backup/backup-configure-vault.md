<properties
	pageTitle="Preparación del entorno para realizar la copia de seguridad de Windows Server o del Cliente de Windows en Azure | Microsoft Azure"
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
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Preparación del entorno para la copia de seguridad de máquinas virtuales de Windows
Este artículo le ayudará a preparar la copia de seguridad de Windows Server o del cliente de Windows en Azure. Para ello, necesitará una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

Si ya lo hizo, puede comenzar a [realizar la copia de seguridad de las máquinas virtuales](backup-azure-backup-windows-server.md). Si no es así, siga estos pasos para asegurarse de que el entorno esté listo.

>[AZURE.NOTE] Anteriormente, era necesario crear o adquirir un certificado X.509 v3 con el fin de registrar el servidor de copia de seguridad. Los certificados aún se admiten, pero ahora, para facilitar el proceso de registro de almacenes de Azure en un servidor, puede generar una credencial de almacén directamente en la página Inicio rápido.

## Antes de comenzar
Para hacer una copia de seguridad de los archivos y los datos de Windows Server en Azure, primero debe:

- **Crear un almacén de copia de seguridad**: cree un almacén en el [portal de administración de Copia de seguridad de Azure](http://manage.windowsazure.com).
- **Descargar credenciales de almacén**: en la página Panel del almacén de Copia de seguridad de Azure, descargue las credenciales de almacén que se usarán para registrar la máquina de Windows en el almacén de copia de seguridad.
- **Instalar Azure Backup Agent y registrar el servidor**: en la página Panel, haga clic en el vínculo para descargar [Azure Backup Agent](http://aka.ms/azurebackup_agent). Instale el agente y registre el servidor en el almacén de copia de seguridad con las credenciales de almacén.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Pasos siguientes
- [Copia de seguridad de Windows Server o el cliente de Windows](backup-azure-backup-windows-server.md)
- [Administración de Windows Server o el cliente de Windows](backup-azure-manage-windows-server.md)
- [Restauración de Windows Server o el cliente de Windows desde Azure](backup-azure-restore-windows-server.md)
- [Preguntas más frecuentes de Copia de seguridad de Azure](backup-azure-backup-faq.md)
- [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0128_2016-->