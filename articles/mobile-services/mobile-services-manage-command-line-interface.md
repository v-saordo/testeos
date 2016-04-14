<properties
	pageTitle="Administración de un servicio móvil desde la línea de comandos | Microsoft Azure"
	description="Obtenga información acerca de cómo crear, implementar y administrar el servicio móvil de Azure mediante herramientas de línea de comandos."
	services="mobile-services"
	documentationCenter="Mobile"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="NA"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/27/2016"
	ms.author="glenga"/>

# Automatización de servicios móviles con herramientas de línea de comandos

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


##Información general

En este tema se muestra cómo usar las herramientas de línea de comandos de Azure para automatizar la creación y administración de servicios móviles de Azure. Este tema le muestra cómo instalar y comenzar a usar herramientas de línea de comandos y utilizarlas para realizar Servicios móviles clave:

Cuando se combinan en un archivo por lotes o un único script, estos comandos individuales automatizan los procesos de creación, comprobación y eliminación de un servicio móvil.

Este tema incluye una selección de tareas de administración comunes compatibles con las herramientas de línea de comandos de Azure. Para obtener más información, consulte [Documentación de herramientas de línea de comandos de Azure][reference-docs].

##Instalación de las herramientas de línea de comandos de Azure

La siguiente lista contiene información para instalar las herramientas de línea de comandos, según su sistema operativo:

* **Windows**: descargue el [instalador de las herramientas de línea de comandos de Azure][windows-installer]. Abra el archivo .msi descargado y complete los pasos de instalación a medida que se le solicita.

* **Mac**: descargue el [instalador de SDK de Azure][mac-installer]. Abra el archivo .pkg descargado y complete los pasos de instalación a medida que se le solicita.

* **Linux**: instale la última versión de [Node.js][nodejs-org] (consulte [Instalación de Node.js a través del Administrador de paquetes][install-node-linux]) y, a continuación, ejecute el siguiente comando:

	npm install azure-cli -g

Para probar la instalación, escriba `azure` en el símbolo del sistema. Si la instalación es correcta, verá una lista de los comandos de `azure` disponibles.

##Descarga e importación de la configuración de publicación

Para comenzar, primero necesita descargar e importar su configuración de publicación. A continuación, puede usar las herramientas para crear y administrar servicios de Azure. Para descargar la configuración de publicación, utilice el comando `account download`:

	azure account download

Esto abrirá el explorador predeterminado y le solicitará iniciar sesión en el Portal de Azure clásico. Después de iniciar sesión, se descargará su archivo `.publishsettings`. Anote la ubicación del archivo guardado.

A continuación, importe el archivo `.publishsettings` mediante la ejecución del siguiente comando y reemplace `<path-to-settings-file>` por la ruta hacia el archivo `.publishsettings`:

	azure account import <path-to-settings-file>

Puede eliminar toda la información almacenada con el comando <code>import</code> usando el comando <code>account clear</code>:

	azure account clear

Para ver una lista de opciones para los comandos `account`, use la opción `-help`:

	azure account -help

Después de importar su configuración de publicación, debe eliminar el archivo `.publishsettings` por motivos de seguridad. Para obtener más información, consulte [Instalación de las herramientas de línea de comandos de Azure para Mac y Linux]. Ahora está preparado para comenzar a crear y administrar servicios móviles de Azure desde la línea de comandos o en los archivos por lotes.

##Creación de un servicio móvil

Puede usar las herramientas de línea de comandos para crear una nueva instancia de servicio móvil. Cuando se crea un servicio móvil, también se crea una instancia de Base de datos SQL en un nuevo servidor.

El siguiente comando crea una nueva instancia de servicios móviles en la suscripción, donde `<service-name>` es el nombre del nuevo servicio móvil, `<server-admin>` es el nombre de inicio de sesión del nuevo servidor y `<server-password>` es la contraseña para el nuevo inicio de sesión:

	azure mobile create <service-name> <server-admin> <server-password>

Se produce un error en el comando `mobile create` cuando existe el servicio móvil especificado. En los scripts de automatización, debe intentar eliminar un servicio móvil antes de intentar volver a crearlo.

##Enumeración de servicios móviles existentes en una suscripción

> [AZURE.NOTE] Los comandos de la CLI relacionados con "list" y "script" solo funcionan con el backend de JavaScript.

El siguiente comando devuelve una lista de todos los servicios móviles en una suscripción a Azure:

	azure mobile list

Este comando también muestra el estado actual y la dirección URL de cada servicio móvil.

##Eliminación de un servicio móvil existente

Puede usar las herramientas de línea de comandos para eliminar un servicio móvil existente, junto con el servidor y la base de datos SQL relacionados. El siguiente comando elimina el servicio móvil, donde `<service-name>` es el nombre del servicio móvil para eliminar:

	azure mobile delete <service-name> -a -q

Si incluye los parámetros `-a` y `-q`, este comando también elimina el servidor y la base de datos SQL usados por el servicio móvil sin mostrar una solicitud.

> [AZURE.NOTE] Si no especifica el parámetro <code>-q</code> junto con <code>-</code> o <code>-d</code>, la ejecución se pausa y se le solicita que seleccione las opciones de eliminación para su Base de datos SQL. Utilice únicamente el parámetro <code>-a</code> cuando ningún otro servicio use la base de datos o el servidor; de lo contrario, use el parámetro <code>-d</code> para eliminar solamente los datos que pertenezcan al servicio móvil que se vaya a eliminar.

##Creación de una tabla en el servicio móvil

El siguiente comando crea una tabla en el servicio móvil especificado, donde `<service-name>` es el nombre del servicio móvil y `<table-name>` el nombre de la tabla que desea crear:

	azure mobile table create <service-name> <table-name>

De esta forma, se crea una nueva tabla con permisos predeterminados, `application`, para las operaciones de tabla: `insert`, `read`, `update` y `delete`.

El siguiente comando crea una nueva tabla con un permiso `read` público pero con el permiso `delete` concedido solo a administradores:

	azure mobile table create <service-name> <table-name> -p read=public,delete=admin

La siguiente tabla muestra el valor de permiso del script en comparación con el valor de permiso en el [Portal de Azure clásico].

|Valor de script|Valor del Portal| |========|========| |`public`|Todos| |`application`(valor predeterminado)|Cualquiera con la clave de aplicación| |`user`|Solo usuarios autenticados| |`admin`|Solo scripts y administradores|

Se produce un error en el comando `mobile table create` cuando existe ya la tabla especificada. En los scripts de automatización, debe intentar eliminar una tabla antes de intentar volver a crearlo.

##Enumeración de tablas existentes en un servicio móvil

El siguiente comando devuelve una lista de todas las tablas de un servicio móvil, donde `<service-name>` es el nombre del servicio móvil:

	azure mobile table list <service-name>

Este comando también muestra el número de índices en cada tabla y el número de filas de datos actualmente en la tabla.

##Eliminación de una tabla existente del servicio móvil

El siguiente comando elimina una tabla del servicio móvil, donde `<service-name>` es el nombre del servicio móvil y `<table-name>` es el nombre de la tabla para eliminar:

	azure mobile table delete <service-name> <table-name> -q

En los scripts de automatización, use el parámetro `-q` para eliminar la tabla sin mostrar un mensaje de confirmación que bloquee la ejecución.

##Registro de un script en una operación de tabla

El siguiente comando carga y registra una función en una operación en una tabla, donde `<service-name>` es el nombre del servicio móvil, `<table-name>` es el nombre de la tabla y `<operation>` es la operación de tabla, que puede ser `read`, `insert`, `update` o `delete`:

	azure mobile script upload <service-name> table/<table-name>.<operation>.js

Tenga en cuenta que esta operación carga un archivo JavaScript (.js) desde el equipo local. El nombre del archivo debe estar compuesto por los nombres de operación y de tabla, y debe encontrarse en la subcarpeta `table` relativa a la ubicación en la que se ejecuta el comando. Por ejemplo, la siguiente operación carga y registra un nuevo script `insert` que pertenece a la tabla `TodoItems`:

	azure mobile script upload todolist table/todoitems.insert.js

La declaración de función en el archivo de script también debe coincidir con la operación de tabla registrada. Esto significa que para un script `insert`, el script cargado contiene una función con la siguiente firma:

	function insert(item, user, request) {
	    ...
	}

Para obtener más información sobre el registro de scripts, consulte [Referencia del script de servidor de Servicios móviles].

<!-- Anchors. -->
[Download and install the command-line tools]: #install
[Download and import publish settings]: #import
[Create a new mobile service]: #create-service
[Get the master key]: #get-master-key
[Create a new table]: #create-table
[Register a new table script]: #register-script
[Delete an existing table]: #delete-table
[Delete an existing mobile service]: #delete-service
[Test the mobile service]: #test-service
[List mobile services]: #list-services
[List tables]: #list-tables
[Next steps]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Portal de Azure clásico]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/#Commands_to_manage_mobile_services
[Instalación de las herramientas de línea de comandos de Azure para Mac y Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795

<!---HONumber=AcomDC_0211_2016-->