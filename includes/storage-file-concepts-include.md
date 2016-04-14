## ¿Qué es Almacenamiento de archivos de Azure?

Almacenamiento de archivos ofrece almacenamiento compartido para aplicaciones que usan el protocolo SMB 2.1 o SMB 3.0 estándar. Las máquinas virtuales y los servicios en la nube de Microsoft Azure pueden compartir datos de archivo entre componentes de aplicaciones a través de recursos compartidos montados, y las aplicaciones locales pueden acceder a datos de archivo de un recurso compartido a través de la API de Almacenamiento de archivos.

Las aplicaciones que se ejecutan en máquinas virtuales o servicios en la nube de Azure pueden montar un recurso compartido de Almacenamiento de archivos para acceder a archivos de datos, igual que montaría una aplicación de escritorio un recurso compartido SMB normal. Cualquier número de roles o máquinas virtuales de Azure puede montar y acceder simultáneamente al recurso compartido de Almacenamiento de archivos.

Puesto que un recurso compartido de Almacenamiento de archivos es un recurso compartido de archivos estándar en aplicaciones que usan el protocolo SMB, las aplicaciones que se ejecutan en Azure pueden acceder al recurso compartido a través de API de E/S de archivos. Por tanto, los desarrolladores pueden aprovechar el código y los conocimientos que ya tienen para migrar las aplicaciones actuales. Los profesionales de TI pueden usar cmdlets de PowerShell para crear, montar y administrar recursos compartidos de Almacenamiento de archivos como parte de la administración de aplicaciones de Azure. Esta guía le mostrará ejemplos de ambos.

Almacenamiento de archivos suele usarse para realizar las siguientes tareas:

- Migrar aplicaciones locales que se basan en recursos compartidos de archivos para ejecutarse en máquinas virtuales o servicios en la nube de Azure, sin necesidad de reescribir código, con el coste que eso supone;
- Almacenar configuraciones de aplicaciones compartidas, por ejemplo en archivos de configuración;
- Almacenar datos de diagnóstico como registros, métricas y volcados de memoria en una ubicación compartida; 
- Almacenar herramientas y utilidades necesarias para desarrollar o administrar máquinas virtuales o servicios en la nube de Azure.

## Conceptos de Almacenamiento de archivos

Almacenamiento de archivos contiene los siguientes componentes:

![files-concepts][files-concepts]

-   **Cuenta de almacenamiento:** todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](../articles/storage/storage-scalability-targets.md) para obtener información sobre la capacidad de la cuenta de almacenamiento.

-   **Compartir:** un recurso compartido de Almacenamiento de archivos es un recurso compartido de archivos de SMB en Azure. Todos los directorios y archivos se deben crear en un recurso compartido principal. Una cuenta puede contener un número ilimitado de recursos compartidos y un recurso compartido puede almacenar un número ilimitado de archivos, hasta una capacidad total de 5 TB del recurso compartido de archivos.

-   **Directorio:** una jerarquía de directorios opcional.

-	**Archivo:** se trata de un archivo del recurso compartido. Un archivo puede tener un tamaño de hasta 1 TB.

-   **Formato de URL:** los archivos son direccionables mediante el formato de URL siguiente: https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`
    
    En el diagrama anterior podría usarse la siguiente dirección URL de ejemplo para dirigir uno de los archivos: `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Para obtener detalles sobre cómo asignar un nombre a recursos compartidos, directorios y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](http://msdn.microsoft.com/library/azure/dn167011.aspx).

[files-concepts]: ./media/storage-file-concepts-include/files-concepts.png

<!----HONumber=AcomDC_0204_2016-->