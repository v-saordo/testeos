<properties 
	pageTitle="Conectores negocio a negocio y aplicaciones de API en el Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Aprenda a crear y a configurar conectores EDI, EDIFACT, AS2 y TPM; arquitectura de microservicios." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2016" 
	ms.author="mandia"/>

# Conectores negocio a negocio y aplicaciones de API en el Servicio de aplicaciones de Microsoft Azure
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

El Servicio de aplicaciones de Microsoft Azure (o Servicio de aplicaciones para abreviar) incluye muchas aplicaciones de API de BizTalk que son esenciales para los entornos de integración. Estas aplicaciones de API se basan en conceptos y herramientas que se usan en BizTalk Server, pero que ahora están disponibles como parte de Servicios de aplicaciones de Azure.

Una categoría de estas aplicaciones de API son las aplicaciones de API de negocio a negocio (B2B). Mediante estas aplicaciones de API B2B, puede agregar fácilmente socios, crear acuerdos y hacer todo lo que haría en local mediante EDI, AS2 y EDIFACT.

Estas aplicaciones de API B2B ofrecen funcionalidades de "Desencadenador" y "Acción". Un desencadenador inicia una nueva instancia en función de un suceso específico, como la llegada de un mensaje X12 de un socio. Una acción es el resultado, por ejemplo, después de recibir un mensaje X12 enviarlo mediante AS2.


## Qué son el Conector negocio a negocio o las aplicaciones de API
La característica Negocio a negocio (B2B, Business-to-Business) incluye aplicaciones de API previamente incorporadas existentes que permiten que distintas empresas, divisiones, aplicaciones, etc., se comuniquen mediante AS2, EDI y EDIFACT.

Las aplicaciones de API B2B incluyen:

Conector o aplicaciones de API | Descripción
--- | ---
Administración de socios comerciales de BizTalk | Una aplicación de API que crea relaciones negocio a negocio (B2B) mediante socios y acuerdos. Estas relaciones emplean el protocolo AS2, EDIFACT y X12.<br/><br/>La aplicación de API TPM es el requisito básico del conector AS2 y de Aplicaciones de API X12 o EDIFACT. 
Conector AS2 | Un conector que recibe y envía mensajes mediante el transporte AS2. El conector transporta datos de forma segura y confiable a través de Internet.
BizTalk EDIFACT | Una aplicación de API que recibe y envía mensajes mediante EDIFACT. EDIFACT se conoce también comúnmente como UN/EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport) y se usa ampliamente en las industrias.
BizTalk X12 | Una aplicación de API que recibe y envía mensajes mediante el protocolo X12. X12 se conoce también comúnmente como ASC X12 (Accredited Standards Committee X12) y se usa ampliamente en las industrias. 


Mediante estas aplicaciones de API, puede llevar a cabo distintas tareas de mensajería de EDI. Por ejemplo, con el conector AS2, puede recibir y enviar de forma segura distintos tipos de mensajes (EDI, XML, archivo plano, etc.) a un cliente, a una división de su empresa, como Recursos Humanos, o a alguien que use AS2.

Puede crear tantas aplicaciones de API como desee y crearlas de forma fácil. También puede reutilizar una sola aplicación de API en varios escenarios o flujos de trabajo.

Y todo ello sin escribir ningún código. Comencemos.


## Requisitos de inicio
Cuando cree aplicaciones de API B2B, necesitará algunos recursos. Estos elementos debe crearlos usted para que puedan usarse en otras aplicaciones de API. Estos requisitos son:

Requisito | Descripción
--- | ---
Base de datos SQL de Azure | Almacena elementos B2B, lo que incluye socios, esquemas, certificados y acuerdos. Cada una de las aplicaciones de API de B2B requiere su propia base de datos de SQL Azure. <br/><br/>**Nota** Copie la cadena de conexión en esta base de datos.<br/><br/>[Creación de una base de datos SQL Azure](../sql-database/sql-database-get-started.md)
Contenedor de Almacenamiento de blobs de Azure | Almacena las propiedades de los mensajes cuando está habilitado el archivado AS2. Si no necesita el archivado de mensajes de AS2, no se necesita un contenedor de almacenamiento. <br/><br/>**Nota** Si va a habilitar el archivado, copie la cadena de conexión para el almacenamiento de blobs.<br/><br/>[Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md)
Espacio de nombres de Bus de servicio y sus valores de clave | Almacena datos de procesamiento por lotes X12 y EDIFACT. Si no necesita el procesamiento por lotes, no es necesario un espacio de nombres del Bus de servicio.<br/><br/>**Nota** Si va a habilitar el procesamiento por lotes, copie estos valores.<br/><br/>[Creación de un espacio de nombres del Bus de servicio](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Instancia de TPM | Una instancia de Administración de socios comerciales de BizTalk (TPM) es necesaria para crear un conector AS2 y la aplicación de API X12 o EDIFACT. Cuando crea la aplicación de API TPM, está creando la instancia de TPM.<br/><br/>**Nota** Conozca el nombre de su aplicación de API TPM. 


## Creación de aplicaciones de API
Las aplicaciones de API B2B se pueden crear mediante el portal de Azure o las API de REST.


### Creación de las aplicaciones de API mediante las API de REST
[Vea la documentación sobre cómo usar las API de REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### Creación de las aplicaciones de API B2B en el portal de Azure
En el portal de Azure, puede crear aplicaciones de API B2B cuando cree aplicaciones lógicas, aplicaciones web o aplicaciones móviles. O bien, puede crear uno con su propia hoja. Ambas formas son sencillas, por lo que depende de sus necesidades o preferencias. Algunos usuarios prefieren crear todas las aplicaciones de API B2B con sus propiedades específicas primero. Luego, crean las aplicaciones lógicas/aplicaciones web/aplicaciones móviles y agregan las aplicaciones de API B2B que ha creado.

Mediante los siguientes pasos se crean las aplicaciones de API B2B usando la hoja de aplicaciones de API.


#### Creación de las aplicaciones de API de Administración de socios comerciales (TPM)

> [AZURE.NOTE] Una instancia de Administración de socios comerciales de BizTalk (TPM) es necesaria para crear un conector AS2 y la aplicación de API X12 o EDIFACT. Cuando crea la aplicación de API TPM, está creando la instancia de TPM.

En los siguientes pasos se crea la instancia de TPM:

1. En el Panel de inicio del portal de Azure (la página principal), seleccione **Marketplace**. En **Aplicaciones de API** se muestran todas las aplicaciones de API y conectores existentes. También puede **buscar** las aplicaciones de API B2B específicas.
2. Seleccione **Administración de socios comerciales de BizTalk**. En la nueva hoja, seleccione **Crear**. 
3. Especifique las propiedades: 

	Propiedad | Descripción
--- | ---
Nombre | Escriba cualquier nombre para la instancia de TPM. Por ejemplo, llámelo, *AccountsPayableTPM*.
Configuración del paquete | Escriba la **cadena de conexión a base de datos de ADO.NET** para la Base de datos SQL de Azure que ha creado. <br/><br/>Cuando copie la cadena de conexión, la contraseña no se agregará a esta. Asegúrese de escribir la contraseña en la cadena de conexión.
Plan de servicio de aplicación | Muestra el plan de pagos. Puede cambiarlo si necesita más o menos recursos.
Nivel de precios | Propiedad de solo lectura que muestra la categoría de precio de su suscripción de Azure. 
El grupos de recursos | Cree uno nuevo o utilice un grupo ya existente. Todas las aplicaciones de API y conectores de las aplicaciones lógicas, aplicaciones web y aplicaciones móviles deben estar en el mismo grupo de recursos. En <br/><br/>[Uso de grupos de recursos](../resource-group-overview.md) se explica esta propiedad. 
La suscripción | Propiedad de solo lectura que muestra su suscripción actual.
Ubicación | La ubicación geográfica que hospeda el servicio de Azure. 
Agregar al Panel de inicio | Seleccione esta opción para agregar la aplicación de API B2B a su Panel de inicio (la página principal).

4. Seleccione **Crear**.

Después de crear la aplicación de API TPM (instancia de TPM), puede crear luego el conector AS2 y/o las aplicaciones de API X12 o EDIFACT.


#### Creación del conector AS2

1. En el Panel de inicio del portal de Azure (la página principal), seleccione **Marketplace**. En **Aplicaciones de API** se muestran todas las aplicaciones de API y conectores existentes. También puede **buscar** las aplicaciones de API B2B específicas.
2. Seleccione **Conector AS2**. En la nueva hoja, seleccione **Crear**. 
3. Especifique las propiedades: 

	Propiedad | Descripción
--- | ---
Nombre | Escriba un nombre para el conector AS2. Por ejemplo, llámelo, *AS2Connector*.
Configuración del paquete | Escriba la configuración específica para esa aplicación de API, como el nombre de la instancia de TPM. <br/><br/>Consulte [Adición de la configuración del paquete AS2](#AddAS2Conn) en este tema para ver las propiedades específicas. 
Plan de servicio de aplicación | Muestra el plan de pagos. Puede cambiarlo si necesita más o menos recursos.
Nivel de precios | Propiedad de solo lectura que muestra la categoría de precio de su suscripción de Azure. 
El grupos de recursos | Cree uno nuevo o utilice un grupo ya existente. En [Uso de grupos de recursos](../resource-group-overview.md) se explica esta propiedad. 
La suscripción | Propiedad de solo lectura que muestra su suscripción actual.
Ubicación | La ubicación geográfica que hospeda el servicio de Azure. 
Agregar al Panel de inicio | Seleccione esta opción para agregar la aplicación de API B2B a su Panel de inicio (la página principal).

	**<a name="AddAS2Conn"></a>Configuración del paquete de conector AS2**

	Propiedad | Descripción
--- | --- 
Cadena de conexión de base de datos | Escriba la cadena de conexión de ADO.NET para la Base de datos SQL de Azure que ha creado. Cuando copie la cadena de conexión, la contraseña no se agregará a esta. Asegúrese de escribir la contraseña en la cadena de conexión antes de pegarla.
Habilitación del archivado para los mensajes entrantes | Opcional. Habilite esta propiedad para almacenar las propiedades de mensaje de un mensaje AS2 entrante recibido de un socio. 
Cadena de conexión de Almacenamiento de blobs de Azure | Escriba la cadena de conexión al contenedor de Almacenamiento de blobs de Azure que ha creado. Cuando el archivado está habilitado, los mensajes codificados y descodificados se almacenan en este contenedor de almacenamiento.
Nombre de la instancia de TPM | Escriba el nombre de la aplicación de API de **Administración de socios comerciales de BizTalk** que ha creado anteriormente. Cuando crea el conector AS2, este conector ejecuta solo los acuerdos de AS2 que contiene esta instancia específica de TPM.

4. Seleccione **Crear**.


#### Creación de las aplicaciones de API X12 o EDIFACT

1. En el Panel de inicio del portal de Azure (la página principal), seleccione **Marketplace**. En **Aplicaciones de API** se muestran todas las aplicaciones de API y conectores existentes. También puede **buscar** las aplicaciones de API B2B específicas.
2. Seleccione **BizTalk X12** o **BizTalk EDIFACT**. En la nueva hoja, seleccione **Crear**. 
3. Especifique las propiedades: 

	Propiedad | Descripción
--- | ---
Nombre | Escriba un nombre para la aplicación de API B2B. Por ejemplo, llámelo *EDI850APIApp*.
Configuración del paquete | Escriba la configuración específica para esa aplicación de API, como el nombre de la instancia de TPM. <br/><br/>Consulte [Configuración del paquete X12 o EDIFACT](#AddX12) en este tema para ver las propiedades específicas. 
Plan de servicio de aplicación | Muestra el plan de pagos. Puede cambiarlo si necesita más o menos recursos.
Nivel de precios | Propiedad de solo lectura que muestra la categoría de precio de su suscripción de Azure. 
El grupos de recursos | Cree uno nuevo o utilice un grupo ya existente. En [Uso de grupos de recursos](../resource-group-overview.md) se explica esta propiedad. 
La suscripción | Propiedad de solo lectura que muestra su suscripción actual.
Ubicación | La ubicación geográfica que hospeda el servicio de Azure. 
Agregar al Panel de inicio | Seleccione esta opción para agregar la aplicación de API B2B a su Panel de inicio (la página principal).

	**<a name="AddX12"></a>Configuración del paquete de aplicaciones de API X12 y EDIFACT**

	Propiedad | Descripción
--- | --- 
Cadena de conexión de base de datos | Escriba la cadena de conexión de ADO.NET para la Base de datos SQL de Azure que ha creado. Cuando copie la cadena de conexión, la contraseña no se agregará a esta. Asegúrese de escribir la contraseña en la cadena de conexión antes de pegarla.
Espacio de nombres de Bus de servicio | Escriba el espacio de nombres de Bus de servicio que ha creado. Solo se requiere cuando está habilitado el procesamiento por lotes. 
Nombre de la clave de acceso compartido del espacio de nombres de Bus de servicio | Escriba la clave de acceso del espacio de nombres de Bus de servicio que ha creado. Solo se requiere cuando está habilitado el procesamiento por lotes. 
Valor de la clave de acceso compartido del espacio de nombres de Bus de servicio | Escriba el valor de la clave de acceso del espacio de nombres de Bus de servicio que ha creado. Solo se requiere cuando está habilitado el procesamiento por lotes. 
Nombre de la instancia de TPM | Escriba el nombre de la aplicación de API de **Administración de socios comerciales de BizTalk** que ha creado anteriormente. Cuando crea las aplicaciones de API X12 o EDIFACT, esta aplicación de API solo ejecuta los acuerdos X12/EDFIACT que contiene esta instancia de TPM específica.

4. Seleccione **Crear**.


## Adición de los socios, los acuerdos, los certificados y los esquemas 
En el Portal de Azure, abra la aplicación de API TPM. En la sección **Componentes**, agregue sus socios, acuerdos, certificados y esquemas.

También puede agregar acuerdos a sus conectores AS2, aplicaciones de API X12 y aplicaciones de API EDIFACT.


## Supervisión de las aplicaciones de API
En el Portal de Azure, abra la aplicación de API TPM. En la sección **Operaciones**, puede ver distintas operaciones de administración. Por ejemplo, puede:

- Ver sucesos informativos y de error
- Ver el uso de memoria y el recuento de subprocesos del proceso de trabajo (w3wp)
- Ver los registros de aplicaciones y de servidor web

Más información en [Supervisión de Aplicaciones lógicas](app-service-logic-monitor-your-logic-apps.md).


## Adición de las aplicaciones de API a la aplicación 
El Servicio de aplicaciones de Microsoft Azure expone diferentes tipos de aplicaciones que pueden usar estas aplicaciones de API B2B. Puede crear una nueva o agregar sus aplicaciones de API B2B existentes a aplicaciones lógicas, aplicaciones móviles o aplicaciones web.

En la aplicación, con solo seleccionar las aplicaciones de API B2B de la Galería se agregan automáticamente a la aplicación.

> [AZURE.IMPORTANT] Para agregar conectores y aplicaciones de API creadas anteriormente, cree las aplicaciones lógicas, las aplicaciones móviles o las aplicaciones web en el mismo grupo de recursos.

En los siguientes pasos se agregan las aplicaciones de API B2B a las aplicaciones lógicas, las aplicaciones móviles o las aplicaciones web:

1. En el panel de inicio del Portal de Azure (página principal), vaya a **Marketplace** y busque Aplicaciones lógicas, móviles o web. 

	Si va a crear una nueva aplicación, busque aplicaciones lógicas, aplicaciones móviles o aplicaciones web. Seleccione la aplicación en la nueva hoja, seleccione **Crear**. En [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md) se muestran los pasos.

2. Abra su aplicación y seleccione **Desencadenadores y acciones**.

3. En la **Galería**, seleccione la aplicación de API B2B, que se agregará automáticamente a su aplicación. También puede crear una nueva aplicación de API B2B.

	> [AZURE.IMPORTANT] El conector AS2 y las aplicaciones de API X12 y EDIFACT requieren una instancia de TPM. De modo que, si va a crear nuevas aplicaciones de API B2B, cree primero la aplicación de API TPM, y luego cree el conector AS2 y la aplicación de API X12 o EDIFACT.

4. Seleccione **Aceptar** para guardar los cambios.

>[AZURE.NOTE] Si desea empezar a usar Aplicaciones lógicas de Azure antes de suscribirse para obtener una cuenta de Azure, [pruebe Aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic). Podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Más recursos B2B

[Creación de un proceso B2B](app-service-logic-create-a-b2b-process.md)<br/> [Creación de un acuerdo entre socios comerciales](app-service-logic-create-a-trading-partner-agreement.md)<br/> [¿Qué son los conectores y las aplicaciones de la API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## Lea acerca de las aplicaciones lógicas y las aplicaciones web
[¿Qué son Aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)<br/> [Sitios web y aplicaciones web en el Servicio de aplicaciones de Azure](../app-service-web/app-service-web-overview.md)


## Más conectores

[Lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md)<br/><br/> [Qué son los conectores y las aplicaciones de API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=AcomDC_0224_2016-->