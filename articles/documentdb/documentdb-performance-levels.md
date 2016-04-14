<properties 
	pageTitle="Niveles de rendimiento en DocumentDB | Microsoft Azure" 
	description="Obtenga información sobre cómo los niveles de rendimiento de DocumentDB le permiten reservar capacidad de proceso para cada colección." 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016" 
	ms.author="johnmac"/>

#Niveles de rendimiento en DocumentDB

Este artículo proporciona información general sobre los niveles de rendimiento en [DocumentDB de Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

Después de leer este artículo, podrá responder a las preguntas siguientes:

-	¿Qué es un nivel de rendimiento?
-	¿Cómo se reserva la capacidad de proceso para una cuenta de base de datos?
-	¿Cómo se trabaja con los niveles de rendimiento?
-	¿Cómo se me facturará por los niveles de rendimiento?

##Introducción a los niveles de rendimiento

Cada colección de DocumentDB creada con una cuenta estándar se aprovisiona con un nivel de rendimiento asociado. Los niveles de rendimiento se designan como S1, S2 o S3, de menor a mayor rendimiento. El nivel de rendimiento de la colección determina la cantidad de recursos de procesamiento de solicitudes reservados para la aplicación. Cada colección incluida en una base de datos puede tener un nivel de rendimiento diferente que permite asignar una mayor capacidad de proceso a las colecciones de acceso frecuente y menos capacidad de proceso a las colecciones que a las que se accede con menos frecuencia. El nivel de rendimiento mínimo para cualquier colección es S1.

Cada nivel de rendimiento tiene asociado un límite de velocidad de la unidad de solicitud (RU). Esta es la capacidad de proceso que se reservará para una colección en función de su nivel de rendimiento, el cual estará disponible para uso exclusivo de esa colección. Se pueden crear colecciones a través del [Portal de Microsoft Azure](https://portal.azure.com) o de cualquiera de los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Las API de DocumentDB permiten especificar el nivel de rendimiento de una colección.

Nivel de rendimiento de colección|Capacidad de proceso reservada
---|---
S1|250 RU/s
S2|1000 RU/s
S3|2500 RU/s

DocumentDB permite efectuar una amplia gama de operaciones de base de datos, por ejemplo, consultas, consultas con funciones definidas por el usuario (UDF), procedimientos almacenados y desencadenadores. El coste de procesamiento asociado a cada una de estas operaciones variará en función de la CPU, las E/S y la memoria que se necesite para completar la operación. En lugar de pensar en los recursos de hardware y administrarlos, puede considerar que una unidad de solicitud (RU) es como una medida única para los recursos necesarios para realizar varias operaciones de base de datos y dar servicio a una solicitud de la aplicación.

> [AZURE.NOTE] Los niveles de rendimiento se miden en unidades de solicitud. Cada nivel de rendimiento tiene asociada una tasa máxima de unidades de solicitud por segundo. El nivel de rendimiento de una colección se puede ajustar a través de las API o del [Portal de Microsoft Azure](https://portal.azure.com/). Se espera que los cambios de nivel de rendimiento se completen en 3 minutos.

##Definición de los niveles de rendimiento para las colecciones
Una vez que se crea una colección, la asignación completa de RU según el nivel de rendimiento designado se reserva para la colección. Por ejemplo, si una colección se establece como S3, la colección será capaz de procesar 2.500 RU por segundo. Cada colección reserva su capacidad de proceso designada y 10 GB de almacenamiento de base de datos. El precio de la colección varía según el nivel elegido rendimiento (S1, S2 o S3). Tenga en cuenta que DocumentDB funciona según la reserva de capacidad: al crear una colección, se reserva una aplicación y se factura según el almacenamiento de base de datos y el rendimiento reservados, con independencia de la cantidad de almacenamiento y del rendimiento que se use activamente.

Una vez creadas las colecciones, puede modificar el nivel de rendimiento a través de los SDK de DocumentDB o desde el Portal de Azure clásico.

> [AZURE.IMPORTANT] Las colecciones estándares de DocumentDB se facturan por horas y cada colección que se cree se facturará por un mínimo de una hora de uso.

Si ajusta el nivel de rendimiento de una colección para un plazo de una hora, se le facturará por el nivel de rendimiento más alto que se configure durante dicha hora. Por ejemplo, si aumenta el nivel de rendimiento de una colección a las 08:53 horas, se le cobrará el nuevo nivel a partir de las 08:00. Del mismo modo, si disminuye el nivel de rendimiento a las 08:53, la nueva tarifa se aplicará a las 09:00.

Las unidades de solicitud se reservan para cada colección en función del nivel de rendimiento definido. El consumo de las unidades de solicitud se evalúa por cada segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada (o nivel de rendimiento) en su colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado para esa colección. Si su aplicación necesita un nivel mayor de capacidad de proceso, puede aumentar el nivel de rendimiento para cada colección.

> [AZURE.NOTE] Cuando la aplicación supera los niveles de rendimiento de una o varias colecciones, se limitarán las solicitudes para cada colección. Esto significa que algunas solicitudes de aplicación pueden tener éxito mientras que otras quedarán limitadas.

##Trabajo con niveles de rendimiento
Las colecciones de DocumentDB permiten particionar los datos según los patrones de consulta y los requisitos de rendimiento de la aplicación. Consulte la [documentación sobre particiones de datos](documentdb-partition-data.md) para obtener más información sobre cómo efectuar particiones de datos con DocumentDB. Con la función de indexación automática y de consultas de DocumentDB, es bastante habitual ubicar documentos heterogéneos en la misma colección. Estas son algunas de las consideraciones clave que se deben tener en cuenta a la hora de decidir si hay que utilizar colecciones independientes:

- Consultas: una colección es el ámbito para la ejecución de la consulta. Si necesita efectuar consultas en un conjunto de documentos, los patrones de lectura más eficaces se consiguen al colocar los documentos en una sola colección.
- Transacciones: una colección es el dominio de transacción para los procedimientos almacenados y los desencadenadores. Todas las transacciones tienen como ámbito una sola colección. 
- Rendimiento: una colección tiene un nivel de rendimiento asociado. Esto garantiza que cada colección tenga un rendimiento predecible mediante RU reservadas. Se pueden asignar datos a colecciones diferentes, con distintos niveles de rendimiento, en función de la frecuencia de acceso.

> [AZURE.IMPORTANT] Es importante tener en cuenta que se aplicarán tarifas estándares completas según el número de colecciones creadas por la aplicación.

Es recomendable que la aplicación haga uso de un pequeño número de colecciones a menos que tenga requisitos de almacenamiento o rendimiento elevados. Asegúrese de que ha comprendido bien patrones de aplicación para la creación de nuevas colecciones. Puede optar por reservar la creación de la colección como una acción de administración gestionada fuera de la aplicación. De forma similar, ajustar el nivel de rendimiento para una colección cambiará el precio por hora a la que se factura a la colección. Los niveles de rendimiento de la colección se deben supervisar en caso de que la aplicación los ajuste dinámicamente.

##Cambio de los niveles de rendimiento mediante el Portal de Azure clásico

El Portal de Azure clásico es una opción disponible al administrar los niveles de rendimiento de las colecciones. Siga estos pasos para cambiar el nivel de rendimiento de una colección desde el Portal de Azure clásico.

1. Vaya al [**Portal de Microsoft Azure**](https://portal.azure.com) desde el explorador.
2. Haga clic en **Examinar** en la barra de accesos directos del lado izquierdo.
3. En el concentrador **Examinar**, haga clic en **Cuentas de DocumentDB** en la etiqueta **Filtrar por**.
4. En la hoja **Cuentas de DocumentDB**, haga clic en la cuenta de DocumentDB que contiene la colección deseada.
5. En la hoja **Cuenta de DocumentDB**, desplácese hacia abajo hasta el modo **Bases de datos** y haga clic en la base de datos que contiene la colección deseada. 
6. En la hoja **Base de datos** recién abierta, desplácese hacia abajo hasta el modo **Colecciones** y seleccione la colección deseada.
7. Por último, en la hoja **Colección**, busque el icono **Nivel de precios** del modo **Uso** y haga clic en él.
8. En la hoja **Elija su nivel de precios**, haga clic en el nivel de rendimiento deseado y, a continuación, en **Seleccionar** en la parte inferior de la hoja. 

>[AZURE.NOTE] Cambiar los niveles de rendimiento de una colección puede llevar hasta 2 minutos.

![Cambio del nivel de precios][1]

##Cambio de los niveles de rendimiento mediante el SDK de .NET

Otra opción para cambiar los niveles de rendimiento de las colecciones es a través de nuestros SDK. Esta sección solo cubre el cambio del nivel de rendimiento de una colección mediante el [SDK de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), pero el proceso es similar para otros [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Si no está familiarizado con SDK. de NET, visite nuestro [tutorial de inicio](documentdb-get-started.md).

A continuación se facilita un fragmento de código para cambiar el tipo de oferta:

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
	                          .Where(r => r.ResourceLink == "collection selfLink")    
	                          .AsEnumerable()
	                          .SingleOrDefault();
	                          
	//Change the user mode to All
	offer.OfferType = "S3";
	                    
	//Now persist these changes to the database by replacing the original resource
	Offer updated = await client.ReplaceOfferAsync(offer);

Visite [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver más ejemplos y obtener más información sobre nuestros métodos de oferta:

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) 

##Pasos siguientes

Para obtener más información sobre los precios y la administración de datos con Azure DocumentDB, consulte estos recursos:
 
- [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Administración de la capacidad de DocumentDB](documentdb-manage.md) 
- [Modelado de datos en DocumentDB](documentdb-modeling-data.md)
- [Partición de datos en DocumentDB](documentdb-partition-data.md)

Para obtener más información acerca de DocumentDB, consulte la [documentación](https://azure.microsoft.com/documentation/services/documentdb/) de Azure DocumentDB.

[1]: ./media/documentdb-performance-levels/img1.png

<!---HONumber=AcomDC_0204_2016-->