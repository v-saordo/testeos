
<properties
	pageTitle="Mejora de la aplicación de API para las aplicaciones lógicas | Microsoft Azure"
	description="Cómo decorar su aplicación de API para que funcione bien con las aplicaciones lógicas"
	services="app-service\logic"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016" 
	ms.author="sameerch"/>

# Mejora de las aplicaciones de API para las aplicaciones lógicas #

En este artículo, aprenderá a configurar la definición de la API de su aplicación de API de forma que funcione bien con las aplicaciones lógicas. Esto mejorará la experiencia del usuario final para la aplicación de API cuando se utilice en el Diseñador de aplicaciones lógicas.

## Requisitos previos

Si no está familiarizado con las [aplicaciones de API](app-service-api-apps-why-best-platform.md) del el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md), le recomendamos que lea la serie formada por varias partes en [Creación de aplicaciones de API](app-service-dotnet-create-api-app.md).


## Adición de nombres para mostrar ##
El Diseñador de aplicaciones lógicas muestra los nombres de las operaciones, los campos y los parámetros de forma tal que a veces puede ser difícil leerlos, ya que se generan mediante programación. Para mejorar la legibilidad, el Diseñador de aplicaciones lógicas, si está disponible, podrá mostrar un valor de texto más legible (lo que se conoce como *nombre para mostrar*) en lugar de mostrar los nombres predeterminados de las operaciones, los campos y los parámetros. Para ello, el Diseñador de aplicaciones lógicas detecta la presencia de determinadas propiedades de los metadatos de swagger proporcionados por la aplicación de API. Las siguientes propiedades se utilizan como nombres para mostrar:

* Operaciones (acciones y desencadenadores) el valor de la propiedad **summary** si está presente; de lo contrario, el valor de la propiedad **operationId**. Tenga en cuenta que la especificación Swagger 2.0 permite hasta 120 caracteres para la propiedad **summary**.

* Parámetros (entradas) El valor de la propiedad de extensión **x-ms-summary** si está presente; en caso contrario, el valor de la propiedad **name**. La propiedad de extensión **x-ms-summary** se debe establecer dinámicamente en código. Este proceso se describe en la sección "Uso de atributos personalizados para anotar propiedades de extensión" de este tema. La propiedad **name** puede establecerse utilizando comentarios / / /. Este proceso se describe en la sección "Uso de comentarios XML en la generación de la definición de API" de este tema.

* Campos de esquema (respuestas de salida) El valor de la propiedad de extensión **x-ms-summary** si está presente; en caso contrario, el valor de la propiedad **name**. La propiedad de extensión **x-ms-summary** se debe establecer dinámicamente en código. Este proceso se describe en la sección "Uso de atributos personalizados para anotar propiedades de extensión" de este tema. La propiedad **name** puede establecerse utilizando comentarios / / /. Este proceso se describe en la sección "Uso de comentarios XML en la generación de la definición de API" de este tema.

**Nota:** se recomienda mantener la longitud de los nombres para mostrar en 30 caracteres o menos.

### Uso de comentarios XML en la generación de la definición de API

Para el desarrollo con Visual Studio, es una práctica común anotar los controladores de API mediante [comentarios XML](https://msdn.microsoft.com/library/b2s063f7.aspx). Cuando se compila con [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx), el compilador crea un archivo de documentación XML. El conjunto de herramientas de Swashbuckle incluido con el SDK de la aplicación de API puede incorporar dichos comentarios al generar los metadatos de API y se puede configurar el proyecto de API siguiendo estos pasos:

1. Abra el proyecto en Visual Studio.

2. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Propiedades**.

	![Project Properties](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. Cuando se muestren las páginas de propiedades del proyecto, realice los pasos siguientes:

	- Seleccione **Configuración** para la configuración que se aplicará. Normalmente, se seleccionan todas las configuraciones para que la configuración que especifique se aplique a las compilaciones de depuración y lanzamiento.
	
	- Seleccione la pestaña **Compilación** situada a la izquierda.
	
	- Confirme que la opción **Archivo de documentación XML** esté activada. Visual Studio proporcionará un nombre de archivo predeterminado basado en el nombre del proyecto. También puede establecer su valor en cualquier convención de nomenclatura o dejarlo tal cual.

	![Definición de la propiedad de documento XML](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. Abra el archivo *SwaggerConfig.cs* (que se encuentra en la carpeta **App\_Start** del proyecto).

5. Agregue directivas de **uso** en la parte superior del archivo *SwaggerConfig.cs* para los espacios de nombre **System** y **System.Globalization**.

		using System;
		using System.Globalization;
 
6. Busque el archivo *SwaggerConfig.cs* para efectuar una llamada a **GetXmlCommentsPath** y anular los comentarios de la línea para que se ejecute. Puesto que aún no ha implementado este método, Visual Studio subraya la llamada a **GetXmlCommentsPath** e indicar que no está definido en el contexto actual. Eso está bien. Se implementará en el paso siguiente.

7. Agregue la siguiente implementación del método **GetXmlCommentsPath** en la clase **SwaggerConfig** (definida en el archivo *SwaggerConfig.cs*). Este método simplemente devolverá el archivo de documentación XML que especificó anteriormente en la configuración del proyecto.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. Por último, especifique los comentarios XML para los métodos del controlador. Para ello, abra uno de los archivos de controlador de la aplicación de API y escriba / / / en una línea vacía anterior a un método de controlador que desee documentar. Visual Studio insertará automáticamente una sección con comentarios dentro de la cual puede especificar un resumen de método, así como parámetros y devolver información del valor.

Ahora, al compilar y publicar la aplicación de API, verá que el archivo de documentación se encuentra también en la carga y se ha cargado con el resto de la aplicación de API.

## Clasificación de las propiedades y las operaciones avanzadas

El Diseñador de aplicaciones lógicas tiene limitado el espacio de la pantalla para mostrar las operaciones, los parámetros y las propiedades. Además, una aplicación de API puede definir un conjunto extenso de propiedades y operaciones. Como resultado de mostrar tanta información en un área pequeña, es posible que el uso el diseñador resulte difícil para el usuario final.

Para mitigar este desorden, el Diseñador de aplicaciones lógicas permite agrupar operaciones y propiedades de las aplicaciones de API en categorías definidas por el usuario. Mediante el uso de una clasificación correcta de las propiedades y operaciones, una aplicación de API puede mejorar la experiencia del usuario presentando en primer lugar las propiedades y las operaciones más básicas y útiles.

Para proporcionar esta capacidad, el Diseñador de aplicaciones lógicas comprueba la presencia de una propiedad de extensión del proveedor personalizado específico en la definición de la API de swagger de la aplicación de API. Esta propiedad se denomina **x-ms-visibility** y puede adoptar los siguientes valores:

* vacío o "none" Estas propiedades y operaciones las ve fácilmente el usuario.

* "advanced" Como estas operaciones y propiedades son avanzadas, están ocultas de forma predeterminada. Sin embargo, el usuario puede tener acceso a ellas fácilmente si es necesario.

* "internal" Estas operaciones y propiedades se consideran como propiedades internas o de sistema y no están diseñadas para que las utilice directamente el usuario. Como resultado, el diseñador las oculta y están disponibles solo en la vista de código. Para estas propiedades, también puede especificar la propiedad de extensión **x-ms-scheduler-recommendation** para definir el valor mediante el Diseñador de aplicaciones lógicas. Para ver un ejemplo, consulte el artículo sobre la [incorporación de desencadenadores a una aplicación de API](app-service-api-dotnet-triggers.md).


## Uso de atributos personalizados para anotar propiedades de extensión

Como se mencionó anteriormente, se utilizan propiedades de extensión de proveedor personalizado para anotar los metadatos de la API a fin de proporcionar información más completa que puede utilizar el Diseñador de aplicaciones lógicas. Si utiliza metadatos estáticos para describir la aplicación de API, puede editar directamente */metadata/apiDefinition.swagger.json* en el proyecto para agregar manualmente las propiedades de extensión necesarias.

Para las aplicaciones de API que usan metadatos dinámicos, puede hacer uso de atributos personalizados para anotar el código. A continuación, puede definir un filtro de operación en el archivo *SwaggerConfig.cs* archivo para buscar los atributos personalizados y agregar la extensión de proveedor que proceda. Este enfoque se describe en detalle a continuación para generar dinámicamente la propiedad de extensión **x-ms-summary**.

1. Defina una clase de atributo denominada **CustomSummaryAttribute** que se usa para anotar el código.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. Defina un filtro de operación denominado **AddCustomSummaryFilter** que buscará este atributo personalizado en los parámetros de operación.


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. Edite el archivo *SwaggerConfig.cs* y agregue la clase de filtro definida anteriormente.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. Utilice la clase **CustomSummaryAttribute** para anotar el código, como se muestra en el siguiente fragmento de código.

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	Cuando se compila la aplicación de API anterior, se generan los siguientes metadatos de API:


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. De forma similar, puede definir el filtro de esquema **AddCustomSummarySchemaFilter** para realizar anotaciones automáticamente en la propiedad de extensión **x-ms-summary** para los modelos de esquema, como en el ejemplo siguiente.

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## Resumen

En este artículo, ha visto cómo mejorar la experiencia del usuario de la aplicación de API cuando se utiliza en el Diseñador de aplicaciones lógicas. Como práctica recomendada, se pueden proporcionar nombres descriptivos adecuados para todas las propiedades y los parámetros de las operaciones (acciones y desencadenadores). También se recomienda que proporcione 5 operaciones básicas como máximo. En cuanto a los parámetros de entrada, la recomendación es restringir el número de propiedades básicas a 4 como máximo. Para las propiedades, la recomendación es 5 o menos. Las demás operaciones y propiedades deberían marcarse como avanzadas.
 

<!---HONumber=AcomDC_0114_2016-->