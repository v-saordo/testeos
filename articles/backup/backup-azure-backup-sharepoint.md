<properties
	pageTitle="Protección de la granja de SharePoint en Azure con DPM | Microsoft Azure"
	description="En este artículo se incluye información general sobre la protección de la granja de servidores de SharePoint en Azure con DPM."
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="giridham;jimpark"/>


# Copia de seguridad de una granja de SharePoint en Azure
Una copia de seguridad de una granja de SharePoint se crea mediante System Center Data Protection Manager (DPM) casi de la misma manera que realiza una copia de otros orígenes de datos. Azure ofrece flexibilidad en la programación de copia de seguridad para crear puntos de copia de seguridad diarios, semanales, mensuales o anuales de copia de seguridad señala, y le ofrece diferentes opciones de directiva de retención para varios puntos de copia de seguridad. DPM proporciona la capacidad de almacenar copias de disco local para objetivos de tiempo de recuperación (RTO) más rápidos y en Azure para un destino de retención rentable a largo plazo.

## Las versiones compatibles de SharePoint y relacionadas con escenarios de protección
Copia de seguridad de Azure para DPM admite los siguientes escenarios.

| Carga de trabajo | Versión | Implementación de SharePoint | Tipo de implementación de DPM | DPM: System Center 2012 R2 | Protección y recuperación |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| SharePoint | SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 | SharePoint implementado como servidor físico o máquina virtual de Hyper-V/VmWare<br> -------------- <br> SQL AlwaysOn | Servidor físico o máquina virtual de Hyper-V local | Admite la copia de seguridad en Azure desde el paquete acumulativo de actualizaciones 5 | Protección de recuperación de la granja de SharePoint: granja de servidores, base de datos, archivo o elemento de lista del disco y granja de recuperación y base de datos de Azure |

## Antes de comenzar
Debe asegurarse de comprobar algunas cosas antes de crear una copia de seguridad de una granja de SharePoint en Azure.

### Requisitos previos
Antes de continuar, asegúrese de que se cumplen todos los [requisitos previos](backup-azure-dpm-introduction.md#prerequisites) para usar Copia de seguridad de Microsoft Azure para proteger cargas de trabajo. Los requisitos previos cubren tareas como: crear un almacén de copia de seguridad, descargar las credenciales del almacén, instalar el agente de Copia de seguridad de Azure y registrar el servidor con el almacén.

### Agente de DPM
El agente de DPM debe instalarse en el servidor de SharePoint, servidores SQL Server y cualquier otro servidor que forman parte de la granja de servidores de SharePoint. Para más información sobre cómo configurar el agente de protección, vea [Programa de instalación de agente de protección](https://technet.microsoft.com/library/hh758039.aspx). La única excepción es que sólo instale al agente en un único servidor Web Front-End (WFE). DPM solo necesita el agente en un servidor WFE para servir como punto de entrada para la protección.

### Granja de SharePoint
Para cada 10 millones de elementos del conjunto de servidores, debe haber al menos 2 GB de espacio en el volumen donde se encuentra la carpeta DPM. Este espacio se requiere para la generación del catálogo. Para que DPM pueda recuperar elementos específicos (colecciones de sitios, sitios, listas, bibliotecas de documentos, carpetas, documentos individuales y elementos de lista), generación de catálogos crea una lista de las direcciones URL dentro de cada base de datos de contenido. Puede ver la lista de direcciones URL en el panel elemento recuperable en el área de tareas de recuperación de la consola de administrador DPM.

### SQL Server
DPM se ejecuta como sistema local y para realizar copias de seguridad de las bases de datos de SQL Server, necesita privilegios de sysadmin en esa cuenta para el SQL Server. Establezca NT AUTHORITY\\SYSTEM en *sysadmin* en el servidor SQL Server cuya copia de seguridad quiere crear.

En la granja de SharePoint, si tiene bases de datos de SQL Server que están configuradas con alias de SQL Server, instale los componentes de cliente de SQL Server en el servidor web front-end que DPM protegerá.

### SharePoint Server
Aunque el rendimiento depende de muchos factores, como el tamaño de la granja de SharePoint, como guía general, un servidor DPM puede utilizarse para proteger una granja de SharePoint de 25 TB.

### Paquete acumulativo de actualizaciones 5 de DPM
Para empezar a proteger una granja de SharePoint en Azure, debe instalar el paquete acumulativo de actualizaciones 5 o superior de DPM. El Paquete acumulativo de actualizaciones 5 ofrece la capacidad de proteger una granja de SharePoint (configurada con AlwaysOn de SQL) en Azure. Para más información, vea [Instalación del Paquete acumulativo de actualizaciones 5 de DPM](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### Lo que no se admite
1. Proteger una granja de servidores de SharePoint no protege las bases de datos del servicio de aplicación ni los índices de búsqueda. Deberá configurar la protección de estas bases de datos por separado.
2. DPM no proporciona copia de seguridad de bases de datos SQL de SharePoint hospedadas en recursos compartidos del servidor de archivos de escalabilidad horizontal (SOFS).

## Configuración de la protección de SharePoint
Debe configurar el servicio VSS Writer de SharePoint (servicio WSS Writer) mediante **ConfigureSharePoint.exe** para poder proteger SharePoint con DPM.

Puede encontrar **ConfigureSharePoint.exe** en la carpeta [ruta de instalación de DPM]\\bin en el servidor web front-end. Esta herramienta proporciona al agente de protección las credenciales para la granja de servidores de SharePoint. Debe ejecutarlo en un solo servidor WFE. Si tiene varios servidores WFE, seleccione solo uno al configurar un grupo de protección.

### Para configurar el servicio VSS Writer de SharePoint
1. En el servidor WFE, en un símbolo del sistema, vaya a [ubicación de instalación de DPM]\\bin\\
2. Ejecute ConfigureSharePoint - EnableSharePointProtection
3. Escriba las credenciales de administrador de la granja de servidores. Esta cuenta debe ser miembro del grupo de administradores local en el servidor WFE. Si el administrador de la granja no es un administrador local, conceda los permisos siguientes en el servidor WFE:
  - Conceda al grupo WSS\_Admin\_WPG control total sobre la carpeta DPM (%Program Files%\\Microsoft Data Protection Manager\\DPM).
  - Conceda al grupo WSS\_Admin\_WPG acceso de lectura a la clave del Registro de DPM (HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Data Protection Manager).

>[AZURE.NOTE]Deberá ejecutar ConfigureSharePoint.exe cada vez que haya un cambio en las credenciales de administrador de la granja de servidores de SharePoint.

## Creación de copia de seguridad de una granja de SharePoint con DPM
Una vez que haya configurado DPM y la granja de servidores de SharePoint, como se explicó anteriormente, DPM puede proteger SharePoint.

### Para proteger una granja de SharePoint
1. En la pestaña **Protección** de la Consola de administrador DPM, haga clic en **Nuevo**. ![Nueva pestaña de protección](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)

2. En la pantalla **Seleccionar tipo de grupo de protección** del asistente **Crear nuevo grupo de protección**, seleccione **Servidores** y haga clic en **Siguiente**.

    ![Seleccionar tipo de grupo de protección](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)

3. En la pantalla **Seleccionar miembros del grupo**, active la casilla del servidor de SharePoint que desea proteger y haga clic en **Siguiente**.

    ![Seleccionar a miembros del grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

    >[AZURE.NOTE]Con el agente DPM instalado, puede ver el servidor en el asistente. DPM también muestra su estructura. Como se ejecutó ConfigureSharePoint.exe, DPM se comunica con VSS Writer de SharePoint y sus bases de datos SQL correspondientes y reconoce la estructura de la granja de servidores de SharePoint (las bases de datos de contenido asociadas y los elementos correspondientes).

4. En la pantalla **Seleccionar método de protección de datos**, escriba el nombre del *Grupo de protección* y seleccione los *métodos de protección* que prefiera. Haga clic en **Siguiente**.

    ![Seleccionar método de protección de datos](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

    >[AZURE.NOTE]La elección de protección del disco ayuda a cumplir los objetivos de tiempo de recuperación con tiempos de cortos. Azure es un objetivo de protección a largo plazo más rentable en comparación con las cintas. Para más información, vea este [artículo](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/).

5. En la pantalla **Especificar objetivos a corto plazo**, seleccione el *intervalo de retención* que prefiera y especifique cuándo quiere que se creen las copias de seguridad.

    ![Especificar objetivos a corto plazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

    >[AZURE.NOTE]Como la recuperación suele ser más necesaria para datos de menos de 5 días, seleccionamos un intervalo de retención de 5 días en el disco y nos aseguramos que la copia de seguridad se crea durante las horas que no son de producción para este ejemplo.

6. Revise el espacio en disco del grupo de almacenamiento asignado al grupo de protección y haga clic en**Siguiente**.

7. Para cada grupo de protección, DPM asigna espacio en disco para almacenar y administrar réplicas. En este punto, DPM debe crear una copia de los datos seleccionados. Seleccione cómo y cuándo quiere que se cree la réplica y haga clic en **Siguiente**.

    ![Elegir método de creación de réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

    >[AZURE.NOTE]Para asegurarse de que no se aplica el tráfico de red, seleccione una hora fuera del horario de producción.

8. DPM garantiza la integridad de los datos con comprobaciones de coherencia en la réplica. Hay dos opciones disponibles. Puede definir una programación para ejecutar comprobaciones de coherencia o permitir que DPM pueda ejecutar comprobaciones de coherencia automáticamente en la réplica cada vez que sea incoherente. Seleccione la opción que quiera y haga clic en **Siguiente**.

    ![Comprobación de coherencia](./media/backup-azure-backup-sharepoint/consistency-check.png)

9. En la pantalla **Especificar datos de protección en línea**, seleccione la granja de servidores de SharePoint que desea proteger y haga clic en **Siguiente**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)

10. En la pantalla **Especificar programación de copia de seguridad en línea**, seleccione la programación que prefiera y haga clic en **Siguiente**.

    ![Online\_backup\_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    >[AZURE.NOTE]DPM permite dos copias de seguridad diarias en momentos diferentes en Azure.

11. Según la programación de copia de seguridad seleccionada, en la pantalla **Especificar directiva de retención en línea**, seleccione la directiva de retención para los puntos de copia de seguridad diaria, semanal, mensual y anual.

    ![Online\_retention\_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    >[AZURE.NOTE]DPM usa un esquema de retención abuelo-padre-hijo en el que se puede elegir una directiva de retención diferente para distintos puntos de copia de seguridad.

12. Como ocurre con el disco, se debe crear una réplica de punto de referencia inicial en Azure. Seleccione la opción que prefiera para crear la copia de seguridad inicial en Azure y haga clic en **Siguiente**.

    ![Online\_replica](./media/backup-azure-backup-sharepoint/online-replication.png)

13. Revise la configuración seleccionada en la página **Resumen** y haga clic en **Crear grupo**. Verá un mensaje de correcto una vez que se haya creado el grupo de protección.

    ![Resumen](./media/backup-azure-backup-sharepoint/summary.png)

## Restauración de un elemento de SharePoint desde el disco con DPM
En el ejemplo siguiente, el *elemento de recuperación de SharePoint* se eliminó accidentalmente y se debe recuperar.![Protección de SharePoint con DPM4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra la **Consola de administrador DPM**. En la ficha Protección, se muestran todas las granjas de SharePoint protegidas por DPM.

    ![Protección de SharePoint con DPM3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)

2. Para empezar a recuperar el elemento, seleccione la pestaña **Recuperación**.

    ![Protección de SharePoint con DPM5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)

3. Puede buscar el *elemento de recuperación de SharePoint* en SharePoint mediante una búsqueda basada en comodines en un intervalo de puntos de recuperación.

    ![Protección de SharePoint con DPM6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)

4. Seleccione el punto de recuperación adecuado en los resultados de búsqueda, haga clic con el botón derecho y elija **Recuperar**.

5. También puede navegar a través de varios puntos de recuperación y seleccionar una base de datos o un elemento para recuperar. Seleccione **fecha > Hora de recuperación** y luego el elemento **base de datos > granja de SharePoint > punto de recuperación > adecuado**.

    ![Protección de SharePoint con DPM7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)

6. Haga clic con el botón derecho en el elemento y seleccione **Recuperar** para abrir el **Asistente para recuperación**. Haga clic en **Siguiente**.

    ![Revisar selección de recuperación](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)

7. Seleccione el tipo de recuperación que quiere realizar y haga clic en **Siguiente**.

    ![Tipo de recuperación](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

    >[AZURE.NOTE]El ejemplo anterior muestra el elemento de recuperación para el sitio de SharePoint original.

8. Seleccione el **Proceso de recuperación** que quiere usar.
    - Seleccione **Recuperar sin una granja de servidores de recuperación** si la granja de SharePoint no ha cambiado y es la misma que el punto de recuperación que se restaura.
    - Seleccione **Recuperar con una granja de servidores de recuperación** si la granja de servidores de SharePoint ha cambiado desde que se creó el punto de recuperación.

    ![Proceso de recuperación](./media/backup-azure-backup-sharepoint/recovery-process.png)

9. Proporcione una ubicación provisional de la instancia SQL para recuperar la base de datos temporalmente y un recurso compartido de archivos de almacenamiento provisional en el servidor de DPM y el servidor de SharePoint para recuperar el elemento.

    ![Ubicación provisional1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM conecta la base de datos de contenido, que hospeda el elemento de SharePoint, a la instancia de SQL temporal. Desde la base de datos de contenido, el servidor de DPM recupera el elemento y lo coloca en la ubicación del archivo provisional en el servidor de DPM. Ahora, el elemento recuperado en la ubicación de almacenamiento provisional, en el servidor de DPM, debe exportarse en la ubicación provisional de la granja de servidores de SharePoint.

    ![Ubicación provisional2](./media/backup-azure-backup-sharepoint/staging-location2.png)

10. Seleccione **Especificar opciones de recuperación** y aplique la configuración de seguridad a la granja de SharePoint o aplique la configuración de seguridad del punto de recuperación. Haga clic en **Siguiente**.

    ![Opciones de recuperación](./media/backup-azure-backup-sharepoint/recovery-options.png)

    >[AZURE.NOTE]Puede limitar el uso de ancho de banda de red. Esto minimiza el impacto en el servidor de producción durante las horas de producción.

11. Revise la información de resumen y haga clic en **Recuperar** para empezar la recuperación del archivo.

    ![Resumen de recuperación](./media/backup-azure-backup-sharepoint/recovery-summary.png)

12. Ahora seleccione la pestaña **Supervisión** en la **Consola de administrador DPM** para ver el *Estado*de la recuperación.

    ![Estado de recuperación](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    >[AZURE.NOTE]El archivo ya se ha restaurado. Puede actualizar el sitio de SharePoint para comprobar el archivo restaurado.

## Restauración de una base de datos de SharePoint de Azure con DPM

1. Para recuperar una base de datos de contenido de SharePoint, desplácese a través de varios puntos de recuperación (tal como se mostró anteriormente) y seleccione el punto de recuperación desee recuperar.

    ![Protección de SharePoint con DPM8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)

2. Haga doble clic en el punto de recuperación de SharePoint para mostrar la información de catálogo de SharePoint disponible.

    > [AZURE.NOTE]Como la granja ´de SharePoint está protegida para la retención a largo plazo en Azure, no hay información de catálogo (metadatos) disponible del servidor de DPM. Como resultado, cada vez que se debe recuperar una base de datos de contenido de SharePoint en un momento dado deberá volver a catalogar la granja de servidores de SharePoint.

3. Haga clic en **Volver a catalogar**.

    ![Protección de SharePoint con DPM10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Aparece la ventana de estado **Volver a catalogar la nube **.

    ![Protección de SharePoint con DPM11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Una vez completada la catalogación, el estado cambia a*Correcto*. Haga clic en **Cerrar**.

    ![Protección de SharePoint con DPM12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)

4. Haga clic en el objeto de SharePoint que se muestra en la pestaña **Recuperación** de DPM para obtener la estructura de la base de datos de contenido. Haga clic con el botón derecho en > **Recuperar**.

    ![Protección de SharePoint con DPM13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)

5. En este momento, siga los [pasos de recuperación anteriores](#restore-a-sharepoint-item-from-disk-using-dpm) para recuperar una base de datos de contenido de Sharepoint desde el disco.

## Preguntas más frecuentes
P: ¿Qué versiones de DPM admiten SQL 2014 y SQL 2012 (SP2)<br> R: DPM 2012 R2 con Paquete acumulativo de actualizaciones 4 admite las mismas

P: ¿Puedo recuperar el elemento de SharePoint en la ubicación original si SharePoint está configurada con AlwaysOn de SQL (con protección en disco)?<br> R: Sí, se puede recuperar el elemento hasta el sitio de SharePoint original

P: ¿Puedo recuperar una base de datos de SharePoint en la ubicación original si SharePoint está configurada con AlwaysOn de SQL?<br> R: Como las bases de datos de SharePoint están configuradas en AlwaysOn de SQL, no se pueden modificar a menos que se quite el grupo de disponibilidad (AG). En consecuencia, DPM no puede restaurar la base de datos en la ubicación original. Puede recuperar la base de datos SQL a otra instancia de SQL.

## Pasos siguientes
- Más información sobre la protección de SharePoint con DPM; vea [Serie de vídeos: protección de SharePoint con DPM](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
- Vea [Notas de la versión de System Center 2012: Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
- Vea [Notas de la versión de Data Protection Manager en System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

<!---HONumber=AcomDC_1217_2015-->