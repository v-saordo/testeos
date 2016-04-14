=======
Entidad|Oferta (estándar)
---|---
Cuentas de la base de datos*|5
Número de bases de datos por cuenta de base de datos|100
Número de usuarios por cuenta de base de datos en todas las bases de datos|500\.000
Número de permisos por cuenta de base de datos en todas las bases de datos|2\.000.000
Almacenamiento anexo por cuenta de base de datos (característica de vista previa)|2 GB
Máximo de unidades de solicitud/segundos por colección|2\.500
Número de procedimientos almacenados, desencadenadores y UDF por colección* |25 cada uno
Tiempo de ejecución máximo para el procedimiento almacenado y desencadenador|5 segundos
Almacenamiento de documentos/colección|10 GB
Colecciones máxima por cuenta de base de datos *|100
Almacenamiento de documentos máximo por base de datos (100 colecciones)* |1 TB
Longitud máxima de la propiedad de identificador|255 caracteres
Elementos máximos por página|Sin límite práctico
Tamaño de solicitud máximo del documento y el adjunto |512KB
Tamaño máximo del procedimiento almacenado, desencadenador y UDF|100 KB
Tamaño máximo de respuesta|1MB
String|Todas las cadenas se deben ajustar a la codificación UTF-8. Dado que UTF-8 es una codificación de ancho variable, los tamaños de las cadenas se determinan mediante los bytes UTF-8.
Longitud máxima de propiedad o valor|Sin límite práctico
Número máximo de UDF por consulta* |2
Número máximo de JOIN por consulta* |5
Número máximo de cláusulas AND por consulta* |20 |
Número máximo de cláusulas OR por consulta* |20 |
Número máximo de valores por expresión IN* |200
Número máximo de puntos de un argumento de polígono de una consulta ST\_WITHINP* |16
Número máximo de creaciones de colección por minuto* |5
Número máximo de operaciones de escala por minuto* |5

Las cuotas que aparecen con un asterisco (*) [se pueden ajustar poniéndose en contacto con el soporte técnico de Azure](../articles/documentdb/documentdb-increase-limits.md).

<!---HONumber=AcomDC_0302_2016-->