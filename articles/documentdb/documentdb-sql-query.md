<properties 
	pageTitle="Sintaxis SQL y consulta SQL para DocumentDB | Microsoft Azure" 
	description="Más información sobre la sintaxis SQL, los conceptos de base de datos y las consultas SQL para DocumentDB, una base de datos NoSQL. Puede utilizar SQL como lenguaje de consulta JSON en DocumentDB." 
	keywords="consulta sql, consultas sql, sintaxis sql, lenguaje de consulta json, conceptos de base de datos y consultas sql"
	services="documentdb" 
	documentationCenter="" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="arramac"/>

# Consulta SQL y sintaxis SQL en DocumentDB
Microsoft Azure DocumentDB admite la consulta de documentos con SQL (lenguaje de consulta estructurado) como lenguaje de consulta de JSON. Base de documentos realmente no tiene esquemas. En virtud de su compromiso con el modelo de datos JSON que se encuentra directamente en el motor de base de datos, proporciona índices automáticos de documentos JSON sin necesidad de un esquema explícito o de la creación de índices secundarios.

Durante el diseño del lenguaje de consulta de DocumentDB, teníamos dos objetivos en mente:

-	En lugar de inventar un nuevo lenguaje de consulta JSON, preferimos ofrecer compatibilidad con el lenguaje SQL. SQL es uno de los lenguajes de consulta más familiares y populares. SQL de DocumentDB proporciona un modelo de programación formal para consultas enriquecidas en documentos JSON.
-	Igual que una base de datos de documentos JSON capaz de ejecutar JavaScript directamente en el motor de base de datos, queríamos usar el modelo de programación de JavaScript como base para nuestro lenguaje de consulta. SQL de DocumentDB se basa en el sistema de tipos de JavaScript, la evaluación de expresiones y la invocación de funciones. A su vez, este proporciona un modelo de programación natural para proyecciones relacionales, navegación jerárquica por documentos JSON, autocombinaciones, consultas espaciales e invocación de funciones definidas por el usuario (UDF) escritas íntegramente en JavaScript, entre otras características. 

Creemos que estas capacidades son clave para reducir la fricción entre la aplicación y la base de datos, y que son cruciales para la productividad de los desarrolladores.

Se recomienda comenzar por ver el vídeo siguiente, donde Aravind Ramachandran muestra las capacidades de consulta de DocumentDB y visitar [Query Playground](http://www.documentdb.com/sql/demo), donde puede probar DocumentDB y ejecutar consultas SQL con nuestro conjunto de datos.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Luego, vuelva a este artículo, donde comenzaremos con un tutorial de consulta SQL que le guiará a través de algunos documentos sencillos de JSON y comandos SQL.

## Introducción a los comandos SQL en DocumentDB
Para consultar SQL de DocumentDB trabajando, empezaremos con unos documentos JSON sencillos y realizaremos algunas consultas fáciles con él. Tenga en cuenta estos dos documentos JSON sobre dos familias. No olvide que, con Base de datos de documentos, no es preciso que creemos ningún esquema ni índice secundario de forma explícita. Simplemente tenemos que insertar los documentos JSON en una colección DocumentDB y posteriormente realizar una consulta. Aquí tenemos un documento JSON sencillo para la familia Andersen, los padres, los hijos (y sus mascotas), la dirección y la información de registro. El documento tiene cadenas, números, booleanos, matrices y propiedades anidadas.

**Documento**

	{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}


Aquí se muestra un segundo documento con una sutil diferencia: se usan `givenName` y `familyName` en lugar de `firstName` y `lastName`.

**Documento**

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	            "familyName": "Miller", 
	             "givenName": "Lisa", 
	             "gender": "female", 
	             "grade": 8 }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "creationDate": 1431620462,
	    "isRegistered": false
	}



Ahora realicemos algunas consultas con estos datos para entender algunos aspectos clave de SQL de Base de datos de documentos. Por ejemplo, la consulta siguiente devolverá los documentos en los que el campo de id. coincida con `AndersenFamily`. Puesto que es `SELECT *`, la salida de la consulta es el documento JSON completo:

**Consultar**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]


Ahora preste atención al caso donde debemos cambiar el formato al resultado JSON con otra forma. Esta consulta proyecta un nuevo objeto JSON con dos campos seleccionados, Nombre y Ciudad, cuando la ciudad de la dirección tiene el mismo nombre que el estado. En este caso, "NY, NY" coincide.

**Consultar**

	SELECT {"Name":f.id, "City":f.address.city} AS Family 
	FROM Families f 
	WHERE f.address.city = f.address.state

**Resultados**

	[{
	    "Family": {
	        "Name": "WakefieldFamily", 
	        "City": "NY"
	    }
	}]


La consulta siguiente devuelve todos los nombres proporcionados de los niños de la familia cuyo id. coincida con `WakefieldFamily`, ordenados por ciudad de residencia.

**Consultar**

	SELECT c.givenName 
	FROM Families f 
	JOIN c IN f.children 
	WHERE f.id = 'WakefieldFamily'
	ORDER BY f.address.city ASC

**Resultados**

	[
	  { "givenName": "Jesse" }, 
	  { "givenName": "Lisa"}
	]


Nos gustaría llamar la atención sobre algunos aspectos destacados del lenguaje de consulta de Base de datos de documentos a través de los ejemplos que hemos visto hasta el momento:
 
-	Como SQL de Base de datos de documentos trabaja en valores JSON, trata entidades en forma de árbol en lugar de filas y columnas. Por consiguiente, el lenguaje permite que se haga referencia a los nodos del árbol a cualquier profundidad arbitraria, como `Node1.Node2.Node3…..Nodem`, de forma similar al lenguaje SQL relacional que hace alusión a la referencia dos partes de `<table>.<column>`.   
-	El lenguaje de consulta estructurado trabaja con datos sin esquemas. Por lo tanto, es necesario que el sistema de tipo se enlace dinámicamente. La misma expresión podría producir diversos tipos en distintos documentos. El resultado de una consulta es un valor JSON válido, pero no se garantiza que sea de un esquema fijo.  
-	Base de datos de documentos solo admite documentos JSON estrictos. Esto significa que el sistema de tipo y las expresiones se restringen para tratar únicamente tipos JSON. Para obtener más información, consulte la [especificación de JSON](http://www.json.org/).  
-	Una recopilación de Base de datos de documentos es un contenedor sin esquemas de documentos JSON. Las relaciones en las entidades de datos dentro de los documentos de una colección y entre ellos se capturan de manera implícita por contención y no por relaciones entre clave principal y clave externa. Se trata de un aspecto importante que merece la pena señalar teniendo en cuenta las combinaciones internas descritas posteriormente en este artículo.

## Indexación de DocumentDB

Antes de entrar en la sintaxis de SQL de DocumentDB, vale la pena explorar el diseño de indexación de DocumentDB.

El objetivo de los índices de base de datos es atender consultas en sus diversas formas con un consumo de los recursos mínimo (como CPU y entrada y salida) mientras se proporcionan un buen rendimiento y una latencia baja. A menudo, la elección del índice adecuado para consultar una base de datos requiere mucha planificación y experimentación. Este enfoque plantea un desafío para las bases de datos sin esquemas en las que los datos no cumplen un esquema estricto y evolucionan rápidamente.

Por lo tanto, al diseñar el subsistema de indización de DocumentDB, establecemos los siguientes objetivos:

-	Indexar documentos sin necesidad de esquema: el subsistema de indexación no requiere información de esquema alguna ni la realización de ninguna suposición sobre el esquema de los documentos. 

-	Compatibilidad con consultas eficaces, enriquecidas jerárquicas y relacionales: el índice admite el lenguaje de consulta de DocumentDB de manera eficaz, incluida la compatibilidad con proyecciones jerárquicas y relacionales.

-	Compatibilidad con consultas coherentes frente a un volumen de escrituras sostenido: en el caso de las cargas de trabajo de alto rendimiento de escritura con consultas coherentes, el índice se actualiza paulatinamente, de forma eficaz y en línea frente a un volumen de escrituras sostenido. La actualización del índice coherente es crucial para atender las consultas en el nivel de coherencia en el que el usuario configura el servicio de documentos.

-	Compatibilidad con la arquitectura multiempresa: una vez proporcionado el modelo basado en la reserva para la gobernanza de recursos de los inquilinos, se realizan actualizaciones de los índices sin sobrepasar el presupuesto de los recursos del sistema (CPU, memoria, IOPS) asignadas por réplica.

-	Eficacia de almacenamiento: para obtener rentabilidad, se enlaza la sobrecarga de almacenamiento en el disco del índice y es predecible. Esto es fundamental porque DocumentDB permite que el desarrollador haga concesiones basadas en el coste entre la sobrecarga de índices y el rendimiento de las consultas.

Consulte los [ejemplos de DocumentDB](https://github.com/Azure/azure-documentdb-net) en MSDN para ver casos en los que se muestra cómo configurar la directiva de indexación para una colección. Adentrémonos ahora en los detalles de la sintaxis de SQL de DocumentDB.


## Conceptos básicos de una consulta SQL de DocumentDB
Todas las consultas constan de una cláusula SELECT y cláusulas FROM y WHERE opcionales por estándares ANSI-SQL. Normalmente, para cada consulta, se enumera el origen de la cláusula FROM. A continuación, el filtro de la cláusula WHERE se aplica en el origen para recuperar un subconjunto de documentos JSON. Por último, la cláusula SELECT se usa para proyectar los valores JSON solicitados en la lista seleccionada.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## Cláusula FROM
La cláusula `FROM <from_specification>` es opcional, a menos que el origen se filtre o se proyecte posteriormente en la consulta. El objetivo de esta cláusula es especificar el origen de datos sobre el que debe operar la consulta. Normalmente, toda la recopilación es el origen pero, en su lugar, puede especificarse un subconjunto de la recopilación.

Una consulta como `SELECT * FROM Families` indica que toda la colección Families es el origen sobre el que se va a realizar la enumeración. Se puede usar una RAÍZ de identificador especial para representar la colección en lugar de usar el nombre de la colección. La lista siguiente contiene las reglas que se aplican por consulta:

- Puede establecerse para la colección un alias como `SELECT f.id FROM Families AS f`, o simplemente `SELECT f.id FROM Families f`. Aquí, `f` es el equivalente de `Families`. `AS` es una palabra clave opcional para establecer un alias para el identificador.

-	Tenga en cuenta que, una vez establecido un alias, no podrá enlazarse el origen original. Por ejemplo, `SELECT Families.id FROM Families f` no es válido sintácticamente porque el identificador "Families" no puede resolverse.

-	Todas las propiedades a las que es necesario hacer referencia deben estar completas. A falta de un cumplimiento del esquema estricto, esto se impone para evitar cualquier enlace ambiguo. Por lo tanto, `SELECT id FROM Families f` no es válido sintácticamente porque la propiedad `id` no está enlazada.
	
### Subdocumentos
El origen también se puede reducir a un subconjunto más pequeño. Por ejemplo, para enumerar únicamente un subárbol en cada documento, la subraíz podría convertirse en el origen, como se muestra en el ejemplo siguiente.

**Consultar**

	SELECT * 
	FROM Families.children

**Resultados**

	[
	  [
	    {
	        "firstName": "Henriette Thaulow",
	        "gender": "female",
	        "grade": 5,
	        "pets": [
	          {
	              "givenName": "Fluffy"
	          }
	        ]
	    }
	  ],
	  [
	    {
	        "familyName": "Merriam",
	        "givenName": "Jesse",
	        "gender": "female",
	        "grade": 1
	    },
	    {
	        "familyName": "Miller",
	        "givenName": "Lisa",
	        "gender": "female",
	        "grade": 8
	    }
	  ]
	]

Aunque en el ejemplo anterior se usó una matriz como origen, también podría usarse un objeto como origen como se muestra en el ejemplo siguiente. Cualquier valor JSON válido (que no sea Undefined) que se pueda encontrar en el origen se tendrá en cuenta para su inclusión en el resultado de la consulta. Si algunas familias no tienen un valor `address.state`, se excluirán del resultado de la consulta.

**Consultar**

	SELECT * 
	FROM Families.address.state

**Resultados**

	[
	  "WA", 
	  "NY"
	]


## Cláusula WHERE
La cláusula WHERE (**`WHERE <filter_condition>`**) es opcional. Especifica las condiciones de que los documentos JSON proporcionados por el origen deben satisfacer para incluirse como parte del resultado. Cualquier documento JSON debe evaluar las condiciones especificadas en "true" para su consideración para el resultado. La capa de índice usa la cláusula WHERE para determinar el subconjunto más pequeño absoluto de documentos de origen que pueden formar parte del resultado.

En la consulta siguiente se solicitan documentos que contienen una propiedad de nombre cuyo valor es `AndersenFamily`. Cualquier otro documento que no tenga una propiedad de nombre o en el que el valor no coincida con `AndersenFamily` se excluye.

**Consultar**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


En el ejemplo anterior se mostraba una sencilla consulta de igualdad. SQL de Base de datos de documentos también admite diversas expresiones escalares. Las que más se suelen usar son binarias y unarias. Las referencias de propiedad del objeto JSON de origen también son expresiones válidas.

Actualmente se admiten los siguientes operadores binarios y pueden usarse en consultas como se muestra en los ejemplos siguientes: <table> <tr> <td>Aritmético</td> <td>+,-,*,/,%</td> </tr> <tr> <td>Bit a bit</td> <td>|, &, ^, <<, >>, >>> (desplazamiento a la derecha con relleno de ceros) </td> </tr> <tr> <td>Lógico</td> <td>AND, OR, NOT</td> </tr> <tr> <td>Comparación</td> <td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td> </tr> <tr> <td>Cadena</td> <td>|| (concatenar)</td> </tr> </table>

Echemos un vistazo a algunas consultas usando operadores binarios.

	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade % 2 = 1     -- matching grades == 5, 1
	
	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade ^ 4 = 1    -- matching grades == 5
	
	SELECT *
	FROM Families.children[0] c
	WHERE c.grade >= 5     -- matching grades == 5


También se admiten los operadores unarios +,-, ~ y NOT, y se pueden usar dentro de consultas como se muestra en el ejemplo siguiente:

	SELECT *
	FROM Families.children[0] c
	WHERE NOT(c.grade = 5)  -- matching grades == 1
	
	SELECT *
	FROM Families.children[0] c
	WHERE (-c.grade = -5)  -- matching grades == 5



Además de los operadores unarios y binarios, también se permiten referencias de propiedad. Por ejemplo, `SELECT * FROM Families f WHERE f.isRegistered` devuelve el documento JSON que contenga la propiedad `isRegistered` en la que el valor de la propiedad sea igual al valor `true` JSON. Cualquier otro valor (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>`, etc.) hace que el documento de origen se excluya del resultado.

### Operadores de igualdad y de comparación
En la siguiente tabla se muestra el resultado de las comparaciones de igualdad en el lenguaje SQL de DocumentDB entre dos tipos JSON cualesquiera. <table style = "width:300px"> <tbody> <tr> <td valign="top"> <strong>Op</strong> </td> <td valign="top"> <strong>Undefined</strong> </td> <td valign="top"> <strong>Null</strong> </td> <td valign="top"> <strong>Boolean</strong> </td> <td valign="top"> <strong>Number</strong> </td> <td valign="top"> <strong>String</strong> </td> <td valign="top"> <strong>Object</strong> </td> <td valign="top"> <strong>Array</strong> </td> </tr> <tr> <td valign="top"> <strong>Undefined<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Null<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Boolean<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Number<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>String<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Object<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> <td valign="top"> Undefined </td> </tr> <tr> <td valign="top"> <strong>Array<strong> </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> Undefined </td> <td valign="top"> <strong>OK</strong> </td> </tr> </tbody> </table>

Para otros operadores de comparación como >, >=, !=, < y <=, se aplican las siguientes reglas:

-	La comparación entre los tipos da lugar a Undefined.
-	La comparación entre dos objetos o dos matrices da lugar a Undefined.   

Si el resultado de la expresión escalar del filtro es Undefined, el documento correspondiente no se incluiría en el resultado, pues Undefined no es igual lógicamente a "true".

### Palabra clave BETWEEN
También puede usar la palabra clave BETWEEN para expresar consultas en intervalos de valores como en ANSI SQL. BETWEEN puede utilizarse con cadenas o números.

Por ejemplo, esta consulta devuelve todos los documentos de la familia en los que el curso del primer hijo se encuentra entre 1 y 5 (ambos inclusive).

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Al contrario que en ANSI SQL, también se puede usar la cláusula BETWEEN en la cláusula FROM, como en el ejemplo siguiente.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Para que la consulta se ejecute de forma más rápida, no olvide crear una directiva de indización que use un tipo de índice de intervalo en cualquier ruta o propiedad numérica que se filtre en la cláusula BETWEEN.

La principal diferencia entre usar BETWEEN en DocumentDB y ANSI SQL es que puede expresar consultas por rangos en propiedades de tipos mixtos. Por ejemplo, "grade" podría ser un número (5) en algunos documentos y una cadena ("grade4") en otros. En estos casos, al igual que en JavaScript, una comparación entre dos tipos distintos da como resultado "undefined" y el documento se omitirá.

### Operadores lógicos (Y, O y NO)
Los operadores lógicos operan en valores booleanos. Las tablas de verdad lógica para estos operadores se muestran en las siguientes tablas.

OR|True|False|Undefined
---|---|---|---
True|True|True|True
False|True|False|Undefined
Undefined|True|Undefined|Undefined

Y|True|False|Undefined
---|---|---|---
True|True|False|Undefined
False|False|False|False
Undefined|Undefined|False|Undefined

NO| |
---|---
True|False
False|True
Undefined|Undefined

### Palabra clave IN
La palabra clave IN puede usarse para comprobar si un valor especificado coincide con algún valor de una lista. Por ejemplo, esta consulta devuelve todos los documentos de la familia en los que el identificador sea "WakefieldFamily" o "AndersenFamily".
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Este ejemplo devuelve todos los documentos en los que el estado es cualquiera de los valores especificados.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

IN equivale a encadenar varias cláusulas OR; pero, puesto que puede obtenerse mediante un índice único, DocumentDB admite un [límite](documentdb-limits.md) superior para el número de argumentos especificados dentro de una cláusula IN.

### Operadores ternario (?) y de fusión (??)
El operador ternario y el operador de combinación pueden usarse para crear expresiones condicionales, de forma similar a lenguajes de programación populares como C# y JavaScript.

El operador ternario (?) puede ser muy útil al construir nuevas propiedades JSON sobre la marcha. Así, ahora puede escribir consultas para clasificar los niveles de clase en formato de lenguaje natural, por ejemplo Principiante/Intermedio/Avanzado, como se muestra a continuación.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

También puede anidar las llamadas al operador como en la consulta siguiente.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Como ocurre con otros operadores de consulta, si las propiedades a las que se hace referencia en la expresión condicional faltan en cualquier documento, o si los tipos que se comparan son diferentes, esos documentos se excluirán de los resultados de la consulta.

El operador de fusión (??) se puede usar para comprobar eficazmente la presencia de una propiedad (es decir, si esta se ha definido) en un documento. Esto es útil cuando se consultan datos semiestructurados o de tipos combinados. Por ejemplo, esta consulta devuelve el valor "lastName" si está presente o "surname" si no lo está.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### Descriptor de acceso de propiedad entre comillas
También es posible obtener acceso a las propiedades mediante el operador de la propiedad entre comillas `[]`. Por ejemplo, `SELECT c.grade` y `SELECT c["grade"]` son equivalentes. Esta sintaxis es útil cuando se necesita crear una secuencia de escape para una propiedad que contiene espacios en blanco, caracteres especiales, o que comparte el nombre con una palabra clave SQL o una palabra reservada.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## Cláusula SELECT
La cláusula SELECT (**`SELECT <select_list>`**) es obligatoria y especifica los valores que se recuperarán de la consulta, de la misma forma que en ANSI-SQL. El subconjunto que se ha filtrado en la parte superior de los documentos de origen pasa a la fase de proyección, en la cual se recuperan los valores JSON especificados y se construye un nuevo objeto JSON para cada una de las entradas que pasan a él.

En el ejemplo siguiente se muestra una consulta SELECT típica:

**Consultar**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


### Propiedades anidadas
En el ejemplo siguiente, se proyectan dos propiedades anidadas, `f.address.state` y `f.address.city`.

**Consultar**

	SELECT f.address.state, f.address.city
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	  "state": "WA", 
	  "city": "seattle"
	}]


La proyección también admite experiencias JSON, como se muestra en el siguiente ejemplo.

**Consultar**

	SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle", 
	    "name": "AndersenFamily"
	  }
	}]


Analicemos el rol que `$1` tiene aquí. La cláusula `SELECT` debe crear un objeto JSON y, como no se proporciona ninguna clave, usamos nombres de variable de argumentos implícitos que empiezan por `$1`. Por ejemplo, esta consulta devuelve dos variables de argumentos implícitos, etiquetadas como `$1` y `$2`.

**Consultar**

	SELECT { "state": f.address.state, "city": f.address.city }, 
	       { "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "$2": {
	    "name": "AndersenFamily"
	  }
	}]


### Establecimiento de alias
Ampliemos ahora el ejemplo anterior con un establecimiento de alias explícito para valores. AS es la palabra clave usada para el establecimiento de alias. Tenga en cuenta que es opcional, como se muestra al proyectarse el segundo valor como `NameInfo`.

En caso de que una consulta tenga dos propiedades con el mismo nombre, el establecimiento de alias debe usarse para cambiar el nombre de las propiedades de modo que se elimine su ambigüedad en el resultado proyectado.

**Consultar**

	SELECT 
	       { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
	       { "name": f.id } NameInfo
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	  "AddressInfo": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "NameInfo": {
	    "name": "AndersenFamily"
	  }
	}]


### Expresiones escalares
Además de las referencias de propiedad, la cláusula SELECT también admite expresiones escalares como constantes, expresiones aritméticas, expresiones lógicas, etc. Por ejemplo, aquí hay una sencilla consulta "Hello World".

**Consultar**

	SELECT "Hello World"

**Resultados**

	[{
	  "$1": "Hello World"
	}]


A continuación se muestra un ejemplo más complejo que usa una expresión escalar.

**Consultar**

	SELECT ((2 + 11 % 7)-2)/3	

**Resultados**

	[{
	  "$1": 1.33333
	}]


En el ejemplo siguiente, el resultado de la expresión escalar es un valor booleano.

**Consultar**

	SELECT f.address.city = f.address.state AS AreFromSameCityState
	FROM Families f	

**Resultados**

	[
	  {
	    "AreFromSameCityState": false
	  }, 
	  {
	    "AreFromSameCityState": true
	  }
	]


### Creación de objetos y matrices
Otra característica clave del lenguaje SQL de Base de datos de documentos es la creación de matrices u objetos. En el ejemplo anterior, observe que creamos un nuevo objeto JSON. De manera similar, uno también puede construir matrices como se muestra en los siguientes ejemplos.

**Consultar**

	SELECT [f.address.city, f.address.state] AS CityState 
	FROM Families f	

**Resultados**

	[
	  {
	    "CityState": [
	      "seattle", 
	      "WA"
	    ]
	  }, 
	  {
	    "CityState": [
	      "NY", 
	      "NY"
	    ]
	  }
	]

### Palabra clave VALUE
La palabra clave **VALUE** proporciona una forma de devolver un valor JSON. Por ejemplo, la consulta que se muestra a continuación devuelve la expresión escalar `"Hello World"`, en lugar de `{$1: "Hello World"}`.

**Consultar**

	SELECT VALUE "Hello World"

**Resultados**

	[
	  "Hello World"
	]


La siguiente consulta devuelve el valor JSON sin la etiqueta `"address"` en los resultados.

**Consultar**

	SELECT VALUE f.address
	FROM Families f	

**Resultados**

	[
	  {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }, 
	  {
	    "state": "NY", 
	    "county": "Manhattan", 
	    "city": "NY"
	  }
	]

El ejemplo siguiente se amplía para mostrar cómo devolver valores primitivos JSON (el nivel de hoja del árbol JSON).

**Consultar**

	SELECT VALUE f.address.state
	FROM Families f	

**Resultados**

	[
	  "WA",
	  "NY"
	]


###Operador *
Se admite el operador especial (*) para proyectar el documento tal cual. Al usarse, debe ser el único campo proyectado. Aunque una consulta como `SELECT * FROM Families f` es válida, `SELECT VALUE * FROM Families f ` y `SELECT *, f.id FROM Families f ` no lo son.

**Consultar**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**Resultados**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]

###Operador TOP
La palabra clave TOP se puede usar para limitar la cantidad de valores de una consulta. Cuando se usa TOP junto con la cláusula ORDER BY, el conjunto de resultados se limita a los primeros N valores ordenados; de otro modo, devuelve los primeros N resultados en orden indefinido. Como procedimiento recomendado, en una instrucción SELECT, siempre use una cláusula ORDER BY con la cláusula TOP. Esta es la única forma previsible de indicar qué filas afecta TOP.


**Consultar**

	SELECT TOP 1 * 
	FROM Families f 

**Resultados**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "creationDate": 1431620472,
	    "isRegistered": true
	}]

TOP se puede usar con un valor constante (como se muestra anteriormente) o con un valor variable usando consultas con parámetros. Si desea obtener más información, consulte las consultas con parámetros que aparecen a continuación.

## Cláusula ORDER BY
Al igual que en ANSI SQL, puede incluir una cláusula Order By opcional al realizar la consulta. La cláusula puede incluir un argumento ASC o DESC opcional para especificar el orden en que se deben recuperar los resultados. Si desea obtener más información sobre Order By, consulte [Ordenación de datos de DocumentDB con Order By](documentdb-orderby.md).

Por ejemplo, aquí hay una consulta que recupera las familias ordenadas por nombre de la ciudad de residencia.

**Consultar**

	SELECT f.id, f.address.city
	FROM Families f 
	ORDER BY f.address.city
	
**Resultados**
	
	[
	  {
	    "id": "WakefieldFamily",
	    "city": "NY"
	  },
	  {
	    "id": "AndersenFamily",
	    "city": "Seattle"	
	  }
	]

Y la siguiente es una consulta que recupera las familias ordenadas por fecha de creación, que se almacena como un número que representa el tiempo epoch, es decir, el tiempo transcurrido desde el 1 de enero de 1970, en segundos.

**Consultar**

	SELECT f.id, f.creationDate
	FROM Families f 
	ORDER BY f.creationDate DESC
	
**Resultados**
	
	[
	  {
	    "id": "WakefieldFamily",
	    "creationDate": 1431620462
	  },
	  {
	    "id": "AndersenFamily",
	    "creationDate": 1431620472	
	  }
	]
	
## Conceptos avanzados de base de datos y consultas SQL
### Iteración
Se ha agregado una nueva construcción mediante la palabra clave **IN** en SQL de DocumentDB que proporcionar compatibilidad con la iteración en las matrices JSON. El origen FROM proporciona compatibilidad con la iteración. Empecemos con el ejemplo siguiente:

**Consultar**

	SELECT * 
	FROM Families.children

**Resultados**

	[
	  [
	    {
	      "firstName": "Henriette Thaulow", 
	      "gender": "female", 
	      "grade": 5, 
	      "pets": [{ "givenName": "Fluffy"}]
	    }
	  ], 
	  [
	    {
	        "familyName": "Merriam", 
	        "givenName": "Jesse", 
	        "gender": "female", 
	        "grade": 1
	    }, 
	    {
	        "familyName": "Miller", 
	        "givenName": "Lisa", 
	        "gender": "female", 
	        "grade": 8
	    }
	  ]
	]

Analicemos ahora otra consulta que realice una iteración sobre elementos secundarios de la recopilación. Observe la diferencia en la matriz de salida. En este ejemplo se divide `children` y se reducen los resultados a una sola matriz.

**Consultar**

	SELECT * 
	FROM c IN Families.children

**Resultados**

	[
	  {
	      "firstName": "Henriette Thaulow",
	      "gender": "female",
	      "grade": 5,
	      "pets": [{ "givenName": "Fluffy" }]
	  },
	  {
	      "familyName": "Merriam",
	      "givenName": "Jesse",
	      "gender": "female",
	      "grade": 1
	  },
	  {
	      "familyName": "Miller",
	      "givenName": "Lisa",
	      "gender": "female",
	      "grade": 8
	  }
	]

Esto puede usarse más veces para filtrar por cada entrada individual de la matriz como se muestra en el ejemplo siguiente.

**Consultar**

	SELECT c.givenName
	FROM c IN Families.children
	WHERE c.grade = 8

**Resultados**

	[{
	  "givenName": "Lisa"
	}]

### Combinaciones
En una base de datos relacional, la necesidad de combinar en tablas es muy importante. Es la consecuencia lógica de diseñar esquemas normalizados. Al contrario que esto, Base de datos de documentos aborda el modelo de datos desnormalizado de documentos sin esquemas. Este es el equivalente lógico de una "autocombinación".

La sintaxis que el lenguaje admite es <from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>. Generalmente, esto devuelve un conjunto de **N** tuplas (tupla con **N** valores). Cada tupla tiene valores generados por sus respectivos conjuntos en iteración de todos los alias de colección. En otras palabras, se trata de un producto cruzado completo de los conjuntos que participan en la combinación.

En los ejemplos siguientes se muestra cómo funciona la cláusula JOIN. En el siguiente ejemplo, el resultado está vacío porque el producto cruzado de cada documento del origen y un conjunto vacío está vacío.

**Consultar**

	SELECT f.id
	FROM Families f
	JOIN f.NonExistent

**Resultados**

	[{
	}]


En el ejemplo siguiente, la combinación se realiza entre la raíz del documento y la subraíz de `children`. Es un producto cruzado entre dos objetos JSON. El hecho de que los elementos secundarios sean una matriz no funciona en JOIN porque abordamos una sola raíz que es la matriz secundaria. Así pues, en el resultado se incluyen únicamente dos resultados, pues el producto cruzado de cada documento con la matriz produce exactamente solo un documento.

**Consultar**

	SELECT f.id
	FROM Families f
	JOIN f.children
 
**Resultados**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]


En el ejemplo siguiente se muestra una combinación más convencional:

**Consultar**

	SELECT f.id
	FROM Families f
	JOIN c IN f.children 

**Resultados**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]



Lo primero que hay que tener en cuenta es que `from_source` de la cláusula **JOIN** es un iterador. Por lo tanto, el flujo en este caso es como sigue:

-	Amplía cada elemento secundario **c** en la matriz.
-	Aplique un producto cruzado con la raíz del documento **f** con cada elemento secundario **c** del que se quitó el formato en el primer paso.
-	Por último, proyecte solo la propiedad de nombre **f** del objeto raíz. 

El primer documento (`AndersenFamily`) contiene únicamente un elemento secundario, por lo que el conjunto de resultados contiene solo un objeto correspondiente a este documento. El segundo documento (`WakefieldFamily`) contiene dos elementos secundarios. De este modo, el producto cruzado produce un objeto independiente para cada elemento secundario, lo que da lugar a dos objetos, uno para cada elemento secundario correspondiente a este documento. Tenga en cuenta que los campos raíces de estos dos documentos serán los mismos, justo como esperaría en un producto cruzado.

La utilidad real de JOIN es la formación de tuplas a partir del producto cruzado con una forma que de otro modo es difícil de proyectar. Además, como veremos en el siguiente ejemplo, podría filtrarse por la combinación de una tupla que permite que el usuario elija una condición satisfecha por las tuplas en general.

**Consultar**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
 
**Resultados**

	[
	  {
	    "familyName": "AndersenFamily", 
	    "childFirstName": "Henriette Thaulow", 
	    "petName": "Fluffy"
	  }, 
	  {
	    "familyName": "WakefieldFamily", 
	    "childGivenName": "Jesse", 
	    "petName": "Goofy"
	  }, 
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]



Este ejemplo es una ampliación natural del anterior y realiza una combinación doble. De este modo, el producto cruzado se puede ver como el pseudocódigo siguiente.

	for-each(Family f in Families)
	{	
		for-each(Child c in f.children)
		{
			for-each(Pet p in c.pets)
			{
				return (Tuple(f.id AS familyName, 
	              c.givenName AS childGivenName, 
	              c.firstName AS childFirstName,
	              p.givenName AS petName));
			}
		}
	}

`AndersenFamily` tiene un hijo que tiene una mascota. De esta manera, el producto cruzado produce una fila (1 * 1 * 1) a partir de esta familia. La familia Wakefield tiene, sin embargo, dos hijos, pero solo uno, "Jesse", tiene mascotas. Tiene dos mascotas, sin embargo. Así pues, el producto cruzado produce 1 * 1 * 2 = 2 filas a partir de esta familia.

En el ejemplo siguiente, hay un filtro adicional en `pet` Este excluye todas las tuplas donde el nombre de mascota no sea "Shadow". Tenga en cuenta que podemos crear tuplas a partir de matrices, filtrar por cualquiera de los elementos de la tupla y proyectar cualquier combinación de los elementos.

**Consultar**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
	WHERE p.givenName = "Shadow"

**Resultados**

	[
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]


## Integración de JavaScript
Base de datos de documentos proporciona un modelo de programación para ejecutar una lógica de aplicación basada en JavaScript directamente en las recopilaciones en términos de procedimientos y desencadenadores almacenados. Esto les proporciona:

-	La posibilidad de realizar operaciones CRUD transaccionales de alto rendimiento y consultas en los documentos de una recopilación en virtud de una mayor integración del tiempo de ejecución de JavaScript directamente en el motor de base de datos. 
-	Un modelo natural de flujo de control, ámbito variable, asignación e integración de primitivas de control de excepciones con transacciones de base de datos. Para obtener más detalles sobre la compatibilidad de Base de datos de documentos con la integración de JavaScript, consulta la documentación de programación del servidor de JavaScript.

###Funciones definidas por el usuario (UDF)
Junto con los tipos ya definidos en este artículo, el lenguaje SQL de DocumentDB ofrece compatibilidad para las funciones definidas por el usuario (UDF). En particular, se admiten las UDF escalares allí donde los desarrolladores puedan proporcionar cero o muchos argumentos y devolver un solo resultado de argumento. Cada uno de estos argumentos se comprueba para ver si se trata de valores JSON legales.

La sintaxis del lenguaje SQL de DocumentDB se amplía para admitir una lógica de aplicación personalizada usando estas funciones definidas por el usuario. Las funciones definidas por el usuario pueden registrarse con DocumentDB y, a continuación, se puede hacer referencia a ellas como parte de una consulta de SQL. De hecho, las UDF están exquisitamente diseñadas para su invocación por parte de las consultas. Como resultado de esta opción, las UDF no tienen acceso al objeto de contexto que los otros tipos de JavaScript (procedimientos y desencadenadores almacenados) tienen. Puesto que las consultas se ejecutan como de solo lectura, pueden ejecutarse en réplicas principales o secundarias. Por consiguiente, las UDF están diseñadas para ejecutarse en réplicas secundarias, a diferencia de otros tipos de JavaScript.

A continuación, vemos un ejemplo de cómo puede registrarse una UDF en la base de datos de Base de datos de documentos, concretamente en una recopilación de documentos.

   
	   UserDefinedFunction regexMatchUdf = new UserDefinedFunction
	   {
	       Id = "REGEX_MATCH",
	       Body = @"function (input, pattern) { 
	                   return input.match(pattern) !== null;
	               };",
	   };
	   
	   UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
	       collectionSelfLink/* link of the parent collection*/, 
	       regexMatchUdf).Result;  
                                                                             
El ejemplo anterior crea una UDF cuyo nombre es `REGEX_MATCH`. Acepta dos valores de cadena JSON, `input` y `pattern`, y comprueba si el primero coincide con el patrón especificado en el segundo mediante la función string.match() de JavaScript.


Ahora podemos usar esta UDF en una consulta de una proyección. Las UDF deben estar calificadas con el prefijo, que distingue mayúsculas de minúsculas, "udf." cuando se las llama desde las consultas.

>[AZURE.NOTE] Antes del 17/03/2015, DocumentDB admitía las llamadas de UDF sin el prefijo "udf." como SELECT REGEX\_MATCH(). Este patrón de llamada está desusado.

**Consultar**

	SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
	FROM Families

**Resultados**

	[
	  {
	    "$1": true
	  }, 
	  {
	    "$1": false
	  }
	]

La UDF también puede usarse en un filtro tal como se muestra en el ejemplo siguiente, calificado igualmente con el prefijo "udf.":

**Consultar**

	SELECT Families.id, Families.address.city
	FROM Families
	WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultados**

	[{
	    "id": "AndersenFamily",
	    "city": "Seattle"
	}]


Básicamente, las UDF son expresiones escalares válidas y pueden usarse en ambas proyecciones y filtros.

Para expandir el poder de las UDF, echemos un vistazo a otro ejemplo con lógica condicional:

	   UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
	   {
	       Id = "SEALEVEL",
	       Body = @"function(city) {
	       		switch (city) {
	       		    case 'seattle':
	       		        return 520;
	       		    case 'NY':
	       		        return 410;
	       		    case 'Chicago':
	       		        return 673;
	       		    default:
	       		        return -1;
	                }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);
	
	
A continuación se muestra un ejemplo que ejerce la UDF.

**Consultar**

	SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
	FROM Families f	

**Resultados**

	 [
	  {
	    "city": "seattle", 
	    "seaLevel": 520
	  }, 
	  {
	    "city": "NY", 
	    "seaLevel": 410
	  }
	]


Al igual que en los ejemplos anteriores se muestran casos, las UDF integran el poder del lenguaje de JavaScript con el lenguaje SQL de DocumentDB para proporcionar una interfaz programable enriquecida a fin de hacer lógica condicional de procedimientos compleja con la ayuda de capacidades en tiempo real de JavaScript integradas.

El lenguaje SQL de Base de datos de documentos proporciona los argumentos a las UDF para cada uno de los documentos del origen en la fase actual (cláusulas WHERE o SELECT) de procesamiento de la UDF. El resultado se incorpora en el proceso de ejecución general perfectamente. Si las propiedades a las que los parámetros UDF hacen referencia no están disponibles en el valor JSON, el parámetro se considera Undefined y así pues, la invocación de UDF se omite por completo. De forma similar, si el resultado de la UDF es Undefined, no se incluye en el resultado.

En resumen, las UDF son excelentes herramientas para hacer lógica de negocios como parte de la consulta.

### Evaluación de operadores
Base de datos de documentos, en virtud de ser una base de datos JSON, establece paralelismos con los operadores de JavaScript y su semántica de evaluación. Aunque Base de datos de documentos intenta conservar la semántica de JavaScript en términos de soporte para JSON, la evaluación de operaciones se desvía en algunas instancias.

En SQL de DocumentDB, a diferencia del lenguaje SQL tradicional, los tipos de valores no suelen conocerse hasta que los valores se recuperan realmente de la base de datos. Para ejecutar consultas de forma eficaz, la mayoría de los operadores tienen requisitos de tipo estrictos.

El lenguaje SQL de DocumentDB no realiza conversiones implícitas a diferencia de JavaScript. Por ejemplo, una consulta como `SELECT * FROM Person p WHERE p.Age = 21` encuentra documentos que contienen una propiedad Age cuyo valor es 21. Cualquier otro documento cuya propiedad Age coincida con la cadena "21", u otras variaciones posiblemente infinitas, como "021", "21,0", "0021", "00021", etc. no producirán coincidencias. Esto es en contraste al JavaScript donde los valores de cadena se convierten de manera implícita en números (en función del operador, por ejemplo, ==). Esta opción es fundamental para conseguir una coincidencia de índices eficaz en el lenguaje SQL de DocumentDB.

## Consultas SQL con parámetros
DocumentDB admite las consultas con parámetros que se expresen con la notación @ ya conocida. El uso de SQL con parámetros permite controlar y evitar de forma sólida la entrada por parte de los usuarios, impidiendo así la exposición accidental de datos a través de la inyección de código SQL.

Por ejemplo, puede escribir una consulta que acepte los apellidos y el estado de la dirección como parámetros y, a continuación, ejecutarla para distintos valores de los parámetros mencionados en función de la entrada del usuario.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Después, esta solicitud puede enviarse a DocumentDB como consulta JSON con parámetros, como se muestra a continuación.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

El argumento para TOP se puede definir mediante el uso de consultas con parámetros, tal como se muestra a continuación.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Los valores de los parámetros pueden ser cualquier tipo de JSON válido (cadenas, números, booleanos, null o incluso matrices o JSON anidado). Además, como DocumentDB no tiene ningún esquema, los parámetros no se validan respecto a ningún tipo.

##Funciones integradas
DocumentDB también admite un número de funciones integradas para operaciones comunes, que se pueden usar dentro de las consultas como funciones definidas por el usuario (UDF).

<table>
<tr>
<td>Funciones matemáticas</td>	
<td>ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN y TAN</td>
</tr>
<tr>
<td>Funciones de comprobación de tipos</td>	
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE</td>
</tr>
<tr>
<td>Funciones de cadena</td>	
<td>CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING y UPPER</td>
</tr>
<tr>
<td>Funciones de matriz</td>	
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH y ARRAY_SLICE</td>
</tr>
<tr>
<td>Funciones espaciales</td>	
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID y ST_ISVALIDDETAILED</td>
</tr>
</table>

Si actualmente utiliza una función definida por el usuario (UDF) para la que ahora hay disponible una función integrada, debe usar la función integrada correspondiente ya que se va a ejecutar más rápidamente y va a ser más eficaz.

### Funciones matemáticas
Las funciones matemáticas realizan un cálculo, basado normalmente en valores de entrada proporcionados como argumentos, y devuelven un valor numérico. Esta es una tabla de las funciones matemáticas integradas admitidas.

<table>
<tr>
<td><strong>Uso</strong></td>
<td><strong>Descripción</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td>	
<td>Devuelve el valor absoluto (positivo) de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td>	
<td>Devuelve el valor entero más pequeño mayor o igual que la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td>	
<td>Devuelve el valor entero más grande menor o igual que la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td>	
<td>Devuelve el exponente de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [,base])</a></td>	
<td>Devuelve el logaritmo natural de la expresión numérica especificada o bien el logaritmo que utiliza la base especificada</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td>	
<td>Devuelve el valor logarítmico de base 10 de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td>	
<td>Devuelve un valor numérico, redondeado al valor entero más cercano.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td>	
<td>Devuelve un valor numérico, truncado al valor entero más cercano.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>	
<td>Devuelve la raíz cuadrada de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">SQUARE (num_expr)</a></td>	
<td>Devuelve el cuadrado de la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>	
<td>Devuelve la potencia de la expresión numérica especificada al valor especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGN (num_expr)</a></td>	
<td>Devuelve el valor de signo (-1, 0, 1) de la expresión numérica especificada.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>	
<td>Devuelve el ángulo, en radianes, cuyo coseno es la expresión numérica especificada; también se denomina arcocoseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>	
<td>Devuelve el ángulo, en radianes, cuyo seno es la expresión numérica especificada. También se denomina arcoseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>	
<td>Devuelve el ángulo, en radianes, cuya tangente es la expresión numérica especificada. También se denomina arcotangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>	
<td>Devuelve el ángulo, en radianes, entre el eje x positivo y el rayo desde el origen hasta el punto (y, x), donde x e y son los valores de las dos expresiones de punto flotante especificadas.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td>	
<td>Devuelve el coseno trigonométrico del ángulo especificado, en radianes, en la expresión especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td>	
<td>Devuelve la cotangente trigonométrica del ángulo especificado, en radianes, en la expresión numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">DEGREES (num_expr)</a></td>	
<td>Devuelve el ángulo correspondiente en grados de un ángulo especificado en radianes.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI ()</a></td>	
<td>Devuelve el valor constante de PI.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANS (num_expr)</a></td>	
<td>Devuelve radianes cuando se especifica una expresión numérica en grados.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td>	
<td>Devuelve el seno trigonométrico del ángulo especificado, en radianes, en la expresión especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td>	
<td>Devuelve la tangente de la expresión de entrada en la expresión especificada.</td>
</tr>

</table>

Por ejemplo, ya puede ejecutar consultas similares a las siguientes:

**Consultar**

    SELECT VALUE ABS(-4)

**Resultados**

    [4]

La principal diferencia entre funciones de DocumentDB y ANSI SQL es que están diseñadas para funcionar bien con datos sin esquemas y datos de esquemas mixtos. Por ejemplo, si tiene un documento donde falta la propiedad de tamaño o tiene un valor no numérico, como "desconocido", se omite el documento, en lugar de devolver un error.

### Funciones de comprobación de tipos
Las funciones de comprobación de tipos permiten comprobar el tipo de una expresión dentro de consultas SQL. Las funciones de comprobación de tipos pueden utilizarse para determinar el tipo de propiedades dentro de los documentos sobre la marcha cuando es variable o desconocido Esta es una tabla de las funciones de comprobación de tipos integradas admitidas.

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es una matriz.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es un valor booleano.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es nulo.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es un número.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es un objeto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es una cadena.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Devuelve un valor booleano que indica si se ha asignado un valor a la propiedad.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es una cadena, número, valor booleano o null.</td>
</tr>

</table>

Con estas funciones, ya puede ejecutar consultas similares a las siguientes:

**Consultar**

    SELECT VALUE IS_NUMBER(-4)

**Resultados**

    [true]

### Funciones de cadena
Las siguientes funciones escalares realizan una operación sobre un valor de entrada de cadena y devuelven una cadena, un valor numérico o un booleano. A continuación se facilita una tabla de funciones de cadena integradas:

Uso|Descripción
---|---
[LENGTH (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Devuelve el número de caracteres de la expresión de cadena especificada.
[CONCAT (str\_expr, str\_expr [, str\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Devuelve una cadena que es el resultado de concatenar dos o más valores de cadena.
[SUBSTRING (str\_expr, num\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Devuelve parte de una expresión de cadena.
[STARTSWITH (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Devuelve un valor booleano que indica si la primera expresión de cadena finaliza con la segunda.
[ENDSWITH (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Devuelve un valor booleano que indica si la primera expresión de cadena finaliza con la segunda.
[CONTAINS (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Devuelve un valor booleano que indica si la primera expresión de cadena contiene la segunda.
[INDEX\_OF (str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Devuelve la posición inicial de la primera aparición de la expresión de la segunda cadena dentro de la primera expresión de cadena especificada, o -1 si no se encuentra la cadena.
[LEFT (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Devuelve la parte izquierda de una cadena con el número especificado de caracteres.
[RIGHT (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Devuelve la parte derecha de una cadena con el número especificado de caracteres.
[LTRIM (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Devuelve una expresión de cadena después de quitar los espacios en blanco iniciales.
[RTRIM (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Devuelve una expresión de cadena después de truncar todos los espacios en blanco finales.
[LOWER (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Devuelve una expresión de cadena después de convertir los datos de caracteres en mayúsculas a minúsculas.
[UPPER (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Devuelve una expresión de cadena después de convertir datos de caracteres en minúsculas a mayúsculas.
[REPLACE (str\_expr, str\_expr, str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Reemplaza todas las apariciones de un valor de cadena especificado por otro valor de cadena.
[REPLICATE (str\_expr, num\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Repite un valor de cadena un número especificado de veces.
[REVERSE (str\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Devuelve el orden inverso de un valor de cadena.

Con estas funciones, ya puede ejecutar consultas similares a las siguientes. Por ejemplo, puede devolver el nombre de familia en mayúsculas del modo siguiente:

**Consultar**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultados**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

O concatenar cadenas como en este ejemplo:

**Consultar**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultados**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Las funciones de cadena también pueden usarse en la cláusula WHERE para filtrar los resultados, al igual que en el ejemplo siguiente:

**Consultar**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultados**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### Funciones de matriz
Las siguientes funciones escalares realizan una operación en un valor de entrada de matriz y devolver un valor numérico, booleano o de matriz. A continuación se facilita una tabla de funciones de matriz integradas:

Uso|Descripción
---|---
[ARRAY\_LENGTH (arr\_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Devuelve el número de elementos de la expresión de matriz especificada.
[ARRAY\_CONCAT (arr\_expr, arr\_expr [, arr\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Devuelve una matriz que es el resultado de concatenar dos o más valores de la matriz.
[ARRAY\_CONTAINS (arr\_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Devuelve un valor booleano que indica si la matriz contiene el valor especificado.
[ARRAY\_SLICE (arr\_expr, num\_expr [, num\_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Devuelve parte de una expresión de matriz.

Las funciones de matriz pueden usarse para manipular matrices en JSON. Por ejemplo, a continuación se facilita una consulta que devuelve todos los documentos en los que uno de los elementos primarios es "Robin Wakefield".

**Consultar**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Este es otro ejemplo que usa ARRAY\_LENGTH para obtener el número de hijos por familia.

**Consultar**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultados**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### Funciones espaciales

DocumentDB admite las siguientes funciones integradas de Open Geospatial Consortium (OGC) para realizar consultas geoespaciales. Para obtener más información sobre la compatibilidad geoespacial en DocumentDB, consulte [Uso de datos geoespaciales en Azure DocumentDB](documentdb-geospatial.md).

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devuelve la distancia entre las dos expresiones de punto de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devuelve una expresión booleana que indica si el punto de GeoJSON especificado en el primer argumento está dentro del polígono GeoJSON en el segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devuelve un valor booleano que indica si la expresión de punto o polígono GeoJSON especificada es válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devuelve un valor JSON que contiene un valor booleano si la expresión de punto o polígono GeoJSON especificada es válida; si no es válida, devuelve también el motivo en forma de valor de cadena.</td>
</tr>
</table>

Las funciones espaciales pueden usarse para realizar consultas de proximidad con datos espaciales. Por ejemplo, aquí hay una consulta que devuelve todos los documentos de la familia que estén dentro de un radio de 30 km de la ubicación especificada mediante la función integrada ST\_DISTANCE.

**Consultar**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Si incluye la indexación espacial en la directiva de indexación, las "consultas de distancia" se atenderán eficazmente a través del índice. Para obtener más detalles sobre la indexación espacial, consulte la sección siguiente. Aunque no tenga un índice espacial para las rutas de acceso especificadas, aún podrá realizar consultas espaciales mediante la especificación del encabezado de solicitud `x-ms-documentdb-query-enable-scan` con el valor establecido en "true". En. NET, para hacerlo es preciso pasar el argumento **FeedOptions** opcional en consultas en las que [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) está establecido en true.

ST\_WITHIN puede usarse para comprobar si un punto se encuentra dentro de un polígono. Normalmente, los polígonos se usan para representar límites, como códigos postales, límites estatales o formaciones naturales. Una vez más, si incluye la indexación espacial en la directiva de indexación, las consultas "interiores" se atenderán eficazmente a través del índice.

Los argumentos de polígono de ST\_WITHIN solo pueden contener un anillo individual, es decir, los polígonos no deben contener orificios. En [Límites y cuotas de DocumentDB](documentdb-limits.md) encontrará el número máximo de puntos permitidos en un polígono para una consulta ST\_WITHIN.

**Consultar**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
    	'type':'Polygon', 
    	'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] De forma parecida a cómo funcionan los tipos no coincidentes en una consulta de DocumentDB, si el valor de ubicación especificado en cualquier argumento está mal formado o no es válido, se evaluará como **sin definir** y el documento evaluado se omite de los resultados de la consulta. Si la consulta no devuelve resultados, ejecute ST\_ISVALIDDETAILED para depurarla y saber por qué el tipo espacial no es válido.

ST\_ISVALID y ST\_ISVALIDDETAILED pueden usarse para comprobar si un objeto espacial es válido. Por ejemplo, la consulta siguiente comprueba la validez de un punto con un valor de latitud fuera del intervalo (-132,8). ST\_ISVALID devuelve solo un valor booleano y ST\_ISVALIDDETAILED devuelve el valor booleano y una cadena que contiene el motivo por el que se considera no válida.

**Consultar**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Estas funciones también se pueden usar para validar polígonos. Por ejemplo, aquí usamos ST\_ISVALIDDETAILED para validar un polígono que no está cerrado.

**Consultar**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
    	[ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
    	]]})

**Resultados**

    [{
       "$1": { 
      	  "valid": false, 
      	  "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
      	}
    }]
    
Que contiene funciones espaciales y sintaxis de SQL para DocumentDB. Ahora veamos cómo funciona la consulta LINQ y cómo interactúa con la sintaxis que hemos visto hasta ahora.

## LINQ para lenguaje SQL de Base de datos de documentos
LINQ es un modelo de programación de .NET que expresa cálculos como consultas de secuencias de objetos. Base de datos de documentos proporciona una biblioteca del cliente para interactuar con LINQ facilitando una conversión entre objetos JSON y .NET y una asignación a partir de un subconjunto de consultas de LINQ a consultas de Base de datos de documentos.

En la imagen que se muestra a continuación vemos la arquitectura de consultas compatibles con LINQ que usa Base de datos de documentos. Con el cliente de DocumentDB, los desarrolladores pueden crear un objeto **IQueryable** que consulta directamente al proveedor de consultas de DocumentDB que, a continuación, convierte la consulta de LINQ en una consulta de DocumentDB. La consulta pasa entonces al servidor de Base de datos de documentos para recuperar un conjunto de resultados en formato JSON. Los resultados devueltos se deserializan en una secuencia de objetos .NET en el cliente.

![Arquitectura de consultas compatibles con LINQ que usa DocumentDB - sintaxis SQL, lenguaje de consulta JSON, conceptos de base de datos y consultas SQL][1]
 


### Asignación de .NET y JSON
La asignación entre objetos .NET y documentos JSON es natural (cada campo del miembro de datos se asigna a un objeto JSON, donde el nombre del campo se asigna a la parte "clave" del objeto y la parte de "valor" se asigna de forma recursiva a la parte de valor del objeto. Considere el ejemplo siguiente. El objeto Familia creado se asigna al documento JSON como se muestra a continuación. Y viceversa, el documento JSON se reasigna de nuevo a un objeto .NET.

**Clase de C#**

	public class Family
	{
	    [JsonProperty(PropertyName="id")]
	    public string Id;
	    public Parent[] parents;
	    public Child[] children;
	    public bool isRegistered;
	};
	
	public struct Parent
	{
	    public string familyName;
	    public string givenName;
	};
	
	public class Child
	{
	    public string familyName;
	    public string givenName;
	    public string gender;
	    public int grade;
	    public List<Pet> pets;
	};
	
	public class Pet
	{
	    public string givenName;
	};
	
	public class Address
	{
	    public string state;
	    public string county;
	    public string city;
	};
	
	// Create a Family object.
	Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
	Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
	Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
	Pet pet = new Pet { givenName = "Fluffy" };
	Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
	Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", 
	            "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	          "familyName": "Miller", 
	          "givenName": "Lisa", 
	          "gender": "female", 
	          "grade": 8 
	        }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	};



### LINQ para traducción de lenguaje SQL
El proveedor de consulta de Base de datos de documentos realiza una mejor opción de asignación desde una consulta de LINQ a una consulta de Base de datos de documentos. En la siguiente descripción, asumimos la familiaridad básica del lector con LINQ.

En primer lugar, para el sistema de tipos, admitimos todos los tipos primitivos JSON (tipos numéricos, booleanos, de cadena y null). Solo se admiten estos tipos JSON. Se admiten las siguientes expresiones escalares.

-	Valores constantes: entre estos se incluyen los valores constantes de los tipos de datos primitivos durante la evaluación de la consulta.

-	Expresiones de índice de propiedad o matriz: estas expresiones hacen referencia a la propiedad de un objeto o a un elemento de matriz.

		family.Id;
		family.children[0].familyName;
		family.children[0].grade;
		family.children[n].grade; //n is an int variable

-	Expresiones aritméticas: entre estas se incluyen expresiones aritméticas comunes o valores numéricos y booleanos. Para ver la lista completa, consulte la especificación de SQL.

		2 * family.children[0].grade;
		x + y;

-	Expresión de comparación de cadenas: entre estas se incluye la comparación de un valor de cadena con algún valor de cadena constante.
 
		mother.familyName == "Smith";
		child.givenName == s; //s is a string variable

-	Expresión de creación de objetos o matrices: estas expresiones devuelven un objeto de tipo de valor compuesto o tipo anónimo o una matriz de estos objetos. Estos valores se pueden anidar.

		new Parent { familyName = "Smith", givenName = "Joe" };
		new { first = 1, second = 2 }; //an anonymous type with 2 fields              
		new int[] { 3, child.grade, 5 };

### Lista de los operadores LINQ admitidos
La siguiente es una lista de los operadores LINQ admitidos en el proveedor LINQ incluido en el SDK de .NET de DocumentDB.

-	**Select**: Las proyecciones se traducen en la instrucción SQL SELECT, incluida la construcción de objetos.
-	**Where**: Los filtros se traducen a la instrucción SQL WHERE y admiten la traducción entre && , || y ! a los operadores SQL.
-	**SelectMany**: Permite desenredar las matrices a la cláusula SQL JOIN. Se puede usar para encadenar/anidar expresiones para filtrar los elementos de la matriz.
-	**OrderBy y OrderByDescending**: Se traduce a ORDER BY ascendente/descendente:
-	**CompareTo**: Se traduce a las comparaciones de intervalos. Se usa frecuentemente para las cadenas, debido a que no son comparables en .NET.
-	**Take**: Se traduce a la instrucción SQL TOP para limitar los resultados desde una consulta.
-	**Funciones matemáticas**: Admite la traducción desde Abs de .NET, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate a las funciones SQL integradas equivalentes.
-	**Funciones de cadena**: Admite la traducción desde Concat .NET, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper a las funciones SQL integradas equivalentes.
-	**Funciones de matriz**: Admite la traducción desde Concat .NET, Contains y Count a las funciones SQL integradas equivalentes.
-	**Funciones de extensión geoespacial**: Admite la traducción desde los métodos auxiliares Distance, Within, IsValid y IsValidDetailed a las funciones SQL integradas equivalentes.
-	**Función de extensión de función definida por el usuario**: Admite la traducción desde el método auxiliar UserDefinedFunctionProvider.Invoke a la correspondiente función definida por el usuario.
-	**Varios**: Admite la traducción de los operadores condicionales y de fusión. Puede traducir Contains a String CONTAINS, ARRAY\_CONTAINS o SQL IN, según el contexto.

### Operadores de consulta SQL
A continuación, vemos algunos ejemplos que ilustran la traducción de algunos de los operadores de consulta de LINQ estándar a consultas de Base de datos de documentos.

#### Operador Select
La sintaxis es `input.Select(x => f(x))`, donde `f` es una expresión escalar.

**Expresión lambda de LINQ**

	input.Select(family => family.parents[0].familyName);

**SQL**

	SELECT VALUE f.parents[0].familyName
	FROM Families f



**Expresión lambda de LINQ**

	input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL**

	SELECT VALUE f.children[0].grade + c
	FROM Families f 



**Expresión lambda de LINQ**

	input.Select(family => new
	{
	    name = family.children[0].familyName,
	    grade = family.children[0].grade + 3
	});


**SQL**

	SELECT VALUE {"name":f.children[0].familyName, 
	              "grade": f.children[0].grade + 3 }
	FROM Families f



#### Operador SelectMany
La sintaxis es `input.SelectMany(x => f(x))`, donde `f` es una expresión escalar que devuelve un tipo de colección.

**Expresión lambda de LINQ**

	input.SelectMany(family => family.children);

**SQL**

	SELECT VALUE child
	FROM child IN Families.children



#### Operador Where
La sintaxis es `input.Where(x => f(x))`, donde `f` es una expresión escalar que devuelve un valor booleano.

**Expresión lambda de LINQ**

	input.Where(family=> family.parents[0].familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith" 



**Expresión lambda de LINQ**

	input.Where(
	    family => family.parents[0].familyName == "Smith" && 
	    family.children[0].grade < 3);

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"
	AND f.children[0].grade < 3


### Composición de consultas SQL
Los operadores anteriores pueden ser compuestos para formar consultas más eficaces. Como DocumentDB admite recopilaciones anidadas, la composición puede concatenarse o anidarse.

#### Concatenación 

La sintaxis es `input(.|.SelectMany())(.Select()|.Where())*`. Una consulta concatenada puede empezar por una consulta `SelectMany` opcional seguida de varios operadores `Select` o `Where`.


**Expresión lambda de LINQ**

	input.Select(family=>family.parents[0])
	    .Where(familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"



**Expresión lambda de LINQ**

	input.Where(family => family.children[0].grade > 3)
	    .Select(family => family.parents[0].familyName);

**SQL**

	SELECT VALUE f.parents[0].familyName
	FROM Families f
	WHERE f.children[0].grade > 3



**Expresión lambda de LINQ**

	input.Select(family => new { grade=family.children[0].grade}).
	    Where(anon=> anon.grade < 3);
            
**SQL**

	SELECT *
	FROM Families f
	WHERE ({grade: f.children[0].grade}.grade > 3)



**Expresión lambda de LINQ**

	input.SelectMany(family => family.parents)
	    .Where(parent => parents.familyName == "Smith");

**SQL**

	SELECT *
	FROM p IN Families.parents
	WHERE p.familyName = "Smith"



#### Anidamiento

La sintaxis es `input.SelectMany(x=>x.Q())`, donde Q es un operador `Select`, `SelectMany` o `Where`.

En una consulta anidada, la consulta interna se aplica a cada uno de los elementos de la recopilación externa. Una característica importante es que la consulta interna puede hacer referencia a los campos de los elementos de la recopilación externa como las autocombinaciones.

**Expresión lambda de LINQ**

	input.SelectMany(family=> 
	    family.parents.Select(p => p.familyName));

**SQL**

	SELECT VALUE p.familyName
	FROM Families f
	JOIN p IN f.parents


**Expresión lambda de LINQ**

	input.SelectMany(family => 
	    family.children.Where(child => child.familyName == "Jeff"));
            
**SQL**

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = "Jeff"



**Expresión lambda de LINQ**
            
	input.SelectMany(family => family.children.Where(
	    child => child.familyName == family.parents[0].familyName));

**SQL**

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = f.parents[0].familyName


## Ejecución de consultas SQL
DocumentDB expone recursos mediante la API de REST, que puede invocar cualquier lenguaje capaz de realizar solicitudes de HTTP/HTTPS. Además, Base de datos de documentos ofrece bibliotecas de programación para varios lenguajes populares como .NET, Node.js, JavaScript y Python. La API de REST y las diversas bibliotecas admiten la realización de consultas a través de SQL. El SDK de .NET admite la realización de consultas de LINQ, además del lenguaje SQL.

En los ejemplos siguientes se muestra cómo crear una consulta y enviarla a una cuenta de la base de datos de Base de datos de documentos.

### API de REST
Base de datos de documentos ofrece un modelo de programación RESTful sobre HTTP. Las cuentas de la base de datos pueden aprovisionarse usando una suscripción de Azure. El modelo de recursos de DocumentDB consta de un conjunto de recursos en una cuenta de la base de datos, cada uno de los cuales se puede dirigir mediante un URI lógico y estable. En este documento, se hace referencia a un conjunto de recursos como fuente. Una cuenta de la base de datos consta de un conjunto de bases de datos, cada una incluyendo varias recopilaciones que, a su vez, contienen documentos, UDF y otros tipos de recursos.

El modelo de interacción básico con estos recursos se lleva a cabo a través de los verbos de HTTP GET, PUT, POST y DELETE con su interpretación estándar. El verbo POST se usa para la creación de un nuevo recurso, para ejecutar un procedimiento almacenado o para emitir una consulta de Base de datos de documentos. Las consultas siempre son operaciones de solo lectura sin efectos secundarios.

En los ejemplos siguientes se muestra POST para una consulta de DocumentDB realizada a una recopilación que incluye los dos documentos de ejemplo que hemos revisado hasta el momento. La consulta tiene un filtro sencillo por la propiedad de nombre JSON. Fíjese en el uso de los encabezados `x-ms-documentdb-isquery` y Content-Type: `application/query+json` para denotar que la operación es una consulta.


**Solicitud**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
	

**Resultados**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
	x-ms-item-count: 1
	x-ms-request-charge: 0.32
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "id":"AndersenFamily",
	         "lastName":"Andersen",
	         "parents":[  
	            {  
	               "firstName":"Thomas"
	            },
	            {  
	               "firstName":"Mary Kay"
	            }
	         ],
	         "children":[  
	            {  
	               "firstName":"Henriette Thaulow",
	               "gender":"female",
	               "grade":5,
	               "pets":[  
	                  {  
	                     "givenName":"Fluffy"
	                  }
	               ]
	            }
	         ],
	         "address":{  
	            "state":"WA",
	            "county":"King",
	            "city":"seattle"
	         },
	         "_rid":"u1NXANcKogEcAAAAAAAAAA==",
	         "_ts":1407691744,
	         "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
	         "_etag":"00002b00-0000-0000-0000-53e7abe00000",
	         "_attachments":"_attachments\/"
	      }
	   ],
	   "count":1
	}


En el segundo ejemplo se muestra una consulta más compleja que devuelve varios resultados de la combinación.

**Solicitud**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json
	
    {      
        "query": "SELECT 
				     f.id AS familyName, 
				     c.givenName AS childGivenName, 
				     c.firstName AS childFirstName, 
				     p.givenName AS petName 
				  FROM Families f 
				  JOIN c IN f.children 
				  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultados**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
	x-ms-item-count: 1
	x-ms-request-charge: 7.84
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "familyName":"AndersenFamily",
	         "childFirstName":"Henriette Thaulow",
	         "petName":"Fluffy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Goofy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Shadow"
	      }
	   ],
	   "count":3
	}


Si los resultados de una consulta no caben en una sola página, la API de REST devuelve un token de continuación a través del encabezado de respuesta `x-ms-continuation-token`. Los clientes pueden paginar los resultados incluyendo el encabezado en resultados posteriores. El número de resultados por página también se puede controlar a través del encabezado numérico `x-ms-max-item-count`.

Para administrar la directiva de coherencia de datos para consultas, use el encabezado `x-ms-consistency-level` como todas las solicitudes de la API de REST. Para que la sesión sea coherente, también es necesario enviar el último encabezado de cookie `x-ms-session-token` en la solicitud de la consulta. Tenga en cuenta que la directiva de índices de la recopilación consultada también puede afectar a la coherencia de los resultados de la consulta. En el caso de las recopilaciones, con la configuración de la directiva de índices predeterminada, el índice siempre es actual con el contenido del documento y los resultados de la consulta coincidirán con la coherencia elegida para los datos. Si la directiva de índices se suaviza para los perezosos, las consultas pueden devolver resultados obsoletos. Para obtener más información, consulte [Niveles de coherencia de Base de datos de documentos][consistency-levels].

Si la directiva de índices configurada de la recopilación no puede admitir la consulta especificada, el servidor de Base de datos de documentos devuelve 400 de "solicitud incorrecta". Esto se devuelve para las consultas por rango en rutas de acceso configuradas para búsquedas hash (igualdad) y rutas de acceso excluidas de forma explícita de los índices. Se puede especificar el encabezado `x-ms-documentdb-query-enable-scan` para permitir que la consulta realice un examen si algún índice no está disponible.

### SDK de C# (.NET)
El SDK de .NET admite la realización de consultas de LINQ y SQL. En el ejemplo siguiente se muestra cómo realizar la consulta de filtro simple incluida anteriormente en este documento.


	foreach (var family in client.CreateDocumentQuery(collectionLink, 
	    "SELECT * FROM Families f WHERE f.id = "AndersenFamily""))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

	foreach (var family in (
	    from f in client.CreateDocumentQuery(collectionLink)
	    where f.Id == "AndersenFamily"
	    select f))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in client.CreateDocumentQuery(collectionLink)
	    .Where(f => f.Id == "AndersenFamily")
	    .Select(f => f))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


En este ejemplo se comparan dos propiedades para igualdad en cada documento y se usan proyecciones anónimas.


	foreach (var family in client.CreateDocumentQuery(collectionLink,
	    @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
	    FROM Families f 
	    WHERE f.address.city = f.address.state"))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
	foreach (var family in (
	    from f in client.CreateDocumentQuery<Family>(collectionLink)
	    where f.address.city == f.address.state
	    select new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .Where(f => f.address.city == f.address.state)
	    .Select(f => new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


En el ejemplo siguiente se muestran combinaciones, expresadas a través de SelectMany de LINQ.


	foreach (var pet in client.CreateDocumentQuery(collectionLink,
	      @"SELECT p
	        FROM Families f 
	             JOIN c IN f.children 
	             JOIN p in c.pets 
	        WHERE p.givenName = ""Shadow"""))
	{
	    Console.WriteLine("\tRead {0} from SQL", pet);
	}
	
	// Equivalent in Lambda expressions
	foreach (var pet in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .SelectMany(f => f.children)
	    .SelectMany(c => c.pets)
	    .Where(p => p.givenName == "Shadow"))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", pet);
	}



El cliente de .NET se itera automáticamente a través de todas las páginas de resultados de la consulta de los bloques foreach, como se muestra anteriormente. Las opciones de consulta especificadas en la sección de la API de REST también están disponibles en el SDK de .NET mediante las clases `FeedOptions` y `FeedResponse` del método CreateDocumentQuery. El número de páginas se puede controlar con el valor `MaxItemCount`.

Los desarrolladores también pueden controlar de forma explícita la paginación mediante la creación de `IDocumentQueryable` con el objeto `IQueryable` y, a continuación, la lectura de los valores ` ResponseContinuationToken` y volviéndolos a pasar como `RequestContinuationToken` en `FeedOptions`. Se puede establecer `EnableScanInQuery` para habilitar los exámenes cuando la directiva de indexación configurada no pueda admitir la consulta.

Consulte los [ejemplos de .NET de DocumentDB](https://github.com/Azure/azure-documentdb-net) para obtener más casos que contengan consultas.

### API del servidor de JavaScript 
Base de datos de documentos proporciona un modelo de programación para ejecutar una lógica de aplicación basada en JavaScript directamente en las recopilaciones usando procedimientos y desencadenadores almacenados. La lógica de JavaScript registrada en un nivel de recopilación puede emitir operaciones de base de datos en las operaciones de los documentos de la recopilación especificada. Estas operaciones se incluyen en transacciones ACID ambientales.

En el ejemplo siguiente se muestra cómo usar queryDocuments en la API del servidor de JavaScript para realizar consultas a partir de procedimientos y desencadenadores almacenados dentro.


	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();
	    var collectionLink = collectionManager.getSelfLink()
	
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        { name: name, author: author },
	        function (err, documentCreated) {
	            if (err) throw new Error(err.message);
	
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function (err, matchingDocuments) {
	                    if (err) throw new Error(err.message);
	context.getResponse().setBody(matchingDocuments.length);
	
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);
	                    }
	                })
	        });
	}


##Referencias
1.	[Introducción a Base de datos de documentos de Azure][introduction]
2.	[Especificación de SQL de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.	[Ejemplos de .NET de DocumentDB](https://github.com/Azure/azure-documentdb-net)
4.	[Niveles de coherencia de Base de datos de documentos][consistency-levels]
5.	ANSI SQL 2011 [http://www.iso.org/iso/iso\_catalogue/catalogue\_tc/catalogue\_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.	JSON [http://json.org/](http://json.org/)
7.	Especificación de JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.	LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.	Técnicas de evaluación de consultas para bases de datos de gran tamaño [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10.	Procesamiento de consultas en sistemas paralelos de bases de datos relacionales, IEEE Computer Society Press, 1994
11.	Lu, Ooi, Tan, Procesamiento de consultas en sistemas paralelos de bases de datos relacionales, IEEE Computer Society Press, 1994.
12.	Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13.     G. Graefe. El marco de cascadas para la optimización de consultas. IEEE Data Eng. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 

<!---HONumber=AcomDC_0128_2016-->