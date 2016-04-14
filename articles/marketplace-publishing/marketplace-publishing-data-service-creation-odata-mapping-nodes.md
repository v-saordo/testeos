<properties
   pageTitle="Guía para crear un Servicio de datos en Marketplace | Microsoft Azure"
   description="Instrucciones detalladas para crear, certificar e implementar un servicio de datos para la venta en Azure Marketplace."
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

# Descripción del esquema de nodos para la asignación de un servicio web existente a OData mediante CSDL
En este documento se clarificará la estructura de nodos para la asignación de un protocolo de OData en CSDL. Es importante tener en cuenta que la estructura de nodos es un código XML con formato correcto. Por tanto, el esquema de raíz, primarios y secundarios se puede aplicar al diseñar una asignación de OData.

## Elementos omitidos
A continuación encontrará los elementos de CSDL de alto nivel (nodos XML) que no van a usar el back-end de Azure Marketplace en la importación de metadatos del servicio web. Pueden estar presentes, pero se omitirán.

| Elemento | Scope |
|----|----|
| Elemento Using | El nodo, los subnodos y todos los atributos |
| Elemento Documentation | El nodo, los subnodos y todos los atributos |
| ComplexType | El nodo, los subnodos y todos los atributos |
| Element Association | El nodo, los subnodos y todos los atributos |
| Propiedad extendida | El nodo, los subnodos y todos los atributos |
| EntityContainer | Se omiten solo los siguientes atributos: *extends* y *AssociationSet* |
| Esquema | Se omiten solo los siguientes atributos: *Namespace* |
| FunctionImport | Se omiten solo los siguientes atributos: *Mode* (se asume el valor predeterminado, ln) |
| EntityType | Se omiten los siguientes subnodos: *Key* y *PropertyRef* |

A continuación se describen detalladamente los cambios (elementos agregados e ignorados) realizados en los distintos nodos XML de CSDL.

## Nodo FunctionImport
Un nodo FunctionImport representa una dirección URL (punto de entrada) que expone un servicio al usuario final. El nodo permite describir cómo se trata la dirección URL, qué parámetros están disponibles para el usuario final y cómo se proporcionan estos parámetros.

En [http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx][MSDNFunctionImportLink] se puede encontrar mas información sobre este nodo

[MSDNFunctionImportLink]: 'http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx'

Éstos son los atributos adicionales (o adiciones a los atributos) que expone el nodo FunctionImport:

**d:BaseUri**: la plantilla del identificador URI para el recurso de REST que se expone en Marketplace. Marketplace usa la plantilla para construir consultas en el servicio web REST. La plantilla del identificador URI contiene marcadores de posición para los parámetros cuyo formato es {parameterName}, donde parameterName es el nombre del parámetro. P. ej. apiVersion = {apiVersion}. Los parámetros pueden aparecer como parámetros URI o como parte de la ruta de acceso de URI. En el caso de la apariencia de la ruta de acceso siempre son obligatorios (no se pueden marcar como que admitan valores NULL). *Ejemplo:* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Name**: el nombre de la función importada. No puede ser igual que otros nombres definidos en el CSDL. Ejemplo: Name = "GetModelUsageFile"

**EntitySet** *(opcional)*: si la función devuelve una colección de tipos de entidad, el valor de **EntitySet** deben ser el conjunto de entidades al que pertenece la colección. De lo contrario, el atributo **EntitySet** no debe usarse. *Ejemplo:* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(opcional)*: especifica el tipo de elementos que devuelve el URI. No utilice este atributo si la función no devuelve un valor. Estos son los tipos que se admiten:

 - **Collection (<Entity type name>)**: especifica una colección de tipos de entidad definidos. El nombre está presente en el atributo Name del nodo EntityType. Un ejemplo es Collection(WXC.HourlyResult).
 - **Raw (<mime type>)**: especifica un documento o blob sin procesar que se devuelve al usuario. Un ejemplo es Raw(image/jpeg). Otros ejemplos:

  - ReturnType="Raw(text/plain)"
  - ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging**: especifica la forma en que el recurso REST controla la paginación. Los valores del parámetro se usan entre llaves, por ejemplo, page = {$page} & itemsperpage = {$size}. Las opciones disponibles son:

- **None:** no hay paginación disponible
- **Skip:** la paginación se expresa mediante un "skip" y "take" (arriba) lógicos. Skip salta M elementos y take devuelve los N elementos siguientes. Valor de parámetro: $skip
- **Take:** Take devuelve los N elementos siguientes. Valor de parámetro: $take
- **PageSize:** la paginación se expresa mediante una página y un tamaño lógicos (elementos por página). Page representa la página actual que se devuelve. Valor de parámetro: $page
- **Size:** size representa el número de elementos devueltos para cada página. Valor de parámetro: $size

**d:AllowedHttpMethods** *(opcional)*: especifica el verbo que se controla mediante el recurso REST. Además, restringe el verbo que se acepta al valor especificado. Valor predeterminado = POST. *Ejemplo:* `d:AllowedHttpMethods="GET"` las opciones disponibles son:

- **GET:** normalmente se usa para devolver datos
- **POST:** normalmente se usa para insertar datos nuevos
- **PUT:** normalmente se usa para actualizar datos
- **DELETE:** se usa para eliminar datos

Los nodos secundarios adicionales (no cubiertos por la documentación de CSDL) del nodo FunctionImport son:

**d:RequestBody** *(opcional)*: el cuerpo de la solicitud se usa para indicar que la solicitud espera un cuerpo para enviarlo. Los parámetros se pueden proporcionar en el cuerpo de solicitud. Se expresa en entre corchetes, por ejemplo, {parameterName}. Dichos parámetros se asignan desde la entrada del usuario al cuerpo que se transfiere al servicio del proveedor de contenido. El elemento requestBody tiene un atributo con el nombre httpMethod. El atributo permite dos valores:

- **POST:** se usa si la solicitud es un HTTP POST
- **GET:** se usa si la solicitud es un HTTP GET

	Ejemplo:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** y **d:Namespace**: este nodo describe los espacios de nombres que se definen en el XML que devuelve la función import (punto de conexión de URI). El XML que devuelve el servicio back-end puede contener cualquier número de espacios de nombres para diferenciar el contenido que se devuelve. **Todos estos espacios de nombres, si se usan en consultas XPath de d:Map o d:Match deben enumerarse.** El nodo d:Namespaces contiene un conjunto o lista de nodos de d:Namespace. Cada uno de ellos enumera un espacio de nombres usado en la respuesta del servicio back-end. Éstos son los atributos del nodo d:Namespace:

-	**d:Prefix:** el prefijo del espacio de nombres, como se ve en los resultados XML que devuelve el servicio, por ejemplo, f:FirstName o f:LastName, donde f es el prefijo.
- **d:Uri:** el identificador URI completo del espacio de nombres usado en el documento de resultados. Representa el valor en que se resuelve el prefijo en tiempo de ejecución.

**d:ErrorHandling** *(opcional)*: este nodo contiene las condiciones para el control de errores. Todas las condiciones se validan con el resultado que devuelve el servicio del proveedor de contenido. Si alguna de ellas coincide con el código de error HTTP propuesto, se devuelve un mensaje de error al usuario final.

**d:ErrorHandling** *(opcional)* y **d:Condition** *(opcional)*: un nodo de condición contiene una condición que se comprueba en el resultado que devuelve el servicio del proveedor de contenido. Estos son los atributos **requeridos**:

- **d:Match:** expresión XPath que valida si un nodo o valor determinados están presentes en el XML de salida del proveedor de contenido. XPath se ejecuta contra la salida y debe devolver true si la condición es una coincidencia o false en caso contrario.
- **d:HttpStatusCode:** el código de estado HTTP que debe devolver Marketplace en el caso de que se dé la condición. Marketplace señaliza los errores al usuario mediante los códigos de estado HTTP. Hay una lista de códigos de estado HTTP en http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage:** el mensaje de error que se devuelve (con el código de estado HTTP) al usuario final. Debe ser de un mensaje de error descriptivo que no contenga secretos.

**d:Title** *(opcional)*: permite la descripción del título de la función. El valor del título procede de:

- El atributo de asignación opcional (una expresión xpath) que especifica dónde encontrar el título en la respuesta que devuelve la solicitud de servicio.
- O bien, el título especificado como valor del nodo.

**d:Rights** *(opcional)*: los derechos (por ejemplo, copyright) asociados con la función. El valor de los derechos procede de:

- El atributo de asignación opcional (una expresión xpath) que especifica dónde encontrar los derechos en la respuesta que devuelve la solicitud de servicio.
-	O bien, los derechos especificados como valor del nodo.

**d:Description** *(opcional)*: una breve descripción de la función. El valor de la descripción procede de:

- El atributo de asignación opcional (una expresión xpath) que especifica dónde encontrar la descripción en la respuesta que devuelve la solicitud de servicio.
- O bien, la descripción especificada como valor del nodo.

**d:EmitSelfLink**: *consulte el ejemplo anterior "FunctionImport para 'Paging' a través de los datos devueltos"*

**d:EncodeParameterValue**: extensión opcional de OData

**d:QueryResourceCost**: extensión opcional de OData

**d:Map**: extensión opcional de OData

**d:Headers**: extensión opcional de OData

**d:Headers**: extensión opcional de OData

**d:Value**: extensión opcional de OData

**d:HttpStatusCode**: extensión opcional de OData

**d:ErrorMessage**: extensión opcional de OData

## Nodo Parameter

Este nodo representa un parámetro que se expone como parte de la plantilla del identificador URI o cuerpo de la solicitud que se ha especificado en el nodo FunctionImport.

En [http://msdn.microsoft.com/library/ee473431.aspx](http://msdn.microsoft.com/library/ee473431.aspx) encontrará una página muy útil del documento de información acerca del "Elemento Parameter" (use la lista desplegable **Otras versiones** para seleccionar una versión diferente si es necesario para ver la documentación). *Ejemplo:* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Atributo del parámetro | Es obligatorio | Valor |
|----|----|----|
| Name | Sí | El nombre del parámetro. Distingue mayúsculas de minúsculas Coincide con el uso de mayúsculas y minúsculas en BaseUri. **Ejemplo:** `<Property Name="IsDormant" Type="Byte" />` |
| Tipo | Sí | El tipo de parámetro. El valor debe ser un tipo **EDMSimpleType** o un tipo complejo que se encuentre dentro del ámbito del modelo. Para más información, consulte "Tipos que se admiten en parámetros y propiedades". (Distingue mayúsculas de minúsculas. El primer carácter está en mayúsculas y el resto en minúsculas.) Consulte también [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][MSDNParameterLink]. **Ejemplo:** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode | No | **In**, Out o InOut, en función de que el parámetro sea de entrada, de salida o de entrada y salida ("IN" es el único disponible en Azure Marketplace). **Ejemplo:** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | No | La longitud máxima permitida del parámetro. **Ejemplo:** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision | No | La precisión del parámetro. **Ejemplo:** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Scale | No | La escala del parámetro. **Ejemplo:** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: 'http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx'

Estos son los atributos que se han agregado a la especificación de CSDL:

| Atributo del parámetro | Descripción |
|----|----|
| **d:Regex** *(opcional)* | Instrucción Regex que se usa para validar el valor de entrada del parámetro. Si el valor de entrada no coincide con la instrucción, el valor se rechaza. Esto permite especificar también un conjunto de valores posibles, por ejemplo, ^[0-9]+?$ para permitir solo números. **Ejemplo:** `<Parameter Name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters" d:SampleValues="George|John|Thomas|James"/>` |
| **d:Enum** *(opcional)* | Lista de valores válidos para el parámetro separada por barras verticales. Es preciso que el tipo de los valores coincida con el tipo definido del parámetro. Ejemplo: `english|metric|raw`. Enum se mostrará en la interfaz de usuario como una lista desplegable en que se pueden seleccionar parámetros (Explorador de servicios). **Ejemplo:** `<Parameter Name="Duration" Type="String" Mode="In" Nullable="true" d:Enum="1year|5years|10years"/>` |
| **d.: Nullable** *(opcional)* | Permite definir si un parámetro puede ser nulo. El valor predeterminado es: true. Sin embargo, los parámetros que se exponen como parte de la ruta de acceso de la plantilla del identificador URI no pueden ser nulos. Cuando el atributo se establece en false para estos parámetros, se reemplaza la entrada del usuario. **Ejemplo:** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(opcional)* | Valor de ejemplo que se muestra en forma de nota al cliente en la interfaz de usuario. Es posible agregar varios valores mediante una lista de canalización separado, es decir `a|b|c` **Ejemplo:** `<Parameter Name="BikeOwner" Type="String" Mode="In" d:SampleValues="George|John|Thomas|James"/>` |

## Nodo EntityType

Este nodo representa uno de los tipos que Marketplace devuelve al usuario final. También contiene la asignación de la salida que devuelve el servicio del proveedor de contenido a los valores que se devuelven al usuario final.

En [http://msdn.microsoft.com/library/bb399206.aspx](http://msdn.microsoft.com/library/bb399206.aspx) encontrará más información sobre este nodo (use la lista desplegable **Otras versiones** para seleccionar una versión diferente si es necesario para ver la documentación).

| Nombre del atributo | Es obligatorio | Valor |
|----|----|----|
| Name | Sí | El nombre del tipo de entidad. **Ejemplo:** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | No | El nombre de otro tipo de entidad que sea el tipo base del tipo de entidad que se define. **Ejemplo:** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Estos son los atributos que se han agregado a la especificación de CSDL:

**d:Map**: expresión XPath que se ejecuta contra la salida del servicio. Aquí se asume que la salida del servicio contiene un conjunto de elementos que se repiten, como una fuente ATOM en la que hay un conjunto de nodos de entrada que se repiten. Cada uno de estos nodos contiene un registro. A continuación, se especifica que la expresión XPath apunte al nodo individual que se repite en el resultado del servicio del proveedor de contenido que contiene los valores de un registro individual. Ejemplo de salida del servicio:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

La expresión XPath sería /foo/bar porque cada instancia del nodo bar es el nodo repetido de la salida y contiene el contenido real que se devuelve al usuario final.

**Key**: Marketplace omite este atributo. Servicios web basados en REST, en general no exponen una clave principal.


## Nodo Property

Este nodo contiene una propiedad del registro.

En [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) encontrará más información sobre este nodo (use la lista desplegable **Otras versiones** para seleccionar una versión diferente si es necesario para ver la documentación). *Ejemplo:* `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name" 	Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
		...
        </EntityType>`

| AttributeName | Obligatorio | Valor |
|----|----|----|
| Name | Sí | El nombre de la propiedad. |
| Type | Sí | El tipo de valor de la propiedad. El tipo de valor de la propiedad debe ser un tipo **EDMSimpleType** o un tipo complejo (indicado mediante un nombre completo) que se encuentre dentro del ámbito del modelo. Para más información, consulte Tipos de modelos conceptuales (CSDL). |
| Nullable | No | **True** (el valor predeterminado) o **False**, en función de que la propiedad puede tener un valor NULL. Nota: en la versión de CSDL que indica el espacio de nombres [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm), las propiedades de tipo complejo deben tener Nullable = "False". |
| DefaultValue | No | El valor predeterminado de la propiedad. |
|MaxLength | No | La longitud máxima del valor de la propiedad. |
| FixedLength | No | **True** o **False**, en función de que el valor de la propiedad se almacene como una cadena de longitud fija, o no. |
| Precision | No | Hace referencia al número máximo de dígitos que se conservan en el valor numérico. |
| Scale | No | Número máximo de posiciones decimales que se conservan en el valor numérico. |
| Unicode | No | **True** o **False**, en función de que el valor de la propiedad se almacene como una cadena Unicode, o no. |
| Collation | No | Cadena que especifica la secuencia de intercalación que se usará en el origen de datos. |
| ConcurrencyMode | No | **None** (el valor predeterminado) o **Fixed**. Si el valor se establece en **Fixed**, el valor de la propiedad se usará en las comprobaciones de simultaneidad optimista. |

Estos son los atributos adicionales que se han agregado a la especificación de CSDL:

**d:Map**: expresión XPath que se ejecuta contra la salida del servicio y extrae una propiedad de la salida. La expresión XPath especificada es relativa al nodo que se repite seleccionado en la expresión XPath del nodo EntityType. También se puede especificar una expresión XPath absoluta para permitir la inclusión de un recurso estático en cada uno de los nodos de salida, como por ejemplo una instrucción de copyright que se encuentra solo una vez en la salida del servicio original, pero que debe estar presente en todas las filas de la salida de OData. Ejemplo del servicio:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

Aquí, la expresión XPath sería ./bar/baz0 para obtener el nodo baz0 del servicio del proveedor de contenido.

**d:CharMaxLength**: para el tipo de cadena, puede especificar la longitud máxima. Consulte DataService CSDL Example

**d:IsPrimaryKey**: indica si la columna es la clave principal de la tabla o vista. Consulte DataService CSDL Example

**d:isExposed**: determina si se expone el esquema de tabla (generalmente true). Consulte DataService CSDL Example

**d:IsView** *(opcional)* : true si se basa en una vista, en lugar de en una tabla. Consulte DataService CSDL Example

**d:Tableschema**: consulte DataService CSDL Example

**d:ColumnName**: es el nombre de la columna en la tabla o vista. Consulte DataService CSDL Example

**d:IsReturned**: es el valor booleano que determina si el servicio expone este valor al cliente. Consulte DataService CSDL Example

**d:IsQueryable**: es el valor booleano que determina si la columna se puede usar en una consulta de base de datos. Consulte DataService CSDL Example

**d:OrdinalPosition**: es la posición numérica de la apariencia de la columna, x, en la tabla o vista, donde x va desde 1 al número de columnas de la tabla. Consulte DataService CSDL Example

**d:DatabaseDataType**: es el tipo de datos de la columna de la base de datos, es decir, el tipo de datos SQL. Consulte DataService CSDL Example

## Tipos que se admiten en parámetros y propiedades
Éstos son los tipos que se admiten para los parámetros y propiedades. (Distingue mayúsculas de minúsculas)

| Tipos primitivos | Descripción |
|----|----|
| Null | Representa la ausencia de cualquier valor |
| Booleano | Representa el concepto matemático de la lógica de valores binarios|
| Byte | Valor entero de 8 bits sin signo|
|DateTime| Representa la fecha y hora con valores que oscilan entre la medianoche, 12:00:00, del 1 de enero de 1753 D.C. y las 11:59:59 p.m. de diciembre de 9999 D.C.|
|Decimal | Representa valores numéricos con precisión y escala fijas. Este tipo puede describir un valor numérico comprendido entre menos 10 ^ 255 + 1 y más 10 ^-255 -1|
| Double | Representa un número de punto flotante con una precisión de 15 dígitos que puede representar valores con un intervalo aproximado de ± 2,23e-308 a 1,79e+308. **Utilice Decimal debido a un problema de exportación de Excel**|
| Single | Representa un número de punto flotante con una precisión de 7 dígitos que puede representar valores con un intervalo aproximado de ± 1,18e-38 a 3,40e+38.|
|Guid |Representa un valor de identificador único de 16 bytes (128 bits) |
|Int16|Representa un valor entero de 16 bits con signo |
|Int32|Representa un valor entero de 32 bits con signo |
|Int64|Representa un valor entero de 64 bits con signo |
|String | Representa datos de caracteres de longitud fija o variable |


## Otras referencias
- Si está interesado en conocer el proceso general de asignación de OData y su finalidad, consulte el artículo [Mapping an existing web service to OData through CSDL](marketplace-publishing-data-service-creation-odata-mapping.md), donde podrá encontrar definiciones, información sobre las estructuras e instrucciones.
- Si está interesado en ver ejemplos, consulte el artículo [Examples of mapping an existing web service to OData through CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md), donde podrá ver ejemplos de código y comprender el contexto y la sintaxis del código.
- Para volver a la ruta de acceso prescrita para publicar un servicio de datos en Azure Marketplace, consulte el artículo [Data Service Publishing Guide for the Azure Marketplace](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_0114_2016-->