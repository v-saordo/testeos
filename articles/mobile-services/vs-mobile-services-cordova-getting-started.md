<properties
	pageTitle="Introducción a un proyecto de servicios móviles Cordova (Visual Studio Connected Services) | Microsoft Azure"
	description="Describe los primeros pasos que puede llevar a cabo tras la conexión del proyecto Cordova a servicios móviles de Azure mediante Visual Studio Connected Services."
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="mlearned"/>

# Introducción a Servicios móviles (proyectos Cordova)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

##Primeros pasos
El primer paso que debe realizar para seguir el código de estos ejemplos depende del tipo de servicio móvil al que se conecte.

- Para usar un servicio móvil del backend de JavaScript, cree una tabla con el nombre TodoItem. Para crear la tabla, busque el servicio móvil en el nodo Azure en el Explorador de servidores, haga clic con el botón secundario en el nodo del servicio móvil para abrir el menú contextual y elija **Crear tabla**. Escriba "TodoItem" como nombre de la tabla.

- Si utiliza un servicio móvil del backend .NET, hay ya una tabla TodoItem en la plantilla de proyecto predeterminada que Visual Studio ha creado para usted, pero tiene que publicarla en Azure. Para publicarla, abra el menú contextual del proyecto de servicio móvil en el Explorador de soluciones y elija **Publicación web**. Acepte los valores predeterminados y elija el botón **Publicar**.

##Creación de una referencia a una tabla

El código siguiente obtiene una referencia a una tabla que contiene datos para TodoItem, que puede utilizar en operaciones posteriores para leer y actualizar la tabla de datos. La tabla TodoItem se crea automáticamente cuando se crea un servicio móvil.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Para que funcionen estos ejemplos, los permisos de la tabla deben estar configurados en **Cualquier persona con la clave de aplicación**. Más adelante podrá configurar la autenticación. Consulte [Introducción a la autenticación](mobile-services-html-get-started-users.md).

##Agregación de un elemento a una tabla

Inserte un nuevo elemento en una tabla de datos. Se crea automáticamente un identificador (un GUID de tipo cadena) como clave principal para la nueva fila. Llame al método **done** en el objeto [Promise](https://msdn.microsoft.com/library/dn802826.aspx) devuelto para obtener una copia del objeto insertado y abordar los errores.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

##Lectura o consulta de una tabla

El código siguiente consulta una tabla para todos los elementos, ordenados por campo de texto. Puede agregar código para procesar los resultados de la consulta en el controlador de proceso correcto. En este caso, se actualiza una matriz local de los elementos.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

Puede usar el método where para modificar la consulta. Aquí mostramos un ejemplo que filtra los elementos completados.

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

Para obtener más ejemplos de las consultas que puede utilizar, consulte el objeto [query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

##Actualización de un elemento de tabla

Actualice una fila en la tabla de datos. En este código, si el servicio móvil responde, el elemento se quita de la lista. Llame al método **done** en el objeto [Promise](https://msdn.microsoft.com/library/dn802826.aspx) devuelto para obtener una copia del objeto insertado y abordar los errores.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

##Eliminación de un elemento de tabla

Elimine una fila en la tabla de datos utilizando el método **del**. Llame al método **done** en el objeto [Promise](https://msdn.microsoft.com/library/dn802826.aspx) devuelto para obtener una copia del objeto insertado y abordar los errores.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[Más información acerca de Servicios móviles](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0128_2016-->