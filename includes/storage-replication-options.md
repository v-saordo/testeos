Los datos de la cuenta de almacenamiento se replican para garantizar la durabilidad y una alta disponibilidad y deben cumplir los [contratos de nivel de servicio para almacenamiento de Azure](/es-es/support/legal/sla/), incluso en caso de errores transitorios del hardware. Almacenamiento de Azure se ha implementado en 15 regiones de todo el mundo e incluye también soporte para la replicación de datos entre regiones. Tiene diversas opciones para replicar los datos en la cuenta de almacenamiento:

- El *almacenamiento con redundancia local (LRS)* mantiene tres copias de sus datos. LRS se replica tres veces dentro de una única instalación de una sola región. LRS protege los datos frente a errores comunes del hardware, pero no frente a errores de una única instalación.

	LRS se ofrece con un descuento. Para la máxima durabilidad, es recomendable utilizar el almacenamiento con redundancia geográfica, que se describe a continuación.

- El *almacenamiento con redundancia de zona (ZRS)* mantiene tres copias de sus datos. ZRS se replica tres veces entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones, proporcionando mayor durabilidad que LRS. ZRS garantiza la durabilidad de sus datos dentro de una sola región.
 
	ZRS ofrece un mayor nivel de durabilidad que LRS; sin embargo, para disfrutar de la máxima durabilidad, es recomendable utilizar el almacenamiento con redundancia geográfica, que se describe a continuación.

	> [AZURE.NOTE] ZRS solo está disponible actualmente para blobs en bloques. Tenga en cuenta que una vez que haya creado la cuenta de almacenamiento y haya seleccionado la replicación con redundancia de zona, no podrá convertirla para utilizar cualquier otro tipo de aplicación (o viceversa).

- El *almacenamiento con redundancia geográfica (ZRS)* está habilitado para su cuenta de almacenamiento de manera predeterminada cuando la crea. GRS mantiene seis copias de sus datos. Con GRS, sus datos se replican tres veces dentro de la región principal, y se replican también tres veces en una región secundaria a cientos de kilómetros de distancia de la región principal, proporcionando el nivel más alto de durabilidad. En caso de que se produzca un error en la región principal, Almacenamiento de Azure conmutará por error a la región secundaria. GRS garantiza la durabilidad de sus datos en dos regiones independientes. 

	> [AZURE.NOTE] GRS se recomienda sobre ZRS o LRS para una máxima durabilidad.

- El *almacenamiento geográficamente redundante con acceso de lectura (RA-GRS)* frece todas las ventajas del almacenamiento con redundancia geográfica indicado anteriormente pero, además, permite el acceso de lectura a los datos de la región secundaria en caso de que la primera deje de estar disponible. Para una disponibilidad y durabilidad máximas se recomienda el almacenamiento geográficamente redundante con acceso de lectura.  

Para obtener más detalles acerca de las opciones de replicación, consulte el [Blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) y [Opciones de redundancia del Almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).
	
Las diferencias de precios entre las distintas opciones de replicación se encuentran en la página [Detalles de precios de Almacenamiento](/es-es/pricing/details/storage/).

<!--HONumber=42-->
