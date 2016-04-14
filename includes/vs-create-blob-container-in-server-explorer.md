Puede crear las colas de almacenamiento de Azure mediante el **Explorador de servidores** de Visual Studio.

![Blobs del explorador de servidores][Image1]

1. En el menú **Ver**, elija **Explorador de servidores**.
2. En el Explorador de servidores, expanda el nodo **Azure** de la suscripción, el nodo **Almacenamiento** y el nodo de la cuenta de almacenamiento que especificó en el servicio conectado al Almacenamiento de Azure.
3. Seleccione el nodo **Colas** y elija **Crear coa** en el menú contextual.
4. Escriba un nombre para el contenedor y elija **Aceptar**.   

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea hacer públicos los archivos del contenedor, seleccione el contenedor en **Explorador de servidores** y presione `F4` para mostrar la ventana **Propiedades**. Establezca el **Acceso de lectura público** en **Blob**. Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=Oct15_HO3-->