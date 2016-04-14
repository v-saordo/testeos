<properties
pageTitle="Incorporación de la API de Dynamic CRM Online en PowerApps Enterprise o en las Aplicaciones lógicas| Microsoft Azure"
description="Información general de la API de CRM Online con los parámetros de la API de REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/02/2016"
ms.author="deonhe"/>

# Introducción a la API de CRM
Conéctese a Dynamics CRM Online para crear un nuevo registro, actualizar un elemento y mucho más. La API de CRM Online puede utilizarse desde:

- Aplicaciones lógicas
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Con CRM Online, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos que obtiene de CRM Online. 
- Usar acciones para eliminar registros, obtener entidades y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se actualice un elemento en CRM, puede enviar un correo electrónico mediante Office 365.


Para obtener información sobre cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API en PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de CRM incluye las siguientes acciones. No hay desencadenadores.

| Desencadenadores | Acciones|
| --- | --- |
|None| <ul><li>Crear un nuevo registro</li><li>Obtener registros</li><li>Eliminar un registro</li><li>Obtener un registro</li><li>Obtener entidades</li><li>Actualizar un elemento</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a CRM Online


### Incorporación de una configuración adicional en PowerApps
Cuando agregue CRM Online a PowerApps Enterprise, especifique los valores para **Id. de cliente** y **Clave de la aplicación** de la aplicación de Azure Active Directory (AAD) de Dynamics CRM Online. El valor de **dirección URL de redireccionamiento** también se usa en la aplicación de CRM Online. Si no tiene una aplicación, puede usar los siguientes pasos para crearla:

1. En el [Portal de Azure](https://portal.azure.com), abra **Active Directory** y seleccione el nombre del inquilino de su organización.
2. En la pestaña Aplicaciones, seleccione **Agregar**. En **Agregar aplicación**:  

	1. Escriba un **nombre** para la aplicación.  
	2. Deje el tipo de aplicación como **Web**.  
	3. Seleccione **Siguiente**.

	![Agregar aplicación de AAD: información de la aplicación][9]

3. En **Propiedades de la aplicación**:

	1. Especifique la **URL de inicio de sesión** de la aplicación. Dado que va a autenticarse con AAD para PowerApps, establezca la URL de inicio de sesión en _https://login.windows.net_.
	2. Escriba un valor válido de **URI de id. de aplicación** para la aplicación.  
	3. Seleccione **Aceptar**.  

	![Agregar aplicación de AAD: propiedades de la aplicación][10]

4. En la nueva aplicación, seleccione **Configurar**.
5. En _OAuth 2_, establezca el valor **URL de respuesta** en el valor de la URL de redireccionamiento que se muestra al agregar la API de CRM Online en el Portal de Azure: ![Configurar aplicación AAD de Contoso][12]

Ahora, copie y pegue los valores de **Id. de cliente** y **Clave de la aplicación** en la configuración de CRM Online, en el Portal de Azure.

### Incorporación de una configuración adicional en Aplicaciones lógicas
Al agregar esta API a las aplicaciones lógicas, debe iniciar sesión en Dynamics CRM Online.

Siga estos pasos para iniciar sesión en CRM Online y completar la configuración de la **conexión** en la aplicación lógica:

1. Seleccione **Periodicidad**.
2. Seleccione una **Frecuencia** y escriba un **Intervalo**.
3. Seleccione **Agregar una acción**  
![Configurar CRM Online][13]
4. Escriba CRM en el cuadro de búsqueda y espere a que la búsqueda devuelva todas las entradas que incluyan CRM en el nombre.
5. Seleccione **Dynamics CRM Online: Crear un nuevo registro**.
6. Seleccione **Iniciar sesión en Dynamics CRM Online**:  
![Configurar CRM Online][14]
7. Proporcione sus credenciales de CRM Online para iniciar sesión y autorizar la aplicación
![Configurar CRM Online][15].  
8. Después de iniciar sesión, vuelva a la aplicación lógica para completar el proceso agregando otros desencadenadores y otras acciones que necesite.
9. Para guardar el trabajo, seleccione **Guardar** en la barra de menús anterior.


Después de crear la conexión, especifique las propiedades de CRM Online, como table o dataset. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear un nuevo registro 
Crea un nuevo registro en una entidad. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|item| |yes|body|Ninguna|Registro para crear|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtiene registros 
 Obtiene registros de una entidad. ```GET: /datasets/{dataset}/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir. El valor predeterminado es 0.|
|$top|integer|no|query|Ninguna|Número máximo de entradas para capturar. El valor predeterminado es 100.|
|$filter|cadena|no|query|Ninguna|Consulta de filtro ODATA para restringir el número de entradas.|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas.|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Devolver los conjuntos de datos 
 Devuelve los conjuntos de datos. ```GET: /datasets```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Obtiene un elemento de tabla 
Se usa para obtener un determinado registro que existe para una entidad CRM. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|id|cadena|yes|path|Ninguna|Identificador del registro|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Eliminar un elemento de una lista 
Elimina un elemento de una lista. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|id|cadena|yes|path|Ninguna|Identificador del registro|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Aplica un parche a un elemento de tabla existente 
Se usa para actualizar parcialmente un registro existente para una entidad CRM. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|id|cadena|yes|path|Ninguna|Identificador del registro|
|item| |yes|body|Ninguna|Registro para actualizar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Obtiene entidades 
Se usa para obtener la lista de entidades existentes en una instancia de CRM. ```GET: /datasets/{dataset}/tables```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto

#### DataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|


#### TableMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|

#### DataSetsList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|

#### DataSet

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|


#### Tabla

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Elemento

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|

#### ItemsList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### TablesList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


## Pasos siguientes
Después de agregar la API de CRM Online a PowerApps Enterprise, [conceda permisos a los usuarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usarla en sus aplicaciones.

[Cree una aplicación lógica.](../app-service-logic/app-service-logic-create-a-logic-app.md)


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/create-api-crmonline/crmconfig1.png
[14]: ./media/create-api-crmonline/crmconfig2.png
[15]: ./media/create-api-crmonline/crmconfig3.png

<!---HONumber=AcomDC_0302_2016-->


