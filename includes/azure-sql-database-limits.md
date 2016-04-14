Recurso|Límite predeterminado
---|---
Tamaño de base de datos|Depende del nivel de rendimiento<sup>1</sup>
Inicios de sesión|Depende del nivel de rendimiento<sup>1</sup>
Uso de la memoria|16 MB de concesión de memoria durante más de 20 segundos
Sesiones|Depende del nivel de rendimiento<sup>1</sup>
Tamaño de tempdb|5 GB
Duración de la transacción|24 horas<sup>2</sup>
Bloqueos por transacción|1 millón
Tamaño de cada transacción|2 GB
Porcentaje de espacio del registro total usado por transacción|20 %
Número máximo de solicitudes simultáneas (subprocesos de trabajo)|Depende del nivel de rendimiento<sup>1</sup>


<sup>1</sup>La Base de datos SQL tiene niveles de rendimiento Básico, Estándar y Premium. Estándar y Premium se subdividen a su vez en más niveles de rendimiento. Para ver los límites detallados por nivel y nivel de servicio, consulta el tema [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Si la transacción bloquea un recurso necesario para una tarea del sistema subyacente, la duración máxima es de 20 segundos.

<!---HONumber=August15_HO7-->