<properties
	pageTitle="P+F sobre bases de datos MySql ClearDB con el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Respuestas a preguntas habituales acerca del uso de las bases de datos MySQL ClearDB con el Servicio de aplicaciones de Azure."
	documentationCenter="php"
	services=""
	authors="sunbuild"
	manager="yochayk"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sumuth"/>

# P+F sobre bases de datos MySql ClearDB con el Servicio de aplicaciones de Azure

Estas P+F responden a preguntas comunes sobre el uso y la adquisición de bases de datos MySQL ClearDB para aplicaciones web de Azure.

## ¿Qué opciones tengo para MySQL en Azure?

Tiene varias opciones:

* [Base de datos MySQL ClearDB compartida](/marketplace/partners/cleardb/databases/)

* [Clústeres de MySQL ClearDB Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Clúster de MySQL en ejecución en una máquina virtual de Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Instancia única de MySQL en ejecución en una máquina virtual de Azure](virtual-machines/virtual-machines-mysql-windows-server-2008r2.md)

ClearDB es un servicio de hospedaje de MySQL que administra la infraestructura de MySQL en lugar del usuario. Cuando ejecute su propio clúster de MySQL o una base de datos en una máquina virtual de Azure, tendrá que configurar el servidor MySQL y mantenerlo actualizado con las revisiones.

## ¿Necesito una tarjeta de crédito para la aplicación web + plantilla MySQL en Azure Marketplace?

Depende del tipo de suscripción que esté utilizando. Estos son algunos tipos de suscripción de uso frecuente:

* [De pago por uso](/offers/ms-azr-0003p/): Requiere una tarjeta de crédito. Al adquirir una base de datos MySQL de pago, se cargará el importe correspondiente en su tarjeta de crédito.

* [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/): Incluye créditos para usarlos con los servicios de Microsoft Azure pero no permite la adquisición de recursos de terceros. Para adquirir servicios de terceros o una base de datos MySQL de pago, hay que usar una suscripción con tarjeta de crédito habilitada. Para las aplicaciones web, puede crear una base de datos MySQL ClearDB gratis.

* [Suscripción a MSDN](/pricing/member-offers/msdn-benefits/) y **pruebas de desarrollo MSDN de pago por uso**: Al igual que la versión de prueba gratuita, una suscripción a MSDN requiere una tarjeta de crédito para adquirir una solución de MySQL de pago de ClearDB.

* [Contrato Enterprise (EA)](/pricing/enterprise-agreement/): Los clientes de EA reciben su facturación de EA cada trimestre para todas sus compras en Azure Marketplace (terceros) mediante una factura independiente y consolidada. Se le facturará fuera el compromiso monetario para las compras en Marketplace. Tenga en cuenta que, en este momento, la tienda de Azure no está disponible para los clientes inscritos en Azerbaiyán, Croacia, Noruega y Puerto Rico.

*   [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): Puede crear únicamente bases de datos ClearDB gratis para las aplicaciones web. No hay ningún límite en el número de bases de datos MySQL ClearDB gratuitas que se pueden crear. Tenga en cuenta que las bases de datos gratuitas no se pueden usar para las aplicaciones web de producción, ya que este servicio está destinado solo a fines de evaluación.

## ¿Por qué se me ha cobrado 3,50 USD por una aplicación web + MySQL desde Azure Marketplace?

La opción de base de datos predeterminada es Titan, que cuesta 3,50 USD. El costo no aparece durante la creación de la base de datos y, por error, puede adquirir una base de datos que no deseaba. Estamos intentando encontrar una manera de mejorar la experiencia pero, hasta entonces, debe comprobar todos los planes de tarifa seleccionados para la aplicación web y la base de datos antes de hacer clic en **Crear** e iniciar la implementación de los recursos.

## Estoy ejecutando MySQL en mi propia máquina virtual de Azure. ¿Puedo conectar mi aplicación web de Azure a la base de datos?

Sí. Puede conectar la aplicación web a la base de datos siempre y cuando a la máquina virtual de Azure se le haya dado acceso remoto a la aplicación web. Para más información, consulte [Instalación de MySQL en una máquina virtual creada con el modelo de implementación clásico que disponga de Windows Server 2012 R2](../virtual-machines/virtual-machines-mysql-windows-server-2008r2.md).

## ¿En qué países se pueden usar los clústeres de MySQL Premium de ClearDB?

Los [clústeres MySQL ClearDB Premium](/marketplace/partners/cleardb-clusters/cluster/) están disponibles en todas las regiones de Azure de todo el mundo, excepto en la India, Australia, Sur de Brasil y China.

## ¿Puedo crear un nuevo clúster antes de crear una base de datos con la solución de clúster ClearDB Premium?

No, no se pueden crear clústeres de ClearDB vacíos. El Portal de Azure permite crear bases de datos en un clúster, por lo que puede crear un nuevo clúster al mismo tiempo.

## ¿Se me avisará si intento eliminar una base de datos MySQL ClearDB que esté utilizando una de mis aplicaciones?

No, Azure no avisa si se elimina una compra de Marketplace de la que dependa una aplicación.

## ¿En qué regiones puedo crear bases de datos ClearDB?

Azure Marketplace no está disponible para los clientes inscritos en Azerbaiyán, Croacia, Noruega y Puerto Rico. ClearDB no está disponible en estas regiones.

## ¿Qué plan de tarifa debo elegir para una base de datos y una aplicación web de producción?

Use el plan de tarifa Básico o uno de nivel superior para las aplicaciones web. Para ClearDB, se recomienda un plan Júpiter o Saturno. Revise las características y limitaciones de cada plan de tarifa para las [aplicaciones web](/pricing/details/app-service/) y las [bases de datos MySQL ClearDB](/marketplace/partners/cleardb/databases/) con objeto de elegir el que se adapte a sus necesidades.

## ¿Cómo actualizo mi base de datos ClearDB de un plan a otro?

Puede usar el [Asistente para actualización de ClearDB](https://www.cleardb.com/store/azure/upgrade). Actualmente, no tenemos una ruta de actualización en el Portal de Azure.

## ¿Con quién debo ponerme en contacto para obtener soporte técnico cuando tenga problemas con la base de datos?

Si tiene algún problema relacionado con la base de datos, póngase en contacto con el [soporte técnico de ClearDB](https://www.cleardb.com/developers/help/support). Tenga a mano la información de suscripción de Azure para proporcionarla.

## ¿Puedo crear usuarios adicionales para la solución de clúster de base de datos MySQL ClearDB?  

No. No se pueden crear usuarios adicionales, pero puede crear bases de datos adicionales en el clúster de base de datos de ClearDB.

## Al migrar mis recursos de una suscripción a otra, ¿se migra también la base de datos MySQL ClearDB?  

Al realizar una migración de recursos entre suscripciones, se aplican algunas [limitaciones](app-service-move-resources.md). Una base de datos MySQL ClearDB es un servicio de terceros, de ahí que no se migre durante la migración de la suscripción de Azure. Si no administra la migración de su base de datos de MySQL antes de migrar recursos de Azure, se pueden deshabilitar las bases de datos MySQL ClearDB. En primer lugar migre manualmente las bases de datos y luego realice la migración de la suscripción de Azure de la aplicación web.

## ¿Puedo adquirir WordPress escalable con una suscripción de contrato Enterprise (EA)?

El proceso es el mismo para cualquier suscripción. Vaya a Azure Marketplace en el [Portal de Azure](https://portal.azure.com/) y elija [WordPress escalable](https://portal.azure.com/?feature.customportal=false#create/WordPress.ScalableWordPress) para comenzar a crear la aplicación. WordPress escalable solo admite los planes de tarifa Saturno y Júpiter de ClearDB. Los créditos de EA se usarán en las aplicaciones web que se ejecutan en el plan de tarifa de las aplicaciones web estándar y la base de datos MySQL (compartida) de ClearDB de pago.[/marketplace/p+f/](/marketplace/faq/) Recibirá su facturación de EA cada trimestre para todas las compras en la tienda mediante una factura independiente y consolidada.

## ¿Puedo transferir una base de datos ClearDB desde una suscripción de tarjeta de crédito hasta una suscripción de EA?

Las bases de datos existentes de ClearDB utilizarán la tarjeta de crédito asociada a las suscripciones existentes. Para usar una suscripción de EA, deberá migrar los datos a una base de datos nueva:

* Adquiera una nueva base de datos de ClearDB con su suscripción de EA.
* Migre los datos a la base de datos nueva.
* Actualice la aplicación para que use la nueva base de datos.
* Elimine la base de datos antigua de ClearDB.

Al crear una nueva aplicación web con MySQL (ClearDB) o al crear una base de datos de MySQL (ClearDB), la suscripción que elija determinará cómo se paga por el servicio. Tenga en cuenta que con una suscripción de EA, no se bloqueará la adquisición de servicios de terceros como ClearDB en el Portal de Azure. Las suscripciones de EA se facturan fuera del compromiso monetario y se facturan de forma trimestral a pago vencido. Los clientes de EA tendrían que establecer un método de pago, como una tarjeta de crédito, para pagar los servicios de Marketplace de terceros.

## ¿Dónde puedo ver los cargos para los recursos de ClearDB en una suscripción de EA?

Para los clientes directos de EA, los gastos de Azure Marketplace están visibles en Enterprise Portal. Tenga en cuenta que todas las compras y los consumos de Marketplace se facturan fuera del compromiso monetario y se facturan trimestralmente a pago vencido. Los clientes de EA tienen que pagar directamente a los proveedores de servicios de terceros. Para ello, pueden habilitar un método de pago, como una tarjeta de crédito, con su cuenta de EA.

Los clientes indirectos de EA pueden encontrar sus suscripciones de Azure Marketplace en la página **Administrar suscripciones** de Enterprise Portal, pero no se muestran los precios. Los clientes deben ponerse en contacto con su LSP para obtener información sobre los gastos en Marketplace.

El acceso a Azure Marketplace para los servicios de terceros puede administrarse mediante los administradores de inscripción de EA Azure. Estos pueden deshabilitar o volver a habilitar el acceso a las compras de terceros desde la Tienda en Administrar cuentas y Administrar suscripciones, en la sección Cuentas en Enterprise Portal.

## ¿Con quién puedo ponerme en contacto si tengo dudas sobre la factura de los servicios de ClearDB en mi suscripción EA?

Póngase en contacto con el servicio de [atención al cliente de Enterprise](http://aka.ms/AzureEntSupport) para resolver las dudas sobre la facturación de su inscripción EA. El equipo de soporte técnico del Portal EA responderá a sus preguntas o resolverá el problema.

## Más información

[P+F sobre Azure Marketplace](/marketplace/faq/)

<!---HONumber=AcomDC_0218_2016-->