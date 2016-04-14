<properties
    pageTitle="Implementación de implementaciones de gran tamaño"
    description="Obtenga información sobre cómo realizar implementaciones grandes mediante el kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="02/26/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# Implementación de implementaciones de gran tamaño #

Si su implementación es demasiado grande para incluirse en la carpeta approot predeterminada, puede usar un recurso de almacenamiento local como la carpeta raíz de implementación para su JDK y el servidor de aplicaciones.

## Para usar un recurso de almacenamiento local como la carpeta raíz de implementación para implementaciones grandes ##

1. Cree un recurso de almacenamiento local nuevo. El nombre del recurso no importa. Los recursos de almacenamiento se definen en el nivel de rol. La forma más rápida de obtener acceso al cuadro de diálogo de configuración de almacenamiento local para el que podría crear un nuevo recurso de almacenamiento local es mediante los siguientes pasos: haga clic con el botón secundario en el rol en la vista del **Explorador de proyectos** (expandir el nodo del proyecto de Azure si no ve el rol), haga clic en **Azure** y luego haga clic en **Almacenamiento local**. En el cuadro de diálogo **Almacenamiento local**, haga clic en **Agregar** para crear un recurso de almacenamiento local nuevo.
1. Establezca el tamaño que quiera en un mínimo 2048 MB (cualquier tamaño menor podría producir los mismos problemas de tamaño de archivo que se encontraría en approot).
1. Asegúrese de que la opción **Limpiar el contenido cuando se recicle la instancia de rol** está activada; esto le ayudará a evitar que la lógica de inicio de la implementación tenga conflictos con archivos ya existentes en el recurso cuando se recicle la instancia de rol.
1. Asegúrese de que el valor de **Variable de entorno que almacena la ruta de acceso de directorio del recurso tras la implementación** se establece en la cadena **DEPLOYROOT**. Su cuadro de diálogo de recursos del almacenamiento local tendrá un aspecto similar al siguiente. ![][ic667943]

Como alternativa, si usa **DEPLOYROOT** como el *nombre* de su recurso local y no cambia el nombre de la variable de entorno generada automáticamente (que se establecerá en **DEPLOYROOT\_PATH** en ese caso), eso funcionará también para su aplicación.

Encontrará información adicional sobre la creación de un recurso de almacenamiento local en [Propiedades de almacenamiento local][].

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hello World para Azure en Eclipse][]

[Instalación del kit de herramientas de Azure para Eclipse][]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creación de una aplicación Hello World para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalación del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propiedades de almacenamiento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!---HONumber=AcomDC_0302_2016-->