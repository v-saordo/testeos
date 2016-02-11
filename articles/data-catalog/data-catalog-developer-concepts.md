<properties
   pageTitle="Conceptos para desarrolladores del Catálogo de datos de Azure"
   description="Introducción a los conceptos clave en el modelo conceptual del Catálogo de datos de Azure, como se expone mediante la API de REST del catálogo."
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags 
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/27/2015"
   ms.author="derrickv"/>

# Conceptos para desarrolladores del Catálogo de datos de Azure

**Catálogo de datos de Microsoft Azure** es un servicio en la nube totalmente administrado que proporciona capacidades de detección de origen de datos y para metadatos de origen de datos de micromecenazgo. Los desarrolladores pueden usar el servicio a través de sus API de REST. Comprender los conceptos que se implementa en el servicio es importante para los desarrolladores para integrarse correctamente con **Catálogo de datos de Azure**.

## Conceptos clave

El modelo conceptual **Catálogo de datos de Azure** se basa en cuatro conceptos clave: el **catálogo**, los **usuarios**, los **recursos** y las **anotaciones**.

![concepto][1]

*Figura 1: Modelo conceptual simplificado del Catálogo de datos de Azure*

### Catálogo

Un **catálogo** es el contenedor de nivel superior para todos los metadatos que se van a almacenar en una organización. Se permite un **catálogo** por cuenta de Azure. Los catálogos están vinculados a una suscripción de Azure, pero solo se puede crear un **catálogo** para una sola cuenta de Azure, aunque una cuenta puede tener varias suscripciones.

Un catálogo contiene **usuarios** y **recursos**.

### Usuarios

Los usuarios son entidades de seguridad que tienen permisos para realizar acciones (buscar en el catálogo, agregar, editar o eliminar elementos, etc...) en el catálogo.

Existen diferentes roles que un usuario puede tener. Para obtener más información sobre las funciones, consulte la sección Funciones y autorización.

Se pueden agregar usuarios individuales y grupos de seguridad.

Catálogo de datos de Azure usa Azure Active Directory para la administración de identidades y acceso. Cada usuario de catálogo debe ser un miembro de Active Directory para la cuenta.

### Recursos

Un **catálogo** contiene recursos de datos. Los **Recursos** son la unidad de granularidad que administra el catálogo.

La granularidad de un recurso varía según el origen de datos. Para SQL Server o Base de datos de Oracle, un recurso puede ser una tabla o una vista. Para SQL Server Analysis Services, un recurso puede ser una Medida, una Dimensión o un indicador clave de rendimiento (KPI). Para SQL Server Reporting Services, un recurso es un Informe.

Un **Recurso** es el elemento que se agrega o quita de un Catálogo. Es la unidad del resultado que recibimos de la **Búsqueda**.

Un **Recurso** se compone de su nombre, ubicación y tipo, así como de las anotaciones que lo describen.

### Anotaciones

Las anotaciones son elementos que representan los metadatos acerca de los recursos.

Como ejemplos de anotaciones, se encuentran la descripción, las etiquetas, el esquema, la documentación, etc. En la sección de modelo de objeto del recurso se encuentra una lista completa de los tipos de recursos y anotaciones.

## Anotaciones de micromecenazgo y perspectiva del usuario (multiplicidad de opinión)

Un aspecto clave del Catálogo de datos de Azure es cómo admite el micromecenazgo de los metadatos en el sistema. En contraposición con un enfoque de wiki (en el que solo hay una opinión y el último escritor es el que gana), el modelo del Catálogo de datos de Azure permite escribir varias opiniones en el sistema.

Este enfoque refleja el mundo real de los datos empresariales, en el que distintos usuarios pueden tener distintas perspectivas en un recurso:

-	Un administrador de base de datos puede proporcionar información sobre los acuerdos de nivel de servicio o la ventana de procesamiento disponible para las operaciones de ETL masivas
-	Un administrador de datos puede proporcionar información sobre los procesos de negocio a los que se aplica el recurso, o las clasificaciones que la empresa le ha aplicado
-	Un analista financiero puede proporcionar información acerca de cómo se usan los datos durante las tareas de informes de final de período

Para admitir este ejemplo, cada usuario (el DBA, el ddministrador de datos y el analista) puede agregar una descripción a una única tabla que se ha registrado en el catálogo. Todas las descripciones se mantienen en el sistema y en el portal del Catálogo de datos de Azure se muestran todas las descripciones.

Este patrón se aplica a la mayoría de los elementos del modelo de objetos. Esta es la razón por la que los tipos de objetos de la carga de JSON son a menudo matrices de las propiedades en las que se puede esperar un singleton.

Por ejemplo, en la raíz del recurso se encuentra una matriz de objetos de descripción. La propiedad de matriz se denomina "descriptions". Un objeto de descripción tiene tres propiedades, description, tags y friendlyName. El patrón es que cada usuario que escribe una o varias de estas propiedades obtiene un objeto de descripción creado para los valores proporcionados por el usuario.

A continuación, la experiencia de usuario puede elegir cómo mostrar la combinación. A continuación se muestran tres modelos diferentes para mostrar.

-	El modelo más simple es "Show All". En este patrón se muestran todos los objetos en algún tipo de vista de lista. Esto es lo que hace la experiencia del usuario del portal de Catálogo de datos de Azure por la descripción.
-	Otro patrón es "Merge". En este patrón todos los valores de los distintos usuarios se combinan, y se eliminan los duplicados. Ejemplos de este patrón en la experiencia del usuario del portal de Catálogo de datos de Azure son las etiquetas y las propiedades de expertos.
-	Un tercer patrón es "el último escritor gana". En este patrón se muestra solo el valor más reciente escrito. friendlyName es un ejemplo de este patrón.

## Modelo de objeto de recurso

Como se mencionó en la sección Conceptos clave, el modelo de objetos **Catálogo de datos de Azure** incluye elementos, que pueden ser recursos o anotaciones. Los elementos tienen propiedades, que pueden ser optional o required. Algunas propiedades se aplican a todos los elementos. Algunas propiedades se aplican a todos los recursos. Algunas propiedades se aplican solo a tipos de recursos específicos.

### Propiedades comunes

Estas propiedades se aplican a todos los tipos de recursos de raíz y a todos los tipos de anotación.

> [AZURE.NOTE] Las propiedades cuyos nombres comienzan por un carácter de subrayado doble son tipos de sistema.

<table><tr><td><b>Nombre de propiedad</b></td><td><b>Tipo de datos</b></td><td><b>Comentarios</b></td></tr><tr><td>modifiedTime</td><td>DateTime</td><td>La última vez que se modificó la raíz. Esta la establece el cliente. (El servidor no mantiene este valor).</td></tr><tr><td>__id</td><td>Cadena</td><td>Identificador del elemento (solo lectura). Se garantiza que este identificador es único para el activo de un catálogo.</td></tr><tr><td>__type</td><td>Cadena</td><td>El tipo de recurso (solo lectura)</td></tr><tr><td>__creatorId</td><td>String</td><td>Cadena usada por el creador del recurso para identificar de manera exclusiva el recurso. </td></tr></table>

### Propiedades de raíz comunes

Estas propiedades se aplican a todos los tipos de recursos de raíz.

<table><tr><td><b>Nombre de propiedad</b></td><td><b>Tipo de datos</b></td><td><b>Comentarios</b></td></tr><tr><td>name</td><td>String</td><td>Un nombre derivado de la información de ubicación del origen de datos</td></tr><tr><td>dsl</td><td>Ubicación del origen de datos</td><td>Describe el origen de datos de forma exclusiva y es uno de los identificadores del recurso. (Consulte la sección de identidad dual). La estructura del dsl varía según el tipo de origen.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Más información sobre el tipo de recurso.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Describe el usuario que registró más recientemente este recurso. Contiene tanto el identificador único para el usuario (upn) como un nombre para mostrar (lastName y firstName).</td></tr><tr><td>lastRegisteredTime</td><td>dateTime</td><td>La última vez que se registró este recurso en el catálogo.</td></tr><tr><td>containerId</td><td>Cadena</td><td>Id. del activo de contenedor para el origen de datos. Esta propiedad no se admite para el tipo de contenedor.</td></tr></table>

### Tipos de recursos de raíz

Los tipos de recursos de raíz son aquellos que representan los distintos tipos de recursos de datos que se pueden registrar en el catálogo.

<table><tr><td><b>Tipo de recurso</b></td><td><b>Propiedades adicionales</b></td><td><b>Tipo de datos</b></td><td><b>Comentarios</b></td></tr><tr><td>Tabla</td><td></td><td></td><td>Una tabla representa datos tabulares. Aquí se podría incluir una tabla SQL, una vista SQL, una tabla tabular de Analysis Services, una dimensión multidimensional de Analysis Services, una tabla de Oracle, etc. ...   </td></tr><tr><td>Measure</td><td></td><td></td><td>Este tipo representa una medida de Analysis Services.</td></tr><tr><td></td><td>Measure</td><td>Columna</td><td>Metadatos que describen la medida</td></tr><tr><td></td><td>isCalculated </td><td>Booleano</td><td>Especifica si se calcula la medida o no.</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td>Contenedor físico de medida</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td>Una expresión numérica MDX o un cálculo que devuelve el valor objetivo del KPI.</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td>Una expresión numérica MDX que devuelve el valor real del KPI.</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td>Una expresión MDX que representa el estado del KPI en un punto especificado en el tiempo.</td></tr><tr><td></td><td>trendExpression</td><td>Cadena</td><td>Una expresión MDX que evalúa el valor del KPI en el tiempo. La tendencia puede ser cualquier criterio basado en el tiempo que sea útil en un contexto empresarial específico.</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td>contenedor físico de medida</td></tr><tr><td>Informe</td><td></td><td></td><td>Este tipo representa un informe de SQL Server Reporting Services </td></tr><tr><td></td><td>CreatedBy</td><td>String</td><td></td></tr><tr><td></td><td>CreatedDate</td><td>Cadena</td><td></td></tr><tr><td>Contenedor</td><td></td><td></td><td>Este tipo representa un contenedor de otros activos, como una base de datos SQL, un contenedor de blobs de Azure o un modelo de Analysis Services.</td></tr></table>

### Tipos de anotación

Los tipos de anotación representan tipos de metadatos que se pueden asignar a otros tipos dentro del catálogo.

<table><tr><td><b>Tipo de anotación</b></td><td><b>Propiedades adicionales</b></td><td><b>Tipo de datos</b></td><td><b>Comentarios</b></td></tr><tr><td>Descripción</td><td></td><td></td><td>Cada usuario del sistema puede agregar sus propias etiquetas y descripciones. Solo ese usuario puede editar el objeto de la descripción. (Los administradores y los propietarios de recursos pueden eliminar el objeto de la descripción, pero no editarlo). El sistema los mantiene por separado. Por lo tanto, hay una matriz de descripciones en cada recurso (una para cada usuario que ha contribuido con sus conocimientos sobre el recurso, además de posiblemente uno que contenga información derivada del origen de datos).</td></tr><tr><td></td><td>friendlyName</td><td>cadena</td><td>Un nombre descriptivo que se puede usar en lugar del nombre derivado del origen de datos. Esto es útil para mostrar y para realizar búsquedas.</td></tr><tr><td></td><td>etiquetas</td><td>string[]</td><td>Una matriz de etiquetas para el recurso</td></tr><tr><td></td><td>description</td><td>cadena</td><td>una descripción breve (de entre 2 y 3 líneas) del recurso</td></tr><tr><td>Esquema</td><td></td><td></td><td>El esquema describe la estructura de los datos. Enumera los nombres, tipos ,etc., de atributo (es decir, columna, atributo, campo, etc.), así como otros metadatos. Esta información se deriva del origen de datos. Normalmente en un recurso se encuentra un esquema.</td></tr><tr><td></td><td>columnas</td><td>Column[]</td><td>Una matriz de objetos de columna. Describen la columna con información obtenida a partir del origen de datos.</td></tr><tr><td>SchemaDescription</td><td></td><td></td><td>Contiene una descripción y un conjunto de etiquetas para cada atributo definido en el esquema. Cada usuario del sistema puede agregar sus propias etiquetas y descripciones. Solo puede editar el objeto de la descripción ese usuario. (Los administradores y los propietarios de recursos pueden eliminar el objeto SchemaDescription, pero no editarlo). El sistema los mantiene por separado. Por lo tanto, hay una matriz de objetos SchemaDescription en cada recurso (uno para cada usuario que ha contribuido con sus conocimientos acerca de los atributos, además de posiblemente uno que contiene información derivada del origen de datos). SchemaAttributes está enlazado en líneas generales al esquema para que pueda dejar de estar sincronizado, es decir, SchemaDescription podría describir las columnas que ya no existen en el esquema o no hacen referencia a una columna nueva que se agregó recientemente. Depende del escritor el mantenerlos sincronizados. Es posible que el origen de datos también tenga información de descripción. Esta sería un objeto schemaDescription adicional que se crearía al ejecutar la herramienta.</td></tr><tr><td></td><td>columnDescriptions</td><td>ColumnDescription[]</td><td>Una matriz de ColumnDescriptions que describen las columnas del esquema. </td></tr><tr><td>Experto</td><td></td><td></td><td>Contiene una lista de los usuarios considerados expertos en el conjunto de datos. Las opiniones de los expertos (por ejemplo, descripciones) se mostrarán en la parte superior de la experiencia de usuario al enumerar las descripciones. Cada usuario puede especificar su propia lista de expertos. Solo ese usuario podrá modificar el objeto de expertos. (Los administradores y los propietarios de recursos pueden eliminar el objeto de expertos, pero no editarlo).</td></tr><tr><td></td><td>expertos</td><td>string[]</td><td>Matriz de direcciones de correo electrónico.</td></tr><tr><td>Vista previa</td><td></td><td></td><td>La vista previa contiene una instantánea de las 20 filas principales de datos para el recurso. La vista previa solo tiene sentido para algunos tipos de recursos (por ejemplo, tiene sentido para las tablas, pero no para las medidas).</td></tr><tr><td></td><td>Vista previa</td><td>object[]</td><td>Matriz de objetos que representan una columna. Cada objeto tiene una asignación de propiedad para una columna con un valor para la columna de la fila.</td></tr>
<tr><td>AccessInstruction</td><td></td><td></td><td>Contiene información sobre cómo solicitar acceso al origen de datos. Esta información es lo que se muestra en el campo "Solicitar acceso" en el portal del Catálogo.</td></tr>
<tr><td></td><td>mimeType</td><td>cadena</td><td>El tipo MIME del contenido.</td></tr>
<tr><td></td><td>contenido</td><td>cadena</td><td>Las instrucciones sobre cómo obtener acceso a este recurso de datos. Podría tratarse de una dirección URL, una dirección de correo electrónico o un conjunto de instrucciones.</td></tr>

<tr><td>TableDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>El número de filas del conjunto de datos</td></tr>
<tr><td></td><td>size</td><td>long</td><td>El tamaño en bytes del conjunto de datos.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>cadena</td><td>La última vez que se modificó el esquema.</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>cadena</td><td>La última vez que se modificó el conjunto de datos (se han agregado, modificado o eliminado datos)</td></tr>

<tr><td>ColumnsDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnas</td></td><td>ColumnDataProfile[]</td><td>El número de filas del conjunto de datos</td></tr>

<tr><td>Documentación</td><td></td><td></td><td>Un activo dado solo puede tener una documentación asociada con él.</td></tr>
<tr><td></td><td>mimeType</td><td>cadena</td><td>El tipo MIME del contenido.</td></tr>
<tr><td></td><td>contenido</td><td>cadena</td><td>El contenido de la documentación.</td></tr>


</table>

### Tipos comunes

Tipos comunes pueden usarse como tipos de propiedades, pero no son elementos.

<table><tr><td><b>Tipo común</b></td><td><b>Propiedades</b></td><td><b>Tipo de datos</b></td><td><b>Comentarios</b></td></tr><tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr><tr><td></td><td>sourceType</td><td>cadena</td><td>Describe el tipo de origen de datos, es decir, SQL Server, Base de datos de Oracle, etc. ...  </td></tr><tr><td></td><td>objectType</td><td>cadena</td><td>Describe el tipo de objeto del origen de datos, por ejemplo, tabla, vista de SQL Server.</td></tr><tr><td></td><td>formatType</td><td>cadena</td><td>Describe la estructura de los datos. Los valores actuales son estructurados o no estructurados.</td></tr><tr><td>SecurityPrincipal</td><td></td><td></td><td></td></tr><tr><td></td><td>upn</td><td>cadena</td><td>Dirección de correo electrónico única del usuario.</td></tr><tr><td></td><td>firstName</td><td>cadena</td><td>Nombre de usuario (para fines de presentación).</td></tr><tr><td></td><td>lastName</td><td>cadena</td><td>Apellidos del usuario (para fines de presentación).</td></tr><tr><td>Columna</td><td></td><td></td><td></td></tr><tr><td></td><td>name</td><td>cadena</td><td>Nombre de la columna o atributo.</td></tr><tr><td></td><td>type</td><td>cadena</td><td>Tipo de datos de la columna o atributo. Los tipos permitidos dependerán del sourceType de datos del recurso. Solo se admite un subconjunto de tipos.</td></tr><tr><td></td><td>maxLength</td><td>int</td><td>Longitud máxima permitida para la columna o el atributo. Derivada del origen de datos. Solo se aplica a algunos tipos de origen.</td></tr><tr><td></td><td>Precisión</td><td>byte</td><td>Precisión de la columna o atributo. Derivada del origen de datos. Solo se aplica a algunos tipos de origen.</td></tr><tr><td></td><td>isNullable</td><td>Booleano</td><td>Si se permite que la columna tenga un valor null o no. Derivada del origen de datos. Solo se aplica a algunos tipos de origen.</td></tr><tr><td></td><td>expresión</td><td>cadena</td><td>Si el valor es una columna calculada, este campo contiene la expresión que expresa el valor. Derivada del origen de datos. Solo se aplica a algunos tipos de origen.</td></tr><tr><td></td><td>defaultValue</td><td>objeto</td><td>Valor predeterminado insertado si no se especifica en la instrucción insert para el objeto. Derivada del origen de datos. Solo se aplica a algunos tipos de origen.</td>

</tr><tr><td>ColumnDescription</td><td></td><td></td><td></td></tr>
<tr><td></td><td>etiquetas</td><td>string[]</td><td>Matriz de etiquetas que describen la columna.</td></tr>
<tr><td></td><td>description</td><td>cadena</td><td>Descripción que describe la columna.</td></tr><tr><td></td><td>columnName</td><td>cadena</td><td>Nombre de la columna a la que hace referencia esta información.</td></tr>

</tr><tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>cadena</td><td>El nombre de la columna</td></tr>
<tr><td></td><td>type </td><td>cadena</td><td>El tipo de la columna</td></tr>
<tr><td></td><td>min </td><td>cadena</td><td>El valor mínimo del conjunto de datos</td></tr>
<tr><td></td><td>max </td><td>cadena</td><td>El valor máximo del conjunto de datos</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>El valor promedio del conjunto de datos</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>La desviación estándar del conjunto de datos</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>El número de valores null del conjunto de datos</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>El número de valores distinct del conjunto de datos</td></tr>



</table>

## Roles y autorización

Catálogo de datos de Microsoft Azure proporciona capacidades de autorización para las operaciones CRUD en recursos y anotaciones.

## Conceptos clave

El Catálogo de datos de Azure usa dos mecanismos de autorización:

- Autorización basada en roles
- Autorización basada en permisos

### Roles

Existen 3 roles: **Administrador**, **Propietario** y **Colaborador**. Cada rol tiene su ámbito y derechos, que se resumen en la tabla siguiente.

<table><tr><td><b>Rol</b></td><td><b>Ámbito</b></td><td><b>Derechos</b></td></tr><tr><td>Administrador</td><td>Catálogo (es decir, todos los recursos/anotaciones del catálogo)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Propietario</td><td>Cada recurso (es decir, también conocido como elemento raíz)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Colaborador</td><td>Cada recurso y anotación individual</td><td>Read Update Delete ViewRoles Nota: todos los derechos se revocarán si la operación de lectura en el elemento se ha revocado desde el colaborador</td></tr></table>

> [AZURE.NOTE] Los derechos **Read**, **Update**, **Delete** y **ViewRoles** se pueden aplicar a cualquier elemento (recurso o anotación), mientras que **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** y **ViewPermissions** solo se pueden aplicar al recurso de raíz.
>
>El derecho **Delete** se aplica a un elemento, así como a los elementos secundarios o únicos situados debajo de él. Por ejemplo, la eliminación de un recurso también eliminará las anotaciones de ese recurso.

### Permisos

Un permiso es un lista de entradas de control de acceso. Cada entrada de control de acceso asigna un conjunto de derechos a una entidad de seguridad. Los permisos solo se pueden especificar en un recurso (es decir, el elemento raíz) y se aplican al recurso y a los elementos secundarios.

Durante la vista previa del **Catálogo de datos de Azure**, solo se admite el derecho **Read** en la lista de permisos para habilitar el escenario que restringe la visibilidad de un recurso.

De forma predeterminada, cualquier usuario autenticado tiene derecho **Read** para cualquier elemento del catálogo, a menos que la visibilidad esté restringida al conjunto de entidades de los permisos.

## API de REST

Las solicitudes de elementos de visualización **PUT** y **POST** pueden usarse para controlar roles y permisos; además de la carga de elementos, pueden especificarse dos propiedades del sistema: **\_\_roles** y **\_\_permissions**.

> [AZURE.NOTE]
>
> **\_\_permissions** solo se aplica a un elemento de raíz.
>
> El rol **Propietario** solo es aplicable a un elemento de raíz.
>
> De forma predeterminada, cuando se crea un elemento en el catálogo, su **Colaborador** se establece como el usuario autenticado actualmente. Si el elemento debe poder ser actualizable por todo el mundo, **Colaborador** debe establecerse como entidad de seguridad especial <Everyone> en la propiedad **\_\_roles** cuando se publique la propiedad por primera vez (consulte el ejemplo siguiente). **Colaborador** no se puede cambiar y permanece igual durante la duración de un elemento (es decir, incluso **Administrador** o **Propietario** no tienen derecho para cambiar **Colaborador**). El único valor que se admite para la configuración explícita de **Colaborador** es <Everyone>; es decir, **Colaborador** solo puede ser un usuario que haya creado un elemento o <Everyone>.

###Ejemplos
**Establecer Colaborador como <Everyone> al publicar un elemento.** La entidad de seguridad especial <Everyone> tiene el objectId "00000000-0000-0000-0000-000000000201". **POST** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/?api-version=2015-07.1.0-Preview **Body**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**Asignar propietarios y restringir la visibilidad de un elemento de raíz existente** **PUT**https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
	        {
	            "role": "Owner",
	            "members": [
	                {
	                    "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
	                    "upn": "user1@contoso.com"
	                },
	                {
	                    "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
	                    "upn": "user2@contoso.com"
	                }
	            ]
	        }
	    ],
	    "__permissions": [
	        {
	            "principal": {
	                "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
	                "upn": "user3@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        },
	        {
	            "principal": {
	                "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
	                "upn": "user4@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        }
	    ]
	}

> [AZURE.NOTE] En PUT no es necesario especificar una carga de elementos en el cuerpo: PUT puede usarse para actualizar solo roles y permisos.

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png

<!---HONumber=AcomDC_0128_2016-->