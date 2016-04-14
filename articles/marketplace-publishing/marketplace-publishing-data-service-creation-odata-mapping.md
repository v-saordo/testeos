<properties
   pageTitle="Guía para crear un Servicio de datos en Marketplace | Microsoft Azure"
   description="Se ofrecen instrucciones detalladas sobre cómo crear, certificar e implementar un servicio de datos para la venta en Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="01/04/2016"
      ms.author="hascipio; avikova" />

# Asignación de un servicio web existente a OData a través de CSDL

Este artículo brinda información general sobre cómo usar CSDL para asignar un servicio existente a un servicio compatible de OData. Explica cómo crear el documento de asignación (CSDL) que transforma la solicitud de entrada desde el cliente a través de una llamada de servicio y el resultado (datos) de vuelta al cliente a través de una fuente compatible con OData. Microsoft Azure Marketplace expone los servicios a los usuarios finales a través del protocolo de OData. Los proveedores de contenido (propietarios de los datos) exponen los servicios de diversas formas, como REST, SOAP, etc.

## ¿Qué es un CSDL y cuál es su estructura?
Un CSDL (lenguaje de definición de esquemas conceptuales) es una especificación que define cómo describir el servicio web o el servicio de base de datos en un lenguaje XML común para Azure Marketplace.

Información general simple sobre el **flujo de solicitud:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

El **flujo de datos** va en la dirección opuesta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

La **figura 1** muestra cómo un cliente puede obtener datos de un proveedor de contenido (su servicio) a través de Azure Marketplace. El componente de asignación/transformación utiliza el CSDL para controlar el paso de la solicitud y los datos entre los servicios del proveedor de contenido y el cliente que realiza la solicitud.

*Figura 1: Flujo detallado desde el cliente que realiza la solicitud al proveedor de contenido a través de Azure Marketplace*

  ![dibujo](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Para obtener información sobre Atom, Atom Pub y el protocolo OData en que se basan las extensiones de Azure Marketplace, revise: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Extracto del vínculo anterior: *"El propósito del protocolo Open Data (en adelante, OData) es brindar un protocolo basado en REST para las operaciones de tipo CRUD (crear, leer, actualizar y eliminar) en los recursos expuestos como servicios de datos. Un "servicio de datos" es un punto de conexión donde hay datos expuestos desde una o más "colecciones", cada una de las cuales con cero o más "entradas", que consisten en pares de tipo nombre-valor. OData es publicado por Microsoft según los estándares OASIS (Organización para el fomento de los estándares de información estructurada) para que cualquier persona que así lo desee pueda crear servidores, clientes o herramientas sin regalías ni restricciones".*

### El CSDL debe definir tres partes fundamentales:

- El **punto de conexión** del proveedor de servicios: La dirección web (URI) del servicio.
- Los **parámetros de datos** transmitidos como entrada al proveedor de servicios: Las definiciones de los parámetros que se envían al servicio del proveedor de contenido al tipo de datos.
- El **esquema** de los datos que se devuelven al servicio que realiza la solicitud: El esquema de los datos que entrega el servicio del proveedor de contenido, incluido el contenedor, las colecciones/tablas, las variables/columnas y los tipos de datos.

El siguiente diagrama muestra información general del flujo desde donde el cliente ingresa la instrucción OData (llamada al servicio web del proveedor de contenido) para recuperar los resultados/datos.

  ![dibujo](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### Pasos:

1. El cliente envía la solicitud a través de una llamada de servicio completa con los parámetros de entrada definidos en XML a Azure Marketplace.
2. CSDL se utiliza para validar la llamada de servicio.
	- Azure Marketplace envía luego la llamada de servicio con formato al servicio de proveedores de contenido.
3. El servicio web ejecuta y realiza la acción del verbo Http (es decir, GET). Los datos se devuelven a Azure Marketplace donde los datos solicitados (si hay) se exponen en formato XML al cliente con la asignación definida en el CSDL.
4. El cliente recibe los datos (si hay) en formato XML o JSON.

## Definiciones

### OData ATOM Pub

Una extensión de ATOM Pub donde cada entrada representa una fila de un conjunto de resultados. Se mejora la parte de contenido de la entrada para contener los valores de la fila, como pares de clave-valor. Puede encontrar más información en este vínculo: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### CSDL: lenguaje de definición de esquemas conceptuales

Permitir definir funciones (SPROC) y entidades expuestas a través de una base de datos. Puede encontrar más información en este vínculo: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)

> [AZURE.TIP]Haga clic en el menú desplegable **Otras versiones** y seleccione una versión si no ve el artículo.

### EDM: modelo de datos de entrada
- Información general: [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink]
[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
- Vista previa: [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink]
[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
- Tipos de datos: [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink]
[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

El siguiente diagrama el flujo detallado de izquierda a derecha desde donde el cliente ingresa la instrucción OData (llamada al servicio web del proveedor de contenido) para recuperar los resultados/datos:

  ![dibujo](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## Aspectos básicos de CSDL

Un CSDL (lenguaje de definición de esquemas conceptuales) es una especificación que define cómo describir el servicio web o el servicio de base de datos en un lenguaje XML común para Azure Marketplace. CSDL describe las partes fundamentales que **hacen posible el paso de los datos desde el origen de datos a Azure Marketplace.** A continuación, se describen las partes fundamentales:

- Información de interfaz que describe todas las funciones disponibles al público (nodo FunctionImport).
- Información de tipo de datos para todas las solicitudes de mensajes (entrada) y respuestas de mensajes (salidas) (nodos EntityContainer/EntitySet/EntityType).
- Información de enlace sobre el protocolo de transporte que se usará (nodo Header).
- Información de dirección para ubicar el servicio especificado (atributo BaseURI).

En resumen, el CSDL representa un contrato independiente de la plataforma y del lenguaje entre el solicitante del servicio y el proveedor de servicios. Con el CSDL, un cliente puede ubicar un servicio web o un servicio de base de datos e invocar cualquiera de sus funciones disponibles al público.

### Relación de un CSDL con una base de datos o una colección
**La especificación de CSDL**

CSDL es la gramática de XML para describir un servicio web. La especificación misma se divide en 4 elementos importantes: EntitySet, FunctionImport NameSpace y EntityType.

Para poder comprender mejor esta abstracción, relacionemos un CSDL con una tabla.

Recuerde:

  Representa un contrato independiente de la plataforma y del lenguaje entre el **solicitante del servicio** y el **proveedor de servicios**. Con el CSDL, un **cliente** puede ubicar un **servicio web o un servicio de base de datos** e invocar cualquiera de sus **funciones** disponibles al público.

En el caso de un servicio de datos, las cuatro partes de un CSDL se pueden pensar como una base de datos, una tabla, una columna y un procedimiento almacenado.

Relacione estos elementos de la siguiente manera en un servicio de datos:

- EntityContainer ~= Base de datos
- EntitySet ~= Tabla
- EntityType ~= Columnas
- FunctionImport ~= Procedimiento almacenado

**Verbos HTTP permitidos**: GET: devuelve valores desde la base de datos (devuelve una colección). POST: se usa para transmitir datos a una base de datos y, de manera opcional, devolver valores desde la misma (crea una nueva entrada en la colección, identificador/URI de devolución). DELETE: elimina los datos de la base de datos (elimina una colección). PUT: actualiza los datos de una base de datos (reemplaza una colección o crea una).

## Documento de asignación/metadatos

El documento de asignación/metadatos se usa para asignar los servicios web existentes de un proveedor de contenido para que el sistema de Azure Marketplace pueda exponerlo como un servicio web OData. Se basa en CSDL e implementa algunas extensiones en CSDL para incluir las necesidades de los servicios web basados en REST que se exponen a través de Azure Marketplace. Las extensiones se encuentran en el espacio de nombres [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04).

A continuación, aparece un ejemplo del CSDL: (copie y pegue el CSDL de ejemplo siguiente en un editor de XML y cámbielo para que coincida con su servicio. Luego, péguelo en Asignación de CSDL en la pestaña Servicio de datos cuando cree su servicio en el [Portal de publicación de Azure Marketplace](https://publish.windowsazure.com)).

**Términos:** relación de los términos de CSDL con los términos de la interfaz de usuario del [Portal de publicación](https://publish.windowsazure.com) (PPUI). -En la PPUI, el "título" de la oferta se relaciona con MyWebOffer - En la PPUI, MyCompany se relaciona con [Nombre para mostrar del publicador](http://dev.windows.com/registration?accountprogram=azure) en la interfaz de usuario del **Centro para desarrolladores de Microsoft** - La API se relaciona con un servicio web o un servicio de datos (un plan en la PPUI).

**Jerarquía:** una empresa (proveedor de contenido) es propietaria de ofertas que tienen planes, es decir, servicios, que se alinean con una API.

### Ejemplo de CSDL de servicio web

Se conecta a un servicio que expone un punto de conexión de aplicaciones web (como una aplicación C#).

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
        	<!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

        	<!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"	d:IsPrimaryKey="True" Type="Int32"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"	Type="Double"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"	Type="String"	Nullable="false" d:Map="./City"/>
        <Property Name="State"	Type="String"	Nullable="false" d:Map="./State"/>
        <Property Name="Zip"	Type="Int32"	Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"	Type="DateTime"	Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP]Puede ver más ejemplos de servicio web de CSDL en el artículo [Examples of mapping an existing web service to OData through CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md).

###Ejemplo de CSDL de servicio de datos

Se conecta a un servicio que expone una vista o una tabla de base de datos como un punto de conexión. Los ejemplos siguientes muestran dos API para un CSDL de API basada en base de datos (puede usar vistas en lugar de tablas).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        	Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
        	Map is the schema.tabel or schema.view
        	dals.TableName is the table Name
        	Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
        	dals:IsExposed determines if the table schema is exposed (generally true).
        	dals:IsView (optional) true if this is based on a view rather than a table
        	dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
        	dals:ColumnName is the name of the column in the table /view.
        	Type is the emd.SimpleType
        	Nullable determines if NULL is a valid output value
        	dals.CharMaxLenght is the maximum length of the output value
        	Name is the name of the Property and is exposed to the client facing UI
        	dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
        	IsQueryable is the Boolean that determines if the column can be used in a database query
        	(For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
        	dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
        	dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## Otras referencias
- Si está interesado en conocer los nodos específicos y sus parámetros, consulte el artículo [Understanding the nodes schema for mapping an existing web service to OData through CSDL](marketplace-publishing-data-service-creation-odata-mapping-nodes.md), donde encontrará definiciones y explicaciones, ejemplos y contexto de caso de uso.
- Si está interesado en revisar ejemplos, consulte el artículo [Examples of mapping an existing web service to OData through CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md), donde encontrará ejemplos de código y una explicación del contexto y la sintaxis del código.
- Para volver a la ruta de acceso prescrita para publicar un servicio de datos en Azure Marketplace, consulte el artículo [Data Service Publishing Guide for the Azure Marketplace](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_0114_2016--->