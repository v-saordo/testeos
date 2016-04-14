<properties 
	pageTitle="Aprenda a proteger el acceso a datos en DocumentDB | Microsoft Azure" 
	description="Obtenga información sobre los conceptos de control de acceso en DocumentDB, incluidas las claves maestras, las claves de solo lectura, los usuarios y los permisos." 
	services="documentdb" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="ryancraw"/>

# Protección del acceso a los datos de DocumentDB #

En este artículo se proporciona información general sobre la protección del acceso a los datos almacenados en [DocumentDB de Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

Después de leer dicha información, podrá responder a las preguntas siguientes:

-	¿Qué son las claves maestras de DocumentDB?
-	¿Qué son las claves de solo lectura de DocumentDB?
-	¿Qué son los tokens de recursos de DocumentDB?
-	¿Cómo puedo utilizar los usuarios y permisos de DocumentDB para proteger el acceso a los datos de dicha base?

##<a id="Sub1"></a>Conceptos de control de acceso a DocumentDB##

DocumentDB proporciona conceptos de primera clase para controlar el acceso a sus recursos. Para la finalidad de este tema, los recursos de DocumentDB se agrupan en dos categorías:

- Recursos administrativos
	- Cuenta
	- Base de datos
	- Usuario
	- Permiso
- Recursos de aplicación
	- Colección
	- Documento
	- Datos adjuntos
	- Procedimiento almacenado
	- Desencadenador
	- Función definida por el usuario

En el contexto de estas dos categorías, DocumentDB admite tres tipos de roles de control de acceso: administrador de cuenta, administrador de solo lectura y usuario de base de datos. Los derechos de cada rol de control de acceso son:
 
- Administrador de cuenta: acceso completo a todos los recursos (administrativos y de aplicación) en una cuenta de DocumentDB determinada.
- Administrador de solo lectura: acceso de solo lectura a todos los recursos (administrativos y de aplicación) en una cuenta de DocumentDB determinada. 
- Usuario de la base de datos: recurso de usuario de DocumentDB asociado a un conjunto específico de recursos propios de DocumentDB (por ejemplo, colecciones, documentos, scripts). Puede haber uno o más recursos de usuario asociados a una base de datos determinada y cada uno de estos puede tener uno o varios permisos asociados.

Teniendo en cuenta las categorías y los recursos mencionados, el modelo de control de acceso de DocumentDB define tres tipos de construcciones de acceso:

- Claves maestras: tras crear una cuenta de DocumentDB, se crean dos claves maestras (principal y secundaria). Estas claves habilitan el acceso administrativo completo a todos los recursos de dicha cuenta.

![Ilustración de las claves maestras de DocumentDB](./media/documentdb-secure-access-to-data/masterkeys.png)

- Claves de solo lectura: tras crear una cuenta de DocumentDB, se crean dos claves de solo lectura (principal y secundaria). Estas claves habilitan el acceso de solo lectura a todos los recursos de dicha cuenta.

![Ilustración de claves de solo lectura de DocumentDB](./media/documentdb-secure-access-to-data/readonlykeys.png)

- Tokens de recursos: un token de recurso se asocia a un recurso de permiso de DocumentDB y captura la relación entre el usuario de una base de datos y el permiso que este tiene para un recurso de aplicación de DocumentDB específico (por ejemplo, colección o documento).

![Ilustración de tokens de recursos de DocumentDB](./media/documentdb-secure-access-to-data/resourcekeys.png)

##<a id="Sub2"></a>Trabajo con claves maestras y de solo lectura de DocumentDB ##
Como se mencionó anteriormente, las claves maestras de DocumentDB proporcionan acceso administrativo completo a todos los recursos de una cuenta de este tipo, mientras que las claves de solo lectura habilitan el acceso de lectura a todos los recursos de la cuenta. El fragmento de código siguiente muestra cómo usar un extremo y la clave principal de la cuenta de DocumentDB para crear una instancia de DocumentClient y una nueva base de datos.

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Classic Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
	private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
	//Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


##<a id="Sub3"></a>Información general de tokens de recursos de DocumentDB ##
Si desea proporcionar acceso a los recursos de su cuenta de DocumentDB a un cliente que no es de confianza con la clave maestra, puede usar un token de recurso (mediante la creación de usuarios y permisos de DocumentDB). Las claves maestras de DocumentDB incluyen una clave principal y una secundaria, cada una de las cuales concede acceso administrativo a la cuenta y a todos los recursos incluidos en esta. La exposición de alguna de las claves maestras posibilita que se haga un uso negligente o malintencionado de la cuenta.

Del mismo modo, las claves de solo lectura de DocumentDB proporcionan acceso de lectura a todos los recursos (excepto, por supuesto, los recursos de permisos) de una cuenta de este tipo y no se pueden usar para proporcionar un acceso más específico a recursos concretos de dicha base de datos.

El token de recurso de DocumentDB ofrece una alternativa segura que permite a los clientes leer, escribir y eliminar recursos en la cuenta de DocumentDB en función de los permisos concedidos y sin necesidad de una clave maestra o de solo lectura.

Este es un patrón de diseño típico mediante el cual los tokens de recursos se pueden solicitar, generar y entregar a los clientes:

1. Se configura un servicio de nivel intermedio para que una aplicación móvil pueda usarlo para compartir fotos del usuario.
2. Dicho servicio tiene la clave maestra de la cuenta de DocumentDB.
3. La aplicación fotográfica se instala en dispositivos móviles del usuario final. 
4. Al iniciar sesión, dicha aplicación establece la identidad del usuario con el servicio de nivel intermedio. Este mecanismo de establecimiento de identidad depende únicamente de la aplicación.
5. Una vez establecida la identidad, el servicio de nivel intermedio solicita permisos en función de esta.
6. El servicio de nivel intermedio reenvía un token de recurso a la aplicación de teléfono.
7. La aplicación de teléfono puede seguir usando el token de recurso para obtener acceso directo a los recursos de DocumentDB con los permisos definidos por el token de recurso y para el intervalo permitido por dicho token. 
8. Cuando el token de recurso expira, las solicitudes posteriores reciben un mensaje 401 de excepción no autorizada. En este punto, la aplicación de teléfono restablece la identidad y solicita un nuevo token de recurso.

![Flujo de trabajo de tokens de recursos de DocumentDB](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

##<a id="Sub4"></a>Trabajo con usuarios y permisos de DocumentDB ##
Un recurso de usuario de DocumentDB se asocia a una base de datos de este tipo. Cada base de datos puede contener cero o más usuarios de DocumentDB. El fragmento de código siguiente muestra cómo crear un recurso de usuario de DocumentDB:

	//Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(database.SelfLink, docUser);

> [AZURE.NOTE] Cada usuario de DocumentDB tiene una propiedad PermissionsLink que se puede usar para recuperar la lista de permisos asociados al usuario.

Un recurso de permiso de DocumentDB se asocia a un usuario de dicha base de datos. Cada usuario puede contener cero o más permisos de DocumentDB. Un recurso de permiso proporciona acceso a un token de seguridad que el usuario necesita al intentar obtener acceso a un recurso de aplicación específico. Un recurso de permiso puede proporcionar dos posibles niveles de acceso:

- Todo: el usuario tiene pleno permiso en el recurso.
- Lectura: el usuario solo puede leer el contenido del recurso, pero no realizar operaciones de escritura, actualización o eliminación en este.


> [AZURE.NOTE] Para ejecutar procedimientos almacenados de DocumentDB, el usuario debe tener el permiso Todo en la colección donde se va a ejecutar el procedimiento almacenado.


El fragmento de código siguiente muestra cómo crear un recurso de permiso, leer el token de dicho recurso y asociar los permisos al usuario creado anteriormente.

	//Create a permission.

    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
	docPermission = await client.CreatePermissionAsync(docUser.SelfLink, docPermission);
	Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);

Para poder obtener fácilmente todos los recursos de permiso asociados a un usuario determinado, DocumentDB dispone de una fuente de permisos para cada objeto de usuario. El fragmento de código siguiente muestra cómo recuperar el permiso asociado al usuario creado anteriormente, construir una lista de permisos y crear instancias de un nuevo elemento DocumentClient en nombre del usuario.

	//Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(docUser.SelfLink);
	
	List<Permission> permList = new List<Permission>();
    
	foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl),permList);

> [AZURE.TIP] Los tokens de recursos tienen un intervalo de tiempo válido predeterminado de 1 hora. Sin embargo, la vigencia del token puede especificarse explícitamente hasta un máximo de 5 horas.

##<a name="NextSteps"></a>Pasos siguientes

- Para obtener más información sobre DocumentDB, haga clic [aquí](http://azure.com/docdb).
- Para obtener más información acerca de cómo administrar las claves maestras y de solo lectura, haga clic [aquí](documentdb-manage-account.md).
- Para obtener más información acerca de cómo construir tokens de autorización de DocumentDB, haga clic [aquí](https://msdn.microsoft.com/library/azure/dn783368.aspx)
 

<!---HONumber=AcomDC_0211_2016-->