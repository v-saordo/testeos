<properties
   pageTitle="Guía de inicio rápido de la API Graph de Azure AD | Microsoft Azure"
   description="La API Graph de Azure Active Directory proporciona acceso mediante programación a Azure AD a través de los extremos de la API de REST OData. Las aplicaciones pueden usar la API Graph para ejecutar operaciones de creación, lectura, actualización y eliminación (CRUD) en objetos y datos de directorio."
   services="active-directory"
   documentationCenter="n/a"
   authors="JimacoMS"
   manager="msmbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="11/17/2015"
      ms.author="v-jibran@microsoft.com"/>

# Guía de inicio rápido de la API Graph de Azure AD

La API Graph de Azure Active Directory (AD) proporciona acceso mediante programación a Azure AD a través de los extremos de la API de REST OData. Las aplicaciones pueden usar la API Graph para ejecutar operaciones de creación, lectura, actualización y eliminación (CRUD) en objetos y datos de directorio. Por ejemplo, la API Graph se puede usar para crear un nuevo usuario, ver o actualizar las propiedades de un usuario, cambiar la contraseña de un usuario, comprobar la pertenencia al grupo para el acceso basado en roles y deshabilitar o eliminar el usuario. Para obtener más información sobre los escenarios de aplicaciones y las características de API Graph, consulte [API Graph de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) y [Los requisitos previos de la API Graph de Azure AD](https://msdn.microsoft.com/library/hh974476(Azure.100).aspx).

> [AZURE.IMPORTANT] Esta función también está disponible mediante [Microsoft Graph](https://graph.microsoft.io/), una API unificada que incluye las API de otros servicios de Microsoft como Outlook, OneDrive, OneNote, Organizador y Office Graph, accesible con un único punto de conexión y un solo token de acceso.

## Construcción de una dirección URL de la API Graph

En la API Graph, para tener acceso a los datos y objetos de los directorios (en otras palabras, a los recursos o entidades) con los que desee realizar operaciones CRUD, puede usar direcciones URL basadas en el protocolo Open Data (OData) Protocol. Las direcciones URL que se usan en la API Graph constan de cuatro partes principales: raíz del servicio, identificador de inquilino, ruta de acceso a recursos y opciones de cadena de consulta: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Tome como ejemplo la siguiente dirección URL: `https://graph.windows.net/contoso.com/groups?api-version=1.5`.

- **Raíz del servicio**: en la API Graph de Azure AD, la raíz del servicio es siempre https://graph.windows.net.
- **Identificador de inquilino**: puede ser un nombre de dominio (registrado) comprobado, en el ejemplo anterior, contoso.com. También puede ser un Id. de objeto de inquilino o los alias "myorganiztion" o "me". Para más información, consulte [Información general de operaciones | Conceptos sobre la API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
- **Ruta de acceso a recursos**: esta sección de una dirección URL identifica el recurso con el que se va a interactuar (usuarios, grupos, un usuario concreto o un grupo determinado, etc.) En el ejemplo anterior, son los "grupos" de nivel superior a los que se dirige este conjunto de recursos También se puede dirigir una entidad concreta, como por ejemplo, “users/{objectId}” o “users/userPrincipalName”.
- **Parámetros de consulta**:? separa la sección de ruta de acceso a recursos de la sección de parámetros de consulta. Todas las solicitudes de la API Graph requieren el parámetro de consulta “api-version”. La API Graph también admite las siguientes opciones de consulta de OData: **$filter**, **$orderby**, **$expand**, **$top**, y **$format**. Sin embargo, las siguientes opciones de consulta no se admiten actualmente: **$count**, **$inlinecount**, y **$skip**. Para más información, consulte [Consultas, filtros y opciones de paginación admitidos | Conceptos sobre la API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## Versiones de la API Graph

Se han publicado las versiones siguientes de la API Graph.

* Versión beta
* Versión 1.5
* Versión 2013-11-08
* Versión 2013-04-05

La versión de las solicitudes de la API Graph se especifica en el parámetro de consulta "api-version". En el caso de la versión 1.5 use un valor de versión numérico; api-version=1.5. En el caso de las versiones anteriores, use una cadena de fecha que se ajuste al formato AAAA-MM-DD; por ejemplo, api-version=2013-11-08. Para las características de vista previa, use la cadena "beta"; por ejemplo, api-version = beta. Para más información sobre las diferencias entre las distintas versiones de la API Graph, consulte [Control de versiones de API Graph de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## Metadatos de la API Graph

Para devolver el archivo de metadatos de la API Graph, agregue el segmento "$metadata" después del identificador de inquilino en la URL. Por ejemplo, la dirección URL siguiente devuelve los metadatos de la compañía de demostración que ha usado el Explorador de gráficos: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.5`. Puede escribir esta dirección URL en la barra de direcciones de un explorador web para ver los metadatos. El documento de metadatos CSDL devuelto describe las entidades y los tipos complejos, sus propiedades, y las funciones y acciones expuestas por la versión de la API Graph que ha solicitado. Si se omite el parámetro api-version se devolverán los metadatos de la versión más reciente.

## Consultas comunes

En [Consultas comunes de la API Graph de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries), se enumeran las consultas comunes que pueden usarse con Azure AD Graph, incluidas las consultas que pueden usarse para obtener acceso a recursos de nivel superior del directorio y las consultas para realizar operaciones en el directorio.

Por ejemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.5` devuelve la información de la compañía en el directorio contoso.com.

O, `https://graph.windows.net/contoso.com/users?api-version=1.5` enumera todos los objetos de usuario del directorio de contoso.com.

## Uso del Explorador de gráficos

El Explorador de gráficos se puede usar para que la API Graph de Azure AD consulte los datos de directorio al compilar la aplicación.

> [AZURE.IMPORTANT] El Explorador de gráficos no admite la escritura ni la eliminación los datos de un directorio. Con el Explorador de gráficos solo se pueden realizar operaciones de lectura en el directorio de Azure AD.

A continuación se muestra el resultado que vería si fuera al Explorador de gráficos, seleccionara Usar compañía de demostración y escribiera `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.5` para mostrar todos los usuarios del directorio de demostración:

![Azure AD api graph explorador](./media/active-directory-graph-api-quickstart/screen_shot.jpg)

**Cargar el Explorador de gráficos**: para cargar la herramienta, navegue a [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Haga clic en **Usar compañía de demostración** para ejecutar el Explorador de gráficos con los datos de un inquilino de ejemplo. Para usar la compañía de demostración no se necesitan credenciales. Como alternativa, puede hacer clic en **Iniciar sesión** e iniciar sesión con las credenciales de su cuenta de Azure AD para ejecutar el Explorador de gráficos con el inquilino. Si ejecuta el Explorador de gráficos con su propio inquilino, usted o el administrador tendrán que dar su consentimiento durante el inicio de sesión. Si dispone de una suscripción a Office 365, tendrá automáticamente un inquilino de Azure AD. De hecho, las credenciales que usa para iniciar sesión en Office 365 son cuentas de Azure AD y puede usarlas con el Explorador de gráficos.

**Ejecutar una consulta**: para ejecutar una consulta, escríbala en el cuadro de texto de la solicitud y haga clic en **GET** o en la tecla **Entrar**. Los resultados se muestran en el cuadro de respuesta. Por ejemplo, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.5` enumerará todos los objetos de grupo del directorio de demostración.

Tenga en cuenta las siguientes características y limitaciones del Explorador de gráficos:
- la funcionalidad Autocompletar en conjuntos de recursos. Para verla, haga clic en **Usar compañía de demostración** y luego haga clic en el cuadro de texto de la solicitud (donde aparece la dirección URL de la compañía). Puede seleccionar un conjunto de recursos en la lista desplegable.

- Admite los alias de direccionamiento “me” y “myorganization”. Por ejemplo, puede usar `https://graph.windows.net/me?api-version=1.5` para devolver el objeto de usuario del usuario con sesión iniciada o `https://graph.windows.net/myorganization/users?api-version=1.5` para devolver todos los usuarios del directorio actual. Tenga en cuenta que el alias "me" devuelve un error de la compañía de demostración porque no hay ningún usuario con sesión iniciada que realice la solicitud.

- Una sección de encabezados de respuesta. Se puede usar como ayuda para solucionar los problemas que se producen al ejecutar consultas.

- Un visor JSON para la respuesta con capacidades de expansión y contracción.

- No permite mostrar fotos en miniatura.

## Uso de Fiddler para escribir en el directorio

Para seguir esta guía de inicio rápido, puede usar el depurador web Fiddler para practicar la realización de operaciones de "escritura" con su directorio de Azure AD. Para más información y para instalar Fiddler, consulte [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

En el ejemplo siguiente, usará el depurador web Fiddler para crear un nuevo grupo de seguridad, "MyTestGroup", en el directorio de Azure AD.

**Obtener un token de acceso**: para obtener acceso a Azure AD Graph, los clientes primero deben autenticarse correctamente en Azure AD. Para obtener más información, consulte [Escenarios de autenticación en Azure AD](active-directory-authentication-scenarios.md).

**Redactar y ejecutar una consulta**: lleve a cabo los pasos siguientes.

1. Abra el depurador web Fiddler y cambie a la pestaña **Composer** (Compositor).
2. Puesto que desea crear un nuevo grupo de seguridad, seleccione **Post** como método de HTTP en el menú desplegable. Para más información sobre las operaciones y los permisos de los objeto de grupo, consulte la sección sobre los [grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) en [Referencia de entidad y de tipo complejo | Referencia de la API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. En el campo que se encuentra junto a **Post**, escriba la siguiente dirección URL de solicitud: `https://graph.windows.net/mytenantdomain/groups?api-version=1.5`.

    > [AZURE.NOTE] Debe sustituir mytenantdomain por el nombre de dominio de su directorio de Azure AD.

4. En el campo que se encuentra inmediatamente debajo de Post, escriba lo siguiente:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Sustituya & lt; el token de acceso & gt; por el token de acceso del directorio de Azure AD.

5. En el campo **Request body** (Cuerpo de la solicitud) escriba lo siguiente:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Para más información sobre la creación de grupos, consulte [Crear un grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para más información sobre las entidades y los tipos de Azure AD expuestos por Graph, así como sobre las operaciones que se pueden realizar en ellos con Graph, consulte [Referencia de la API Graph de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## Pasos siguientes

Obtenga más información sobre la [API Graph de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

<!----HONumber=AcomDC_1210_2015-->