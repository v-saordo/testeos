Recurso|Límite predeterminado
---|---
Número máximo de cuentas de almacenamiento por suscripción|100<sup>1</sup>
TB por cuenta de almacenamiento|500 TB
Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento|El único límite es los 500 TB de capacidad de la cuenta de almacenamiento
Tamaño máximo de un contenedor de blobs, una tabla o una cola|500 TB
Número máximo de bloques en un blob en bloques o blob de anexión|50\.000
Tamaño máximo de un bloque en un blob en bloques o blob de anexión|4 MB
Tamaño máximo de un blob en bloques o blob de anexión|50 000 x 4 MB (195 GB aproximadamente) 
Tamaño máximo de un blob en páginas |1 TB
Tamaño máximo de una entidad de tabla|1 MB
Número máximo de propiedades de una entidad de tabla|252
Tamaño máximo de un mensaje de una cola|64 KB
Tamaño máximo de un recurso compartido de archivos|5 TB
Tamaño máximo de un recurso compartido de archivos|1 TB
Número máximo de archivos en un recurso compartido de archivos|El único límite es la capacidad total de 5 TB del recurso compartido de archivos
Máximo de 8 KB de IOPS por recurso compartido|1000
Número máximo de archivos en un recurso compartido de archivos|El único límite es la capacidad total de 5 TB del recurso compartido de archivos
Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento|El único límite es los 500 TB de capacidad de la cuenta de almacenamiento
Número máximo de directivas de acceso almacenadas por contenedor, recurso compartido de archivos, tabla o cola|5
Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB) por cuenta de almacenamiento|Hasta 20.000 IOPS, entidades por segundo o mensajes por segundo
Rendimiento de un blob|Hasta 60 MB por segundo o hasta 500 solicitudes por segundo
Rendimiento de una cola (mensajes de 1 KB)|Hasta 2000 mensajes por segundo
Rendimiento de destino de una sola partición de tabla (entidades de 1 KB)|Hasta 2000 entidades por segundo
Rendimiento de un recurso compartido de archivos|Hasta 60 MB por segundo
Entrada máxima<sup>2</sup> por cuenta de almacenamiento (regiones de EE. UU.)|10 Gbps si GRS/ZRS<sup>3</sup> está habilitado, 20 Gbps para LRS
Salida máxima<sup>2</sup> por cuenta de almacenamiento (regiones de EE. UU.)|20 Gbps si RA-GRS/GRS/ZRS<sup>3</sup> está habilitado, 30 Gbps para LRS
Entrada máxima<sup>2</sup> por cuenta de almacenamiento (regiones de Europa y Asia)|5 Gbps si GRS/ZRS<sup>3</sup> está habilitado, 10 Gbps para LRS
Salida máxima<sup>2</sup> por cuenta de almacenamiento (regiones de Europa y Asia)|10 Gbps si RA-GRS/GRS/ZRS<sup>3</sup> está habilitado, 15 Gbps para LRS

<sup>1</sup>Si necesita más de 100 cuentas de almacenamiento, póngase en contacto con el [servicio de soporte técnico de Azure](https://azure.microsoft.com/support/faq/) para obtener ayuda.

<sup>2</sup>*Entrada* hace referencia a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* hace referencia a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

<sup>3</sup>Entre las opciones de replicación de Almacenamiento de Azure se incluyen:

- **RA-GRS**: almacenamiento con redundancia geográfica con acceso de lectura. Si RA-GRS está habilitada, los destinos de salida para la ubicación secundaria son idénticos a los de la ubicación principal.
- **GRS**: almacenamiento con redundancia geográfica. 
- **ZRS**: almacenamiento con redundancia de zona. Disponible solo para blobs en bloques. 
- **LRS**: almacenamiento con redundancia local. 

<!---HONumber=AcomDC_0128_2016-->