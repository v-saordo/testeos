Recurso|Límite predeterminado|Límite máximo
---|---|---
Cuentas de Servicios multimedia de Azure (AMS) en una única suscripción||25
Recursos por cuenta de AMS||1\.000.000<sup>1</sup>
Tareas encadenadas por trabajo||30
Recursos por tarea.||50
Recursos por trabajo||100
Trabajos por cuenta de AMS ||50\.000<sup>2</sup>
Localizadores únicos asociados a un recurso al mismo tiempo||5<sup>4</sup>
Canales activos por cuenta de AMS</p></td>|5</p></td>|N/A<sup>1</sup>
Programas en estado detenido por canal </p></td>|50</p></td>|N/A<sup>1</sup>
Programas en estado de ejecución por canal </p></td>|3</p></td>|3
Extremos de streaming en estado de ejecución por cuenta de ASM</p></td>|2</p></td>|N/A<sup>1</sup>
Unidades de streaming por extremo de streaming</p></td>|10 </p></td>|N/A<sup>1</sup>
Unidades de codificación por cuenta de AMS</p></td>|25</p></td>|N/A<sup>1</sup>
Cuentas de almacenamiento | |1\.000<sup>5</sup>

<sup>1</sup> Puede solicitar la actualización de los límites de esta cuota abriendo una incidencia de soporte técnico. No cree más cuentas de AMS para aumentar los límites; en su lugar, envíe una incidencia de soporte técnico.

<sup>2</sup> Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados. Puede eliminar los trabajos antiguos con **IJob.Delete** o con la solicitud HTTP **DELETE**.

<sup>3</sup> Al realizar una solicitud para obtener una lista de las entidades Job, se devolverá un máximo de 1000 por solicitud. Si necesita realizar un seguimiento de todos los trabajos enviados, puede usar top y skip, tal como se describe en [Opciones de consulta del sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Los localizadores no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM).

<sup>5</sup> Las cuentas de almacenamiento deben proceder de la misma suscripción de Azure.

<!---HONumber=AcomDC_1217_2015-->