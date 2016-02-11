<properties 	
	pageTitle="Factoría de datos de Azure: ejemplos" 
	description="Proporciona detalles sobre ejemplos que se distribuyen con el servicio Factoría de datos de Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Factoría de datos de Azure: ejemplos

## Ejemplos en el Portal de Azure clásico
Puede implementar, revisar y probar rápidamente un ejemplo de Factoría de datos de Azure con la hoja **Canalizaciones de ejemplo** en el Portal de Azure clásico.

1. Cree una factoría de datos nueva o abra una ya existente. Consulte [Introducción a Factoría de datos de Azure][data-factory-get-started] para ver los pasos crear una factoría de datos.
2. En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en el icono **Canalizaciones de ejemplo**.

	![Icono Canalizaciones de ejemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. En la hoja **Canalizaciones de ejemplo**, haga clic en el **ejemplo** que quiere implementar.
	
	![Hoja Canalizaciones de ejemplo](./media/data-factory-samples/SampleTile.png)

3. Especifique las opciones de configuración para el ejemplo. Por ejemplo, el nombre de la cuenta de almacenamiento de Azure y la clave de la cuenta, el nombre del servidor de SQL Azure, la base de datos, el id. de usuario y la contraseña, etc.

	![Hoja de ejemplo](./media/data-factory-samples/SampleBlade.png)

4. Cuando haya terminado de especificar las opciones de configuración, haga clic en **Crear** para crear o implementar las canalizaciones de ejemplo y los servicios o las tablas vinculados que usan las canalizaciones.
5. Verá el estado de implementación en el icono de ejemplo en el que hizo clic anteriormente en la hoja **Canalizaciones de ejemplo canalizaciones**.

	![Estado de la implementación](./media/data-factory-samples/DeploymentStatus.png)

6. Cuando vea el mensaje **La implementación se realizó correctamente** en el icono del ejemplo, cierre la hoja **Canalizaciones de ejemplo**.
5. En la hoja **FACTORÍA DE DATOS**, verá que los servicios vinculados, los conjuntos de datos y las canalizaciones se han agregado a la factoría de datos.  

	![Hoja de la Factoría de datos](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

La tabla siguiente incluye una descripción breve de los ejemplos disponibles en el icono **Canalizaciones de ejemplo**.

Nombre del ejemplo | description
----------- | -----------
Generación de perfiles de clientes de juegos | Contoso es una empresa de juegos que crea juegos para varias plataformas: consolas de juegos, dispositivos portátiles y PC. Cada uno de estos juegos produce miles de registros. El objetivo de Contoso es recopilar y analizar los registros generados por estos juegos para obtener información de uso, identificar las oportunidades de venta mejorada y venta cruzada, desarrollar características nuevas y atractivas, etc. para mejorar el negocio y ofrecer una experiencia mejor a los clientes. En este ejemplo se recopilan registros de ejemplo que se procesan y se enriquecen con datos de referencia, y se transforman los datos para evaluar la eficacia de una campaña de marketing que Contoso ha lanzado recientemente.
 
## Ejemplos en GitHub
El [repositorio de GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contiene varios ejemplos que le ayudarán a arrancar rápidamente el servicio Factoría de datos de Azure (o) modificar los scripts y usarlos en su propia aplicación. La carpeta Samples\\JSON contiene fragmentos de JSON para escenarios comunes.


[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=AcomDC_0128_2016-->