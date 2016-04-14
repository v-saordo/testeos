<properties
	pageTitle="Fase 5 de la granja de SharePoint Server 2013 | Microsoft Azure"
	description="Cree un grupo de disponibilidad y agréguele las bases de datos de SharePoint en la fase 5 de la granja de SharePoint Server 2013 en Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Fase 5 de carga de trabajo de granja de intranet de SharePoint: Creación del grupo de disponibilidad y adición de las bases de datos de SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

En esta fase final de la implementación de una granja solo de intranet de SharePoint 2013 con grupos de disponibilidad AlwaysOn de SQL Server en los servicios de infraestructura de Azure, creará un nuevo grupo de disponibilidad AlwaysOn y agregará las bases de datos de la granja de SharePoint.

Consulte [Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para ver todas las fases.

## Creación del grupo de disponibilidad y adición de bases de datos

SharePoint crea varias bases de datos como parte de la configuración inicial. Las bases de datos deben estar preparados con estos pasos:

1.	Realice una copia de seguridad completa y una copia de seguridad del registro de transacciones de la base de datos en el equipo principal.
2.	Restaure las copias de seguridad completas y de registros en la máquina de copia de seguridad.

Una vez que se ha realizado una copia de seguridad de las bases de datos y se han restaurado, pueden agregarse al grupo de disponibilidad. SQL Server solo permite en el grupo aquellas bases de datos de las que se haya realizado una copia de seguridad (al menos una vez) y que se hayan restaurado en otro equipo.

### Uso compartido de las carpetas de copia de seguridad

Para habilitar la copia de seguridad y la restauración, los archivos de copia de seguridad (.bak) deben ser accesibles desde la máquina virtual secundaria de SQL Server. Utilice el siguiente procedimiento:

1.	Inicie sesión en el host de servidor SQL principal como **[dominio]\\sp\_farm\_db**.
2.	Vaya al disco F:\\.
3.	Haga clic con el botón derecho en la carpeta **Copia de seguridad**, haga clic en **Compartir con** y después en **determinadas personas**.
4.	En el cuadro de diálogo **Uso compartido de archivos**, escriba **[dominio]\\sqlservice** y después haga clic en **Agregar**.
5.	Haga clic en la columna **Nivel de permiso** para el nombre de cuenta de **sqlservice** y, a continuación, haga clic en **Lectura/escritura**.
6.	Haga clic en **Compartir** dos veces y, a continuación, en **Hecho**.

Realice el procedimiento anterior en el host secundario de SQL Server, salvo que asigne a la cuenta de sqlservice permiso de **lectura** para la carpeta F:\\Backup en el paso 5.

### Copia de seguridad y restauración de una base de datos

Los siguientes procedimientos deben repetirse para cada base de datos que debe agregarse al grupo de disponibilidad. Algunas bases de datos de SharePoint 2013 no son compatibles con los grupos de disponibilidad AlwaysOn de SQL Server. Para obtener más información, consulte [Opciones de recuperación ante desastres y de alta disponibilidad compatibles para bases de datos de SharePoint](http://technet.microsoft.com/library/jj841106.aspx).

Siga estos pasos para hacer una copia de seguridad de una base de datos:

1.	En la pantalla de inicio del equipo del servidor de SQL Server principal, escriba **SQL Studio** y después haga clic en **SQL Server Management Studio**.
2.	Haga clic en **Conectar**.
3.	En el panel izquierdo, amplíe el nodo **Base de datos**.
4.	Haga clic con el botón derecho en una base de datos para realizar una copia de seguridad, seleccione **Tareas** y después haga clic en **Copia de seguridad**.
5.	En la sección **Destino**, haga clic en **Quitar** para quitar la ruta de acceso del archivo predeterminado para el archivo de copia de seguridad.
6.	Haga clic en **Agregar**. En **Nombre de archivo**, escriba **\\[nombreDeEquipo]\\backup[nombreDeBaseDeDatos].bak**, donde nombreDeEquipo es el nombre del equipo del servidor SQL principal y nombreDeBaseDeDatos es el nombre de la base de datos. Haga clic en **Aceptar** y, a continuación, haga clic en **Aceptar** de nuevo después del mensaje sobre la copia de seguridad correcta.
7.	En el panel izquierdo, haga clic con el botón derecho en **[nombreDeBaseDeDatos]**, seleccione **Tareas** y después haga clic en **Copia de seguridad**.
8.	En **Tipo de copia de seguridad**, seleccione **Registro de transacciones** y, a continuación, haga clic en **Aceptar** dos veces.
9.	Mantenga esta sesión de Escritorio remoto abierta.

Use estos pasos para restaurar una base de datos:

1.	Inicie sesión en el equipo de SQL Server secundario como **[nombreDeDominio]\\sp\_farm\_db**.
2.	En la pantalla de inicio, escriba **SQL Studio** y, a continuación, haga clic en **SQL Server Management Studio**.
3.	Haga clic en **Conectar**.
4.	En el panel izquierdo, haga clic con el botón derecho en **Bases de datos** y, a continuación, haga clic en **Restaurar base de datos**.
5.	En la sección **Origen**, seleccione **Dispositivo** y haga clic en el botón de puntos suspensivos (...).
6.	En **Seleccionar dispositivos de copia de seguridad**, haga clic en **Agregar**.
7.	En **Ubicación de archivo de copia de seguridad**, escriba **\\[nombreDeEquipo]\\backup**, presione Entrar, seleccione **[nombreDeBaseDeDatos].bak** y después haga clic en **Aceptar** dos veces. Ahora debería ver la copia de seguridad completa y la copia de seguridad del registro en la sección **Conjuntos de copia de seguridad para restaurar**.
8.	En **Seleccionar una página**, haga clic en **Opciones**. En la sección **Opciones de restauración** en **Estado de recuperación**, seleccione **RESTAURAR CON NORECOVERY** y, a continuación, haga clic en **Aceptar**.
9.	Cuando se le solicite, haga clic en **Aceptar**.

### Creación de un grupo de disponibilidad

Después de preparar al menos una base de datos (mediante el método de copia de seguridad y restauración), cree un grupo de disponibilidad.

1.	Vuelva a la sesión de Escritorio remoto para el equipo principal de SQL Server.
2.	En **SQL Server Management Studio**, en el panel izquierdo, haga clic en **Alta disponibilidad AlwaysOn** y, a continuación, haga clic en **Asistente para nuevo grupo de disponibilidad**.
3.	En la página **Introducción**, haga clic en **Siguiente**.
4.	En la página **Especificar nombre de grupo de disponibilidad**, escriba el nombre del grupo de disponibilidad en **Nombre de grupo de disponibilidad** (ejemplo: AG1) y después haga clic en **Siguiente**.
5.	En la página **Seleccionar bases de datos**, seleccione las bases de datos para la granja de SharePoint para la que se realizó la copia de seguridad y haga clic en **Siguiente**. Estas bases de datos cumplen los requisitos previos para un grupo de disponibilidad porque se ha realizado al menos una copia de seguridad completa en la réplica principal pretendida.
6.	En la página **Especificar réplicas**, haga clic en **Agregar réplica**.
7.	En **Conectar al servidor**, escriba el nombre del equipo de SQL Server secundario y, a continuación, haga clic en **Conectar**.
8.	En la página **Especificar réplicas**, el host de servidor SQL Server secundario aparece en **Réplicas de disponibilidad**. Para ambas instancias, establezca los valores de las opciones siguientes:

Rol inicial | Opción | Valor
--- | --- | ---
Principal | Conmutación automática por error (hasta 2) | Seleccionado
Secundario | Conmutación automática por error (hasta 2) | Seleccionado
Principal | Confirmación sincrónica (hasta 3) | Seleccionado
Secundario | Confirmación sincrónica (hasta 3) | Seleccionado
Principal | Secundaria legible | Sí
Secundario | Secundaria legible | Sí

9.	Haga clic en **Siguiente**.  
10.	En la página **Seleccionar sincronización de datos iniciales**, haga clic en **Solo unirse** y después haga clic en **Siguiente**. La sincronización de datos se ejecuta manualmente realizando copias de seguridad completas y de transacciones en el servidor principal y restaurándola en la copia de seguridad.  
En su lugar, puede seleccionar **Completo** para permitir que el Asistente de nuevo grupo de disponibilidad realice la sincronización de datos por usted. Sin embargo, no se recomienda sincronizar las bases de datos de gran tamaño que se encuentran en algunas empresas.  
11.	En la página **Validación**, haga clic en **Siguiente**. Hay una advertencia para una configuración de agente de escucha ausente porque no hay configurado un agente de escucha del grupo de disponibilidad.
12.	En la página **Resumen**, haga clic en **Finalizar**. Una vez que el asistente haya finalizado, inspeccione la página **Resultados** para comprobar que se ha creado correctamente el grupo de disponibilidad. Si es así, haga clic en **Cerrar** para salir del asistente.
13.	Desde la pantalla de inicio, escriba **Conmutación por error** y, a continuación, haga clic en **Administrador de clústeres de conmutación por error**. En el panel izquierdo, abra el nombre del clúster y, a continuación, haga clic en **Roles**. Debe estar presente una nueva función con el nombre del grupo de disponibilidad.  

Ha configurado correctamente un grupo de disponibilidad de SQL Server AlwaysOn para las bases de datos de SharePoint.

## Configuración de un agente de escucha para el grupo de disponibilidad AlwaysOn

Opcionalmente, puede crear una configuración de agente de escucha para el grupo de disponibilidad AlwaysOn. Para ver los pasos, consulte [Tutorial: Configuración del agente de escucha de los Grupos de disponibilidad AlwaysOn](https://msdn.microsoft.com/library/dn425027.aspx). Debe crear un único agente de escucha y configurarlo para que use una dirección IP virtual de una instancia de carga equilibrada interna.

Una vez creado el agente de escucha, deberá configurar todas las máquinas virtuales de SharePoint para que usen el agente de escucha, en lugar del nombre del primer equipo SQL Server en el clúster. En lugar de usar nombres, configure las máquinas virtuales de SharePoint para que usen un alias de SQL. Para obtener información y conocer los pasos, consulte [Alias de SQL para SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx).

Para obtener información adicional acerca de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server, vea [Configurar grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).


## Paso siguiente

- [Configure](https://technet.microsoft.com/library/ee836142.aspx) su granja de SharePoint.

<!---HONumber=AcomDC_1217_2015-->