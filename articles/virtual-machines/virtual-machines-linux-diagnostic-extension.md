
<properties
		pageTitle="Supervisión de una máquina virtual Linux con una extensión de máquina virtual | Microsoft Azure"
		description="Obtenga información acerca de cómo usar la extensión de diagnóstico de Linux para supervisar los datos de rendimiento y diagnóstico de una máquina virtual Linux en Azure."
		services="virtual-machines"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="12/15/2015"
		ms.author="Ning"/>


# Uso de la extensión de diagnóstico de Linux para supervisar los datos de rendimiento y diagnóstico de una máquina virtual Linux

## Introducción

La extensión de diagnóstico de Linux ayuda a un usuario a supervisar las máquinas virtuales Linux que se ejecutan en Microsoft Azure, con las siguientes funcionalidades:

- Recopila y carga los datos de rendimiento del sistema, diagnóstico y registro del sistema de la máquina virtual Linux en la tabla de almacenamiento del usuario.
- Permite al usuario personalizar las métricas de datos que se recopilan y se cargan.
- Permite que el usuario cargar los archivos de registro especificados en la tabla de almacenamiento designada.

Para la versión 2.0, los datos incluyen:

- Todos los registros de Linux Rsyslog, incluidos los registros del sistema, seguridad y aplicaciones.
- Todos los datos de sistema especificados en este [documento](https://scx.codeplex.com/wikipage?title=xplatproviders").
- Archivos de registro especificados por el usuario.

Tenga en cuenta que esta extensión funciona tanto con el modelo de implementación clásico como con el modelo de implementación del Administrador de recursos.


## Habilitación de la extensión
La extensión puede habilitarse a través del [portal de Azure](https://ms.portal.azure.com/#), Azure PowerShell o scripts de la CLI de Azure.

Para ver y configurar los datos de rendimiento y sistema directamente desde el Portal de Azure, siga estos [steps](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "URL del blog de Windows"/).


Este artículo se centra en habilitar y configurar la extensión mediante comandos de la CLI de Azure. Esto permite leer y ver los datos de la tabla de almacenamiento directamente.


## Requisitos previos
- Agente Linux de Microsoft Azure versión 2.0.6 o posterior. Tenga en cuenta que la mayoría de las imágenes de la galería de máquina virtual Linux de Azure incluyen la versión 2.0.6 o posterior. Puede ejecutar **WAAgent -version** para confirmar la versión instalada en la máquina virtual. Si la máquina virtual está ejecutando una versión anterior a 2.0.6, puede seguir estas [instrucciones](https://github.com/Azure/WALinuxAgent "instrucciones") para actualizarla.
- [CLI de Azure](./xplat-cli-install.md). Siga [esta guía](./xplat-cli-install.md) para configurar el entorno de la CLI de Azure en su máquina. Cuando se haya instalado la CLI de Azure, puede usar el comando **azure** desde su interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema) para obtener acceso a los comandos de la CLI de Azure. Por ejemplo, ejecute **azure vm extension set --help** para un uso detallado, ejecute **azure login** para iniciar sesión en Azure, ejecute **azure vm list** para enumerar todas las máquinas virtuales que tiene en Azure.
- Una cuenta de almacenamiento para almacenar los datos. Necesitará un nombre de la cuenta de almacenamiento creada previamente y una clave de acceso para cargar los datos en el almacenamiento.


## Use el comando de la CLI de Azure para habilitar la extensión de diagnóstico de Linux

###  Escenario 1. Habilitación de la extensión con el conjunto de datos predeterminado
Para la versión 2.0 o posteriores, los datos predeterminados que se recopilarán incluyen:

- Todas la información de Rsyslog, incluidos los registros del sistema, seguridad y aplicaciones.  
- Un conjunto de datos del sistema base; tenga en cuenta que el conjunto de datos completo está descrito en este [documento](https://scx.codeplex.com/wikipage?title=xplatproviders). Si desea habilitar datos adicionales, continúe con los pasos descritos en el escenario 2 y 3.

Paso 1. Cree un archivo llamado PrivateConfig.json con el siguiente contenido.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account"
	}

Paso 2: Ejecute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Escenario 2. Personalización de la métrica del monitor de rendimiento  
En esta sección se describe cómo personalizar la tabla de datos de rendimiento y diagnóstico.

Paso 1. Cree un archivo denominado PrivateConfig.json con el contenido que aparece en el ejemplo siguiente. Especificar los datos concretos que quiere recopilar.

Para todos los proveedores y variables admitidos, consulte este [documento](https://scx.codeplex.com/wikipage?title=xplatproviders). Puede tener varias consultas y almacenarlas en varias tablas anexando más consultas al script.

De forma predeterminada, siempre se recopilan los datos de Rsyslog.

	{
     	"storageAccountName":"storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"perfCfg":[
           	{"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
           	}
          ]
	}


Paso 2: Ejecute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Escenario 3. Carga de sus propios archivos de registro
En esta sección se describe cómo recopilar y cargar los archivos de registro concretos en su cuenta de almacenamiento. Deberá especificar la ruta de acceso al archivo de registro y el nombre de la tabla en la que se almacenará el registro. Para tener varios archivos de registro puede agregar varias entradas de archivo o tabla al script.

Paso 1. Cree un archivo llamado PrivateConfig.json con el siguiente contenido.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"fileCfg":[
           	{"file":"/var/log/mysql.err",
             "table":"mysqlerr"
           	}
          ]
	}


Paso 2: Ejecute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Escenario 4. Deshabilitación de la extensión de diagnóstico de Linux
Paso 1. Cree un archivo llamado PrivateConfig.json con el siguiente contenido.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account",
     	“perfCfg”:[],
     	“enableSyslog”:”False”
	}


Paso 2: Ejecute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


## Revisión de los datos
Los datos de diagnóstico y rendimiento y se almacenan en una tabla de almacenamiento de Azure. Revise [este artículo](storage-ruby-how-to-use-table-storage.md) para información sobre cómo obtener acceso a los datos de la tabla de almacenamiento mediante scripts de la CLI de Azure.

Además, puede utilizar las siguientes herramientas de la interfaz de usuario para tener acceso a los datos:

1.	Utilice el Explorador de servidores de Visual Studio. Vaya a la cuenta de almacenamiento. Después de que la máquina virtual se ejecute aproximadamente 5 minutos debe ver las cuatro tablas predeterminadas: "LinuxCpu", "LinuxDisk", "LinuxMemory" y "Linuxsyslog". Haga doble clic en el nombre de la tabla para ver los datos.
2.	Utilice [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/ "Explorador de almacenamiento de Azure") para tener acceso a los datos.

![imagen](./media/virtual-machines-linux-diagnostic-extension/no1.png)

Si ha habilitado el archivo fileCfg o perfCfg especificado en los escenarios 2 y 3, puede usar las herramientas anteriores para ver los datos no predeterminados.



## Problemas conocidos
- En la versión 2.0, solo puede tener acceso a la información de Rsyslog y el archivo de registro del cliente especificado a través de scripts.

<!---HONumber=AcomDC_0128_2016-->