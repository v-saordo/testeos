<properties
	pageTitle="Uso del almacenamiento de Azure para copias de seguridad y restauración de SQL Server | Microsoft Azure"
	description="Realizar una copia de seguridad de SQL Server y de la Base de datos SQL en el almacenamiento de Azure. Explica las ventajas de la copia de seguridad de las Bases de datos SQL en el almacenamiento de Azure y los componentes de SQL Server y almacenamiento de Azure que se necesitan"
	services="sql-database, virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor="tysonn"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="vm-windows-sql-server"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="carlrab"/>



# Uso del almacenamiento de Azure para copias de seguridad y restauración de SQL Server

## Información general

La característica que ofrece la posibilidad de escribir copias de seguridad de SQL Server en el servicio de almacenamiento de bobs de Azure se lanzó en SQL Server 2012 SP1 CU2. Puede usar esta funcionalidad para realizar la copia de seguridad en el servicio BLOB de Azure y restaurar desde él con una base de datos SQL Server local o una base de datos SQL Server de una máquina virtual de Azure. Las copias de seguridad en la nube ofrecen ventajas de disponibilidad, almacenamiento externo ilimitado replicado geográficamente y facilidad de migración de datos en la nube y desde ella. Puede emitir instrucciones BACKUP o RESTORE mediante T-SQL o SMO. Además, cuando los archivos de base de datos se almacenan en un blob de Azure y está utilizando SQL Server 2016, puede usar [la copia de seguridad archivo-instantánea](http://msdn.microsoft.com/library/mt169363.aspx) para realizar copias de seguridad casi instantáneas y restauraciones increíblemente rápidas.

## Ventajas del uso del servicio BLOB de Azure para las copias de seguridad de SQL Server

La administración del almacenamiento, el riesgo de errores en él, el acceso al almacenamiento externo y la configuración de los dispositivos son algunos de los desafíos generales de las copias de seguridad. Estos desafíos existen para instancias de base de datos locales e instancias de base de datos de la máquina virtual de Azure. A continuación se incluyen algunas de las ventajas principales de usar el servicio de almacenamiento BLOB de Azure para las copias de seguridad de SQL Server:

* Almacenamiento externo flexible, fiable y sin límites: almacenar las copias de seguridad en blobs de Azure puede ser una opción práctica, flexible y que ofrece un fácil acceso externo. Crear almacenamiento externo para sus copias de seguridad de SQL Server puede ser tan fácil como modificar sus scripts o trabajos existentes para usar la sintaxis **BACKUP TO URL**. El almacenamiento externo normalmente debe estar lo suficientemente alejado de la base de datos de producción como para impedir que un solo desastre pueda afectar tanto a las ubicaciones de la base de datos de producción como a la externa. Al elegir la [replicación geográfica de los blobs de Azure](../storage/storage-redundancy.md), tiene una capa adicional de protección en caso de que se produzca un desastre que pudiera afectar a toda la región. 
* Archivo de copia de seguridad: el servicio de almacenamiento Blob de Azure ofrece una alternativa mejor a las opciones de cinta que se usan normalmente para archivar las copias de seguridad. El almacenamiento en cintas puede requerir transporte físico a unas instalaciones externas y medidas para proteger los soportes. Almacenar las copias de seguridad en el almacenamiento de blobs de Azure ofrece una opción de archivado instantánea, altamente disponible y resistente.
* Sin sobrecarga por administrar el hardware: con los servicios de Azure no hay sobrecarga por la administración del hardware. Los servicios de Azure administran el hardware y ofrecen replicación geográfica para conseguir redundancia y protección contra los errores del hardware.
* Actualmente, para las instancias de SQL Server que se ejecutan en una máquina virtual de Azure, es posible realizar una copia de seguridad en los servicios de almacenamiento de blobs de Azure mediante el acoplamiento de discos. No obstante, hay un límite en el número de discos que se pueden acoplar a una máquina virtual de Azure para copias de seguridad. Este límite es de 16 discos para una instancia muy grande e inferior para las instancias menores. Al habilitar una copia de seguridad directa a blobs de Azure, tendrá acceso a un almacenamiento prácticamente ilimitado.
* Las copias de seguridad almacenadas en blobs de Azure están disponibles desde cualquier lugar y en cualquier momento, y es fácil tener acceso a ellas para restauraciones en un servidor SQL Server local o en otro servidor SQL Server que se ejecute en una máquina virtual de Azure sin necesidad de acoplar/desacoplar la base de datos o descargar y acoplar el VHD.
* Ventajas en los costes: pague solo por el servicio que use. Puede ser tan rentable como una opción de archivado de copias de seguridad externa. Consulte la [Calculadora de precios de Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de precios") y el [artículo sobre precios de Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Precios de los artículos") para obtener más información.
* Aproveche las instantáneas de almacenamiento: cuando los archivos de base de datos se almacenan en un blob de Azure y está utilizando SQL Server 2016, puede usar [la copia de seguridad archivo-instantánea](http://msdn.microsoft.com/library/mt169363.aspx) para realizar copias de seguridad casi instantáneas y restauraciones increíblemente rápidas.

Para obtener más información, consulte [Copia de seguridad y restauración de SQL Server con el servicio de almacenamiento Blob de Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Las dos secciones siguientes presentan el servicio de almacenamiento de blobs de Azure y los componentes de SQL Server que se usan para realizar la copia de seguridad en el servicio de almacenamiento de blobs de Azure o desde él. A tal efecto, es importante conocer los componentes y la interacción que se produce entre ellos.

El primer paso de este proceso es crear una cuenta de Azure. Para ver un tutorial completo sobre cómo crear una cuenta de almacenamiento y realizar una restauración sencilla con SQL Server 2014, consulte [Introducción al servicio de almacenamiento de Azure para copia de seguridad y restauración de SQL Server](https://msdn.microsoft.com/library/jj720558(v=sql.120).aspx). Para ver un tutorial completo sobre la creación de una cuenta de almacenamiento y la realización de una restauración simple mediante SQL Server 2014, consulte [Tutorial: uso del servicio de almacenamiento de blobs de Microsoft Azure con bases de datos de SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## Componentes del servicio de almacenamiento de blobs de Azure

* Cuenta de almacenamiento: la cuenta de almacenamiento es el punto de partida de todos los servicios de almacenamiento. Para obtener acceso a un servicio de almacenamiento de blobs de Azure, primero debe crear una cuenta de almacenamiento de Azure. Para obtener más información acerca del servicio de almacenamiento de blobs de Azure, consulte [Uso del servicio de almacenamiento de blobs de Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)

* Contenedor: un contenedor ofrece la agrupación de un conjunto de blobs y puede almacenar un número ilimitado de ellos. Para realizar una copia de seguridad de SQL Server en un servicio BLOB de Azure, debe haber creado un contenedor raíz como mínimo.

* Blob: archivo de cualquier tipo y tamaño. Los blobs se pueden dirigir usando el siguiente formato de URL: `https://<storage account>.blob.core.windows.net/<container>/<blob>` Para obtener más información acerca de los blobs en páginas, consulte [Introducción a los blobs en bloques y a los blobs en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).

## Componentes de SQL Server

* URL: una URL especifica el Identificador uniforme de recursos (URI) para un único archivo de copia de seguridad. La URL se usa para proporcionar la ubicación y el nombre del archivo de la copia de seguridad de SQL Server. La URL debe apuntar a un blob real, no solo a un contenedor. Si el blob no existe, se crea. Si se especifica un blob que ya existe, BACKUP provoca un error, a menos que se especifique la opción > WITH FORMAT. A continuación, encontrará un ejemplo de la URL que debería especificar en el comando BACKUP: ****`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Nota:</b> no es necesario usar HTTPS, pero sí recomendable. <b>Importante</b> Si elige copiar y cargar un archivo de copia de seguridad en un servicio de almacenamiento de blobs de Azure, tiene que usar un tipo de blob en páginas como opción de almacenamiento si tiene pensado usar este archivo para las operaciones de restauración. RESTORE desde un tipo de blob en bloques provocará un error.

* Credencial: la información necesaria para conectarse y autenticarse en un servicio de almacenamiento de blobs de Azure se almacena como credencial. Para que SQL Server escriba copias de seguridad en un blob de Azure o realice la restauración desde él es preciso crear una credencial de SQL Server. Para obtener más información, vea [Credencial de SQL Server](https://msdn.microsoft.com/library/ms189522.aspx).

## Copia de seguridad y restauración de bases de datos SQL Server con los blobs de Azure: conceptos y tareas.

**Conceptos, consideraciones y ejemplos de código:**

[Copia de seguridad y restauración de SQL Server con el servicio de almacenamiento Blob de Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Tutorial introductorio:**

[Tutorial: uso del servicio de almacenamiento de blobs de Microsoft Azure con bases de datos de SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

**Prácticas recomendadas y solución de problemas:**

[Prácticas recomendadas de copia de seguridad y restauración (servicio de almacenamiento de blobs de Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)

<!---HONumber=AcomDC_0218_2016-->