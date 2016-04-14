Los datos de la cuenta de almacenamiento de Microsoft Azure siempre se replican para garantizar la durabilidad y la alta disponibilidad, y cumplen el [contrato de nivel de servicio de Almacenamiento](https://azure.microsoft.com/support/legal/sla/storage/) incluso en caso de errores de hardware transitorios. Cuando cree una cuenta de almacenamiento, debe seleccionar una de las siguientes opciones de replicación:

- **Almacenamiento con redundancia local (LRS).** El almacenamiento con redundancia local mantiene tres copias de sus datos. LRS se replica tres veces dentro de una única instalación de una sola región. LRS protege los datos frente a errores comunes del hardware, pero no frente a errores de una única instalación.  
  
	LRS se ofrece con un descuento. Para la máxima durabilidad, es recomendable utilizar el almacenamiento con redundancia geográfica, que se describe a continuación.


- **Almacenamiento con redundancia de zona (ZRS).** El almacenamiento con redundancia de zona mantiene tres copias de los datos. ZRS se replica tres veces entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones, proporcionando mayor durabilidad que LRS. ZRS garantiza la durabilidad de sus datos dentro de una sola región.

	ZRS ofrece un mayor nivel de durabilidad que LRS; sin embargo, para disfrutar de la máxima durabilidad, es recomendable usar el almacenamiento con redundancia geográfica, que se describe a continuación.

	> [AZURE.NOTE] Actualmente, el ZRS solo está disponible para los blobs en bloques y únicamente se admite en las versiones del 14/02/2014 y posteriores.
	> 
	> Una vez haya creado su cuenta de almacenamiento y seleccionado el ZRS, no es posible convertirla para usar ningún otro tipo de replicación o viceversa.

- **Almacenamiento con redundancia geográfica (GRS)**. El almacenamiento con redundancia geográfica está habilitado para su cuenta de almacenamiento de manera predeterminada cuando la crea. GRS mantiene seis copias de sus datos. Con GRS, sus datos se replican tres veces dentro de la región primaria, y se replican también tres veces en una región secundaria a cientos de kilómetros de distancia de la región primaria, proporcionando el nivel más alto de durabilidad. En caso de que se produzca un error en la región primaria, Almacenamiento de Azure conmutará por error a la región secundaria. GRS garantiza la durabilidad de sus datos en dos regiones independientes.


- **Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)**. El almacenamiento con redundancia geográfica con acceso de lectura replica sus datos a una ubicación geográfica secundaria y, además, proporciona acceso de lectura a sus datos en la ubicación secundaria. El almacenamiento con redundancia geográfica con acceso de lectura le permite tener acceso a los datos desde la ubicación principal o la secundaria, ante la eventualidad de que alguna de las ubicaciones deje de estar disponible.

	> [AZURE.IMPORTANT] Si no especificó ZRS cuando creó la cuenta, puede cambiar cómo se replican los datos después de haber creado su cuenta. Sin embargo, tenga en cuenta que cambiar de LRS a GRS o RA-GRS puede suponer un coste adicional único de transferencia de datos.
 
Consulte [Replicación de almacenamiento de Azure](../articles/storage/storage-redundancy.md) para obtener más información sobre las opciones de replicación de almacenamiento.

Para obtener información de precios para la replicación de cuentas de almacenamiento, consulte [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/).

Para obtener detalles de arquitectura sobre la durabilidad con Almacenamiento de Azure, consulte [Documento de SOSP: Almacenamiento de Azure: un servicio de almacenamiento en la nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

<!---HONumber=AcomDC_0309_2016-->