<properties 
	pageTitle="Configuración y uso de la API de recomendaciones de Aprendizaje automático | Microsoft Azure" 
	description="Preguntas frecuentes de la API de RECOMENDACIONES de Microsoft creada con el aprendizaje automático de Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="luisca"/>

#Preguntas más frecuentes (P+F) sobre cómo configurar y utilizar la API de recomendaciones de Aprendizaje automático


**¿Qué son las recomendaciones?**

Para las organizaciones y empresas que dependen de las recomendaciones para la venta cruzada y la venta adicional de productos a sus clientes, las recomendaciones de Aprendizaje automático de Azure les ofrecen un motor de recomendaciones de autoservicio. Es una implementación del filtrado de colaboración que usa la factorización matricial como algoritmo básico. Los desarrolladores de aplicaciones pueden tener acceso a recomendaciones mediante las API de REST.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**¿Qué puedo hacer con las recomendaciones?**

Las recomendaciones toman como entrada un elemento o un conjunto de elementos y devuelven una lista de recomendaciones pertinentes. Por ejemplo, un cliente de un minorista en línea hace clic en un producto. El minorista en línea envía ese producto como entrada a las recomendaciones, recibe una lista de productos a cambio y decide cuál de estos productos se mostrará al cliente. Puede que desee utilizar las recomendaciones para optimizar la tienda en línea o incluso para informar al departamento de ventas interno o centro de llamadas.

**¿Hay alguna limitación de uso?**

Las recomendaciones tienen las siguientes limitaciones de uso: * Número máximo de modelos por suscripción: 10 * Número máximo de elementos que puede albergar un catálogo: 100.000 * El número máximo de puntos de uso que se guardan es de 5.000.000 aproximadamente. El más antiguo se eliminará si se cargan o notifican nuevos. * El tamaño máximo de los datos que se pueden enviar por correo electrónico (por ejemplo, importar datos de catálogo o importar datos de uso) es de 200 MB * El número de transacciones por segundo (TPS) para una compilación de modelo de recomendaciones que no esté activa es de 2 TPS aproximadamente. Una compilación de modelo de recomendaciones que esté activa puede contener hasta 20 TPS.

##Compra y facturación 


**¿Cuánto cuestan las recomendaciones durante el período de lanzamiento?**

Las recomendaciones son un servicio basado en suscripción. El pago se realiza en función del volumen de transacciones al mes. Puede consultar la [página de ofertas](https://datamarket.azure.com/dataset/amla/recommendations) de Microsoft Azure Marketplace para obtener más información sobre los precios.

**¿Hay algún coste asociado al seguimiento de las recomendaciones o al almacenamiento de la actividad del usuario?**

No en este momento.

**¿Tienen las recomendaciones una prueba gratuita?**

Hay una prueba gratuita que se limita a 10.000 transacciones al mes.

**¿Cuánto se me facturará por las recomendaciones?**

Una suscripción de pago es cualquier suscripción para la que hay que pagar una cuota mensual. Al comprar una suscripción de pago, se le cobrará inmediatamente por el uso del primer mes. Se le cobrará la cantidad asociada a la oferta en la página de suscripción (además de los impuestos correspondientes). Este cargo mensual se realiza cada mes en la misma fecha que la compra original hasta que se cancela la suscripción.

**¿Cómo actualizo a un servicio de nivel superior?**

Puede comprar o actualizar su suscripción desde la [página de oferta](https://datamarket.azure.com/dataset/amla/recommendations) de Microsoft Azure Marketplace.

Al actualizar una suscripción:

* Las transacciones que quedan en la suscripción anterior no se agregan a la nueva. 
* Pagará el precio completo de la nueva suscripción, aunque tenga transacciones no utilizadas en su suscripción anterior.

Proceso para actualizar una suscripción:

* Acceda a la [página de oferta](https://datamarket.azure.com/dataset/amla/recommendations).
* Inicie sesión en Marketplace si no la ha iniciado todavía.
* En el panel derecho, se muestran todos los planes disponibles. Haga clic en el botón de opción del plan al que desee actualizar.
* Si desea actualizar, haga clic en **Aceptar**. Si no desea actualizar, haga clic en **Cancelar**.

**Importante**: lea detenidamente el cuadro de diálogo antes de actualizar, ya que hay información sobre facturación y uso.

**¿Cuándo finalizará mi suscripción a las recomendaciones?**

La suscripción finalizará cuando la cancele. Si desea cancelar las suscripciones, consulte las instrucciones siguientes.

**¿Cómo cancelo mi suscripción a las recomendaciones?**

Para cancelar la suscripción, siga estos pasos. Si su suscripción actual es una suscripción de pago, la suscripción seguirá en vigor hasta el final del período de facturación actual. Si necesita que la cancelación surta efecto inmediatamente, póngase en contacto con nosotros a través del [Soporte técnico de Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Nota** No hay ningún reembolso si cancela antes del final del período de facturación. Tampoco se reembolsan las transacciones no utilizadas en un período de facturación.

* Acceda a la [página de oferta](https://datamarket.azure.com/dataset/amla/recommendations).
* Inicie sesión en Marketplace si no la ha iniciado todavía.
* Haga clic en **Cancelar** a la derecha del nombre y el estado del conjunto de datos. Puede usar esta suscripción hasta que llegue al final del período de facturación actual o hasta que se alcance el límite de la transacción, lo que ocurra primero.

Si desea cancelar su suscripción inmediatamente, para poder adquirir una nueva suscripción, presente una incidencia en el [Soporte técnico de Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##Introducción a las recomendaciones

**¿Me van a servir las recomendaciones?**

Las recomendaciones de Aprendizaje automático de Azure son aconsejables para aquellas organizaciones y empresas que dependen de recomendaciones para la venta cruzada y la venta adicional de productos a sus clientes. Si tiene un sitio web orientado a clientes, personal de ventas, un equipo de ventas interno o un centro de llamadas y si, además, ofrece un catálogo con varias docenas de productos o servicios, su balance final puede beneficiarse de usar estas recomendaciones.

La experimentación con recomendaciones se ha diseñado para que sea muy sencilla. La versión actual basada en API requiere conocimientos básicos de programación. Si necesita asistencia, póngase en contacto con el proveedor que ha desarrollado su sitio web. Si cuenta con un departamento de TI o un desarrollador internos, estos deben ser capaces de aprovechar todas las ventajas que le ofrecen las recomendaciones.

**¿Cuáles son los requisitos previos para configurar las recomendaciones?**

Las recomendaciones requieren que cuente con un registro de las opciones de los usuarios con respecto a su catálogo. Si no dispone de un registro de este tipo y cuenta con un sitio web orientado al cliente, las recomendaciones pueden recopilar la actividad de los usuarios por usted.

Las recomendaciones también requieren un catálogo de sus productos o servicios. Si no tiene un catálogo, las recomendaciones podrán utilizar los datos de uso de los clientes reales y elaborar un catálogo. Un catálogo "implícito" no incluirá artículos que no estaban "notificados" como parte de transacciones de usuario.

**¿Cómo configuro las recomendaciones por primera vez?**

Después de [suscribirse](https://datamarket.azure.com/dataset/amla/recommendations) a las recomendaciones, debe usar la documentación de API que figura en la [Guía de inicio rápido sobre recomendaciones de Aprendizaje automático de Azure](machine-learning-recommendation-api-quick-start-guide.md) para configurar el servicio.

**¿Dónde puedo encontrar documentación de API?**

La documentación de API está incluida en la [Guía de inicio rápido sobre recomendaciones de Aprendizaje automático de Azure](machine-learning-recommendation-api-quick-start-guide.md).

**¿De qué opciones dispongo para cargar datos de uso y de catálogo en las recomendaciones?**

Tiene dos opciones para cargar los datos de uso y el catálogo: puede exportar estos datos desde el sistema CRM u otros registros y cargarlos en las recomendaciones. La otra opción es agregar etiquetas al sitio web que realizará el seguimiento de las actividades del usuario. Si utiliza el segundo método, los datos se almacenarán en Azure.

##Mantenimiento y soporte técnico

**¿Qué volumen pueden tener mis datos?**

Cada conjunto de datos puede contener hasta 100 000 elementos del catálogo y hasta 2048 MB de datos de uso. Además, una suscripción puede contener hasta 10 conjuntos de datos (modelos).

**¿Dónde puedo obtener soporte técnico para las recomendaciones?**

El soporte técnico está disponible en el sitio de [Soporte técnico de Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**¿Dónde puedo encontrar los términos de uso?**

[Términos de uso de la API de recomendaciones de Aprendizaje automático de Microsoft Azure](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 

<!---HONumber=AcomDC_1210_2015-->