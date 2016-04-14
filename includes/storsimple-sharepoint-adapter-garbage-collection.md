<!--author=SharS last changed: 9/17/15-->

En este procedimiento, hará lo siguiente:

1. [Preparar la ejecución del archivo ejecutable del mantenedor](#to-prepare-to-run-the-maintainer).

2. [Prepare la base de datos de contenido y la papelera de reciclaje para la eliminación inmediata de blobs huérfanos](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).

3. [Ejecutar Maintainer.exe](#to-run-the-maintainer).

4. [Revertir la base de datos de contenido y la configuración de la papelera de reciclaje](#to-revert-the-content-database-and-recycle-bin-settings).

#### Para preparar la ejecución del mantenedor

1. En el servidor web front-end, abra el Shell de administración de SharePoint 2013 como administrador.

2. Vaya a la carpeta *unidad de arranque*:\\Archivos de programa\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer.

3. Cambie el nombre de **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** a **web.config**.

4. Use `aspnet_regiis -pdf connectionStrings` para descifrar el archivo web.config.

5. En el archivo web.config descifrado, en el nodo `connectionStrings`, agregue la cadena de conexión para la instancia de SQL Server y el nombre de la base de datos de contenido. Consulte el ejemplo siguiente.

    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=";Remote Blob Storage Maintainer for WSS_Content";" providerName="System.Data.SqlClient" />`

6. Use `aspnet_regiis –pef connectionStrings` para volver a cifrar el archivo web.config.

7. Cambie el nombre de web.config a Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config.

#### Para preparar el contenido de la base de datos y la papelera de reciclaje para eliminar inmediatamente blobs huérfanos

1. En SQL Server, en SQL Management Studio, ejecute las siguientes consultas de actualización para la base de datos de contenido de destino: 

       `use WSS_Content`

       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. En el servidor web front-end, en**Administración central**, edite la **Configuración general de la aplicación web**para la base de datos de contenido deseada para deshabilitar temporalmente la papelera de reciclaje. Esta acción también vacía la papelera de reciclaje para cualquier colección de sitios relacionados. Para ello, haga clic en **Administración central** -> **Administración de aplicaciones** -> **Aplicaciones web (Administrar aplicaciones web)** -> **SharePoint - 80** -> **Configuración de aplicación general**. Establezca el **Estado de la papelera de reciclaje** en **Desactivado**.

    ![Configuración general de la aplicación web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### Para ejecutar el mantenedor

- En el servidor web front-end, en el Shell de administración de SharePoint 2013, ejecute el mantenedor como sigue:

      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`

    >[AZURE.NOTE]Solo la operación `GarbageCollection` es compatible con StorSimple en este momento. Tenga en cuenta también que los parámetros emitidos para Microsoft.Data.SqlRemoteBlobs.Maintainer.exe distinguen mayúsculas de minúsculas.
 
#### Para revertir la base de datos de contenido y la configuración de la papelera de reciclaje.

1. En SQL Server, en SQL Management Studio, ejecute las siguientes consultas de actualización para la base de datos de contenido de destino:

      `use WSS_Content`

      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`

2. En el servidor web front-end, en**Administración central**, edite la **Configuración general de la aplicación web**para la base de datos de contenido deseada para volver a habilitar la papelera de reciclaje. Para ello, haga clic en **Administración central** -> **Administración de aplicaciones** -> **Aplicaciones web (Administrar aplicaciones web)** -> **SharePoint - 80** -> **Configuración de aplicación general**. Establezca el estado de la Papelera de reciclaje en **Activado**.

<!---HONumber=AcomDC_0107_2016-->