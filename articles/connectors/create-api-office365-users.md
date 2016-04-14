<properties
	pageTitle="Incorporación de la API Usuarios de Office 365 a sus aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API Usuarios de Office 365 con parámetros de la API de REST"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/25/2016"
ms.author="deonhe"/>

# Introducción a la API Usuarios de Office 365

Conéctese a Usuarios de Office 365 para obtener perfiles, buscar usuarios, etc. La API de Usuarios de Office 365 puede usarse desde:

- PowerApps 
- Aplicaciones lógicas 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [API de Office 365](../app-service-logic/app-service-logic-connector-office365.md).


Con Usuarios de Office 365, puede:

- Compilar el flujo de su negocio en función de los datos que obtiene de Usuarios de Office 365. 
- Usar acciones que obtienen informes directos, obtienen el perfil de usuario de un administrador, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, obtenga informes directos de la persona y, luego, tome esta información y actualice una base de datos de SQL Azure. 
- Agregar la API de Usuarios de Office 365 a PowerApps Enterprise A continuación, los usuarios pueden usar esta API en sus aplicaciones. 

Para obtener información acerca de cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación a las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

La API de Usuarios de Office 365 tiene las siguientes acciones disponibles: No hay ningún desencadenador.

| Desencadenadores | Acciones|
| --- | --- |
|None | <ul><li>Obtener administrador</li><li>Obtener mi perfil</li><li>Obtener informes directos</li><li>Obtener perfil de usuario</li><li>Buscar usuarios</li></ul>|

Todas las API admiten datos en formato JSON y XML.


## Creación de una conexión a Usuarios de Office 365

### Incorporación de una configuración adicional en PowerApps
Cuando agregue esta API a PowerApps Enterprise, escriba los valores **Id. de cliente** y **Secreto de cliente** de la aplicación Azure Active Directory (AAD) de Office 365. El valor de **URL de redireccionamiento** también se usa en su aplicación de Office 365. Si no tiene ninguna aplicación de Office 365, puede usar los siguientes pasos para crearla:

1. En el [Portal de Azure][5], abra **Active Directory** y seleccione el nombre de inquilino de su organización.
2. Seleccione la pestaña **Aplicaciones** y seleccione **Agregar**:  
![Aplicaciones del inquilino de AAD][7].

3. En **Agregar aplicación**:  

	1. Escriba el **nombre** de la aplicación.  
	2. Deje el tipo de aplicación como **Web**.  
	3. Seleccione **Siguiente**.  

	![Agregar aplicación de AAD: información de la aplicación][8]

4. En **Propiedades de la aplicación**:  

	1. Especifique la **URL de inicio de sesión** de la aplicación. Dado que va a realizar la autenticación con AAD para PowerApps, establezca la URL de inicio de sesión en \__https://login.windows.net_.  
	2. Escriba un valor válido de **URI de id. de aplicación** para la aplicación.  
	3. Seleccione **Aceptar**.  

	![Agregar aplicación de AAD: propiedades de la aplicación][9]

5. Cuando haya finalizado, se abre la nueva aplicación de AAD. Seleccione **Configurar**:  
![Aplicación AAD de Contoso][10]

6. En la sección **OAuth 2**, en **URL de respuesta** especifique el valor de la dirección URL de redireccionamiento que se muestra al agregar la API de Usuarios de Office 365 al Portal de Azure. Seleccione **Agregar una aplicación**:  
![Configurar aplicación AAD de Contoso][11]

7. En **Permisos para otras aplicaciones**, seleccione **API unificada de Office 365 (versión preliminar)** y haga clic en **Aceptar**.

	De nuevo en la página de configuración, tenga en cuenta que la _API unificada de Office 365 (vista previa)_ se agrega a la lista _Permisos para otras aplicaciones_.

8. Para **Office 365 Exchange Online**, seleccione **Permisos delegados** y seleccione el permiso **Leer los perfiles básicos de todos los usuarios**.

Se creará una nueva aplicación de Azure Active Directory. Puede copiar y pegar los valores de **Id. de cliente** y **Secreto de cliente** en la configuración de la API de Usuarios de Office 365 en el Portal de Azure.

Encontrará información válida sobre aplicaciones de AAD en [Cómo y por qué se agregan aplicaciones a Azure AD](../active-directory/active-directory-how-applications-are-added.md).


### Incorporación de una configuración adicional en Aplicaciones lógicas
Al agregar esta API a las aplicaciones lógicas, tiene que iniciar sesión en su cuenta de Usuarios de Office 365 y permitir que las aplicaciones lógicas se conecten a su cuenta.

1. Inicie sesión en su cuenta de Usuarios de Office 365.
2. Permita que las aplicaciones lógicas se conecten a su cuenta de Office 365 y la usen. 

Después de crear la conexión, especifique las propiedades de Usuarios de Office 365, como el identificador de usuario. La **referencia de la API de REST** de este tema describe estas propiedades.

>[AZURE.TIP] Esta misma conexión de Usuarios de Office 365 se puede usar en otras aplicaciones lógicas.


## Referencia de API de REST de Usuarios de Office 365
Se aplica a la versión: 1.0.

### Obtener mi perfil 
Recupera el perfil del usuario actual.  
```GET: /users/me```

No hay parámetros para esta llamada.

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener perfil de usuario 
Recupera un perfil de usuario específico.  
```GET: /users/{userId}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener administrador 
Recupera el perfil de usuario del administrador del usuario especificado.  
```GET: /users/{userId}/manager```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|



### Obtener informes directos 
Obtener informes directos.  
```GET: /users/{userId}/directReports```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|



### Buscar usuarios 
Recupera los resultados de búsqueda de los perfiles de usuario. ```GET: /users```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|searchTerm|cadena|no|query|Ninguna|Cadena de búsqueda (se aplica a: nombre para mostrar, nombre dado, apellidos, correo, sobrenombre de correo y nombre principal del usuario)|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|



## Definiciones de objeto

#### Usuario: clase de modelo de usuario.

| Nombre | Tipo de datos |Obligatorio
|---|---|---|
|DisplayName|cadena|no|
|GivenName|cadena|no|
|Surname|cadena|no|
|Mail|cadena|no|
|MailNickname|cadena|no|
|TelephoneNumber|cadena|no|
|AccountEnabled|boolean|no|
|Id|cadena|yes
|UserPrincipalName|cadena|no|
|Departamento|cadena|no|
|JobTitle|cadena|no|
|mobilePhone|cadena|no|


## Pasos siguientes
Después de agregar la API de Office 365 a PowerApps Enterprise, [conceda a los usuarios los permisos necesarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usar dicha API en sus aplicaciones.

[Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0302_2016-->


