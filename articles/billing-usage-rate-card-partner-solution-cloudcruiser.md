<properties
   pageTitle="Integración de la API de facturación de Cloud Cruiser y Microsoft Azure | Microsoft Azure"
   description="Proporciona una perspectiva exclusiva del socio de facturación de Microsoft Azure Cloud Cruiser, sobre sus experiencias de integración de las API de facturación de Azure en su producto. Esto es especialmente útil para los clientes de Azure y de Cloud Cruiser que están interesados en usar o probar Cloud Cruiser para el paquete de Microsoft Azure."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="02/19/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# Integración de Cloud Cruiser y de las API de facturación de Microsoft Azure

Este artículo describe cómo puede utilizarse la información recopilada desde las API de facturación de Microsoft Azure en Cloud Cruiser para análisis y simulación de costos de flujos de trabajo.

## API de RateCard de Azure
La API de RateCard proporciona información de tarifas de Azure. Después de autenticarse con las credenciales adecuadas, puede consultar la API para recopilar metadatos acerca de los servicios disponibles en Azure, junto con las tarifas asociadas a su identificador de oferta.

A continuación aparece una respuesta de ejemplo de la API que muestra los precios de la instancia A0 (Windows):

    {
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
		"MeterName": "Compute Hours",
		"MeterCategory": "Virtual Machines",
		"MeterSubCategory": "A0 VM (Windows)",
		"Unit": "Hours",
		"MeterRates":
		{
			"0": 0.029
		},
		"EffectiveDate": "2014-08-01T00:00:00Z",
		"IncludedQuantity": 0.0
	},

### Interfaz de Cloud Cruiser con la API de RateCard de Azure
Cloud Cruiser puede aprovechar la información de la API de RateCard de distintas maneras. En este artículo mostraremos cómo la vamos a utilizar para realizar la simulación y el análisis de costos de la carga de trabajo de IaaS.

Para demostrar este caso de uso, imagine una carga de trabajo de varias instancias que se ejecutan en el paquete de Microsoft Azure (WAP). El objetivo es simular esta misma carga de trabajo en Azure y estimar los costes de tal migración. Para crear esta simulación, hay dos tareas principales que se pueden realizar:

1. **Importación y proceso de la información de servicio recopilada de la API de RateCard**: esta tarea también se realiza en los libros, en lo que el extracto de la API de RateCard se transforma y publica en un nuevo plan de tarifas. Este nuevo plan de tarifas se utilizará en las simulaciones para realizar una estimación de los precios de Azure.

2. **Normalización de servicios de WAP y servicios de Azure para IaaS**: de forma predeterminada, los servicios de WAP se basan en recursos individuales (CPU, tamaño de memoria, tamaño de disco, etc.) mientras que los de Azure se basan en el tamaño de instancia (A0, A1, A2, etc.). Esta primera tarea puede realizarla el motor ETL de Cloud Cruiser, llamado libros, en los que estos recursos se pueden integrar en tamaños de instancia, de forma análoga a los servicios de instancia de Azure.

### Importación de datos desde la API de RateCard

Los libros de Cloud Cruiser proporcionan una forma automatizada de recopilar y procesar información de la API de RateCard. Los libros ETL (extracción, transformación y carga) le permiten configurar la colección, transformación y publicación de datos en la base de datos de Cloud Cruiser.

Cada libro puede tener una o varias colecciones. Esto permite correlacionar la información de distintas fuentes para complementar o aumentar los datos de uso. En las dos capturas de pantalla siguientes, se muestra la creación de una nueva *colección* en un libro existente y la importación de información a la *colección* de la API de RateCard:

![Ilustración 1 - Creación de una nueva colección][1]

![Ilustración 2 - Importación de datos de la nueva colección][2]

Después de importar los datos al libro, es posible crear varios pasos y procesos de transformación para modificar y modelar los datos. En este ejemplo, puesto que solo estamos interesados en la infraestructura como servicio (IaaS), podemos usar los pasos de la transformación para quitar las filas o registros innecesarios, relacionados con los servicios que no sean de IaaS.

La captura de pantalla siguiente muestra los pasos de transformación usados para procesar los datos recopilados a través de la API de RateCard:

![Ilustración 3 - Pasos de transformación para procesar los datos recopilados a través de API de RateCard][3]

### Definición de nuevos servicios y planes de tarifas

Existen diferentes formas de definir servicios en Cloud Cruiser. Una de las opciones consiste en importar los servicios de los datos de uso. Este método se utiliza normalmente cuando se trabaja con nubes públicas, en las que los servicios ya están definidos por el proveedor.

Un plan de tarifas es un conjunto de tarifas o precios que se pueden aplicar a distintos servicios, en función de las fechas de vigencia o grupo de clientes, entre otras opciones. Los planes de tarifas también pueden utilizarse en Cloud Cruiser para crear escenarios de simulación o de hipótesis, a fin de comprender cómo pueden afectar los cambios de servicios en el costo total de una carga de trabajo.

En este ejemplo, usaremos la información del servicio de la API de RateCard para definir nuevos servicios en Cloud Cruiser. Del mismo modo, podemos usar las tarifas asociadas a los servicios para crear un nuevo plan de tarifas en Cloud Cruiser.

Al final del proceso de transformación, es posible crear un nuevo paso y publicar los datos de la API de RateCard como nuevas tarifas y servicios.

![Ilustración 4 - Publicación de datos de la API de RateCard como nuevos servicios y tarifas][4]

### Comprobación de servicios y tarifas de Azure

Después de publicar los servicios y tarifas, puede comprobar la lista de servicios importados en la pestaña *Services* (Servicios) de Cloud Cruiser:

![Ilustración 5 - Comprobación de los nuevos servicios][5]

En la pestaña *Rate Plans* (Planes de tarifas) puede comprobar el nuevo plan de tarifas llamado "AzureSimulation" con las tarifas importadas de la API de RateCard.

![Ilustración 6 - Comprobación del nuevo plan de tarifas y tarifas asociadas][6]

### Normalización de servicios de WAP y de Azure

De forma predeterminada, WAP proporciona información de uso en función del uso de recursos de proceso, memoria y red. En Cloud Cruiser, puede definir los servicios basándose directamente en la asignación o el uso medido de estos recursos. Por ejemplo, puede establecer una tarifa básica para cada hora de uso de CPU o cargar los GB de memoria asignados a una instancia.

En este ejemplo, para comparar los costos entre WAP y Azure, necesitamos agregar el uso de recursos en WAP en paquetes que se pueden asignar a los servicios de Azure. Esta transformación puede implementarse fácilmente en los libros:

![Ilustración 7 - Transformación de datos WAP para normalizar los servicios][7]

El último paso en el libro es publicar los datos en la base de datos de Cloud Cruiser. Durante este paso, los datos de uso están ahora agrupados en servicios (que se asignan a los servicios de Azure) y vinculados a tarifas predeterminadas para crear los cargos.

Tras finalizar el libro, puede automatizar el procesamiento de los datos, agregando una nueva tarea en el programador y especificando la frecuencia y la hora de ejecución del libro.

![Ilustración 8 - Programación de libros][8]

### Creación de informes para análisis de simulación de costos de cargas de trabajo

Según se va recopilando el uso y se van cargando los cargos en la base de datos de Cloud Cruiser, podemos aprovechar el módulo Cloud Cruiser Insights, una herramienta de creación de informes de análisis avanzada, para crear la simulación de costos de cargas de trabajo que deseamos.

Para demostrar este escenario, hemos creado el informe siguiente:

![Comparación de costos][9]

El gráfico superior muestra una comparación de costos dividida por servicios y compara el precio de la ejecución de la carga de trabajo para cada servicio específico entre WAP (color azul oscuro) y Azure (color azul claro).

En el gráfico inferior aparecen los mismos datos pero desglosados por departamento, lo que muestra los costos de cada departamento para ejecutar su carga de trabajo en WAP y Azure, junto con la diferencia entre estos dos: barra Savings (Ahorros) (color verde).

## API de uso de Azure


### Introducción

Recientemente, Microsoft presentó la API de uso de Azure, que permite a los suscriptores recopilar datos de uso mediante programación para obtener información sobre su consumo. Es una gran noticia para los clientes de Cloud Cruiser, que pueden beneficiarse del conjunto de datos más completo disponible a través de esta API.

Cloud Cruiser puede aprovechar la integración con la API de uso de varias maneras. La granularidad (información de uso cada hora) y la información de metadatos de recursos disponible a través de la API proporciona el conjunto de datos necesario para admitir los modelos flexibles de visualización completa de los gastos o contracargo.

En este tutorial, presentamos un ejemplo de cómo Cloud Cruiser puede beneficiarse de la información de la API de uso. Más específicamente, crearemos un nuevo grupo de recursos en Azure, asociaremos etiquetas para la estructura de la cuenta y después describiremos el proceso de extracción y procesamiento de la información de las etiquetas en Cloud Cruiser.
 
El objetivo final es poder crear informes como el que aparece a continuación y ser capaces de analizar el costo y el consumo basándose en la estructura de la cuenta rellenada por las etiquetas.

![Ilustración 10 - Informe con desgloses usando etiquetas][10]

### Etiquetas de Microsoft Azure

Los datos disponibles a través de la API de uso de Azure no solo incluyen información sobre el consumo, sino también metadatos de recursos que incluyen las etiquetas asociadas a ellos. Las etiquetas proporcionan una forma sencilla de organizar los recursos, pero para que resulte efectivo, debe asegurarse de que:

- Las etiquetas se aplican correctamente a los recursos en tiempo de aprovisionamiento
- Las etiquetas se utilizan correctamente en el proceso de visualización completa de los gastos y contracargo para vincular el uso a la estructura de la cuenta de la organización.

Ambos de estos requisitos pueden ser complicados, especialmente cuando hay algún tipo de proceso manual en el aprovisionamiento o cobro. Las etiquetas mal escritas, incorrectas o, incluso, omitidas constituyen quejas comunes de los clientes cuando se usan etiquetas y estos errores puede dificultar la vida desde el punto de vista del cobro.

Con la nueva API de uso de Azure, Cloud Cruiser puede extraer información de etiquetado de recursos y, a través de una herramienta ETL muy sofisticada llamada libros, corregir estos errores habituales de etiquetado. A través de los pasos de transformación que aprovechan las expresiones regulares y la correlación de los datos, Cloud Cruiser es capaz de identificar recursos incorrectamente etiquetados y aplicar las etiquetas correctas, rellenar los huecos y garantizar la asociación correcta de los recursos con el consumidor.

En lo que al cobro se refiere, Cloud Cruiser automatiza el proceso de visualización completa de los gastos y contracargo y pueden aprovechar la información de las etiquetas para asociar el uso al consumidor adecuado (departamento, división, proyecto, etc.). Esta automatización proporciona una enorme mejora y puede garantizar un proceso de cobro coherente y auditable.
 

### Creación de un grupo de recursos con etiquetas en Microsoft Azure
El primer paso de este tutorial es crear un nuevo grupo de recursos en el Portal de Azure y después crear nuevas etiquetas para asociar a los recursos. En este ejemplo, crearemos las siguientes etiquetas: Departamento, Entorno, Propietario y Proyecto.

La captura de pantalla que aparece a continuación correspondiente al Portal de Azure muestra un grupo de recursos con las etiquetas asociadas.

![Ilustración 11 - Grupo de recursos con etiquetas asociadas en el Portal de Azure][11]

El siguiente paso es extraer la información de la API de uso en Cloud Cruiser. Actualmente, la API de uso proporciona respuestas en formato JSON. Este es un ejemplo de los datos recuperados:


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{"Microsoft.Resources":{"resourceUri":"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM","location":"eastus","tags":{"Department":"Sales","Project":"Demo Usage API","Environment":"Test","Owner":"RSE"},"additionalInfo":{"ImageType":"Canonical","ServiceType":"Standard_D1"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
	},


### Importación de datos desde la API de uso en Cloud Cruiser

Los libros de Cloud Cruiser proporcionan una forma automatizada de recopilar y procesar información de la API de uso. Un libro ETL (extracción, transformación y carga) le permite configurar la colección, transformación y publicación de datos en la base de datos de Cloud Cruiser.

Cada libro puede tener una o varias colecciones. Esto permite correlacionar la información de distintas fuentes para complementar o aumentar los datos de uso. En este ejemplo, crearemos una nueva hoja en el libro de plantilla de Azure (_UsageAPI)_ y estableceremos una nueva _colección_ para importar información desde la API de uso.

![Ilustración 3 - Datos de la API de uso importados en la hoja UsageAPI][12]

Observe que este libro ya tiene otras hojas para importar servicios desde Azure (_ImportServices_) y procesar la información de consumo de la API de facturación (_PublishData_).

Vamos a extraer y procesar la información de la API de uso en la hoja _UsageAPI_ y a correlacionar la información con los datos de consumo de la API de facturación en la hoja _PublishData_.

### Procesamiento de la información de las etiquetas de la API de uso

Después de importar los datos en el libro, vamos a crear pasos de transformación en la hoja _UsageAPI_ para procesar la información de la API. El primer paso consiste en usar un procesador "División de JSON" para extraer las etiquetas de un solo campo (como se importan desde la API) y crear nuevos campos para cada uno de ellas (Departamento, Proyecto, Propietario y Entorno).

![Ilustración 4 - Creación de nuevos campos para la información de etiqueta][13]

Tenga en cuenta que en el servicio "Redes" falta la información de las etiquetas (cuadro amarillo), pero podemos decir que este servicio es parte del mismo grupo de recursos echando un vistazo al campo _ResourceGroupName_. Puesto que tenemos las etiquetas para los otros recursos de este mismo grupo de recursos, podemos usar esta información para aplicar las etiquetas que faltan a este recurso posteriormente en el proceso.

El siguiente paso es crear una tabla de búsqueda que asocie la información de las etiquetas al campo _ResourceGroupName_. Esta tabla de búsqueda se utilizará en el paso siguiente para enriquecer los datos de consumo con información de etiqueta.

### Adición de información de etiqueta a los datos de consumo

Ahora podemos ir a la hoja _PublishData_, que procesa la información de consumo de la API de facturación y agregar los campos extraídos de las etiquetas. Este proceso se realiza examinando la tabla de búsqueda creada en el paso anterior, usando el campo _ResourceGroupName_ como la clave para las búsquedas.

![Ilustración 5 - Relleno de la estructura de la cuenta con la información de las búsquedas][14]

Observe que se han aplicado los campos de la estructura de la cuenta adecuados para el servicio "Redes", lo que corrige el problema con las etiquetas que faltan. También rellenamos los campos de la estructura de la cuenta para los recursos que no sean de nuestro grupo de recursos de destino con “Otro”, para diferenciarlos en los informes.

Ahora solo tenemos que agregar otro paso para publicar los datos de uso. Durante este paso, las tarifas adecuadas para cada servicio definido en el plan de tarifas se aplicarán a la información de uso, de manera que la cuota resultante se cargará en la base de datos.

Lo mejor de todo es que solamente tiene que realizar este proceso una vez. Cuando el libro se completa, simplemente tiene que agregarlo al programador y se ejecutará cada hora o día a la hora programada. Después, es cuestión de crear nuevos informes, o personalizar los existentes, para visualizar y analizar los datos para obtener información útil del uso de la nube.

### Pasos siguientes

+ Para obtener instrucciones detalladas sobre la creación de libros e informes de Cloud Cruiser, consulte la [documentación](http://docs.cloudcruiser.com/) en línea de Cloud Cruiser (inicio de sesión válida necesaria). Para obtener más información acerca de Cloud Cruiser, póngase en contacto con [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Consulte [Obtención de información sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md) para obtener información general sobre las API de uso de recursos de Azure y de RateCard.
+ Consulte [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (Referencia de API de REST de facturación de Azure) para obtener más información sobre ambas API, que forman parte del conjunto de API proporcionadas por Administrador de recursos de Azure.
+ Si quiere profundizar en el código de ejemplo, consulte nuestros ejemplos de código de la API de facturación de Microsoft Azure en [Ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### Más información
+ Consulte el artículo [Información general de Administrador de recursos de Azure](resource-group-overview.md) para obtener más información sobre Administrador de recursos de Azure.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Ilustración 1 - Creación de una nueva colección"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Ilustración 2 - Importación de datos de la nueva colección"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Ilustración 3 - Pasos de transformación para procesar los datos recopilados a través de API de RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Ilustración 4 - Publicación de datos de la API de RateCard como nuevos servicios y tarifas"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Ilustración 5 - Comprobación de los nuevos servicios"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Ilustración 6 - Comprobación del nuevo plan de tarifas y tarifas asociadas"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Ilustración 7 - Transformación de datos WAP para normalizar los servicios"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Ilustración 8 - Programación de libros"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Ilustración 9 - Informe de ejemplo para el escenario de comparación de costos de carga de trabajo"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Ilustración 10 - Informe con desgloses usando etiquetas"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Ilustración 11 - Grupo de recursos con etiquetas asociadas en el Portal de Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Ilustración 12 - Datos de la API de uso importados en la hoja UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Ilustración 13 - Creación de nuevos campos para la información de etiqueta"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Ilustración 14 - Relleno de la estructura de la cuenta con la información de las búsquedas"

<!---HONumber=AcomDC_0302_2016-->