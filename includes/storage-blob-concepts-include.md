## ¿Qué es el almacenamiento de blobs?

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados, como texto o datos binarios, a los que puede acceder desde cualquier lugar del mundo a través de HTTP o HTTPS. Puede usar el almacenamiento de blobs para exponer datos públicamente o para almacenar datos de la aplicación de manera privada.

El almacenamiento de blobs suele usarse para realizar las siguientes tareas:

-   Servicio de imágenes o documentos directamente a un explorador
-   Almacenamiento de archivos para acceso distribuido
-   Streaming de audio y vídeo
-   Realización de copia de seguridad segura y recuperación ante desastres
-   Almacenamiento de datos para el análisis en local o en un servicio hospedado de Azure

## Conceptos del servicio BLOB

El servicio BLOB contiene los componentes siguientes:

![Blob1][Blob1]

-   **Cuenta de almacenamiento:** todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md) para obtener información sobre la capacidad de la cuenta de almacenamiento.

-   **Contenedor:** un contenedor proporciona una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs.

-   **Blob:** archivo de cualquier tipo y tamaño. Almacenamiento de Azure ofrece tres tipos de blobs: blobs en bloques, blobs en páginas y blobs en anexos.
    
	Los *blobs en bloques* son ideales para almacenar archivos binarios o de texto, como documentos y archivos multimedia. Los *blobs en anexos* se parecen a los blobs en bloques porque se componen de bloques, pero están optimizados para las operaciones de anexión, por lo que son útiles para escenarios de registro. Un único blob en bloques o blob en anexos puede contener un máximo de 50.000 bloques de hasta 4 MB cada uno, hasta un tamaño total de algo más de 195 GB (4 MB × 50.000).
    
	Los *blobs en páginas* pueden tener un tamaño de hasta 1 TB y son más eficaces para operaciones frecuentes de lectura y escritura. Máquinas virtuales de Azure usa blobs en páginas como discos de sistema operativo y de datos.

	Para más información sobre los blobs, consulte [Descripción de los blobs en bloques, en anexos y en páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).

## Asignación de nombres y referencia a contenedores y blobs

Puede resolver la dirección de un blob en la cuenta de almacenamiento con el formato de dirección URL siguiente:
   
    http://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>  
      
Por ejemplo, esta es una dirección URL que trata uno de los blobs del diagrama anterior:

    http://sally.blob.core.windows.net/movies/MOV1.AVI

### Reglas de nomenclatura de contenedor

Un nombre de contenedor debe ser un nombre DNS válido y cumplir las reglas siguientes:

- Un nombre de contenedor debe aparecer todo en letras minúsculas.
- Los nombres de contenedor deben comenzar por una letra o un número, y pueden contener solo letras, números y el carácter de guión (-).
- Todos los caracteres de guión (-) deben estar inmediatamente precedidos y seguidos por una letra o un número; no se permiten guiones consecutivos en nombres de contenedor.
- Los nombres de contenedor deben tener entre 3 y 63 caracteres de longitud.

### Reglas de nomenclatura de blobs

Un nombre de blob debe cumplir las reglas siguientes:

- Un nombre de blob puede contener cualquier combinación de caracteres.
- Un nombre de blob debe tener al menos un carácter y no puede tener más de 1 024 caracteres.
- Los nombres de blobs distinguen entre mayúsculas y minúsculas.
- Los caracteres de URL reservados deben convertirse correctamente.
- El número de segmentos de ruta de acceso que componen el nombre de blob no puede ser superior a 254. Un segmento de ruta de acceso es la cadena entre caracteres delimitadores consecutivos (*por ejemplo*, la barra diagonal "/") que corresponde al nombre de un directorio virtual.

El servicio de blob se basa en un esquema de almacenamiento sin formato. Puede crear una jerarquía virtual especificando un delimitador de cadena o carácter dentro del nombre de blob para crear una jerarquía virtual. Por ejemplo, la siguiente lista muestra algunos nombres de blob válidos y únicos:

	/a
	/a.txt
	/a/b
	/a/b.txt

Puede usar el carácter delimitador para enumerar blobs jerárquicamente.


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=AcomDC_0224_2016-->