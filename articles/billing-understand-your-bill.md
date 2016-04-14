<properties
   pageTitle="Comprender la factura de Azure"
   description="Comprender la factura de Azure"
   services=""
   documentationCenter="Azure"
   authors="erihur"
   manager="kareni"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="erihur"/>


# Comprender la factura de Microsoft Azure

Los cargos de las suscripciones de Microsoft Azure varían según el plan de tarifas. Algunos planes de tarifas, como el de los suscriptores de Visual Studio Enterprise (MPN), incluyen créditos mensuales que puede usar en cualquier servicio de Azure según sus necesidades.

Tenga en cuenta que es posible informar un uso latente de hasta 24 horas correspondiente al período de facturación anterior en el período de facturación actual.

Para obtener más información acerca del consumo y los planes de tarifas, consulte la [página Opciones de compra de Microsoft Azure](https://azure.microsoft.com/pricing/purchase-options/).

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

<!-- Below section commented out as a new "right nav" floating bar is created for each article and is no longer necessary to have this section.

## Content:

This topic helps you with the following tasks when reading your bill.

-  View or Download a Bill for Azure
-  Customer Information
-  Understand the Invoice Summary
-  Understand the Current Charges
-  Footer Information
-  Understand the Additional Information
-  Understand Detailed Usage Charges
-  Analyze Daily Usage Data -->

### Ver o descargar una factura de Microsoft Azure:

En el [portal Uso y facturación](https://account.windowsazure.com/subscriptions), puede ver la factura actual y descargar facturas anteriores.

Para ver o descargar una factura:

1. Inicie sesión en el [portal Uso y facturación](https://account.windowsazure.com/subscriptions) con su identificador de cuenta de Microsoft o su identificador de cuenta profesional o educativa.

2. Haga clic en la suscripción para la que desea consultar los detalles y el uso.

3. Haga clic en Historial de **facturación**

    ![Resumen: historial de facturación: 1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)


4. La sección **Historial de facturación** muestra los extractos correspondientes a los seis últimos períodos de facturación, además del período actual no facturado. El extracto del período actual es una estimación de los cargos en el momento en que se generó la estimación. Esta información solo se actualiza diariamente y es posible que no incluya todo el uso en el que se ha incurrido a la fecha. La factura mensual puede ser distinta a esta estimación.

    ![Resumen: historial de facturación: 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Haga clic en **Ver extracto actual** para ver una estimación de los cargos en el momento en que se generó la estimación. Esta información solo se actualiza diariamente y es posible que no incluya todo el uso en el que se ha incurrido a la fecha. La factura mensual puede ser distinta a esta estimación.

    ![Resumen: historial de facturación: 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Resumen: historial de facturación: 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Haga clic en **Descargar factura** para ver una copia de la factura anterior.

    ![Resumen: historial de facturación: 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)


***Importante:*** *Los cargos que se enumeran en las instrucciones de facturación de los clientes internacionales tienen únicamente carácter estimativo dados los diferentes tipos de conversión de los bancos.*


A continuación están disponibles en Microsoft Azure algunas instrucciones de ejemplo para dos ofertas diferentes.

 **TIPO DE OFERTA** | **DESCRIPCIÓN** | **DESCARGA** |
 :--------- |:-------- | :-------|
Pay-As-You-Go | Pagar mensualmente con retraso | [Archivo de muestra](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf)
Oferta de compromiso | Gasto deducido de su compromiso de prepago | [Archivo de muestra](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf)



## Encabezado - Información del cliente

La sección de información del cliente identifica la información pertinente respecto del uso y del perfil. ![encabezado](./media/billing-understand-your-bill/Header.png)

### Número de factura
Identificador único de factura con fines de seguimiento.

### Ciclo de facturación
El período de tiempo en que se realizó el uso.

### Fecha de la factura
Fecha en que se generó la factura.

### Método de pago
Tipo de pago que se utiliza en la cuenta (es decir, factura o tarjeta de crédito).

### Dirección de facturación
Dirección de pagos de Microsoft Azure.

### Oferta de suscripción
Tipo de oferta de suscripción adquirida (es decir, pago por uso, MSDN-Visual Studio Ultimate, etc.)

### Correo electrónico del propietario de cuenta
La dirección de correo electrónico de la cuenta con que está registrada la cuenta de Microsoft Azure.



## Comprender el resumen de factura
La sección Resumen de factura de la factura resume las transacciones desde la última factura y los cargos de uso actuales.

![resumen de factura](./media/billing-understand-your-bill/InvoiceSummary.png)

La sección Saldos, pagos y otros créditos de la factura resume las transacciones desde la última factura.

### Saldo anterior
El saldo anterior es el importe total debido en la última factura.

### Pagos
Los pagos son los pagos totales aplicados a la última factura.

### Saldo pendiente (desde el ciclo de facturación anterior)
Todos los ajustes de factura (créditos o saldos) aplicados a la cuenta desde la última factura.


## Comprender los cargos actuales
La sección Cargos actuales de la factura contiene detalles acerca de los cargos mensuales. Los vínculos están organizados en las siguientes subsecciones.

### Cargos de uso
Los cargos de uso son los cargos mensuales totales de una suscripción. Se le facturará con un mes de retraso según el uso que haya realizado el mes anterior.

### Descuentos
Los descuentos de servicio en el uso se verían reflejados en este elemento de línea y se aplican a la factura actual.

### Ajustes
Los ajustes varios son cargos pendientes o créditos varios que se aplican a la factura actual. Por ejemplo, si tiene la oferta de Visual Studio Ultimate con MSDN, podría ver un crédito mensual en este elemento de línea. Si cancela la suscripción, podría ver los cargos por el uso mensual que excedan el crédito mensual incluido en la oferta desde el comienzo del período de facturación actual hasta la fecha de cancelación de la suscripción.

## Información de pie de página
![pie de página](./media/billing-understand-your-bill/footerinformation.png)

## Comprender la información adicional
La página de información adicional le brinda referencias a otros recursos para poder comprender su factura y vínculos para ver el uso, además de otra información importante relacionada con su factura.

![información adicional](./media/billing-understand-your-bill/AdditionalInformation.png)

### Uso detallado
Un vínculo en la descripción bajo Uso detallado le dirige al portal Uso y facturación de Azure, donde puede consultar el uso detallado correspondiente a esta suscripción. Ahora hay dos versiones disponibles para descargar: **versión 1 de .csv** contiene la antigua convención de nomenclatura y campos de uso y **versión 2 de .csv** contiene los nombres descriptivos de cliente para cada una de las categorías además de campos adicionales que le ayudarán a entender qué servicios está utilizando en Microsoft Azure.

### Información adicional y recursos útiles
Esta sección cuenta con vínculos a preguntas sencillas relacionadas con los tamaños de las instancias de proceso, los cargos de Base de datos SQL y vínculos útiles para permitirle responder cualquier otra pregunta.

### Dirección de venta
Se rellena previamente con la dirección del perfil de la cuenta.

### Instrucciones de pago
En esta sección se encuentran las instrucciones de pago de dónde enviar cheques, transferencias bancarias o cheques urgentes si el método de pago es la factura.

## Comprender los cargos de uso detallados

Como parte de nuestro constante compromiso con los clientes para ayudarles a administrar fácilmente el uso de Azure, hemos mejorado el archivo de uso de descarga que informa sobre el uso de los servicios y los costos de Azure. El vínculo de descarga contiene dos versiones del archivo de uso: **Versión 1** utiliza el formato existente; **Versión 2** incluye información adicional y nombres de columna actualizados en la sección Uso diario.

Los cargos de uso son los cargos **mensuales** totales de una suscripción menos cualquier crédito o descuento. Se le facturará con un mes de retraso según el uso que haya realizado el mes anterior. La sección superior del archivo muestra los detalles de los servicios que le están facturando durante el ciclo de facturación del mes anterior. La tabla siguiente enumera los nombres de columnas para cada uno de los archivos de la versión de .csv.

**Versión 1** | **Versión 2** | **Descripción**|
:---------------| :---------------- | --------|
Período de facturación | Período de facturación | El período de facturación en que se consumió el recurso.
Nombre | Categoría de medidor | Identifica el servicio de nivel superior al que pertenece este uso.
Tipo | Subcategoría de medidor | El servicio de Azure se puede definir por tipo en esta columna, lo que puede afectar la tarifa.
Recurso | Nombre de medidor | Identifica la unidad de medida del recurso que se está utilizando.
Region | Medidor de la región | Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos.
SKU | SKU | Identifica el identificador único de sistema de cada recurso de Azure.
Unidad | Unidad | Identifica la unidad en que se cobra el servicio. Por ejemplo, GB, horas, por 10.000.
Consumida | Cantidad consumida | Contiene la cantidad consumida del recurso durante el período de facturación.
Se incluye | Cantidad incluida | Contiene la cantidad del recurso incluida sin cargo en el período de facturación actual.
Facturable | Cantidad de superávit | Si la cantidad Consumida supera la cantidad incluida, esta columna muestra la diferencia. Se le facturará esta cantidad. En el caso de ofertas de pago por uso que no incluyen cantidad en la oferta, este total será igual a la cantidad Consumida.
Dentro del compromiso | Dentro del compromiso | Contiene los cargos de recurso que se restan del importe comprometido asociado a su oferta de 6 o 12 meses. Observe que los cargos de recurso se restan del importe comprometido en orden cronológico.
Moneda | Moneda | Identifica la moneda reflejada en el período de facturación actual.
Superávit | Superávit | Contiene los cargos de recurso que superan el importe comprometido asociado a su oferta de 6 o 12 meses.
Tarifa de compromiso | Tarifa de compromiso | Contiene la tarifa de compromiso basada en el importe comprometido total asociado a su oferta de 6 o 12 meses.
Tarifa | Tarifa | Muestra la tarifa que se le cobra por unidad facturable.
Valor | Valor | Muestra el resultado de multiplicar la columna Facturable por la columna Tarifa. Si la cantidad Consumida no supera la cantidad incluida, no habrá cargo en esta columna.

## Analizar los datos de uso diario
Dependiendo del uso, puede haber miles de filas de datos de uso diario. Si desea analizar estos datos, haga clic en **Descargar uso** y elija una versión de archivo de variables separadas por comas (.csv) para ver los datos de uso diario del período de facturación adecuado. Como referencia, puede descargar un archivo .csv de muestra para cada versión mostrada a continuación.

 NOMBRE | DESCARGAR |
 :----------:| :-------: |
 Uso detallado .csv Versión 1| [Archivo de muestra](https://azurepricing.blob.core.windows.net/sampleinvoices/Micorosft_Azure_Detailed_Usage_v1.csv)
 Uso detallado .csv Versión 2 | [Archivo de muestra](https://azurepricing.blob.core.windows.net/sampleinvoices/Micorosft_Azure_Detailed_Usage_v2.csv)



![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)



En el archivo .csv, los elementos se desglosan para mostrar una lista de la cantidad de cada recurso que se consumió dentro del período de facturación actual.

![instantánea de csv](./media/billing-understand-your-bill/csvsnapshotportal.png)

Las siguientes columnas muestran detalles que afectan las tarifas al comienzo del período de facturación:

**Versión 1** | **Versión 2** | **Descripción** |
:---------------| :----------------| -----|
Fecha de uso | Fecha de uso | La fecha en que se emitió el recurso.
Nombre | Categoría de medidor | Identifica el servicio de nivel superior al que pertenece este uso.
GUID de recursos | Id. de medidor | El identificador del medidor facturado. Esto se utiliza como identificador para el uso de facturación de precios.
Tipo | Subcategoría de medidor | El servicio de Azure se puede definir por tipo en esta columna, lo que puede afectar la tarifa.
Recurso | Nombre de medidor | Identifica la unidad de medida del recurso que se está utilizando.
Region | Medidor de la región | Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos.
Unidad | Unidad | Identifica la unidad en que se cobra el servicio. Por ejemplo, GB, horas, por 10.000.
Consumida | Cantidad consumida | Contiene la cantidad del recurso consumida durante ese día.
Subregión | Ubicación del recurso | Identifica el centro de datos donde se está ejecutando el recurso.
Servicio | Servicio consumido | Esta columna se utiliza para hacer un seguimiento del servicio individual de la plataforma Azure que puede no estar específicamente identificado en la columna Nombre. Esta columna Servicio indicará el servicio específico al que pertenece el uso.
N/D | El grupos de recursos | _**Nueva adición de columna.**_ El grupo de recursos en el que se ejecuta el recurso implementado. Consulte http://azure.microsoft.com/documentation/articles/resource-group-overview/
Componente | Id. de instancia | El identificador para el recurso en ejecución. El identificador contiene el nombre especificado para el recurso cuando se creó
N/D | Etiquetas | _**Nueva adición de columna.**_ Los nuevos tipos de recursos de Azure le permiten etiquetar los recursos. Consulte http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/
Información adicional | Información adicional | Metadatos adicionales relacionados con el servicio.
Información de servicio 1 | Información de servicio 1 | Esta columna proporciona el nombre del proyecto al que pertenece el servicio de la suscripción.
Información de servicio 2 | Información de servicio 2 | Se trata de un campo heredado que captura los metadatos específicos del servicio opcional.

Además de algunos campos nuevos y cambios de nombres en la versión 2 de csv, habrá formatos estandarizados para los datos en los campos siguientes:

- **Id. de instancia**: el campo de Id. de instancia representa el identificador especificado por el usuario para el servicio de aprovisionamiento. Actualmente, existen dos formatos en los que está representado el id. de instancia: el nombre del recurso o el id. de recurso completo. Los servicios de Microsoft Azure están realizando la transición para representar el id. de instancia en un formato de Id. de recurso completo estandarizado _**(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)**_. Al realizar los servicios la transición al nuevo formato, verá el campo de datos de Id. de instancia cambiar desde el nombre del recurso al Id. de recurso. El Id. de recurso es el formato utilizado por la [API de Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx) para identificar los recursos en una suscripción.

![InstanceId](./media/billing-understand-your-bill/instanceid.png)

- **Información adicional**: la columna Información adicional en el .csv de uso especifica metadatos específicos del servicio. Por ejemplo, un tipo de imagen de una máquina virtual. Actualmente, un servicio emite metadatos específicos del servicio en varias columnas: campos Información adicional, Información de servicio 1 e Información de servicio 2. Los servicios de Microsoft Azure estandarizarán la emisión de metadatos específicos del servicio solo en la columna Información adicional. Vea la siguiente instantánea del formato estandarizado:

![additionalinfo\_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

- **Etiquetas**: esta columna contiene las etiquetas de recursos especificadas por el usuario. Las etiquetas se pueden utilizar para agrupar los registros de facturación. Por ejemplo, puede utilizar etiquetas para distribuir los costos por departamento mediante el servicio. Obtenga más información sobre el [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md). Los servicios que admiten la emisión de etiquetas son:  
    - Máquinas virtuales
    - Almacenamiento y
    - servicios de redes aprovisionados con la [API de Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![etiquetas](./media/billing-understand-your-bill/tags.png)


## Más recursos
Vaya a la sección **Administrar cuentas, suscripciones y roles administrativos**, en [Administrar los servicios](https://msdn.microsoft.com/library/azure/dn578292.aspx) para ver algunos vínculos muy útiles:

- [Administrar el método de pago](https://msdn.microsoft.com/library/azure/dn736054.aspx)

- [Editar información de pago de una tarjeta de crédito existente](https://msdn.microsoft.com/library/azure/dn736053.aspx)

- [Agregar una nueva tarjeta de crédito para usarla como método de pago](https://msdn.microsoft.com/library/azure/dn736057.aspx)

- [Cambiar la tarjeta de crédito de la cuenta de Microsoft Azure](https://msdn.microsoft.com/library/azure/dn736050.aspx)

<!-- - [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)-->



<!--Image references-->

<!---HONumber=AcomDC_0218_2016-->