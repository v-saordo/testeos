<!--author=SharS last changed: 9/17/15-->

### Actualización de SharePoint 2010 a SharePoint 2013 e instalación del Adaptador de StorSimple para SharePoint

>[AZURE.IMPORTANT]Los archivos migrados previamente al almacenamiento externo mediante RBS no estarán disponibles hasta que finalice la actualización y se vuelva a habilitar la característica RBS. Para limitar el impacto en el usuario, realice cualquier actualización o reinstalación durante una ventana de mantenimiento programada.

#### Para actualizar SharePoint 2010 a SharePoint 2013 y, a continuación, instalar el adaptador

1. En la granja de SharePoint 2010, anote la ruta de acceso de almacenamiento de blobs para los blobs externalizados y las bases de datos de contenido para las que RBS está habilitado. 

2. Instale y configure la nueva granja de SharePoint 2013.

3. Mueva las bases de datos, aplicaciones y colecciones de sitios de la granja SharePoint 2010 a la nueva granja de SharePoint 2013. Para obtener instrucciones, vaya a [Información general del proceso de actualización a SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).

4. Instale el adaptador de StorSimple para SharePoint en una nueva granja. Vaya a [Instalación del adaptador de StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint) para ver los procedimientos.

5. Con la información que anotó en el paso 1, habilite RBS para el mismo conjunto de bases de datos de contenido y proporcione la misma ruta de almacén de blobs que se usó en la instalación de SharePoint 2010. Vaya a [Configuración de RBS](#configure-rbs) para ver los procedimientos. Después de completar este paso, los archivos anteriormente externalizados deben ser accesibles desde la nueva granja.

### Actualización del adaptador de StorSimple para SharePoint

>[AZURE.IMPORTANT]Debe programar esta actualización para que se produzca durante una ventana de mantenimiento planeada por las razones siguientes:
>
>- El contenido anteriormente externalizado no estará disponible hasta que se vuelva a instalar el adaptador.
>
>- Cualquier contenido cargado en el sitio después de desinstalar la versión anterior del adaptador de StorSimple para SharePoint, pero antes de instalar la nueva versión, se almacenará en la base de datos de contenido. Necesitará mover ese contenido al dispositivo de StorSimple después de instalar el nuevo adaptador. Puede usar el cmdlet de Microsoft` RBS Migrate()` PowerShell incluido con SharePoint para migrar el contenido. Para obtener más información, vea [migrar contenido dentro o fuera de RBS](https://technet.microsoft.com/library/ff628255.aspx).


#### Para actualizar el adaptador de StorSimple para SharePoint 

1. Desinstale la versión anterior del adaptador de StorSimple para SharePoint.

    >[AZURE.NOTE]Esto deshabilitará automáticamente RBS en las bases de datos de contenido. Sin embargo, los blobs existentes permanecerán en el dispositivo de StorSimple. Dado que RBS se deshabilita y no se han migrado los blobs a las bases de datos de contenido, se producirá un error en todas las solicitudes de esos blobs.
 
2. Instale el nuevo adaptador de StorSimple para SharePoint. El nuevo adaptador reconocerá automáticamente las bases de datos de contenido que se hayan habilitado o deshabilitados para RBS y usará la configuración anterior.

<!---HONumber=Oct15_HO3-->