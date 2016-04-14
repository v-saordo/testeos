<properties
   pageTitle="Registro de orígenes de datos"
   description="Artículo de procedimientos que destaca cómo registrar orígenes de datos con el Catálogo de datos de Azure, incluidos los campos de metadatos que se extraen y los orígenes de datos que se admiten durante la vista previa."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/03/2016"
   ms.author="maroche"/>


# Registro de orígenes de datos

## Introducción
El **Catálogo de datos de Microsoft Azure** es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección de orígenes de datos empresariales. En otras palabras, el **Catálogo de datos de Azure** consiste en ayudar a las personas a detectar, comprender y usar orígenes de datos, y en ayudar a las organizaciones a obtener más valor de sus datos. El primer paso para crear un origen de datos que se pueda detectar a través del **Catálogo de datos de Azure** es registrar ese origen de datos.
## Registro de orígenes de datos
El registro es el proceso de extraer metadatos del origen de datos y copiarlos en el servicio **Catálogo de datos de Azure**. Los datos permanecen donde se encuentren en ese momento y quedan bajo el control de los administradores y las directivas del sistema actual.

Para registrar un origen de datos, basta con iniciar la herramienta de registro de orígenes de datos del **Catálogo de datos de Azure** en el portal **Catálogo de datos de Azure**. Inicie sesión con su cuenta profesional o educativa (las mismas credenciales de Azure Active Directory que emplea para iniciar sesión en el portal) y seleccione el origen de datos que quiera registrar. Vea el tutorial [Introducción al Catálogo de datos de Azure](data-catalog-get-started.md) para obtener las instrucciones paso a paso.

Una vez registrado el origen de datos, el Catálogo realiza el seguimiento de su ubicación e indexa los metadatos para que los usuarios puedan buscar, examinar y detectar el origen de datos y luego usen su ubicación para conectarse al mismo con la aplicación o herramienta de su elección.

## Orígenes admitidos
Para ver la lista de orígenes de datos admitidos actualmente, consulte [DSR del Catálogo de datos](data-catalog-dsr.md). <br/>


## Metadatos estructurales
Al registrar un origen de datos, la herramienta de registro extrae información sobre la estructura de los objetos seleccionados, lo que se conoce como metadatos estructurales.

En todos los objetos, estos metadatos estructurales incluirán la ubicación del objeto, de modo que los usuarios que detecten los datos pueden usar esa información para conectar con el objeto en las herramientas de cliente de su elección. Entre otros metadatos estructurales se incluyen el nombre y tipo del objeto, así como el nombre de atributo/columna y el tipo de datos.

## Metadatos descriptivos
Además de los metadatos estructurales de núcleo extraídos del origen de datos, la herramienta de registro de orígenes de datos también extraerá metadatos descriptivos. En el caso de SQL Server Analysis Services y SQL Server Reporting Services, esto procede de las propiedades de descripción expuestas por estos servicios. En SQL Server, se extraerán los valores especificados mediante la propiedad extendida ms\_description. En el caso de Base de datos de Oracle, la herramienta de registro de orígenes de datos extraerá la columna COMMENTS de la vista ALL\_TAB\_COMMENTS.

Además de los metadatos descriptivos extraídos del origen de datos, los usuarios también pueden especificar metadatos descriptivos con la herramienta de registro de orígenes de datos. Los usuarios pueden agregar etiquetas e identificar expertos para los objetos que se registran. Todos estos metadatos descriptivos se copian en el servicio **Catálogo de datos de Azure** junto con los metadatos estructurales.

## Inclusión de vistas previas

De forma predeterminada, solo se extraen metadatos de orígenes de datos y se copian en el servicio **Catálogo de datos de Azure**, pero la comprensión de un origen de datos suele simplificarse viendo una muestra de los datos que contiene.

La herramienta de registro de orígenes de datos del **Catálogo de datos de Azure** permite a los usuarios incluir una vista previa de instantánea de los datos presentes en cada tabla y vista que se registre. Si el usuario opta por incluir vistas previas durante el registro, la herramienta de registro incluirá un máximo de 20 registros de cada tabla y vista. Esta instantánea se copia después en el Catálogo junto con los metadatos descriptivos y estructurales.


> [AZURE.NOTE]  Las tablas anchas que tienen un gran número de columnas pueden tener menos de 20 registros incluidos en la vista previa.


## Inclusión de los perfiles de datos

De la misma manera que incluir vistas previas puede proporcionar contexto valioso para los usuarios que buscan orígenes de datos en el **Catálogo de datos de Azure**, incluir un perfil de datos también puede facilitar la comprensión de los orígenes de datos detectados.

La herramienta de registro de orígenes de datos del **Catálogo de datos de Azure** permite a los usuarios incluir un perfil de datos para tabla y vista que se registre. Si el usuario decide incluir un perfil de datos durante el registro, la herramienta de registro incluirá estadísticas agregadas sobre los datos de cada tabla o vista, incluyendo:

* El número de filas y el tamaño de los datos que contiene el objeto
* La fecha de la actualización más reciente de los datos y el esquema de objeto
* El número de registros nulos y valores distintos para las columnas
* Los valores mínimo, máximo, promedio y de desviación estándar de las columnas

Estas estadísticas se copian después en el Catálogo junto con los metadatos estructurales y descriptivos.

> [AZURE.NOTE]  Las columnas de texto y fecha no incluirá las estadísticas de promedio o desviación estándar en su perfil de datos.

## Actualización de registros

El registro de un origen de datos hará que sea detectable en el **Catálogo de datos de Azure** con los metadatos y la vista previa opcional que se extraen durante el registro. Si el origen de datos debe actualizarse en el Catálogo (por ejemplo, si cambia el esquema de un objeto, hay que incluir tablas que se excluyeron originalmente o un usuario quiere actualizar los datos incluidos en las vistas previas), puede volver a ejecutarse la herramienta de registro de orígenes de datos.

Al volver a registrar un origen de datos ya registrado se realiza una operación de combinación "upsert": los objetos existentes se actualizan al tiempo que los nuevos objetos se crean. Los metadatos especificados por los usuarios en el portal del **Catálogo de datos de Azure** se mantendrán.

## Resumen
Al registrar un origen de datos con el **Catálogo de datos de Azure** se facilita la detección y comprensión de ese origen de datos, al copiar los metadatos estructurales y descriptivos del origen de datos en el servicio Catálogo. Una vez registrado un origen de datos, se puede anotar, administrar y detectar mediante el portal **Catálogo de datos de Azure**.

## Consulte también
- Consulte el tutorial [Introducción al Catálogo de datos de Azure](data-catalog-get-started.md) para obtener información paso a paso sobre cómo registrar orígenes de datos.

<!---HONumber=AcomDC_0309_2016-->