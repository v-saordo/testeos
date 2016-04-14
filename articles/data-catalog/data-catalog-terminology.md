<properties
   pageTitle="Terminología del Catálogo de datos de Azure"
   description="Introducción a los conceptos y términos usados en la documentación del Catálogo de datos de Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/08/2016"
   ms.author="maroche"/>

# Terminología del Catálogo de datos de Azure

## Catálogo

El Catálogo de datos de Azure es un repositorio de metadatos basado en la nube en el que se pueden registrar recursos y orígenes de datos. El catálogo actúa como una ubicación de almacenamiento central para metadatos estructurales extraídos de orígenes de datos y para metadatos descriptivos agregados por los usuarios.

## Origen de datos

Un origen de datos es un sistema o un contenedor que administra los recursos de datos. Como ejemplos se incluyen las bases de datos de SQL Server, de Oracle, de SQL Server Analysis Services (tabulares o multidimensionales) y servidores de SQL Server Reporting Services.

## Recurso de datos

Los recursos de datos son objetos contenidos dentro de los orígenes de datos que se pueden registrar con el catálogo. Entre algunos ejemplos se incluyen tablas y vistas de SQL Server, tablas y vistas de Oracle, medidas de SQL Server Analysis Services, dimensiones y KPI e informes de SQL Server Reporting Services.

## Ubicación del recurso de datos

El catálogo almacena la ubicación de un origen de datos o un recurso de datos, que puede usarse para conectar con el origen mediante una aplicación cliente. El formato y los detalles de la ubicación varían según el tipo de origen de datos. Por ejemplo, una tabla de SQL Server puede identificarse mediante su nombre de cuatro partes (nombre del servidor, nombre de la base de datos, nombre del esquema y nombre de objeto), mientras que un informe de SQL Server Reporting Services se puede identificar mediante su dirección URL.

## Metadatos estructurales

Los metadatos estructurales son los metadatos extraídos de un origen de datos que describe la estructura de un recurso de datos. Aquí se incluye la ubicación de los activos, su nombre y tipo de objeto y características específicas del tipo. Por ejemplo, los metadatos estructurales de las tablas y vistas incluyen los nombres y tipos de datos para las columnas del objeto.

## Metadatos descriptivos

Metadatos descriptivos son los metadatos que describen el propósito o el objetivo de un recurso de datos. Normalmente, los metadatos descriptivos son agregados por los usuarios del catálogo mediante el portal del Catálogo de datos de Azure, pero también se pueden extraer del origen de datos durante el registro. La herramienta de registro de Catálogo de datos de Azure extraerá las descripciones de la propiedad Description en SQL Server Analysis Services y SQL Server Reporting Services y desde [la propiedad extendida ms\_description](https://technet.microsoft.com/library/ms190243.aspx)en bases de datos de SQL Server, si estas propiedades se han rellenado con valores.

## Solicitar acceso

Entre los metadatos descriptivos de un activo de datos se puede incluir información sobre cómo solicitar acceso al activo u origen de datos. Esta información se presenta con la ubicación del activo de datos y puede incluir una o más de las siguientes opciones:

- La dirección de correo electrónico del usuario o equipo responsable de la concesión de acceso al origen de datos.
- La URL del proceso documentado que los usuarios deben seguir para obtener acceso al origen de datos.
- La URL de una herramienta de administración de identidades y de accesos (por ejemplo, Microsoft Identity Manager) que puede usarse para tener acceso al origen de datos.
- Una entrada de texto sin formato que describe cómo los usuarios pueden tener acceso al origen de datos.

## Vistas previas

Una vista previa en el Catálogo de datos de Azure es una instantánea de hasta 20 registros que se puede extraer del origen de datos durante el registro y almacenar en el catálogo con los metadatos de recursos de datos. La vista previa puede ayudar a los usuarios que descubren un activo de datos a comprender mejor su función y objetivo. En otras palabras, ver datos de ejemplo puede resultar más valioso que ver tan solo los nombres de columna y los tipos de datos. Las vistas previas solo se admiten para las tablas y vistas y deben seleccionarse explícitamente por el usuario durante el registro.

## Perfil de datos

Un perfil de datos en el Catálogo de datos de Azure es una instantánea de metadatos de nivel de tabla y columna sobre un activo de los datos registrados que se puede extraer del origen de datos durante el registro y almacenar en el catálogo con los metadatos de activos de datos. El perfil de datos puede ayudar a los usuarios que descubren un activo de datos a comprender mejor su función y objetivo. De forma similar a las vistas previas, los perfiles de datos deben ser seleccionados explícitamente por el usuario durante el registro.

> [AZURE.NOTE] Extraer un perfil de datos puede ser una operación costosa en tablas y vistas grandes, y puede aumentar significativamente el tiempo necesario para registrar un origen de datos.

## Perspectiva del usuario

En el Catálogo de datos de Azure, cualquier usuario puede proporcionar metadatos descriptivos para un recurso de datos registrados. Cada usuario tiene una perspectiva distinta de los datos y su uso. Por ejemplo, el administrador de un servidor puede proporcionar los detalles de su contrato de nivel de servicio (SLA) o windows de respaldo; un administrador de datos puede proporcionar vínculos a la documentación para los procesos de negocio admitidos por los datos; un analista puede proporcionar una descripción en los términos que sean más relevantes para otros analistas y que pueden ser más valiosos para aquellos usuarios que necesitan descubrir y comprender los datos.

Cada una de estas perspectivas son intrínsecamente valiosas, y con Catálogo de datos de Azure, cada usuario puede proporcionar la información que le resulte significativa, mientras que todos los usuarios pueden usar esa información para comprender los datos y su objetivo.

## Experto

Un experto es un usuario que se ha identificado como poseedor de una perspectiva informada "experta" para un recurso de datos. Cualquier usuario puede agregarse a sí mismo o a otro usuario como experto para un recurso. Ser enumerado como experto no transmite privilegios adicionales en el Catálogo de datos de Azure; permite a los usuarios localizar fácilmente las perspectivas que tienen más probabilidades de ser útiles para consultar los metadatos descriptivos de un recurso.

## Propietario

Un propietario es un usuario que tiene privilegios adicionales para administrar un recurso de datos en el Catálogo de datos de Azure. Los usuarios pueden tomar posesión de los recursos de datos registrados y los propietarios pueden agregar a otros usuarios como copropietarios.
> [AZURE.NOTE] Propiedad y administración solo están disponibles en la Standard Edition del Catálogo de datos de Azure.

## Registro

El registro es el acto de extraer metadatos de recursos de datos de un origen de datos y copiarla en el servicio del Catálogo de datos de Azure. Es posible anotar y descubrir los recursos de datos que se han registrado.

## Consulte también

- [¿Qué es el Catálogo de datos de Azure?](data-catalog-what-is-data-catalog.md): este artículo proporciona información general sobre el servicio del Catálogo de datos de Azure, el valor que proporciona y los escenarios que admite.

- [Introducción al Catálogo de datos de Azure](data-catalog-get-started.md): este artículo ofrece un tutorial integral que muestra cómo usar el Catálogo de datos de Azure para la detección del orígenes de datos.

<!---HONumber=AcomDC_0211_2016-->