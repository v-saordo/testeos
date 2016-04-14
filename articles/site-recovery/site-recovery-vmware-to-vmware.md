<properties
	pageTitle="Replicación de máquinas virtuales locales de VMware o de servidores físicos en un sitio secundario | Microsoft Azure"
	description="Use este artículo para replicar máquinas virtuales de VMware o servidores físicos de Windows o Linux en un sitio secundario con Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="raynew"/>


# Replicación de máquinas virtuales locales de VMware o de servidores físicos en un sitio secundario


## Información general

InMage Scout en Azure Site Recovery proporciona características de replicación en tiempo real entre los sitios locales de VMware. InMage Scout se incluye en las suscripciones al servicio Azure Site Recovery.


## Requisitos previos

- **Cuenta de Azure**: necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery.


## Paso 1: Creación de un almacén

1. Inicie sesión en el [Portal de administración](https://portal.azure.com).
2. Haga clic en **Servicios de datos** > **Servicios de recuperación** y **Almacén de Site Recovery**.
3. Haga clic en **Crear nuevo** > **Creación rápida**.
4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.
5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.

## Paso 2: Configuración del almacén y descarga de componentes InMage Scout

1. Haga clic en **Crear almacén**.
2. En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página Inicio rápido.
3. En la lista desplegable, seleccione **Entre dos sitios locales de VMware**.
4. Descargue a InMage Scout. Los archivos de instalación para todos los componentes necesarios están en el archivo zip descargado.


## Paso 3: Instalación de actualizaciones de componentes

Obtenga información acerca de las [actualizaciones](#updates) más recientes. Deberá instalar los archivos de actualización en el orden siguiente:

1. Servidor RX si hay alguno
2. Servidores de configuración
3. Servidores de proceso
3. Servidores de destino principal.
4. Servidores vContinuum.

Realice la instalación de la manera siguiente:

1. Descargue el archivo zip de la [actualización](http://aka.ms/scoutupdates). Este archivo zip contiene los archivos siguientes:

	-  RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz
	-  CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe
	-  UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe
	-  UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz
	-  vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe
2. Extraiga los archivos zip.
2. **Servidor RX**: copie **RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz** en el servidor RX y extráigalo. En la carpeta extraída, ejecute **/Install**.
2. **Servidor de configuración/servidor de proceso**: copie **CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe** en el servidor de configuración y el servidor de proceso. Haga doble clic para ejecutarlo.
3. **Servidor de destino principal de Windows**: para actualizar el agente unificado, copie **UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe** en el servidor de destino principal. Haga doble clic en él para ejecutarlo. Tenga en cuenta que el agente unificado para Windows no se aplica en el servidor de origen. Solamente debe instalarse en el servidor de destino principal de Windows.
4. **Servidor de destino principal de Linux**: para actualizar el agente unificado, copie **UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz** en el servidor de destino principal y extráigalo. En la carpeta extraída, ejecute **/Install**.
5. **Servidor vContinuum**: copie **vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe** en el servidor vContinuum. Asegúrese de que ha cerrado el asistente vContinuum. Haga doble clic en el archivo para ejecutarlo.

## Paso 4: Configuración de la replicación
5. Configure la replicación entre los sitios de origen y de destino de VMware.
6. Para obtener instrucciones, use la documentación de InMage Scout que se descarga con el producto. También se puede tener acceso a la documentación del modo indicado a continuación:

	- [Notas de la versión](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [Matriz de compatibilidad](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [Guía de usuario](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [Guía de usuario de RX](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [Guía de instalación rápida](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## Actualizaciones

### Actualización del 3 de diciembre de 2015 de ASR Scout 8.0.1

Entre las correcciones de la actualización del 3 de diciembre de 2015 se incluyen:

- **Servidor de configuración**: corrige un problema que impedía que la característica de disponibilidad gratuita durante 31 días funcionase según lo esperado al registrar el servidor de configuración en Site Recovery.
- **Agente unificado**: corrige un problema en la actualización 1 del destino principal, cuyo resultado era que la actualización no se instalaba en el servidor principal al actualizar de la versión 8.0 a 8.0.1.

>[AZURE.NOTE]
>
>-	Todas las actualizaciones de ASR son acumulativas.
>-	Las actualizaciones de CS y RX no se pueden revertir una vez aplicadas en el sistema.


### ASR Scout 8.0.1 Update 1

Esta última actualización incluye correcciones de errores y características nuevas:

- 31 días de protección gratuita por instancias de servidor. Esto le permite probar la funcionalidad o configurar una prueba de concepto.
	- Todas las operaciones en el servidor, incluida la conmutación por error y la conmutación por recuperación son gratuitas durante los primeros 31 días a partir del momento en que un servidor primero es protegido por primera vez mediante un rastreador ASR.
	- Desde el 32º día en adelante, a cada servidor protegido se le cargará la tarifa de instancia estándar de la protección de Azure Site Recovery en un sitio propiedad de un cliente.
	- En cualquier momento, el número de servidores protegidos que se están cargando actualmente está disponible en la página de Panel del almacén de Azure Site Recovery.
- Compatibilidad con vCLI 5.5 Update 2 agregada.
- Compatibilidad agregada para los sistemas operativos Linux en el servidor de origen:
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- Correcciones de errores para resolver los problemas siguientes:
	- Se produce un error en el registro del almacén para el servidor de configuración o el servidor RX.
	- Los volúmenes de clúster no aparecen del modo esperado cuando las máquinas virtuales agrupadas en clústeres se vuelven a proteger durante la reanudación.
	- La conmutación por recuperación falla cuando el servidor de destino principal se hospeda en un servidor ESXi diferente de las máquinas virtuales de producción locales.
	- Los permisos de archivo de configuración se cambian al actualizar a la versión 8.0.1, lo cual afecta a la protección y las operaciones.
	- El umbral de resincronización no se aplica del modo esperado, dando lugar a un comportamiento de replicación incoherente.
	- La configuración de RPO no aparece correctamente en la interfaz del servidor de configuración. El valor de datos sin comprimir muestra incorrectamente el valor comprimido.
	-  La operación de eliminación no efectúa la eliminación como se esperaba en el asistente de vContinuum y la replicación no se elimina de la interfaz del servidor de configuración.
	-  En el asistente de vContinuum el disco se deselecciona automáticamente al hacer clic en **Detalles** en la vista de disco durante la protección de las máquinas virtuales MSCS.
	- Durante el escenario P2V, servicios HP requeridos como CIMnotify y CqMgHost no se mueven a Manual en la máquina virtual de recuperación, lo cual provoca que se produzca un tiempo de arranque adicional.
	- Se produce un error en la máquina virtual Linux protegida cuando hay más de 26 discos en el servidor de destino principal.

## Pasos siguientes

Publique cualquier pregunta en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

<!---HONumber=AcomDC_0218_2016-->